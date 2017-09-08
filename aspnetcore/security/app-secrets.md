---
title: "アプリ シークレットは、ASP.NET Core での開発中の安全な格納場所"
author: rick-anderson
description: "開発中に機密情報を安全に格納する方法を示します"
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 7/14/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/app-secrets
ms.openlocfilehash: 99a1129549d6b9802315c7e5accfa22907994a41
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2017
---
# <a name="safe-storage-of-app-secrets-during-development-in-aspnet-core"></a><span data-ttu-id="6ab01-104">アプリ シークレットは、ASP.NET Core での開発時の安全な格納場所</span><span class="sxs-lookup"><span data-stu-id="6ab01-104">Safe storage of app secrets during development in ASP.NET Core</span></span>

<a name=security-app-secrets></a>

<span data-ttu-id="6ab01-105">によって[Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、および[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="6ab01-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), and [Scott Addie](https://scottaddie.com)</span></span> 

<span data-ttu-id="6ab01-106">このドキュメントでは、開発でシークレット マネージャー ツールを使用して、コードからシークレットを保持する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="6ab01-106">This document shows how you can use the Secret Manager tool in development to keep secrets out of your code.</span></span> <span data-ttu-id="6ab01-107">最も重要な点は、ソース コードで、パスワードや他の機密データを格納する必要がありますしないと、開発およびテスト モードで運用環境の機密情報を使用しないでください。</span><span class="sxs-lookup"><span data-stu-id="6ab01-107">The most important point is you should never store passwords or other sensitive data in source code, and you shouldn't use production secrets in development and test mode.</span></span> <span data-ttu-id="6ab01-108">代わりに使用することができます、[構成](../fundamentals/configuration.md)システム環境変数からこれらの値を読み取るまたはシークレット マネージャーを使用して格納されている値からツールです。</span><span class="sxs-lookup"><span data-stu-id="6ab01-108">You can instead use the [configuration](../fundamentals/configuration.md) system to read these values from environment variables or from values stored using the Secret Manager tool.</span></span> <span data-ttu-id="6ab01-109">シークレット マネージャー ツールでは、機密データがソース管理にチェックインされているを防ぐのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-109">The Secret Manager tool helps prevent sensitive data from being checked into source control.</span></span> <span data-ttu-id="6ab01-110">[構成](../fundamentals/configuration.md)システムは、この記事で説明されているシークレット マネージャー ツールを使用して格納されているシークレットを読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-110">The [configuration](../fundamentals/configuration.md) system can read secrets stored with the Secret Manager tool described in this article.</span></span>

<span data-ttu-id="6ab01-111">シークレット マネージャー ツールは、開発でのみ使用されます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-111">The Secret Manager tool is used only in development.</span></span> <span data-ttu-id="6ab01-112">Azure のテストおよび運用シークレットを保護することができます、 [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/)構成プロバイダーです。</span><span class="sxs-lookup"><span data-stu-id="6ab01-112">You can safeguard Azure test and production secrets with the [Microsoft Azure Key Vault](https://azure.microsoft.com/services/key-vault/) configuration provider.</span></span> <span data-ttu-id="6ab01-113">参照してください[Azure Key Vault の構成プロバイダー](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration)詳細についてはします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-113">See [Azure Key Vault configuration provider](https://docs.microsoft.com/aspnet/core/security/key-vault-configuration) for more information.</span></span>

## <a name="environment-variables"></a><span data-ttu-id="6ab01-114">環境変数</span><span class="sxs-lookup"><span data-stu-id="6ab01-114">Environment variables</span></span>

<span data-ttu-id="6ab01-115">コードまたはローカルの構成ファイルでは、アプリ シークレットを格納するを回避するのには、環境変数に機密情報を保存します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-115">To avoid storing app secrets in code or in local configuration files, you store secrets in environment variables.</span></span> <span data-ttu-id="6ab01-116">セットアップすることができます、[構成](../fundamentals/configuration.md)を呼び出して環境変数から値を読み取るために、フレームワーク`AddEnvironmentVariables`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-116">You can setup the [configuration](../fundamentals/configuration.md) framework to read values from environment variables by calling `AddEnvironmentVariables`.</span></span> <span data-ttu-id="6ab01-117">環境変数を使用して以前に指定した構成のすべてのソースの構成値を上書きできます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-117">You can then use environment variables to override configuration values for all previously specified configuration sources.</span></span>

<span data-ttu-id="6ab01-118">個々 のユーザー アカウントを持つ新しい ASP.NET Core web アプリを作成する場合に、追加するなど、既定の接続文字列を*される appsettings.json*キーを持つプロジェクト ファイルで`DefaultConnection`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-118">For example, if you create a new ASP.NET Core web app with individual user accounts, it will add a default connection string to the *appsettings.json* file in the project with the key `DefaultConnection`.</span></span> <span data-ttu-id="6ab01-119">既定の接続文字列は、ユーザー モードで実行され、パスワードを必要としない LocalDB を使用するセットアップです。</span><span class="sxs-lookup"><span data-stu-id="6ab01-119">The default connection string is setup to use LocalDB, which runs in user mode and doesn't require a password.</span></span> <span data-ttu-id="6ab01-120">オーバーライドすることができます、アプリケーションをテストまたは実稼働サーバーを展開するときに、`DefaultConnection`で環境変数の設定をテストまたは実稼働データベースの (場合によっては資格情報の機密)、接続文字列を含むキーの値サーバー。</span><span class="sxs-lookup"><span data-stu-id="6ab01-120">When you deploy your application to a test or production server, you can override the `DefaultConnection` key value with an environment variable setting that contains the connection string (potentially with sensitive credentials) for a test or production database server.</span></span>

>[!WARNING]
> <span data-ttu-id="6ab01-121">環境変数は、プレーン テキストで格納されると、暗号化されていません。</span><span class="sxs-lookup"><span data-stu-id="6ab01-121">Environment variables are generally stored in plain text and are not encrypted.</span></span> <span data-ttu-id="6ab01-122">コンピューターまたはプロセスが侵害された場合、環境変数は信頼されていないパーティによってアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-122">If the machine or process is compromised, then environment variables can be accessed by untrusted parties.</span></span> <span data-ttu-id="6ab01-123">追加の手段をユーザーの機密情報の漏えいを防ぐためには、必要な可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6ab01-123">Additional measures to prevent disclosure of user secrets may still be required.</span></span>

## <a name="secret-manager"></a><span data-ttu-id="6ab01-124">シークレット マネージャー</span><span class="sxs-lookup"><span data-stu-id="6ab01-124">Secret Manager</span></span>

<span data-ttu-id="6ab01-125">シークレット マネージャー ツールは、プロジェクト ツリーの外部で開発作業の機密データを格納します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-125">The Secret Manager tool stores sensitive data for development work outside of your project tree.</span></span> <span data-ttu-id="6ab01-126">シークレット マネージャー ツールは、の機密情報の格納に使用できるプロジェクト ツール、 [.NET Core](https://microsoft.com/net/core)開発中のプロジェクトです。</span><span class="sxs-lookup"><span data-stu-id="6ab01-126">The Secret Manager tool is a project tool that can be used to store secrets for a [.NET Core](https://microsoft.com/net/core) project during development.</span></span> <span data-ttu-id="6ab01-127">シークレット マネージャー ツール アプリ シークレットは、特定のプロジェクトに関連付けるでき、それらを複数のプロジェクト間で共有できます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-127">With the Secret Manager tool, you can associate app secrets with a specific project and share them across multiple projects.</span></span>

>[!WARNING]
> <span data-ttu-id="6ab01-128">シークレット マネージャー ツールは、格納された機密情報は暗号化されず、信頼できるストアとして扱うことはできません。</span><span class="sxs-lookup"><span data-stu-id="6ab01-128">The Secret Manager tool does not encrypt the stored secrets and should not be treated as a trusted store.</span></span> <span data-ttu-id="6ab01-129">これは、開発目的でのみです。</span><span class="sxs-lookup"><span data-stu-id="6ab01-129">It is for development purposes only.</span></span> <span data-ttu-id="6ab01-130">キーと値は、ユーザーのプロファイル ディレクトリに JSON 構成ファイルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-130">The keys and values are stored in a JSON configuration file in the user profile directory.</span></span>

### <a name="visual-studio-2017-installing-the-secret-manager-tool"></a><span data-ttu-id="6ab01-131">Visual Studio 2017: シークレット マネージャー ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-131">Visual Studio 2017: Installing the Secret Manager tool</span></span>

<span data-ttu-id="6ab01-132">ソリューション エクスプ ローラーでプロジェクトを右クリックし **編集\<project_name\>.csproj**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="6ab01-132">Right-click the project in Solution Explorer, and select **Edit \<project_name\>.csproj** from the context menu.</span></span> <span data-ttu-id="6ab01-133">強調表示された行を追加、 *.csproj*ファイル、および関連付けられている NuGet パッケージを復元する保存します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-133">Add the highlighted line to the *.csproj* file, and save to restore the associated NuGet package:</span></span>

<span data-ttu-id="6ab01-134">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]</span><span class="sxs-lookup"><span data-stu-id="6ab01-134">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]</span></span>

<span data-ttu-id="6ab01-135">ソリューション エクスプ ローラーでプロジェクトを右クリックし **管理ユーザーの機密情報**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="6ab01-135">Right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6ab01-136">このジェスチャが新しく追加`UserSecretsId`内のノード、`PropertyGroup`の*.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-136">This gesture adds a new `UserSecretsId` node within a `PropertyGroup` of the *.csproj* file.</span></span> <span data-ttu-id="6ab01-137">これを開くことも、`secrets.json`ファイル テキスト エディターでします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-137">It also opens a `secrets.json` file in the text editor.</span></span>

<span data-ttu-id="6ab01-138">`secrets.json` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-138">Add the following to `secrets.json`:</span></span>

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-2015-installing-the-secret-manager-tool"></a><span data-ttu-id="6ab01-139">Visual Studio 2015: シークレット マネージャー ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-139">Visual Studio 2015: Installing the Secret Manager tool</span></span>

<span data-ttu-id="6ab01-140">プロジェクトの`project.json`ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-140">Open the project's `project.json` file.</span></span> <span data-ttu-id="6ab01-141">参照を追加`Microsoft.Extensions.SecretManager.Tools`内で、`tools`プロパティ、および関連付けられている NuGet パッケージを復元する保存。</span><span class="sxs-lookup"><span data-stu-id="6ab01-141">Add a reference to `Microsoft.Extensions.SecretManager.Tools` within the `tools` property, and save to restore the associated NuGet package:</span></span>

```json
"tools": {
    "Microsoft.Extensions.SecretManager.Tools": "1.0.0-preview2-final",
    "Microsoft.AspNetCore.Server.IISIntegration.Tools": "1.0.0-preview2-final"
},
```

<span data-ttu-id="6ab01-142">ソリューション エクスプ ローラーでプロジェクトを右クリックし **管理ユーザーの機密情報**コンテキスト メニュー。</span><span class="sxs-lookup"><span data-stu-id="6ab01-142">Right-click the project in Solution Explorer, and select **Manage User Secrets** from the context menu.</span></span> <span data-ttu-id="6ab01-143">このジェスチャが新しく追加`userSecretsId`プロパティを`project.json`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-143">This gesture adds a new `userSecretsId` property to `project.json`.</span></span> <span data-ttu-id="6ab01-144">これを開くことも、`secrets.json`ファイル テキスト エディターでします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-144">It also opens a `secrets.json` file in the text editor.</span></span>

<span data-ttu-id="6ab01-145">`secrets.json` に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-145">Add the following to `secrets.json`:</span></span>

```json
{
    "MySecret": "ValueOfMySecret"
}
```

### <a name="visual-studio-code-or-command-line-installing-the-secret-manager-tool"></a><span data-ttu-id="6ab01-146">Visual Studio Code またはコマンド ライン: シークレット マネージャー ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-146">Visual Studio Code or Command Line: Installing the Secret Manager tool</span></span>

<span data-ttu-id="6ab01-147">追加`Microsoft.Extensions.SecretManager.Tools`を*.csproj*ファイル実行して`dotnet restore`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-147">Add `Microsoft.Extensions.SecretManager.Tools` to the *.csproj* file and run `dotnet restore`.</span></span>

<span data-ttu-id="6ab01-148">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]</span><span class="sxs-lookup"><span data-stu-id="6ab01-148">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?highlight=21)]</span></span>

