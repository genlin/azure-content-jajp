<properties
	pageTitle="SQL Server のパフォーマンスに関するベスト プラクティス | Microsoft Azure"
	description="Microsoft Azure VM で SQL Server のパフォーマンスを最適化するためのベスト プラクティスを紹介します。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="07/15/2016"
	ms.author="jroth" />

# Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス

## 概要

このトピックでは、Microsoft Azure 仮想マシンで SQL Server のパフォーマンスを最適化するためのベスト プラクティスを紹介します。Azure Virtual Machines で SQL Server を実行するときは、オンプレミスのサーバー環境で SQL Server に適用されるデータベース パフォーマンス チューニング オプションと同じものを引き続き使用することをお勧めします。ただし、パブリック クラウド内のリレーショナル データベースのパフォーマンスは、仮想マシンのサイズやデータ ディスクの構成などのさまざまな要素に左右されます。

SQL Server イメージを作成するときは、[VM を Azure ポータルにプロビジョニングすることを検討してください](virtual-machines-windows-portal-sql-server-provision.md)。Resource Manager を使用してポータルにプロビジョニングされた SQL Server VM は、ストレージの構成を含むすべてのベスト プラクティスを実装します。

この記事は、Azure VM で SQL Server の*最適な*パフォーマンスを得ることに重点を置いています。ワークロードの要求が厳しくない場合は、以下に示す最適化がすべて必要になるわけではありません。各推奨事項を評価するときに、パフォーマンスのニーズとワークロードのパターンを考慮してください。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## クイック チェック リスト

Azure Virtual Machines で SQL Server の最適なパフォーマンスを実現するためのクイック チェック リストを次に示します。

