---
title: Abonnements in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Konzept von Abonnements in Azure API Management. Consumer erhalten Zugriff auf APIs mithilfe von Abonnements in Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: cdc7668b06308bd5532f4885d0e1365fc34ae989
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97511245"
---
# <a name="subscriptions-in-azure-api-management"></a>Abonnements in Azure API Management

Abonnements sind ein wichtiges Konzept in Azure API Management. Über sie erhalten API-Nutzer am häufigsten Zugriff auf APIs, die über eine API Management-Instanz veröffentlicht werden. Dieser Artikel bietet eine Übersicht über das Konzept.

## <a name="what-are-subscriptions"></a>Was sind Abonnements?

Wenn Sie APIs mit API Management veröffentlichen, ist es einfach und üblich, auf diese APIs mithilfe von Abonnementschlüsseln sicher zuzugreifen. Entwickler, die die veröffentlichten APIs nutzen möchten, müssen einen gültigen Abonnementschlüssel in HTTP-Anforderungen einschließen, wenn sie Aufrufe an diese APIs senden. Andernfalls werden die Aufrufe sofort vom API Management-Gateway abgelehnt. Sie werden nicht an die Back-End-Dienste weitergeleitet.

Um einen Abonnementschlüssel für den API-Zugriff zu erhalten, ist ein Abonnement erforderlich. Ein Abonnement ist im Wesentlichen ein benannter Container für ein Paar von Abonnementschlüsseln. Entwickler, die die veröffentlichten APIs verarbeiten müssen, können Abonnements erhalten. Sie benötigen keine Genehmigung durch API-Herausgeber. API-Herausgeber können Abonnements für API-Nutzer auch direkt erstellen.

> [!TIP]
> API Management unterstützt auch andere Mechanismen zum Schützen des Zugriffs auf APIs, einschließlich der folgenden Beispiele:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Clientzertifikate](api-management-howto-mutual-certificates-for-clients.md)
> - [Beschränkung für Aufrufer-IP](./api-management-access-restriction-policies.md#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Bereich von Abonnements

Abonnements können verschiedenen Bereichen zugeordnet werden: einem Produkt, allen APIs oder einer bestimmten API.

### <a name="subscriptions-for-a-product"></a>Abonnements für ein Produkt

Traditionell wurden Abonnements in APIM als Bereich immer einem einzelnen [API-Produkt](api-management-terminology.md) zugeordnet. Entwickler fanden die Liste der Produkte im Entwicklerportal. Dann sendeten sie Abonnementanforderungen für die Produkte, die sie verwenden wollten. Nachdem eine Abonnementanforderung genehmigt wurde – entweder automatisch oder durch den API-Herausgeber – können die Entwickler die Schlüssel darin für den Zugriff auf alle APIs im Produkt verwenden. Derzeit zeigt das Entwicklerportal die Abonnements auf Produktebene nur im Abschnitt mit dem Benutzerprofil an. 

![Produktabonnements](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> In bestimmten Szenarien möchten API-Herausgeber vielleicht ein API-Produkt für die Öffentlichkeit veröffentlichen, ohne dass hierzu Abonnements erforderlich sind. Sie können hierzu im Azure-Portal auf der Seite **Einstellungen** die Option **Abonnement erforderlich** deaktivieren. Als Ergebnis kann auf alle APIs für das Produkt ohne einen API-Schlüssel zugegriffen werden.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Abonnements für alle APIs oder eine einzelne API

Bei Einführung des [Verbrauchstarifs](https://aka.ms/apimconsumptionblog) für API Management wurde einige Änderungen vorgenommen, um die Schlüsselverwaltung zu optimieren:
- Zunächst wurden zwei weitere Abonnementbereiche hinzugefügt: alle APIs und eine einzelne API. Der Bereich von Abonnements ist nicht länger auf ein API-Produkt beschränkt. Es ist jetzt möglich, Schlüssel zur Zugriffserteilung für eine API (oder alle APIs innerhalb einer API Management-Instanz) zu erstellen, ohne dass zunächst ein Produkt erstellt und diesem die APIs hinzugefügt werden müssen. Außerdem gehört zu jeder API Management-Instanz nun ein unveränderliches Abonnement für alle APIs. Dieses Abonnement erleichtert das Testen und Debuggen von APIs in der Testkonsole.

- Außerdem lässt API Management jetzt **eigenständige** Abonnements zu. Es ist nicht mehr erforderlich, dass Abonnements einem Entwicklerkonto zugeordnet sind. Dieses Feature ist insbesondere in Szenarien nützlich, in denen ein Abonnement von mehreren Entwicklern oder Teams gemeinsam genutzt wird.

- Und schließlich können API-Herausgeber jetzt direkt im Azure-Portal [Abonnements erstellen](api-management-howto-create-subscriptions.md):

    ![Flexible Abonnements](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu API Management:

+ Informationen zu weiteren [Konzepten](api-management-terminology.md) in API Management
+ [Tutorials](import-and-publish.md) zum weiteren Kennenlernen von API Management
+ [FAQ-Seite](api-management-faq.md) für häufig gestellte Fragen
