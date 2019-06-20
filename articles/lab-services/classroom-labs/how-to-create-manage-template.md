---
title: Verwalten einer Vorlage eines Classroom-Labs in Azure Lab Services | Microsoft-Dokumentation
description: Informationen zum Erstellen und Verwalten einer Vorlage eines Classroom-Labs in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: b287a67c470cc1697065838e52916c285a2233a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704351"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Erstellen und Verwalten einer Classroom-Vorlage in Azure Lab Services
Eine Vorlage in einem Lab ist ein VM-Basisimage und dient zur Erstellung der virtuellen Computer aller Benutzer. Richten Sie den virtuellen Computer der Vorlage so ein, dass er genau das enthält, was Sie den Lab-Benutzern zur Verfügung stellen möchten. Sie können einen Namen und eine Beschreibung der Vorlage angeben, die den Lab-Benutzern angezeigt werden. Anschließend veröffentlichen Sie die Vorlage, um Instanzen der Vorlagen-VM für Ihre Lab-Benutzer zur Verfügung zu stellen. Wenn Sie eine Vorlage veröffentlichen, werden von Azure Lab Services im Lab mithilfe der Vorlage virtuelle Computer erstellt. Die Anzahl der in diesem Vorgang erstellten virtuellen Computer entspricht der maximalen Anzahl von Benutzern im Lab, die Sie in der Nutzungsrichtlinie des Labs festlegen können. Alle virtuellen Computer haben die gleiche Konfiguration wie die Vorlage.

Dieser Artikel beschreibt das Erstellen und Verwalten einer Vorlagen-VM in einem Classroom-Lab von Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Veröffentlichen einer Vorlage beim Erstellen eines Classroom-Labs
Als Erstes können Sie beim Erstellen eines Classroom-Labs eine Vorlage einrichten und veröffentlichen.

