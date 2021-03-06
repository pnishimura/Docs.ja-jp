---
uid: web-forms/overview/moving-to-aspnet-20/master-pages
title: マスター ページ |Microsoft ドキュメント
author: microsoft
description: 成功した Web サイトに主要なコンポーネントの 1 つは、一貫したルック アンド フィールです。 ASP.NET で 1.x では、共通のページ elem をレプリケートするに使用されるユーザー コントロールを開発者.
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/20/2005
ms.topic: article
ms.assetid: 9c0cce4d-efd9-4c14-b0e8-a1a140abb3f4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/moving-to-aspnet-20/master-pages
msc.type: authoredcontent
ms.openlocfilehash: f45dd9704f665244d2a48ec000326f6e98984e4f
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30885118"
---
<a name="master-pages"></a>マスター ページ
====================
によって[Microsoft](https://github.com/microsoft)

> 成功した Web サイトに主要なコンポーネントの 1 つは、一貫したルック アンド フィールです。 ASP.NET で 1.x では、Web アプリケーション全体で共通のページ要素を複製するに使用されるユーザー コントロールを開発者。 有効な解決策は確かに、中にユーザー コントロールを使用する欠点があります。 たとえば、ユーザー コントロールの位置の変更には、サイト全体での複数のページへの変更が必要です。 ユーザー コントロールはもページに挿入される後、デザイン ビューでレンダリングされません。


成功した Web サイトに主要なコンポーネントの 1 つは、一貫したルック アンド フィールです。 ASP.NET で 1.x では、Web アプリケーション全体で共通のページ要素を複製するに使用されるユーザー コントロールを開発者。 有効な解決策は確かに、中にユーザー コントロールを使用する欠点があります。 たとえば、ユーザー コントロールの位置の変更には、サイト全体での複数のページへの変更が必要です。 ユーザー コントロールはもページに挿入される後、デザイン ビューでレンダリングされません。

ASP.NET 2.0 ではマスター ページとする一貫したルック アンド フィールを維持するための手段としてすぐに表示されます、マスター ページは、ユーザー コントロールのメソッドよりが大幅に向上を表します。

## <a name="why-master-pages"></a>なぜマスター ページですか。

比較を行う ASP.NET 2.0 のマスター ページが必要な理由です。 結局のところ、Web サイト開発者で既に使用してユーザー コントロール ASP.NET ページ間のコンテンツ領域を共有する 1.x です。 実際には、いくつかの理由がなぜユーザー コントロールは、一般的なレイアウトを作成するための最善の以下の解決策があります。

ユーザー コントロールでは、ページ レイアウトを定義しない実際にします。 代わりに、レイアウトとページの一部の機能を定義します。 により、ユーザー コントロールのソリューションの管理の容易性が非常に困難であるために、これらの 2 つの違いが重要です。 たとえば、ページ上のユーザー コントロールの位置を変更する場合は、ユーザー コントロールが表示される実際のページを編集する必要があります。 これは、いくつかのページのみが大規模なサイトですぐに、サイト大変な作業を微調整!

ASP.NET そのもののアーキテクチャは、ユーザー コントロールを使用して、一般的なレイアウトを定義するためのもう 1 つの欠点をルートとします。 ユーザー コントロールのすべてのパブリック メンバーが変更された場合、すべてのユーザー コントロールを使用するページを再コンパイルが必要です。 さらに、ASP.NET は、再度 JIT 最初にあるときに、ページにアクセスし、されます。 これには、もう一度、非スケーラブル アーキテクチャと大規模なサイトのサイト管理の問題を生成します。

これらの問題 (およびさらに多く) の両方のリソースは、ASP.NET 2.0 のマスター ページで適切にアドレス指定します。

## <a name="how-master-pages-work"></a>マスター ページの動作

マスター ページは、他のページのテンプレートに似ています。 (メニューの罫線など) は、他のページで共有されるページの要素は、マスター ページに追加されます。 サイトに新しいページを追加するときは、マスター ページと関連付けることができます。 マスター ページに関連付けられているページが呼び出され、**コンテンツ ページ**です。 既定では、コンテンツ ページは、マスター ページの外観になります。 ただし、マスター ページを作成するときに、コンテンツ ページは、独自のコンテンツを置き換えることができる、ページの部分を定義できます。 これらの部分は、ASP.NET 2.0 で導入された新しいコントロールを使用して定義されます。**ContentPlaceHolder**コントロール。

マスター ページ プレース ホルダー コントロール (またはすべてではありません。) の任意の数を含めることができます。内のコンテンツ ページで、プレース ホルダー コントロールからコンテンツが表示される**コンテンツ**コントロール、ASP.NET 2.0 では、別の新しいコントロールです。 既定では、独自のコンテンツを提供できるように、コンテンツ ページのコンテンツ コントロールが空です。 コンテンツ コントロールの内部で、マスター ページからコンテンツを使用する場合は、行うことができますので、このモジュールの後で確認するためです。 コンテンツ コントロールは、コンテンツ コントロールの ContentPlaceHolderID 属性を使用して、プレース ホルダー コントロールにマップされます。 マップの下に、コード、コンテンツは、マスター ページ mainBody と呼ばれる ContentPlaceHolder コントロールに制御します。

[!code-aspx[Main](master-pages/samples/sample1.aspx)]

> [!NOTE]
> 他のページの基本クラスとしてのマスター ページについて説明する人を聞くことが多くの場合、します。 これが実際にはない場合は true です。 マスター ページとコンテンツ ページ間のリレーションシップは、継承のいずれかではないです。


**図 1** Visual Studio 2005 で表示されるマスター ページと関連するコンテンツ ページを示しています。 マスター ページと、対応する ContentPlaceHolder コントロールを確認できますコンテンツ ページのコントロールのコンテンツします。 ContentPlaceHolder 外にあるマスター ページ コンテンツが表示されますが、コンテンツ ページで淡色であることを確認します。 ContentPlaceHolder の内部でのみのコンテンツは、コンテンツ ページに置き換わる可能性ことができます。 マスター ページから取得した他のすべてのコンテンツは変更できません。


![マスター ページとその関連するコンテンツ ページ](master-pages/_static/image1.jpg)

**図 1**: マスター ページとその関連するコンテンツ ページ


## <a name="creating-a-master-page"></a>マスター ページの作成

新しいマスター ページを作成します。

1. Visual Studio 2005 を開き、新しい Web サイトを作成します。
2. ファイルを新しいファイルをクリックします。
3. ように、新しい項目の追加 ダイアログ ボックスからマスター ファイルを選択**図 2**です。
4. [追加] をクリックします。


![新しいマスター ページを作成します。](master-pages/_static/image2.jpg)

**図 2**: 新しいマスター ページを作成します。


マスター ページのファイル拡張子は<em>.master</em>です。 これは、通常のページから、マスター ページとは異なる方法のいずれかです。 代わりに、その他の主な違いは、@Pageディレクティブ、マスター ページには、@Masterディレクティブです。 先ほど作成したコードをレビューしてページをソース ビューに、マスターの切り替え。

新しいマスター ページは、既定では、1 つのプレース ホルダー コントロールがあります。 ほとんどの場合、方が有意義共通のページ要素をまず作成し、ContentPlaceHolder コントロールを挿入するカスタム コンテンツが必要な場合です。 ような場合、開発者する既定のプレース ホルダー コントロールを削除し、ページが作成されると、新しいものを挿入できます。 ContentPlaceHolder コントロールがサイズ変更ハンドルを表示しないでことでも、サイズ変更可能ではありません。 ContentPlaceHolder のコントロールのサイズが 1 つの例外が含まれている内容に基づいて、自動的にテーブルのセルなどブロック要素内でプレース ホルダー コントロールを配置する場合は、要素のサイズに応じてサイズがします。

## <a name="lab-1-working-with-master-pages"></a>ラボ 1 マスター ページを使用します。

このラボでは、新しいマスター ページを作成し、ContentPlaceHolder の 3 つのコントロールを定義します。 新しいコンテンツ ページを作成し、少なくとも 1 つのプレース ホルダー コントロールからのコンテンツを置き換えるはします。

1. マスター ページを作成し、ContentPlaceHolder のコントロールを挿入します。 

    1. 上記のように、新しいマスター ページを作成します。
    2. 既定のプレース ホルダー コントロールを削除します。
    3. コントロールの網掛けの上枠をクリックして ContentPlaceHolder コントロールを選択し、キーボードの DEL キーを押すことでそれを削除します。
    4. 新しいテーブルを使用して、挿入、*ヘッダーと側*テンプレート図 3 に示すようにします。 テーブル全体がデザイナーで表示されるように、幅と高さを 90% に変更します。


![](master-pages/_static/image3.jpg)

**図 3**


1. テーブルの各セルにカーソルを置き、設定、 *valign*プロパティを*上部*です。
2. ツールボックスから、(ヘッダー セルです。) テーブルの一番上のセルで ContentPlaceHolder コントロールを挿入します。
3. このプレース ホルダー コントロールを挿入するときに、図 4 に示すように、行の高さがページ全体ではほとんどを使用はことがわかります。 Dont をについては、この時点で考慮します。


![空の領域は、プレース ホルダーとして同じセルで、します。](master-pages/_static/image1.gif)

**図 4**: ContentPlaceHolder として同じセルでは、空の領域


1. その他の 2 つのセルに ContentPlaceHolder コントロールを配置します。 他のコントロールのプレース ホルダーが挿入されると、テーブルのセルのサイズは想定されるようにする必要があります。 ページに表示されるページのようになります**図 5**です。


![ContentPlaceHolder のすべてのコントロールを使用してマスター。 ヘッダー セルのセルの高さがする必要があります。](master-pages/_static/image2.gif)

**図 5**: ContentPlaceHolder のすべてのコントロールを使用して、マスター。 ヘッダー セルのセルの高さがする必要があります。


1. ContentPlaceHolder の 3 つのコントロールのそれぞれに、任意のテキストを入力します。
2. Exercise1.master として、マスター ページを保存します。
3. 新しい Web フォームを作成し、exercise1.master マスター ページに関連付けます。
4. ファイルの Visual Studio 2005 で、新しいファイルを選択します。
5. 選択**Web フォーム**[新しい項目の追加] ダイアログ ボックス。
6. 図 6 に示すように、マスター ページを選択 チェック ボックスがオンになっていることを確認してください。


![新しいコンテンツ ページを追加します。](master-pages/_static/image3.gif)

**図 6**: 新しいコンテンツ ページを追加します。


1. [追加] をクリックします。
2. [Exercise1.master 選択マスター ページ] ダイアログ図 7 に示すようにします。
3. 新しいコンテンツ ページを追加するには、[ok] をクリックします。

マスター ページ プレース ホルダー コントロールごとに 1 つのコンテンツ コントロールに、Visual Studio で新しいコンテンツ ページが表示されます。 既定では、独自のコンテンツを追加できるように、コンテンツ コントロールが空です。 マスター ページ プレース ホルダー コントロールからコンテンツを使用すると、単にスマート タグのシンボル (コントロールの右上隅に小さな黒い矢印) をクリックし、選択*マスター コンテンツを既定の*示すようにスマート タグから**図 8**です。 これを行うと、メニュー項目に変わります*カスタム コンテンツの作成*です。 その時点でクリックすると、その特定のコンテンツ コントロールのカスタム コンテンツを定義することができます、マスター ページからコンテンツを削除します。


![既定で、マスター ページ コンテンツにコンテンツ コントロールの設定](master-pages/_static/image4.gif)

**図 7**: コンテンツ コントロールをマスター ページ コンテンツを既定に設定


## <a name="connecting-master-page-and-content-pages"></a>マスター ページとコンテンツ ページを接続します。

マスター ページと、コンテンツ ページ間の関連付けは、4 つの方法のいずれかで構成できます。

- <strong>MasterPageFile</strong>の属性、@Pageディレクティブ
- 設定、 **Page.MasterPageFile**コード内のプロパティです。
- **&lt;ページ&gt;** アプリケーション構成ファイル (アプリケーションのルート フォルダーに web.config) で要素
- **&lt;ページ&gt;** サブフォルダー構成ファイル (サブフォルダーの web.config) で要素

## <a name="masterpagefile-attribute"></a>MasterPageFile Attribute

MasterPageFile 属性では、簡単に特定の ASP.NET ページにマスター ページを適用します。 チェックするときに、マスター ページを適用するために使用するメソッドも、**マスター ページの選択**としてするチェック ボックスは、手順 1 ででした。

## <a name="setting-pagemasterpagefile-in-code"></a>コードで Page.MasterPageFile の設定

コードの MasterPageFile プロパティの設定によっては、実行時に、コンテンツに特定のマスター ページを適用できます。 これは、ユーザー ロールまたはその他のいくつかの条件に基づいて、特定のマスター ページを適用する必要がある場合に便利です。 PreInit メソッドの MasterPageFile プロパティを設定する必要があります。 PreInit メソッドの後に設定されている場合、InvalidOperationException がスローされます。 このプロパティが設定されているページは、コンテンツにも必要、ページのコントロールをトップレベル コントロールと同様に制御します。 それ以外の場合、MasterPageFile プロパティが設定されている場合、HttpException がスローされます。

## <a name="using-the-ltpagesgt-element"></a>使用して、&lt;ページ&gt;要素

MasterPageFile 属性を設定して、ページのマスター ページを構成することができます、&lt;ページ&gt;web.config ファイルの要素。 このメソッドを使用する場合は、アプリケーションの構造内の下位の web.config ファイルがこの設定を上書きできますに留意してください。 どの MasterPageFile の属性セット、@Pageディレクティブでは、この設定は上書きもします。 使用して、&lt;ページ&gt;要素を簡単に作成、<em>マスター</em>マスター ページを特定のフォルダーまたはファイルに必要な場合はオーバーライドできます。

## <a name="properties-in-master-pages"></a>マスター ページのプロパティ

マスター ページでは、単にこれらのプロパティをマスター ページ内で公開することで、プロパティを公開できます。 たとえば、次のコードでは、SomeProperty をという名前のプロパティを定義します。

[!code-csharp[Main](master-pages/samples/sample2.cs)]

プロパティにアクセスする、SomeProperty コンテンツ ページから、マスターを使用する必要がありますプロパティ次のようにします。

[!code-csharp[Main](master-pages/samples/sample3.cs)]

## <a name="nesting-master-pages"></a>入れ子のマスター ページ

マスター ページとは、大規模な Web アプリケーション間で共通のルック アンド フィールを確保するための最適なソリューションです。 ただし、特定の部分が、大規模なサイトの共有、共通のインターフェイスの他の部分を共有する別のインターフェイスは珍しいことではありません。 そのに対処するには、複数のマスター ページは、最適なソリューションです。 ただし、まだ解消されない場合、大規模なアプリケーションがあるすべてのページ間で共有される (たとえばメニュー) などの特定のコンポーネントとのみ特定のセクションでは、サイトの間で共有されるその他のコンポーネントをファクトです。 このような状況では、入れ子になったマスター ページでは必要性が適切に入力します。 既に説明したマスター ページ、およびコンテンツ ページの標準のマスター ページで構成されます。 入れ子になったマスター ページの状況では、2 つのマスター ページです。親マスターと子マスター。 子のマスター ページ コンテンツ ページともそのマスタが親のマスター ページあります。

一般的なマスター ページのコードを次に示します。

[!code-aspx[Main](master-pages/samples/sample4.aspx)]

入れ子になったマスター シナリオでは、親マスターになります。 別のマスター ページは、このページを使用して、マスター ページとしては、そのコードは次のようになります。

[!code-aspx[Main](master-pages/samples/sample5.aspx)]

このシナリオでは、子マスターがあるも親マスターのコンテンツ ページに注意してください。 親のプレース ホルダー コントロールからそのコンテンツを取得するコンテンツ コントロールの内部で、すべての子マスターのコンテンツが表示されます。

> [!NOTE]
> デザイナーのサポートでは、入れ子になったマスター ページを使用できません。 入れ子になったマスターを使用して、開発する際は、ソース ビューを使用する必要があります。


このビデオでは、入れ子になったマスター ページの使用に関するチュートリアルを示します。


![](master-pages/_static/image1.png)


[開いているビデオを全画面](master-pages/_static/nested1.wmv)


![マスター ページの選択](master-pages/_static/image4.jpg)

**図 8**: マスター ページの選択
