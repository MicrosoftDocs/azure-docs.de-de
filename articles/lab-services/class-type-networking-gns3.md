---
title: Einrichten eines Labs für Netzwerke mit Azure Lab Services und GNS3 | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure Lab Services ein Lab einrichten, um Kenntnisse zu Netzwerken mit GNS3 zu vermitteln.
ms.topic: article
ms.date: 01/19/2021
ms.openlocfilehash: dec5dea13d5a89536a06da45fc57d33881a9b3ad
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99499394"
---
# <a name="set-up-a-lab-to-teach-a-networking-class"></a>Einrichten eines Labs für einen Kurs zu Netzwerken 
In diesem Artikel erfahren Sie, wie Sie einen Kurs einrichten, in dem Kursteilnehmer unter Verwendung der Software [GNS3](https://www.gns3.com/) virtuelle und reale Netzwerke emulieren, konfigurieren und testen und auftretende Fehler beheben. 

Dieser Artikel besteht aus zwei Hauptteilen. Im ersten Abschnitt wird erläutert, wie das Kurs-Lab erstellt wird. Im zweiten Abschnitt wird erläutert, wie der Vorlagencomputer mit aktivierter geschachtelter Virtualisierung sowie mit der installierten und konfigurierten Software GNS3 erstellt wird.

## <a name="lab-configuration"></a>Labkonfiguration
Zum Einrichten dieses Labs benötigen Sie als Einstieg ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie entweder ein neues Lab-Konto in Azure Lab Services erstellen oder ein vorhandenes Konto verwenden. Sehen Sie sich das folgende Tutorial zum Erstellen eines neuen Lab-Kontos an: [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).

Absolvieren Sie [dieses Tutorial](tutorial-setup-classroom-lab.md), um ein neues Lab mit den folgenden Einstellungen zu erstellen:

| Größe des virtuellen Computers | Image |
| -------------------- | ----- | 
| Groß (geschachtelte Virtualisierung) | Windows 10 Pro Version 1909 |

## <a name="template-machine"></a>Vorlagencomputer 

Nachdem der Vorlagencomputer erstellt wurde, starten Sie den Computer, und stellen Sie eine Verbindung mit ihm her, um die folgenden drei Hauptaufgaben auszuführen. 
 
1. Vorbereiten des Vorlagencomputers für die geschachtelte Virtualisierung
2. Installieren von GNS3
3. Erstellen einer geschachtelten GNS3-VM in Hyper-V
4. Konfigurieren von GNS3 zur Verwendung der Windows Hyper-V-VM
5. Hinzufügen geeigneter Appliances
6. Veröffentlichen der Vorlage


### <a name="prepare-template-machine-for-nested-virtualization"></a>Vorbereiten des Vorlagencomputers für geschachtelte Virtualisierung
- Befolgen Sie die Anweisungen in [diesem Artikel](how-to-enable-nested-virtualization-template-vm.md), um ihren virtuellen Vorlagencomputer für geschachtelte Virtualisierung vorzubereiten. 

### <a name="install-gns3"></a>Installieren von GNS3
- Befolgen Sie die Anweisungen zur [Installation von GNS3 unter Windows](https://docs.gns3.com/docs/getting-started/installation/windows).  Stellen Sie sicher, dass Sie wie unten gezeigt die Installation der **GNS3-VM** im Dialogfeld für Komponenten auswählen.

![Auswählen der GNS3-VM](./media/class-type-networking-gns3/gns3-select-vm.png)

Schließlich gelangen Sie zur Auswahl des GNS3-VM-Typs. Wählen Sie hier die Option **Hyper-V** aus.

![Auswählen von Hyper-V](./media/class-type-networking-gns3/gns3-vm-hyper-v.png)

  Mit dieser Option werden das PowerShell-Skript und die VHD-Dateien heruntergeladen, um die GNS3-VM im Hyper-V-Manager zu erstellen. Setzen Sie die Installation mit den Standardwerten fort. **Starten Sie GNS3 nicht, nachdem die Installation abgeschlossen ist**.

### <a name="create-gns3-vm"></a>Erstellen der GNS3-VM
Nach Abschluss der Installation wird die ZIP-Datei **GNS3.VM.Hyper-V.2.2.17.zip** in denselben Ordner wie die Installationsdatei heruntergeladen. Sie enthält die Laufwerke und das PowerShell-Skript zum Erstellen der Hyper-V-VM.
- Wählen Sie **Alle extrahieren** für die Datei „GNS3.VM.Hyper-V.2.2.17.zip“ aus.  Mit dieser Aktion werden die Laufwerke und das PowerShell-Skript zum Erstellen der VM extrahiert.
- Klicken Sie mit der rechten Maustaste auf das PowerShell-Skript „create-vm.ps1“, und wählen Sie **Mit PowerShell ausführen** aus.
- Möglicherweise wird eine Anforderung zum Ändern der Ausführungsrichtlinie angezeigt. Geben Sie „Y“ ein, um das Skript auszuführen.

![PowerShell, Ausführungsrichtlinie](./media/class-type-networking-gns3/powershell-execution-policy-change.png)

- Nach der Ausführung des Skripts können Sie überprüfen, ob die VM „GNS3 VM“ im Hyper-V-Manager erstellt wurde.

### <a name="configure-gns3-to-use-hyper-v-vm"></a>Konfigurieren von GNS3 zur Verwendung der Hyper-V-VM
Nachdem GNS3 installiert und die GNS3-VM hinzugefügt wurde, starten Sie GNS3, um die beiden miteinander zu verknüpfen.  Der [GNS3-Setup-Assistent wird automatisch gestartet](https://docs.gns3.com/docs/getting-started/setup-wizard-gns3-vm#local-gns3-vm-setup-wizard).  
- Verwenden Sie die Option **Run appliances from virtual machine** (Appliances über VM ausführen). (Neuen Branch erstellen und Pull Request starten).  Verwenden Sie in den restlichen Schritten des Assistenten die Standardwerte, bis der Fehler **VMware vmrun tool cannot be found.** (VMware-Tool vmrun kann nicht gefunden werden) angezeigt wird. Fehler zurück.

![VMware-Fehler](./media/class-type-networking-gns3/gns3-vmware-vmrun-tool-not-found.png)

- Wählen Sie im Assistenten **OK** und dann **Cancel** (Abbrechen) aus.
- Um die Verbindung mit der Hyper-V-VM herzustellen, öffnen Sie **Edit** -> **Preferences** -> **GNS3 VM** (Bearbeiten > Einstellungen > GNS3 VM), und wählen Sie die Optionen **Enable the GNS3 VM** (GNS3-VM aktivieren) und **Hyper-V** aus.
 
![Aktivieren der GNS3-VM](./media/class-type-networking-gns3/gns3-preference-vm.png)

### <a name="add-appropriate-appliances"></a>Hinzufügen geeigneter Appliances

An diesem Punkt fügen Sie die geeigneten [Appliances für den Kurs hinzu](https://docs.gns3.com/docs/using-gns3/beginners/install-from-marketplace).

### <a name="publish-template"></a>Vorlage veröffentlichen

Nachdem die Vorlagen-VM ordnungsgemäß eingerichtet wurde und für die Veröffentlichung bereit ist, müssen Sie einige wichtige Punkte überprüfen.
- Stellen Sie sicher, dass die GNS3-VM heruntergefahren oder ausgeschaltet ist.  Wenn die VM während der Veröffentlichung noch ausgeführt wird, wird die VM beschädigt.
- Schließen Sie GNS3. Wenn GNS3 während der Veröffentlichung ausgeführt wird, kann dies zu unbeabsichtigten Nebenwirkungen führen.
- Bereinigen Sie alle Installationsdateien oder andere nicht benötigte Dateien.

## <a name="cost"></a>Kosten  

Das folgende Beispiel dient der Einschätzung der Lab-Kosten: 
 
Für einen Kurs mit 25 Teilnehmern, 20 planmäßigen Kursstunden und 10 Stunden Hausaufgaben bzw. Arbeitsaufträgen entstünden folgende Kosten für das Lab: 

25 Kursteilnehmer × (20 + 10) Stunden × 84 Lab-Einheiten × 0,01 USD pro Stunde = 630 USD 

**Wichtig:** Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung
In diesem Artikel wurden die Schritte zum Erstellen eines Labs für die Netzwerkkonfiguration mithilfe von GNS3 erläutert.

## <a name="next-steps"></a>Nächste Schritte
Die nächsten Schritte sind die gleichen für sämtliche Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Registrierungslinks an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users).