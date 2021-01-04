---
title: Überprüfen von Bereichen und App-Rollen mit geschützter Web-API | Azure
titleSuffix: Microsoft identity platform
description: Vergewissern Sie sich, dass die API nur von Anwendungen mit den richtigen Bereichen (im Namen von Benutzern) und von Daemon-Apps mit den richtigen Anwendungsrollen aufgerufen wird.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442904"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Geschützte Web-API: Überprüfen von Bereichen und App-Rollen

Dieser Artikel beschreibt, wie Sie Ihrer Web-API eine Berechtigung hinzufügen können. Dieser Schutz stellt sicher, dass die API nur aufgerufen wird von:

- Anwendungen im Auftrag von Benutzern, die über die richtigen Bereiche verfügen
- Daemon-Apps mit den richtigen Anwendungsrollen

> [!NOTE]
> Die Codeausschnitte in diesem Artikel stammen aus den folgenden Codebeispielen auf GitHub:
>
> - [Inkrementelles Tutorial zur ASP.NET Core-Web-API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [Beispiel zur ASP.NET-Web-API](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

Um eine ASP.NET- oder ASP.NET Core-Web-API zu schützen, müssen Sie das `[Authorize]`-Attribut einem der folgenden Elemente hinzufügen:

- dem Controller selbst, wenn alle Aktionen des Controllers geschützt werden sollen
- einer individuellen Controlleraktion für Ihre API

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Aber dieser Schutz reicht nicht aus. Es wird nur garantiert, dass ASP.NET und ASP.NET Core das Token validieren. Ihre API muss überprüfen, ob das zum Aufrufen der API verwendete Token mit den erwarteten Ansprüchen angefordert wurde. Diese Ansprüche müssen insbesondere überprüft werden:

- Die *Bereiche*, wenn die API im Namen eines Benutzers aufgerufen wird.
- Die *App-Rollen*, wenn die API von einer Daemon-App aufgerufen wird.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Überprüfen von Bereichen in APIs, die im Namen von Benutzern aufgerufen werden

Wenn Ihre API von einer Client-App im Namen eines Benutzers aufgerufen wird, muss sie ein Bearertoken anfordern, das bestimmte Bereiche für die API enthält. Weitere Informationen finden Sie unter [Codekonfiguration | Bearertoken](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>Überprüfen der Bereiche bei jeder Controlleraktion

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

Mit der `VerifyUserHasAnyAcceptedScope`-Methode werden in etwa folgende Schritte ausgeführt:

- Überprüfen, ob ein Anspruch mit dem Namen `http://schemas.microsoft.com/identity/claims/scope` oder `scp` vorhanden ist
- Überprüfen, ob der Anspruch einen Wert hat, der den von der API erwarteten Bereich enthält


#### <a name="verify-the-scopes-more-globally"></a>Globaleres Überprüfen der Bereiche

Als Vorgehensweise werden die Definition präziser Bereiche für Ihre Web-API und die Überprüfung der Bereiche bei jeder Controlleraktion empfohlen. Es ist jedoch auch möglich, die Bereiche mithilfe von ASP.NET Core auf Anwendungs- oder Controllerebene zu überprüfen. Einzelheiten hierzu finden Sie in der ASP.NET Core-Dokumentation unter [Anspruchsbasierte Autorisierung](/aspnet/core/security/authorization/claims).

### <a name="net-mvc"></a>.NET MVC

Ersetzen Sie bei ASP.NET einfach `HttpContext.User` durch `ClaimsPrincipal.Current`, und ersetzen Sie den Anspruchstyp `"http://schemas.microsoft.com/identity/claims/scope"` durch `"scp"`. Beachten Sie auch den Codeausschnitt weiter unten in diesem Artikel.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Überprüfen von App-Rollen in APIs, die von Daemon-Apps aufgerufen werden

Wenn Ihre Web-API von einer [Daemon-App](scenario-daemon-overview.md) aufgerufen wird, sollte diese App eine Anwendungsberechtigung für Ihre Web-API anfordern. Unter [Verfügbarmachen von Anwendungsberechtigungen (App-Rollen)](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) haben Sie gesehen, dass Ihre API solche Berechtigungen verfügbar macht. Ein Beispiel hierfür ist die App-Rolle `access_as_application`.

Ihre API muss nun überprüfen, ob das empfangene Token den Anspruch `roles` enthält und ob dieser Anspruch den erwarteten Wert hat. Der Prüfcode ähnelt dem Code, der delegierte Berechtigungen überprüft, wobei jedoch Ihre Controlleraktion jetzt Rollen und nicht Bereiche testet:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

Die `ValidateAppRole`-Methode wird in Microsoft.Identity.Web in [RolesRequiredHttpContextExtensions.cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28) definiert.

### <a name="aspnet-mvc"></a>ASP.NET MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Akzeptieren von reinen App-Token, wenn die Web-API nur von Daemon-Apps aufgerufen werden sollte

Benutzer können auch Rollenansprüche in Benutzerzuweisungsmustern verwenden, wie unter [ Hinzufügen von App-Rollen in Ihrer Anwendung und Empfangen der Rollen im Token](howto-add-app-roles-in-azure-ad-apps.md) beschrieben. Wenn Sie also Rollen überprüfen, können sich Apps als Benutzer anmelden und umgekehrt, wenn die Rollen beiden zugewiesen werden können. Wir empfehlen, verschiedene Rollen für Benutzer und Apps zu deklarieren, um diese Verwechslung zu vermeiden.

Wenn nur Daemon-Apps Ihre Web-API aufrufen können sollen, fügen Sie bei der Validierung der App-Rolle die Bedingung hinzu, dass das Token ein reines App-Token sein muss.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Durch das Überprüfen der inversen Bedingung können nur Apps, die einen Benutzer anmelden, Ihre API aufrufen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](scenario-protected-web-api-production.md).
