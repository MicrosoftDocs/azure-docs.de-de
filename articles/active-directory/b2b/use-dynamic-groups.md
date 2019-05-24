---
title: Dynamische Gruppen und B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Hier wird gezeigt, wie Sie dynamische Azure AD-Gruppen mit Azure Active Directory B2B-Zusammenarbeit verwenden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f3cbdfa590583da59a5083f52595d54cc7f4f86
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65767216"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dynamische Gruppen und Azure Active Directory B2B-Zusammenarbeit

## <a name="what-are-dynamic-groups"></a>Was sind dynamische Gruppen?
Die dynamische Konfiguration der Mitgliedschaft in Sicherheitsgruppen für Azure Active Directory (Azure AD) ist im [Azure-Portal](https://portal.azure.com) verfügbar. Administratoren können Regeln einrichten, um in Azure AD erstellte Gruppen anhand von Benutzerattributen (z.B. Benutzertyp, Abteilung oder Land/Region) aufzufüllen. Mitglieder können auf der Grundlage ihrer Attribute automatisch zu einer Sicherheitsgruppe hinzugefügt oder daraus entfernt werden. Diese Gruppen können Zugriff auf Anwendungen oder Cloudressourcen (SharePoint-Websites, Dokumente) gewähren oder den Mitgliedern Lizenzen zuweisen. Weitere Informationen zu dynamischen Gruppen erhalten Sie unter [Dedizierte Gruppen in Azure Active Directory](../active-directory-accessmanagement-dedicated-groups.md).

Für die Erstellung und Verwendung dynamischer Gruppen wird die passende [Azure AD-Lizenzierung (Premium P1 oder Premium P2)](https://azure.microsoft.com/pricing/details/active-directory/) benötigt. Weitere Informationen finden Sie im Artikel [Erstellen attributbasierter Regeln für dynamische Gruppenmitgliedschaft in Azure Active Directory](../users-groups-roles/groups-dynamic-membership.md).

## <a name="what-are-the-built-in-dynamic-groups"></a>Was sind die integrierten dynamischen Gruppen?
Die dynamische Gruppe **Alle Benutzer** ermöglicht es Mandantenadministratoren, mit nur einem Klick eine Gruppe zu erstellen, die alle Benutzer des Mandanten umfasst. Die Gruppe **Alle Benutzer** umfasst standardmäßig alle Benutzer im Verzeichnis, einschließlich Mitgliedern und Gästen.
Im neuen Azure Active Directory-Verwaltungsportal können Sie die Gruppe **Alle Benutzer** in der Ansicht „Gruppeneinstellungen“ aktivieren.

![Option zum Aktivieren der Gruppe „Alle Benutzer“ auf „Ja“ festgelegt](media/use-dynamic-groups/enable-all-users-group.png)

## <a name="hardening-the-all-users-dynamic-group"></a>Härten der dynamischen Gruppe „Alle Benutzer“
Die Gruppe **Alle Benutzer** umfasst standardmäßig auch Ihre (Gast-)Benutzer für die B2B-Zusammenarbeit. Mit einer Regel zum Entfernen von Gastbenutzern kann die Gruppe **Alle Benutzer** noch besser geschützt werden. Die folgende Abbildung zeigt die Gruppe **Alle Benutzer** nach der Anpassung zum Ausschließen von Gästen.

![Zeigt die Regel, bei der der Benutzertyp ungleich „Gast“ ist](media/use-dynamic-groups/exclude-guest-users.png)

Unter Umständen empfiehlt es sich auch, eine neue dynamische Gruppe zu erstellen, die nur Gastbenutzer enthält, um Richtlinien auf sie anwenden zu können (etwa Azure AD-Richtlinien für den bedingten Zugriff).
Eine solche Gruppe könnte beispielsweise wie folgt aussehen:

![Zeigt die Regel, bei der der Benutzertyp gleich „Gast“ ist](media/use-dynamic-groups/only-guest-users.png)

## <a name="next-steps"></a>Nächste Schritte

- [Eigenschaften von B2B-Zusammenarbeitsbenutzern](user-properties.md)
- [Hinzufügen eines B2B-Zusammenarbeitsbenutzers zu einer Rolle](add-guest-to-role.md)
- [Bedingter Zugriff für Benutzer der B2B-Zusammenarbeit](conditional-access.md)

