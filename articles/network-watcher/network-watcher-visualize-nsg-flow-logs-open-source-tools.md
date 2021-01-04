---
title: Visualisieren von NSG-Datenflussprotokollen – Elastic Stack
titleSuffix: Azure Network Watcher
description: Verwalten und Analysieren von Datenflussprotokollen für Netzwerksicherheitsgruppen in Azure mithilfe von Network Watcher und Elastic Stack.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: aca8c75f262e472cbc770c052b86d6e760ee449a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026470"
---
# <a name="visualize-azure-network-watcher-nsg-flow-logs-using-open-source-tools"></a>Visualisieren von Azure Network Watcher-NSG-Datenflussprotokollen mit Open-Source-Tools

NSG-Datenflussprotokolle (Netzwerksicherheitsgruppen) enthalten Informationen für ein besseres Verständnis von ein- und ausgehendem IP-Datenverkehr in Netzwerksicherheitsgruppen. Diese Datenflussprotokolle zeigen ausgehende und eingehende Datenflüsse pro Regel, die NIC, auf die sich der Datenfluss bezieht, 5-Tupel-Informationen über den Datenfluss (Quell-/Ziel-IP, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder verweigertem Datenverkehr.

Die manuelle Analyse und Auswertung dieser Datenflussprotokolle kann schwierig sein. Es gibt jedoch mehrere Open-Source-Tools, die zur Visualisierung dieser Daten beitragen können. In diesem Artikel wird eine Lösung für Visualisierung dieser Protokolle mit dem Elastic Stack bereitgestellt, mit der Sie Ihre Datenflussprotokolle schnell in einem Kibana-Dashboard indizieren und visualisieren können.

## <a name="scenario"></a>Szenario

In diesem Artikel richten wir eine Lösung ein, mit der Sie die Datenflussprotokolle von Netzwerksicherheitsgruppen mithilfe des Elastic Stack visualisieren können.  Ein Logstash-Eingangs-Plug-In ruft die Flowprotokolle direkt aus dem für die Flowprotokolle konfigurierten Speicherblob ab. Anschließend werden die Datenflussprotokolle mit dem Elastic Stack indiziert und für das Erstellen eines Kibana-Dashboards verwendet, mit dem die Informationen visualisiert werden.

![Das Diagramm zeigt ein Szenario, in dem Sie Datenflussprotokolle für Netzwerksicherheitsgruppen mit Elastic Stack visualisieren können.][scenario]

## <a name="steps"></a>Schritte

### <a name="enable-network-security-group-flow-logging"></a>Aktivieren der Datenflussprotokollierung für Netzwerksicherheitsgruppen
Für dieses Szenario müssen Sie die NSG-Datenflussprotokollierung für mindestens eine Netzwerksicherheitsgruppe in Ihrem Konto aktivieren. Anweisungen zum Aktivieren von NSG-Datenflussprotokollen finden Sie im Artikel [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md).

### <a name="set-up-the-elastic-stack"></a>Einrichten des Elastic Stack
Durch das Verbinden von NSG-Datenflussprotokollen mit dem Elastic Stack können wir ein Kibana-Dashboard erstellen, in dem wir die Protokolle durchsuchen, grafisch darstellen, analysieren und auswerten können.

#### <a name="install-elasticsearch"></a>Installieren von Elasticsearch

1. Elastic Stack ab Version 5.0 erfordert Java 8. Führen Sie den Befehl `java -version` aus, um Ihre Version zu überprüfen. Wenn Java nicht installiert ist, lesen Sie die Dokumentation zu den [von Azure unterstützten JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support).
2. Laden Sie das richtige Binärpaket für Ihr System herunter:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
   sudo dpkg -i elasticsearch-5.2.0.deb
   sudo /etc/init.d/elasticsearch start
   ```

   Andere Methoden zur Installation finden Sie auf der [Installationsseite von Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html).

3. Sie überprüfen die Ausführung von Elasticsearch mit dem Befehl:

    ```bash
    curl http://127.0.0.1:9200
    ```

    Daraufhin sollte eine Antwort angezeigt werden, die etwa wie folgt aussieht:

    ```json
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Weitere Anweisungen zum Installieren von Elasticsearch finden Sie unter den [Installationsanweisungen](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installieren von Logstash

1. Führen Sie die folgenden Befehle zum Installieren von Logstash aus:

    ```bash
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
2. Im nächsten Schritt muss Logstash für den Zugriff auf die und die Analyse der Flowprotokolle konfiguriert werden. Erstellen Sie die Datei „logstash.conf“ wie folgt:

    ```bash
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

3. Fügen Sie der Datei Folgendes hinzu:

   ```
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

     mutate{
      split => { "[records][resourceId]" => "/"}
      add_field => {"Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"}
      convert => {"Subscription" => "string"}
      convert => {"ResourceGroup" => "string"}
      convert => {"NetworkSecurityGroup" => "string"}
      split => { "[records][properties][flows][flows][flowTuples]" => ","}
      add_field => {
                  "unixtimestamp" => "%{[records][properties][flows][flows][flowTuples][0]}"
                  "srcIp" => "%{[records][properties][flows][flows][flowTuples][1]}"
                  "destIp" => "%{[records][properties][flows][flows][flowTuples][2]}"
                  "srcPort" => "%{[records][properties][flows][flows][flowTuples][3]}"
                  "destPort" => "%{[records][properties][flows][flows][flowTuples][4]}"
                  "protocol" => "%{[records][properties][flows][flows][flowTuples][5]}"
                  "trafficflow" => "%{[records][properties][flows][flows][flowTuples][6]}"
                  "traffic" => "%{[records][properties][flows][flows][flowTuples][7]}"
                  "flowstate" => "%{[records][properties][flows][flows][flowTuples][8]}"
                   "packetsSourceToDest" => "%{[records][properties][flows][flows][flowTuples][9]}"
                   "bytesSentSourceToDest" => "%{[records][properties][flows][flows][flowTuples][10]}"
                   "packetsDestToSource" => "%{[records][properties][flows][flows][flowTuples][11]}"
                   "bytesSentDestToSource" => "%{[records][properties][flows][flows][flowTuples][12]}"
                   }
      convert => {"unixtimestamp" => "integer"}
      convert => {"srcPort" => "integer"}
      convert => {"destPort" => "integer"}        
     }

     date{
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

Weitere Anweisungen zum Installieren von Logstash finden Sie in der [offiziellen Dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-the-logstash-input-plugin-for-azure-blob-storage"></a>Installieren des Logstash-Eingangs-Plug-Ins für Azure Blob Storage

Mit diesem Logstash-Plug-In können Sie direkt auf die Flowprotokolle im jeweiligen Speicherkonto zugreifen. Sie installieren dieses Plug-In, indem Sie über das Logstash-Standardinstallationsverzeichnis (in diesem Fall „/usr/share/logstash/bin“) den folgenden Befehl ausführen:

```bash
logstash-plugin install logstash-input-azureblob
```

Führen Sie zum Starten von Logstash folgenden Befehl aus:

```bash
sudo /etc/init.d/logstash start
```

Weitere Informationen zu diesem Plug-In finden Sie in der [Dokumentation](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="install-kibana"></a>Installieren von Kibana

1. Führen Sie die folgenden Befehle zum Installieren von Kibana aus:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
   tar xzvf kibana-5.2.0-linux-x86_64.tar.gz
   ```

2. Verwenden Sie zum Ausführen von Kibana diese Befehle:

   ```bash
   cd kibana-5.2.0-linux-x86_64/
   ./bin/kibana
   ```

3. Navigieren Sie zum Anzeigen Ihrer Kibana-Weboberfläche zu `http://localhost:5601`.
4. In diesem Szenario wird das Indexmuster „nsg-flow-logs“ für die Datenflussprotokolle verwendet. Sie können das Indexmuster im Abschnitt „output“ der Datei „logstash.conf“ ändern.
5. Wenn Sie das Kibana-Dashboard remote anzeigen möchten, erstellen Sie eine eingehende NSG-Regel, die den Zugriff auf **Port 5601** erlaubt.

### <a name="create-a-kibana-dashboard"></a>Erstellen eines Kibana-Dashboards

Ein Beispieldashboard zum Anzeigen von Trends und Details in den Warnungen ist in der folgenden Abbildung dargestellt:

![Abbildung 1][1]

Laden Sie die [Dashboarddatei](https://aka.ms/networkwatchernsgflowlogdashboard), die [Visualisierungsdatei](https://aka.ms/networkwatchernsgflowlogvisualizations) und die [Datei mit der gespeicherten Suche](https://aka.ms/networkwatchernsgflowlogsearch) herunter.

Navigieren Sie auf der Registerkarte **Management** (Verwaltung) von Kibana zu **Saved Objects** (Gespeicherte Objekte), und importieren Sie alle drei Dateien. Sie können dann auf der Registerkarte **Dashboard** das Beispieldashboard öffnen und laden.

Sie können auch eigene Visualisierungen und Dashboards erstellen, die auf die für Sie relevanten Metriken zugeschnitten sind. Weitere Informationen zum Erstellen von Kibana-Visualisierungen finden Sie in der [offiziellen Dokumentation](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm) von Kibana.

### <a name="visualize-nsg-flow-logs"></a>Visualisieren von NSG-Datenflussprotokollen

Das Beispieldashboard stellt mehrere Visualisierungen der Datenflussprotokolle bereit:

1. Datenfluss nach Entscheidung/Richtung über die Zeit: Zeitraumdiagramme zeigen die Anzahl der Datenflüsse für den Zeitraum an. Sie können Zeiteinheit und Dauer bei beiden Visualisierungen ändern. „Datenflüsse nach Entscheidung“ zeigt den Anteil von Entscheidungen für das Zulassen bzw. Verweigern an, während „Datenflüsse nach Richtung“ den Anteil von eingehendem und ausgehendem Datenverkehr darstellt. Mithilfe dieser Visualisierungen können Sie Datenverkehrstrends über einen Zeitraum auf Spitzen oder ungewöhnliche Muster untersuchen.

   ![Screenshot zeigt ein Beispieldashboard mit Datenflüssen nach Entscheidung und Richtung im Zeitverlauf.][2]

2. Datenflüsse nach Ziel/Quellport: Kreisdiagramme zeigen eine Aufschlüsselung der Datenflüsse an den jeweiligen Ports an. In dieser Ansicht können Sie die am häufigsten verwendeten Ports ermitteln. Wenn Sie im Kreisdiagramm auf einen bestimmten Port klicken, wird das übrige Dashboard nach Datenflüssen über diesen Port gefiltert.

   ![Screenshot zeigt ein Beispieldashboard mit Datenflüssen nach Ziel- und Quellport.][3]

3. Anzahl von Datenflüssen und früheste Protokollzeit: Metriken zeigen die Anzahl der aufgezeichneten Datenflüsse und das Datum des ältesten aufgezeichneten Protokolls.

   ![Screenshot zeigt ein Beispieldashboard mit der Anzahl der Datenflüsse und der frühesten Protokollzeit.][4]

4. Datenflüsse nach NSG und Regel: Ein Balkendiagramm zeigt die Verteilung der Datenflüsse innerhalb der einzelnen NSGs sowie die Verteilung der Regeln in den NSGs. Hier können Sie sehen, welche NSGs und Regeln den meisten Datenverkehr generieren.

   ![Screenshot zeigt ein Beispieldashboard mit Datenflüssen nach NSG und einer Regel.][5]

5. 10 häufigste Quell-/Ziel-IP-Adressen: Balkendiagramme zeigen die zehn häufigsten Quell- und Ziel-IP-Adressen. Sie können diese Diagramme anpassen, um mehr oder weniger IP-Adressen anzuzeigen. Hier sehen Sie die am häufigsten auftretenden IP-Adressen sowie die Entscheidung zum Datenverkehr (zulassen oder verweigern), die für jede IP-Adresse getroffen wurden.

   ![Screenshot zeigt ein Beispieldashboard mit Datenflüssen nach den ersten zehn Ziel- und Quell-IP-Adressen.][6]

6. Datenflusstupel: Diese Tabelle zeigt Informationen innerhalb jedes Datenflusstupels mit der zugehörigen NSG und Regel.

   ![Screenshot zeigt Datenflusstupel in einer Tabelle.][7]

Mithilfe der Abfrageleiste oben im Dashboard können Sie das Dashboard basierend auf beliebige Parameter der Datenflüsse filtern, z.B. nach Abonnement-ID, Ressourcengruppen, Regel oder andere relevante Variablen. Weitere Informationen zu Kibana-Abfragen und -Filtern finden Sie in der [offiziellen Dokumentation](https://www.elastic.co/guide/en/beats/packetbeat/current/kibana-queries-filters.html).

## <a name="conclusion"></a>Zusammenfassung

Durch die Kombination der Datenflussprotokolle von Netzwerksicherheitsgruppen mit dem Elastic Stack steht Ihnen eine leistungsstarke und anpassbare Methode zum Visualisieren des Netzwerkdatenverkehrs zur Verfügung. Mithilfe dieser Dashboards können Sie schnell Einblicke in Ihren Netzwerkdatenverkehr erhalten und teilen. Außerdem können Sie Filter anwenden und potenzielle Anomalien untersuchen. Mit Kibana können Sie diese Dashboards anpassen und geeignete Visualisierungen erstellen, die bestehende Anforderungen an Sicherheit, Überwachung und Compliance erfüllen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie unter [Visualisieren der Datenflussprotokolle von Netzwerksicherheitsgruppen mit Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md), wie Sie Ihre NSG-Datenflussprotokolle mit Power BI visualisieren.

<!--Image references-->

[scenario]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/scenario.png
[1]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-open-source-tools/figure7.png