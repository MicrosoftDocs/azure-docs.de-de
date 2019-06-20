---
title: Verfeinern einer Bewertungsgruppe per Mapping von Gruppenabhängigkeiten in Azure Migrate | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine Bewertung verfeinern, indem Sie für den Azure Migrate-Dienst das Mapping von Gruppenabhängigkeiten verwenden.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 12/05/2018
ms.author: raynew
ms.openlocfilehash: 3ee528cc68a2a5637e85dc1d5ef68203916138e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60596894"
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Verfeinern einer Gruppe per Mapping von Gruppenabhängigkeiten

Dieser Artikel beschreibt, wie Sie eine Gruppe verfeinern, indem Sie die Abhängigkeiten aller Computer in der Gruppe visualisieren. Sie verwenden diese Methode normalerweise, wenn Sie die Mitgliedschaften für eine vorhandene Gruppe durch eine Überprüfung der Gruppenabhängigkeiten vor dem Durchführen einer Bewertung verfeinern möchten. Durch das Optimieren einer Gruppe mithilfe der Visualisierung der Abhängigkeiten können Sie Ihre Migration zu Azure effizient planen. Ermitteln Sie alle unabhängigen Systeme, die zusammen migriert werden müssen. So können Sie sicherstellen, dass Sie nichts übersehen und dass es bei der Migration zu Azure nicht zu unvorhergesehenen Ausfällen kommt.


> [!NOTE]
> Gruppen, für die Sie Gruppenabhängigkeiten visualisieren möchten, sollten höchstens zehn Computer enthalten. Wenn mehr als zehn Computer in der Gruppe vorhanden sind, empfiehlt es sich, sie in kleinere Gruppen aufzuteilen, um die Visualisierung der Abhängigkeiten nutzen zu können.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prepare-for-dependency-visualization"></a>Vorbereiten für die Visualisierung der Abhängigkeiten
Azure Migrate nutzt für die Visualisierung von Computerabhängigkeiten die Dienstzuordnungslösung in Azure Monitor-Protokollen.

> [!NOTE]
> Die Funktion zur Visualisierung von Abhängigkeiten ist in Azure Government nicht verfügbar.

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
Um Abhängigkeiten einer Gruppe anzuzeigen, müssen Sie auf allen lokalen Computern, die der Gruppe angehören, Agents herunterladen und installieren. Falls Sie über Computer ohne Internetverbindung verfügen, ist es außerdem erforderlich, dafür das [Log Analytics-Gateway](../azure-monitor/platform/gateway.md) herunterzuladen und zu installieren.

1. Klicken Sie unter **Übersicht** auf **Verwalten** > **Gruppen**, und wechseln Sie zur gewünschten Gruppe.
2. Klicken Sie in der Liste der Computer in der Spalte **Abhängigkeits-Agent** auf **Installation erforderlich**, um Anweisungen zum Herunterladen und Installieren der Agents anzuzeigen.
3. Laden Sie auf der Seite **Abhängigkeiten** den Microsoft Monitoring Agent (MMA) herunter, und installieren Sie diesen sowie den Abhängigkeits-Agent auf allen VMs, die der Gruppe angehören.
4. Kopieren Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel. Sie benötigen diese Angaben beim Installieren des MMA auf dem lokalen Computer.

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

#### <a name="install-the-agent-on-a-machine-monitored-by-system-center-operations-manager"></a>Installieren des Agent auf einem Computer, der von System Center Operations Manager überwacht wird

Bei Computern, die von Operations Manager 2012 R2 oder höher überwacht werden, besteht keine Notwendigkeit den MMA-Agent zu installieren. Die Dienstzuordnung verfügt über eine Integration mit Operations Manager, die den Operations Manager MMA zum Sammeln der erforderlichen Abhängigkeitsdaten nutzt. Sie können die Integration mithilfe der [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites) verfügbaren Anleitungen aktivieren. Beachten Sie aber, dass der Abhängigkeits-Agent auf diesen Computern installiert sein muss.

### <a name="install-the-dependency-agent"></a>Installieren des Abhängigkeits-Agents
1. Doppelklicken Sie zum Installieren des Abhängigkeits-Agents auf einem Windows-Computer auf die Setupdatei, und befolgen Sie die Schritte im Assistenten.
2. Installieren Sie den Abhängigkeits-Agent auf einem Linux-Computer als „root“, indem Sie den folgenden Befehl verwenden:

    ```sh InstallDependencyAgent-Linux64.bin```

