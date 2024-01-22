---
lab:
  title: 構成された Document Intelligence モデルを作成する
  module: Module 6 - Document Intelligence
---

# 構成された Document Intelligence モデルを作成する

この演習では、異なる納税申告フォームを分析する 2 つのカスタム モデルを作成してトレーニングします。 次に、これらのカスタム モデルを両方とも含む作成済みモデルを作成します。 申告書を送信してモデルをテストし、ドキュメントの種類とラベル付けされたフィールドが正しく認識されていることを確認します。

## リソースのセットアップ

スクリプトを使用して、Azure AI Document Intelligence リソース、サンプル フォームを含むストレージ アカウント、リソース グループを作成します。

1. Visual Studio Code を起動します。
1. パレットを開き (SHIFT+CTRL+P)、**Git:Clone** コマンドを実行して、`https://github.com/MicrosoftLearning/mslearn-ai-document-intelligence` リポジトリをローカル フォルダーに複製します (どのフォルダーでも問題ありません)。
1. リポジトリを複製したら、Visual Studio Code でフォルダーを開きます。
1. リポジトリ内の C# コード プロジェクトをサポートするために追加のファイルがインストールされるまで待ちます。

    > **注**: ビルドとデバッグに必要なアセットを追加するように求めるプロンプトが表示された場合は、**[今はしない]** を選択します。

1. **03-composed-model** ディレクトリを右クリックし、統合ターミナルで開き、次のセットアップ スクリプトを実行します。

   ``` bash
   bash setup.sh
   ```

## 1040 Forms カスタム モデルを作成する

作成済みモデルを作成するには、最初に 2 つ以上のカスタム モデルを作成する必要があります。 最初のカスタム モデルを作成するには、次の操作を行います。

1. 新しいブラウザー タブで、[Azure AI Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/studio) を起動します。
1. 下にスクロールし、**[カスタム モデル]** で **[カスタム モデル]** を選択します。
1. アカウントへのサインインを求められた場合は、Azure 資格情報を使用します。
1. どの Azure AI Document Intelligence リソースを使用するかを確認されたら、Azure AI Document Intelligence リソースを作成した時に使用したサブスクリプションとリソース名を選択します。
1. **[マイ プロジェクト]** で、 **[+ Create a project](+ プロジェクトの作成)** を選択します。
1. **[プロジェクト名]** テキストボックスに「**1040 Forms**」と入力し、**[続行]** を選択します。
1. **[サービス リソースの構成]** ページの **[サブスクリプション]** ドロップダウン リストで、Azure サブスクリプションを選択します。
1. **[リソース グループ]** ドロップダウン リストで、**[DocumentIntelligenceResources]** を選択します。
1. **[Azure AI Document Intelligence or Azure AI Service Resource] (Azure AI Document Intelligence または Azure AI サービス リソース)** ドロップダウン リストで、**[DocumentIntelligence]** を選択します
1. **[API バージョン]** ドロップダウン リストで、**[2022-06-30-preview]** が選択されていることを確認し、**[続行]** を選択します。

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Azure AI Document Intelligence Studio カスタム モデル ウィザードの [サービス リソースの構成] ページを示すスクリーンショット。" lightbox="../media/4-configure-service-resources.png":::

1. **[トレーニング データ ソースの構成]** ページの **[サブスクリプション]** ドロップダウン リストで、Azure サブスクリプションを選択します。
1. **[リソース グループ]** ドロップダウン リストで、**[DocumentIntelligenceResources]** を選択します。
1. **[ストレージ アカウント]** ドロップダウン リストで、一覧表示されているストレージ アカウントのみを選択します。
1. **[BLOB コンテナー]** ドロップダウン リストで **[1040examples]** を選択し、**[続行]** を選択します。

    :::image type="content" source="../media/4-connect-training-data-source.png" alt-text="Azure AI Document Intelligence Studio カスタム モデル ウィザードの [トレーニング データ ソースの接続] ページを示すスクリーンショット。" lightbox="../media/4-connect-training-data-source.png":::

1. **[確認と作成]** ページで、**[プロジェクトの作成]** を選択します。

## 1040 Forms カスタム モデルにラベルを付ける

次に、フォーム例のフィールドにラベルを付けましょう。

1. **[ラベル データ]** ページで、ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。

    :::image type="content" source="../media/4-add-label.png" alt-text="Azure AI Document Intelligence Studio で新しいラベルを追加する方法を示すスクリーンショット。" lightbox="../media/4-add-label.png":::

