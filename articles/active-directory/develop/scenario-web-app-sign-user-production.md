---
title: Überführen einer Web-App für Benutzeranmeldungen in die Produktion – Microsoft Identity Platform | Azure
description: Erfahren Sie, wie Sie eine Web-App erstellen, die Benutzer anmeldet (Übergang in die Produktion).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: fd9890cb94bf6bb4b82ebbb585ab8bbb9d5ba46a
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169289"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Web-App für Benutzeranmeldungen: Überführen in die Produktion

Da Sie nun wissen, wie Sie ein Token zum Abrufen von Web-APIs aufrufen, erfahren Sie in diesem Artikel, wie Ihnen der Übergang in die Produktion gelingt.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Problembehandlung

> [!NOTE]
> Wenn Benutzer sich das erste Mal bei der Webanwendung anmelden, ist eine Einwilligung erforderlich. In manchen Organisationen wird Benutzern jedoch eine Meldung ähnlich der folgenden angezeigt:
>
> *Name_der_App benötigt für den Zugriff auf Ressourcen in Ihrer Organisation Berechtigungen, die nur ein Administrator erteilen kann. Bitten Sie einen Administrator, die Berechtigungen für diese App zu erteilen, damit Sie die App verwenden können.*
>
> Dies liegt daran, dass Ihr Mandantenadministrator die Möglichkeit für Benutzer zur Zustimmung **deaktiviert** hat. In diesem Fall müssen Sie sich an Ihre Mandantenadministratoren wenden, damit diese eine Administratorzustimmung für die Bereiche erteilen, die für die Anwendung erforderlich sind.

## <a name="same-site"></a>Gleiche Website

Informieren Sie sich über mögliche Probleme im Zusammenhang mit neuen Versionen des Chrome-Browsers: [Verarbeiten von SameSite-Cookieänderungen im Chrome-Browser](howto-handle-samesite-cookie-changes-chrome-browser.md)

Das Microsoft.Identity.Web-NuGet-Paket verarbeitet die häufigsten SameSite-Probleme.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Ausführliche Betrachtung: Tutorial für ASP.NET Core-Web-Apps

Lernen Sie mithilfe dieses ASP.NET Core-Tutorials andere Möglichkeiten zum Anmelden von Benutzern kennen: 

[Ermöglichen Sie Ihren Web-Apps das Anmelden von Benutzern und das Aufrufen von APIs mit der Microsoft Identity-Plattform für Entwickler](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

Dieses fortschreitende Tutorial enthält Web-App-Code, der in Produktionsumgebungen eingesetzt werden kann. Außerdem wird beschrieben, wie eine Anmeldung mit Konten hinzugefügt wird, die sich in folgenden Umgebungen befinden:

- Ihrer Organisation
- mehreren Organisationen
- Geschäfts-, Schul- oder Unikonten bzw. persönlichen Microsoft-Konten
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Nationale Clouds

## <a name="sample-code-java-web-app"></a>Beispielcode: Java-Web-App

Weitere Informationen zur Java-Web-App aus dem Beispiel auf GitHub finden Sie hier: 

[Eine Java-Webanwendung, die Benutzer bei Microsoft Identity Platform anmeldet und Microsoft Graph aufruft](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Nächste Schritte

Sobald Ihre Web-App Benutzer anmeldet, kann sie für diese angemeldeten Benutzer Web-APIs aufrufen. Genau darum geht es im folgenden Szenario: [eine Web-App, die Web-APIs aufruft](scenario-web-app-call-api-overview.md).