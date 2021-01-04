---
title: Systemstatus- und Bare-Metal-Recovery-Schutz
description: Verwenden Sie Azure Backup Server, um Ihren Systemstatus zu sichern und BMR-Schutz (Bare-Metal-Recovery) bereitzustellen.
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021621"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Sichern des Systemstatus und Durchführen einer Bare-Metal-Wiederherstellung mithilfe von Azure Backup Server

Azure Backup Server sichert den Systemstatus und bietet Bare-Metal-Recovery-Schutz (BMR).

* **Systemstatussicherung**: Sichert Betriebssystemdateien. Diese Sicherung ermöglicht die Wiederherstellung, wenn ein Computer gestartet wird, aber Systemdateien und die Registrierung fehlen. Eine Systemstatussicherung umfasst folgende Elemente:
  * Domänenmitglied: Startdateien, COM+-Klassenregistrierungsdatenbank, Registrierung
  * Domänencontroller: Windows Server Active Directory (NTDS), Startdateien, COM+-Klassenregistrierungsdatenbank, Registrierung, Systemvolume (SYSVOL)
  * Computer, auf dem Clusterdienste ausgeführt werden: Metadaten des Clusterservers
  * Computer, auf dem Zertifikatdienste ausgeführt werden: Zertifikatdaten
* **Bare-Metal-Sicherung**: Sichert Betriebssystemdateien und alle Daten auf wichtigen Volumes mit Ausnahme von Benutzerdaten. Laut Definition umfasst eine BMR-Sicherung eine Systemstatussicherung. Sie bietet Schutz, wenn ein Computer nicht gestartet werden kann und Sie alles wiederherstellen müssen.

In der folgenden Tabelle wird zusammengefasst, was Sie sichern und wiederherstellen können. Ausführliche Informationen zu App-Versionen, die mittels Systemstatus und BMR geschützt werden können, finden Sie in der [Azure Backup Server-Schutzmatrix](backup-mabs-protection-matrix.md).

|Backup|Problem|Wiederherstellung aus Azure Backup Server-Sicherung|Wiederherstellung aus einer Systemstatussicherung|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Dateidaten**<br /><br />Reguläre Datensicherung<br /><br />BMR-/Systemstatussicherung|Verlust von Dateidaten|J|N|N|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Datenvolumes intakt)|N|N|J|
|**Dateidaten**<br /><br />Azure Backup Server-Sicherung von Dateidaten<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Verlust von Datenvolumes)|J|N|J<br /><br />BMR, gefolgt von regulärer Wiederherstellung gesicherter Dateidaten|
|**SharePoint-Daten**<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Verlust von Website, Listen, Listenelementen, Dokumenten|J|N|N|
|**SharePoint-Daten**<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|**SharePoint-Daten**<br /><br />Azure Backup Server-Sicherung von Farmdaten<br /><br />BMR-/Systemstatussicherung|Notfallwiederherstellung|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust der VM|J|N|N|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust des Hyper-V-Hosts (virtuelle Computer intakt)|N|N|J|
|Hyper-V<br /><br />Azure Backup Server-Sicherung von Hyper-V-Host oder -Gast<br /><br />BMR-/Systemstatussicherung des Hosts|Verlust des Hyper-V-Hosts (Verlust virtueller Computer)|N|N|J<br /><br />BMR gefolgt von regulärer Azure Backup Server-Wiederherstellung|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust von Anwendungsdaten|J|N|N|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust oder Beschädigung des Betriebssystems|N|J|J|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Datenbank-/Transaktionsprotokolle intakt)|N|N|J|
|SQL Server/Exchange<br /><br />Azure Backup Server-App-Sicherung<br /><br />BMR-/Systemstatussicherung|Verlust eines Servers (Verlust von Datenbank-/Transaktionsprotokollen)|N|N|J<br /><br />BMR-Wiederherstellung, gefolgt von regulärer Azure Backup Server-Wiederherstellung|

## <a name="how-system-state-backup-works"></a>Funktionsweise der Systemstatussicherung

