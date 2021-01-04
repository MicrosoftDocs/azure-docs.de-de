---
title: Ausführen der Notfallwiederherstellung virtueller Computer
description: In diesem Artikel wird gezeigt, wie Sie die Notfallwiederherstellung virtueller Computer mithilfe von Azure VMware Solution durchführen.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 688d91bc181e1479f5090a10af4b3b262d7ddb7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779610"
---
# <a name="complete-disaster-recovery-of-virtual-machines-using-azure-vmware-solution"></a>Ausführen der Notfallwiederherstellung virtueller Computer mithilfe von Azure VMware Solution

In diesem Artikel wird das Verfahren zum Durchführen einer Notfallwiederherstellung Ihrer virtuellen Computer (VMs) mit der VMware HCX-Lösung (Hybrid Cloud Extension) und unter Verwendung einer privaten Azure VMware Solution-Cloud als Wiederherstellungs- oder Zielstandort beschrieben.

VMware HCX bietet verschiedene Vorgänge, die eine genaue Kontrolle und präzise Steuerung in Replikationsrichtlinien ermöglichen. Die verfügbaren Vorgänge umfassen:

- **Reverse** (Umkehren): Dieser Vorgang wird nach dem Eintreten eines Notfalls durchgeführt. Durch Reverse wird Standort B der Quellstandort und Standort A zu dem Standort, an dem sich die geschützte VM nun befindet.

- **Pause** (Anhalten): Hiermit wird die aktuelle Replikationsrichtlinie angehalten, die der ausgewählten VM zugeordnet ist.

- **Resume** (Fortsetzen): Hiermit wird die aktuelle Replikationsrichtlinie fortgesetzt, die der ausgewählten VM zugeordnet ist.

- **Remove** (Entfernen): Hiermit wird die aktuelle Replikationsrichtlinie entfernt, die der ausgewählten VM zugeordnet ist.

- **Sync Now** (Jetzt synchronisieren): Hiermit wird eine außerplanmäßige Synchronisierung der Quell-VM mit der geschützten VM durchgeführt.

In diesem Leitfaden werden die folgenden Replikationsszenarios behandelt:

- Schützen einer VM oder einer Gruppe von VMs.

- Ausführen einer Testwiederherstellung einer VM oder einer Gruppe von VMs.

- Wiederherstellen einer VM oder einer Gruppe von VMs.

- Umgekehrter Schutz einer VM oder einer Gruppe von VMs.

## <a name="protect-vms"></a>Schützen von virtuellen Computern

1. Melden Sie sich beim **vSphere-Client** am Quellstandort an, und greifen Sie auf das **HCX-Plug-In** zu.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/hcx-vsphere.png" alt-text="HCX-Option in vSphere" border="true":::

1. Navigieren Sie zum Bereich **Disaster Recovery** (Notfallwiederherstellung), und klicken Sie auf **PROTECT VMS** (VMs SCHÜTZEN).

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png" alt-text="Auswählen von „Protect VMs“" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine.png":::

1. Wählen Sie die Quelle und die Remotestandorte aus. In diesem Fall sollte der Remotestandort die private Azure VMware Solution-Cloud sein.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machines.png" alt-text="Fenster „VMs schützen“" border="true":::

1. Wählen Sie bei Bedarf die Optionen für die **Standardreplikation** aus:

   - **Enable Compression (Komprimierung aktivieren):** Empfohlen für Szenarien mit geringem Durchsatz.

   - **Enable Quiescence (Ruhezustand aktivieren):** Hält den virtuellen Computer an, um sicherzustellen, dass eine konsistente Kopie mit dem Remotestandort synchronisiert wird.

   - **Destination Storage (Zielspeicher):** Hierbei handelt es sich um den Remotedatenspeicher für die geschützten VMs. In einer privaten Azure VMware Solution-Cloud sollte dies der vSAN-Datenspeicher sein.

   - **Compute Container:** Hierbei handelt es sich um den vSphere-Remotecluster oder den entsprechenden Ressourcenpool.

   - **Destination Folder (Zielordner):** Hierbei handelt es sich um den Remotezielordner, der optional ist. Wenn kein Ordner ausgewählt wird, werden die VMs unmittelbar unter dem ausgewählten Cluster platziert.

   - **RPO:** Dies ist der Synchronisierungsintervall zwischen der Quell-VM und der geschützten VM. Dieser kann zwischen fünf Minuten und 24 Stunden liegen.

   - **Snapshot interval (Momentaufnahmeintervall):** Das Intervall zwischen Momentaufnahmen.

   - **Number of Snapshots (Anzahl Momentaufnahmen):** Die Gesamtzahl der Momentaufnahmen innerhalb des konfigurierten Momentaufnahmeintervalls.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png" alt-text="Optionen zum Schutz von VMs" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-virtual-machine-options.png":::

