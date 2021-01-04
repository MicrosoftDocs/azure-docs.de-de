---
title: Ausnahmebehandlungs- und Fehlerprotokollierungsszenario
description: Anwendungsfall und Szenario aus der Praxis zur erweiterten Ausnahmebehandlung und Fehlerprotokollierung in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: fdf5f25ae6f89ccc06c95ee1be021691dab0047a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000350"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Szenario: Ausnahmebehandlung und Fehlerprotokollierung für Logik-Apps

In diesem Szenario erfahren Sie, wie Sie eine Logik-App erweitern, um die Unterstützung der Ausnahmebehandlung zu verbessern. Hier wird ein Anwendungsfall aus der Praxis verwendet, um die Frage zu beantworten, ob Azure Logic Apps die Ausnahme- und Fehlerbehandlung unterstützt.

> [!NOTE]
> Das aktuelle Azure Logic Apps-Schema enthält eine Standardvorlage für Antworten auf Aktionen. Diese Vorlage umfasst sowohl Antworten der internen Überprüfung als auch Fehlerantworten, die von einer API-App zurückgegeben werden.

## <a name="scenario-and-use-case-overview"></a>Übersicht über Szenario und Anwendungsfall

Der Anwendungsfall für dieses Szenario basiert auf folgender Situation: 

Wir wurden von einem bekannten Unternehmen aus dem Gesundheitswesen damit beauftragt, eine Azure-Lösung für ein Patientenportal mit Microsoft Dynamics CRM Online zu entwickeln. Das Unternehmen benötigte eine Lösung, mit der sich Termindatensätze zwischen dem Dynamics CRM Online-Patientenportal und Salesforce austauschen lassen. Für alle Patientendatensätze sollte die Norm [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) verwendet werden.

Das Projekt musste zwei wesentliche Anforderungen erfüllen:  

* Bereitstellung einer Methode zum Protokollieren von Datensätzen, die aus dem Dynamics CRM Online-Portal gesendet werden
* Schaffung einer Möglichkeit zum Anzeigen von Fehlern, die innerhalb des Workflows auftreten

