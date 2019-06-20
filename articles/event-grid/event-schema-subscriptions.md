---
title: Ereignisschema des Azure Event Grid-Abonnements
description: Beschreibt die Eigenschaften, die mit Azure Event Grid für Abonnementereignisse bereitgestellt werden
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 4994063dfc3bce88489f70969c06bf36b591f907
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561675"
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Azure Event Grid-Ereignisschema für Abonnements

In diesem Artikel werden die Eigenschaften und das Schema für Azure-Abonnementereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

Azure-Abonnements und Ressourcengruppen geben die gleichen Ereignistypen aus. Die Ereignistypen beziehen sich auf Ressourcenänderungen oder -aktionen. Der Hauptunterschied besteht darin, dass Ressourcengruppen Ereignisse für Ressourcen innerhalb der Ressourcengruppe und Azure-Abonnements Ereignisse für Ressourcen im gesamten Abonnement ausgeben.

Ressourcenereignisse werden für PUT-, PATCH-, POST- und DELETE-Vorgänge erstellt, die an `management.azure.com` gesendet werden. Für GET-Vorgänge werden keine Ereignisse erstellt. Für Vorgänge, die an die Datenebene gesendet werden (etwa `myaccount.blob.core.windows.net`), werden keine Ereignisse erstellt. Die Aktionsereignisse stellen Ereignisdaten für Vorgänge wie das Auflisten der Schlüssel für eine Ressource bereit.

Wenn Sie Ereignisse für ein Azure-Abonnement abonnieren, erhält Ihr Endpunkt alle Ereignisse für dieses Abonnement. Hierzu können Ereignisse gehören, die für Ihre Zwecke angezeigt werden sollen, z.B. zur Aktualisierung eines virtuellen Computers. Es können aber auch Ereignisse vorhanden sein, die für Sie ggf. weniger wichtig sind, z.B. das Schreiben eines neuen Eintrags im Bereitstellungsverlauf. Sie können alle Ereignisse an einem Endpunkt empfangen und Code schreiben, in dem die Ereignisse verarbeitet werden, die Sie behandeln möchten. Sie können aber auch einen Filter festlegen, wenn Sie das Ereignisabonnement erstellen.

Zur programmgesteuerten Verarbeitung von Ereignissen können Sie Ereignisse sortieren, indem Sie sich den Wert für `operationName` ansehen. Beispielsweise werden für Ihren Endpunkt ggf. nur Ereignisse für Vorgänge verarbeitet, die `Microsoft.Compute/virtualMachines/write` oder `Microsoft.Storage/storageAccounts/write` entsprechen.

Der Betreff des Ereignisses ist die Ressourcen-ID der Ressource, die das Ziel des Vorgangs ist. Geben Sie zum Filtern von Ereignissen für eine Ressource beim Erstellen des Ereignisabonnements die Ressourcen-ID an. Verwenden Sie zum Filtern nach einem Ressourcentyp einen Wert im folgenden Format: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Eine Liste von Beispielskripts und Tutorials finden Sie in den Informationen zur [Ereignisquelle für Azure-Abonnements](event-sources.md#azure-subscriptions).

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure-Abonnements geben Verwaltungsereignisse von Azure Resource Manager aus, beispielsweise wenn ein virtueller Computer erstellt oder ein Speicherkonto gelöscht wird.

| Ereignistypen | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Wird ausgelöst, wenn eine Aktion für eine Ressource abgebrochen wird. |
| Microsoft.Resources.ResourceActionFailure | Wird ausgelöst, wenn eine Aktion für eine Ressource fehlschlägt. |
| Microsoft.Resources.ResourceActionSuccess | Wird ausgelöst, wenn eine Aktion für eine Ressource erfolgreich ausgeführt wurde. |
| Microsoft.Resources.ResourceDeleteCancel | Wird ausgelöst, wenn ein Löschvorgang abgebrochen wird. Dieses Ereignis tritt ein, wenn eine Vorlagenbereitstellung abgebrochen wird. |
| Microsoft.Resources.ResourceDeleteFailure | Wird ausgelöst, wenn ein Löschvorgang fehlschlägt. |
| Microsoft.Resources.ResourceDeleteSuccess | Wird ausgelöst, wenn ein Löschvorgang erfolgreich ausgeführt wurde. |
| Microsoft.Resources.ResourceWriteCancel | Wird ausgelöst, wenn ein Erstellungs- oder Aktualisierungsvorgang abgebrochen wird. |
| Microsoft.Resources.ResourceWriteFailure | Wird ausgelöst, wenn ein Erstellungs- oder Aktualisierungsvorgang fehlschlägt. |
| Microsoft.Resources.ResourceWriteSuccess | Wird ausgelöst, wenn ein Erstellungs- oder Aktualisierungsvorgang erfolgreich ausgeführt wurde. |

## <a name="example-event"></a>Beispielereignis

Im folgenden Beispiel wird das Schema für das Ereignis **ResourceWriteSuccess** veranschaulicht. Das gleiche Schema wird für die Ereignisse **ResourceWriteFailure** und **ResourceWriteCancel** verwendet, nur mit anderen Werten für `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Im folgenden Beispiel wird das Schema für das Ereignis **ResourceDeleteSuccess** veranschaulicht. Das gleiche Schema wird für die Ereignisse **ResourceDeleteFailure** und **ResourceDeleteCancel** verwendet, nur mit anderen Werten für `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}"
}]
```

Im folgenden Beispiel wird das Schema für ein **ResourceActionSuccess**-Ereignis veranschaulicht. Das gleiche Schema wird für **ResourceActionFailure**- und **ResourceActionCancel**-Ereignisse verwendet, nur mit anderen Werten für `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}" 
}]
```

## <a name="event-properties"></a>Ereigniseigenschaften

Ein Ereignis weist die folgenden Daten auf oberster Ebene aus:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| topic | Zeichenfolge | Vollständiger Ressourcenpfaf zur Ereignisquelle. Dieses Feld ist nicht beschreibbar. Dieser Wert wird von Event Grid bereitgestellt. |
| subject | Zeichenfolge | Vom Herausgeber definierter Pfad zum Ereignisbetreff |
| eventType | Zeichenfolge | Einer der registrierten Ereignistypen für die Ereignisquelle. |
| eventTime | Zeichenfolge | Die Zeit, in der das Ereignis generiert wird, basierend auf der UTC-Zeit des Anbieters. |
| id | Zeichenfolge | Eindeutiger Bezeichner für das Ereignis. |
| data | object | Abonnementereignisdaten. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| authorization | object | Die angeforderte Autorisierung für den Vorgang. |
| claims | object | Die Eigenschaften der Ansprüche. Weitere Informationen finden Sie auf der Seite zur [JWT-Spezifikation](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | Zeichenfolge | Eine Vorgangs-ID für die Problembehandlung. |
| httpRequest | object | Die Details des Vorgangs. Dieses Objekt ist nur enthalten, wenn eine vorhandene Ressource aktualisiert oder eine Ressource gelöscht wird. |
| resourceProvider | Zeichenfolge | Der Ressourcenanbieter für den Vorgang. |
| resourceUri | Zeichenfolge | Der URI der Ressource im Vorgang. |
| operationName | Zeichenfolge | Der Vorgang, der übernommen wurde. |
| status | Zeichenfolge | Der Status des Vorgangs. |
| subscriptionId | Zeichenfolge | Die Abonnement-ID der Ressource. |
| tenantId | Zeichenfolge | Die Mandanten-ID der Ressource. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
