---
lab:
  title: Azure AI サービスのコンテナーの使用
  module: Module 2 - Developing AI Apps with Azure AI Services
---

# Azure AI サービスのコンテナーの使用

Azure でホストされている Azure AI サービスを使用すると、アプリケーション開発者は、Microsoft が管理するスケーラブルなサービスの利点を得ながら、独自のコードのインフラストラクチャに集中できます。 ただし、多くのシナリオでは、組織はサービス インフラストラクチャとサービス間で受け渡されるデータをより細かく制御する必要があります。

Azure AI サービス API の多くは、*コンテナー*にパッケージ化してデプロイできるため、組織は独自のインフラストラクチャで Azure AI サービスをホストできます。たとえば、ローカル Docker サーバー、Azure Container Instances または Azure Kubernetes Services クラスターなどです。 コンテナー化された Azure AI サービスは、課金をサポートするために Azure ベースの Azure AI サービス アカウントと通信する必要があります。ただし、アプリケーション データはバックエンド サービスに渡されず、組織はコンテナーの展開構成をより細かく制御できるため、認証、スケーラビリティ、その他の考慮事項に対応したカスタム ソリューションを実現できます。

## Visual Studio Code でリポジトリをクローンする

Visual Studio Code を使用してコードを開発します。 アプリのコード ファイルは、GitHub リポジトリで提供されています。

> **ヒント**: 既に **mslearn-ai-services** リポジトリをクローンしている場合は、Visual Studio Code で開きます。 それ以外の場合は、次の手順に従って開発環境に複製します。

1. Visual Studio Code を起動します。
2. パレットを開き (SHIFT+CTRL+P)、**Git:Clone** コマンドを実行して、`https://github.com/MicrosoftLearning/mslearn-ai-services` リポジトリをローカル フォルダーに複製します (どのフォルダーでも問題ありません)。
3. リポジトリを複製したら、Visual Studio Code でフォルダーを開きます。
4. 必要に応じて、リポジトリ内の C# コード プロジェクトをサポートするために追加のファイルがインストールされるまで待ちます

    > **注**: ビルドとデバッグに必要なアセットを追加するように求めるプロンプトが表示された場合は、**[今はしない]** を選択します。

5. `Labfiles/04-use-a-container` フォルダーを展開します。

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
5. リソースがデプロイされたら、そこに移動して、その **[キーとエンドポイント]** ページを表示します。 次の手順では、このページのエンドポイントとキーの 1 つが必要になります。

## 感情分析コンテナーをデプロイして実行する

