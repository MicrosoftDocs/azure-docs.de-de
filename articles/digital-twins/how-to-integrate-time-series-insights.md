---
title: Integration in Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Ereignisrouten von Azure Digital Twins in Azure Time Series Insights einrichten.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 951c52cdba191aa291061259e1c15b9190513770
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092717"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrieren von Azure Digital Twins in Azure Time Series Insights

In diesem Artikel erfahren Sie, wie Sie Azure Digital Twins in [Azure Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) integrieren.

Mithilfe der in diesem Artikel beschriebenen Lösung können Sie Verlaufsdaten zu ihrer IoT-Lösung erfassen und analysieren. Azure Digital Twins eignet sich hervorragend dazu, Daten in Time Series Insights einzuspeisen, da Sie mehrere Datenströme zu korrelieren und Ihre Informationen standardisieren können, bevor sie an Time Series Insights gesendet werden. 

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie eine Beziehung mit Time Series Insights einrichten können, benötigen Sie eine **Azure Digital Twins -Instanz**. Diese Instanz sollte mit der Möglichkeit eingerichtet werden, Informationen zu digitalen Zwillingen auf der Grundlage von Daten zu aktualisieren, da Sie die Informationen zu den Zwillingen mehrmals aktualisieren müssen, um die Nachverfolgung dieser Daten in Time Series Insights anzuzeigen. 

Wenn Sie dieses Setup nicht bereits eingerichtet haben, nutzen Sie zum Erstellen die Informationen im Azure Digital Twins-Tutorial [ *Erstellen einer End-to-End-Lösung*](./tutorial-end-to-end.md). Das Tutorial führt Sie durch das Einrichten einer Azure Digital Twins-Instanz, die mit einem virtuellen IoT-Gerät funktioniert, um Updates für digitale Zwillinge auszulösen.

## <a name="solution-architecture"></a>Lösungsarchitektur

Sie fügen Time Series Insights über den folgenden Pfad an Azure Digital Twins an.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Eine Ansicht der Azure-Dienste in einem End-to-End-Szenario, wobei Time Series Insights hervorgehoben wird" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Erstellen einer Route und eines Filters für Benachrichtigungen beim Aktualisieren von Zwillingen

Azure Digital Twins-Instanzen können [Ereignisse zur Aktualisierung von Zwillingen](how-to-interpret-event-data.md) ausgeben, wenn der Zustand eines Zwillings aktualisiert wird. In diesem Abschnitt erstellen Sie eine Azure Digital Twins-[**Ereignisroute**](concepts-route-events.md), die diese Aktualisierungsereignisse zur weiteren Verarbeitung an Azure [Event Hubs](../event-hubs/event-hubs-about.md) weiterleitet.

Das Azure Digital Twins-Tutorial [ *Erstellen einer End-to-End-Lösung*](./tutorial-end-to-end.md) führt Sie durch ein Szenario, in dem ein Temperaturattribut, das einem digitalen Zwilling angefügt ist, der einen Raum darstellt, mit einem Thermometer aktualisiert wird. Dieses Muster basiert auf den Zwillingsaktualisierungen und nicht auf der Weiterleitung von Telemetriedaten von einem IoT-Gerät. So haben Sie die Flexibilität, die zugrunde liegende Datenquelle zu ändern, ohne die Time Series Insights-Logik aktualisieren zu müssen.

