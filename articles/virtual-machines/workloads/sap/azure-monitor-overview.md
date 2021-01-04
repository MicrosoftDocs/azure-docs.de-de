---
title: 'Azure Monitor für SAP-Lösungen: Übersicht und Architektur | Microsoft-Dokumentation'
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Monitor für SAP-Lösungen.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: bcb912a24dfb2a5e78719cf9010fd23afe0df185
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484395"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor für SAP-Lösungen (Vorschauversion)

## <a name="overview"></a>Übersicht

Azure Monitor für SAP-Lösungen ist ein natives Azure-Überwachungsprodukt für Kunden, die ihre SAP-Landschaften in Azure betreiben. Das Produkt funktioniert sowohl mit [SAP auf virtuellen Azure-Computern](./hana-get-started.md) als auch mit [SAP in Azure (große Instanzen)](./hana-overview-architecture.md).
Mit Azure Monitor für SAP-Lösungen können Kunden Telemetriedaten aus der Azure-Infrastruktur sowie aus Datenbanken an einem zentralen Ort sammeln und Telemetriedaten zur schnelleren Problembehandlung visuell korrelieren.

Azure Monitor für SAP-Lösungen wird über den Azure Marketplace angeboten. Er bietet eine einfache, intuitive Benutzeroberfläche für das Setup und benötigt nur einige wenige Klicks, um die Ressource für Azure Monitor für SAP-Lösungen (als **SAP-Monitorressource** bekannt) bereitzustellen.

Kunden können verschiedene Komponenten einer SAP-Landschaft wie Azure Virtual Machines, Hochverfügbarkeitscluster, SAP HANA-Datenbank usw. überwachen, indem sie den entsprechenden **Anbieter** für diese Komponente hinzufügen.

Unterstützte Infrastruktur:

- Virtueller Azure-Computer
- Azure (große Instanz)

Unterstützte Datenbanken:
- SAP HANA-Datenbank
- Microsoft SQL Server

