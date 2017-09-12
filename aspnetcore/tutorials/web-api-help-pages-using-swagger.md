---
title: "Swagger を使用する ASP.NET Core Web API のヘルプ ページ"
author: spboyer
description: "このチュートリアルでは、Swagger を追加して、Web API アプリケーションのドキュメントとヘルプ ページを生成する手順を説明します。"
keywords: "ASP.NET Core、Swagger、Swashbuckle、ヘルプ ページ、Web API"
ms.author: spboyer
manager: wpickett
ms.date: 09/01/2017
ms.topic: article
ms.assetid: 54bb961d-29d9-4dee-8e2c-a93fc33c16f2
ms.technology: aspnet
ms.prod: asp.net-core
uid: tutorials/web-api-help-pages-using-swagger
ms.openlocfilehash: fd2f415947c049d1239ce4e6bf0b1cf0264e7836
ms.sourcegitcommit: 41e3e007512c175a42910bc69678f3f0403cab04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2017
---
# <a name="aspnet-web-api-help-pages-using-swagger"></a><span data-ttu-id="8d353-104">Swagger を使用する ASP.NET Web API のヘルプ ページ</span><span class="sxs-lookup"><span data-stu-id="8d353-104">ASP.NET Web API Help Pages using Swagger</span></span>

<a name=web-api-help-pages-using-swagger></a>

