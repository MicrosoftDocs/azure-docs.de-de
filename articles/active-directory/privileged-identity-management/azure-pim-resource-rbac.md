---
title: Anzeigen von Aktivitäten und des Überwachungsverlaufs für Azure-Ressourcenrollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Zeigen Sie Aktivitäten und den Überwachungsverlauf für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) an.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74607f6a746558238ead65036d708b515d370035
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60441406"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Anzeigen von Aktivitäten und Überwachungsverlauf für Azure-Ressourcenrollen in PIM

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Aktivitäten, Aktivierungen und den Überwachungsverlauf für Azure-Ressourcenrollen innerhalb Ihrer Organisation anzeigen. Dies schließt Abonnements, Ressourcengruppen und sogar virtuelle Computer ein. Alle Ressourcen im Azure-Portal, die die Azure-Funktion für die rollenbasierte Zugriffssteuerung (Role Based Access Control, RBAC) nutzen, können von den Sicherheits- und Lebenszyklusverwaltungsfunktionen von PIM profitieren.

## <a name="view-activity-and-activations"></a>Anzeigen von Aktivitäten und Aktivierungen

Um zu sehen, welche Aktionen ein bestimmter Benutzer für verschiedene Ressourcen ausgeführt hat, können Sie die Azure-Ressourcenaktivität für einen bestimmten Aktivierungszeitraum anzeigen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, für die Sie Aktivitäten und Aktivierungen anzeigen möchten.

1. Klicken Sie auf **Rollen** oder **Mitglieder**.

1. Klicken Sie auf einen Benutzer.

    Daraufhin wird eine nach Datum aufgeschlüsselte Grafik mit den Aktionen des Benutzers in Azure-Ressourcen dargestellt. Außerdem sind die letzten Rollenaktivierungen im selben Zeitraum zu sehen.

    ![Benutzerdetails](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Klicken Sie auf eine bestimmte Rollenaktivierung, um Details und die entsprechende Azure-Ressourcenaktivität anzuzeigen, die ausgeführt wurde, während der Benutzer aktiv war.

    ![Rollenaktivierung auswählen](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportieren von Rollenzuweisungen mit untergeordneten Elementen

Angenommen, Sie müssen Prüfern aus Konformitätsgründen eine vollständige Liste mit allen Rollenzuweisungen zur Verfügung stellen. Mit PIM können Sie Rollenzuweisungen für eine bestimmte Ressource abfragen – einschließlich Rollenzuweisungen für alle untergeordneten Ressourcen. In der Vergangenheit war es für Administratoren nicht ganz einfach, eine vollständige Liste mit den Rollenzuweisungen für ein Abonnement zu erhalten, und die Rollenzuweisungen mussten für jede spezifische Ressource exportiert werden. Mit PIM können Sie alle aktiven und geeigneten Rollenzuweisungen in einem Abonnement abrufen – einschließlich Rollenzuweisungen für alle Ressourcengruppen und Ressourcen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, für die Sie Rollenzuweisungen exportieren möchten (beispielsweise ein Abonnement).

1. Klicken Sie auf **Mitglieder**.

1. Klicken Sie auf **Exportieren**, um den Bereich „Mitgliedschaft exportieren“ zu öffnen.

    ![Bereich „Mitgliedschaft exportieren“](media/azure-pim-resource-rbac/export-membership.png)

1. Klicken Sie auf **Export all members** (Alle Mitglieder exportieren), um alle Rollenzuweisungen in eine CSV-Datei zu exportieren.

    ![Exportieren in eine CSV-Datei](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Anzeigen des Ressourcenüberwachungsverlaufs

Die Ressourcenüberwachung bietet einen Überblick über alle Rollenaktivitäten für eine Ressource.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, für die Sie den Überwachungsverlauf anzeigen möchten.

1. Klicken Sie auf **Ressourcenüberwachung**.

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    ![Filtern der Ressourcenüberwachung](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Wählen Sie unter **Überwachungstyp** die Option **Aktivieren** (zugewiesen und aktiviert) aus.

    ![Aktivitätsdetails](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Klicken Sie unter **Aktion** für einen Benutzer auf **(Aktivität)** , um Details zur Aktivität des Benutzers in Azure-Ressourcen anzuzeigen.

    ![Details zur Benutzeraktivität](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Anzeigen der eigenen Überwachung

In der eigenen Überwachung können Sie Ihre persönliche Rollenaktivität anzeigen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure-Ressourcen**.

1. Klicken Sie auf die Ressource, für die Sie den Überwachungsverlauf anzeigen möchten.

1. Klicken Sie auf **My audit** (Meine Überwachung).

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    ![Persönliche Rollenaktivität](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
- [Anzeigen des Überwachungsverlaufs für Azure AD-Rollen in PIM](pim-how-to-use-audit-log.md)
