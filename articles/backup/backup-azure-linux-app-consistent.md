---
title: 'Azure Backup: anwendungskonsistente Sicherungen von Linux-VMs'
description: Erstellen Sie anwendungskonsistente Sicherungen Ihrer virtuellen Linux-Computer in Azure. In diesem Artikel wird beschrieben, wie Sie das Skriptframework zum Sichern von in Azure bereitgestellten Linux-VMs konfigurieren. Außerdem enthält dieser Artikel Informationen zur Problembehandlung.
services: backup
author: anuragmehrotra
manager: shivamg
keywords: App-konsistente Sicherung; anwendungskonsistente Sicherung für Azure-VM; Linux-VM-Sicherung; Azure-Sicherung
ms.service: backup
ms.topic: conceptual
ms.date: 1/12/2018
ms.author: anuragm
ms.openlocfilehash: a81c0b9c87db85771fcecab87c6b9ac88dcbd472
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641125"
---
# <a name="application-consistent-backup-of-azure-linux-vms"></a>Anwendungskonsistente Sicherung von virtuellen Linux-Computern in Azure

Beim Erstellen von Sicherungsmomentaufnahmen für Ihre VMs bedeutet Anwendungskonsistenz, dass Ihre Anwendungen gestartet werden, wenn die VMs nach dem Wiederherstellen gestartet werden. Wie Sie sich sicherlich denken können, ist die Anwendungskonsistenz äußerst wichtig. Zur Sicherstellung der Anwendungskonsistenz für Ihre Linux-VMs könne Sie das Pre-Skript- und Post-Skript-Framework von Linux verwenden, um anwendungskonsistente Sicherungen zu erstellen. Das Pre-Skript- und Post-Skript-Framework unterstützt virtuelle Linux-Computer, die mit Azure Resource Manager bereitgestellt wurden. Skripts für Anwendungskonsistenz unterstützen keine mit Service Manager bereitgestellten virtuellen Computer oder Windows-VMs.

## <a name="how-the-framework-works"></a>Funktionsweise des Frameworks

Das Framework bietet eine Option zum Ausführen benutzerdefinierter Pre-Skripts und Post-Skripts beim Erstellen einer VM-Momentaufnahme. Pre-Skripts werden unmittelbar vor dem Erstellen der VM-Momentaufnahme und Post-Skripts unmittelbar nach dem Erstellen der VM-Momentaufnahme ausgeführt. Pre-Skripts und Post-Skripts ermöglichen es Ihnen, Ihre Anwendung und Umgebung beim Erstellen von VM-Momentaufnahmen flexibel zu steuern.

Pre-Skripts rufen APIs nativer Anwendungen auf, mit denen die E/A-Vorgänge stillgelegt werden und der Arbeitsspeicherinhalt auf den Datenträger geleert wird. Mit diesen Aktionen wird sichergestellt, dass die Momentaufnahme anwendungskonsistent ist. Für Post-Skripts werden APIs nativer Anwendungen genutzt, um die E/A-Vorgänge zu reaktivieren, damit die Anwendung nach der Erstellung der VM-Momentaufnahme wieder den normalen Betrieb aufnehmen kann.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Schritte zum Konfigurieren des Pre- und Post-Skripts

1. Melden Sie sich bei der zu sichernden Linux-VM als Root-Benutzer an.

2. Laden Sie von [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) die Datei **VMSnapshotScriptPluginConfig.json** herunter, und kopieren Sie sie für alle VMs, die Sie sichern möchten, in den Ordner **/etc/azure**. Erstellen Sie den Ordner **/etc/azure**, falls er nicht vorhanden ist.

3. Kopieren Sie das Pre-Skript und Post-Skript für Ihre Anwendung auf alle zu sichernden VMs. Sie können die Skripts in einem beliebigen Speicherort auf der VM kopieren. In der Datei **VMSnapshotScriptPluginConfig.json** müssen Sie den vollständigen Pfad der Skriptdateien aktualisieren.

