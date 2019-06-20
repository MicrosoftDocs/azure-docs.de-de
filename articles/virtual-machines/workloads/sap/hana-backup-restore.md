---
title: HANA-Sicherung und -Wiederherstellung in SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine HANA-Sicherung und -Wiederherstellung in SAP HANA in Azure (große Instanzen) durchführen.
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 21232e5a678d6deed920e57cd0433a3b85ca4fdc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64987914"
---
# <a name="backup-and-restore"></a>Sichern und Wiederherstellen

>[!IMPORTANT]
>Dieser Artikel ist kein Ersatz für die Verwaltungsdokumentation zu SAP HANA oder für SAP Notes. Es wird davon ausgegangen, dass Sie über solide Kenntnisse in Bezug auf die SAP HANA-Verwaltung und den Betrieb verfügen, insbesondere in den Bereichen Sicherung, Wiederherstellung, Hochverfügbarkeit und Notfallwiederherstellung. In diesem Artikel werden Screenshots von SAP HANA Studio gezeigt. Inhalt, Struktur und grundlegender Aufbau der Bildschirme in den SAP-Verwaltungstools sowie auch die Tools selbst können sich von Release zu Release von SAP HANA ändern.

Es ist wichtig, dass Sie alle Schritte und Verfahren in Ihrer Umgebung und mit Ihren HANA-Versionen und -Releases ausführen. Einige der in diesem Artikel beschriebenen Vorgänge sind für ein besseres allgemeines Verständnis vereinfacht dargestellt. Sie sind nicht als detaillierte Schritte für Betriebshandbücher gedacht. Wenn Sie Betriebshandbücher für Ihre Konfigurationen erstellen möchten, sollten Sie Ihre Verfahren testen und umsetzen und in Bezug auf Ihre spezifischen Konfigurationen dokumentieren. 

Einer der wichtigsten Aspekte beim Betrieb von Datenbanken ist der Schutz vor Katastrophenfällen. Diese Ereignisse können sowohl durch Naturkatastrophen als auch einfache Benutzerfehler verursacht werden.

Durch das Sichern einer Datenbank mit der Möglichkeit einer Wiederherstellung zu einem beliebigen Zeitpunkt. z. B. bevor kritische Daten gelöscht wurden, kann ein Status wiederhergestellt werden, der möglichst genau dem Status vor der Störung entspricht.

Um die Möglichkeit zum Wiederherstellen zu erreichen, müssen zwei Arten von Sicherungen ausgeführt werden:

- Datenbanksicherungen: vollständig, inkrementell oder differenziell
- Transaktionsprotokollsicherungen

Zusätzlich zu vollständigen Datenbanksicherungen auf Anwendungsebene können Sie Sicherungen mit Speichermomentaufnahmen erstellen. Speichermomentaufnahmen sind jedoch kein Ersatz für Transaktionsprotokollsicherungen. Transaktionsprotokollsicherungen werden auch weiterhin für Point-in-Time-Datenbankwiederherstellungen sowie zum Leeren der Protokolle von Transaktionen, für die bereits ein Commit ausgeführt wurde, benötigt. Speichermomentaufnahmen können die Wiederherstellung durch schnelles Bereitstellen eines Rollforwardimages der Datenbank beschleunigen. 

Es gibt zwei Sicherungs- und Wiederherstellungsoptionen für SAP HANA in Azure (große Instanzen):

- **Eigenständig.** Nachdem sichergestellt wurde, dass ausreichend Speicherplatz vorhanden ist, führen Sie vollständige Datenbank- und Protokollsicherungen mit einer der folgenden Datenträger-Sicherungsmethoden durch. Sie können entweder direkt auf Volumes sichern, die an die HANA-Einheiten (große Instanz) angefügt sind, oder auf NFS-Freigaben, die auf einem virtuellen Azure-Computer (VM) eingerichtet sind. In letzterem Fall richten Kunden eine Linux-VM in Azure ein, fügen Azure Storage an die VM an und geben den Speicher über einen konfigurierten NFS-Server auf dieser VM frei. Wenn Sie die Sicherung für Volumes ausführen, die direkt an HANA-Einheiten (große Instanz) angefügt sind, kopieren Sie die Sicherungen in ein Azure Storage-Konto. Dies können Sie nach dem Einrichten einer Azure-VM erledigen, die auf Azure Storage basierende NFS-Freigaben exportiert. Außerdem können Sie einen Azure Backup-Tresor oder Azure Cold Storage verwenden. 

   Eine weitere Option besteht darin, die Sicherungen nach dem Kopieren in einem Azure Storage-Konto mithilfe eines Drittanbieter-Datenschutztools zu speichern. Die eigenständige Sicherungsoption kann auch bei Daten erforderlich sein, die zu Konformitäts- oder Überprüfungszwecken über einen längeren Zeitraum gespeichert werden müssen. In allen Fällen werden die Sicherungen in NFS-Freigaben kopiert, die durch eine VM und Azure Storage dargestellt werden.

- **Funktionen für Sicherungen und Wiederherstellungen der Infrastruktur.** Sie können auch die Sicherungs- und Wiederherstellungsfunktionen verwenden, mit denen die zugrunde liegende Infrastruktur von SAP HANA in Azure (große Instanzen) bereitgestellt wird. Diese Option sorgt für die nötigen Sicherungen und ermöglicht schnelle Wiederherstellungen. Im restlichen Teil dieses Abschnitts werden die Sicherungs- und Wiederherstellungsfunktionen behandelt, die von HANA (große Instanzen) bereitgestellt werden. In diesem Abschnitt wird auch die Beziehung der Sicherung und Wiederherstellung mit der Notfallwiederherstellungsfunktionalität von HANA (große Instanzen) behandelt.

> [!NOTE]
>   Die Momentaufnahmetechnologie der zugrunde liegenden Infrastruktur von HANA (große Instanzen) ist von SAP HANA-Momentaufnahmen abhängig. Zum aktuellen Zeitpunkt können SAP HANA-Momentaufnahmen nicht in Verbindung mit mehreren Mandanten von mehrinstanzenfähigen SAP HANA-Datenbankcontainern verwendet werden. Wenn nur ein Mandant bereitgestellt wird, funktionieren SAP HANA-Momentaufnahmen, und Sie können diese Methode verwenden.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Verwenden von Speichermomentaufnahmen von SAP HANA in Azure (große Instanzen)

