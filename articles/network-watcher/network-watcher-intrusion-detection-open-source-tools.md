---
title: Durchführen der Erkennung von Netzwerkangriffen mit Open Source-Tools
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird beschrieben, wie Sie Azure Network Watcher und Open Source-Tools zum Durchführen der Erkennung von Netzwerkangriffen verwenden.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 6834f9cc8202f49f53c20f3941945bba4a4b2efd
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026745"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Durchführen der Erkennung von Netzwerkangriffen mit Network Watcher und Open Source-Tools

Paketerfassungen sind eine wichtige Komponente zum Implementieren von Systemen zur Erkennung von Netzwerkangriffen (Intrusion Detection Systems, IDS) und Durchführen der Sicherheitsüberwachung für Netzwerke (Network Security Monitoring, NSM). Es gibt mehrere Open Source-Tools für den IDS-Bereich, mit denen Paketerfassungen verarbeitet werden können und nach Signaturen von möglichen Netzwerkangriffen und schädlichen Aktivitäten gesucht werden kann. Mit den von Network Watcher bereitgestellten Paketerfassungen können Sie Ihr Netzwerk im Hinblick auf schädliche Eindringversuche oder Sicherheitsrisiken analysieren.

Ein Open Source-Tool dieser Art ist Suricata. Es handelt sich um eine IDS-Engine, bei der Regelsätze zum Überwachen von Netzwerkdatenverkehr verwendet und Warnungen ausgelöst werden, wenn verdächtige Ereignisse eintreten. Suricata verfügt über eine Multithread-Engine, mit der Analysen des Netzwerkdatenverkehrs mit erhöhter Geschwindigkeit und Effizienz durchgeführt werden können. Weitere Details zu Suricata und seinen Funktionen finden Sie auf der entsprechenden Website unter https://suricata-ids.org/.

## <a name="scenario"></a>Szenario

In diesem Artikel wird beschrieben, wie Sie Ihre Umgebung einrichten, um die Erkennung von Netzwerkangriffen mit Network Watcher, Suricata und Elastic Stack durchzuführen. Mithilfe von Network Watcher erhalten Sie die Paketerfassungen, die für die Erkennung von Netzwerkangriffen benötigt werden. Mit Suricata werden die Paketerfassungen verarbeitet und Warnungen basierend auf Paketen ausgelöst, für die sich eine Überstimmung mit dem angegebenen Regelsatz für Bedrohungen ergibt. Diese Warnungen werden in einer Protokolldatei auf Ihrem lokalen Computer gespeichert. Mit Elastic Stack können die von Suricata generierten Protokolle indiziert und zum Erstellen eines Kibana-Dashboards verwendet werden. So erhalten Sie eine visuelle Darstellung der Protokolle und ein Werkzeug zum schnellen Gewinnen von Erkenntnissen zu potenziellen Sicherheitsrisiken des Netzwerks.  

![Einfaches Webanwendungsszenario][1]

Beide Open Source-Tools können auf einem virtuellen Azure-Computer eingerichtet werden, sodass Sie diese Analyse in Ihrer eigenen Azure-Netzwerkumgebung durchführen können.

## <a name="steps"></a>Schritte

### <a name="install-suricata"></a>Installieren von Suricata

Informationen zu allen anderen Installationsmethoden finden Sie unter https://suricata.readthedocs.io/en/suricata-5.0.2/quickstart.html#installation

1. Führen Sie im Befehlszeilenterminal Ihrer VM die folgenden Befehle aus:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Führen Sie zum Überprüfen Ihrer Installation den Befehl `suricata -h` aus, um die vollständige Liste mit den Befehlen anzuzeigen.

### <a name="download-the-emerging-threats-ruleset"></a>Herunterladen des Regelsatzes für neue Bedrohungen (Emerging Threats)