4. Stellen Sie für diese Dateien die folgenden Berechtigungen sicher:

   - **VMSnapshotScriptPluginConfig.json:** Berechtigung „600“. Beispiel: Nur der Root-Benutzer darf die Berechtigungen „read“ und „write“ für diese Datei haben. Kein Benutzer darf die Berechtigung „execute“ haben.

   - **Pre-Skriptdatei:** Berechtigung „700“.  Beispiel: Nur der Root-Benutzer darf die Berechtigungen „read“, „write“ und „execute“ für diese Datei haben.

   - **Post-Skript:** Berechtigung „700“. Beispiel: Nur der Root-Benutzer darf die Berechtigungen „read“, „write“ und „execute“ für diese Datei haben.

   > [!Important]
   > Das Framework bietet Benutzern sehr viele Möglichkeiten. Schützen Sie das Framework, und stellen Sie sicher, dass nur der Benutzer „root“ Zugriff auf kritische JSON-Dateien und Skriptdateien hat.
   > Wenn die Anforderungen nicht erfüllt sind, wird das Skript nicht ausgeführt. Dies führt zu einem Absturz des Dateisystems und einer inkonsistenten Sicherung.
   >

5. Konfigurieren Sie **VMSnapshotScriptPluginConfig.json** entsprechend der folgenden Beschreibung:
    - **pluginName:** Lassen Sie dieses Feld unverändert, da die Skripts andernfalls ggf. nicht wie erwartet ausgeführt werden.

    - **preScriptLocation:** Geben Sie den vollständigen Pfad des Pre-Skripts auf der zu sichernden VM an.

    - **postScriptLocation:** Geben Sie den vollständigen Pfad des Post-Skripts auf der zu sichernden VM an.

    - **preScriptParams:** Geben Sie die optionalen Parameter an, die an das Pre-Skript übergeben werden müssen. Alle Parameter müssen in Anführungszeichen eingeschlossen werden. Trennen Sie die Parameter durch ein Komma, falls Sie mehrere Parameter verwenden.

    - **postScriptParams:** Geben Sie die optionalen Parameter an, die an das Post-Skript übergeben werden müssen. Alle Parameter müssen in Anführungszeichen eingeschlossen werden. Trennen Sie die Parameter durch ein Komma, falls Sie mehrere Parameter verwenden.

    - **preScriptNoOfRetries:** Legen Sie die Häufigkeit fest, mit der das Pre-Skript bei einem Fehler vor seinem Abschluss wiederholt werden soll. Null bedeutet nur einen Versuch und keine Wiederholung bei einem Fehler.

    - **postScriptNoOfRetries:**  Legen Sie die Häufigkeit fest, mit der das Post-Skript bei einem Fehler vor seinem Abschluss wiederholt werden soll. Null bedeutet nur einen Versuch und keine Wiederholung bei einem Fehler.

    - **timeoutInSeconds:** Geben Sie individuelle Timeouts für das Pre-Skript und das Post-Skript an (der Höchstwert beträgt 1800).

    - **continueBackupOnFailure:** Legen Sie diesen Wert auf **TRUE** fest, wenn Azure Backup bei einem Pre- oder Post-Skript-Fehler zu einer dateisystem- bzw. absturzkonsistenten Sicherung wechseln soll. Bei Festlegung auf **FALSE** misslingt die Sicherung bei einem Skriptfehler (außer bei einer VM mit einzelnem Datenträger, bei der unabhängig von dieser Einstellung auf eine absturzkonsistente Sicherung zurückgegriffen wird).

    - **fsFreezeEnabled:** Geben Sie an, ob unter Linux „fsfreeze“ aufgerufen werden soll, während die VM-Momentaufnahme erstellt wird, um Dateisystemkonsistenz sicherzustellen. Wir empfehlen die Beibehaltung dieser Einstellung auf **TRUE**, es sei denn, Ihre Anwendung ist von der Deaktivierung von „fsfreeze“ abhängig.

6. Das Skriptframework ist nun konfiguriert. Wenn die VM-Sicherung bereits konfiguriert ist, werden bei der nächsten Sicherung die Skripts aufgerufen und eine anwendungskonsistente Sicherung ausgelöst. Wenn die VM-Sicherung nicht konfiguriert ist, konfigurieren Sie sie entsprechend den Anweisungen unter [Sichern virtueller Azure-Computer in Recovery Services-Tresoren](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm).

## <a name="troubleshooting"></a>Problembehandlung

