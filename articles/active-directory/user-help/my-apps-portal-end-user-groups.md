---
title: Aktualisieren Ihrer Gruppeninformationen im Portal „Meine Apps“ – Azure AD
description: Erfahren Sie, wie Sie Ihre gruppenbezogenen Informationen anzeigen und aktualisieren können, einschließlich der Anzeige der Gruppen, die Sie besitzen, der Erstellung neuer Gruppen, der Anzeige der Gruppen, deren Mitglied Sie bereits sind, und des Beitritts zu allen Gruppen, denen Sie noch nicht angehören.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/19/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: cff1298f12d26b1260213d75701c282e0a82511d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004149"
---
# <a name="update-your-groups-info-on-the-my-apps-portal"></a>Aktualisieren Ihrer Gruppeninformationen im Portal „Meine Apps“

Sie können über Ihr Geschäfts-, Schul- oder Unikonto beim webbasierten Portal **Meine Apps** viele der cloudbasierten Apps Ihrer Organisation anzeigen und starten, bestimmte Profil- und Kontoinformationen aktualisieren, Informationen zu Ihren **Gruppen** anzeigen sowie **Zugriffsüberprüfungen** für Ihre Apps und Gruppen durchführen. Sollten Sie keinen Zugriff auf das Portal **Meine Apps** haben, wenden Sie sich an Ihren Helpdesk.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Dieser Inhalt richtet sich an Benutzer. Administratoren können sich in der [Dokumentation zur Anwendungsverwaltung](../manage-apps/index.yml) über die Einrichtung und Verwaltung cloudbasierter Apps informieren.

## <a name="view-your-groups-information"></a>Anzeigen von Informationen zu Ihren Gruppen

Wenn Ihr Administrator Ihnen die Berechtigung zum Anzeigen der Kachel **Gruppen** erteilt hat, haben Sie folgende Möglichkeiten:

- **Als Gruppenmitglied.** Details anzeigen oder Gruppen verlassen.

- **Als Gruppenbesitzer.** Details anzeigen, neue Gruppe erstellen, Mitglieder hinzufügen oder entfernen, Gruppe löschen.

### <a name="to-view-your-groups-information"></a>So zeigen Sie Informationen zu Ihren Gruppen an

1. Melden Sie sich bei Ihrem Geschäfts-, Schul- oder Unikonto an.

2. Öffnen Sie Ihren Webbrowser, und navigieren Sie zu https://myapps.microsoft.com, oder verwenden Sie den von Ihrer Organisation bereitgestellten Link. So können Sie beispielsweise zu einer benutzerdefinierten Seite Ihrer Organisation wie https://myapps.microsoft.com/contoso.com geleitet werden.

    Die Seite **Apps** wird mit allen im Besitz Ihrer Organisation befindlichen Cloud-Apps angezeigt, die Ihnen zur Verfügung stehen.

    ![Seite „Apps“ im Portal „Meine Apps“](media/my-apps-portal-end-user-groups/my-apps-home-large.png)

3. Wählen Sie im Pulldownmenü **Meine Apps** die Option **Meine Gruppen** aus, um Ihre gruppenbezogenen Informationen anzuzeigen.