<span data-ttu-id="6ab01-149">次のコマンドを実行して、シークレット マネージャー ツールをテストします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-149">Test the Secret Manager tool by running the following command:</span></span>

```console
dotnet user-secrets -h
```

<span data-ttu-id="6ab01-150">シークレット マネージャー ツールは、使用、オプションおよびコマンドのヘルプに表示されます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-150">The Secret Manager tool will display usage, options and command help.</span></span>

> [!NOTE]
> <span data-ttu-id="6ab01-151">同じディレクトリにする必要があります、 *.csproj*で定義されているツールを実行するファイル、 *.csproj*ファイルの`DotNetCliToolReference`ノード。</span><span class="sxs-lookup"><span data-stu-id="6ab01-151">You must be in the same directory as the *.csproj* file to run tools defined in the *.csproj* file's `DotNetCliToolReference` nodes.</span></span>

<span data-ttu-id="6ab01-152">シークレット マネージャー ツールは、ユーザー プロファイルに格納されているプロジェクトに固有の構成設定で動作します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-152">The Secret Manager tool operates on project-specific configuration settings that are stored in your user profile.</span></span> <span data-ttu-id="6ab01-153">ユーザーの機密情報を使用するプロジェクトを指定する必要があります、`UserSecretsId`値でその*.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-153">To use user secrets, the project must specify a `UserSecretsId` value in its *.csproj* file.</span></span> <span data-ttu-id="6ab01-154">値`UserSecretsId`任意で、一般にプロジェクトに固有です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-154">The value of `UserSecretsId` is arbitrary, but is generally unique to the project.</span></span> <span data-ttu-id="6ab01-155">開発者が通常の GUID を生成、`UserSecretsId`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-155">Developers typically generate a GUID for the `UserSecretsId`.</span></span>

