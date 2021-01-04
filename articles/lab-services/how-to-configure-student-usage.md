---
title: Konfigurieren von Nutzungseinstellungen in Labs von Azure Lab Services
description: Erfahren Sie, wie Sie die Anzahl der Kursteilnehmer für ein Lab konfigurieren, sie beim Lab registrieren, die Anzahl der Stunden steuern, in denen sie den virtuellen Computer verwenden können, und vieles mehr.
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: 3b05246445aea708312891ec631a35da3bc1eb8e
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602630"
---
# <a name="add-and-manage-lab-users"></a>Hinzufügen und Verwalten von Labbenutzern

In diesem Artikel wird beschrieben, wie Sie einem Lab Kursteilnehmer hinzufügen, diese beim Lab registrieren, die Anzahl der zusätzlichen Stunden steuern, für die sie den virtuellen Computer (VM) verwenden können, und vieles mehr. 

Wenn Sie Benutzer hinzufügen, ist die Option **Zugriff einschränken** standardmäßig aktiviert. Wenn sie nicht in der Benutzerliste enthalten sind, können sich Kursteilnehmer daher selbst dann nicht beim Labor registrieren, wenn sie über einen Registrierungslink verfügen. Nur in der Liste enthaltene Benutzer können sich über den von Ihnen gesendeten Registrierungslink beim Lab registrieren. Sie können **Zugriff beschränken** deaktivieren. In diesem Fall können sich Kursteilnehmer beim Lab registrieren, wenn sie über den Registrierungslink verfügen. 

In diesem Artikel wird beschrieben, wie Sie Benutzer einem Lab hinzufügen.

## <a name="add-users-from-an-azure-ad-group"></a>Hinzufügen von Benutzern aus einer Azure AD-Gruppe

### <a name="overview"></a>Übersicht

Sie können eine Lab-Benutzerliste jetzt mit einer vorhandenen Azure AD-Gruppe (Azure Active Directory) synchronisieren, damit Sie Benutzer nicht manuell hinzufügen oder löschen müssen. 

In der Azure Active Directory-Instanz Ihrer Organisation kann eine Azure AD-Gruppe erstellt werden, um den Zugriff auf Organisationsressourcen und cloudbasierte Apps zu verwalten. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Apps und Ressourcen mithilfe von Azure Active Directory-Gruppen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups). Wenn in Ihrer Organisation Microsoft Office 365- oder Azure-Dienste verwendet werden, verfügt Ihre Organisation bereits über Administratoren, die ihre Azure Active Directory-Instanz verwalten. 

### <a name="sync-users-with-azure-ad-group"></a>Synchronisieren von Benutzern mit der Azure AD-Gruppe

> [!IMPORTANT]
> Stellen Sie sicher, dass die Benutzerliste leer ist. Wenn in einem Lab Benutzer vorhanden sind, die Sie manuell oder durch Importieren einer CSV-Datei hinzugefügt haben, wird die Option zum Synchronisieren des Labs mit einer vorhandenen Gruppe nicht angezeigt. 

