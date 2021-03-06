---
uid: identity/overview/migrations/migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity
title: メンバーシップと ASP.NET Identity (c#) するユーザー プロファイル用のユニバーサル プロバイダーにデータの移行 |Microsoft ドキュメント
author: rustd
description: このチュートリアルでは、ユーザーおよびロールのデータと、既存アプリのユニバーサル プロバイダーを使用して作成されたユーザー プロファイル データを移行するために必要な手順について説明しています.
ms.author: aspnetcontent
manager: wpickett
ms.date: 12/13/2013
ms.topic: article
ms.assetid: 2e260430-d13c-4658-bd05-e256fc0d63b8
ms.technology: ''
ms.prod: .net-framework
msc.legacyurl: /identity/overview/migrations/migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: f65f93b20543d06ea70a9009b6921e297477c99e
ms.sourcegitcommit: f8852267f463b62d7f975e56bea9aa3f68fbbdeb
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30871572"
---
<a name="migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity-c"></a>メンバーシップと ASP.NET Identity (c#) するユーザー プロファイル用のユニバーサル プロバイダー データの移行
====================
によって[Pranav Rastogi](https://github.com/rustd)、 [Rick Anderson](https://github.com/Rick-Anderson)、 [Robert McMurray](https://github.com/rmcmurray)、 [Suhas Joshi](https://github.com/suhasj)

> このチュートリアルでは、ユーザーおよびロールのデータと、ASP.NET Identity モデルに既存のアプリケーションのユニバーサル プロバイダーを使用して作成されたユーザー プロファイル データを移行するために必要な手順について説明します。 ここで説明した方法はユーザー プロファイル データを移行すると、SQL のメンバーシップも使用してアプリケーションで使用できます。


Visual Studio 2013 のリリースで ASP.NET チームは、新しい ASP.NET Id システムを導入し、詳細を読み取ることができます、リリースに関する[ここ](../../index.md)です。 Web アプリケーションを移行するには、そのアーティクルでフォロー アップ[新しい Id システムに SQL メンバーシップ](migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)、この記事は、次のユーザーとロール管理のためのプロバイダー モデルを既存のアプリケーションを移行する手順を示しています。新しい Identity モデル。 このチュートリアルの焦点は、シームレスにそれを新しいシステムにフックするユーザー プロファイル データを移行する方法、主になります。 移行するユーザーおよびロールの詳細については、SQL メンバーシップ用に似ています。 プロファイル データの移行に書き込み、続けてアプローチは、SQL のメンバーシップも使用してアプリケーションで使用できます。

たとえば、プロバイダー モデルを使用する Visual Studio 2012 を使用して作成された web アプリを開始します。 おをプロファイルの管理にコードを追加、ユーザーを登録、ユーザーのプロファイル データを追加する、データベース スキーマを移行し、ユーザーおよびロールの管理に Id システムを使用するアプリケーションを変更します。 移行のテストとして Universal Providers を使用して作成できる必要がありますにログインして新しいユーザーを登録することにする必要があります。

> [!NOTE]
> 完全なサンプルを見つけることができます[ https://github.com/suhasj/UniversalProviders-Identity-Migrations](https://github.com/suhasj/UniversalProviders-Identity-Migrations)です。


## <a name="profile-data-migration-summary"></a>プロファイル データの移行の概要

以降、移行では、前にプロバイダー モデルでプロファイル データを格納する場合の動作について見てみましょう。 ユーザーは、複数の方法で格納できるアプリケーションのプロファイル データ、それらの間で最も一般的なされているプロバイダーを使用して、組み込みのプロファイル Universal Providers と共に出荷します。 手順が含まれます

1. プロファイル データを格納するためのプロパティを持つクラスを追加します。
2. 'ProfileBase' を拡張し、ユーザーの上記のプロファイル データを取得するメソッドを実装するクラスを追加します。
3. 既定のプロファイル プロバイダーを使用して有効にする、 *web.config*ファイルし、プロファイル情報へのアクセスに使用される 2 の手順で宣言されたクラスを定義します。

プロファイル情報は、シリアル化された xml と、データベース内の 'プロファイル' テーブル内のバイナリ データとして格納されます。

新しい ASP.NET Id システムを使用するアプリケーションを移行した後、プロファイル情報が逆シリアル化し、ユーザー クラスのプロパティとして格納します。 各プロパティは、ユーザー テーブル内の列にマップできます。 プロパティをデータの情報をシリアル化/逆シリアル化する必要がないだけでなく、ユーザー クラスを使用して直接に作業できることの利点は、アクセスするときの時刻します。

## <a name="getting-started"></a>作業の開始

1. Visual Studio 2012 では、新しい ASP.NET 4.5 Web フォーム アプリケーションを作成します。 現在のサンプルが Web フォーム テンプレートを使用しますが、MVC アプリケーションにも使用できます。  
    ![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image1.jpg)
2. 'モデル' プロファイル情報を格納する新しいフォルダーを作成します。  
    ![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image1.png)
3. 例として、プロファイルに生年月日、市区町村、高さ、ユーザーの重みの日付を格納お知らせします。 幅と高さは、'PersonalStats' と呼ばれるカスタム クラスとして格納されます。 保存して、プロファイルを取得、'ProfileBase' を拡張するクラスが必要です。 新しいクラスを取得し、プロファイル情報を格納するには、' AppProfile' を作成してみましょう。

    [!code-csharp[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample1.cs)]
4. プロファイルを有効にする、 *web.config*ファイル。 手順 3 で作成したユーザーの情報を格納および取得するために使用するクラス名を入力します。

    [!code-xml[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample2.xml)]
5. ユーザーのプロファイル データを取得し、保存 'Account' フォルダー内には、web フォーム ページを追加します。 プロジェクトを右クリックし、新しい項目の追加 を選択します。 マスター ページ 'AddProfileData.aspx' で新しい webforms ページを追加します。 'メイン' セクションで、次をコピーします。

    [!code-html[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample3.html)]

   分離コードで、次のコードを追加します。

    [!code-csharp[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample4.cs)]

   コンパイル エラーを削除するどの AppProfile 下にあるクラスが定義されている名前空間を追加します。
6. アプリを実行して、ユーザー名を持つ新しいユーザーの作成 '**olduser' です。** 'AddProfileData' のページに移動し、ユーザーのプロファイル情報を追加します。  
    ![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image2.png)

サーバー エクスプ ローラー ウィンドウを使用して 'プロファイル' テーブルのシリアル化された xml としてデータが格納されていることを確認することができます。 Visual Studio で、表示 メニューから ' サーバー エクスプ ローラー を選択します。 定義されているデータベースのデータ接続が存在する必要があります、 *web.config*ファイル。 データ接続をクリックすると、別のサブ カテゴリを示します。 データベースで別のテーブルを表示し、'プロファイル' を右クリックし、プロファイル テーブルに格納されているプロファイル データを表示する ' テーブル データの表示 ' を選択するには、' テーブル' を展開します。

![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image3.png)

![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image4.png)

## <a name="migrating-database-schema"></a>データベース スキーマを移行します。

Id システムを使用する既存のデータベースにするには、元のデータベースに追加したフィールドをサポートするためにユーザー データベースでスキーマを更新する必要があります。 これを行う SQL スクリプトを使用する新しいテーブルを作成し、既存の情報をコピーします。 ' [サーバー エクスプ ローラー] ウィンドウで、テーブルを表示する 'DefaultConnection' を展開します。 テーブルを右クリックし、[新規クエリ] を選択

![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image5.png)

SQL スクリプトを貼り付けます[ https://raw.github.com/suhasj/UniversalProviders-Identity-Migrations/master/Migration.txt ](https://raw.github.com/suhasj/UniversalProviders-Identity-Migrations/master/Migration.txt)し実行します。 'DefaultConnection' が更新される場合は、新しいテーブルが追加されたことを確認できます。 情報が移行されたことを確認するテーブル内のデータを確認することができます。

![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image6.png)

## <a name="migrating-the-application-to-use-aspnet-identity"></a>ASP.NET の Id を使用するアプリケーションへの移行

1. ASP.NET Identity の必要な Nuget パッケージをインストールします。

    - Microsoft.AspNet.Identity.EntityFramework
    - Microsoft.AspNet.Identity.Owin
    - Microsoft.Owin.Host.SystemWeb
    - Microsoft.Owin.Security.Facebook
    - Microsoft.Owin.Security.Google
    - Microsoft.Owin.Security.MicrosoftAccount
    - Microsoft.Owin.Security.Twitter

   Nuget パッケージの管理の詳細についてを参照できます[ここ](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog)
2. テーブル内の既存のデータを操作するには、テーブルにマップされ、Id システムにフック モデル クラスを作成する必要があります。 Identity コントラクトの一部として、モデル クラスは Identity.Core dll で定義されているインターフェイスを実装する必要がありますか、または Microsoft.AspNet.Identity.EntityFramework で利用できるこれらのインターフェイスの既存の実装を拡張することができます。 使用する既存のクラスの役割、ユーザーのログインおよびユーザーの信頼性情報のです。 このサンプルのカスタム ユーザーを使用する必要があります。 プロジェクトを右クリックし、新しいフォルダー 'IdentityModels' を作成します。 次に示すように、新しい 'User' クラスを追加します。

    [!code-csharp[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample5.cs)]

   'ProfileInfo' が、ユーザー クラスのプロパティになったことに注意してください。 そのためのプロファイル データを直接操作するユーザー クラスを使用できます。

内のファイルをコピー、 **IdentityModels**と**IdentityAccount**ダウンロード元のフォルダー ( [ https://github.com/suhasj/UniversalProviders-Identity-Migrations/tree/master/UniversalProviders-Identity-Migrations ](https://github.com/suhasj/UniversalProviders-Identity-Migrations/tree/master/UniversalProviders-Identity-Migrations) )。 これらは、残りのモデル クラスおよびユーザーと、ASP.NET Identity の Api を使用してロールの管理に必要な新しいページがあります。 使用されている方法は、SQL メンバーシップに似ており、詳細についてを参照して[ここ](migrating-an-existing-website-from-sql-membership-to-aspnet-identity.md)です。

## <a name="copying-profile-data-to-the-new-tables"></a>新しいテーブルにプロファイル データのコピー

前述のように、プロファイル テーブル内の xml データを逆シリアル化し、AspNetUsers テーブルの列に格納する必要があります。 新しい列は、ため、これらの列に必要なデータを設定することですが、残っているすべて前の手順で、ユーザー テーブルで作成されました。 これを行うには、ユーザー テーブルに新しく作成された列の設定に 1 回実行され、コンソール アプリケーションを使用します。

1. 既存のソリューションに新しいコンソール アプリケーションを作成します。  
    ![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image2.jpg)
2. Entity Framework パッケージの最新バージョンをインストールします。
3. コンソール アプリケーションへの参照として上記で作成した web アプリケーションを追加します。 行うにはこのプロジェクトを右クリック、し、' 参照の追加 ' で、ソリューション、プロジェクトをクリックし、[ok] をクリックします。
4. コピー、Program.cs クラスのコードの下。 このロジックは、'ProfileInfo' のオブジェクトとしてシリアル化して、およびデータベースに格納する各ユーザーのプロファイル データを読み取ります。

    [!code-csharp[Main](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/samples/sample6.cs)]

   使用モデルの一部は定義されて、web アプリケーション プロジェクトの 'IdentityModels' フォルダーにため、対応する名前空間を含める必要があります。
5. 上記のコードは、アプリ内のデータベース ファイルで動作\_前の手順で、web アプリケーション プロジェクトのデータ フォルダーを作成します。 それを参照するには、web アプリケーションの web.config に接続文字列で、コンソール アプリケーションの app.config ファイル内の接続文字列を更新します。 また、'AttachDbFilename' プロパティに完全な物理パスを提供します。
6. コマンド プロンプトを開き、上記のコンソール アプリケーションの bin フォルダーに移動します。 実行可能ファイルを実行し、次の図のように、ログの出力を確認します。  
    ![](migrating-universal-provider-data-for-membership-and-user-profiles-to-aspnet-identity/_static/image3.jpg)
7. サーバー エクスプ ローラーで 'AspNetUsers' テーブルを開き、プロパティを保持する新しい列のデータを確認します。 対応するプロパティ値で更新される必要があります。

## <a name="verify-functionality"></a>機能を確認します。

古いデータベースからユーザーをログインに ASP.NET の Id を使用して実装されている、新しく追加されたメンバーシップ ページを使用します。 ユーザーは、同じ資格情報を使用してログインできる必要があります。 ユーザーをロールなどを追加する OAuth など、パスワードの変更、新しいユーザーを作成するの役割の追加の追加などの他の機能を実行してください。

古いユーザーと新しいユーザーのプロファイル データが取得され、ユーザー テーブルに格納されるべきです。 古いテーブルを参照できなくする必要があります。

## <a name="conclusion"></a>まとめ

アーティクルでは、ASP.NET Identity のメンバーシップのプロバイダー モデルを使用する web アプリケーションの移行のプロセスについて説明します。 さらに、アーティクルには、Id システムにフックするユーザーのプロファイル データの移行が記載されています。 以下の質問と、アプリを移行するときに発生した問題のコメントを入力してください。

*Rick Anderson および Robert McMurray、記事のレビューいただきありがとうございます。*
