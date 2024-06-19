---
lab:
  title: 事前構築済みの Document Intelligence モデルを使う
  module: Module 6 - Document Intelligence
---

# 事前構築済みの Document Intelligence モデルを使う

この演習では、Azure サブスクリプションで Azure AI Document Intelligence リソースを設定します。 Azure AI Document Intelligence Studio と C# または Python の両方を使って、分析のためにフォームをそのリソースに送信します。

## Azure AI Document Intelligence リソースを作成する

Azure AI Document Intelligence サービスを呼び出すには、そのサービスを Azure でホストするためのリソースを先に作成する必要があります。

1. ブラウザー タブで Azure portal ([https://portal.azure.com](https://portal.azure.com?azure-portal=true)) を開き、自分の Azure サブスクリプションに関連付けられている Microsoft アカウントを使用してサインインします。
1. Azure portal のホーム ページで、上部の検索ボックスに「**Document Intelligence**」と入力し、**Enter** キーを押します。
1. **[Document Intelligence]** ページで、**[作成]** を選択します。
1. **[Document Intelligence の作成]** ページで、次のコマンドを使用してリソースを構成します。
    - **サブスクリプション**: Azure サブスクリプション。
    - **リソース グループ**: *DocIntelligenceResources* などの 一意の名前を持つリソース グループを選択または作成します。
    - **リージョン**: 最寄りのリージョンを選択します。
    - **名前**: グローバルに一意の名前を入力します。
    - **価格レベル**: Free F0** を選択**します (Free レベルを利用できない場合は、**Standard S0** を選択します)。
1. 次に **[確認および作成]**、**[作成]** の順に選択します。 Azure が Azure AI Document Intelligence リソースを作成するまで待ちます。
1. デプロイが完了したら、**[リソースに移動]** を選択します。 この演習の残りの部分のため、このページを開いたままにします。

## 読み取りモデルを使用する

まず、**Azure AI Document Intelligence Studio** と読み取りモデルを使って、複数の言語が使われているドキュメントを分析してみましょう。 分析を実行するには、作成したリソースに Azure AI Document Intelligence Studio を接続します。

1. 新しいブラウザー タブを開き、**Azure AI Document Intelligence Studio** ([https://documentintelligence.ai.azure.com/studio](https://documentintelligence.ai.azure.com/studio)) に移動します。
1. **[ドキュメントの分析]** で **[読み取り]** タイルを選びます。
1. アカウントへのサインインを求められた場合は、Azure 資格情報を使用します。
1. 使用する Azure AI Document Intelligence リソースを確認するメッセージが表示されたら、Azure AI Document Intelligence リソースの作成時に使用したサブスクリプションとリソース名を選択します。
1. 左側のドキュメントの一覧で、**read-german.pdf** を選択します。

    ![Azure AI Document Intelligence Studio の [読み取り] ページを示すスクリーンショット。](../media/read-german-sample.png#lightbox)

1. 左上で **[分析オプション]** を選択したら、**[分析オプション]** ウィンドウの **[言語]** チェックボックス (**[オプション検出]** の下) をオンにし、**[保存]** をクリックします。 
1. 左上の **[分析の実行]** を選びます。
1. 分析が完了すると、画像から抽出されたテキストが右側の **[コンテンツ]** タブに表示されます。このテキストを確認し、元の画像のテキストと比較して正確さを調べます。
1. **[結果]** タブを選びます。このタブには、抽出された JSON コードが表示されます。 
1. **[結果]** タブで JSON コードの一番下までスクロールします。読み取りモデルによって各スパンの言語が検出されていることに注意してください。 ほとんどのスパンはドイツ語 (言語コード `de`) ですが、スパン内で他の言語コードが見つかります (たとえば、英語 - 言語コード `en` が最後のスパンの 1 つにあります)。

    ![Azure AI Document Intelligence Studio の読み取りモデルの結果で 2 つのスパンの言語の検出を示すスクリーンショット。](../media/language-detection.png#lightbox)

## Visual Studio Code でアプリを開発する準備をする

次に、Azure Document Intelligence Service SDK を使用するアプリを調べてみましょう。 Visual Studio Code を使用してアプリを開発します。 アプリのコード ファイルは、GitHub リポジトリで提供されています。

> **ヒント**: mslearn-ai-document-intelligence** リポジトリを**既に複製している場合は、Visual Studio Code で開きます。 それ以外の場合は、次の手順に従って開発環境に複製します。

1. Visual Studio Code を起動します。
1. パレットを開き (SHIFT+CTRL+P)、**Git:Clone** コマンドを実行して、`https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` リポジトリをローカル フォルダーに複製します (どのフォルダーでも問題ありません)。
1. リポジトリを複製したら、Visual Studio Code でフォルダーを開きます。
1. リポジトリ内の C# コード プロジェクトをサポートするために追加のファイルがインストールされるまで待ちます。

    > **注**: ビルドとデバッグに必要なアセットを追加するように求めるプロンプトが表示された場合は、**[今はしない]** を選択します。 *フォルダー内の Azure Functions プロジェクトが検出されたことを示す*メッセージが表示されたら、そのメッセージを安全に閉じることができます。

## アプリケーションを構成する

C# と Python の両方のアプリケーションと、Document Intelligence のテストに使用するサンプル PDF ファイルが提供されています。 どちらのアプリにも同じ機能があります。 最初に、Azure Document Intelligence リソースの使用を有効にするために、アプリケーションのいくつかの重要な部分を完成します。

1. 次の請求書を調べて、そのフィールドと値をいくつかメモしておきます。 この請求書をコードで分析します。

    ![請求書ドキュメントのサンプルを示すスクリーンショット。](../media/sample-invoice.png#lightbox)

1. Visual Studio Code の**エクスプローラー** ペインで、**Labfiles/01-prebuild-models** フォルダーを参照し、言語の設定に応じて **CSharp** または **Python** フォルダーを展開します。 各フォルダーには、Azure Document Intelligence 機能を統合するアプリの言語固有のファイルが含まれています。

1. コード ファイルを含む **CSharp** または **Python** フォルダーを右クリックし、**[統合ターミナルを開く]** を選択します。 次に、言語設定に応じて適切なコマンドを実行して、Azure Form Recognizer (Document Intelligence の以前の名前) SDK パッケージをインストールします。

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

## Azure Document Intelligence サービスを使用するコードを追加する

これで、SDK を使用して PDF ファイルを評価する準備が整いました。

1. Azure portal の Azure AI Document Intelligence の概要が表示されているブラウザー タブに切り替えます。 左ペインの *[リソース管理]* の下の **[キーとエンドポイント]** を選択します。 **[エンドポイント]** の値の右側にある **[クリップボードにコピー]** ボタンをクリックします。
1. **エクスプローラー** ペインの **CSharp** または **Python** フォルダーで、目的の言語のコード ファイルを開き、先ほどコピーした`<Endpoint URL>`文字列に置き換えます。

    **C#**: ***Program.cs***

    ```csharp
    string endpoint = "<Endpoint URL>";
    ```

    **Python**: ***document-analysis.py***

    ```python
    endpoint = "<Endpoint URL>"
    ```

1. Azure portal の Azure AI Document Intelligence の**キーとエンドポイント**が表示されているブラウザー タブに切り替えます。 **[キー 1]** の値の右側にある *[クリップボードにコピー]** ボタンをクリックします。
1. Visual Studio Code のコード ファイルで、次の行を見つけて、`<API Key>`先ほどコピーした文字列に置き換えます。

    **C#**

    ```csharp
    string apiKey = "<API Key>";
    ```

    **Python**

    ```python
    key = "<API Key>"
    ```

1. `Create the client` というコメントを見つけます。 その後、新しい行に次のコードを入力します。

    **C#**

    ```csharp
    var cred = new AzureKeyCredential(apiKey);
    var client = new DocumentAnalysisClient(new Uri(endpoint), cred);
    ```

    **Python**

    ```python
    document_analysis_client = DocumentAnalysisClient(
        endpoint=endpoint, credential=AzureKeyCredential(key)
    )
    ```

1. `Analyze the invoice` というコメントを見つけます。 その後、新しい行に次のコードを入力します。

    **C#**

    ```csharp
    AnalyzeDocumentOperation operation = await client.AnalyzeDocumentFromUriAsync(WaitUntil.Completed, "prebuilt-invoice", fileUri);
    ```

    **Python**

    ```python
    poller = document_analysis_client.begin_analyze_document_from_url(
        fileModelId, fileUri, locale=fileLocale
    )
    ```

1. `Display invoice information to the user` というコメントを見つけます。 その後、新しい行に次のコードを入力します。

    **C#**

    ```csharp
    AnalyzeResult result = operation.Value;
    
    foreach (AnalyzedDocument invoice in result.Documents)
    {
        if (invoice.Fields.TryGetValue("VendorName", out DocumentField? vendorNameField))
        {
            if (vendorNameField.FieldType == DocumentFieldType.String)
            {
                string vendorName = vendorNameField.Value.AsString();
                Console.WriteLine($"Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}.");
            }
        }
    ```

    **Python**

    ```python
    receipts = poller.result()
    
    for idx, receipt in enumerate(receipts.documents):
    
        vendor_name = receipt.fields.get("VendorName")
        if vendor_name:
            print(f"\nVendor Name: {vendor_name.value}, with confidence {vendor_name.confidence}.")
    ```

    > [!NOTE]
    > ベンダー名を表示するコードを追加しました。 スターター プロジェクトには、*顧客名*と*請求書の合計*を表示するコードも含まれています。

1. コード ファイルに加えた変更を保存します。

1. 対話型ターミナル ペインで、フォルダー コンテキストが優先言語のフォルダーであることを確認します。 その後、次のコマンドを入力してアプリケーションを作成します。

1. ***C# の場合のみ***、プロジェクトをビルドするには、次のコマンドを入力します。

    **C#:**

    ```powershell
    dotnet build
    ```

1. コードを実行するには、次のコマンドを入力します。

    **C#:**

    ```powershell
    dotnet run
    ```

    **Python**:

    ```powershell
    python document-analysis.py
    ```

プログラムにより、ベンダー名、顧客名、請求書合計と信頼度レベルが表示されます。 表示された値と、このセクションの最初で開いたサンプルの請求書を比べます。

## クリーンアップ

Azure リソースでの作業が完了したら、追加料金が発生しないように、[Azure portal](https://portal.azure.com/?azure-portal=true) 内のリソースを必ず削除してください。

## 詳細情報

Document Intelligence サービスの詳細については、[Document Intelligence のドキュメント](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)を参照してください。
