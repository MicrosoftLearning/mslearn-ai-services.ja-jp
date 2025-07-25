---
lab:
  title: Azure AI サービスのセキュリティを管理する
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI サービスのセキュリティを管理する

セキュリティはどのアプリケーションにとっても重要な考慮事項であり、開発者は、Azure AI サービスなどのリソースへのアクセスがそれを必要とする人だけに制限されていることを確認する必要があります。

Azure AI サービスへのアクセスは通常、認証キーを介して制御されます。認証キーは、Azure AI サービス リソースを最初に作成したときに生成されます。

## Visual Studio Code でリポジトリをクローンする

Visual Studio Code を使用してコードを開発します。 アプリのコード ファイルは、GitHub リポジトリで提供されています。

> **ヒント**: 最近既に **mslearn-ai-services** リポジトリをクローンした場合は、Visual Studio Code で開きます。 それ以外の場合は、次の手順に従って開発環境に複製します。

1. Visual Studio Code を起動します。
2. パレットを開き (SHIFT+CTRL+P)、**Git:Clone** コマンドを実行して、`https://github.com/MicrosoftLearning/mslearn-ai-services` リポジトリをローカル フォルダーに複製します (どのフォルダーでも問題ありません)。
3. リポジトリを複製したら、Visual Studio Code でフォルダーを開きます。
4. 必要に応じて、リポジトリ内の C# コード プロジェクトをサポートするために追加のファイルがインストールされるまで待ちます

    > **注**: ビルドとデバッグに必要なアセットを追加するように求めるプロンプトが表示された場合は、**[今はしない]** を選択します。

5. `Labfiles/02-ai-services-security` フォルダーを展開します。

C# と Python の両方のコードが用意されています。 目的の言語のフォルダーを展開します。

## Azure AI サービス リソースをプロビジョニングする

サブスクリプションに **Azure AI サービス** リソースがまだない場合は、プロビジョニングする必要があります。

1. Azure portal (`https://portal.azure.com`) を開き、ご利用の Azure サブスクリプションに関連付けられている Microsoft アカウントを使用してサインインします。
2. 上部の検索バーで「*Azure AI サービス*」を検索し、**[Azure AI サービス マルチサービス アカウント]** を選択し、次の設定でリソースを作成します。
    - **[サブスクリプション]**:"*ご自身の Azure サブスクリプション*"
    - **リソース グループ**: *リソース グループを選択または作成します (制限付きサブスクリプションを使用している場合は、新しいリソース グループを作成する権限がないことがあります。提供されているものを使ってください)*
    - **[リージョン]**: 使用できるリージョンを選択します**
    - **[名前]**: *一意の名前を入力します*
    - **価格レベル**: Standard S0
3. 必要なチェック ボックスをオンにして、リソースを作成します。
4. デプロイが完了するまで待ち、デプロイの詳細を表示します。

## 認証キーの管理

Azure AI サービス リソースを作成したときに、2 つの認証キーが生成されました。 これらは、Azure portal で、または Azure コマンド ライン インターフェイス (CLI) を使用して管理できます。 

1. 認証キーとエンドポイントを取得する方法を 1 つ選択します。 

    **Azure portal の使用**: Azure portal で Azure AI サービス リソースに移動し、その **[キーとエンドポイント]** ページを表示します。 このページには、リソースに接続して、開発したアプリケーションからリソースを使用するために必要な情報が含まれています。 具体的な内容は次のとおりです。
    - クライアント アプリケーションが要求を送信できる HTTP *エンドポイント*。
    - 認証に使用できる 2 つの *キー* (クライアント アプリケーションはどちらのキーも使用できます。 一般的な方法は、1 つを開発用に、もう 1 つを本番用に使用する方法です。 開発者が作業を終了した後、開発キーを簡単に再生成して、継続的なアクセスを防ぐことができます)。
    - リソースがホストされている *場所*。 これは、一部の (すべてではない) API へのリクエストに必要です。

    **コマンド ラインの使用**: または、次のコマンドを使用して、Azure AI サービス キーの一覧を取得できます。 Visual Studio Code で、新しいターミナルを開きます。 これで、次のコマンドを使用して、*&lt;resourceName&gt;* を Azure AI サービス リソースの名前に置き換え、*&lt;resourceGroup&gt;* を、作成したリソース グループの名前に置き換えます。

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

    このコマンドは、Azure AI サービス リソースのキーのリストを返します。**key1** と **key2** という名前の 2 つのキーがあります。

    > **ヒント**: Azure CLI をまだ認証していない場合は、まず `az login` を実行してアカウントにサインインします。

