---
title: 'Web-API, die Web-APIs aufruft: Microsoft Identity Platform | Azure'
description: Hier erfahren Sie, wie Sie eine Web-API erstellen, die Web-APIs aufruft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b8eb737752741dc55da24ad6b605b081053b8a11
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443700"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Eine Web-API, die Web-APIs aufruft: Aufrufen einer API

Sobald Sie über ein Token verfügen, können Sie eine geschützte Web-API aufrufen. In der Regel rufen Sie die Downstream-APIs über den Controller oder die Seiten Ihrer Web-API auf.

## <a name="controller-code"></a>Controllercode

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Wenn Sie *Microsoft.Identity.Web* verwenden, gibt es drei Verwendungsszenarien:

- [Option 1: Aufrufen von Microsoft Graph mit dem Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Option 2: Aufrufen einer Downstream-Web-API mit der Hilfsklasse](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Option 3: Aufrufen einer Downstream-Web-API ohne die Hilfsklasse](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Option 1: Aufrufen von Microsoft Graph mit dem SDK

In diesem Szenario haben Sie `.AddMicrosoftGraph()` in der Datei *Startup.cs* (wie unter [Codekonfiguration](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph) angegeben) hinzugefügt, und Sie können den `GraphServiceClient` direkt in den Controller oder Seitenkonstruktor zur Verwendung in den Aktionen einfügen. Auf der folgenden Razor-Beispielseite wird das Foto des angemeldeten Benutzers angezeigt.

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

In diesem Szenario haben Sie `.AddDownstreamWebApi()` in der Datei *Startup.cs* (wie unter [Codekonfiguration](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph) angegeben) hinzugefügt, und Sie können einen `IDownstreamWebApi`-Dienst direkt in den Controller oder Seitenkonstruktor einfügen und in den Aktionen verwenden:

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
```

Die `CallWebApiForUserAsync`-Methode verfügt auch über stark typisierte generische Überschreibungen, mit denen Sie ein Objekt direkt empfangen können. Beispielsweise wurde mit der folgenden Methode eine `Todo`-Instanz empfangen, die eine stark typisierte Darstellung der von der Web-API zurückgegebenen JSON ist.

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

Wenn Sie sich entschieden haben, ein Token mit dem `ITokenAcquisition`-Dienst manuell abzurufen, müssen Sie das Token jetzt verwenden. In diesem Fall setzt der folgende Code den Beispielcode fort, den Sie hier finden: [Web-API, die Web-APIs aufruft: Abrufen eines Tokens für die App](scenario-web-api-call-api-acquire-token.md). Der Code wird in den Aktionen der API-Controller aufgerufen. Er ruft eine nachgelagerte API namens *todolist* auf.

 Nachdem Sie das Token abgerufen haben, können Sie es als Bearertoken zum Aufrufen der nachgelagerten API verwenden.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Der folgende Code setzt den Beispielcode fort, den Sie hier finden: [Eine Web-API, die Web-APIs aufruft: Abrufen eines Tokens für die App](scenario-web-api-call-api-acquire-token.md). Der Code wird in den Aktionen der API-Controller aufgerufen. Er ruft die Downstream-API von Microsoft Graph auf.

Nachdem Sie das Token abgerufen haben, können Sie es als Bearertoken zum Aufrufen der nachgelagerten API verwenden.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Es ist noch kein Beispiel verfügbar, das diesen Flow mit MSAL Python veranschaulicht.

---

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](scenario-web-api-call-api-production.md).