一般的に使用される多くの Azure AI サービス API は、コンテナー イメージで利用できます。 完全なリストについては、「[Azure AI サービスのドキュメント](https://learn.microsoft.com/en-us/azure/ai-services/cognitive-services-container-support#containers-in-azure-ai-services)」を確認してください。 この演習では、Text Analytics *感情分析* API のコンテナー イメージを使用します。ただし、原則は利用可能なすべての画像で同じです。

1. Azure portal の **ホーム** ページで、 **[&#65291;リソースの作成]** ボタンを選択し、*Container Instances* を検索して、次の設定で **Container Instances** リソースを作成します。

    - **[基本]** :
        - **[サブスクリプション]**:"*ご自身の Azure サブスクリプション*"
        - **リソース グループ**: *Azure AI サービス リソースを含むリソース グループを選択する*
        - **コンピューティング名**: "*一意の名前を入力します*"
        - **[リージョン]**: 使用できるリージョンを選択します**
        - **可用性ゾーン**: なし
        - **SKU**: Standard
        - **イメージのソース**:その他のレジストリ
        - **イメージの種類**: パブリック
        - **イメージ**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest`
        - **OS の種類**: Linux
        - **サイズ**: 1 vCPU、8 GB メモリ
    - **[ネットワーク]**:
        - **ネットワークの種類**: パブリック
        - **DNS 名ラベル**: "*コンテナー エンドポイントの一意の名前を入力します*"
        - **ポート**: "*TCP ポートを 80 から **5000** に変更します*"
    - **詳細**:
        - **再起動ポリシー**: 失敗時
        - **環境変数**:

            | セキュアとしてマーク | Key | Value |
            | -------------- | --- | ----- |
            | はい | `ApiKey` | *Azure AI サービス リソースのいずれかのキー* |
            | はい | `Billing` | *Azure AI サービス リソースのエンドポイント URI* |
            | いいえ | `Eula` | `accept` |

        - **コマンドのオーバーライド**: [ ]
        - **キー管理**: Microsoft マネージド キー (MMK)
    - **タグ**:
        - "*タグを追加しないでください*"

2. **[確認および作成]** を選択し、**[作成]** を選択します。 デプロイが完了するまで待ち、デプロイされたリソースに移動します。
    > **注**: Azure AI コンテナーを Azure Container Instances にデプロイすると、使用する準備が整うまでに通常 5 - 10 分 (プロビジョニング) かかります。
3. **[概要]** ページで、コンテナー インスタンス リソースの次のプロパティを確認します。
    - **状態**: "*実行中*" である必要があります。
    - **IP アドレス**: これは、コンテナー インスタンスへのアクセスに使用できるパブリック IP アドレスです。
    - **FQDN**: これはコンテナー インスタンス リソースの "*完全修飾ドメイン名*" です。これを使用して、IP アドレスの代わりにコンテナー インスタンスにアクセスできます。

    > **注**: この演習では、感情を分析するための Azure AI サービス コンテナー イメージを Azure Container Instances (ACI) リソースにデプロイしました。 同様のアプローチを使用して、次のコマンド (1 行) を実行して感情分析コンテナーをローカルの Docker インスタンスにデプロイすることにより、ご自分のコンピューターまたはネットワーク上の *[Docker](https://www.docker.com/products/docker-desktop)* ホストにデプロイして、*&lt;yourEndpoint&gt;* と *&lt;yourKey&gt;* をエンドポイント URI と Azure AI サービス リソースのいずれかのキーに置き換えます。
    > このコマンドはローカル マシン上の画像を検索しますが見つからない場合は、*mcr.microsoft.com* イメージ レジストリからプルされ、Docker インスタンスにデプロイされます。 デプロイが完了すると、コンテナーが起動し、ポート 5000 で着信要求をリッスンします。

    ```
    docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/sentiment:latest Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    ```

## コンテナーを使用する

1. エディターで **rest-test.cmd** を開き、それに含まれる **curl** コマンドを編集し (以下を参照)、*&lt;your_ACI_IP_address_or_FQDN&gt;* を実際のコンテナーの IP アドレスまたは FQDN に置き換えます。

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.1/sentiment" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'The performance was amazing! The sound could have been clearer.'},{'id':2,'text':'The food and service were unacceptable. While the host was nice, the waiter was rude and food was cold.'}]}"
    ```

2. **Ctrl+S** キーを押して、スクリプトに対する変更を保存します。 Azure AI サービスのエンドポイントまたはキーを指定する必要はないことに注意してください。要求はコンテナー化されたサービスによって処理されます。 次に、コンテナーは Azure のサービスと定期的に通信して、請求の使用状況を報告しますが、要求データは送信しません。
3. 次のコマンドを入力してスクリプトを実行します。

    ```
    ./rest-test.cmd
    ```

4. コマンドが 2 つの入力ドキュメント (肯定的および否定的なものがその順序である必要があります) で検出された感情に関する情報を含む JSON ドキュメントを返すことを確認します。

## クリーンアップ

Container Instances の実験が終了したら、それを削除する必要があります。

1. Azure portal で、この演習用のリソースを作成したリソース グループを開きます。
2. Container Instances リソースを選択して削除します。

## リソースをクリーンアップする

このラボで作成した Azure リソースを他のトレーニング モジュールに使用していない場合は、それらを削除して、追加料金が発生しないようにすることができます。

1. `https://portal.azure.com` で Azure portal を開き、上部の検索バーで、このラボで作成したリソースを検索します。

2. [リソース] ページで **[削除]** を選択し、指示に従ってリソースを削除します。 または、リソース グループ全体を削除して、すべてのリソースを同時にクリーンアップすることもできます。

## 詳細情報

Azure AI サービスのコンテナー化の詳細については、「[Azure AI サービス コンテナーのドキュメント](https://learn.microsoft.com/azure/ai-services/cognitive-services-container-support)」を参照してください。