<span data-ttu-id="8d353-105">[Shayne Boyer](https://twitter.com/spboyer) および [Scott Addie](https://twitter.com/Scott_Addie) 著</span><span class="sxs-lookup"><span data-stu-id="8d353-105">By [Shayne Boyer](https://twitter.com/spboyer) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="8d353-106">開発者が、コンシューマー向けアプリケーションを作成するときには、API のさまざまな使用方法を理解するのが困難な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8d353-106">Understanding the various methods of an API can be a challenge for a developer when building a consuming application.</span></span>

<span data-ttu-id="8d353-107">[Swagger](http://swagger.io) と .NET Core の実装 [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) を使用して Web API 向けのドキュメントとヘルプ ページを生成する作業は、いくつかの NuGet パッケージを追加して *Startup.cs* を変更するのと同じくらい簡単です。</span><span class="sxs-lookup"><span data-stu-id="8d353-107">Generating good documentation and help pages for your Web API, using [Swagger](http://swagger.io) with the .NET Core implementation [Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore), is as easy as adding a couple of NuGet packages and modifying the *Startup.cs*.</span></span>

* <span data-ttu-id="8d353-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) は、ASP.NET Core Web API の Swagger ドキュメントを生成するためのオープン ソース プロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8d353-108">[Swashbuckle.AspNetCore](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) is an open source project for generating Swagger documents for ASP.NET Core Web APIs.</span></span>

* <span data-ttu-id="8d353-109">[Swagger](http://swagger.io) は、対話型のドキュメント、クライアント SDK の生成、および探索可能性のサポートを有効にする RESTful API のコンピューターが読み取り可能な表現です。</span><span class="sxs-lookup"><span data-stu-id="8d353-109">[Swagger](http://swagger.io) is a machine-readable representation of a RESTful API that enables support for interactive documentation, client SDK generation, and discoverability.</span></span>

<span data-ttu-id="8d353-110">このチュートリアルでは、「[Building Your First Web API with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-web-api)」(ASP.NET Core MVC と Visual Studio を使用した最初の Web API の構築) のサンプルを基にして作成します。</span><span class="sxs-lookup"><span data-stu-id="8d353-110">This tutorial builds on the sample on [Building Your First Web API with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="8d353-111">サンプルの手順に従う場合は、[https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample) からサンプルをダウンロードしてください。</span><span class="sxs-lookup"><span data-stu-id="8d353-111">If you'd like to follow along, download the sample at [https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/first-web-api/sample).</span></span>

## <a name="getting-started"></a><span data-ttu-id="8d353-112">作業の開始</span><span class="sxs-lookup"><span data-stu-id="8d353-112">Getting Started</span></span>

<span data-ttu-id="8d353-113">Swashbuckle には 3 つの主要なコンポーネントがあります。</span><span class="sxs-lookup"><span data-stu-id="8d353-113">There are three main components to Swashbuckle:</span></span>

* <span data-ttu-id="8d353-114">`Swashbuckle.AspNetCore.Swagger`: `SwaggerDocument` オブジェクトを JSON エンドポイントとして公開するための Swagger オブジェクト モデルとミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="8d353-114">`Swashbuckle.AspNetCore.Swagger`: a Swagger object model and middleware to expose `SwaggerDocument` objects as JSON endpoints.</span></span>

* <span data-ttu-id="8d353-115">`Swashbuckle.AspNetCore.SwaggerGen`: ルート、コント ローラー、およびモデルから直接 `SwaggerDocument` オブジェクトを構築する Swagger ジェネレーター。</span><span class="sxs-lookup"><span data-stu-id="8d353-115">`Swashbuckle.AspNetCore.SwaggerGen`: a Swagger generator that builds `SwaggerDocument` objects directly from your routes, controllers, and models.</span></span> <span data-ttu-id="8d353-116">通常、Swagger エンドポイント ミドルウェアと組み合わせて Swagger JSON を自動的に公開します。</span><span class="sxs-lookup"><span data-stu-id="8d353-116">It's typically combined with the Swagger endpoint middleware to automatically expose Swagger JSON.</span></span>

* <span data-ttu-id="8d353-117">`Swashbuckle.AspNetCore.SwaggerUI`: 埋め込みバージョンの Swagger UI ツールであり、Swagger JSON を解釈して、Web API の機能を説明するカスタマイズ可能な機能豊富なエクスペリエンスを構築します。</span><span class="sxs-lookup"><span data-stu-id="8d353-117">`Swashbuckle.AspNetCore.SwaggerUI`: an embedded version of the Swagger UI tool which interprets Swagger JSON to build a rich, customizable experience for describing the Web API functionality.</span></span> <span data-ttu-id="8d353-118">これには、パブリック メソッド用の組み込みのテスト ハーネスが含まれます。</span><span class="sxs-lookup"><span data-stu-id="8d353-118">It includes built-in test harnesses for the public methods.</span></span>

## <a name="nuget-packages"></a><span data-ttu-id="8d353-119">NuGet パッケージ</span><span class="sxs-lookup"><span data-stu-id="8d353-119">NuGet Packages</span></span>

<span data-ttu-id="8d353-120">Swashbuckle は、次の方法で追加できます。</span><span class="sxs-lookup"><span data-stu-id="8d353-120">Swashbuckle can be added with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8d353-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d353-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8d353-122">[**パッケージ マネージャー コンソール**] ウィンドウから:</span><span class="sxs-lookup"><span data-stu-id="8d353-122">From the **Package Manager Console** window:</span></span>

    ```powershell
    Install-Package Swashbuckle.AspNetCore
    ```

* <span data-ttu-id="8d353-123">[**NuGet パッケージの管理**] ダイアログ ボックスから:</span><span class="sxs-lookup"><span data-stu-id="8d353-123">From the **Manage NuGet Packages** dialog:</span></span>

     * <span data-ttu-id="8d353-124">[**ソリューション エクスプローラー**] > [**NuGet パッケージの管理**] でプロジェクトを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-124">Right-click your project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
     * <span data-ttu-id="8d353-125">**パッケージ ソース**を "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="8d353-125">Set the **Package source** to "nuget.org"</span></span>
     * <span data-ttu-id="8d353-126">検索ボックスに「Swashbuckle.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="8d353-126">Enter "Swashbuckle.AspNetCore" in the search box</span></span>
     * <span data-ttu-id="8d353-127">[**参照**] タブから "Swashbuckle.AspNetCore"パッケージを選択して、[**インストール**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-127">Select the "Swashbuckle.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8d353-128">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8d353-128">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8d353-129">[**Solution Pad**] > [**パッケージを追加**] で [*パッケージ*] フォルダーを右クリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-129">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="8d353-130">[**パッケージを追加**] ウィンドウの [**ソース**] ドロップダウンを "nuget.org" に設定します。</span><span class="sxs-lookup"><span data-stu-id="8d353-130">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="8d353-131">検索ボックスに「Swashbuckle.AspNetCore」と入力します。</span><span class="sxs-lookup"><span data-stu-id="8d353-131">Enter Swashbuckle.AspNetCore in the search box</span></span>
* <span data-ttu-id="8d353-132">結果ウィンドウから Swashbuckle.AspNetCore パッケージを選択し、[**パッケージを追加**] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-132">Select the Swashbuckle.AspNetCore package from the results pane and click **Add Package**</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8d353-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d353-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d353-134">**統合端末**からから次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8d353-134">Run the following command from the **Integrated Terminal**:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8d353-135">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8d353-135">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="8d353-136">次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="8d353-136">Run the following command:</span></span>

```console
dotnet add TodoApi.csproj package Swashbuckle.AspNetCore
```

---

## <a name="add-and-configure-swagger-to-the-middleware"></a><span data-ttu-id="8d353-137">Swagger をミドルウェアに追加して構成します。</span><span class="sxs-lookup"><span data-stu-id="8d353-137">Add and configure Swagger to the middleware</span></span>

<span data-ttu-id="8d353-138">*Startup.cs* の `ConfigureServices` メソッドで Swagger ジェネレーターをサービス コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-138">Add the Swagger generator to the services collection in the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="8d353-139">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]</span><span class="sxs-lookup"><span data-stu-id="8d353-139">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_ConfigureServices&highlight=7-10)]</span></span>

<span data-ttu-id="8d353-140">*Startup.cs* の `Configure` メソッドで、生成された JSON ドキュメントと SwaggerUI 対応のミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8d353-140">In the `Configure` method of *Startup.cs*, enable the middleware for serving the generated JSON document and the SwaggerUI:</span></span>

<span data-ttu-id="8d353-141">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]</span><span class="sxs-lookup"><span data-stu-id="8d353-141">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup2.cs?name=snippet_Configure&highlight=4,7-10)]</span></span>

<span data-ttu-id="8d353-142">アプリを起動し、`http://localhost:<random_port>/swagger/v1/swagger.json` に移動します。</span><span class="sxs-lookup"><span data-stu-id="8d353-142">Launch the app, and navigate to `http://localhost:<random_port>/swagger/v1/swagger.json`.</span></span> <span data-ttu-id="8d353-143">エンドポイントを説明する生成されたドキュメントが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-143">The generated document describing the endpoints appears.</span></span>

<span data-ttu-id="8d353-144">**注:** Microsoft Edge、Google Chrome、および Firefox は JSON ドキュメントをネイティブで表示します。</span><span class="sxs-lookup"><span data-stu-id="8d353-144">**Note:** Microsoft Edge, Google Chrome, and Firefox display JSON documents natively.</span></span> <span data-ttu-id="8d353-145">読みやすくするためのドキュメントの書式設定する Chrome の拡張機能があります。</span><span class="sxs-lookup"><span data-stu-id="8d353-145">There are extensions for Chrome that format the document for easier reading.</span></span> <span data-ttu-id="8d353-146">*簡略化のための次の例は短縮されています。*</span><span class="sxs-lookup"><span data-stu-id="8d353-146">*The following example is reduced for brevity.*</span></span>

```json
{
   "swagger": "2.0",
   "info": {
       "version": "v1",
       "title": "API V1"
   },
   "basePath": "/",
   "paths": {
       "/api/Todo": {
           "get": {
               "tags": [
                   "Todo"
               ],
               "operationId": "ApiTodoGet",
               "consumes": [],
               "produces": [
                   "text/plain",
                   "application/json",
                   "text/json"
               ],
               "responses": {
                   "200": {
                       "description": "Success",
                       "schema": {
                           "type": "array",
                           "items": {
                               "$ref": "#/definitions/TodoItem"
                           }
                       }
                   }
                }
           },
           "post": {
               ...
           }
       },
       "/api/Todo/{id}": {
           "get": {
               ...
           },
           "put": {
               ...
           },
           "delete": {
               ...
   },
   "definitions": {
       "TodoItem": {
           "type": "object",
            "properties": {
                "id": {
                    "format": "int64",
                    "type": "integer"
                },
                "name": {
                    "type": "string"
                },
                "isComplete": {
                    "default": false,
                    "type": "boolean"
                }
            }
       }
   },
   "securityDefinitions": {}
}
```

<span data-ttu-id="8d353-147">このドキュメントでは、`http://localhost:<random_port>/swagger` ドライブに移動して表示できる Swagger UI をお勧めしています。</span><span class="sxs-lookup"><span data-stu-id="8d353-147">This document drives the Swagger UI, which can be viewed by navigating to `http://localhost:<random_port>/swagger`:</span></span>

![Swagger UI](web-api-help-pages-using-swagger/_static/swagger-ui.png)

<span data-ttu-id="8d353-149">`TodoController` 内の各パブリック アクション メソッドを UI からテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="8d353-149">Each public action method in `TodoController` can be tested from the UI.</span></span> <span data-ttu-id="8d353-150">メソッドの名前をクリックし、セクションを展開します。</span><span class="sxs-lookup"><span data-stu-id="8d353-150">Click a method name to expand the section.</span></span> <span data-ttu-id="8d353-151">任意の必要なパラメーターを追加し、[Try it out!] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-151">Add any necessary parameters, and click "Try it out!".</span></span>

![Swagger GET テストの例](web-api-help-pages-using-swagger/_static/get-try-it-out.png)

## <a name="customization--extensibility"></a><span data-ttu-id="8d353-153">カスタマイズと拡張機能</span><span class="sxs-lookup"><span data-stu-id="8d353-153">Customization & Extensibility</span></span>

<span data-ttu-id="8d353-154">Swagger は、テーマに合わせてオブジェクト モデルを文書化して、UI をカスタマイズするオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="8d353-154">Swagger provides options for documenting the object model and customizing the UI to match your theme.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="8d353-155">API 情報と説明</span><span class="sxs-lookup"><span data-stu-id="8d353-155">API Info and Description</span></span>

<span data-ttu-id="8d353-156">`AddSwaggerGen` メソッドに渡された構成アクションを使用して、作成者、ライセンス、説明などの情報を追加することができます。</span><span class="sxs-lookup"><span data-stu-id="8d353-156">The configuration action passed to the `AddSwaggerGen` method can be used to add information such as the author, license, and description:</span></span>

<span data-ttu-id="8d353-157">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]</span><span class="sxs-lookup"><span data-stu-id="8d353-157">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?range=20-30,36)]</span></span>

