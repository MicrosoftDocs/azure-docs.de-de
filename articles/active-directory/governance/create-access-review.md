---
title: Erstellen einer Zugriffsüberprüfung von Gruppen und Anwendungen – Azure AD
description: Erfahren Sie, wie Sie eine Gruppenmitglieder oder den Anwendungszugriff betreffende Zugriffsüberprüfung in Azure Active Directory-Zugriffsüberprüfungen erstellen.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/07/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b12eb95a7840bdbb902701fc644eee30ffe9900f
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778536"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Erstellen einer Zugriffsüberprüfung für Gruppen und Anwendungen in Azure AD-Zugriffsüberprüfungen

Der Zugriff auf Gruppen und Anwendungen für Mitarbeiter und Gäste ändert sich im Laufe der Zeit. Zur Senkung der Risiken im Zusammenhang mit veralteten Zugriffszuweisungen können Administratoren mithilfe von Azure Active Directory (Azure AD) Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff erstellen. Für eine routinemäßige Überprüfung können bei Bedarf auch wiederkehrende Zugriffsüberprüfungen erstellt werden. Weitere Informationen zu diesen Szenarien finden Sie unter [Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen](manage-user-access-with-access-reviews.md) sowie unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](manage-guest-access-with-access-reviews.md).

Sehen Sie sich ein kurzes Video zum Aktivieren von Zugriffsüberprüfungen an:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

In diesem Artikel wird die Erstellung einer oder mehrerer Zugriffsüberprüfungen für Gruppenmitglieder oder Anwendungszugriff beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2
- Globaler Administrator oder Benutzeradministrator

