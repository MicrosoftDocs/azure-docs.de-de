---
title: Verwenden von REST-APIs zum Implementieren von CI/CD für Azure Stream Analytics in IoT Edge
description: Erfahren Sie, wie Sie mithilfe von REST-APIs eine Pipeline für Continuous Integration und Deployment für Azure Stream Analytics implementieren.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: a7e56758a1a76933d6bb18883aa15ce33ce2e89e
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130917"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>Implementieren von CI/CD für Stream Analytics in IoT Edge mithilfe von APIs

Sie können Continuous Integration und Deployment für Azure Stream Analytics-Aufträge mithilfe von REST-APIs aktivieren. Dieser Artikel bietet Beispiele zu den zu verwendenden APIs und ihrer Verwendung. REST-APIs werden in Azure Cloud Shell nicht unterstützt.

## <a name="call-apis-from-different-environments"></a>Rufen Sie APIs aus anderen Umgebungen auf

REST-APIs können sowohl aus Linux als auch aus Windows aufgerufen werden. Die folgenden Befehle veranschaulichen die korrekte Syntax für den API-Aufruf. Die spezifische API-Verwendung wird in späteren Abschnitten dieses Artikels angesprochen.

### <a name="linux"></a>Linux

Unter Linux können Sie die Befehle `Curl` oder `Wget` verwenden:

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" { <url> }   
```

```bash
wget -q -O- --{ <method> } -data="<request body>" --header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Verwenden Sie unter Windows Powershell: 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url> -Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Erstellen eines ASA-Auftrags auf Edge 
 
Um einen Stream Analytics-Auftrag zu erstellen, rufen Sie die PUT-Methode mithilfe der Stream Analytics-API auf.

|Methode|Anfrage-URL|
|------|-----------|
|PUT|`https://management.azure.com/subscriptions/{\**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview`|
 
Befehlsbeispiel unter Verwendung von **curl** :

```curl
curl -u { <username:password> } -H "Content-Type: application/json" -X { <method> } -d "{ <request body> }" https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
Beispiel für den Anforderungskörper in JSON:

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
Weitere Informationen finden Sie in der [API-Dokumentation](/rest/api/streamanalytics/).  
 
## <a name="publish-edge-package"></a>Veröffentlichen des Edgepakets 
 
Um einen Stream Analytics-Auftrag auf IoT Edge zu veröffentlichen, rufen Sie die POST-Methode mithilfe der Veröffentlichungs-API für Edgepakete auf.

|Methode|Anfrage-URL|
|------|-----------|
|POST|`https://management.azure.com/subscriptions/{\**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview`|

Dieser asynchrone Vorgang gibt so lange den Status 202 zurück, bis der Auftrag erfolgreich veröffentlicht wurde. Der Antwortheader des Speicherorts enthält den URI, der zum Abrufen des Status des Prozesses verwendet wird. Während der Prozess ausgeführt wird, gibt ein Aufruf des URIs im Speicherortheader den Status 202 zurück. Während der Prozess abgeschlossen ist, gibt der URI im Speicherortheader den Status 200 zurück. 

Beispiel für einen Veröffentlichungsaufruf für ein Edgepaket mithilfe von **curl** : 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
Nach dem POST-Aufruf sollten Sie eine Antwort mit einem leeren Körper erwarten. Suchen Sie nach der URL, die sich im Kopfteil der Antwort befindet, und halten Sie sie zur späteren Verwendung fest.
 
Beispiel für die URL aus dem Kopfteil der Antwort:

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
Führen Sie nach einem Wait von einer oder zwei Minuten den folgenden Befehl aus, um einen API-Aufruf mit der URL vorzunehmen, die Sie dem Kopfteil der Antwort entnommen haben. Wiederholen Sie den Befehl, wenn Sie keine 200-Antwort erhalten.
 
Beispiel für den API-Aufruf mit der zurückgegebenen URL mit **curl** :

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

Die Antwort enthält die Informationen, die Sie dem Edge-Bereitstellungsskript hinzufügen müssen. Die Beispiele unten zeigen, welche Informationen Sie sammeln und an welchen Stellen Sie sie im Bereitstellungsmanifest hinzufügen müssen.
 
Beispiel-Antwortkörper nach erfolgreicher Veröffentlichung:

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["\],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}","PublishTimeStamp":"{publishtimestamp}"}}}}" 
  status : "Succeeded" 
} 
```

Beispiel für ein Bereitstellungsmanifest: 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

Lesen Sie nach der Konfiguration des Bereitstellungsmanifests [Bereitstellen von Azure IoT Edge-Modulen mit der Azure CLI](../iot-edge/how-to-deploy-modules-cli.md), um sich über die Bereitstellung zu informieren.


## <a name="next-steps"></a>Nächste Schritte 
 
* [Azure Stream Analytics auf IoT Edge](stream-analytics-edge.md)
* [Tutorial zu ASA unter IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md)
* [Entwickeln von Stream Analytics-Edge-Aufträgen mit Visual Studio-Tools](stream-analytics-tools-for-visual-studio-edge-jobs.md)