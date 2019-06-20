---
title: Verwalten von Datenflussprotokollen für Netzwerksicherheitsgruppen mit Network Watcher und Grafana | Microsoft-Dokumentation
description: Auf dieser Seite wird erläutert, wie Datenflussprotokolle für Netzwerksicherheitsgruppen in Azure mithilfe von Network Watcher und Grafana verwaltet werden.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: mareat
ms.openlocfilehash: 73173c144f979d4a10b90a16aec783fe51a3f90e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62116240"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-using-network-watcher-and-grafana"></a>Verwalten von Datenflussprotokollen für Netzwerksicherheitsgruppen mit Network Watcher und Grafana

[Datenflussprotokolle von Netzwerksicherheitsgruppen (NSG)](network-watcher-nsg-flow-logging-overview.md) enthalten Informationen zum besseren Verstehen von ein- und ausgehendem IP-Datenverkehr an Netzwerkschnittstellen. Diese Datenflussprotokolle zeigen aus- und eingehende Datenflüsse pro NSG-Regel, die NIC, auf die sich der Datenfluss bezieht, fünf Informationen zum Datenfluss (Quell-/Ziel-IP-Adresse, Quell-/Zielport, Protokoll) und Informationen dazu, ob der Datenverkehr zugelassen oder verweigert wurde.

> [!Warning]  
> In den folgenden Schritten werden Flowprotokolle der Version 1 verwendet. Ausführliche Informationen finden Sie unter [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md). Die folgenden Anweisungen funktionieren ohne Änderungen nicht mit Version 2 der Protokolldateien.

Für viele NSGs in Ihrem Netzwerk kann die Datenflussprotokollierung aktiviert sein. Diese Menge an Protokolldaten macht es allerdings umständlich, Ihre Protokolle zu analysieren, um Einblicke zu gewinnen. Dieser Artikel bietet eine Lösung zur zentralen Verwaltung dieser NSG-Datenflussprotokolle mit Grafana, einem Open-Source-Visualisierungstool, Elasticsearch, einer verteilten Engine für Suche und Analyse, und Logstash, einer serverseitigen Open-Source-Datenverarbeitungspipeline.  

## <a name="scenario"></a>Szenario

NSG-Datenflussprotokolle werden mithilfe von Network Watcher aktiviert und in Azure Blob Storage abgelegt. Ein Logstash-Plug-In wird verwendet, um mit Datenflussprotokollen im Blob Storage eine Verbindung herzustellen, diese zu verarbeiten und an Elasticsearch zu senden.  Nachdem die Datenflussprotokolle in Elasticsearch gespeichert wurden, können sie analysiert und in benutzerdefinierten Dashboards in Grafana visualisiert werden.

![NSG Network Watcher Grafana](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig1.png)

## <a name="installation-steps"></a>Installationsschritte

### <a name="enable-network-security-group-flow-logging"></a>Aktivieren der Datenflussprotokollierung für Netzwerksicherheitsgruppen

Für dieses Szenario müssen Sie die NSG-Datenflussprotokollierung für mindestens eine Netzwerksicherheitsgruppe in Ihrem Konto aktivieren. Anweisungen zum Aktivieren von NSG-Datenflussprotokollen finden Sie im Artikel [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md).

### <a name="setup-considerations"></a>Überlegungen zum Setup

Bei diesem Beispiel sind Grafana, Elasticsearch und Logstash auf einem in Azure bereitgestellten Ubuntu 16.04 LTS-Server konfiguriert. Dieses minimale Setup wird für die Ausführung aller drei Komponenten verwendet, die alle auf derselben VM ausgeführt werden. Dieses Setup sollte allerdings nur für Test- und unkritische Workloads verwendet werden. Logstash, Elasticsearch und Grafana können so gestaltet werden, dass eine unabhängige Skalierung auf zahlreiche Instanzen möglich ist. Weitere Informationen finden Sie in der Dokumentation zu den einzelnen Komponenten.

### <a name="install-logstash"></a>Installieren von Logstash

Mithilfe von Logstash können Sie die JSON-formatierten Datenflussprotokolle auf Flusstupelebene vereinfachen.

