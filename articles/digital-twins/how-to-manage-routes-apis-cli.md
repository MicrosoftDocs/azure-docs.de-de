---
title: Verwalten von Endpunkten und Routen (APIs und CLI)
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Endpunkte und Ereignisrouten für Azure Digital Twins-Daten einrichten und verwalten.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d25a429873ccf8b546c0919456c97e64445f184c
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071697"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Verwalten von Endpunkten und Routen in Azure Digital Twins (APIs und CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

In Azure Digital Twins können Sie [Ereignisbenachrichtigungen](how-to-interpret-event-data.md) an Downstreamdienste oder verbundene Computeressourcen weiterleiten. Dies erfolgt, indem zunächst **Endpunkte** eingerichtet werden, die die Ereignisse empfangen können. Anschließend können Sie [**Ereignisrouten**](concepts-route-events.md) erstellen, die angeben, welche von Azure Digital Twins generierten Ereignisse an welche Endpunkte übermittelt werden.

In diesem Artikel wird das Erstellen von Endpunkten und Routen mit den [REST-APIs](/rest/api/azure-digitaltwins/), dem [.NET SDK (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) und der [Azure Digital Twins-CLI](how-to-use-cli.md) ausführlich erläutert.

Alternativ können Sie auch Endpunkte und Routen mit dem [Azure-Portal](https://portal.azure.com) verwalten. Eine Version dieses Artikels, die stattdessen das Portal verwendet, finden Sie unter [*Vorgehensweise: Verwalten von Endpunkten und Routen (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein **Azure-Konto** (Sie können [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ein kostenloses Konto einrichten).
- Sie benötigen eine **Azure Digital Twins-Instanz** in Ihrem Azure-Abonnement. Falls Sie noch keine Instanz besitzen, können Sie zum Erstellen die Schritte unter [*Vorgehensweise: Einrichten einer Instanz und von Authentifizierung*](how-to-set-up-instance-cli.md) verwenden. Notieren Sie sich die folgenden Werte aus dem Setup, um sie später in diesem Artikel zu verwenden:
    - Instanzname
    - Ressourcengruppe

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Erstellen eines Endpunkts für Azure Digital Twins

Dies sind die unterstützten Typen von Endpunkten, die Sie für Ihre Instanz erstellen können:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Weitere Informationen zu den verschiedenen Endpunkttypen finden Sie unter [*Wählen zwischen Azure-Messagingdiensten*](../event-grid/compare-messaging-services.md).

In diesem Abschnitt wird erläutert, wie diese Endpunkte mithilfe der Azure CLI erstellt werden. Sie können Endpunkte auch mit den [DigitalTwinsEndpoint-Steuerungsebenen-APIs](/rest/api/digital-twins/controlplane/endpoints) verwalten.

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Erstellen des Endpunkts

Sobald Sie die Endpunktressourcen erstellt haben, können Sie sie für einen Azure Digital Twins-Endpunkt verwenden. In den folgenden Beispielen wird gezeigt, wie Endpunkte mithilfe des Befehls [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) für die [Azure Digital Twins-CLI](how-to-use-cli.md) erstellt werden. Ersetzen Sie die Platzhalter in den Befehlen durch die Details Ihrer eigenen Ressourcen.

Erstellen eines Event Grid-Endpunkts:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Erstellen eines Event Hubs-Endpunkts (schlüsselbasierte Authentifizierung):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Erstellen eines Service Bus-Themenendpunkts (schlüsselbasierte Authentifizierung):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Nachdem Sie diese Befehle erfolgreich ausgeführt haben, steht das Event Grid-, Event Hub- oder Service Bus-Thema mit dem von Ihnen mit dem `--endpoint-name`-Argument angegebenen Namen als Endpunkt in Azure Digital Twins zur Verfügung. Normalerweise verwenden Sie diesen Namen als Ziel einer **Ereignisroute**, die Sie [später in diesem Artikel](#create-an-event-route) erstellen.

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Erstellen eines Endpunkts mit identitätsbasierter Authentifizierung

Sie können auch einen Endpunkt mit identitätsbasierter Authentifizierung erstellen, um den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) zu verwenden. Diese Option ist nur für Event Hub- und Service Bus-Endpunkte verfügbar (sie wird nicht für Event Grid unterstützt).

Den CLI-Befehl zum Erstellen dieser Art von Endpunkt finden Sie unten. Sie benötigen die folgenden Werte, um die Platzhalter im Befehl einzuschließen:
* die Azure-Ressourcen-ID Ihrer Azure Digital Twins-Instanz
* einen Endpunktnamen
* einen Endpunkttyp
* den Namespace der Endpunktressource
* den Namen des Event Hub- oder Service Bus-Themas
* den Speicherort Ihrer Azure Digital Twins-Instanz

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Erstellen eines Endpunkts mit unzustellbaren Nachrichten

Wenn ein Endpunkt innerhalb eines bestimmten Zeitraums oder nach einer bestimmten Anzahl von Übermittlungsversuchen nicht übermittelt werden kann, kann Event Grid das nicht übermittelte Ereignis an ein Speicherkonto senden. Dieser Prozess wird als Speicherung **unzustellbarer Nachrichten** bezeichnet.

Endpunkte mit aktivierter Funktion für unzustellbare Nachrichten können mit der [Azure Digital Twins-CLI](how-to-use-cli.md) oder mit [Steuerungsebenen-APIs](how-to-use-apis-sdks.md#overview-control-plane-apis) eingerichtet werden.

Weitere Informationen zu unzustellbaren Nachrichten finden Sie unter [*Konzepte: Ereignisrouten*](concepts-route-events.md#dead-letter-events). Anweisungen zum Einrichten eines Endpunkts mit unzustellbaren Nachrichten finden Sie im Rest dieses Abschnitts.

#### <a name="set-up-storage-resources"></a>Einrichten von Speicherressourcen

Wenn Sie den Speicherort für unzustellbare Nachrichten festlegen möchten, benötigen Sie ein [Speicherkonto](../storage/common/storage-account-create.md?tabs=azure-portal) mit einem [Containersetup](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) in Ihrem Azure-Konto. 

Später geben Sie den URI für diesen Container an, wenn Sie den Endpunkt erstellen. Die Position der unzustellbaren Nachrichten wird dem Endpunkt als Container-URI mit einem [SAS-Token](../storage/common/storage-sas-overview.md) bereitgestellt. Dieses Token benötigt die `write`-Berechtigung für den Zielcontainer innerhalb des Speicherkontos. Der vollständig formatierte **SAS-URI für unzustellbare Nachrichten** weist das Format `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` auf.

Führen Sie die folgenden Schritte aus, um diese Speicherressourcen in Ihrem Azure-Konto einzurichten, um so die Einrichtung der Endpunktverbindung im nächsten Abschnitt vorzubereiten.

1. Befolgen Sie die Schritte in [*Erstellen eines Speicherkontos*](../storage/common/storage-account-create.md?tabs=azure-portal), um ein **Speicherkonto** in Ihrem Azure-Abonnement zu erstellen. Notieren Sie den Namen des Speicherkontos zur späteren Verwendung.
2. Befolgen Sie die Schritte in [*Erstellen eines Containers*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container), um einen **Container** innerhalb des neuen Speicherkontos zu erstellen. Notieren Sie sich den Containernamen, um ihn später zu verwenden.
3. Erstellen Sie als nächstes ein **SAS-Token** für Ihr Speicherkonto, das der Endpunkt für den Zugriff darauf verwenden kann. Navigieren Sie zunächst zu Ihrem Speicherkonto im [Azure-Portal](https://ms.portal.azure.com/#home) (Sie finden es über die Suchleiste im Portal).
4. Wählen Sie auf der Speicherkontoseite den Link _Shared Access Signature_ in der linken Navigationsleiste, um mit der Einrichtung des SAS-Tokens zu beginnen.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Speicherkontoseite im Azure-Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. Wählen Sie auf der Seite *Shared Access Signature* unter *Zulässige Dienste* und *Zulässige Ressourcentypen* die gewünschten Einstellungen aus. Sie müssen mindestens ein Feld in jeder Kategorie auswählen. Wählen Sie unter *Zulässige Berechtigungen* die Option **Schreiben** aus (Sie können auch andere Berechtigungen auswählen).
1. Stellen Sie für die übrigen Einstellungen die gewünschten Werte ein.
1. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche _SAS und Verbindungszeichenfolge generieren_, um das SAS-Token zu generieren. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Seite „Speicherkonto“ im Azure-Portal, die die gesamte Einstellungsauswahl zur Generierung eines SAS-Tokens zeigt und die Schaltfläche „SAS und Verbindungszeichenfolge generieren“ hervorhebt" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Hierdurch werden im unteren Bereich der gleichen Seite unter der Einstellungsauswahl mehrere Werte für die SAS und Verbindungszeichenfolgen generiert. Scrollen Sie nach unten, um die Werte anzuzeigen, und klicken Sie auf das Symbol *In Zwischenablage kopieren*, um den Wert des **SAS-Tokens** zu kopieren. Speichern Sie ihn zur späteren Verwendung.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Kopieren des SAS-Tokens zum Verwenden des Geheimnisses für unzustellbare Nachrichten" lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Erstellen des Endpunkts für unzustellbare Nachrichten

Fügen Sie die folgenden Parameter für unzustellbare Nachrichten zum Befehl [az dt endpoint create](/cli/azure/ext/azure-iot/dt/endpoint/create?view=azure-cli-latest&preserve-view=true) für die [Azure Digital Twins-CLI](how-to-use-cli.md) hinzu, um einen Endpunkt mit aktivierter Funktion für unzustellbare Nachrichten zu erstellen.

Der Wert für den Parameter ist der **SAS-URI für unzustellbare Nachrichten**, der aus dem Speicherkontonamen, dem Containernamen und dem SAS-Token besteht, die Sie im [vorherigen Abschnitt](#set-up-storage-resources) gesammelt haben. Dieser Parameter erstellt den Endpunkt mit der schlüsselbasierten Authentifizierung.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Fügen Sie diesen Parameter am Ende des Befehls zum Erstellen des Endpunkts aus dem Abschnitt [*Erstellen des Endpunkts*](#create-the-endpoint) hinzu, um einen Endpunkt des gewünschten Typs zu erstellen, für den die Funktion für unzustellbare Nachrichten aktiviert ist.

Alternativ können Sie Endpunkte für unzustellbare Nachrichten mithilfe der [Azure Digital Twins-Steuerungsebenen-APIs](how-to-use-apis-sdks.md#overview-control-plane-apis) anstelle der CLI verwenden. Lesen Sie hierzu die [DigitalTwinsEndpoint-Dokumentation](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate), um herauszufinden, wie Sie die Anforderung strukturieren und die Parameter für unzustellbare Nachrichten hinzufügen.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Erstellen eines Endpunkts für unzustellbare Nachrichten mit identitätsbasierter Authentifizierung

Sie können auch einen Endpunkt für unzustellbare Nachrichten mit identitätsbasierter Authentifizierung erstellen, um den Endpunkt mit einer [verwalteten Identität](concepts-security.md#managed-identity-for-accessing-other-resources-preview) zu verwenden. Diese Option ist nur für Event Hub- und Service Bus-Endpunkte verfügbar (sie wird nicht für Event Grid unterstützt).

Verwenden Sie zum Erstellen dieser Art von Endpunkt denselben CLI-Befehl wie oben, um einen [Endpunkt mit identitätsbasierter Authentifizierung](#create-an-endpoint-with-identity-based-authentication) mit einem zusätzlichen Feld in den JSON-Nutzdaten für einen `deadLetterUri` zu erstellen.

Im Folgenden finden Sie die Werte, die Sie benötigen, um die Platzhalter im Befehl einzuschließen:
* die Azure-Ressourcen-ID Ihrer Azure Digital Twins-Instanz
* einen Endpunktnamen
* einen Endpunkttyp
* den Namespace der Endpunktressource
* den Namen des Event Hub- oder Service Bus-Themas
* Details zum **SAS-URI für unzustellbare Nachrichten**: Speicherkontoname, Containername
* den Speicherort Ihrer Azure Digital Twins-Instanz

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Nachrichtenspeicherschema

Sobald der Endpunkt für unzustellbare Nachrichten eingerichtet ist, werden unzustellbare Nachrichten im folgenden Format in Ihrem Speicherkonto gespeichert:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

Unzustellbare Nachrichten entsprechen dem Schema des ursprünglichen Ereignisses, das an den ursprünglichen Endpunkt übermittelt werden sollte.

Im Folgenden finden Sie ein Beispiel für eine unzustellbare Nachricht für eine [Benachrichtigung zur Erstellung eines Zwillings](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Erstellen einer Ereignisroute

**Voraussetzung**: Sie müssen wie weiter oben in diesem Artikel beschrieben Endpunkte erstellen, bevor Sie mit dem Erstellen einer Route fortfahren können. Nachdem die Einrichtung Ihrer Endpunkte abgeschlossen ist, können Sie mit dem Erstellen einer Ereignisroute fortfahren.

> [!NOTE]
> Wenn Sie Ihre Endpunkte gerade erst bereitgestellt haben, vergewissern Sie sich, dass die Bereitstellung abgeschlossen ist, **bevor** Sie versuchen, die Endpunkte für eine neue Ereignisroute zu verwenden. Wenn die Routenbereitstellung fehlschlägt, weil die Endpunkte noch nicht bereit sind, warten Sie einige Minuten, und versuchen Sie es dann erneut.
>
> Wenn Sie eine Skripterstellung für diesen Flow durchführen, sollten Sie dies berücksichtigen, indem Sie eine Wartezeit von zwei bis drei Minuten vorsehen, damit der Endpunktdienst die Bereitstellung abschließen kann, bevor mit der Routeneinrichtung fortgefahren wird.

Wenn Sie tatsächlich Daten von Azure Digital Twins an einen Endpunkt senden möchten, müssen Sie eine **Ereignisroute** definieren. Ereignisrouten werden verwendet, um Ereignisflows im gesamten System zu verknüpfen und Dienste nachzuschalten.

Eine Routendefinition kann folgende Elemente enthalten:
* Den Routennamen, den Sie verwenden möchten.
* Den Namen des Endpunkts, den Sie verwenden möchten.
* Ein Filter, der definiert, welche Ereignisse an den Endpunkt gesendet werden. 

Wenn es keinen Routennamen gibt, werden keine Nachrichten außerhalb von Azure Digital Twins weitergeleitet. Wenn es einen Routennamen gibt und der Filter `true` ergibt, werden alle Nachrichten an den Endpunkt weitergeleitet. Wenn es einen Routennamen gibt und ein anderer Filter hinzugefügt wird, werden Nachrichten auf der Grundlage des Filters gefiltert.

Eine Route sollte es ermöglichen, mehrere Benachrichtigungen und Ereignistypen auszuwählen. 

Ereignisrouten können mit den [**EventRoutes**-Datenebenen-APIs](/rest/api/digital-twins/dataplane/eventroutes) für Azure Digital Twins oder den [**az dt route**-CLI-Befehlen](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true) erstellt werden. Der Rest dieses Abschnitts führt Sie durch den Erstellungsprozess.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Erstellen von Routen mit den APIs und dem C#-SDK

Eine Möglichkeit zum Definieren von Ereignisrouten sind die [Datenebenen-APIs](how-to-use-apis-sdks.md#overview-data-plane-apis). Die Beispiele in diesem Abschnitt verwenden das [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true).

`CreateOrReplaceEventRouteAsync` ist der SDK-Aufruf, der zum Hinzufügen einer Ereignisroute verwendet wird. Hier ist ein Beispiel für seine Verwendung:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Alle SDK-Funktionen sind in synchronen und asynchronen Versionen enthalten.

#### <a name="event-route-sample-sdk-code"></a>Beispielcode für Ereignisroute mit SDK

Die folgende Beispielmethode zeigt, wie Sie eine Ereignisroute mit dem C#-SDK erstellen, auflisten und löschen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Erstellen von Routen mit der CLI

Routen können auch mithilfe der [az dt route](/cli/azure/ext/azure-iot/dt/route?view=azure-cli-latest&preserve-view=true)-Befehle für die Azure Digital Twins-CLI verwaltet werden. 

Weitere Informationen zur Verwendung der CLI und zu den verfügbaren Befehlen finden Sie unter [*Vorgehensweise: Verwenden der Azure Digital Twins-Befehlszeilenschnittstelle*](how-to-use-cli.md).

## <a name="filter-events"></a>Filtern von Ereignissen

Ohne Filterung erhalten die Endpunkte eine Vielzahl von Ereignissen von Azure Digital Twins:
* Telemetrie, die von [digitalen Zwillingen](concepts-twins-graph.md) unter Verwendung der Azure Digital Twins-Dienst-API ausgelöst wird.
* Änderungsbenachrichtigungen für Zwillingseigenschaften, ausgelöst bei Eigenschaftsänderungen für einen Zwilling in der Azure Digital Twins-Instanz.
* Lebenszyklusereignisse, die ausgelöst werden, wenn Zwillinge oder Beziehungen erstellt oder gelöscht werden.

Sie können die gesendeten Ereignisse einschränken, indem Sie der Ereignisroute einen **Filter** für einen Endpunkt hinzufügen.

Um einen Filter hinzuzufügen, können Sie eine PUT-Anforderung für *https://{Your-azure-digital-twins-hostname}/eventRoutes/{event-route-name}?api-version=2020-10-31* mit folgendem Textkörper verwenden:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Dies sind die unterstützten Routenfilter. Verwenden Sie die Details in der Spalte *Filtertextschema*, um den Platzhalter `<filter-text>` im Anforderungstext oben zu ersetzen.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über die verschiedenen Arten von Ereignismeldungen, die Sie erhalten können:
* [*Gewusst wie: Interpretieren von Ereignisdaten*](how-to-interpret-event-data.md)
