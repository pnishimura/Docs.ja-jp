---
title: ASP.NET Core のビュー コンポーネント
author: rick-anderson
description: ASP.NET Core でのビュー コンポーネントの使用方法とそれらをアプリに追加する方法を説明します。
manager: wpickett
ms.author: riande
ms.date: 02/14/2017
ms.prod: asp.net-core
ms.technology: aspnet
ms.topic: article
uid: mvc/views/view-components
ms.openlocfilehash: cdf44fc15ac64497b2589e8b7b289beb94c5b2c4
ms.sourcegitcommit: 74be78285ea88772e7dad112f80146b6ed00e53e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33962685"
---
# <a name="view-components-in-aspnet-core"></a>ASP.NET Core のビュー コンポーネント

作成者: [Rick Anderson](https://twitter.com/RickAndMSFT)

[サンプル コードを表示またはダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)します ([ダウンロード方法](xref:tutorials/index#how-to-download-a-sample))。

## <a name="view-components"></a>ビュー コンポーネント

ビュー コンポーネントは部分ビューと似ていますが、はるかに強力なものです。 ビュー コンポーネントでは、モデル バインドを使用せず、呼び出すときに指定されたデータのみに依存します。 この記事は ASP.NET Core MVC を使用して作成されましたが、ビュー コンポーネントは Razor ページでも利用できます。

ビュー コンポーネントの特徴は次のとおりです。

* 応答全体ではなく、チャンクをレンダリングします。
* コントローラーとビューの間にあるのと同じ関心の分離とテストの容易性の利点があります。
* パラメーターとビジネス ロジックを含めることができます。
* 通常、レイアウト ページから呼び出されます。

ビュー コンポーネントは、次のような部分ビューには複雑すぎる、再利用可能なレンダリング ロジックをどこでも使用できるようにするためのものです。

* 動的なナビゲーション メニュー
* タグ クラウド (データベースをクエリする場所)
* ログイン パネル
* ショッピング カート
* 新着情報の記事
* 一般的なブログのサイドバーのコンテンツ
* すべてのページでレンダリングされ、ユーザーの状態のログに応じて、ログアウトまたはログインのいずれかのリンクを示すログイン パネル

ビュー コンポーネントは、次の 2 つのパーツで構成されます。クラス (通常、[ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent) から派生) と、クラスで返される結果 (通常はビュー) です。 コントローラーと同様に、ビュー コンポーネントは POCO の場合がありますが、ほとんどの開発者は `ViewComponent` から派生させて、利用できるメソッドとプロパティを活用する必要があります。

## <a name="creating-a-view-component"></a>ビューのコンポーネントを作成する

このセクションには、ビュー コンポーネントを作成するための高レベルの要件が含まれています。 記事の後半で、各ステップの詳細を検証し、ビュー コンポーネントを作成します。

### <a name="the-view-component-class"></a>ビュー コンポーネント クラス

ビュー コンポーネント クラスは、次のいずれかによって作成できます。

* *ViewComponent* から派生させる
* `[ViewComponent]` 属性でクラスを装飾するか、`[ViewComponent]` 属性でクラスから派生させる
* 名前がサフィックス *ViewComponent* で終わるクラスを作成する

コントローラーと同様に、ビュー コンポーネントは、パブリック クラス、入れ子にされていないクラス、および非抽象クラスである必要があります。 ビュー コンポーネント名は、"ViewComponent" サフィックスを除いたクラス名です。 また、これは `ViewComponentAttribute.Name` プロパティを使用して、明示的に指定することもできます。

ビュー コンポーネント クラスの特徴は次のとおりです。

* コンストラクターの[依存性の注入](../../fundamentals/dependency-injection.md)を完全にサポートします

* コントローラーのライフサイクルに関わりません。つまり、ビュー コンポーネントで[フィルター](../controllers/filters.md)を使用できないということです

### <a name="view-component-methods"></a>ビュー コンポーネント メソッド

ビュー コンポーネントでは、`IViewComponentResult` を返す `InvokeAsync` メソッドでそのロジックを定義します。 パラメーターは、モデル バインドではなく、ビュー コンポーネントから直接取得します。 ビュー コンポーネントが要求を直接処理することはありません。 通常、ビュー コンポーネントは、モデルを初期化し、`View` メソッドを呼び出してビューに渡します。 要約すると、ビュー コンポーネント メソッドの特徴は次のとおりです。

* `IViewComponentResult` を返す `InvokeAsync` メソッドを定義します
* 通常、モデルを初期化し、`ViewComponent` `View` メソッドを呼び出してビューに渡します
* パラメーターは HTTP ではなく、呼び出しメソッドから取得されます。モデル バインドはありません
* HTTP エンドポイントとして直接到達することはできません。自分のコード (通常はビュー内) から呼び出されます。 ビュー コンポーネントは要求を処理しません
* 現在の HTTP 要求からの詳細ではなく、シグネチャでオーバーロードされます

### <a name="view-search-path"></a>ビューの検索パス

ランタイムでは、次のパスでビューを検索します。

   * Views/\<コントローラー名>/Components/\<ビュー コンポーネント名>/\<ビュー名>
   * Views/Shared/Components/\<ビュー コンポーネント名>/\<ビュー名>

ビュー コンポーネントの既定のビュー名は、*Default* です。つまり、通常、ビュー ファイルは *Default.cshtml* という名前になるということです。 ビュー コンポーネントの結果を作成したり、`View` メソッドを呼び出したりするときに、別のビュー名を指定することができます。

ビュー ファイルに *Default.cshtml* という名前を付けて、*Views/Shared/Components/\<ビュー コンポーネント名>/\<ビュー名>* パスを使用することをお勧めします。 このサンプルで使用される `PriorityList` ビュー コンポーネントは、ビュー コンポーネント ビューに *Views/Shared/Components/PriorityList/Default.cshtml* を使用します。

## <a name="invoking-a-view-component"></a>ビュー コンポーネントを呼び出す

ビュー コンポーネントを使用するには、ビュー内で以下を呼び出します。

```cshtml
@Component.InvokeAsync("Name of view component", <anonymous type containing parameters>)
```

パラメーターは、`InvokeAsync` メソッドに渡されます。 この記事で開発された `PriorityList` ビュー コンポーネントは、*Views/Todo/Index.cshtml* ビュー ファイルから呼び出されます。 以下では、`InvokeAsync` メソッドは、2 つのパラメーターで呼び出されます。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

## <a name="invoking-a-view-component-as-a-tag-helper"></a>タグ ヘルパーとしてビュー コンポーネントを呼び出す

ASP.NET Core 1.1 以降の場合は、[タグ ヘルパー](xref:mvc/views/tag-helpers/intro)としてビュー コンポーネントを呼び出すことができます。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

タグ ヘルパーのパスカル ケースのクラスとメソッドのパラメーターは、それぞれ[小文字のケバブ ケース](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)に変換されます。 ビュー コンポーネントを呼び出すタグ ヘルパーでは、`<vc></vc>` 要素を使用します。 ビュー コンポーネントは、次のように指定されます。

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

注: ビュー コンポーネントをタグ ヘルパーとして使用するには、`@addTagHelper` ディレクティブを使用して、ビュー コンポーネントを含むアセンブリを登録する必要があります。 たとえば、ビュー コンポーネントが "MyWebApp" と呼ばれるアセンブリにある場合は、次のディレクティブを `_ViewImports.cshtml` ファイルに追加します。

```cshtml
@addTagHelper *, MyWebApp
```

ビュー コンポーネントを参照する任意のファイルへのタグ ヘルパーとして、ビュー コンポーネントを登録できます。 タグ ヘルパーを登録する方法の詳細については、「[Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)」 (タグ ヘルパーのスコープの管理) を参照してください。

このチュートリアルで使用される `InvokeAsync` メソッドは、次のとおりです。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

タグ ヘルパーのマークアップでは、次のようになります。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexTagHelper.cshtml?range=37-38)]

上記のサンプルでは、`PriorityList` ビュー コンポーネントは `priority-list` になります。 ビュー コンポーネントに対するパラメーターは、小文字のケバブ ケースの属性として渡されます。

### <a name="invoking-a-view-component-directly-from-a-controller"></a>ビュー コンポーネントをコントローラーから直接呼び出す

通常、ビュー コンポーネントはビューから呼び出されますが、コントローラー メソッドから直接呼び出すことができます。 ビュー コンポーネントでコントローラーなどのエンドポイントを定義しないときに、`ViewComponentResult` のコンテンツを返すコントローラー アクションを簡単に実装できます。

この例では、ビュー コンポーネントは、コントローラーから直接呼び出されます。

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a>チュートリアル: 単純なビュー コンポーネントの作成

スタート コードを[ダウンロード](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、ビルド、およびテストします。 これは、*[Todo]* 項目のリストを表示する `Todo` コントローラーを含む単純なプロジェクトです。

![[ToDo] のリスト](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a>ViewComponent クラスの追加

*ViewComponents* フォルダーを作成して、次の `PriorityListViewComponent` クラスを追加します。

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

コードに関する注意事項

* ビュー コンポーネント クラスは、プロジェクト内の**任意**のフォルダーに含めることができます。
* クラス名 PriorityList**ViewComponent** は、サフィックス **ViewComponent** で終わるため、ビューからクラス コンポーネントを参照するときに、ランタイムでは文字列 "PriorityList" を使用します。 これについては、後で詳しく説明します。
* `[ViewComponent]` 属性では、ビュー コンポーネントを参照するために使用する名前を変更できます。 たとえば、クラスに `XYZ` という名前を付けて、`ViewComponent` 属性を適用することができます。

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* 上述の `[ViewComponent]` 属性は、ビュー コンポーネント セレクターに、コンポーネントに関連付けられたビューを探す場合は `PriorityList` という名前を使用し、ビューからクラス コンポーネントを参照する場合は文字列 "PriorityList" を使用するように指示します。 これについては、後で詳しく説明します。
* コンポーネントでは、[依存性の注入](../../fundamentals/dependency-injection.md)を使用して、データ コンテキストを利用できるようにします。
* `InvokeAsync` ではビューから呼び出すことができるメソッドを表示し、任意の数の引数を取得できます。
* `InvokeAsync` メソッドでは、`isDone` と `maxPriority` パラメーターを満たす `ToDo` 項目のセットを返します。

### <a name="create-the-view-component-razor-view"></a>ビュー コンポーネントの Razor ビューの作成

* *Views/Shared/Components* フォルダーを作成します。 このフォルダーは、*Components* という名前にする**必要があります**。

* *Views/Shared/Components/PriorityList* フォルダーを作成します。 このフォルダー名は、ビュー コンポーネント クラスの名前、または (規則に従い、クラス名に *ViewComponent* サフィックスを使用した場合は) サフィックスを差し引いたクラスの名前に一致する必要があります。 `ViewComponent` 属性を使用した場合は、クラス名は属性の指定に一致する必要があります。

* *Views/Shared/Components/PriorityList/Default.cshtml* Razor ビューを作成します。[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]
    
   Razor ビューでは、`TodoItem` のリストを取得して表示します。 ビュー コンポーネントの `InvokeAsync` メソッドで (サンプルのように) ビューの名前を渡さない場合、*Default* が規則によってビュー名に使用されます。 このチュートリアルの後半で、ビューの名前を渡す方法について示します。 特定のコントローラーの既定のスタイルをオーバーライドするには、コントローラーに固有のビュー フォルダー (例: *Views/Todo/Components/PriorityList/Default.cshtml)* にビューを追加します。
    
    ビュー コンポーネントがコントローラーに固有の場合、コントローラーに固有のフォルダー (*Views/Todo/Components/PriorityList/Default.cshtml*) に追加できます。

* 優先順位リストのコンポーネントの呼び出しを含む `div` を *Views/Todo/index.cshtml* ファイルの下部に追加します。

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFirst.cshtml?range=34-38)]

`@await Component.InvokeAsync` マークアップは、ビュー コンポーネントを呼び出すための構文を示します。 最初の引数は、呼び出す必要があるコンポーネントの名前です。 後続のパラメーターは、そのコンポーネントに渡されます。 `InvokeAsync` では、任意の数の引数を取得できます。

アプリをテストします。 次の画像は、[ToDo] リストと優先順位の項目を示します。

![[ToDo] リストと優先順位の項目](view-components/_static/pi.png)

また、コントローラーから直接ビュー コンポーネントを呼び出すこともできます。

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC アクションからの優先順位の項目](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a>ビュー名を指定する

複雑なビュー コンポーネントでは、いくつかの条件下で、既定以外のビューを指定する必要がある場合があります。 次のコードでは、`InvokeAsync` メソッドから "PVC" ビューを指定する方法を示しています。 `PriorityListViewComponent` クラスで `InvokeAsync` メソッドを更新します。

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

*Views/Shared/Components/PriorityList/Default.cshtml* ファイルを *Views/Shared/Components/PriorityList/PVC.cshtml* という名前のビューにコピーします。 PVC ビューが使用されていることを示すために、見出しを追加します。

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

*Views/TodoList/Index.cshtml* を更新します。

<!-- Views/TodoList/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexFinal.cshtml?range=35)]