1. Wählen Sie eine oder mehrere VMs aus der Liste aus, und konfigurieren Sie die gewünschten Replikationsoptionen.

   Standardmäßig erben die VMs die in den Standardreplikationsoptionen konfigurierte Richtlinie für globale Einstellungen. Konfigurieren Sie für jede Netzwerkschnittstelle auf dem ausgewählten virtuellen Computer die **Remote-Netzwerkportgruppe** , und wählen Sie **Finish** (Fertig stellen) aus, um den Schutz Prozess zu starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/network-interface-options.png" alt-text="Netzwerkschnittstellenoptionen" border="true" lightbox="./media/disaster-recovery-virtual-machines/network-interface-options.png":::

1. Überwachen Sie den Prozess für die einzelnen ausgewählten VMs im selben Notfallwiederherstellungsbereich.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png" alt-text="Überwachen des Status des Schutzes" border="true" lightbox="./media/disaster-recovery-virtual-machines/protect-monitor-progress.png":::

1. Nachdem für den Schutz der VM gesorgt wurde, können die verschiedenen Momentaufnahmen auf der Registerkarte **Snapshots** (Momentaufnahmen) angezeigt werden.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/list-of-snapshots.png" alt-text="Liste der Momentaufnahmen" border="true" lightbox="./media/disaster-recovery-virtual-machines/list-of-snapshots.png":::

   Das gelbe Dreieck bedeutet, dass die Momentaufnahmen und die VMs nicht in einem Testwiederherstellungsvorgang getestet wurden.

   Es gibt wesentliche Unterschiede zwischen einer VM, die ausgeschaltet ist, und einer VM, die eingeschaltet ist. Auf der Abbildung sehen Sie den Synchronisierungsprozess für eine eingeschaltete VM. Der Synchronisierungsvorgang wird gestartet, bis die erste Momentaufnahme abgeschlossen ist. Dabei handelt es sich um eine vollständige Kopie der VM. Die nächsten Momentaufnahmen werden dann im konfigurierten Intervall abgeschlossen. Es wird eine Kopie für eine ausgeschaltete VM synchronisiert, und dann wird die VM als inaktiv und der Schutzvorgang als abgeschlossen angezeigt.  Wenn die VM eingeschaltet ist, wird der Synchronisierungsprozess an den Remotestandort gestartet.

## <a name="complete-a-test-recover-of-vms"></a>Ausführen einer Testwiederherstellung für VMs

1. Melden Sie sich beim **vSphere-Client** am Remotestandort (der privaten Cloud der Azure-VMware-Lösung) an. 
1. Klicken Sie im **HCX-Plug-In** im Bereich „Disaster Recovery“ (Notfallwiederherstellung) auf die vertikalen Auslassungspunkte für eine beliebige VM, um das Vorgangsmenü anzuzeigen. Klicken Sie dann auf **Test Recover VM** (VM-Wiederherstellung testen).

   :::image type="content" source="./media/disaster-recovery-virtual-machines/test-recover-virtual-machine.png" alt-text="Auswählen von „Test Recover VM“ (Testwiederherstellung VM)" border="true":::

1. Wählen Sie die Optionen für den Test und die Momentaufnahme aus, die Sie verwenden möchten, um verschiedene Status der VM zu testen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/choose-snapshot.png" alt-text="Auswählen einer Momentaufnahme und Klicken auf „Test“ (Testen)" border="true":::

1. Nachdem Sie auf **Test** geklickt haben, wird der Wiederherstellungsvorgang gestartet.

1. Nach Abschluss können Sie die neue VM in der vCenter-Instanz für die private Azure VMware Solution-Cloud überprüfen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/verify-test-recovery.png" alt-text="Überprüfen des Wiederherstellungsvorgangs" border="true" lightbox="./media/disaster-recovery-virtual-machines/verify-test-recovery.png":::

1. Führen Sie nach dem Testen der VM oder einer Anwendung, die auf dieser ausgeführt wird, eine Bereinigung durch, um die Testinstanz zu löschen.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png" alt-text="Bereinigen einer Testinstanz" border="true" lightbox="./media/disaster-recovery-virtual-machines/cleanup-test-instance.png":::

