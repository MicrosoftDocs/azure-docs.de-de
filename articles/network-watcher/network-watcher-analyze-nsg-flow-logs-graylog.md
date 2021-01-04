---
title: Analysieren der Datenflussprotokolle von Azure-Netzwerksicherheitsgruppen – Graylog | Microsoft-Dokumentation
description: Hier wird erläutert, wie Datenflussprotokolle für Netzwerksicherheitsgruppen in Azure mithilfe von Network Watcher und Graylog verwaltet und analysiert werden.
services: network-watcher
documentationcenter: na
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2017
ms.author: damendo
ms.openlocfilehash: 8b363d90d05e95b017c3a655b57dbabc3712a155
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965543"
---
# <a name="manage-and-analyze-network-security-group-flow-logs-in-azure-using-network-watcher-and-graylog"></a>Verwalten und Analysieren von Datenflussprotokollen für Netzwerksicherheitsgruppen in Azure mithilfe von Network Watcher und Graylog

[Datenflussprotokolle von Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md) enthalten Informationen für ein besseres Verständnis von ein- und ausgehendem IP-Datenverkehr in Azure-Netzwerkschnittstellen. Datenflussprotokolle zeigen ausgehende und eingehende Datenflüsse pro Netzwerksicherheitsgruppe, die Netzwerkschnittstelle, auf die sich der Datenfluss bezieht, 5-Tupel-Informationen über den Datenfluss (Quell-/Ziel-IP, Quell-/Zielport, Protokoll) und Informationen zu zugelassenem oder verweigertem Datenverkehr.

Sie können für die NSGs in Ihrem Netzwerk die Datenflussprotokollierung aktivieren. Mehrere Netzwerksicherheitsgruppen mit aktivierter Datenflussprotokollierung machen es aufwendig, Ihre Protokolle zu analysieren und einen Einblick in sie zu erhalten. Dieser Artikel bietet eine Projektmappe, um diese Datenflussprotokolle von Netzwerksicherheitsgruppen mit Graylog und Logstash zu verwalten. Hierbei handelt es sich um ein Open Source-Tool zur Protokollverwaltung und -analyse und eine serverseitige Open Source-Datenverarbeitungspipeline.

> [!Warning]
> In den folgenden Schritten werden Flowprotokolle der Version 1 verwendet. Ausführliche Informationen finden Sie unter [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md). Die folgenden Anweisungen funktionieren ohne Änderungen nicht mit Version 2 der Protokolldateien.

## <a name="scenario"></a>Szenario

Datenflussprotokolle von Netzwerksicherheitsgruppen werden mithilfe von Network Watcher aktiviert. Datenflussprotokolle in Azure Blob Storage. Ein Logstash-Plug-In wird verwendet, um mit Datenflussprotokollen in Blob Storage eine Verbindung herzustellen, diese zu verarbeiten und an Graylog zu senden. Nachdem die Datenflussprotokolle in Graylog gespeichert wurden, können sie analysiert und in benutzerdefinierten Dashboards visualisiert werden.

![Graylog-Workflow](./media/network-watcher-analyze-nsg-flow-logs-graylog/workflow.png)

## <a name="installation-steps"></a>Installationsschritte

### <a name="enable-network-security-group-flow-logging"></a>Aktivieren der Datenflussprotokollierung für Netzwerksicherheitsgruppen

Für dieses Szenario müssen Sie die NSG-Datenflussprotokollierung für mindestens eine Netzwerksicherheitsgruppe in Ihrem Konto aktivieren. Eine Anleitung zum Aktivieren von NSG-Datenflussprotokollen finden Sie im Artikel [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](network-watcher-nsg-flow-logging-overview.md).

### <a name="setting-up-graylog"></a>Einrichten von Graylog

Bei diesem Beispiel sind Graylog und Logstash auf einem in Azure bereitgestellten Ubuntu 14.04-Server konfiguriert.

