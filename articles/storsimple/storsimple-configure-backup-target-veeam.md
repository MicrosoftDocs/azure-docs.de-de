---
title: StorSimple 8000-Serie als Sicherungsziel mit Veeam | Microsoft-Dokumentation
description: Erfahren Sie mehr über die StorSimple-Sicherungszielkonfiguration mit Veeam sowie die bewährten Methoden für die Integration beider Lösungen.
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
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: bf28265de2b297dade545695c9369b8074eeb72c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962551"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple als Sicherungsziel mit Veeam

## <a name="overview"></a>Übersicht

Azure StorSimple ist eine hybride Cloudspeicherlösung von Microsoft. StorSimple dient zum Bewältigen der Komplexität des exponentiellen Datenwachstums. Die Lösung verwendet dazu ein Azure Storage-Konto als Erweiterung der lokalen Lösung und verteilt Daten automatisch auf lokalen Speicher und Cloudspeicher.

In diesem Artikel werden die StorSimple-Integration mit Veeam sowie bewährte Methoden für die Integration beider Lösungen beschrieben. Außerdem erhalten Sie Empfehlungen, wie Sie Veeam für eine optimale Integration in StorSimple einrichten. Wir gehen auf die bewährten Methoden von Veeam für Sicherungsarchitekten und Administratoren ein, um Veeam optimal zum Erfüllen einzelner Sicherungsanforderungen und Vereinbarungen zum Servicelevel (SLAs) einzurichten.

In diesem Artikel werden Konfigurationsschritte und wichtige Konzepte veranschaulicht. Es handelt jedoch keinesfalls um eine detaillierte Anleitung für die Konfiguration oder Installation. Es wird von uns vorausgesetzt, dass die grundlegenden Komponenten und Infrastrukturen funktionieren und die hier beschriebenen Konzepte unterstützen können.

### <a name="who-should-read-this"></a>Zielgruppe dieses Artikels

Die Informationen in diesem Artikel sind vornehmlich für Sicherungsadministratoren, Speicheradministratoren und Speicherarchitekten gedacht, die über Kenntnisse zu Speichern, Windows Server 2012 R2, Ethernet, Clouddiensten und Veeam verfügen.

### <a name="supported-versions"></a>Unterstützte Versionen