Wenn eine Systemstatussicherung ausgeführt wird, kommuniziert Azure Backup Server mit der Windows Server-Sicherung und fordert eine Sicherung des Systemstatus des Servers an. Azure Backup Server und die Windows Server-Sicherung verwenden standardmäßig das Laufwerk, das über den meisten verfügbaren freien Speicherplatz verfügt. Informationen zu diesem Laufwerk werden in der Datei *PSDataSourceConfig.xml* gespeichert.

Das Laufwerk, das von Azure Backup Server für die Systemstatussicherung verwendet wird, kann wie folgt angepasst werden:

1. Navigieren Sie auf dem geschützten Server zu *C:\Programme\Microsoft Data Protection Manager\MABS\Datasources*.
1. Öffnen Sie die Datei *PSDataSourceConfig.xml*, um sie zu bearbeiten.
1. Ändern Sie den Wert \<FilesToProtect\> für den Laufwerksbuchstaben.
1. Speichern und schließen Sie die Datei.

Wenn eine Schutzgruppe zum Schützen des Systemstatus des Computers festgelegt ist, führen Sie eine Konsistenzprüfung durch. Sollte eine Warnung generiert werden, wählen Sie in der Warnung **Schutzgruppe ändern** aus, und durchlaufen Sie anschließend die Seiten des Assistenten. Führen Sie danach eine weitere Konsistenzprüfung aus.

Befindet sich der Schutzserver in einem Cluster, wird als Laufwerk mit dem meisten freien Speicherplatz unter Umständen ein Clusterlaufwerk ausgewählt. Wenn der Besitz dieses Laufwerks auf einen anderen Knoten übergeht und eine Systemstatussicherung ausgeführt wird, steht das Laufwerk nicht mehr zur Verfügung, und die Sicherung ist nicht erfolgreich. Ändern Sie in diesem Fall die Datei *PSDataSourceConfig.xml* so, dass auf ein lokales Laufwerk verwiesen wird.

Als Nächstes erstellt die Windows Server-Sicherung einen Ordner namens *WindowsImageBackup* im Stammverzeichnis des Wiederherstellungsordners. Da die Windows Server-Sicherung die Sicherung erstellt, werden alle Daten in diesem Ordner abgelegt. Nach Abschluss der Sicherung wird die Datei auf den Backup Server-Computer übertragen. Beachten Sie die folgenden Informationen:

* Dieser Ordner und sein Inhalt werden nach Abschluss der Sicherung oder Übertragung nicht bereinigt. Stellen Sie sich diesen Bereich am besten als reservierten Bereich für die nächste Sicherung vor.
* Der Ordner wird für jede Sicherung erstellt. Uhrzeit- und Datumsstempel geben den Zeitpunkt der letzten Systemstatussicherung wieder.

## <a name="how-bmr-backup-works"></a>Funktionsweise der BMR-Sicherung

Für die BMR-Sicherung (die auch eine Systemstatussicherung einschließt) wird der Sicherungsauftrag direkt in einer Freigabe auf dem Backup Server-Computer gespeichert. Er wird nicht in einem Ordner auf dem geschützten Server gespeichert.

Backup Server ruft die Windows Server-Sicherung auf und gibt das Replikatvolume für diese BMR-Sicherung frei. In diesem Fall muss die Windows Server-Sicherung nicht das Laufwerk mit dem meisten freien Speicherplatz verwenden. Stattdessen wird die Freigabe genutzt, die für den Auftrag erstellt wurde.

Nach Abschluss der Sicherung wird die Datei auf den Backup Server-Computer übertragen. Protokolle werden unter *C:\Windows\Logs\WindowsServerBackup* gespeichert.

## <a name="prerequisites-and-limitations"></a>Voraussetzungen und Einschränkungen

* BMR wird nicht für Computer mit Windows Server 2003 oder Computer unterstützt, auf denen ein Clientbetriebssystem ausgeführt wird.

* Sie können BMR und den Systemstatus nicht für denselben Computer in unterschiedlichen Schutzgruppen schützen.

* Ein Backup Server-Computer kann nicht selbst für BMR schützen.

