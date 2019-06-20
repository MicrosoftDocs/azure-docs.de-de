---
title: Support- und Hilfeoptionen für Entwickler von Azure AD-Apps| Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie beim Erstellen einer Anwendung, die mit Microsoft-Identitäten (Azure Active Directory und Microsoft-Konto) integriert wird, Hilfe und Support zu entwicklungsbezogenen Problemen und Fragen erhalten.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efa14e88eeb8ab43f998a32aaa0c14220acab03a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235334"
---
# <a name="support-and-help-options-for-developers"></a>Support- und Hilfeoptionen für Entwickler

Wenn Sie gerade mit der Integration mit Azure Active Directory (Azure AD), Microsoft-Identitäten oder Microsoft Graph-API beginnen oder ein neues Feature in Ihrer Anwendung implementieren, benötigen Sie manchmal Hilfe von der Community oder müssen die Supportoptionen kennen, die Ihnen als Entwickler zur Verfügung stehen. Dieser Artikel soll Ihnen helfen, diese Optionen zu verstehen. Dazu zählen:

> [!div class="checklist"]
> * Sie können über eine Suche herausfinden, ob Ihr Problem nicht vielleicht schon von der Community beantwortet wurde oder ob für das Feature, das Sie implementieren möchten, bereits eine Dokumentation vorhanden ist.
> * In manchen Fällen möchten Sie einfach nur unsere Supporttools nutzen, um das Debuggen eines bestimmten Problems zu vereinfachen.
> * Wenn Sie die gesuchte Antwort nicht finden, können Sie auf *Stack Overflow* eine Frage stellen.
> * Wenn Sie auf ein Problem mit einer unserer Authentifizierungsbibliotheken stoßen, erstellen Sie ein *GitHub*-Problem.
> * Wenn Sie schließlich mit einer Person sprechen müssen, stellen Sie eine Supportanfrage.

## <a name="search"></a>Suchen,

Wenn Sie eine entwicklungsspezifische Frage haben, finden Sie die gewünschte Antwort möglicherweise in der Dokumentation, den [GitHub-Beispielen](https://github.com/azure-samples) oder in den Antworten auf Fragen auf [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Bereichsbezogene Suche

Um schneller Ergebnisse zu erzielen, beschränken Sie Ihre Suche auf Stack Overflow, die Dokumentation und Codebeispiele, indem Sie in Ihrer bevorzugten Suchmaschine die folgende Abfrage verwenden:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Hierbei entspricht *{Your Search Terms}* Ihren Schlüsselwörtern für die Suche.

## <a name="use-the-development-support-tools"></a>Verwenden der Supporttools für die Entwicklung

| Tool  | BESCHREIBUNG  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Fügen Sie eine ID oder ein Zugriffstoken zum Entschlüsseln von Anspruchsnamen und Werten ein. |
| [Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Ein Tool, mit dem Sie Anforderungen an die Microsoft Graph-API richten und deren Antworten anzeigen können. |

## <a name="post-a-question-to-stack-overflow"></a>Richten einer Frage an Stack Overflow

Stack Overflow ist der bevorzugte Kanal für entwicklungsbezogene Fragen. Hier sind Mitglieder der Entwicklercommunity und Microsoft-Teammitglieder direkt an der Lösung Ihrer Probleme beteiligt.

Wenn Sie über die Suche keine Antwort auf Ihre Frage finden können, übermitteln Sie eine neue Frage auf Stack Overflow. Verwenden Sie bei der Fragestellung eines der folgenden Tags, damit die Community Ihre Frage schneller identifizieren und beantworten kann:

|Komponente/Bereich  | `Tags` |
|---------|---------|
| ADAL-Bibliothek | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| MSAL-Bibliothek     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| OWIN-Middleware  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [Microsoft Graph-API](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Alle anderen Bereiche im Zusammenhang mit Authentifizierungs- oder Autorisierungsthemen | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Die folgenden Beiträge aus Stack Overflow enthalten Tipps zum Formulieren von Fragen und zum Hinzufügen von Quellcode. Das Befolgen dieser Hinweise erhöht die Chancen, dass Mitglieder der Community Ihre Frage schnell bewerten und beantworten:

* [How do I ask a good question?](https://stackoverflow.com/help/how-to-ask) (Wie stelle ich meine Frage optimal?)
* [How to create a Minimal, Complete, and Verifiable example](https://stackoverflow.com/help/mcve) (Erstellen eines minimalen, vollständigen und überprüfbaren Beispiels)

## <a name="create-a-github-issue"></a>Erstellen eines GitHub-Problems

Wenn Sie einen Fehler oder ein Problem im Zusammenhang mit unseren Bibliotheken gefunden haben, erstellen Sie in unseren GitHub-Repositorys ein Problem. Da unsere Bibliotheken Open Source sind, können Sie auch einen Pull Request übermitteln.

Die folgenden Ressourcen enthalten eine Liste der Bibliotheken und ihrer GitHub-Repositorys:

* [ADAL](active-directory-authentication-libraries.md)-Bibliotheken und GitHub-Repositorys
* Bibliotheken [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL.Android](https://github.com/AzureAD/microsoft-authentication-library-for-android) und [MSAL.obj_c](https://github.com/AzureAD/microsoft-authentication-library-for-objc) und GitHub-Repositorys

## <a name="open-a-support-request"></a>Stellen einer Supportanfrage

Wenn Sie mit einer Person sprechen müssen, stellen Sie eine Supportanfrage. Wenn Sie ein Azure-Kunde sind, stehen Ihnen mehrere Supportoptionen zur Verfügung. Um Pläne zu vergleichen, lesen Sie [diese Seite](https://azure.microsoft.com/support/plans/). Support für Entwickler ist auch für Azure-Kunden verfügbar. Informationen zum Erwerb von Support für Entwickler finden Sie auf [dieser Seite](https://azure.microsoft.com/support/plans/developer/).

* Wenn Sie bereits einen Azure-Supportplan haben, [stellen Sie hier eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

* Wenn Sie kein Azure-Kunde sind, können Sie auch über [unseren kommerziellen Support](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial) eine Supportanfrage an Microsoft stellen.

Sie können auch einen [virtuellen Agent](https://support.microsoft.com/contactus/?ws=support) ausprobieren, um Support zu erhalten oder Fragen zu stellen.

### <a name="free-chat-support-for-a-limited-time"></a>Kostenloser Chatsupport für begrenzte Zeit

Sie können auch unseren Chatsupport nutzen, der für Microsoft Partner für einen begrenzten Zeitraum kostenlos ist. Wenn Ihr Unternehmen kein Microsoft Partner ist, können Sie es kostenlos registrieren und erhalten weitere Vorteile – klicken Sie [hier](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx).

Nach der Registrierung Ihres Unternehmens können Sie die Chatanfrage [hier](https://aka.ms/devchat) starten.