1. Erstellen Sie zuerst einen Event Hub-Namespace, der Ereignisse von Ihrer Azure Digital Twins-Instanz empfängt. Sie können entweder die folgenden Azure CLI Anweisungen oder das Azure-Portal verwenden: [*Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals*](../event-hubs/event-hubs-create.md). Informationen zu Regionen mit Unterstützung von Event Hubs finden Sie unter [*Verfügbare Produkte nach Region*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Erstellen Sie einen Event Hub innerhalb des Namespace, um Änderungsereignisse für Zwillinge zu empfangen. Geben Sie einen Namen für den Event Hub an.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Erstellen Sie eine [Autorisierungsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) mit Sende- und Empfangsberechtigungen. Geben Sie einen Namen für die Regel an.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Erstellen Sie einen Azure Digital Twins-[Endpunkt](concepts-route-events.md#create-an-endpoint), der Ihren Event Hub mit Ihrer Azure Digital Twins-Instanz verknüpft.

    ```azurecli-interactive
    az dt endpoint create eventhub -n <your Azure Digital Twins instance name> --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above>
    ```

5. Erstellen Sie eine [Route](concepts-route-events.md#create-an-event-route) in Azure Digital Twins, um Ereignisse zur Aktualisierung von Zwillingen an Ihren Endpunkt zu senden. Der Filter in dieser Route erlaubt nur das Weitergeben von Nachrichten über Zwillingsaktualisierungen an Ihren Endpunkt.

    >[!NOTE]
    >Zurzeit besteht ein **bekanntes Problem** in Cloud Shell, das sich auf diese Befehlsgruppen auswirkt: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Problemlösung: Führen Sie vor Ausführung des Befehls das Cmdlet `az login` in Cloud Shell aus, oder verwenden Sie die [lokale Befehlszeilenschnittstelle](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) anstelle von Cloud Shell. Weitere Informationen hierzu finden Sie unter [*Problembehandlung: Bekannte Probleme in Azure Digital Twins*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Bevor Sie fortfahren, notieren Sie sich *Event Hubs-Namespace* und *Ressourcengruppe*, da Sie sie später in diesem Artikel zum Erstellen eines weiteren Event Hubs erneut verwenden werden.

## <a name="create-a-function-in-azure"></a>Erstellen einer Funktion in Azure

Als Nächstes verwenden Sie Azure Functions, um eine von **Event Hubs ausgelöste Funktion** innerhalb einer Funktions-App zu erstellen. Sie können die im End-to-End-Tutorial ([ *Erstellen einer End-to-End-Lösung*](./tutorial-end-to-end.md)) erstellte Funktions-App oder Ihre eigene verwenden. 

Diese Funktion konvertiert diese Zwillingsaktualisierungsereignisse von ihrer ursprünglichen Form als JSON-Patchdokumente in JSON-Objekte, die nur aktualisierte und hinzugefügte Werte von Ihren Zwillingen enthalten.

Weitere Informationen zur Verwendung von Event Hubs mit Azure Functions finden Sie unter [*Azure Event Hubs-Trigger für Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Fügen Sie in Ihrer veröffentlichten Funktions-App eine neue Funktion mit dem Namen **ProcessDTUpdatetoTSI** mit dem folgenden Code hinzu.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

>[!NOTE]
>Sie müssen die Pakete möglicherweise mithilfe des `dotnet add package`-Befehls oder des NuGet-Paket-Managers von Visual Studio Ihrem Projekt hinzufügen.

**Veröffentlichen** Sie als Nächstes die neue Azure-Funktion. Anweisungen dazu finden Sie unter [ *von Azure Functions-Apps für die Verarbeitung von Daten*](how-to-create-azure-function.md#publish-the-function-app-to-azure).

Die Funktion sendet dann die JSON-Objekte, die sie erstellt, an einen zweiten Event Hub, den Sie mit Time Series Insights verbinden. Sie erstellen diesen Event Hub im nächsten Abschnitt.

Später legen Sie auch einige Umgebungsvariablen fest, die von dieser Funktion verwendet werden, um eine Verbindung mit Ihren eigenen Event Hubs herzustellen.

## <a name="send-telemetry-to-an-event-hub"></a>Senden von Telemetriedaten an einen Event Hub

Sie erstellen jetzt einen zweiten Event Hub und konfigurieren ihre Funktion so, dass die Ausgabe an diesen Event Hub gestreamt wird. Dieser Event Hub wird dann mit Time Series Insights verbunden.

### <a name="create-an-event-hub"></a>Erstellen eines Ereignis-Hubs

Um den zweiten Event Hub zu erstellen, können Sie entweder die folgenden Azure CLI-Anweisungen oder das Azure-Portal verwenden: [*Schnellstart: Erstellen eines Event Hubs mithilfe des Azure-Portals*](../event-hubs/event-hubs-create.md).

1. Halten Sie den *Event Hubs-Namespace* und den Namen der *Ressourcengruppe* bereit, die Sie weiter oben in diesem Artikel verwendet haben.

2. Erstellen Sie einen neuen Event Hub. Geben Sie einen Namen für den Event Hub an.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Erstellen Sie eine [Autorisierungsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) mit Sende- und Empfangsberechtigungen. Geben Sie einen Namen für die Regel an.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Konfigurieren Ihrer Funktion

Als nächstes müssen Sie in ihrer Funktions-App von oben Umgebungsvariablen festlegen, die die Verbindungszeichenfolgen für die von Ihnen erstellten Event Hubs enthalten.

### <a name="set-the-twins-event-hub-connection-string"></a>Festlegen der Twins-Event Hub-Verbindungszeichenfolge

1. Rufen Sie die Twins-[Event Hub-Verbindungszeichenfolge](../event-hubs/event-hubs-get-connection-string.md) mithilfe der Autorisierungsregeln ab, die Sie zuvor für den Twins-Hub erstellt haben.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Verwenden Sie den Wert *primaryConnectionString* aus dem Ergebnis, um eine App-Einstellung in Ihrer Funktions-App zu erstellen, die Ihre Verbindungszeichenfolge enthält:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Festlegen der Time Series Insights-Event Hub-Verbindungszeichenfolge

1. Rufen Sie die TSI-[Event Hub-Verbindungszeichenfolge](../event-hubs/event-hubs-get-connection-string.md) mithilfe der Autorisierungsregeln ab, die Sie zuvor für den Time Series Insights-Hub erstellt haben:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. Verwenden Sie den Wert *primaryConnectionString* aus dem Ergebnis, um eine App-Einstellung in Ihrer Funktions-App zu erstellen, die Ihre Verbindungszeichenfolge enthält:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Erstellen und Verbinden einer Time Series Insights-Instanz

Als Nächstes richten Sie eine Time Series Insights-Instanz (TSI) ein, um die Daten aus dem zweiten Event Hub zu empfangen. Führen Sie die folgenden Schritte aus. Weitere Informationen zu diesem Prozess finden Sie im [*Tutorial: Einrichten einer Azure Time Series Insights Gen2-Umgebung*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. Beginnen Sie im Azure-Portal mit der Erstellung einer Time Series Insights-Umgebung. 
    1. Wählen Sie als Tarif **Gen2(L1)** aus.
    2. Sie müssen eine **Time Series-ID** für diese Umgebung auswählen. Ihre Time Series-ID kann bis zu drei Werte aufweisen, mit denen Sie in Time Series Insights nach Ihren Daten suchen. Für dieses Tutorial können Sie **$dtId** verwenden. Weitere Informationen zur Auswahl eines ID-Werts finden Sie unter [*Bewährte Methoden für die Auswahl einer Time Series-ID*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Die Benutzeroberfläche eines Portals zum Erstellen einer Time Series Insights-Umgebung. Wählen Sie in den Dropdownlisten Ihr Abonnement, Ihre Ressourcengruppe und den Standort und anschließend einen Namen für Ihre Umgebung aus." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Die Benutzeroberfläche eines Portals zum Erstellen einer Time Series Insights-Umgebung. Der Tarif „Gen2(L1)“ ist ausgewählt, und der Eigenschaftenname der Zeitreihen-ID lautet $dtId" lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::.

2. Klicken Sie auf **Weiter: Ereignisquelle**, und wählen Sie Ihre TSI-Event Hub-Informationen von oben aus. Sie müssen auch eine neue Event Hubs-Consumergruppe erstellen.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Die Benutzeroberfläche eines Portals zum Erstellen einer Ereignisquelle einer Time Series Insights-Umgebung. Sie erstellen eine Ereignisquelle mit den Event Hub-Informationen von oben. Außerdem erstellen Sie eine neue Consumergruppe." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Beginnen mit dem Senden von IoT-Daten an Azure Digital Twins

Um mit dem Senden von Daten an Time Series Insights zu beginnen, müssen Sie mit dem Aktualisieren der Eigenschaften des digitalen Zwillings in Azure Digital Twins mit sich ändernden Datenwerten beginnen. Verwenden Sie den Befehl [az dt twin update](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update).

Wenn Sie das End-to-End-Tutorial verwenden ([ *Erstellen einer End-to-End-Lösung*](tutorial-end-to-end.md)) beim Einrichten der Umgebung verwenden, können Sie mit dem Senden simulierter IoT-Daten beginnen, indem Sie das *DeviceSimulator*-Projekt aus dem Beispiel ausführen. Die Anweisungen finden Sie im Abschnitt [*Konfigurieren und Ausführen der Simulation*](tutorial-end-to-end.md#configure-and-run-the-simulation) des Tutorials.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisieren Ihrer Daten in Time Series Insights

Nun sollten Daten in Ihre Time Series Insights-Instanz übertragen werden, die zur Analyse bereit sind. Führen Sie die folgenden Schritte aus, um die eingehenden Daten zu untersuchen.

1. Öffnen Sie Ihre Time Series Insights-Umgebung im [Azure-Portal](https://portal.azure.com) (Sie können den Namen Ihrer Umgebung in der Suchleiste des Portals suchen). Besuchen Sie die in der Instanzübersicht angezeigte *Time Series Insights-Explorer-URL*.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Auswählen der Time Series Insights-Explorer-URL auf der Übersichtsregisterkarte der Time Series Insights-Umgebung":::

2. Im Explorer werden Ihre drei Zwillinge von Azure Digital Twins auf der linken Seite angezeigt. Wählen Sie _**thermostat67**_ und **Temperature** aus, und klicken Sie auf **Hinzufügen**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Auswählen von **thermostat67** und **temperature** und Klicken auf **Hinzufügen**":::

3. Sie sollten jetzt die anfänglichen Temperaturmesswerte Ihres Thermostats wie unten dargestellt sehen. Der gleiche Temperaturmesswert wird für *room21* und *floor1* aktualisiert, und Sie können diese Datenströme zusammen visualisieren.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Die anfänglichen Temperaturdaten werden im TSI-Explorer grafisch dargestellt. Es handelt sich um eine Linie zufälliger Werte zwischen 68 und 85.":::

4. Nach einer viel längeren Ausführung der Simulation sieht die Visualisierung etwa wie folgt aus:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Die Temperaturdaten für jeden Zwilling werden in drei parallelen Linien in unterschiedlichen Farben dargestellt.":::

## <a name="next-steps"></a>Nächste Schritte

Die digitalen Zwillinge werden standardmäßig als flache Hierarchie in Time Series Insights gespeichert, können jedoch für die Organisation mit Modellinformationen und einer Hierarchie mit mehreren Ebenen erweitert werden. Weitere Informationen zu diesem Vorgang finden Sie im 

* [*Tutorial: Definieren und Anwenden eines Modells*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Sie können eine benutzerdefinierte Logik schreiben, um diese Informationen mithilfe der bereits in Azure Digital Twins gespeicherten Modell- und Diagrammdaten automatisch bereitzustellen. Weitere Informationen zum Verwalten, Aktualisieren und Abrufen von Informationen aus dem Zwillingsgraphen finden Sie in den folgenden Referenzen:

* [*Verwenden Verwalten digitaler Zwillinge*](./how-to-manage-twin.md)
* [*Verwenden Abfragen des Zwillingsgraphen*](./how-to-query-graph.md)