Die zugrunde liegende Speicherinfrastruktur von SAP HANA in Azure (große Instanzen) unterstützt Speichermomentaufnahmen von Volumes. Sicherung und Wiederherstellung von Volumes werden unterstützt, dabei ist jedoch Folgendes zu beachten:

- Anstelle von vollständigen Datenbanksicherungen werden in regelmäßigen Abständen Speichervolume-Momentaufnahmen erstellt.
- Beim Auslösen einer Momentaufnahme für die Volumes „/hana/data“ und „/hana/shared“ (einschließlich „/usr/sap“) initiiert die Momentaufnahmetechnologie vor dem Ausführen der Speichermomentaufnahme eine SAP HANA-Momentaufnahme. Diese SAP HANA-Momentaufnahme ist der Einrichtungspunkt für nachfolgende Wiederherstellungen von Protokollen, nachdem die Speichermomentaufnahme wiederhergestellt wurde. Für erfolgreiche HANA-Momentaufnahmen benötigen Sie eine aktive HANA-Instanz. In HSR-Szenarien werden Speichermomentaufnahmen auf aktuellen sekundären Knoten, auf denen keine HANA Momentaufnahmen ausgeführt werden können, nicht unterstützt.
- Nach dem erfolgreichen Ausführen der Speichermomentaufnahme wird die SAP HANA-Momentaufnahme gelöscht.
- Transaktionsprotokollsicherungen werden in regelmäßigen Abständen erstellt und auf dem Volume „/hana/logbackups“ oder in Azure gespeichert. Sie können für das Volume „/hana/logbackups“ mit den Transaktionsprotokollsicherungen eine separate Momentaufnahmenerstellung auslösen. In diesem Fall müssen Sie keine HANA-Momentaufnahme ausführen.
- Wenn Sie bei einem Produktionsausfall eine Datenbank zu einem bestimmten Zeitpunkt wiederherstellen müssen, fordern Sie beim Microsoft Azure-Support oder bei SAP HANA in Azure die Wiederherstellung einer bestimmten Speichermomentaufnahme an. Ein Beispiel ist eine geplante Wiederherstellung eines Sandkastensystems in seinen ursprünglichen Zustand.
- Die in der Speichermomentaufnahme enthaltene SAP HANA-Momentaufnahme ist ein Ausgangspunkt für die Anwendung von Transaktionsprotokollsicherungen, die nach dem Erstellen der Speichermomentaufnahme ausgeführt und gespeichert wurden.
- Diese Transaktionsprotokollsicherungen werden erstellt, um eine Point-in-Time-Wiederherstellung der Datenbank zu ermöglichen.

Sie können Speichermomentaufnahmen für drei Volumeklassen durchführen:

- Eine kombinierte Momentaufnahme für „/hana/data“ und „/hana/shared“ einschließlich „/usr/sap“. Diese Momentaufnahme erfordert die Erstellung einer SAP HANA-Momentaufnahme als Vorbereitung für die Speichermomentaufnahme. Die SAP HANA-Momentaufnahme stellt sicher, dass die Datenbank aus Speichersicht in einem konsistenten Zustand verbleibt. Dies stellt einen Punkt dar, an dem die Wiederherstellung beginnen kann.
- Eine separate Momentaufnahme für „/hana/logbackups“
- Eine Betriebssystempartition.

Die neuesten Momentaufnahmeskripts und -dokumente finden Sie bei [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0). Wenn Sie das Skriptpaket mit den Momentaufnahmen von [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) herunterladen, erhalten Sie drei Dateien. Eine der Dateien ist in einer PDF-Datei für die bereitgestellten Funktionen dokumentiert. Nachdem Sie die Tools heruntergeladen haben, befolgen Sie die Anweisungen unter „Get the snapshot tools“ (Abrufen der Momentaufnahmetools).

## <a name="storage-snapshot-considerations"></a>Aspekte von Speichermomentaufnahmen

>[!NOTE]
>Speichermomentaufnahmen beanspruchen Speicherplatz, der den Einheiten von HANA (große Instanzen) zugeteilt ist. Berücksichtigen Sie bei der Planung von Speichermomentaufnahmen und der Festlegung der Anzahl von Speichermomentaufnahmen die folgenden Aspekte. 

Zur besonderen Funktionsweise von Speichermomentaufnahmen von SAP HANA in Azure (große Instanzen) gehört Folgendes:

- Eine individuelle Speichermomentaufnahme beansprucht zum Zeitpunkt der Erstellung nur wenig Speicherplatz.
- Wenn sich Dateninhalte und der Inhalt in SAP HANA-Datendateien auf dem Speichervolume ändern, müssen der ursprüngliche Blockinhalt und die Datenänderungen in der Momentaufnahme gespeichert werden.
- Die Speichermomentaufnahme vergrößert sich infolgedessen. Je länger die Momentaufnahme vorhanden ist, desto größer wird die Speichermomentaufnahme.
- Je mehr Änderungen am SAP HANA-Datenbankvolume während der Lebensdauer einer Speichermomentaufnahme erfolgen, desto mehr Speicherplatz belegt die Speichermomentaufnahme.

SAP HANA in Azure (große Instanzen) bietet standardmäßig feste Volumegrößen für die SAP HANA-Daten- und Protokollvolumes. Die Erstellung von Momentaufnahmen dieser Volumes verringert Ihren Volumespeicherplatz. Sie müssen folgende Schritte durchführen:

- Legen Sie fest, wann geplante Speichermomentaufnahmen erstellt werden sollen.
- Überwachen Sie den Speicherplatzverbrauch der Speichervolumes. 
- Verwalten Sie die Anzahl der gespeicherten Momentaufnahmen. 

Sie können Speichermomentaufnahmen deaktivieren, wenn Sie Unmengen von Daten importieren oder andere wichtige Änderungen an der HANA-Datenbank vornehmen. 


Die folgenden Abschnitte enthalten Informationen zum Erstellen dieser Momentaufnahmen sowie allgemeine Empfehlungen:

- Obwohl die Hardware 255 Momentaufnahmen pro Volume unterstützen kann, ist es ratsam, unter diesem Wert zu bleiben. Die Empfehlung liegt bei höchstens 250.
- Überwachen und verfolgen Sie den freien Speicherplatz, bevor Sie Speichermomentaufnahmen erstellen.
- Verringern Sie die Anzahl von Speichermomentaufnahmen basierend auf dem freien Speicherplatz. Sie können die Anzahl der gespeicherten Momentaufnahmen verringern oder die Volumes erweitern. Sie können zusätzlichen Speicher in 1-TB-Schritten anfordern.
- Deaktivieren Sie Speichermomentaufnahmen für das Volume „/hana/data“ bei Aktivitäten wie dem Verschieben von Daten in SAP HANA mit SAP-Plattformmigrationstools (R3load) oder dem Wiederherstellen von SAP HANA-Datenbanken aus Sicherungen. 
- Vermeiden Sie bei größeren Neuorganisationen von SAP HANA-Tabellen nach Möglichkeit Speichermomentaufnahmen.
- Speichermomentaufnahmen sind eine Voraussetzung, um von den Notfallwiederherstellungsfunktionen von SAP HANA in Azure (große Instanzen) profitieren zu können.

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Voraussetzungen für die Nutzung von Self-Service-Speichermomentaufnahmen

Um zu gewährleisten, dass das Momentaufnahmeskript ausgeführt werden kann, stellen Sie sicher, dass im Linux-Betriebssystem auf dem Server mit HANA (große Instanzen) Perl installiert ist. Perl ist auf Ihrer HANA-Einheit (große Instanzen) vorinstalliert. Verwenden Sie folgenden Befehl, um die Perl-Version zu überprüfen:

`perl -v`

![Der öffentliche Schlüssel wird durch Ausführen dieses Befehls kopiert.](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Einrichten von Speichermomentaufnahmen

Führen Sie diese Schritte aus, um Speichermomentaufnahmen mit HANA (große Instanzen) einzurichten.
1. Stellen Sie sicher, dass Perl im Linux-Betriebssystem auf dem Server mit HANA (große Instanzen) installiert ist.
1. Fügen Sie „/etc/ssh/ssh\_config“ die Zeile _MACs hmac-sha1_ hinzu.
1. Erstellen Sie ggf. ein SAP HANA-Sicherungsbenutzerkonto auf dem Masterknoten für jede SAP HANA-Instanz, die Sie ausführen.
1. Installieren Sie den SAP HANA HDB-Client auf allen Servern mit SAP HANA (große Instanzen).
1. Erstellen Sie auf dem ersten Server mit SAP HANA (große Instanzen) in jeder Region einen öffentlichen Schlüssel, um auf die zugrunde liegende Speicherinfrastruktur zuzugreifen, die die Erstellung von Momentaufnahmen steuert.
1. Kopieren Sie die Skripts und die Konfigurationsdatei von [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) im Speicherort **hdbsql** der SAP HANA-Installation.
1. Ändern Sie bei Bedarf die Datei *HANABackupDetails.txt* gemäß den jeweiligen Kundenvorgaben.

Rufen Sie die neuesten Momentaufnahmeskripts und -dokumente bei [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) ab. Weitere Informationen zu den zuvor aufgeführten Schritten finden Sie unter [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

### <a name="consideration-for-mcod-scenarios"></a>Überlegung für MCOD-Szenarien
Wenn Sie ein Betriebssystem mit einem [MCOD-Szenario](https://launchpad.support.sap.com/#/notes/1681092) mit mehreren SAP HANA-Instanzen auf einer HANA-Einheit (große Instanzen) ausführen, verfügen Sie über separat bereitgestellte Speichervolumes für jede einzelne SAP HANA-Instanz. Weitere Informationen zu MDC und zusätzliche Überlegungen finden Sie unter „Important things to remember“ (Wichtige zu beachtende Punkte) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Schritt 1: Installieren des SAP HANA HDB-Clients

Das für SAP HANA in Azure (große Instanzen) installierte Linux-Betriebssystem enthält die Ordner und Skripts, die benötigt werden, um SAP HANA-Speichermomentaufnahmen für die Sicherung und Notfallwiederherstellung auszuführen. Überprüfen Sie, ob auf [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) neuere Versionen zur Verfügung stehen. Die neueste Skriptversion ist 4.0. Andere Skripts weisen möglicherweise abweichende Nebenversionen innerhalb derselben Hauptversion auf.

Sie müssen bei der Installation von SAP HANA den SAP HANA HDB-Client für die Einheiten von HANA (große Instanzen) selbst installieren.

### <a name="step-2-change-the-etcsshsshconfig"></a>Schritt 2: Ändern von „/etc/ssh/ssh\_config“

Dieser Schritt wird unter „Enable communication with storage“ (Aktivieren der Kommunikation mit dem Speicher) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure) beschrieben.


### <a name="step-3-create-a-public-key"></a>Schritt 3: Erstellen eines öffentlichen Schlüssels

Um den Zugriff auf die Schnittstellen für Speichermomentaufnahmen Ihres HANA-Mandanten (große Instanzen) zu ermöglichen, richten Sie ein Anmeldeverfahren ein, das auf einem öffentlichen Schlüssel basiert. 

Erstellen Sie auf dem ersten Server mit SAP HANA in Azure (große Instanzen) in Ihrem Mandanten einen öffentlichen Schlüssel zum Zugreifen auf die Speicherinfrastruktur. Mit einem öffentlichen Schlüssel ist für die Anmeldung bei den Schnittstellen für Speichermomentaufnahmen kein Kennwort erforderlich. Außerdem müssen Sie mit einem öffentlichen Schlüssel keine Kennwortanmeldeinformationen verwalten. 

Informationen zum Erstellen eines öffentlichen Schlüssels finden Sie unter „Enable communication with storage“ (Aktivieren der Kommunikation mit dem Speicher) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).


### <a name="step-4-create-an-sap-hana-user-account"></a>Schritt 4: Erstellen eines SAP HANA-Benutzerkontos

Um mit der Erstellung von SAP HANA-Momentaufnahmen zu beginnen, erstellen Sie in SAP HANA ein Benutzerkonto, das die Skripts für Speichermomentaufnahmen verwenden kann. Erstellen Sie hierzu in SAP HANA Studio ein SAP HANA-Benutzerkonto. Der Benutzer muss unter der SYSTEMDB erstellt werden, *nicht* unter der SID-Datenbank für MDC. In einer Umgebung mit einem einzelnen Container wird der Benutzer in der Mandantendatenbank erstellt. Dieses Konto benötigt die Berechtigungen **Backup Admin** und **Catalog Read**. 

Informationen zum Einrichten und Verwenden eines Benutzerkontos finden Sie unter „Enable communication with SAP HANA“ (Aktivieren der Kommunikation mit SAP HANA) bei [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0).


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Schritt 5: Autorisieren des SAP HANA-Benutzerkontos

In diesem Schritt autorisieren Sie das erstellte SAP HANA-Benutzerkonto, damit die Skripts zur Laufzeit keine Kennwörter übermitteln müssen. Der SAP HANA-Befehl `hdbuserstore` ermöglicht die Erstellung eines SAP HANA-Benutzerschlüssels. Der Schlüssel wird auf SAP HANA-Knoten gespeichert. Der Benutzerschlüssel ermöglicht dem Benutzer den Zugriff auf SAP HANA ohne Kennwortverwaltung im Rahmen des Skriptprozesses. Der Skriptprozess wird weiter unten in diesem Artikel erläutert.

>[!IMPORTANT]
>Führen Sie diese Konfigurationsbefehle im selben Benutzerkontext aus, in dem die Momentaufnahmebefehle ausgeführt werden. Andernfalls funktionieren die Momentaufnahmebefehle nicht ordnungsgemäß.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Schritt 6: Abrufen der Momentaufnahmeskripts, Konfigurieren der Momentaufnahmen und Testen der Konfiguration und Konnektivität

Laden Sie von [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.0) die neueste Version der Skripts herunter. Die Art der Installation von Skripts wurde mit Release 4.0 der Skripts geändert. Weitere Informationen finden Sie unter „Enable communication with SAP HANA“ (Aktivieren der Kommunikation mit SAP HANA) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Die genaue Befehlsfolge finden Sie unter „Easy installation of snapshot tools (default)“ (Einfache Installation von Momentaufnahmetools [Standard]) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). Es wird die Verwendung der Standardinstallation empfohlen. 

