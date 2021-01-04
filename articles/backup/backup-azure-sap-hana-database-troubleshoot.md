---
title: Problembehandlung bei Sicherungsfehlern in SAP HANA-Datenbanken
description: Beschreibt, wie häufige Fehler behoben werden, die auftreten können, wenn Sie SAP HANA-Datenbanken mithilfe von Azure Backup sichern.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: b9fa73ee38e337a547816432212bc68d419f40bb
ms.sourcegitcommit: 1d366d72357db47feaea20c54004dc4467391364
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95411324"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Behandeln von Problemen beim Sichern von SAP HANA-Datenbanken in Azure

Dieser Artikel enthält Informationen zur Problembehandlung beim Sichern von SAP HANA-Datenbanken in Azure Virtual Machines. Weitere Informationen zu den derzeit unterstützten SAP HANA-Sicherungsszenarien finden Sie unter [Unterstützung von Szenarien](sap-hana-backup-support-matrix.md#scenario-support).

## <a name="prerequisites-and-permissions"></a>Voraussetzungen und Berechtigungen

Informationen finden Sie in den Abschnitten zu den [Voraussetzungen](tutorial-backup-sap-hana-db.md#prerequisites) und [Aufgaben des Vorregistrierungsskripts](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) vor dem Konfigurieren von Sicherungen.

## <a name="common-user-errors"></a>Häufige Benutzerfehler

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **Fehlermeldung**      | <span style="font-weight:normal">Azure Backup hat nicht die erforderlichen Rollenberechtigungen zum Ausführen der Sicherung</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Die Rolle wurde möglicherweise überschrieben.                          |
| **Empfohlene Maßnahme** | Um das Problem zu beheben, führen Sie das Skript im Bereich **DBs ermitteln** aus, oder laden Sie es [hier](https://aka.ms/scriptforpermsonhana) herunter. Fügen Sie alternativ die Rolle SAP_INTERNAL_HANA_SUPPORT dem Benutzer für die Workloadsicherung (AZUREWLBACKUPHANAUSER) hinzu. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| Fehlermeldung      | <span style="font-weight:normal">Fehler beim Herstellen der Verbindung mit dem HANA-System</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Die SAP HANA-Instanz ist möglicherweise ausgefallen.<br/>Die erforderlichen Berechtigungen für die Interaktion von Azure Backup mit der Hana-Datenbank sind nicht festgelegt. |
| **Empfohlene Maßnahme** | Überprüfen Sie, ob die SAP HANA-Datenbank aktiv ist. Wenn die Datenbank ausgeführt wird, überprüfen Sie, ob alle erforderlichen Berechtigungen festgelegt sind. Wenn Berechtigungen nicht vorhanden sind, führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) aus, um die fehlenden Berechtigungen hinzuzufügen. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| Fehlermeldung      | <span style="font-weight:normal">Die angegebene SAP HANA-Instanz ist entweder ungültig oder wurde nicht gefunden.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Es können nicht mehrere SAP HANA-Instanzen auf einem einzelnen virtuellen Azure-Computer gesichert werden. |
| **Empfohlene Maßnahme** | Führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) auf der SAP HANA-Instanz aus, die gesichert werden soll. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft-Support. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| Fehlermeldung      | <span style="font-weight:normal">Der angegebene SAP HANA-Vorgang wird nicht unterstützt.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Azure Backup für SAP HANA unterstützt keine inkrementellen Sicherungen und keine Aktionen, die für SAP HANA-native Clients (Studio/Cockpit/DBA Cockpit) ausgeführt werden. |
| **Empfohlene Maßnahme** | Weitere Informationen finden Sie [hier](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| Fehlermeldung      | <span style="font-weight:normal">Diese SAP HANA-Datenbank unterstützt nicht den angeforderten Sicherungstyp.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Azure Backup unterstützt keine inkrementellen Sicherungen und keine Sicherungen mit Momentaufnahmen. |
| **Empfohlene Maßnahme** | Weitere Informationen finden Sie [hier](./sap-hana-backup-support-matrix.md#scenario-support). |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| Fehlermeldung      | <span style="font-weight:normal">Die Sicherungsprotokollkette ist unterbrochen.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Das Protokollsicherungsziel wurde möglicherweise von backint in das Dateisystem geändert, oder die ausführbare backint-Datei wurde geändert. |
| **Empfohlene Maßnahme** | Lösen Sie eine vollständige Sicherung aus, um das Problem zu beheben.                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| Fehlermeldung      | <span style="font-weight:normal">Upgrade von SDC auf MDC erkannt.</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Es wurde ein Upgrade der SAP HANA-Instanz von SDC auf MDC ausgeführt. Sicherungen können nach dem Upgrade nicht mehr ausgeführt werden. |
| **Empfohlene Maßnahme** | Führen Sie die unter [Upgrade von SDC zu MDC ](#sdc-to-mdc-upgrade-with-a-change-in-sid) aufgeführten Schritte aus, um das Problem zu beheben. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| Fehlermeldung      | <span style="font-weight:normal">Ungültige backint-Konfiguration erkannt.</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **Mögliche Ursachen**    | Die Sicherungsparameter sind nicht ordnungsgemäß für Azure Backup angegeben. |
| **Empfohlene Maßnahme** | Überprüfen Sie, ob die folgenden backint-Parameter festgelegt sind:<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>Wenn in HOST backint-Parameter vorhanden sind, entfernen Sie sie. Wenn Parameter auf HOST-Ebene nicht vorhanden sind, aber auf Datenbankebene manuell geändert wurden, setzen Sie sie wie oben beschrieben auf die entsprechenden Werte zurück. Oder führen Sie im Azure-Portal [Schutz beenden und Sicherungsdaten beibehalten](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) aus, und wählen Sie dann **Sicherung fortsetzen** aus. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|Fehlermeldung  |Das Quell- und das Zielsystem für die Wiederherstellung sind nicht kompatibel.  |
|---------|---------|
|Mögliche Ursachen   | Das ausgewählte Quellsystem und das ausgewählte Zielsystem für die Wiederherstellung sind nicht kompatibel.        |
|Empfohlene Maßnahme   |   Stellen Sie sicher, dass das Wiederherstellungsszenario nicht in der folgenden Liste möglicher inkompatibler Wiederherstellungen enthalten ist: <br><br>   **Fall 1:** SYSTEMDB kann während der Wiederherstellung nicht umbenannt werden.  <br><br> **Fall 2:** Quelle = SDC und Ziel = MDC: Die Quelldatenbank kann auf dem Ziel nicht als SYSTEMDB oder Mandanten-DB wiederhergestellt werden. <br><br> **Fall 3:** Quelle = MDC und Ziel = SDC: Die Quelldatenbank (SYSTEMDB oder Mandanten-DB) kann auf dem Ziel nicht wiederhergestellt werden. <br><br>  Weitere Informationen finden Sie in Hinweis **1642148** im [SAP Support Launchpad](https://launchpad.support.sap.com). |

## <a name="restore-checks"></a>Wiederherstellungsprüfungen

### <a name="single-container-database-sdc-restore"></a>Wiederherstellung einer Datenbank mit einem einzelnen Container (SDC)

Achten Sie auf Eingaben während der Wiederherstellung einer Datenbank mit einem einzelnen Container (SDC) für HANA auf einen anderen SDC-Computer. Der Datenbankname sollte in Kleinbuchstaben angegeben werden, wobei „sdc“ in Klammern angefügt wird. Die HANA-Instanz wird in Großbuchstaben angezeigt.

Angenommen, eine SDC HANA-Instanz „H21“ wird gesichert. Auf der Seite mit den Sicherungselementen wird der Sicherungselementname **„h21(sdc)“** angezeigt. Wenn Sie versuchen, diese Datenbank auf einem anderen Ziel-SDC wiederherzustellen (z. B. „H11“), müssen Sie die folgenden Eingaben bereitstellen:

![Name der wiederhergestellten SDC-Datenbank](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

Beachten Sie folgende Punkte:

- Standardmäßig wird der Name der wiederhergestellten Datenbank mit dem Namen des Sicherungselements aufgefüllt. In diesem Fall: „h21(sdc)“.
- Wenn Sie als Ziel „H11“ auswählen, wird der Name der wiederhergestellten Datenbank nicht automatisch geändert. **Er sollte in „h11(sdc)“ geändert werden**. Im Fall von SDC ist der Name der wiederhergestellten Datenbank die ID der Zielinstanz in Kleinbuchstaben, an die „sdc“ in Klammern angefügt wird.
- Da SDC nur über eine einzelne Datenbank verfügen kann, müssen Sie auch das Kontrollkästchen aktivieren, um das Überschreiben der vorhandenen Datenbankdaten mit den Daten des Wiederherstellungspunkts zuzulassen.
- Bei Linux muss die Groß-/Kleinschreibung beachtet werden. Verwenden Sie daher unbedingt die korrekte Groß-/Kleinschreibung.

### <a name="multiple-container-database-mdc-restore"></a>Wiederherstellung einer Datenbank mit mehreren Containern (MDC)

In Datenbanken mit mehreren Containern (MDC) für HANA ist die Standardkonfiguration „SYSTEMDB + 1 oder mehr Mandantendatenbanken“. Das Wiederherstellen einer vollständigen SAP HANA-Instanz bedeutet, dass sowohl SYSTEMDB als auch die Mandantendatenbanken wiederhergestellt werden. Zuerst wird SYSTEMDB wiederhergestellt, und dann wird die Wiederherstellung für die Mandantendatenbank fortgesetzt. Systemdatenbank bedeutet im Wesentlichen, dass die Systeminformationen für das ausgewählte Ziel überschrieben werden. Bei dieser Wiederherstellung werden auch die BackInt-bezogenen Informationen in der Zielinstanz überschrieben. Nachdem die Systemdatenbank auf einer Zielinstanz wiederhergestellt wurde, führen Sie das Vorregistrierungsskript erneut aus. Nur dann ist die nachfolgende Wiederherstellung der Mandantendatenbanken erfolgreich.

## <a name="back-up-a-replicated-vm"></a>Sichern eines replizierten virtuellen Computers

### <a name="scenario-1"></a>Szenario 1

Der ursprüngliche virtuelle Computer wurde mithilfe von Azure Site Recovery oder einer Azure-VM-Sicherung repliziert. Der neue virtuelle Computer wurde erstellt, um den alten virtuellen Computer zu simulieren. Das heißt, die Einstellungen sind identisch. (Das liegt daran, dass der ursprüngliche virtuelle Computer gelöscht wurde und die Wiederherstellung aus der VM-Sicherung oder über Azure Site Recovery erfolgte).

Für dieses Szenario gibt es zwei mögliche Anwendungsfälle. Erfahren Sie, wie Sie den replizierten virtuellen Computer in diesen beiden Fällen sichern:

1. Der neu erstellte virtuelle Computer hat den gleichen Namen und befindet sich in der gleichen Ressourcengruppe und im gleichen Abonnement wie der gelöschte virtuelle Computer.

    - Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
    - Führen Sie das Vorregistrierungsskript aus.
    - Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
    - Die Sicherung der bereits vorhandenen gesicherten Datenbanken (vom gelöschten virtuellen Computer) sollte dann erfolgreich gestartet werden.

2. Der neu erstellte virtuelle Computer hat entweder:

    - einen anderen Name als der gelöschte virtuelle Computer
    - oder den gleichen Namen wie der gelöschte virtuelle Computers, befindet sich jedoch in einer anderen Ressourcengruppe bzw. einem anderen Abonnement als der gelöschte virtuellen Computer

    Ist dies der Fall, führen Sie die folgenden Schritte aus:

    - Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
    - Führen Sie das Vorregistrierungsskript aus.
    - Wenn Sie die neuen Datenbanken ermitteln und schützen, werden im Portal doppelte aktive Datenbanken angezeigt. Um dies zu vermeiden, [beenden Sie den Schutz mit Beibehaltung der Daten](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) für die alten Datenbanken. Fahren Sie dann mit den verbleibenden Schritten fort.
    - Ermitteln Sie die Datenbanken zum Aktivieren der Sicherung.
    - Aktivieren Sie Sicherungen für diese Datenbanken.
    - Die bereits vorhandenen gesicherten Datenbanken (vom gelöschten virtuellen Computer) werden weiterhin im Tresor gespeichert (ihre Sicherungen werden gemäß der Richtlinie aufbewahrt).

### <a name="scenario-2"></a>Szenario 2

Der ursprüngliche virtuelle Computer wurde mithilfe von Azure Site Recovery oder einer Azure-VM-Sicherung repliziert. Der neue virtuelle Computer wurde aus dem Inhalt erstellt, das als Vorlage verwendet werden soll. Dabei handelt es sich um einen neuen virtuellen Computer mit einer neuen SID.

Führen Sie die folgenden Schritte aus, um Sicherungen auf dem neuen virtuellen Computer zu aktivieren:

- Die Erweiterung ist auf dem virtuellen Computer bereits vorhanden, ist aber für keinen der Dienste sichtbar.
- Führen Sie das Vorregistrierungsskript aus. Basierend auf der SID des neuen virtuellen Computers sind zwei Szenarien denkbar:
  - Der ursprüngliche virtuelle Computer und der neue virtuelle Computer haben dieselbe SID. Das Vorregistrierungsskript wird erfolgreich ausgeführt.
  - Der ursprüngliche virtuelle Computer und der neue virtuelle Computer haben unterschiedliche SIDs. Das Vorregistrierungsskript schlägt fehl. Kontaktieren Sie den Support, um Hilfe in diesem Szenario zu erhalten.
- Ermitteln der Datenbanken, die Sie sichern möchten
- Aktivieren der Sicherungen für diese Datenbanken

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>Upgrade der SDC-Version oder Upgrade der MDC-Version auf demselben virtuellen Computer

Upgrades des Betriebssystems oder Änderungen der SDC- oder MDC-Version, die keine SID-Änderung auslösen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue Betriebssystem-, SDC- oder MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- [Beenden Sie den Schutz](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) mit Beibehaltung der Daten für die Datenbank.
- Führen Sie das Upgrade oder Update durch.
- Führen Sie das Vorregistrierungsskript erneut aus. Beim Upgradeprozess werden häufig die [erforderlichen Rollen](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) entfernt. Durch Ausführen des Vorregistrierungsskripts werden alle erforderlichen Rollen überprüft.
- Setzen Sie den Schutz für die Datenbank fort.

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>Upgrade von SDC zu MDC ohne SID-Änderung

Upgrades von SDC auf MDC, die keine SID-Änderung verursachen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- [Beenden Sie den Schutz mit Beibehaltung der Daten](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) für die alte SDC-Datenbank.
- Führen Sie das Upgrade durch. Nach Abschluss des Vorgangs ist die HANA-System nun eine MDC-Instanz mit einer Systemdatenbank und einer Mandantendatenbank.
- Führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) erneut aus.
- Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
- Wählen Sie **Datenbanken neu ermitteln** für denselben virtuellen Computer aus. Durch diese Aktion sollten die neuen Datenbanken in Schritt 3 als SYSTEMDB und Mandantendatenbank (nicht SDC) angezeigt werden.
- Die ältere SDC-Datenbank ist weiterhin im Tresor vorhanden, und die alten gesicherten Daten werden gemäß der Richtlinie aufbewahrt.
- Konfigurieren der Sicherung für diese Datenbanken

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>Upgrade von SDC zu MDC mit SID-Änderung

Upgrades von SDC auf MDC, die eine SID-Änderung verursachen, können wie folgt behandelt werden:

- Stellen Sie sicher, dass die neue MDC-Version aktuell von [Azure Backup unterstützt wird](sap-hana-backup-support-matrix.md#scenario-support).
- **Beenden Sie den Schutz mit Beibehaltung der Daten** für die alte SDC-Datenbank.
- Führen Sie das Upgrade durch. Nach Abschluss des Vorgangs ist die HANA-System nun eine MDC-Instanz mit einer Systemdatenbank und einer Mandantendatenbank.
- Führen Sie das [Vorregistrierungsskript](https://aka.ms/scriptforpermsonhana) mit den richtigen Details (neue SID und MDC) erneut aus. Aufgrund einer Änderung der SID treten möglicherweise Probleme bei der Ausführung des Skripts auf. Wenden Sie sich an Azure Backup-Support, wenn Probleme auftreten.
- Registrieren Sie die Erweiterung im Azure-Portal erneut für den gleichen Computer (**Sicherung** -> **Details anzeigen** -> entsprechende Azure-VM auswählen -> erneut registrieren).
- Wählen Sie **Datenbanken neu ermitteln** für denselben virtuellen Computer aus. Durch diese Aktion sollten die neuen Datenbanken in Schritt 3 als SYSTEMDB und Mandantendatenbank (nicht SDC) angezeigt werden.
- Die ältere SDC-Datenbank ist weiterhin im Tresor vorhanden, und die alten gesicherten Daten werden gemäß der Richtlinie aufbewahrt.
- Konfigurieren der Sicherung für diese Datenbanken

## <a name="re-registration-failures"></a>Fehler bei der erneuten Registrierung

Überprüfen Sie vor dem Auslösen der erneuten Registrierung, ob ein oder mehrere der folgenden Symptome vorhanden sind:

- Für alle Vorgänge (z.B. Sicherung, Wiederherstellung und Sicherungskonfiguration) tritt auf der VM ein Fehler mit einem der folgenden Fehlercodes auf: **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- Wenn im Bereich **Sicherungsstatus** für das Sicherungselement der Status **Nicht erreichbar** angezeigt wird, schließen Sie alle anderen Gründe aus, die zum gleichen Status führen könnten:

  - Fehlende Berechtigung zur Durchführung von sicherungsbezogenen Vorgängen auf der VM
  - Herunterfahren des virtuellen Computers, sodass die Sicherungen nicht durchgeführt werden können
  - Netzwerkfehler

Zu diesen Symptomen kann es aufgrund von einer oder mehreren der folgenden Ursachen kommen:

- Eine Erweiterung wurde gelöscht oder im Portal deinstalliert.
- Die VM wurde über eine direkte Datenträgerwiederherstellung rechtzeitig wiederhergestellt.
- Die VM wurde für längere Zeit heruntergefahren, sodass die Erweiterungskonfiguration dafür abgelaufen ist.
- Die VM wurde gelöscht, und eine andere VM wurde mit dem gleichen Namen und in derselben Ressourcengruppe wie die gelöschte VM erstellt.

In den vorhergehenden Szenarien sollten Sie einen Neuregistrierungsvorgang auf der VM auszulösen.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [häufig gestellten Fragen](./sap-hana-faq-backup-azure-vm.md) zum Sichern von SAP HANA-Datenbanken auf Azure-VMs an.