<span data-ttu-id="6ab01-156">追加、`UserSecretsId`に、プロジェクトの*.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-156">Add a `UserSecretsId` for your project in the *.csproj* file:</span></span>

<span data-ttu-id="6ab01-157">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?range=7-9&highlight=2)]</span><span class="sxs-lookup"><span data-stu-id="6ab01-157">[!code-xml[Main](app-secrets/sample/UserSecrets/UserSecrets.csproj?range=7-9&highlight=2)]</span></span>

<span data-ttu-id="6ab01-158">シークレット マネージャー ツールを使用すると、機密情報を設定します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-158">Use the Secret Manager tool to set a secret.</span></span> <span data-ttu-id="6ab01-159">たとえば、コマンド ウィンドウで、プロジェクト ディレクトリから、次のように入力します。</span><span class="sxs-lookup"><span data-stu-id="6ab01-159">For example, in a command window from the project directory, enter the following:</span></span>

```console
dotnet user-secrets set MySecret ValueOfMySecret
```

<span data-ttu-id="6ab01-160">その他のディレクトリからシークレット マネージャー ツールを実行することができますが、使用する必要があります、`--project`へのパスに渡すオプション、 *.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-160">You can run the Secret Manager tool from other directories, but you must use the `--project` option to pass in the path to the *.csproj* file:</span></span>
 