Weitere Informationen finden Sie unter [Lizenzanforderungen](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Erstellen einer oder mehrerer Zugriffsüberprüfungen

1. Melden Sie sich beim Azure-Portal an, und öffnen Sie die Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. Klicken Sie im linken Menü auf **Zugriffsüberprüfungen**.

3. Klicken Sie auf **Neue Zugriffsüberprüfung**, um eine neue Zugriffsüberprüfung zu erstellen.

    ![Bereich „Zugriffsüberprüfungen“ in Identity Governance](./media/create-access-review/access-reviews.png)

4. Wählen Sie in **Schritt 1: Auswählen, was überprüft werden soll** aus, welche Ressource Sie überprüfen möchten.

    ![Erstellen einer Zugriffsüberprüfung – Name und Beschreibung der Überprüfung](./media/create-access-review/select-what-review.png)

5. Wenn Sie in Schritt 1 **Teams und Gruppen** ausgewählt haben, stehen Ihnen in Schritt 2 zwei Optionen zur Auswahl.
   - **All Microsoft 365 groups with guest users** (Alle Microsoft 365-Gruppen mit Gastbenutzern): Wählen Sie diese Option aus, wenn Sie wiederkehrende Überprüfungen für alle Gastbenutzer in allen Microsoft Teams- und Microsoft 365-Gruppen in Ihrer Organisation einrichten möchten. Sie können bestimmte Gruppen ausschließen, indem Sie auf „Select group(s) to exclude“ (Auszuschließende Gruppen auswählen) klicken.
   - **Select teams + groups** (Teams und Gruppen auswählen): Wählen Sie diese Option aus, wenn Sie feste Teams und Gruppen für die Überprüfung angeben möchten. Nach dem Klicken auf diese Option wird auf der rechten Seite eine Liste mit Gruppen zur Auswahl angezeigt.

     ![Teams und Gruppen](./media/create-access-review/teams-groups.png)

     ![Ausgewählte Option „Teams + Groups“ (Teams und Gruppen) auf der Benutzeroberfläche](./media/create-access-review/teams-groups-detailed.png)

6. Wenn Sie in Schritt 1 **Anwendungen** ausgewählt haben, können Sie in Schritt 2 eine oder mehrere Anwendungen auswählen.

    >[!NOTE]
    > Wenn Sie mehrere Gruppen oder Anwendungen auswählen, werden mehrere Zugriffsüberprüfungen erstellt. Wenn Sie z. B. fünf zu überprüfende Gruppen auswählen, werden auch fünf separate Zugriffsüberprüfungen erstellt.

   ![Benutzeroberfläche bei Auswahl von „Anwendungen“ anstelle von Gruppen](./media/create-access-review/select-application-detailed.png)

7. Als Nächstes können Sie in Schritt 3 einen Bereich für die Überprüfung auswählen. Folgende Optionen sind verfügbar:
   - **Nur Gastbenutzer:** Wenn Sie diese Option auswählen, wird die Zugriffsüberprüfung ausschließlich auf die Azure AD B2B-Gastbenutzer in Ihrem Verzeichnis beschränkt.
   - **Jeder:** Wenn Sie diese Option auswählen, wird die Zugriffsüberprüfung auf alle Benutzerobjekte angewandt, die der Ressource zugeordnet sind.

    >[!NOTE]
    > Wenn Sie in Schritt 2 „All Microsoft 365 groups with guest users“ (Alle Microsoft 365-Gruppen mit Gastbenutzern) ausgewählt haben, steht Ihnen in Schritt 3 als einzige Option die Überprüfung von Gastbenutzern zur Verfügung.

8. Klicken Sie auf „Weiter: Überprüfungen
9. Wählen Sie im Abschnitt **Prüfer auswählen** mindestens eine Person für die Zugriffsüberprüfungen aus. Es gibt folgende Auswahlmöglichkeiten:
    - **Gruppenbesitzer** (nur verfügbar, wenn eine Überprüfung für ein Team oder eine Gruppe durchgeführt wird)
    - **Ausgewählte Benutzer oder Gruppen**
    - **Users review own access** (Benutzer überprüfen eigenen Zugriff)
    - **(Vorschau:) Managers of users** (Benutzermanager)
    Wenn Sie **Managers of users** (Benutzermanager) oder **Gruppenbesitzer** auswählen, haben Sie auch die Möglichkeit, einen Fallbackprüfer anzugeben. Fallbackprüfer werden aufgefordert, eine Überprüfung durchzuführen, wenn für den Benutzer kein Manager im Verzeichnis angegeben ist oder die Gruppe keinen Besitzer hat.

    ![Neue Zugriffsüberprüfung](./media/create-access-review/new-access-review.png)

10. Im Abschnitt **Specify recurrence of review** (Wiederholung für Überprüfung angeben) können Sie eine Häufigkeit angeben, z. B. **wöchentlich, monatlich, vierteljährlich, halbjährlich, jährlich**. Anschließend geben Sie eine **Dauer** an. Diese legt fest, wie lange eine Überprüfung für die Eingabe von Prüfern geöffnet bleiben soll. Für eine monatliche Überprüfung kann beispielsweise eine maximale Dauer von 27 Tagen angegeben werden, um Überschneidungen zu vermeiden. Sie sollten die Dauer eventuell verkürzen, um sicherzustellen, dass die Eingaben Ihrer Prüfer früher angewandt werden. Als Nächstes können Sie ein **Startdatum** und ein **Enddatum** auswählen.

    ![Auswählen der Häufigkeit von Überprüfungen](./media/create-access-review/frequency.png)

11. Klicken Sie auf die Schaltfläche **Weiter: Einstellungen** unten auf der Seite.
12. Unter **Einstellungen nach Abschluss** können Sie angeben, was nach Abschluss der Überprüfung geschehen soll.

    ![Erstellen einer Zugriffsüberprüfung: „Einstellungen nach Abschluss“](./media/create-access-review/upon-completion-settings-new.png)

Soll abgelehnten Benutzern automatisch der Zugriff entzogen werden, legen Sie Ergebnisse automatisch auf Ressource anwenden auf Aktivieren fest. Falls Sie die Ergebnisse nach Abschluss der Überprüfung manuell anwenden möchten, legen Sie die Einstellung auf Deaktivieren fest.
Geben Sie mithilfe der Liste If reviewers don‘t respond (Wenn die Prüfer nicht reagieren) an, was bei Benutzern geschehen soll, die vom Prüfer nicht innerhalb des vorgesehenen Zeitraums überprüft werden. Diese Einstellung hat keine Auswirkungen auf Benutzer, die von den Prüfern manuell überprüft wurden. Lautet die Entscheidung des Prüfers letztlich „Verweigern“, wird dem Benutzer der Zugriff entzogen.

- **Keine Änderung**: Der Zugriff des Benutzers bleibt unverändert.
- **Zugriff entfernen**: Dem Benutzer wird der Zugriff entzogen.
- **Zugriff genehmigen**: Der Zugriff des Benutzers wird genehmigt.
- **Empfehlungen annehmen**: Die Systemempfehlungen hinsichtlich der Ablehnung oder Gewährung des weiteren Benutzerzugriffs werden verwendet.

    ![Optionen für „Einstellungen nach Abschluss“](./media/create-access-review/upon-completion-settings-new.png)

Verwenden Sie **Auf verweigerte Gastbenutzer anzuwendende Aktion**, um festzulegen, was mit Gastbenutzern geschieht, wenn diese abgelehnt werden.
- Mit „Remove user’s membership from the resource“ (Benutzermitgliedschaft für Ressource entfernen) können Sie den Zugriff des abgelehnten Benutzers auf die zu überprüfende Gruppe oder Anwendung deaktivieren. Der Benutzer kann sich allerdings weiterhin beim Mandanten anmelden.
- Mit „Block user from signing-in for 30 days, then remove user from the tenant“ (Anmeldung des Benutzers für 30 Tage blockieren und den Benutzer anschließend aus Mandanten entfernen) können Sie verhindern, dass der abgelehnte Benutzer sich beim Mandanten anmeldet, unabhängig davon, ob er Zugriff auf andere Ressourcen hat. Wenn ein Fehler aufgetreten ist oder ein Administrator beschließt, den Zugriff erneut zu aktivieren, kann das innerhalb von 30 Tagen nach der Deaktivierung des Benutzers geschehen. Wenn keine Aktionen mit dem deaktivierten Benutzer durchgeführt werden, wird dieser aus dem Mandanten gelöscht.

Weitere Informationen zu bewährten Methoden zum Entfernen von Gastbenutzern, die keinen Zugriff mehr auf Ressourcen in Ihrer Organisation haben sollen, finden Sie im Artikel [Verwenden von Azure AD Identity Governance zum Überprüfen und Entfernen externer Benutzer, die keinen Zugriff mehr auf Ressourcen haben](access-reviews-external-users.md).

   >[!NOTE]
   >Die auf abgelehnte Gastbenutzer anzuwendende Aktion kann nicht für Überprüfungen mit einem größeren Gültigkeitsbereich als Gastbenutzer konfiguriert werden. Sie kann auch nicht für die Überprüfung **aller Microsoft 365-Gruppen mit Gastbenutzern** konfiguriert werden. Wenn diese Option nicht konfiguriert werden kann, wird für abgelehnte Benutzer die Standardoption (Entfernen der Benutzermitgliedschaft aus der Ressource) verwendet.

13. Wählen Sie unter **Enable review decision helpers** (Entscheidungshilfen für Überprüfungen aktivieren) aus, ob Ihre Prüfer während des Überprüfungsvorgangs Empfehlungen erhalten sollen.

    ![Optionen für das Aktivieren von Entscheidungshilfen](./media/create-access-review/helpers.png)

14. Sie können im Abschnitt **Erweiterte Einstellungen** Folgendes auswählen:
    - Legen Sie **Begründung erforderlich** auf **Aktivieren** fest, damit Prüfer einen Grund für die Genehmigung angeben müssen.
    - Legen Sie **E-Mail-Benachrichtigungen** auf **Aktivieren** fest, damit Azure AD beim Start einer Zugriffsüberprüfung E-Mail-Benachrichtigungen an die Prüfer und beim Abschluss einer Überprüfung Benachrichtigungen an Administratoren sendet.
    - Legen Sie **Erinnerungen** auf **Aktivieren** fest, damit Azure AD Erinnerungen zu laufenden Zugriffsüberprüfungen an Prüfer sendet, die ihre Überprüfung noch nicht abgeschlossen haben. Diese Erinnerungen erfolgen nach Ablauf der Hälfte der Überprüfungsdauer automatisch.
    - Der Inhalt der an Prüfer gesendeten E-Mail wird automatisch basierend auf den Überprüfungsdetails generiert, z. B. Name der Überprüfung, Name der Ressource, Fälligkeitsdatum usw. Wenn Sie eine Möglichkeit benötigen, zusätzliche Informationen wie etwa weitere Anweisungen oder Kontaktinformationen mitzuteilen, können Sie diese Informationen im Abschnitt **E-Mail mit zusätzlichen Inhalten für Prüfer** angeben. Die eingegebenen Informationen werden in die Einladungs- und Erinnerungs-E-Mails an die zugewiesenen Prüfer eingefügt. Der in der Abbildung unten hervorgehobene Abschnitt zeigt, wo diese Informationen angezeigt werden.


      ![Zusätzliche Inhalte für Prüfer](./media/create-access-review/additional-content-reviewer.png)

15. Klicken Sie auf **Weiter: Überprüfen und erstellen**, um zur nächsten Seite zu wechseln.
16. Benennen Sie die Zugriffsüberprüfung. Wahlweise können Sie jeder Überprüfung eine Beschreibung hinzufügen. Den Prüfern werden Name und Beschreibung angezeigt.
17. Überprüfen Sie die Informationen, und wählen Sie **Erstellen** aus.

       ![Bildschirm „Erstellen“ für Überprüfungen](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Starten der Zugriffsüberprüfung

Klicken Sie nach dem Festlegen der Einstellungen für eine Zugriffsüberprüfung auf **Starten**. Die Zugriffsüberprüfung wird in der Liste mit einer Angabe des Status angezeigt.

![Liste der Zugriffsüberprüfungen mit jeweiligem Status](./media/create-access-review/access-reviews-list.png)

Standardmäßig sendet Azure AD kurz nach dem Start der Überprüfung eine E-Mail an die Prüfer. Wenn Sie nicht möchten, dass Azure AD die E-Mail sendet, stellen Sie sicher, dass die Prüfer darüber in Kenntnis gesetzt werden, dass sie eine ausstehende Zugriffsüberprüfung abschließen müssen. Sie können ihnen die Anweisungen zum [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md) anzeigen. Wenn Ihre Überprüfung für Gäste gedacht ist, die ihren eigenen Zugriff überprüfen sollen, können Sie ihnen die Anweisungen zum [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen ](review-your-access.md) anzeigen.

Wenn Sie Gäste als Prüfer zugewiesen haben, diese die Einladung aber nicht angenommen haben, erhalten sie keine E-Mail zu Zugriffsüberprüfungen, da die Einladung zuerst akzeptiert werden muss, bevor Überprüfungen vorgenommen werden können.

## <a name="access-review-status-table"></a>Zugriffsüberprüfungs-Statustabelle

| Status | Definition |
|--------|------------|
|NotStarted | Die Überprüfung wurde erstellt, die Benutzerermittlung wartet auf den Start. |
|Wird initialisiert...   | Die Benutzerermittlung ist dabei, alle Benutzer zu identifizieren, die Teil der Überprüfung sind. |
|Wird gestartet | Die Überprüfung wird gestartet. Wenn E-Mail-Benachrichtigungen erlaubt sind, werden E-Mails an Überprüfer gesendet. |
|InProgress | Die Überprüfung hat begonnen. Wenn E-Mail-Benachrichtigungen aktiviert sind, wurden E-Mails an Überprüfer gesendet. Überprüfer können Entscheidungen bis zum Fälligkeitsdatum einreichen. |
|Wird abgeschlossen | Die Überprüfung wird abgeschlossen, und E-Mails werden an den Besitzer der Überprüfung gesendet. |
|Automatische Überprüfung | Die Überprüfung befindet sich in einer Systemüberprüfungsphase. Das System zeichnet Entscheidungen für Benutzer auf, die auf der Grundlage von Empfehlungen oder vorkonfigurierten Entscheidungen nicht geprüft wurden. |
|Automatisch überprüft | Für alle Benutzer, die nicht überprüft wurden, wurden Entscheidungen vom System aufgezeichnet. Die Überprüfung ist bereit, zu **Anwenden** fortzuschreiten, wenn die automatische Übernahme aktiviert ist. |
|Anwenden | Für Benutzer, die genehmigt wurden, wird der Zugriff nicht geändert. |
|Übernommen | Abgelehnte Benutzer, sofern vorhanden, wurden aus der Ressource oder dem Verzeichnis entfernt. |
|Fehler | Die Überprüfung konnte nicht fortgesetzt werden. Dieser Fehler kann auf das Löschen des Mandanten, eine Änderung an Lizenzen oder andere interne Mandantenänderungen zurückzuführen sein. |

## <a name="create-reviews-via-apis"></a>Erstellen von Überprüfungen über APIs

Zugriffsüberprüfungen können auch unter Verwendung von APIs erstellt werden. Die Aktionen, die Sie zur Verwaltung von Zugriffsüberprüfungen für Gruppen und Anwendungsbenutzer im Azure-Portal ausführen, können auch über Microsoft Graph-APIs ausgeführt werden. Weitere Informationen finden Sie unter [Azure AD-Zugriffsüberprüfungen](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Ein Codebeispiel finden Sie unter [Beispiel für das Abrufen von Azure AD-Zugriffsüberprüfungen über Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md)
- [Überprüfen des eigenen Zugriffs auf Gruppen oder Anwendungen](review-your-access.md)
- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)