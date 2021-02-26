---
title: Anbieter von Azure Monitor für SAP-Lösungen | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf häufig gestellte Fragen zu Azure Monitor für SAP-Lösungsanbieter.
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 8192d7104daf1474a2123331183edf05e6fa1ada
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007413"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Anbieter von Azure Monitor für SAP-Lösungen (Vorschau)

## <a name="overview"></a>Übersicht  

Im Kontext von Azure Monitor für SAP-Lösungen bezieht sich ein *Anbietertyp* auf einen bestimmten *Anbieter*. Beispiel: *SAP HANA*, das für eine bestimmte Komponente innerhalb der SAP-Landschaft konfiguriert ist, wie die SAP HANA-Datenbank. Ein Anbieter enthält die Verbindungsinformationen für die entsprechende Komponente und hilft, Telemetriedaten von dieser Komponente zu sammeln. Eine Ressource von Azure Monitor für SAP-Lösungen (auch als SAP-Monitorressource bezeichnet) kann mit mehreren Anbietern desselben Anbietertyps oder mehreren Anbietern verschiedener Anbietertypen konfiguriert werden.
   
Kunden können verschiedene benutzerdefinierte Anbietertypen konfigurieren, um die Datensammlung aus der entsprechenden Komponente in ihrer SAP-Landschaft zu ermöglichen. Beispielsweise können Kunden einen Anbieter für den Anbietertyp „SAP HANA“, einen anderen Anbieter für den Anbietertyp „Hochverfügbarkeitscluster“ usw. konfigurieren.  

Kunden können auch mehrere Anbieter eines bestimmten Anbietertyps konfigurieren, um dieselbe SAP-Monitorressource und die zugehörige verwaltete Gruppe wiederzuverwenden. Erfahren Sie mehr über die verwaltete Ressourcengruppe. Für die öffentliche Vorschau werden die folgenden Anbietertypen unterstützt:   
- SAP HANA
- Hochverfügbarkeitscluster
- Microsoft SQL Server

![Anbieter von Azure Monitor für SAP-Lösungen](./media/azure-monitor-sap/azure-monitor-providers.png)

Kunden wird empfohlen, zum Zeitpunkt der Bereitstellung der SAP-Monitorressource mindestens einen Anbieter aus den verfügbaren Anbietertypen zu konfigurieren. Durch die Konfiguration eines Anbieters initiieren Kunden die Datenerfassung aus der entsprechenden Komponente, für die der Anbieter konfiguriert ist.   

Wenn Kunden zum Zeitpunkt der Bereitstellung der SAP-Monitorressource keine Anbieter konfigurieren, werden keine Telemetriedaten erfasst, obwohl die SAP-Monitorressource erfolgreich bereitgestellt wird. Kunden haben die Möglichkeit, Anbieter nach der Bereitstellung über die SAP-Monitorressource im Azure-Portal hinzuzufügen. Kunden können jederzeit Anbieter in der SAP-Monitorressource hinzufügen oder löschen.

> [!Tip]
> Wenn Sie möchten, dass Microsoft einen bestimmten Anbieter implementiert, hinterlassen Sie Ihr Feedback über den Link am Ende dieses Dokuments, oder wenden Sie sich an Ihr Kontoteam.  

## <a name="provider-type-sap-hana"></a>Anbietertyp „SAP HANA“

Kunden können einen oder mehrere Anbieter des Anbietertyps *SAP HANA* konfigurieren, um die Datenerfassung aus der SAP HANA-Datenbank zu ermöglichen. Der SAP HANA-Anbieter stellt über den SQL-Port eine Verbindung mit der SAP HANA-Datenbank her, pullt Telemetriedaten aus der Datenbank und pusht sie in den Log Analytics-Arbeitsbereich des Kundenabonnements. Der SAP HANA-Anbieter führt die Erfassung aus der SAP HANA-Datenbank jede Minute durch.  