```console
dotnet user-secrets set MySecret ValueOfMySecret --project c:\work\WebApp1\src\webapp1
```

<span data-ttu-id="6ab01-161">シークレット マネージャー ツールを使用して、一覧を削除し、アプリのシークレットをオフにすることができますも。</span><span class="sxs-lookup"><span data-stu-id="6ab01-161">You can also use the Secret Manager tool to list, remove and clear app secrets.</span></span>

## <a name="accessing-user-secrets-via-configuration"></a><span data-ttu-id="6ab01-162">構成を使用してユーザーの機密情報にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-162">Accessing user secrets via configuration</span></span>

<span data-ttu-id="6ab01-163">構成システムを介したシークレット Manager の機密情報にアクセスします。</span><span class="sxs-lookup"><span data-stu-id="6ab01-163">You access Secret Manager secrets through the configuration system.</span></span> <span data-ttu-id="6ab01-164">追加、`Microsoft.Extensions.Configuration.UserSecrets`パッケージ化し、実行`dotnet restore`です。</span><span class="sxs-lookup"><span data-stu-id="6ab01-164">Add the `Microsoft.Extensions.Configuration.UserSecrets` package and run `dotnet restore`.</span></span>

<span data-ttu-id="6ab01-165">ユーザーの機密情報の構成ソースを追加、`Startup`メソッド。</span><span class="sxs-lookup"><span data-stu-id="6ab01-165">Add the user secrets configuration source to the `Startup` method:</span></span>

