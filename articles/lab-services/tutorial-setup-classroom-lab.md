---
title: Einrichten eines Classroom-Labs in Azure Lab Services | Microsoft-Dokumentation
description: In diesem Tutorial richten Sie unter Verwendung von Azure Lab Services ein Classroom-Lab mit virtuellen Computern für Kursteilnehmer ein.
ms.topic: tutorial
ms.date: 12/03/2020
ms.openlocfilehash: 3abbf5221382b46dbf4e73f9f4dc3b639bc5ecbd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602495"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Tutorial: Einrichten eines Classroom-Labs 
In diesem Tutorial richten Sie ein Classroom-Lab mit virtuellen Computern ein, die von den Teilnehmern im Classroom verwendet werden.  

In diesem Tutorial führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
> * Erstellen eines Classroom-Labs
> * Hinzufügen von Benutzern zum Lab
> * Festlegen eines Zeitplans für das Lab
> * Senden einer Einladungs-E-Mail an Schüler/Studenten

## <a name="prerequisites"></a>Voraussetzungen
In diesem Tutorial richten Sie ein Lab mit virtuellen Computern für Ihre Klasse ein. Zum Einrichten eines Classroom-Labs in einem Labkonto müssen Sie Mitglied einer der folgenden Rollen für das Labkonto sein: „Besitzer“, „Ersteller des Labs“ oder „Mitwirkender“. Das zum Erstellen eines Labkontos verwendete Konto wird automatisch der Rolle „Besitzer“ hinzugefügt. Daher können Sie mit dem Benutzerkonto, das Sie zum Erstellen eines Labkontos verwendet haben, ein Classroom-Lab erstellen. 

Nachfolgend ist der typische Workflow für die Verwendung von Azure Lab Services beschrieben:

1. Der Ersteller eines Labkontos kann andere Benutzer zur Rolle **Ersteller des Labs** hinzufügen. Der Ersteller/Administrator des Labkontos fügt der Rolle **Ersteller des Labs** beispielsweise Lehrkräfte hinzu, damit diese Labs für ihre Kurse erstellen können. 
2. Anschließend erstellen die Lehrkräfte Labs mit virtuellen Computern für ihre Kurse und senden Registrierungslinks an die Kursteilnehmer. 
3. Kursteilnehmer verwenden zum Registrieren für das Lab den Registrierungslink, den sie von Lehrkräften erhalten. Nach der Registrierung können sie virtuelle Computer in den Labs für Unterrichtsarbeit und Hausaufgaben nutzen. 

## <a name="create-a-classroom-lab"></a>Erstellen eines Classroom-Labs
In diesem Schritt erstellen Sie ein Lab für Ihre Klasse in Azure. 

