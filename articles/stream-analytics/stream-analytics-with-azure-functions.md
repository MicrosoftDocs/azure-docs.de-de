---
title: 'Tutorial: Ausführen von Azure Functions in Azure Stream Analytics-Aufträgen'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Functions als Ausgabesenke für Stream Analytics-Aufträge konfigurieren.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/27/2020
ms.openlocfilehash: bb2eb36e4116c17efb20946b0da4586678838f3b
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862002"
---
# <a name="tutorial-run-azure-functions-from-azure-stream-analytics-jobs"></a>Tutorial: Ausführen von Azure Functions in Azure Stream Analytics-Aufträgen 

Sie können Azure Functions in Azure Stream Analytics ausführen, indem Sie Functions als eine der Ausgabesenken für den Stream Analytics-Auftrag konfigurieren. Functions ist eine ereignisgesteuerte On-Demand-Computeumgebung, mit der Sie Code implementieren können, der durch in Azure- oder Drittanbieterdiensten auftretende Ereignisse ausgelöst wird. Aufgrund der Möglichkeit, auf Trigger zu antworten, ist Functions die ideale Ausgabe für Stream Analytics-Aufträge.

Stream Analytics ruft Functions über HTTP-Trigger auf. Der Functions-Ausgabeadapter ermöglicht Benutzern das Verbinden von Functions mit Stream Analytics, sodass die Ereignisse basierend auf Stream Analytics-Abfragen ausgelöst werden können. 

> [!NOTE]
> Eine Verbindung mit Azure Functions innerhalb eines virtuellen Netzwerks (VNET) aus einem Stream Analytics-Auftrag, der in einem mehrinstanzenfähigen Cluster ausgeführt wird, wird nicht unterstützt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen und Ausführen eines Stream Analytics-Auftrags
> * Erstellen einer Azure Cache for Redis-Instanz
> * Erstellen einer Azure Function
> * Überprüfen der Ergebnisse in Azure Cache for Redis

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="configure-a-stream-analytics-job-to-run-a-function"></a>Konfigurieren eines Stream Analytics-Auftrags zum Ausführen einer Funktion 

Dieser Abschnitt veranschaulicht das Konfigurieren eines Stream Analytics-Auftrags zum Ausführen einer Funktion, die Daten in Azure Cache for Redis schreibt. Der Stream Analytics-Auftrag liest Ereignisse aus Azure Event Hubs und führt eine Abfrage aus, die die Funktion aufruft. Diese Funktion liest Daten aus dem Stream Analytics-Auftrag und schreibt sie in Azure Cache for Redis.

![Diagramm der Beziehungen zwischen den Azure-Diensten](./media/stream-analytics-with-azure-functions/image1.png)

## <a name="create-a-stream-analytics-job-with-event-hubs-as-input"></a>Erstellen eines Stream Analytics-Auftrags mit Event Hubs als Eingabe

Führen Sie das Tutorial zur [Betrugserkennung in Echtzeit](stream-analytics-real-time-fraud-detection.md) aus, um einen Event Hub zu erstellen, die Ereignisgenerator-Anwendung zu starten und einen Stream Analytics-Auftrag zu erstellen. Überspringen Sie die Schritte zum Erstellen der Abfrage und Ausgabe. Sehen Sie sich stattdessen die folgenden Abschnitte zum Einrichten einer Azure Functions-Ausgabe an.

## <a name="create-an-azure-cache-for-redis-instance"></a>Erstellen einer Azure Cache for Redis-Instanz

1. Erstellen Sie anhand der Schritte in [Erstellen eines Caches](../azure-cache-for-redis/cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache) einen Cache in Azure Cache for Redis.  

2. Wählen Sie nach der Erstellung des Caches unter **Einstellungen** die Option **Zugriffsschlüssel** aus. Notieren Sie sich die **Primäre Verbindungszeichenfolge**.

   ![Screenshot der Azure Cache for Redis-Verbindungszeichenfolge](./media/stream-analytics-with-azure-functions/image2.png)

