---
title: Erstellen einer Datenpipeline mit der Datensammler-API von Azure Monitor | Microsoft-Dokumentation
description: Mit der HTTP-Datensammler-API von Azure Monitor können Sie dem Log Analytics-Arbeitsbereich über jeden Client, der die REST-API aufrufen kann, POST JSON-Daten hinzufügen. In diesem Artikel wird beschrieben, wie in Dateien gespeicherte Daten auf automatisierte Weise hochgeladen werden.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/09/2018
ms.author: magoedte
ms.openlocfilehash: 53457a044f5c69af7bf68561f24732e8f02219d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603232"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Erstellen einer Datenpipeline mit der Datensammler-API

Mit der [Datensammler-API von Azure Monitor](data-collector-api.md) können Sie benutzerdefinierte Protokolldaten in einen Log Analytics-Arbeitsbereich in Azure Monitor importieren. Die einzige Anforderung besteht darin, dass die Daten das JSON-Format aufweisen und in Segmente mit einer Größe von maximal 30 MB unterteilt sein müssen. Dies ist ein vollständig flexibler Mechanismus, der auf viele verschiedene Arten genutzt werden kann: vom direkten Senden von Daten aus Ihrer Anwendung bis hin zu einmaligen Ad-hoc-Uploads. In diesem Artikel werden einige Ausgangspunkte für ein allgemeines Szenario beschrieben: das regelmäßige Hochladen von in Dateien gespeicherten Daten auf automatisierte Weise. Die hier veranschaulichte Pipeline weist nicht die höchste Leistung auf und ist auch sonst nicht speziell optimiert, aber sie kann als Ausgangspunkt für die Erstellung Ihrer eigenen Pipeline für die Produktion dienen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Beispiel für ein Problem
Im restlichen Teil dieses Artikels untersuchen wir Seitenzugriffsdaten in Application Insights. In unserem hypothetischen Szenario möchten wir geografische Informationen, die vom Application Insights SDK standardmäßig gesammelt werden, mit benutzerdefinierten Daten zur Bevölkerungszahl jedes Lands bzw. jeder Region der Welt korrelieren. So soll ermittelt werden, wo wir die höchsten Anteile des Marketingbudgets ausgeben sollten. 