1. Führen Sie zum Installieren von Logstash die folgenden Befehle aus:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```

2. Konfigurieren Sie Logstash, um die Datenflussprotokolle zu analysieren und an Elasticsearch zu senden. Erstellen Sie die Datei „logstash.conf“ wie folgt:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Fügen Sie der Datei folgenden Inhalt hinzu. Ändern Sie den Namen des Speicherkontos und Zugriffsschlüssels entsprechend Ihren Speicherkontodetails:

   ```bash
    input {
      azureblob
      {
        storage_account_name => "mystorageaccount"
        storage_access_key => "VGhpcyBpcyBhIGZha2Uga2V5Lg=="
        container => "insights-logs-networksecuritygroupflowevent"
        codec => "json"
        # Refer https://docs.microsoft.com/azure/network-watcher/network-watcher-read-nsg-flow-logs
        # Typical numbers could be 21/9 or 12/2 depends on the nsg log file types
        file_head_bytes => 12
        file_tail_bytes => 2
        # Enable / tweak these settings when event is too big for codec to handle.
        # break_json_down_policy => "with_head_tail"
        # break_json_batch_count => 2
      }
    }
    filter {
      split { field => "[records]" }
      split { field => "[records][properties][flows]"}
      split { field => "[records][properties][flows][flows]"}
      split { field => "[records][properties][flows][flows][flowTuples]"}

      mutate {
        split => { "[records][resourceId]" => "/"}
        add_field => { "Subscription" => "%{[records][resourceId][2]}"
          "ResourceGroup" => "%{[records][resourceId][4]}"
          "NetworkSecurityGroup" => "%{[records][resourceId][8]}" 
        }
        convert => {"Subscription" => "string"}
        convert => {"ResourceGroup" => "string"}
        convert => {"NetworkSecurityGroup" => "string"}
        split => { "[records][properties][flows][flows][flowTuples]" => "," }
        add_field => {
          "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
          "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
          "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
          "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
          "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
          "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
          "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
          "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
        }
        add_field => {
          "time" => "%{[records][time]}"
          "systemId" => "%{[records][systemId]}"
          "category" => "%{[records][category]}"
          "resourceId" => "%{[records][resourceId]}"
          "operationName" => "%{[records][operationName}}"
          "Version" => "%{[records][properties][Version}}"
          "rule" => "%{[records][properties][flows][rule]}"
          "mac" => "%{[records][properties][flows][flows][mac]}"
        }
        convert => {"unixtimestamp" => "integer"}
        convert => {"srcPort" => "integer"}
        convert => {"destPort" => "integer"}
        add_field => { "message" => "%{Message}" }        
      }
 
      date {
        match => ["unixtimestamp" , "UNIX"]
      }
    }
    output {
      stdout { codec => rubydebug }
      elasticsearch {
        hosts => "localhost"
        index => "nsg-flow-logs"
      }
    }
   ```

Die bereitgestellte CONF-Datei von Logstash besteht aus drei Teilen: Eingabe, Filter und Ausgabe.
Der Eingabebereich bezeichnet die Eingabequelle der Protokolle, die Logstash verarbeitet. In diesem Fall verwenden wir ein „azureblob“-Eingabe-Plug-In (das in den nächsten Schritten installiert wird). Es erlaubt uns, auf die JSON-Dateien des NSG-Datenflussprotokolls zuzugreifen, die in Blob Storage gespeichert sind. 

Der Filterabschnitt vereinfacht dann jede Datenfluss-Protokolldatei so, dass jedes einzelnen Flusstupel und die ihm zugeordneten Eigenschaften zu einem gesonderten Logstash-Ereignis werden.

Schließlich leitet der Ausgabeteil jedes Logstash-Ereignis an den Elasticsearch-Server weiter. Sie können die CONF-Datei von Logstash nach Wunsch an Ihre spezifischen Anforderungen anpassen.

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installieren des Logstash-Eingabe-Plug-Ins für Azure Blob Storage

Mithilfe dieses Logstash-Plug-Ins können Sie direkt auf die Datenflussprotokolle im jeweiligen Blob Storage-Konto zugreifen. Sie installieren dieses Plug-In, indem Sie über das Logstash-Standardinstallationsverzeichnis (in diesem Fall /usr/share/logstash/bin) folgenden Befehl ausführen:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Weitere Informationen zu diesem Plug-in finden Sie unter [Logstash-Eingabe-Plug-In für Azure Storage Blobs](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-elasticsearch"></a>Installieren von Elasticsearch

Sie können das folgende Skript verwenden, um Elasticsearch zu installieren. Informationen zum Installieren von Elasticsearch finden Sie unter [Elastic Stack](https://www.elastic.co/guide/en/elastic-stack/current/index.html).

```bash
apt-get install apt-transport-https openjdk-8-jre-headless uuid-runtime pwgen -y
wget -qO - https://packages.elastic.co/GPG-KEY-elasticsearch | apt-key add -
echo "deb https://packages.elastic.co/elasticsearch/5.x/debian stable main" | tee -a /etc/apt/sources.list.d/elasticsearch-5.x.list
apt-get update && apt-get install elasticsearch
sed -i s/#cluster.name:.*/cluster.name:\ grafana/ /etc/elasticsearch/elasticsearch.yml
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service
```

### <a name="install-grafana"></a>Installieren von Grafana

Führen Sie die folgenden Befehle zum Installieren und Ausführen von Grafana aus:

```bash
wget https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_4.5.1_amd64.deb
sudo apt-get install -y adduser libfontconfig
sudo dpkg -i grafana_4.5.1_amd64.deb
sudo service grafana-server start
```

Weitere Informationen finden Sie unter [Installieren unter Debian/Ubuntu](https://docs.grafana.org/installation/debian/).

#### <a name="add-the-elasticsearch-server-as-a-data-source"></a>Hinzufügen des Elasticsearch-Servers als Datenquelle

Als Nächstes müssen Sie den Elasticsearch-Index mit Datenflussprotokollen als Datenquelle hinzufügen. Sie können eine Datenquelle hinzufügen, indem Sie **Add data source** auswählen und das Formular mit den entsprechenden Informationen ausfüllen. Ein Beispiel dieser Konfiguration finden Sie im folgenden Screenshot:

![Hinzufügen einer Datenquelle](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig2.png)

#### <a name="create-a-dashboard"></a>Erstellen eines Dashboards

Nachdem Sie Grafana nun erfolgreich so konfiguriert haben, dass Daten aus dem Elasticsearch-Index mit den NSG-Datenflussprotokollen gelesen werden, können Sie Dashboards erstellen und personalisieren. Wählen Sie zum Erstellen eines neuen Dashboards **Create your first dashboard** aus. Die folgende Beispielkonfiguration eines Graphen zeigt nach NSG-Regel segmentierte Datenflüsse:

![Dashboardgraph](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig3.png)

Der folgende Screenshot zeigt einen Graphen und ein Diagramm der wichtigsten Datenflüsse und ihrer Häufigkeit. Datenflüsse werden auch nach NSG-Regel und nach Entscheidung angezeigt. Grafana ist überaus anpassbar, weshalb es ratsam ist, Dashboards entsprechend Ihren Überwachungsanforderungen zu erstellen. Das folgende Beispiel zeigt ein typisches Dashboard:

![Dashboardgraph](./media/network-watcher-nsg-grafana/network-watcher-nsg-grafana-fig4.png)

## <a name="conclusion"></a>Zusammenfassung

Durch die Integration von Network Watcher, Elasticsearch und Grafana haben Sie jetzt eine einfache und zentrale Möglichkeit zum Verwalten und Visualisieren von NSG-Datenflussprotokollen und anderer Daten. Grafana verfügt über eine Reihe weiterer leistungsfähiger Grafikfunktionen, die auch zur weiteren Verwaltung von Datenflussprotokollen und zum besseren Verständnis des Netzwerkdatenverkehrs genutzt werden können. Nachdem Sie eine Grafana-Instanz eingerichtet und mit Azure verbunden haben, können Sie die anderen gebotenen Funktionen weiter erkunden.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr zur Verwendung von [Network Watcher](network-watcher-monitoring-overview.md).