<span data-ttu-id="8d353-158">次の図は、バージョン情報を表示する Swagger UI を示しています。</span><span class="sxs-lookup"><span data-stu-id="8d353-158">The following image depicts the Swagger UI displaying the version information:</span></span>

![バージョン情報を含む Swagger UI: 説明、作成者、およびその他のリンクの表示](web-api-help-pages-using-swagger/_static/custom-info.png)

### <a name="xml-comments"></a><span data-ttu-id="8d353-160">XML コメント</span><span class="sxs-lookup"><span data-stu-id="8d353-160">XML Comments</span></span>

<span data-ttu-id="8d353-161">XML コメントは、次の方法で有効にすることができます。</span><span class="sxs-lookup"><span data-stu-id="8d353-161">XML comments can be enabled with the following approaches:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8d353-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8d353-162">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8d353-163">**ソリューション エクスプローラー**で、プロジェクトを右クリックし、[**プロパティ**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="8d353-163">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="8d353-164">[**ビルド**] タブの [**出力**] セクションの下にある [**XML ドキュメント ファイル**] チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="8d353-164">Check the **XML documentation file** box under the **Output** section of the **Build** tab:</span></span>

![プロジェクトのプロパティの [ビルド] タブ](web-api-help-pages-using-swagger/_static/swagger-xml-comments.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8d353-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8d353-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8d353-167">[**プロジェクト オプション**] ダイアログ > [**ビルド**] > [**コンパイラ**] を開きます。</span><span class="sxs-lookup"><span data-stu-id="8d353-167">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="8d353-168">[**全般オプション**] セクションの下にある [**XML ドキュメントを生成する**] チェック ボックスをオンにします。</span><span class="sxs-lookup"><span data-stu-id="8d353-168">Check the **Generate xml documentation** box under the **General Options** section:</span></span>

![プロジェクトのオプションの [全般オプション] セクション](web-api-help-pages-using-swagger/_static/swagger-xml-comments-mac.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8d353-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8d353-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="8d353-171">次のスニペットを *.csproj* ファイルに手動で追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-171">Manually add the following snippet to the *.csproj* file:</span></span>

<span data-ttu-id="8d353-172">[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]</span><span class="sxs-lookup"><span data-stu-id="8d353-172">[!code-xml[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/TodoApi.csproj?range=7-9)]</span></span>

---

<span data-ttu-id="8d353-173">生成された XML ファイルを使用するように Swagger を構成します。</span><span class="sxs-lookup"><span data-stu-id="8d353-173">Configure Swagger to use the generated XML file.</span></span> <span data-ttu-id="8d353-174">Linux または Windows 以外のオペレーティング システムでは、ファイル名やパスで大文字小文字が区別されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-174">For Linux or non-Windows operating systems, file names and paths can be case sensitive.</span></span> <span data-ttu-id="8d353-175">たとえば、*ToDoApi.XML* ファイルは Windows では見つかりますが、CentOS では見つかりません。</span><span class="sxs-lookup"><span data-stu-id="8d353-175">For example, a *ToDoApi.XML* file would be found on Windows but not CentOS.</span></span>

<span data-ttu-id="8d353-176">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]</span><span class="sxs-lookup"><span data-stu-id="8d353-176">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_ConfigureServices&highlight=20-22)]</span></span>