## <a name="recover-vms"></a>Wiederherstellen von VMs

1. Melden Sie sich beim **vSphere-Client** am Remotestandort (der privaten Cloud der Azure-VMware-Lösung) an, und greifen Sie auf das **HCX-Plug-In** zu.

   Für das Wiederherstellungsszenario wird eine Gruppe von VMs für dieses Beispiel verwendet.

1. Wählen Sie die wiederherzustellende VM aus der Liste aus, öffnen Sie das Menü **ACTIONS** (AKTIONEN), und klicken Sie auf **Recover VMs** (VMs wiederherstellen).

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines.png" alt-text="Die Option „Recover VMs“" border="true":::

1. Konfigurieren Sie die Wiederherstellungsoptionen für jede Instanz, und klicken Sie auf **Recover** (Wiederherstellen), um den Wiederherstellungsvorgang zu starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/recover-virtual-machines-confirm.png" alt-text="Bestätigung der VM-Wiederherstellung" border="true":::

1. Nachdem der Wiederherstellungsvorgang abgeschlossen ist, werden die neuen VMs im Inventar der vCenter Server-Remoteinstanz angezeigt.

## <a name="complete-a-reverse-replication-on-vms"></a>Durchführen einer umgekehrten Replikation auf VMs

1. Melden Sie sich beim **vSphere-Client** in der privaten Cloud Ihrer Azure-VMware-Lösung an, und greifen Sie auf das **HCX-Plug-In** zu.
   
   >[!NOTE]
   > Stellen Sie sicher, dass die ursprünglichen VMs am Quellstandort ausgeschaltet werden, bevor Sie mit der umgekehrten Replikation beginnen. Während des Vorgangs tritt ein Fehler auf, wenn die VMs nicht ausgeschaltet sind.

1. Wählen Sie in der Liste die VMs aus, die zurück an den Quellstandort repliziert werden sollen, öffnen Sie das Menü **ACTIONS** (AKTIONEN), und klicken Sie auf **Reverse** (Umkehren). 
1. Klicken Sie auf **Reverse** (Umkehren), um die Replikation zu starten.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/reverse-operation-virtual-machines.png" alt-text="Auswählen der Aktion „Umgekehrt“ unter den Schutzvorgängen" border="true":::

1. Überwachen Sie den Abschnitt mit Details zu den einzelnen VMs.

   :::image type="content" source="./media/disaster-recovery-virtual-machines/review-reverse-operation.png" alt-text="Überprüfen der Ergebnisse der Aktion „Umgekehrt“" border="true" lightbox="./media/disaster-recovery-virtual-machines/review-reverse-operation.png":::

## <a name="disaster-recovery-plan-automation"></a>Automatisierung des Plans zur Notfallwiederherstellung

VMware HCX verfügt derzeit nicht über einen integrierten Mechanismus zum Erstellen und Automatisieren eines Plans für die Notfallwiederherstellung. Es werden jedoch mehrere REST-APIs von VMware HCX bereitgestellt, einschließlich APIs für den Notfallwiederherstellungsvorgang. Auf die API-Spezifikation kann in VMware HCX Manager über die URL zugegriffen werden.

Diese APIs decken die folgenden Vorgänge bei der Notfallwiederherstellung ab.

- Schützen

- Wiederherstellen

- Testwiederherstellung

- Geplante Wiederherstellung

- Reverse

- Abfrage

- Testbereinigung

- Pause

- Fortfahren

- Schutz entfernen

- Neu konfigurieren

Im Folgenden finden Sie ein Beispiel für eine Wiederherstellungsvorgangsnutzlast in JSON.

```json
[

    {

        "replicationId": "string",

        "needPowerOn": true,

        "instanceId": "string",

        "source": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "destination": {

            "endpointType": "string",

            "endpointId": "string",

            "endpointName": "string",

            "resourceType": "string",

            "resourceId": "string",

            "resourceName": "string"

        },

        "placement": [

            {

                "containerType": "string",

                "containerId": "string"

            }

        ],

        "resourceId": "string",

        "forcePowerOff": true,

        "isTest": true,

        "forcePowerOffAfterTimeout": true,

        "isPlanned": true

    }

]
```

Mit diesen APIs können Sie einen benutzerdefinierten Mechanismus erstellen, um die Erstellung und Ausführung eines Notfallwiederherstellungsplans zu automatisieren.