2. Azure AI サービス をテストするには、HTTP 要求に **curl** コマンド ライン ツールを使用できます。 **02-ai-services-security** フォルダーで **rest-test.cmd** を開き、そこに含まれる **curl** コマンド (以下に表示) を編集し、*&lt;yourEndpoint&gt;* と *&lt;yourKey&gt;* をお使いのエンドポイント URI と **Key1** キーに置き換えて、Azure AI サービス リソースで Analyze Text API を使います。

    ```bash
    curl -X POST "<yourEndpoint>/language/:analyze-text?api-version=2023-04-01" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <your-key>" --data-ascii "{'analysisInput':{'documents':[{'id':1,'text':'hello'}]}, 'kind': 'LanguageDetection'}"
    ```

3. 変更を保存。 ターミナルで、"02-ai-services-security" フォルダーに移動します。 (**注**: エクスプローラーで "02-ai-services-security" フォルダーを右クリックし、 *統合ターミナルで開く*) を選択すると、これを行うことができます)。 次に、次のコマンドを実行します。

    ```
    ./rest-test.cmd
    ```

このコマンドは、入力データで検出された言語に関する情報を含む JSON ドキュメントを返します (これは英語でなければなりません)。

4. キーが危険にさらされた場合、またはキーを持っている開発者がアクセスを必要としなくなった場合は、ポータルで、または Azure CLI を使用してキーを再生成できます。 次のコマンドを実行して、**key1** キーを再生成します (リソースの *&lt;resourceName&gt;* と *&lt;resourceGroup&gt;* を置き換えます)。

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Azure AI サービス リソースのキーのリストが返されます。**key1** は前回取得したときから変更されていることに注意してください。

5. 古いキーを使って **rest-test** コマンドを再実行し (キーボードの **^** 方向キーを使って前のコマンドを切り替えることができます)、失敗することを確認します。
6. **rest-test.cmd** の *curl* コマンドを編集して、キーを新しい **key1** 値に置き換え、変更を保存します。 次に、**rest-test** コマンドを再実行し、成功することを確認します。

> **ヒント**: この演習では、 **--resource-group** などの Azure CLI パラメーターのフルネームを使用しました。  **-g** などの短い代替手段を使用して、コマンドの冗長性を低くすることもできます (ただし、理解が少し難しくなります)。  [Azure AI サービス CLI コマンド リファレンス](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest)には、各 Azure AI サービス CLI コマンドのパラメーター オプションがリストされています。

## Azure Key Vault を使用した安全なキー アクセス

認証にキーを使し、Azure AI サービスを利用するアプリケーションを開発できます。 ただし、これは、アプリケーションコードがキーを取得できる必要があることを意味します。 1 つのオプションは、アプリケーションがデプロイされている環境変数または構成ファイルにキーを格納することですが、このアプローチでは、キーが不正アクセスに対して脆弱なままになります。 Azure でアプリケーションを開発する場合のより良いアプローチは、キーを Azure Key Vault に安全に保存し、*マネージド ID* (つまり、アプリケーション自体が使用するユーザー アカウント) を介してキーへのアクセスを提供することです。

### Key Vault を作成してシークレットを追加する

最初に、キー コンテナ―を作成して Azure AI サービス キーの*シークレット*を追加する必要があります。

キー コンテナーを作成してシークレットを追加する方法のいずれかを選択します。