Informationen zum Upgrade von Version 3.x auf 4.0 finden Sie unter „Upgrade an existing install“ (Aktualisieren einer vorhandenen Installation) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). Informationen zum Deinstallieren der Tools in Version 4.0 finden Sie unter „Uninstallation of the snapshot tools“ (Deinstallation der Momentaufnahmetools) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Denken Sie daran, die unter „Complete setup of snapshot tools“ (Vollständiges Setup der Momentaufnahmetools) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure) beschriebenen Schritte auszuführen.

Informationen zum Nutzen der einzelnen installierten Skripts und Dateien finden Sie unter „What are these snapshot tools?“ (Was sind diese Momentaufnahmetools?). in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Bevor Sie die Momentaufnahmetools konfigurieren, stellen Sie sicher, dass Sie auch die Speicherorte und Einstellungen für HANA-Sicherungen ordnungsgemäß konfiguriert haben. Weitere Informationen finden Sie unter „SAP HANA Configuration“ (SAP HANA-Konfiguration) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Die Konfiguration der Momentaufnahmetools wird unter „Config file – HANABackupCustomerDetails.txt“ (Konfigurationsdatei „HANABackupCustomerDetails.txt“) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure) beschrieben.

#### <a name="test-connectivity-with-sap-hana"></a>Testen der Konnektivität mit SAP HANA

Nachdem Sie alle Konfigurationsdaten in die Datei *HANABackupCustomerDetails.txt* eingegeben haben, sollten Sie überprüfen, ob die Konfigurationen für die HANA-Instanzdaten korrekt sind. Verwenden Sie das Skript `testHANAConnection`. Hierbei spielt es keine Rolle, ob es sich um eine SAP HANA-Konfiguration für zentrales oder horizontales Hochskalieren handelt.

Weitere Informationen finden Sie unter „Check connectivity with SAP HANA – testHANAConnection“ (Überprüfen der Konnektivität mit SAP HANA – testHANAConnection) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

#### <a name="test-storage-connectivity"></a>Testen der Speicherkonnektivität

Der nächste Schritt besteht darin, anhand der Daten, die Sie in der Konfigurationsdatei *HANABackupCustomerDetails.txt* angegeben haben, die Verbindung mit dem Speicher zu überprüfen. Führen Sie dann eine Testmomentaufnahme aus. Sie müssen diesen Test vor dem Ausführen des Befehls `azure_hana_backup` ausführen. Informationen zur Befehlsfolge für diesen Test finden Sie unter „Check connectivity with storage – testStorageSnapshotConnection“ (Überprüfen der Konnektivität mit dem Speicher – testStorageSnapshotConnection) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Nach einer erfolgreichen Anmeldung bei den Schnittstellen der virtuellen Speichercomputer fährt das Skript mit Phase 2 fort und erstellt eine Testmomentaufnahme. Die hier angegebene Ausgabe zeigt eine SAP HANA-Konfiguration mit drei Knoten für horizontales Hochskalieren.

Wenn die Testmomentaufnahme mit dem Skript erfolgreich ausgeführt werden kann, können Sie die eigentlichen Speichermomentaufnahmen planen. Ist der Test nicht erfolgreich, sollten Sie die Probleme untersuchen, bevor Sie fortfahren. Bewahren Sie die Testmomentaufnahme, bis die ersten echten Momentaufnahmen fertig gestellt sind.


### <a name="step-7-perform-snapshots"></a>Schritt 7: Erstellen von Momentaufnahmen

Nach Abschluss der Vorbereitungsschritte können Sie damit beginnen, die eigentlichen Speichermomentaufnahmen zu konfigurieren und zu planen. Das zu planende Skript kann für SAP HANA-Konfigurationen mit zentralem und horizontalem Hochskalieren verwendet werden. Um das Sicherungsskript regelmäßig normal auszuführen, planen Sie es mit dem Cron-Hilfsprogramm. 

