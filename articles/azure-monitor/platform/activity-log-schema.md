---
title: Ereignisschema des Azure-Aktivitätsprotokolls
description: Beschreibt das Ereignisschema für jede Kategorie im Azure-Aktivitätsprotokoll.
author: bwren
services: azure-monitor
ms.topic: reference
ms.date: 09/30/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 9bda92667cfc3afb44a55adf3f3c12798a734ddc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95522718"
---
# <a name="azure-activity-log-event-schema"></a>Ereignisschema des Azure-Aktivitätsprotokolls
Das [Azure-Aktivitätsprotokoll](platform-logs-overview.md) gewährt Einblick in alle Ereignisse auf Abonnementebene, die in Azure aufgetreten sind. Dieser Artikel beschreibt die Kategorien des Aktivitätsprotokolls und das jeweils zugehörige Schema. 

Das Schema variiert je nachdem, wie Sie auf das Protokoll zugreifen:
 
- Die in diesem Artikel beschriebenen Schemas gelten für den Zugriff auf das Aktivitätsprotokoll über die [REST-API](/rest/api/monitor/activitylogs). Diese Schemas werden auch verwendet, wenn Sie beim Anzeigen eines Ereignisses im Azure-Portal die Option **JSON** auswählen.
- Im letzten Abschnitt, [Schema aus Speicherkonto und Event Hubs](#schema-from-storage-account-and-event-hubs) finden Sie Informationen zum Schema für eine [Diagnoseeinstellung](diagnostic-settings.md) zum Senden des Aktivitätsprotokolls an Azure Storage oder Azure Event Hubs.
- Unter [Azure Monitor-Datenreferenz](/azure/azure-monitor/reference/) finden Sie das Schema, das zur Anwendung kommt, wenn Sie das Aktivitätsprotokoll über eine [Diagnoseeinstellung](diagnostic-settings.md) an einen Log Analytics-Arbeitsbereich senden.

## <a name="severity-level"></a>Schweregrad
Jeder Eintrag im Aktivitätsprotokoll weist einen Schweregrad auf. Der Schweregrad kann einen der folgenden Werte haben:  

| Schweregrad | Beschreibung |
|:---|:---|
| Kritisch | Ereignisse, die die sofortige Aufmerksamkeit eines Systemadministrators erfordern. Kann darauf hinweisen, dass eine Anwendung oder ein System einen Fehler aufweist oder nicht mehr reagiert.
| Fehler | Ereignisse, die auf ein Problem hinweisen, aber keine sofortige Aufmerksamkeit erfordern.
| Warnung | Ereignisse, die eine Vorwarnung für potenzielle Probleme sind, obwohl es sich nicht um einen tatsächlichen Fehler handelt. Weist darauf hin, dass sich eine Ressource nicht in einem idealen Zustand befindet und sich dadurch später Fehler oder kritische Ereignisse ergeben könnten.  
| Informational | Ereignisse, die nicht kritische Informationen an den Administrator weitergeben. Ähnelt einer Anmerkung vom Typ „Zu Ihrer Information“. 

Die Entwickler der einzelnen Ressourcenanbieter wählen den Schweregrad ihrer Ressourceneinträge aus. Je nachdem, wie Ihre Anwendung aufgebaut ist, kann der tatsächliche Schweregrad für Sie variieren. Beispielsweise können Elemente, die für eine bestimmte Ressource isoliert „kritisch“ sind, nicht so wichtig sein wie „Fehler“ bei einem Ressourcentyp, der für Ihre Azure-Anwendung von zentraler Bedeutung ist. Berücksichtigen Sie dies bei der Entscheidung, für welche Ereignisse eine Warnung ausgegeben werden soll.  

## <a name="categories"></a>Kategorien
Jedes Ereignis im Aktivitätsprotokoll verfügt über eine bestimmte Kategorie. Die Kategorien sind in der folgenden Tabelle beschrieben. In den folgenden Abschnitten finden Sie detaillierte Informationen zu jeder Kategorie und dem zugehörigen Schema, wenn Sie über PowerShell, die CLI, das Portal oder die REST-API auf das Aktivitätsprotokoll zugreifen. Das Schema ist unterschiedlich, wenn Sie das [Aktivitätsprotokoll in den Speicher oder an Event Hubs streamen](./resource-logs.md#send-to-azure-event-hubs). Eine Zuordnung der Eigenschaften zum [Ressourcenprotokollschema](./resource-logs-schema.md) befindet sich im letzten Abschnitt dieses Artikels.

| Category | BESCHREIBUNG |
|:---|:---|
| [Verwaltung](#administrative-category) | Enthält die Datensätze aller Erstellungs-, Aktualisierungs-, Lösch- und Aktionsvorgänge, die über Resource Manager ausgeführt wurden. Beispiele für Verwaltungsereignisse sind das _Erstellen des virtuellen Computers_ und das _Löschen der Netzwerksicherheitsgruppe_.<br><br>Jede Aktion, die von einem Benutzer oder einer Anwendung mit Resource Manager durchgeführt wird, wird als Vorgang basierend auf einem bestimmten Ressourcentyp modelliert. Wenn der Vorgangstyp _Schreiben_, _Löschen_ oder _Aktion_ lautet, werden die Datensätze zum Start und zum Erfolg oder Fehler dieses Vorgangs in der Kategorie „Verwaltung“ aufgezeichnet. Verwaltungsereignisse umfassen außerdem alle Änderungen an der rollenbasierten Zugriffssteuerung in Azure in einem Abonnement. |
| [Dienstintegrität](#service-health-category) | Enthält Datensätze zu allen Incidents im Zusammenhang mit der Dienstintegrität, die in Azure aufgetreten sind. Beispiel für ein Service Health-Ereignis: _Ausfallzeiten bei SQL Azure in der Region „USA, Osten“_ . <br><br>Es gibt sechs Typen von Service Health-Ereignissen: _Aktion erforderlich_, _Unterstützte Wiederherstellung_, _Incident_, _Wartung_, _Informationen_ und _Sicherheit_. Diese Ereignisse werden nur erstellt, wenn Sie über eine Ressource im Abonnement verfügen, die vom Ereignis betroffen wäre.
| [Resource Health](#resource-health-category) | Enthält Datensätze zu allen Ereignissen im Zusammenhang mit der Ressourcenintegrität, die für Ihre Azure-Ressourcen aufgetreten sind. Ein Beispiel für ein Resource Health-Ereignis ist _Integritätsstatus des virtuellen Computers ist zu „Nicht verfügbar“ gewechselt_.<br><br>Resource Health-Ereignisse können über einen von vier Integritätsstatus verfügen: _Verfügbar_, _Nicht verfügbar_, _Heruntergestuft_ und _Unbekannt_. Darüber hinaus können Resource Health-Ereignisse kategorisiert werden. Hierbei sind die Kategorien _Von der Plattform initiiert_ und _Vom Benutzer initiiert_ verfügbar. |
| [Warnung](#alert-category) | Enthält den Datensatz mit den Aktivierungen für Azure-Warnungen. Ein Beispiel für ein Warnungsereignis ist _CPU-Auslastung auf ‚myVM‘ liegt in den letzten 5 Minuten über 80_.|
| [Automatische Skalierung](#autoscale-category) | Enthält Datensätze zu Ereignissen im Zusammenhang mit der Engine für die Autoskalierung – basierend auf den Einstellungen für die Autoskalierung, die Sie in Ihrem Abonnement definiert haben. Ein Beispiel für ein Ereignis der Autoskalierung ist _Fehler beim automatischen Hochskalieren_. |
| [Empfehlung](#recommendation-category) | Enthält Empfehlungsereignisse von Azure Advisor. |
| [Security](#security-category) | Enthält den Datensatz, der von allen Warnungen in Azure Security Center generiert wurde. Ein Beispiel für ein Sicherheitsereignis ist _Verdächtige Datei mit doppelter Erweiterung ausgeführt_. |
| [Richtlinie](#policy-category) | Enthält Datensätze aller Aktionsvorgänge für Auswirkungen, die von Azure Policy ausgeführt werden. Beispiele für Policy-Ereignisse sind _Überwachen_ und _Ablehnen_. Jede Aktion, die von Policy ausgeführt wird, ist als ein Vorgang für eine Ressource modelliert. |

## <a name="administrative-category"></a>Kategorie „Verwaltung“
Diese Kategorie enthält die Datensätze aller Erstellungs-, Aktualisierungs-, Lösch- und Aktionsvorgänge, die über Resource Manager ausgeführt wurden. Zu den Ereignissen in dieser Kategorie gehören das Erstellen eines virtuellen Computers und das Löschen einer Netzwerksicherheitsgruppe. Jede Aktion, die von einem Benutzer oder einer Anwendung mithilfe von Resource Manager ausgeführt wird, wird als Vorgang für einen bestimmten Ressourcentyp modelliert. Wenn der Vorgangstyp „Schreiben“, „Löschen“ oder „Aktion“ ist, werden die Datensätze zum Start und zum Erfolg oder Fehler dieses Vorgangs in der Kategorie „Administration“ aufgezeichnet. Die Kategorie „Administration“ enthält außerdem alle Änderungen an der rollenbasierten Zugriffssteuerung in Azure in einem Abonnement.

### <a name="sample-event"></a>Beispielereignis
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.microsoft.com/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| authorization |Blob mit Azure RBAC-Eigenschaften des Ereignisses. Enthält normalerweise die Eigenschaften „action“, „role“ und „scope“. |
| caller |E-Mail-Adresse des Benutzers, der den Vorgang, UPN-Anspruch oder SPN-Anspruch auf Grundlage der Verfügbarkeit ausgeführt hat. |
| channels |Einer der folgenden Werte: „Admin“, „Operation“ |
| claims |Das JWT-Token, das von Active Directory zum Authentifizieren des Benutzers oder der Anwendung zur Ausführung dieses Vorgangs in Resource Manager verwendet wird. |
| correlationId |Normalerweise eine GUID im Zeichenfolgenformat. Ereignisse, die über die gleiche correlationId verfügen, gehören zu derselben übergeordneten Aktion. |
| description |Statische Beschreibung eines Ereignisses in Textform. |
| eventDataId |Eindeutiger Bezeichner eines Ereignisses. |
| eventName | Anzeigename des Administrative-Ereignisses. |
| category | Immer „Administrative“ |
| httpRequest |Blob, das die HTTP-Anforderung beschreibt. Umfasst üblicherweise „clientRequestId“, „clientIpAddress“ und „method“ (HTTP-Methode, z.B. PUT). |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Name der Ressourcengruppe für die betroffene Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die betroffene Ressource. |
| resourceType | Der Typ der Ressource, die von einem Administrative-Ereignis betroffen war. |
| resourceId |Ressourcen-ID der betroffenen Ressource. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| properties |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus |Üblicherweise der HTTP-Statuscode des entsprechenden REST-Aufrufs, kann aber auch weitere Zeichenfolgen zur Beschreibung eines untergeordneten Status enthalten, z. B. die folgenden gängigen Werte: OK (HTTP-Statuscode: 200), Erstellt (HTTP-Statuscode: 201), Akzeptiert (HTTP-Statuscode: 202), Kein Inhalt (HTTP-Statuscode: 204), Ungültige Anforderung (HTTP-Statuscode: 400), Nicht gefunden (HTTP-Statuscode: 404), Konflikt (HTTP-Statuscode: 409), Interner Serverfehler (HTTP-Statuscode: 500), Dienst nicht verfügbar (HTTP-Statuscode: 503), Gatewaytimeout (HTTP-Statuscode: 504). |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="service-health-category"></a>Kategorie „Dienstintegrität“
Diese Kategorie enthält Datensätze zu allen Incidents im Zusammenhang mit der Dienstintegrität, die in Azure aufgetreten sind. Ein Beispiel für ein Ereignis in dieser Kategorie ist „Ausfallzeiten bei SQL Azure in der Region ‚USA, Osten‘“. Es gibt fünf Typen von Ereignissen zur Dienstintegrität: „Action Required“, „Assisted Recovery“, „Incident“, „Maintenance“, „Information“ oder „Security“. Sie werden nur angezeigt, wenn eine Ressource in Ihrem Abonnement von dem Ereignis betroffen wäre.

### <a name="sample-event"></a>Beispielereignis
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Cognitive Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Informationen zu den Werten in den Eigenschaften finden Sie im Artikel [Anzeigen von Dienstintegritätsbenachrichtigungen im Azure-Portal](../../service-health/service-notifications.md).

## <a name="resource-health-category"></a>Kategorie „Ressourcenintegrität“
Diese Kategorie enthält Datensätze zu allen Ereignissen im Zusammenhang mit der Ressourcenintegrität, die für Ihre Azure-Ressourcen aufgetreten sind. Ein Beispiel für die Art der Ereignisse, die in dieser Kategorie angezeigt werden, ist „Integritätsstatus des virtuellen Computers ist zu Nicht verfügbar gewechselt“. Ereignisse zur Ressourcenintegrität können über einen von vier Integritätsstatus verfügen: „Available“, „Unavailable“, „Degraded“ und „Unknown“. Darüber hinaus können Ereignisse zur Ressourcenintegrität kategorisiert werden. Dabei sind die Kategorien „Von der Plattform initiiert“ oder „Vom Benutzer initiiert“ verfügbar.

### <a name="sample-event"></a>Beispielereignis

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Admin, Operation“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| description |Statische Beschreibung des Warnungsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Warnungsereignisses. |
| category | Immer „ResourceHealth“ |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“, „Informational“ oder „Verbose“ |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| resourceGroupName |Der Name der Ressourcengruppe, die die Ressource enthält. |
| resourceProviderName |Immer „Microsoft.Resourcehealth/healthevent/action“. |
| resourceType | Der Typ der Ressource, die von einem Resource Health-Ereignis betroffen war. |
| resourceId | Der Name der Ressourcen-ID für die betroffene Ressource. |
| status |Zeichenfolge, die den Status des Integritätsereignisses beschreibt. Mögliche Werte: „Active“, „Resolved“, „InProgress“, „Updated“. |
| subStatus | In der Regel für Warnungen null. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |
| properties |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben.|
| properties.title | Eine benutzerfreundliche Zeichenfolge, die den Integritätsstatus der Ressource beschreibt. |
| properties.details | Eine benutzerfreundliche Zeichenfolge, die weitere Details zum Ereignis beschreibt. |
| properties.currentHealthStatus | Der aktuelle Integritätsstatus der Ressource. Einer der folgenden Werte: „Available“, „Unavailable“, „Degraded“ und „Unknown“. |
| properties.previousHealthStatus | Der vorherige Integritätsstatus der Ressource. Einer der folgenden Werte: „Available“, „Unavailable“, „Degraded“ und „Unknown“. |
| properties.type | Eine Beschreibung des Typs des Resource Health-Ereignisses. |
| properties.cause | Eine Beschreibung der Ursache des Resource Health-Ereignisses. Entweder „UserInitiated“ oder „PlatformInitiated“. |


## <a name="alert-category"></a>Kategorie „Warnung“
Diese Kategorie enthält die Datensätze zu allen Aktivierungen von klassischen Azure-Warnungen. Ein Beispiel für ein Ereignis in dieser Kategorie ist „CPU-Auslastung auf ‚myVM‘ liegt in den letzten 5 Minuten über 80“. Eine Vielzahl von Azure-Systemen weist ein Konzept für Warnungen auf: Sie können eine Regel definieren und erhalten eine Benachrichtigung, wenn die Bedingungen mit der Regel übereinstimmen. Jedes Mal, wenn ein unterstützter Azure-Warnungstyp „aktiviert“ wird oder die Bedingungen erfüllt sind, sodass eine Benachrichtigung generiert wird, wird ein Datensatz der Aktivierung auch in dieser Kategorie des Aktivitätsprotokolls abgelegt.

### <a name="sample-event"></a>Beispielereignis

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| caller | Immer „Microsoft.Insights/alertRules“ |
| channels | Immer „Admin, Operation“ |
| claims | JSON-Blob mit dem Dienstprinzipalnamen (Service Principal Name, SPN) oder dem Ressourcentyp der Warnungs-Engine. |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| description |Statische Beschreibung des Warnungsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Warnungsereignisses. |
| category | Immer „Alert“ |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Bei einer Metrikwarnung der Name der Ressourcengruppe für die betroffene Ressource. Bei anderen Warnungstypen der Name der Ressourcengruppe, die die Warnung selbst enthält. |
| resourceProviderName |Bei einer Metrikwarnung der Name des Ressourcenanbieters für die betroffene Ressource. Bei anderen Warnungstypen der Name des Ressourcenanbieters für die Warnung selbst. |
| resourceId | Bei einer Metrikwarnung der Name der Ressourcen-ID für die betroffene Ressource. Bei anderen Warnungstypen die Ressourcen-ID der Warnungsressource selbst. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| properties |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | In der Regel für Warnungen null. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

### <a name="properties-field-per-alert-type"></a>Feld „properties“ pro Warnungstyp
Das Feld „properties“ enthält abhängig von der Quelle des Warnungsereignisses unterschiedliche Werte. Zwei allgemeine Ereignisanbieter für Warnungen sind Aktivitätsprotokollwarnungen und Metrikwarnungen.

#### <a name="properties-for-activity-log-alerts"></a>Eigenschaften für Aktivitätsprotokollwarnungen
| Elementname | BESCHREIBUNG |
| --- | --- |
| properties.subscriptionId | Die Abonnement-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.eventDataId | Die Ereignisdaten-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.resourceGroup | Die Ressourcengruppe aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.resourceId | Die Ressourcen-ID aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.eventTimestamp | Der Ereigniszeitstempel aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.operationName | Der Vorgangsname aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde. |
| properties.status | Der Status aus dem Aktivitätsprotokollereignis, das verursacht hat, dass diese Warnungsregel des Aktivitätsprotokolls aktiviert wurde.|

#### <a name="properties-for-metric-alerts"></a>Eigenschaften für Metrikwarnungen
| Elementname | BESCHREIBUNG |
| --- | --- |
| properties.RuleUri | Ressourcen-ID der Metrikwarnungsregel selbst. |
| properties.RuleName | Der Name der Metrikwarnungsregel. |
| properties.RuleDescription | Die Beschreibung der Metrikwarnungsregel (wie in der Warnungsregel definiert). |
| properties.Threshold | Der Schwellenwert, der bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.WindowSizeInMinutes | Die Fenstergröße, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.Aggregation | Der Aggregationstyp, der in der Metrikwarnungsregel definiert ist. |
| properties.Operator | Der bedingte Operator, der bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.MetricName | Der Metrikname der Metrik, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |
| properties.MetricUnit | Die Metrikeinheit für die Metrik, die bei der Auswertung der Metrikwarnungsregel verwendet wird. |

## <a name="autoscale-category"></a>Kategorie „Autoskalierung“
Diese Kategorie enthält Datensätze zu Ereignissen im Zusammenhang mit der Engine für die automatische Skalierung – basierend auf den Einstellungen für die automatische Skalierung, die Sie in Ihrem Abonnement definiert haben. Ein Beispiel für Ereignisse in dieser Kategorie ist „Fehler beim automatischen Hochskalieren“. Mit der automatischen Skalierung können Sie die Anzahl der Instanzen eines unterstützten Ressourcentyps basierend auf der Tageszeit und/oder Lastdaten (Metrik) mithilfe einer Einstellung für die automatische Skalierung automatisch auf- oder abskalieren. Wenn die Bedingungen zum Hoch- oder Herunterskalieren erfüllt sind, werden Ereignisse zum Start und zum Erfolg oder Fehler in dieser Kategorie aufgezeichnet.

### <a name="sample-event"></a>Beispielereignis
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| caller | Immer „Microsoft.Insights/autoscaleSettings“ |
| channels | Immer „Admin, Operation“ |
| claims | JSON-Blob mit dem Dienstprinzipalnamen (Service Principal Name, SPN) oder dem Ressourcentyp der Engine für die automatische Skalierung. |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| description |Statische Beschreibung des Ereignisses der automatischen Skalierung. |
| eventDataId |Eindeutiger Bezeichner des Ereignisses der automatischen Skalierung. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ und „Informational“ |
| resourceGroupName |Name der Ressourcengruppe der Einstellung für die automatische Skalierung. |
| resourceProviderName |Name des Ressourcenanbieters der Einstellung für die automatische Skalierung. |
| resourceId |Ressourcen-ID der Einstellung für die automatische Skalierung. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| properties |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. |
| properties.Description | Detaillierte Beschreibung des Vorgangs der Engine für die automatische Skalierung. |
| properties.ResourceName | Ressourcen-ID der betroffenen Ressource (die Ressource, für die die Skalierungsaktion ausgeführt wurde) |
| properties.OldInstancesCount | Die Anzahl von Instanzen, bevor die automatische Skalierung wirksam war. |
| properties.NewInstancesCount | Die Anzahl von Instanzen, nachdem die automatische Skalierung wirksam war. |
| properties.LastScaleActionTime | Der Zeitstempel des Zeitpunkts, zu dem die automatische Skalierung aufgetreten ist. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | In der Regel für die automatische Skalierung null. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="security-category"></a>Kategorie „Sicherheit“
Diese Kategorie enthält den Datensatz, der von Warnungen in Azure Security Center generiert wurde. Ein Beispiel für den Typ der Ereignisse, die in dieser Kategorie angezeigt werden, ist „Verdächtige Datei mit doppelter Erweiterung ausgeführt“.

### <a name="sample-event"></a>Beispielereignis
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Vorgang“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| description |Statische Beschreibung des Sicherheitsereignisses. |
| eventDataId |Eindeutiger Bezeichner des Sicherheitsereignisses. |
| eventName |Anzeigename des Sicherheitsereignisses. |
| category | Immer „Security“ |
| id |Eindeutiger Ressourcenbezeichner des Sicherheitsereignisses. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ oder „Informational“ |
| resourceGroupName |Name der Ressourcengruppe für die Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die Azure Security Center. Immer „Microsoft.Security“. |
| resourceType |Der Typ der Ressource, die das Sicherheitsereignis generiert hat, z. B. „Microsoft.Security/locations/alerts“. |
| resourceId |Ressourcen-ID der Sicherheitswarnung. |
| operationId |Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName |Name des Vorgangs. |
| properties |Satz mit `<Key, Value>`-Paaren (Wörterbuch), die Details des Ereignisses beschreiben. Diese Eigenschaften variieren je nach Typ der Sicherheitswarnung. Eine Beschreibung der Warnungstypen, die aus Security Center stammen, finden Sie [auf dieser Seite](../../security-center/security-center-alerts-overview.md). |
| properties.Severity |Der Schweregrad. Mögliche Werte sind „Hoch“, „Mittel“ oder „Niedrig“. |
| status |Zeichenfolge, die den Status des Vorgangs beschreibt. Gängige Werte: „Started“, „In Progress“, „Succeeded“, „Failed“, „Active“, „Resolved“. |
| subStatus | Für Sicherheitsereignisse in der Regel NULL. |
| eventTimestamp |Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |

## <a name="recommendation-category"></a>Kategorie „Empfehlung“
Diese Kategorie enthält den Datensatz mit den neuen Empfehlungen, die für Ihre Dienste generiert werden. Ein Beispiel für eine Empfehlung wäre „Verwenden Sie für eine verbesserte Fehlertoleranz Verfügbarkeitsgruppen“. Vier Typen von Empfehlungsereignissen können generiert werden: „High Availability“, „Performance“, „Security“ und „Cost Optimization“. 

### <a name="sample-event"></a>Beispielereignis
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Beschreibungen der Eigenschaften
| Elementname | BESCHREIBUNG |
| --- | --- |
| channels | Immer „Vorgang“ |
| correlationId | Eine GUID im Zeichenfolgenformat. |
| description |Statische Beschreibung des Empfehlungsereignisses. |
| eventDataId | Eindeutiger Bezeichner des Empfehlungsereignisses. |
| category | Immer „Empfehlung“ |
| id |Eindeutiger Ressourcenbezeichner des Empfehlungsereignisses. |
| level |Ebene des Ereignisses. Einer der folgenden Werte: „Critical“, „Error“, „Warning“ oder „Informational“ |
| operationName |Name des Vorgangs.  Immer „Microsoft.Advisor/generateRecommendations/action“|
| resourceGroupName |Name der Ressourcengruppe für die Ressource. |
| resourceProviderName |Name des Ressourcenanbieters für die Ressource, für die diese Empfehlung gilt, z.B. „MICROSOFT.COMPUTE“. |
| resourceType |Name des Ressourcentyps für die Ressource, für die diese Empfehlung gilt, z.B. „MICROSOFT.COMPUTE/virtualmachines“. |
| resourceId |Ressourcen-ID der Ressource, für die die Empfehlung gilt. |
| status | Immer „Aktiv“ |
| submissionTimestamp |Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId |Die Azure-Abonnement-ID. |
| properties |Satz mit `<Key, Value>`-Paaren (also ein Wörterbuch), die die Details der Empfehlung beschreiben.|
| properties.recommendationSchemaVersion| Schemaversion der Empfehlungseigenschaften, die im Aktivitätsprotokolleintrag veröffentlicht werden. |
| properties.recommendationCategory | Kategorie der Empfehlung. Mögliche Werte sind „Hochverfügbarkeit“, „Leistung“, „Sicherheit“ und „Kosten“. |
| properties.recommendationImpact| Auswirkung der Empfehlung. Mögliche Werte sind „Hoch“, „Mittel“ oder „Niedrig“. |
| properties.recommendationRisk| Risiko der Empfehlung. Mögliche Werte sind „Fehler“, „Warnung“, „Kein“. |

## <a name="policy-category"></a>Kategorie „Richtlinie“

Diese Kategorie enthält Datensätze aller Aktionsvorgänge für Auswirkungen, die von [Azure Policy](../../governance/policy/overview.md) ausgeführt werden. Beispiele für Ereignistypen, die in dieser Kategorie angezeigt werden, sind _Audit_ und _Deny_. Jede Aktion, die von Policy ausgeführt wird, ist als ein Vorgang für eine Ressource modelliert.

### <a name="sample-policy-event"></a>Beispiel eines Richtlinienereignisses

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Beschreibungen der Richtlinienereigniseigenschaften

| Elementname | BESCHREIBUNG |
| --- | --- |
| authorization | Array von Azure RBAC-Eigenschaften des Ereignisses. Bei neuen Ressourcen ist dies die Aktion und der Bereich der Anforderung, die eine Auswertung ausgelöst hat. Bei vorhandenen Ressourcen lautet die Aktion „Microsoft.Resources/checkPolicyCompliance/read“. |
| caller | Bei neuen Ressourcen ist dies die Identität, die eine Bereitstellung initiiert hat. Bei vorhandenen Ressourcen ist dies die GUID des Microsoft Azure Policy Insights-Ressourcenanbieters. |
| channels | Richtlinienereignisse verwenden nur den Kanal „Operation“. |
| claims | Das JWT-Token, das von Active Directory zum Authentifizieren des Benutzers oder der Anwendung zur Ausführung dieses Vorgangs in Resource Manager verwendet wird. |
| correlationId | Normalerweise eine GUID im Zeichenfolgenformat. Ereignisse, die über die gleiche correlationId verfügen, gehören zu derselben übergeordneten Aktion. |
| description | Dieses Feld ist bei Richtlinienereignissen leer. |
| eventDataId | Eindeutiger Bezeichner eines Ereignisses. |
| eventName | Entweder „BeginRequest“ oder „EndRequest“. „BeginRequest“ wird für verzögerte Auswertungen des Typs „AuditIfNotExists“ und „DeployIfNotExists“ verwendet und wenn eine „DeployIfNotExists“-Auswirkung eine Vorlagenbereitstellung startet. Alle anderen Vorgänge geben „EndRequest“ zurück. |
| category | Deklariert das Aktivitätsprotokollereignis als zu „Policy“ gehörend. |
| eventTimestamp | Zeitstempel der Ereignisgenerierung durch den Azure-Dienst, der die zum Ereignis gehörende Anforderung verarbeitet hat. |
| id | Eindeutiger Bezeichner des Ereignisses auf der jeweiligen Ressource. |
| level | Ebene des Ereignisses. „Audit“ verwendet „Warning“ und „Deny“ verwendet „Error“. Ein „AuditIfNotExists“- oder „DeployIfNotExists“-Fehler kann je nach Schweregrad „Warning“ oder „Error“ generieren. Alle anderen Richtlinienereignisse verwenden „Informational“. |
| operationId | Eine GUID, die von den Ereignissen eines einzelnen Vorgangs gemeinsam genutzt wird. |
| operationName | Der Name des Vorgangs und korreliert direkt mit der Richtlinienauswirkung. |
| resourceGroupName | Der Name der Ressourcengruppe für die ausgewertete Ressource. |
| resourceProviderName | Der Name des Ressourcenanbieters für die ausgewertete Ressource. |
| resourceType | Bei neuen Ressourcen ist dies der Typ, der ausgewertet wird. Bei vorhandenen Ressourcen wird „Microsoft.Resources/checkPolicyCompliance“ zurückgegeben. |
| resourceId | Die Ressourcen-ID der ausgewerteten Ressource. |
| status | Eine Zeichenfolge, die den Status des Ergebnisses der Richtlinienauswertung beschreibt. Die meisten Richtlinienauswertungen geben „Succeeded“ zurück, doch wird bei einer „Deny“-Auswirkung „Failed“ zurückgegeben. Fehler in „AuditIfNotExists“ oder „DeployIfNotExists“ geben ebenfalls „Failed“ zurück. |
| subStatus | Das Feld ist bei Richtlinienereignissen leer. |
| submissionTimestamp | Zeitstempel des Zeitpunkts, ab dem das Ereignis für Abfragen verfügbar war. |
| subscriptionId | Die Azure-Abonnement-ID. |
| properties.isComplianceCheck | Gibt „False“ zurück, wenn eine neue Ressource bereitgestellt wird oder Resource Manager-Eigenschaften einer vorhandenen Ressource aktualisiert werden. Alle anderen [Auswertungsauslöser](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) ergeben „True“. |
| properties.resourceLocation | Die Azure-Region der ausgewerteten Ressource. |
| properties.ancestors | Eine durch Trennzeichen getrennte Liste von übergeordneten Verwaltungsgruppen in der Reihenfolge von der direkt übergeordneten Gruppe bis zur entferntesten über-übergeordneten Gruppe. |
| properties.policies | Enthält Details zur Richtliniendefinition, Zuweisung, Auswirkung und Parametern, deren Ergebnis diese Richtlinienauswertung ist. |
| relatedEvents | Dieses Feld ist bei Richtlinienereignissen leer. |


## <a name="schema-from-storage-account-and-event-hubs"></a>Schema aus Speicherkonto und Event Hubs
Beim Streamen des Azure-Aktivitätsprotokolls an ein Speicherkonto oder Event Hub entsprechen die Daten dem [Ressourcenprotokollschema](./resource-logs-schema.md). Die folgende Tabelle zeigt die Zuordnung der Eigenschaften aus den oben genannten Schemas zum Ressourcenprotokollschema.

> [!IMPORTANT]
> Das Format der Aktivitätsprotokolldaten, die in das Speicherkonto geschrieben werden, wurde am 1. November 2018 in JSON Lines geändert. Einzelheiten zu dieser Formatumstellung finden Sie unter [Vorbereiten der Formatumstellung auf Azure Monitor-Ressourcenprotokolle, die in einem Speicherkonto archiviert werden](./resource-logs-blob-format.md).


| Eigenschaft im Ressourcenprotokollschema | Eigenschaft im REST-API-Schema des Aktivitätsprotokolls | Notizen |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | „subscriptionId“, „resourceType“ und „resourceGroupName“ werden alle aus der „resourceId“ abgeleitet. |
| operationName | operationName.value |  |
| category | Teil des Vorgangsnamens | Entnahme des Vorgangstyps – „Write“/„Delete“/„Action“ |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | – | Immer 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | Ansprüche und Autorisierungseigenschaften |  |
| Ebene | Ebene |  |
| location | – | Ort, an dem das Ereignis verarbeitet wurde. *Dies ist nicht der Speicherort der Ressource, sondern der Ort, an dem das Ereignis verarbeitet wurde. Diese Eigenschaft wird in einem kommenden Update entfernt.* |
| Eigenschaften | properties.eventProperties |  |
| properties.eventCategory | category | Wenn „properties.eventCategory“ nicht vorhanden ist, ist die Kategorie „Administrative“ |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |

Es folgt ein Beispiel für ein Ereignis mit diesem Schema.

``` JSON
{
    "records": [
        {
            "time": "2019-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "00000000-0000-0000-0000-000000000000",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```





## <a name="next-steps"></a>Nächste Schritte
* [Weitere Informationen zum Aktivitätsprotokoll](platform-logs-overview.md)
* [Erstellen einer Diagnoseeinstellung zum Senden des Aktivitätsprotokolls an einen Log Analytics-Arbeitsbereich, Azure Storage oder Event Hubs](diagnostic-settings.md)
