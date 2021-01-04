---
title: Verwalten einer Vorlage eines Classroom-Labs in Azure Lab Services | Microsoft-Dokumentation
description: Informationen zum Erstellen und Verwalten einer Vorlage eines Classroom-Labs in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434276"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Erstellen und Verwalten einer Classroom-Vorlage in Azure Lab Services
Eine Vorlage in einem Lab ist ein VM-Basisimage und dient zur Erstellung der virtuellen Computer aller Benutzer. Richten Sie den virtuellen Computer der Vorlage so ein, dass er genau das enthält, was Sie den Lab-Benutzern zur Verfügung stellen möchten. Sie können einen Namen und eine Beschreibung der Vorlage angeben, die den Lab-Benutzern angezeigt werden. Anschließend veröffentlichen Sie die Vorlage, um Instanzen der Vorlagen-VM für Ihre Lab-Benutzer zur Verfügung zu stellen. Wenn Sie eine Vorlage veröffentlichen, werden von Azure Lab Services im Lab mithilfe der Vorlage virtuelle Computer erstellt. Die Anzahl der in diesem Vorgang erstellten virtuellen Computer entspricht der maximalen Anzahl von Benutzern im Lab, die Sie in der Nutzungsrichtlinie des Labs festlegen können. Alle virtuellen Computer haben die gleiche Konfiguration wie die Vorlage.

Dieser Artikel beschreibt das Erstellen und Verwalten einer Vorlagen-VM in einem Classroom-Lab von Azure Lab Services. 

> [!NOTE]
> Wenn Sie ein Lab erstellen,wird die Vorlagen-VM zwar erstellt, aber nicht gestartet. Sie können diese starten, eine Verbindung herstellen, eine beliebige erforderliche Software für das Lab installieren und die VM dann veröffentlichen. Wenn Sie die Vorlagen-VM veröffentlichen, wird diese automatisch für Sie heruntergefahren, wenn Sie dies nicht bereits selbst getan haben. 
> 
> Bei Vorlagen-VMs entstehen **Kosten**, wenn sie ausgeführt werden. Stellen Sie daher sicher, dass die Vorlagen-VM heruntergefahren wird, wenn sie nicht ausgeführt werden muss. 


## <a name="set-or-update-template-title-and-description"></a>Festlegen oder Aktualisieren von Vorlagentitel und -beschreibung
Gehen Sie wie folgt vor, um Titel und Beschreibung erstmals festzulegen und später zu aktualisieren. 

1. Geben Sie auf der Seite **Vorlage** den neuen **Titel** für das Lab ein.  
2. Geben Sie die neue **Beschreibung** für die Vorlage ein. Wenn Sie den Fokus aus dem Textfeld verschieben, wird es automatisch gespeichert. 

    ![Vorlagenname und -beschreibung](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualisieren einer Vorlage für virtuelle Computer
Gehen Sie wie folgt vor, um eine Vorlage für virtuelle Computer zu aktualisieren:  

1. Warten Sie, bis die Vorlage für virtuelle Computer gestartet wurde, und wählen Sie dann auf der Symbolleiste **Mit Vorlage verbinden** aus, um eine Verbindung mit der Vorlage für virtuelle Computer herzustellen. Befolgen Sie dann die Anweisungen. Handelt es sich um einen Windows-Computer, wird eine Option zum Herunterladen der RDP-Datei angezeigt. 
1. Wenn Sie eine Verbindung mit der Vorlage hergestellt und Änderungen vorgenommen haben, hat sie nicht mehr dasselbe Setup wie die virtuellen Computer, die zuletzt für die Benutzer veröffentlicht wurden. Vorlagenänderungen werden erst nach der erneuten Veröffentlichung auf den vorhandenen virtuellen Computern der Benutzer widergespiegelt.

    ![Herstellen einer Verbindung mit der Vorlage für virtuelle Computer](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installieren Sie die Software, die die Teilnehmer für das Lab benötigen (z.B. Visual Studio, Azure Storage-Explorer usw.). 
1. Trennen Sie die Verbindung mit der Vorlage für virtuelle Computer (indem Sie die Remotedesktopsitzung schließen). 
1. **Beenden** Sie die Vorlage für virtuelle Computer, indem Sie **Stop template** (Vorlage beenden) auswählen. 
1. Führen Sie die Schritte im nächsten Abschnitt aus, um die aktualisierte Vorlage für virtuelle Maschinen zu **veröffentlichen**. 

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
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Erstellen und Verwalten von Labkonten als Administrator](how-to-manage-lab-accounts.md)
- [Erstellen und Verwalten von Labs als Labbesitzer](how-to-manage-classroom-labs.md)
- [Konfigurieren und Steuern der Nutzung eines Labs als Labbesitzer](how-to-configure-student-usage.md)
- [Zugreifen auf Labs als Labbenutzer](how-to-use-classroom-lab.md)
