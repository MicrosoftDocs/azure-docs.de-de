---
title: Gruppieren von Computern mithilfe von Computerabhängigkeiten per Azure Migrate | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie mithilfe von Computerabhängigkeiten mit dem Azure Migrate-Dienst eine Bewertung erstellen.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: af47678b19209936aed86c132a8a3f400c3a7e8f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596805"
---
# <a name="group-machines-using-machine-dependency-mapping"></a>Gruppieren von Computern per Mapping von Computerabhängigkeiten

In diesem Artikel wird beschrieben, wie Sie eine Gruppe mit Computern für eine [Azure Migrate](migrate-overview.md)-Bewertung erstellen, indem Sie die Computerabhängigkeiten visualisieren. Sie verwenden diese Methode normalerweise, wenn Sie Gruppen mit VMs mit höheren Zuverlässigkeitsgraden bewerten möchten, indem Sie vor dem Durchführen einer Bewertung die Computerabhängigkeiten überprüfen. Durch eine Visualisierung der Abhängigkeiten können Sie Ihre Migration zu Azure effizient planen. So können Sie sicherstellen, dass Sie nichts übersehen und dass es bei der Migration zu Azure nicht zu unvorhergesehenen Ausfällen kommt. Sie können alle voneinander abhängigen Systeme ermitteln, die gemeinsam migriert werden müssen, und ermitteln, ob ein ausgeführtes System noch benötigt wird oder eher außer Betrieb gesetzt als migriert werden sollte.

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

## <a name="prepare-for-dependency-visualization"></a>Vorbereiten für die Visualisierung der Abhängigkeiten
Azure Migrate nutzt die Dienstzuordnungslösung in Azure Monitor-Protokollen, um die Visualisierung von Computerabhängigkeiten zu ermöglichen.

### <a name="associate-a-log-analytics-workspace"></a>Zuordnen eines Log Analytics-Arbeitsbereichs
Sie müssen jedem Azure Migrate-Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zuordnen, um die Abhängigkeitsvisualisierung nutzen zu können. Sie können einen Arbeitsbereich nur in dem Abonnement erstellen oder anfügen, in dem das Migrationsprojekt erstellt wird.

- Um einem Projekt einen Log Analytics-Arbeitsbereich anzufügen, wechseln Sie unter **Overview** (Übersicht) zum Projektabschnitt **Essentials** (Zusammenfassung), und klicken Sie auf **Requires configuration** (Erfordert Konfiguration).

    ![Zuordnen von Log Analytics-Arbeitsbereichen](./media/concepts-dependency-visualization/associate-workspace.png)