* Kurzfristiger Schutz auf Band (D2T oder Datenträger zu Band) wird für BMR nicht unterstützt. Langfristige Speicherung auf Band (D2D2T oder Datenträger zu Datenträger zu Band) wird unterstützt.

* Für den BMR-Schutz muss die Windows Server-Sicherung auf dem geschützten Computer installiert sein.

* Im Gegensatz zum Systemstatusschutz gelten beim BMR-Schutz für Backup Server keine Speicherplatzanforderungen auf dem geschützten Computer. Von der Windows Server-Sicherung werden die Sicherungen direkt auf den Backup Server-Computer übertragen. Der Sicherungsübertragungsauftrag wird nicht in der Backup Server-Ansicht **Aufträge** angezeigt.

* Backup Server reserviert für BMR 30 GB Speicherplatz auf dem Replikatvolume. Diese Speicherplatzzuordnung kann auf der Seite **Datenträgerzuordnung** des Assistenten zum Ändern von Schutzgruppen geändert werden. Alternativ können hierzu die PowerShell-Cmdlets „Get-DatasourceDiskAllocation“ und „Set-DatasourceDiskAllocation“ verwendet werden. Auf dem Wiederherstellungspunktvolume erfordert der BMR-Schutz rund 6 GB bei einer Aufbewahrungsdauer von fünf Tagen.
  * Die Replikatvolumegröße kann nicht auf unter 15 GB verringert werden.
  * Backup Server berechnet nicht die Größe der BMR-Datenquelle. Es wird von 30 GB für alle Server ausgegangen. Ändern Sie den Wert basierend auf der Größe von BMR-Sicherungen, die Sie in Ihrer Umgebung erwarten. Die Größe einer BMR-Sicherung entspricht grob der Summe des beanspruchten Speicherplatzes auf allen wichtigen Volumes. Wichtige Volumes = Startvolume + Systemvolume + Volume mit den Systemstatusdaten, wie z.B. Active Directory.

* Wenn Sie vom Systemstatusschutz zum BMR-Schutz wechseln, erfordert der BMR-Schutz weniger Speicherplatz auf dem *Wiederherstellungspunktvolume*. Der zusätzliche Speicherplatz auf dem Volume wird allerdings nicht freigegeben. Sie können die Volumegröße auf der Seite **Datenträgerzuordnung ändern** des Assistenten zum Ändern von Schutzgruppen manuell verringern. Alternativ können hierzu die PowerShell-Cmdlets „Get-DatasourceDiskAllocation“ und „Set-DatasourceDiskAllocation“ verwendet werden.

    Wenn Sie vom Systemstatusschutz zum BMR-Schutz wechseln, erfordert der BMR-Schutz mehr Speicherplatz auf dem *Replikatvolume*. Das Volume wird automatisch erweitert. Wenn Sie die standardmäßige Speicherplatzzuordnung ändern möchten, verwenden Sie das PowerShell-Cmdlet „Modify-DiskAllocation“.

* Wenn Sie vom BMR-Schutz zum Systemstatusschutz wechseln, benötigen Sie mehr Speicherplatz auf dem Wiederherstellungspunktvolume. Azure Backup Server versucht ggf., das Volume automatisch zu vergrößern. Sollte im Speicherpool nicht genügend Speicherplatz zur Verfügung stehen, tritt ein Fehler auf.

    Wenn Sie vom BMR-Schutz zum Systemstatusschutz wechseln, benötigen Sie Speicherplatz auf dem geschützten Computer. Der Grund: Der Systemstatusschutz schreibt das Replikat zunächst auf den lokalen Computer und überträgt es dann auf den Backup Server-Computer.

## <a name="before-you-begin"></a>Voraussetzungen

1. **Stellen Sie Azure Backup Server bereit**. Überprüfen Sie, ob Backup Server ordnungsgemäß bereitgestellt ist. Weitere Informationen finden Sie unter
    * [Systemanforderungen für Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Backup Server-Schutzmatrix](backup-mabs-protection-matrix.md)

