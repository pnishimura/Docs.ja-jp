---
uid: web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-vb
title: データ バインド スライダー コントロール (VB) |Microsoft ドキュメント
author: wenz
description: AJAX コントロール Toolkit のスライダー コントロールは、マウスを使用して制御できるグラフィカルなスライダーを提供します。 現在の positio をバインドすることはしています.
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: 4f3ba53f-d166-422d-b29c-403348057836
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/slider/databinding-the-slider-control-vb
msc.type: authoredcontent
ms.openlocfilehash: 3ecd8598cd7fdcbbb4812e501bb30fa1f563a054
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30879141"
---
<a name="databinding-the-slider-control-vb"></a>スライダー コントロール (VB) のデータ バインド
====================
によって[Christian Wenz](https://github.com/wenz)

[コードをダウンロードする](http://download.microsoft.com/download/9/3/f/93f8daea-bebd-4821-833b-95205389c7d0/Slider0.vb.zip)または[PDF のダウンロード](http://download.microsoft.com/download/2/d/c/2dc10e34-6983-41d4-9c08-f78f5387d32b/slider0VB.pdf)

> AJAX コントロール Toolkit のスライダー コントロールは、マウスを使用して制御できるグラフィカルなスライダーを提供します。 スライダーの現在の位置を別の ASP.NET コントロールにバインドすることができます。


## <a name="overview"></a>概要

AJAX コントロール Toolkit のスライダー コントロールは、マウスを使用して制御できるグラフィカルなスライダーを提供します。 スライダーの現在の位置を別の ASP.NET コントロールにバインドすることができます。

## <a name="steps"></a>手順

ASP.NET AJAX とコントロール Toolkit の機能をアクティブ化するために、`ScriptManager`コントロールを任意の場所 ページで配置する必要があります (ただし内、`<form>`要素)。

[!code-aspx[Main](databinding-the-slider-control-vb/samples/sample1.aspx)]

次に、2 つ追加`TextBox`ページへのコントロールです。 1 つは、グラフィカルなスライダーに変換され、もう 1 つは、スライダーの位置を保持します。

[!code-aspx[Main](databinding-the-slider-control-vb/samples/sample2.aspx)]

次の手順は、最後の手順では既にです。 `SliderExtender` ASP.NET AJAX コントロール Toolkit からコントロールをスライダー最初のテキスト ボックスの外になり、スライダーの位置が変わったときに、2 つ目のテキスト ボックスが自動的に更新します。 を使用するためには、`SliderExtender`の`TargetControlID`属性は、最初のテキスト ボックスの ID に設定する必要があります、`BoundControlID`属性は、2 つ目のテキスト ボックスの ID に設定する必要があります。

[!code-aspx[Main](databinding-the-slider-control-vb/samples/sample3.aspx)]

データ バインディングの双方向のしくみ、ブラウザーで示すように、: スライダーの位置を更新して、テキスト ボックスに新しい値を入力します。 行う場合、2 つ目のテキスト ボックスは読み取り専用、そこに値を手動で更新するユーザーが困難であるように、テキスト フィールドに弱い保護を追加することがあります。


[![スライダーとテキスト ボックスが同期](databinding-the-slider-control-vb/_static/image2.png)](databinding-the-slider-control-vb/_static/image1.png)

スライダーとテキスト ボックスが同期 ([フルサイズのイメージを表示するをクリックして](databinding-the-slider-control-vb/_static/image3.png))

> [!div class="step-by-step"]
> [前へ](using-the-slider-control-with-auto-postback-vb.md)
