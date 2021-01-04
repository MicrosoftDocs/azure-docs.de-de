---
title: Festlegen von Kennwörtern für VMs in Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Kennwörter für virtuelle Computer (VMs) in Labs in Azure Lab Services festlegen und zurücksetzen.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6ae577ee4c0c7e31760e0fb12afeaeac1ef8b7e2
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96434225"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Einrichten und Verwalten eines VM-Pools 
In diesem Artikel wird gezeigt, wie Sie Folgendes durchführen:

- Erhöhen der Anzahl virtueller Computer (VMs) im Lab
- Starten von allen virtuellen Computern oder von ausgewählten virtuellen Computern 
- Zurücksetzen von virtuellen Computern

## <a name="update-the-lab-capacity"></a>Aktualisieren der Labkapazität
Führen Sie die folgenden Schritte aus, um die Labkapazität (Anzahl der virtuellen Computer in einem Lab) zu erhöhen bzw. zu verringern:

1. Wählen Sie auf der Seite **VM-Pool** die Option **Lab capacity: &lt;number&gt; machines** (Labkapazität: <Anzahl> Computer) aus.
2. Geben Sie die gewünschte neue **Anzahl virtueller Computer** für das Lab ein. Diese Zahl muss größer oder gleich der Anzahl von Benutzern sein, die im Lab registriert sind. 
3. Wählen Sie anschließend **Speichern** aus. 

    ![Screenshot: Fenster „Labkapazität“ mit den ausgewählten Schaltflächen „Maximale VM-Anzahl im Lab“ und „Speichern“](./media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Wenn Sie die Kapazität erhöht haben, sehen Sie, wie die virtuellen Computer erstellt werden. Wenn die neue VM nicht in der Liste angezeigt wird, aktualisieren Sie die Seite. 

    ![Erstellung eines virtuellen Computers](./media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>VMs starten

### <a name="start-ot-stop-all-vms"></a>Starten oder Beenden aller virtuellen Computer
1. Wechseln Sie zur Seite **VM-Pool**. 
2. Wählen Sie auf der Symbolleiste **Start all** (Alle starten) aus. 

    ![Schaltfläche „Start all“ (Alle starten)](./media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Nachdem alle virtuellen Computer gestartet wurden, können Sie sie durch Auswählen der Schaltfläche **Stop all** (Alle beenden) auf der Symbolleiste beenden. 

    ![Schaltfläche „Stop all“ (Alle beenden)](./media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Starten von ausgewählten virtuellen Computern
Es gibt zwei Möglichkeiten, ausgewählte virtuelle Computer (einen oder mehrere) zu starten. Eine Möglichkeit besteht darin, die virtuellen Computer in der Liste auszuwählen und dann auf der Symbolleiste auf **Starten** zu klicken. 

Die zweite Möglichkeit besteht darin, mindestens eine VM in der Liste auszuwählen und die Schaltfläche in der Spalte **Status** umzuschalten. 

![Starten von ausgewählten virtuellen Computern](./media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Ebenso können Sie mindestens eine VM beenden, indem Sie die Schaltfläche in der Spalte **Status** umschalten oder **Beendem** in der Symbolleiste auswählen. 

> [!NOTE]
> Wenn ein Lehrer/Dozent eine Kursteilnehmer-VM aktiviert, hat dies keine Auswirkung auf das Kontingent für den Kursteilnehmer. Das Kontingent für einen Benutzer gibt die Anzahl von Labstunden an, die für den Benutzer außerhalb der geplanten Kurszeit verfügbar sind. Weitere Informationen zu Kontingenten finden Sie unter [Festlegen von Kontingenten für Benutzer](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Zurücksetzen von virtuellen Computern

Wenn Sie virtuelle Computer zurücksetzen möchten, wählen Sie die entsprechenden Computer in der Liste aus, und klicken Sie dann auf der Symbolleiste auf **Zurücksetzen**. 

![Zurücksetzen von ausgewählten virtuellen Computern](./media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

Wählen Sie im Dialogfeld **Reset virtual machine(s)** (Virtuelle(n) Computer zurücksetzen) die Option **Zurücksetzen** aus. 

![Dialogfeld zum Zurücksetzen virtueller Computer](./media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)

## <a name="set-password-for-vms"></a>Festlegen eines Kennworts für virtuelle Computer
Ein Labbesitzer (Lehrkraft) kann die Kennwörter für virtuelle Computer zum Zeitpunkt der Erstellung des Labs (Assistent für Lab-Erstellung) oder nach dem Erstellen des Labs (auf der Seite **Vorlage**) festlegen und zurücksetzen. 

### <a name="set-password-at-the-time-of-lab-creation"></a>Festlegen des Kennworts zum Zeitpunkt der Lab-Erstellung
Ein Labbesitzer (Lehrkraft) kann auf der Seite **VM-Anmeldeinformationen** im Assistenten zur Lab-Erstellung ein Kennwort für die virtuellen Computer im Lab festlegen.

![Fenster „Neues Lab“](./media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Durch das Aktivieren/Deaktivieren der Option **Dasselbe Kennwort für alle VMs verwenden** auf dieser Seite kann eine Lehrkraft festlegen, ob für alle virtuellen Computer im Lab dasselbe Kennwort verwendet werden soll oder ob die Kursteilnehmer eigene Kennwörter für ihre virtuellen Computer festlegen können. Standardmäßig ist diese Einstellung für alle Images mit den Betriebssystemen Windows und Linux mit Ausnahme von Ubuntu aktiviert. Wenn diese Einstellung deaktiviert ist, werden die Kursteilnehmer bei der ersten Verbindung mit der VM aufgefordert, ein Kennwort festzulegen. 

### <a name="reset-password-later"></a>Späteres Zurücksetzen des Kennworts

1. Wählen Sie auf der Seite **Vorlage** des Labs auf der Symbolleiste die Option **Kennwort zurücksetzen** aus. 
1. Geben Sie im Dialogfeld **Kennwort zurücksetzen** ein Kennwort ein, und wählen Sie **Kennwort zurücksetzen** aus.
    
    ![Dialogfeld „Kennwort festlegen“](./media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Herstellen einer Verbindung virtuellen Computern von Kursteilnehmern
Der Ersteller des Labs (Lehrkraft) kann eine Verbindung mit dem virtuellen Computer eines Kursteilnehmers herstellen, wenn die folgenden Bedingungen erfüllt sind: 

- Die Option **Gleiches Kennwort für alle virtuellen Computer verwenden** wurde beim Erstellen des Labs ausgewählt.
- Die VM wird ausgeführt. 

 Um eine Verbindung mit dem virtuellen Kursteilnehmercomputer herzustellen, zeigen Sie mit der Maus auf den virtuellen Computer in der Liste, und wählen Sie dann die Schaltfläche „Computer“ aus.  

![Schaltfläche „Herstellen einer Verbindung mit Kursteilnehmer-VM“](./media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Wenn die Lehrkraft den virtuellen Computer startet und eine Verbindung mit ihm herstellt, ist das Kontingent für Kursteilnehmer davon nicht betroffen. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Exportieren einer Liste mit virtuellen Computern in eine CSV-Datei

1. Wechseln Sie zur Registerkarte **VM-Pool**.
2. Wählen Sie auf der Symbolleiste die Auslassungspunkte ( **...** ) und dann **CSV exportieren** aus. 

    ![Exportieren einer Liste mit virtuellen Computern](./media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Nächste Schritte
Informationen zu anderen Verwendungsoptionen für Kursteilnehmer, die Sie (als Lab-Besitzer) konfigurieren können, finden Sie im folgenden Artikel: [Konfigurieren von Nutzungseinstellungen und Richtlinien](how-to-configure-student-usage.md).

Informationen dazu, wie Kursteilnehmer Kennwörter für ihre virtuellen Computer zurücksetzen können, finden Sie unter [Festlegen oder Zurücksetzen von Kennwörtern für virtuelle Computer in Labs (Kursteilnehmer)](how-to-set-virtual-machine-passwords-student.md).