1. 「**FirstName**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[John]** を選択し、**[FirstName]** を選択します。

    :::image type="content" source="../media/4-label-first-name.png" alt-text="Azure AI Document Intelligence Studio で新しいラベルを完了する方法を示すスクリーンショット。" lightbox="../media/4-label-first-name.png":::

1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**LastName**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[Doe]** を選択し、**[LastName]** を選択します。
1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**City**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[Los Angeles]** を選択し、**[City]** を選択します。
1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**State**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[CA]** を選択し、**[State]** を選択します。
1. 左側の一覧にある残りのフォームについてラベル付けのプロセスを繰り返します。 同じ 4 つのフィールドにラベル (*FirstName*、*LastName*、*City*、*State*) を付けます。

> [!IMPORTANT]
> この演習では目的上、5 つのフォームの例のみを使用し、4 つのフィールドのみにラベルを付けます。 実際のモデルでは、できるだけ多くのサンプルを使用して、予測の精度と信頼度を最大化する必要があります。 また、4 つのフィールドだけでなく、フォームで使用可能なすべてのフィールドにラベルを付ける必要もあります。

## 1040 Forms カスタム モデルをトレーニングする

サンプル フォームにラベルを付けたので、最初のモデルをトレーニングできます。

1. Azure AI Document Intelligence Studio で、**[トレーニング]** を選択します。
1. **[新しいモデルのトレーニング]** ダイアログの **[モデル ID]** テキストボックスに「**1040FormsModel**」と入力します。
1. **[ビルド モード]** ドロップダウン リストで **[テンプレート]** を選択し、**[トレーニング]** を選択します。 
1. **[トレーニングの進行中]** ダイアログで、**[モデルに移動]** を選択します。

## 1099 Forms カスタム モデルを作成する

次に、例 1099 の納税申告フォームでトレーニングを行う 2 つ目のモデルを作成する必要があります。

1. Azure AI Document Intelligence Studio で、**[カスタム モデル]** を選択します。
1. **[マイ プロジェクト]** で、 **[+ Create a project](+ プロジェクトの作成)** を選択します。
1. **[プロジェクト名]** テキストボックスに「**1099 Forms**」と入力し、**[続行]** を選択します。
1. **[サービス リソースの構成]** ページの **[サブスクリプション]** ドロップダウン リストで、Azure サブスクリプションを選択します。
1. **[リソース グループ]** ドロップダウン リストで、**[DocumentIntelligenceResources]** を選択します。
1. **[Azure AI Document Intelligence or Azure AI Service Resource] (Azure AI Document Intelligence または Azure AI サービス リソース)** ドロップダウン リストで、**[DocumentIntelligence]** を選択します
1. **[API バージョン]** ドロップダウン リストで、**[2022-06-30-preview]** が選択されていることを確認し、**[続行]** を選択します。

    :::image type="content" source="../media/4-configure-service-resources.png" alt-text="Azure AI Document Intelligence Studio カスタム モデル ウィザードの [サービス リソースの構成] ページを示すスクリーンショット。" lightbox="../media/4-configure-service-resources.png":::

1. **[トレーニング データ ソースの構成]** ページの **[サブスクリプション]** ドロップダウン リストで、Azure サブスクリプションを選択します。
1. **[リソース グループ]** ドロップダウン リストで、**[DocumentIntelligenceResources]** を選択します。
1. **[ストレージ アカウント]** ドロップダウン リストで、一覧表示されているストレージ アカウントのみを選択します。
1. **[BLOB コンテナー]** ドロップダウン リストで **[1099examples]** を選択し、**[続行]** を選択します。
1. **[確認と作成]** ページで、**[プロジェクトの作成]** を選択します。

## 1099 Forms カスタム モデルにラベルを付ける

次に、いくつかのフィールドを含むフォーム例にラベルを付けます。

1. **[ラベル データ]** ページで、ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**FirstName**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[John]** を選択し、**[FirstName]** を選択します。
1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**LastName**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[Doe]** を選択し、**[LastName]** を選択します。
1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**City**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[New Haven]** を選択し、**[City]** を選択します。
1. ページの右上にある [**+**] を選択し、**[フィールド]** を選択します。
1. 「**State**」と入力し、<kbd>Enter</kbd> キーを押します。
1. ドキュメントで **[CT]** を選択し、**[State]** を選択します。
1. 左側の一覧にある残りのフォームについてラベル付けのプロセスを繰り返します。 同じ 4 つのフィールドにラベル (*FirstName*、*LastName*、*City*、*State*) を付けます。