-   Veeam 9 und höher
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
![Diagramm zu StorSimple-Tiering](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Speicherkapazität | 8100 | 8600 |
|---|---|---|
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

![StorSimple als primäres Sicherungsziel – logisches Diagramm](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![StorSimple als sekundäres Sicherungsziel – logisches Diagramm](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Bereitstellen von Veeam

Jeder Schritt wird in den folgenden Abschnitten ausführlich erläutert.

### <a name="set-up-the-network"></a>Einrichten des Netzwerks

Da StorSimple eine in die Azure-Cloud integrierte Lösung ist, erfordert StorSimple eine aktive und funktionierende Verbindung mit der Azure-Cloud. Dieser Verbindung wird für Vorgänge wie z.B. Cloudmomentaufnahmen, Datenverwaltung, Metadatenübertragung sowie zum Tiering älterer, seltener genutzter Daten in Azure-Cloudspeicher verwendet.

Damit die Lösung optimal funktioniert, empfehlen sich die folgenden bewährten Methoden für die Netzwerkeinrichtung:

-   Die Verbindung zwischen Ihrem StorSimple-Tiering und Azure muss die Bandbreitenanforderungen erfüllen. Ordnen Sie hier Ihren Infrastrukturswitches die erforderliche Servicequalitätsstufe zu, um Ihre SLAs für Recovery Point Objective (RTO) und Recovery Time Objective (RTO) zu erfüllen.
-   Die maximalen Zugriffslatenzen für Azure Blob Storage sollten um 80 Millisekunden betragen.

### <a name="deploy-storsimple"></a>Bereitstellen von StorSimple

Eine detaillierte Anleitung für die Bereitstellung von StorSimple finden Sie unter [Bereitstellen lokaler StorSimple-Geräte](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Bereitstellen von Veeam

Bewährte Methoden für die Veeam-Installation finden Sie unter [Veeam Backup & Replication Best Practices](https://bp.veeam.expert/) (Sicherung und Replikation mit Veeam – Bewährte Methoden) und im Benutzerhandbuch [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Veeam Help Center (Technische Dokumentation)).

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

- Verwenden Sie keine (von der Windows-Datenträgerverwaltung erstellten) übergreifenden Volumes. Übergreifende Volumes werden nicht unterstützt.
- Formatieren Sie die Volumes mit NTFS und einer Zuordnungseinheitsgröße von 64 KB.
- Ordnen Sie die StorSimple-Volumes direkt dem Veeam-Server zu.
    - Verwenden Sie iSCSI für physische Server.


## <a name="best-practices-for-storsimple-and-veeam"></a>Bewährte Methoden für StorSimple und Veeam

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

### <a name="veeam-best-practices"></a>Bewährte Methoden für Veeam

-   Die Veeam-Datenbank sollte sich lokal auf dem Server befinden, nicht auf einem StorSimple-Volume.
-   Für die Notfallwiederherstellung sichern Sie Ihre Veeam-Datenbank auf einem StorSimple-Volume.
-   Wir unterstützen vollständige und inkrementelle Sicherungen von Veeam für diese Lösung. Es empfiehlt sich nicht, synthetische und differenzielle Sicherungen zu verwenden.
-   Sicherungsdatendateien sollten nur die Daten für einen bestimmten Auftrag enthalten. Es können beispielsweise keine Medien aus unterschiedlichen Aufträgen angefügt werden.
-   Deaktivieren Sie die Auftragsüberprüfung. Falls erforderlich, sollte die Überprüfung nach dem letzten Sicherungsauftrag geplant werden. Es ist wichtig zu wissen, dass sich dieser Auftrag auf Ihr Sicherungsfenster auswirkt.
-   Aktivieren Sie die Vorabzuweisung von Medien.
-   Stellen Sie sicher, dass die parallele Verarbeitung aktiviert ist.
-   Deaktivieren Sie die Komprimierung.
-   Deaktivieren Sie die Deduplizierung im Sicherungsauftrag.
-   Legen Sie die Optimierung auf **LAN Target** (LAN-Ziel) fest.
-   Aktivieren Sie **Create active full backup** (Aktive vollständige Sicherung erstellen) (alle zwei Wochen).
-   Richten Sie im Sicherungsrepository die Option **Use per-VM backup files** (Sicherungsdateien pro VM verwenden) ein.
-   Legen Sie **Use multiple upload streams per job** (Mehrere Uploadstreams pro Auftrag verwenden) auf **8** fest (maximal 16 sind zulässig). Passen Sie diesen Wert je nach CPU-Auslastung auf dem StorSimple-Gerät an.

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

## <a name="set-up-veeam-storage"></a>Einrichten von Veeam-Speicher

### <a name="to-set-up-veeam-storage"></a>So richten Sie Veeam-Speicher ein

1.  Navigieren Sie in der Veeam-Konsole für die Sicherung und Replikation unter **Repository Tools** (Repositorytools) zu **Backup Infrastructure** (Sicherungsinfrastruktur). Klicken Sie mit der rechten Maustaste auf **Backup Repositories** (Sicherungsrepositorys), und wählen Sie dann die Option **Add Backup Repository** (Sicherungsrepository hinzufügen).

    ![Screenshot der Veeam-Verwaltungskonsole, auf dem die Option „Add Backup Repository“ (Sicherungsrepository hinzufügen) hervorgehoben wird](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  Geben Sie im Dialogfeld **New Backup Repository** (Neues Sicherungsrepository) einen Namen und eine Beschreibung für das Repository ein. Wählen Sie **Weiter** aus.

    ![Veeam-Verwaltungskonsole, Seite zum Eingeben von Name und Beschreibung](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Wählen Sie als Typ die Option **Microsoft Windows server** (Microsoft Windows-Server). Wählen Sie den Veeam-Server aus. Wählen Sie **Weiter** aus.

    ![Veeam-Verwaltungskonsole, Bildschirm zur Auswahl des Speicherrepositorytyps](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Zum Angeben des Speicherorts suchen Sie unter **Location** (Speicherort) nach dem Volume und wählen es aus. Aktivieren Sie das Kontrollkästchen **Limit maximum concurrent tasks to:** (Maximale gleichzeitige Aufgaben beschränken auf:), und legen Sie den Wert auf **4** fest. So wird sichergestellt, dass nur vier virtuelle Datenträger gleichzeitig verarbeitet werden, während die einzelnen virtuellen Computer (VMs) verarbeitet werden. Wählen Sie die Schaltfläche **Advanced** (Erweitert).

    ![Veeam-Verwaltungskonsole, Auswählen des Volumes](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  Aktivieren Sie im Dialogfeld **Storage Compatibility Settings** (Einstellungen für Speicherkompatibilität) das Kontrollkästchen **Use per-VM backup files** (Sicherungsdateien pro VM verwenden).

    ![Veeam-Verwaltungskonsole, Einstellungen für Speicherkompatibilität](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  Aktivieren Sie im Dialogfeld **New Backup Repository** (Neues Sicherungsrepository) das Kontrollkästchen **Enable vPower NFS service on the mount server (recommended)** (vPower NFS-Dienst auf dem Bereitstellungsserver aktivieren (empfohlen)). Wählen Sie **Weiter** aus.

    ![Screenshot der Veeam-Verwaltungskonsole, über die Sie ein neues Sicherungsrepository hinzufügen können](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Überprüfen Sie die Einstellungen, und wählen Sie **Weiter**.

    ![Veeam-Verwaltungskonsole, Seite mit Sicherungsrepositorys](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Dem Veeam-Server wird ein Repository hinzugefügt.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Einrichten von StorSimple als primäres Sicherungsziel

> [!IMPORTANT]
> Die Wiederherstellung von Daten aus einer Sicherung in Cloudspeicher erfolgt mit der Geschwindigkeit der Cloud.

In der folgenden Abbildung wird die Zuordnung eines typischen Volumes zu einem Sicherungsauftrag gezeigt. In diesem Fall werden alle wöchentlichen Sicherungen dem Datenträger mit der vollständigen Sicherung von Samstag und die inkrementellen Sicherungen dem Datenträger mit den inkrementellen Sicherungen von Montag bis Freitag zugeordnet. Alle Sicherungen und Wiederherstellungen erfolgen mithilfe eines mehrstufigen StorSimple-Volumes.

![Logisches Diagramm der Konfiguration des primären Sicherungsziels](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple als primäres Sicherungsziel: Beispiel eines GFS-Zeitplans

Hier sehen Sie ein Beispiel für einen GFS-Rotationszeitplan für „Vier Wochen“, „Monatlich“ und „Jährlich“:

| Häufigkeit/Sicherungstyp | Vollständig | Inkrementell (Tag 1 - 5)  |   
|---|---|---|
| Wöchentlich (Woche 1 - 4) | Samstag | Montag - Freitag |
| Monatlich  | Samstag  |   |
| Jährlich | Samstag  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Zuweisen von StorSimple-Volumes zu einem Veeam-Sicherungsauftrag

Erstellen Sie für ein Szenario mit primärem Sicherungsziel einen täglichen Auftrag mit Ihrem primären Veeam StorSimple-Volume. Erstellen Sie für ein Szenario mit sekundärem Sicherungsziel einen täglichen Auftrag, indem Sie Speicher vom Typ DAS (Direct Attached Storage), NAS (Network Attached Storage) oder JBOD (Just a Bunch of Disks) verwenden.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>So weisen Sie StorSimple-Volumes einem Veeam-Sicherungsauftrag zu

1.  Wählen Sie in der Veeam-Konsole für Sicherung und Replikation die Option **Backup & Replication** (Sicherung und Replikation). Klicken Sie mit der rechten Maustaste auf **Backup** (Sicherung), und wählen Sie dann je nach Umgebung die Option **VMware** oder **Hyper-V**.

    ![Veeam-Verwaltungskonsole, neuer Sicherungsauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  Geben Sie im Dialogfeld **New Backup Job** (Neuer Sicherungsauftrag) einen Namen und eine Beschreibung für den täglichen Sicherungsauftrag ein.

    ![Screenshot der Veeam-Verwaltungskonsole, über die Sie den Namen und die Beschreibung hinzufügen können](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Wählen Sie einen virtuellen Computer aus, auf den die Sicherung erfolgen soll.

    ![Screenshot der Veeam-Verwaltungskonsole, über die Sie die VM auswählen können](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Wählen Sie für **Backup proxy** (Sicherungsproxy) und **Backup repository** (Sicherungsrepository) die gewünschten Werte aus. Wählen Sie einen Wert für **Restore points to keep on disk** (Auf Datenträger beizubehaltende Wiederherstellungspunkte) auf dem lokal verbundenen Speicher gemäß den RPO- und RTO-Definitionen für Ihre Umgebung aus. Wählen Sie **Erweitert** aus.

    ![Veeam-Verwaltungskonsole, Seite für neuen Sicherungsauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. Wählen Sie im Dialogfeld **Advanced Settings** (Erweiterte Einstellungen) auf der Registerkarte **Backup** (Sicherung) die Option **Incremental** (Inkrementell). Achten Sie darauf, dass das Kontrollkästchen **Create synthetic full backups periodically** (Regelmäßig synthetische vollständige Sicherungen erstellen) deaktiviert ist. Aktivieren Sie das Kontrollkästchen **Create active full backups periodically** (Regelmäßig aktive vollständige Sicherungen erstellen). Aktivieren Sie unter **Active full backup** (Aktive vollständige Sicherung) das Kontrollkästchen **Weekly on selected days** (Wöchentlich an ausgewählten Tagen) für Samstag.

    ![Screenshot der Veeam-Verwaltungskonsole: Seite „Erweiterte Einstellungen“ für den neuen Sicherungsauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Stellen Sie auf der Registerkarte **Storage** (Speicher) sicher, dass das Kontrollkästchen **Enable inline data deduplication** (Inline-Datendeduplizierung aktivieren) deaktiviert ist. Aktivieren Sie das Kontrollkästchen **Exclude swap file blocks** (Zugriffsschutz für Auslagerungsdateien ausschließen) und das Kontrollkästchen **Exclude deleted file blocks** (Zugriffsschutz für gelöschte Dateien ausschließen). Legen Sie **Compression level** (Komprimierungsgrad) auf **None** (Kein) fest. Legen Sie **Storage optimization** (Speicheroptimierung) auf **LAN target** (LAN-Ziel) fest, um eine ausgeglichene Leistung und Deduplizierung zu erzielen. Klicken Sie auf **OK**.

    ![Veeam-Verwaltungskonsole, Seite mit erweiterten Einstellungen für neuen Sicherungsauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Informationen zur Deduplizierung und zu den Komprimierungseinstellungen von Veeam finden Sie unter [Data Compression and Deduplication](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html) (Komprimierung und Deduplizierung von Daten).

7.  Im Dialogfeld **Edit Backup Job** (Sicherungsauftrag bearbeiten) können Sie das Kontrollkästchen **Enable application-aware processing** (Anwendungsorientierte Verarbeitung aktivieren) aktivieren (optional).

    ![Veeam-Verwaltungskonsole, Seite für neuen Sicherungsauftrag, Gastverarbeitung](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Legen Sie für den Zeitplan die Ausführung einmal pro Tag fest, und wählen Sie eine Zeit, die Sie angeben können.

    ![Veeam-Verwaltungskonsole, Seite für neuen Sicherungsauftrag, Planung](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Einrichten von StorSimple als sekundäres Sicherungsziel

> [!NOTE]
> Die Wiederherstellung von Daten aus einer Sicherung in Cloudspeicher erfolgt mit der Geschwindigkeit der Cloud.

In diesem Modell muss ein Speichermedium (nicht StorSimple) vorhanden sein, das als temporärer Cache dient. Sie können ein RAID-Volume (Redundant Array of Independent Disks) nutzen, um die Anforderungen an Speicherplatz, Ein-/Ausgabe und Bandbreite zu erfüllen. Wir empfehlen RAID 5, 50 und 10.

Die folgende Abbildung zeigt typische Volumes für die kurzfristige lokale Aufbewahrung (auf dem Server) und die langfristige Aufbewahrung im Archiv. In diesem Szenario erfolgen alle Sicherungen auf dem lokalen RAID-Volume (auf dem Server). Diese Sicherungen werden regelmäßig dupliziert und auf einem Archivvolume archiviert. Es ist wichtig, die Größe des lokalen RAID-Volumes (auf dem Server) so einzurichten, dass die Kapazitäts- und Leistungsanforderungen für die kurzfristige Aufbewahrung erfüllt werden.

![StorSimple als sekundäres Sicherungsziel – logisches Diagramm](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple als sekundäres Sicherungsziel – GFS-Beispiel

In der folgenden Tabelle wird gezeigt, wie Sicherungen für die Ausführung auf den lokalen und StorSimple-Datenträgern eingerichtet werden. Die Tabelle enthält individuelle und gesamte Kapazitätsanforderungen.

| Sicherungstyp und Aufbewahrung | Konfigurierter Speicher | Größe (TiB) | GFS-Multiplikator | Gesamtkapazität \* (TiB) |
|---|---|---|---|---|
| Woche 1 (vollständig und inkrementell) |Lokaler Datenträger (kurzfristig)| 1 | 1 | 1 |
| StorSimple, Woche 2-4 |StorSimple-Datenträger (langfristig) | 1 | 4 | 4 |
| Monatlich vollständig |StorSimple-Datenträger (langfristig) | 1 | 12 | 12 |
| Jährlich vollständig |StorSimple-Datenträger (langfristig) | 1 | 1 | 1 |
|Größenanforderungen für GFS-Volumes |  |  |  | 18*|

\* Die Gesamtkapazität umfasst 17 TiB für StorSimple-Datenträger und 1 TiB für lokale RAID-Volumes.


### <a name="gfs-example-schedule"></a>GFS-Beispielzeitplan

GFS-Rotation mit wöchentlichem, monatlichem und jährlichem Zeitplan

| Week | Vollständig | Inkrementell, Tag 1 | Inkrementell, Tag 2 | Inkrementell, Tag 3 | Inkrementell, Tag 4 | Inkrementell, Tag 5 |
|---|---|---|---|---|---|---|
| Woche 1 | Lokales RAID-Volume  | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume | Lokales RAID-Volume |
| Woche 2 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Woche 3 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Woche 4 | StorSimple, Woche 2-4 |   |   |   |   |   |
| Monatlich | StorSimple monatlich |   |   |   |   |   |
| Jährlich | StorSimple jährlich  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Zuweisen von StorSimple-Volumes zu einem Veeam-Kopierauftrag

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>So weisen Sie StorSimple-Volumes einem Veeam-Kopierauftrag zu

1.  Wählen Sie in der Veeam-Konsole für Sicherung und Replikation die Option **Backup & Replication** (Sicherung und Replikation). Klicken Sie mit der rechten Maustaste auf **Backup** (Sicherung), und wählen Sie dann je nach Umgebung die Option **VMware** oder **Hyper-V**.

    ![Screenshot der Veeam-Verwaltungskonsole mit den Optionen „VMware“ und „Hyper-V“, die Sie auswählen können](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  Geben Sie im Dialogfeld **New Backup Copy Job** (Neuer Sicherungskopierauftrag) einen Namen und eine Beschreibung für den Auftrag ein.

    ![Screenshot der Veeam-Verwaltungskonsole, über die Sie den Namen und die Beschreibung für den Auftrag eingeben können](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Wählen Sie die VMs aus, die Sie verarbeiten möchten. Treffen Sie eine Auswahl unter den Sicherungen, und wählen Sie dann die zuvor erstellte tägliche Sicherung.

    ![Veeam-Verwaltungskonsole, Seite für neuen Sicherungskopierauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Schließen Sie bei Bedarf Objekte aus dem Sicherungskopierauftrag aus.

5.  Wählen Sie Ihr Sicherungsrepository aus, und legen Sie einen Wert für **Restore points to keep** (Beizubehaltende Wiederherstellungspunkte) fest. Achten Sie darauf, dass das Kontrollkästchen **Keep the following restore points for archival purposes** (Folgende Wiederherstellungspunkte zu Archivierungszwecken beibehalten) aktiviert ist. Definieren Sie die Sicherungshäufigkeit, und wählen Sie **Advanced** (Erweitert).

    ![Screenshot, auf dem zu sehen ist, an welcher Stelle Sie das Intervall für Sicherungen festlegen können](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Geben Sie die folgenden erweiterten Einstellungen an:

    * Deaktivieren Sie auf der Registerkarte **Maintenance** (Wartung) den Beschädigungsschutz auf Speicherebene.

    ![Screenshot der Registerkarte „Wartung“ in der Veeam-Verwaltungskonsole](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Stellen Sie auf der Registerkarte **Storage** (Speicher) sicher, dass die Deduplizierung und Komprimierung deaktiviert sind.

    ![Veeam-Verwaltungskonsole, Seite mit erweiterten Einstellungen für neuen Sicherungskopierauftrag](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Geben Sie an, dass die Datenübertragung direkt erfolgt.

8.  Definieren Sie den Zeitplan für Sicherungskopiefenster gemäß Ihren Anforderungen, und schließen Sie den Assistenten ab.

Weitere Informationen finden Sie unter [Create backup copy jobs](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html) (Erstellen von Sicherungskopieraufträgen).

## <a name="storsimple-cloud-snapshots"></a>StorSimple-Cloudmomentaufnahmen

StorSimple-Cloudmomentaufnahmen schützen die Daten, die sich auf Ihrem StorSimple-Gerät befinden. Das Erstellen einer Cloudmomentaufnahme ist gleichbedeutend mit dem Überführen lokaler Sicherungsbänder an einen externen Standort. Bei Verwendung von georedundantem Azure-Speicher entspricht das Erstellen einer Cloudmomentaufnahme dem Versenden mehrerer Bänder an mehrere Standorte. Wenn nach einem Notfall eine Gerätewiederherstellung erforderlich ist, können Sie ein anderes StorSimple-Gerät online schalten und ein Failover ausführen. Nach dem Failover können Sie aus der jüngsten Cloudmomentaufnahme (mit der Geschwindigkeit der Cloud) auf die Daten zugreifen.

Der folgende Abschnitt zeigt, wie Sie ein kurzes Skript schreiben, um StorSimple-Cloudmomentaufnahmen während der Nachverarbeitung von Sicherungen zu starten und zu löschen.

> [!NOTE]
> Manuell oder programmgesteuert erstellte Momentaufnahmen folgen nicht der Ablaufrichtlinie für StorSimple-Momentaufnahmen. Diese Momentaufnahmen müssen manuell oder programmgesteuert gelöscht werden.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Starten und Löschen von Cloudmomentaufnahmen mit einem Skript

> [!NOTE]
> Bewerten Sie sorgfältig die Auswirkungen auf Compliance und Datenaufbewahrung, bevor Sie eine StorSimple-Momentaufnahme löschen. Weitere Informationen zum Ausführen eines Skripts nach der Sicherung finden Sie in der Veeam-Dokumentation.


### <a name="backup-lifecycle"></a>Sicherungslebenszyklus

![Diagramm zum Sicherungslebenszyklus](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Um das Skript zum Sicherungsauftrag hinzuzufügen, bearbeiten Sie die erweiterten Auftragsoptionen in Veeam.

    ![Erweiterte Einstellungen in Veeam, Registerkarte mit Skripteinstellungen](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Es empfiehlt sich, die Sicherungsrichtlinie für StorSimple-Cloudmomentaufnahmen als Nachverarbeitungsskript am Ende des täglichen Sicherungsauftrags auszuführen. Weitere Informationen zum Sichern und Wiederherstellen Ihrer Sicherungsanwendungsumgebung, um Ihre RPO- und RTO-Ziele zu erfüllen, erhalten Sie von Ihrem Sicherungsarchitekten.

## <a name="storsimple-as-a-restore-source"></a>StorSimple als Wiederherstellungsquelle

Wiederherstellungen aus einem StorSimple-Gerät funktionieren ähnlich wie Wiederherstellungen aus anderen Blockspeichergeräten. Bei Daten, die per Tiering in der Cloud gespeichert wurden, erfolgt die Wiederherstellung mit der Geschwindigkeit der Cloud. Bei lokalen Daten erfolgt die Wiederherstellung mit der Geschwindigkeit des lokalen Datenträgers des Geräts.

Mit Veeam erhalten Sie eine schnelle, genau definierte Wiederherstellung auf Dateiebene per StorSimple. Dabei werden die integrierten Explorer-Ansichten in der Veeam-Konsole verwendet. Verwenden Sie die Veeam-Explorer, um einzelne Elemente, z.B. E-Mail-Nachrichten, Active Directory-Objekte und SharePoint-Elemente, aus Sicherungen wiederherzustellen. Die Wiederherstellung kann ohne Unterbrechung der lokalen virtuellen Computer erfolgen. Sie können auch eine Zeitpunktwiederherstellung für Azure SQL-Datenbank und Oracle-Datenbanken durchführen. Mit Veeam und StorSimple ist die Wiederherstellung auf Elementebene aus Azure schnell und einfach. Informationen zum Ausführen einer Wiederherstellung finden Sie in der Veeam-Dokumentation:

- Für [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Für [Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Für [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Für [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Für [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple-Failover und -Notfallwiederherstellung

> [!NOTE]
> In Sicherungszielszenarien wird die StorSimple Cloud Appliance nicht als Wiederherstellungsziel unterstützt.

Ein Notfall kann durch eine Vielzahl von Faktoren verursacht werden. In der folgenden Tabelle sind häufige Notfallwiederherstellungsszenarien aufgeführt.

| Szenario | Auswirkung | Wiederherstellung | Notizen |
|---|---|---|---|
| Ausfall eines StorSimple-Geräts | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Ersetzen Sie das ausgefallene Gerät, und führen Sie die [Schritte für StorSimple-Failover und -Notfallwiederherstellung](./storsimple-8000-device-failover-disaster-recovery.md) durch. | Wenn nach der Wiederherstellung des Geräts eine Datenwiederherstellung erforderlich ist, werden die vollständigen Arbeitssätze mit Daten aus der Cloud auf das neue Gerät abgerufen. Alle Vorgänge erfolgen mit der Geschwindigkeit der Cloud. Dieses erneute Scannen von Index und Katalog kann dazu führen, dass alle Sicherungssätze gescannt und aus der Cloudspeicherstufe in die lokale Speicherstufe des Geräts übertragen werden. Dies kann ein sehr zeitaufwendiger Prozess sein. |
| Ausfall des Veeam-Servers | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Erstellen Sie den Sicherungsserver neu, und führen Sie eine Datenbankwiederherstellung durch, wie im [Veeam Help Center (Technical Documentation)](https://www.veeam.com/documentation-guides-datasheets.html) (Veeam Help Center (Technische Dokumentation)) beschrieben.  | Sie können den Veeam-Server am Notfallwiederherstellungsstandort neu erstellen oder wiederherstellen. Stellen Sie die Datenbank auf den jüngsten Zeitpunkt wieder her. Wenn die wiederhergestellte Veeam-Datenbank nicht mit Ihren jüngsten Sicherungsaufträgen synchron ist, ist eine Indizierung und Katalogisierung erforderlich. Das erneute Scannen von Index und Katalog kann dazu führen, dass alle Sicherungssätze gescannt und aus der Cloudspeicherstufe in die lokale Speicherstufe des Geräts übertragen werden. Damit wird diese Aufgabe noch zeitaufwendiger. |
| Standortausfall, der zum Verlust des Sicherungsservers und von StorSimple führt | Sicherungs- und Wiederherstellungsvorgänge werden unterbrochen. | Stellen Sie zuerst StorSimple und dann Veeam wieder her. | Stellen Sie zuerst StorSimple und dann Veeam wieder her. Wenn nach der Wiederherstellung des Geräts eine Datenwiederherstellung erforderlich ist, werden die vollständigen Arbeitssätze mit Daten aus der Cloud auf das neue Gerät abgerufen. Alle Vorgänge erfolgen mit der Geschwindigkeit der Cloud. |


## <a name="references"></a>References

Folgende Dokumente haben als Referenz für diesen Artikel gedient:

- [StorSimple multipath I/O setup](./storsimple-8000-configure-mpio-windows-server.md) (StorSimple: Einrichten von Multipfad-E/A)
- [Storage scenarios: Thin provisioning](/windows-hardware/drivers/storage/thin-provisioning) (Speicherszenarien: schlanke Speicherzuweisung)
- [Using GPT drives](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD) (Verwenden von GPT-Laufwerken)
- [Einrichten von Schattenkopien für freigegebene Ordner](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die [Wiederherstellung aus einem Sicherungssatz](./storsimple-8000-restore-from-backup-set-u2.md) durchführen.
- Erfahren Sie, wie Sie [ein Gerätefailover und eine Notfallwiederherstellung](./storsimple-8000-device-failover-disaster-recovery.md) ausführen.