Bisher verfügen wir noch nicht über Regeln, die von Suricata ausgeführt werden können. Sie können Ihre eigenen Regeln erstellen, falls es bestimmte Bedrohungen für Ihr Netzwerk gibt, die Sie erkennen möchten. Sie können aber auch entwickelte Regelsätze unterschiedlicher Anbieter nutzen, z.B. Emerging Threats oder VRT-Regeln von Snort. Wir verwenden hier den frei erhältlichen Emerging Threats-Regelsatz:

Laden Sie den Regelsatz herunter, und kopieren Sie ihn in das Verzeichnis:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Verarbeiten von Paketerfassungen mit Suricata

Führen Sie den folgenden Befehl aus, um Paketerfassungen mit Suricata zu verarbeiten:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Die sich ergebenden Warnungen können Sie der Datei „fast.log“ entnehmen:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Einrichten des Elastic Stack

Die von Suricata generierten Protokolle enthalten zwar wertvolle Informationen zu den Abläufen im Netzwerk, aber diese Protokolldateien sind nicht sehr einfach zu lesen und zu verstehen. Durch das Verbinden von Suricata mit dem Elastic Stack können wir ein Kibana-Dashboard erstellen, in dem wir die Protokolle durchsuchen, grafisch darstellen, analysieren und auswerten können.

#### <a name="install-elasticsearch"></a>Installieren von Elasticsearch

1. Elastic Stack ab Version 5.0 erfordert Java 8. Führen Sie den Befehl `java -version` aus, um Ihre Version zu überprüfen. Wenn Java nicht installiert ist, lesen Sie die Dokumentation zu den [von Azure unterstützten JDKs](/azure/developer/java/fundamentals/java-jdk-long-term-support).

1. Laden Sie das richtige Binärpaket für Ihr System herunter:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andere Methoden zur Installation finden Sie auf der [Installationsseite von Elasticsearch](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html).

1. Sie überprüfen die Ausführung von Elasticsearch mit dem Befehl:

    ```
    curl http://127.0.0.1:9200
    ```

    Daraufhin sollte eine Antwort angezeigt werden, die etwa wie folgt aussieht:

    ```
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

Weitere Anweisungen zum Installieren von Elasticsearch finden Sie auf der Seite [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html).

### <a name="install-logstash"></a>Installieren von Logstash

1. Führen Sie die folgenden Befehle zum Installieren von Logstash aus:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Als Nächstes konfigurieren Sie Logstash so, dass die Ausgabe der Datei „eve.json“ gelesen wird. Erstellen Sie die Datei „logstash.conf“ wie folgt:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Fügen Sie der Datei den folgenden Inhalt hinzu (achten Sie darauf, dass der Pfad zur Datei „eve.json“ richtig angegeben ist):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Stellen Sie sicher, dass Sie für die Datei „eve.json“ die richtigen Berechtigungen vergeben, damit die Datei von Logstash erfasst werden kann.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Führen Sie zum Starten von Logstash folgenden Befehl aus:

    ```
    sudo /etc/init.d/logstash start
    ```

Weitere Anweisungen zum Installieren von Logstash finden Sie in der [offiziellen Dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html).

### <a name="install-kibana"></a>Installieren von Kibana

1. Führen Sie die folgenden Befehle zum Installieren von Kibana aus:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Verwenden Sie zum Ausführen von Kibana diese Befehle:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Navigieren Sie zum Anzeigen Ihrer Kibana-Weboberfläche zu `http://localhost:5601`.
1. In diesem Szenario wird für die Suricata-Protokolle das Indexmuster „logstash-*“ verwendet.

1. Wenn Sie das Kibana-Dashboard remote anzeigen möchten, erstellen Sie eine eingehende NSG-Regel, die den Zugriff auf **Port 5601** erlaubt.

### <a name="create-a-kibana-dashboard"></a>Erstellen eines Kibana-Dashboards

Für diesen Artikel stellen wir ein Beispieldashboard bereit, in dem Sie Trends und Details in den Warnungen anzeigen können.

