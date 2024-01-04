---
lab:
  title: フォームからデータを抽出する
  module: Module 11 - Reading Text in Images and Documents
---

# フォームからデータを抽出する 

ある会社が現在、手動で注文シートを購入し、データベースにデータを入力することを従業員に求めているとします。 会社は AI サービスを活用してデータ入力プロセスを改善したいと考えています。 あなたは、フォームを読み取り、データベースを自動的に更新するために使用できる構造化データを生成する機械学習モデルを構築することを決定しました。

**Azure AI Document Intelligence** は、ユーザーが自動データ処理ソフトウェアを構築できるようにする Azure AI サービスです。 このソフトウェアは、光学式文字認識 (OCR) を使用して、フォーム ドキュメントからテキスト、キーと値のペア、テーブルを抽出できます。 Azure AI Document Intelligence には、請求書、領収書、名刺を認識するための事前構築されたモデルがあります。 このサービスは、カスタム モデルをトレーニングする機能も提供します。 この演習では、カスタム モデルの構築に焦点を当てます。

## Visual Studio Code でアプリを開発する準備をする

次に、サービス SDK を使用して、Visual Studio Code を使用してアプリを開発してみましょう。 アプリのコード ファイルは、GitHub リポジトリで提供されています。

> **ヒント**: mslearn-ai-document-intelligence** リポジトリを**既に複製している場合は、Visual Studio Code で開きます。 それ以外の場合は、次の手順に従って開発環境に複製します。

1. Visual Studio Code を起動します。
1. パレットを開き (SHIFT+CTRL+P)、**Git:Clone** コマンドを実行して、`https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` リポジトリをローカル フォルダーに複製します (どのフォルダーでも問題ありません)。
1. リポジトリを複製したら、Visual Studio Code でフォルダーを開きます。
1. リポジトリ内の C# コード プロジェクトをサポートするために追加のファイルがインストールされるまで待ちます。

    > **注**: ビルドとデバッグに必要なアセットを追加するように求めるダイアログが表示された場合は、 **[今はしない]** を選択します。 *フォルダー内の Azure 関数プロジェクトが検出されたことを示す*メッセージが表示されたら、そのメッセージを安全に閉じてかまいません。

## Azure AI Document Intelligence リソースを作成する

Azure AI Document Intelligence サービスを使用するには、Azure サブスクリプションに Azure AI Document Intelligence または Azure AI Services リソースが必要です。 Azure portal を使用してリソースを作成します。

1. ブラウザー タブで Azure portal (`https://portal.azure.com`) を開き、自分の Azure サブスクリプションに関連付けられている Microsoft アカウントを使用してサインインします。
1. Azure portal のホーム ページで、上部の検索ボックスに「**Document Intelligence**」と入力し、**Enter** キーを押します。
1. **[Document Intelligence]** ページで、**[作成]** を選択します。
1. **[Document Intelligence の作成]** ページで、次のコマンドを使用してリソースを構成します。
    - **サブスクリプション**: Azure サブスクリプション。
    - **リソース グループ**: *DocIntelligenceResources* などの 一意の名前を持つリソース グループを選択または作成します。
    - **リージョン**: 最寄りのリージョンを選択します。
    - **名前**: グローバルに一意の名前を入力します。
    - **価格レベル**: Free F0** を選択**します (Free レベルを利用できない場合は、**Standard S0** を選択します)。
1. 次に **[確認および作成]**、**[作成]** の順に選択します。 Azure が Azure AI Document Intelligence リソースを作成するまで待ちます。
1. デプロイが完了したら、**[リソースに移動]** を選択してリソースの **[概要]** ページを開きます。 

## トレーニング用のドキュメントを収集する

次のようなサンプル フォームを使用して、モデルのトレーニングとテストを行います。 

![このプロジェクトで使用される請求書の画像。](../media/Form_1.jpg)

