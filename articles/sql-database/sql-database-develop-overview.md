<properties
	pageTitle="SQL Database の開発の概要 | Microsoft Azure"
	description="SQL Database に接続するアプリケーションで使用できる接続ライブラリとベスト プラクティスについて説明します。"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="genemi"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="annemill"/>

# SQL Database の開発: 概要
この記事では、Azure SQL Database に接続するコードを記述するときの基本的な考慮事項について説明します。

## 言語とプラットフォーム
さまざまなプログラミング言語とプラットフォームで利用できるコード サンプルがあります。コード サンプルについては、次のリンクをご覧ください。

* 詳細: [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)

## リソースの制限事項
Azure SQL Database では、リソース ガバナンスと制限の適用という 2 つの異なるメカニズムを使用して、データベースで使用できるリソースを管理します。

* 詳細: [Azure SQL Database のリソース制限](sql-database-resource-limits.md)

## セキュリティ
Azure SQL Database には、SQL Database に対するアクセスの制限、データの保護、およびアクティビティの監視を行うためのリソースが用意されています。

* 詳細: [SQL Database の保護](sql-database-security.md)

## 認証
* Azure SQL Database では、SQL Server 認証のユーザーとログインの両方と、[Azure Active Directory 認証](sql-database-aad-authentication.md)ユーザーとログインがサポートされています。
* 既定の*マスター* データベースではなく、特定のデータベースを明示的に指定する必要があります。
* Transact-SQL の **USE myDatabaseName;** ステートメントを SQL Database に対して使用して別のデータベースに切り替えることはできません。
* 詳細: [SQL Database のセキュリティ: データベースのアクセスとログインのセキュリティの管理](sql-database-manage-logins.md)

## 回復性
SQL Database への接続中に一時エラーが発生した場合は、コードで呼び出しを再試行する必要があります。再試行ロジックでは、複数のクライアントが同時に再試行することによって SQL Database に不必要な負荷がかかることがないようにするために、バックオフ ロジックを使用することをお勧めします。

* コード サンプル: 再試行ロジックを示すコード サンプルについては、次の記事で好みの言語用のサンプルを参照してください: [SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)
* 詳細: [SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)

## 接続の管理
* クライアント接続ロジックの中で、タイムアウトが 30 秒になるように既定値をオーバーライドします。既定では 15 秒ですが、インターネットに依存する接続の場合、それでは短すぎます。
* [接続プール](http://msdn.microsoft.com/library/8xx3tyca.aspx)を使用している場合は、プログラムで接続をアクティブに使用しておらず、再使用の準備をしていない時間は、接続を必ず閉じてください。

## ネットワークに関する考慮事項
* クライアント プログラムをホストするコンピューターのファイアウォールで、ポート 1433 での発信 TCP が許可されていることを確認します。詳細: [ Azure ポータルを使用して Azure SQL Database ファイアウォールを構成する](sql-database-configure-firewall-settings.md)
* クライアントが Azure 仮想マシン (VM) で実行されているときに、クライアント プログラムが SQL Database V12 に接続する場合、VM で特定のポートの範囲を開く必要があります。詳細: [ADO.NET 4.5 および SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)
* Azure SQL Database V12 へのクライアント接続はプロキシを使用せずに、データベースに直接やり取りする場合があります。1433 以外のポートが重要になります。詳細: [ADO.NET 4.5 および SQL Database V12 における 1433 以外のポート](sql-database-develop-direct-route-ports-adonet-v12.md)

## Elastic Scale によるデータ シャーディング
Elastic Scale は、スケール アウト (およびスケール イン) のプロセスを簡略化します。

* [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* [データ依存ルーティング](sql-database-elastic-scale-data-dependent-routing.md)
* [Azure SQL Database Elastic Scale プレビューの概要](sql-database-elastic-scale-get-started.md)

## 次のステップ

[SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべてを確認します。

<!---HONumber=AcomDC_0720_2016-->