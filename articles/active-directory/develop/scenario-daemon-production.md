---
title: Verschieben einer Daemon-App, die Web-APIs aufruft, in die Produktion | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine Daemon-App, die Web-APIs aufruft, in die Produktion verschieben.
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
ms.openlocfilehash: 8dc9bff86a07f3d4a0ec6fd224de6d5633165a6d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582840"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon-App, die Web-APIs aufruft (Übergang in die Produktion)

Da Sie nun wissen, wie Sie ein Token für einen Dienst-zu-Dienst-Aufruf abrufen und verwenden, erfahren Sie im Folgenden, wie Sie Ihre App in die Produktion bringen.

## <a name="deployment---multitenant-daemon-apps"></a>Bereitstellung: mehrinstanzenfähige Daemon-Apps

Wenn Sie ein unabhängiger Softwarehersteller sind, der eine Daemonanwendung erstellt, die in mehreren Mandanten ausgeführt werden kann, müssen Sie sicherstellen, dass der Mandantenadministrator folgende Aufgaben ausführt:

- Er muss einen Dienstprinzipal für die Anwendung bereitstellen.
- Er muss der Anwendung Zustimmung gewähren.

Sie müssen Ihren Kunden erklären, wie diese Vorgänge durchgeführt werden. Weitere Informationen finden Sie im Abschnitt zum [Anfordern der Zustimmung für einen gesamten Mandanten](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Codebeispiele

# <a name="net"></a>[.NET](#tab/dotnet)

- In der Dokumentation finden Sie Informationen zum:
  - Instanziieren von [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Aufrufen von [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Weitere Beispiele und Tutorials:
  - [microsoft-identity-platform-console-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) ist eine kleine .NET Core-Daemon-Konsolenanwendung, die die Benutzer eines Mandanten anzeigt, die Microsoft Graph abfragen.

    ![Beispieltopologie für Daemon-App](media/scenario-daemon-app/daemon-app-sample.svg)

    Dasselbe Beispiel zeigt auch eine Variante mit Zertifikaten:

    ![Beispieltopologie für Daemon-App: Zertifikate](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [microsoft-identity-platform-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) ist eine ASP.NET MVC-Webanwendung, die Daten von Microsoft Graph mithilfe der Identität der Anwendung (statt im Namen eines Benutzers) synchronisiert. Das Beispiel veranschaulicht auch den Vorgang für die Administratoreinwilligung.

    ![Topologie](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>Nächste Schritte

Im Folgenden finden Sie Links zu weiteren Informationen:

# <a name="python"></a>[Python](#tab/python)

Arbeiten Sie den Schnellstart [Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Python-Konsolen-App anhand der Identität einer App](./quickstart-v2-python-daemon.md) durch.

# <a name="java"></a>[Java](#tab/java)

Arbeiten Sie den Schnellstart [Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über eine Java-Konsolen-App anhand der Identität einer App](./quickstart-v2-java-daemon.md) durch.

---
