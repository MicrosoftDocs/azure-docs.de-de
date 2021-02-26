---
title: Microsoft Azure StorSimple Virtual Array – Übersicht
description: Enthält eine Beschreibung von StorSimple Virtual Array. Hierbei handelt es sich um eine integrierte Speicherlösung, mit der Speicheraufgaben zwischen einem lokalen virtuellen Array und dem Microsoft Azure-Cloudspeicher verwaltet werden.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: af48e1f415e0ca0b1027d277f70c3f0f1a11e687
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526764"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Einführung in das StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Übersicht

Bei Microsoft Azure StorSimple Virtual Array handelt es sich um eine integrierte Speicherlösung, mit der Speicheraufgaben zwischen einem lokalen virtuellen Array, das in einem Hypervisor ausgeführt wird, und dem Microsoft Azure-Cloudspeicher verwaltet werden. Das virtuelle Array ist eine effiziente, kostengünstige und einfach zu verwaltende Dateiserver- oder iSCSI-Serverlösung, mit der viele der Probleme und Kosten vermieden werden, die mit der Datenspeicherung und dem Datenschutz im Unternehmen verbunden sind. Das virtuelle Array eignet sich sehr gut zum Speichern von Archivdaten, auf die selten zugegriffen wird.

Dieser Artikel enthält eine Übersicht über das virtuelle Array. Weitere Ressourcen:

