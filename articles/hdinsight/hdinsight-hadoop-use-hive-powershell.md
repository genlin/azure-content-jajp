<properties
   pageTitle="HDInsight での Hadoop Hive と Hadoop の使用 | Microsoft Azure"
   description="PowerShell を使用して、HDInsight での Hadoop で Hive クエリを実行します。"
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/16/2016"
   ms.author="larryfr"/>

#PowerShell を使用して Hive クエリを実行

[AZURE.INCLUDE [hive セレクター](../../includes/hdinsight-selector-use-hive.md)]

このドキュメントでは、Azure PowerShell を Azure リソース グループ モードで使用して、HDInsight クラスターの Hadoop で Hive クエリを実行する方法について説明します。

> [AZURE.NOTE] このドキュメントには、例で使用される HiveQL ステートメントで何が実行されるかに関する詳細は含まれていません。この例で使用される HiveQL については、「[HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)」をご覧ください。


**前提条件**

この記事の手順を完了するには、次のものが必要です。

- **Azure HDInsight (HDInsight での Hadoop) クラスター (Windows または Linux ベース)**
- **Azure PowerShell を実行できるワークステーション**。

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##Azure PowerShell を使用して Hive クエリを実行する

Azure PowerShell では、HDInsight で Hive クエリをリモートに実行できる*コマンドレット*が提供されます。これは、HDInsight クラスター上で実行される [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) への REST 呼び出し (旧称: Templeton) を内部的に使用することで機能します。

リモート HDInsight クラスターで Hive クエリを実行するときに次のコマンドレットを使用します。

* **Add-AzureRmAccount**: Azure サブスクリプションに対して Azure PowerShell を認証します。

* **New-AzureRmHDInsightHiveJobDefinition**: 指定された HiveQL ステートメントを使用して、新しい*ジョブ定義*を作成します。

* **Start-AzureRmHDInsightJob**: ジョブ定義を HDInsight に送信し、ジョブを開始して、ジョブのステータスの確認に使用できる*ジョブ* オブジェクトを返します。

* **Wait-AzureRmHDInsightJob**: ジョブ オブジェクトを使用して、ジョブのステータスを確認します。ジョブの完了を待機するか、待機時間が上限に達します。

* **Get-AzureRmHDInsightJobOutput**: ジョブの出力を取得する場合に使用します。

* **Invoke-AzureRmHDInsightHiveJob**: HiveQL ステートメントを実行する場合に使用します。これはクエリの完了にブロックし、その結果を返します。

* **Use-AzureRmHDInsightCluster**: **Invoke-AzureRmHDInsightHiveJob** コマンドに対して現在のクラスターを使用するように設定します。

これらのコマンドレットを使用して、HDInsight クラスターでジョブを実行するための手順を以下に示します。

1. エディターを使用して、次のコードを **hivejob.ps1** として保存します。**CLUSTERNAME** を HDInsight クラスターの名前に置き換えます。

		#Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. **Azure PowerShell** コマンド プロンプトを開きます。ディレクトリを **hivejob.ps1** ファイルの場所に変更し、次のコマンドを使用してスクリプトを実行します。

		.\hivejob.ps1

    スクリプトの実行時に、クラスター用の HTTPS/管理者アカウントの資格情報を入力するように求められます。Azure サブスクリプションへのログインが求められる場合もあります。
    
7. ジョブが完了すると、次のような情報が返されます。

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. 前述のように、**Invoke-Hive** を使用してクエリを実行し、応答を待機できます。次のコマンドを使用して、**CLUSTERNAME** をクラスターの名前で置き換えます。

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

	出力は次のようになります。

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] より長い HiveQL クエリの場合は、Azure PowerShell の **Here-Strings** コマンドレットや HiveQL スクリプト ファイルを使用できます。次のスニペットでは、**Invoke-Hive** コマンドレットを使用して HiveQL スクリプト ファイルを実行する方法を示します。HiveQL スクリプト ファイルは、wasbs:// にアップロードする必要があります。
	>
	> `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> **Here-Strings** の詳細については、「<a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings の使用</a>」をご覧ください。

##トラブルシューティング

ジョブの完了時に情報が返されない場合は、処理中にエラーが発生した可能性があります。このジョブに関するエラーを表示するには、以下を **hivejob.ps1** ファイルの末尾に追加して保存し、再実行します。

	# Print the output of the Hive job.
	Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

これにより、ジョブの実行時にサーバー上の STDERR に書き込まれた情報が返されるため、ジョブ失敗の特定に役立ちます。

##概要

このように、Azure PowerShell を使用すると、HDInsight クラスターで簡単に Hive クエリを実行し、ジョブ ステータスを監視し、出力を取得できます。

##次のステップ

HDInsight での Hive に関する全般的な情報

* [HDInsight での Hive と Hadoop の使用](hdinsight-use-hive.md)

HDInsight での Hadoop のその他の使用方法に関する情報

* [HDInsight での Pig と Hadoop の使用](hdinsight-use-pig.md)

* [HDInsight での MapReduce と Hadoop の使用](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0727_2016-->