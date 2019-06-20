---
title: Azure Backup Server schützt den Systemstatus und stellt Bare-Metal-Sicherungen wieder her
description: Verwenden Sie Azure Backup Server, um Ihren Systemstatus zu sichern und Bare-Metal-Recovery-Schutz (BMR) zu bieten.
services: backup
author: rayne-wiselman
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: raynew
ms.openlocfilehash: 35ab150670cdc27efcedca233928e0c2184aeca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116174"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Sichern des Systemstatus und Bare-Metal-Wiederherstellung mit Azure Backup Server

Azure Backup Server sichert den Systemstatus und bietet Bare-Metal-Recovery-Schutz (BMR).

*   **Systemstatussicherung**: Sichert Betriebssystemdateien, sodass eine Wiederherstellung erfolgen kann, wenn ein Computer gestartet wird, aber die Systemdateien und die Registrierung gehen verloren. Eine Systemstatussicherung umfasst Folgendes:
    * Domänenmitglied: Startdateien, COM+-Klassenregistrierungsdatenbank, Registrierung
    * Domänencontroller: Windows Server Active Directory (NTDS), Startdateien, COM+-Klassenregistrierungsdatenbank, Registrierung, Systemvolume (SYSVOL)
    * Computer, auf dem Clusterdienste ausgeführt werden: Metadaten des Clusterservers
    * Computer, auf dem Zertifikatdienste ausgeführt werden: Zertifikatdaten
* **Bare-Metal-Sicherung**: Sichert Betriebssystemdateien und allen Daten auf wichtigen Volumes (mit Ausnahme von Benutzerdaten). Laut Definition umfasst eine BMR-Sicherung eine Systemstatussicherung. Sie bietet Schutz, wenn ein Computer nicht gestartet werden kann und Sie alles wiederherstellen müssen.

In der folgenden Tabelle wird zusammengefasst, was Sie sichern und wiederherstellen können. Ausführliche Informationen zu App-Versionen, die mit der Systemstatus- und BMR-Sicherung geschützt werden können, finden Sie im Artikel dazu, [was mit Azure Backup Server gesichert wird](backup-mabs-protection-matrix.md).

|Backup|Problem|Wiederherstellung aus Azure Backup Server-Sicherung|Wiederherstellung aus einer Systemstatussicherung|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dateidaten**<br /><br />Reguläre Datensicherung<br /><br />BMR-/Systemstatussicherung|Verlust von Dateidaten|J|N|N|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Datenvolumes intakt)|N|N|J|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Verlust von Datenvolumes)|J|Nein|Ja (BMR, gefolgt von regulärer Wiederherstellung gesicherter Dateidaten)|
|**SharePoint-Daten**:<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Verlust von Website, Listen, Listenelementen, Dokumenten|J|N|N|
|**SharePoint-Daten**:<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|**SharePoint-Daten**:<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Notfallwiederherstellung|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust der VM|J|N|N|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust des Hyper-V-Hosts (virtuelle Computer intakt)|N|N|J|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust des Hyper-V-Hosts (Verlust virtueller Computer)|N|N|J<br /><br />BMR gefolgt von regulärer Azure Backup Server-Wiederherstellung|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust von Anwendungsdaten|J|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|y|J|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Datenbank-/Transaktionsprotokolle intakt)|N|N|J|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Verlust von Datenbank-/Transaktionsprotokollen)|N|N|J<br /><br />BMR-Wiederherstellung, gefolgt von regulärer Azure Backup Server-Wiederherstellung|

## <a name="how-system-state-backup-works"></a>Funktionsweise der Systemstatussicherung

Wenn eine Systemstatussicherung ausgeführt wird, kommuniziert Azure Backup Server mit der Windows Server-Sicherung und fordert eine Sicherung des Systemstatus des Servers an. Azure Backup Server und die Windows Server-Sicherung verwenden standardmäßig das Laufwerk, das über den meisten verfügbaren freien Speicherplatz verfügt. Informationen zu diesem Laufwerk werden in der Datei „psdatasourceconfig.xml“ gespeichert. Dies ist das Laufwerk, das die Windows Server-Sicherung für Sicherungen verwendet.