Informationen zur genauen Befehlssyntax und den Funktionen finden Sie unter „Perform snapshot backup – azure_hana_backup“ (Überprüfen der Durchführen einer Momentaufnahmesicherung – azure_hana_backup) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 

Bei Ausführung des Skripts `azure_hana_backup` wird die Speichermomentaufnahme in den folgenden drei Phasen erstellt:

1. Ausführen einer SAP HANA-Momentaufnahme
1. Ausführen einer Speichermomentaufnahme
1. Entfernen der SAP HANA-Momentaufnahme, die vor dem Ausführen der Speichermomentaufnahme erstellt wurde

Um das Skript auszuführen, rufen Sie es in dem ausführbaren HDB-Ordner auf, in den es kopiert wurde. 

Die Aufbewahrungsdauer richtet sich nach der Anzahl von Momentaufnahmen, die bei der Skriptausführung als Parameter übergeben wird. Der von Speichermomentaufnahmen abgedeckte Zeitraum ist eine Funktion aus dem Ausführungszeitraum und der Anzahl von Momentaufnahmen, die bei der Skriptausführung als Parameter angegeben wird. 

Wenn die Anzahl gespeicherter Momentaufnahmen die beim Skriptaufruf als Parameter angegebene übersteigt, wird vor dem Ausführen einer neuen Momentaufnahme die älteste Speichermomentaufnahme mit der gleichen Bezeichnung gelöscht. Die Anzahl, die Sie als letzten Parameter des Aufrufs angeben, ist die Anzahl der beibehaltenen Momentaufnahmen, die Sie steuern können. Mit dieser Anzahl können Sie indirekt auch den für Momentaufnahmen beanspruchten Speicherplatz steuern. 


## <a name="snapshot-strategies"></a>Strategien für Momentaufnahmen
Die Momentaufnahmenhäufigkeit für die unterschiedlichen Typen hängt davon ab, ob Sie die Notfallwiederherstellung von HANA (große Instanzen) verwenden. Diese Funktionalität basiert auf Speichermomentaufnahmen, für die ggf. besondere Empfehlungen in Bezug auf die Häufigkeit und Ausführungszeiträume der Speichermomentaufnahmen erforderlich sind. 

Bei den folgenden Überlegungen und Empfehlungen wird davon ausgegangen, dass Sie die Notfallwiederherstellung von HANA (große Instanzen) *nicht* verwenden. Stattdessen verwenden Sie die Speichermomentaufnahmen, um über Sicherungen zu verfügen und eine Zeitpunktwiederherstellung für die letzten 30 Tage zu ermöglichen. Angesichts der Einschränkungen hinsichtlich der Anzahl von Momentaufnahmen und des Speicherplatzes sollten Sie folgende Anforderungen berücksichtigen:

- Zeitraum für Zeitpunktwiederherstellung
- Belegter Speicherplatz
- Recovery Point Objective (RPO) und Recovery Time Objective (RTO) für eine potenzielle Wiederherstellung nach einem Notfall.
- Tatsächliche Ausführung vollständiger HANA-Datenbanksicherungen für Datenträger. Immer wenn eine vollständige Datenbanksicherung für Datenträger oder die Schnittstelle **backint** erfolgt, tritt bei der Ausführung von Speichermomentaufnahmen ein Fehler auf. Wenn Sie zusätzlich zu Speichermomentaufnahmen vollständige Datenbanksicherungen ausführen möchten, sollten Sie sicherstellen, dass die Ausführung von Speichermomentaufnahmen während dieses Zeitraums deaktiviert ist.
- Die Anzahl von Momentaufnahmen, die pro Volume auf 250 beschränkt ist.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Wenn Sie die Notfallwiederherstellung von HANA (große Instanzen) nicht verwenden, ist der Momentaufnahmezeitraum kürzer. Führen Sie in solchen Fällen die kombinierten Momentaufnahmen für „/hana/data“ und „/hana/shared“ (einschließlich „/usr/sap“) in einem Intervall von 12 oder 24 Stunden durch. Bewahren Sie die Momentaufnahmen einen Monat lang auf. Gleiches gilt für die Momentaufnahmen des Protokollsicherungsvolumes. SAP HANA-Transaktionsprotokollsicherungen werden für das Protokollsicherungsvolume in einem Intervall von 5 bis 15 Minuten ausgeführt.

Es ist am besten, geplante Speichermomentaufnahmen mit Cron durchzuführen. Verwenden Sie dasselbe Skript für alle Sicherungs- und Notfallwiederherstellungsanforderungen. Ändern Sie die Skripteingaben so, dass sie den verschiedenen angeforderten Sicherungszeiten entsprechen. Diese Momentaufnahmen werden in Cron abhängig von ihrer Ausführungszeit jeweils unterschiedlich geplant. Möglich sind: stündlich, alle 12 Stunden, täglich oder wöchentlich. 

Das folgende Beispiel zeigt einen Cron-Zeitplan in „/etc/crontab“:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
Das vorherige Beispiel deckt eine stündliche kombinierte Momentaufnahme der Volumes mit den Speicherorten „/hana/data“ und „/hana/shared/SID“ (einschließlich „/usr/sap“) ab. Verwenden Sie diese Art von Momentaufnahme, um eine schnellere Zeitpunktwiederherstellung für die letzten zwei Tage zu erzielen. Außerdem wird eine tägliche Momentaufnahme für diese Volumes erstellt. Somit verfügen Sie über stündliche Momentaufnahmen für einen Zeitraum von zwei Tagen sowie über tägliche Momentaufnahmen für einen Zeitraum von vier Wochen. Das Volume für die Transaktionsprotokollsicherung wird ebenfalls täglich gesichert. Diese Sicherungen werden vier Wochen lang aufbewahrt. 

In der dritten Zeile von „crontab“ sehen Sie, dass für die Sicherung des HANA-Transaktionsprotokolls ein Ausführungsintervall von fünf Minuten geplant ist. Die Startzeiten der verschiedenen Cron-Aufträge, die Speichermomentaufnahmen ausführen, sind gestaffelt. Dadurch werden nicht alle Momentaufnahmen zu einem bestimmten Zeitpunkt auf einmal ausgeführt. 

