---
title: 'Schemaaktualisierungen für die Vorschauversion vom 1. August 2015: Azure Logic Apps | Microsoft-Dokumentation'
description: Aktualisierte Schemaversion 2015-08-01-preview für Logik-App-Definitionen in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: 92f522c72f69218e55b1ee4cfff74511a30288b0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60553758"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Schemaaktualisierungen für Azure Logic Apps – 1. August 2015 (Vorschau)

Diese Schema- und API-Version für Azure Logic Apps enthält wichtige Verbesserungen, mit denen Logik-Apps zuverlässiger und einfacher zu verwenden sind:

* Der Aktionstyp **APIApp** heißt jetzt [**APIConnection**](#api-connections).
* Die **Repeat**-Aktion heißt jetzt [**Foreach**](#foreach).
* Die [**HTTP-Listener**-API-App](#http-listener) ist nicht mehr erforderlich.
* Für den Aufruf untergeordneter Workflows wird ein [neues Schema](#child-workflows) verwendet.

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>Wechsel zu API-Verbindungen

Die größte Änderung besteht darin, dass Sie zur Verwendung von APIs keine API-Apps in Ihrem Azure-Abonnement bereitstellen müssen. Hier sind die Verwendungsmöglichkeiten für APIs:

* Verwaltete APIs
* Ihre benutzerdefinierten Web-APIs

Jede dieser Möglichkeiten wird etwas anders gehandhabt, da sich ihre Verwaltungs- und Hostingmodelle unterscheiden. Ein Vorteil dieses Modells besteht darin, dass Sie nicht mehr auf in Ihrer Azure-Ressourcengruppe bereitgestellte Ressourcen beschränkt sind. 

### <a name="managed-apis"></a>Verwaltete APIs

Microsoft verwaltet einige APIs in Ihrem Namen, z.B. Office 365, Salesforce, Twitter und FTP. Sie können einige dieser verwalteten APIs ohne weitere Bearbeitung verwenden, etwa Bing Translate, wohingegen andere APIs konfiguriert werden müssen, auch als *Verbindung* bezeichnet.

Wenn Sie Office 365 verwenden, müssen Sie beispielsweise eine Verbindung erstellen, die Ihr Office 365-Anmeldetoken einbezieht. Ihr Token wird sicher gespeichert und aktualisiert, damit Ihre Logik-App jederzeit die Office 365-API aufrufen kann. Wenn Sie eine Verbindung mit Ihrer SQL Server-Instanz oder dem FTP-Server herstellen möchten, müssen Sie eine Verbindung erstellen, die die Verbindungszeichenfolge enthält. 

Innerhalb dieser Definition werden diese Aktionen als `APIConnection` bezeichnet. Hier sehen Sie ein Beispiel für eine Verbindung, die Office 365 zum Senden einer E-Mail aufruft:

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

Das `host`-Objekt stellt den Teil der Eingaben dar, der nur für API-Verbindungen gilt. Es besteht aus diesen Teilen: `api` und `connection`. Das `api`-Objekt enthält die Laufzeit-URL, für die diese verwaltete API gehostet wird. Sie können alle verfügbaren verwalteten APIs anzeigen, indem Sie diese Methode aufrufen:

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

Wenn Sie eine API verwenden, müssen für die API nicht unbedingt *Verbindungsparameter* definiert sein. Wenn die API diese Parameter nicht definiert, ist also keine Verbindung erforderlich. Wenn die API diese Parameter definiert, müssen Sie eine Verbindung mit dem angegebenen Namen herstellen.  
Sie verweisen dann im `connection`-Objekt innerhalb des `host`-Objekts auf den Namen. Rufen Sie zum Erstellen einer Verbindung in einer Ressourcengruppe folgende Methode auf:

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

Verwenden Sie folgenden Text:

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Bereitstellen von verwalteten APIs in einer Azure Resource Manager-Vorlage

Solange keine interaktive Anmeldung erforderlich ist, können Sie eine vollständige App in einer Resource Manager-Vorlage erstellen.
Wenn eine Anmeldung erforderlich ist, können Sie dennoch eine Resource Manager-Vorlage verwenden, müssen dann aber die Verbindungen über das Azure-Portal autorisieren. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

Sie sehen in diesem Beispiel, dass es sich bei den Verbindungen lediglich um Ressourcen in der Ressourcengruppe handelt. Sie verweisen auf die verwalteten APIs, die Ihnen in Ihrem Abonnement zur Verfügung stehen.

### <a name="your-custom-web-apis"></a>Ihre benutzerdefinierten Web-APIs

Wenn Sie eigene anstatt von Microsoft verwaltete APIs einsetzen, verwenden Sie für das Aufrufen der APIs die integrierte **HTTP**-Aktion. Im Idealfall geben Sie einen Swagger-Endpunkt für Ihre API an. Dieser Endpunkt dient dem Logik-App-Designer zur Anzeige der Eingaben und Ausgaben Ihrer APIs. Ohne Swagger-Endpunkt kann der Designer die Ein- und Ausgaben nur als nicht transparente JSON-Objekte anzeigen.

Hier sehen Sie ein Beispiel für die neue `metadata.apiDefinitionUrl` -Eigenschaft:

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Wenn Sie Ihre Web-API in Azure App Service hosten, wird die Web-API automatisch in der Liste der im Designer verfügbaren Aktionen angezeigt. Andernfalls müssen Sie die URL direkt einfügen. Der Swagger-Endpunkt darf nicht authentifiziert sein, damit er im Logik-App-Designer verwendet werden kann, aber Sie können die API selbst mit den von Swagger unterstützten Methoden sichern.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>Aufrufen bereitgestellter API-Apps mit „2015-08-01-preview“

Wenn Sie zuvor eine API-App bereitgestellt haben, können Sie sie über die **HTTP**-Aktion aufrufen.
Beispiel: Wenn Sie Dropbox zum Auflisten von Dateien verwenden, ist in der Schemaversionsdefinition **2014-12-01-preview** etwa Folgendes enthalten:

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

Sie können nun eine ähnliche HTTP-Aktion erstellen und den Abschnitt `parameters` der Logik-App-Definition unverändert lassen, wie zum Beispiel:

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

Informationen zu den einzelnen Eigenschaften:

| Aktionseigenschaft | BESCHREIBUNG |
| --- | --- |
| `type` | `Http` anstelle von `APIapp` |
| `metadata.apiDefinitionUrl` | Wenn Sie diese Aktion im Logik-App-Designer verwenden möchten, sollten Sie den Metadatenendpunkt aufnehmen. Er wird erstellt aus: `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | Erstellt aus: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | Immer `POST` |
| `inputs.body` | Identisch mit den API-App-Parametern |
| `inputs.authentication` | Identisch mit der API-App-Authentifizierung |

Dieser Ansatz sollte bei allen API-App-Aktionen funktionieren. Beachten Sie jedoch, dass diese früheren API-Apps nicht mehr unterstützt werden. Daher sollten Sie auf eine der beiden anderen vorherigen Optionen umsteigen, eine verwaltete API oder Hosten Ihrer benutzerdefinierten Web-API.

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>„Repeat“ umbenannt in „Foreach“

Für die vorherige Schemaversion haben wir umfangreiches Kundenfeedback mit dem Hinweis erhalten, dass bei dem Aktionsnamen **Repeat** nicht ganz klar sei, dass es sich bei **Repeat** tatsächlich um eine For-Each-Schleife handelt. Also haben wir `repeat` in `foreach` umbenannt. Zuvor hätten Sie diese Aktion wie in diesem Beispiel geschrieben:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

Stattdessen würden Sie diese Version nun so schreiben:

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

Außerdem wurde die `repeatItem()`-Funktion, die auf das Element verwiesen hat, das die Schleife während der aktuellen Iteration verarbeitet, nun in `item()` umbenannt. 

### <a name="reference-outputs-from-foreach"></a>Referenzausgaben von „foreach“

Zur Vereinfachung sind Ausgaben von `foreach`-Aktionen nicht mehr in ein Objekt namens `repeatItems` eingeschlossen. Auch werden infolge dieser Änderungen die Funktionen `repeatItem()`, `repeatBody()` und `repeatOutputs()` entfernt.

Deshalb erhalten Sie mithilfe des vorherigen `repeat`-Beispiels diese Ausgaben:

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

Jetzt erhalten Sie stattdessen diese Ausgaben:

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

Bisher mussten Sie zum Aufrufen des `body` der Aktion beim Verweisen auf diese Ausgaben Folgendes verwenden:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

Stattdessen können Sie jetzt diese Version verwenden:

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>Nativer HTTP-Listener

Die HTTP-Listener-Features sind jetzt integriert, daher müssen Sie keine HTTP-Listener-API-App bereitstellen. Erfahren Sie mehr darüber, wie Sie [den Logik-App-Endpunkt aufrufbar machen](../logic-apps/logic-apps-http-endpoint.md). 

Mit diesen Änderungen ersetzt Logic Apps die Funktion `@accessKeys()` durch die Funktion `@listCallbackURL()`, die bei Bedarf den Endpunkt abruft. Außerdem müssen Sie jetzt mindestens einen Trigger in Ihrer Logik-App definieren. Wenn Sie für den Workflow `/run` ausführen möchten, müssen Sie einen der folgenden Trigger verwenden: `Manual`, `ApiConnectionWebhook` oder `HttpWebhook`.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>Aufrufen von untergeordneten Workflows

Zuvor mussten Sie zum Aufrufen von untergeordneten Workflows den gewünschten Workflow aufrufen, das Zugriffstoken abrufen und dieses Token in die Definition der Logik-App einfügen, die den untergeordneten Workflow aufrufen sollte. Mit diesem Schema generiert die Logic Apps-Engine zur Laufzeit automatisch eine SAS für den untergeordneten Workflow, sodass Sie keine geheimen Schlüssel in die Definition einfügen müssen. Beispiel:

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

Zudem erhalten untergeordnete Workflows vollen Zugriff auf die eingehende Anforderung. Folglich können Sie Parameter im Abschnitt `queries` und im Objekt `headers` übergeben. Sie können auch den gesamten Abschnitt `body` vollständig definieren.

Schließlich verfügen untergeordnete Workflows über diese erforderlichen Änderungen. Zuvor konnten Sie einen untergeordneten Workflow direkt aufrufen. Nun müssen Sie einen Triggerendpunkt im Workflow festlegen, den der übergeordnete Workflow aufruft. Im Allgemeinen fügen Sie einen Trigger vom Typ `Manual` hinzu und verwenden ihn dann in der übergeordneten Definition. Die `host`-Eigenschaft enthält ein spezielles `triggerName`-Element, da Sie immer den aufzurufenden Trigger angeben müssen.

## <a name="other-changes"></a>Weitere Änderungen

### <a name="new-queries-property"></a>Neue „queries“-Eigenschaft

Alle Aktionstypen unterstützen jetzt eine neue Eingabe namens `queries`. Diese Eingabe kann ein strukturiertes Objekt sein, sodass Sie die Zeichenfolge nicht manuell zusammensetzen müssen.

### <a name="renamed-parse-function-to-json"></a>Funktion „parse()“ umbenannt in „json()“

Für künftige Inhaltstypen wird die Funktion `parse()` in `json()` umbenannt.

## <a name="enterprise-integration-apis"></a>APIs für die Unternehmensintegration

Verwaltete Versionen für APIs für die Unternehmensintegration, wie AS2, werden von diesem Schema noch nicht unterstützt. Sie können jedoch die vorhandenen bereitgestellten BizTalk-APIs über die HTTP-Aktion verwenden. Weitere Informationen finden Sie in der [Integrationsroadmap](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/) unter „Verwenden der bereits bereitgestellten API-Apps“. 
