<properties
	pageTitle="チュートリアル: Azure Active Directory と SanSan の統合 | Microsoft Azure"
	description="Azure Active Directory と SanSan の間でシングル サインオンを構成する方法について確認します。"
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/20/2016"
	ms.author="jeedes"/>


# チュートリアル: Azure Active Directory と SanSan の統合

このチュートリアルでは、SanSan と Azure Active Directory (Azure AD) を統合する方法について説明します。

SanSan と Azure AD の統合には、次の利点があります。

- SanSan にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SanSan にサインオン (シングル サインオン) できるようにすることが可能です。
- 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## 前提条件

SanSan と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SanSan でのシングル サインオンが有効なサブスクリプション


> [AZURE.NOTE] このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SanSan の追加
2. Azure AD シングル サインオンの構成とテスト


## ギャラリーからの SanSan の追加
Azure AD への SanSan の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SanSan を追加する必要があります。

**ギャラリーから SanSan を追加するには、次の手順を実行します。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。

	![Active Directory][1]

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![アプリケーション][2]

4. ページの下部にある **[追加]** をクリックします。

	![アプリケーション][3]

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。

	![アプリケーション][4]

6. 検索ボックスに、「**SanSan**」と入力します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_01.png)

7. 結果ウィンドウで **[SanSan]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_06.png)

##  Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SanSan で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SanSan ユーザーが Azure AD で認識されている必要があります。言い換えると、Azure AD ユーザーと SanSan の関連ユーザーの間で、リンク関係が確立されている必要があります。このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を SanSan の **[Username (ユーザー名)]** の値として割り当てます。

SanSan で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[SanSan のテスト ユーザーの作成](#creating-an-sansan-test-user)** - SanSan で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
5. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### Azure AD シングル サインオンの構成

このセクションでは、クラシック ポータルで Azure AD のシングル サインオンを有効にして、SanSan アプリケーションでシングル サインオンを構成します。


**SanSan で Azure AD シングル サインオンを構成するには、次の手順に従います。**


1. Azure クラシック ポータルの **SanSan** アプリケーション統合ページで [シングル サインオンの構成] をクリックし、[シングル サインオンの構成] ダイアログを開きます。

	![Configure Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png)

2. **[ユーザーの SanSan へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
 	
	![Configure Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_03.png)

3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。

	![Configure Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_04.png)

	a.**[サインオン URL]** ボックスに、次の形式で URL を入力します。

    | 環境 | URL |
    | :--                     | :-- |
    | PC Web | `https://ap.sansan.com/v/saml2/<company name>/acs`|
    | ネイティブ モバイル アプリ | `https://internal.api.sansan.com/saml2/<company name>/acs` |
    | モバイル ブラウザーの設定 | `https://ap.sansan.com/s/saml2/<company name>/acs` |


	b.**[識別子]** ボックスに、次のパターンを使用して URL を入力します。

    | 環境 | URL |
    | :--                     | :-- |
    | PC Web | `https://ap.sansan.com/v/saml2/<company name>`|
    | ネイティブ モバイル アプリ | `https://internal.api.sansan.com/saml2/<company name>` |
    | モバイル ブラウザーの設定 | `https://ap.sansan.com/s/saml2/<company name>` |


    c.**[次へ]** をクリックします。

4. **[SanSan でのシングル サインオンの構成]** ページで、次の手順を実行します。

	![Configure Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_05.png)

    a.**[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。

    b.**[次へ]** をクリックします。


5.  アプリケーション用に構成された SSO を入手するには、SanSan のサポート チームに問い合わせてください。SSO 構成のサポートを受けられます。次のものを情報として提供してください。

	• ダウンロードした**証明書**

	• **ID プロバイダーの ID**

	• **SAML SSO URL**

	• **シングル サインアウト サービス URL**

> [AZURE.NOTE] PC のブラウザーの設定は、PC Web だけでなく、モバイル アプリおよびモバイル ブラウザーでも機能します。


6. クラシック ポータルで、シングル サインオンの構成確認を選択し、**[次へ]** をクリックします。
	
	![Azure AD Single Sign-On][10]

7. **[シングル サインオンの確認]** ページで **[完了]** をクリックします。
  	
	![Azure AD Single Sign-On][11]



### Azure AD のテスト ユーザーの作成

このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。ユーザーの一覧で **[Britta Simon]** を選択します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
	
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_09.png)

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. 上部のメニューで **[ユーザー]** をクリックして、ユーザーの一覧を表示します。
	
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_03.png)

4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_04.png)

5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。
 
	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_05.png)

    a.[ユーザーの種類] として [組織内の新しいユーザー] を選択します。

    b.**[ユーザー名]** ボックスに「**BrittaSimon**」と入力します。

    c.**[次へ]** をクリックします。

6.  **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_06.png)

    a.**[名]** ボックスに「**Britta**」と入力します。

    b.**[姓]** ボックスに「**Simon**」と入力します。

    c.**[表示名]** ボックスに「**Britta Simon**」と入力します。

    d.**[ロール]** 一覧で **[ユーザー]** を選択します。

    e.**[次へ]** をクリックします。

7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_07.png)

8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。

	![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sansan-tutorial/create_aaduser_08.png)

    a.**[新しいパスワード]** の値を書き留めます。

    b.**[完了]** をクリックします。



### SanSan のテスト ユーザーの作成

このセクションでは、SanSan で Britta Simon というユーザーを作成します。SanSan アプリケーションでは、SSO を実行する前に、ユーザーをアプリケーションにプロビジョニングする必要があります。


> [AZURE.NOTE] ユーザーを手動で作成する必要がある場合、またはユーザーのバッチを作成する必要がある場合は、SanSan のサポート チームにお問い合わせください。


### Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SanSan へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200]

**SanSan に Britta Simon を割り当てるには、次の手順を実行します。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。

	![ユーザーの割り当て][201]

2. アプリケーションの一覧で **[SanSan]** を選択します。

	![Configure Single Sign-On](./media/active-directory-saas-sansan-tutorial/tutorial_sansan_50.png)

1. 上部のメニューで **[ユーザー]** をクリックします。

	![ユーザーの割り当て][203]

1. ユーザーの一覧で **[Britta Simon]** を選択します。

2. 下部にあるツール バーで **[割り当て]** をクリックします。

	![ユーザーの割り当て][205]


### シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。アクセス パネルで [SanSan] タイルをクリックすると、SanSan アプリケーションに自動的にサインオンします。


## その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-sansan-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0727_2016-->