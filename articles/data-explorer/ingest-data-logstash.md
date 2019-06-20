---
title: Erfassen von Daten aus Logstash in Azure Data Explorer
description: In diesem Artikel erfahren Sie, wie Sie Daten aus Logstash in Azure Data Explorer erfassen (laden).
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494545"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Erfassen von Daten aus Logstash in Azure Data Explorer

[Logstash](https://www.elastic.co/products/logstash) ist eine serverseitige Open-Source-Datenverarbeitungspipeline, die gleichzeitig Daten aus zahlreichen Quellen erfasst, transformiert und anschließend an Ihren bevorzugten Stash sendet. In diesem Artikel werden Sie diese Daten an Azure Data Explorer senden. Dabei handelt es sich um einen schnellen und hochgradig skalierbaren Dienst zur Untersuchung von Protokoll- und Telemetriedaten. Sie erstellen eine Tabelle und eine Datenzuordnung in einem Testcluster und weisen Logstash anschließend an, Daten an die Tabelle zu senden und die Ergebnisse zu überprüfen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Sollten Sie keins besitzen, erstellen Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.
* [Testcluster und Datenbank](create-cluster-database-portal.md) für Azure Data Explorer
* Version 6 von Logstash ([Installationsanleitung](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html))

## <a name="create-a-table"></a>Erstellen einer Tabelle

Wenn Sie über einen Cluster und eine Datenbank verfügen, können Sie als Nächstes eine Tabelle erstellen.

1. Führen Sie in Ihrem Datenbank-Abfragefenster den folgenden Befehl aus, um eine Tabelle erstellen:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Führen Sie den folgenden Befehl aus, um sich zu vergewissern, dass die neue Tabelle `logs` erstellt wurde und leer ist:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Erstellen einer Zuordnung

Die Zuordnung wird von Azure Data Explorer verwendet, um die eingehenden Daten in das Zieltabellenschema zu transformieren. Der folgende Befehl erstellt eine neue Zuordnung namens `basicmsg`, die Eigenschaften aus den eingehenden JSON-Daten (auf der Grundlage von `path`) extrahiert und sie in `column` ausgibt.

Führen Sie im Abfragefenster den folgenden Befehl aus:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Installieren des Logstash-Ausgabe-Plug-Ins

Das Logstash-Ausgabe-Plug-In kommuniziert mit Azure Data Explorer und sendet die Daten an den Dienst.
Führen Sie im Logstash-Stammverzeichnis den folgenden Befehl aus, um das Plug-In zu installieren:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Konfigurieren von Logstash, um ein Beispieldataset zu generieren

Logstash kann Beispielereignisse zum Testen einer End-to-End-Pipeline generieren.
Falls Sie Logstash bereits verwenden und Zugriff auf Ihren eigenen Ereignisdatenstrom haben, fahren Sie direkt mit dem nächsten Abschnitt fort. 

> [!NOTE]
> Ändern Sie bei Verwendung Ihrer eigenen Daten die Tabelle und die Zuordnungsobjekte, die in den vorherigen Schritten definiert wurden.

1. Bearbeiten Sie eine neue Textdatei, um sie mit den erforderlichen Pipelineeinstellungen zu versehen (mithilfe von vi):

    ```sh
    vi test.conf
    ```

1. Fügen Sie die folgenden Einstellungen ein, um Logstash anzuweisen, 1.000 Testereignisse zu generieren:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Diese Konfiguration enthält auch das Eingabe-Plug-In `stdin`, das es Ihnen ermöglicht, weitere Nachrichten zu schreiben. (Drücken Sie die*EINGABETASTE*, um die Nachrichten an die Pipeline zu übermitteln.)

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Konfigurieren von Logstash, um Daten an Azure Data Explorer zu senden

Fügen Sie die folgenden Einstellungen in die Konfigurationsdatei aus dem vorherigen Schritt ein. Ersetzen Sie die Platzhalter jeweils durch die entsprechenden Werte für Ihr Setup. Weitere Informationen finden Sie unter [HowTo: Creating an AAD Application](/azure/kusto/management/access-control/how-to-provision-aad-app) (Erstellen einer AAD-Anwendung). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Parametername | BESCHREIBUNG |
| --- | --- |
| **path** | Das Logstash-Plug-In schreibt Ereignisse vor dem Senden an Azure Data Explorer in temporäre Dateien. Dieser Parameter enthält einen Pfad, an dem die Dateien geschrieben werden sollen, sowie einen Zeitausdruck für die Dateirotation, um einen Uploadvorgang an den Azure Data Explorer-Dienst auszulösen.|
| **ingest_url** | Der Kusto-Endpunkt für die erfassungsbezogene Kommunikation.|
| **app_id**, **app_key** und **app_tenant**| Anmeldeinformationen für die Verbindungsherstellung mit Azure Data Explorer. Wichtig: Verwenden Sie eine Anwendung mit Erfassungsberechtigungen. |
| **database**| Name der Zieldatenbank für Ereignisse. |
| **Tabelle** | Name der Zieltabelle für Ereignisse. |
| **mapping** | Mit dem mapping-Parameter wird die JSON-Zeichenfolge eines eingehenden Ereignisses dem korrekten Zeilenformat zugeordnet (also definiert, welche Eigenschaft in welche Spalte eingefügt wird). |

## <a name="run-logstash"></a>Ausführen von Logstash

Nun können Sie Logstash ausführen und die Einstellungen testen.

1. Führen Sie im Logstash-Stammverzeichnis den folgenden Befehl aus:

    ```sh
    bin/logstash -f test.conf
    ```

    Auf dem Bildschirm sollten Informationen und anschließend die 1.000 durch die Beispielkonfiguration generierten Nachrichten angezeigt werden. An dieser Stelle können Sie manuell weitere Nachrichten eingeben.

1. Führen Sie nach ein paar Minuten die folgende Data Explorer-Abfrage aus, um die Nachrichten in der von Ihnen definierten Tabelle anzuzeigen:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Drücken Sie STRG+C, um Logstash zu beenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie in Ihrer Datenbank den folgenden Befehl aus, um die Tabelle `logs` zu bereinigen:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Nächste Schritte

* [Write queries](write-queries.md) (Schreiben von Abfragen)