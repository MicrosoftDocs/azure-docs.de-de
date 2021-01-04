---
title: Durchführen einer Zugriffsüberprüfung von Gruppen und Anwendungen – Azure AD
description: Erfahren Sie, wie Sie eine Gruppenmitglieder oder den Anwendungszugriff betreffende Zugriffsüberprüfung in Azure Active Directory-Zugriffsüberprüfungen durchführen.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6491de18e65c5071ac0972e7ff49d1253cbd402
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779543"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Durchführen einer Zugriffsüberprüfung für Gruppen und Anwendungen in Azure AD-Zugriffsüberprüfungen

Als Administrator [erstellen Sie eine Zugriffsüberprüfung für Gruppen oder Anwendungen](create-access-review.md), und Prüfer [führen dann die Zugriffsüberprüfung durch](perform-access-review.md). In diesem Artikel wird beschrieben, wie Sie die Ergebnisse der Zugriffsüberprüfung anzeigen und anwenden.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2
- Globaler Administrator, Benutzeradministrator, Sicherheitsadministrator oder Sicherheitsleseberechtigter

Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Anzeigen einer Zugriffsüberprüfung

Sie können den Status nachverfolgen, während die Prüfer die Überprüfungen durchführen.

1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Klicken Sie im linken Menü auf **Zugriffsüberprüfungen**.

