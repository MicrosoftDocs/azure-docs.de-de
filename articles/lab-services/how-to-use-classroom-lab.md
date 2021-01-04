---
title: Zugreifen auf ein Classroom-Lab in Azure Lab Services | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie sich bei einem Classroom-Lab registrieren, alle Labs anzeigen, auf die Sie zugreifen können, einen virtuellen Computer im Lab starten/beenden und eine Verbindung mit dem virtuellen Computer herstellen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6d5585082473a363df5f25f331475536544ae828
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433137"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Zugreifen auf ein Classroom-Lab in Azure Lab Services
In diesem Artikel wird beschrieben, wie Sie sich bei einem Classroom-Lab registrieren, alle Labs anzeigen, auf die Sie zugreifen können, einen virtuellen Computer im Lab starten/beenden und eine Verbindung mit dem virtuellen Computer herstellen. 

## <a name="register-to-the-lab"></a>Registrieren beim Lab

1. Navigieren Sie zur **Registrierungs-URL**, die Sie von dem Lehrer/Dozenten erhalten haben. Die Registrierungs-URL muss nach Abschluss der Registrierung nicht mehr verwendet werden. Verwenden Sie stattdessen die folgende URL: [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 wird noch nicht unterstützt. 

    ![Registrieren beim Lab](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Melden Sie sich mit Ihrem Geschäfts-, Schul- oder Unikonto beim Dienst an, um die Registrierung abzuschließen. 

    > [!NOTE]
    > Für die Verwendung von Azure Lab Services ist ein Microsoft-Konto erforderlich. Wenn Sie ein nicht von Microsoft stammendes Konto (beispielsweise ein Yahoo- oder Google-Konto) für die Anmeldung beim Portal verwenden möchten, befolgen Sie die Anweisungen zum Erstellen eines Microsoft-Kontos, das mit Ihrem Nicht-Microsoft-Konto verknüpft wird. Führen Sie anschließend die Schritte zum Abschließen des Registrierungsprozesses aus. 
1. Vergewissern Sie sich nach der Registrierung, dass Sie den virtuellen Computer für das Lab sehen, auf das Sie Zugriff haben. 

    ![Zugängliche VMs](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Warten Sie, bis der virtuelle Computer bereit ist. Beachten Sie auf der VM-Kachel die folgenden Felder:
    1. Im oberen Kachelbereich wird der **Name des Labs** angezeigt.
    1. Auf der rechten Seite wird das Symbol angezeigt, das für das **Betriebssystem** des virtuellen Computers steht. In diesem Beispiel ist es das Windows-Betriebssystem. 
    1. Im unteren Kachelbereich befinden sich Symbole und Schaltflächen, über die Sie den virtuellen Computer starten und beenden sowie eine Verbindung mit dem virtuellen Computer herstellen können. 
    1. Rechts von den Schaltflächen wird der Status des virtuellen Computers angezeigt. Vergewissern Sie sich, dass der Status des virtuellen Computers **Beendet** lautet.

        ![Virtueller Computer im Status „Beendet“](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Starten oder Beenden des virtuellen Computers
1. **Starten** Sie den virtuellen Computer, indem Sie die erste Schaltfläche auswählen, wie in der folgenden Abbildung gezeigt. Dieser Prozess nimmt einige Zeit in Anspruch.  

    ![Starten der VM](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Vergewissern Sie sich, dass der Status des virtuellen Computers **Wird ausgeführt** lautet. 

    ![Ausgeführter virtueller Computer](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Beachten Sie, dass das Symbol der ersten Schaltfläche so geändert wurde, dass es nun einen Vorgang zum **Beenden** darstellt. Sie können diese Schaltfläche auswählen, um den virtuellen Computer zu beenden. 

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

1. Wählen Sie die zweite Schaltfläche aus wie in der folgenden Abbildung gezeigt, um eine **Verbindung** mit dem virtuellen Computer des Labs herzustellen. 

    ![Herstellen einer Verbindung mit dem virtuellen Computer](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Führen Sie einen der folgenden Schritte aus: 
    1. Speichern Sie für virtuelle **Windows**-Computer die **RDP**-Datei auf der Festplatte. Öffnen Sie die RDP-Datei, um eine Verbindung mit dem virtuellen Computer herzustellen. Verwenden Sie die Kombination aus **Benutzername** und **Kennwort**, die Sie von Ihrem Lehrer/Dozenten erhalten haben, um sich bei dem Computer anzumelden. 
    3. Bei virtuellen **Linux**-Computern können Sie **SSH** oder **RDP** (sofern RDP aktiviert ist) für die Verbindungsherstellung verwenden. Weitere Informationen finden Sie unter [Enable and use remote desktop for Linux virtual machines in a lab in Azure Lab Services](how-to-enable-remote-desktop-linux.md) (Aktivieren und Verwenden von Remotedesktop für virtuelle Linux-Computer in einem Lab in Azure Lab Services). 
    1. Wenn Sie einen **Mac** verwenden, um eine Verbindung mit dem virtuellen Lab-Computer herzustellen, führen Sie die im nächsten Abschnitt beschriebenen Schritte aus. 

## <a name="progress-bar"></a>Statusanzeige 
Die Statusanzeige auf der Kachel gibt Aufschluss darüber, wie viele Stunden der Ihnen zugewiesenen [Kontingentstunden](how-to-configure-student-usage.md#set-quotas-for-users) bereits verwendet wurden. Diese Zeit wurde Ihnen zusätzlich zu der geplanten Zeit für das Lab zugewiesen. Die Farbe der Statusanzeige und der Text unterhalb der Statusanzeige variieren gemäß den folgenden Szenarien:

- Findet gerade ein Kurs (innerhalb des Kurszeitplans) statt, ist die Statusanzeige abgeblendet. Damit wird angegeben, dass keine Kontingentstunden verwendet werden. 

    ![Graue Statusanzeige](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Ist kein Kontingent zugewiesen (0 Stunden), wird anstelle der Statusanzeige der Text **Available during classes only** (Nur während Kursen verfügbar) angezeigt. 
    
    ![Status, wenn kein Kontingent festgelegt ist](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Wenn das **Kontingent aufgebraucht ist**, ist die Statusanzeige **rot**. 

    ![Rote Statusanzeige](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Die Statusanzeige ist **blau**, wenn gerade kein Kurs stattfindet (Zeitpunkt außerhalb des Labzeitplans) und ein Teil der Kontingents verbraucht wurde. 

    ![Blaue Statusanzeige](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-labs"></a>Anzeigen aller Labs
Nachdem Sie sich bei den Labs registriert haben, können Sie alle Labs anzeigen, indem Sie die folgenden Schritte ausführen: 

1. Navigieren Sie zu [https://labs.azure.com](https://labs.azure.com). Internet Explorer 11 wird noch nicht unterstützt. 
2. Melden Sie sich mit dem Benutzerkonto, das Sie zum Registrieren beim Lab verwendet haben, beim Dienst an. 
3. Überprüfen Sie, ob alle Labs angezeigt werden, auf die Sie Zugriff haben. 

    ![Anzeigen aller Labs](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen und Verwalten von Labkonten als Administrator](how-to-manage-lab-accounts.md)
- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Einrichten und Veröffentlichen von Vorlagen als Labbesitzer](how-to-create-manage-template.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
 