1. **Richten Sie Speicher ein**. Sie können mit Azure Sicherungsdaten auf Datenträger, auf Band und in der Cloud speichern. Weitere Informationen finden Sie unter [Vorbereiten des Datenspeichers](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Richten Sie den Schutz-Agent ein**. Installieren Sie den Schutz-Agent auf dem Computer, den Sie sichern möchten. Weitere Informationen finden Sie unter [Bereitstellen des DPM-Schutz-Agents](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Sichern von Systemstatus und BMR

So sichern Sie Systemstatus und BMR:

1. Wählen Sie in der Backup Server-Administratorkonsole **Schutz** > **Aktionen** > **Schutzgruppe erstellen** aus, um den Assistenten zum Erstellen einer neuen Schutzgruppe zu öffnen.

1. Klicken Sie auf der Seite **Schutzgruppentyp auswählen** auf **Server** und dann auf **Weiter**.

1. Erweitern Sie auf der Seite **Gruppenmitglieder auswählen** den Computer, und wählen Sie dann entweder **BMR** oder **Systemstatus** aus.

    Zur Erinnerung: Sie können BMR und den Systemstatus nicht für denselben Computer in unterschiedlichen Gruppen schützen. Bei Wahl von BMR wird der Systemstatus automatisch aktiviert. Weitere Informationen finden Sie unter [Bereitstellen von Schutzgruppen](/system-center/dpm/create-dpm-protection-groups).

1. Wählen Sie auf der Seite **Datenschutzmethode auswählen** den gewünschten Umgang mit der kurz- und langfristigen Sicherung aus.

    Die kurzfristige Sicherung erfolgt immer zuerst auf Datenträger – mit der Option, Daten vom Datenträger per Azure Backup in der Azure-Cloud zu sichern (kurz- oder langfristig). Eine Alternative zur langfristigen Sicherung in der Cloud ist das Einrichten einer langfristigen Sicherung auf einem eigenständigen Bandgerät oder in einer Bandbibliothek, das bzw. die mit Backup Server verbunden ist.

1. Wählen Sie auf der Seite **Kurzfristige Ziele auswählen** aus, wie die Sicherung in kurzfristigem Speicher auf dem Datenträger erfolgen soll:
    * Wählen Sie unter **Beibehaltungsdauer** aus, wie lange die Daten auf dem Datenträger beibehalten werden sollen.
    * Wählen Sie unter **Synchronisierungsfrequenz** aus, wie oft eine inkrementelle Sicherung auf dem Datenträger erfolgen soll. Wenn Sie kein Intervall für die Sicherung festlegen möchten, können Sie **Direkt vor einem Wiederherstellungspunkt** auswählen. Von Backup Server wird unmittelbar vor jedem geplanten Wiederherstellungspunkt eine schnelle vollständige Sicherung ausgeführt.

1. Wenn Sie Daten langfristig auf Band speichern möchten, können Sie auf der Seite **Langfristige Ziele angeben** auswählen, wie lange Banddaten aufbewahrt werden sollen (1–99 Jahre).
    1. Wählen Sie unter **Sicherungshäufigkeit** aus, wie oft die Sicherung auf Band erfolgen soll. Die Häufigkeit basiert auf der gewählten Beibehaltungsdauer:
        * Bei einer Beibehaltungsdauer zwischen einem Jahr und 99 Jahren können Sicherungen täglich, wöchentlich, alle zwei Wochen, monatlich, vierteljährlich, halbjährlich oder jährlich erfolgen.
        * Bei einer Beibehaltungsdauer zwischen einem Monat und elf Monaten können Sicherungen täglich, wöchentlich, alle zwei Wochen oder monatlich erfolgen.
        * Bei einer Beibehaltungsdauer zwischen einer Woche und vier Wochen können Sicherungen täglich oder wöchentlich erfolgen.

    1. Wählen Sie auf der Seite **Bibliothek- und Banddetails auswählen** das zu verwendende Band und die zu verwendende Bibliothek aus. Geben Sie außerdem an, ob Daten komprimiert und verschlüsselt werden sollen.

1. Überprüfen Sie auf der Seite **Datenträgerzuordnungen überprüfen** den Speicherplatz im Speicherpool, der für die Schutzgruppe zur Verfügung steht.

    * **Gesamtdatengröße** ist die Größe der Daten, die Sie sichern möchten.
    * **Für Azure Backup Server bereitzustellender Speicherplatz** ist der Speicherplatz, den Backup Server für die Schutzgruppe empfiehlt. Backup Server wählt auf der Grundlage dieser Einstellungen das ideale Sicherungsvolume aus. Die Sicherungsvolumeoptionen können jedoch unter **Details zur Datenspeicherzuordnung** bearbeitet werden.
    * Wählen Sie für Workloads im Dropdownmenü den bevorzugten Speicher aus. Durch Ihre Änderungen verändern sich im Bereich **Verfügbarer Speicherplatz** die Werte für **Gesamtspeicher** und **Freier Speicher**. Nicht ausreichend bereitgestellter Speicherplatz ist die Menge an Speicher, die dem Volume laut Backup Server hinzugefügt werden sollte, um reibungslose Sicherungen zu gewährleisten.

1. Wählen Sie auf der Seite **Replikaterstellungsmethode auswählen** die gewünschte Vorgehensweise bei der ersten vollständigen Datenreplikation aus.

    Wenn Sie sich für die Replikation über das Netzwerk entscheiden, sollten Sie hierfür eine Nebenzeit auswählen. Ziehen Sie bei großen Datenmengen oder nicht optimalen Netzwerkbedingungen die Offlinereplikation der Daten mit Wechselmedien in Betracht.

1. Legen Sie im Bildschirm **Konsistenzprüfungsoptionen auswählen** fest, wie Konsistenzprüfungen automatisiert werden sollen.

    Sie haben nur dann die Wahl der Ausführung einer Überprüfung, wenn Replikatdaten inkonsistent werden, oder gemäß einem Zeitplan. Wenn Sie keine automatische Konsistenzprüfung konfigurieren möchten, können Sie jederzeit eine manuelle Überprüfung ausführen. Klicken Sie zum Ausführen einer manuellen Prüfung im Bereich **Schutz** der Backup Server-Administratorkonsole mit der rechten Maustaste auf die Schutzgruppe, und wählen Sie **Konsistenzprüfung ausführen** aus.

1. Wenn Sie sich für die Sicherung in der Cloud mithilfe von Azure Backup entschieden haben, wählen Sie auf der Seite **Online zu schützende Daten angeben** die Workloads aus, die Sie in Azure sichern möchten.

1. Wählen Sie auf der Seite **Onlinesicherungszeitplan angeben** aus, wie oft inkrementelle Sicherungen in Azure erfolgen sollen.

    Sicherungen können täglich, wöchentlich, monatlich und jährlich ausgeführt werden. Darüber hinaus können Sie den gewünschten Ausführungszeitpunkt (Uhrzeit und Datum) für Sicherungen auswählen. Sicherungen können bis zu zweimal täglich erfolgen. Bei jeder Ausführung einer Sicherung wird auf der Grundlage der auf dem Backup Server-Datenträger gespeicherten Sicherungsdatenkopie ein Datenwiederherstellungspunkt in Azure erstellt.

1. Wählen Sie auf der Seite **Onlineaufbewahrungsrichtlinie angeben** aus, wie die Wiederherstellungspunkte in Azure aufbewahrt werden sollen, die auf der Grundlage der täglichen, wöchentlichen, monatlichen und jährlichen Sicherungen erstellt werden.

1. Wählen Sie auf der Seite **Onlinereplikation wählen** aus, wie die erste vollständige Replikation der Daten erfolgt.

    Daten können über das Netzwerk repliziert oder offline gesichert werden (Offlineseeding). Bei der Offlinesicherung wird das Importfeature von Azure verwendet. Weitere Informationen finden Sie unter [Workflow zur Offlinesicherung in Azure Backup](offline-backup-azure-data-box.md).

1. Überprüfen Sie auf der Seite **Zusammenfassung** Ihre Einstellungen. Nach Auswahl von **Gruppe erstellen** erfolgt die erste Replikation der Daten. Nach Abschluss der Datenreplikation hat die Schutzgruppe auf der Seite **Status** den Status **OK**. Sicherungen werden dann gemäß den Schutzgruppeneinstellungen ausgeführt.

## <a name="recover-system-state-or-bmr"></a>Wiederherstellen des Systemstatus oder BMR

Sie können die BMR oder den Systemstatus an einem Speicherort im Netzwerk wiederherstellen. Verwenden Sie nach der BMR-Sicherung die Windows-Wiederherstellungsumgebung (Windows Recovery Environment, WinRE), um Ihr System zu starten und mit dem Netzwerk zu verbinden. Verwenden Sie anschließend die Windows Server-Sicherung, um die Wiederherstellung über den Netzwerkpfad durchzuführen. Wenn Sie den Systemstatus gesichert haben, verwenden Sie lediglich die Windows Server-Sicherung für die Wiederherstellung über den Netzwerkpfad.

### <a name="restore-bmr"></a>Wiederherstellen von BMR

So führen Sie die Wiederherstellung auf dem Backup Server-Computer aus:

1. Suchen Sie im Bereich **Wiederherstellung** nach dem Computer, den Sie wiederherstellen möchten. Wählen Sie **Bare-Metal-Recovery** aus.

1. Verfügbare Wiederherstellungspunkte sind fett im Kalender formatiert. Wählen Sie Datum und Uhrzeit des Wiederherstellungspunkts aus, den Sie verwenden möchten.

1. Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **In einen Netzwerkordner kopieren** aus.

1. Wählen Sie auf der Seite **Ziel angeben** das Ziel für die kopierten Daten aus.

    Zur Erinnerung: Am Ziel muss genügend Speicherplatz für die Daten zur Verfügung stehen. Es empfiehlt sich, einen neuen Ordner für das Ziel zu erstellen.

1. Wählen Sie auf der Seite **Wiederherstellungsoptionen angeben** die gewünschten Sicherheitseinstellungen aus. Wählen Sie anschließend aus, ob Sie SAN-basierte (Storage Area Network) Hardwaremomentaufnahmen verwenden möchten, um die Wiederherstellung zu beschleunigen. Diese Option ist nur verfügbar, wenn folgende Voraussetzungen erfüllt sind:
    * Sie verfügen über ein SAN, das die Verwendung dieser Funktion ermöglicht.
    * Sie können einen Klon erstellen und teilen, um ihn beschreibbar zu machen.
    * Der geschützte Computer und der Backup Server-Computer sind mit dem gleichen Netzwerk verbunden.

1. Richten Sie Benachrichtigungsoptionen ein.
1. Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**.

So richten Sie den Speicherort der Freigabe ein:

1. Wechseln Sie am Wiederherstellungsspeicherort zum Ordner mit der Sicherung.

1. Geben Sie den Ordner frei, der sich eine Ebene über *WindowsImageBackup* befindet, damit der Stamm des freigegebenen Ordners der Ordner *WindowsImageBackup* ist.

    Wenn Sie diesen Ordner nicht freigeben, wird die Sicherung nicht gefunden. Für die Verbindungsherstellung mittels WinRE benötigen Sie eine Freigabe, auf die Sie in WinRE zugreifen können (mit der richtigen IP-Adresse und den richtigen Anmeldeinformationen).

So stellen Sie das System wieder her:

1. Starten Sie den Computer, auf dem Sie das Image mithilfe der Windows-DVD für das wiederherzustellende System wiederherstellen möchten.

1. Überprüfen Sie auf der ersten Seite die Einstellungen für Sprache und Gebietsschema. Wählen Sie auf der Seite **Installieren** die Option **Computer reparieren** aus.

1. Wählen Sie auf der Seite **Systemwiederherstellungsoptionen** die Option **Stellen Sie den Computer mithilfe eines zuvor erstellten Systemabbilds wieder her**.

1. Wählen Sie auf der Seite **Systemabbildsicherung auswählen** nacheinander **Systemabbild auswählen** > **Erweitert** > **Im Netzwerk nach einem Systemabbild suchen**. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja**. Wechseln Sie zum Freigabepfad, geben Sie die Anmeldeinformationen ein, und wählen Sie dann den Wiederherstellungspunkt aus. Das System sucht nach bestimmten Sicherungen, die an diesem Wiederherstellungspunkt verfügbar sind. Wählen Sie den gewünschten Wiederherstellungspunkt aus.

1. Wählen Sie auf der Seite **Art der Wiederherstellung wählen** die Option **Datenträger formatieren und neu partitionieren** aus. Überprüfen Sie auf der nächsten Seite die Einstellungen.

1. Um die Wiederherstellung zu starten, klicken Sie auf **Fertig stellen**. Ein Neustart ist erforderlich.

### <a name="restore-system-state"></a>Wiederherstellen des Systemstatus

So führen Sie die Wiederherstellung in Backup Server aus:

1. Suchen Sie im Bereich **Wiederherstellung** den Computer, den Sie wiederherstellen möchten, und wählen Sie dann **Bare-Metal-Recovery** aus.

1. Verfügbare Wiederherstellungspunkte sind fett im Kalender formatiert. Wählen Sie Datum und Uhrzeit des Wiederherstellungspunkts aus, den Sie verwenden möchten.

1. Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **In einen Netzwerkordner kopieren** aus.

1. Wählen Sie auf der Seite **Ziel angeben** aus, wohin die Daten kopiert werden sollen.

    Zur Erinnerung: Am gewählten Ziel muss genügend Speicherplatz für die Daten zur Verfügung stehen. Es empfiehlt sich, einen neuen Ordner für das Ziel zu erstellen.

1. Wählen Sie auf der Seite **Wiederherstellungsoptionen angeben** die gewünschten Sicherheitseinstellungen aus. Wählen Sie anschließend aus, ob Sie SAN-basierte Hardwaremomentaufnahmen verwenden möchten, um die Wiederherstellung zu beschleunigen. Diese Option ist nur verfügbar, wenn folgende Voraussetzungen erfüllt sind:
    * Sie verfügen über ein SAN, das die Verwendung dieser Funktion ermöglicht.
    * Sie können einen Klon erstellen und teilen, um ihn beschreibbar zu machen.
    * Der geschützte Computer und der Backup Server-Computer sind mit dem gleichen Netzwerk verbunden.

1. Richten Sie Benachrichtigungsoptionen ein.
1. Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**.

So führen Sie die Windows Server-Sicherung aus:

1. Wählen Sie **Aktionen** > **Wiederherstellen** > **Dieser Server** > **Weiter** aus.

1. Wählen Sie **Einen anderen Server**, die Seite **Speicherorttyp angeben** und dann **Freigegebener Remoteordner** aus. Geben Sie den Pfad zum Ordner ein, der den Wiederherstellungspunkt enthält.

1. Wählen Sie auf der Seite **Wiederherstellungstyp auswählen** die Option **Systemstatus** aus.

1. Wählen Sie auf der Seite **Speicherort für Wiederherstellung des Systemstatus auswählen** die Option **Ursprungsspeicherort** aus.

1. Klicken Sie auf der Seite **Bestätigung** auf **Wiederherstellen**.

1. Starten Sie den Server nach der Wiederherstellung neu.

Sie können die Systemstatuswiederherstellung auch an einer Eingabeaufforderung ausführen:

1. Starten Sie die Windows Server-Sicherung auf dem Computer, den Sie wiederherstellen möchten.

1. Geben Sie zum Abrufen des Versionsbezeichners an der Eingabeaufforderung Folgendes ein:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Verwenden Sie den Versionsbezeichner zum Starten der Systemstatuswiederherstellung. Geben Sie an der Eingabeaufforderung Folgendes ein:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Bestätigen Sie, dass Sie die Wiederherstellung starten möchten. Der Prozess wird im Eingabeaufforderungsfenster angezeigt. Ein Wiederherstellungsprotokoll wird erstellt.

1. Starten Sie den Server nach der Wiederherstellung neu.