1. Melden Sie sich bei der [Azure Lab Services-Website](https://labs.azure.com/) an.
1. Wählen Sie das Lab aus, mit dem Sie arbeiten möchten.
1. Wählen Sie im linken Bereich die Option **Benutzer**. 
1. Klicken Sie auf **Über Gruppe synchronisieren**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Hinzufügen von Benutzern aus einer Azure AD-Gruppe durch Synchronisieren":::
    
1. Sie werden aufgefordert, eine vorhandene Azure AD-Gruppe auszuwählen, mit der ihr Lab synchronisiert werden soll. 
    
    Wenn keine Azure AD-Gruppe in der Liste angezeigt wird, kommen folgende Gründe infrage:

    -   Sie sind ein Gastbenutzer einer Azure Active Directory-Instanz (trifft in der Regel zu, wenn Sie sich außerhalb der Organisation befinden, die die Azure AD-Instanz besitzt) und können nicht innerhalb der Azure AD-Instanz nach Gruppen suchen. In diesem Fall können Sie dem Lab keine Azure AD-Gruppe hinzufügen. 
    -   Über Teams erstellte Azure AD-Gruppen werden in dieser Liste nicht angezeigt. Sie können die Azure Lab Services-App innerhalb von Teams hinzufügen, um Labs direkt darin zu erstellen und zu verwalten. Weitere Informationen finden Sie unter [Verwalten von Lab Services-Benutzerlisten aus Teams](how-to-manage-user-lists-within-teams.md). 
1. Nachdem Sie die Azure AD-Gruppe ausgewählt haben, mit der ihr Lab synchronisiert werden soll, klicken Sie auf **Hinzufügen**.
1. Sobald ein Lab synchronisiert ist, werden alle Benutzer innerhalb der Azure AD-Gruppe als Benutzer in das Lab gepullt, und die Benutzerliste wird aktualisiert. Nur die Personen in dieser Azure AD-Gruppe haben Zugriff auf das Lab. Die Benutzerliste wird alle 24 Stunden aktualisiert, damit sie der aktuellen Mitgliedschaft der Azure AD-Gruppe entspricht. Sie können auch auf der Registerkarte „Benutzer“ auf die Schaltfläche „Synchronisieren“ klicken, um die neuesten Änderungen in der Azure AD-Gruppe manuell zu synchronisieren.
1. Laden Sie die Benutzer in Ihr Lab ein, indem Sie auf die Schaltfläche **Alle einladen** klicken, sodass an alle Benutzer eine E-Mail mit dem Link zur Registrierung beim Lab gesendet wird. 

### <a name="automatic-management-of-virtual-machines-based-on-changes-to-the-azure-ad-group"></a>Automatische Verwaltung virtueller Computer basierend auf Änderungen an der Azure AD-Gruppe 

Nachdem das Lab mit einer Azure AD-Gruppe synchronisiert wurde, entspricht die Anzahl der virtuellen Computer im Lab automatisch der Anzahl der Benutzer in der Gruppe. Sie sind dann nicht mehr in der Lage, die Lab-Kapazität manuell zu aktualisieren. Wenn ein Benutzer der Azure AD-Gruppe hinzugefügt wird, fügt ein Lab automatisch einen virtuellen Computer für diesen Benutzer hinzu. Wenn ein Benutzer aus der Azure AD-Gruppe gelöscht wird, löscht ein Lab automatisch den virtuellen Computer dieses Benutzers aus dem Lab. 

## <a name="add-users-manually-from-emails-or-csv-file"></a>Manuelles Hinzufügen von Benutzern aus E-Mails oder CSV-Dateien

In diesem Abschnitt fügen Sie manuell (per E-Mail-Adresse oder durch Hochladen einer CSV-Datei) Kursteilnehmer hinzu. 

### <a name="add-users-by-email-address"></a>Hinzufügen von Benutzern per E-Mail-Adresse

1. Wählen Sie im linken Bereich die Option **Benutzer**. 
1. Klicken Sie auf **Benutzer manuell hinzufügen**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-manually.png" alt-text="Benutzer manuell hinzufügen":::
1. Wählen Sie **Per E-Mail-Adresse hinzufügen** (Standard) aus, und geben Sie die E-Mail-Adressen der Kursteilnehmer in separaten Zeilen oder durch Semikolons getrennt in einer einzelnen Zeile ein. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-email-addresses.png" alt-text="Hinzufügen der E-Mail-Adressen von Benutzern":::
1. Wählen Sie **Speichern** aus. 

    In der Liste werden die E-Mail-Adressen und Status der aktuellen Benutzer angezeigt, und zwar unabhängig davon, ob Sie beim Lab registriert sind oder nicht. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Benutzerliste":::

    > [!NOTE]
    > Nachdem die Kursteilnehmer beim Lab registriert wurden, werden ihre Namen in der Liste angezeigt. Der in der Liste angezeigte Name wird aus Vor- und Nachnamen der Kursteilnehmer in Azure Active Directory zusammengesetzt. 

### <a name="add-users-by-uploading-a-csv-file"></a>Hinzufügen von Benutzern durch Hochladen einer CSV-Datei

Sie können auch eine CSV-Datei mit E-Mail-Adressen von Benutzern hochladen, um Benutzer hinzuzufügen. 

Eine CSV-Textdatei wird zum Speichern von durch Trennzeichen getrennten tabellarischen Daten (Zahlen und Text) verwendet. Anstatt Informationen in Spaltenfeldern (z. B. in Tabellenkalkulationen) zu speichern, speichert eine CSV-Datei Informationen, die durch Trennzeichen voneinander getrennt sind. Jede Zeile in einer CSV-Datei verfügt über die gleiche Anzahl von durch Trennzeichen getrennten „Feldern“. Sie können Excel verwenden, um auf einfache Weise CSV-Dateien zu erstellen und zu bearbeiten.

1. Erstellen Sie in Microsoft Excel eine CSV-Datei, die die E-Mail-Adressen der Kursteilnehmer in einer Spalte auflistet.

    :::image type="content" source="./media/how-to-configure-student-usage/csv-file-with-users.png" alt-text="Liste der Benutzer in einer CSV-Datei":::
1. Wählen Sie oben im Bereich **Benutzer** die Option **Benutzer hinzufügen** und anschließend **CSV hochladen** aus.
1. Wählen Sie die CSV-Datei aus, die die E-Mail-Adressen der Kursteilnehmer enthält, und wählen Sie dann **Öffnen** aus.

    Im Fenster **Benutzer hinzufügen** wird die Liste der E-Mail-Adressen aus der CSV-Datei angezeigt. 
1. Wählen Sie **Speichern** aus. 
1. Zeigen Sie im Bereich **Benutzer** die Liste der hinzugefügten Kursteilnehmer an. 

    :::image type="content" source="./media/how-to-configure-student-usage/list-of-added-users.png" alt-text="Liste der hinzugefügten Benutzer im Bereich „Benutzer“":::

## <a name="send-invitations-to-users"></a>Senden von Einladungen an Benutzer

Um einen Registrierungslink an neue Benutzer zu senden, verwenden Sie eine der folgenden Methoden. 

Wenn die Option **Zugriff einschränken** für das Lab aktiviert ist, können sich nur in der Liste enthaltene Benutzer über den Registrierungslink beim Lab registrieren. Diese Option ist standardmäßig aktiviert. 

### <a name="invite-all-users"></a>Einladen aller Benutzer

Diese Methode zeigt, wie Sie E-Mails mit dem Registrierungslink und einer optionalen Nachricht an alle aufgelisteten Kursteilnehmer senden können.

1. Wählen Sie im Bereich **Benutzer** die Option **Alle einladen** aus. 

    ![Schaltfläche „Alle einladen“](./media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Geben Sie im Fenster **Einladung per E-Mail senden** eine optionale Nachricht ein, und wählen Sie anschließend die Option **Senden** aus. 

    Die E-Mail enthält automatisch den Registrierungslink. Zum separaten Aktivieren und Speichern des Registrierungslinks wählen Sie die Auslassungspunkte ( **...** ) am oberen Rand des Bereichs **Benutzer** aus, und wählen Sie dann **Registrierungslink** aus. 

    ![Fenster „Registrierungslink per E-Mail senden“](./media/tutorial-setup-classroom-lab/send-email.png)

    In der Spalte **Einladung** der Liste **Benutzer** wird der Einladungsstatus für jeden hinzugefügten Benutzer angezeigt. Der Status sollte sich in **Wird gesendet** und dann in **Gesendet am \<date>** ändern. 

### <a name="invite-selected-users"></a>Einladen ausgewählter Benutzer

Diese Methode zeigt Ihnen, wie Sie nur bestimmte Kursteilnehmer einladen und einen Registrierungslink abrufen, den Sie für andere Personen freigeben können.

1. Wählen Sie im Bereich **Benutzer** einen Kursteilnehmer oder mehrere Kursteilnehmer in der Liste aus. 

1. Wählen Sie in der Zeile für den ausgewählten Kursteilnehmer das **Briefumschlagsymbol** aus, oder wählen Sie auf der Symbolleiste **Einladen** aus. 

    ![Einladen ausgewählter Benutzer](./media/how-to-configure-student-usage/invite-selected-users.png)

1. Geben Sie im Fenster **Send invitation by email** (Einladung per E-Mail senden) eine optionale **Nachricht** ein, und wählen Sie anschließend die Option **Senden** aus. 

    ![Senden einer E-Mail an ausgewählte Benutzer](./media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Im Bereich **Benutzer** wird der Status dieses Vorgangs in der Spalte **Einladung** der Tabelle angezeigt. Die Einladungs-E-Mail enthält den Registrierungslink, über den sich Kursteilnehmer beim Lab registrieren können.

## <a name="get-the-registration-link"></a>Abrufen des Registrierungslinks

In diesem Abschnitt können Sie den Registrierungslink über das Portal abrufen und mithilfe Ihrer eigenen E-Mail-Anwendung senden. 

1. Wählen Sie im Bereich **Benutzer** die Option **Registrierungslink** aus.

    ![Registrierungslink für Teilnehmer](./media/how-to-configure-student-usage/registration-link-button.png)

1. Wählen Sie im Fenster **Benutzerregistrierung** die Option **Kopieren** aus, und klicken Sie dann auf **Fertig**. 

    ![Fenster „Benutzerregistrierung“](./media/how-to-configure-student-usage/registration-link.png)

    Der Link wird in die Zwischenablage kopiert. 
    
1. Fügen Sie in Ihrer E-Mail-Anwendung den Registrierungslink ein, und senden Sie die E-Mail an einen Kursteilnehmer, damit sich der Kursteilnehmer für den Kurs registrieren kann. 

## <a name="view-registered-users"></a>Anzeigen registrierter Benutzer

1. Navigieren Sie zur Website [Azure Lab Services](https://labs.azure.com). 
1. Wählen Sie **Anmelden** aus, und geben Sie dann Ihre Anmeldeinformationen ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten.
1. Wählen Sie auf der Seite **Meine Labs** das Lab aus, dessen Nutzung Sie nachverfolgen möchten. 
1. Wählen Sie im linken Bereich **Benutzer** aus, oder wählen Sie die Kachel **Benutzer** aus. 

    Im Bereich **Benutzer** wird eine Liste von Kursteilnehmern angezeigt, die sich bei Ihrem Lab registriert haben.  

    ![Liste der registrierten Benutzer](./media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Festlegen von Kontingenten für Benutzer

Sie können ein Stundenkontingent für jeden Kursteilnehmer festlegen, indem Sie folgendermaßen vorgehen: 

1. Wählen Sie im Bereich **Benutzer** auf der Symbolleiste die Option **Kontingent pro Benutzer: \<number> Stunden** aus. 
1. Geben Sie im Fenster **Kontingent pro Benutzer** die Anzahl der Stunden an, die Sie jedem Kursteilnehmer außerhalb der planmäßigen Kursstunden zuteilen möchten, und wählen Sie dann **Speichern** aus.

    ![Fenster „Kontingent pro Benutzer“](./media/how-to-configure-student-usage/quota-per-user.png)    

    Die geänderten Werte werden jetzt auf der Schaltfläche **Kontingent pro Benutzer: \<number of hours>** auf der Symbolleiste und in der Benutzerliste angezeigt, wie hier gezeigt:

    ![Kontingent Stunden pro Benutzer](./media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > Die [geplante Ausführungszeit von VMs](how-to-create-schedules.md) wird nicht mit dem Kontingent eines Kursteilnehmers verrechnet. Das Kontingent gilt für die Zeiten außerhalb der geplanten Stunden, die ein Kursteilnehmer mit VMs verbringt. 

## <a name="set-additional-quotas-for-specific-users"></a>Festlegen eines zusätzlichen Kontingents für bestimmte Benutzer

Sie können Kontingente für bestimmte Kursteilnehmer angeben, die über die allgemeinen Kontingente hinausgehen, die für alle Benutzer im vorherigen Abschnitt festgelegt wurden. Wenn Sie beispielsweise als Dozent das Kontingent für alle Kursteilnehmer auf 10 Stunden und ein zusätzliches Kontingent von 5 Stunden für einen bestimmten Kursteilnehmer festlegen, erhält dieser Kursteilnehmer ein Kontingent von 15 (10 + 5) Stunden. Wenn Sie das allgemeine Kontingent später z. B. in den Wert 15 ändern, erhält der Kursteilnehmer ein Kontingent von 20 (15 + 5) Stunden. Beachten Sie, dass dieses Gesamtkontingent außerhalb der geplanten Zeit liegt. Die Zeit, die ein Kursteilnehmer während der planmäßigen Kursstunden an einer Lab-VM verbringt, wird nicht auf dieses Kontingent angerechnet. 

Gehen Sie folgendermaßen vor, um zusätzliche Kontingente festzulegen:

1. Wählen Sie im Bereich **Benutzer** einen Kursteilnehmer aus der Liste aus, und wählen Sie dann auf der Symbolleiste **Kontingent anpassen** aus. 

    ![Schaltfläche „Kontingent anpassen“](./media/how-to-configure-student-usage/adjust-quota-button.png)

1. Geben Sie unter **Kontingent anpassen für \<selected user or users email address>** die Anzahl zusätzlicher Lab-Stunden ein, die Sie den ausgewählten Kursteilnehmern gewähren möchten, und wählen Sie dann **Anwenden** aus. 

    ![Fenster „Kontingent anpassen“](./media/how-to-configure-student-usage/additional-quota.png)

    In der Spalte **Nutzung** wird das aktualisierte Kontingent für die ausgewählten Kursteilnehmer angezeigt. 

    ![Neue Nutzung für den Benutzer](./media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Konten für Kursteilnehmer

Zum Hinzufügen von Kursteilnehmern zu einem Classroom-Lab verwenden Sie deren E-Mail-Konten. Kursteilnehmer können die folgenden Arten von E-Mail-Konten verwenden:

- Ein E-Mail-Konto für Kursteilnehmer, das von der Azure Active Directory-Instanz Ihrer Universität bereitgestellt wird.
- Ein Microsoft-Domänen-E-Mail-Konto, z. B. *outlook.com*, *hotmail.com*, *msn.com* oder *live.com*.
- Ein Microsoft-fremdes E-Mail-Konto, z. B. ein von Yahoo! oder Google bereitgestelltes Konto. Diese Kontotypen müssen jedoch mit einem Microsoft-Konto verknüpft werden.
- Ein GitHub-Konto. Dieses Konto muss mit einem Microsoft-Konto verknüpft werden.

### <a name="use-a-non-microsoft-email-account"></a>Verwenden eines Microsoft-fremden E-Mail-Kontos

Kursteilnehmer können Microsoft-fremde E-Mail-Konten verwenden, um sich für ein Classroom-Lab zu registrieren und sich bei diesem anzumelden.  Die Registrierung erfordert jedoch, dass sie zunächst ein Microsoft-Konto erstellen, das mit ihrer Microsoft-fremden E-Mail-Adresse verknüpft ist.

Viele Kursteilnehmer verfügen möglicherweise bereits über ein Microsoft-Konto, das mit ihrer Microsoft-fremden E-Mail-Adresse verknüpft ist. Beispielsweise verfügen Kursteilnehmer bereits über ein Microsoft-Konto, wenn sie ihre E-Mail-Adresse mit anderen Microsoft-Produkten oder -Diensten wie Office, Skype, OneDrive oder Windows verwendet haben.  

Wenn Kursteilnehmer den Registrierungslink verwenden, um sich bei einem Classroom anzumelden, werden sie aufgefordert, ihre E-Mail-Adresse und ihr Kennwort einzugeben. Kursteilnehmer, die versuchen, sich mit einem Microsoft-fremden Konto anzumelden, das nicht mit einem Microsoft-Konto verknüpft ist, erhalten die folgende Fehlermeldung: 

![Fehlermeldung bei der Anmeldung](./media/how-to-configure-student-usage/cant-find-account.png)

Hier ist ein Link für Kursteilnehmer, um sich [für ein Microsoft-Konto zu registrieren](http://signup.live.com).  

> [!IMPORTANT]
> Wenn sich Kursteilnehmer bei einem Classroom-Lab anmelden, ist keine Option zum Erstellen eines Microsoft-Kontos verfügbar. Daher wird empfohlen, diesen Registrierungslink (http://signup.live.com ) in die Registrierungs-E-Mail für das Classroom-Lab einzufügen, die Sie an Kursteilnehmer senden, die Microsoft-fremde Konten verwenden.

### <a name="use-a-github-account"></a>Verwenden eines GitHub-Kontos

Kursteilnehmer können auch ein vorhandenes GitHub-Konto verwenden, um sich für ein Classroom-Lab zu registrieren und bei diesem anzumelden. Wenn Kursteilnehmer bereits über ein Microsoft-Konto verfügen, das mit ihrem GitHub-Konto verknüpft ist, können sie sich anmelden und ihr Kennwort angeben, wie im vorherigen Abschnitt gezeigt. 

Wenn sie ihr GitHub-Konto noch nicht mit einem Microsoft-Konto verknüpft haben, können sie folgendermaßen vorgehen:

1. Wählen Sie den Link **Anmeldeoptionen** aus, wie hier gezeigt:

    ![Link „Anmeldeoptionen“](./media/how-to-configure-student-usage/signin-options.png)

1. Wählen Sie im Fenster **Anmeldeoptionen** die Option **Mit GitHub anmelden** aus.

    ![Link „Mit GitHub anmelden“](./media/how-to-configure-student-usage/signin-github.png)

    An der Eingabeaufforderung erstellen die Kursteilnehmer dann ein Microsoft-Konto, das mit ihrem GitHub-Konto verknüpft ist. Die Verknüpfung erfolgt automatisch, wenn sie **Weiter** auswählen. Anschließend werden sie sofort angemeldet und mit dem Classroom-Lab verbunden.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Exportieren einer Liste von Benutzern in eine CSV-Datei

1. Navigieren Sie zum Bereich **Benutzer**.
1. Wählen Sie auf der Symbolleiste die Auslassungspunkte ( **...** ) und dann **CSV exportieren** aus. 

    ![Schaltfläche „CSV exportieren“](./media/how-to-export-users-virtual-machines-csv/users-export-csv.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln:

- Für Administratoren: [Erstellen und Verwalten von Lab-Konten](how-to-manage-lab-accounts.md)
- Für Lab-Besitzer: [Erstellen und Verwalten von Labs](how-to-manage-classroom-labs.md) und [Einrichten und Veröffentlichen von Vorlagen](how-to-create-manage-template.md)
- Für Lab-Benutzer: [Zugreifen auf Labs](how-to-use-classroom-lab.md)