1. Laden Sie die Dashboarddatei [hier](https://aka.ms/networkwatchersuricatadashboard), die Visualisierungsdatei [hier](https://aka.ms/networkwatchersuricatavisualization) und die Datei mit der gespeicherten Suche [hier](https://aka.ms/networkwatchersuricatasavedsearch) herunter.

1. Navigieren Sie auf der Registerkarte **Management** (Verwaltung) von Kibana zu **Saved Objects** (Gespeicherte Objekte), und importieren Sie alle drei Dateien. Sie können dann auf der Registerkarte **Dashboard** das Beispieldashboard öffnen und laden.

Sie können auch eigene Visualisierungen und Dashboards erstellen, die auf die für Sie relevanten Metriken zugeschnitten sind. Weitere Informationen zum Erstellen von Kibana-Visualisierungen finden Sie in der [offiziellen Dokumentation](https://www.tutorialspoint.com/kibana/kibana_create_visualization.htm) von Kibana.

![Kibana-Dashboard][2]

### <a name="visualize-ids-alert-logs"></a>Visualisieren von IDS-Warnungsprotokollen

Das Beispieldashboard enthält mehrere Visualisierungen der Suricata-Warnungsprotokolle:

1. Alerts by GeoIP (Warnungen nach GeoIP): Karte zur Verteilung von Warnungen nach Ursprungsland/Ursprungsregion basierend auf dem geografischen Standort (anhand der IP-Adresse).

    ![GeoIP][3]

1. Top 10 Alerts (Top 10 der Warnungen): Zusammenfassung der zehn am häufigsten ausgelösten Warnungen mit einer Beschreibung. Wenn Sie auf eine Warnung klicken, wird die Anzeige im Dashboard nach den Informationen der jeweiligen Warnung gefiltert.

    ![Abbildung 4][4]

1. Number of Alerts (Anzahl von Warnungen): Gesamtzahl von Warnungen, die vom Regelsatz ausgelöst wurden.

    ![Abbildung 5][5]

1. Top 20 Source/Destination IPs/Ports (Top 20 der Quell-/Ziel-IP-Adressen/-Ports): Kreisdiagramme mit den Top 20 der IP-Adressen und Ports, für die Warnungen ausgelöst wurden. Sie können nach bestimmten IP-Adressen oder Ports filtern, um zu ermitteln, wie viele und welche Arten von Warnungen ausgelöst wurden.

    ![Abbildung 6][6]

1. Alert Summary (Benachrichtigungszusammenfassung): Tabelle mit einer Übersicht über bestimmte Details jeder einzelnen Warnung. Sie können diese Tabelle anpassen, um für jede Warnung andere Parameter anzuzeigen, die für Sie von Interesse sind.

    ![Abbildung 7][7]

Weitere Informationen zur Erstellung von benutzerdefinierten Visualisierungen und Dashboards finden Sie in der [offiziellen Kibana-Dokumentation](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Zusammenfassung

Indem Sie die von Network Watcher bereitgestellten Paketerfassungen mit Open Source-Tools wie Suricata kombinieren, können Sie die Erkennung von Netzwerkangriffen für einen großen Bereich von Bedrohungen durchführen. Mit diesen Dashboards können Sie in Ihrem Netzwerk schnell Trends und Anomalien erkennen und die Daten untersuchen, um die Grundursachen von Warnungen zu ermitteln, z.B. schädliche Benutzer-Agents oder anfällige Ports. Mit diesen extrahierten Daten können Sie fundierte Entscheidungen darüber treffen, wie Sie auf schädliche Eindringversuche reagieren und Ihr Netzwerk davor schützen und Regeln erstellen, um zukünftige Angriffe auf Ihr Netzwerk zu verhindern.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich unter [Verwenden der Paketerfassung für die proaktive Netzwerküberwachung mit Azure Functions](network-watcher-alert-triggered-packet-capture.md) darüber, wie Sie Paketerfassungen basierend auf Warnungen auslösen.

Erfahren Sie unter [Visualisieren der Datenflussprotokolle von Netzwerksicherheitsgruppen mit Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md), wie Sie Ihre NSG-Datenflussprotokolle mit Power BI visualisieren.



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png