1. **Visual Studio Code** に戻ります。 *エクスプローラー* ウィンドウで、**Labfiles/02-custom-document-intelligence** フォルダーを開き、**sample-forms** フォルダーを展開します。 フォルダー内に **.json** と **.jpg** で終わるファイルがあることに注意してください。

    **.jpg** ファイルを使用し、モデルをトレーニングします。  

    **.json** ファイルが生成され、ラベル情報が含まれています。 ファイルは、フォームと共に BLOB ストレージ コンテナーにアップロードされます。

    使用している画像は、Visual Studio Code で 選択することで、*sample-forms* フォルダーで表示できます。

1. **Azure portal** に戻り、リソースの **[概要]** ページに移動します (まだ表示されていない場合)。 *[要点] *セクションで、Document Intelligence リソースを作成した**リソース グループ**を表示します。

1. リソース グループの **[概要]** ページで、**サブスクリプション ID** と**場所**を記録します。 これらの値は、後続の手順で**リソース グループ**名とともに必要になります。

    ![リソース グループ ページの例。](../media/resource_group_variables.png)

1. Visual Studio Code の*エクスプローラー* ウィンドウで、**Labfiles/02-custom-document-intelligence** フォルダーを参照し、言語の設定に応じて **CSharp** または **Python** フォルダーを展開します。 各フォルダーには、Azure OpenAI 機能を統合するアプリの言語固有のファイルが含まれています。

1. コード ファイルを含む **CSharp** または **Python** フォルダーを右クリックし、**[統合ターミナルを開く]** を選択します。 

1. ターミナルで次のコマンドを実行して Azure にログインします。 **az login** コマンドを実行すると、Microsoft Edge ブラウザーが開き、Azure AI Document Intelligence リソースの作成に使用したのと同じアカウントでログインします。 ログインしたら、ブラウザー ウィンドウを閉じます。

    ```powershell
    az login
    ```

1. Visual Studio Code に戻ります。 ターミナル ウィンドウで次のコマンドを実行して、Azure の場所を一覧表示します。

    ```powershell
    az account list-locations -o table
    ```

1. 出力で、リソース グループの場所に対応する **Name** の値を見つけます (たとえば、*米国東部* の場合、対応する名前は *eastus* です)。

    > **重要**: **Name** の値を記録しておき、手順 11 で使用します。

1. Visual Studio Code の **Labfiles/02-custom-document-intelligence** フォルダーで **setup.cmd** を選択します。 このスクリプトを使用して、必要な他の Azure リソースを作成するために必要な Azure コマンド ライン インターフェイス (CLI) コマンドを実行します。

1. **setup.cmd** スクリプトで、コマンドを確認します。 プログラムは次のことを行います。
    - リソース グループにストレージ アカウントを作成する
    - ローカルの *sampleforms* フォルダーからストレージ アカウントの *sampleforms* というコンテナーにファイルをアップロードする
    - 共有アクセス署名 URI を印刷する

1. **subscription_id**、**resource_group**、**location** 変数の宣言を、Document Intelligence リソースをデプロイしたサブスクリプション、リソース グループ、およびロケーション名に適切な値で変更します。
その後、変更を**保存**します。

    **expiry_date** 変数は、演習用のままにしておきます。 この変数は、共有アクセス署名 (SAS) URI を生成するときに使用されます。 実際には、SAS に適切な有効期限を設定する必要があります。 SAS について詳しくは、[こちら](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works)をご覧ください。  

1. **Labfiles/02-custom-document-intelligence** フォルダーのターミナルで、次のコマンドを入力してスクリプトを実行します。

    ```PowerShell
    $currentdir=(Get-Item .).FullName
    cd ..
    ./setup.cmd
    cd $currentdir

    ```

1. スクリプトが完了したら、表示された出力を確認します。

1. Azure portal で、リソース グループを更新し、作成したばかりの Azure ストレージ アカウントが含まれていることを確認します。 ストレージ アカウントを開き、左側のウィンドウで **[ストレージ ブラウザー]** を選択します。 次に、ストレージ ブラウザーで、 **[BLOB コンテナー]** を展開し、**sampleforms** コンテナーを選択して、ファイルがローカルの **02-custom-document-intelligence/sample-forms** フォルダーからアップロードされていることを確認します。