- Sie finden in der [Dokumentation](https://docs.graylog.org/en/2.2/pages/installation/os/ubuntu.html) von Graylog Schritt-für-Schritt-Anweisungen zum Installieren auf Ubuntu.
- Stellen Sie sicher, dass Sie auch die Graylog-Webschnittstelle gemäß der [Dokumentation](https://docs.graylog.org/en/2.2/pages/configuration/web_interface.html#configuring-webif) konfigurieren.

Dieses Beispiel verwendet die Graylog-Mindestkonfiguration (d.h. eine einzelne GraylogInstanz), aber Graylog kann je nach System und Produktionsanforderungen ressourcenübergreifend entworfen werden. Weitere Informationen zu Architekturüberlegungen oder architektonische Leitfaden finden Sie in der [Dokumentation](https://docs.graylog.org/en/2.2/pages/architecture.html) von Graylog und im [architektonischen Leitfaden](https://www.slideshare.net/Graylog/graylog-engineering-design-your-architecture).

Graylog kann in vielerlei Hinsicht je nach Plattform und Vorlieben installiert werden. Eine vollständige Liste der möglichen Installationsmethoden finden Sie in Graylogs offizieller [Dokumentation](https://docs.graylog.org/en/2.2/pages/installation.html). Die Graylog-Serveranwendung wird auf Linux-Verteilungen ausgeführt und hat die folgenden Voraussetzungen:

-  Java SE 8 oder höher – [Azul Azure JDK-Dokumentation](/azure/developer/java/fundamentals/java-jdk-long-term-support)
-  Elastic Search 2.x (2.1.0 oder höher) – [Elasticsearch-Installationsdokumentation](https://www.elastic.co/guide/en/elasticsearch/reference/2.4/_installation.html)
-  MongoDB 2.4 oder höher – [MongoDB-Installationsdokumentation](https://docs.mongodb.com/manual/administration/install-on-linux/)

### <a name="install-logstash"></a>Installieren von Logstash

Mithilfe von Logstash können Sie die JSON-formatierten Datenflussprotokolle auf Flusstupelebene vereinfachen. Das Vereinfachen der Datenflussprotokolle erleichtert die Organisation und Suche in Graylog.

1. Führen Sie die folgenden Befehle zum Installieren von Logstash aus:

   ```bash
   curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
   sudo dpkg -i logstash-5.2.0.deb
   ```

2. Konfigurieren Sie Logstash, um die Datenflussprotokolle zu analysieren und an Graylog zu senden. Erstellen Sie eine Logstash.conf-Datei:

   ```bash
   sudo touch /etc/logstash/conf.d/logstash.conf
   ```

3. Fügen Sie der Datei folgenden Inhalt hinzu. Ändern Sie die hervorgehobenen Werte entsprechend Ihrer Speicherkontodetails:

   ```
    input {
        azureblob
        {
            storage_account_name => "mystorageaccount"
            storage_access_key => "NrUZmx7pJSKaRJzvQbeiZWi5nBRWOTr7Wwr9DrvK7YtDBrADYxT1y0oEExtSlkDnGRt7qcRiZzEBCCyRYND8SxSt"
            container => "insights-logs-networksecuritygroupflowevent"
            registry_create_policy => "start_over"
            codec => "json"
            file_head_bytes => 21
            file_tail_bytes => 9
            # Possible options: `do_not_break`, `with_head_tail`, `without_head_tail`
            break_json_down_policy  => 'with_head_tail'
            break_json_batch_count => 2
            interval => 5
        }
    }
    
    filter {
        split { field => "[records]" }
        split { field => "[records][properties][flows]"}
        split { field => "[records][properties][flows][flows]"}
        split { field => "[records][properties][flows][flows][flowTuples]"
    }
    
     mutate {
        split => { "[records][resourceId]" => "/"}
        add_field =>{
                    "Subscription" => "%{[records][resourceId][2]}"
                    "ResourceGroup" => "%{[records][resourceId][4]}"
                    "NetworkSecurityGroup" => "%{[records][resourceId][8]}"
        }
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
        udp {
            host => "127.0.0.1"
            port => 12201
        }
    }
    ```
   Die bereitgestellte Konfigurationsdatei von Logstash besteht aus drei Teilen: Eingabe, Filter und Ausgabe. Der Eingabebereich bezeichnet die Eingabequelle der Protokolle, die Logstash verarbeitet. In diesem Fall wird ein Azure-Blog-Eingabe-Plug-In verwendet (das in den nächsten Schritten installiert wird). Es erlaubt uns, auf die JSON-Dateien des NSG-Datenflussprotokolls zuzugreifen, die in Blob Storage gespeichert sind.

Der Filterabschnitt vereinfacht dann jede Datenfluss-Protokolldatei so, dass jedes einzelnen Flusstupel und die ihm zugeordneten Eigenschaften zu einem gesonderten Logstash-Ereignis werden.

Schließlich leitet der Ausgabeteil jedes Logstash-Ereignis an den Graylog-Server weiter. Sie können die CONF-Datei von Logstash nach Wunsch an Ihre spezifischen Anforderungen anpassen.

   > [!NOTE]
   > In der vorherigen Konfigurationsdatei wird davon ausgegangen, dass der Graylog-Server auf der Loopback-IP-Adresse 127.0.0.1 des lokalen Hosts konfiguriert wurde. Andernfalls stellen Sie sicher, dass der Hostparameter im Ausgabebereich auf die richtige IP-Adresse eingestellt ist.

Weitere Anweisungen zum Installieren von Logstash finden Sie in der [Dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html) von Logstash.

### <a name="install-the-logstash-input-plug-in-for-azure-blob-storage"></a>Installieren des Logstash-Eingangs-Plug-Ins für Azure Blob Storage

Mithilfe dieses Logstash-Plug-Ins können Sie direkt auf die Datenflussprotokolle im jeweiligen Blob Storage-Konto zugreifen. Sie installieren dieses Plug-In, indem Sie über das Logstash-Standardinstallationsverzeichnis (in diesem Fall /usr/share/logstash/bin) folgenden Befehl ausführen:

```bash
cd /usr/share/logstash/bin
sudo ./logstash-plugin install logstash-input-azureblob
```

Weitere Informationen zu diesem Plug-In finden Sie in der [Dokumentation](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob).

### <a name="set-up-connection-from-logstash-to-graylog"></a>Einrichten der Verbindung von Logstash zu Graylog

Nachdem Sie mit Logstash eine Verbindung mit den Datenflussprotokollen hergestellt und den Graylog-Server eingerichtet haben, müssen Sie Graylog zum Akzeptieren von eingehenden Protokolldateien konfigurieren.

1. Navigieren Sie mit der URL, die Sie konfiguriert haben, zur Weboberfläche Ihres Graylog-Servers. Sie können auf die Schnittstelle zugreifen, indem Sie in Ihrem Browser zu `http://<graylog-server-ip>:9000/` navigieren

2. Wählen Sie zum Navigieren auf die Konfigurationsseite das Dropdown-Menü **System** in der oberen Navigationsleiste auf der rechten Seite, und klicken Sie dann auf **Eingaben**.
   Alternativ können Sie zu `http://<graylog-server-ip>:9000/system/inputs` navigieren

   ![Erste Schritte](./media/network-watcher-analyze-nsg-flow-logs-graylog/getting-started.png)

3. Um die neue Eingabe zu starten, wählen Sie *GELF-UDP* in der Dropdown-Liste **Eingabe auswählen** aus, und füllen Sie dann das Formular aus. GELF steht für Graylog Extended Log Format (erweitertes Graylog-Protokollformat). Das GELF-Format wird von Graylog entwickelt. Weitere Informationen zu dessen Vorteilen finden Sie unter der Graylog-[Dokumentation](https://docs.graylog.org/en/2.2/pages/gelf.html).

   Stellen Sie sicher, dass Sie die Eingabe an die IP-Adresse binden, auf der Sie Ihren Graylog-Server konfiguriert haben. Die IP-Adresse sollte mit dem **Host**-Feld der UDP-Ausgabe der Logstash-Konfigurationsdatei übereinstimmen. Der Standardport sollte *12201* sein. Stellen Sie sicher, dass der Port dem **Port**-Feld in der UDP-Ausgabe entspricht, die in der Konfigurationsdatei von Logstash festgelegt ist.

   ![Screenshot der Graylog-Eingaben mit Optionen zum Starten und Suchen von Eingaben.](./media/network-watcher-analyze-nsg-flow-logs-graylog/inputs.png)

   Nachdem Sie die Eingabe starten, sollten Sie eswie in der folgenden Abbildung gezeigt, im Abschnitt **Lokale Eingaben** sehen:

   ![Screenshot mit dem Abschnitt „Lokale Eingaben“, der die von Ihnen gestartete Eingabe enthält.](./media/network-watcher-analyze-nsg-flow-logs-graylog/local-inputs.png)

   Weitere Informationen zum Graylog-Nachrichteneingaben finden Sie in der [Dokumentation](https://docs.graylog.org/en/2.2/pages/sending_data.html#what-are-graylog-message-inputs).

4. Sobald diese Konfigurationen vorgenommen wurden, können Sie Logstash starten, um den Lesevorgang für Datenflussprotokolle mithilfe des folgenden Befehls zu beginnen: `sudo systemctl start logstash.service`

### <a name="search-through-graylog-messages"></a>Durchsuchen von Graylog-Nachrichten

Nachdem Ihr Graylog-Server genug Zeit zum Erfassen von Nachrichten hatte, können Sie die Meldungen durchsuchen. Überprüfen Sie auf der Konfigurationsseite **Eingaben** die Nachrichten, die an Ihren Graylog-Server gesendet werden, indem Sie auf die Schaltfläche **Empfangene Nachrichten anzeigen** der erstellten GELF-UDP-Eingabe klicken. Sie werden zu einem Bildschirm weitergeleitet, der der folgenden Abbildung ähnelt: 

![Screenshot des Graylog-Servers mit Suchergebnis, Histogramm und Meldungen.](./media/network-watcher-analyze-nsg-flow-logs-graylog/histogram.png)

Durch Klicken auf den blauen Link „% {Message}“ wird jede Nachricht erweitert, um die Parameter der einzelnen Tupel-Datenflüsse anzuzeigen, wie in der folgenden Abbildung gezeigt:

![Screenshot mit Meldungsdetails vom Graylog-Server.](./media/network-watcher-analyze-nsg-flow-logs-graylog/messages.png)

Standardmäßig sind alle Nachrichtenfelder in der Suche enthalten, wenn Sie kein bestimmtes Nachrichtenfeld für die Suche auswählen. Wenn Sie nach bestimmten Nachrichten suchen (d.h. – Datenflusstupel aus einer bestimmten Quell-IP), können Sie die Graylog-Suchabfragesprache wie [dokumentiert](https://docs.graylog.org/en/2.2/pages/queries.html) verwenden

## <a name="analyze-network-security-group-flow-logs-using-graylog"></a>Analysieren von Datenflussprotokollen für Netzwerksicherheitsgruppen mit Graylog

Nun, da Graylog eingerichtet ist und ausgeführt wird, können Sie einige der Funktionen verwenden, um Ihre Datenflussprotokolldaten besser zu verstehen. Eine solche Methode ist die Verwendung von Dashboards zum Erstellen bestimmter Datenansichten.

### <a name="create-a-dashboard"></a>Erstellen eines Dashboards

1. Wählen Sie in der oberen Navigationsleiste **Dashboards** aus, oder navigieren Sie zu`http://<graylog-server-ip>:9000/dashboards/`

2. Klicken Sie dort auf die grüne **Dashboard erstellen**-Schaltfläche, und füllen Sie das kurze Formular mit dem Titel und der Beschreibung des Dashboards aus. Klicken Sie auf die **Speichern**-Schaltfläche, um das neue Dashboard zu erstellen. Sie sehen ein Dashboard ähnlich dem folgenden Bild:

    ![Screenshot mit Dashboards des Graylog-Servers mit den Optionen zum Erstellen und Bearbeiten von Dashboards.](./media/network-watcher-analyze-nsg-flow-logs-graylog/dashboards.png)

### <a name="add-widgets"></a>Hinzufügen von Widgets

Sie können auf den Titel des Dashboards klicken, um ihn anzuzeigen. Im Moment ist er jedoch leer, da wir noch keine Widgets hinzugefügt haben. Ein einfacher und nützlicher Widgettyp, der zum Dashboard hinzugefügt werden kann, sind die Diagramme **Quick Vales (Kurze Werte)** , die eine Liste der Werte für das ausgewählte Feld und deren Verteilung anzeigen.

1. Navigieren Sie zurück zu den Suchergebnissen der UDP-Eingabe, die Datenflussprotokolle empfängt, indem Sie **Suche** in der oberen Navigationsleiste auswählen.

2. Im Bereich **Suchergebnis** auf der linken Seite des Bildschirms finden Sie die Registerkarte **Felder**, die diverse Felder jeder eingehenden Datenflusstupelnachricht auflistet.

3. Wählen Sie alle gewünschten Parameter zur Visualisierung aus (in diesem Beispiel ist die Quell-IP ausgewählt). Um die Liste der möglichen Widgets anzuzeigen, klicken Sie auf den blauen Dropdown-Pfeil auf der linken Seite des Felds, und wählen Sie dann **Quick Values** (Schnelle Werte) aus, um das Widget zu generieren. Es sollte etwa das folgende Bild angezeigt werden:

   ![Quell-IP](./media/network-watcher-analyze-nsg-flow-logs-graylog/srcip.png)

4. Von dort aus können Sie die Schaltfläche **Zum Dashboard hinzufügen** in der oberen rechten Ecke des Widgets und das entsprechende Dashboard auswählen.

5. Navigieren Sie zurück zum Dashboard, um Widgets anzuzeigen, die Sie gerade hinzugefügt haben.

   Sie können eine Vielzahl von anderen Widgets zu Ihrem Dashboard hinzufügen, z.B. Histogramme und Anzahlen, um wichtige Metriken nachzuverfolgen. Dazu gehört das Beispiel-Dashboard in der folgenden Abbildung:

   ![Datenflussprotokoll-Dashboard](./media/network-watcher-analyze-nsg-flow-logs-graylog/flowlogs-dashboard.png)

    Weitere Erläuterungen zu Dashboards und anderen Widgettypen finden Sie in der [Dokumentation](https://docs.graylog.org/en/2.2/pages/dashboards.html) von Graylog.

Durch die Integration von Network Watcher mit Graylog haben Sie jetzt eine einfache und zentrale Möglichkeit zum Verwalten und Visualisieren von NSG-Datenflussprotokollen. Graylog verfügt über eine Reihe weiterer leistungsfähiger Features wie Datenströme und Warnungen, die auch zur weiteren Verwaltung von Datenflussprotokollen und zum besseren Verständnis des Netzwerkdatenverkehrs genutzt werden können. Nachdem Sie Graylog eingerichtet und mit Azure verbunden haben, können Sie die anderen gebotenen Funktionen weiter erkunden.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie unter [Visualize network security group flows logs with Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md) (Visualisieren der Datenflussprotokolle von Netzwerksicherheitsgruppen mit Power BI), wie Sie Ihre NSG-Datenflussprotokolle mit Power BI visualisieren.