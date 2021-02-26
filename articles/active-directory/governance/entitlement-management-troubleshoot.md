---
title: Problembehandlung bei der Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, welche Punkte Sie überprüfen sollten, um Probleme mit der Azure Active Directory-Berechtigungsverwaltung zu beheben.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b01321c2cbb15be5526bbe9ed04f52238390574
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516888"
---
# <a name="troubleshoot-azure-ad-entitlement-management"></a>Problembehandlung bei der Azure AD-Berechtigungsverwaltung

In diesem Artikel werden einige Punkte beschrieben, die Sie überprüfen sollten, um Probleme mit der Berechtigungsverwaltung von Azure Active Directory (Azure AD) zu beheben.

## <a name="administration"></a>Verwaltung

* Wenn Sie beim Konfigurieren der Berechtigungsverwaltung eine Meldung vom Typ „Zugriff verweigert“ erhalten und Sie ein globaler Administrator sind, stellen Sie sicher, dass Ihr Verzeichnis über eine [Azure AD Premium P2- oder EMS E5-Lizenz](entitlement-management-overview.md#license-requirements) verfügt.

* Wenn Sie beim Erstellen oder Anzeigen von Zugriffspaketen eine Meldung vom Typ „Zugriff verweigert“ erhalten und Sie Mitglied der Gruppe „Katalogersteller“ sind, müssen Sie vor dem Erstellen Ihres ersten Zugriffspakets einen [Katalog erstellen](entitlement-management-catalog-create.md).

## <a name="resources"></a>Ressourcen

* Rollen für Anwendungen werden von der Anwendung selbst definiert und in Azure AD verwaltet. Wenn eine Anwendung keine Ressourcenrollen umfasst, ordnet die Berechtigungsverwaltung die Benutzer einer Rolle mit **Standardzugriff** zu.

    Beachten Sie, dass das Azure-Portal möglicherweise auch Dienstprinzipale für Dienste anzeigt, die nicht als Anwendungen ausgewählt werden können.  Insbesondere sind **Exchange Online** und **SharePoint Online** Dienste, aber keine Anwendungen, die über Ressourcenrollen im Verzeichnis verfügen. Daher können sie nicht in ein Zugriffspaket einbezogen werden.  Verwenden Sie stattdessen die gruppenbasierte Lizenzierung, um geeignete Lizenzen für Benutzer einzurichten, die Zugriff auf diese Dienste benötigen.

* Damit eine Gruppe als Ressource in einem Zugriffspaket dienen kann, muss sie in Azure AD bearbeitet werden können.  Gruppen, die aus einer lokalen Active Directory-Instanz stammen, können nicht als Ressourcen zugewiesen werden, weil ihre Besitzer- oder Mitgliedsattribute in Azure AD nicht geändert werden können.   Gruppen, die ursprünglich als Verteilergruppen in Exchange Online vorliegen, können in Azure AD ebenfalls nicht geändert werden. 

* SharePoint Online-Dokumentbibliotheken und einzelne Dokumente können nicht als Ressourcen hinzugefügt werden.  Erstellen Sie stattdessen eine [Azure AD-Sicherheitsgruppe](../fundamentals/active-directory-groups-create-azure-portal.md), fügen Sie diese Gruppe und eine Standortrolle zum Zugriffspaket hinzu, und verwenden Sie die Gruppe in SharePoint Online, um den Zugriff auf die Dokumentbibliothek oder das Dokument zu steuern.

* Wenn bereits Benutzer einer Ressource zugewiesen wurden, die Sie mit einem Zugriffspaket verwalten möchten, stellen Sie sicher, dass die Benutzer dem Zugriffspaket mit einer entsprechenden Richtlinie zugewiesen sind. Möglicherweise möchten Sie beispielsweise eine Gruppe in ein Zugriffspaket einschließen, das bereits Benutzer in der Gruppe enthält. Wenn diese Benutzer in der Gruppe weiterhin Zugriff benötigen, müssen sie über eine entsprechende Richtlinie für die Zugriffspakete verfügen, damit sie ihren Zugriff auf die Gruppe nicht verlieren. Sie können das Zugriffspaket zuweisen, indem Sie entweder die Benutzer bitten, das Zugriffspaket mit der entsprechenden Ressource anzufordern, oder die Benutzer direkt dem Zugriffspaket zuweisen. Weitere Informationen finden Sie unter [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket](entitlement-management-access-package-request-policy.md).

* Wenn Sie ein Teammitglied entfernen, wird es auch aus der Microsoft 365-Gruppe entfernt. Das Entfernen aus der Chatfunktion des Teams kann etwas länger dauern. Weitere Informationen finden Sie unter [Gruppenmitgliedschaft](/microsoftteams/office-365-groups#group-membership).


## <a name="access-packages"></a>Zugriffspakete

* Wenn Sie versuchen, ein Zugriffspaket oder eine Zugriffsrichtlinie zu löschen, wird unter Umständen eine Fehlermeldung mit dem Hinweis angezeigt, dass aktive Zuweisungen vorhanden sind. In diesem Fall sollten Sie überprüfen, ob kürzlich gelöschte Benutzer noch über Zuweisungen verfügen, wenn keine Benutzer mit Zuweisungen angezeigt werden. Das Benutzerkonto kann während eines Zeitfensters von 30 Tagen nach der Löschung eines Benutzers wiederhergestellt werden.   

## <a name="external-users"></a>Externe Benutzer

* Wenn ein externer Benutzer Zugriff auf ein Zugriffspaket anfordern möchte, müssen Sie sicherstellen, dass der Benutzer den **Link zum Portal „Mein Zugriff“** für das Zugriffspaket verwendet. Weitere Informationen finden Sie unter [Teilen des Links zum Anfordern eines Zugriffspakets](entitlement-management-access-package-settings.md). Wenn ein externer Benutzer lediglich zu **myaccess.microsoft.com** navigiert und nicht den vollständigen Link zum Portal „Mein Zugriff“ verwendet, werden nur die verfügbaren Zugriffspakete in der eigenen Organisation angezeigt, nicht diejenigen in Ihrer Organisation.

* Wenn ein externer Benutzer nicht in der Lage ist, den Zugriff auf ein Zugriffspaket anzufordern oder auf Ressourcen zuzugreifen, überprüfen Sie Ihre [Einstellungen für externe Benutzer](entitlement-management-external-users.md#settings-for-external-users).

* Wenn ein neuer externer Benutzer, der sich vorher noch nicht bei Ihrem Verzeichnis angemeldet hat, ein Zugriffspaket einschließlich einer SharePoint Online-Website erhält, wird das Zugriffspaket erst als vollständig übermittelt angezeigt, nachdem das Benutzerkonto in SharePoint Online bereitgestellt wurde. Weitere Informationen zu Freigabeeinstellungen finden Sie unter [Überprüfen der SharePoint Online-Einstellungen für externe Freigaben](entitlement-management-external-users.md#review-your-sharepoint-online-external-sharing-settings).

## <a name="requests"></a>Requests

* Wenn ein Benutzer Zugriff auf ein Zugriffspaket anfordern möchte, müssen Sie sicherstellen, dass der Benutzer den **Link zum Portal „Mein Zugriff“** für das Zugriffspaket verwendet. Weitere Informationen finden Sie unter [Teilen des Links zum Anfordern eines Zugriffspakets](entitlement-management-access-package-settings.md).

* Wenn Sie das Portal „Mein Zugriff“ mit einem Browser öffnen, der sich im privaten Modus oder im Inkognitomodus befindet, kann dies zu einem Konflikt mit dem Anmeldeverhalten führen. Es wird davon abgeraten, den privaten oder den Inkognitomodus für Ihren Browser zu verwenden, wenn Sie das Portal „Mein Zugriff“ besuchen.

* Wenn sich ein Benutzer, der noch nicht in Ihrem Verzeichnis enthalten ist, sich beim Portal „Mein Zugriff“ anmeldet, um ein Zugriffspaket anzufordern, müssen Sie sicherstellen, dass er sich mit seinem Organisationskonto authentifiziert. Bei dem Organisationskonto kann es sich um ein Konto im Ressourcenverzeichnis oder um ein Konto in einem Verzeichnis handeln, das in einer der Richtlinien des Zugriffspakets enthalten ist. Wenn es sich bei dem Konto des Benutzers nicht um ein Organisationskonto handelt oder das Verzeichnis, bei dem sie sich authentifizieren, nicht in der Richtlinie enthalten ist, wird dem Benutzer das Zugriffspaket nicht angezeigt. Weitere Informationen finden Sie unter [Anfordern des Zugriffs auf ein Zugriffspaket](entitlement-management-request-access.md).

* Wenn die Benutzeranmeldung beim Ressourcenverzeichnis blockiert wird, kann der Benutzer keinen Zugriff im Portal „Mein Zugriff“ anfordern. Bevor der Benutzer den Zugriff anfordern kann, müssen Sie die Anmeldeblockierung aus dem Profil des Benutzers entfernen. Um die Anmeldeblockierung zu entfernen, klicken Sie im Azure-Portal auf **Azure Active Directory**, klicken Sie auf **Benutzer**, klicken Sie auf den Benutzer, und klicken Sie dann auf **Profil**. Bearbeiten Sie den Abschnitt **Einstellungen**, und ändern Sie **Anmeldung blockieren** in **Nein**. Weitere Informationen finden Sie unter [Hinzufügen oder Aktualisieren von Benutzerprofilinformationen mit Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Sie können auch überprüfen, ob der Benutzer aufgrund einer [Identity Protection-Richtlinie](../identity-protection/howto-identity-protection-remediate-unblock.md) blockiert wurde.

* Wenn ein Benutzer sowohl Anforderer als auch Genehmiger ist, wird ihm im Portal „Mein Zugriff“ auf der Seite **Genehmigungen** die Anforderung für ein Zugriffspaket nicht angezeigt. Dieses Verhalten ist beabsichtigt: Ein Benutzer kann seine eigene Anforderung nicht genehmigen. Stellen Sie sicher, dass für das angeforderte Zugriffspaket zusätzliche genehmigende Personen in der Richtlinie konfiguriert sind. Weitere Informationen finden Sie unter [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket](entitlement-management-access-package-request-policy.md).

### <a name="view-a-requests-delivery-errors"></a>Anzeigen der Fehler bei der Übermittlung einer Anforderung

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Wählen Sie die Anforderung aus, die angezeigt werden soll.

    Sind bei der Übermittlung der Anforderung Fehler aufgetreten, lautet der Status der Anforderung **Nicht übermittelt** oder **Teilweise übermittelt**.

    Sind Übermittlungsfehler aufgetreten, wird deren Anzahl im Detailbereich der Anforderung angezeigt.

1. Klicken Sie auf die Anzahl, um alle Fehler bei der Übermittlung der Anforderung anzuzeigen.

### <a name="reprocess-a-request"></a>Erneutes Verarbeiten einer Anforderung

Wenn nach dem Auslösen einer Anforderung zum erneuten Verarbeiten eines Zugriffspakets ein Fehler auftritt, müssen Sie warten, bis die Anforderung vom System erneut verarbeitet wurde. Das System versucht über mehrere Stunden mehrmals, eine erneute Verarbeitung durchzuführen, daher können Sie während dieser Zeit keine erneute Verarbeitung erzwingen. 

Die erneute Verarbeitung ist nur für Anforderungen mit dem Status **Übermittlungsfehler** oder **Teilweise übermittelt** und einem Abschlussdatum möglich, das weniger eine Woche zurückliegt. Andernfalls ist die Schaltfläche **Erneut verarbeiten** deaktiviert.

![Ausgegraute Schaltfläche „Erneut verarbeiten“](./media/entitlement-management-troubleshoot/cancel-reprocess-grayedout.png)

- Wenn der Fehler während dieses Versuchsintervalls korrigiert wird, ändert sich der Anforderungsstatus in **Wird übermittelt**. Die Anforderung wird ohne zusätzliche Aktionen vom Benutzer erneut verarbeitet.

- Wenn der Fehler während des Versuchsintervalls nicht korrigiert wurde, lautet der Anforderungsstatus **Übermittlungsfehler** oder **Teilweise geliefert**. Sie können dann die Schaltfläche **Erneut verarbeiten** verwenden. Sie haben sieben Tage Zeit, die Anforderung erneut zu verarbeiten.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Wählen Sie die Anforderung aus, die Sie erneut verarbeiten möchten.

1. Klicken Sie im Detailbereich der Anforderung auf **Anforderung erneut verarbeiten**.

    ![Erneutes Verarbeiten einer nicht erfolgreichen Anforderung](./media/entitlement-management-troubleshoot/reprocess-request.png)

### <a name="cancel-a-pending-request"></a>Abbrechen einer ausstehenden Anforderung

Es können nur ausstehende Anforderungen abgebrochen werden, die noch nicht übermittelt wurden oder bei deren Übermittlung ein Fehler aufgetreten ist. Andernfalls ist die Schaltfläche **Abbrechen** ausgegraut.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaketmanager oder Zugriffspaketzuweisungsmanager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Anforderungen**.

1. Wählen Sie die Anforderung aus, die Sie abbrechen möchten.

1. Klicken Sie im Detailbereich der Anforderung auf **Anforderung abbrechen**.

## <a name="multiple-policies"></a>Mehrere Richtlinien

* Die Berechtigungsverwaltung folgt den Best Practices der geringsten Rechte. Wenn ein Benutzer Zugriff auf ein Zugriffspaket mit mehreren gültigen Richtlinien anfordert, sorgt die Logik der Berechtigungsverwaltung dafür, dass strengere oder spezifischere Richtlinien gegenüber generischen Richtlinien priorisiert werden. Wenn eine Richtlinie generisch ist, zeigt die Berechtigungsverwaltung die Richtlinie dem Anforderer möglicherweise nicht an oder wählt automatisch eine strengere Richtlinie aus.

* Betrachten Sie folgendes Beispiel: Es ist ein Zugriffspaket mit zwei Richtlinien für interne Mitarbeiter vorhanden, bei dem beide Richtlinien für den Anforderer gelten. Die erste Richtlinie gilt für bestimmte Benutzer, zu denen auch der Anforderer gehört. Die zweite Richtlinie gilt für alle Benutzer in einem Verzeichnis, in dem der Anforderer Mitglied ist. In diesem Szenario wird automatisch die erste Richtlinie für den Anforderer ausgewählt, weil sie strenger ist. Dem Anforderer wird nicht die Möglichkeit gegeben, die zweite Richtlinie auszuwählen.

* Wenn mehrere Richtlinien gelten, basiert die automatische Auswahl einer Richtlinie oder die Richtlinienanzeige für den Anforderer auf der folgenden Prioritätslogik:

    | Richtlinienpriorität | `Scope` |
    | --- | --- |
    | P1 | Bestimmte Benutzer und Gruppen in Ihrem Verzeichnis ODER bestimmte verbundene Organisationen |
    | P2 | Alle Mitglieder in Ihrem Verzeichnis (Gastbenutzer ausgeschlossen) |
    | P3 | Alle Benutzer in Ihrem Verzeichnis (Gastbenutzer eingeschlossen) ODER bestimmte verbundene Organisationen |
    | P4 | Alle konfigurierten verbundenen Organisationen ODER alle Benutzer (alle verbundenen Organisationen und neue externe Benutzer) |
    
    Fällt eine Richtlinie in eine Kategorie mit höherer Priorität, werden die Kategorien mit niedrigerer Priorität ignoriert. Ein Beispiel dafür, wie dem Anforderer mehrere Richtlinien mit gleicher Priorität angezeigt werden, finden Sie unter [Auswählen einer Richtlinie](entitlement-management-request-access.md#select-a-policy).

## <a name="next-steps"></a>Nächste Schritte

- [Steuern des Zugriffs für externe Benutzer in der Azure AD-Berechtigungsverwaltung (Vorschau)](entitlement-management-external-users.md)
- [Anzeigen von Berichten und Protokollen in der Azure AD-Berechtigungsverwaltung](entitlement-management-reports.md)
