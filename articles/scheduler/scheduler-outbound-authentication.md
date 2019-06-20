---
title: Ausgehende Authentifizierung – Azure Scheduler
description: Hier erhalten Sie Informationen zum Einrichten oder Entfernen der ausgehenden Authentifizierung für Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 42d6ec93a3382f494b49fb574c4aee5e8eec142a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708946"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Ausgehende Authentifizierung für Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) ersetzt den Azure Scheduler, der in Kürze eingestellt wird. Zum Planen von Aufträgen sollten Sie stattdessen [Azure Logic Apps ausprobieren](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler-Aufträge müssen möglicherweise Dienste aufrufen, die eine Authentifizierung verlangen, z.B. andere Azure-Dienste, Salesforce.com, Facebook und sichere benutzerdefinierte Websites. Der aufgerufene Dienst kann festlegen, ob dem Scheduler-Auftrag Zugriff auf die angeforderten Ressourcen gewährt werden soll. 

Der Scheduler unterstützt folgende Authentifizierungsmodelle: 

* Die *Clientzertifikatauthentifizierung* bei Verwendung von SSL-/TLS-Clientzertifikaten
* Die *Standardauthentifizierung*
* Die *Active Directory OAuth-Authentifizierung*

## <a name="add-or-remove-authentication"></a>Hinzufügen oder Entfernen der Authentifizierung

* Um einem Scheduler-Auftrag eine Authentifizierung hinzuzufügen, fügen Sie dem `request`-Element beim Erstellen oder Aktualisieren des Auftrags das untergeordnete JSON-Element (JavaScript Object Notation) `authentication` hinzu. 

  In den Antworten werden niemals Geheimnisse zurückgegeben, die dem Scheduler-Dienst über eine PUT-, PATCH- oder POST-Anforderung im Objekt `authentication` übergeben werden. 
  In Antworten werden geheime Informationen auf NULL festgelegt, oder sie verwenden unter Umständen ein öffentliches Token, das die authentifizierte Entität darstellt. 

* Um die Authentifizierung von einem Scheduler-Auftrag zu entfernen, müssen Sie explizit eine PUT- oder PATCH-Anforderung für den Auftrag ausführen und das `authentication`-Objekt auf NULL festlegen. Die Antwort enthält keine Authentifizierungseigenschaften.

## <a name="client-certificate"></a>Clientzertifikat

### <a name="request-body---client-certificate"></a>Anforderungstext: Clientzertifikat

Wenn Sie die Authentifizierung anhand des `ClientCertificate`-Modells hinzufügen, geben Sie im Anforderungstext zusätzlich folgende Elemente an.  

| Element | Erforderlich | BESCHREIBUNG |
|---------|----------|-------------|
| **authentication** (übergeordnetes Element) | Das Authentifizierungsobjekt für die Verwendung eines SSL-Clientzertifikats |
| **type** | Ja | Der Authentifizierungstyp. Für SSL-Clientzertifikate lautet der Wert `ClientCertificate`. |
| **pfx** | Ja | Der Base64-codierte Inhalt der PFX-Datei |
| **password** | Ja | Der Parameter für den Zugriff auf die PFX-Datei |
||| 

### <a name="response-body---client-certificate"></a>Antworttext: Clientzertifikat 

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden Authentifizierungselemente.

| Element | BESCHREIBUNG | 
|---------|-------------| 
| **authentication** (übergeordnetes Element) | Das Authentifizierungsobjekt für die Verwendung eines SSL-Clientzertifikats |
| **type** | Der Authentifizierungstyp. Für SSL-Clientzertifikate lautet der Wert `ClientCertificate`. |
| **certificateThumbprint** |Der Fingerabdruck des Zertifikats |
| **certificateSubjectName** |Der Distinguished Name des Antragstellers für das Zertifikat |
| **certificateExpiration** | Das Ablaufdatum des Zertifikats |
||| 

### <a name="sample-rest-request---client-certificate"></a>REST-Beispielanforderung: Clientzertifikat

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>REST-Beispielantwort: Clientzertifikat

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Anforderungstext: Standard

Wenn Sie die Authentifizierung anhand des `Basic`-Modells hinzufügen, geben Sie im Anforderungstext zusätzlich folgende Elemente an.

| Element | Erforderlich | BESCHREIBUNG |
|---------|----------|-------------|
| **authentication** (übergeordnetes Element) | Das Authentifizierungsobjekt für die Verwendung der Standardauthentifizierung | 
| **type** | Ja | Der Authentifizierungstyp. Für die Standardauthentifizierung lautet der Wert `Basic`. | 
| **username** | Ja | Der zu authentifizierende Benutzername | 
| **password** | Ja | Das zu authentifizierende Kennwort |
|||| 

### <a name="response-body---basic"></a>Antworttext: Standard

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden Authentifizierungselemente.

| Element | BESCHREIBUNG | 
|---------|-------------|
| **authentication** (übergeordnetes Element) | Das Authentifizierungsobjekt für die Verwendung der Standardauthentifizierung |
| **type** | Der Authentifizierungstyp. Für die Standardauthentifizierung lautet der Wert `Basic`. |
| **username** | Der authentifizierte Benutzername |
||| 

### <a name="sample-rest-request---basic"></a>REST-Beispielanforderung: Standard

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>REST-Beispielantwort: Standard

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Anforderungstext: Active Directory OAuth 

Wenn Sie die Authentifizierung anhand des `ActiveDirectoryOAuth`-Modells hinzufügen, geben Sie im Anforderungstext zusätzlich folgende Elemente an.

| Element | Erforderlich | BESCHREIBUNG |
|---------|----------|-------------|
| **authentication** (übergeordnetes Element) | Ja | Das Authentifizierungsobjekt für die Verwendung der ActiveDirectoryOAuth-Authentifizierung |
| **type** | Ja | Der Authentifizierungstyp. Für die ActiveDirectoryOAuth-Authentifizierung lautet der Wert `ActiveDirectoryOAuth`. |
| **tenant** | Ja | Die Mandanten-ID für den Azure AD-Mandanten. Durch Ausführen von `Get-AzureAccount` in Azure PowerShell können Sie die Mandanten-ID für den Azure AD-Mandanten ermitteln. |
| **audience** | Ja | Dieser Wert ist auf `https://management.core.windows.net/` festgelegt. | 
| **clientId** | Ja | Die Client-ID für die Azure AD-Anwendung | 
| **secret** | Ja | Das Geheimnis für den Client, der das Token anfordert | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Antworttext: Active Directory OAuth

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden Authentifizierungselemente.

| Element | BESCHREIBUNG |
|---------|-------------|
| **authentication** (übergeordnetes Element) | Das Authentifizierungsobjekt für die Verwendung der ActiveDirectoryOAuth-Authentifizierung |
| **type** | Der Authentifizierungstyp. Für die ActiveDirectoryOAuth-Authentifizierung lautet der Wert `ActiveDirectoryOAuth`. | 
| **tenant** | Die Mandanten-ID für den Azure AD-Mandanten |
| **audience** | Dieser Wert ist auf `https://management.core.windows.net/` festgelegt. |
| **clientId** | Die Client-ID für die Azure AD-Anwendung |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>REST-Beispielanforderung: Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>REST-Beispielantwort: Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="see-also"></a>Weitere Informationen

* [Was ist der Azure Scheduler?](scheduler-intro.md)
* [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)
* [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)
* [Azure Scheduler-REST-API](https://msdn.microsoft.com/library/mt629143)
* [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)