アプリを実行して、PVC ビューを確認します。

![優先順位のビュー コンポーネント](view-components/_static/pvc.png)

PVC ビューがレンダリングされない場合は、4 以上の優先順位でビュー コンポーネントを呼び出していることを確認します。

### <a name="examine-the-view-path"></a>ビューのパスを調べる

* 優先順位ビューが返されないように、優先順位パラメーターを 3 以下に変更します。
* 一時的に *Views/Todo/Components/PriorityList/Default.cshtml* の名前を *1Default.cshtml* に変更します。
* アプリをテストすると、次のエラーが表示されます。

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* *Views/Todo/Components/PriorityList/1Default.cshtml* を *Views/Shared/Components/PriorityList/Default.cshtml* にコピーします。
* *[Shared]* の [ToDo] ビュー コンポーネントにいくつかのマークアップを追加して、*[Shared]* フォルダーからのビューを示します。
* **[Shared]** コンポーネント ビューをテストします。

![[Shared] コンポーネント ビューを含む [ToDo] 出力](view-components/_static/shared.png)

### <a name="avoiding-magic-strings"></a>マジック文字列の回避

コンパイル時間の安全性を確保する必要がある場合は、ハードコーディングされたビュー コンポーネント名をクラス名に置き換えることができます。 "ViewComponent" サフィックスのないビュー コンポーネントを作成します。

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

`using` ステートメントを Razor ビュー ファイルに追加して、`nameof` 演算子を使用します。

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/Todo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="additional-resources"></a>その他の技術情報

* [ビューへの依存性の注入](xref:mvc/views/dependency-injection)