1. Navigieren Sie zur Website [Azure Lab Services](https://labs.azure.com). Beachten Sie den Hinweis, dass Internet Explorer 11 noch nicht unterstützt wird. 
2. Wählen Sie **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. 
3. Wählen Sie **Neues Lab** aus. 
    
    ![Screenshot, der „Azure Lab Services“ mit ausgewählter Schaltfläche „Neues Lab“ zeigt.](./media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Führen Sie im Fenster **Neues Lab** die folgenden Aktionen aus: 
    1. Geben Sie unter **Name** einen Namen für Ihr Lab ein, und wählen Sie **Weiter** aus.  

        ![Erstellen eines Classroom-Labs](./media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Geben Sie auf der Seite mit den **VM-Anmeldeinformationen** die Standardanmeldeinformationen für alle VMs im Lab an. Geben Sie **Name** und **Kennwort** für den Benutzer an, und wählen Sie anschließend **Weiter** aus.  

        ![Fenster „Neues Lab“](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Notieren Sie sich den Benutzernamen und das Kennwort. Diese Angaben werden nicht noch einmal angezeigt.
    3. Wählen Sie auf der Seite **Labrichtlinien** die Option **Fertig stellen** aus. 

        ![Kontingent pro Benutzer](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Der folgende Bildschirm sollte angezeigt werden, auf dem der Status der VM-Vorlagenerstellung angegeben ist. Dieser Vorgang dauert bis zu 20 Minuten. 

    ![Status der VM-Vorlagenerstellung](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Führen Sie auf der Seite **Vorlage** die folgenden Schritte aus: Diese Schritte sind für das Tutorial **optional**.

    1. Stellen Sie eine Verbindung mit der Vorlage für virtuelle Computer her, indem Sie **Verbinden** wählen. Handelt es sich um einen virtuellen Computer mit Linux-Vorlage, legen Sie fest, ob eine Verbindung per SSH oder RDP (sofern RDP aktiviert ist) hergestellt werden soll.
    3. Installieren und konfigurieren Sie die für Ihre Klasse erforderliche Software auf dem virtuellen Vorlagencomputer. 
    4. **Beenden** Sie den virtuellen Vorlagencomputer.  

    > [!NOTE]
    > Bei Vorlagen-VMs entstehen **Kosten**, wenn sie ausgeführt werden. Stellen Sie daher sicher, dass die Vorlagen-VM heruntergefahren wird, wenn sie nicht ausgeführt werden muss. 

## <a name="publish-the-template-vm"></a>Veröffentlichen der Vorlage für virtuelle Computer
In diesem Schritt veröffentlichen Sie den virtuellen Vorlagencomputer. Wenn Sie den virtuellen Vorlagencomputer veröffentlichen, erstellt Azure Lab Services im Lab mithilfe der Vorlage virtuelle Computer. Alle virtuellen Computer haben die gleiche Konfiguration wie die Vorlage.

1. Wählen Sie auf der Seite **Vorlage** auf der Symbolleiste die Option **Veröffentlichen** aus. 

    ![Schaltfläche „Vorlage veröffentlichen“](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nachdem die Veröffentlichung erfolgt ist, kann sie nicht mehr rückgängig gemacht werden. 
2. Geben Sie auf der Seite **Vorlage veröffentlichen** die Anzahl von virtuellen Computern ein, die Sie im Lab erstellen möchten, und wählen Sie anschließend die Option **Veröffentlichen** aus. 

    ![Vorlage veröffentlichen: Anzahl von VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Auf der Seite wird der **Veröffentlichungsstatus** der Vorlage angezeigt. Dieser Vorgang kann bis zu einer Stunde dauern. 

    ![Veröffentlichen der Vorlage – Status](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Warten Sie, bis die Veröffentlichung abgeschlossen ist, und navigieren Sie zur Seite **VM-Pool**, indem Sie die Option **Virtuelle Computer** im linken Menü bzw. die Kachel **Virtuelle Computer** auswählen. Vergewissern Sie sich, dass virtuelle Computer mit dem Status **Nicht zugewiesen** angezeigt werden. Diese virtuellen Computer sind noch keinen Teilnehmern zugewiesen. Sie sollten den Status **Beendet** aufweisen. Auf dieser Seite können Sie einen virtuellen Computer für einen Teilnehmer starten, eine Verbindung damit herstellen und ihn beenden und löschen. Sie können virtuelle Computer auf dieser Seite starten oder sie von Ihren Teilnehmern starten lassen. 

    ![Virtuelle Computer im Status „Beendet“](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Wenn ein Lehrer/Dozent eine Kursteilnehmer-VM aktiviert, hat dies keine Auswirkung auf das Kontingent für den Kursteilnehmer. Das Kontingent für einen Benutzer gibt die Anzahl von Labstunden an, die für den Benutzer außerhalb der geplanten Kurszeit verfügbar sind. Weitere Informationen zu Kontingenten finden Sie unter [Festlegen von Kontingenten für Benutzer](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="set-a-schedule-for-the-lab"></a>Festlegen eines Zeitplans für das Lab
Erstellen Sie ein geplantes Ereignis für das Lab, damit VMs im Lab zu bestimmten Zeiten automatisch gestartet und beendet werden. Das von Ihnen zuvor angegebene Benutzerkontingent (Standard: zehn Stunden) ist die zusätzliche Zeit, die jedem Benutzer außerhalb dieser geplanten Zeit zugewiesen ist. 

1. Wechseln Sie zur Seite **Zeitpläne**, und wählen Sie in der Symbolleiste die Option **Add scheduled event** (Geplantes Ereignis hinzufügen) aus. 

    ![Screenshot, der die Schaltfläche „Geplantes Ereignis hinzufügen“ auf der Seite „Zeitpläne“ zeigt.](./media/how-to-create-schedules/add-schedule-button.png)
2. Führen Sie auf der Seite **Add scheduled event** (Geplantes Ereignis hinzufügen) die folgenden Schritte aus:
    1. Vergewissern Sie sich, dass unter **Ereignistyp** die Option **Standard** ausgewählt ist.  
    2. Wählen Sie das **Startdatum** für die Klasse aus. 
    4. Wählen Sie die **Startzeit** aus, zu der die virtuellen Computer gestartet werden sollen.
    5. Wählen Sie die **Endzeit** aus, zu der die virtuellen Computer heruntergefahren werden sollen. 
    6. Wählen Sie die **Zeitzone** für die von Ihnen angegebene Start- und Endzeit aus. 
3. Wählen Sie auf der gleichen Seite (**Add scheduled event** (Geplantes Ereignis hinzufügen)) im Abschnitt **Wiederholen** den aktuellen Zeitplan aus.  

    ![Schaltfläche „Zeitplan hinzufügen“ auf der Seite „Zeitpläne“](./media/how-to-create-schedules/select-current-schedule.png)
5. Führen Sie im Dialogfeld **Wiederholen** die folgenden Schritte aus:
    1. Vergewissern Sie sich, dass für das Feld **Wiederholen** die Option **Wöchentlich** festgelegt ist. 
    2. Wählen Sie die Tage aus, an denen der Zeitplan gelten soll. Im folgenden Beispiel sind Montag bis Freitag ausgewählt. 
    3. Wählen Sie ein **Enddatum** für den Zeitplan aus.
    8. Wählen Sie **Speichern** aus. 

        ![Festlegen des Zeitplans für die Wiederholung](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Geben Sie nun auf der Seite **Add scheduled event** (Geplantes Ereignis hinzufügen) unter **Hinweise (optional)** ein beliebige Beschreibung bzw. Hinweise zum Zeitplan ein. 
4. Wählen Sie auf der Seite **Add scheduled event** (Geplantes Ereignis hinzufügen) die Option **Speichern** aus. 

    ![Wöchentlicher Zeitplan](./media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navigieren Sie im Kalender zum Startdatum, um sich zu vergewissern, dass der Zeitplan festgelegt wurde.
    
    ![Zeitplans im Kalender](./media/how-to-create-schedules/schedule-calendar.png)

    Weitere Informationen zum Erstellen und Verwalten von Zeitplänen für eine Klasse finden Sie unter [Erstellen und Verwalten von Zeitplänen für Classroom-Labs in Azure Lab Services](how-to-create-schedules.md).


## <a name="add-users-to-the-lab"></a>Hinzufügen von Benutzern zum Lab

Wenn Sie Benutzer hinzufügen, ist die Option **Zugriff einschränken** standardmäßig aktiviert. Wenn sie nicht in der Benutzerliste enthalten sind, können sich Kursteilnehmer daher selbst dann nicht beim Labor registrieren, wenn sie über einen Registrierungslink verfügen. Nur in der Liste enthaltene Benutzer können sich über den von Ihnen gesendeten Registrierungslink beim Lab registrieren. Sie können **Zugriff beschränken** deaktivieren. In diesem Fall können sich Kursteilnehmer beim Lab registrieren, wenn sie über den Registrierungslink verfügen. 

### <a name="add-users-from-an-azure-ad-group"></a>Hinzufügen von Benutzern aus einer Azure AD-Gruppe

Sie können eine Labbenutzerliste mit einer vorhandenen Azure AD-Gruppe (Azure Active Directory) synchronisieren, damit Sie Benutzer nicht manuell hinzufügen oder löschen müssen. 

In der Azure Active Directory-Instanz Ihrer Organisation kann eine Azure AD-Gruppe erstellt werden, um den Zugriff auf Organisationsressourcen und cloudbasierte Apps zu verwalten. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Apps und Ressourcen mithilfe von Azure Active Directory-Gruppen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups). Wenn in Ihrer Organisation Microsoft Office 365- oder Azure-Dienste verwendet werden, verfügt Ihre Organisation bereits über Administratoren, die Ihre Azure Active Directory-Instanz verwalten. 

> [!IMPORTANT]
> Stellen Sie sicher, dass die Benutzerliste leer ist. Wenn in einem Lab Benutzer vorhanden sind, die Sie manuell oder durch Importieren einer CSV-Datei hinzugefügt haben, wird die Option zum Synchronisieren des Labs mit einer vorhandenen Gruppe nicht angezeigt. 

1. Wählen Sie im linken Bereich die Option **Benutzer**. 
1. Klicken Sie auf **Über Gruppe synchronisieren**. 

    :::image type="content" source="./media/how-to-configure-student-usage/add-users-sync-group.png" alt-text="Hinzufügen von Benutzern aus einer Azure AD-Gruppe durch Synchronisieren":::
    
1. Sie werden aufgefordert, eine vorhandene Azure AD-Gruppe auszuwählen, mit der ihr Lab synchronisiert werden soll. 
    
    Wird keine Azure AD-Gruppe in der Liste angezeigt, kann dies folgende Gründe haben:

    -   Sie sind ein Gastbenutzer einer Azure Active Directory-Instanz (trifft in der Regel zu, wenn Sie sich außerhalb der Organisation befinden, die die Azure AD-Instanz besitzt) und können nicht innerhalb der Azure AD-Instanz nach Gruppen suchen. In diesem Fall können Sie dem Lab keine Azure AD-Gruppe hinzufügen. 
    -   Über Teams erstellte Azure AD-Gruppen werden in dieser Liste nicht angezeigt. Sie können die Azure Lab Services-App innerhalb von Teams hinzufügen, um Labs direkt darin zu erstellen und zu verwalten. Weitere Informationen finden Sie unter [Verwalten von Lab Services-Benutzerlisten aus Teams](how-to-manage-user-lists-within-teams.md). 
1. Nachdem Sie die Azure AD-Gruppe ausgewählt haben, mit der ihr Lab synchronisiert werden soll, klicken Sie auf **Hinzufügen**.
1. Sobald ein Lab synchronisiert ist, werden alle Benutzer innerhalb der Azure AD-Gruppe als Benutzer in das Lab gepullt, und die Benutzerliste wird aktualisiert. Nur die Personen in dieser Azure AD-Gruppe haben Zugriff auf das Lab. Die Benutzerliste wird alle 24 Stunden aktualisiert, damit sie der aktuellen Mitgliedschaft der Azure AD-Gruppe entspricht. Sie können auch auf der Registerkarte „Benutzer“ auf die Schaltfläche „Synchronisieren“ klicken, um die neuesten Änderungen in der Azure AD-Gruppe manuell zu synchronisieren.
1. Laden Sie die Benutzer in Ihr Lab ein, indem Sie auf die Schaltfläche **Alle einladen** klicken, sodass an alle Benutzer eine E-Mail mit dem Link zur Registrierung beim Lab gesendet wird. 

### <a name="add-users-manually-from-emails-or-csv-file"></a>Manuelles Hinzufügen von Benutzern aus E-Mails oder CSV-Dateien

In diesem Abschnitt fügen Sie manuell (per E-Mail-Adresse oder durch Hochladen einer CSV-Datei) Kursteilnehmer hinzu. 

#### <a name="add-users-by-email-address"></a>Hinzufügen von Benutzern per E-Mail-Adresse

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

#### <a name="add-users-by-uploading-a-csv-file"></a>Hinzufügen von Benutzern durch Hochladen einer CSV-Datei

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

## <a name="send-invitation-emails-to-users"></a>Senden von Einladungs-E-Mails an Benutzer

1. Wechseln Sie zur Ansicht **Benutzer**, falls Sie sich nicht bereits auf der Seite befinden, und wählen Sie in der Symbolleiste die Option **Invite all** (Alle einladen) aus. 

    ![Auswählen von Kursteilnehmern](./media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Geben Sie auf der Seite **Send invitation by email** (Einladung per E-Mail senden) eine optionale Nachricht ein, und wählen Sie anschließend die Option **Senden** aus. Die E-Mail enthält automatisch den Registrierungslink. Sie erhalten diesen Registrierungslink, indem Sie in der Symbolleiste die Option **...** (Auslassungszeichen) und dann **Registrierungslink** auswählen. 

    ![Senden eines Registrierungslinks per E-Mail](./media/tutorial-setup-classroom-lab/send-email.png)
4. Der Status der **Einladung** wird in der Liste **Benutzer** angezeigt. Der Status sollte sich in **Wird gesendet** und dann in **Gesendet am &lt;Datum&gt;** ändern. 

Weitere Informationen zum Hinzufügen von Kursteilnehmern zu einer Klasse sowie zum Verwalten der Verwendung des Labs finden Sie unter [Hinzufügen und Verwalten von Labbenutzern](how-to-configure-student-usage.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie ein Lab für Ihre Klasse in Azure erstellt. Um zu erfahren, wie ein Teilnehmer auf einen virtuellen Computer im Labor über den Registrierungslink zugreifen kann, fahren Sie mit nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit einem virtuellen Computer im Classroom-Lab](tutorial-connect-virtual-machine-classroom-lab.md)