In der öffentlichen Vorschau können Kunden die folgenden Daten mit dem SAP HANA-Anbieter erwarten: Nutzung der zugrundeliegenden Infrastruktur, Status des SAP HANA-Hosts, SAP HANA-Systemreplikation und Telemetriedaten für SAP HANA-Sicherungen. Zur Konfiguration des SAP HANA-Anbieters sind die Host-IP-Adresse, die HANA SQL-Portnummer und der SYSTEMDB-Benutzername sowie das Kennwort erforderlich. Kunden wird empfohlen, den SAP HANA-Anbieter für SYSTEMDB zu konfigurieren. Weitere Anbieter können jedoch auch für andere Datenbankmandanten konfiguriert werden.

![Anbieter von Azure Monitor für SAP-Lösungen – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Anbietertyp „Hochverfügbarkeitscluster“
Kunden können einen oder mehrere Anbieter des Anbietertyps *Hochverfügbarkeitscluster* konfigurieren, um die Datenerfassung aus Pacemaker-Clustern innerhalb der SAP-Landschaft zu ermöglichen. Der Anbieter des Hochverfügbarkeitsclusters stellt über den Endpunkt [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) eine Verbindung mit Pacemaker her, pullt Telemetriedaten aus der Datenbank und pusht sie in den Log Analytics-Arbeitsbereich des Kundenabonnements. Der Anbietertyp „Hochverfügbarkeitscluster“ erfasst alle 60 Sekunden Daten von Pacemaker.  

In der öffentlichen Vorschau können benutzerdefinierte Kunden die folgenden Daten mit Anbietern von Hochverfügbarkeitsclustern erwarten:   
 - Clusterstatus dargestellt als Rollup des Knoten- und Ressourcenstatus 
 - [Sonstige](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Anbieter von Azure Monitor für SAP-Lösungen – Hochverfügbarkeitscluster](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Die Konfiguration eines Anbieters von Hochverfügbarkeitsclustern erfolgt in zwei Hauptschritten:

1. Installieren Sie [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *jedem* Knoten innerhalb des Pacemaker-Clusters.

   Für das Installieren von „ha_cluster_exporter“ gibt es zwei Möglichkeiten:
   
   - Verwenden Sie benutzerdefinierte Azure Automation-Skripts, um Hochverfügbarkeitscluster bereitzustellen. Die Skripts installieren [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in jedem Clusterknoten.  
   - Führen Sie eine [manuelle Installation](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) durch. 

2. Konfigurieren Sie den Anbieter von Hochverfügbarkeitsclustern in *jedem* Knoten innerhalb des Pacemaker-Clusters.

   Zum Konfigurieren des Anbieters für Hochverfügbarkeitscluster sind die folgenden Informationen erforderlich:
   
   - **Name**. Ein Name für diesen Anbieter. Dieser sollte für die Instanz der Azure Monitor für SAP-Lösung eindeutig sein.
   - **Prometheus-Endpunkt:** http\://\<servername or ip address\>:9664/metrics.
   - **SID:** Verwenden Sie für SAP-Systeme die SAP-SID. Verwenden Sie für andere Systeme (z. B. NFS-Cluster) einen aus drei Zeichen bestehenden Namen für den Cluster. Die SID muss sich von anderen überwachten Clustern unterscheiden.   
   - **Clustername:** Der Clustername, der beim Erstellen des Clusters verwendet wird. Sie finden den Clusternamen in der Clustereigenschaft `cluster-name`.
   - **Hostname**. Der Linux-Hostname der VM.  


## <a name="provider-type-os-linux"></a>Anbietertyp für Betriebssystem (Linux)
Kunden können einen oder mehrere Anbieter für den Bereitstellungstyp (Linux) konfigurieren, damit die Datensammlung von BareMetal- oder VM-Knoten aktiviert wird. Der Betriebssystemanbieter (Linux) verbindet sich mit BareMetal- oder VM-Knoten, verwendet den Endpunkt  [Node_Exporter](https://github.com/prometheus/node_exporter) , ruft Telemetriedaten von den Knoten ab und überträgt sie in den Log Analytics-Arbeitsbereich im Abonnement des Kunden. Der Betriebssystemanbieter (Linux) sammelt alle 60 Sekunden Daten für die meisten Metriken von den Knoten. 

In der öffentlichen Vorschau können Kunden die folgenden Daten beim Betriebssystemanbieter (Linux) erwarten: 
   - CPU-Auslastung, CPU-Auslastung nach Prozess 
   - Datenträgerauslastung, E/A für Lese- und Schreibvorgänge 
   - Arbeitsspeicherverteilung, Arbeitsspeicherauslastung, Auslastung des Auslagerungsspeichers 
   - Netzwerkauslastung, Details zum im Netzwerk ein- und ausgehenden Datenverkehr 

Die Konfiguration eines Betriebssystemanbieters (Linux) erfolgt in zwei Hauptschritten:
1. Installieren Sie  [Node_Exporter](https://github.com/prometheus/node_exporter)  auf allen BareMetal- oder VM-Knoten.
   Für das Installieren von [Node_Exporter](https://github.com/prometheus/node_exporter) gibt es zwei Möglichkeiten: 
      - Für die Azure Automation-Installation mit Ansible verwenden Sie [Node_Exporter](https://github.com/prometheus/node_exporter) auf jedem BareMetal- oder VM-Knoten, um den Betriebssystemanbieter (Linux) zu installieren.  
      - Führen Sie eine  [manuelle Installation](https://prometheus.io/docs/guides/node-exporter/) aus.

2. Konfigurieren Sie einen Betriebssystemanbieter (Linux) für jede BareMetal- oder VM-Knoteninstanz in Ihrer Umgebung. 
   Zum Konfigurieren des Betriebssystemanbieters (Linux) sind die folgenden Informationen erforderlich: 
      - Name. Ein Name für diesen Anbieter. Dieser sollte für die Instanz der Azure Monitor für SAP-Lösung eindeutig sein. 
      - Endpunkt von Node Exporter. In der Regel http://<servername or ip address>:9100/metrics 

> [!NOTE]
> 9100 ist ein Port, der für den Endpunkt von Node_Exporter verfügbar gemacht wird.

> [!Warning]
> Stellen Sie sicher, dass Node Exporter nach dem Neustart des Knotens weiter ausgeführt wird. 


## <a name="provider-type-microsoft-sql-server"></a>Anbietertyp „Microsoft SQL Server“

Kunden können einen oder mehrere Anbieter des Anbietertyps *Microsoft SQL Server* konfigurieren, um die Datenerfassung von [SQL Server auf virtuellen Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) zu ermöglichen. Der SQL Server-Anbieter stellt über den SQL-Port eine Verbindung mit Microsoft SQL Server her, pullt Telemetriedaten aus der Datenbank und pusht sie in den Log Analytics-Arbeitsbereich des Kundenabonnements. Der SQL Server muss für die SQL-Authentifizierung konfiguriert werden, und es muss eine SQL Server-Anmeldung mit der SAP DB als Standarddatenbank für den Anbieter erstellt werden. Der SQL Server-Anbieter erfasst Daten zwischen alle 60 Sekunden bis zu jeder Stunde von SQL Server.  

In der öffentlichen Vorschau können Kunden die folgenden Daten mit dem SQL Server-Anbieter erwarten: Nutzung der zugrunde liegenden Infrastruktur, wichtigste SQL-Anweisungen, größte Tabelle, Aufzeichnung von Problemen in den SQL Server-Fehlerprotokollen, Blockierung von Prozessen und andere.  

Zur Konfiguration des Microsoft SQL Server-Anbieters sind die SAP-System-ID, die Host-IP-Adresse, die SQL Server-Portnummer sowie der SQL Server-Anmeldename und das Kennwort erforderlich.

![Anbieter von Azure Monitor für SAP-Lösungen – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Nächste Schritte

- Erstellen Sie Ihre erste Ressource für Azure Monitor für SAP-Lösungen.
- Haben Sie Fragen zu Azure Monitor für SAP-Lösungen? Weitere Informationen finden Sie im [FAQ](./azure-monitor-faq.md)-Abschnitt.
