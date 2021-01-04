---
title: StorSimple 8000-Serie als Sicherungsziel mit NetBackup | Microsoft-Dokumentation
description: Erfahren Sie mehr über die StorSimple-Sicherungszielkonfiguration mit Veritas NetBackup sowie die bewährten Methoden für die Integration beider Lösungen.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 4f71cf82b675222836a73eec12d68bd8f62a5538
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967277"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple als Sicherungsziel mit NetBackup

## <a name="overview"></a>Übersicht

Azure StorSimple ist eine hybride Cloudspeicherlösung von Microsoft. StorSimple dient zum Bewältigen der Komplexität des exponentiellen Datenwachstums. Die Lösung verwendet dazu ein Azure-Speicherkonto als Erweiterung der lokalen Lösung und verteilt Daten automatisch (per Tiering) auf lokalen Speicher und Cloudspeicher.

In diesem Artikel beschäftigen wir uns mit der StorSimple-Integration in Veritas NetBackup sowie den bewährten Methoden für die Integration beider Lösungen. Außerdem erhalten Sie Empfehlungen, wie Sie Veritas NetBackup für eine optimale Integration in StorSimple einrichten. Wir gehen auf die bewährten Methoden von Veritas für Sicherungsarchitekten und Administratoren ein, um Vereinbarung NetBackup optimal zum Erfüllen einzelner Sicherungsanforderungen und Vereinbarungen zum Servicelevel (SLAs) einzurichten.

In diesem Artikel werden Konfigurationsschritte und wichtige Konzepte veranschaulicht. Es handelt jedoch keinesfalls um eine detaillierte Anleitung für die Konfiguration oder Installation. Es wird von uns vorausgesetzt, dass die grundlegenden Komponenten und Infrastrukturen funktionieren und die hier beschriebenen Konzepte unterstützen können.

### <a name="who-should-read-this"></a>Zielgruppe dieses Artikels

Die Informationen in diesem Artikel sind vornehmlich für Sicherungsadministratoren, Speicheradministratoren und Speicherarchitekten gedacht, die über Kenntnisse zu Speichern, Windows Server 2012 R2, Ethernet, Clouddiensten und Veritas NetBackup verfügen.

### <a name="supported-versions"></a>Unterstützte Versionen

