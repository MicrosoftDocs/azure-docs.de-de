ConvertTo-SecureString -String---
title: "Azure Monitoring-REST-API – exemplarische Vorgehensweise | Microsoft-Dokumentation"
description: "Informationen zum Authentifizieren von Anforderungen und Verwenden der Azure Monitor-REST-API, um verfügbare Metrikdefinitionen und Metrikwerte abzurufen."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.openlocfilehash: 357a63c65a4f6864dca259aad8a76f83681cd501
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2017
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Exemplarische Vorgehensweise für die Azure Monitoring-REST-API
In diesem Artikel erfahren Sie, wie Sie die Authentifizierungen so durchführen, dass Ihr Code die [REST-API-Referenz für Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx)nutzen kann.         

Mit der Azure Monitor-API können Sie die verfügbaren Standardmetrikdefinitionen, Granularität und Metrikwerte programmgesteuert abrufen. Die Daten können in einem separaten Datenspeicher abgelegt werden, z.B. in Azure SQL-Datenbank, Azure Cosmos DB oder Azure Data Lake. Von dort aus können ggf. weitere Analysen durchgeführt werden.

Außer dem Arbeiten mit verschiedenen Metrikdatenpunkten können Sie mit der Monitor-API auch Warnungsregeln auflisten, Aktivitätsprotokolle anzeigen und vieles mehr. Eine vollständige Liste aller verfügbaren Vorgänge finden Sie in der [REST-API-Referenz für Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Authentifizieren von Azure Monitor-Anforderungen
Zunächst muss die Anforderung authentifiziert werden.

Alle für die Azure Monitor-API ausgeführten Aufgaben verwenden das Azure Resource Manager-Authentifizierungsmodell. Daher müssen alle Anforderungen mithilfe von Azure Active Directory (Azure AD) authentifiziert werden. Ein Ansatz für die Authentifizierung der Clientanwendung ist die Erstellung eines Azure AD-Dienstprinzipals und das Abrufen des Authentifizierungstokens (JWT). Das folgende Beispielskript zeigt, wie ein Azure AD-Dienstprinzipal mithilfe von PowerShell erstellt werden kann. Eine ausführlichere Anleitung finden Sie unter [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). Es ist außerdem möglich, [einen Dienstprinzipal über das Azure-Portal zu erstellen](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = ConvertTo-SecureString -String "{service-principal-password}" -AsPlainText -Force

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Für die Abfrage der Azure Monitor-API sollte die Clientanwendung für die Authentifizierung den vorher erstellten Dienstprinzipal verwenden. Das folgende beispielhafte PowerShell-Skript zeigt einen Ansatz, wie die [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) verwendet werden kann, um das JWT-Authentifizierungstoken zu erhalten. Das JWT-Token wird als Teil eines HTTP-Autorisierungsparameters bei Anforderungen an die Azure Monitor-REST-API weitergegeben.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Nach der Authentifizierung können Abfragen für die Azure Monitor-REST-API ausgeführt werden. Es stehen zwei hilfreiche Abfragen zur Verfügung:

1. Auflisten der Metrikdefinitionen für eine Ressource
2. Abrufen der Metrikwerte

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Abrufen von Metrikdefinitionen (mehrdimensionale API)

Verwenden Sie die [Azure Monitor-REST-API für Metrikdefinitionen](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) für den Zugriff auf die Liste von Metriken, die für einen Dienst verfügbar sind.

**Methode**: GET

**Anforderungs-URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Für das Abrufen von Metrikdefinitionen für ein Azure Storage-Konto würde die Anforderung folgendermaßen aussehen:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Zum Abrufen der Metrikdefinitionen mithilfe der REST-API der Azure Monitor-Metriken verwenden Sie die API-Version „2017-05-01-preview“.
>
>

Der resultierende JSON-Antworttext ähnelt folgendem Beispiel: (Beachten Sie, dass die zweite Metrik über Dimensionen verfügt)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Abrufen der Dimensionswerte (mehrdimensionale API)
Nachdem die verfügbaren Metrikdefinitionen bekannt sind, gibt es möglicherweise einige Metriken mit Dimensionen. Bevor Sie die Metrik abfragen, sollten Sie den Bereich der Werte einer Dimension ermitteln. Basierend auf diesen Dimensionswerten können Sie dann die Metriken filtern oder segmentieren, die auf Dimensionswerten basieren, während Sie Metriken abfragen. Für sämtliche Filteranforderungen verwenden Sie den Metriknamen „value“ (nicht „localizedValue“; z.B. zum Abrufen der Metrikdatenpunkte „CpuTime“ und „Requests“). Werden keine Filter verwendet, wird die Standardmetrik zurückgegeben.

> [!NOTE]
> Zum Abrufen der Dimensionswerte mithilfe der Azure Monitor-REST-API verwenden Sie die API-Version „2017-05-01-preview“.
>
>

**Methode**: GET

**Anforderungs-URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&amp;timespan=*{starttime/endtime}*&amp;$filter=*{filter}*&amp;resultType=metadata&amp;api-version=*{apiVersion}*

Zum Abrufen der Liste potenzieller Werte der Dimension „API-Namen“ für die Metrik „Transaktionen“ während eines angegebenen Zeitbereichs würde die Anforderung beispielsweise folgendermaßen aussehen:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Der resultierende JSON-Antworttext ähnelt folgendem Beispiel:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Abrufen von Metrikwerten (mehrdimensionale API)
Sobald die verfügbaren Metrikdefinitionen und alle möglichen Dimensionswerte bekannt sind, können die entsprechenden Metrikwerte abgerufen werden. Verwenden Sie den Metriknamen „value“ (nicht „localizedValue“) für sämtliche Filteranforderungen. Wenn keine Dimensionsfilter angegeben werden, wird die zurückgesetzte aggregierte Metrik zurückgegeben.

> [!NOTE]
> Zum Abrufen der mehrdimensionalen Metrikwerte mithilfe der Azure Monitor-REST-API verwenden Sie die API-Version „2017-05-01-preview“.
>
>

**Methode**: GET

**Anforderungs-URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&amp;timespan=*{starttime/endtime}*&amp;$filter=*{filter}*&amp;interval=*{timeGrain}*&amp;aggregation=*{aggreation}*&amp;api-version=*{apiVersion}*

Zum Abrufen der Metrikwerte der Speichermetrik „Transaktionen“ für alle Transaktionen zum API-Namen „GetBlobProperties“ während eines Zeitraums von fünf Minuten würde die Anfrage beispielsweise folgendermaßen aussehen:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Der resultierende JSON-Antworttext ähnelt folgendem Beispiel:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Abrufen der Metrikdefinitionen
Verwenden Sie die [Azure Monitor-REST-API für Metrikdefinitionen](https://msdn.microsoft.com/library/mt743621.aspx) für den Zugriff auf die Liste von Metriken, die für einen Dienst verfügbar sind.

**Methode**: GET

**Anforderungs-URI**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Für das Abrufen von Metrikdefinitionen für eine Azure-Logik-App würde die Anforderung folgendermaßen aussehen:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Zum Abrufen der Metrikdefinitionen mithilfe der Azure Monitor-REST-API verwenden Sie die API-Version 2016-03-01.
>
>

Der resultierende JSON-Antworttext ähnelt folgendem Beispiel:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Weitere Informationen finden Sie in der Dokumentation zum [Auflisten der Metrikdefinitionen für eine Ressource in der Azure Monitor-REST-API](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Abrufen von Metrikwerten
Sobald die verfügbaren Metrikdefinitionen bekannt sind, können die entsprechenden Metrikwerte abgerufen werden. Für sämtliche Filteranforderungen verwenden Sie den Metriknamen „value“ (nicht „localizedValue“; z.B. zum Abrufen der Metrikdatenpunkte „CpuTime“ und „Requests“). Werden keine Filter verwendet, wird die Standardmetrik zurückgegeben.

> [!NOTE]
> Zum Abrufen der Metrikwerte mithilfe der Azure Monitor-REST-API verwenden Sie die API-Version „2016-09-01“.
>
>

**Methode**: GET

**Anforderungs-URI**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&amp;api-version=*{apiVersion}*

Um zum Beispiel RunsSucceeded-Metrikdatenpunkte für einen bestimmten Zeitraum und ein Aggregationsintervall von einer Stunde abzurufen, sähe die Anforderung wie folgt aus:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Der resultierende JSON-Antworttext ähnelt folgendem Beispiel:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Um mehrere Daten- oder Aggregationspunkte abzurufen, fügen Sie dem Filter die Metrikdefinitionsnamen und Aggregationstypen hinzu, wie im folgenden Beispiel dargestellt:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Der resultierende JSON-Antworttext ähnelt folgendem Beispiel:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Verwenden von ARMClient
Eine weitere Möglichkeit ist das Verwenden von [ARMClient](https://github.com/projectkudu/armclient) auf Ihrem Windows-Computer. ARMClient führt die Azure AD-Authentifizierung automatisch durch (und verwendet das entsprechende JWT-Token). Die folgenden Schritte beschreiben die Verwendung von ARMClient zum Abrufen von Metrikdaten:

1. Installieren Sie [Chocolatey](https://chocolatey.org/) und [ARMClient](https://github.com/projectkudu/armclient).
2. Geben Sie in einem Terminalfenster *armclient.exe login*ein. Dabei werden Sie dazu aufgefordert, sich bei Azure anzumelden.
3. Geben Sie *armclient GET [Ihre_Ressourcen_ID]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01* ein.
4. Geben Sie *armclient GET [Ihre_Ressourcen_ID]/providers/microsoft.insights/metrics?api-version=2016-09-01* ein.

Führen Sie folgenden Befehl aus, um beispielsweise die Metrikdefinitionen für eine bestimmte Logik-App abzurufen:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Abrufen der Ressourcen-ID
Die Verwendung der REST-API kann Ihnen dabei helfen, die verfügbaren Metrikdefinitionen, die Granularität und die entsprechenden Werte zu verstehen. Diese Informationen sind für die Nutzung der [Azure-Verwaltungsbibliothek](https://msdn.microsoft.com/library/azure/mt417623.aspx)hilfreich.

Für den oben angegebenen Code muss als Ressourcen-ID der vollständige Pfad zur gewünschten Azure-Ressource angegeben werden. Zum Beispiel würde die Ressourcen-ID für die Abfrage einer Azure Web-App wie folgt lauten:

*/subscriptions/{abonnement-id}/resourceGroups/{ressourcengruppenname}/providers/Microsoft.Web/sites/{sitename}/*

Die folgende Liste enthält einige Beispiele für Ressourcen-ID-Formate für verschiedene Azure-Ressourcen:

* **IoT Hub** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Elastischer SQL-Pool** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL-Datenbank (v12)** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **VM-Skalierungsgruppen** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VMs** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** – /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Es gibt alternative Ansätze für das Abrufen von Ressourcen-ID – darunter die Verwendung von Azure-Ressourcen-Explorers – und für das Anzeigen der gewünschten Ressource im Azure-Portal, über PowerShell oder die Azure-CLI.

### <a name="azure-resource-explorer"></a>Azure-Ressourcen-Explorer
Ein hilfreicher Ansatz zum Finden der Ressourcen-ID für die gewünschte Ressource ist die Verwendung des [Azure-Ressourcen-Explorers](https://resources.azure.com) . Navigieren Sie zur gewünschten Ressource, und sehen Sie sich dort die angezeigte ID an, wie im folgenden Screenshot dargestellt:

![Alt "Azure-Ressourcen-Explorer"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Azure-Portal
Die Ressourcen-ID finden Sie auch im Azure-Portal. Navigieren Sie dazu zur gewünschten Ressource, und wählen Sie „Eigenschaften“. Die Ressourcen-ID wird wie im folgenden Screenshot dargestellt im Abschnitt „Eigenschaften“ angezeigt:

![Alt "Ressourcen-ID wird auf dem Blatt „Eigenschaften“ im Azure-Portal angezeigt"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
Die Ressourcen-ID kann auch mithilfe von Azure PowerShell-Cmdlets abgerufen werden. Um zum Beispiel die Ressourcen-ID für eine Azure-Logik-App abzurufen, führen Sie wie im folgenden Beispiel dargestellt das Cmdlet Get-AzureLogicApp aus:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Die Ergebnisse sollten in etwa wie das folgende Beispiel aussehen:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Führen Sie wie im folgenden Beispiel dargestellt den Befehl „az storage account show“ aus, um die Ressourcen-ID eines Azure Storage-Kontos mithilfe von Azure CLI abzurufen:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Die Ergebnisse sollten in etwa wie das folgende Beispiel aussehen:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Azure Logic Apps ist noch nicht über Azure CLI verfügbar, daher wird im vorherigen Beispiel ein Azure Storage-Konto verwendet.
>
>

## <a name="retrieve-activity-log-data"></a>Abrufen von Aktivitätsprotokolldaten
Außer Metrikdefinitionen und den entsprechenden Werten können auch zusätzliche interessante Informationen über Ihre Azure-Ressourcen mithilfe der Azure Monitor-REST-API abgerufen werden. So können beispielsweise [Aktivitätsprotokolldaten](https://msdn.microsoft.com/library/azure/dn931934.aspx) abgefragt werden. Das folgende Muster zeigt die Verwendung der Azure Monitor-REST-API, um Aktivitätsprotokolldaten innerhalb eines bestimmten Zeitraums für Ihr Azure-Abonnement abzufragen:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die [Übersicht über die Überwachung](monitoring-overview.md).
* Lesen Sie [Unterstützte Metriken von Azure Monitor](monitoring-supported-metrics.md).
* Lesen Sie die [REST-API-Referenz zu Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Schlagen Sie in der [Azure-Verwaltungsbibliothek](https://msdn.microsoft.com/library/azure/mt417623.aspx)nach.
