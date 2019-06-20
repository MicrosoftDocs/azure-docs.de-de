---
title: Filtern und Sortieren von Azure Media Services-Entitäten sowie Einteilen der Entitäten in Seiten| Microsoft-Dokumentation
description: Dieser Artikel erörtert die Filterung, Sortierung und Paginierung von Azure Media Services-Entitäten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60732364"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Filterung, Sortierung und Paginierung von Media Services-Entitäten

Media Services unterstützt die folgenden OData-Abfrageoptionen für Media Services v3-Entitäten: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Operatorbeschreibung:

* Eq = equal to (ist gleich)
* Ne = not equal to (ist nicht gleich)
* Ge = Greater than or equal to (ist größer als oder gleich)
* Le = Less than or equal to (ist kleiner als oder gleich)
* Gt = Greater than (größer als)
* Lt = Less than (kleiner als)

Eigenschaften von Entitäten vom Datetime-Typ liegen immer im UTC-Format vor.

## <a name="page-results"></a>Seitenergebnisse

Wenn eine Abfrageantwort viele Elemente enthält, gibt der Dienst eine „\@odata.nextLink“-Eigenschaft zurück, um die nächste Seite der Ergebnisse abzurufen. Auf diese Weise kann das gesamte Resultset paginiert werden. Sie können die Seitengröße nicht konfigurieren. Die Seitengröße unterscheidet sich nach der Art der Entität, bitte entnehmen Sie Details den folgenden Einzelabschnitten.

Wenn während der Paginierung der Sammlung Entitäten erstellt oder gelöscht werden, werden die Änderungen in den zurückgegebenen Ergebnissen reflektiert (sofern sich diese Änderungen in dem Teil der Sammlung befinden, der nicht heruntergeladen wurde). 

> [!TIP]
> Verwenden Sie immer den Link „Weiter“ zum Enumerieren der Auflistung und keine bestimmte Seitengröße als Referenz.

## <a name="assets"></a>Objekte

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Filter- und Sortieroptionen auf die Eigenschaften von [Medienobjekten](https://docs.microsoft.com/rest/api/media/assets) angewendet werden können: 

|NAME|Filter|Reihenfolge|
|---|---|---|
|id|||
|name|eq, gt, lt| Aufsteigend und absteigend|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq, gt, lt| Aufsteigend und absteigend|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

Im folgenden C#-Beispiel wird nach dem Erstellungsdatum gefiltert:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Paginierung 

Die Paginierung wird für jede der vier aktivierten Sortierreihenfolgen unterstützt. Derzeit ist die Seitengröße 1000.

#### <a name="c-example"></a>C#-Beispiel

Im folgenden C#-Beispiel wird gezeigt, wie alle Medienobjekte im Konto durchlaufen werden.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>Beispiel für REST

Betrachten Sie das folgende Beispiel für die Verwendung von „$skiptoken“. Stellen Sie sicher, dass Sie *amstestaccount* durch Ihren Kontonamen ersetzen und den Wert für *api-version* auf die neueste Version festlegen.

Fordern Sie folgendermaßen eine Liste von Medienobjekten an:

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Sie erhalten eine Antwort, die etwa wie folgt aussieht:

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Danach fordern Sie die nächste Seite an, indem Sie eine get-Anforderung senden:

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

Weitere REST-Beispiele finden Sie unter [Medienobjekte – Liste](https://docs.microsoft.com/rest/api/media/assets/list).

## <a name="content-key-policies"></a>Richtlinien für Inhaltsschlüssel

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Optionen auf die Eigenschaften von [Inhaltsschlüsselrichtlinien](https://docs.microsoft.com/rest/api/media/contentkeypolicies) angewandt werden können: 

|NAME|Filter|Reihenfolge|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.created |eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.description |eq, ne, ge, le, gt, lt||
|properties.lastModified|eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.options |||
|properties.policyId|eq, ne||
|type|||

### <a name="pagination"></a>Paginierung

Die Paginierung wird für jede der vier aktivierten Sortierreihenfolgen unterstützt. Derzeit ist die Seitengröße 10.

Im folgenden C#-Beispiel wird gezeigt, wie alle **Inhaltsschlüsselrichtlinien** im Konto durchlaufen werden.

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST-Beispiele finden Sie unter [Richtlinien für Inhaltsschlüssel – Liste](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list).

## <a name="jobs"></a>Aufträge

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Optionen auf die Eigenschaften von [Aufträgen](https://docs.microsoft.com/rest/api/media/jobs) angewendet werden können: 

| NAME    | Filter                        | Reihenfolge |
|---------|-------------------------------|-------|
| name                    | eq            | Aufsteigend und absteigend|
| properties.state        | eq, ne        |                         |
| properties.created      | gt, ge, lt, le| Aufsteigend und absteigend|
| properties.lastModified | gt, ge, lt, le | Aufsteigend und absteigend| 

### <a name="pagination"></a>Paginierung

Die Paginierung von Aufträgen wird in Media Services v3 unterstützt.

Im folgenden C#-Beispiel wird gezeigt, wie alle Aufträge im Konto durchlaufen werden.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

Beispiele zu REST finden Sie unter [Aufträge – Liste](https://docs.microsoft.com/rest/api/media/jobs/list).

## <a name="streaming-locators"></a>Streaminglocators

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Optionen auf die Eigenschaften des Streaminglocators angewendet werden können: 

|NAME|Filter|Reihenfolge|
|---|---|---|
|id |||
|name|eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>Paginierung

Die Paginierung wird für jede der vier aktivierten Sortierreihenfolgen unterstützt. Derzeit ist die Seitengröße 10.

Das folgende C#-Beispiel zeigt, wie alle Streaminglocators im Konto enumeriert werden.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST-Beispiele finden Sie unter [Streaminglocators – Liste](https://docs.microsoft.com/rest/api/media/streaminglocators/list).

## <a name="streaming-policies"></a>Streamingrichtlinien

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Optionen auf die StreamingPolicy-Eigenschaften angewendet werden können: 

|NAME|Filter|Reihenfolge|
|---|---|---|
|id|||
|name|eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq, ne, ge, le, gt, lt|Aufsteigend und absteigend|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>Paginierung

Die Paginierung wird für jede der vier aktivierten Sortierreihenfolgen unterstützt. Derzeit ist die Seitengröße 10.

Im folgenden C#-Beispiel wird gezeigt, wie alle StreamingPolicies im Konto durchlaufen werden.

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST-Beispiele finden Sie unter [Streamingrichtlinien – Liste](https://docs.microsoft.com/rest/api/media/streamingpolicies/list).

## <a name="transform"></a>Transformieren

### <a name="filteringordering"></a>Filterung/Sortierung

Die folgende Tabelle zeigt, wie diese Optionen auf die Eigenschaften von [Transformationen](https://docs.microsoft.com/rest/api/media/transforms) angewendet werden können: 

| NAME    | Filter                        | Reihenfolge |
|---------|-------------------------------|-------|
| name                    | eq            | Aufsteigend und absteigend|
| properties.created      | gt, ge, lt, le| Aufsteigend und absteigend|
| properties.lastModified | gt, ge, lt, le | Aufsteigend und absteigend|

## <a name="next-steps"></a>Nächste Schritte

[Streamen einer Datei](stream-files-dotnet-quickstart.md)