|領域|最適化|
|---|---|
|[VM サイズ](#vm-size-guidance)|[DS3](virtual-machines-windows-sizes.md#standard-tier-ds-series) 以上 (SQL Enterprise Edition の場合)。<br/><br/>[DS2](virtual-machines-windows-sizes.md#standard-tier-ds-series) 以上 (SQL Standard Edition および Web Edition の場合)。|
|[Storage](#storage-guidance)|[Premium Storage](../storage/storage-premium-storage.md) を使用します。標準ストレージは dev/test に対してのみ推奨されます。<br/><br/>[ストレージ アカウント](../storage/storage-create-storage-account.md)と SQL Server VM を同じリージョンに保持します。<br/><br/>ストレージ アカウントで Azure geo [冗長ストレージ](../storage/storage-redundancy.md) (geo レプリケーション) を無効にします。|
|[ディスク](#disks-guidance)|少なくとも 2 つの [P30 ディスク](../storage/storage-premium-storage.md#scalability-and-performance-targets-whja-JPing-premium-storage) (ログ ファイル用に 1 つ、データ ファイルと TempDB 用に 1 つ) を使用します。<br/><br/>データベースの保存またはログ記録にオペレーティング システム ディスクまたは一時ディスクを使用することは避けます。<br/><br/>データ ファイルと TempDB をホストするディスクで読み取りキャッシュを有効にします。<br/><br/>ログ ファイルをホストするディスクでは、キャッシュを有効にしないでください。<br/><br/>複数の Azure データ ディスクをストライプして、IO スループットを増やします。<br/><br/>ドキュメントに記載されている割り当てサイズでフォーマットします。|
|[I/O](#io-guidance)|データベース ページの圧縮を有効にします。<br/><br/>データ ファイルの瞬時初期化を有効にします。<br/><br/>データベースで自動拡張を制限するか、無効にします。<br/><br/>データベースで自動圧縮を無効にします。<br/><br/>システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。<br/><br/>SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。<br/><br/>既定のバックアップ ファイルとデータベース ファイルの場所を設定します。<br/><br/>ロックされたページを有効にします。<br/><br/>SQL Server パフォーマンス修正プログラムを適用します。|
|[機能固有](#feature-specific-guidance)|BLOB ストレージに直接バックアップします。|

これらの最適化を行うための*方法*と*理由*については、 次のセクションに記載されている詳細とガイダンスを確認してください。

## VM サイズのガイダンス

パフォーマンス重視のアプリケーションでは、次の[仮想マシン サイズ](virtual-machines-windows-sizes.md)を使用することをお勧めします。

- **SQL Server Enterprise Edition**: DS3 以上

- **SQL Server Standard Edition または Web Edition**: DS2 以上


## ストレージのガイダンス

DS シリーズ (DSv2 シリーズおよび GS シリーズと共に) の VM は、[Premium Storage](../storage/storage-premium-storage.md) をサポートしています。すべての運用環境のワークロードには Premium Storage をお勧めします。

> [AZURE.WARNING] Standard Storage には、さまざまな待機時間や帯域幅があり、開発/テスト ワークロードにのみ推奨されます。運用環境のワークロードでは、Premium Storage を使用する必要があります。

さらに、転送遅延を低減するために、SQL Server 仮想マシンと同じデータ センターで Azure ストレージ アカウントを作成することをお勧めします。ストレージ アカウントの作成時に、geo レプリケーションを無効にします。複数のディスクでの一貫性のある書き込み順序が保証されないためです。代わりに、2 つの Azure データ センター間で SQL Server 障害復旧テクノロジを構成することを検討します。詳細については、「[Azure Virtual Machines における SQL Server の高可用性と障害復旧](virtual-machines-windows-sql-high-availability-dr.md)」を参照してください。

## ディスクのガイダンス

Azure VM には、次の 3 種類のメイン ディスクがあります。

- **OS ディスク**: Azure Virtual Machine を作成すると、プラットフォームによって、オペレーティング システム ディスク用に少なくとも 1 つのディスク (**C** ドライブとしてラベル付けされる) が VM に接続されます。このディスクは、ページ BLOB としてストレージに格納されている VHD です。
- **一時ディスク**: Azure Virtual Machines には、一時ディスクと呼ばれる別のディスク (**D**: ドライブとしてラベル付けされる) が含まれています。これは、スクラッチ領域に使用できるノード上のディスクです。
- **データ ディスク**: 追加のディスクをデータ ディスクとして仮想マシンに接続することもできます。これらのディスクは、ページ BLOB としてストレージに格納されます。

次のセクションでは、これらの異なるディスクの使用に関する推奨事項について説明します。

### オペレーティング システム ディスク

オペレーティング システム ディスクは、実行中のバージョンのオペレーティング システムとして起動およびマウントできる VHD であり、**C** ドライブとしてラベル付けされます。

オペレーティング システム ディスクの既定のキャッシュ ポリシーは、**読み取り/書き込み**です。パフォーマンス重視のアプリケーションでは、オペレーティング システム ディスクではなく、データ ディスクを使用することをお勧めします。下記のデータ ディスクに関するセクションをご覧ください。

### 一時ディスク

**D**: ドライブとしてラベル付けされる一時ストレージ ドライブは、Azure BLOB ストレージに保持されません。ユーザー データベース ファイルやユーザー トランザクション ログ ファイルを **D**: ドライブに保存しないでください。

D シリーズ、Dv2 シリーズ、および G シリーズの VM では、これらの VM 上の一時ドライブは SSD ベースです。一時オブジェクトや複雑な結合などのワークロードで TempDB が多用される場合、TempDB を **D** ドライブに格納すると、TempDB のスループットは高く、TempDB の遅延時間は低くなる可能性があります。

Premium Storage (DS シリーズ、DSv2 シリーズ、および GS シリーズ) をサポートする VM の場合は、Premium Storage をサポートし、読み取りキャッシングが有効なディスクに TempDB とバッファー プール拡張機能を格納することをお勧めします。この推奨事項には 1 つの例外があります。TempDB の使用が書き込み重視である場合は、TempDB をローカル **D** ドライブに格納することで、より高いパフォーマンスを実現できます。これも、マシン サイズに基づく SSD ベースです。

### データ ディスク

- **データ ファイルとログ ファイル用のデータ ディスクの使用**: 少なくとも、2 つの Premium Storage [P30 ディスク](../storage/storage-premium-storage.md#scalability-and-performance-targets-whja-JPing-premium-storage)を使用し、一方のディスクにログ ファイル、もう一方にデータ ファイルと TempDB を含めます。

- **ディスク ストライピング**: スループットを向上させるには、データ ディスクをさらに追加して、ディスク ストライピングを使用します。データ ディスクの数を決定するには、データおよびログ ディスクで使用可能な IOPS の数を分析する必要があります。詳細については、[ディスクへの Premium Storage の使用](../storage/storage-premium-storage.md)に関する記事で [VM サイズ](virtual-machines-windows-sizes.md)およびディスク サイズごとの IOPS を示す表をご覧ください。次のガイドラインに従ってください。

	- Windows 8 と Windows Server 2012 以降では、[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)を使用します。ストライプ サイズを、OLTP ワークロードでは 64 KB、データ ウェアハウス ワークロードでは 256 KB にそれぞれ設定して、パーティションの不適切な配置に起因するパフォーマンスの低下を防ぎます。また、列数を物理ディスクの数に設定します。8 個以上のディスクで記憶域スペースを構成するには、(サーバー マネージャーの UI ではなく) PowerShell を使用して、ディスクの数に一致する列数を明示的に設定する必要があります。[記憶域スペース](https://technet.microsoft.com/library/hh831739.aspx)の構成方法の詳細については、[Windows PowerShell の記憶域スペース コマンドレット](https://technet.microsoft.com/library/jj851254.aspx)に関するページをご覧ください。

	- Windows 2008 R2 以前では、ダイナミック ディスク (OS ストライプ ボリューム) を使用できます。ストライプ サイズは常に 64 KB です。Windows 8 および Windows Server 2012 の時点で、このオプションは使用されていません。詳細については、[Windows Storage Management API に移行しつつある仮想ディスク サービス](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx)に関するページでサポートに関する声明をご覧ください。

	- ワークロードで大量のログが発生するわけではなく、専用の IOPS を必要としない場合は、記憶域プールを 1 つだけ構成します。それ以外の場合は、記憶域プールを 2 つ作成して、1 つをログ ファイルに使用し、もう 1 つをデータ ファイルと TempDB に使用します。負荷予測に基づいて、各記憶域プールに関連付けるディスクの数を決定します。接続できるデータ ディスクの数は VM サイズによって異なることに注意してください。詳細については、「[Virtual Machines のサイズ](virtual-machines-windows-sizes.md)」を参照してください。

	- Premium Storage (開発/テスト シナリオ) を使用しない場合は、ご使用の [VM サイズ](virtual-machines-windows-sizes.md)でサポートされる最大数のデータ ディスクを追加し、ディスク ストライピングを使用することをお勧めします。

- **キャッシュ ポリシー**: Premium Storage データ ディスクの場合は、データ ファイルと TempDB のみをホストするデータ ディスクで読み取りキャッシュを有効にします。Premium Storage を使用していない場合は、どのデータ ディスクでもキャッシュを有効にしないでください。ディスクのキャッシュを構成する手順については、「[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847)」および「[Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)」をご覧ください。

- **NTFS アロケーション ユニット サイズ**: データ ディスクをフォーマットするときは、データ ファイルとログ ファイルに加えて TempDB にも 64 KB アロケーション ユニット サイズを使用することをお勧めします。

## I/O のガイダンス

- Premium Storage では、アプリケーションと要求の並列処理を実行するときに最良の結果が得られます。Premium Storage は、IO キューの深さが 1 より大きいシナリオ向けに設計されているので、シングル スレッド シリアル要求では (ストレージを集中的に使用する場合でも)、パフォーマンスの向上はほとんどまたはまったくありません。たとえば、これは、パフォーマンス分析ツール (SQLIO など) のシングル スレッド テストの結果に影響する可能性があります。

- I/O 集中型ワークロードのパフォーマンスを向上させるために、[データベース ページの圧縮](https://msdn.microsoft.com/library/cc280449.aspx)を使用することを検討します。ただし、データ圧縮を使用すると、データベース サーバーでの CPU 消費量が増加する場合があります。

- 初期ファイル割り当てに必要な時間を短縮するために、ファイルの瞬時初期化を有効にすることを検討します。ファイルの瞬時初期化を利用するには、SQL Server (MSSQLSERVER) サービス アカウントに SE\_MANAGE\_VOLUME\_NAME を付与し、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーにそのサービス アカウントを追加します。Azure の SQL Server プラットフォーム イメージを使用している場合、既定のサービス アカウント (NT Service\\MSSQLSERVER) は、**[ボリュームの保守タスクを実行]** セキュリティ ポリシーに追加されません。つまり、SQL Server Azure プラットフォーム イメージでは、ファイルの瞬時初期化は有効になりません。**[ボリュームの保守タスクを実行]** セキュリティ ポリシーに SQL Server サービス アカウントを追加したら、SQL Server サービスを再起動します。この機能を使用する場合、セキュリティに関する考慮事項があります。詳細については、「[データベース ファイルの初期化](https://msdn.microsoft.com/library/ms175935.aspx)」をご覧ください。

- **自動拡張**は、予想外の増加に付随するものと見なされています。自動拡張を使用して、データやログの増加に日常的に対処しないでください。自動拡張を使用する場合は、Size スイッチを使用してファイルを事前に拡張します。

- パフォーマンスに悪影響を及ぼすおそれのある不要なオーバーヘッドを回避するために、**自動圧縮**が無効になっていることを確認します。

- システム データベースも含め、すべてのデータベースをデータ ディスクに移動します。詳細については、「[システム データベースの移動](https://msdn.microsoft.com/library/ms345408.aspx)」をご覧ください。

- SQL Server エラー ログとトレース ファイルのディレクトリをデータ ディスクに移動します。これは、SQL Server インスタンスを右クリックしてプロパティを選択することにより、SQL Server 構成マネージャーで実行できます。エラー ログとトレース ファイルの設定は、**[起動時のパラメーター]** タブで変更できます。ダンプ ディレクトリは、 **[詳細設定]** タブで指定します。次のスクリーンショットでは、エラー ログの起動時のパラメーターを検索する場所を示します。

	![SQL ErrorLog のスクリーンショット](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

- 既定のバックアップ ファイルとデータベース ファイルの場所を設定します。このトピックでは、推奨事項を使用し、[サーバーのプロパティ] ウィンドウで変更を行います。手順については、「[データ ファイルとログ ファイルの既定の場所の表示または変更 (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)」を参照してください。次のスクリーンショットでは、これらの変更を行う場所を示します。

	![SQL データのログおよびバックアップ ファイル](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)

- ロックされたページを有効にして、IO とページング アクティビティを減らします。詳細については、「[Lock Pages in Memory オプションの有効化 (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)」をご覧ください。

- SQL Server 2012 を実行している場合は、Service Pack 1 Cumulative Update 10 をインストールします。この更新プログラムには、SQL Server 2012 で一時テーブルに対して SELECT INTO ステートメントを実行したときに I/O のパフォーマンスが低下する問題に対処するための修正プログラムが含まれています。詳細については、この[サポート技術情報の記事](http://support.microsoft.com/kb/2958012)をご覧ください。

- Azure との間での転送時にデータ ファイルを圧縮することを検討します。

## 機能固有のガイダンス

一部のデプロイでは、より高度な構成手法を使用することで、パフォーマンスがさらに向上する場合があります。パフォーマンスの向上を実現する際に役立つ SQL Server の機能を次に示します。

- **Azure Storage へのバックアップ**: Azure 仮想マシンで実行される SQL Server のバックアップを実行する際は、[SQL Server Backup to URL](https://msdn.microsoft.com/library/dn435916.aspx) を使用できます。SQL Server 2012 SP1 CU2 以降で使用できるこの機能は、接続されているデータ ディスクにバックアップする場合に推奨されます。Azure Storage との間でバックアップと復元を行うときは、「[SQL Server Backup to URL に関するベスト プラクティスとトラブルシューティング](https://msdn.microsoft.com/library/jj919149.aspx)」に記載されている推奨事項に従ってください。[Azure Virtual Machines での SQL Server の自動バックアップ](virtual-machines-windows-classic-sql-automated-backup.md)を使用して、バックアップを自動化することもできます。

	SQL Server 2012 より前のバージョンでは、[SQL Server Backup to Azure Tool](https://www.microsoft.com/download/details.aspx?id=40740) を使用できます。このツールでは、複数のバックアップ ストライプ ターゲットを使用することで、バックアップ スループットを向上させることができます。

- **Azure 内の SQL Server データ ファイル**: [Azure 内の SQL Server データ ファイル](https://msdn.microsoft.com/library/dn385720.aspx)は、SQL Server 2014 以降で使用できる新機能です。Azure 内のデータ ファイルを使用して SQL Server を実行すると、Azure データ ディスクを使用する場合と同等のパフォーマンス特性が得られます。

## 次のステップ

SQL Server と Premium Storage についてさらに詳しく調べたい場合は、「[Virtual Machines 上での Azure Premium Storage と SQL Server の使用](virtual-machines-windows-classic-sql-server-premium-storage.md)」をご覧ください。

セキュリティのベスト プラクティスについては、[Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項](virtual-machines-windows-sql-security.md)に関するページをご覧ください。

SQL Server 仮想マシンに関する他のトピックについては、[Azure 仮想マシンにおける SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)に関するページをご覧ください。

<!---HONumber=AcomDC_0720_2016-->