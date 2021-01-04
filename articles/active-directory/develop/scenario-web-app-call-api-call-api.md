---
title: Aufrufen einer Web-API über eine Web-App | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine Web-App erstellen, die Web-APIs aufruft (Aufrufen einer geschützten Web-API).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 016a2917c02deaecf35c526426cb2a6dbc89e338
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173188"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Web-App, die Web-APIs aufruft: Aufrufen einer Web-API

Da Sie nun über ein Token verfügen, können Sie eine geschützte Web-API aufrufen. In der Regel rufen Sie eine Downstream-API über den Controller oder die Seiten Ihrer Web-App auf.

## <a name="call-a-protected-web-api"></a>Aufrufen einer geschützten Web-API

Das Aufrufen einer geschützten Web-API hängt von der verwendeten Programmiersprache und dem Framework ab:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Bei Verwendung von *Microsoft.Identity.Web* stehen Ihnen drei Optionen zum Aufrufen einer API zur Verfügung:

- [Option 1: Aufrufen von Microsoft Graph mit dem Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Option 2: Aufrufen einer Downstream-Web-API mit der Hilfsklasse](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Option 3: Aufrufen einer Downstream-Web-API ohne die Hilfsklasse](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Option 1: Aufrufen von Microsoft Graph mit dem SDK

Sie möchten Microsoft Graph aufrufen. In diesem Szenario haben Sie `AddMicrosoftGraph` in der Datei *Startup.cs* (wie unter [Codekonfiguration](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph) angegeben) hinzugefügt, und Sie können den `GraphServiceClient` direkt in den Controller oder Seitenkonstruktor zur Verwendung in den Aktionen einfügen. Auf der folgenden Razor-Beispielseite wird das Foto des angemeldeten Benutzers angezeigt.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Option 2: Aufrufen einer Downstream-Web-API mit der Hilfsklasse

Sie möchten eine andere Web-API als Microsoft Graph aufrufen. In diesem Fall haben Sie `AddDownstreamWebApi` in der Datei *Startup.cs* (wie unter [Codekonfiguration](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph) angegeben) hinzugefügt, und Sie können einen `IDownstreamWebApi`-Dienst direkt in den Controller oder Seitenkonstruktor einfügen und in den Aktionen verwenden:

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync` verfügt auch über stark typisierte generische Überschreibungen, mit denen Sie ein Objekt direkt empfangen können. Beispielsweise wird mit der folgenden Methode eine `Todo`-Instanz empfangen, die eine stark typisierte Darstellung der von der Web-API zurückgegebenen JSON ist.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Option 3: Aufrufen einer Downstream-Web-API ohne die Hilfsklasse

Sie haben sich entschieden, ein Token mit dem `ITokenAcquisition`-Dienst manuell abzurufen, und jetzt müssen Sie das Token verwenden. In diesem Fall setzt der folgende Code den Beispielcode fort, den Sie hier finden: [Web-App, die Web-APIs aufruft: Abrufen eines Tokens für die App](scenario-web-app-call-api-acquire-token.md). Der Code wird in den Aktionen der Web-App-Controller aufgerufen.

Nachdem Sie das Token abgerufen haben, können Sie es als Bearertoken zum Aufrufen der Downstream-API (in diesem Fall Microsoft Graph) verwenden.

 ```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```
> [!NOTE]
> Sie können dasselbe Prinzip für den Aufruf jeder anderen Web-API verwenden.
>
> Die meisten Azure-Web-APIs bieten ein SDK zur Vereinfachung des API-Aufrufs, wie es auch bei Microsoft Graph der Fall ist. Ein Beispiel für eine Web-App, die Microsoft.Identity.Web und das Azure Storage SDK verwendet, finden Sie unter [Erstellen einer Webanwendung, die Zugriff auf Blob Storage über Azure AD gewährt](../../storage/common/storage-auth-aad-app.md?tabs=dotnet&toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](scenario-web-app-call-api-production.md).