1. Navigieren Sie zur Website [Azure Lab Services](https://labs.azure.com). 
2. Wählen Sie **Anmelden**, und geben Sie Ihre Anmeldeinformationen ein. Azure Lab Services unterstützt Geschäfts-, Schul- oder Unikonten und Microsoft-Konten. 
3. Führen Sie im Fenster **Neues Lab** die folgenden Aktionen aus: 
    1. Geben Sie unter **Name** einen Namen für Ihr Lab an. 
    2. Geben Sie die maximale **Anzahl von Benutzern** an, die für das Lab zulässig sind. 
    6. Wählen Sie **Speichern** aus.

        ![Erstellen eines Classroom-Labs](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Führen Sie auf der Seite **Select virtual machine specifications** (Spezifikationen für virtuellen Computer auswählen) die folgenden Schritte aus:
    1. Wählen Sie eine **Größe** für die virtuellen Computer (VMs), die im Lab erstellt werden. 
    2. Wählen Sie die **Region** aus, in der die VMs erstellt werden sollen. 
    3. Wählen Sie das **VM-Image** aus, das zum Erstellen von VMs im Lab verwendet werden soll. 
    4. Klicken Sie auf **Weiter**.

        ![Angeben von VM-Spezifikationen](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Geben Sie auf der Seite **Anmeldeinformationen festlegen** die Standardanmeldeinformationen für alle VMs im Lab an. 
    1. Geben Sie den **Namen des Benutzers** für alle VMs im Lab an.
    2. Geben Sie das **Kennwort** für den Benutzer an. 

        > [!IMPORTANT]
        > Notieren Sie sich den Benutzernamen und das Kennwort. Diese Angaben werden nicht noch einmal angezeigt.
    3. Klicken Sie auf **Erstellen**. 

        ![Festlegen von Anmeldeinformationen](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Auf der Seite **Vorlage konfigurieren** wird der Status für den Prozess der Laberstellung angezeigt. Die Erstellung der Vorlage im Lab dauert bis zu 20 Minuten. 

    ![Konfigurieren der Vorlage](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Nachdem die Konfiguration der Vorlage abgeschlossen ist, wird die folgende Seite angezeigt: 

    ![Seite „Vorlage konfigurieren“ nach Abschluss des Vorgangs](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Die folgenden Schritte sind in diesem Tutorial optional: 
    1. Starten Sie die Vorlage für virtuelle Computer, indem Sie **Starten** wählen.
    2. Stellen Sie eine Verbindung mit der Vorlage für virtuelle Computer her, indem Sie **Verbinden** wählen. 
    3. Installieren und konfigurieren Sie die Software in der Vorlage für virtuelle Computer. 
    4. **Beenden** Sie den virtuellen Computer.  
    5. Geben Sie eine **Beschreibung** für die Vorlage ein.

        ![„Weiter“ auf der Seite „Vorlage konfigurieren“](../media/tutorial-setup-classroom-lab/configure-template-next.png)
9. Wählen Sie auf der Vorlagenseite die Option **Weiter**. 
10. Führen Sie auf der Seite **Vorlage veröffentlichen** die folgenden Aktionen durch. 
    1. Aktivieren Sie zum sofortigen Veröffentlichen der Vorlage das Kontrollkästchen *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Ich weiß, dass die Vorlage nach dem Veröffentlichen nicht mehr geändert werden kann. Dieser Prozess kann nur einmal durchgeführt werden und bis zu einer Stunde dauern.), und wählen Sie **Veröffentlichen**.  

        > [!WARNING]
        > Nachdem die Veröffentlichung erfolgt ist, kann sie nicht mehr rückgängig gemacht werden. 
    2. Wählen Sie **Für später speichern**, wenn Sie die Veröffentlichung später durchführen möchten. Sie können die Vorlage für virtuelle Computer veröffentlichen, nachdem der Assistent abgeschlossen wurde. Weitere Informationen zum Konfigurieren und Veröffentlichen nach Abschluss des Assistenten finden Sie im Abschnitt „Veröffentlichen der Vorlage“ des Artikels [Verwalten von Classroom-Labs](how-to-manage-classroom-labs.md).

        ![Vorlage veröffentlichen](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Für die Vorlage wird der **Status der Veröffentlichung** angezeigt. Dieser Vorgang kann bis zu einer Stunde dauern. 

    ![Veröffentlichen der Vorlage – Status](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Die folgende Seite wird angezeigt, wenn die Veröffentlichung der Vorlage erfolgreich war. Wählen Sie **Fertig**aus.

    ![Veröffentlichen der Vorlage – Erfolg](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Das **Dashboard** für das Lab wird angezeigt. 
    
    ![Dashboard für Classroom-Lab](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

 
## <a name="set-or-update-template-title-and-description"></a>Festlegen oder Aktualisieren von Vorlagentitel und -beschreibung
Gehen Sie wie folgt vor, um Titel und Beschreibung erstmals festzulegen und später zu aktualisieren. 

1. Wählen Sie im Abschnitt **Vorlage** die Option **Name** oder **Beschreibung** der Vorlage aus. 
2. Geben Sie den **neuen Namen** bzw. die **neue Beschreibung** für die Vorlage ein, und drücken Sie die**EINGABETASTE**.

    ![Vorlagenname und -beschreibung](../media/how-to-create-manage-template/template-name-description.png)

## <a name="set-up-or-update-a-template-vm"></a>Einrichten oder Aktualisieren einer Vorlage für virtuelle Computer
 Sie können eine Verbindung mit der Vorlage für virtuelle Computer herstellen und darauf die erforderliche Software installieren, bevor Sie ihn für Ihre Teilnehmer bereitstellen. Gehen Sie wie folgt vor, um erstmals eine Vorlage für virtuelle Computer einzurichten oder den virtuellen Computer zu aktualisieren. 

1. Warten Sie, bis die Vorlage für virtuelle Computer bereit ist. Wenn dies der Fall ist, wird die Schaltfläche **Starten** aktiviert. Wählen Sie die Option **Starten**, um den virtuellen Computer zu starten.

    ![Starten der Vorlage für virtuelle Computer](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Überprüfen Sie die Warnung, und wählen Sie **Starten** aus. 

    ![Startvorlage – Warnung](../media/how-to-create-manage-template/start-template-warning.png)
2. Sie finden Sie den Status der auf der Kachel des Labs im Abschnitt **Vorlage**.

    ![Startvorlage – Status](../media/how-to-create-manage-template/template-start-status.png)
1. Wählen Sie nach dem Start die Option **Verbinden** aus, und befolgen Sie die Anweisungen, um eine Verbindung mit dem virtuellen Computer herzustellen. 

    ![Herstellen und einer Verbindung mit und Beenden der VM-Vorlage](../media/how-to-create-manage-template/connect-stop-vm.png)
1. Installieren Sie die Software, die die Teilnehmer für das Lab benötigen (z.B. Visual Studio, Azure Storage-Explorer usw.). 
2. Trennen Sie die Verbindung mit der Vorlage für virtuelle Computer (indem Sie die Remotedesktopsitzung schließen). 
3. **Beenden** Sie die Vorlage für virtuelle Computer, indem Sie **Beenden** wählen. 

## <a name="publish-the-template-vm"></a>Veröffentlichen der Vorlage für virtuelle Computer  
Wenn Sie die Vorlage nicht beim Erstellen des Labs veröffentlichen, können Sie dies später nachholen. Vor der Veröffentlichung empfiehlt es sich, eine Verbindung mit der Vorlagen-VM herzustellen und sie ggf. mit Software zu aktualisieren. Wenn Sie eine Vorlage veröffentlichen, werden von Azure Lab Services im Lab mithilfe der Vorlage virtuelle Computer erstellt. Die Anzahl der in diesem Vorgang erstellten virtuellen Computer entspricht der maximalen Anzahl von Benutzern im Lab, die Sie in der Nutzungsrichtlinie des Labs festlegen können. Alle virtuellen Computer haben die gleiche Konfiguration wie die Vorlage. 

1. Wählen Sie im Abschnitt **Vorlage** die Option **Veröffentlichen**. 

    ![Veröffentlichen der Vorlage für virtuelle Computer](../media/tutorial-setup-classroom-lab/public-access.png)
1. Prüfen Sie die Meldung im Meldungsfeld **Vorlage veröffentlichen**, und wählen Sie **Veröffentlichen**. Dieser Vorgang kann einige Zeit dauern, je nachdem, wie viele virtuelle Computer erstellt werden.
    
    > [!IMPORTANT]
    > Sobald eine Vorlage veröffentlicht wurde, kann dies nicht mehr rückgängig gemacht werden. Sie können die Vorlage jedoch erneut veröffentlichen. 
4. Warten Sie, bis der Status der Vorlage in **Veröffentlicht** geändert wird. 

    ![Veröffentlichungsstatus](../media/how-to-create-manage-template/publish-status.png)
1. Wechseln Sie zur Seite **Virtuelle Computer**, und vergewissern Sie sich, dass virtuelle Computer mit dem Status **Nicht zugewiesen** angezeigt werden. Diese virtuellen Computer sind noch keinen Teilnehmern zugewiesen. Warten Sie, bis die virtuellen Computer erstellt wurden. Sie sollten den Status **Beendet** aufweisen. Auf dieser Seite können Sie einen virtuellen Computer für einen Teilnehmer starten, eine Verbindung damit herstellen und ihn beenden und löschen. Sie können virtuelle Computer auf dieser Seite starten oder sie von Ihren Kursteilnehmern starten lassen. 

    ![Virtuelle Computer im Status „Beendet“](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="republish-the-template"></a>Erneutes Veröffentlichen der Vorlage 
Nachdem Sie eine Vorlage veröffentlicht haben, können Sie weiterhin eine Verbindung mit der Vorlagen-VM herstellen, sie aktualisieren und dann erneut veröffentlichen. Wenn Sie eine Vorlagen-VM erneut veröffentlichen, werden alle Benutzer getrennt und anhand der aktualisierten Vorlage neu erstellt. 

1. Wählen Sie auf der Dashboardseite Ihres Labs im Abschnitt „Vorlage“ die Option **Erneut veröffentlichen**. 

    ![Schaltfläche „Erneut veröffentlichen“](../media/how-to-create-manage-template/republish-button.png)
2. Prüfen Sie den Text im Meldungsfeld **Vorlage erneut veröffentlichen**, und wählen Sie **Erneut veröffentlichen**, um den Vorgang fortzusetzen. Klicken Sie andernfalls auf **Abbrechen**. 

    ![Meldung zum erneuten Veröffentlichen der Vorlage](../media/how-to-create-manage-template/republish-template-message.png)
3. Sie finden Sie den Status der erneuten Veröffentlichung auf der Kachel im Abschnitt **Vorlage**.

    ![Status des erneuten Veröffentlichung](../media/how-to-create-manage-template/republish-status-publishing.png)
4. Nachdem die Vorlage veröffentlicht wurde, wird der Status auf **Veröffentlicht** festgelegt. 

    ![Neuveröffentlichung erfolgreich](../media/how-to-create-manage-template/republish-success.png)

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln:

- [Erstellen und Verwalten von Labkonten als Administrator](how-to-manage-lab-accounts.md)
- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf ein Classroom-Lab in Azure Lab Services](how-to-use-classroom-lab.md) (als Labbenutzer)
