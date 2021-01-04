---
title: 'Anzeigen des Überwachungsberichts für Azure-Ressourcenrollen in Privileged Identity Management (PIM): Azure AD | Microsoft-Dokumentation'
description: Zeigen Sie Aktivitäten und den Überwachungsverlauf für Azure-Ressourcenrollen in Azure AD Privileged Identity Management (PIM) an.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 01/10/2020
ms.author: curtand
ms.reviewer: shaunliu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45144f64789a19390984c3f9f6a660e3c3300215
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002492"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-privileged-identity-management"></a>Anzeigen von Aktivitäten und des Überwachungsverlaufs für Azure-Ressourcenrollen in Privileged Identity Management

Mit Azure Active Directory (Azure AD) Privileged Identity Management (PIM) können Sie Aktivitäten, Aktivierungen und den Überwachungsverlauf für Azure-Ressourcenrollen innerhalb Ihrer Organisation anzeigen. Dies schließt Abonnements, Ressourcengruppen und sogar virtuelle Computer ein. Alle Ressourcen im Azure-Portal, die die Azure-Funktion für die rollenbasierte Zugriffssteuerung nutzen, können von den Sicherheits- und Lebenszyklusverwaltungsfunktionen von Privileged Identity Management profitieren.

> [!NOTE]
> Wenn Ihre Organisation Verwaltungsfunktionen an einen Dienstanbieter ausgelagert hat, der die [Delegierte Azure-Ressourcenverwaltung](../../lighthouse/concepts/azure-delegated-resource-management.md) verwendet, werden die von diesem Dienstanbieter autorisierten Rollenzuweisungen hier nicht angezeigt.

## <a name="view-activity-and-activations"></a>Anzeigen von Aktivitäten und Aktivierungen

Um zu sehen, welche Aktionen ein bestimmter Benutzer für verschiedene Ressourcen ausgeführt hat, können Sie die Azure-Ressourcenaktivität für einen bestimmten Aktivierungszeitraum anzeigen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die Ressource aus, für die Sie Aktivitäten und Aktivierungen anzeigen möchten.

1. Wählen Sie **Rollen** oder **Mitglieder** aus.