Erfahren Sie mehr zur Unterstützung des Dependency-Agents für die Betriebssysteme [Windows](../azure-monitor/insights/service-map-configure.md#supported-windows-operating-systems) und [Linux](../azure-monitor/insights/service-map-configure.md#supported-linux-operating-systems).

[Erfahren Sie mehr](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) darüber, wie Sie den Dependency-Agent mithilfe von Skripts installieren können.

## <a name="refine-the-group-based-on-dependency-visualization"></a>Verfeinern der Gruppe anhand der Visualisierung der Abhängigkeiten
Nachdem Sie die Agents auf allen Computern der Gruppe installiert haben, können Sie die Abhängigkeiten der Gruppe visualisieren und diese anhand der unten aufgeführten Schritte verfeinern.

1. Klicken Sie im Azure Migrate-Projekt unter **Verwalten** auf  **Gruppen**, und wählen Sie eine Gruppe aus.
2. Klicken Sie auf der Seite der Gruppe auf  **Abhängigkeiten anzeigen**, um die Übersicht mit den Gruppenabhängigkeiten zu öffnen.
3. In der Abhängigkeitsübersicht für die Gruppe werden die folgenden Informationen angezeigt:
   - Eingehende (Clients) und ausgehende TCP-Verbindungen (Server) von/zu allen Computern in der Gruppe
       - Die abhängigen Computer, auf denen keine MMA- und Abhängigkeits-Agents installiert sind, gruppiert nach Portnummern
       - Die abhängigen Computer, auf denen MMA- und Abhängigkeits-Agents installiert sind, als separate Felder
   - Innerhalb des Computers ausgeführte Prozesse können Sie einblenden, indem Sie das Feld für den jeweiligen Computer erweitern.
   - Eigenschaften wie der vollqualifizierte Domänenname, das Betriebssystem, die MAC-Adresse usw. für jeden Computer können Sie anzeigen, indem Sie auf das Feld für den jeweiligen Computer klicken.

     ![Anzeigen der Gruppenabhängigkeiten](./media/how-to-create-group-dependencies/view-group-dependencies.png)

3. Klicken Sie auf den Zeitraum, und ändern Sie ihn, um ausführlichere Informationen zu den Abhängigkeiten zu erhalten. Standardmäßig ist ein Bereich von einer Stunde ausgewählt. Sie können den Zeitraum ändern oder das Start- und Enddatum und die Dauer angeben.

   > [!NOTE]
   >    Aktuell unterstützt die Benutzeroberfläche der Abhängigkeitsvisualisierung keine Auswahl eines Zeitraums von mehr als einer Stunde. Verwenden Sie Azure Monitor-Protokolle zum [Abfragen der Abhängigkeitsdaten](https://docs.microsoft.com/azure/migrate/how-to-create-a-group) über einen längeren Zeitraum.

4. Überprüfen Sie die abhängigen Computer und die auf jedem Computer ausgeführten Prozesse, und identifizieren Sie die Computer, die der Gruppe hinzugefügt oder aus dieser entfernt werden sollen.
5. Klicken Sie bei gedrückter STRG-Taste, um Computer in der Übersicht auszuwählen, die der Gruppe hinzugefügt oder daraus entfernt werden sollen.
    - Sie können nur Computer hinzufügen, die ermittelt wurden.
    - Wenn Sie Computer für eine Gruppe hinzufügen und entfernen, werden die vorherigen Bewertungen dafür ungültig.
    - Sie können optional eine neue Bewertung erstellen, wenn Sie die Gruppe ändern.
5. Klicken Sie auf **OK**, um die Gruppe zu speichern.

    ![Hinzufügen oder Entfernen von Computern](./media/how-to-create-group-dependencies/add-remove.png)

Wenn Sie die Abhängigkeiten eines bestimmten Computers überprüfen möchten, der in der Übersicht mit den Gruppenabhängigkeiten angezeigt wird, können Sie eine [Übersicht mit den Computerabhängigkeiten einrichten](how-to-create-group-machine-dependencies.md).

## <a name="query-dependency-data-from-azure-monitor-logs"></a>Abfragen von Abhängigkeitsdaten aus Azure Monitor-Protokollen

Von der Dienstzuordnung erfasste Abhängigkeitsdaten stehen zur Abfrage im Log Analytics-Arbeitsbereich zur Verfügung, der Ihrem Azure Migrate-Projekt zugeordnet ist. [Weitere Informationen](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) zu den Dienstzuordnungs-Datentabellen zum Abfragen in Azure Monitor-Protokollen. 

So führen Sie die Kusto-Abfragen aus:

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Übersicht**.
2. Wechseln Sie in der 0**Übersicht** zum Abschnitt **Essentials** des Projekts, und klicken Sie auf den Arbeitsbereichsnamen, der neben dem **OMS-Arbeitsbereich** steht.
3. Klicken Sie auf der Log Analytics-Arbeitsbereichsseite auf **Allgemein** > **Protokolle**.
4. Schreiben Sie Ihre Abfrage, um mit Azure Monitor-Protokollen Abhängigkeitsdaten zu sammeln. Beispielabfragen finden Sie im nächsten Abschnitt.
5. Führen Sie Ihre Abfrage aus, indem Sie auf „Ausführen“ klicken. 

[Erfahren Sie mehr](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) über das Schreiben von Kusto-Abfragen. 

## <a name="sample-azure-monitor-logs-queries"></a>Beispielabfragen für Azure Monitor-Protokolle

Nachfolgend finden Sie Beispielabfragen, mit denen Sie Abhängigkeitsdaten extrahieren können. Sie können die Abfragen ändern, um Ihre bevorzugten Datenpunkte zu extrahieren. Eine vollständige Liste der Felder in Abhängigkeitsdatensätzen ist [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records) verfügbar. Weitere Beispielabfragen finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches).

### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>Zusammenfassen von eingehenden Verbindungen in einer Gruppe von Computern

Beachten Sie, dass die Datensätze in der Tabelle für Verbindungsmetriken „VMConnection“ keine einzelnen physischen Netzwerkverbindungen darstellen. Mehrere physische Netzwerkverbindungen werden in einer logischen Verbindung gruppiert. Weitere Informationen dazu, wie Daten von physischen Netzwerkverbindungen in einem einzelnen logischen Datensatz in VMConnection aggregiert werden, finden Sie [hier](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections). 

```
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
- [Weitere Informationen](concepts-assessment-calculation.md) zur Berechnung von Bewertungen
