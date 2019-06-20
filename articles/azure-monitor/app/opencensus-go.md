---
title: OpenCensus Go-Ablaufverfolgung mit Azure Application Insights | Microsoft-Dokumentation
description: Enthält Anweisungen zur Integration der OpenCensus Go-Ablaufverfolgung in die lokale Weiterleitung und Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577860"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Sammeln verteilter Ablaufverfolgungsdaten von Go (Vorschau)

Application Insights unterstützt jetzt die verteilte Ablaufverfolgung von Go-Anwendungen durch die Integration in [OpenCensus](https://opencensus.io) und unsere neue [lokale Weiterleitung](./opencensus-local-forwarder.md). Dieser Artikel führt Sie Schritt für Schritt durch den Prozess der Einrichtung von OpenCensus für Go und die Weiterleitung Ihrer Ablaufverfolgungsdaten an Application Insights.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Abonnement.
- Go muss installiert sein. In diesem Artikel wird Version 1.11 verwendet ([Go herunterladen](https://golang.org/dl/)).
- Führen Sie die Anweisungen zum Installieren der [lokalen Weiterleitung als Windows-Dienst](./opencensus-local-forwarder.md) aus.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-application-insights-resource"></a>Erstellen einer Application Insights-Ressource

Zuerst müssen Sie eine Application Insights-Ressource erstellen, die einen Instrumentierungsschlüssel (ikey) generiert. Der ikey wird dann verwendet, um die lokale Weiterleitung so zu konfigurieren, dass verteilte Ablaufverfolgungsdaten aus Ihrer mit OpenCensus instrumentierten Anwendung an Application Insights gesendet werden.   

1. Klicken Sie auf **Ressource erstellen** > **Entwicklertools** > **Application Insights**.

   ![Hinzufügen einer Application Insights-Ressource](./media/opencensus-Go/0001-create-resource.png)

   Ein Dialogfeld für die Konfiguration wird geöffnet. Füllen Sie die Eingabefelder anhand der Informationen in der folgenden Tabelle aus:

    | Einstellungen        | Wert           | BESCHREIBUNG  |
   | ------------- |:-------------|:-----|
   | **Name**      | Global eindeutiger Wert | Der Name, der die zu überwachende App identifiziert. |
   | **Anwendungstyp** | Allgemein | Der Typ der zu überwachenden App. |
   | **Ressourcengruppe**     | myResourceGroup      | Der Name der neuen Ressourcengruppe, die Application Insights-Daten hosten soll. |
   | **Location** | USA (Ost) | Wählen Sie einen Standort in Ihrer Nähe oder in der Nähe des Standorts, in dem Ihre App gehostet wird. |

2. Klicken Sie auf **Create**.

## <a name="configure-local-forwarder"></a>Konfigurieren der lokalen Weiterleitung

1. Wählen Sie **Übersicht** > **Grundlagen** aus, und kopieren Sie den**Instrumentierungsschlüssel** Ihrer Anwendung.

   ![Screenshot des Instrumentierungsschlüssels](./media/opencensus-Go/0003-instrumentation-key.png)

2. Bearbeiten Sie Ihre `LocalForwarder.config`-Datei, und fügen Sie Ihren Instrumentierungsschlüssel hinzu. Wenn Sie den Anweisungen unter [Voraussetzungen](./opencensus-local-forwarder.md) gefolgt sind, befindet die Datei sich unter `C:\LF-WindowsServiceHost`.

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Starten Sie den Anwendungsdienst **lokale Weiterleitung** neu.

## <a name="opencensus-go-packages"></a>OpenCensus Go-Pakete

1. Installieren der OpenCensus-Pakete für Go über die Befehlszeile:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Fügen Sie den folgenden Code zu einer GO-Datei hinzu, dann generieren Sie den Code und führen ihn aus. (Dieses Beispiel stammt aus der offiziellen OpenCensus-Richtlinie mit zusätzlichem Code, der die Integration mit der lokalen Weiterleitung erleichtert)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Sobald die einfache Go-App ausgeführt wird, navigieren Sie zu `http://localhost:50030`. Bei jeder Aktualisierung des Browsers wird der Text „Hallo Welt“ generiert, begleitet von entsprechenden Span-Daten, die von der lokalen Weiterleitung aufgesammelt werden.

4. Um sicherzustellen, dass die **lokale Weiterleitung** die Überprüfungen übernimmt, überprüfen Sie die `LocalForwarder.config`-Datei. Wenn Sie die Schritte unter [Voraussetzungen](https://docs.microsoft.com/azure/application-insights/local-forwarder) befolgt haben, befindet sie sich im Verzeichnis `C:\LF-WindowsServiceHost`.

    In der Abbildung unten in der Protokolldatei sehen Sie, dass vor der Ausführung des zweiten Skripts, in dem wir einen Exporter hinzugefügt haben, `OpenCensus input BatchesReceived` gleich 0 war. Sobald wir mit der Ausführung des aktualisierten Skripts begonnen haben, wurde `BatchesReceived` um die Anzahl der eingegebenen Werte erhöht:
    
    ![Formular für neue App Insights-Ressource](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Starten der Überwachung im Azure-Portal

1. Sie können jetzt im Azure-Portal erneut die Seite **Übersicht** für Application Insights öffnen, um Details zu Ihrer aktuell ausgeführten Anwendung anzuzeigen. Wählen Sie **Live Metrics Stream** aus.

   ![Screenshot des Bereichs "Übersicht" mit Live Metrics Stream in einem roten Kasten](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Wenn Sie die zweite Go-App erneut ausführen und mit der Aktualisierung des Browsers für `http://localhost:50030` beginnen, werden die Ablaufverfolgungsdaten, die vom lokalen Weiterleitungsdienst in Application Insights eingehen, in Echtzeit angezeigt.

   ![Screenshot von Live Metrics Stream mit Leistungsdaten](./media/opencensus-go/0006-stream.png)

3. Navigieren Sie zurück zur Seite **Übersicht**, wählen Sie **Anwendungsübersicht** aus, um ein visuelles Layout der Abhängigkeitsbeziehungen und der zeitlichen Steuerung der Aufrufe zwischen den Komponenten Ihrer Anwendung zu erhalten.

    ![Screenshot der Basisanwendungsübersicht](./media/opencensus-go/0007-application-map.png)

    Da nur ein Methodenaufruf überwacht wurde, ist die Anwendungsübersicht nicht so interessant. Die Anwendungsübersicht kann jedoch skaliert werden, um wesentlich mehr verteilte Anwendungen zu visualisieren:

   ![Anwendungszuordnung](media/opencensus-go/application-map.png)

4. Wählen Sie **Leistung untersuchen** aus, um detaillierte Leistungsanalysen durchzuführen und die Hauptursache von Leistungseinbußen zu ermitteln.

    ![Screenshot des Leistungsbereichs](./media/opencensus-go/0008-performance.png)

5. Indem Sie **Beispiele** auswählen und dann klicken auf eines der im rechten Bereich angezeigten Beispiele klicken, wird die Detailansicht der End-to-End-Transaktionen gestartet. Von unserer Beispiel-App wird zwar nur ein einzelnes Ereignis angezeigt, eine komplexere Anwendung ermöglicht Ihnen jedoch, die End-to-End-Transaktion bis zur Ebene der Aufrufliste eines einzelnen Ereignisses zu untersuchen.

     ![Screenshot der Schnittstelle der End-to-End-Transaktion](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>OpenCensus-Ablaufverfolgung für Go

Wir haben nur die Grundlagen der Integration von OpenCensus für Go mit der lokalen Weiterleitung und Application Insights behandelt. Die offizielle [OpenCensus Go-Gebrauchsanweisung](https://godoc.org/go.opencensus.io) umfasst weiterführende Themen.

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungszuordnung](./../../azure-monitor/app/app-map.md)
* [End-to-End-Leistungsüberwachung](./../../azure-monitor/learn/tutorial-performance.md)