Fügen Sie beim Schreiben Ihres Pre-Skripts und Post-Skripts unbedingt eine entsprechende Protokollierung hinzu, und überprüfen Sie die Skriptprotokolle, um etwaige Skriptprobleme zu beheben. Wenn Sie bei der Ausführung von Skripts weiter Probleme haben sollten, konsultieren Sie die folgende Tabelle.

| Error | Fehlermeldung | Empfohlene Maßnahme |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Das Pre-Skript hat einen Fehler zurückgegeben, sodass die Sicherung gegebenenfalls nicht anwendungskonsistent ist.   | Untersuchen Sie die Fehlerprotokolle des Skripts, um das Problem zu beheben.|  
|   Post-ScriptExecutionFailed |    Das Post-Skript hat einen Fehler zurückgegeben, der sich auf den Anwendungszustand auswirken kann. |    Untersuchen Sie die Fehlerprotokolle des Skripts, um das Problem zu beheben, und überprüfen Sie den Anwendungszustand. |
| Pre-ScriptNotFound |  Das Pre-Skript wurde nicht am in der Konfigurationsdatei **VMSnapshotScriptPluginConfig.json** angegebenen Speicherort gefunden. |   Stellen Sie sicher, dass das Pre-Skript im Pfad vorhanden ist, der in der Konfigurationsdatei angegeben ist, um eine anwendungskonsistente Sicherung zu gewährleisten.|
| Post-ScriptNotFound | Das Post-Skript wurde nicht am in der Konfigurationsdatei **VMSnapshotScriptPluginConfig.json** angegebenen Speicherort gefunden. |   Stellen Sie sicher, dass das Post-Skript im Pfad vorhanden ist, der in der Konfigurationsdatei angegeben ist, um eine anwendungskonsistente Sicherung zu gewährleisten.|
| IncorrectPluginhostFile | Die zur Erweiterung „VmSnapshotLinux“ gehörige Datei **Pluginhost** ist beschädigt, sodass Pre-Skript und Post-Skript nicht ausgeführt werden können und die Sicherung nicht anwendungskonsistent sein wird. | Deinstallieren Sie die Erweiterung **VmSnapshotLinux**. Diese wird dann zum Beheben des Problems bei der nächsten Sicherung automatisch neu installiert. |
| IncorrectJSONConfigFile | Die Datei **VMSnapshotScriptPluginConfig.json** ist falsch, sodass Pre-Skript und Post-Skript nicht ausgeführt werden können und die Sicherung nicht anwendungskonsistent sein wird. | Laden Sie die Kopie von [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) herunter, und konfigurieren Sie sie erneut. |
| InsufficientPermissionforPre-Script | Zum Ausführen von Skripts muss der Root-Benutzer der Besitzer der Datei sein, und für die Datei muss die Berechtigung „700“ gelten (d.h., dass nur der Besitzer die Berechtigungen „read“, „write“ und „execute“ haben darf). | Stellen Sie sicher, dass der Root-Benutzer der Besitzer der Skriptdatei ist und dass nur der Besitzer die Berechtigungen „read“, „write“ und „execute“ hat. |
| InsufficientPermissionforPost-Skript | Zum Ausführen von Skripts muss der Root-Benutzer der Besitzer der Datei sein, und für die Datei muss die Berechtigung „700“ gelten (d.h., dass nur der Besitzer die Berechtigungen „read“, „write“ und „execute“ haben darf). | Stellen Sie sicher, dass der Root-Benutzer der Besitzer der Skriptdatei ist und dass nur der Besitzer die Berechtigungen „read“, „write“ und „execute“ hat. |
| Pre-ScriptTimeout | Bei Ausführung des Pre-Skripts für eine anwendungskonsistente Sicherung ist ein Timeout aufgetreten. | Überprüfen Sie das Skript, und erhöhen Sie den Timeoutwert in der Datei **VMSnapshotScriptPluginConfig.json** unter **/etc/azure**. |
| Post-ScriptTimeout | Bei Ausführung des Post-Skripts für eine anwendungskonsistente Sicherung ist ein Timeout aufgetreten. | Überprüfen Sie das Skript, und erhöhen Sie den Timeoutwert in der Datei **VMSnapshotScriptPluginConfig.json** unter **/etc/azure**. |

## <a name="next-steps"></a>Nächste Schritte
[Konfigurieren der VM-Sicherung in einem Recovery Services-Tresor](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
