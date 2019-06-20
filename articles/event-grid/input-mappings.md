---
title: Zuordnen benutzerdefinierter Felder zum Azure Event Grid-Schema
description: Dieser Artikel beschreibt, wie Sie ein benutzerdefiniertes Schema in das Azure Event Grid-Schema konvertieren.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: a0e054be3ab7d4818ac323eb5fb93968f57eca4f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60565496"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Zuordnen benutzerdefinierter Felder zum Event Grid-Schema

Auch wenn Ihre Daten nicht dem erwarteten [Event Grid-Schema](event-schema.md) entsprechen, können Sie dennoch Event Grid verwenden, um Ereignisse an Abonnenten weiterzuleiten. Dieser Artikel beschreibt, wie Sie Ihr Schema dem Event Grid-Schema zuordnen.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Installieren des Vorschaufeatures

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Ursprüngliches Ereignisschema

Angenommen, Sie verfügen über eine Anwendung, die Ereignisse in folgendem Format sendet:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Obwohl dieses Format nicht dem erforderlichen Schema entspricht, ermöglicht Event Grid es Ihnen, Ihre Felder dem Schema zuzuordnen. Alternativ dazu können Sie die Werte auch im ursprünglichen Schema empfangen.

## <a name="create-custom-topic-with-mapped-fields"></a>Erstellen eines benutzerdefinierten Themas mit zugeordneten Feldern

Wenn Sie ein benutzerdefiniertes Thema erstellen, geben Sie an, wie Felder aus Ihrem ursprünglichen Ereignis dem Event Grid-Schema zugeordnet werden sollen. Zum Anpassen der Zuordnung werden drei Werte verwendet:

* Mit dem Wert **input schema** wird der Typ des Schemas angegeben. Die verfügbaren Optionen sind das CloudEvents-Schema, das benutzerdefinierte Ereignisschema und das Event Grid-Schema. Der Standardwert ist das Event Grid-Schema. Wenn Sie eine benutzerdefinierte Zuordnung zwischen Ihrem Schema und dem Event Grid-Schema erstellen, verwenden Sie das benutzerdefinierte Ereignisschema (customeventschema). Wenn Ereignisse im CloudEvents-Schema vorhanden sind, verwenden Sie das CloudEvents-Schema.

* Mit der **mapping default values**-Eigenschaft werden die Standardwerte für Felder im Event Grid-Schema angegeben. Sie können Standardwerte für `subject`, `eventtype` und `dataversion` festlegen. In der Regel verwenden Sie diesen Parameter, wenn Ihr benutzerdefiniertes Schema kein Feld enthält, das einem dieser drei Felder entspricht. Sie können z.B. angeben, dass die Dataversion immer auf **1.0** festgelegt wird.

* Mit dem Wert **mapping fields** werden Felder Ihres Schemas dem Event Grid-Schema zugeordnet. Werte werden in mit Leerzeichen getrennten Schlüssel-Wert-Paaren angegeben. Verwenden Sie als Schlüsselnamen den Namen des Event Grid-Felds. Als Wert verwenden Sie den Namen Ihres Felds. Sie können Schlüsselnamen für `id`, `topic`, `eventtime`, `subject`, `eventtype` und `dataversion` verwenden.

Verwenden Sie Folgendes, um mit der Azure CLI ein benutzerdefiniertes Thema zu erstellen:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Abonnieren eines Event Grid-Themas

Wenn Sie das benutzerdefinierte Thema abonnieren, geben Sie das Schema an, das Sie für den Empfang der Ereignisse verwenden möchten. Sie geben das CloudEvents-Schema, benutzerdefinierte Ereignisschema oder Event Grid-Schema an. Der Standardwert ist das Event Grid-Schema.

Im folgenden Beispiel wird ein Event Grid-Thema abonniert und das Event Grid-Schema verwendet. Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

Das nächste Beispiel verwendet das Eingabeschema des Ereignisses:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Im folgenden Beispiel wird ein Event Grid-Thema abonniert und das Event Grid-Schema verwendet. Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

Das nächste Beispiel verwendet das Eingabeschema des Ereignisses:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Veröffentlichen des Ereignisses im Thema

Sie können jetzt ein Ereignis an das benutzerdefinierte Thema senden und die Ergebnisse der Zuordnung anzeigen. Das folgende Skript postet ein Ereignis im [Beispielschema](#original-event-schema):

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Sehen Sie sich jetzt Ihren WebHook-Endpunkt an. Die beiden Abonnements haben Ereignisse in verschiedenen Schemas übermittelt.

Das erste Abonnement hat das Event Grid-Schema verwendet. Das übermittelte Ereignis liegt in folgendem Format vor:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Diese Felder enthalten die Zuordnungen aus dem benutzerdefinierten Thema. **myEventTypeField** ist **EventType** zugeordnet. Es werden die Standardwerte für **DataVersion** und **Subject** verwendet. Das **Data**-Objekt enthält die ursprünglichen Ereignisschemafelder.

Das zweite Abonnement verwendet das Eingabeereignisschema. Das übermittelte Ereignis liegt in folgendem Format vor:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Beachten Sie, dass die ursprünglichen Felder übermittelt wurden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Ereignisübermittlungen und Wiederholungen finden Sie unter [Event Grid – Übermittlung und Wiederholung von Nachrichten](delivery-and-retry.md).
* Eine Einführung in Event Grid finden Sie unter [Informationen zu Event Grid](overview.md).
* Um sich schnell mit der Verwendung von Event Grid vertraut zu machen, lesen Sie [Erstellen und Weiterleiten benutzerdefinierter Ereignisse mit Azure Event Grid](custom-event-quickstart.md).