* Bewährte Methoden finden Sie unter [StorSimple Virtual Array – Bewährte Methoden](storsimple-ova-best-practices.md).
* Eine Übersicht über StorSimple-Geräte der 8000er Serie finden Sie unter [StorSimple 8000er Serie: Hybridcloudlösung](storsimple-overview.md).
* Informationen zu den Geräten der StorSimple 5000/7000er Serie finden Sie unter [StorSimple-Onlinehilfe](http://onlinehelp.storsimple.com/).

Das virtuelle Array unterstützt das iSCSI- oder Server Message Block-Protokoll (SMB). Es wird in Ihrer vorhandenen Hypervisor-Infrastruktur ausgeführt und bietet Staffelung in der Cloud, Cloudsicherung, schnelle Wiederherstellung, Wiederherstellung auf Elementebene und Features für die Notfallwiederherstellung.

In der folgenden Tabelle sind die wichtigen Features von StorSimple Virtual Array zusammengefasst.

| Funktion | StorSimple Virtual Array |
| --- | --- |
| Installationsanforderungen |Verwendung der Virtualisierungsinfrastruktur (Hyper-V oder VMware) |
| Verfügbarkeit |Einzelner Knoten |
| Gesamtkapazität (einschließlich Cloud) |Bis zu 64 TB nutzbare Kapazität pro virtuellem Array |
| Lokale Kapazität |390 GB bis 6,4 TB nutzbare Kapazität pro virtuellem Array (Bereitstellung von Speicherplatz auf dem Datenträger im Bereich 500 GB bis 8 TB erforderlich) |
| Systemeigene Protokolle |iSCSI oder SMB |
| Recovery Time Objective (RTO) |iSCSI: weniger als 2 Minuten, unabhängig von der Größe |
| Recovery Point Objective (RPO) |Tägliche Sicherungen und bedarfsgesteuerte Sicherungen |
| Speicherstaffelung |Verwendung von Heat Maps zum Ermitteln, welche Daten einbezogen bzw. nicht einbezogen werden sollen |
| Support |Vom Anbieter unterstützte Virtualisierungsinfrastruktur |
| Leistung |Variiert je nach zugrunde liegender Infrastruktur |
| Datenmobilität |Wiederherstellung auf demselben Gerät oder Wiederherstellung auf Elementebene (Dateiserver) |
| Speicherebenen |Lokale Hypervisor-Speicherung und Cloud |
| Freigabegröße |Mehrstufig: bis zu 20 TB; Lokal: bis zu 2 TB |
| Volumegröße |Mehrstufig: 500 GB bis 5 TB; lokal angeheftet: 50 GB bis 200 GB <br> Die maximale lokale Reservierung für mehrstufige Volumes beträgt 200 GB. |
| Momentaufnahmen |Absturzkonsistenz |
| Wiederherstellung auf Elementebene |Ja, Benutzer können von Freigaben wiederherstellen |

## <a name="why-use-storsimple"></a>Gründe für die Verwendung von StorSimple

StorSimple verbindet Benutzer und Server in wenigen Minuten mit Azure-Speicher – ohne Änderung der Anwendung.

In der folgenden Tabelle sind einige wichtige Vorteile der StorSimple Virtual Array-Lösung aufgeführt.

| Funktion | Vorteil |
| --- | --- |
| Transparente Integration |Das virtuelle Array unterstützt das iSCSI- oder SMB-Protokoll. Die Datenverschiebung zwischen der lokalen und der Cloudebene erfolgt nahtlos und transparent für den Benutzer. |
| Reduzierte Speicherkosten |Mit StorSimple stellen Sie ausreichend lokalen Speicher bereit, um die aktuellen Anforderungen für sehr häufig verwendete heiße Daten zu erfüllen. Wenn die Speicheranforderungen steigen, ordnet StorSimple kalte Daten im kostengünstigen Cloudspeicher an. Die Daten werden vor dem Senden an die Cloud dedupliziert und komprimiert, um die Speicheranforderungen und -kosten noch weiter zu reduzieren. |
| Vereinfachte Speicherverwaltung |StorSimple ermöglicht die zentrale Verwaltung mehrerer Geräte in der Cloud über den StorSimple-Geräte-Manager. |
| Verbesserte Notfallwiederherstellung und Vorschrifteneinhaltung |StorSimple ermöglicht eine schnellere Notfallwiederherstellung, indem Metadaten sofort und Daten nach Bedarf wiederhergestellt werden. Normale Vorgänge können mit minimaler Unterbrechung fortgesetzt werden. |
| Datenmobilität |Auf die in der Cloud auf verschiedenen Ebenen angeordneten Daten kann über andere Standorte zu Wiederherstellungs- und Migrationszwecken zugegriffen werden. Sie können Daten nur auf dem ursprünglichen virtuellen Array wiederherstellen. Sie verwenden aber Features für die Notfallwiederherstellung, um das gesamte virtuelle Array auf einem anderen virtuellen Array wiederherzustellen. |



## <a name="storsimple-workload-summary"></a>StorSimple-Workload – Übersicht

In der folgenden Tabelle finden Sie eine Übersicht über unterstützte StorSimple-Workloads.

|Szenario     |Workload     |Unterstützt      |Beschränkungen               | Anwendbare Versionen|
|-------------|-------------|---------------|---------------------------|--------------------|
|Remotebüro/Zweigniederlassung (ROBO)  |Dateifreigabe     |Ja      |Informationen finden Sie im Thema zu den [Obergrenzen für Dateiserver](storsimple-ova-limits.md).<br></br>Informationen finden Sie im Thema zu den [Systemanforderungen für unterstützte SMB-Versionen](storsimple-ova-system-requirements.md).| Alle Versionen     |
|Cloudarchivierung  |Archivdateifreigabe     |Ja      |Informationen finden Sie im Thema zu den [Obergrenzen für Dateiserver](storsimple-ova-limits.md).<br></br>Informationen finden Sie im Thema zu den [Systemanforderungen für unterstützte SMB-Versionen](storsimple-ova-system-requirements.md).| Alle Versionen     |

Das StorSimple Virtual Array eignet sich am besten für selten genutzte Daten. Zur Verbesserung der Leistung verfügt das virtuelle Array zwar über einen lokalen Cache, Benutzer sollten jedoch davon ausgehen, dass das Gerät Dateien auf der niedrigsten Speicherstufe (der Cloud) bereitstellt. Jedes virtuelle Array kann mit einer Geschwindigkeit von etwa 100 Mbit/s in Azure-Speicher schreiben bzw. daraus lesen. Diese Verbindung wird für alle an das Gerät gerichteten Anforderungen genutzt und kann sich als Engpass erweisen, wie im folgenden Diagramm zu sehen:

![Cloudarchivierung](./media/storsimple-ova-overview/cloud-archiving.png)

Wenn mehrere Benutzer gleichzeitig auf das virtuelle Array zugreifen, teilen sich alle die gleiche Verbindung mit Azure. Dies wirkt sich negativ auf die Leistung aus. Es gibt keine garantierte Leistung pro Benutzer, und das Gerät verarbeitet einzelne Anforderungen in der Reihenfolge, in der sie eingehen.

Das StorSimple Virtual Array eignet sich nicht für Workloads, die Hochverfügbarkeit erfordern. Das virtuelle Array ist ein Gerät mit einem einzelnen Knoten, bei dem es zu Ausfällen kommt, wenn Softwareupdates installiert werden. Administratoren sollten pro Jahr drei bis vier Wartungsfenster mit jeweils 30 Minuten einplanen.

## <a name="workflows"></a>Workflows

Das StorSimple Virtual Array eignet sich besonders gut für die folgenden Workflows:

* [Cloudbasierte Speicherverwaltung](#cloud-based-storage-management)
* [Standortunabhängige Sicherungen](#location-independent-backup)
* [Schutz von Daten und Notfallwiederherstellung](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Cloudbasierte Speicherverwaltung
Sie können den im Azure-Portal ausgeführten StorSimple-Geräte-Manager-Dienst verwenden, um Daten zu verwalten, die auf mehreren Geräten und an mehreren Standorten gespeichert sind. Das ist besonders bei Szenarien mit weit auseinanderliegenden Zweigstellen hilfreich. Sie müssen separate Instanzen des StorSimple-Geräte-Manager-Diensts erstellen, um virtuelle Arrays und physische StorSimple-Geräte zu verwalten. Das virtuelle Array verwendet jetzt das neue Azure-Portal anstelle des klassischen Azure-Portals.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![Cloudbasierte Speicherverwaltung](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Standortunabhängige Sicherungen
Beim virtuellen Array ermöglichen Cloudmomentaufnahmen die Erstellung einer standortunabhängigen Point-in-Time-Kopie eines Volumes oder einer Freigabe. Cloudmomentaufnahmen sind standardmäßig aktiviert und können nicht deaktiviert werden. Alle Volumes und Freigaben werden über eine Richtlinie für die tägliche Sicherung gleichzeitig gesichert, und Sie können bei Bedarf jederzeit zusätzliche Ad-hoc-Sicherungen erstellen.

### <a name="data-protection-and-disaster-recovery"></a>Schutz von Daten und Notfallwiederherstellung
Das virtuelle Array unterstützt die folgenden Szenarien für den Schutz der Daten und die Notfallwiederherstellung:

* **Wiederherstellung von Volumes oder Freigaben:** Verwenden Sie die Wiederherstellung als neuen Workflow zum Wiederherstellen eines Volumes oder einer Freigabe. Nutzen Sie diesen Ansatz, um das gesamte Volume oder die Freigabe wiederherzustellen.
* **Wiederherstellung auf Elementebene:** Freigaben ermöglichen den vereinfachten Zugriff auf die letzten Sicherungen. Sie können eine einzelne Datei leicht über einen speziellen Ordner mit der Bezeichnung *.backup* in der Cloud wiederherstellen. Diese Wiederherstellungsfunktion ist benutzergesteuert, und es ist kein Eingreifen des Administrators erforderlich.
* **Notfallwiederherstellung:** Verwenden Sie die Failoverfunktion, um alle Volumes oder Freigaben auf einem neuen virtuellen Array wiederherzustellen. Sie erstellen das neue virtuelle Array und registrieren es beim StorSimple-Geräte-Manager-Dienst. Anschließend führen Sie das Failover für das ursprüngliche virtuelle Array durch. Das neue virtuelle Array übernimmt dann die bereitgestellten Ressourcen.

## <a name="storsimple-virtual-array-components"></a>StorSimple Virtual Array-Komponenten

Das virtuelle Array umfasst die folgenden Komponenten:

* [Virtuelles Array:](#virtual-array) Ein Hybrid-Cloudspeichergerät basierend auf einem virtuellen Computer, der in Ihrer virtualisierten Umgebung oder im Hypervisor bereitgestellt wird.
* [StorSimple-Geräte-Manager-Dienst:](#storsimple-device-manager-service) Eine Erweiterung des Azure-Portals, mit der Sie StorSimple-Geräte über eine zentrale Webschnittstelle verwalten können, auf die über verschiedene geografische Standorte zugegriffen werden kann. Sie können den StorSimple-Geräte-Manager-Dienst verwenden, um Dienste zu erstellen und zu verwalten, Geräte und Warnungen anzuzeigen und zu verwalten und Volumes, Freigaben und vorhandene Momentaufnahmen zu verwalten.
* [Lokale Webbenutzeroberfläche:](#local-web-user-interface) Eine webbasierte Benutzeroberfläche zum Konfigurieren des Geräts. So kann die Verbindung mit dem lokalen Netzwerk hergestellt und das Gerät anschließend beim StorSimple-Geräte-Manager-Dienst registriert werden. 
* [Befehlszeilenschnittstelle:](#command-line-interface) Eine Windows PowerShell-Schnittstelle, die Sie zum Starten einer Supportsitzung für das virtuelle Array verwenden können.
  In den folgenden Abschnitten werden die einzelnen Komponenten ausführlicher beschrieben. Es wird zudem erläutert, wie die Lösung Daten anordnet, Speicher zuweist sowie die Speicherverwaltung und den Schutz von Daten vereinfacht.

### <a name="virtual-array"></a>Virtuelles Array

Das virtuelle Array ist eine Speicherlösung mit einem einzelnen Knoten, die Folgendes ermöglicht: primäre Speicherung, Verwaltung der Kommunikation mit dem Cloudspeicher und Gewährleistung der Sicherheit und Vertraulichkeit aller auf dem Gerät gespeicherter Daten.

Das virtuelle Array ist als Modell verfügbar, das heruntergeladen werden kann. Das virtuelle Array hat eine maximale Kapazität von 6,4 TB auf dem Gerät (mit einer zugrunde liegenden Speicheranforderung von 8 TB) und – einschließlich des Cloudspeichers – von 64 TB.

Das virtuelle Array bietet die folgenden Features:

* Es ist kostengünstig. Es nutzt Ihre vorhandene Virtualisierungsinfrastruktur und kann auf Ihrem vorhandenen Hyper-V- oder VMware-Hypervisor bereitgestellt werden.
* Es befindet sich im Rechenzentrum und kann als iSCSI-Server oder Dateiserver konfiguriert werden.
* Es ist in die Cloud integriert.
* Sicherungen werden in der Cloud gespeichert, um die Notfallwiederherstellung und Wiederherstellung auf Elementebene zu vereinfachen.
* Sie können Updates auf das virtuelle Array anwenden, wie Sie dies auch für ein physisches Gerät durchführen.

> [!NOTE]
> Ein virtuelles Array kann nicht erweitert werden. Daher ist es wichtig, ausreichend Speicherplatz bereitzustellen, wenn Sie das virtuelle Array erstellen.

### <a name="storsimple-device-manager-service"></a>StorSimple-Geräte-Manager-Dienst

Microsoft Azure StorSimple stellt eine webbasierte Benutzeroberfläche (den StorSimple-Geräte-Manager-Dienst) zur Verfügung, die eine zentrale Verwaltung des StorSimple-Speichers ermöglicht. Mithilfe des StorSimple-Geräte-Manager-Diensts können Sie die folgenden Aufgaben ausführen:

* Verwalten von mehreren StorSimple Virtual Arrays über einen zentralen Dienst
* Konfigurieren und Verwalten von Sicherheitseinstellungen für StorSimple Virtual Array. (Die Verschlüsselung in der Cloud richtet sich nach den Microsoft Azure-APIs.)
* Konfigurieren der Anmeldeinformationen und Eigenschaften für das Speicherkonto
* Konfigurieren und Verwalten der Volumes oder Freigaben
* Sichern und Wiederherstellen von Daten auf Volumes oder Freigaben
* Überwachen der Leistung
* Überprüfen von Systemeinstellungen und Bestimmen möglicher Probleme

Sie verwenden den StorSimple-Geräte-Manager-Dienst für die tägliche Verwaltung Ihres virtuellen Arrays.

Weitere Informationen finden Sie unter [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokale Webbenutzeroberfläche

Das virtuelle Array enthält eine webbasierte Benutzeroberfläche, die für die einmalige Konfiguration und Registrierung des Geräts beim StorSimple-Geräte-Manager-Dienst verwendet wird. Sie können sie zum Herunterfahren und Neustarten des virtuellen Arrays, zum Ausführen von Diagnosetests, zum Aktualisieren von Software, zum Ändern des Geräteadministratorkennworts, zum Anzeigen von Systemprotokollen und zum Senden einer Serviceanfrage an den Microsoft-Support verwenden.

Informationen zur Verwendung der webbasierten Benutzeroberfläche finden Sie unter [Verwenden der webbasierten Benutzeroberfläche zum Verwalten des StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Befehlszeilenschnittstelle

Mit der integrierten Windows PowerShell-Schnittstelle können Sie eine Supportsitzung mit dem Microsoft-Support initiieren, um Hilfe bei der Problembehandlung und beim Lösen von Problemen zu erhalten, die auf dem virtuellen Array auftreten können.

## <a name="storage-management-technologies"></a>Speicherverwaltungstechnologien

Zusätzlich zum virtuellen Array und anderen Komponenten nutzt die StorSimple-Lösung die folgende Softwaretechnologie, um den schnellen Zugriff auf wichtige Daten zu ermöglichen, den Speicherverbrauch zu verringern und Daten zu schützen, die auf dem virtuellen Array gespeichert sind:

* [Automatische Speicherstaffelung](#automatic-storage-tiering) 
* [Lokale Freigaben und Volumes](#locally-pinned-shares-and-volumes)
* Deduplizierung und Komprimierung für in der Cloud angeordnete oder gesicherte Daten 
* [Geplante und bedarfsgesteuerte Sicherungen](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatische Speicherstaffelung
Für das virtuelle Array wird ein neuer Staffelungsmechanismus zum Verwalten gespeicherter Daten im virtuellen Array und in der Cloud verwendet. Es gibt nur zwei Ebenen: das lokale virtuelle Array und den Azure-Cloudspeicher. Das StorSimple Virtual Array ordnet die Daten basierend auf einer Heat Map automatisch auf den Ebenen an. Mit der Heat Map werden die aktuelle Nutzung, das Alter und die Beziehungen mit anderen Daten nachverfolgt. Daten, die am aktivsten („hot“) sind, werden lokal gespeichert, während weniger aktive und inaktive Daten automatisch in die Cloud migriert werden. (Alle Sicherungen werden in der Cloud gespeichert.) StorSimple passt Daten- und Speicherzuordnungen an und ordnet diese neu, wenn sich die Verwendungsmuster ändern. Im Lauf der Zeit können einige Informationen z. B. weniger aktiv werden. Wenn der Aktivitätsgrad immer mehr absinkt, werden sie in die Cloud verlagert. Wenn diese Daten dann wieder aktiv werden, werden sie im Speicherarray angeordnet.

Für Daten einer bestimmten mehrstufigen Freigabe oder eines bestimmten mehrstufigen Volumes wird auf lokaler Ebene jeweils eigener Speicherplatz garantiert (etwa 10 Prozent des insgesamt bereitgestellten Speicherplatzes einer Freigabe bzw. eines Volumes). Hierdurch wird zwar der verfügbare Speicher auf dem virtuellen Array für die Freigabe oder das Volume reduziert, aber es wird auch sichergestellt, dass die Staffelung für eine Freigabe oder ein Volume nicht durch die Staffelungsanforderungen anderer Freigaben oder Volumes beeinträchtigt wird. Daher kann es nicht passieren, dass eine Workload mit hoher Auslastung auf einer Freigabe oder einem Volume alle anderen Workloads in die Cloud verdrängt.

Bei für iSCSI erstellten mehrstufigen Volumes ist die lokale Reservierung unabhängig von der Größe des Volumes auf 200 GB begrenzt.

![Automatische Speicherstaffelung](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Sie können für ein Volume angeben, dass es nur lokal vorhanden sein soll. In diesem Fall verbleiben die Daten im virtuellen Array und werden nicht in die Cloud ausgelagert. Weitere Informationen finden Sie unter [Lokale Freigaben und Volumes](#locally-pinned-shares-and-volumes).

> [!IMPORTANT]
> Konvertieren Sie bei Verwendung von StorSimple keine Blobs in eine Archivierung, selbst wenn Ihr Gerät eingestellt wird. Um Daten von dem Gerät abzurufen, müssen Sie die Blobs aus der Archivierung als Typ „heiß“ oder „kalt“ aktivieren, was zu erheblichen Kosten führt.


### <a name="locally-pinned-shares-and-volumes"></a>Lokale Freigaben und Volumes

Sie können bestimmte Freigaben und Volumes als lokale Komponenten erstellen. Mit dieser Funktion wird sichergestellt, dass von kritischen Anwendungen erforderliche Daten im virtuellen Array verbleiben und nicht in die Cloud ausgelagert werden. Lokale Freigaben und Volumes verfügen über die folgenden Features:

* Sie unterliegen keinen Cloudlatenzen oder Konnektivitätsproblemen.
* Sie profitieren dennoch von den Features für die StorSimple-Cloudsicherung und -Notfallwiederherstellung.

Sie können eine lokale Freigabe bzw. ein Volume als mehrstufiges Element oder eine mehrstufige Freigabe bzw. ein Volume als lokales Element wiederherstellen. 

Weitere Informationen zu lokalen Volumes finden Sie unter [Verwalten von Volumes mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplizierung und Komprimierung für in der Cloud angeordnete oder gesicherte Daten

StorSimple arbeitet mit Deduplizierung und Datenkomprimierung, um die Speicheranforderungen in der Cloud weiter zu reduzieren. Durch Deduplizierung wird die Datenmenge verringert, indem Redundanz im gespeicherten Dataset beseitigt wird. Wenn sich Informationen ändern, ignoriert Azure StorSimple die unveränderten Daten und erfasst nur die Änderungen. Außerdem verringert StorSimple die Menge der gespeicherten Daten, indem doppelte Informationen ermittelt und entfernt werden.

> [!NOTE]
> Im virtuellen Array gespeicherte Daten werden nicht dedupliziert oder komprimiert. Die Deduplizierung und Komprimierung wird jeweils direkt vor dem Senden der Daten in die Cloud durchgeführt.


### <a name="scheduled-and-on-demand-backups"></a>Geplante und bedarfsgesteuerte Sicherungen

Mit StorSimple-Features zum Schutz der Daten können Sie bedarfsgesteuerte Sicherungen erstellen. Außerdem wird mit einem standardmäßigen Sicherungszeitplan dafür gesorgt, dass die Daten täglich gesichert werden. Sicherungen werden in Form von inkrementellen Momentaufnahmen erstellt, die in der Cloud gespeichert werden. Momentaufnahmen, bei denen nur die Änderungen seit der letzten Sicherung aufgezeichnet werden, können schnell erstellt und wiederhergestellt werden. Diese Momentaufnahmen können bei Wiederherstellungen im Notfall extrem wichtig sein, da sie sekundäre Speichersysteme (z. B. Backup auf Bandlaufwerken) ersetzen und es Ihnen ermöglichen, Daten bei Bedarf in Ihrem Rechenzentrum oder an anderen Standorten wiederherzustellen.

## <a name="managing-personal-information"></a>Verwalten personenbezogener Informationen

Der StorSimple-Geräte-Manager für virtuelle Serien sammelt in zwei wichtigen Instanzen personenbezogene Daten:
 - Benutzereinstellungen für Warnungen, in denen die E-Mail-Adressen von Benutzern konfiguriert werden. Diese Informationen können vom Administrator gelöscht werden. 
 - Benutzer, die auf die Daten auf den Freigaben zugreifen können. Eine Liste mit Benutzern, die auf die Freigabedaten zugreifen können, kann exportiert werden. Die Liste wird zusammen mit der Freigabe gelöscht.

Weitere Informationen finden Sie im [Trust Center in der Microsoft-Datenschutzrichtlinie](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie das [Virtual Array-Portal vorbereiten](storsimple-virtual-array-deploy1-portal-prep.md).