<span data-ttu-id="8d353-177">上記のコードで、`ApplicationBasePath` はアプリの基本パスを取得します。</span><span class="sxs-lookup"><span data-stu-id="8d353-177">In the preceding code, `ApplicationBasePath` gets the base path of the app.</span></span> <span data-ttu-id="8d353-178">基本パスは XML のコメント ファイルの検索に使用します。</span><span class="sxs-lookup"><span data-stu-id="8d353-178">The base path is used to locate the XML comments file.</span></span> <span data-ttu-id="8d353-179">生成された XML の名前はアプリケーション名に基づいているので、*TodoApi.xml* は、この例でのみ機能します。</span><span class="sxs-lookup"><span data-stu-id="8d353-179">*TodoApi.xml* only works for this example, since the name of the generated XML comments file is based on the application name.</span></span>

<span data-ttu-id="8d353-180">メソッドにトリプル スラッシュのコメントを追加すると、セクション ヘッダーの説明が追加されて Swagger UI が強化されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-180">Adding the triple-slash comments to the method enhances the Swagger UI by adding the description to the section header:</span></span>

<span data-ttu-id="8d353-181">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="8d353-181">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Delete&highlight=2)]</span></span>

![DELETE メソッドの XML コメント 'Deletes a specific TodoItem.' を](web-api-help-pages-using-swagger/_static/triple-slash-comments.png)

