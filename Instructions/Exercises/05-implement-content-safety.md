---
lab:
  title: Azure AI Content Safety を実装する
---

# Azure AI Content Safety を実装する

この演習では、Content Safety リソースをプロビジョニングし、Azure AI Studio でリソースをテストし、コードでリソースをテストします。

## *Content Safety* リソースをプロビジョニングする

まだ所有していない場合は、Azure サブスクリプションの **Content Safety** リソースをプロビジョニングする必要があります。

1. Azure portal (`https://portal.azure.com`) を開き、ご利用の Azure サブスクリプションに関連付けられている Microsoft アカウントを使用してサインインします。
1. **[リソースの作成]** を選択します。
1. 検索フィールドで、**Content Safety** を検索します。 次に、検索結果の **[Azure AI Content Safety]** で **[作成]** を選択します。
1. 次の設定を使用してリソースをプロビジョニングします。
    - **[サブスクリプション]**: *お使いの Azure サブスクリプション*。
    - **リソース グループ**: *リソース グループを選択または作成します*。
    - **[リージョン]**: **[米国東部]** を選択します。
    - **[名前]**: *一意の名前を入力します*。
    - **価格レベル**: F0 が利用できない場合、**F0** (*Free*) または **S** (*Standard*) を選択します。
1. **[確認および作成]** を選び、**[作成]** を選んでリソースをプロビジョニングします。
1. デプロイが完了するまで待ってから、デプロイされたリソースに移動します。
1. 左側のナビゲーション バーで **[アクセス制御]** を選択し、**[+ 追加]** および **[ロールの割り当ての追加]** を選択します。
1. 下にスクロールして、**Cognitive Services ユーザー** ロールを選択し、**[次へ]** を選択します。
1. このロールにアカウントを追加し、**[レビュー + 割り当て]** を選択します。
1. 左側のナビゲーション バーで、**[リソース管理]** を選択し、**[キーとエンドポイント]** を選択します。 後でキーをコピーできるように、このページは開いたままにします。

## Azure AI Content Safety のプロンプト シールドを使用する

この演習では、Azure AI Studio を使用して、2 つのサンプル入力で Content Safety のプロンプト シールドをテストします。 1 つはユーザー プロンプトをシミュレートし、もう 1 つは安全でない可能性のあるテキストが埋め込まれたドキュメントをシミュレートします。