Sie können das Laufwerk anpassen, das Azure Backup Server für die Systemstatussicherung verwendet. Wechseln Sie auf dem geschützten Server zu „C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources“. Öffnen Sie die Datei „PSDataSourceConfig.xml“ zur Bearbeitung. Ändern Sie den Wert von \<FilesToProtect\> entsprechend dem Laufwerkbuchstaben. Speichern und schließen Sie die Datei. Wenn eine Schutzgruppe zum Schützen des Systemstatus des Computers festgelegt ist, führen Sie eine Konsistenzprüfung durch. Falls eine Warnung generiert wird, wählen Sie in der Warnung **Schutzgruppe ändern** aus, und schließen Sie den Assistenten. Führen Sie dann eine weitere Konsistenzprüfung aus.

Wenn sich der Schutzserver in einem Cluster befindet, wird möglicherweise ein Clusterlaufwerk als das Laufwerk mit dem meisten freien Speicherplatz ausgewählt. Wenn der Besitz dieses Laufwerks auf einen anderen Knoten umschaltet wurde und eine Systemstatussicherung erfolgt, steht dieses Laufwerk nicht zur Verfügung, weshalb die Sicherung misslingt. Ändern Sie in diesem Fall die Datei „psdatasourceconfig.xml“ so, dass auf ein lokales Laufwerk verwiesen wird.

Als Nächstes erstellt die Windows Server-Sicherung einen Ordner namens „WindowsImageBackup“ im Stammverzeichnis des Wiederherstellungsordners. Da die Windows Server-Sicherung die Sicherung erstellt, werden alle Daten in diesem Ordner abgelegt. Nach Abschluss der Sicherung wird die Datei auf den Backup Server-Computer übertragen. Beachten Sie die folgenden Informationen:

* Dieser Ordner und sein Inhalt werden nicht bereinigt, wenn die Sicherung oder Übertragung abgeschlossen ist. Sie stellen sich dies am besten als den Bereich vor, der für das nächste Mal, dass eine Sicherung abgeschlossen wird, reserviert wird.
* Jedes Mal, wenn eine Sicherung erfolgt, wird der Ordner erstellt. Uhrzeit- und Datumsstempel geben den Zeitpunkt der letzten Systemstatussicherung wieder.

## <a name="bmr-backup"></a>BMR-Sicherung

Für die BMR-Sicherung (die auch eine Systemstatussicherung einschließt) wird der Sicherungsauftrag direkt in einer Freigabe auf dem Backup Server-Computer gespeichert. Er wird nicht in einen Ordner auf dem geschützten Server gespeichert.

Backup Server ruft die Windows Server-Sicherung auf und gibt das Replikatvolume für diese BMR-Sicherung frei. In diesem Fall wird die Windows Server-Sicherung nicht angewiesen, das Laufwerk mit dem meisten freien Speicherplatz zu verwenden. Stattdessen wird die Freigabe genutzt, die für den Auftrag erstellt wurde.

Nach Abschluss der Sicherung wird die Datei auf den Backup Server-Computer übertragen. Protokolle werden in „C:\Windows\Logs\WindowsServerBackup“ gespeichert.

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

-   BMR wird nicht für Computer mit Windows Server 2003 oder Computer unterstützt, auf denen ein Clientbetriebssystem ausgeführt wird.

-   Sie können BMR und den Systemstatus nicht für denselben Computer in unterschiedlichen Schutzgruppen schützen.

-   Ein Backup Server-Computer kann nicht selbst für BMR schützen.

-   Kurzfristiger Schutz auf Band (D2T oder Datenträger zu Band) wird für BMR nicht unterstützt. Langfristige Speicherung auf Band (D2D2T oder Datenträger zu Datenträger zu Band) wird unterstützt.

-   Für den BMR-Schutz muss die Windows Server-Sicherung auf dem geschützten Computer installiert sein.

-   Für den BMR-Schutz gelten für Backup Server im Gegensatz zum Systemstatusschutz auf dem geschützten Computer keine Speicherplatzanforderungen. Von der Windows Server-Sicherung werden die Sicherungen direkt auf den Backup Server-Computer übertragen. Der Sicherungsübertragungsauftrag wird nicht in der Backup Server-Ansicht **Aufträge** angezeigt.

