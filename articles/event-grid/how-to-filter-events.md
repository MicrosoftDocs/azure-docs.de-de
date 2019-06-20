---
title: Filtern von Ereignissen für Azure Event Grid
description: Erläutert das Erstellen von Azure Event Grid-Abonnements, die Ereignisse filtern.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: spelluru
ms.openlocfilehash: 5bb95b80e12c818641e2be2b929cdfd01f8f5b5c
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304227"
---
# <a name="filter-events-for-event-grid"></a>Filtern von Ereignissen für Event Grid

Dieser Artikel zeigt, wie Sie Ereignisse beim Erstellen eines Event Grid-Abonnements filtern können. Weitere Informationen zu den Optionen für die Ereignisfilterung finden Sie unter [Grundlegendes zur Ereignisfilterung für Event Grid-Abonnements](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtern nach Ereignistyp

Beim Erstellen eines Event Grid-Abonnements können Sie angeben, welche [Ereignistypen](event-schema.md) an den Endpunkt gesendet werden sollen. Die Beispiele in diesem Abschnitt erstellen Ereignisabonnements für eine Ressourcengruppe, beschränken jedoch die Ereignisse, die an `Microsoft.Resources.ResourceWriteFailure` und `Microsoft.Resources.ResourceWriteSuccess` gesendet werden. Wenn Sie mehr Flexibilität beim Filtern von Ereignissen nach Ereignistypen benötigen, lesen Sie „Filtern nach erweiterten Operatoren und Datenfeldern“.

Verwenden Sie für PowerShell den Parameter `-IncludedEventType` beim Erstellen des Abonnements.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

Verwenden Sie für die Azure CLI den Parameter `--included-event-types`. Im folgenden Beispiel wird die Azure-CLI in einer Bash-Shell verwendet:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Verwenden Sie für eine Resource Manager-Vorlage, die Eigenschaft `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtern nach Betreff

Sie können Ereignisse nach dem Betreff in den Ereignisdaten filtern. Sie können einen Wert angeben, der dem Anfang oder Ende des Betreffs entspricht. Wenn Sie mehr Flexibilität beim Filtern von Ereignissen nach Betreff benötigen, lesen Sie „Filtern nach erweiterten Operatoren und Datenfeldern“.

Im folgenden PowerShell-Beispiel erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit dem Parameter `-SubjectBeginsWith` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Im nächste PowerShell-Beispiel wird ein Abonnement für einen Blob Storage erstellt. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

Im folgenden Azure CLI-Beispiel erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit dem Parameter `--subject-begins-with` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Das nächste Azure CLI-Beispiel erstellt ein Abonnement für einen Blob Storage. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

Im folgenden Beispiel für eine Resource Manager-Vorlage erstellen Sie ein Ereignisabonnement, das nach dem Anfang des Betreffs filtert. Mit der Eigenschaft `subjectBeginsWith` können Sie Ereignisse auf solche für eine bestimmte Ressource beschränken. Sie übergeben die Ressourcen-ID einer Netzwerksicherheitsgruppe.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

Das nächste Beispiel für eine Resource Manager-Vorlage erstellt ein Abonnement für einen Blob Storage. Es begrenzt die Ereignisse auf die, deren Betreff auf `.jpg` endet.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtern nach Operatoren und Daten

Um bei der Filterung mehr Flexibilität zu erhalten, können Sie Ereignisse nach Operatoren und Dateneigenschaften filtern.

### <a name="subscribe-with-advanced-filters"></a>Abonnieren mit erweiterten Filtern

Weitere Informationen zu den Operatoren und den Schlüsseln, die Sie für die erweiterte Filterung verwenden können, finden Sie unter [Erweiterte Filterung](event-filtering.md#advanced-filtering).

Diese Beispiele erstellen ein benutzerdefiniertes Thema. Sie abonnieren das benutzerdefinierte Thema und filtern nach einem Wert im Datenobjekt. Ereignisse, bei denen die Farbeigenschaft auf „Blau“, „Rot“ oder „Grün“ eingestellt ist, werden an das Abonnement gesendet.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Beachten Sie, dass für das Abonnement ein [Ablaufdatum](concepts.md#event-subscription-expiration) festgelegt ist.

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Testen der Filter

Um den Filter zu testen, senden Sie ein Ereignis, bei dem im Farbfeld „Grün“ festgelegt ist. Da „Grün“ einer der Werte im Filter ist, wird das Ereignis an den Endpunkt übermittelt.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Um ein Szenario zu testen, in dem das Ereignis nicht gesendet wird, senden Sie ein Ereignis, bei dem das Farbfeld auf „Gelb“ gesetzt ist. Gelb ist nicht einer der im Abonnement angegebenen Werte, sodass das Ereignis nicht an Ihr Abonnement gesendet wird.

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
Verwenden Sie für PowerShell Folgendes:

```azurepowershell-interactive
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Überwachung von Ereignisübermittlungen finden Sie unter [Überwachen der Event Grid-Nachrichtenübermittlung](monitor-event-delivery.md).
* Weitere Informationen zum Authentifizierungsschlüssel finden Sie unter [Event Grid – Sicherheit und Authentifizierung](security-authentication.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
