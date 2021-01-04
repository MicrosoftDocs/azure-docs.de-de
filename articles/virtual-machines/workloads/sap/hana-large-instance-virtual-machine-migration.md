---
title: Migrieren von SAP HANA in Azure (große Instanzen) zu virtuellen Azure-Computern | Microsoft-Dokumentation
description: Migrieren von SAP HANA in Azure (große Instanzen) zu virtuellen Azure-Computern
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 25eae9f9ba0e28a5aa069972c8c7d5eb2877545f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967685"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA in Azure (große Instanzen) – Migration zu Azure Virtual Machines
In diesem Artikel werden mögliche Szenarien für die Bereitstellung von Azure (große Instanzen) beschrieben, und es werden Planungs- und Migrationskonzepte mit minimierten Ausfallzeiten beim Übergang angeboten.

## <a name="overview"></a>Übersicht
Seit der Ankündigung von Azure (große Instanzen) für SAP HANA (HLI) im September 2016 haben viele Kunden diese Hardware als Dienstangebot für ihre In-Memory-Computeplattform eingeführt.  In den letzten Jahren hat die Azure-VM-Größenerweiterung in Verbindung mit der Unterstützung der HANA-Bereitstellung mit horizontaler Skalierung die Kapazitätsanforderungen der ERP-Datenbanken der meisten Unternehmenskunden übertroffen.  Die ersten Kunden bekunden mittlerweile Interesse, ihre SAP HANA-Workloads von physischen Servern zu Azure-VMs zu migrieren.
Dieser Leitfaden ist keine Schritt-für-Schritt-Anleitung zur Konfiguration. Er beschreibt die allgemeinen Bereitstellungsmodelle und gibt Ratschläge zu Planung und Migration.  Dies geschieht in der Absicht, die erforderlichen vorbereitenden Überlegungen bewusst zu machen, um die Ausfallzeit bei der Umstellung zu minimieren.