Zu diesem Zweck verwenden wir eine öffentliche Datenquelle, z.B. die [Informationen der UN zu den weltweiten Bevölkerungszahlen](https://esa.un.org/unpd/wpp/). Für die Daten wird das folgende einfache Schema verwendet:

![Beispiel für einfaches Schema](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

In diesem Beispiel wird davon ausgegangen, dass wir eine neue Datei mit den Daten des aktuellen Jahrs hochladen, sobald diese Daten verfügbar sind.

## <a name="general-design"></a>Allgemeiner Entwurf
Wir verwenden eine klassische ETL-Logik, um die Pipeline zu entwerfen. Die Architektur sieht wie folgt aus:

![Architektur der Pipeline für die Datensammlung](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

In diesem Artikel wird nicht beschrieben, wie Sie Daten erstellen oder [in ein Azure Blob Storage-Konto hochladen](../../storage/blobs/storage-upload-process-images.md). Wir steigen stattdessen an dem Punkt ein, an dem eine neue Datei in das Blob hochgeladen wird. Dies ist der Punkt:

1. Ein Prozess erkennt, dass neue Daten hochgeladen wurden.  In unserem Beispiel wird eine [Azure-Logik-App](../../logic-apps/logic-apps-overview.md) verwendet, die über einen Trigger verfügt, mit dem das Hochladen neuer Daten in ein Blob erkannt wird.

2. Ein Prozessor liest diese neuen Daten und konvertiert sie in JSON-Code – also in das für Azure Monitor erforderliche Format. In diesem Beispiel verwenden wir eine [Azure-Funktion](../../azure-functions/functions-overview.md) als einfache und kostengünstige Möglichkeit zum Ausführen unseres Verarbeitungscodes. Die Funktion wird von derselben Logik-App gestartet, die wir zum Erkennen der neuen Daten verwendet haben.

3. Wenn das JSON-Objekt verfügbar ist, wird es abschließend an Azure Monitor gesendet. Dieselbe Logik-App sendet die Daten an Azure Monitor, indem die integrierte Log Analytics-Datensammler-Aktivität verwendet wird.

In diesem Artikel wird nicht die detaillierte Einrichtung des Blobspeichers, der Logik-App oder der Azure-Funktion beschrieben, aber auf den Seiten der einzelnen Produkte finden Sie ausführliche Anleitungen.

Zur Überwachung dieser Pipeline nutzen wir Application Insights für unsere Azure-Funktion ([Details](../../azure-functions/functions-monitoring.md)) und Azure Monitor für unsere Logik-App ([Details](../../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)). 

## <a name="setting-up-the-pipeline"></a>Einrichten der Pipeline
Stellen Sie zum Festlegen der Pipeline zuerst sicher, dass Sie Ihren Blobcontainer erstellt und konfiguriert haben. Stellen Sie auch sicher, dass der Log Analytics-Arbeitsbereich erstellt wurde, an den Sie die Daten senden möchten.

## <a name="ingesting-json-data"></a>Erfassen von JSON-Daten
Die Erfassung von JSON-Daten ist mit Logik-Apps einfach, und da keine Transformation durchgeführt werden muss, können wir die gesamte Pipeline in eine einzelne Logik-App einbinden. Nachdem sowohl der Blobcontainer als auch der Log Analytics-Arbeitsbereich konfiguriert wurde, können Sie eine neue Logik-App erstellen und wie folgt konfigurieren:

![Beispiel für Logik-App-Workflow](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Speichern Sie Ihre Logik-App, und fahren Sie dann mit dem Testen fort.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Erfassen von XML-, CSV- oder anderen Datenformaten
Logic Apps verfügt derzeit nicht über integrierte Funktionen zum einfachen Transformieren von XML, CSV oder anderen Formaten in das JSON-Format. Aus diesem Grund müssen wir anders vorgehen, um diese Transformation zu erzielen. Für diesen Artikel verwenden wir die serverlosen Computefunktionen von Azure Functions als sehr einfache und kostengünstige Option. 

In diesem Beispiel analysieren wir eine CSV-Datei, aber auch alle anderen Dateitypen können auf ähnliche Weise verarbeitet werden. Ändern Sie einfach den Deserialisierungsteil der Azure-Funktion, um die richtige Logik für Ihren spezifischen Datentyp widerzuspiegeln.

1.  Erstellen Sie eine neue Azure-Funktion, indem Sie Version 1 der Functions-Laufzeit und die nutzungsbasierte Vorgehensweise wählen, wenn die entsprechende Aufforderung angezeigt wird.  Wählen Sie die Vorlage **HTTP-Trigger** für C# als Ausgangspunkt aus, damit Ihre Bindungen wie benötigt konfiguriert werden. 
2.  Erstellen Sie auf der Registerkarte **Dateien anzeigen** auf der rechten Seite eine neue Datei mit dem Namen **project.json**, und fügen Sie den folgenden Code aus den verwendeten NuGet-Paketen ein:

    ![Azure Functions-Beispielprojekt](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Wechseln Sie im rechten Bereich zu **run.csx**, und ersetzen Sie den Standardcode durch Folgendes. 

    >[!NOTE]
    >Für Ihr Projekt müssen Sie das Datensatzmodell („PopulationRecord“-Klasse) durch Ihr eigenes Datenschema ersetzen.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Speichern Sie Ihre Funktion.
5. Testen Sie die Funktion, um sicherzustellen, dass der Code richtig funktioniert. Wechseln Sie im rechten Bereich zur Registerkarte **Test**, und konfigurieren Sie den Test wie folgt. Fügen Sie in das Textfeld **Anforderungstext** einen Link zu einem Blob mit Beispieldaten ein. Nach dem Klicken auf **Ausführen** sollte im Feld **Ausgabe** die JSON-Ausgabe angezeigt werden:

    ![Testcode für Funktions-App](./media/create-pipeline-datacollector-api/functions-test-01.png)

Nun müssen wir zurückgehen und die Logik-App ändern, mit deren Erstellung wir zuvor begonnen haben, um die erfassten und in das JSON-Format konvertierten Daten einzubinden.  Führen Sie die Konfiguration mit dem Ansicht-Designer wie folgt durch, und speichern Sie anschließend Ihre Logik-App:

![Vollständiges Beispiel für Logic Apps-Workflow](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Testen der Pipeline
Nun können Sie eine neue Datei in das zuvor konfigurierte Blob hochladen und mit Ihrer Logik-App überwachen. Nach kurzer Zeit sollte eine neue Instanz der Logik-App angezeigt werden, über die Ihre Azure-Funktion aufgerufen wird, und die Daten sollten anschließend erfolgreich an Azure Monitor gesendet werden. 

>[!NOTE]
>Es kann bis zu 30 Minuten dauern, bis die Daten in Azure Monitor zu sehen sind, wenn Sie zum ersten Mal einen neuen Datentyp senden.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Korrelieren mit anderen Daten in Log Analytics und Application Insights
Führen Sie die folgenden Abfrage entweder über Ihr Application Insights-Analysefenster oder den Log Analytics-Arbeitsbereich durch, um die Application Insights-Seitenzugriffsdaten wie gewünscht mit den Daten zu den Bevölkerungszahlen zu korrelieren, die wir von unserer benutzerdefinierten Datenquelle erfasst haben:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

In der Ausgabe sollten die beiden Datenquellen jetzt verknüpft angezeigt werden.  

![Beispiel: Korrelieren von unverbundenen Daten in einem Suchergebnis](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Verbesserungsvorschläge für eine Produktionspipeline
In diesem Artikel wurde ein funktionierender Prototyp vorgestellt, dessen Logik auf eine echte Lösung für die Produktion angewendet werden kann. Für eine Lösung dieser Art, die für die Produktion geeignet ist, werden die folgenden Verbesserungen empfohlen:

* Fügen Sie die Fehlerbehandlung und Wiederholungslogik in Ihrer Logik-App und Funktion hinzu.
* Fügen Sie die Logik hinzu, um sicherzustellen, dass der Grenzwert von 30 MB pro Aufruf der Log Analytics-Erfassungs-API nicht überschritten wird. Unterteilen Sie die Daten in kleinere Segmente, falls dies erforderlich ist.
* Richten Sie in Ihrem Blobspeicher eine Bereinigungsrichtlinie ein. Sofern Sie die unformatierten Daten nicht für Archivierungszwecke aufbewahren möchten, besteht kein Grund, sie nach dem erfolgreichen Senden an den Log Analytics-Arbeitsbereich weiter zu speichern. 
* Vergewissern Sie sich, dass die Überwachung für die gesamte Pipeline aktiviert ist, und fügen Sie je nach Bedarf Verfolgungspunkte und Warnungen hinzu.
* Nutzen Sie die Quellcodeverwaltung, um den Code für Ihre Funktion und Logik-App zu verwalten.
* Stellen Sie sicher, dass eine geeignete Richtlinie für die Änderungsverwaltung eingehalten wird, damit die Funktion und Logik-App entsprechend angepasst werden, wenn sich das Schema ändert.
* Falls Sie mehrere unterschiedliche Datentypen hochladen, sollten Sie diese in Ihrem Blobcontainer auf mehrere einzelne Ordner aufteilen und Logik erstellen, um die Logik basierend auf dem Datentyp aufzufächern. 


## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die [Datensammler-API](data-collector-api.md), um Daten von jedem REST-API-Client in den Log Analytics-Arbeitsbereich zu schreiben.