1. Klicken Sie in der Liste auf eine Zugriffsüberprüfung.

    Um zukünftige Ausführungen einer Zugriffsüberprüfung anzuzeigen, navigieren Sie zu dieser Zugriffsüberprüfung, und wählen Sie „Geplante Überprüfungen“ aus.

    Auf der Seite **Übersicht** können Sie den Status verfolgen. Zugriffsrechte werden im Verzeichnis erst geändert, wenn die Überprüfung abgeschlossen ist.

    ![Fortschritt der Zugriffsüberprüfungen](./media/complete-access-review/overview-progress.png)
    
    Wenn Sie eine Zugriffsüberprüfung für den Gastzugriff für Microsoft 365-Gruppen (Vorschau) anzeigen, werden auf dem Blatt „Übersicht“ alle Gruppen in der Überprüfung aufgelistet.  

   ![Überprüfen des Gastzugriffs für Microsoft 365-Gruppen](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Klicken Sie auf eine Gruppe, um den Fortschritt der Überprüfung für diese Gruppe anzuzeigen.

   ![Details zur Überprüfung des Gastzugriffs für Microsoft 365-Gruppen](./media/complete-access-review/progress-group-review.png)

1. Klicken Sie auf die Schaltfläche **Beenden**, wenn Sie eine Zugriffsüberprüfung beenden möchten, bevor dafür das geplante Enddatum erreicht wurde.

    Nachdem eine Überprüfung beendet wurde, können Prüfer dafür keine Antworten mehr abgeben. Eine Überprüfung kann nicht neu gestartet werden, nachdem sie beendet wurde.

1. Wenn Sie die Zugriffsüberprüfung nicht mehr benötigen, können Sie sie löschen, indem Sie auf die Schaltfläche **Löschen** klicken.

## <a name="apply-the-changes"></a>Übernehmen von Änderungen

Bei Aktivierung von **Ergebnisse automatisch auf Ressource anwenden** wird basierend auf Ihrer Auswahl unter **Einstellungen nach Abschluss** nach dem Enddatum der Überprüfung oder bei ihrer manuellen Beendigung die automatische Anwendung durchgeführt.

Klicken Sie auf **Anwenden**, um die Änderungen manuell anzuwenden, wenn die Option **Ergebnisse automatisch auf Ressource anwenden** für die Überprüfung nicht aktiviert war. Wenn der Zugriff eines Benutzers in der Überprüfung verweigert wurde, entfernt Azure AD dessen Mitgliedschaft oder Anwendungszuweisung, nachdem Sie auf **Anwenden** geklickt haben.

![Anwenden von Änderungen der Zugriffsüberprüfung](./media/complete-access-review/apply-changes.png)

Der Status der Überprüfung ändert sich von **Abgeschlossen** über Zwischenzustände wie **Wird angewandt** schließlich in den Status **Ergebnis angewendet**. Erwartungsgemäß sollten abgelehnte Benutzer (sofern vorhanden) innerhalb weniger Minuten aus der Gruppenmitgliedschaft oder Anwendungszuweisung entfernt werden.

Die konfigurierte automatische Anwendung einer Überprüfung oder die Auswahl von **Anwenden** haben keine Auswirkung auf eine Gruppe, die aus einem lokalen Verzeichnis stammt, oder auf eine dynamische Gruppe. Wenn Sie eine Gruppe ändern möchten, die aus einem lokalen Verzeichnis stammt, laden Sie die Ergebnisse herunter, und wenden Sie diese Änderungen auf die Darstellung der Gruppe im Verzeichnis an.

## <a name="retrieve-the-results"></a>Abrufen der Ergebnisse

Klicken Sie auf die Seite **Ergebnisse**, um die Ergebnisse für eine einmalige Zugriffsüberprüfung anzuzeigen. Geben Sie im Suchfeld den Anzeigenamen oder Benutzerprinzipalnamen eines Benutzers ein, dessen Zugriff überprüft wurde, um nur den Zugriff dieses Benutzers anzuzeigen.

![Abrufen der Ergebnisse für eine Zugriffsüberprüfung](./media/complete-access-review/retrieve-results.png) 

Klicken Sie zum Anzeigen des Status einer aktiven Serienzugriffsüberprüfung auf die Seite **Ergebnisse**.

Klicken Sie zum Anzeigen der Ergebnisse einer abgeschlossenen Instanz einer Serienzugriffsüberprüfung auf **Ausführungsverlauf**. Wählen Sie anschließend basierend auf dem Start- und Enddatum der Instanz die spezifische Instanz in der Liste mit den abgeschlossenen Zugriffsüberprüfungsinstanzen aus. Die Ergebnisse dieser Instanz können auf der Seite **Ergebnisse** abgerufen werden.

Klicken Sie auf die Schaltfläche **Herunterladen**, wenn Sie alle Ergebnisse einer Zugriffsüberprüfung abrufen möchten. Die CSV-Datei mit den Ergebnissen kann in Excel oder in anderen Programmen angezeigt werden, die mit UTF-8 codierte CSV-Dateien öffnen können.

## <a name="remove-users-from-an-access-review"></a>Entfernen von Benutzern aus einer Zugriffsüberprüfung

 Ein gelöschter Benutzer bleibt in Azure AD standardmäßig 30 Tage lang gelöscht. In diesem Zeitraum kann er von einem Administrator wiederhergestellt werden, sofern erforderlich.  Nach 30 Tagen wird dieser Benutzer endgültig gelöscht.  Darüber hinaus kann ein globaler Administrator über das Azure Active Directory-Portal explizit [einen kürzlich gelöschten Benutzer endgültig löschen](../fundamentals/active-directory-users-restore.md), bevor dieser Zeitraum abgelaufen ist.  Wurde ein Benutzer endgültig gelöscht, werden nachfolgende Daten zu diesem Benutzer aus aktiven Zugriffsüberprüfungen entfernt.  Überwachungsinformationen zu gelöschten Benutzern verbleiben im Überwachungsprotokoll.

## <a name="next-steps"></a>Nächste Schritte

- [Manage user access with Azure AD access reviews (Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen)](manage-user-access-with-access-reviews.md)
- [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](manage-guest-access-with-access-reviews.md)
- [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)
- [Erstellen einer Zugriffsüberprüfung von Benutzern in der Azure AD-Administratorrolle](../privileged-identity-management/pim-how-to-start-security-review.md)