## <a name="assumptions"></a>Annahmen
Dieser Artikel geht von den folgenden Annahmen aus:
- Es wird ausschließlich eine homogene Migration von HANA-Datenbank-Computediensten von HANA (große Instanzen; HANA Large Instance, HLI) zu einem virtuellen Azure-Computer ohne größere Softwareupgrades oder -patches berücksichtigt. Geringfügige Updates schließen die Verwendung einer neueren Betriebssystemversion oder HANA-Version ein, wenn diese ausdrücklich in den relevanten SAP-Hinweisen als unterstützt angegeben ist. 
- Alle Update-/Upgradeaktivitäten müssen vor oder nach der Migration erfolgen.  Beispiel: Umwandlung von SAP HANA MCOS- in MDC-Bereitstellung. 
- Der Migrationsansatz mit den geringsten Ausfallzeiten ist die SAP HANA-Systemreplikation. Andere Migrationsmethoden werden in diesem Dokument nicht behandelt.
- Dieser Leitfaden bezieht sich auf die SKUs Rev3 und Rev4 von HLI.
- Die HANA-Bereitstellungsarchitektur bleibt während der Migration größtenteils unverändert.  Das heißt, ein System mit Notfallwiederherstellung auf einer einzelnen Instanz bleibt auf dem Ziel auf gleiche Weise erhalten.
- Die Kunden haben die Vereinbarung zum Servicelevel (SLA) der Zielarchitektur gelesen und verstanden. 
- Die gewerblichen Nutzungsbedingungen von HLIs und VMs unterscheiden sich. Kunden sollten zwecks Kostenmanagement die Nutzung ihrer VMs überwachen.
- Den Kunden sollte bewusst sein, dass HLI eine dedizierte Computeplattform ist, während VMs von einander isoliert auf einer gemeinsamen Infrastruktur ausgeführt werden.
- Die Kunden haben bestätigt, dass die Ziel-VMs Ihre beabsichtigte Architektur unterstützen. Alle unterstützten VM-SKUs, die für SAP HANA-Bereitstellungen zertifiziert sind, finden Sie im [SAP HANA-Hardwareverzeichnis](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Die Kunden haben den Entwurf und den Migrationsplan überprüft.
- Planen Sie VMs für die Notfallwiederherstellung zusammen mit dem primären Standort.  Kunden können den HLI-Knoten nicht für die Notfallwiederherstellung am primären Standort verwenden, der nach der Migration auf VMs ausgeführt wird.
- Die Kunden haben die erforderlichen Sicherungsdateien entsprechend den Anforderungen für Geschäftswiederherstellung und Compliance auf die Ziel-VMs kopiert. Dies ermöglicht bei von den virtuellen Computern aus zugänglichen Sicherungen die Zeitpunktwiederherstellung während des Übergangszeitraums.
- Für HSR-Hochverfügbarkeit müssen Kunden das STONITH-Gerät gemäß den SAP HANA-Anweisungen zur Hochverfügbarkeit für [SLES](./high-availability-guide-suse-pacemaker.md) und [RHEL](./high-availability-guide-rhel-pacemaker.md) einrichten und konfigurieren.  Dies ist nicht wie HLI vorkonfiguriert.
- Dieser Migrationsansatz ist nicht für HLI-SKUs mit Optane-Konfiguration ausgelegt.

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien
In der folgenden Tabelle sind allgemeine Bereitstellungsmodelle mit HLI-Kunden zusammengefasst.  Die Migration zu Azure-VMs ist für alle HLI-Szenarien möglich.  Um die verfügbaren ergänzenden Azure-Dienste nutzen zu können, sind möglicherweise kleinere Änderungen an der Architektur erforderlich.

| Szenario-ID | HLI-Szenario | Unverändert zu VM migrieren? | Anmerkung |
| --- | --- | --- | --- |
| 1 | [Einzelner Knoten mit einer SID](./hana-supported-scenario.md#single-node-with-one-sid) | Ja | - |
| 2 | [Einzelner Knoten mit MCOS](./hana-supported-scenario.md#single-node-mcos) | Ja | - |
| 3 | [Einzelner Knoten mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Nein | Die Speicherreplikation ist für die virtuelle Azure-Plattform nicht verfügbar. Ändern Sie die aktuelle Notfallwiederherstellungslösung in HSR oder Sicherung/Wiederherstellung. |
| 4 | [Einzelner Knoten mit DR (Mehrzweck) mithilfe der Speicherreplikation](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Nein | Die Speicherreplikation ist für die virtuelle Azure-Plattform nicht verfügbar. Ändern Sie die aktuelle Notfallwiederherstellungslösung in HSR oder Sicherung/Wiederherstellung. |
| 5 | [HSR mit STONITH für Hochverfügbarkeit](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Ja | Keine vorkonfigurierte SBD für Ziel-VMs.  Auswählen und Bereitstellen einer STONITH-Lösung.  Mögliche Optionen: Azure Fencing Agent (unterstützt sowohl für [RHEL](./high-availability-guide-rhel-pacemaker.md) als auch für [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [Hochverfügbarkeit mit HSR, Notfallwiederherstellung mit Speicherreplikation](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Nein | Ersetzen der Speicherreplikation für Notfallwiederherstellungsanforderungen durch HSR oder Sicherung/Wiederherstellung |
| 7 | [Automatisches Hostfailover (1+1)](./hana-supported-scenario.md#host-auto-failover-11) | Ja | Verwenden von ANF für freigegebenen Speicher mit Azure-VMs |
| 8 | [Horizontales Hochskalieren mit Standby](./hana-supported-scenario.md#scale-out-with-standby) | Ja | BW/4HANA mit VMs der Typen M128s, M416s, M416ms nur mit ANF für Speicherung |
| 9 | [Horizontales Hochskalieren ohne Standby](./hana-supported-scenario.md#scale-out-without-standby) | Ja | BW/4HANA mit VMs der Typen M128s, M416s, M416ms (mit oder ohne ANF für Speicherung) |
| 10 | [Horizontale Skalierung mit DR mithilfe der Speicherreplikation](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Nein | Ersetzen der Speicherreplikation für Notfallwiederherstellungsanforderungen durch HSR oder Sicherung/Wiederherstellung |
| 11 | [Einzelner Knoten mit DR über HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Ja | - |
| 12 | [HSR in einem einzelnen Knoten für Notfallwiederherstellung (kostenoptimiert)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Ja | - |
| 13 | [Hochverfügbarkeit und Notfallwiederherstellung mit HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Ja | - |
| 14 | [Hochverfügbarkeit und Notfallwiederherstellung mit HSR (kostenoptimiert)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Ja | - |
| 15 | [Horizontale Skalierung mit DR über HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Ja | BW/4HANA mit VMs der Typen M128s. M416s, M416ms (mit oder ohne ANF für Speicherung) |


## <a name="source-hli-planning"></a>Quellplanung (HLI)
Beim Onboarding eines HLI-Servers mussten das Microsoft Service Management und die Kunden Compute, Netzwerk, Speicher und betriebssystemspezifische Einstellungen planen, um die SAP HANA-Datenbank auszuführen.  Eine ähnliche Planung muss für die Migration zur Azure-VM durchgeführt werden.

### <a name="sap-hana-housekeeping"></a>SAP HANA-Housekeeping 
Es ist eine bewährte Betriebspraxis, den Datenbankinhalt zu bereinigen, sodass unerwünschte oder veraltete Daten sowie veraltete Protokolle nicht zur neuen Datenbank migriert werden.  Das Housekeeping umfasst im Allgemeinen das Löschen oder Archivieren alter, abgelaufener oder inaktiver Daten.  Diese „Datenhygiene“-Aktionen sollten in Nicht-Produktionssystemen getestet werden, um die Gültigkeit der Datenbereinigung vor dem Einsatz in einer Produktionsumgebung zu überprüfen.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Zulassen der Netzwerkkonnektivität für neue VMs oder virtuelle Netzwerke 
In der HLI-Bereitstellung eines Kunden wurde das Netzwerk entsprechend den Informationen im Artikel [SAP HANA-Netzwerkarchitektur (große Instanzen)](./hana-network-architecture.md) eingerichtet. Das Routing von Netzwerkdatenverkehr erfolgt außerdem wie im Abschnitt „Routing in Azure“ beschrieben.
- Durch Einrichten einer neuen VM als Migrationsziel ist kein weiteres Update der Konnektivität erforderlich, falls die VM im vorhandenen virtuellen Netzwerk mit IP-Adressbereichen platziert wird, die bereits für Verbindungen mit HLI zugelassen sind.
- Wenn die neue Azure-VM in einem neuen Microsoft Azure Virtual Network platziert wird – etwa in einer anderen Region – und per Peering mit dem vorhandenen virtuellen Netzwerk verbunden wird, können der ExpressRoute-Dienstschlüssel und die Ressourcen-ID aus der ursprünglichen HLI-Bereitstellung verwendet werden, um den Zugriff dieses neuen IP-Adressbereichs für das virtuelle Netzwerk zuzulassen.  Sprechen Sie sich mit Microsoft Service Management ab, um die Verbindung des virtuellen Netzwerks mit HLI zuzulassen.  Hinweis: Um die Netzwerklatenz zwischen der Anwendungs- und der Datenbankschicht zu minimieren, müssen sich die Anwendungs- und die Datenbankschichten im selben virtuellen Netzwerk befinden.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Vorhandene Verfügbarkeitsgruppe, Verfügbarkeitszonen und Näherungsplatzierungsgruppe (Proximity Placement Group, PPG) der Anwendungsschicht
Das aktuelle Bereitstellungsmodell soll bestimmte Servicelevelziele erfüllen.  Stellen Sie bei dieser Verschiebung sicher, dass die Zielinfrastruktur die festgelegten Ziele erfüllt oder überschreitet.  
Höchstwahrscheinlich werden die SAP-Anwendungsserver der Kunden in einer Verfügbarkeitsgruppe platziert.  Wenn der aktuelle Servicelevel der Bereitstellung zufriedenstellend ist, gilt Folgendes: 
- Wenn die Ziel-VM den logischen HLI-Namen als Hostnamen übernimmt, würde die Aktualisierung der DNS-Adressauflösung (Domain Name Service), die auf die IP der VM verweist, ohne Aktualisierung der SAP-Profile funktionieren.
- Wenn Sie keine PPG verwenden, platzieren Sie alle Anwendungs- und DB-Server in derselben Zone, um die Netzwerklatenz zu minimieren.
- Wenn Sie PPG verwenden, finden Sie weitere Informationen in diesem Abschnitt dieses Dokuments: „Zielplanung, Verfügbarkeitsgruppe, Verfügbarkeitszonen und Näherungsplatzierungsgruppe (Proximity Placement Group, PPG)“.

### <a name="storage-replication-discontinuance-process-if-used"></a>Verfahren zur Beendigung der Speicherreplikation (sofern verwendet)
Wenn Speicherreplikation als Verfahren zur Notfallwiederherstellung verwendet wird, sollte sie nach dem Herunterfahren der SAP-Anwendung beendet (ihre Planung aufgehoben) werden.  Darüber hinaus wurden der letzte SAP HANA-Katalog, die Protokolldatei und Datensicherungen auf die DR HLI-Remotespeichervolumes repliziert.  Dies stellt eine Vorsichtsmaßnahme dar, die beim Eintreten eines Notfalls während des Übergangs von physischem Server zur Azure VM zum Tragen kommt.

### <a name="data-backups-preservation-consideration"></a>Überlegungen zur Beibehaltung von Datensicherungen
Nach dem Übergang zu SAP HANA auf der Azure-VM sind die Momentaufnahmedaten oder Protokollsicherungen unter HLI nicht mehr einfach zugänglich und können nicht ohne Weiteres bei Bedarf wiederhergestellt werden. Im frühen Übergangszeitraum, bevor die Azure-basierte Sicherung einen ausreichend langen Verlauf aufgebaut hat, um Anforderungen zur Zeitpunktwiederherstellung zu erfüllen, empfiehlt es sich, einige Tage/Wochen vor der Umstellung zusätzlich zu den Momentaufnahmen in HLI Sicherungen auf Dateiebene zu erstellen.  Kopieren Sie diese Sicherungen in ein Azure Storage-Konto, auf das die neue SAP HANA-VM zugreifen kann.
Über das Sichern der HLI-Inhalte hinaus ist es außerdem ratsam, die vollständigen Sicherungen der SAP-Landschaft zugänglich zu halten, falls ein Rollback erforderlich ist.

### <a name="adjusting-system-monitoring"></a>Anpassen der Systemüberwachung 
Kunden verwenden viele verschiedene Tools, um Warnbenachrichtigungen für Systeme in ihrer SAP-Landschaft zu überwachen und zu senden.  Bei diesem Element handelt es sich lediglich um einen Aufruf zu einer geeigneten Aktion, um Änderungen in die Überwachung einzubeziehen und ggf. die Empfänger von Warnbenachrichtigungen zu aktualisieren.

### <a name="microsoft-operations-team-involvement"></a>Beteiligung des Microsoft Operations-Teams 
Öffnen Sie über das Azure-Portal ein Ticket für die vorhandene HLI-Instanz.  Nach dem Erstellen des Supporttickets werden Sie per E-Mail von einem Supporttechniker kontaktiert.  

### <a name="engage-microsoft-account-team"></a>Einbeziehung des Microsoft-Kontoteams
Planen Sie die Migration in der Nähe des Verlängerungszeitpunkts für den HLI-Vertrag am Jahrestag, um unnötige Zusatzkosten für Computeressourcen zu vermeiden. Um den HLI-Bladeserver außer Betrieb zu nehmen, müssen Sie die Vertragsbeendigung und das eigentliche Herunterfahren der Einheit koordinieren.

## <a name="destination-planning"></a>Zielplanung
Das Aufbauen einer neuen Infrastruktur als Ersatz für eine vorhandene setzt einige Überlegungen voraus, um sicherzustellen, dass der Neuzugang in das Gesamtbild passt.  Im Folgenden finden Sie einige der wichtigsten zu beachtenden Punkte.

### <a name="resource-availability-in-the-target-region"></a>Verfügbarkeit von Ressourcen in der Zielregion 
Die aktuelle Bereitstellungsregion des SAP-Anwendungsservers befindet sich normalerweise in unmittelbarer Nähe zu den zugeordneten HLIs.  HLIs werden jedoch an weniger Standorten angeboten, als Azure-Regionen verfügbar sind.  Die Migration der physischen HLI-Umgebung zu Azure-VM stellt außerdem einen guten Anlass dar, die Nähe aller beteiligten Dienste zwecks Leistungsoptimierung zu anzupassen.  Dabei sollte vor allem sichergestellt werden, dass in der ausgewählten Region alle erforderlichen Ressourcen vorhanden sind.  Beispiele: Verfügbarkeit bestimmter VM-Familien oder das Angebot von Azure-Zonen zum Einrichten von Hochverfügbarkeit.

### <a name="virtual-network"></a>Virtuelles Netzwerk 
Die Kunden müssen entscheiden, ob die neue HANA-Datenbank in einem vorhandenen virtuellen Netzwerk betrieben oder ein neues erstellt werden soll.  Der wichtigste Faktor für diese Entscheidung ist das aktuelle Netzwerklayout für die SAP-Landschaft.  Außerdem bringt es Änderungen der Architektur mit sich, wenn die Infrastruktur von einer Bereitstellung in einer Zone zu einer in zwei Zonen mit PPG wechselt. Weitere Informationen finden Sie im Artikel [Azure-PPG für optimale Netzwerklatenz bei SAP-Anwendungen](./sap-proximity-placement-scenarios.md).   

### <a name="security"></a>Sicherheit
Unabhängig davon, ob die neue SAP HANA-VM in einem neuen oder bereits vorhandenen VNET/Subnetz betrieben wird, stellt sie einen wichtigen Geschäftsdienst dar, der abgesichert werden muss.  Für diese neue Dienstklasse muss eine Zugriffsteuerung bewertet und bereitgestellt werden, die der Informationssicherheitsrichtlinie des Unternehmens entspricht.

### <a name="vm-sizing-recommendation"></a>Empfehlungen für die VM-Größe
Die Migration stellt außerdem eine gute Gelegenheit dar, die Größe Ihrer HANA-Compute-Engine zu optimieren.  Sie können HANA-[Systemansichten](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) in Verbindung mit HANA Studio verwenden, um den Verbrauch von Systemressourcen zu verstehen und damit die richtige Größe für eine höhere Ausgabeneffizienz zu ermitteln.

### <a name="storage"></a>Storage 
Die Speicherleistung ist einer der Faktoren, die sich auf die Benutzererfahrung mit SAP-Anwendungen auswirken.  Für jede VM-SKU gibt es eine Mindestempfehlung für das Speicherlayout, die unter [SAP HANA: Speicherkonfigurationen für virtuelle Azure-Computer](./hana-vm-operations-storage.md) veröffentlicht ist. Es wird empfohlen, diese Mindestspezifikationen zu überprüfen und mit den vorhandenen HLI-Systemstatistiken zu vergleichen, um eine angemessene E/A-Kapazität und Leistung für die neue HANA-VM sicherzustellen.

Wenn Sie eine PPG für die neue HANA-VM und die zugehörigen Server konfigurieren, übermitteln Sie ein Supportticket, um die Zusammenstellung des Speichers und der VM zu überprüfen und sicherzustellen. Da die Sicherungslösung möglicherweise geändert werden muss, sollten die Speicherkosten ebenfalls erneut untersucht werden, um Überraschungen bei den Betriebsausgaben zu vermeiden.

### <a name="storage-replication-for-disaster-recovery"></a>Speicherreplikation für die Notfallwiederherstellung
Mit der Einführung von HLI wurde Speicherreplikation als Standardoption für die Notfallwiederherstellung angeboten. Dieses Feature ist nicht die Standardoption für SAP HANA auf Azure-VMs. Ziehen Sie HSR, Sicherung/Wiederherstellung oder andere unterstützte Lösungen gemäß Ihren geschäftlichen Anforderungen in Betracht.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Verfügbarkeitsgruppen, Verfügbarkeitszonen und Näherungsplatzierungsgruppen 
Um die Entfernung zwischen der Anwendungsschicht und SAP HANA zu verringern und die Netzwerklatenz so gering wie möglich zu halten, sollten die neue Datenbank-VM und die aktuellen SAP-Anwendungsserver in einer PPG platziert werden. Informationen zur Funktionsweise von Verfügbarkeitsgruppen und Verfügbarkeitszonen in Azure mit PPG für SAP-Bereitstellungen finden Sie unter [Azure-Näherungsplatzierungsgruppen für optimale Netzwerklatenz mit SAP-Anwendungen](./sap-proximity-placement-scenarios.md).
Wenn Teile des HANA-Zielsystems in mehreren Azure-Zonen bereitgestellt werden, sollten die Kunden über eine klare Ansicht des Latenzprofils der ausgewählten Zonen verfügen. Die Platzierung der SAP-Systemkomponenten ist in Bezug auf die Entfernung zwischen der SAP-Anwendung und der Datenbank optimal.  Mit dem [Testtool für die Verfügbarkeitszonenlatenz](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) (Public Domain) können Sie die Messung vereinfachen.  


### <a name="backup-strategy"></a>Sicherungsstrategie
Viele Kunden verwenden bereits Sicherungslösungen von Drittanbietern für SAP HANA in HLI.  In diesem Fall müssen nur eine zusätzliche geschützte VM und die HANA-Datenbanken konfiguriert werden.  Laufende HLI-Sicherungsaufträge können jetzt aus der Planung entfernt werden, wenn der Computer nach der Migration außer Betrieb gesetzt wird.
Azure Backup für SAP HANA auf VMs ist jetzt allgemein verfügbar.  Unter den folgenden Links finden Sie ausführliche Informationen: [Sichern](../../../backup/backup-azure-sap-hana-database.md), [Wiederherstellen](../../../backup/sap-hana-db-restore.md), [Verwalten](../../../backup/sap-hana-db-manage.md) von SAP HANA-Sicherungen auf Azure-VMs.

### <a name="dr-strategy"></a>Strategie zur Notfallwiederherstellung
Wenn Ihre Servicelevelziele eine längere Wiederherstellungszeit, eine einfache Sicherung in Blob Storage und eine lokale Wiederherstellung oder Wiederherstellung auf einer neuen VM zulassen, ist dies die einfachste und kostengünstigste Strategie zur Notfallwiederherstellung.  
Wie bei der Plattform für große Instanzen, bei der die HANA-Notfallwiederherstellung üblicherweise mit HSR ausgeführt wird, ist HSR auch auf virtuellen Azure-Computern die natürlichste und native SAP HANA-Notfallwiederherstellungslösung.  Unabhängig davon, ob Ihre Quellbereitstellung aus einer Einzelinstanz oder einem Cluster besteht, ist ein Replikat der Quellinfrastruktur in der Region für die Notfallwiederherstellung erforderlich.
Dieses Replikat zur Notfallwiederherstellung wird nach Abschluss der Migration der primären HLI-Umgebung zur VM konfiguriert.  Die HANA-Datenbankinstanz zur Notfallwiederherstellung wird bei der primären SAP HANA-Instanz auf der VM als sekundärer Replikationsstandort registriert.  

### <a name="sap-application-server-connectivity-destination-change"></a>Änderung der Verbindungsziele für SAP-Anwendungsserver
Die HSR-Migration führt zu einem neuen HANA-Datenbankhost und somit zu einem neuen Datenbankhostnamen für die Anwendungsschicht. SAP-Profile müssen entsprechend dem neuen Hostnamen geändert werden.  Wenn der Wechsel durch Namensauflösung unter Beibehaltung des Hostnamens erfolgt, ist keine Profiländerung erforderlich.

### <a name="operating-system"></a>Betriebssystem
Die Betriebssystemimages für HLI und VMs sind nicht identisch, obwohl sie dieselbe Releaseversion aufweisen, z. B. SLES 12 SP4. Kunden müssen die erforderlichen Pakete, Hotfixes, Patches, Kernel und Sicherheitskorrekturen auf HLI validieren, um die gleichen Pakete auf dem Ziel zu installieren.  Die Verwendung von HSR zum Replizieren von einem älteren Betriebssystem auf eine VM mit einer neueren Betriebssystemversion wird unterstützt.  Die im Einzelnen unterstützten Versionen können Sie im [SAP-Hinweis 2763388](https://launchpad.support.sap.com/#/notes/2763388) überprüfen.

### <a name="new-sap-license-request"></a>Neue SAP-Lizenzanforderung
Eine einfache Anforderung einer neuen SAP-Lizenz für das neue HANA-System nach der Migration zu VMs.

### <a name="service-level-agreement-sla-differences"></a>Unterschiede in den Vereinbarungen zum Servicelevel (SLA)
Die Autoren möchten auf den Unterschied der Verfügbarkeits-SLA zwischen HLI und Azure-VM hinweisen.  Beispielsweise bieten Hochverfügbarkeitspaare geclusterter HLIs eine Verfügbarkeit von 99,99 %. Um die gleiche SLA zu erreichen, müssen VMs in Verfügbarkeitszonen bereitgestellt werden. In diesem [Artikel](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) wird die Verfügbarkeit mit zugeordneten Bereitstellungsarchitekturen erläutert, damit Kunden ihre Zielinfrastruktur entsprechend planen können.


## <a name="migration-strategy"></a>Migrationsstrategie
In diesem Dokument wird nur der HANA-Systemreplikationsansatz für die Migration von HLI zu Azure-VMs behandelt.  Abhängig von der bereitgestellten Zielspeicherlösung unterscheidet sich das Verfahren geringfügig. Die allgemeinen Schritte sind nachfolgend beschrieben.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM mit Datenträgern in den Tarifen Premium/Ultra für Daten
Bei VMs, die mit Datenträgern in den Tarifen Premium oder Ultra bereitgestellt werden, gilt die Standardkonfiguration für die SAP HANA-Systemreplikation für die Einrichtung von HSR.  Der [SAP-Hilfeartikel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) bietet eine Übersicht über die Schritte zum Einrichten der Systemreplikation, Übergeben auf ein sekundäres System, Failback auf das primäre System und Deaktivieren der Systemreplikation.  Für die Migration benötigen wir nur die Schritte zur Einrichtung, zum Übergeben und zum Deaktivieren der Replikation.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM mit ANF für Daten- und Protokollvolumes
Auf allgemeiner Ebene müssen die neuesten HLI-Speichermomentaufnahmen der vollständigen Daten- und Protokollvolumes in Azure Storage kopiert werden, wo sie für die HANA-Ziel-VM zugänglich sind und wiederhergestellt werden können.  Der Kopiervorgang kann mit beliebigen nativen Linux-Kopiertools durchgeführt werden.  

> [!IMPORTANT]
> Das Kopieren und die Datenübertragung können abhängig von der HANA-Datenbankgröße und der Netzwerkbandbreite mehrere Stunden dauern.  Der Großteil des Kopiervorgangs sollte vor der Ausfallzeit der primären HANA-Datenbank erfolgen.

### <a name="mcos-to-mdc-conversion"></a>Wechsel von MCOS zu MDC
Einige unserer HLI-Kunden haben das Bereitstellungsmodell mit mehreren Komponenten in einem System (Multiple Components in One System, MCOS) verwendet.  Die Motivation bestand darin, die Einschränkungen bei MDC-Speichermomentaufnahmen (Multiple Databases Container) früherer SAP HANA-Versionen zu umgehen.  Im MCOS-Modell werden mehrere unabhängige SAP HANA-Instanzen auf einem HLI-Bladeserver gestapelt.  HSR kann problemlos für die Migration verwendet werden, führt aber zu mehreren HANA-VMs mit jeweils einer Mandanten-DB.  Die Landschaft ist in diesem Endzustand möglicherweise umfangreicher, als ein Kunde gewöhnt ist.  Mit MDC als der SAP HANA 2.0-Standardbereitstellung besteht eine sinnvolle Alternative darin, [HANA-Mandanten](https://launchpad.support.sap.com/#/notes/2472478) nach der HSR-Migration zu verschieben.  Bei diesem Vorgang werden diese unabhängigen HANA-Datenbanken als gemeinsame Mandanten in einem einzigen HANA-Container konsolidiert.  

### <a name="application-layer-consideration"></a>Überlegungen zur Anwendungsschicht
Der Datenbankserver gilt als Mittelpunkt eines SAP-Systems.  Alle Anwendungsserver sollten sich in der Nähe der SAP HANA-Datenbank befinden.  Wenn eine PPG neu verwendet werden soll, müssen in einigen Fällen möglicherweise vorhandene Anwendungsserver in die PPG verschoben werden, in der sich die HANA-VM befindet.  Das Erstellen neuer Anwendungsserver wird möglicherweise als einfacher angesehen, wenn Sie bereits über Bereitstellungsvorlagen verfügen.  
Wenn die vorhandenen Anwendungsserver und die neue HANA-VM optimal platziert wurden, müssen keine neuen Anwendungsserver erstellt werden, sofern keine zusätzliche Kapazität erforderlich ist.  
Wenn eine neue Infrastruktur zur Verbesserung der Dienstverfügbarkeit erstellt wird, sind die vorhandenen Anwendungsserver möglicherweise nicht mehr erforderlich und sollten heruntergefahren und gelöscht werden.
Wenn der Hostname der Ziel-VM geändert wurde und sich vom HLI-Hostnamen unterscheidet, müssen die SAP-Anwendungsserverprofile angepasst werden, sodass sie auf den neuen Host verweisen.  Wenn sich nur die IP-Adresse der HANA-Datenbank geändert hat, ist eine Aktualisierung des DNS-Eintrags erforderlich, um eingehende Verbindungen zur neuen HANA-VM zu leiten.

### <a name="acceptance-test"></a>Akzeptanztest
Auch wenn die Migration von HLI zu VMs im Vergleich zu einer heterogenen Migration nicht zu wesentlichen Änderungen an den Datenbankinhalten führt, empfiehlt es sich trotzdem, die wichtigsten Funktions- und Leistungsaspekte der neuen Installation zu überprüfen.  

### <a name="cutover-plan"></a>Umstellungsplan
Diese Migration ist zwar sehr direkt, schließt jedoch auch die Außerbetriebnahme einer vorhandenen Datenbank ein.  Eine sorgfältige Planung ist von zentraler Bedeutung, um das Quellsystem mit den zugehörigen Inhalten und Sicherungsimages auch für den Fall beizubehalten, dass ein Fallback erforderlich ist.  Eine gute Planung ermöglicht eine schnellere Umkehrung.   


## <a name="post-migration"></a>Nach der Migration
Der Migrationsauftrag wird erst ausgeführt, wenn alle HLI-abhängigen Dienste und Verbindungen sicher entkoppelt wurden, um sicherzustellen, dass die Datenintegrität gewahrt bleibt.  Fahren Sie außerdem nicht benötigte Dienste herunter.  In diesem Abschnitt werden einige der wichtigsten Elemente erläutert.

### <a name="decommissioning-the-hli"></a>Außerbetriebsetzung der HLI-Umgebung
Achten Sie darauf, dass nach einer erfolgreichen Migration der HANA-Datenbank zu einer Azure-VM keine Produktionsgeschäftstransaktionen in der HLI-DB durchgeführt werden.  Es ist jedoch ein sicheres Verfahren, die HLI-Umgebung für einen Zeitraum gleich dem lokalen Beibehaltungsfenster für Sicherungsdaten weiterhin auszuführen, um bei Bedarf eine schnellere Wiederherstellung sicherzustellen.  Erst dann sollte der HLI-Bladeserver außer Betrieb genommen werden.  Kunden sollten die HLI-Verpflichtungen mit Microsoft in vertraglicher Form festhalten und sich dazu an ihren Microsoft-Vertriebspartner wenden.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Entfernen Sie etwaige Proxys, die für HLI konfiguriert sind (z. B. iptables oder BIG-IP). 
Wenn ein Proxydienst wie iptables zum Weiterleiten von lokalem Datenverkehr an und von HLI verwendet wird, ist er nach der erfolgreichen Migration zum virtuellen Computer nicht mehr erforderlich.  Dieser Konnektivitätsdienst sollte jedoch so lange beibehalten werden, wie der HLI-Bladeserver noch bereitsteht.  Fahren Sie den Dienst erst herunter, nachdem der HLI-Bladeserver vollständig außer Betrieb genommen wurde.

### <a name="remove-global-reach-for-hli"></a>Entfernen von Global Reach für HLI 
Global Reach wird verwendet, um das ExpressRoute-Gateway von Kunden mit dem HLI ExpressRoute-Gateway zu verbinden.  Es macht es möglich, dass der lokale Datenverkehr von Kunden den HLI-Mandanten direkt erreicht, ohne Nutzung eines Proxydiensts.  Diese Verbindung wird nach der Migration und in Abwesenheit der HLI-Einheit nicht mehr benötigt.  Wie im Fall des iptables-Proxydiensts sollte auch Global Reach aufrechterhalten werden, bis der HLI-Bladeserver vollständig außer Betrieb genommen wurde.

### <a name="operating-system-subscription--movereuse"></a>Betriebssystemabonnement: Verschieben/Wiederverwenden
Im Verlauf der Einrichtung der VM-Server und der Außerbetriebnahme der HLI-Bladeserver können die Betriebssystemabonnements ersetzt oder wiederverwendet werden, um eine doppelte Bezahlung von Betriebssystemlizenzen zu vermeiden.



## <a name="next-steps"></a>Nächste Schritte
Informationen hierzu finden Sie in diesen Artikeln:
- [SAP HANA-Infrastrukturkonfigurationen und -vorgänge in Azure](./hana-vm-operations.md)
- [Prüfliste für die Planung und Bereitstellung von SAP-Workloads in Azure](./sap-deployment-checklist.md)
