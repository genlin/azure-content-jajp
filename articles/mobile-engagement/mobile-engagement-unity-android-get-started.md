<properties
	pageTitle="Unity Android デプロイでの Azure Mobile Engagement の使用"
	description="Android デバイスにデプロイする Unity アプリの分析やプッシュ通知で Azure Mobile Engagement を使用する方法について説明します。"
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Unity Android デプロイでの Azure Mobile Engagement の使用

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

このトピックでは、Azure Mobile Engagement を使用してアプリの使用状況を把握する方法と、Android デバイスへのデプロイ時に Unity アプリケーションのセグメント化されたユーザーにプッシュ通知を送信する方法について説明します。このチュートリアルでは、出発点として、従来の Unity Roll a Ball チュートリアルを使用します。以下のチュートリアルで紹介する Mobile Engagement の統合に進む前に、この[チュートリアル](mobile-engagement-unity-roll-a-ball.md)の手順を実行しておく必要があります。

このチュートリアルには、次のものが必要です。

+ [Unity エディター](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ Google Android SDK

> [AZURE.NOTE] このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、[Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fja-JP%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started)を参照してください。

##<a id="setup-azme"></a>Android アプリ用のモバイル エンゲージメントの設定

[AZURE.INCLUDE [ポータルで Mobile Engagement アプリを作成する](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>アプリを Mobile Engagement のバックエンドに接続します

###Unity パッケージのインポート

1. [Mobile Engagement Unity パッケージ](https://aka.ms/azmeunitysdk)をダウンロードし、ローカル コンピューターに保存します。 

2. **[Assets] \(アセット)、[Import Package] \(パッケージのインポート)、[Custom Package] \(カスタム パッケージ)** の順に移動し、前の手順でダウンロードしたパッケージを選択します。

	![][70]

3. すべてのファイルを選択し、**[Import]** (インポート) ボタンをクリックします。

	![][71]

4. インポートが成功すると、プロジェクトにインポートされた SDK ファイルが表示されます。

	![][72]

###EngagementConfiguration の更新

1. SDK フォルダーの **EngagementConfiguration** スクリプト ファイルを開き、先ほど Azure ポータルで取得した接続文字列を使って **ANDROID\_CONNECTION\_STRING** を更新します。  

	![][73]

2. ファイルを保存します。

3. **[File] \(ファイル)、[Engagement] \(エンゲージメント)、[Generate Android Manifest] \(Android Manifest の生成)** の順にクリックして実行します。これは Mobile Engagement SDK によって追加されたプラグインで、クリックすると、プロジェクトの設定が自動的に更新されます。

	![][74]

> [AZURE.IMPORTANT] この操作は、**EngagementConfiguration** ファイルを更新するたびに必ず実行してください。実行しないと、変更がアプリに反映されません。

###基本的な追跡を行うためのアプリの構成

1. Player オブジェクトに関連付けられた **PlayerController** スクリプトを編集するために開きます。 

2. 次の using ステートメントを追加します。

		using Microsoft.Azure.Engagement.Unity;

3. 次の行を `Start()` メソッドに追加します。
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###アプリのデプロイと実行
この Unity アプリをデバイスにデプロイする前に、Android SDK がコンピューターにインストールされていることを確認してください。

1. Android デバイスをコンピューターに接続します。 

2. **[File] \(ファイル)、[Build Settings] \(設定をビルド)** の順に開きます。

	![][40]

3. **[Android]** を選択し、**[Switch Platform]** (プラットフォームを切り替える) をクリックします。

	![][51]

	![][52]

4. **[Player settings]** (プレイヤーの設定) をクリックし、有効なバンドル識別子を入力します。

	![][53]

5. 最後に、**[Build And Run]** (ビルドと実行) をクリックします。

	![][54]

6. Android パッケージを格納するフォルダー名の入力を求められる場合があります。

7. すべての手順が正常に行われると、接続されているデバイスにパッケージがデプロイされ、スマートフォンに Unity ゲームが表示されます。

##<a id="monitor"></a>リアルタイム監視を使用してアプリを接続する

[AZURE.INCLUDE [リアルタイム監視を使用してアプリを接続する](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>プッシュ通知とアプリ内メッセージングを有効にする

[AZURE.INCLUDE [Google Cloud Messaging を有効にする](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###EngagementConfiguration の更新

1. SDK フォルダーから **EngagementConfiguration** スクリプト ファイルを開き、先ほど Google クラウド開発者ポータルで取得した **Google プロジェクト番号**を使って **ANDROID\_GOOGLE\_NUMBER** を更新します。これは文字列値のため、二重引用符で囲む必要があります。 

	![][75]

2. ファイルを保存します。

3. **[File] \(ファイル)、[Engagement] \(エンゲージメント)、[Generate Android Manifest] \(Android Manifest の生成)** の順にクリックして実行します。これは Mobile Engagement SDK によって追加されたプラグインで、クリックすると、プロジェクトの設定が自動的に更新されます。

	![][74]

###通知を受け取るためのアプリの構成

1. Player オブジェクトに関連付けられた **PlayerController** スクリプトを編集するために開きます。 

2. 次の行を `Start()` メソッドに追加します。

		EngagementReachAgent.Initialize();

3. アプリが更新されたので、以下の手順に従って、デバイス上でアプリをデプロイして実行します。

[AZURE.INCLUDE [Send notification from portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!-----HONumber=AcomDC_0330_2016-->