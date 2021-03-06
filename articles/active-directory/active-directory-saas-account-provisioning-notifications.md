<properties
	pageTitle="アカウント プロビジョニング通知 | Microsoft Azure"
	description="アカウント プロビジョニング通知を有効にすることで、注意が必要なユーザー プロビジョニングに関連する問題を確実に知る方法について説明します。"
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="markusvi"/>


# アカウント プロビジョニング通知

ユーザー プロビジョニングを使用すると、サード パーティの SaaS アプリケーションでユーザーを管理するプロセスを自動化できます。<br>これは自動化されたプロセスですが、このプロセスとのやり取りが必要になることがあります。<br>たとえば、サード パーティの SaaS アプリケーションとデータを交換するために構成したアカウントのパスワードの期限が切れたときなどです。

アカウント プロビジョニング通知を有効にすると、注意が必要なユーザー プロビジョニングに関連する問題を、確実に知ることができます。

アカウント プロビジョニング通知は、サードパーティの SaaS アプリケーションのユーザーのプロビジョニング構成の一部として、アクティブまたは非アクティブにします。

![ユーザーのプロビジョニング][1]



アカウント プロビジョニング通知をアクティブにするには、**[確認]** ダイアログ ページで関連するチェックボックスを選択し、受信者の電子メール エイリアスを入力します。

![アカウント プロビジョニング通知][2]
 


受信者として配布リストに登録することができます。ただし、通知電子メールには、Azure AD 管理者しかアクセスできないレポートへのリンクが含まれていることに注意する必要があります。

アカウント プロビジョニング通知を有効にした場合は、ユーザー プロビジョニングに関連する重要な問題についての電子メールを受信するようになります。ただし、電子メールのオーバーロードを避けるため、通知電子メールは、通知電子メールを有効にしている SaaS アプリケーションごとに、1 日に 1 通のみ受信します。


##関連記事

- [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
- [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
- [ユーザーのプロビジョニング用の属性マッピングのカスタマイズ](active-directory-saas-customizing-attribute-mappings.md)
- [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
- [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)



<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=AcomDC_0720_2016-->