- Beim Zuordnen eines Arbeitsbereichs erhalten Sie die Möglichkeit, einen neuen Arbeitsbereich zu erstellen oder einen vorhandenen anzufügen:
  - Wenn Sie einen neuen Arbeitsbereich erstellen, müssen Sie für diesen einen Namen angeben. Der Arbeitsbereich wird dann in einer Region in derselben [Azure-Geografie](https://azure.microsoft.com/global-infrastructure/geographies/) erstellt, in der auch das Migrationsprojekt erstellt wurde.
  - Wenn Sie einen vorhandenen Arbeitsbereich anfügen, können Sie zwischen allen verfügbaren Arbeitsbereichen im selben Abonnement wie das Migrationsprojekt auswählen. Beachten Sie, dass nur die Arbeitsbereiche aufgeführt sind, die in einer Region erstellt wurden, in der die [Dienstzuordnung unterstützt wird](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-configure#supported-azure-regions). Um einen Arbeitsbereich anzufügen, stellen Sie sicher, dass Sie Lesezugriff auf den Arbeitsbereich besitzen.

> [!NOTE]
> Den einem Migrationsprojekt zugeordneten Arbeitsbereich können Sie nicht ändern.

### <a name="download-and-install-the-vm-agents"></a>Herunterladen und Installieren der VM-Agents
Beim Konfigurieren eines Arbeitsbereiches müssen Sie auf allen lokalen Computern, für die Sie eine Bewertung durchführen möchten, Agents herunterladen und installieren. Falls Sie über Computer ohne Internetverbindung verfügen, ist es außerdem erforderlich, dafür das [Log Analytics-Gateway](../azure-monitor/platform/gateway.md) herunterzuladen und zu installieren.

1. Klicken Sie unter **Übersicht** auf **Verwalten** > **Computer**, und wählen Sie den gewünschten Computer aus.
2. Klicken Sie in der Spalte **Abhängigkeiten** auf **Agents installieren**.
3. Laden Sie auf der Seite **Abhängigkeiten** den Microsoft Monitoring Agent (MMA) herunter, und installieren Sie ihn – sowie den Abhängigkeits-Agent auf allen VMs, die Sie auswerten möchten.
4. Kopieren Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel. Sie benötigen diese Angaben beim Installieren des MMA auf dem lokalen Computer.

> [!NOTE]
> Um die Installation von Agents zu automatisieren, können Sie Bereitstellungstools wie System Center Configuration Manager verwenden oder unser Partner-Tool [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration), das eine Agent-Bereitstellungslösung für Azure Migrate enthält.

### <a name="install-the-mma"></a>Installieren des MMA

#### <a name="install-the-agent-on-a-windows-machine"></a>Installieren des Agent auf einem Windows-Computer

Gehen Sie wie folgt vor, um den Agent auf einem Windows-Computer zu installieren:

1. Doppelklicken Sie auf den heruntergeladenen Agent.
2. Klicken Sie auf der Seite **Willkommen**auf **Weiter**. Klicken Sie auf der Seite **Lizenzbedingungen** auf **Ich stimme zu**, um die Lizenzbedingungen zu akzeptieren.
3. Behalten Sie unter **Zielordner** den Standardinstallationsordner bei, oder ändern Sie ihn, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie unter **Agent-Setupoptionen** die Optionen **Azure Log Analytics** > **Weiter**.
5. Klicken Sie auf **Hinzufügen**, um einen neuen Log Analytics-Arbeitsbereich hinzuzufügen. Fügen Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel ein, die bzw. den Sie im Portal kopiert haben. Klicken Sie auf **Weiter**.

Sie können den Agent über die Befehlszeile oder mithilfe einer automatisierten Methode wie System Center Configuration Manager installieren. [Weitere Informationen](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) zur Verwendung dieser Methoden zum Installieren des MMA-Agent.

#### <a name="install-the-agent-on-a-linux-machine"></a>Installieren des Agent auf einem Linux-Computer

Gehen Sie wie folgt vor, um einen Agent auf einem Linux-Computer zu installieren:

1. Übertragen Sie das entsprechende Paket (x86 oder x64) mithilfe von scp/sftp auf Ihren Linux-Computer.
2. Installieren Sie das Bundle mit dem Argument „--install“.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Erfahren Sie mehr](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) über die Liste der Unterstützungen durch den MMA für Linux-Betriebssysteme.

#### <a name="install-the-agent-on-a-machine-monitored-by-scom"></a>Installieren des Agent auf einem Computer, der vom SCOM überwacht wird

Bei Computern, die von System Center Operations Manager 2012 R2 oder höher überwacht werden, besteht keine Notwendigkeit den MMA-Agent zu installieren. Die Dienstzuordnung verfügt über eine Integration mit SCOM, die den SCOM MMA zum Sammeln der erforderlichen Abhängigkeitsdaten nutzt. Sie können die Integration mithilfe der [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) verfügbaren Anleitungen aktivieren. Beachten Sie aber, dass der Abhängigkeits-Agent auf diesen Computern installiert sein muss.


### <a name="install-the-dependency-agent"></a>Installieren des Abhängigkeits-Agents
1. Doppelklicken Sie zum Installieren des Abhängigkeits-Agents auf einem Windows-Computer auf die Setupdatei, und befolgen Sie die Schritte im Assistenten.
2. Installieren Sie den Abhängigkeits-Agent auf einem Linux-Computer als „root“, indem Sie den folgenden Befehl verwenden:

    ```sh InstallDependencyAgent-Linux64.bin```

Erfahren Sie mehr zur Unterstützung des Dependency-Agents für die Betriebssysteme [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) und [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[Erfahren Sie mehr](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) darüber, wie Sie den Dependency-Agent mithilfe von Skripts installieren können.


## <a name="create-a-group"></a>Erstellen einer Gruppe

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Verwalten** > **Computer**.
2. Suchen Sie nach dem Computer, auf dem Sie die Agents installiert haben.
3. Die Spalte **Abhängigkeiten** für den Computer sollte jetzt als **Abhängigkeiten anzeigen** erscheinen. Klicken Sie auf die Spalte, um die Abhängigkeiten des Computers anzuzeigen.
4. In der Abhängigkeitszuordnung für den Computer werden die folgenden Informationen angezeigt:
    - Eingehende (Clients) und ausgehende (Server) TCP-Verbindungen vom/zum Computer.
        - Die abhängigen Computer, auf denen keine MMA- und Abhängigkeits-Agents installiert sind, gruppiert nach Portnummern
        - Die abhängigen Computer, auf denen MMA- und Abhängigkeits-Agents installiert sind, als separate Felder
    - Innerhalb des Computers ausgeführte Prozesse können Sie einblenden, indem Sie das Feld für den jeweiligen Computer erweitern.
    - Eigenschaften wie vollqualifizierter Domänenname, Betriebssystem, MAC-Adresse usw. für jeden Computer können Sie anzeigen, indem Sie auf das Feld für den jeweiligen Computer klicken.

      ![Anzeigen von Computerabhängigkeiten](./media/how-to-create-group-machine-dependencies/machine-dependencies.png)

4. Sie können Abhängigkeiten für verschiedene Zeiträume anzeigen, indem Sie auf im Zeitbereich auf die Zeitdauer klicken. Standardmäßig ist ein Bereich von einer Stunde ausgewählt. Sie können den Zeitraum ändern oder das Start- und Enddatum und die Dauer angeben.

   > [!NOTE]
   >    Aktuell unterstützt die Benutzeroberfläche der Abhängigkeitsvisualisierung keine Auswahl eines Zeitraums von mehr als einer Stunde. Verwenden Sie Azure Monitor-Protokolle zum [Abfragen der Abhängigkeitsdaten](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) über einen längeren Zeitraum.

5. Nachdem Sie abhängige Computer identifiziert haben, die Sie gruppieren möchten, können Sie die gewünschten Computer mit Strg+Klick auswählen und auf **Computer gruppieren** klicken.
6. Geben Sie einen Gruppennamen an. Stellen Sie sicher, dass die abhängigen Computer von Azure Migrate ermittelt werden.

    > [!NOTE]
    > Wenn ein abhängiger Computer nicht von Azure Migrate ermittelt wurde, können Sie ihn nicht zur Gruppe hinzufügen. Um solche Computer zur Gruppe hinzuzufügen, müssen Sie den Ermittlungsprozess mit dem richtigen Bereich erneut in vCenter Server ausführen und sicherstellen, dass der Computer von Azure Migrate ermittelt wird.  

7. Wenn Sie eine Bewertung für diese Gruppe erstellen möchten, aktivieren Sie das Kontrollkästchen, um eine neue Bewertung für die Gruppe zu erstellen.
8. Klicken Sie auf **OK**, um die Gruppe zu speichern.

Nach der Erstellung der Gruppe wird empfohlen, alle Agents auf sämtlichen Computern in der Gruppe zu installieren und die Gruppe zu verfeinern, indem die Abhängigkeiten der gesamten Gruppe visualisiert werden.

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Abfragen von Abhängigkeitsdaten aus Azure Monitor-Protokollen

Von der Dienstzuordnung erfasste Abhängigkeitsdaten stehen zur Abfrage im Log Analytics-Arbeitsbereich zur Verfügung, der Ihrem Azure Migrate-Projekt zugeordnet ist. [Weitere Informationen](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) zu den Dienstzuordnungs-Datentabellen zum Abfragen in Azure Monitor-Protokollen. 

So führen Sie die Kusto-Abfragen aus:

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Übersicht**.
2. Wechseln Sie in der 0**Übersicht** zum Abschnitt **Essentials** des Projekts, und klicken Sie auf den Arbeitsbereichsnamen, der neben dem **OMS-Arbeitsbereich** steht.
3. Klicken Sie auf der Log Analytics-Arbeitsbereichsseite auf **Allgemein** > **Protokolle**.
4. Schreiben Sie Ihre Abfrage, um mit Azure Monitor-Protokollen Abhängigkeitsdaten zu sammeln. Beispielabfragen finden Sie im nächsten Abschnitt.
5. Führen Sie Ihre Abfrage aus, indem Sie auf „Ausführen“ klicken. 

[Erfahren Sie mehr](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) über das Schreiben von Kusto-Abfragen. 

### <a name="sample-azure-monitor-logs-queries"></a>Beispielabfragen für Azure Monitor-Protokolle

Nachfolgend finden Sie Beispielabfragen, mit denen Sie Abhängigkeitsdaten extrahieren können. Sie können die Abfragen ändern, um Ihre bevorzugten Datenpunkte zu extrahieren. Eine vollständige Liste der Felder in Abhängigkeitsdatensätzen ist [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) verfügbar. Weitere Beispielabfragen finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Zusammenfassen von eingehenden Verbindungen in einer Gruppe von Computern

Beachten Sie, dass die Datensätze in der Tabelle für Verbindungsmetriken „VMConnection“ keine einzelnen physischen Netzwerkverbindungen darstellen. Mehrere physische Netzwerkverbindungen werden in einer logischen Verbindung gruppiert. Weitere Informationen dazu, wie Daten von physischen Netzwerkverbindungen in einem einzelnen logischen Datensatz in VMConnection aggregiert werden, finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections). 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>Zusammenfassen der gesendeten und empfangenen Daten in eingehenden Verbindungen zwischen einer Gruppe von Computern

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr](https://docs.microsoft.com/azure/migrate/resources-faq#dependency-visualization) über die häufig gestellten Fragen (FAQs) zur Visualisierung von Abhängigkeiten.
- Informieren Sie sich über die [Vorgehensweise](how-to-create-group-dependencies.md) zum Verfeinern der Gruppe durch das Visualisieren von Gruppenabhängigkeiten.
- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