Azure Monitor für SAP-Lösungen nutzt die Leistungsfähigkeit bestehender [Azure Monitor](../../../azure-monitor/overview.md)-Funktionen wie Log Analytics und [Arbeitsmappen](../../../azure-monitor/platform/workbooks-overview.md), um zusätzliche Überwachungsfunktionen anzubieten. Kunden können [benutzerdefinierte Visualisierungen](../../../azure-monitor/platform/workbooks-overview.md#getting-started) erstellen, indem sie die von Azure Monitor für SAP-Lösungen bereitgestellten Standardarbeitsmappen bearbeiten, [benutzerdefinierte Abfragen](../../../azure-monitor/log-query/log-analytics-tutorial.md) und [benutzerdefinierte Warnungen](../../../azure-monitor/learn/tutorial-response.md) erstellen, indem sie den Azure Log Analytics-Arbeitsbereich verwenden, die Vorteile der [flexiblen Beibehaltungsdauer](../../../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) nutzen und Überwachungsdaten mit ihrem Ticketsystem verbinden.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Welche Daten sammelt Azure Monitor für SAP-Lösungen?

Die Datenerfassung in Azure Monitor für SAP-Lösungen hängt von den Anbietern ab, die vom Kunden konfiguriert werden. Während der Public Preview werden die folgenden Daten gesammelt.

Hochverfügbarkeits-Pacemaker-Clustertelemetrie:
- Knoten-, Ressourcen- und SBD-Gerätestatus
- Pacemaker-Standorteinschränkungen
- Quorumstimmen und Ringzustand
- [Andere](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA-Telemetrie:
- CPU-, Arbeitsspeicher-, Datenträger- und Netzwerkauslastung
- HANA-Systemreplikation (HSR)
- HANA-Sicherung
- Status des HANA-Hosts
- Indexserver- und Namenserverrollen

Microsoft SQL Server-Telemetrie:
- CPU-, Arbeitsspeicher-, Datenträgerauslastung
- Hostname, SQL-Instanzname, SAP-System-ID
- Batchanforderungen, Kompilierungen und Seitenlebenserwartung im Zeitverlauf
- Die zehn aufwendigsten SQL-Anweisungen im Zeitverlauf
- Die 12 größten Tabelle im SAP-System
- In den SQL Server-Fehlerprotokollen aufgezeichnete Probleme
- Blockieren von Prozessen und SQL-Wartestatistiken im Zeitverlauf

## <a name="data-sharing-with-microsoft"></a>Datenfreigabe mit Microsoft

Azure Monitor für SAP-Lösungen sammelt Systemmetadaten, um unseren Kunden einen verbesserten Support für SAP in Azure zu bieten. PII/EUII wird nicht gesammelt.
Kunden können den Datenaustausch mit Microsoft zum Zeitpunkt der Erstellung der Azure Monitor für SAP-Lösungen aktivieren, indem sie *Freigabe* in der Dropdownliste auswählen.
Es wird dringend empfohlen, dass Kunden die Datenfreigabe aktivieren, da sie den Support- und Entwicklungsteams von Microsoft mehr Informationen über die Kundenumgebung liefert und unseren geschäftskritischen Kunden von SAP in Azure einen verbesserten Support bietet.

## <a name="architecture-overview"></a>Übersicht über die Architektur

Auf einer hohen Ebene erläutert das folgende Diagramm, wie Azure Monitor für SAP-Lösungen Telemetrie aus der SAP HANA Datenbank sammelt. Die Architektur ist unabhängig davon, ob SAP HANA auf Azure Virtual Machines oder Azure (große Instanzen) bereitgestellt wird.

![Azure Monitor für SAP-Lösungen: Architektur](./media/azure-monitor-sap/azure-monitor-architecture.png)

Die Hauptkomponenten der Architektur sind:
- Azure-Portal – Der Ausgangspunkt für Kunden. Kunden können innerhalb des Azure-Portals zum Marketplace navigieren und Azure Monitor für SAP-Lösungen entdecken.
- Azure Monitor für SAP-Lösungen: Ressource – Ein Anlaufpunkt für Kunden zum Anzeigen der Überwachungstelemetrie.
- Verwaltete Ressourcengruppe – Automatisch im Rahmen der Ressourcenbereitstellung der Azure Monitor für SAP-Lösungen bereitgestellt. Die innerhalb der verwalteten Ressourcengruppe bereitgestellten Ressourcen helfen bei der Erfassung der Telemetrie. Die wichtigsten bereitgestellten Ressourcen und ihr Zweck:
   - Azure Virtual Machine: Auch bekannt als *Collector-VM*. Dies ist ein virtueller Standard_B2ms-Computer. Der Hauptzweck dieses virtuellen Computers ist das Hosten der *Überwachungsnutzlast*. Die Überwachungsnutzlast bezieht sich auf die Logik der Erfassung der Telemetrie von den Quellsystemen und die Übertragung der gesammelten Daten an das Überwachungsframework. Im obigen Diagramm enthält die Überwachungsnutzlast die Logik für die Verbindung mit der SAP HANA-Datenbank über den SQL-Port.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): Diese Ressource wird bereitgestellt, um die Anmeldeinformationen der SAP HANA-Datenbank sicher zu verwahren und Informationen über [Anbieter](./azure-monitor-providers.md) zu speichern.
   - Log Analytics-Arbeitsbereich: Das Ziel, an dem sich die Telemetriedaten befinden.
      - Die Visualisierung baut auf der Telemetrie in Log Analytics auf und verwendet [Azure-Arbeitsmappen](../../../azure-monitor/platform/workbooks-overview.md). Kunden können die Visualisierung anpassen. Kunden können ihre Arbeitsmappen oder bestimmte Visualisierungen innerhalb von Arbeitsmappen auch an das Azure-Dashboard anheften, um die Möglichkeit zur automatischen Aktualisierung mit der niedrigsten Granularität von 30 Minuten zu erhalten.
      - Kunden können ihren vorhandenen Arbeitsbereich innerhalb desselben Abonnements als SAP-Monitorressource verwenden, indem sie diese Option zum Zeitpunkt der Bereitstellung auswählen.
      - Kunden können die Kusto-Abfragesprache (KQL) verwenden, um [Abfragen](../../../azure-monitor/log-query/log-query-overview.md) für die unformatierten Tabellen im Log Analytics-Arbeitsbereich auszuführen. Betrachten Sie die *benutzerdefinierten Protokolle*.

> [!Note]
> Kunden sind für das Patchen und Warten der VM verantwortlich, die in der verwalteten Ressourcengruppe bereitgestellt wird.

> [!Tip]
> Kunden können wählen, ob sie einen bestehenden Log Analytics-Arbeitsbereich für die Telemetrieerfassung verwenden möchten, wenn dieser innerhalb desselben Azure-Abonnements wie die Ressource für Azure Monitor für SAP-Lösungen bereitgestellt wird.

### <a name="architecture-highlights"></a>Highlights der Architektur

Im Folgenden sind die wichtigsten Highlights der Architektur aufgeführt:
 - **Mehrere Instanzen** – Kunden können einen Monitor für mehrere Instanzen eines bestimmten Komponententyps (z. B. HANA DB, HA-Cluster, Microsoft SQL Server) über mehrere SAP-SIDs innerhalb eines VNETs mit einer einzelnen Ressource von Azure Monitor für SAP-Lösungen erstellen.
 - **Mehrere Anbieter** – Das obige Architekturdiagramm zeigt als Beispiel den Anbieter „SAP HANA“. Ebenso können Kunden zusätzliche Anbieter für entsprechende Komponenten konfigurieren (z. B. HANA DB, HA-Cluster, Microsoft SQL Server), um Daten von diesen Komponenten zu sammeln.
 - **Open Source** – Der Quellcode von Azure Monitor für SAP-Lösungen ist in [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) verfügbar. Kunden können auf den Anbietercode verweisen und mehr über das Produkt erfahren, Beiträge leisten oder Feedback senden.
 - **Erweiterbares Abfrageframework** – SQL-Abfragen zur Erfassung von Telemetriedaten werden in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) geschrieben. Es können leicht zusätzliche SQL-Abfragen zur Erfassung weiterer Telemetriedaten hinzugefügt werden. Kunden können spezifische Telemetriedaten anfordern, die zu Azure Monitor für SAP-Lösungen hinzugefügt werden sollen, indem sie Feedback über den Link am Ende dieses Dokuments senden oder ihr Kontoteam kontaktieren.

## <a name="pricing"></a>Preise
Azure Monitor für SAP-Lösungen ist ein kostenloses Produkt (keine Lizenzgebühr). Kunden sind dafür verantwortlich, die Kosten für die zugrundeliegenden Komponenten in der verwalteten Ressourcengruppe zu bezahlen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Anbieter, und erstellen Sie Ihre erste Ressource für Azure Monitor für SAP-Lösungen.
 - Weitere Informationen zu [Anbietern](./azure-monitor-providers.md).
 - [Bereitstellen von Azure Monitor für SAP-Lösungen mit Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Haben Sie Fragen zu Azure Monitor für SAP-Lösungen? Weitere Informationen finden Sie im [FAQ](./azure-monitor-faq.md)-Abschnitt.