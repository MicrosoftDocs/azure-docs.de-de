---
title: Verwenden von APIs mit aktivierter fortlaufender Zugriffsevaluierung in Ihren Anwendungen | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie die Sicherheit und Resilienz Ihrer Apps erhöhen, indem Sie Unterstützung für die fortlaufende Zugriffsevaluierung hinzufügen. Auf diese Weise lassen sich langlebige Zugriffstoken nutzen, die basierend auf Richtlinienauswertung und kritischen Ereignissen widerrufen werden können.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: 86c379316737b7718b62165a6feb93ca3a0e9954
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484038"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Verwenden von APIs mit aktivierter fortlaufender Zugriffsevaluierung in Ihren Anwendungen

Die [fortlaufende Zugriffsevaluierung](../conditional-access/concept-continuous-access-evaluation.md) (Continuous Access Evaluation, CAE) ist ein neuer Branchenstandard, der es ermöglicht, Zugriffstoken basierend auf [kritischen Ereignissen](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) und [Richtlinienauswertung](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) zu widerrufen, anstatt sich auf den Tokenablauf basierend auf der Lebensdauer zu verlassen. Dies kann bei einigen Ressourcen-APIs die Lebensdauer eines Tokens um bis zu 28 Stunden verlängern, weil Risiken und Richtlinien in Echtzeit ausgewertet werden. Diese langlebigen Token werden von der Microsoft Authentication Library (MSAL) proaktiv aktualisiert, wodurch die Resilienz Ihrer Anwendungen steigt.

In diesem Artikel erfahren Sie, wie Sie APIs mit aktivierter fortlaufender Zugriffsevaluierung in Ihren Anwendungen verwenden.

## <a name="implementation-considerations"></a>Überlegungen zur Implementierung

Damit die fortlaufende Zugriffsevaluierung genutzt werden kann, muss dieses Feature sowohl für Ihre App als auch für die Ressourcen-API aktiviert werden, die auf die App zugreift. Auch wenn Sie Ihren Code für die Nutzung einer Ressource mit aktivierter CAE vorbereiten, bedeutet das nicht, dass Sie keine APIs verwenden können, für die dieses Feature nicht aktiviert wurde.

Wenn eine Ressourcen-API CAE implementiert und in Ihrer App deklariert wurde, dass sie das Feature verarbeiten kann, erhält die App CAE-Token für diese Ressource. Aus diesem Grund gilt Folgendes: Wenn Sie Ihre App als CAE-fähig deklarieren, muss die App die CAE-Anspruchsabfrage für alle Ressourcen-APIs verarbeiten, die Microsoft Identity-Zugriffstoken akzeptieren. Wenn CAE-Antworten in diesen API-Aufrufen nicht verarbeitet werden, kann es zu einer Schleife kommen. Hierbei versucht die App immer wieder, einen API-Aufruf mit einem Token zu verarbeiten, das sich zwar noch innerhalb der zurückgegebenen Tokenlebensdauer befindet, aber aufgrund der fortlaufenden Zugriffsevaluierung widerrufen wurde.

## <a name="the-code"></a>Der Code

Als Erstes müssen Sie Code hinzufügen, um eine Antwort von der Ressourcen-API zu verarbeiten, die den Aufruf aufgrund von CAE ablehnt. Mit CAE geben APIs einen 401-Statuscode sowie einen WWW-Authenticate-Header zurück, wenn das Zugriffstoken widerrufen wurde oder die API eine Änderung in der verwendeten IP-Adresse erkennt. Der WWW-Authenticate-Header enthält eine Anspruchsabfrage, mit der die Anwendung ein neues Zugriffstoken abrufen kann.

Zum Beispiel:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
 authorization_uri="https://login.windows.net/common/oauth2/authorize",
 error="insufficient_claims",
 claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Ihre App überprüft Folgendes:

- den API-Aufruf, der den 401-Status zurückgibt
- das Vorhandensein eines WWW-Authenticate-Headers mit folgendem Inhalt:
  - ein error-Parameter mit dem Wert „insufficient_claims“
  - ein claims-Parameter

Wenn diese Bedingungen erfüllt sind, kann die App die Anspruchsabfrage extrahieren und decodieren.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Dann verwendet Ihre App die Anspruchsabfrage, um ein neues Zugriffstoken für die Ressource abzurufen.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Sobald Ihre Anwendung dazu bereit ist, die von einer für CAE aktivierten Ressource zurückgegebene Anspruchsabfrage zu verarbeiten, können Sie Microsoft Identity mitteilen, dass Ihre Anwendung CAE-fähig ist. Um dies in Ihrer MSAL-Anwendung umzusetzen, erstellen Sie Ihren öffentlichen Client mit den Clientfunktionen von „cp1“.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Sie können Ihre App testen, indem einen Benutzer bei der App anmelden und dann das Azure-Portal verwenden, um die Sitzungen dieses Benutzers zu widerrufen. Wenn die App das nächste Mal die API mit aktivierter CAE aufruft, wird der Benutzer aufgefordert, sich erneut zu authentifizieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Fortlaufende Zugriffsevaluierung](../conditional-access/concept-continuous-access-evaluation.md).
