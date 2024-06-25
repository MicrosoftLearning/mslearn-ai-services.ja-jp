---
lab:
  title: ラボ環境のセットアップ
  module: Setup
---

# ラボ環境のセットアップ

演習は、ホストされたラボ環境で実行することを意図しています。 ご自分のコンピューターで実行する場合は、次のソフトウェアをインストールしてください。 独自の環境を使用すると、予期しないダイアログや動作が発生する場合があります。 さまざまなローカル構成が考えられるので、ご自分の環境で問題が発生しても、コース チームが問題をサポートすることはできません。

> **注**:以下の手順は、Windows 11 コンピューター用です。 また、Linux または MacOS も使用できます。 選択した OS に対してラボの手順を調整する必要がある場合があります。

### 基本オペレーティング システム (Windows 11)

#### Windows 11

Windows 11 をインストールし、すべての更新プログラムを適用します。

#### Edge

[Edge (Chromium)](https://microsoft.com/edge) をインストールします

### .NET Core SDK

1. https://dotnet.microsoft.com/download からダウンロードしてインストールします (ランタイムだけでなく、.NET Core SDK をダウンロードします)。 このコースのラボを自分のコンピューターで実行している場合は、.NET 7.0 を所有している必要があります。 このラボは .NET 7.0 に対してテストされていますが、現在 7.0 はサポート対象外です。 8.0 を使用できますが、いくつかの小さな問題が発生する可能性があります。 ホストされている環境を使用することを強くお勧めします。

### C++ 再頒布可能パッケージ

1. https://aka.ms/vs/16/release/vc_redist.x64.exe から Visual C++ 再頒布可能パッケージ (x64) をダウンロードしてインストールします

### Node.JS

1. https://nodejs.org/en/download/ から最新の LTS バージョンをダウンロードします 
2. 既定のオプションを使用してインストールします

### Python (および必要なパッケージ)

1. https://docs.conda.io/en/latest/miniconda.html からバージョン3.11 をダウンロードします 
2. セットアップを実行してインストールします - **重要**: Miniconda を PATH 変数に追加し、Miniconda を既定の Python 環境として登録するオプションを選択します。
3. インストール後、Anaconda プロンプトを開き、次のコマンドを入力してパッケージをインストールします。 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Azure CLI

1. https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest からダウンロードします 
2. 既定のオプションを使用してインストールします

### Git

1. 既定のオプションを使用して https://git-scm.com/download.html からダウンロードしてインストールします


### Visual Studio Code (および拡張機能)

1. https://code.visualstudio.com/Download からダウンロードします 
2. 既定のオプションを使用してインストールします 
3. インストール後、Visual Studio Code を起動し、 **[拡張機能]** タブ (CTRL + SHIFT + X) で、Microsoft から次の拡張機能を検索してインストールします。
    - Python
    - C#
    - Azure Functions
    - PowerShell