-   Backup Server reserviert für BMR 30 GB Speicherplatz auf dem Replikatvolume. Sie können dies auf der Seite **Datenträgerzuordnung** im Assistenten zum Ändern von Schutzgruppen oder mithilfe der PowerShell-Cmdlets „Get-DatasourceDiskAllocation“ und „Set-DatasourceDiskAllocation“ ändern. Auf dem Wiederherstellungspunktvolume erfordert der BMR-Schutz rund 6 GB bei einer Aufbewahrungsdauer von fünf Tagen.
    * Beachten Sie, dass die Replikatvolumegröße nicht auf weniger als 15 GB reduziert werden kann.
    * Backup Server berechnet nicht die Größe der BMR-Datenquelle. Es wird von 30 GB für alle Server ausgegangen. Ändern Sie den Wert basierend auf der Größe von BMR-Sicherungen, die Sie in Ihrer Umgebung erwarten. Die Größe einer BMR-Sicherung kann ungefähr als die Summe des belegten Speicherplatzes auf allen wichtigen Volumes berechnet werden. Wichtige Volumes = Startvolume + Systemvolume + Volume mit den Systemstatusdaten, wie z.B. Active Directory.

-   Wenn Sie vom Systemstatusschutz zum BMR-Schutz wechseln, erfordert der BMR-Schutz weniger Speicherplatz auf dem *Wiederherstellungspunktvolume*. Der zusätzliche Speicherplatz auf dem Volume wird allerdings nicht freigegeben. Sie können die Volumegröße auf der Seite **Datenträgerzuordnung ändern** im Assistenten zum Ändern von Schutzgruppen oder mithilfe der PowerShell-Cmdlets „Get-DatasourceDiskAllocation“ und „Set-DatasourceDiskAllocation“ manuell verkleinern.

    Wenn Sie vom Systemstatusschutz zum BMR-Schutz wechseln, erfordert der BMR-Schutz mehr Speicherplatz auf dem *Replikatvolume*. Das Volume wird automatisch erweitert. Wenn Sie die standardmäßige Speicherplatzzuordnung ändern möchten, verwenden Sie das PowerShell-Cmdlet „Modify-DiskAllocation“.

-   Wenn Sie vom BMR-Schutz zum Systemstatusschutz wechseln, benötigen Sie mehr Speicherplatz auf dem Wiederherstellungspunktvolume. Azure Backup Server versucht ggf., das Volume automatisch zu vergrößern. Wenn nicht genügend Speicherplatz im Speicherpool vorhanden ist, tritt ein Fehler auf.

    Wenn Sie vom BMR-Schutz zum Systemstatusschutz wechseln, benötigen Sie Speicherplatz auf dem geschützten Computer. Der Grund ist, dass der Systemstatusschutz das Replikat zuerst auf den lokalen Computer schreibt und anschließend auf den Backup Server-Computer überträgt.

## <a name="before-you-begin"></a>Voraussetzungen

1.  **Stellen Sie Azure Backup Server bereit**. Überprüfen Sie, ob Backup Server ordnungsgemäß bereitgestellt ist. Weitere Informationen finden Sie unter
    * [Systemanforderungen für Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Backup Server-Schutzmatrix](backup-mabs-protection-matrix.md)