## Document Intelligence Studio を使用してモデルをトレーニングする

次に、ストレージ アカウントにアップロードされたファイルを使用してモデルをトレーニングします。

1. ブラウザーで、Document Intelligence Studio (`https://documentintelligence.ai.azure.com/studio`) に移動します。
1. **[カスタム モデル]** セクションまで下にスクロールし、**[カスタム抽出モデル]** タイルを選択します。
1. アカウントへのサインインを求められた場合は、Azure 資格情報を使用します。
1. 使用する Azure AI Document Intelligence リソースを確認するメッセージが表示されたら、Azure AI Document Intelligence リソースの作成時に使用したサブスクリプションとリソース名を選択します。
1. **[マイ プロジェクト]** で、**[プロジェクトの作成]** を選択します。 次の構成を使用します。

    - **プロジェクト名**: 一意の名前を入力します。
        - *[続行]* を選択します。
    - **サービス リソースの構成**: このラボで前に作成したサブスクリプション、リソース グループ、ドキュメント インテリジェンス リソースを選択します。 *[既定値に設定]* チェック ボックスをオンにします。 既定の API バージョンをそのまま使用します。 
        - *[続行]* を選択します。
    - **トレーニング データ ソースの接続**: セットアップ スクリプトによって作成されたサブスクリプション、リソース グループ、ストレージ アカウントを選択します。 *[既定値に設定]* チェック ボックスをオンにします。 `sampleforms` BLOB コンテナーを選択し、フォルダー パスを空白のままにします。
        - *[続行]* を選択します。
    - *[プロジェクトの作成]* を選択します

1. プロジェクトが作成されたら、**[トレーニング]** を選択してモデルをトレーニングします。 次の構成を使用します。
    - **モデル ID**: *グローバルに一意の名前を指定します (次の手順でモデル ID 名が必要になります*)。 
    - **テンプレートの構築**: テンプレート。
1. トレーニングには時間がかかる場合があります。 完了すると、通知が表示されます。

## カスタム Document Intelligence モデルをテストする

1. Visual Studio Code に戻ります。 ターミナルで、言語設定に適したコマンドを実行して、Document Intelligence パッケージをインストールします。

    **C#:**

    ```powershell
    dotnet add package Azure.AI.FormRecognizer --version 4.1.0
    ```

    **Python**:

    ```powershell
    pip install azure-ai-formrecognizer==3.3.0
    ```

1. Visual Studio Code の **Labfiles/02-custom-document-intelligence** フォルダーで、使用している言語を選択します。 構成ファイル (言語設定に応じて **appsettings.json** または **.env**) を次の値で編集します。
    - Document Intelligence エンドポイント。
    - Document Intelligence キー。
    - モデルのトレーニング時に指定して生成されたモデル ID。 これは、Document Intelligence Studio の **[モデル]** ページで確認できます。 変更内容を**保存**します。

1. Visual Studio Code で、クライアント アプリケーションのコード ファイル (C# 用 *Program.cs*、Python 用 *test-model.py*) を開き、含まれているコードを確認します。特に、URL の画像が Web 上のこの GitHub リポジトリのファイルを参照していることを確認します。

1. ターミナルに戻り、次のコマンドを入力して、プログラムを実行します。

    **C#**

    ```powershell
    dotnet build
    dotnet run
    ```

    **Python**

    ```powershell
    python test-model.py
    ```

1. 出力を表示し、`Merchant` や `CompanyPhoneNumber` などのフィールド名がモデルの出力に含まれる様子を観察します。

## クリーンアップ

Azure リソースでの作業が完了したら、追加料金が発生しないように、[Azure portal](https://portal.azure.com/?azure-portal=true) 内のリソースを必ず削除してください。

## 詳細情報

Document Intelligence サービスの詳細については、[Document Intelligence のドキュメント](https://learn.microsoft.com/azure/ai-services/document-intelligence/?azure-portal=true)を参照してください。