Im folgenden Beispiel führen Sie stündlich eine kombinierte Momentaufnahme durch, die die Volumes mit den Speicherorten „/hana/data“ und „/hana/shared/SID“ (einschließlich „/usr/sap“) abdeckt. Diese Momentaufnahmen werden zwei Tage lang aufbewahrt. Die Momentaufnahmen der Volumes mit der Transaktionsprotokollsicherung werden alle fünf Minuten ausgeführt und vier Stunden lang aufbewahrt. Das Ausführungsintervall für die Sicherung der HANA-Transaktionsprotokolldatei ist wie zuvor auf fünf Minuten festgelegt. 

Die Momentaufnahme des Volumes mit der Transaktionsprotokollsicherung wird jeweils zwei Minuten nach dem Start der Transaktionsprotokollsicherung erstellt. Normalerweise sollte die Sicherung des SAP HANA-Transaktionsprotokolls innerhalb dieser zwei Minuten abgeschlossen sein. Das Volume mit der Start-LUN wird wie zuvor jeweils einmal pro Tag durch eine Speichermomentaufnahme gesichert und vier Wochen lang aufbewahrt.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

Die folgende Grafik veranschaulicht die Sequenzen des vorherigen Beispiels. Die Start-LUN ist ausgeschlossen.

![Beziehung zwischen Sicherungen und Momentaufnahmen](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA führt regelmäßig Schreibvorgänge für das Volume „/hana/log“ aus, um die Datenbankänderungen, für die ein Commit ausgeführt wurde, zu dokumentieren. In regelmäßigen Abständen schreibt SAP HANA einen Sicherungspunkt auf das Volume „/hana/data“. Wie in „crontab“ angegeben, wird alle fünf Minuten eine SAP HANA-Transaktionsprotokollsicherung ausgeführt. 

Aufgrund der Auslösung einer kombinierten Speichermomentaufnahme für die Volumes „/hana/data“ und „/hana/shared/SID“ wird zudem stündlich eine SAP HANA-Momentaufnahme ausgeführt. Nach erfolgreicher Ausführung der HANA-Momentaufnahme wird die kombinierte Speichermomentaufnahme ausgeführt. Wie in „crontab“ angegeben, wird alle fünf Minuten (etwa zwei Minuten nach der HANA-Transaktionsprotokollsicherung) die Speichermomentaufnahme des Volumes „/hana/logbackup“ ausgeführt.

> 

>[!IMPORTANT]
> Die Verwendung von Speichermomentaufnahmen für SAP HANA-Sicherungen ist nur sinnvoll, wenn diese in Verbindung mit SAP HANA-Transaktionsprotokollsicherungen erstellt werden. Diese Transaktionsprotokollsicherungen müssen die Zeiträume zwischen den Speichermomentaufnahmen abdecken. 

Folgendes ist erforderlich, wenn Sie für Benutzer eine Zeitpunktwiederherstellung für einen Zeitraum von 30 Tagen zugesagt haben:

- Greifen Sie in Extremfällen auf eine kombinierte Speichermomentaufnahme für „/hana/data“ und „/hana/shared/SID“ zu, die 30 Tage alt ist. 
- Sie benötigen lückenlose Transaktionsprotokollsicherungen, die die Zeit zwischen den kombinierten Speichermomentaufnahmen abdecken. Die älteste Momentaufnahme des Volumes mit der Transaktionsprotokollsicherung muss also 30 Tage alt sein. Dies gilt nicht, wenn Sie die Transaktionsprotokollsicherungen auf eine andere NFS-Freigabe in Azure Storage kopieren. In diesem Fall können Sie ältere Transaktionsprotokollsicherungen von dieser NFS-Freigabe abrufen.

Um die Vorteile der Speichermomentaufnahmen und der letztlichen Speicherreplikation von Transaktionsprotokollsicherungen nutzen zu können, ändern Sie den Speicherort, an den SAP HANA die Transaktionsprotokollsicherungen schreibt. Sie können diese Änderung in HANA Studio vornehmen. 

Auch wenn SAP HANA automatisch vollständige Protokollsegmente sichert, sollten Sie ein deterministisches Protokollsicherungsintervall angeben. Dies empfiehlt sich insbesondere bei Verwendung der Notfallwiederherstellungsoption, da Sie in der Regel Protokollsicherungen mit einem deterministischen Intervall ausführen. Im folgenden Fall sind 15 Minuten als Protokollsicherungsintervall festgelegt.

![Planen von SAP HANA-Sicherungsprotokollen in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

Sie können auch Sicherungen auswählen, die häufiger als alle 15 Minuten erstellt werden. Eine Einstellung für eine höhere Häufigkeit wird oft in Verbindung mit der Notfallwiederherstellung von HANA (große Instanzen) verwendet. Einige Kunden führen Transaktionsprotokollsicherungen alle fünf Minuten durch.

Falls die Datenbank noch nie gesichert wurde, wird im letzten Schritt eine dateibasierte Datenbanksicherung durchgeführt, um einen einzelnen Sicherungseintrag zu erstellen, der im Sicherungskatalog vorhanden sein muss. Andernfalls kann SAP HANA Ihre angegebenen Protokollsicherungen nicht initiieren.

![Erstellen einer dateibasierten Sicherung zum Hinzufügen eines einzelnen Sicherungseintrags](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Löschen Sie nach dem Erstellen der ersten Speichermomentaufnahmen die in Schritt 6 erstellte Testmomentaufnahme. Weitere Informationen finden Sie unter „Remove test snapshots – removeTestStorageSnapshot“ (Entfernen von Testsnapshots – removeTestStorageSnapshot) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Überwachen der Anzahl und Größe von Momentaufnahmen auf dem Datenträgervolume

Sie können die Anzahl von Momentaufnahmen und deren Speicherbedarf auf einem bestimmten Speichervolume überwachen. Mit dem Befehl `ls` werden keine Momentaufnahmenverzeichnisse oder -dateien angezeigt. Der Befehl `du` des Linux-Betriebssystems liefert Details zu den Speichermomentaufnahmen, da sie auf denselben Volumes gespeichert sind. Für den Befehl stehen folgende Optionen zur Verfügung:

- `du –sh .snapshot`: Mit dieser Option wird die Summe aller Momentaufnahmen im Verzeichnis für die Momentaufnahmen angegeben.
- `du –sh --max-depth=1`: Mit dieser Option werden alle Momentaufnahmen, die im Ordner **.snapshot** gespeichert sind, und die Größe der einzelnen Momentaufnahmen aufgelistet.
- `du –hc`: Mit dieser Option wird die Gesamtgröße des Speicherplatzes angegeben, der von allen Momentaufnahmen genutzt wird.

Verwenden Sie diese Befehle, um sicherzustellen, dass die erstellten und gespeicherten Momentaufnahmen nicht den gesamten Speicher auf den Volumes belegen.

>[!NOTE]
>Die Momentaufnahmen der Start-LUN werden bei Verwendung der vorherigen Befehle nicht angezeigt.

### <a name="get-details-of-snapshots"></a>Abrufen von Details zu Momentaufnahmen
Um weitere Informationen zu Momentaufnahmen zu erhalten, verwenden Sie das Skript `azure_hana_snapshot_details`. Sie können dieses Skript an beiden Standorten ausführen, sofern am Standort für die Notfallwiederherstellung ein aktiver Server vorhanden ist. Das Skript enthält die folgende Ausgabe, die nach den einzelnen Volumes mit Momentaufnahmen aufgeschlüsselt sind: 
   * Größe der gesamten Momentaufnahmen in einem Volume
   * Jede Momentaufnahme in diesem Volume enthält die folgenden Details: 
      - Name der Momentaufnahme 
      - Erstellungszeitpunkt 
      - Größe der Momentaufnahme
      - Häufigkeit der Momentaufnahme
      - HANA-Sicherungs-ID im Zusammenhang mit dieser Momentaufnahme, sofern relevant

Die Syntax des Befehls und die Ausgaben finden Sie unter „List snapshots – azure_hana_snapshot_details“ (Auflisten von Momentaufnahmen – azure_hana_snapshot_details) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Verringern der Anzahl von Momentaufnahmen auf einem Server

Wie bereits beschrieben, können Sie die Anzahl von bestimmten Bezeichnungen der gespeicherten Momentaufnahmen reduzieren. Die beiden letzten Parameter des Befehls zum Initiieren einer Momentaufnahme sind die Bezeichnung und die Anzahl von Momentaufnahmen, die Sie beibehalten möchten.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

Im vorherigen Beispiel lautet die Bezeichnung der Momentaufnahme **dailyhana**. Die Anzahl der beibehaltenen Momentaufnahmen mit dieser Bezeichnung ist **28**. Als Reaktion auf die Speicherplatzbelegung können Sie die Anzahl von gespeicherten Momentaufnahmen reduzieren. Die Anzahl von Momentaufnahmen kann ganz einfach auf beispielsweise 15 verringert werden, indem der letzte Parameter bei der Skriptausführung auf **15** festgelegt wird:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Wenn Sie das Skript mit dieser Einstellung ausführen, beträgt die Anzahl von Momentaufnahmen (einschließlich der neuen Speichermomentaufnahme) 15. Die 15 neuesten Momentaufnahmen werden gespeichert, und die 15 älteren Momentaufnahmen werden gelöscht.

 >[!NOTE]
 > Die Anzahl von Momentaufnahmen verringert sich durch dieses Skript aber nur, wenn Momentaufnahmen vorhanden sind, die älter als eine Stunde sind. Mit dem Skript werden keine Momentaufnahmen gelöscht, die weniger als eine Stunde alt sind. Diese Einschränkungen beziehen sich auf die bereitgestellten optionalen Funktionen für die Notfallwiederherstellung.

Falls Sie eine Gruppe von Momentaufnahmen mit dem Sicherungspräfix (in den Syntaxbeispielen **dailyhana**) nicht mehr benötigen, führen Sie das Skript mit der zu speichernden Anzahl **0** aus. Alle Momentaufnahmen, die für diese Bezeichnung eine Übereinstimmung ergeben, werden dann entfernt. Das Entfernen aller Momentaufnahmen kann sich auf die Funktionen für die Notfallwiederherstellung von großen HANA-Instanzen auswirken.

Alternativ können Sie bestimmte Momentaufnahmen auch mithilfe des Skripts `azure_hana_snapshot_delete` löschen. Dieses Skript dient zum Löschen einer Momentaufnahme oder einer Gruppe von Momentaufnahmen – entweder anhand der HANA-Sicherungs-ID aus HANA Studio oder anhand des Namens der Momentaufnahme. Die Sicherungs-ID ist derzeit nur mit den Momentaufnahmen verknüpft, die für den Momentaufnahmetyp **hana** erstellt werden. Für Momentaufnahmesicherungen vom Typ **logs** und **boot** wird keine SAP HANA-Momentaufnahme erstellt, sodass für diese Momentaufnahmen keine Sicherungs-ID zu finden ist. Bei Eingabe des Namens der Momentaufnahme wird auf den verschiedenen Volumes nach allen Momentaufnahmen gesucht, die dem eingegebenen Namen entsprechen. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Weitere Informationen zum Skript finden Sie unter „Delete a snapshot – azure_hana_snapshot_delete“ (Löschen einer Momentaufnahme – azure_hana_snapshot_delete) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.0/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.0.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).

Führen Sie das Skript als Benutzer **root** aus.

>[!IMPORTANT]
>Falls Daten nur auf der zu löschenden Momentaufnahme vorhanden sind, sind diese Daten nach dem Löschen der Momentaufnahme unwiederbringlich verloren.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Wiederherstellung auf Dateiebene basierend auf einer Speichermomentaufnahme

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Bei den Momentaufnahmetypen **hana** und **logs** können Sie direkt auf den Volumes im Verzeichnis **.snapshot** auf die Momentaufnahmen zugreifen. Dort finden Sie jeweils ein Unterverzeichnis für die einzelnen Momentaufnahmen. Kopieren Sie jede Datei in dem Zustand, der bei der Erstellung der Momentaufnahme geherrscht hat, aus diesem Unterverzeichnis in die eigentliche Verzeichnisstruktur. 

In der aktuellen Version des Skripts wird *kein* Wiederherstellungsskript für die Wiederherstellung einer Momentaufnahme als Self-Service bereitgestellt. Die Wiederherstellung einer Momentaufnahme kann während eines Failovers im Rahmen des Skripts zur Self-Service-Notfallwiederherstellung am Standort für die Notfallwiederherstellung ausgeführt werden. Um eine gewünschte Momentaufnahme aus den vorhandenen verfügbaren Momentaufnahmen wiederherzustellen, müssen Sie sich an das Microsoft-Betriebsteam wenden, indem Sie eine Serviceanfrage öffnen.

>[!NOTE]
>Die Wiederherstellung einzelner Dateien funktioniert nicht für Momentaufnahmen der Start-LUN, unabhängig vom Typ der HANA-Einheiten (große Instanzen). Das Verzeichnis **.snapshot** wird in der Start-LUN nicht verfügbar gemacht. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Wiederherstellen der letzten HANA-Momentaufnahme

Bei einem Ausfall der Produktionsumgebung kann die Wiederherstellung auf der Grundlage einer Speichermomentaufnahme als Kundenincident über den Microsoft Azure-Support gestartet werden. Eine äußerst dringliche Angelegenheit ist ein Szenario, bei dem Daten in einem Produktionssystem gelöscht wurden und die einzige Möglichkeit zu ihrer Wiedererlangung in der Wiederherstellung der Produktionsdatenbank besteht.

Eine Zeitpunktwiederherstellung kann dagegen eine niedrigere Dringlichkeit haben und Tage im Voraus geplant werden. Diese Wiederherstellung können Sie mit SAP HANA in Azure planen, anstatt ein Flag mit hoher Priorität auszulösen. Beispielsweise können Sie die Durchführung eines Upgrades für die SAP-Software planen, indem Sie ein neues Erweiterungspaket anwenden. Sie müssen dann eine Momentaufnahme wiederherstellen, die den Zustand vor dem Upgrade mit dem Erweiterungspaket darstellt.

Vor dem Senden der Anforderung müssen Sie diese vorbereiten. Das SAP HANA in Azure-Team kann die Anforderung dann bearbeiten und die wiederhergestellten Volumes bereitstellen. Anschließend stellen Sie die HANA-Datenbank basierend auf den Momentaufnahmen wieder her.

Informationen zu den Möglichkeiten zum Wiederherstellen einer Momentaufnahme mit den neuen Tools finden Sie unter „How to restore a snapshot“ (Wiederherstellen einer Momentaufnahme) in [Manual recovery guide for SAP HANA on Azure from a storage snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) (Handbuch für die manuelle Wiederherstellung einer Speichermomentaufnahme unter SAP HANA in Azure).

Führen Sie zur Vorbereitung auf die Anforderung die folgenden Schritte aus.

1. Entscheiden Sie, welche Momentaufnahme wiederhergestellt werden soll. Falls nicht anders angegeben, wird nur das Volume „hana/data“ wiederhergestellt. 

1. Fahren Sie die HANA-Instanz herunter.

   ![Herunterfahren der HANA-Instanz](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Heben Sie die Bereitstellung der Datenvolumes auf jedem Knoten der HANA-Datenbank auf. Wenn die Datenvolumes noch in das Betriebssystem eingebunden sind, tritt bei der Wiederherstellung der Momentaufnahme ein Fehler auf.

   ![Aufheben der Bereitstellung der Datenvolumes auf jedem Knoten der HANA-Datenbank](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Erstellen Sie eine Azure-Supportanfrage, und fügen Sie Anweisungen zur Wiederherstellung einer bestimmten Momentaufnahme ein:

   - Während der Wiederherstellung: Unter Umständen werden Sie vom SAP HANA in Azure-Dienst zu Koordinierungs-, Überprüfungs- und Bestätigungszwecken gebeten, an einer Telefonkonferenz teilzunehmen, um die Wiederherstellung der richtigen Speichermomentaufnahme sicherzustellen. 

   - Nach der Wiederherstellung: Sie werden vom SAP HANA in Azure-Dienst benachrichtigt, wenn die Speichermomentaufnahme wiederhergestellt wurde.

1. Stellen Sie nach Abschluss des Wiederherstellungsvorgangs alle Datenvolumes wieder bereit.

   ![Erneutes Bereitstellen aller Datenvolumes](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Eine weitere Möglichkeit zum Abrufen beispielsweise von SAP HANA-Datendateien, die aus einer Speichermomentaufnahme wiederhergestellt wurden, ist in Schritt 7 des [Manual recovery guide for SAP HANA on Azure from a storage snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) (Handbuch für die manuelle Wiederherstellung einer Speichermomentaufnahme unter SAP HANA in Azure) dokumentiert.

Informationen zum Wiederherstellen einer Momentaufnahmesicherung finden Sie unter [Manual recovery guide for SAP HANA on Azure from a storage snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) (Handbuch für die manuelle Wiederherstellung einer Speichermomentaufnahme unter SAP HANA in Azure). 

>[!Note]
>Wenn die Momentaufnahme von Microsoft Operations wiederhergestellt wurde, können Sie Schritt 7 auslassen.


### <a name="recover-to-another-point-in-time"></a>Wiederherstellen eines anderen Zeitpunkts
Informationen zum Wiederherstellen eines bestimmten Zeitpunkts finden Sie unter „Recover the database to the following point in time“ (Wiederherstellen der Datenbank zum folgenden Zeitpunkt) in [Manual recovery guide for SAP HANA on Azure from a storage snapshot](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/guides/Manual%20recovery%20of%20snapshot%20with%20HANA%20Studio.pdf) (Handbuch für die manuelle Wiederherstellung einer Speichermomentaufnahme unter SAP HANA in Azure). 


## <a name="next-steps"></a>Nächste Schritte
- Lesen Sie [Prinzipien und Vorbereitung für die Notfallwiederherstellung](hana-concept-preparation.md).
