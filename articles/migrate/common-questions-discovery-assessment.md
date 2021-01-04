---
title: Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate
description: Erhalten Sie Antworten auf häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 4531d68c2fbd0698c33d70a75bb82ac9c7f52f49
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752242"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zur Ermittlung, Bewertung und Abhängigkeitsanalyse in Azure Migrate. Wenn Sie weitere Fragen haben, sehen Sie sich die folgenden Ressourcen an:

- [Allgemeine Fragen](resources-faq.md) zu Azure Migrate
- Fragen zur [Azure Migrate-Appliance](common-questions-appliance.md)
- Fragen zur [Servermigration](common-questions-server-migration.md)
- Antworten auf Fragen im [Azure Migrate-Forum](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Welche geografischen Regionen werden für die Ermittlung und Bewertung mit Azure Migrate unterstützt?

In der Unterstützungsmatrix für Azure Migrate finden Sie die unterstützten geografischen Regionen für die [öffentliche Cloud](migrate-support-matrix.md#supported-geographies-public-cloud) und für [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Wie viele VMs können mit einer Appliance ermittelt werden?

Sie können bis zu 10.000 VMware-VMs und bis zu 5.000 Hyper-V-VMs sowie bis zu 1.000 physische Server mithilfe einer einzigen Appliance ermitteln. Wenn Sie mehr Computer haben, informieren Sie sich über die Skalierung der [Hyper-V-](scale-hyper-v-assessment.md), [VMware-](scale-vmware-assessment.md) oder [physischen](scale-physical-assessment.md) Bewertung.

## <a name="how-do-i-choose-the-assessment-type"></a>Wie wähle ich den Bewertungstyp aus?

- Verwenden Sie **Bewertungen vom Typ „Virtueller Azure-Computer“** , wenn Sie Ihre lokalen [virtuellen VMware-Computer](how-to-set-up-appliance-vmware.md), [virtuellen Hyper-V-Computer](how-to-set-up-appliance-hyper-v.md) und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu virtuellen Azure-Computern bewerten möchten. [Weitere Informationen](concepts-assessment-calculation.md)

- Verwenden Sie Bewertungen vom Typ **Azure VMware Solution (AVS)** , wenn Sie Ihre lokalen [virtuellen VMware-Computer](how-to-set-up-appliance-vmware.md) für die Migration zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md) mit diesem Bewertungstyp bewerten möchten. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

- Eine allgemeine Gruppe mit VMware-Computern kann nur zum Ausführen beider Bewertungstypen verwendet werden. Wenn Sie erstmals AVS-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Gruppe mit VMware-Computern zu erstellen.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Warum fehlen in meinem Bewertungsbericht die Leistungsdaten für einige oder alle VMs?

Für eine Bewertung vom Typ „Leistungsbasiert“ enthält der Bewertungsbericht „PercentageOfCoresUtilizedMissing“ oder „PercentageOfMemoryUtilizedMissing“, wenn die Azure Migrate-Appliance keine Leistungsdaten für die lokalen virtuellen Computer sammeln kann. Überprüfen Sie Folgendes:

- Ob die VMs im Zeitraum, in dem Sie die Bewertung erstellen, eingeschaltet sind
- Falls nur Arbeitsspeicher-Leistungsindikatoren fehlen und Sie versuchen, virtuelle Hyper-V-Computer zu bewerten, überprüfen Sie, ob auf diesen virtuellen Computern dynamischer Arbeitsspeicher aktiviert ist. Die Azure Migrate-Appliance kann derzeit aufgrund eines bekannten Problems keine Arbeitsspeicherauslastung für virtuelle Computer dieser Art erfassen.
- Falls alle Leistungsindikatoren fehlen, sollten Sie sicherstellen, dass ausgehende Verbindungen über Port 443 (HTTPS) zulässig sind.

Hinweis: Falls einer der Leistungsindikatoren fehlt, greift die Azure Migrate-Serverbewertung auf die lokalen zugeordneten Kerne bzw. auf den lokalen zugeordneten Arbeitsspeicher zurück und empfiehlt eine entsprechende VM-Größe.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Weshalb weist meine Bewertung eine niedrige Zuverlässigkeitsstufe auf?

Die Zuverlässigkeitsstufe wird für Bewertungen vom Typ „Leistungsbasiert“ anhand des Prozentsatzes von [verfügbaren Datenpunkten](./concepts-assessment-calculation.md#ratings) berechnet, die zum Berechnen der Bewertung benötigt werden. Im Folgenden werden mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung aufgeführt:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil Ihrer Umgebung erstellt. Wenn Sie beispielsweise eine Bewertung mit einer auf eine Woche festgelegten Leistungsdauer erstellen, müssen Sie nach dem Start der Ermittlung mindestens eine Woche warten, damit alle Datenpunkte erfasst werden können. Falls Sie nicht so lange warten können, sollten Sie die Leistungsdauer in einen kürzeren Zeitraum ändern und die Bewertung neu berechnen (Option „Neu berechnen“).
 
- Die Serverbewertung kann die Leistungsdaten für einige oder alle VMs im Bewertungszeitraum nicht erfassen. Überprüfen Sie, ob die VMs während der Dauer der Bewertung eingeschaltet und ausgehende Verbindungen über Port 443 zulässig waren. Wenn bei Verwendung von Hyper-V-VMs der dynamische Arbeitsspeicher aktiviert ist, fehlen Arbeitsspeicher-Leistungsindikatoren. Dies führt zu einer niedrigen Zuverlässigkeitsstufe. Führen Sie die erneute Berechnung der Bewertung mit der Option „Neu berechnen“ durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln. 

- Nach dem Start der Ermittlung in der Serverbewertung wurden wenige VMs erstellt. Ein Beispiel: Angenommen, Sie erstellen eine Bewertung für den Leistungsverlauf des letzten Monats, und in der Umgebung wurden letzte Woche einige virtuelle Computer erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen VMs zur Verfügung und die Zuverlässigkeitsstufe wäre gering.

[Weitere Informationen](./concepts-assessment-calculation.md#confidence-ratings-performance-based) zur Zuverlässigkeitsstufe.

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Es werden bei der Erstellung einer Bewertung vom Typ „Azure VMware Solution (AVS)“ einige Gruppen nicht angezeigt.

- AVS-Bewertungen können nur für Gruppen durchgeführt werden, die ausschließlich VMware-Computer enthalten. Entfernen Sie alle VMware-fremden Computer aus der Gruppe, wenn Sie eine AVS-Bewertung durchführen möchten.
- Wenn Sie erstmals AVS-Bewertungen in Azure Migrate ausführen, empfiehlt es sich, eine neue Gruppe mit VMware-Computern zu erstellen.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Einige VM-Typen werden in Azure Government nicht angezeigt

Die für die Bewertung und Migration unterstützten VM-Typen hängen von der Verfügbarkeit am Azure Government-Standort ab. Sie können die VM-Typen in Azure Government [hier anzeigen und vergleichen](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines).

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Die Größe meiner VM hat sich geändert. Kann ich die Bewertung erneut ausführen?

Die Azure Migrate-Appliance sammelt kontinuierlich Informationen zur lokalen Umgebung.  Eine Bewertung ist eine Point-in-Time-Momentaufnahme von lokalen VMs. Wenn Sie die Einstellungen für eine VM, die Sie bewerten möchten, ändern, verwenden Sie die Option „Neu berechnen“, um die Bewertung mit den neuesten Änderungen zu aktualisieren.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Wie ermittle ich VMs in einer mehrinstanzenfähigen Umgebung?

- **VMware**: Wenn eine Umgebung von Mandanten gemeinsam genutzt wird und Sie die VMs eines Mandanten nicht im Abonnement eines anderen Mandanten ermitteln möchten, erstellen Sie VMware vCenter Server-Anmeldeinformationen mit Zugriff nur auf die VMs, die Sie ermitteln möchten. Verwenden Sie diese Anmeldeinformationen dann, wenn Sie die Ermittlung in der Azure Migrate-Appliance starten.
- **Hyper-V:** Bei der Ermittlung werden Anmeldeinformationen für den Hyper-V-Host verwendet. Wenn virtuelle Computer denselben Hyper-V-Host nutzen, gibt es derzeit keine Möglichkeit, die Ermittlung zu trennen.  

## <a name="do-i-need-vcenter-server"></a>Benötige ich vCenter Server?

Ja, Azure Migrate erfordert in einer VMware-Umgebung vCenter Server für die Ermittlung. Die Ermittlung von ESXi-Hosts, die nicht von vCenter Server verwaltet werden, wird von Azure Migrate nicht unterstützt.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Welche Größenoptionen gibt es bei einer Azure VM-Bewertung?

Bei der lokal orientierten Größenanpassung berücksichtigt Azure Migrate keine VM-Leistungsdaten für die Bewertung. Azure Migrate bewertet VM-Größen auf Grundlage der lokalen Konfiguration. Bei der leistungsbasierten Größenanpassung basiert diese auf Auslastungsdaten.

Wenn z. B. eine lokale VM über vier Kerne und 8 GB Arbeitsspeicher bei einer CPU- und Arbeitsspeicherauslastung von jeweils 50 % verfügt:
- Bei der lokal orientierten Größenanpassung wird eine Azure-VM-SKU mit vier Kernen und 8 GB Arbeitsspeicher empfohlen.
- Bei der leistungsbasierten Größenanpassung wird jedoch eine VM-SKU mit zwei Kernen und 4 GB Arbeitsspeicher empfohlen, da auch der Auslastungsprozentsatz berücksichtigt wird.

Entsprechend hängt die Größenanpassung der Datenträger von dem Größenkriterium und dem Speichertyp ab:
- Wenn das leistungsbasierte Größenkriterium und der Speichertyp „Automatisch“ verwendet werden, berücksichtigt Azure Migrate die IOPS- und Durchsatzwerte des Datenträgers, um den Typ des Zieldatenträgers zu ermitteln (Standard oder Premium).
- Wenn das leistungsbasierte Größenkriterium und der Speichertyp „Premium“ verwendet werden, empfiehlt Azure Migrate eine Premium-Datenträger-SKU basierend auf der Größe des lokalen Datenträgers. Die gleiche Logik wird verwendet, um die Größenanpassung für Datenträger durchzuführen, wenn die lokal orientierte Größenanpassung verwendet wird und der Speichertyp „Standard“ oder „Premium“ lautet.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Wirken sich Leistungsverlauf und Auslastung auf die Größenanpassung in einer Azure VM-Bewertung aus?

Ja, der Leistungsverlauf und die Auslastung wirken sich auf die Größenanpassung in einer Azure VM-Bewertung aus.

### <a name="performance-history"></a>Leistungsverlauf

Nur bei der leistungsbasierten Größenanpassung sammelt Azure Migrate den Leistungsverlauf von lokalen Computern und nutzt diese Daten dann, um die VM-Größe und den Datenträgertyp in Azure zu empfehlen:

1. Die Appliance erstellt für die lokale Umgebung kontinuierlich Profildaten, um alle 20 Sekunden in Echtzeit Auslastungsdaten erfassen zu können.
2. Die Appliance führt für die gesammelten 20-Sekunden-Stichproben einen Rollup aus und erstellt daraus alle 15 Minuten einen einzelnen Datenpunkt.
3. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen 20-Sekunden-Stichproben aus.
4. Die Appliance sendet den Datenpunkt an Azure.

### <a name="utilization"></a>Auslastung

Beim Erstellen einer Bewertung in Azure berechnet Azure Migrate in Abhängigkeit von der Leistungsdauer und dem festgelegten Perzentilwert des Leistungsverlaufs den geltenden Auslastungswert und verwendet ihn dann für die Größenanpassung.

Wenn Sie die Leistungsdauer beispielsweise auf einen Tag und den Perzentilwert auf das 95. Perzentil festgelegt haben, sortiert Azure Migrate die vom Collector gesendeten 15-Minuten-Stichprobenpunkte für den gesamten letzten Tag in aufsteigender Reihenfolge. Dann wird der Wert des 95. Perzentils als gültige Auslastung ausgewählt.

Die Verwendung des Werts des 95. Perzentils stellt sicher, dass Ausreißer ignoriert werden. Ausreißer können eingeschlossen werden, wenn Azure Migrate das 99. Perzentil verwendet. Wenn Sie die Spitzenauslastung für den Zeitraum erfassen möchten, ohne Ausreißer auszulassen, legen Sie Azure Migrate auf die Verwendung des 99. Perzentils fest.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Worin unterscheiden sich importbasierte Bewertungen von Bewertungen mit der Ermittlungsquelle als Appliance?

Bei importbasierten Azure VM-Bewertungen handelt es sich um Bewertungen, die mit Computern erstellt werden, die mithilfe einer CSV-Datei in Azure Migrate importiert werden. Für den Import sind nur vier Felder erforderlich: Servername, Kerne, Arbeitsspeicher und Betriebssystem. Die folgenden Punkte sind zu berücksichtigen: 
 - Das Bereitschaftskriterium ist bei importbasierten Bewertungen für den Starttypparameter weniger streng. Ist der Starttyp nicht angegeben, wird davon ausgegangen, dass der Computer den Starttyp „BIOS“ aufweist und nicht als **Bedingt bereit** gekennzeichnet ist. Bei Bewertungen mit der Ermittlungsquelle als Appliance wird die Bereitschaft als **Bedingt bereit** gekennzeichnet, wenn der Starttyp fehlt. Dieser Unterschied bei der Bereitschaftsberechnung ergibt sich dadurch, dass Benutzer in den frühen Phasen der Migrationsplanung möglicherweise nicht über alle Informationen zu den Computern verfügen, wenn importbasierte Bewertungen durchgeführt werden. 
 - Bei leistungsbasierten Importbewertungen wird der vom Benutzer bereitgestellte Auslastungswert für die Berechnungen zur Größenanpassung verwendet. Da der Auslastungswert vom Benutzer bereitgestellt wird, werden die Optionen für **Leistungsverlauf** und **Perzentilwert der Nutzung** in den Bewertungseigenschaften deaktiviert. Bei Bewertungen mit der Ermittlungsquelle als Appliance wird der ausgewählte Perzentilwert den von der Appliance gesammelten Leistungsdaten entnommen.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Warum wird das vorgeschlagene Migrationstool in der importbasierten AVS-Bewertung als unbekannt markiert?

Für Computer, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool in einer AVS-Bewertung unbekannt. Für VMware-Computer empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension). [Weitere Informationen](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Was ist die Visualisierung von Abhängigkeiten?

Anhand der Visualisierung von Abhängigkeiten können Sie Gruppen von VMs für eine Migration mit größerer Sicherheit bewerten. Die Visualisierung von Abhängigkeiten überprüft die Abhängigkeiten zwischen Computern, bevor Sie eine Bewertung ausführen. Sie stellt sicher, dass nichts übersehen wird, und hilft, unerwartete Ausfälle bei der Migration zu Azure zu vermeiden. Azure Migrate verwendet die Dienstzuordnungslösung in Azure Monitor, um Abhängigkeitsvisualisierung zu ermöglichen. [Weitere Informationen](concepts-dependency-visualization.md)

> [!NOTE]
> Die Agent-basierte Abhängigkeitsanalyse ist in Azure Government nicht verfügbar. Sie können die Abhängigkeitsanalyse ohne Agent verwenden.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Was ist der Unterschied zwischen „Agent-basiert“ und „ohne Agent“?

Die Unterschiede zwischen der Visualisierung ohne Agent und der Agent-basierten Visualisierung sind in der Tabelle zusammengefasst.

**Anforderung** | **Ohne Agent** | **Agent-basiert**
--- | --- | ---
Support | Diese Option befindet sich derzeit in der Vorschauphase und ist nur für VMware-VMs verfügbar. [Lesen](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) Sie den Artikel zu den unterstützten Betriebssystemen. | In der allgemeinen Verfügbarkeit (General Availability, GA).
Agent | Sie müssen keine Agents auf Computern installieren, die Sie überprüfen möchten. | Auf jedem lokalen Computer, den Sie analysieren möchten, müssen Agents installiert sein: Der [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md) und der [Dependency-Agent](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Voraussetzungen | [Lesen](concepts-dependency-visualization.md#agentless-analysis) Sie den Abschnitt zu Voraussetzungen und Anforderungen für die Bereitstellung. | [Lesen](concepts-dependency-visualization.md#agent-based-analysis) Sie den Abschnitt zu Voraussetzungen und Anforderungen für die Bereitstellung.
Log Analytics | Nicht erforderlich. | Azure Migrate verwendet für die Abhängigkeitsvisualisierung die [Dienstzuordnung](../azure-monitor/insights/service-map.md) in [Azure Monitor-Protokolle](../azure-monitor/log-query/log-query-overview.md). [Weitere Informationen](concepts-dependency-visualization.md#agent-based-analysis)
Funktionsweise | Erfasst TCP-Verbindungsdaten auf Computern, die für die Visualisierung von Abhängigkeiten aktiviert sind. Nach der Ermittlung werden Daten in Abständen von fünf Minuten gesammelt. | Auf einem Computer installierte Agents für die Dienstzuordnung sammeln Daten über TCP-Prozesse und ein-/ausgehende Verbindungen für die einzelnen Prozesse.
Daten | Servername des Quellcomputers, Prozess, Anwendungsname.<br/><br/> Servername des Zielcomputers, Prozess, Anwendungsname und Port. | Servername des Quellcomputers, Prozess, Anwendungsname.<br/><br/> Servername des Zielcomputers, Prozess, Anwendungsname und Port.<br/><br/> Anzahl der Verbindungen, Wartezeiten und Datenübertragungsinformationen werden gesammelt und stehen für Log Analytics-Abfragen zur Verfügung. 
Visualisierung | Das Abhängigkeitsdiagramm eines einzelnen Servers kann über eine Dauer von einer Stunde bis hin zu 30 Tagen angezeigt werden. | Abhängigkeitsdiagramm eines einzelnen Servers.<br/><br/> Das Diagramm kann nur über eine Stunde angezeigt werden.<br/><br/> Abhängigkeitsdiagramm für eine Gruppe von Servern.<br/><br/> Hinzufügen und Entfernen von Servern in einer Gruppe aus der Diagrammansicht.
Datenexport | Die Daten der letzten 30 Tage können im CSV-Format heruntergeladen werden. | Daten können mit Log Analytics abgefragt werden.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Muss ich die Appliance für die Abhängigkeitsanalyse ohne Agent bereitstellen?

Ja, die [Azure Migrate-Appliance](migrate-appliance.md) muss bereitgestellt werden.

## <a name="do-i-pay-for-dependency-visualization"></a>Ist die Visualisierung der Abhängigkeiten kostenpflichtig?

Nein. Weitere Informationen zu den Preisen von Azure Migrate finden Sie [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Was muss ich für die Agent-basierte Visualisierung der Abhängigkeiten installieren?

Wenn Sie die Agent-basierte Visualisierung von Abhängigkeiten verwenden möchten, müssen Sie Agents auf alle lokalen Computer, die Sie bewerten möchten, herunterladen und dort installieren:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [Dependency-Agent](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Falls Sie über Computer ohne Internetverbindung verfügen, ist es erforderlich, auf diese das Log Analytics-Gateway herunterzuladen und dort zu installieren.

Sie benötigen diese Agents nur, wenn Sie die Agent-basierte Visualisierung von Abhängigkeiten verwenden.

## <a name="can-i-use-an-existing-workspace"></a>Kann ich einen bestehenden Arbeitsbereich verwenden?

Ja, für die Agent-basierte Visualisierung von Abhängigkeiten können Sie einen vorhandenen Arbeitsbereich an das Migrationsprojekt anfügen und für die Visualisierung von Abhängigkeiten nutzen. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Kann ich den Bericht über die Abhängigkeitsvisualisierung exportieren?

Nein, der Bericht für die Visualisierung von Abhängigkeiten in der Agent-basierten Visualisierung kann nicht exportiert werden. Azure Migrate verwendet jedoch die Dienstzuordnung, sodass Sie die Abhängigkeiten mithilfe der [Service Map-REST-API](/rest/api/servicemap/machines/listconnections) im JSON-Format abrufen können.

## <a name="can-i-automate-agent-installation"></a>Kann ich die Agent-Installation automatisieren?

Für die Agent-basierte Visualisierung von Abhängigkeiten:

- Verwenden Sie ein [Skript für die Installation des Dependency-Agents](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).
- Für MMA [verwenden Sie die Befehlszeile oder die Automatisierung](../azure-monitor/platform/log-analytics-agent.md#installation-options), oder verwenden Sie ein [Skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Zusätzlich zu Skripts können Sie Bereitstellungstools wie Microsoft Endpoint Configuration Manager und [Intigua](https://www.intigua.com/intigua-for-azure-migration) zum Bereitstellen der Agents verwenden.

## <a name="what-operating-systems-does-mma-support"></a>Welche Betriebssysteme unterstützt MMA?

- Überprüfen Sie die Liste der [von MMA unterstützten Windows-Betriebssysteme](../azure-monitor/platform/log-analytics-agent.md#installation-options).
- Überprüfen Sie die Liste der [von MMA unterstützten Linux-Betriebssysteme](../azure-monitor/platform/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Kann ich Abhängigkeiten für mehr als eine Stunde visualisieren?

Sie können für die Agent-basierte Visualisierung entsprechende Abhängigkeiten bis zu einer Stunde visualisieren. Sie können bis zu einem Monat zu einem bestimmten Datum im Verlauf zurückkehren, aber die maximale Dauer der Visualisierung beträgt eine Stunde. Sie können beispielsweise den Zeitraum im Abhängigkeitsdiagramm verwenden, um Abhängigkeiten für den gestrigen Tag anzuzeigen. Sie können die Abhängigkeiten allerdings nur für ein Zeitfenster von einer Stunde anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](./how-to-create-group-machine-dependencies.md) für einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

Zur Visualisierung ohne Agents können Sie das Abhängigkeitsdiagramm eines einzelnen Servers für eine Dauer zwischen einer Stunde und 30 Tagen anzeigen.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Kann ich für Gruppen mit mehr als zehn VMs Abhängigkeiten visualisieren?

Sie können [Abhängigkeiten für Gruppen visualisieren](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping), die bis zu zehn VMs umfassen. Falls eine Gruppe aus mehr als zehn VMs besteht, sollten Sie die Gruppe in kleinere Gruppen aufteilen und die Abhängigkeiten anschließend visualisieren.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Übersicht zu Azure Migrate](migrate-services-overview.md) an.