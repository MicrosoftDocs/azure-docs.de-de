---
title: Vergleichen von B2B-Zusammenarbeit und B2C – Azure Active Directory | Microsoft-Dokumentation
description: Was ist der Unterschied zwischen Azure Active Directory B2B-Zusammenarbeit und Azure AD B2C?
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 01/30/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: e48a26b2eea3004d5f2203409603ffe623a8cb5c
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65811891"
---
# <a name="compare-b2b-collaboration-and-b2c-in-azure-active-directory"></a>Vergleichen von B2B-Zusammenarbeit und B2C in Azure Active Directory

Sowohl Azure Active Directory (Azure AD) B2B-Zusammenarbeit als auch Azure AD B2C ermöglichen Ihnen die Arbeit mit externen Benutzern in Azure AD. Aber worin bestehen die Unterschiede?

**Azure AD B2B** eignet sich für Unternehmen, die Dateien und Ressourcen sicher für die gemeinsame Nutzung mit externen Benutzern freigeben möchten, um die Zusammenarbeit zu ermöglichen. Ein Azure-Administrator richtet B2B im Azure-Portal ein, und Azure AD sorgt für den Verbund zwischen Ihrem Unternehmen und Ihrem externen Partner. Benutzer melden sich über einen einfachen Einladungs- und Einlösungsprozess mit ihrem Geschäfts-, Schul- oder Unikonto oder einem E-Mail-Konto bei den freigegebenen Ressourcen an.
 
**Azure AD B2C** ist primär für Unternehmen und Entwickler gedacht, die Apps für Kunden erstellen. Mit Azure AD B2C können Entwickler Azure AD als Identitätssystem mit allen notwendigen Features für ihre Anwendung verwenden. Gleichzeitig können Kunden sich mit einer bereits vorhandenen Identität anmelden (z.B. Facebook oder Gmail).

In der folgenden Tabelle finden Sie einen detaillierten Vergleich.


Funktionen von B2B-Zusammenarbeit |     Eigenständiges Azure AD B2C-Angebot
-------- | --------
Vorgesehen für: Organisationen, die Benutzer einer Partnerorganisation unabhängig vom Identitätsanbieter authentifizieren möchten. | Vorgesehen für: Einladung von Kunden Ihrer mobilen Apps und Web-Apps (Einzelpersonen, Institutionen oder Organisationen) zu Ihrem Azure AD.
Unterstützte Identitäten: Mitarbeiter mit Geschäfts-, Schul- oder Unikonto, Partner mit Geschäfts-, Schul- oder Unikonto oder beliebige E-Mail-Adressen. Direkter Verbund wird in Kürze unterstützt.  | Unterstützte Identitäten: Endbenutzer mit lokalem Anwendungskonto (E-Mail-Adresse oder Benutzername) oder eine beliebige unterstützte soziale Identität mit direktem Verbund.
Externe Benutzer werden im gleichen Verzeichnis verwaltet wie Mitarbeiter, aber speziell gekennzeichnet. Sie können auf die gleiche Weise wie Mitarbeiter verwaltet, denselben Gruppen hinzugefügt werden usw.  | Externe Benutzer werden im Anwendungsverzeichnis verwaltet. Sie werden getrennt vom Verzeichnis für die Mitarbeiter und Partner (sofern vorhanden) der Organisation verwaltet.
Einmaliges Anmelden (SSO) bei allen verbundenen Azure AD-Apps wird unterstützt. Beispielsweise können Sie den Zugriff auf Office 365 oder lokale Apps und auf andere SaaS-Apps wie z.B. Salesforce oder Workday bereitstellen.  |  Einmaliges Anmelden bei Kunden-Apps innerhalb der Azure AD B2C-Mandanten wird unterstützt. Einmaliges Anmelden bei Office 365 oder anderen Microsoft- und Nicht-Microsoft-SaaS-Apps wird nicht unterstützt.
Partnerlebenszyklus: Wird von der Hostorganisation/einladenden Organisation verwaltet.  | Kundenlebenszyklus: Self-Service oder von der Anwendung verwaltet.
Sicherheitsrichtlinie und Compliance: Wird von der Hostorganisation/einladenden Organisation verwaltet (beispielsweise mit [Richtlinien für den bedingten Zugriff](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)).  | Sicherheitsrichtlinie und Compliance: Von der Anwendung verwaltet.
Branding: Branding der Hostorganisation/einladenden Organisation wird verwendet.  |    Branding: Von der Anwendung verwaltet. Normalerweise wird eher das Produktbranding verwendet, und die Organisation tritt in den Hintergrund.
Weitere Informationen: [Blogbeitrag](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)  | Weitere Informationen: [Produktseite](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](https://docs.microsoft.com/azure/active-directory-b2c/)


### <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Eigenschaften von B2B-Zusammenarbeitsbenutzern](user-properties.md)