<span data-ttu-id="8d353-184">UI は、これらのコメントが含まれる生成された JSON ファイルにより強化されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-184">The UI is driven by the generated JSON file, which also contains these comments:</span></span>

```json
"delete": {
    "tags": [
        "Todo"
    ],
    "summary": "Deletes a specific TodoItem.",
    "operationId": "ApiTodoByIdDelete",
    "consumes": [],
    "produces": [],
    "parameters": [
        {
            "name": "id",
            "in": "path",
            "description": "",
            "required": true,
            "type": "integer",
            "format": "int64"
        }
    ],
    "responses": {
        "200": {
            "description": "Success"
        }
    }
}
```

<span data-ttu-id="8d353-185">[<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) タグを `Create` アクション メソッドのドキュメントに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-185">Add a [<remarks>](https://docs.microsoft.com/dotnet/csharp/programming-guide/xmldoc/remarks) tag to the `Create` action method documentation.</span></span> <span data-ttu-id="8d353-186">これは、`<summary>` タグで指定された情報を補足し、より堅牢な Swagger UI を提供します。</span><span class="sxs-lookup"><span data-stu-id="8d353-186">It supplements information specified in the `<summary>` tag and provides a more robust Swagger UI.</span></span> <span data-ttu-id="8d353-187">`<remarks>` タグの内容は、テキスト、JSON、または XML で構成されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-187">The `<remarks>` tag content can consist of text, JSON, or XML.</span></span>

