---
title: Verwalten von Labs in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Classroom-Lab erstellen, alle Labs anzeigen, den Registrierungslink für einen Lab-Benutzer freigeben oder ein Lab löschen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: db7a2f58f99252a7e5076dd86c37b65bbe8ea37a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434208"
---
# <a name="manage-labs-in-azure-lab-services"></a>Verwalten von Labs in Azure Lab Services 
In diesem Artikel erfahren Sie, wie Sie ein Classroom-Lab erstellen und löschen. Außerdem erfahren Sie, wie Sie alle Labs in einem Labkonto anzeigen. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Einrichten eines Classroom-Labs in einem Labkonto müssen Sie Mitglied der Rolle **Ersteller des Labs** für das Labkonto sein. Das zum Erstellen eines Lab-Kontos verwendete Konto wird dieser Rolle automatisch hinzugefügt. Ein Besitzer des Labs kann der Rolle „Ersteller des Labs“ weitere Benutzer hinzufügen, indem er die Schritte im folgenden Artikel ausführt: [Hinzufügen eines Benutzers zur Rolle „Ersteller des Labs“](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Erstellen eines Classroom-Labs

1. Navigieren Sie zur Website [Azure Lab Services](https://labs.azure.com). Internet Explorer 11 wird noch nicht unterstützt. 
1. Wählen Sie **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein. Wählen Sie eine **Benutzer-ID** aus (oder geben Sie eine Benutzer-ID ein), die der Rolle **Lab-Ersteller** im Labkonto angehört, und geben Sie das Kennwort ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. 
1. Wählen Sie **Neues Lab** aus. 
    
    ![Erstellen eines Classroom-Labs](./media/tutorial-setup-classroom-lab/new-lab-button.png)
1. Führen Sie im Fenster **Neues Lab** die folgenden Aktionen aus: 
    1. Geben Sie unter **Name** einen Namen für Ihr Lab an. 
    1. Wählen Sie die **Größe der virtuellen Computer** aus, die Sie für die Klasse benötigen. Die Liste der verfügbaren Größen finden Sie im Abschnitt [VM-Größen ](#vm-sizes). 
    1. Wählen Sie das **VM-Image** aus, das Sie für das Classroom-Lab verwenden möchten. Bei Auswahl eines Linux-Images wird eine Option zum **Aktivieren der Remotedesktopverbindung** angezeigt. Ausführliche Informationen finden Sie unter [Aktivieren der Remotedesktopverbindung für Linux](how-to-enable-remote-desktop-linux.md).

        Wenn Sie sich mit den Anmeldeinformationen für Labkontobesitzer angemeldet haben, wird eine Option angezeigt, mit der Sie weitere Images für das Lab aktivieren können. Weitere Informationen finden Sie unter [Aktivieren von Images zum Zeitpunkt der Laberstellung](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    1. Überprüfen Sie den **Gesamtpreis pro Stunde** , der auf der Seite angezeigt wird. 
    1. Wählen Sie **Speichern** aus.

        ![Screenshot: Fenster „Neues Lab“](./media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Wenn das Labkonto mit der Option [Auswahl des Labstandorts](allow-lab-creator-pick-lab-location.md) konfiguriert wurde, wird eine Option zum Auswählen eines Standorts für das Lab angezeigt. 
4. Geben Sie auf der Seite mit den **VM-Anmeldeinformationen** die Standardanmeldeinformationen für alle VMs im Lab an.
    1. Geben Sie den **Namen des Benutzers** für alle VMs im Lab an.
    2. Geben Sie das **Kennwort** für den Benutzer an. 

        > [!IMPORTANT]
        > Notieren Sie sich den Benutzernamen und das Kennwort. Diese Angaben werden nicht noch einmal angezeigt.
    3. Deaktivieren Sie die Option **Use same password for all virtual machines** (Dasselbe Kennwort für alle virtuellen Computer verwenden), wenn Sie möchten, dass die Kursteilnehmer ihre eigenen Kennwörter festlegen. Dieser Schritt ist **optional**. 

        Ein Lehrer/Dozent kann auch dasselbe Kennwort für alle VMs im Lab verwenden, oder den Kursteilnehmern erlauben, die Kennwörter für ihre virtuellen Computer selbst festzulegen. Standardmäßig ist diese Einstellung für alle Windows- und Linux-Images mit Ausnahme von Ubuntu aktiviert. Bei der Auswahl einer **Ubuntu**-VM ist diese Einstellung deaktiviert, sodass die Kursteilnehmer bei der ersten Anmeldung aufgefordert werden, ein Kennwort festzulegen.  

        ![Fenster „Neues Lab“](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Wählen Sie dann **Weiter** auf der Seite **VM-Anmeldeinformationen** aus. 
5. Führen Sie auf der Seite **Labrichtlinien** die folgenden Schritte aus:
    1. Geben Sie die Anzahl von Stunden ein, die jedem Benutzer außerhalb der geplanten Zeit für das Lab zugeteilt wird (**Kontingent pro Benutzer**). 
    2. Geben Sie für die Option **Richtlinie zum Herunterfahren** an, ob der virtuelle Computer automatisch heruntergefahren werden soll, wenn der Benutzer die Verbindung trennt. Sie können auch angeben, wie lange der virtuelle Computer vor dem automatischen Herunterfahren darauf warten soll, dass der Benutzer die Verbindung wiederherstellt. Weitere Informationen finden Sie unter [Aktivieren des automatischen Herunterfahrens von VMs beim Trennen](how-to-enable-shutdown-disconnect.md).
    3. Wählen Sie anschließend **Fertig stellen** aus. 

        ![Kontingent pro Benutzer](./media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Der folgende Bildschirm sollte angezeigt werden, auf dem der Status der VM-Vorlagenerstellung angegeben ist. Die Erstellung der Vorlage im Lab dauert bis zu 20 Minuten. 

    ![Status der VM-Vorlagenerstellung](./media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Führen Sie auf der Seite **Vorlage** die folgenden Schritte aus: Diese Schritte sind für das Tutorial **optional**.

    1. Stellen Sie eine Verbindung mit der Vorlage für virtuelle Computer her, indem Sie **Verbinden** wählen. Handelt es sich um einen virtuellen Computer mit Linux-Vorlage, legen Sie fest, ob eine Verbindung per SSH oder über einen Remotedesktop mit grafischer Benutzeroberfläche hergestellt werden soll.  Zur Verwendung eines Remotedesktops mit grafischer Benutzeroberfläche ist ein zusätzliches Setup erforderlich. Weitere Informationen finden Sie unter [Aktivieren eines Remotedesktops mit GUI für Linux-VMs](how-to-use-remote-desktop-linux-student.md).
    1. Klicken Sie auf **Kennwort zurücksetzen**, um das Kennwort für die VM zurückzusetzen. 
    1. Installieren und konfigurieren Sie die Software in der Vorlage für virtuelle Computer. 
    1. **Beenden** Sie den virtuellen Computer.  
    1. Geben Sie eine **Beschreibung** für die Vorlage ein.
9.  Wählen Sie auf der Seite **Vorlage** auf der Symbolleiste die Option **Veröffentlichen** aus. 

    ![Schaltfläche „Vorlage veröffentlichen“](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Nachdem die Veröffentlichung erfolgt ist, kann sie nicht mehr rückgängig gemacht werden. 
10. Geben Sie auf der Seite **Vorlage veröffentlichen** die Anzahl von virtuellen Computern ein, die Sie im Lab erstellen möchten, und wählen Sie anschließend die Option **Veröffentlichen** aus. 

    ![Vorlage veröffentlichen: Anzahl von VMs](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Auf der Seite wird der **Veröffentlichungsstatus** der Vorlage angezeigt. Dieser Vorgang kann bis zu einer Stunde dauern. 

    ![Veröffentlichen der Vorlage – Status](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Wechseln Sie zur Seite **VM-Pool**, indem Sie im linken Menü die Option „Virtuelle Computer“ bzw. die entsprechende Kachel auswählen. Vergewissern Sie sich, dass virtuelle Computer mit dem Status **Nicht zugewiesen** angezeigt werden. Diese virtuellen Computer sind noch keinen Teilnehmern zugewiesen. Sie sollten den Status **Beendet** aufweisen. Auf dieser Seite können Sie einen virtuellen Computer für einen Teilnehmer starten, eine Verbindung damit herstellen und ihn beenden und löschen. Sie können virtuelle Computer auf dieser Seite starten oder sie von Ihren Teilnehmern starten lassen. 

    ![Virtuelle Computer im Status „Beendet“](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Auf dieser Seite führen Sie die folgenden Aufgaben aus (Führen Sie diese Schritte nicht für das Tutorial aus. Sie dienen ausschließlich zu Informationszwecken.): 
    
    1. Wählen Sie in der Symbolleiste die Option **Lab capacity** (Labkapazität) aus, um sie zu ändern (Anzahl von VMs im Lab).
    2. Wählen Sie in der Symbolleiste **Start all** (Alle starten) aus, um alle VMs auf einmal zu starten. 
    3. Wählen Sie zum Starten einer bestimmten VM unter **Status** den Abwärtspfeil und dann die Option **Starten** aus. Sie können eine VM auch starten, indem Sie sie in der ersten Spalte auswählen und in der Symbolleiste dann die Option **Starten** auswählen.                

### <a name="vm-sizes"></a>VM-Größen  

| Size | Kerne | RAM | BESCHREIBUNG | 
| ---- | ----- | --- | ----------- | 
| Klein | 2 | 3,5 GB | Diese Größe eignet sich am besten für die Befehlszeile, das Öffnen von Webbrowsern, Webserver mit geringem Datenverkehr und kleine bis mittelgroße Datenbanken. |
| Medium | 4 | 7 GB | Diese Größe eignet sich am besten für relationale Datenbanken, speicherinternes Caching und Analysen. | 
| Mittel (geschachtelte Virtualisierung) | 4 | 16 GB | Diese Größe eignet sich am besten für relationale Datenbanken, speicherinternes Caching und Analysen. Sie unterstützt auch die geschachtelte Virtualisierung. <p>Diese Größe kann in Szenarien verwendet werden, in denen jeder Kursteilnehmer mehrere virtuelle Computer benötigt. Die Lehrer/Dozenten können die geschachtelte Virtualisierung nutzen, um einige kleine geschachtelte virtuelle Computer innerhalb des virtuellen Computers einzurichten. </p> |
| Kleine GPU (Compute) | 6 | 56 GB | <p>Diese Größe eignet sich am besten für rechenintensive und netzwerkintensive Anwendungen wie künstliche Intelligenz und Deep Learning-Anwendungen.</p><p>Azure Lab Services installiert und konfiguriert automatisch die erforderlichen GPU-Treiber, wenn Sie ein Lab mit GPU-Images erstellen. </p> | 
| Kleine GPU (Visualisierung) | 6 | 56 GB | Diese Größe eignet sich am besten für Remotevisualisierung, Streaming, Spiele und Codierung mit Frameworks wie OpenGL und DirectX. | 
| Groß | 8 | 16 GB | Diese Größe eignet sich am besten für Anwendungen, die schnellere CPUs, eine bessere lokale Datenträgerleistung, große Datenbanken und große Caches benötigen. |
| Groß (geschachtelte Virtualisierung) | 8 | 32 GB | Diese Größe eignet sich am besten für Anwendungen, die schnellere CPUs, eine bessere lokale Datenträgerleistung, große Datenbanken und große Caches benötigen. Sie unterstützt auch die geschachtelte Virtualisierung. |  
| Mittlere GPU (Visualisierung) | 12 | 112 GB | Diese Größe eignet sich am besten für Remotevisualisierung, Streaming, Spiele und Codierung mit Frameworks wie OpenGL und DirectX. | 

> [!NOTE]
> Beim Erstellen eines Classroom-Labs werden möglicherweise einige dieser VM-Größen nicht in der Liste angezeigt. Die Liste wird basierend auf der aktuellen Kapazität des Standorts des Labs aufgefüllt. Wenn der Ersteller des Lab-Kontos [Lab-Erstellern das Auswählen eines Standorts für das Lab gestattet](allow-lab-creator-pick-lab-location.md), können Sie versuchen, einen anderen Ort für das Lab auszuwählen, und dann nachsehen, ob die VM-Größe verfügbar ist. 

## <a name="view-all-labs"></a>Anzeigen aller Labs

1. Navigieren Sie zum [Azure Lab Services-Portal](https://labs.azure.com).
1. Wählen Sie **Anmelden**. Wählen Sie eine **Benutzer-ID** aus (oder geben Sie eine Benutzer-ID ein), die der Rolle **Lab-Ersteller** im Labkonto angehört, und geben Sie das Kennwort ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. 

    [!INCLUDE [Select a tenant](./includes/multi-tenant-support.md)]
1. Stellen Sie sicher, dass alle Labs im ausgewählten Lab-Konto angezeigt werden. Auf der Kachel des Labs sehen Sie die Anzahl der virtuellen Computer im Lab und das Kontingent für jeden Benutzer (außerhalb der geplanten Zeit).

    ![Alle Labs](./media/how-to-manage-classroom-labs/all-labs.png)
1. Verwenden Sie die Dropdownliste im oberen Bereich, um ein anderes Lab-Konto auszuwählen. Die Labs im ausgewählten Lab-Konto werden angezeigt. 

## <a name="delete-a-classroom-lab"></a>Löschen eines Classroom-Labs

1. Wählen Sie auf der Kachel für das Lab die drei Punkte (...) in der Ecke und dann **Löschen** aus. 

    ![Schaltfläche „Löschen“](./media/how-to-manage-classroom-labs/delete-button.png)
1. Wählen Sie im Dialogfeld **Lab löschen** den Befehl **Löschen** aus, um mit dem Löschvorgang fortzufahren. 

## <a name="switch-to-another-classroom-lab"></a>Zu einem anderen Classroom-Lab wechseln

Um aus dem aktuellen zu einem anderen Classroom-Lab zu wechseln, treffen Sie im Lab-Konto oben in der Dropdownliste mit den Labs eine Wahl.

![Wählen Sie das Lab in der Dropdownliste oben aus.](./media/how-to-manage-classroom-labs/switch-lab.png)

Sie können auch ein neues Lab erstellen, indem Sie in dieser Dropdownliste **Neues Lab** verwenden. 

> [!NOTE]
> Sie können auch das PowerShell-Modul Az.LabServices (Vorschauversion) verwenden, um Labs zu verwalten. Weitere Informationen finden Sie auf der [Az.LabServices-Startseite auf GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Um zu einem anderen Lab-Konto zu wechseln, wählen Sie die Dropdownliste neben dem Lab-Konto aus, und wählen Sie dann das andere Lab-Konto aus. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf Labs als Labbenutzer](how-to-use-classroom-lab.md)

