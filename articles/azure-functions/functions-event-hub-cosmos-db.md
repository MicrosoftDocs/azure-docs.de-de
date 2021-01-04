---
title: 'Tutorial: Verwenden von Java-Funktionen mit Azure Cosmos DB und Event Hubs'
description: In diesem Tutorial erfahren Sie, wie Sie Ereignisse aus Event Hubs verarbeiten können, um Updates in Azure Cosmos DB mithilfe einer in Java geschriebenen Funktion durchzuführen.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 20792d58ab259f93d7725fbafda1507f9eddc740
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862155"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Tutorial: Erstellen einer Funktion in Java mit einem Event Hub-Trigger und einer Azure Cosmos DB-Ausgabebindung

In diesem Tutorial erfahren Sie, wie Sie mit Azure Functions eine Java-Funktion erstellen, die einen kontinuierlichen Datenstrom von Temperatur- und Druckdaten analysiert. Event Hub-Ereignisse, die Sensormesswerte darstellen, lösen die Funktion aus. Die Funktion verarbeitet die Ereignisdaten und fügt dann Statuseinträge zu Azure Cosmos DB hinzu.

In diesem Tutorial gehen Sie wie folgt vor:

> [!div class="checklist"]
> * Sie erstellen und konfigurieren Azure-Ressourcen mit der Azure-Befehlszeilenschnittstelle.
> * Sie erstellen und testen Java-Funktionen, die mit diesen Ressourcen interagieren.
> * Sie stellen Ihre Funktionen in Azure bereit und überwachen sie mit Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial muss Folgendes installiert sein:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), Version 8
- [Apache Maven](https://maven.apache.org), Version 3.0 oder höher
- [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools), Version 2.6.666 oder höher [!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]
> Damit Sie dieses Tutorial durchführen können, muss die Umgebungsvariable `JAVA_HOME` auf den Installationsspeicherort des JDK festgelegt sein.

Wenn Sie es vorziehen, den Code für dieses Tutorial direkt zu verwenden, finden Sie die entsprechenden Informationen im Beispielrepository [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb).

## <a name="create-azure-resources"></a>Erstellen von Azure-Ressourcen

In diesem Tutorial benötigen Sie folgende Ressourcen:

* Eine Ressourcengruppe, die die anderen Ressourcen enthält.
* Einen Event Hubs-Namespace, ein Event Hub und eine Autorisierungsregel.
* Ein Cosmos DB-Konto, eine Datenbank und eine Sammlung.
* Eine Funktions-App und ein Speicherkonto, um sie zu hosten.

In den folgenden Abschnitten erfahren Sie, wie Sie diese Ressourcen mit der Azure-Befehlszeilenschnittstelle erstellen.

### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Als nächstes erstellen Sie einige Umgebungsvariablen für die Namen und den Speicherort der zu erstellenden Ressourcen. Verwenden Sie die folgenden Befehle, und ersetzen Sie die `<value>`-Platzhalter durch Werte Ihrer Wahl. Die Werte müssen den [Benennungsregeln und -einschränkungen für Azure-Ressourcen](/azure/architecture/best-practices/resource-naming) entsprechen. Verwenden Sie für die Variable `LOCATION` einen der Werte, die durch den Befehl `az functionapp list-consumption-locations` erzeugt werden.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set EVENT_HUB_NAMESPACE=<value>
set EVENT_HUB_NAME=<value>
set EVENT_HUB_AUTHORIZATION_RULE=<value>
set COSMOS_DB_ACCOUNT=<value>
set STORAGE_ACCOUNT=<value>
set FUNCTION_APP=<value>
set LOCATION=<value>
```

---

Im weiteren Verlauf dieses Tutorials werden diese Variablen verwendet. Beachten Sie, dass diese Variablen nur für die Dauer Ihrer aktuellen Azure CLI- oder Cloud Shell-Sitzung bestehen bleiben. Sie müssen diese Befehle erneut ausführen, wenn Sie ein anderes lokales Terminalfenster verwenden oder Ihre Cloud Shell-Sitzung abläuft.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure verwendet Ressourcengruppen, um alle zugehörigen Ressourcen in Ihrem Konto zu sammeln. Auf diese Weise können Sie sie als Einheit anzeigen und mit einem einzelnen Befehl löschen, wenn Sie mit ihnen fertig sind.

Verwenden Sie den folgenden Befehl, um eine Ressourcengruppe zu erstellen:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group create ^
    --name %RESOURCE_GROUP% ^
    --location %LOCATION%
```

---

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Als nächstes erstellen Sie mit den folgenden Befehlen einen Azure Event Hubs-Namespace, einen Event Hub und eine Autorisierungsregel:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventhubs namespace create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAMESPACE%
az eventhubs eventhub create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --message-retention 1
az eventhubs eventhub authorization-rule create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_AUTHORIZATION_RULE% ^
    --eventhub-name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --rights Listen Send
```

---

Der Event Hubs-Namespace enthält den eigentlichen Event Hub und dessen Autorisierungsregel. Die Autorisierungsregel ermöglicht es Ihren Funktionen, Nachrichten an den Hub zu senden und auf die entsprechenden Ereignisse zu lauschen. Eine Funktion sendet Nachrichten, die Telemetriedaten darstellen. Eine andere Funktion lauscht auf Ereignisse, analysiert die Ereignisdaten und speichert die Ergebnisse in Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Erstellen einer Azure Cosmos DB-Instanz

Erstellen Sie anschließend mit den folgenden Befehlen ein Azure Cosmos DB-Konto, eine Datenbank und eine Sammlung:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb sql database create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --name TelemetryDb
az cosmosdb sql container create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --database-name TelemetryDb \
    --name TelemetryInfo \
    --partition-key-path '/temperatureStatus'
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az cosmosdb create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %COSMOS_DB_ACCOUNT%
az cosmosdb sql database create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --name TelemetryDb
az cosmosdb sql container create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --database-name TelemetryDb ^
    --name TelemetryInfo ^
    --partition-key-path "/temperatureStatus"
```

---

Der Wert `partition-key-path` partitioniert Ihre Daten basierend auf dem `temperatureStatus`-Wert der einzelnen Elemente. Der Partitionsschlüssel ermöglicht es Cosmos DB, die Leistung zu steigern, indem Ihre Daten in verschiedene Teilmengen unterteilt werden, auf die sie unabhängig zugreifen können.

### <a name="create-a-storage-account-and-function-app"></a>Erstellen eines Speicherkontos und einer Funktions-App

Als nächstes erstellen Sie ein Azure Storage-Konto, das von Azure Functions benötigt wird, und dann erstellen Sie die Funktions-App. Verwenden Sie die folgenden Befehle:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java \
    --functions-version 2
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az storage account create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %STORAGE_ACCOUNT% ^
    --sku Standard_LRS
az functionapp create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --storage-account %STORAGE_ACCOUNT% ^
    --consumption-plan-location %LOCATION% ^
    --runtime java ^
    --functions-version 2
```

---

Wenn der Befehl `az functionapp create` Ihre Funktions-App erstellt, erstellt er auch eine Application Insights-Ressource mit demselben Namen. Die Funktions-App wird automatisch mit einer Einstellung namens `APPINSIGHTS_INSTRUMENTATIONKEY` konfiguriert, die sie mit Application Insights verbindet. Sie können die Anwendungstelemetrie anzeigen, nachdem Sie Ihre Funktionen in Azure bereitgestellt haben, wie später in diesem Tutorial beschrieben.

## <a name="configure-your-function-app"></a>Konfigurieren Ihrer Funktions-App

Ihre Funktions-App muss auf die anderen Ressourcen zugreifen, um ordnungsgemäß zu funktionieren. In den folgenden Abschnitten erfahren Sie, wie Sie Ihre Funktions-App konfigurieren, dass sie auf Ihrem lokalen Computer ausgeführt werden kann.

### <a name="retrieve-resource-connection-strings"></a>Abrufen von Ressourcenverbindungszeichenfolgen

Verwenden Sie die folgenden Befehle, um die Speicher-, Event Hub- und Cosmos DB-Verbindungszeichenfolgen abzurufen und in Umgebungsvariablen zu speichern:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
FOR /F "delims=" %X IN (' ^
    az storage account show-connection-string ^
        --name %STORAGE_ACCOUNT% ^
        --query connectionString ^
        --output tsv') DO SET AZURE_WEB_JOBS_STORAGE=%X
FOR /F "delims=" %X IN (' ^
    az eventhubs eventhub authorization-rule keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %EVENT_HUB_AUTHORIZATION_RULE% ^
        --eventhub-name %EVENT_HUB_NAME% ^
        --namespace-name %EVENT_HUB_NAMESPACE% ^
        --query primaryConnectionString ^
        --output tsv') DO SET EVENT_HUB_CONNECTION_STRING=%X
FOR /F "delims=" %X IN (' ^
    az cosmosdb keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %COSMOS_DB_ACCOUNT% ^
        --type connection-strings ^
        --query connectionStrings[0].connectionString ^
        --output tsv') DO SET COSMOS_DB_CONNECTION_STRING=%X
```

---

Diese Variablen werden auf Werte festgelegt, die über Azure CLI-Befehle abgerufen werden. Jeder Befehl verwendet eine JMESPath-Abfrage, um die Verbindungszeichenfolge aus der zurückgegebenen JSON-Nutzlast zu extrahieren. Die Verbindungszeichenfolgen werden auch mithilfe von `echo` angezeigt, damit Sie bestätigen können, dass sie erfolgreich abgerufen wurden.

### <a name="update-your-function-app-settings"></a>Aktualisieren der Einstellungen Ihrer Funktions-App

Verwenden Sie anschließend den folgenden Befehl, um die Werte der Verbindungszeichenfolge in die App-Einstellungen Ihres Azure Functions-Kontos zu übertragen:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az functionapp config appsettings set ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --settings ^
        AzureWebJobsStorage=%AZURE_WEB_JOBS_STORAGE% ^
        EventHubConnectionString=%EVENT_HUB_CONNECTION_STRING% ^
        CosmosDBConnectionString=%COSMOS_DB_CONNECTION_STRING%
```

---

Ihre Azure-Ressourcen wurden jetzt erstellt und konfiguriert, um ordnungsgemäß zusammenzuarbeiten.

## <a name="create-and-test-your-functions"></a>Erstellen und Testen Ihrer Funktionen

Als nächstes erstellen Sie ein Projekt auf Ihrem lokalen Computer, fügen Java-Code hinzu und testen ihn. Sie verwenden Befehle, die mit dem Azure Functions-Plug-In für Maven und den Azure Functions Core Tools funktionieren. Ihre Funktionen werden lokal ausgeführt, verwenden aber die von Ihnen erstellten cloudbasierten Ressourcen. Nachdem Sie die Funktionen lokal ausgeführt haben, können Sie sie mit Maven in der Cloud bereitstellen und die Ansammlung Ihrer Daten und Analysen beobachten.

Wenn Sie Cloud Shell zur Erstellung Ihrer Ressourcen verwendet haben, werden Sie nicht lokal mit Azure verbunden. Verwenden Sie in diesem Fall den Befehl `az login`, um den browserbasierten Anmeldevorgang zu starten. Legen Sie dann bei Bedarf das Standardabonnement mit `az account set --subscription` fest, gefolgt von der Abonnement-ID. Führen Sie abschließend die folgenden Befehle aus, um einige Umgebungsvariablen auf Ihrem lokalen Computer neu zu erstellen. Ersetzen Sie die `<value>`-Platzhalter durch dieselben Werte, die Sie zuvor verwendet haben.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set FUNCTION_APP=<value>
```

---

### <a name="create-a-local-functions-project"></a>Erstellen eines lokalen Funktionsprojekts

Verwenden Sie den folgenden Maven-Befehl, um ein Funktionsprojekt zu erstellen und die erforderlichen Abhängigkeiten hinzuzufügen.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate --batch-mode ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype ^
    -DappName=%FUNCTION_APP% ^
    -DresourceGroup=%RESOURCE_GROUP% ^
    -DgroupId=com.example ^
    -DartifactId=telemetry-functions
```

---

Dieser Befehl generiert mehrere Dateien in einem `telemetry-functions`-Ordner:

* Eine `pom.xml`-Datei zur Verwendung mit Maven.
* Eine `local.settings.json`-Datei zum Speichern von App-Einstellungen für lokale Tests.
* Eine `host.json`-Datei, die das Azure Functions Extension Bundle aktiviert, das für die Cosmos DB-Ausgabebindung in Ihrer Datenanalysefunktion benötigt wird.
* Eine `Function.java`-Datei, die eine Standardfunktionsimplementierung enthält.
* Einige Testdateien, die für dieses Tutorial nicht erforderlich sind.

Sie müssen die Testdateien löschen, um Kompilierungsfehler zu vermeiden. Führen Sie die folgenden Befehle aus, um zum neuen Projektordner zu navigieren und den Testordner zu löschen:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
cd telemetry-functions
rm -r src/test
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd telemetry-functions
rmdir /s /q src\test
```

---

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Abrufen der Einstellungen Ihrer Funktions-App für die lokale Verwendung

Für lokale Tests benötigt Ihr Funktionsprojekt die Verbindungszeichenfolgen, die Sie Ihrer Funktions-App in Azure zuvor in diesem Tutorial hinzugefügt haben. Verwenden Sie den folgenden Azure Functions Core Tools-Befehl, der alle in der Cloud gespeicherten Einstellungen der Funktions-App abruft und sie zu Ihrer `local.settings.json`-Datei hinzufügt:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
func azure functionapp fetch-app-settings %FUNCTION_APP%
```

---

### <a name="add-java-code"></a>Hinzufügen von Java-Code

Öffnen Sie als nächstes die Datei `Function.java`, und ersetzen Sie den Inhalt durch den folgenden Code.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Wie Sie sehen können, enthält diese Datei zwei Funktionen, `generateSensorData` und `processSensorData`. Die Funktion `generateSensorData` simuliert einen Sensor, der Temperatur- und Druckmesswerte an den Event Hub sendet. Ein Trigger mit Timer führt die Funktion alle 10 Sekunden aus, und eine Event Hub-Ausgabebindung sendet den Rückgabewert an den Event Hub.

Wenn der Event Hub die Nachricht empfängt, generiert er ein Ereignis. Die Funktion `processSensorData` wird ausgeführt, wenn sie das Ereignis empfängt. Anschließend verarbeitet sie die Ereignisdaten und verwendet eine Azure Cosmos DB-Ausgabebindung, um die Ergebnisse an Azure Cosmos DB zu senden.

Die von diesen Funktionen verwendeten Daten werden in einer Klasse namens `TelemetryItem` gespeichert, die Sie implementieren müssen. Erstellen Sie eine neue Datei namens `TelemetryItem.java` an demselben Speicherort wie `Function.java`, und fügen Sie den folgenden Code hinzu:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Lokales Ausführen

Sie können die Funktionen jetzt lokal erstellen und ausführen und sehen, dass Daten in Ihrer Azure Cosmos DB angezeigt werden.

Verwenden Sie die folgenden Maven-Befehle, um die Funktionen zu erstellen und auszuführen:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn clean package
mvn azure-functions:run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn clean package
mvn azure-functions:run
```

---

Nach einigen Build- und Startnachrichten wird eine Ausgabe wie im folgenden Beispiel für jede Ausführung der Funktion angezeigt:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Sie können dann zum [Azure-Portal](https://portal.azure.com) wechseln und zu Ihrem Azure Cosmos DB-Konto navigieren. Wählen Sie **Daten-Explorer**, erweitern Sie **TelemetryInfo**, und wählen Sie dann **Elemente** aus, um Ihre Daten beim Eintreffen anzuzeigen.

![Cosmos DB-Daten-Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Bereitstellen in Azure und Anzeigen der App-Telemetriedaten

Schließlich können Sie Ihre App auch in Azure bereitstellen und überprüfen, ob sie weiterhin wie bei der lokalen Ausführung funktioniert.

Stellen Sie Ihr Projekt mit dem folgenden Befehl in Azure bereit:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn azure-functions:deploy
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn azure-functions:deploy
```

---

Ihre Funktionen werden jetzt in Azure ausgeführt und sammeln weiterhin Daten in Ihrer Azure Cosmos DB. Sie können Ihre bereitgestellte Funktions-App im Azure-Portal anzeigen und die App-Telemetriedaten über die verbundene Application Insights-Ressource anzeigen, wie in den folgenden Screenshots gezeigt:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Leistung:**

![Blatt „Leistung“ von Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie mithilfe des folgenden Befehls löschen:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group delete --name %RESOURCE_GROUP%
```

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie eine Azure-Funktion erstellen, die Event Hub-Ereignisse verarbeitet und eine Cosmos DB aktualisiert. Weitere Informationen finden Sie im [Java-Entwicklerhandbuch für Azure Functions](./functions-reference-java.md). Informationen zu den verwendeten Anmerkungen finden Sie in der Referenz zu [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation).

In diesem Tutorial wurden Umgebungsvariablen und Anwendungseinstellungen verwendet, um Geheimnisse wie Verbindungszeichenfolgen zu speichern. Informationen zum Speichern dieser Geheimnisse in Azure Key Vault finden Sie unter [Verwenden von Key Vault-Verweisen für App Service und Azure Functions](../app-service/app-service-key-vault-references.md).

Als nächstes erfahren Sie, wie Sie CI/CD in Azure Pipelines für die automatisierte Bereitstellung verwenden:

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen von Java für Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