<span data-ttu-id="8d353-188">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]</span><span class="sxs-lookup"><span data-stu-id="8d353-188">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=4-14)]</span></span>

<span data-ttu-id="8d353-189">これらの追加のコメントによる UI の強化に注意してください。</span><span class="sxs-lookup"><span data-stu-id="8d353-189">Notice the UI enhancements with these additional comments.</span></span>

![追加のコメントが表示された Swagger UI](web-api-help-pages-using-swagger/_static/xml-comments-extended.png)

### <a name="data-annotations"></a><span data-ttu-id="8d353-191">データの注釈</span><span class="sxs-lookup"><span data-stu-id="8d353-191">Data Annotations</span></span>

<span data-ttu-id="8d353-192">`System.ComponentModel.DataAnnotations` にみられる API コント ローラーを属性で装飾し、Swagger UI コンポーネントを強化します。</span><span class="sxs-lookup"><span data-stu-id="8d353-192">Decorate the API controller with attributes, found in `System.ComponentModel.DataAnnotations`, to help drive the Swagger UI components.</span></span>

<span data-ttu-id="8d353-193">`[Required]` 属性を `TodoItem` クラスの `Name` プロパティに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-193">Add the `[Required]` attribute to the `Name` property of the `TodoItem` class:</span></span>

<span data-ttu-id="8d353-194">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]</span><span class="sxs-lookup"><span data-stu-id="8d353-194">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Models/TodoItem.cs?highlight=10)]</span></span>

<span data-ttu-id="8d353-195">この属性の有無は、UI 動作を変更し、基になる JSON スキーマを変更します。</span><span class="sxs-lookup"><span data-stu-id="8d353-195">The presence of this attribute changes the UI behavior and alters the underlying JSON schema:</span></span>

```json
"definitions": {
    "TodoItem": {
        "required": [
            "name"
        ],
        "type": "object",
        "properties": {
            "id": {
                "format": "int64",
                "type": "integer"
            },
            "name": {
                "type": "string"
            },
            "isComplete": {
                "default": false,
                "type": "boolean"
            }
        }
    }
},
```

<span data-ttu-id="8d353-196">API コントローラーに `[Produces("application/json")]` 属性を追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-196">Add the `[Produces("application/json")]` attribute to the API controller.</span></span> <span data-ttu-id="8d353-197">その目的は、コント ローラーのアクションが *application/json* のコンテンツ タイプの戻りをサポートすることを宣言することです。</span><span class="sxs-lookup"><span data-stu-id="8d353-197">Its purpose is to declare that the controller's actions support a return a content type of *application/json*:</span></span>

<span data-ttu-id="8d353-198">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="8d353-198">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_TodoController&highlight=3)]</span></span>