<span data-ttu-id="6ab01-166">[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]</span><span class="sxs-lookup"><span data-stu-id="6ab01-166">[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=16-19)]</span></span>

<span data-ttu-id="6ab01-167">構成 API を使用してユーザーの機密情報にアクセスすることができます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-167">You can access user secrets via the configuration API:</span></span>

<span data-ttu-id="6ab01-168">[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]</span><span class="sxs-lookup"><span data-stu-id="6ab01-168">[!code-csharp[Main](app-secrets/sample/UserSecrets/Startup.cs?highlight=26-29)]</span></span>

## <a name="how-the-secret-manager-tool-works"></a><span data-ttu-id="6ab01-169">シークレット マネージャー ツールの動作</span><span class="sxs-lookup"><span data-stu-id="6ab01-169">How the Secret Manager tool works</span></span>

<span data-ttu-id="6ab01-170">値が格納されている場所と方法など、実装の詳細を抽象シークレット Manager ツール。</span><span class="sxs-lookup"><span data-stu-id="6ab01-170">The Secret Manager tool abstracts away the implementation details, such as where and how the values are stored.</span></span> <span data-ttu-id="6ab01-171">これらの実装の詳細を知ることがなくツールを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="6ab01-171">You can use the tool without knowing these implementation details.</span></span> <span data-ttu-id="6ab01-172">現在のバージョンで、値が格納されている、 [JSON](http://json.org/)ユーザーのプロファイル ディレクトリに構成ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-172">In the current version, the values are stored in a [JSON](http://json.org/) configuration file in the user profile directory:</span></span>

* <span data-ttu-id="6ab01-173">Windows:`%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`</span><span class="sxs-lookup"><span data-stu-id="6ab01-173">Windows: `%APPDATA%\microsoft\UserSecrets\<userSecretsId>\secrets.json`</span></span>

* <span data-ttu-id="6ab01-174">Linux:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span><span class="sxs-lookup"><span data-stu-id="6ab01-174">Linux: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span></span>

* <span data-ttu-id="6ab01-175">Mac:`~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span><span class="sxs-lookup"><span data-stu-id="6ab01-175">Mac: `~/.microsoft/usersecrets/<userSecretsId>/secrets.json`</span></span>

<span data-ttu-id="6ab01-176">値`userSecretsId`で指定された値に由来*.csproj*ファイル。</span><span class="sxs-lookup"><span data-stu-id="6ab01-176">The value of `userSecretsId` comes from the value specified in *.csproj* file.</span></span>

<span data-ttu-id="6ab01-177">これらの実装の詳細を変更する可能性があります、場所やシークレット マネージャー ツールを使用して保存するデータの形式に依存するコードを記述する必要がありますされません。</span><span class="sxs-lookup"><span data-stu-id="6ab01-177">You should not write code that depends on the location or format of the data saved with the Secret Manager tool, as these implementation details might change.</span></span> <span data-ttu-id="6ab01-178">たとえば、シークレットの値が現在は*いない*今日では、暗号化しますが、いつか可能性があります。</span><span class="sxs-lookup"><span data-stu-id="6ab01-178">For example, the secret values are currently *not* encrypted today, but could be someday.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6ab01-179">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="6ab01-179">Additional Resources</span></span>

* [<span data-ttu-id="6ab01-180">構成</span><span class="sxs-lookup"><span data-stu-id="6ab01-180">Configuration</span></span>](../fundamentals/configuration.md)