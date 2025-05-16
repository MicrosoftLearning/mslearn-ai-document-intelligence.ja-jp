---
lab:
  title: Azure AI コンテンツ解釈を使用してコンテンツを分析する
  module: Multimodal analysis with Content Understanding
---

# Azure AI コンテンツ解釈を使用してコンテンツを分析する

この演習では、Azure AI Foundry ポータルを使用して、請求書から情報を抽出できるコンテンツ解釈プロジェクトを作成します。 そして、Azure AI Foundry ポータルでコンテンツ アナライザーをテストし、コンテンツ解釈 REST インターフェイスを使用して実行します。

この演習は約 **30** 分かかります。

## Azure AI Foundry プロジェクトを作成する

まず、Azure AI Foundry プロジェクトを作成します。

1. Web ブラウザーで [Azure AI Foundry ポータル](https://ai.azure.com) (`https://ai.azure.com`) を開き、Azure 資格情報を使用してサインインします。 初めてサインインするときに開いたヒントまたはクイック スタート ウィンドウを閉じます。また、必要に応じて左上にある **Azure AI Foundry** ロゴを使用してホーム ページに移動します。それは次の画像のようになります。

    ![Azure AI Foundry ポータルのスクリーンショット。](../media/ai-foundry-portal.png)

1. ホーム ページで、**[+ 作成]** を選択します。
1. **[プロジェクトの作成]** ウィザードで、有効なプロジェクト名を入力し、既存のハブが推奨された場合は、新しいハブを作成するオプションを選択します。 次に、ハブとプロジェクトをサポートするために自動的に作成される Azure リソースを確認します。
1. **[カスタマイズ]** を選択し、ハブに次の設定を指定します。
    - **ハブ名**: *ハブの有効な名前*
    - **[サブスクリプション]**:"*ご自身の Azure サブスクリプション*"
    - **リソース グループ**: *リソース グループを作成または選択します*
    - **場所**: 次のいずれかのリージョンを選択します\*
        - 米国西部
        - スウェーデン中部
        - オーストラリア東部
    - **Azure AI サービスまたは Azure OpenAI への接続**: *新しい AI サービス リソースを作成します*
    - **Azure AI 検索の接続**: *一意の名前で新しい Azure AI 検索リソースを作成する*

    > \*執筆時点では、Azure AI コンテンツ解釈はこれらのリージョンでのみ使用できます。

1. **[次へ]** を選択し、構成を確認します。 **[作成]** を選択し、プロセスが完了するまで待ちます。
1. プロジェクトが作成されたら、表示されているヒントをすべて閉じて、Azure AI Foundry ポータルのプロジェクト ページを確認します。これは次の画像のようになっているはずです。

    ![Azure AI Foundry ポータルの Azure AI プロジェクトの詳細のスクリーンショット。](../media/ai-foundry-project.png)

## コンテンツ解釈アナライザーを作成する

請求書から情報を抽出できるアナライザーを構築します。 まず、サンプル請求書に基づいてスキーマを定義します。

1. 新しいブラウザー タブで、`https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf` からサンプル フォーム [invoice-1234.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1234.pdf) をダウンロードして、ローカル フォルダーに保存します。
1. Azure AI Foundry プロジェクトのホーム ページを含んだタブに戻り、左側のナビゲーション ウィンドウで **[コンテンツの解釈]** を選択します。
1. **[コンテンツの解釈]** ページで、上部の **[カスタム アナライザー]** タブを選択します。
1. [コンテンツの解釈] カスタム アナライザー ページで、**[+ 作成]** を選択し、次の設定でタスクを作成します。
    - **タスク名**: 請求書分析
    - **説明**: 請求書からデータを抽出する
    - **Azure AI サービス接続**: *お使いの Azure AI Foundry ハブの Azure AI サービス リソース*
    - **Azure Blob Storage アカウント**: *お使いの Azure AI Foundry ハブの既定のストレージ アカウント*
1. タスクが作成されるまで待ちます。

    > **ヒント**: ストレージへのアクセスでエラーが発生した場合は、少し待ってから、もう一度お試しください。

1. **[スキーマの定義]** ページで、ダウンロードした **invoice-1234.pdf** ファイルをアップロードします。
1. **請求書分析**テンプレートを選んで、**[作成]** を選択します。

    *請求書分析*テンプレートには、請求書に一般的に見られるフィールドが含まれています。 スキーマ エディターを使用すると、不要な推奨フィールドを削除したり、必要なカスタム フィールドを追加したりできます。

1. 推奨フィールドの一覧で、**BillingAddress** を選びます。 このフィールドは、アップロードした請求書形式には必要ないので、表示される **[フィールドの削除]** (**&#128465;** アイコン) を使用して削除します。
1. 次に、次の推奨フィールドを削除します。これも不要です。
    - BillingAddressRecipient
    - CustomerAddressRecipient
    - CustomerId
    - CustomerTaxId
    - DueDate
    - InvoiceTotal
    - PaymentTerm
    - PreviousUnpaidBalance
    - PurchaseOrder
    - RemittanceAddress
    - RemittanceAddressRecipient
    - ServiceAddress
    - ServiceAddressRecipient
    - ShippingAddress
    - ShippingAddressRecipient
    - TotalDiscount
    - VendorAddressRecipient
    - VendorTaxId
    - TaxDetails
1. **[+ 新しいフィールドの追加]** ボタンを使用して、次のフィールドを追加します。

    | フィールド名 | フィールド説明 | 値の型 | メソッド |
    |--|--|--|--|
    | `VendorPhone` | `Vendor telephone number` | String | Extract |
    | `ShippingFee` | `Fee for shipping` | 番号 | Extract |

1. 完成したスキーマが次のようになっていることを確認し、**[保存]** を選択します。
    ![請求書のスキーマのスクリーンショット。](../media/invoice-schema.png)

1. **[アナライザーのテスト]** ページで、分析が自動的に開始されない場合は、**[分析の実行]** を選択します。 そして、分析が完了するのを待ち、スキーマ内のフィールドと一致すると特定された、請求書のテキスト値を確認します。
1. 分析結果を確認します。これは次のようになっているはずです。

    ![アナライザーのテスト結果のスクリーンショット。](../media/analysis-results.png)

1. 識別されたフィールドの詳細を **[フィールド]** ウィンドウで確認し、**[結果]** タブで JSON 表現を確認します。

## アナライザーをビルドしてテストする

これで、請求書からフィールドを抽出するモデルのトレーニングが完了したので、類似したフォームで使用するアナライザーを構築できます。

1. **[アナライザーのビルド]** ページを選択したあと、**[+ アナライザーのビルド]** を選択し、次のプロパティ (次に示すとおりに正確に入力) を使用して新しいアナライザーをビルドします。
    - **名前**: `contoso-invoice-analyzer`
    - **説明**: `Contoso invoice analyzer`
1. 新しいアナライザーの準備が整うのを待ちます (**[最新の情報に更新]** ボタンで確認できます)。
1. `https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf` から [invoice-1235.pdf](https://github.com/microsoftlearning/mslearn-ai-document-intelligence/raw/main/Labfiles/05-content-understanding/forms/invoice-1235.pdf) をダウンロードして、ローカル フォルダーに保存します。
1. **[アナライザーのビルド]** ページに戻り、**contoso-invoice-analyzer** リンクを選択します。 アナライザーのスキーマで定義されているフィールドが表示されます。
1. **[contoso-invoice-analyzer]** ページで、**[テスト]** タブを選択します。
1. **[+ テスト ファイルのアップロード]** ボタンを使用して **invoice-1235.pdf** をアップロードし、分析を実行してテスト フォームからフィールド データを抽出します。
1. テストの結果を確認し、アナライザーがテスト請求書から正しいフィールドを抽出したことを確かめます。
1. **[contoso-invoice-analyzer]*** ページを閉じます。

## コンテンツ解釈 REST API を使用する

アナライザーを作成したので、コンテンツ解釈 REST API を使用してクライアント アプリケーションからアナライザーを実行できます。

1. **[プロジェクトの詳細]** エリアで、**[プロジェクト接続文字列]** の内容を書き留めます。 この接続文字列を使用して、クライアント アプリケーションでプロジェクトに接続します。
1. 新しいブラウザー タブを開きます (既存のタブで Azure AI Foundry ポータルを開いたままにします)。 新しいブラウザー タブで [Azure portal](https://portal.azure.com) (`https://portal.azure.com`) を開き、メッセージに応じて Azure 資格情報を使用してサインインします。

    ウェルカム通知を閉じて、Azure portal のホーム ページを表示します。

1. ページ上部の検索バーの右側にある **[\>_]** ボタンを使用して、Azure portal に新しい Cloud Shell を作成し、サブスクリプションにストレージがない ***PowerShell*** 環境を選択します。

    Azure portal の下部にあるペインに Cloud Shell のコマンド ライン インターフェイスが表示されます。 作業しやすくするために、このウィンドウのサイズを変更したり最大化したりすることができます。

    > **注**: *Bash* 環境を使用するクラウド シェルを以前に作成した場合は、それを ***PowerShell*** に切り替えます。

1. Cloud Shell ツール バーの **[設定]** メニューで、**[クラシック バージョンに移動]** を選択します (これはコード エディターを使用するのに必要です)。

    **<font color="red">続行する前に、クラシック バージョンの Cloud Shell に切り替えたことを確認します。</font>**

1. Cloud Shell 画面で、次のコマンドを入力して、この演習のコード ファイルを含む GitHub リポジトリを複製します (コマンドを入力するか、クリップボードにコピーしてから、コマンド ラインで右クリックし、プレーンテキストとして貼り付けます)。

    ```
   rm -r mslearn-ai-foundry -f
   git clone https://github.com/microsoftlearning/mslearn-ai-document-intelligence mslearn-ai-doc
    ```

    > **ヒント**: Cloudshell にコマンドを入力すると、出力が大量のスクリーン バッファーを占有する可能性があります。 `cls` コマンドを入力して、各タスクに集中しやすくすることで、スクリーンをクリアできます。

1. リポジトリが複製されたら、アプリのコード ファイルを含んだフォルダーに移動します。

    ```
   cd mslearn-ai-doc/Labfiles/05-content-understanding/code
   ls -a -l
    ```

1. Cloud Shell コマンド ライン ペインで、次のコマンドを入力して、使用するライブラリをインストールします。

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install dotenv azure-identity azure-ai-projects
    ```

1. 次のコマンドを入力して、提供されている構成ファイルを編集します。

    ```
   code .env
    ```

    このファイルをコード エディターで開きます。

1. コード ファイルで、**YOUR_PROJECT_CONNECTION_STRING** プレースホルダーをプロジェクトの接続文字列 (Azure AI Foundry ポータルでプロジェクトの **[概要]** ページからコピーしたもの) に置き換え、**ANALYZER** が、アナライザーに割り当てた名前 (*contoso-invoice-analyzer*) に設定されていることを確認します
1. プレースホルダーを置き換えたら、コード エディター内で **Ctrl + S** コマンドを使用して変更を保存してから、**Ctrl + Q** コマンドを使用して、Cloud Shell コマンド ラインを開いたままコード エディターを閉じます。

1. Cloud Shell コマンド ラインで、次のコマンドを入力して、提供されている Python コード ファイル **analyze_invoice.py** を編集します。

    ```
    code analyze_invoice.py
    ```
    Python コード ファイルはコード エディターで開かれます。

1. コードを確認します。以下の処理が行われます。
    - 分析対象の請求書ファイルを特定します。既定では **invoice-1236.pdf** です。
    - プロジェクトから Azure AI サービス リソースのエンドポイントとキーを取得します。
    - コンテンツ解釈エンドポイントに HTTP POST 要求を送信し、画像を分析するように指示します。
    - POST 操作からの応答をチェックして、分析操作の ID を取得します。
    - 操作が実行されなくなるまで、HTTP GET 要求をコンテンツ解釈サービスに繰り返し送信します。
    - 操作が成功した場合は、JSON 応答を解析し、取得した値を表示します。
1. **Ctrl + Q** コマンドを使用して、Cloud Shell コマンド ラインを開いたままコード エディターを閉じます。
1. Cloud Shell コマンド ライン ペインで、次のコマンドを入力して Python コードを実行します。

    ```
    python analyze_invoice.py invoice-1236.pdf
    ```

1. プログラムからの出力を確認します。
1. 次のコマンドを使用して、プログラムを別の請求書で実行します。

    ```
    python analyze_invoice.py invoice-1235.pdf
    ```

    > **ヒント**: 使用できる 3 つの請求書ファイル (invoice-1234.pdf、invoice-1235.pdf、invoice-1236.pdf) がコード フォルダーにあります。 

## クリーンアップ

コンテンツ解釈サービスの操作が終わったら、不要な Azure コストが発生しないように、この演習で作成したリソースを削除する必要があります。

1. Azure AI Foundry ポータルで、**travel-insurance** プロジェクトに移動して削除します。
1. Azure portal において、この演習で作成したリソース グループを削除します。