<span data-ttu-id="8d353-199">[**応答コンテンツ タイプ**] ドロップダウンがコント ローラーの GET 操作の既定値としてこのコンテンツ タイプを選択します。</span><span class="sxs-lookup"><span data-stu-id="8d353-199">The **Response Content Type** drop-down selects this content type as the default for the controller's GET actions:</span></span>

![既定の応答のコンテンツ タイプを持つ Swagger UI](web-api-help-pages-using-swagger/_static/json-response-content-type.png)

<span data-ttu-id="8d353-201">Web API のデータの注釈の使用量が多いほど、UI と API のヘルプ ページはわかりやすく便利になります。</span><span class="sxs-lookup"><span data-stu-id="8d353-201">As the usage of data annotations in the Web API increases, the UI and API help pages become more descriptive and useful.</span></span>

### <a name="describing-response-types"></a><span data-ttu-id="8d353-202">応答の種類の説明</span><span class="sxs-lookup"><span data-stu-id="8d353-202">Describing Response Types</span></span>

<span data-ttu-id="8d353-203">開発者は、何が返されるか &mdash; 具体的には、応答の種類とエラー コード (標準以外の場合) を最も考慮します。</span><span class="sxs-lookup"><span data-stu-id="8d353-203">Consuming developers are most concerned with what is returned &mdash; specifically response types and error codes (if not standard).</span></span> <span data-ttu-id="8d353-204">これらは、XML のコメントおよびデータ注釈で処理されます。</span><span class="sxs-lookup"><span data-stu-id="8d353-204">These are handled in the XML comments and data annotations.</span></span>

<span data-ttu-id="8d353-205">`Create` アクションは、成功した場合は `201 Created` を返し、ポスト要求の本文が null の場合は `400 Bad Request` を返します。</span><span class="sxs-lookup"><span data-stu-id="8d353-205">The `Create` action returns `201 Created` on success or `400 Bad Request` when the posted request body is null.</span></span> <span data-ttu-id="8d353-206">Swagger UI に適切なドキュメントがないと、利用者にはこれらの予期される結果の知識がありません。</span><span class="sxs-lookup"><span data-stu-id="8d353-206">Without proper documentation in the Swagger UI, the consumer lacks knowledge of these expected outcomes.</span></span> <span data-ttu-id="8d353-207">その問題を解決するために、次の例では強調表示された行を追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-207">That problem is fixed by adding the highlighted lines in the following example:</span></span>

<span data-ttu-id="8d353-208">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]</span><span class="sxs-lookup"><span data-stu-id="8d353-208">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Controllers/TodoController.cs?name=snippet_Create&highlight=17,18,20,21)]</span></span>

<span data-ttu-id="8d353-209">Swagger UI は、予期される HTTP 応答コードを明確に記述するようになりました。</span><span class="sxs-lookup"><span data-stu-id="8d353-209">The Swagger UI now clearly documents the expected HTTP response codes:</span></span>

![ステータスコードの POST 応答クラスの説明 'Returns the newly created Todo item' and '400 - If the item is null' および応答メッセージの下に理由を示す Swagger UI](web-api-help-pages-using-swagger/_static/data-annotations-response-types.png)

### <a name="customizing-the-ui"></a><span data-ttu-id="8d353-211">UI のカスタマイズ</span><span class="sxs-lookup"><span data-stu-id="8d353-211">Customizing the UI</span></span>

<span data-ttu-id="8d353-212">stock UI は、機能し、表示可能ですが、API のドキュメント ページを作成するときには、ブランドまたはテーマを表する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d353-212">The stock UI is both functional and presentable; however, when building documentation pages for your API, you want it to represent your brand or theme.</span></span> <span data-ttu-id="8d353-213">Swashbuckle コンポーネントでそのタスクを実行するには、静的ファイルを提供するためのリソースを追加して、それらのファイルをホストするフォルダー構造を構築する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8d353-213">Accomplishing that task with the Swashbuckle components requires adding the resources to serve static files and then building the folder structure to host those files.</span></span>

