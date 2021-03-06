条件を追加すると、トリガーによって生成されるデータと関係ができます。次の手順に従って、 **[Salesforce - Get object (Salesforce - オブジェクトの取得)]** アクションを追加します。このアクションは、新しく潜在顧客が作成されるたびにデータを取得します。また、Office 365 コネクタを使用して電子メールを送信する [Salesforce - Get an object (Salesforce - オブジェクト取得)] からデータを使用する 2 番目のアクションを追加します。

この操作を設定するためには、次の情報を提供する必要があります。新しいファイルのプロパティの一部の情報として、トリガーによって生成されたデータを簡単に使用できることを確認します。

|ファイルプロパティの作成|説明|
|---|---|
|オブジェクトの種類|これは、対象となる Salesforce オブジェクトの型です。例として、潜在顧客、アカウントなどがあります。|
|オブジェクト ID|これは、オブジェクトの識別子を表します。|


1. **[アクションの追加]** のリンクを選択します。これにより検索ボックスが開き、行う操作について検索できます。この例では、Salesforce の操作が示されています。  
![Salesforce 操作イメージ 1](./media/connectors-create-api-salesforce/action-1.png)  
- 「*salesforce*」と入力して、Salesforce に関連するアクションを検索します。
- 実行するアクションとして、**[Salesforce - Get object (Salesforce - オブジェクトの取得)]** を選択します。**注**: これまでにその操作を行っていない場合、Salesforce のアカウントにアクセスするロジック アプリを承認するように求められます。  
![Salesforce 操作イメージ 2](./media/connectors-create-api-salesforce/action-2.png)  
- **[Get object (オブジェクトの取得)]** 制御を開きます。
- *"潜在顧客"* をオブジェクトの種類として選択します。
- **[オブジェクト ID]** 制御を選択します。
- **[...]** を選択して、アクションの入力として使用できるトークンの一覧を展開します。  
![Salesforce 操作イメージ 3](./media/connectors-create-api-salesforce/action-3.png)  
- **[Lead ID (潜在顧客 ID)]** 制御を選択して、開きます。  
![Salesforce 操作イメージ 4](./media/connectors-create-api-salesforce/action-4.png)  
- [Get object (オブジェクトの取得)] アクションが、このロジック アプリをトリガーした潜在顧客の ID と同じ ID を持つ潜在顧客を検索することを示して、 潜在顧客 ID のトークンがオブジェクト ID の制御下にあることに注意してください。  
![Salesforce 操作イメージ 5](./media/connectors-create-api-salesforce/action-5.png)  
- 作業内容を保存します。ロジック アプリに、[Get object (オブジェクトの取得)] アクションを追加しました。[Get object (オブジェクトの取得)] コントロールは、次のようになります。  
![Salesforce 操作イメージ 6](./media/connectors-create-api-salesforce/action-6.png)  

潜在顧客を取得する操作を追加すると、新しく作成された潜在顧客に何らかのアクションを取りたくなるかと思います。企業では、配布リストに新しい潜在顧客が作成されたことを通知する電子メールを送信します。Office 365 コネクタを使用して、Salesforce で新しい潜在顧客オブジェクトから関連する情報により電子メールを送信します。

1. **[アクションの追加]** を選択して、検索コントロールに「*email*」と入力します。これにより、電子メールの送受信に関連することに対するアクションがフィルター処理されます。
- **[Office 365 Outlook - Send an email (Office 365 Outlook - 電子メールの送信)]** のリストにある項目を選択します。Office 365 アカウントへの *"接続"* が確立されていない場合、Office 365 の資格情報を入力するよう求められます。入力が完了したら、 **[Send an email (電子メールの送信)]** 制御を開きます。  
![Salesforce 操作イメージ 7](./media/connectors-create-api-salesforce/action-7.png)  
- **[To]** コントロールで電子メールを送信するメール アドレスを入力します。
-  **[件名]** コントロールで、*<新しく作成した新規顧客>* を入力し、*"会社"* トークンを選択します。Salesforce で作成した新しい潜在顧客から*"会社"* フィールドを表示します。
-  **本文** コントロールで、新しい潜在顧客のオブジェクトからのトークンのいずれかを選択し、電子メールの本文に表示するには任意のテキストを入力することもできます。次に例を示します。  
![Salesforce 操作イメージ 8](./media/connectors-create-api-salesforce/action-8.png)  
- ワークフローを保存します。

これで終了です。ロジック アプリが完成しました。

ここで、ロジック アプリをテストできます。 Salesforce で、作成した条件を満たす新しい潜在顧客を作成します。このチュートリアルに完全に従うと、電子メール アドレスに「*amazon.com*」を含む潜在顧客を作成します。数秒後に、ロジック アプリをトリガーする必要があり、結果は以下のような感じになります。  
![Salesforce 操作イメージ 9](./media/connectors-create-api-salesforce/action-9.png)  

<!---HONumber=AcomDC_0727_2016-->