## <a name="create-a-function-in-azure-functions-that-can-write-data-to-azure-cache-for-redis"></a>Erstellen einer Funktion in Azure Functions, die Daten in Azure Cache for Redis schreiben kann

1. Lesen Sie den Abschnitt [Erstellen einer Funktions-App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app) in der Dokumentation zu Functions. In diesem Abschnitt erfahren Sie Schritt für Schritt, wie Sie eine Funktions-App und eine [Funktion in Azure Functions mit Auslösung per HTTP](../azure-functions/functions-create-first-azure-function.md#create-function) in C# erstellen.  

2. Navigieren Sie zu der Funktion **run.csx**. Aktualisieren Sie sie mit dem folgenden Code. Ersetzen Sie **"\<your Azure Cache for Redis connection string goes here\>"** durch die primäre Verbindungszeichenfolge von Azure Cache for Redis, die Sie im vorherigen Abschnitt abgerufen haben. 

    ```csharp
    using System;
    using System.Net;
    using System.Threading.Tasks;
    using StackExchange.Redis;
    using Newtonsoft.Json;
    using System.Configuration;

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        // Get the request body
        dynamic dataArray = await req.Content.ReadAsAsync<object>();

        // Throw an HTTP Request Entity Too Large exception when the incoming batch(dataArray) is greater than 256 KB. Make sure that the size value is consistent with the value entered in the Stream Analytics portal.

        if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
        var connection = ConnectionMultiplexer.Connect("<your Azure Cache for Redis connection string goes here>");
        log.Info($"Connection string.. {connection}");
    
        // Connection refers to a property that returns a ConnectionMultiplexer
        IDatabase db = connection.GetDatabase();
        log.Info($"Created database {db}");
    
        log.Info($"Message Count {dataArray.Count}");

        // Perform cache operations using the cache object. For example, the following code block adds few integral data types to the cache
        for (var i = 0; i < dataArray.Count; i++)
        {
            string time = dataArray[i].time;
            string callingnum1 = dataArray[i].callingnum1;
            string key = time + " - " + callingnum1;
            db.StringSet(key, dataArray[i].ToString());
            log.Info($"Object put in database. Key is {key} and value is {dataArray[i].ToString()}");
       
            // Simple get of data types from the cache
            string value = db.StringGet(key);
            log.Info($"Database got: {value}");
        }

        return req.CreateResponse(HttpStatusCode.OK, "Got");
    }

   ```

   Wenn Stream Analytics die Ausnahme „HTTP-Anforderungseinheit zu groß“ von der Funktion empfängt, verringert der Dienst die Größe der an Functions gesendeten Batches. Der folgende Code stellt sicher, dass Stream Analytics keine zu großen Batches sendet. Stellen Sie sicher, dass die Werte für die maximal zulässige Batchanzahl und -größe in der Funktion mit den Werten übereinstimmen, die im Stream Analytics-Portal eingegeben wurden.

    ```csharp
    if (dataArray.ToString().Length > 262144)
        {
            return new HttpResponseMessage(HttpStatusCode.RequestEntityTooLarge);
        }
   ```

3. Erstellen Sie in einem Text-Editor Ihrer Wahl die JSON-Datei **project.json**. Fügen Sie den folgenden Code ein, und speichern Sie ihn auf Ihrem lokalen Computer. Diese Datei enthält die für die C#-Funktion erforderlichen NuGet-Paketabhängigkeiten.  
   
    ```json
    {
        "frameworks": {
            "net46": {
                "dependencies": {
                    "StackExchange.Redis":"1.1.603",
                    "Newtonsoft.Json": "9.0.1"
                }
            }
        }
    }

   ```
 
4. Wechseln Sie zurück zum Azure-Portal. Navigieren Sie auf der Registerkarte **Plattformfeatures** zu Ihrer Funktion. Wählen Sie unter **Entwicklungstools** die Option **App Service-Editor** aus. 
 
   ![Screenshot: Registerkarte „Plattformfeatures“, auf der „App Service-Editor“ ausgewählt ist](./media/stream-analytics-with-azure-functions/image3.png)

5. Klicken Sie im App Service-Editor mit der rechten Maustaste auf Ihr Stammverzeichnis, und laden Sie die Datei **project.json** hoch. Aktualisieren Sie nach dem erfolgreichen Upload die Seite. Jetzt sollte eine automatisch generierte Datei namens **project.lock.json** angezeigt werden. Die automatisch generierte Datei enthält Verweise auf die DLL-Dateien, die in der Datei „project.json“ angegeben werden.  

   ![Screenshot: Die im Menü ausgewählte Option „Dateien hochladen“](./media/stream-analytics-with-azure-functions/image4.png)

## <a name="update-the-stream-analytics-job-with-the-function-as-output"></a>Aktualisieren des Stream Analytics-Auftrags mit der Funktion als Ausgabe

1. Öffnen Sie den Stream Analytics-Auftrag im Azure-Portal.  

2. Navigieren Sie zu Ihrer Funktion, und wählen Sie **Übersicht** > **Ausgaben** > **Hinzufügen** aus. Um eine neue Ausgabe hinzuzufügen, wählen Sie **Azure-Funktion** als Senkenoption aus. Der Functions-Ausgabeadapter hat die folgenden Eigenschaften:  

   |**Eigenschaftenname**|**Beschreibung**|
   |---|---|
   |Ausgabealias| Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Ausgabe zu verweisen. |
   |Importoption| Sie können die Funktion aus dem aktuellem Abonnement verwenden, oder Sie geben die Einstellungen manuell an, wenn sich die Funktion in einem anderen Abonnement befindet. |
   |Funktionen-App| Der Name der Funktions-App |
   |Funktion| Der Name der Funktion in Ihrer Funktions-App (Name Ihrer run.csx-Funktion)|
   |Max Batch Size|Legt die maximale Größe (in Bytes) für jeden Ausgabebatch fest, der an die Funktion gesendet wird. Dieser Wert ist standardmäßig auf 262.144 Byte (256 KB) festgelegt.|
   |Max Batch Count|Gibt die maximale Anzahl von Ereignissen in jedem Batch an, die an die Funktion gesendet wird. Der Standardwert ist 100. Diese Eigenschaft ist optional.|
   |Schlüssel|Ermöglicht die Verwendung einer Funktion aus einem anderen Abonnement. Geben Sie den Schlüsselwert für den Zugriff auf die Funktion an. Diese Eigenschaft ist optional.|

3. Geben Sie einen Namen für den Ausgabealias an. In diesem Tutorial lautet er **saop1**, aber Sie können einen beliebigen Namen Ihrer Wahl verwenden. Geben Sie weitere Details an.

4. Öffnen Sie den Stream Analytics-Auftrag, und aktualisieren Sie die Abfrage wie folgt. Wenn Sie Ihre Ausgabesenke **saop1** nicht benannt haben, denken Sie daran, sie in der Abfrage zu ändern.  

   ```sql
    SELECT
            System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,
            CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2
        INTO saop1
        FROM CallStream CS1 TIMESTAMP BY CallRecTime
           JOIN CallStream CS2 TIMESTAMP BY CallRecTime
            ON CS1.CallingIMSI = CS2.CallingIMSI AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
        WHERE CS1.SwitchNum != CS2.SwitchNum
   ```

5. Starten Sie die Anwendung „telcodatagen.exe“, indem Sie in der Befehlszeile den folgenden Befehl ausführen. Der Befehl hat das Format `telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]`.  
   
   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```
    
6.  Starten des Stream Analytics-Auftrags

## <a name="check-azure-cache-for-redis-for-results"></a>Überprüfen der Ergebnisse in Azure Cache for Redis

1. Browsen Sie zum Azure-Portal, und suchen Sie Ihre Azure Cache for Redis-Instanz. Wählen Sie **Konsole** aus.  

2. Überprüfen Sie mit [Azure Cache for Redis-Befehlen](https://redis.io/commands), ob Ihre Daten sich in Azure Cache for Redis befinden. (Der Befehl weist das Format „Get {Schlüssel}“ auf.) Beispiel:

   **Get "12/19/2017 21:32:24 - 123414732"**

   Dieser Befehl sollte den Wert für den angegebenen Schlüssel ausgeben:

   ![Screenshot der Azure Cache for Redis-Ausgabe](./media/stream-analytics-with-azure-functions/image5.png)

## <a name="error-handling-and-retries"></a>Fehlerbehandlung und Wiederholungsversuche

Im Falle eines Fehlers beim Senden von Ereignissen an Azure Functions werden die meisten Vorgänge von Stream Analytics wiederholt. Mit Ausnahme des HTTP-Fehlers 413 (Entität zu groß) werden alle HTTP-Ausnahmen bis zur erfolgreichen Ausführung wiederholt. Fehler vom Typ „Entität zu groß“ werden als Datenfehler gemäß der [Ausgabefehlerrichtlinie](stream-analytics-output-error-policy.md) behandelt und entweder wiederholt oder verworfen.

> [!NOTE]
> Das Zeitlimit für HTTP-Anforderungen von Stream Analytics an Azure Functions ist auf 100 Sekunden festgelegt. Falls Ihre Azure Functions-App mehr als 100 Sekunden für die Verarbeitung eines Batchs benötigt, tritt für Stream Analytics ein Fehler auf, und die Batchverarbeitung wird erneut versucht.

Wiederholungsversuche infolge eines Timeouts können dazu führen, dass doppelte Ereignisse in die Ausgabesenke geschrieben werden. Wenn von Stream Analytics ein erneuter Versuch für einen nicht erfolgreichen Batch ausgeführt wird, umfasst dieser Versuch alle Ereignisse im Batch. Stellen Sie sich beispielsweise einen Batch mit 20 Ereignissen vor, die von Stream Analytics an Azure Functions gesendet werden. Angenommen, Azure Functions benötigt 100 Sekunden für die Verarbeitung der ersten zehn Ereignisse. Nach 100 Sekunden wird die Anforderung von Stream Analytics unterbrochen, da keine positive Antwort von Azure Functions empfangen wurde, und es wird eine weitere Anforderung für den gleichen Batch gesendet. Die ersten zehn Ereignisse im Batch werden erneut von Azure Functions verarbeitet, was ein Duplikat zur Folge hat. 

## <a name="known-issues"></a>Bekannte Probleme

Wenn Sie im Azure-Portal versuchen, die Werte für die maximal zulässige Batchgröße oder die maximal zulässige Batchanzahl auf einen leeren Wert (Standard) zurückzusetzen, ändert sich der Wert beim Speichern in den zuvor eingegebenen Wert. Geben Sie in diesem Fall die Standardwerte für diese Felder manuell ein.

Die Verwendung von [HTTP-Routing](/sandbox/functions-recipes/routes?tabs=csharp) für Ihre Azure Functions wird derzeit nicht von Stream Analytics unterstützt.

Die Unterstützung für die Verbindungsherstellung mit der Azure Functions-Lösung, die in einem virtuellen Netzwerk gehostet wird, ist nicht aktiviert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Streamingauftrag und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Durch das Löschen des Auftrags verhindern Sie, dass Kosten für die vom Auftrag verbrauchten Streamingeinheiten anfallen. Wenn Sie den Auftrag in Zukunft verwenden möchten, können Sie ihn beenden und später bei Bedarf neu starten. Wenn Sie diesen Auftrag nicht mehr verwenden möchten, löschen Sie alle Ressourcen, die im Rahmen dieser Schnellstartanleitung erstellt wurden:

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den Namen der erstellten Ressource.  
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen einfachen Stream Analytics-Auftrag erstellt, der eine Azure-Funktion ausführt. Weitere Informationen zu Stream Analytics-Aufträgen erhalten Sie im nächsten Tutorial:

> [!div class="nextstepaction"]
> [Azure Stream Analytics – benutzerdefinierte JavaScript-Funktionen](stream-analytics-javascript-user-defined-functions.md)