> [!TIP]
> Ein allgemeines Video zu diesem Projekt finden Sie unter [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integration User Group") (Integrationsbenutzergruppe).

## <a name="how-we-solved-the-problem"></a>Lösung des Problems

Als Repository für die Protokoll- und Fehlerdatensätze haben wir [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") gewählt. (In Cosmos DB werden Datensätze als Dokumente bezeichnet.) Da Azure Logic Apps über eine Standardvorlage für alle Antworten verfügt, mussten wir kein benutzerdefiniertes Schema erstellen. Wir konnten sowohl für Fehler- als auch für Protokolldatensätze eine API-App zum **Einfügen** und **Abfragen** erstellen. Außerdem konnten wir jeweils ein Schema in der API-App definieren.  

Eine weitere Anforderung war die endgültige Löschung von Datensätzen nach einem bestimmten Datum. Cosmos DB verfügt über eine Eigenschaft namens [Gültigkeitsdauer](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Gültigkeitsdauer") (Time-To-Live, TTL), mit der wir einen Wert vom Typ **Gültigkeitsdauer** für die einzelnen Datensätze oder Sammlungen festlegen konnten. Dadurch müssen Datensätze in Cosmos DB nicht mehr manuell gelöscht werden.

> [!IMPORTANT]
> Zur Durchführung dieses Tutorials müssen Sie eine Cosmos DB-Datenbank und zwei Sammlungen (Protokollierung und Fehler) erstellen.

## <a name="create-the-logic-app"></a>Erstellen der Logik-App

Im ersten Schritt wird die Logik-App erstellt und im Logik-App-Designer geöffnet. In diesem Beispiel verwenden wir übergeordnete und untergeordnete Logik-Apps. Gehen wir davon aus, dass die übergeordnete Logik-App bereits erstellt wurde und wir nun eine untergeordnete Logik-App erstellen.

Da wir den aus Dynamics CRM Online stammenden Datensatz protokollieren möchten, beginnen wir oben. Wir müssen einen **Anforderungstrigger** verwenden, da die übergeordnete Logik-App dieses untergeordnete Element auslöst.

### <a name="logic-app-trigger"></a>Logik-App-Trigger

Wir verwenden einen **Anforderungstrigger**, wie im folgenden Beispiel gezeigt:

``` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

```


## <a name="steps"></a>Schritte

Wir müssen die Quelle (Anforderung) des Patientendatensatzes aus dem Dynamics CRM Online-Portal protokollieren.

1. Wir müssen aus Dynamics CRM Online einen neuen Termindatensatz abrufen.

   Der Trigger aus CRM liefert uns die folgenden Informationen: **CRM-Patienten-ID** (PatientID), **Datensatztyp**, **Neuer oder aktualisierter Datensatz** (boolescher Wert) und **SalesforceId**. **SalesforceId** kann NULL sein, da diese ID nur für Updates verwendet wird.
   Zum Abrufen des CRM-Datensatzes verwenden wir die CRM-Patienten-ID (**PatientID**) und den **Datensatztyp**.

2. Im nächsten Schritt müssen wir den Vorgang **InsertLogEntry** der Azure Cosmos DB-SQL-API-App wie hier veranschaulicht im Logik-App-Designer hinzufügen.

   **Protokolleintrag einfügen**

   ![Screenshot: Logik-App-Designer mit den Konfigurationseinstellungen für InsertLogEntry](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Fehlereintrag einfügen**

   ![Screenshot: Logik-App-Designer mit den Konfigurationseinstellungen für CreateErrorRecord](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Auf Fehler bei der Datensatzerstellung überprüfen**

   ![Screenshot: Bildschirm „CreateErrorRecord“ im Logik-App-Designer mit den Feldern zum Erstellen eines Fehlereintrags](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Logik-App-Quellcode

> [!NOTE]
> Bei den folgenden Angaben handelt es sich nur um Beispiele. Da dieses Tutorial auf einer Implementierung beruht, die jetzt in der Praxis verwendet wird, werden für den Wert eines **Quellknotens** unter Umständen keine Eigenschaften angezeigt, die mit der Terminplanung in Verbindung stehen.> 

### <a name="logging"></a>Protokollierung

Das folgende Logik-App-Codebeispiel veranschaulicht die Behandlung der Protokollierung.

#### <a name="log-entry"></a>Protokolleintrag

Hier sehen Sie den Logik-App-Quellcode zum Einfügen eines Protokolleintrags:

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Protokollanforderung

Dies ist die Protokollanforderungsnachricht für die API-App:

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Protokollantwort

Hier ist die Protokollantwortnachricht der API-App dargestellt:

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Als Nächstes sehen wir uns die Schritte für die Fehlerbehandlung an.

### <a name="error-handling"></a>Fehlerbehandlung

Das folgende Logik-App-Codebeispiel veranschaulicht die Implementierung der Fehlerbehandlung.

#### <a name="create-error-record"></a>Erstellen des Fehlerdatensatzes

Hier sehen Sie den Logik-App-Quellcode zum Erstellen eines Fehlerdatensatzes:

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Einfügen eines Fehlers in Cosmos DB: Anforderung

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Einfügen eines Fehlers in Cosmos DB: Antwort

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce-Fehlerantwort

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Zurückgeben der Antwort an die übergeordnete Logik-App

Nachdem Sie die Antwort erhalten haben, können Sie sie an die übergeordnete Logik-App zurückgeben.

#### <a name="return-success-response-to-parent-logic-app"></a>Zurückgeben der Antwort „Erfolg“ an die übergeordnete Logik-App

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Zurückgeben der Antwort „Fehler“ an die übergeordnete Logik-App

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB-Repository und -Portal

Mit unserer Lösung haben wir den Funktionsumfang von [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) erweitert.

### <a name="error-management-portal"></a>Fehlerverwaltungsportal

Sie können eine MVC-Web-App erstellen, mit der die Fehlerdatensätze aus Cosmos DB angezeigt werden können. In der aktuellen Version sind die Vorgänge **Liste**, **Details**, **Bearbeiten** und **Löschen** enthalten.

> [!NOTE]
> Beim Bearbeitungsvorgang ersetzt Cosmos DB das gesamte Dokument. Die Datensätze, die in der **Listen** und in der **Detailansicht** angezeigt werden, sind lediglich Beispiele. Es handelt sich nicht um echte Datensätze mit Patiententerminen.

Im Anschluss finden Sie Beispiele für unsere MVC-App-Details, die auf der Grundlage des zuvor beschriebenen Konzepts erstellt wurden.

#### <a name="error-management-list"></a>Fehlerverwaltung – Liste
![Fehlerliste](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Fehlerverwaltung – Detailansicht
![Fehlerdetails](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Protokollverwaltungsportal

Zum Anzeigen der Protokolle haben wir ebenfalls eine MVC-Web-App erstellt. Im Anschluss finden Sie Beispiele für unsere MVC-App-Details, die auf der Grundlage des zuvor beschriebenen Konzepts erstellt wurden.

#### <a name="sample-log-detail-view"></a>Beispielprotokoll – Detailansicht
![Protokoll – Detailansicht](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API-App-Details

#### <a name="logic-apps-exception-management-api"></a>Ausnahmeverwaltungs-API für Logik-Apps

Unsere Ausnahmeverwaltungs-API-App für Azure Logic Apps (Open Source) bietet die hier beschriebenen Funktionen. Es gibt zwei Controller:

* **ErrorController** fügt einen Fehlerdatensatz (ein Dokument) in eine Azure Cosmos DB-Sammlung ein.
* **LogController** fügt einen Protokolldatensatz (ein Dokument) in eine Azure Cosmos DB-Sammlung ein.

> [!TIP]
> Beide Controller nutzen `async Task<dynamic>`-Vorgänge, damit Vorgänge zur Laufzeit aufgelöst werden und wir das Azure Cosmos DB-Schema im Text des Vorgangs erstellen können. 
> 

Jedes Dokument in Azure Cosmos DB muss eine eindeutige ID aufweisen. Wir verwenden `PatientId` und fügen einen Zeitstempel hinzu, der in einen Unix-Zeitstempelwert (double) konvertiert wird. Wir schneiden den Wert ab, um den Bruchteil zu entfernen.

Den Quellcode unserer Fehlercontroller-API können Sie sich bei [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs) ansehen.

Wir rufen die API mit der folgenden Syntax aus einer Logik-App auf:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Mit dem Ausdruck im obigen Codebeispiel wird geprüft, ob *Create_NewPatientRecord* den Status **Failed** aufweist.

## <a name="summary"></a>Zusammenfassung

* Sie können die Protokollierung und Fehlerbehandlung in einer Logik-App leicht implementieren.
* Sie können Azure Cosmos DB als Repository für Protokoll- und Fehlerdatensätze (Dokumente) verwenden.
* Mit MVC können Sie ein Portal zum Anzeigen von Protokoll- und Fehlerdatensätzen erstellen.

### <a name="source-code"></a>Quellcode

Den Quellcode für die API-Anwendung zur Logik-App-Ausnahmeverwaltung finden Sie in [diesem GitHub-Repository](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Ausnahmeverwaltungs-API der Logik-App").

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen weiterer Logik-App-Beispiele und -Szenarien](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Überwachen von Logik-Apps](../logic-apps/monitor-logic-apps.md)
* [Automatisieren der Logik-App-Bereitstellung](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