2.  **Richten Sie Speicher ein**. Sie können mit Azure Sicherungsdaten auf Datenträger, auf Band und in der Cloud speichern. Weitere Informationen finden Sie unter [Vorbereiten des Datenspeichers](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Richten Sie den Schutz-Agent ein**. Installieren Sie den Schutz-Agent auf dem Computer, den Sie sichern möchten. Weitere Informationen finden Sie unter [Bereitstellen des DPM-Schutz-Agents](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Sichern von Systemstatus und BMR
Richten Sie gemäß den Anweisungen unter [Bereitstellen von Schutzgruppen](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups) eine Schutzgruppe ein. Sie können BMR und den Systemstatus nicht für denselben Computer in unterschiedlichen Gruppen schützen. Bei Wahl von BMR wird der Systemstatus automatisch aktiviert.


1.  Wählen Sie zum Öffnen des Assistenten zum Erstellen einer neuen Schutzgruppe in der Backup Server-Verwaltungskonsole **Schutz** > **Aktionen** > **Schutzgruppe erstellen** aus.

2.  Klicken Sie auf der Seite **Schutzgruppentyp auswählen** auf **Server** und dann auf **Weiter**.

3.  Erweitern Sie auf der Seite **Gruppenmitglieder auswählen** den Computer, und wählen Sie dann entweder **BMR** oder **Systemstatus** aus.

    Zur Erinnerung: Sie können BMR und den Systemstatus nicht für denselben Computer in unterschiedlichen Gruppen schützen. Bei Wahl von BMR wird der Systemstatus automatisch aktiviert. Weitere Informationen finden Sie unter [Bereitstellen von Schutzgruppen](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Wählen Sie auf der Seite **Datenschutzmethode auswählen** aus, wie Sie die kurz- und langfristige Sicherung handhaben möchten. Die kurzfristige Sicherung erfolgt immer zuerst auf Datenträger, mit der Option der Sicherung vom Datenträger in der Azure-Cloud mithilfe von Azure Backup (kurz- oder langfristig). Eine Alternative zur langfristigen Sicherung in der Cloud ist das Einrichten einer langfristigen Sicherung auf einem eigenständigen Bandgerät oder in einer Bandbibliothek, das bzw. die mit Backup Server verbunden ist.

5.  Wählen Sie auf der Seite **Kurzfristige Ziele auswählen** aus, wie die Sicherung in kurzfristigem Speicher auf Datenträger erfolgen soll:
    1. Wählen Sie für **Beibehaltungsdauer** aus, wie lange die Daten auf dem Datenträger beibehalten werden sollen. 
    2. Wählen Sie für **Synchronisierungsfrequenz** aus, wie oft eine inkrementelle Sicherung auf Datenträger erfolgen soll. Wenn Sie kein Intervall für die Sicherung festlegen möchten, können Sie die Option **Direkt vor einem Wiederherstellungspunkt** aktivieren. Backup Server führt eine vollständige Schnellsicherung unmittelbar vor jedem geplanten Wiederherstellungspunkt aus.

6.  Wenn Sie Daten langfristig auf Band speichern möchten, können Sie auf der Seite **Langfristige Ziele angeben** auswählen, wie lange Banddaten aufbewahrt werden sollen (1-99 Jahre). 
    1. Wählen Sie für **Sicherungshäufigkeit** aus, wie die Sicherung auf Band erfolgen soll. Die Häufigkeit basiert auf der Beibehaltungsdauer, die Sie ausgewählt haben:
        * Wenn die Beibehaltungsdauer 1-99 Jahre beträgt, können Sicherungen täglich, wöchentlich, alle zwei Wochen, monatlich, vierteljährlich, halbjährlich oder jährlich erfolgen.
        * Wenn die Beibehaltungsdauer 1-11 Monate beträgt, können Sicherungen täglich, wöchentlich, alle zwei Wochen oder monatlich erfolgen.
        * Wenn die Beibehaltungsdauer 1-4 Wochen beträgt, können Sicherungen täglich oder wöchentlich erfolgen.

    2. Wählen Sie auf der Seite **Band- und Bibliothekdetails auswählen** das zu verwendende Band oder die zu verwendende Bibliothek und ob Daten komprimiert und verschlüsselt werden sollen.

7.  Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der der Schutzgruppe zugeordnet ist.

    1. **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten.
    2. **Für Azure Backup Server bereitzustellender Speicherplatz** ist der Speicherplatz, den Backup Server für die Schutzgruppe empfiehlt. Backup Server wählt auf Grundlage der Einstellungen das ideale Sicherungsvolume. Sie können jedoch in **Details zur Datenträgerzuordnung** das gewählte Sicherungsvolume ändern. 
    3. Wählen Sie für Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die laut Vorschlag von Backup Server zum Sicherstellen reibungsloser Sicherung dem Volume hinzugefügt werden sollte.

8.  Wählen Sie auf der Seite **Replikaterstellungsmethode auswählen** aus, wie die erste vollständige Datenreplikation erfolgen soll. Wenn Sie sich für die Replikation über das Netzwerk entscheiden, sollten Sie hierfür eine Nebenzeit auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

9. Legen Sie auf dem Bildschirm **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen. Sie haben nur dann die Wahl der Ausführung einer Überprüfung, wenn Replikatdaten inkonsistent werden, oder gemäß einem Zeitplan. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen. Klicken Sie zum Ausführen einer manuellen Prüfung im Bereich **Schutz** der Backup Server-Administratorkonsole mit der rechten Maustaste auf die Schutzgruppe, und wählen Sie **Konsistenzprüfung ausführen** aus.

10. Wenn Sie die Sicherung in der Cloud mithilfe von Azure Backup ausgewählt haben, wählen Sie auf der Seite **Online zu schützende Daten angeben** die Workloads aus, die Sie in Azure sichern möchten.

11. Wählen Sie auf der Seite **Onlinesicherungszeitplan angeben** aus, wie oft inkrementelle Sicherungen in Azure erfolgen sollen. Sie können tägliche, wöchentliche, monatliche und jährliche Sicherungen planen und das Datum und die Uhrzeit ihrer Ausführung festlegen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung einer Sicherung wird ein Datenwiederherstellungspunkt in Azure anhand der Sicherungsdaten erstellt, die auf dem Backup Server-Datenträger gespeichert sind.

12. Wählen Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** aus, wie die Wiederherstellungspunkte in Azure aufbewahrt werden, die aus den täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen erstellt werden.

13. Wählen Sie auf der Seite **Onlinereplikation wählen** aus, wie die erste vollständige Replikation der Daten erfolgt. Sie können eine Replikation über das Netzwerk wählen oder eine Offlinesicherung (sog. Offlineseeding) durchführen. Die Offlinesicherung erfolgt mithilfe der Importfunktion von Azure. Weitere Informationen finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](backup-azure-backup-import-export.md).

14. Überprüfen Sie auf der Seite **Zusammenfassung** Ihre Einstellungen. Nach Auswahl von **Gruppe erstellen** erfolgt die erste Replikation der Daten. Nach Abschluss der Datenreplikation wird der Status der Schutzgruppe auf der Seite **Status** als **OK** angezeigt. Die Sicherung erfolgt anschließend gemäß den Schutzgruppeneinstellungen.

## <a name="recover-system-state-or-bmr"></a>Wiederherstellen des Systemstatus oder BMR
Sie können die BMR oder den Systemstatus an einem Speicherort im Netzwerk wiederherstellen. Wenn Sie BMR gesichert haben, verwenden Sie die Windows-Wiederherstellungsumgebung (WinRE), um das System zu starten und es mit dem Netzwerk zu verbinden. Verwenden Sie anschließend die Windows Server-Sicherung für die Wiederherstellung aus der Netzwerkadresse. Wenn Sie den Systemstatus gesichert haben, verwenden Sie lediglich die Windows Server-Sicherung für die Wiederherstellung aus der Netzwerkadresse.

### <a name="restore-bmr"></a>Wiederherstellen von BMR
Führen Sie die Wiederherstellung auf dem Backup Server-Computer aus:

1.  Suchen Sie im Bereich **Wiederherstellung** den Computer, den Sie wiederherstellen möchten, und wählen Sie dann **Bare-Metal-Recovery** aus.

2.  Verfügbare Wiederherstellungspunkte sind fett im Kalender formatiert. Wählen Sie Datum und Uhrzeit des Wiederherstellungspunkts aus, den Sie verwenden möchten.

3.  Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **In einen Netzwerkordner kopieren** aus.

4.  Wählen Sie auf der Seite **Ziel angeben** aus, wohin die Daten kopiert werden sollen. Denken Sie daran, dass das ausgewählte Ziel über ausreichend Speicherplatz verfügen muss. Es wird empfohlen, einen neuen Ordner zu erstellen.

5.  Wählen Sie auf der Seite **Wiederherstellungsoptionen angeben** die Sicherheitseinstellungen aus, die gelten sollen. Wählen Sie dann, ob Sie für eine schnellere Wiederherstellung SAN-basierte (Storage Area Network) Hardwaresnapshots verwenden möchten. (Dies ist nur eine Option, wenn Sie über ein SAN mit dieser Funktionalität und der Möglichkeit zum Erstellen und Teilen eines Klons verfügen, um ihn beschreibbar zu machen. Darüber hinaus müssen der geschützte Computer und Backup Server-Computer mit dem gleichen Netzwerk verbunden sein.)

6.  Richten Sie Benachrichtigungsoptionen ein. Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**.

Richten Sie den Speicherort der Freigabe ein:

1.  Wechseln Sie am Wiederherstellungsspeicherort zum Ordner mit der Sicherung.

2.  Geben Sie den Ordner frei, der sich eine Ebene über „WindowsImageBackup“ befindet, damit der Stamm des freigegebenen Ordners der Ordner „WindowsImageBackup“ ist. Wenn Sie dies nicht tun, kann die Wiederherstellung die Sicherung nicht finden. Um eine Verbindung mithilfe der Windows-Wiederherstellungsumgebung (WinRE) herzustellen, benötigen Sie eine Freigabe, auf die Sie in WinRE mit der richtigen IP-Adresse und den richtigen Anmeldeinformationen zugreifen können.

Stellen Sie das System wieder her:

1.  Starten Sie den Computer, auf dem Sie das Abbild mithilfe der Windows-DVD für das wiederherzustellende System wiederherstellen möchten.

2.  Überprüfen Sie auf der ersten Seite Sprach- und Gebietsschemaeinstellungen. Wählen Sie auf der Seite **Installieren** die Option **Computer reparieren** aus.

3.  Wählen Sie auf der Seite **Systemwiederherstellungsoptionen** die Option **Stellen Sie den Computer mithilfe eines zuvor erstellten Systemabbilds wieder her**.

4.  Wählen Sie auf der Seite **Systemabbildsicherung auswählen** nacheinander **Systemabbild auswählen** > **Erweitert** > **Im Netzwerk nach einem Systemabbild suchen**. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja**. Wechseln Sie zum Freigabepfad, geben Sie die Anmeldeinformationen ein, und wählen Sie dann den Wiederherstellungspunkt aus. Dadurch wird nach bestimmten Sicherungen gesucht, die an diesem Wiederherstellungspunkt verfügbar sind. Wählen Sie den gewünschten Wiederherstellungspunkt aus.

5.  Wählen Sie auf der Seite **Art der Wiederherstellung wählen** die Option **Datenträger formatieren und neu partitionieren** aus. Überprüfen Sie auf der nächsten Seite die Einstellungen. 

6.  Um die Wiederherstellung zu starten, klicken Sie auf **Fertig stellen**. Ein Neustart ist erforderlich.

### <a name="restore-system-state"></a>Wiederherstellen des Systemstatus

Führen Sie die Wiederherstellung in Backup Server aus:

1.  Suchen Sie im Bereich **Wiederherstellung** den Computer, den Sie wiederherstellen möchten, und wählen Sie dann **Bare-Metal-Recovery** aus.

2.  Verfügbare Wiederherstellungspunkte sind fett im Kalender formatiert. Wählen Sie Datum und Uhrzeit des Wiederherstellungspunkts aus, den Sie verwenden möchten.

3.  Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **In einen Netzwerkordner kopieren** aus.

4.  Wählen Sie auf der Seite **Ziel angeben** aus, wohin die Daten kopiert werden sollen. Denken Sie daran, dass das ausgewählte Ziel über ausreichend Speicherplatz verfügen muss. Es wird empfohlen, einen neuen Ordner zu erstellen.

5.  Wählen Sie auf der Seite **Wiederherstellungsoptionen angeben** die Sicherheitseinstellungen aus, die gelten sollen. Wählen Sie dann, ob Sie für eine schnellere Wiederherstellung SAN-basierte (Storage Area Network) Hardwaresnapshots verwenden möchten. (Dies ist nur eine Option, wenn Sie über ein SAN mit dieser Funktionalität und der Möglichkeit zum Erstellen und Teilen eines Klons verfügen, um ihn beschreibbar zu machen. Darüber hinaus müssen der geschützte Computer und Backup Server-Servercomputer mit dem gleichen Netzwerk verbunden sein.)

6.  Richten Sie Benachrichtigungsoptionen ein. Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**.

Führen Sie die Windows Server-Sicherung aus:

1.  Wählen Sie **Aktionen** > **Wiederherstellen** > **Dieser Server** > **Weiter** aus.

2.  Wählen Sie **Einen anderen Server**, die Seite **Speicherorttyp angeben** und dann **Freigegebener Remoteordner** aus. Geben Sie den Pfad zum Ordner ein, der den Wiederherstellungspunkt enthält.

3.  Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **Systemstatus** aus. 

4. Wählen Sie auf der Seite **Speicherort für Wiederherstellung des Systemstatus auswählen** die Option **Ursprungsspeicherort** aus.

5.  Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**. Starten Sie den Server nach der Wiederherstellung neu.

6.  Sie können die Systemstatuswiederherstellung auch an einer Eingabeaufforderung ausführen. Starten Sie hierzu die Windows Server-Sicherung auf dem Computer, den Sie wiederherstellen möchten. Geben Sie zum Abrufen des Versionsbezeichners an der Eingabeaufforderung Folgendes ein: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Verwenden Sie den Versionsbezeichner zum Starten der Systemstatuswiederherstellung. Geben Sie an der Eingabeaufforderung Folgendes ein: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Bestätigen Sie, dass Sie die Wiederherstellung starten möchten. Der Prozess wird im Eingabeaufforderungsfenster angezeigt. Ein Wiederherstellungsprotokoll wird erstellt. Starten Sie den Server nach der Wiederherstellung neu.

