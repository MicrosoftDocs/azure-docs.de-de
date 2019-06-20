---
title: Azure Migrate – Häufig gestellte Fragen (FAQ) | Microsoft-Dokumentation
description: Beantwortet häufig gestellte Fragen zu Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 17cead93325da903161d95b315435d6e7b106dbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61292978"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – Häufig gestellte Fragen (FAQ)

Dieser Artikel enthält häufig gestellte Fragen zu Azure Migrate. Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Migrate-Forum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Allgemein

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Unterstützt Azure Migrate nur die Bewertung von VMware-Workloads?

Ja, Azure Migrate unterstützt derzeit nur die Bewertung von VMware-Workloads. Die Unterstützung für Hyper-V befindet sich in der Vorschau. Melden Sie sich [hier](https://aka.ms/migratefuture) an, um Zugriff auf die Vorschauversion zu erhalten. Die Unterstützung für physische Server wird zukünftig hinzugefügt.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Benötigt Azure Migrate vCenter Server zum Ermitteln einer VMware-Umgebung?

Ja, Azure Migrate benötigt vCenter Server zum Ermitteln einer VMware-Umgebung. Die Ermittlung von ESXi-Hosts, die nicht von einem vCenter Server verwaltet werden, wird nicht unterstützt.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Was unterscheidet Azure Migrate von Azure Site Recovery?

Azure Migrate ist ein Bewertungsdienst, mit dem Sie Ihre lokalen Workloads bestimmen und Ihre Migration zu Azure planen können. Mit [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), das zudem eine Notfallwiederherstellungslösung ist, können Sie lokale Workloads zu IaaS-VMs in Azure migrieren.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Was ist der Unterschied zwischen der Verwendung von Azure Migrate für Bewertungen und dem MAP Toolkit?

[Azure Migrate](migrate-overview.md) bietet eine Migrationsbewertung speziell für die Unterstützung der Migrationsbereitschaft und die Auswertung lokaler Workloads in Azure. Das [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) bietet weitere Funktionen, beispielsweise die Migrationsplanung für neuere Versionen von Windows-Client- und -Serverbetriebssystemen sowie die Nachverfolgung der Softwarenutzung. Verwenden Sie für diese Szenarien weiterhin das MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Was unterscheidet Azure Migrate von Azure Site Recovery-Bereitstellungsplaner?

Azure Migrate ist ein Tool für die Migrationsplanung und Azure Site Recovery-Bereitstellungsplaner ist ein Tool für die Notfallwiederherstellungsplanung.

**Migration von VMware zu Azure:** Wenn Sie beabsichtigen, Ihre lokalen Workloads zu Azure zu migrieren, verwenden Sie für die Migrationsplanung Azure Migrate. Azure Migrate bewertet lokale Workloads und bietet Anleitungen, Einblicke und Mechanismen, um Sie bei der Migration zu Azure zu unterstützen. Wenn Ihr Migrationsplan fertig ist, können Sie Dienste wie Azure Site Recovery und Azure Database Migration Service nutzen, um die Computer zu Azure zu migrieren.

**Migration von Hyper-V zu Azure:** Derzeit unterstützt die allgemein verfügbare Version von Azure Migrate die Bewertung virtueller VMware-Computer für die Migration zu Azure. Die Unterstützung für Hyper-V befindet sich in der Vorschau mit Produktionsunterstützung. Wenn Sie daran interessiert sind, die Vorschauversion zu testen, registrieren Sie sich [hier](https://aka.ms/migratefuture).

**Notfallwiederherstellung von VMware/Hyper-V zu Azure:** Wenn Sie beabsichtigen, eine Notfallwiederherstellung (DR) mit Azure Site Recovery zu Azure durchzuführen, verwenden Sie den Azure Site Recovery-Bereitstellungsplaner für die DR-Planung. Der Site Recovery-Bereitstellungsplaner führt eine umfassende, ASR-spezifische Bewertung Ihrer lokalen Umgebung durch. Er bietet Empfehlungen, die Site Recovery für erfolgreiche DR-Vorgänge benötigt, z.B. Replikation und Failover Ihrer virtuellen Computer.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Welche Azure-Geografien werden von Azure Migrate unterstützt?

Azure Migrate unterstützt derzeit „Europa“, „USA“ und „Azure Government“ als Projektgeografien. Auch wenn Sie Migrationsprojekte nur in diesen Geografien erstellen können, haben Sie dennoch die Möglichkeit, Ihre Computer für [mehrere Zielstandorte](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties) zu bewerten. Die Projektgeografie wird nur zum Speichern der ermittelten Metadaten verwendet.

**Geografie** | **Speicherort der Metadaten**
--- | ---
Azure Government | US Government, Virginia
Asien | Asien, Südosten
Europa | „Europa, Norden“ oder „Europa, Westen“
USA | „USA, Osten“ oder „USA, Westen-Mitte“

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Wie stellt der lokale Standort eine Verbindung mit Azure Migrate her?

Die Verbindung kann über das Internet erfolgen oder ExpressRoute mit öffentlichem Peering nutzen.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate"></a>Welche Netzwerkverbindungsanforderungen sind für Azure Migrate erforderlich?

Informationen zu den URLs und Ports, die dazu erforderlich sind, dass Azure Migrate und Azure kommunizieren können, finden Sie unter [URLs für Konnektivität](https://docs.microsoft.com/azure/migrate/concepts-collector#urls-for-connectivity).

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Kann ich das VM-Setup mit der OVA-Vorlage absichern?

Zusätzliche Komponenten (z.B. Antivirensoftware) können in der OVA-Vorlage hinzugefügt werden, solange die für die Funktion der Azure Migrate-Appliance erforderlichen Kommunikations- und Firewall-Regeln unverändert bleiben.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Welche Antivirus-Ausschlüsse (AV) werden zum Härten der Azure Migrate-Appliance empfohlen?

Sie müssen die folgenden Ordner in der Appliance von Antivirusscans ausschließen:

- Ordner mit den Binärdateien für den Azure Migrate-Dienst. Schließen Sie alle Unterordner aus.
  %ProgramFiles%\ProfilerService  
- Azure Migrate-Webanwendung. Schließen Sie alle Unterordner aus.
  %SystemDrive%\inetpub\wwwroot
- Lokaler Cache für Datenbank- und Protokolldateien. Der Azure Migrate-Dienst benötigt Lese-/Schreibzugriff auf diesen Ordner.
  %SystemDrive%\Profiler

## <a name="discovery"></a>Ermittlung

### <a name="what-data-is-collected-by-azure-migrate"></a>Welche Daten werden von Azure Migrate gesammelt?

Azure Migrate unterstützt zwei Arten von der Ermittlung – appliancebasierte Ermittlung und Agent-basierte Ermittlung.
Die appliancebasierte Ermittlung sammelt Metadaten über die lokalen VMs. Die vollständige Liste der von der Appliance gesammelten Metadaten wird unten aufgeführt:

**Konfigurationsdaten des virtuellen Computers**
- Anzeigename des virtuellen Computers (in vCenter)
- Inventurpfad des virtuellen Computers (Host/Cluster/Ordner in vCenter)
- IP-Adresse
- MAC-Adresse
- Betriebssystem
- Anzahl der Kerne, Datenträger, NICs
- Arbeitsspeichergröße, Datenträgergrößen

**Leistungsdaten des virtuellen Computers**
- CPU-Auslastung
- Speicherauslastung
- Für jeden an den virtuellen Computer angefügten Datenträger:
  - Datenträgerlesedurchsatz
  - Datenträger-Schreibdurchsatz
  - Datenträgerlesevorgänge pro Sekunde
  - Datenträger-Schreibvorgänge pro Sekunde
- Für jeden Netzwerkadapter, der an den virtuellen Computer angefügt ist:
  - Eingehender Netzwerkverkehr
  - Ausgehender Netzwerkverkehr

Die Agent-basierte Ermittlung ist eine Option, die zusätzlicher zur appliancebasierten Ermittlung verfügbar ist, und Kunden beim [Visualisieren von Abhängigkeiten](how-to-create-group-machine-dependencies.md) der lokalen virtuellen Computer unterstützt. Die Dependency-Agents sammeln Details wie FQDN, BS, IP-Adresse, MAC-Adresse, Prozesse, die innerhalb des virtuellen Computers ausgeführt werden, und die eingehenden/ausgehenden TCP-Verbindungen vom virtuellen Computer. Die Agent-basierte Ermittlung ist optional, und Sie müssen die Agents nicht installieren, wenn Sie die Abhängigkeiten der VMs nicht visualisieren möchten.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Würden Leistungseinbußen in der analysierten ESXi-Hostumgebung auftreten?

Mit kontinuierlicher Profilerstellung für Leistungsdaten muss die Statistikebene von vCenter Server nicht geändert werden, um eine leistungsbasierte Bewertung auszuführen. Die Collectorappliance erstellt ein Profil der lokalen Computer, um die Leistungsdaten der virtuellen Computer zu messen. Dies hätte auf den ESXi-Hosts, aber auch auf vCenter Server nahezu keine Auswirkungen auf die Leistung.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Wo und wie lange werden die gesammelten Daten gespeichert?

Die von der Collectorappliance gesammelten Daten werden im Azure-Speicherort gespeichert, den Sie beim Erstellen des Migrationsprojekts angeben. Die Daten werden sicher in einem Microsoft-Abonnement gespeichert und gelöscht, wenn der Benutzer das Azure Migrate-Projekt löscht.

Wenn Sie für die Abhängigkeitsvisualisierung Agents auf den VMs installieren, werden die von den Dependency-Agents gesammelten Daten in den USA in einem Log Analytics-Arbeitsbereich gespeichert, der im Abonnement des Benutzers erstellt wurde. Diese Daten werden gelöscht, wenn Sie den Log Analytics-Arbeitsbereich in Ihrem Abonnement löschen. [Weitere Informationen](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization)

### <a name="what-is-the-volume-of-data-which-is-uploaded-by-azure-migrate-in-the-case-of-continuous-profiling"></a>Wie hoch ist das Datenvolumen, das von Azure Migrate bei kontinuierlicher Profilerstellung hochgeladen wird?

Das an Azure Migrate gesendete Datenvolumen hängt von verschiedenen Parametern ab. Hier eine Zahl zur Veranschaulichung: Ein Projekt mit zehn Computern (jeweils mit einem Datenträger und einer NIC) würde ungefähr 50 MB pro Tag senden. Dies ist ein ungefährer Wert, der sich auf Grundlage der Anzahl von Datenpunkten für die NICS und Datenträger ändert (die gesendeten Daten verhalten sich nicht linear, wenn die Anzahl der Computer, NICs oder Datenträger zunimmt).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Werden die Daten im Ruhezustand und bei der Übertragung verschlüsselt?

Ja, die gesammelten Daten werden sowohl im Ruhezustand als auch bei der Übertragung verschlüsselt. Die von der Appliance gesammelten Metadaten werden sicher über das Internet über https an den Azure Migrate-Dienst gesendet. Die gesammelte Metadaten werden in [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) und [Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) in einem Microsoft-Abonnement gespeichert und im Ruhezustand verschlüsselt.

Die von den Dependency-Agents gesammelten Daten werden auch bei der Übertragung verschlüsselt (sicherer https-Kanal) und im Log Analytics-Arbeitsbereich im Abonnement des Benutzers gespeichert. Außerdem erfolgt eine Verschlüsselung im Ruhezustand.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Wie kommuniziert der Collector mit dem vCenter Server und dem Azure Migrate-Dienst?

Die Collectorappliance stellt mithilfe der vom Benutzer in der Appliance angegebenen Anmeldeinformationen eine Verbindung mit dem vCenter Server (Port 443) her. Anschließend erfolgt eine Abfrage von vCenter Server mithilfe der VMware PowerCLI, um Metadaten über die von vCenter Server verwalteten Metadaten zu sammeln. Es werden sowohl Konfigurationsdaten zu virtuellen Computern (Kerne, Arbeitsspeicher, Datenträger, NIC usw.) als auch der Leistungsverlauf für jeden virtuellen Computer für den letzten Monat von vCenter Server gesammelt. Die gesammelte Metadaten werden dann zur Bewertung an den Azure Migrate-Dienst (über das Internet über https) gesendet. [Weitere Informationen](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Kann ich eine Collectorappliance mit mehreren vCenter-Servern verbinden?

Ja, eine einzelne Collectorappliance kann für die Ermittlung mehrerer vCenter-Server verwendet werden, jedoch nicht gleichzeitig. Sie müssen die Ermittlungen nacheinander durchführen.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Ist die von Site Recovery verwendete OVA-Vorlage in die von Azure Migrate verwendete OVA-Datei integriert?

Derzeit gibt es keine Integration. Die .OVA-Vorlage in Site Recovery dient zum Einrichten eines Site Recovery-Konfigurationsservers für die Replikation von VMware-VMs und physischen Servern. Die von Azure Migrate verwendete .OVA-Datei wird zur Ermittlung von VMware-VMs, die von einem vCenter-Server verwaltet werden, im Rahmen der Migrationsbewertung verwendet.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Ich habe meine Computergröße geändert. Kann ich die Bewertung erneut ausführen?

Wenn Sie die Einstellungen auf einer VM ändern, auf die Sie zugreifen möchten, lösen Sie erneut die Ermittlung mit der Collectorappliance aus. Verwenden Sie dazu in der Appliance die Option **Sammlung erneut starten**. Nachdem die Sammlung abgeschlossen ist, wählen Sie im Portal die Option **Neu berechnen** für die Bewertung aus, um die aktualisierten Bewertungsergebnisse zu erhalten.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Wie kann ich eine Umgebung mit mehreren Mandanten in Azure Migrate ermitteln?

Wenn Sie bei einer von mehreren Mandanten gemeinsam genutzten Umgebung keine VMs eines Mandanten im Abonnement eines anderen ermitteln möchten, können Sie das Feld „Bereich“ in der Collectorappliance nutzen, um den Bereich der Ermittlung festzulegen. Sollten Mandanten Hosts gemeinsam nutzen, erstellen Sie Anmeldeinformationen, die nur auf diejenigen VMs einen schreibgeschützten Zugriff ermöglichen, die dem bestimmten Mandanten gehören. Verwenden Sie dann diese Anmeldeinformationen in der Collectorappliance und geben Sie den Bereich als Host für die Ermittlung an. Alternativ können Sie auch Ordner in der vCenter Server-Instanz erstellen – z.B. Ordner1 für Mandant1 und Ordner2 für Mandant2 – und unter dem gemeinsam genutzten Host die VMs für Mandant1 in Ordner1 und für Mandant2 in Ordner2 verschieben. Legen Sie anschließend im Collector den Bereich für die Ermittlungen fest, indem Sie den entsprechenden Ordner angeben.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Wie viele virtuelle Computer können in einem einzelnen Migrationsprojekt ermittelt werden?

In einem einzelnen Migrationsprojekt können 1500 virtuelle Computer ermittelt werden. Wenn Ihre lokale Umgebung mehr Computer enthält, finden Sie hier [weitere Informationen](how-to-scale-assessment.md) zur Ermittlung einer großen Umgebung in Azure Migrate.


## <a name="assessment"></a>Bewertung

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Unterstützt Azure Migrate EA-basierte (Enterprise Agreement) Kostenschätzungen?

Azure Migrate unterstützt derzeit keine Kostenschätzung für das [Enterprise Agreement-Angebot](https://azure.microsoft.com/offers/enterprise-agreement-support/). Die Problemumgehung besteht darin, als Angebot „Nutzungsbasierte Zahlung“ anzugeben und den (für das Abonnement geltenden) Rabattprozentsatz manuell in das Feld „Rabatt“ der Bewertungseigenschaften einzugeben.

  ![Discount](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Was ist der Unterschied zwischen den Größenanpassungen „Wie lokal“ und „Leistungsbasiert“?

Wenn Sie als Größenkriterium „Wie lokal“ angeben, berücksichtigt Azure Migrate die Leistungsdaten der virtuellen Computer nicht und legt die Größe der virtuellen Computer basierend auf der lokalen Konfiguration fest. Wenn das Größenkriterium „Leistungsbasiert“ lautet, erfolgt die Größenanpassung basierend auf den Nutzungsdaten. Beispiel: Eine lokale VM mit 4 Kernen und 8 GB Arbeitsspeicher mit einer CPU- und Arbeitsspeicherauslastung von jeweils 50 %. Wenn das Größenkriterium „Wie lokal“ gilt, wird eine Azure-VM-SKU mit 4 Kernen und 8 GB Arbeitsspeicher empfohlen. Falls das Größenkriterium „Leistungsbasiert“ lautet, wird eine VM-SKU mit 2 Kernen und 4 GB Arbeitsspeicher empfohlen, da für die Größenempfehlung der Auslastungsprozentsatz berücksichtigt wird. Entsprechend hängt bei Datenträgern die Größenanpassung von zwei Bewertungseigenschaften ab: dem Größenkriterium und dem Speichertyp. Wenn das Größenkriterium „Leistungsbasiert“ ist und der Speichertyp „Automatisch“ lautet, werden die IOPS- und Durchsatzwerte des Datenträgers berücksichtigt, um den Typ des Zieldatenträgers zu ermitteln (Standard oder Premium). Wenn das Größenkriterium „Leistungsbasiert“ ist und der Speichertyp „Premium“ lautet, wird ein Premium-Datenträger empfohlen. Die Premium-Datenträger-SKU in Azure wird basierend auf der Größe des lokalen Datenträgers ausgewählt. Die gleiche Logik wird verwendet, um die Größenanpassung für Datenträger durchzuführen, wenn das Größenkriterium „Wie lokal“ verwendet wird und der Speichertyp „Standard“ oder „Premium“ lautet.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Welche Auswirkungen haben der Leistungsverlauf und der Quantilwert der Nutzung auf die Größenempfehlungen?

Diese Eigenschaften gelten nur für die leistungsbasierte Größenanpassung. Azure Migrate sammelt den Leistungsverlauf von lokalen Computern und nutzt diese Daten, um die VM-Größe und den Datenträgertyp in Azure zu empfehlen. Die Collectorappliance erstellt für die lokale Umgebung kontinuierlich Profildaten, um alle 20 Sekunden Echtzeit-Verwendungsdaten erfassen zu können. Die Appliance führt für die 20-Sekunden-Stichproben einen Rollup aus und erstellt für jeweils 15 Minuten einen Datenpunkt. Zum Erstellen jedes Datenpunkts wählt die Appliance den Spitzenwert aus allen 20-Sekunden-Stichproben aus und sendet diesen an Azure. Beim Erstellen einer Bewertung in Azure berechnet Azure Migrate basierend auf der Leistungsdauer und dem Quantilwert des Leistungsverlaufs den geltenden Auslastungswert und verwendet ihn für die Größenanpassung. Wenn Sie die Leistungsdauer beispielsweise auf einen Tag und den Quantilwert auf 95 festgelegt haben, verwendet Azure Migrate die vom Collector gesendeten 15-Minuten-Stichprobenpunkte für den gesamten letzten Tag, sortiert sie in aufsteigender Reihenfolge und wählt den Wert des 95. Quantils als gültige Auslastung aus. Mit dem Wert des 95. Quantils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können auftreten, wenn Sie das 99. Quantil wählen. Falls Sie die Spitzenauslastung für den Zeitraum wählen möchten und keine Ausreißer verpassen möchten, sollten Sie das 99. Quantil wählen.

## <a name="dependency-visualization"></a>Visualisierung von Abhängigkeiten

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

### <a name="what-is-dependency-visualization"></a>Was ist die Visualisierung von Abhängigkeiten?

Durch die Visualisierung von Abhängigkeiten können Sie mit größerem Vertrauen Gruppen von virtuellen Computern für die Migration bewerten, indem Sie vor dem Ausführen einer Bewertung die Computerabhängigkeiten überprüfen. Die Visualisierung der Abhängigkeiten trägt dazu bei, dass nichts übersehen wird. Dadurch vermeiden Sie unerwartete Ausfälle bei der Migration zu Azure. Azure Migrate nutzt für die Visualisierung Abhängigkeiten die Dienstzuordnungslösung in Azure Monitor-Protokollen.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Ist die Nutzung des Features zur Visualisierung von Abhängigkeiten kostenpflichtig?

Nein. Weitere Informationen zu den Preisen von Azure Migrate finden Sie [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Muss ich für die Visualisierung von Abhängigkeiten Installationen durchführen?

Sie müssen für die Verwendung der Visualisierung von Abhängigkeiten Agents auf alle lokalen Computer, die Sie bewerten möchten, herunterladen und dort installieren.

- Der [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) muss auf jedem Computer installiert werden.
- Der [Dependency-Agent](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) muss auf jedem Computer installiert werden.
- Falls Sie über Computer ohne Internetverbindung verfügen, ist es außerdem erforderlich, auf diesen das Log Analytics-Gateway herunterzuladen und zu installieren.

Sie benötigen diese Agents auf Computern nur, wenn Sie die Abhängigkeitsvisualisierung verwenden.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Kann ich für die Visualisierung von Abhängigkeiten einen vorhandenen Arbeitsbereich verwenden?

Ja, Azure Migrate ermöglicht Ihnen jetzt, einen vorhandenen Arbeitsbereich an das Migrationsprojekt anzufügen und für die Abhängigkeitsvisualisierung zu nutzen. [Weitere Informationen](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>Kann ich den Bericht über die Abhängigkeitsvisualisierung exportieren?

Nein. Der Bericht über die Abhängigkeitsvisualisierung kann nicht exportiert werden. Da Azure Migrate die Dienstzuordnung zur Abhängigkeitsvisualisierung verwendet, können Sie die Abhängigkeiten jedoch mithilfe der [Dienstzuordnungs-REST-APIs](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) in einem JSON-Format abrufen.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Wie kann ich die Installation von Microsoft Monitoring Agent (MMA) und des Dependency-Agents automatisieren?

[Hier](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) ist ein Skript, das Sie für die Installation des Dependency-Agents verwenden können. [Hier](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) finden Sie Anweisungen zum Installieren von MMA mithilfe der Befehlszeile oder automatisierter Methoden. Für MMA können Sie auch ein Skript nutzen, das auf [TechNet](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) verfügbar ist.

Zusätzlich zu Skripts können Sie auch Bereitstellungstools wie System Center Configuration Manager (SCCM), [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) usw. zum Bereitstellen der Agents verwenden.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Welche Betriebssysteme werden von MMA unterstützt?

Die Liste der von MMA unterstützten Windows-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Die Liste der von MMA unterstützten Linux-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Welche Betriebssysteme werden vom Dependency-Agent unterstützt?

Die Liste der vom Dependency-Agent unterstützten Windows-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Die Liste der vom Dependency-Agent unterstützten Linux-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Kann ich Abhängigkeiten in Azure Migrate für länger als eine Stunde visualisieren?
Nein. Azure Migrate ermöglicht Ihnen das Visualisieren von Abhängigkeiten für einen Zeitraum von bis zu einer Stunde. Mit Azure Migrate können Sie im Verlauf um bis zu einen Monat zu einem bestimmten Datum zurückgehen. Die Abhängigkeiten können Sie jedoch nur für einen Zeitraum von maximal einer Stunde visualisieren. So können Sie beispielsweise mithilfe der Zeitraumfunktionalität im Abhängigkeitsdiagramm Abhängigkeiten für gestern, jedoch nur für ein Zeitfenster von einer Stunde anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) über einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Wird die Abhängigkeitsvisualisierung für Gruppen mit mehr als zehn VMs unterstützt?
Sie können [Abhängigkeiten für Gruppen visualisieren](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies), die bis zu zehn VMs umfassen. Falls eine Gruppe aus mehr als zehn VMs besteht, sollten Sie die Gruppe in kleinere Gruppen aufteilen und die Abhängigkeiten anschließend visualisieren.


## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Übersicht zu Azure Migrate](migrate-overview.md).
- Erfahren Sie, wie Sie eine VMware-Umgebung [ermitteln und bewerten](tutorial-assessment-vmware.md) können.