-   NetBackup 7.7.x und höher
-   [StorSimple Update 3 und höher](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Was spricht für StorSimple als Sicherungsziel?

StorSimple eignet sich aus den folgenden Gründen gut als Sicherungsziel:

-   Die Lösung stellt lokalen Standardspeicher für Sicherungsanwendungen als schnelles Sicherungsziel bereit, ohne dass Änderungen nötig sind. StorSimple eignet sich auch zum schnellen Wiederherstellen der jüngsten Sicherungen.
-   Sein Cloudtiering ist nahtlos in ein Azure-Cloudspeicherkonto integriert, um den kostengünstigen Azure Storage-Speicher nutzen zu können.
-   Die Lösung stellt automatisch Offsitespeicher für die Notfallwiederherstellung bereit.

## <a name="key-concepts"></a>Wichtige Begriffe

Wie bei anderen Speicherlösungen auch ist es für den Erfolg entscheidend, die Speicherleistung, SLAs, Änderungsrate und Anforderungen an das Kapazitätswachstum der Lösung sorgfältig zu bewerten. Der grundlegende Gedanke ist folgender: Aufgrund der Einführung einer Cloudspeicherstufe spielen die Zugriffszeiten und Durchsatzwerte in der Cloud eine wichtige Rolle für die richtige und sinnvolle Funktionsweise von StorSimple.

StorSimple stellt Speicher für Anwendungen bereit, die mit einem klar definierten Satz Arbeitsdaten (heiße Daten) arbeiten. In diesem Modell werden für die Arbeitsdaten lokale Speicherstufen verwendet. Die verbleibenden Daten, mit denen gerade nicht gearbeitet wird („kalte“ oder archivierte Daten), werden per Tiering in der Cloud gespeichert. Dieses Modell ist in der folgenden Abbildung dargestellt. Die nahezu waagerecht verlaufende grüne Linie repräsentiert die Daten, die auf den lokalen Speicherstufen des StorSimple-Geräts gespeichert sind. Die exponentiell ansteigende rote Linie zeigt die Gesamtmenge von Daten, die auf allen Speicherstufen der StorSimple-Lösung gespeichert sind. Der Abstand zwischen der grünen und der roten Linie stellt die Gesamtmenge der in der Cloud gespeicherten Daten dar.

**StorSimple-Tiering**
![Diagramm zu StorSimple-Tiering](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

Dank dieser Architektur eignet sich StorSimple ideal als Sicherungsziel. Sie können StorSimple für folgende Zwecke verwenden:
-   Ausführen der häufigsten Wiederherstellungen aus dem lokalen Arbeitssatz von Daten.
-   Verwenden der Cloud zur externen Notfallwiederherstellung und für ältere Daten, die seltener wiederhergestellt werden müssen.

## <a name="storsimple-benefits"></a>Vorteile von StorSimple

StorSimple bietet eine lokale Lösung, die nahtlos in Microsoft Azure integriert ist, und profitiert vom reibungslosen Zugriff auf lokalen und cloudbasierten Speicher.

StorSimple nutzt ein automatisches Tiering zwischen dem lokalen Gerät, das mit SSD-Speicher (Solid-State Drive) und SAS-Speicher (Serial-Attached SCSI) arbeitet, und Azure Storage. Durch das automatische Tiering verbleiben Daten, auf die häufig zugegriffen wird, lokal auf den SSD- und SAS-Stufen. Selten genutzte Daten werden in Azure Storage verschoben.

StorSimple bietet folgende Vorteile:

-   Eindeutige Algorithmen für Deduplizierung und Komprimierung, die dank Cloud ein nie dagewesenes Deduplizierungslevel erreichen
-   Hochverfügbarkeit
-   Georeplikation durch Nutzen der Azure-Georeplikation
-   Azure-Integration
-   Datenverschlüsselung in der Cloud
-   Verbesserte Notfallwiederherstellung und Vorschrifteneinhaltung

Obwohl StorSimple zwei wichtige Bereitstellungsszenarien (primäres und sekundäres Sicherungsziel) ermöglicht, handelt es sich grundsätzlich um ein einfaches Blockspeichergerät. StorSimple führt sämtliche Komprimierungs- und Deduplizierungsaufgaben aus. Von dieser Lösung werden Daten zwischen der Cloud sowie der Anwendung und dem Dateisystem transparent gesendet und empfangen.

Weitere Informationen zu StorSimple finden Sie unter [StorSimple 8000-Serie: eine Hybridcloud-Speicherlösung](storsimple-overview.md). Lesen Sie auch die [technischen Spezifikationen der StorSimple 8000-Serie](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Das Verwenden eines StorSimple-Geräts als Sicherungsziel wird nur für die Version StorSimple 8000 Update 3 und höher unterstützt.

## <a name="architecture-overview"></a>Übersicht über die Architektur

Die folgenden Tabellen enthalten Informationen zu verschiedenen Gerätemodellen und entsprechenden Architekturen.

**StorSimple-Kapazitäten für lokalen und Cloudspeicher**

| Speicherkapazität       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Lokale Speicherkapazität | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Cloudspeicherkapazität | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Bei der Speichergröße wird von nicht deduplizierten und nicht komprimierten Daten ausgegangen.

**StorSimple-Kapazitäten für primäre und sekundäre Sicherungen**

| Sicherungsszenario  | Lokale Speicherkapazität  | Cloudspeicherkapazität  |
|---|---|---|
| Primäre Sicherung  | Aktuelle Sicherungen im lokalen Speicher für eine schnelle Wiederherstellung zum Erfüllen der RPO-Vorgabe (Recovery Point Objective) | Sicherungsverlauf (RPO) passt in Cloudkapazität |
| Sekundäre Sicherung | Sekundäre Kopie der Sicherungsdaten kann in Cloudkapazität gespeichert werden  | –  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple als primäres Sicherungsziel

In diesem Szenario werden StorSimple-Volumes der Sicherungsanwendung als einziges Repository für Sicherungen präsentiert. Die folgende Abbildung zeigt eine Lösungsarchitektur, in der alle Sicherungen mehrstufige StorSimple-Volumes für Sicherungen und Wiederherstellungen verwenden.

![StorSimple als primäres Sicherungsziel – logisches Diagramm](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logische Schritte bei der Sicherung des Primärziels

1.  Der Sicherungsserver kontaktiert den Sicherungs-Agent auf dem Ziel, und der Sicherungs-Agent überträgt Daten zurück zum Sicherungsserver.
2.  Der Sicherungsserver schreibt Daten auf die mehrstufigen StorSimple-Volumes.
3.  Der Sicherungsserver aktualisiert die Katalogdatenbank und schließt dann den Sicherungsauftrag ab.
4.  Ein Momentaufnahmenskript löst die Verwaltung von StorSimple-Cloudmomentaufnahmen aus (starten oder löschen).
5.  Der Sicherungsserver löscht die abgelaufenen Sicherungen basierend auf einer Aufbewahrungsrichtlinie.

### <a name="primary-target-restore-logical-steps"></a>Logische Schritte bei der Wiederherstellung des Primärziels

1.  Der Sicherungsserver startet die Wiederherstellung der entsprechenden Daten aus dem Speicherrepository.
2.  Der Sicherungs-Agent empfängt die Daten vom Sicherungsserver.
3.  Der Sicherungsserver schließt den Wiederherstellungsauftrag ab.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple als sekundäres Sicherungsziel

In diesem Szenario werden die StorSimple-Volumes hauptsächlich für die langfristige Aufbewahrung oder Archivierung verwendet.

Die folgende Abbildung veranschaulicht die Architektur, in der die anfänglichen Sicherungen und Wiederherstellungen ein Hochleistungsvolume als Ziel haben. Diese Sicherungen werden kopiert und nach einem angegebenen Zeitplan auf einem mehrstufigen StorSimple-Volume archiviert.

Es ist wichtig, das Hochleistungsvolume entsprechend zu bemessen, um die Kapazitäts- und Leistungsanforderungen der Aufbewahrungsrichtlinie zu erfüllen.

![Ein Diagramm, das die Architektur, in der die anfänglichen Sicherungen und Wiederherstellungen ein Hochleistungsvolume als Ziel haben, veranschaulicht.](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logische Schritte bei der Sicherung des Sekundärziels

1.  Der Sicherungsserver kontaktiert den Sicherungs-Agent auf dem Ziel, und der Sicherungs-Agent überträgt Daten zurück zum Sicherungsserver.
2.  Der Sicherungsserver schreibt Daten in den Hochleistungsspeicher.
3.  Der Sicherungsserver aktualisiert die Katalogdatenbank und schließt dann den Sicherungsauftrag ab.
4.  Der Sicherungsserver kopiert Sicherungen basierend auf einer Aufbewahrungsrichtlinie nach StorSimple.
5.  Ein Momentaufnahmenskript löst die Verwaltung von StorSimple-Cloudmomentaufnahmen aus (starten oder löschen).
6.  Der Sicherungsserver löscht die abgelaufenen Sicherungen basierend auf einer Aufbewahrungsrichtlinie.

### <a name="secondary-target-restore-logical-steps"></a>Logische Schritte bei der Wiederherstellung des Sekundärziels

1.  Der Sicherungsserver startet die Wiederherstellung der entsprechenden Daten aus dem Speicherrepository.
2.  Der Sicherungs-Agent empfängt die Daten vom Sicherungsserver.
3.  Der Sicherungsserver schließt den Wiederherstellungsauftrag ab.

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Für das Bereitstellen der Lösung sind drei Schritte erforderlich:
1. Vorbereiten der Netzwerkinfrastruktur
2. Bereitstellen Ihres StorSimple-Geräts als Sicherungsziel
3. Bereitstellen von Veritas NetBackup

Jeder Schritt wird in den folgenden Abschnitten ausführlich erläutert.

### <a name="set-up-the-network"></a>Einrichten des Netzwerks

Da StorSimple eine in die Azure-Cloud integrierte Lösung ist, erfordert StorSimple eine aktive und funktionierende Verbindung mit der Azure-Cloud. Dieser Verbindung wird für Vorgänge wie z.B. Cloudmomentaufnahmen, Datenverwaltung, Metadatenübertragung sowie zum Tiering älterer, seltener genutzter Daten in Azure-Cloudspeicher verwendet.

Damit die Lösung optimal funktioniert, empfehlen sich die folgenden bewährten Methoden für die Netzwerkeinrichtung:

-   Der Link, der das StorSimple-Tiering mit Azure verbindet, muss die Bandbreitenanforderungen erfüllen. Ordnen Sie hier Ihren Infrastrukturswitches die erforderliche Servicequalitätsstufe zu, um Ihre SLAs für Recovery Point Objective (RTO) und Recovery Time Objective (RTO) zu erfüllen.

-   Die maximalen Zugriffslatenzen für Azure Blob Storage sollten um 80 Millisekunden betragen.

### <a name="deploy-storsimple"></a>Bereitstellen von StorSimple

Eine detaillierte Anleitung für die Bereitstellung von StorSimple finden Sie unter [Bereitstellen lokaler StorSimple-Geräte](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Bereitstellen von NetBackup

Eine Schrittanleitung für die Bereitstellung von NetBackup 7.7.x finden Sie in der [Dokumentation zu NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Einrichten der Lösung

In diesem Abschnitt zeigen wir Ihnen einige Konfigurationsbeispiele. Die folgenden Beispiele und Empfehlungen veranschaulichen die einfachste und grundlegendste Implementierung. Diese Implementierung eignet sich möglicherweise nicht direkt für Ihre spezifischen Sicherungsanforderungen.

### <a name="set-up-storsimple"></a>Einrichten von StorSimple

| StorSimple-Bereitstellungsaufgaben  | Weitere Kommentare |
|---|---|
| Stellen Sie Ihr lokales StorSimple-Gerät bereit. | Unterstützte Versionen: Update 3 und höher. |
| Schalten Sie das Sicherungsziel ein. | Verwenden Sie diese Befehle zum Aktivieren oder Deaktivieren des Sicherungszielmodus und Abrufen des Status. Weitere Informationen finden Sie unter [Herstellen einer Remoteverbindung mit einem StorSimple-Gerät](./storsimple-8000-remote-connect.md).</br> So aktivieren Sie den Sicherungsmodus: `Set-HCSBackupApplianceMode -enable`. </br> So deaktivieren Sie den Sicherungsmodus: `Set-HCSBackupApplianceMode -disable`. </br> So rufen Sie den aktuellen Status der Sicherungsmoduseinstellungen ab: `Get-HCSBackupApplianceMode`. |
| Erstellen Sie einen gemeinsamen Volumecontainer für das Volume, auf dem die Sicherungsdaten gespeichert sind. Alle Daten in einem Volumecontainer sind dedupliziert. | StorSimple-Volumecontainer definieren Deduplizierungsdomänen.  |
| Erstellen Sie StorSimple-Volumes. | Erstellen Sie Volumes, deren Größen so nah wie möglich an der prognostizierten Nutzung liegen, da sich die Größe eines Volumes auf die Dauer von Cloudmomentaufnahmen auswirkt. Informationen zum Dimensionieren eines Volumes finden Sie unter [Aufbewahrungsrichtlinien](#retention-policies).</br> </br> Verwenden Sie mehrstufige StorSimple-Volumes, und aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**. </br> Die ausschließliche Verwendung von lokalen Volumes wird nicht unterstützt. |
| Erstellen Sie eine eindeutige StorSimple-Sicherungsrichtlinie für alle Sicherungszielvolumes. | Eine StorSimple-Sicherungsrichtlinie definiert die Volumekonsistenzgruppe. |
| Deaktivieren Sie den Zeitplan, wenn die Momentaufnahmen ablaufen. | Momentaufnahmen werden als Nachverarbeitungsvorgang ausgelöst. |

### <a name="set-up-the-host-backup-server-storage"></a>Einrichten des Speichers auf dem Sicherungshostserver

Richten Sie den Speicher auf dem Sicherungshostserver gemäß den folgenden Richtlinien ein:  

- Verwenden Sie keine (von der Windows-Datenträgerverwaltung erstellten) übergreifenden Volumes, da diese nicht unterstützt werden.
- Formatieren Sie die Volumes mit NTFS und einer Zuordnungsgröße von 64 KB.
- Ordnen Sie die StorSimple-Volumes direkt dem NetBackup-Server zu.
    - Verwenden Sie iSCSI für physische Server.
    - Verwenden Sie Passthrough-Datenträger für virtuelle Server.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Bewährte Methoden für StorSimple und NetBackup

Richten Sie Ihre Lösung gemäß den Leitlinien in den folgenden Abschnitten ein.

### <a name="operating-system-best-practices"></a>Bewährte Methoden für das Betriebssystem

- Deaktivieren Sie die Windows Server-Verschlüsselung und -Deduplizierung für das NTFS-Dateisystem.
- Deaktivieren Sie die Windows Server-Defragmentierung auf den StorSimple-Volumes.
- Deaktivieren Sie die Windows Server-Indizierung auf den StorSimple-Volumes.
- Führen Sie einen Virenscan auf dem Quellhost durch (nicht auf den StorSimple-Volumes).
- Deaktivieren Sie die standardmäßige [Windows Server-Wartung](/windows/win32/w8cookbook/automatic-maintenance) im Task-Manager. Wählen Sie dazu eine der folgenden Methoden:
  - Deaktivieren Sie die Wartungskonfiguration in der Windows-Aufgabenplanung.
  - Laden Sie [PsExec](/sysinternals/downloads/psexec) von Windows Sysinternals herunter. Nachdem Sie PsExec heruntergeladen haben, führen Sie Windows-PowerShell als Administrator aus, und geben Sie Folgendes ein:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Bewährte Methoden für StorSimple

-   Stellen Sie sicher, dass das StorSimple-Gerät die Version [Update 3 oder höher](./index.yml) aufweist.
-   Isolieren Sie iSCSI- und Clouddatenverkehr. Verwenden Sie dedizierte iSCSI-Verbindungen für Datenverkehr zwischen StorSimple und dem Sicherungsserver.
-   Stellen Sie sicher, dass Ihr StorSimple-Gerät ein dediziertes Sicherungsziel ist. Gemischte Workloads werden nicht unterstützt, da sie sich auf die RTO- und RPO-Ziele auswirken.

### <a name="netbackup-best-practices"></a>Bewährte Methoden für NetBackup

-   Die NetBackup-Datenbank sollte sich lokal auf dem Server und nicht auf einem StorSimple-Volume befinden.
-   Zur Notfallwiederherstellung sichern Sie Ihre NetBackup-Datenbank auf einem StorSimple-Volume.
-   Wir unterstützen vollständige und inkrementelle Sicherungen von NetBackup (in NetBackup auch differenzielle inkrementelle Sicherungen genannt) für diese Lösung. Es empfiehlt sich nicht, synthetische und kumulative inkrementelle Sicherungen zu verwenden.
-   Sicherungsdatendateien sollten nur die Daten für einen bestimmten Auftrag enthalten. Es können beispielsweise keine Medien aus unterschiedlichen Aufträgen angefügt werden.

Neueste Informationen zu NetBackup-Einstellungen und bewährten Methoden für die Implementierung dieser Anforderungen finden Sie auf der [Website von Veritas](https://www.veritas.com) in der Dokumentation zu NetBackup.


## <a name="retention-policies"></a>Aufbewahrungsrichtlinien

Eine der gängigsten Aufbewahrungsrichtlinien für Sicherungen ist die GFS-Richtlinie (Grandfather, Father, Son). Eine GFS-Richtlinie sieht tägliche inkrementelle Sicherungen und wöchentliche und monatliche vollständige Sicherungen vor. Diese Richtlinie führt zu sechs mehrstufigen StorSimple-Volumes. Ein Volume enthält die wöchentlichen, monatlichen und jährlichen vollständigen Sicherungen. Auf den anderen fünf Volumes werden die täglichen inkrementellen Sicherungen gespeichert.

Im folgenden Beispiel verwenden wir eine GFS-Rotation. Bei diesem Beispiel wird Folgendes vorausgesetzt:

-   Es werden nicht deduplizierte und nicht komprimierte Daten verwendet.
-   Die vollständigen Sicherungen umfassen jeweils 1 TiB.
-   Die täglichen inkrementellen Sicherungen umfassen jeweils 500 GiB.
-   Vier wöchentliche Sicherungen werden einen Monat lang aufbewahrt.
-   Zwölf monatliche Sicherungen werden ein Jahr lang aufbewahrt.
-   Eine jährliche Sicherung wird zehn Jahre lang aufbewahrt.

Erstellen Sie basierend auf diesen Annahmen ein mehrstufiges StorSimple-Volume mit 26 TiB für die monatlichen und jährlichen vollständigen Sicherungen. Erstellen Sie ein mehrstufiges StorSimple-Volume mit 5 TiB für jede der täglichen inkrementellen Sicherungen.

| Sicherungstyp | Größe (TiB) | GFS-Multiplikator\* | Gesamtkapazität (TiB)  |
|---|---|---|---|
| Wöchentlich vollständig | 1 | 4  | 4 |
| Täglich inkrementell | 0.5 | 20 (Zyklen entsprechen der Anzahl von Wochen pro Monat) | 12 (2 für zusätzliches Kontingent) |
| Monatlich vollständig | 1 | 12 | 12 |
| Jährlich vollständig | 1  | 10 | 10 |
| GFS-Anforderung |   | 38 |   |
| Zusätzliches Kontingent  | 4  |   | 42 (GFS-Anforderung gesamt)  |

\* Der GFS-Multiplikator ist die Anzahl von Kopien, die Sie schützen und beibehalten müssen, um die Anforderungen Ihrer Sicherungsrichtlinie zu erfüllen.

## <a name="set-up-netbackup-storage"></a>Einrichten von NetBackup-Speicher

### <a name="to-set-up-netbackup-storage"></a>So richten Sie NetBackup-Speicher ein

1.  Wählen Sie in der NetBackup-Verwaltungskonsole **Media and Device Management** > **Devices** > **Disk Pools** (Medien- und Geräteverwaltung>Geräte>Datenträgerpools) aus. Wählen Sie im Disk Pool Configuration Wizard (Konfigurations-Assistent für Datenträgerpools) den Speicherservertyp **AdvancedDisk** aus, und klicken Sie dann auf **Next** (Weiter).

    ![NetBackup-Verwaltungskonsole, Konfigurations-Assistent für Datenträgerpools](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Wählen Sie Ihren Server aus, und klicken Sie dann auf **Next** (Weiter).

    ![NetBackup-Verwaltungskonsole, Server auswählen](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Wählen Sie Ihr StorSimple-Volume aus.

    ![NetBackup-Verwaltungskonsole, StorSimple-Volumedatenträger auswählen](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Geben Sie einen Namen für das Sicherungsziel ein, und wählen Sie dann **Next** > **Next** (Weiter>Weiter), um den Assistenten zu beenden.

5.  Überprüfen Sie die Einstellungen, und klicken Sie auf **Finish** (Fertig stellen).

6.  Ändern Sie nach jeder Volumezuordnung die Einstellungen des Speichergeräts so, dass sie den empfohlenen Einstellungen [Bewährte Methoden für StorSimple und NetBackup](#best-practices-for-storsimple-and-netbackup) entsprechen.

7. Wiederholen Sie die Schritte 1-6, bis Sie alle Ihre StorSimple-Volumes zugewiesen haben.

    ![NetBackup-Verwaltungskonsole, Datenträgerkonfiguration](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Einrichten von StorSimple als primäres Sicherungsziel

> [!NOTE]
> Die Wiederherstellung von Daten aus einer Sicherung in Cloudspeicher erfolgt mit der Geschwindigkeit der Cloud.

In der folgenden Abbildung wird die Zuordnung eines typischen Volumes zu einem Sicherungsauftrag gezeigt. In diesem Fall werden alle wöchentlichen Sicherungen dem Datenträger mit der vollständigen Sicherung von Samstag und die inkrementellen Sicherungen dem Datenträger mit den inkrementellen Sicherungen von Montag bis Freitag zugeordnet. Alle Sicherungen und Wiederherstellungen erfolgen mithilfe eines mehrstufigen StorSimple-Volumes.

![Logisches Diagramm der Konfiguration des primären Sicherungsziels](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple als primäres Sicherungsziel: Beispiel eines GFS-Zeitplans

Hier sehen Sie ein Beispiel für einen GFS-Rotationszeitplan für „Vier Wochen“, „Monatlich“ und „Jährlich“:

| Häufigkeit/Sicherungstyp | Vollständig | Inkrementell (Tag 1 - 5)  |   
|---|---|---|
| Wöchentlich (Woche 1 - 4) | Samstag | Montag - Freitag |
| Monatlich  | Samstag  |   |
| Jährlich | Samstag  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Zuweisen von StorSimple-Volumes zu einem NetBackup-Sicherungsauftrag

Bei der folgenden Sequenz wird vorausgesetzt, dass NetBackup und der Zielhost gemäß den NetBackup-Agent-Richtlinien konfiguriert sind.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>So weisen Sie StorSimple-Volumes einem NetBackup-Sicherungsauftrag zu

1. Wählen Sie in der NetBackup-Verwaltungskonsole **NetBackup Management** (NetBackup-Verwaltung) aus. Klicken Sie mit der rechten Maustaste auf **Policies** (Richtlinien), und wählen Sie dann **New Policy** (Neue Richtlinie) aus.

   ![NetBackup-Verwaltungskonsole, Erstellen einer neuen Richtlinie](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. Geben Sie im Dialogfeld **Add a New Policy** (Neue Richtlinie hinzufügen) einen neuen Namen für die Richtlinie ein, und aktivieren Sie dann das Kontrollkästchen **Use Policy Configuration Wizard** (Assistent für die Konfiguration von Richtlinien verwenden). Klicken Sie auf **OK**.

   ![NetBackup-Verwaltungskonsole, Dialogfeld „Neue Richtlinie hinzufügen“](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. Wählen Sie im Backup Policy Configuration Wizard (Assistent für die Konfiguration von Sicherungsrichtlinien) den gewünschten Sicherungstyp aus, und klicken Sie dann auf **Next** (Weiter).

   ![NetBackup-Verwaltungskonsole, Sicherungstyp auswählen](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Wählen Sie zum Festlegen des Richtlinientyps **Standard** aus, und klicken Sie dann auf **Next** (Weiter).

   ![NetBackup-Verwaltungskonsole, Richtlinientyp auswählen](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Wählen Sie Ihren Host aus, aktivieren Sie das Kontrollkästchen **Detect client operating system** (Clientbetriebssystem ermitteln), und klicken Sie dann auf **Add** (Hinzufügen). Wählen Sie **Weiter** aus.

   ![NetBackup-Verwaltungskonsole, Clients in einer neuen Richtlinie auflisten](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Wählen Sie die Laufwerke aus, die Sie sichern möchten.

   ![NetBackup-Verwaltungskonsole, Sicherungsoptionen für eine neue Richtlinie](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Wählen Sie Werte für die Häufigkeit und Aufbewahrungsdauer, die Ihre Anforderungen an die Sicherungsrotation erfüllen.

   ![NetBackup-Verwaltungskonsole, Sicherungshäufigkeit und -rotation für eine neue Richtlinie](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Klicken Sie auf **Next** > **Next** > **Finish** (Weiter>Weiter>Fertig stellen).  Sie können den Zeitplan ändern, nachdem die Richtlinie erstellt wurde.

9. Erweitern Sie die zuvor erstellte Richtlinie, und wählen Sie **Schedules** (Zeitpläne) aus.

   ![NetBackup-Verwaltungskonsole, Zeitpläne für eine neue Richtlinie](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Klicken Sie mit der rechten Maustaste auf **Differential-Inc** (Differenziell/Ink.), wählen Sie **Copy to new** (In neuen kopieren), und klicken Sie dann auf **OK**.

    ![NetBackup-Verwaltungskonsole, Zeitplan in neue Richtlinie kopieren](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Klicken Sie mit der rechten Maustaste auf den neu erstellten Zeitplan, und wählen Sie **Change** (Ändern) aus.

12. Aktivieren Sie auf der Registerkarte **Attributes** (Attribute) das Kontrollkästchen **Override policy storage selection** (Richtlinienauswahl für Speicher außer Kraft setzen), und wählen Sie das Volume aus, auf dem die inkrementellen Sicherungen von Montag gespeichert werden.

    ![NetBackup-Verwaltungskonsole, Zeitplan ändern](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Wählen Sie auf der Registerkarte **Start Windows** (Startfenster) das gewünschte Zeitfenster für Ihre Sicherungen.

    ![NetBackup-Verwaltungskonsole, Startfenster ändern](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Klicken Sie auf **OK**.

15. Wiederholen Sie die Schritte 10-14 für jede inkrementelle Sicherung. Wählen Sie das entsprechende Volume und den Zeitplan für die einzelnen Sicherungen aus, die Sie erstellen.

16. Klicken Sie dann mit der rechten Maustaste auf den Zeitplan **Differential-Inc**, und löschen Sie ihn.

17. Ändern Sie den Zeitplan „Full“ (Vollständig) entsprechend Ihren Sicherungsanforderungen.

    ![NetBackup-Verwaltungskonsole, Zeitplan „Vollständig“ ändern](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Ändern Sie das Startfenster.

    ![NetBackup-Verwaltungskonsole, Startfenster ändern](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Der endgültige Zeitplan sieht wie folgt aus:

    ![NetBackup-Verwaltungskonsole, endgültiger Zeitplan](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Einrichten von StorSimple als sekundäres Sicherungsziel

> [!NOTE]
>Die Wiederherstellung von Daten aus einer Sicherung in Cloudspeicher erfolgt mit der Geschwindigkeit der Cloud.

In diesem Modell muss ein Speichermedium (nicht StorSimple) vorhanden sein, das als temporärer Cache dient. Sie können ein RAID-Volume (Redundant Array of Independent Disks) nutzen, um die Anforderungen an Speicherplatz, Ein-/Ausgabe und Bandbreite zu erfüllen. Wir empfehlen RAID 5, 50 und 10.

Die folgende Abbildung zeigt typische Volumes für die kurzfristige lokale Aufbewahrung (auf dem Server) und die langfristige Aufbewahrung im Archiv. In diesem Szenario erfolgen alle Sicherungen auf dem lokalen RAID-Volume (auf dem Server). Diese Sicherungen werden regelmäßig dupliziert und auf einem Archivvolume archiviert. Es ist wichtig, die Größe des lokalen RAID-Volumes (auf dem Server) so einzurichten, dass die Kapazitäts- und Leistungsanforderungen für die kurzfristige Aufbewahrung erfüllt werden.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple als sekundäres Sicherungsziel – GFS-Beispiel

![StorSimple als sekundäres Sicherungsziel – logisches Diagramm](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

In der folgenden Tabelle wird gezeigt, wie Sicherungen für die Ausführung auf den lokalen und StorSimple-Datenträgern eingerichtet werden. Die Tabelle enthält individuelle und gesamte Kapazitätsanforderungen.

### <a name="backup-configuration-and-capacity-requirements"></a>Sicherungskonfiguration und Kapazitätsanforderungen

| Sicherungstyp und Aufbewahrung | Konfigurierter Speicher | Größe (TiB) | GFS-Multiplikator | Gesamtkapazität \* (TiB) |
|---|---|---|---|---|
| Woche 1 (vollständig und inkrementell) |Lokaler Datenträger (kurzfristig)| 1 | 1 | 1 |
| StorSimple, Woche 2-4 |StorSimple-Datenträger (langfristig) | 1 | 4 | 4 |
| Monatlich vollständig |StorSimple-Datenträger (langfristig) | 1 | 12 | 12 |
| Jährlich vollständig |StorSimple-Datenträger (langfristig) | 1 | 1 | 1 |
|Größenanforderungen für GFS-Volumes |  |  |  | 18*|

\* Die Gesamtkapazität umfasst 17 TiB für StorSimple-Datenträger und 1 TiB für lokale RAID-Volumes.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS-Beispielzeitplan: GFS-Rotation mit wöchentlichem, monatlichem und jährlichem Zeitplan

| Week | Vollständig | Inkrementell, Tag 1 | Inkrementell, Tag 2 | Inkrementell, Tag 3 | Inkrementell, Tag 4 | Inkrementell, Tag 5 |
|---|---|---|---|---|---|---|
| Woche 1 | Lokales RAID-Volume  | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume |
| Woche 2 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Woche 3 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Woche 4 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Monatlich | StorSimple monatlich |   |   |   |   |   |
| Jährlich | StorSimple jährlich  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Zuweisen von StorSimple-Volumes zu einem NetBackup-Archivierungs- und Deduplizierungsauftrag

Da NetBackup eine Vielzahl von Optionen für die Speicher- und Medienverwaltung bietet, wird empfohlen, Veritas oder Ihren NetBackup-Architekten zu konsultieren, um Ihre Anforderungen an die Richtlinie für den Speicherlebenszyklus ordnungsgemäß zu bewerten.

Nachdem Sie die ursprünglichen Datenträgerpools definiert haben, müssen Sie drei zusätzliche Richtlinien für den Speicherlebenszyklus definieren, um insgesamt vier zu erhalten:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>So weisen Sie StorSimple-Volumes einem NetBackup-Archivierungs- und Deduplizierungsauftrag zu

1. Wählen Sie in der NetBackup-Verwaltungskonsole **Storage** > **Storage Lifecycle Policies** > **New Storage Lifecycle Policy** (Speicher>Richtlinien für den Speicherlebenszyklus>Neue Richtlinie für den Speicherlebenszyklus) aus.

   ![NetBackup-Verwaltungskonsole, neue Richtlinie für den Speicherlebenszyklus](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Geben Sie einen Namen für die Momentaufnahme ein, und klicken Sie dann auf **Add** (Hinzufügen).

3. Wählen Sie im Dialogfeld **New Operation** (Neuer Vorgang) auf der Registerkarte **Properties** (Eigenschaften) für **Operation** (Vorgang) **Backup** (Sicherung) aus. Wählen Sie die gewünschten Werte für **Destination storage** (Zielspeicher), **Retention type** (Aufbewahrungstyp) und **Retention period** (Aufbewahrungszeitraum). Klicken Sie auf **OK**.

   ![NetBackup-Verwaltungskonsole, Dialogfeld „Neuer Vorgang“](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Dies definiert den ersten Sicherungsvorgang und das erste Repository.

4. Markieren Sie den vorherigen Vorgang, und wählen Sie dann **Add** (Hinzufügen) aus. Wählen Sie im Dialogfeld **Change Storage Operation** (Speichervorgang ändern) die gewünschten Werte für **Destination storage** (Zielspeicher), **Retention type** (Aufbewahrungstyp) und **Retention period** (Aufbewahrungszeitraum) aus.

   ![NetBackup-Verwaltungskonsole, Dialogfeld „Speichervorgang ändern“](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Markieren Sie den vorherigen Vorgang, und wählen Sie dann **Add** (Hinzufügen) aus. Fügen Sie im Dialogfeld **New Storage Lifecycle Policy** (Neue Richtlinie für den Speicherlebenszyklus) monatliche Sicherungen für ein Jahr hinzu.

   ![NetBackup-Verwaltungskonsole, Dialogfeld „Neue Richtlinie für den Speicherlebenszyklus“](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Wiederholen Sie die Schritte 4-5, bis Sie die benötigte Aufbewahrungsrichtlinie erstellt haben.

   ![NetBackup-Verwaltungskonsole, Hinzufügen von Richtlinien im Dialogfeld „Neue Richtlinie für den Speicherlebenszyklus“](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Definieren Sie nach Fertigstellung der Definition Ihrer Aufbewahrungsrichtlinie unter **Policy** (Richtlinie) eine Sicherungsrichtlinie gemäß den Anweisungen unter [Zuweisen von StorSimple-Volumes zu einem NetBackup-Sicherungsauftrag](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. Klicken Sie unter **Schedules** (Zeitpläne) im Dialogfeld **Change Schedule** (Zeitplan ändern) mit der rechten Maustaste auf **Full** (Vollständig), und wählen Sie dann **Change** (Ändern) aus.

   ![NetBackup-Verwaltungskonsole, Dialogfeld „Zeitplan ändern“](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Aktivieren Sie das Kontrollkästchen **Override policy storage selection** (Richtlinie für Speicherauswahl überschreiben), und wählen Sie die Aufbewahrungsrichtlinie aus, die Sie in den Schritten 1-6 erstellt haben.

   ![NetBackup-Verwaltungskonsole, Richtlinie für Speicherauswahl überschreiben](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Klicken Sie auf **OK**, und wiederholen Sie die Schritte für den Zeitplan für inkrementelle Sicherungen.

    ![NetBackup-Verwaltungskonsole, Dialogfeld „Zeitplan ändern“ für inkrementelle Sicherungen](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Sicherungstyp | Größe (TiB) | GFS-Multiplikator\* | Gesamtkapazität (TiB)  |
|---|---|---|---|
| Wöchentlich vollständig |  1  |  4 | 4  |
| Täglich inkrementell  | 0.5  | 20 (Zyklen entsprechen der Anzahl von Wochen pro Monat) | 12 (2 für zusätzliches Kontingent) |
| Monatlich vollständig  | 1 | 12 | 12 |
| Jährlich vollständig | 1  | 10 | 10 |
| GFS-Anforderung  |     |     | 38 |
| Zusätzliches Kontingent  | 4  |    | 42 (GFS-Anforderung gesamt) |

\* Der GFS-Multiplikator ist die Anzahl von Kopien, die Sie schützen und beibehalten müssen, um die Anforderungen Ihrer Sicherungsrichtlinie zu erfüllen.

## <a name="storsimple-cloud-snapshots"></a>StorSimple-Cloudmomentaufnahmen

StorSimple-Cloudmomentaufnahmen schützen die Daten, die sich auf Ihrem StorSimple-Gerät befinden. Das Erstellen einer Cloudmomentaufnahme ist gleichbedeutend mit dem Überführen lokaler Sicherungsbänder an einen externen Standort. Bei Verwendung von georedundantem Azure-Speicher entspricht das Erstellen einer Cloudmomentaufnahme dem Versenden mehrerer Bänder an mehrere Standorte. Wenn nach einem Notfall eine Gerätewiederherstellung erforderlich ist, können Sie ein anderes StorSimple-Gerät online schalten und ein Failover ausführen. Nach dem Failover können Sie aus der jüngsten Cloudmomentaufnahme (mit der Geschwindigkeit der Cloud) auf die Daten zugreifen.

Der folgende Abschnitt zeigt, wie Sie ein kurzes Skript schreiben, um StorSimple-Cloudmomentaufnahmen während der Nachverarbeitung von Sicherungen zu starten und zu löschen.

> [!NOTE]
> Manuell oder programmgesteuert erstellte Momentaufnahmen folgen nicht der Ablaufrichtlinie für StorSimple-Momentaufnahmen. Diese Momentaufnahmen müssen manuell oder programmgesteuert gelöscht werden.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starten und Löschen von Cloudmomentaufnahmen mit einem Skript

> [!NOTE]
> Bewerten Sie sorgfältig die Auswirkungen auf Compliance und Datenaufbewahrung, bevor Sie eine StorSimple-Momentaufnahme löschen. Weitere Informationen zum Ausführen eines Skripts nach der Sicherung finden Sie in der [NetBackup-Dokumentation](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Sicherungslebenszyklus

![Diagramm zum Sicherungslebenszyklus](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Requirements (Anforderungen)

-   Der Server, auf dem das Skript ausgeführt wird, muss auf Azure-Cloudressourcen zugreifen können.
-   Das Benutzerkonto muss über die notwendigen Berechtigungen verfügen.
-   Eine StorSimple-Sicherungsrichtlinie mit zugehörigen StorSimple-Volumes muss eingerichtet, aber nicht aktiviert sein.
-   Sie benötigen den Ressourcennamen, Registrierungsschlüssel, Gerätenamen und die Sicherungsrichtlinien-ID von StorSimple.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>So starten oder löschen Sie eine Cloudmomentaufnahme

1. [Installieren Sie Azure PowerShell](/powershell/azure/).
2. Herunterladen und Einrichten des [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1)-PowerShell-Skripts.
3. Führen Sie auf dem Server, auf dem das Skript ausgeführt wird, PowerShell als Administrator aus. Stellen Sie sicher, dass Sie das Skript mit `-WhatIf $true` ausführen, um festzustellen, welche Änderungen das Skript durchführt. Sobald die Überprüfung abgeschlossen ist, lassen Sie `-WhatIf $false` ausführen. Führen Sie den folgenden Befehl aus:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Fügen Sie das Skript Ihrem Sicherungsauftrag in NetBackup hinzu. Bearbeiten Sie hierzu die Befehle zur Vor- und Nachverarbeitung in den Optionen Ihres NetBackup-Auftrags.

> [!NOTE]
> Es empfiehlt sich, die Sicherungsrichtlinie für StorSimple-Cloudmomentaufnahmen als Nachverarbeitungsskript am Ende des täglichen Sicherungsauftrags auszuführen. Weitere Informationen zum Sichern und Wiederherstellen Ihrer Sicherungsanwendungsumgebung, um Ihre RPO- und RTO-Ziele zu erfüllen, erhalten Sie von Ihrem Sicherungsarchitekten.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als Wiederherstellungsquelle

Wiederherstellungen aus einem StorSimple-Gerät funktionieren ähnlich wie Wiederherstellungen aus anderen Blockspeichergeräten. Bei Daten, die per Tiering in der Cloud gespeichert wurden, erfolgt die Wiederherstellung mit der Geschwindigkeit der Cloud. Bei lokalen Daten erfolgt die Wiederherstellung mit der Geschwindigkeit des lokalen Datenträgers des Geräts. Informationen zum Ausführen einer Wiederherstellung finden Sie in der [NetBackup-Dokumentation](http://www.veritas.com/docs/000094423). Es wird empfohlen, die bewährten NetBackup-Wiederherstellungsmethoden zu befolgen.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple-Failover und -Notfallwiederherstellung

> [!NOTE]
> In Sicherungszielszenarien wird die StorSimple Cloud Appliance nicht als Wiederherstellungsziel unterstützt.

Ein Notfall kann durch eine Vielzahl von Faktoren verursacht werden. In der folgenden Tabelle sind häufige Notfallwiederherstellungsszenarien aufgeführt.

| Szenario | Auswirkung | Wiederherstellung | Notizen |
|---|---|---|---|
| Ausfall eines StorSimple-Geräts | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Ersetzen Sie das ausgefallene Gerät, und führen Sie die [Schritte für StorSimple-Failover und -Notfallwiederherstellung](./storsimple-8000-device-failover-disaster-recovery.md) durch. | Wenn nach der Wiederherstellung des Geräts eine Datenwiederherstellung erforderlich ist, werden die vollständigen Arbeitssätze mit Daten aus der Cloud auf das neue Gerät abgerufen. Alle Vorgänge erfolgen mit der Geschwindigkeit der Cloud. Dieses erneute Scannen von Index und Katalog kann dazu führen, dass alle Sicherungssätze gescannt und aus der Cloudspeicherstufe in die lokale Speicherstufe des Geräts übertragen werden. Dies kann ein sehr zeitaufwendiger Prozess sein. |
| Ausfall des NetBackup-Servers | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Erstellen Sie den Sicherungsserver neu, und führen Sie eine Datenbankwiederherstellung durch. | Sie können den NetBackup-Server am Notfallwiederherstellungsstandort neu erstellen oder wiederherstellen. Stellen Sie die Datenbank auf den jüngsten Zeitpunkt wieder her. Wenn die wiederhergestellte NetBackup-Datenbank nicht mit Ihren jüngsten Sicherungsaufträgen synchron ist, ist eine Indizierung und Katalogisierung erforderlich. Das erneute Scannen von Index und Katalog kann dazu führen, dass alle Sicherungssätze gescannt und aus der Cloudspeicherstufe in die lokale Speicherstufe des Geräts übertragen werden. Damit wird diese Aufgabe noch zeitaufwendiger. |
| Standortausfall, der zum Verlust des Sicherungsservers und von StorSimple führt | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Stellen Sie zuerst StorSimple und dann NetBackup wieder her. | Stellen Sie zuerst StorSimple und dann NetBackup wieder her. Wenn nach der Wiederherstellung des Geräts eine Datenwiederherstellung erforderlich ist, werden die vollständigen Arbeitssätze mit Daten aus der Cloud auf das neue Gerät abgerufen. Alle Vorgänge erfolgen mit der Geschwindigkeit der Cloud. |

## <a name="references"></a>References

Folgende Dokumente haben als Referenz für diesen Artikel gedient:

- [StorSimple multipath I/O setup](./storsimple-8000-configure-mpio-windows-server.md) (StorSimple: Einrichten von Multipfad-E/A)
- [Storage scenarios: Thin provisioning](/windows-hardware/drivers/storage/thin-provisioning) (Speicherszenarien: schlanke Speicherzuweisung)
- [Using GPT drives](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD) (Verwenden von GPT-Laufwerken)
- [Einrichten von Schattenkopien für freigegebene Ordner](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die [Wiederherstellung aus einem Sicherungssatz](./storsimple-8000-restore-from-backup-set-u2.md) durchführen.
- Erfahren Sie, wie Sie [ein Gerätefailover und eine Notfallwiederherstellung](./storsimple-8000-device-failover-disaster-recovery.md) ausführen.