1. 別のブラウザー タブで [Azure AI Studio](https://ai.azure.com/explore/contentsafety) の Content Safety ページを開いてサインインします。
1. **[テキスト コンテンツのモデレート]** で、**[試してみる]** を選択します。
1. **[テキスト コンテンツのモデレート]** ページの **Azure AI サービス**で、前に作成した Content Safety リソースを選択します。
1. **[1 文で複数のリスク カテゴリ]** を選択します。 潜在的な問題については、ドキュメントのテキストを確認してください。
1. **[テストの実行]** を選択し、結果を確認します。
1. 必要に応じて、しきい値レベルを変更し、**[テストの実行]** を選択します。
1. 左側のナビゲーション バーで、 **[テキスト用に保護された素材の検出]** を選択します。
1. **[保護された歌詞]** を選択し、これらは公開済みの曲の歌詞であることに注意してください。
1. **[テストの実行]** を選択し、結果を確認します。
1. 左側のナビゲーション バーで、**[画像コンテンツのモデレート]** を選択します。
1. **[自傷コンテンツ]** を選択します。
1. AI Studio では、すべての画像が既定でぼやけています。 また、サンプル内の性的コンテンツは非常に穏健であることにも注意する必要があります。
1. **[テストの実行]** を選択し、結果を確認します。
1. 左側のナビゲーション バーで、**[プロンプト シールド]** を選択します
1. **[プロンプト シールド] ページ**の **[Azure AI サービス]** で、先ほど作成した Content Safety リソースを選択します。
1. **[プロンプトおよび攻撃コンテンツのドキュメント化]** を選択します。 潜在的な問題について、ユーザー プロンプトとドキュメント テキストを確認します。
1. **[テストの実行]** を選択します。
1. **[結果の表示]** で、ユーザー プロンプトとドキュメントの両方で脱獄攻撃が検出されたことを確認します。

    > [!TIP]
    > コードは、AI Studio のすべてのサンプルで使用できます。

1. **[次の手順]** の **[コードの表示]** で、**[コードの表示]** を選択します。 **サンプル コード** ウィンドウが表示されます。
1. 下矢印を使用して Python または C# を選択し、**[コピー]** を選択してサンプル コードをクリップボードにコピーします。
1. **サンプル コード**画面を閉じます。

### アプリケーションを構成する

次に、C# または Python でアプリケーションを作成します。

#### C#

##### 前提条件

* [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。
* [.NET 8](https://dotnet.microsoft.com/en-us/download/dotnet/8.0) がこの演習のターゲット フレームワークです。
* Visual Studio Code 用の [C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。

##### 設定

次の手順を実行して、演習用に Visual Studio Code を準備します。

1. Visual Studio Code を起動し、エクスプローラー ビューで **[コンソール アプリ]** を選択して **[.NET プロジェクトの作成]** をクリックします。
1. コンピューター上のフォルダーを選択し、プロジェクトに名前を付けます。 **[プロジェクトの作成]** を選択し、警告メッセージを確認します。
1. [エクスプローラー] ウィンドウで、ソリューション エクスプローラーを展開し、**[Program.cs]** を選択します。
1. **[実行]** -> **[デバッグなしで実行]** を選択して、プロジェクトをビルドして実行します。 
1. ソリューション エクスプローラーで、C# プロジェクトを右クリックし、**[NuGet パッケージの追加]** を選択します。
1. **Azure.AI.TextAnalytics** を検索し、最新バージョンを選択します。
1. 2 つ目の NuGet パッケージである **Microsoft.Extensions.Configuration.Json 8.0.0** を検索します。 これで、プロジェクト ファイルに 2 つの NuGet パッケージが一覧表示されます。

##### コードの追加

1. 前にコピーしたサンプル コードを **ItemGroup** セクションに貼り付けます。
1. 下にスクロールして、*[自分のサブスクリプションの _キーとエンドポイントに置き換える]* を探します。
1. Azure portal の [キーとエンドポイント] ページで、いずれかのキー (1 または 2) をコピーします。 この値を **<your_subscription_key>** に置き換えます。
1. Azure portal の [キーとエンドポイント] ページで、エンドポイントをコピーします。 この値をコードに貼り付けて、**<your_endpoint_value>** を置き換えます。
1. **Azure AI Studio** で、**ユーザー プロンプト**の値をコピーします。 これをコードに貼り付けて、**<test_user_prompt>** を置き換えます。
1. **<this_is_a_document_source>** まで下にスクロールし、このコード行を削除します。
1. **Azure AI Studio** で、**ドキュメント**の値をコピーします。
1. **<this_is_another_document_source>** まで下にスクロールし、ドキュメントの値を貼り付けます。
1. **[実行]** -> **[デバッグなしで実行]** を選択し、攻撃が検出されたことを確認します。 

#### Python

##### 前提条件

* [サポートされているプラットフォーム](https://code.visualstudio.com/docs/supporting/requirements#_platforms)のいずれかにインストールされた [Visual Studio Code](https://code.visualstudio.com/)。

* Visual Studio Code 用の [Python 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-python.python)がインストールされています。

* [要求モジュール](https://pypi.org/project/requests/)がインストールされています。

1. **.py** 拡張子を持つ新しい Python ファイルを作成し、適切な名前を付けます。
1. 前にコピーしたサンプル コードを貼り付けます。
1. 下にスクロールして、*[自分のサブスクリプションの _キーとエンドポイントに置き換える]* というタイトルのセクションを探します。
1. Azure portal の [キーとエンドポイント] ページで、いずれかのキー (1 または 2) をコピーします。 この値を **<your_subscription_key>** に置き換えます。
1. Azure portal の [キーとエンドポイント] ページで、エンドポイントをコピーします。 この値をコードに貼り付けて、**<your_endpoint_value>** を置き換えます。
1. **Azure AI Studio** で、**ユーザー プロンプト**の値をコピーします。 これをコードに貼り付けて、**<test_user_prompt>** を置き換えます。
1. **<this_is_a_document_source>** まで下にスクロールし、このコード行を削除します。
1. **Azure AI Studio** で、**ドキュメント**の値をコピーします。
1. **<this_is_another_document_source>** まで下にスクロールし、ドキュメントの値を貼り付けます。
1. ファイルの統合ターミナルから、次のようにプログラムを実行します。

    - `.\prompt-shield.py`

1. 攻撃が検出されたことを検証します。
1. 必要に応じて、さまざまなテスト コンテンツとドキュメント値を試すことができます。