<span data-ttu-id="8d353-214">.NET Framework を対象にする場合は、`Microsoft.AspNetCore.StaticFiles` NuGet パッケージをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="8d353-214">If targeting .NET Framework, add the `Microsoft.AspNetCore.StaticFiles` NuGet package to the project:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />
```

<span data-ttu-id="8d353-215">静的ファイル ミドルウェアを有効にします。</span><span class="sxs-lookup"><span data-stu-id="8d353-215">Enable the static files middleware:</span></span>

<span data-ttu-id="8d353-216">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]</span><span class="sxs-lookup"><span data-stu-id="8d353-216">[!code-csharp[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/Startup.cs?name=snippet_Configure&highlight=3)]</span></span>

<span data-ttu-id="8d353-217">[Swagger UI GitHub リポジトリ](https://github.com/swagger-api/swagger-ui/tree/2.x/dist)から *dist* フォルダーの内容を取得します。</span><span class="sxs-lookup"><span data-stu-id="8d353-217">Acquire the contents of the *dist* folder from the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui/tree/2.x/dist).</span></span> <span data-ttu-id="8d353-218">このフォルダーには、Swagger UI ページに必要なアセットが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8d353-218">This folder contains the necessary assets for the Swagger UI page.</span></span> <span data-ttu-id="8d353-219">そのフォルダーの内容を *wwwroot/swagger/ui* フォルダーにコピーします。</span><span class="sxs-lookup"><span data-stu-id="8d353-219">Copy the contents of that folder into the *wwwroot/swagger/ui* folder.</span></span>

<span data-ttu-id="8d353-220">次の CSS を使用して *wwwroot/swagger/ui/css/custom.css* ファイルを作成し、ページ ヘッダーをカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="8d353-220">Create a *wwwroot/swagger/ui/css/custom.css* file with the following CSS to customize the page header:</span></span>

<span data-ttu-id="8d353-221">[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]</span><span class="sxs-lookup"><span data-stu-id="8d353-221">[!code-css[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/css/custom.css)]</span></span>

<span data-ttu-id="8d353-222">*index.html* ファイルの *custom.css* を参照します。</span><span class="sxs-lookup"><span data-stu-id="8d353-222">Reference *custom.css* in the *index.html* file:</span></span>

<span data-ttu-id="8d353-223">[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]</span><span class="sxs-lookup"><span data-stu-id="8d353-223">[!code-html[Main](../tutorials/web-api-help-pages-using-swagger/sample/TodoApi/wwwroot/swagger/ui/index.html?range=14)]</span></span>

<span data-ttu-id="8d353-224">`http://localhost:<random_port>/swagger/ui/index.html` で *index.html* ページを参照します。</span><span class="sxs-lookup"><span data-stu-id="8d353-224">Browse to the *index.html* page at `http://localhost:<random_port>/swagger/ui/index.html`.</span></span> <span data-ttu-id="8d353-225">ヘッダーのテキスト ボックスに「`http://localhost:<random_port>/swagger/v1/swagger.json`」を入力し、[**探索**] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="8d353-225">Enter `http://localhost:<random_port>/swagger/v1/swagger.json` in the header's textbox, and click the **Explore** button.</span></span> <span data-ttu-id="8d353-226">結果のページは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8d353-226">The resulting page looks as follows:</span></span>

![カスタム ヘッダーのタイトルを含む Swagger UI](web-api-help-pages-using-swagger/_static/custom-header.png)

<span data-ttu-id="8d353-228">このページでできることはたくさんあります。</span><span class="sxs-lookup"><span data-stu-id="8d353-228">There is much more you can do with the page.</span></span> <span data-ttu-id="8d353-229">[Swagger UI GitHub リポジトリ](https://github.com/swagger-api/swagger-ui)で UI リソースの完全な機能を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8d353-229">See the full capabilities for the UI resources at the [Swagger UI GitHub repository](https://github.com/swagger-api/swagger-ui).</span></span>