## 1099 Forms カスタム モデルをトレーニングする

2 つ目のカスタム モデルをトレーニングできるようになりました。

1. Azure AI Document Intelligence Studio で、**[トレーニング]** を選択します。
1. **[新しいモデルのトレーニング]** ダイアログの **[モデル ID]** テキストボックスに「**1099FormsModel**」と入力します。
1. **[ビルド モード]** ドロップダウン リストで **[テンプレート]** を選択し、**[トレーニング]** を選択します。 
1. **[トレーニングの進行中]** ダイアログで、**[モデルに移動]** を選択します。
1. トレーニング プロセスには数分かかることがあります。 両方のモデルで **[成功]** 状態が表示されるまで、ブラウザーをときどき更新します。

## 作成済みモデルを作成してアセンブルする

1040 と 1099 の納税申告フォームを分析する 2 つのカスタム モデルが完成しました。 次の手順に進み、作成済みモデルを作成できます。

1. [Azure AI Document Intelligence モデル] ページで、**[1040FormsModel]** と **[1099FormsModel]** の両方を選択します。
1. モデルの一覧の上部にある **[作成]** を選択します。

    :::image type="content" source="../media/4-start-compose-model.png" alt-text="Azure AI Document Intelligence Studio でモデルの作成を開始する方法を示すスクリーンショット。" lightbox="../media/4-start-compose-model.png":::

1. **[新しいモデルの作成]** ダイアログの **[モデル ID]** テキストボックスに「**TaxFormsModel**」と入力し、**[作成]** を選択します。 Azure AI Document Intelligence によって作成済みモデルが作成され、カスタム モデルの一覧に表示されます。

## 作成済みモデルを使用する

作成済みモデルが完成したので、フォーム例を使用してモデルをテストしましょう。

1. [Azure portal](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true) で、**[すべてのリソース]** を選択し、**[formsrecstorage&lt;xxxxx&gt;]** ストレージ アカウントを選択します。ここで、&lt;xxxxx&gt; はランダムな数値です。
1. **[データ ストレージ]** で **[コンテナー]** を選択し、**[TestDoc]** を選択します。
1. **[f1040_7.pdf]** の右側にある **[...]** を選択し、**[ダウンロード]** を選択します。
1. PDF ドキュメントをローカル コンピューターに保存し、保存場所をメモします。
1. Azure AI Document Intelligence Studio で、**[TaxFormsModel]** を選択し、**[テスト]** を選択します。
1. **[+ 追加]** を選択し、PDF ドキュメントを保存した場所を参照します。
1. **[f1040_7.pdf]** を選択し、**[開く]** を選択します。
1. **[分析]** を選択します。 Azure AI Document Intelligence により、作成済みモデルを使用してフォームが分析されます。

    :::image type="content" source="../media/4-composed-model-analysis.png" alt-text="Azure AI Document Intelligence Studio で作成したモデルを使用する方法を示すスクリーンショット。" lightbox="../media/4-composed-model-analysis.png":::

1. 分析されたドキュメントは、1040 納税申告フォーム例です。 **[DocType]** プロパティをチェックして、適切なカスタム モデルが使用されたかどうかを確認します。 また、モデルによって識別された **[FirstName]**、**[LastName]**、**[City**]、**[State]** の値も確認します。

## 演習用リソースをクリーンアップする

作成済みモデルの動作を確認したので、Azure サブスクリプションで作成したリソースを削除しましょう。

1. [Azure portal](https://portal.azure.com/learn.docs.microsoft.com?azure-portal=true) で、 **[リソース グループ]** を選択します。
1. **[リソース グループ]** の一覧で **[DocumentIntelligenceResources]** を選択し、**[リソース グループの削除]** を選択します。 
1. **[TYPE THE RESOURCE GROUP NAME] (リソース グループ名の入力)** ボックスに「**DocumentIntelligenceResources**」と入力し、**[削除]** を選択して Document Intelligence リソースとストレージ アカウントを削除します。

## 詳細情報

- [カスタム モデルを作成する](/azure/ai-services/document-intelligence/concept-composed-models)
- [カスタム モデルのトレーニング データセットを構築する](/azure/applied-ai-services/form-recognizer/how-to-guides/build-custom-model-v3)