4. Basierend auf Ihren Berechtigungen können Sie auf der Seite **Meine Gruppen** folgende Aktionen ausführen:

    - **Überprüfen der Gruppen, deren Besitzer Sie sind.** Sie können im Bereich **Gruppen in meinem Besitz** Informationen zu allen Gruppen anzeigen, die Sie innerhalb Ihrer Organisation besitzen. Wenn Sie einen bestimmten Gruppennamen auswählen, erhalten Sie weitere Informationen zur Gruppe, einschließlich Gruppentyp, Anzahl der Mitglieder, Richtlinie für Beitritt und Liste der aktiven Mitglieder.

    - **Erstellen einer neuen Gruppe.** Erstellen Sie im Bereich **Gruppen in meinem Besitz** eine neue Gruppe mit Ihnen als Besitzer. Die einzelnen Schritte finden Sie in diesem Artikel im Abschnitt [Erstellen einer neuen Gruppe](#create-a-new-group).

    - **Bearbeiten einer vorhandenen Gruppe**. Bearbeiten Sie die Details Ihrer eigenen Gruppen. Die einzelnen Schritte finden Sie in diesem Artikel im Abschnitt [Bearbeiten einer vorhandenen Gruppe](#edit-an-existing-group).

    - **Hinzufügen oder Entfernen von Mitgliedern**. Bei Gruppen in Ihrem Besitz können Sie Mitglieder hinzufügen und entfernen. Die einzelnen Schritte finden Sie in diesem Artikel im Abschnitt [Hinzufügen oder Entfernen von Mitgliedern](#add-or-remove-a-member).

    - **Verlängern einer Office 365-Gruppe**. Wenn Ihre Organisation dies zulässt, können Sie Ihre Office 365-Gruppen verlängern. Die einzelnen Schritte finden Sie in diesem Artikel im Abschnitt [Verlängern einer Office 365-Gruppe](#renew-an-office-365-group). 

    - **Löschen einer Gruppe**. Gruppen in Ihrem Besitz können Sie löschen. Die einzelnen Schritte finden Sie in diesem Artikel im Abschnitt [Löschen einer Gruppe](#delete-a-group).

    - **Überprüfen der Gruppen, deren Mitglied Sie sind.** Zeigen Sie im Bereich **Meine Gruppenmitgliedschaften** die Namen von Gruppen an, deren Mitglied Sie sind. Wenn Sie einen bestimmten Gruppennamen auswählen, erhalten Sie weitere Informationen zur Gruppe, einschließlich Gruppentyp, Anzahl der Mitglieder, Richtlinie für Beitritt und Liste der aktiven Mitglieder.

    - **Beitreten zu einer Gruppe**. Im Bereich **Meine Gruppenmitgliedschaften** können Sie einer bestehenden Gruppe beitreten, deren Mitglied Sie noch nicht sind. Die einzelnen Schritte finden Sie unter [Beitreten zu einer vorhandenen Gruppe](#join-an-existing-group).

## <a name="create-a-new-group"></a>Erstellen einer neuen Gruppe

1. Wählen Sie auf der Seite **Gruppen** im Bereich **Gruppen in meinem Besitz** den Befehl **Gruppe erstellen** aus.

    Das Dialogfeld **Gruppe erstellen** wird angezeigt.

    ![Feld „Gruppe erstellen“](media/my-apps-portal/my-apps-portal-create-group-page.png)

2. Geben Sie die erforderlichen Informationen ein:

    - **Gruppentyp:**

        - **Sicherheit.** Dieser Gruppentyp dient zur Verwaltung von Mitgliedern und des Computerzugriffs auf freigegebene Ressourcen für eine Gruppe von Benutzern. Beispielsweise können Sie eine Sicherheitsgruppe für eine bestimmte Sicherheitsrichtlinie erstellen. Auf diese Weise können Sie allen Mitgliedern gleichzeitig eine Gruppe von Berechtigungen gewähren, statt jedem einzelnen Mitglied separat Berechtigungen hinzuzufügen.

        - **Office 365.** Dieser Gruppentyp bietet Möglichkeiten zur Zusammenarbeit, indem Mitgliedern Zugriff auf freigegebene Postfächer, Kalender, Dateien, SharePoint-Websites und vieles mehr gewährt wird. Über diese Option können Sie auch Personen außerhalb Ihrer Organisation Zugriff auf die Gruppe gewähren.

    - **Gruppenname.** Fügen Sie einen aussagekräftigen Namen für die Gruppe hinzu.

    - **Gruppenbeschreibung (optional).** Fügen Sie ggf. eine Beschreibung zu Ihrer Gruppe hinzu.

    - **Gruppenrichtlinie.** Wählen Sie, ob entweder jeder der Gruppe beitreten darf oder ob nur der Besitzer der Gruppe Mitglieder hinzufügen darf.

3. Wählen Sie **Erstellen** aus.

    Die neue Gruppe wird mit Ihnen als Besitzer erstellt und in der Liste **Gruppen in meinem Besitz** angezeigt. Da Sie der Besitzer sind, wird auch diese Gruppe in der Liste **Meine Gruppenmitgliedschaften** angezeigt.

## <a name="edit-an-existing-group"></a>Bearbeiten einer vorhandenen Gruppe

Nachdem Sie eine Gruppe erstellt haben, können Sie ihre Details bearbeiten, d.h. vorhandene Informationen aktualisieren.

1. Wählen Sie auf der Seite **Gruppen** die Gruppe aus, die Sie bearbeiten möchten, und wählen Sie dann auf der Seite *&lt;Gruppenname&gt;* die Option **Details bearbeiten** aus.

    Das Feld **Details bearbeiten** wird angezeigt, in dem Sie die Informationen aktualisieren können, die Sie beim ersten Erstellen der Gruppe hinzugefügt haben.

2. Nehmen Sie die gewünschten Änderungen vor, und klicken Sie dann auf **Aktualisieren**.

## <a name="add-or-remove-a-member"></a>Hinzufügen oder Entfernen eines Mitglieds

Für Gruppen in Ihrem Besitz können Sie Mitglieder hinzufügen und entfernen.

1. Wählen Sie die Gruppe aus, der Sie Mitglieder hinzufügen möchten, und klicken Sie anschließend auf der Seite *&lt;Gruppenname&gt;* auf **+** .

    ![Hinzufügen eines Gruppenmitglieds mit hervorgehobenem Pluszeichen](media/my-apps-portal/my-apps-portal-add-member-link.png)

2. Suchen Sie im Feld **Mitglieder hinzufügen** das Mitglied, das Sie hinzufügen möchten, und klicken Sie dann auf **Hinzufügen**.

    ![Feld „Mitglied hinzufügen“ mit neuem hinzuzufügenden Mitglied](media/my-apps-portal/my-apps-portal-add-member-page.png)

    An das neue Mitglied wird eine Einladung gesendet, mit dem Zugriff auf die Apps der Organisation zu beginnen.

3. Wenn Sie ein Mitglied versehentlich hinzugefügt haben oder ein Mitglied Ihre Organisation verlassen hat, können Sie das Mitglied entfernen, indem Sie auf der Seite *&lt;Gruppenname&gt;* neben dem Namen des Mitglieds auf **Mitglied entfernen** klicken.

    ![Entfernen eines Mitglieds mit hervorgehobenem Link zum Entfernen](media/my-apps-portal/my-apps-portal-remove-member-link.png)

## <a name="renew-an-office-365-group"></a>Verlängern einer Office 365-Gruppe

Wenn Ihre Organisation dies zulässt, können Sie eine Office 365-Gruppe erneuern und damit deren Ablaufdatum verlängern.

1. Wählen Sie die Office 365-Gruppe aus, die Sie verlängern möchten, und wählen Sie dann **Gruppe verlängern** aus.

    ![Verlängern einer Office 365-Gruppe und deren Ablaufdatum](media/my-apps-portal/my-apps-portal-renew-group-link.png)

2. Klicken Sie auf **OK**, um die Bestätigungsmeldung zu schließen.

    Nachdem Sie die Seite aktualisiert haben, werden unter **Zuletzt erneuert** und **Gruppenablauf** die aktualisierten Datumsangaben angezeigt.

## <a name="delete-a-group"></a>Löschen einer Gruppe

Sie können Ihre eigenen Gruppen jederzeit löschen. Wenn Sie eine Gruppe versehentlich gelöscht haben, müssen Sie jedoch neu erstellen und Mitglieder wieder hinzufügen.

1. Wählen Sie die Gruppe aus, die Sie dauerhaft löschen möchten, und klicken Sie dann auf der Seite *&lt;Gruppenname&gt;* auf **Gruppe löschen**.

    ![Seite „<Gruppenname>“ mit hervorgehobenem Link zum Löschen der Gruppe](media/my-apps-portal/my-apps-portal-delete-group-link.png)

2. Klicken Sie in der Bestätigungsmeldung auf **Ja**.

    Die Gruppe wird endgültig gelöscht.

## <a name="join-an-existing-group"></a>Beitreten zu einer vorhandenen Gruppe

Auf der Seite **Gruppen** können Sie einer bereits vorhandenen Gruppe beitreten oder diese verlassen.

1. Wählen Sie auf der Seite **Gruppen** im Bereich **Meine Gruppenmitgliedschaften** den Befehl **Gruppe beitreten** aus.

    Daraufhin wird die Seite **Gruppen beitreten** angezeigt.

    ![Seite „Gruppen beitreten“ mit hervorgehobener Schaltfläche „Gruppe beitreten“](media/my-apps-portal/my-apps-portal-join-group-link.png)

2. Wählen Sie auf der Seite **Gruppen beitreten** den Namen der Gruppe, der Sie beitreten möchten. Zeigen Sie die zugehörigen Gruppendetails an, und wenn die Gruppe verfügbar ist, klicken Sie dann auf **Gruppe beitreten**.

    Wenn die Gruppe vom Gruppenbesitzer die Genehmigung der Mitgliedschaft verlangt, werden Sie aufgefordert, eine geschäftliche Begründung einzugeben, warum Sie der Gruppe beitreten müssen, und dann auf **Anforderung** zu klicken. Wenn die Gruppe keine Genehmigung erfordert, werden Sie sofort als Mitglied hinzugefügt, und die Gruppe wird in Ihrer Liste **Meine Gruppenmitgliedschaften** angezeigt.

3. Wenn Sie einer Gruppe versehentlich beigetreten sind oder Sie nicht mehr daran teilnehmen möchten, können Sie auf der Seite **Gruppen beitreten** den Gruppennamen auswählen und dann auf **Gruppe verlassen** klicken.

    ![Seite „Gruppen beitreten“ mit hervorgehobener Schaltfläche „Gruppe verlassen“](media/my-apps-portal/my-apps-portal-leave-group-link.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](my-apps-portal-end-user-access.md)

- [Ändern Ihrer Profilinformationen](./my-account-portal-settings.md)

- [Durchführen eigener Zugriffsüberprüfungen](my-apps-portal-end-user-access-reviews.md)