#### Azure Portal の使用
1. Azure AI サービス リソースの **key1** 値をメモします (またはクリップボードにコピーします)。
2. Azure portal の **[ホーム]** ページで、 **[&#65291; リソースの作成]** ボタンを選択し、*Key Vault* を検索して、次の設定で **Key Vault** リソースを作成します。

    - **[基本]** タブ
        - **[サブスクリプション]**:"*ご自身の Azure サブスクリプション*"
        - **リソース グループ**: *Azure AI サービス リソースと同じリソース グループ*
        - **キー コンテナー名**: *一意の名前を入力します*
        - **リージョン**: *Azure AI サービス リソースと同じリージョン*
        - **価格レベル**: Standard
    
    - **[構成にアクセスする]** タブ
        -  **[アクセス許可モデル]** : コンテナー アクセス ポリシー
        - 「**アクセス ポリシー**」セクションまで下にスクロールし、左側の チェック ボックスを使用してユーザーを選択します。 次に、**[確認および作成]** を選び、**[作成]** を選んでリソースを作成します。
     
3. デプロイが完了するのを待ってから、Key Vault リソースに移動します。
4. 左側のナビゲーション ウィンドウで、 **[シークレット]** ([オブジェクト] セクションにあります) を選択します。
5. **[+生成/インポート]** を選択し、次の設定で新しいシークレットを追加します。
    - **アップロード オプション**: 手動
    - **[名前]**: AI-Services-Key (後でこの名前に基づいてシークレットを取得するコードを実行するため、これを正確に一致させることが重要です)**
    - **シークレット値**: *自分の **key1** Azure AI サービス キー*
6. **［作成］** を選択します

#### Azure CLI の使用
または、Azure CLI を使用してキー コンテナーを作成し、シークレットを追加することもできます。

1. Visual Studio Code でターミナルを開きます。
2. 次のコマンドを実行して Key Vault を作成します。その際に、`<keyVaultName>`、`<resourceGroup>`、`<location>` を、目的のキー コンテナー名、リソース グループ名、Azure リージョン (たとえば、`eastus`) に置き換えます。

    ```
    az keyvault create \
      --name <key-vault-name> \
      --resource-group <resource-group-name> \
      --location <region> \
      --sku standard \
      --enable-rbac-authorization false
    ```
    フラグ `--enable-rbac-authorization false` を指定すると、アクセス許可モデルは "コンテナー アクセス ポリシー" (既定値) に設定されます。

3. Azure AI サービス キーをシークレットとしてキー コンテナーに追加します。 `<keyVaultName>` をキー コンテナー名に、`<your-key1-value>` を Azure AI サービス key1 の値に置き換えます。

    ```
    az keyvault secret set \
    --vault-name <key-vault-name> \
    --name AI-Services-Key \
    --value <your-azure-ai-services-key>
    ```

これで、キー コンテナーが作成され、Azure AI サービス キーが `AI-Services-Key` というシークレットとして保存されました。

### サービス プリンシパルの作成

Key Vault 内のシークレットにアクセスするには、アプリケーションはシークレットにアクセスできるサービス プリンシパルを使用する必要があります。 Azure コマンド ライン インターフェイス (CLI) を使用して、サービス プリンシパルを作成し、そのオブジェクト ID を検索し、Azure Vault のシークレットへのアクセスを許可します。

1. 次の Azure CLI コマンドを実行します。その際に *&lt;spName&gt;* はアプリケーション ID に適した一意の名前に置き換えます (たとえば、*ai-app* の末尾に自分のイニシャルを追加します。名前はテナント内で一意である必要があります)。 また、*&lt;subscriptionId&gt;* と *&lt;resourceGroup&gt;* を、サブスクリプション ID と、Azure AI サービスおよび キー コンテナ リソースを含むリソース グループの正しい値に置き換えます。

    > **ヒント**: サブスクリプション ID が不明な場合は、**az account show** コマンドを使用してサブスクリプション情報を取得します。サブスクリプション ID は出力の **id** 属性です。 既存のオブジェクトに関するエラーが表示される場合は、別の一意の名前を選択してください。

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

このコマンドの出力には、新しいサービス プリンシパルに関する情報が含まれます。 次のようになります。

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "api://ai-app-",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

**appId**、**password**、**tenant** の値をメモします。後で必要になります (このターミナルを閉じると、パスワードを取得できなくなります。したがって、ここで値をメモすることが重要です。出力をローカル コンピューター上の新しいテキスト ファイルに貼り付けて、後で必要な値を確実に見つけられるようにすることができます)。

2. サービス プリンシパルの**オブジェクト ID** を取得するには、次の Azure CLI コマンドを実行し、 *&lt;appId&gt;* をお使いのサービス プリンシパルのアプリ ID の値に置き換えます。

    ```
    az ad sp show --id <appId>
    ```

3. 応答で返された json の `id` 値をコピーします。 
3. 新しいサービス プリンシパルに Key Vault のシークレットにアクセスするためのアクセス許可を割り当てるには、次の Azure CLI コマンドを実行し、*&lt;keyVaultName&gt;* を Azure Key Vault リソースの名前に置き換え、*&lt;objectId&gt;* を先ほどコピーしたサービス プリンシパルの ID の値に置き換えます。

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### アプリケーションでサービス プリンシパルを使用する

アプリケーションでサービス プリンシパル ID を使用する準備が整ったら、キー コンテナにシークレット Azure AI サービス キーを入力し、それを使用して Azure AI サービス リソースに接続します。

> **注**: この演習では、サービス プリンシパルの資格情報をアプリケーション構成に格納し、それらを使用して、アプリケーション コードで **ClientSecretCredential** IDを認証します。 これは開発とテストには問題ありませんが、実際の運用アプリケーションでは、管理者は *マネージド ID* をアプリケーションに割り当て、パスワードをキャッシュしたり保存したりせずに、サービス プリンシパル ID を使用してリソースにアクセスします。

1. ターミナルで、`cd C-Sharp` または `cd Python` を実行して、言語設定に応じて **C-Sharp** または **Python** フォルダーに切り替えます。 次に、`cd keyvault_client` を実行してアプリ フォルダーに移動します。
2. 言語設定に応じて適切なコマンドを実行して、Azure Key Vault と Text Analytics API に使うために必要なパッケージを Azure AI サービス リソースにインストールします。

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.3.0
    dotnet add package Azure.Identity --version 1.12.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.6.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.3.0
    pip install azure-identity==1.17.1
    pip install azure-keyvault-secrets==4.8.0
    ```

3. **keyvault-client** フォルダーの内容を表示し、構成設定用のファイルが含まれていることに注意してください
    - **C#** : appsettings.json
    - **Python**: .env

    構成ファイルを開き、構成値を更新します。次の設定を反映するために含まれます
    
    - Azure AI サービス リソースの**エンドポイント**
    - **Azure Key Vault** リソースの名前
    - サービス プリンシパルの**テナント**
    - サービス プリンシパルの **appId**
    - サービス プリンシパルの**パスワード**

     **Ctrl + S** キーを押して、変更内容を保存します。
4. **keyvault-client** フォルダーには、クライアント アプリケーションのコード ファイルが含まれていることに注意してください。

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    コード ファイルを開き、含まれているコードを確認して、次の詳細に注意してください。
    - インストールした SDK の名前空間がインポートされます
    - **Main** 関数のコードはアプリケーション構成設定を取得し、次にサービス プリンシパルの資格情報を使用して、キー コンテナから Azure AI サービス キーを取得します。
    - **GetLanguage** 関数は、SDK を使用してサービスのクライアントを作成し、クライアントを使用して入力されたテキストの言語を検出します。
5. 次のコマンドを入力して、このプログラムを実行します。

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. プロンプトが表示されたら、テキストを入力し、サービスによって検出された言語を確認します。 たとえば、「Hello」、「Bonjour」、「Gracias」と入力してみてください。
7. アプリケーションのテストが終了したら、「quit」と入力してプログラムを終了します。

## リソースをクリーンアップする

このラボで作成した Azure リソースを他のトレーニング モジュールに使用していない場合は、それらを削除して、追加料金が発生しないようにすることができます。

1. `https://portal.azure.com` で Azure portal を開き、上部の検索バーで、このラボで作成したリソースを検索します。

2. [リソース] ページで **[削除]** を選択し、指示に従ってリソースを削除します。 または、リソース グループ全体を削除して、すべてのリソースを同時にクリーンアップすることもできます。

## 詳細情報

Azuer AI サービスのセキュリティ保護の詳細については、「[Azure AI サービスのセキュリティのドキュメント](https://docs.microsoft.com/azure/ai-services/security-features)」を参照してください。
