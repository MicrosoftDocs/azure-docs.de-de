---
title: Aufrufen einer Web-API über eine Daemon-App | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine Daemon-App erstellen, die eine Web-API aufruft.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bd0d53049c68843a6fd2cb6128c473d7c4f8d639
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582790"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>Daemon-App, die Web-APIs aufruft – Aufrufen einer Web-API aus der App

.NET-Daemon-Apps können eine Web-API aufrufen. .NET-Daemon-Apps können auch mehrere vorab genehmigte Web-APIs aufrufen.

## <a name="calling-a-web-api-from-a-daemon-application"></a>Aufrufen einer Web-API aus einer Daemonanwendung

Gehen Sie wie folgt vor, um eine API unter Verwendung des Tokens aufzurufen:

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
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
```

---

## <a name="calling-several-apis"></a>Aufrufen mehrerer APIs

Für Daemon-Apps müssen die von Ihnen aufgerufenen Web-APIs vorab genehmigt werden. Es gibt keine inkrementelle Einwilligung für Daemon-Apps. (Es gibt keine Benutzerinteraktion.) Der Mandantenadministrator muss vorab in die Berechtigungen für die Anwendung und alle APIs einwilligen. Wenn Sie mehrere APIs aufrufen möchten, müssen Sie für jede Ressource ein Token abrufen, indem Sie jedes Mal `AcquireTokenForClient` aufrufen. MSAL verwendet den Tokencache der Anwendung, um unnötige Dienstaufrufe zu vermeiden.

## <a name="next-steps"></a>Nächste Schritte

# <a name="net"></a>[.NET](#tab/dotnet)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](./scenario-daemon-production.md?tabs=dotnet).

# <a name="python"></a>[Python](#tab/python)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](./scenario-daemon-production.md?tabs=python).

# <a name="java"></a>[Java](#tab/java)

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](./scenario-daemon-production.md?tabs=java).

---
