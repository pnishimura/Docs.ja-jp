# <a name="response-compression-sample-application-aspnet-core-1x"></a><span data-ttu-id="1ac8f-101">応答の圧縮サンプル アプリケーション (ASP.NET Core 1.x)</span><span class="sxs-lookup"><span data-stu-id="1ac8f-101">Response compression sample application (ASP.NET Core 1.x)</span></span>

<span data-ttu-id="1ac8f-102">このサンプルは、ASP.NET Core の使用方法を示します 1.x の HTTP 応答を圧縮する応答の圧縮のミドルウェア。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-102">This sample illustrates the use of ASP.NET Core 1.x Response Compression Middleware to compress HTTP responses for.</span></span> <span data-ttu-id="1ac8f-103">このサンプルでは、Gzip とテキストおよびイメージ応答の圧縮のカスタム プロバイダーを示していて、圧縮の MIME の種類を追加する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-103">The sample demonstrates Gzip and custom compression providers for text and image responses and shows how to add a MIME type for compression.</span></span> <span data-ttu-id="1ac8f-104">ASP.NET Core 2.x サンプルでは、次を参照してください。[応答の圧縮サンプル アプリケーション (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x)です。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-104">For the ASP.NET Core 2.x sample, see [Response compression sample application (ASP.NET Core 2.x)](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/response-compression/samples/2.x).</span></span>

## <a name="examples-in-this-sample"></a><span data-ttu-id="1ac8f-105">このサンプルの例</span><span class="sxs-lookup"><span data-stu-id="1ac8f-105">Examples in this sample</span></span>
* `GzipCompressionProvider`
  * `text/plain`
    * <span data-ttu-id="1ac8f-106">**/**-927 バイトに圧縮する 2,044 バイトで Lorem Ipsum テキスト ファイルの応答</span><span class="sxs-lookup"><span data-stu-id="1ac8f-106">**/** - Lorem Ipsum text file response at 2,044 bytes that will compress to 927 bytes</span></span>
    * <span data-ttu-id="1ac8f-107">**/testfile1kb.txt** -47 バイトに圧縮する 1,033 バイトでテキスト ファイルの応答</span><span class="sxs-lookup"><span data-stu-id="1ac8f-107">**/testfile1kb.txt** - Text file response at 1,033 bytes that will compress to 47 bytes</span></span>
    * <span data-ttu-id="1ac8f-108">**トリクル/** -1 秒間隔で 1 つの文字として発行される応答</span><span class="sxs-lookup"><span data-stu-id="1ac8f-108">**/trickle** - Response issued as single characters at 1 second intervals</span></span> 
  * `image/svg+xml`
    * <span data-ttu-id="1ac8f-109">**/banner.svg** -4,459 バイトに圧縮する 9,707 バイトで A スケーラブル ベクター グラフィックス (SVG) のイメージの応答</span><span class="sxs-lookup"><span data-stu-id="1ac8f-109">**/banner.svg** - A Scalable Vector Graphics (SVG) image response at 9,707 bytes that will compress to 4,459 bytes</span></span>
* `CustomCompressionProvider`<br><span data-ttu-id="1ac8f-110">ミドルウェアで使用するためのカスタム圧縮プロバイダーを実装する方法を示します</span><span class="sxs-lookup"><span data-stu-id="1ac8f-110">Shows how to implement a custom compression provider for use with the middleware</span></span>

<span data-ttu-id="1ac8f-111">要求が含まれています、`Accept-Encoding`ヘッダー、サンプルは、追加、`Vary: Accept-Encoding`応答ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-111">When the request includes the `Accept-Encoding` header, the sample adds a `Vary: Accept-Encoding` header to the response.</span></span> <span data-ttu-id="1ac8f-112">`Vary`ヘッダーの代替値に基づいて、応答の複数のコピーを維持するためにキャッシュするよう指示`Accept-Encoding`かシステムで、圧縮を受け入れるためのキャッシュに保存する (gzip) の圧縮と圧縮されていないバージョンの両方、または圧縮されていない応答です。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-112">The `Vary` header instructs caches to maintain multiple copies of the response based on alternative values of `Accept-Encoding`, so both a compressed (gzip) and uncompressed version are stored in caches for systems that can either accept the compressed or the uncompressed response.</span></span>

## <a name="using-the-sample"></a><span data-ttu-id="1ac8f-113">サンプルの使用</span><span class="sxs-lookup"><span data-stu-id="1ac8f-113">Using the sample</span></span>
1. <span data-ttu-id="1ac8f-114">要求を使用して作成[Fiddler](http://www.telerik.com/fiddler)、 [Firebug](http://getfirebug.com/)、または[Postman](https://www.getpostman.com/)なしで、アプリケーションに、`Accept-Encoding`ヘッダーと応答のペイロード応答のサイズと応答ヘッダー。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-114">Make a request using [Fiddler](http://www.telerik.com/fiddler), [Firebug](http://getfirebug.com/), or [Postman](https://www.getpostman.com/) to the application without an `Accept-Encoding` header and note the response payload, response size, and response headers.</span></span>
2. <span data-ttu-id="1ac8f-115">追加、`Accept-Encoding: gzip`ヘッダーおよび応答ヘッダーと圧縮された応答のサイズに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-115">Add an `Accept-Encoding: gzip` header and note the compressed response size and response headers.</span></span> <span data-ttu-id="1ac8f-116">削除するには、応答のサイズを参照してください、`Content-Encoding: gzip`サンプル アプリで応答ヘッダーが含まれます。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-116">You see the response size drop, and the `Content-Encoding: gzip` response header is included by the sample app.</span></span> <span data-ttu-id="1ac8f-117">Lorem Ipsum の応答本文の検索または**testfile1kb.txt**応答、表示のテキストが圧縮、または読み取り不可能です。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-117">When you look at the response body for the Lorem Ipsum or **testfile1kb.txt** response, you see that the text is compressed and unreadable.</span></span>
3. <span data-ttu-id="1ac8f-118">追加、`Accept-Encoding: mycustomcompression`ヘッダーおよび応答ヘッダーに注意してください。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-118">Add an `Accept-Encoding: mycustomcompression` header and note the response headers.</span></span> <span data-ttu-id="1ac8f-119">`CustomCompressionProvider` 、応答は実際には圧縮されず、空の実装のラッパーでカスタム圧縮ストリームを作成することができますが、`CreateStream()`メソッドです。</span><span class="sxs-lookup"><span data-stu-id="1ac8f-119">The `CustomCompressionProvider` is an empty implementation that doesn't actually compress the response, but you can create a custom compression stream wrapper for the `CreateStream()` method.</span></span>