---
title: Support- und Hilfeoptionen für Entwickler von Azure AD-Apps
description: Hier erfahren Sie, wie Sie beim Erstellen einer Anwendung, die mit Microsoft-Identitäten (Azure Active Directory und Microsoft-Konto) integriert wird, Hilfe und Support zu entwicklungsbezogenen Problemen und Fragen erhalten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: bce9479d063d091eb4fa68d2452d8a4218d45db9
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219942"
---
# <a name="support-and-help-options-for-developers"></a>Support- und Hilfeoptionen für Entwickler

Wenn Sie gerade mit der Integration mit Azure Active Directory (Azure AD), Microsoft-Identitäten oder Microsoft Graph-API beginnen oder ein neues Feature in Ihrer Anwendung implementieren, benötigen Sie manchmal Hilfe von der Community oder müssen die Supportoptionen kennen, die Ihnen als Entwickler zur Verfügung stehen. Dieser Artikel soll Ihnen helfen, diese Optionen zu verstehen. Dazu zählen:

> [!div class="checklist"]
> * Sie können über eine Suche herausfinden, ob Ihr Problem nicht vielleicht schon von der Community beantwortet wurde oder ob für das Feature, das Sie implementieren möchten, bereits eine Dokumentation vorhanden ist.
> * In manchen Fällen möchten Sie einfach nur unsere Supporttools nutzen, um das Debuggen eines bestimmten Problems zu vereinfachen.
> * Wenn Sie die gesuchte Antwort nicht finden, können Sie auf *Microsoft Q&A* eine Frage stellen.
> * Wenn Sie auf ein Problem mit einer unserer Authentifizierungsbibliotheken stoßen, erstellen Sie ein *GitHub*-Problem.
> * Wenn Sie schließlich mit einer Person sprechen müssen, stellen Sie eine Supportanfrage.

## <a name="search"></a>Suchen,

Wenn Sie eine entwicklungsspezifische Frage haben, finden Sie die gewünschte Antwort möglicherweise in der Dokumentation, den [GitHub-Beispielen](https://github.com/azure-samples) oder in den Antworten auf Fragen auf [Microsoft Q&A](https://docs.microsoft.com/answers/products/).

### <a name="scoped-search"></a>Bereichsbezogene Suche

Um schneller Ergebnisse zu erzielen, beschränken Sie Ihre Suche auf [Microsoft Q&A](https://docs.microsoft.com/answers/products/), die Dokumentation und Codebeispiele, indem Sie in Ihrer bevorzugten Suchmaschine die folgende Abfrage verwenden:

```
{Your Search Terms} (site:http://www.docs.microsoft.com/answers/products/ OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

Hierbei entspricht *{Your Search Terms}* Ihren Schlüsselwörtern für die Suche.

## <a name="use-the-development-support-tools"></a>Verwenden der Supporttools für die Entwicklung

| Tool  | BESCHREIBUNG  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Fügen Sie eine ID oder ein Zugriffstoken zum Entschlüsseln von Anspruchsnamen und Werten ein. |
| [Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)| Ein Tool, mit dem Sie Anforderungen an die Microsoft Graph-API richten und deren Antworten anzeigen können. |

## <a name="post-a-question-to-microsoft-qa"></a>Stellen einer Frage bei Microsoft Q&A

[Microsoft Q&A](https://docs.microsoft.com/answers/products/) ist der bevorzugte Kanal für entwicklungsbezogene Fragen. Hier sind Mitglieder der Entwicklercommunity und Microsoft-Teammitglieder direkt an der Lösung Ihrer Probleme beteiligt.

Wenn Sie über die Suche keine Antwort auf Ihre Frage finden können, übermitteln Sie eine neue Frage auf [Microsoft Q&A](https://docs.microsoft.com/answers/products/). Verwenden Sie bei der Fragestellung eines der folgenden Tags, damit die Community Ihre Frage schneller identifizieren und beantworten kann:

|Komponente/Bereich  | `Tags` |
|---------|---------|
| ADAL-Bibliothek | [[adal]](https://docs.microsoft.com/answers/topics/azure-ad-adal-deprecation.html) |
| MSAL-Bibliothek     | [[msal]](https://docs.microsoft.com/answers/topics/azure-ad-msal.html) |
| OWIN-Middleware  | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |
| [Azure B2B](../external-identities/what-is-b2b.md)  | [[azure-ad-b2b]](https://docs.microsoft.com/answers/topics/azure-ad-b2b.html) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://docs.microsoft.com/answers/topics/azure-ad-b2c.html) |
| [Microsoft Graph-API](https://developer.microsoft.com/graph/) | [[azure-ad-graph]](https://docs.microsoft.com/answers/topics/azure-ad-graph.html) |
| Alle anderen Bereiche im Zusammenhang mit Authentifizierungs- oder Autorisierungsthemen | [[azure-active-directory]](https://docs.microsoft.com/answers/topics/azure-active-directory.html) |

Die folgenden Beiträge aus [Microsoft Q&A](https://docs.microsoft.com/answers/products/) enthalten Tipps zum Formulieren von Fragen und zum Hinzufügen von Quellcode. Das Befolgen dieser Hinweise erhöht die Chancen, dass Mitglieder der Community Ihre Frage schnell bewerten und beantworten:

* [How do I ask a good question?](https://docs.microsoft.com/answers/articles/24951/how-to-write-a-quality-question.html) (Wie stelle ich meine Frage optimal?)
* [How to create a Minimal, Complete, and Verifiable example](https://docs.microsoft.com/answers/articles/24907/how-to-write-a-quality-answer.html) (Erstellen eines minimalen, vollständigen und überprüfbaren Beispiels)

## <a name="create-a-github-issue"></a>Erstellen eines GitHub-Problems

Wenn Sie einen Fehler oder ein Problem im Zusammenhang mit unseren Bibliotheken gefunden haben, erstellen Sie in unseren GitHub-Repositorys ein Problem. Da unsere Bibliotheken Open Source sind, können Sie auch einen Pull Request übermitteln.

Die folgenden Ressourcen enthalten eine Liste der Bibliotheken und ihrer GitHub-Repositorys:

* [Azure Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL)](../azuread-dev/active-directory-authentication-libraries.md)-Bibliotheken und GitHub-Repositorys.
* [Microsoft Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)](reference-v2-libraries.md)-Bibliotheken und GitHub-Repositorys.

## <a name="open-a-support-request"></a>Stellen einer Supportanfrage

Wenn Sie mit einer Person sprechen müssen, stellen Sie eine Supportanfrage. Wenn Sie ein Azure-Kunde sind, stehen Ihnen mehrere Supportoptionen zur Verfügung. Um Pläne zu vergleichen, lesen Sie [diese Seite](https://azure.microsoft.com/support/plans/). Support für Entwickler ist auch für Azure-Kunden verfügbar. Informationen zum Erwerb von Support für Entwickler finden Sie auf [dieser Seite](https://azure.microsoft.com/support/plans/developer/).

* Wenn Sie bereits einen Azure-Supportplan haben, [stellen Sie hier eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

* Wenn Sie kein Azure-Kunde sind, können Sie auch über [unseren kommerziellen Support](https://support.serviceshub.microsoft.com/supportforbusiness) eine Supportanfrage an Microsoft stellen.

Sie können auch einen [virtuellen Agent](https://support.microsoft.com/contactus/?ws=support) ausprobieren, um Support zu erhalten oder Fragen zu stellen.