1. Wählen Sie einen Benutzer aus.

    Daraufhin wird eine nach Datum aufgeschlüsselte Zusammenfassung mit den Aktionen des Benutzers in Azure-Ressourcen angezeigt. Außerdem sind die letzten Rollenaktivierungen im selben Zeitraum zu sehen.

    ![Benutzerdetails mit Zusammenfassung zur Ressourcenaktivität und Rollenaktivierungen](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Wählen Sie eine bestimmte Rollenaktivierung aus, um Details und die entsprechende Azure-Ressourcenaktivität anzuzeigen, die ausgeführt wurde, während der Benutzer aktiv war.

    [![Ausgewählte Rollenaktivierung und Aktivitätsdetails](media/azure-pim-resource-rbac/export-membership.png "Ausgewählte Rollenaktivierung und Aktivitätsdetails")](media/azure-pim-resource-rbac/export-membership.png)

## <a name="export-role-assignments-with-children"></a>Exportieren von Rollenzuweisungen mit untergeordneten Elementen

Angenommen, Sie müssen Prüfern aus Konformitätsgründen eine vollständige Liste mit allen Rollenzuweisungen zur Verfügung stellen. Mit Privileged Identity Management können Sie Rollenzuweisungen für eine bestimmte Ressource einschließlich der Rollenzuweisungen für alle untergeordneten Ressourcen abfragen. In der Vergangenheit war es für Administratoren nicht ganz einfach, eine vollständige Liste mit den Rollenzuweisungen für ein Abonnement zu erhalten, und die Rollenzuweisungen mussten für jede spezifische Ressource exportiert werden. Mit Privileged Identity Management können Sie alle aktiven und geeigneten Rollenzuweisungen in einem Abonnement einschließlich der Rollenzuweisungen für alle Ressourcengruppen und Ressourcen abfragen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die Ressource aus, für die Sie Rollenzuweisungen exportieren möchten (beispielsweise ein Abonnement).

1. Wählen Sie **Mitglieder** aus.

1. Wählen Sie **Exportieren** aus, um den Bereich „Mitgliedschaft exportieren“ zu öffnen.

    [![Bereich „Mitgliedschaft exportieren“ zum Exportieren aller Mitglieder](media/azure-pim-resource-rbac/export-membership.png "Seite „Mitgliedschaft exportieren“ zum Exportieren aller Mitglieder")](media/azure-pim-resource-rbac/export-membership.png)

1. Wählen Sie **Alle Mitglieder exportieren** aus, um alle Rollenzuweisungen in eine CSV-Datei zu exportieren.

    ![Exportierte Rollenzuweisungen in CSV-Datei wie in Excel angezeigt](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Anzeigen des Ressourcenüberwachungsverlaufs

Die Ressourcenüberwachung bietet einen Überblick über alle Rollenaktivitäten für eine Ressource.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die Ressource aus, für die Sie den Überwachungsverlauf anzeigen möchten.

1. Wählen Sie **Ressourcenüberwachung** aus.

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    [![Ressourcenüberwachungsliste mit Filtern](media/azure-pim-resource-rbac/rbac-resource-audit.png "Ressourcenüberwachungsliste mit Filtern")](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. Wählen Sie unter **Überwachungstyp** die Option **Aktivieren** (zugewiesen und aktiviert) aus.

    [![Nach Überwachungstyp „Aktivieren“ gefilterte Ressourcenüberwachungsliste](media/azure-pim-resource-rbac/rbac-audit-activity.png "Nach Überwachungstyp „Aktivieren“ gefilterte Ressourcenüberwachungsliste")](media/azure-pim-resource-rbac/rbac-audit-activity.png) ![Nach Überwachungstyp „Aktivieren“ gefilterte Ressourcenüberwachungsliste](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Klicken Sie unter **Aktion** für einen Benutzer auf **(Aktivität)** , um Details zur Aktivität des Benutzers in Azure-Ressourcen anzuzeigen.

    ![Benutzeraktivitätsdetails für eine bestimmte Aktion](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Anzeigen der eigenen Überwachung

In der eigenen Überwachung können Sie Ihre persönliche Rollenaktivität anzeigen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure-Ressourcen** aus.

1. Wählen Sie die Ressource aus, für die Sie den Überwachungsverlauf anzeigen möchten.

1. Wählen Sie **Meine Überwachung** aus.

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    [![Überwachungsliste für den aktuellen Benutzer](media/azure-pim-resource-rbac/my-audit-time.png "Überwachungsliste für den aktuellen Benutzer")](media/azure-pim-resource-rbac/my-audit-time.png)

> [!NOTE]
> Der Zugriff auf den Überwachungsverlauf erfordert eine der Rollen „Globaler Administrator“ oder „Administrator für privilegierte Rollen“.

## <a name="get-reason-approver-and-ticket-number-for-approval-events"></a>Abrufen von Grund, genehmigender Person und Ticketnummer für Genehmigungsereignisse

1. Melden Sie sich beim [Azure-Portal](https://aad.portal.azure.com) mit den Berechtigungen der Rolle „Administrator für privilegierte Rollen“ an, und öffnen Sie Azure AD.
1. Wählen Sie **Überwachungsprotokolle** aus.
1. Verwenden Sie den Filter **Dienst**, um lediglich Überwachungsereignisse für den Dienst Privileged Identity Management anzuzeigen. Auf der Seite **Überwachungsprotokoll** haben Sie folgende Möglichkeiten:

    - Zeigen Sie Gründe für ein Überwachungsereignis in der Spalte **Statusursache** an.
    - Zeigen Sie die genehmigenden Person in der Spalte **Initiiert von (Akteur)** für das Ereignis „Anforderung zum Hinzufügen von Mitglied zur Rolle genehmigt“ an.

    [![Filtern des Überwachungsprotokolls für den PIM-Dienst](media/azure-pim-resource-rbac/filter-audit-logs.png "Filtern des Überwachungsprotokolls für den PIM-Dienst")](media/azure-pim-resource-rbac/filter-audit-logs.png)

1. Wählen Sie ein Überwachungsprotokollereignis aus, um die Ticketnummer auf der Registerkarte **Aktivität** im **Detailbereich** anzuzeigen.
  
    [![Überprüfen der Ticketnummer für das Überwachungsereignis](media/azure-pim-resource-rbac/audit-event-ticket-number.png "Überprüfen der Ticketnummer für das Überwachungsereignis")](media/azure-pim-resource-rbac/audit-event-ticket-number.png)]

1. Sie können den Anforderer (Person, die die Rolle aktiviert) auf der Registerkarte **Ziele** im **Detailbereich** für ein Überwachungsereignis anzeigen. Es gibt drei Zieltypen für Azure-Ressourcenrollen:

    - Die Rolle (**Typ** = Rolle)
    - Den Anforderer (**Typ** = Andere)
    - Die genehmigende Person (**Typ** = Benutzer)

    [![Überprüfen des Zieltyps](media/azure-pim-resource-rbac/audit-event-target-type.png "Überprüfen des Zieltyps")](media/azure-pim-resource-rbac/audit-event-target-type.png)

In der Regel ist das Protokollereignis direkt oberhalb des Genehmigungsereignisses ein Ereignis für die Funktion „Hinzufügen von Mitglied zur Rolle abgeschlossen“, wobei unter **Initiiert von (Akteur)** der Anforderer steht. In den meisten Fällen ist es für die Überwachung nicht erforderlich, die anfordernde Person in der Genehmigungsanforderung zu ermitteln.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure-Ressourcenrollen in PIM](pim-resource-roles-assign-roles.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure-Ressourcenrollen in PIM](pim-resource-roles-approval-workflow.md)
- [Anzeigen des Überwachungsverlaufs für Azure AD-Rollen in PIM](pim-how-to-use-audit-log.md)
