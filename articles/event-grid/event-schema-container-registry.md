---
title: Container Registry-Ereignisschema in Azure Event Grid
description: In diesem Artikel werden die Eigenschaften beschrieben, die mit Azure Event Grid für Container Registry-Ereignisse bereitgestellt werden.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 03/12/2019
ms.author: spelluru
ms.openlocfilehash: c5998ff428c4b6f4c1f7a4087c6ccb27d93773eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345463"
---
# <a name="azure-event-grid-event-schema-for-container-registry"></a>Azure Event Grid-Ereignisschema für Container Registry

In diesem Artikel werden die Eigenschaften und das Schema für Container Registry-Ereignisse beschrieben. Eine Einführung in Ereignisschemas finden Sie unter [Azure Event Grid-Ereignisschema](event-schema.md).

## <a name="available-event-types"></a>Verfügbare Ereignistypen

Azure Container Registry gibt die folgenden Ereignistypen aus:

| Ereignistypen | BESCHREIBUNG |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Wird ausgelöst, wenn mithilfe von Push ein Image übertragen wird. |
| Microsoft.ContainerRegistry.ImageDeleted | Wird ausgelöst, wenn ein Image gelöscht wird. |
| Microsoft.ContainerRegistry.ChartPushed | Wird ausgelöst, wenn ein Helm-Diagramm gepusht wird. |
| Microsoft.ContainerRegistry.ChartDeleted | Wird ausgelöst, wenn ein Helm-Diagramm gelöscht wird. |

## <a name="example-event"></a>Beispielereignis

Das folgende Beispiel zeigt das Schema eines Ereignisses zu einem mithilfe von Push übertragenen Image: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Das Schema für ein Imagelöschungsereignis sieht ähnlich wie im folgenden Beispiel aus:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Das Schema für ein Ereignis „Diagramm gepusht“ ähnelt dem Schema für ein Ereignis „Image gepusht“, umfasst aber kein Anforderungsobjekt:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Das Schema für ein Ereignis „Diagramm gelöscht“ ähnelt dem Schema für ein Ereignis „Image gelöscht“, umfasst aber kein Anforderungsobjekt:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
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
| data | object | Ereignisdaten für Blob Storage. |
| dataVersion | Zeichenfolge | Die Schemaversion des Datenobjekts. Der Herausgeber definiert die Schemaversion. |
| metadataVersion | Zeichenfolge | Die Schemaversion der Ereignismetadaten. Event Grid definiert das Schema der Eigenschaften der obersten Ebene. Dieser Wert wird von Event Grid bereitgestellt. |

Das Datenobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| id | Zeichenfolge | Die Ereignis-ID. |
| timestamp | Zeichenfolge | Der Zeitpunkt, an dem das Ereignis aufgetreten ist. |
| action | Zeichenfolge | Die Aktion, die das angegebene Ereignis umfasst. |
| target | object | Das Ziel des Ereignisses. |
| request | object | Die Anforderung, die das Ereignis generiert hat. |

Das Zielobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| mediaType | Zeichenfolge | Der MIME-Typ des Objekts, auf das verwiesen wird. |
| size | integer | Die Byte-Anzahl des Inhalts. Entspricht dem Feld „Length“ (Länge). |
| digest | Zeichenfolge | Zusammenfassung des Inhalts gemäß Definition in der HTTP-API-Spezifikation der Registrierung (V2). |
| length | integer | Die Byte-Anzahl des Inhalts. Entspricht dem Feld „Size“ (Größe). |
| repository | Zeichenfolge | Der Name des Repositorys. |
| tag | Zeichenfolge | Der Tagname. |
| name | Zeichenfolge | Der Diagrammname. |
| version | Zeichenfolge | Die Diagrammversion. |

Das Anforderungsobjekt weist die folgenden Eigenschaften auf:

| Eigenschaft | Typ | BESCHREIBUNG |
| -------- | ---- | ----------- |
| id | Zeichenfolge | Die ID der Anforderung, die das Ereignis initiiert hat. |
| addr | Zeichenfolge | Die IP-Adresse oder der Hostname und u.U. der Port der Clientverbindung, die das Ereignis initiiert hat. Dieser Wert ist die „RemoteAddr“ aus der standardmäßigen HTTP-Anforderung. |
| host | Zeichenfolge | Der extern zugängliche Hostname der Registrierungsinstanz, der im HTTP-Hostheader von eingehenden Anforderungen angegeben ist. |
| method | Zeichenfolge | Die Anforderungsmethode, die das Ereignis generiert hat. |
| useragent | Zeichenfolge | Der Benutzer-Agent-Header der Anforderung. |

## <a name="next-steps"></a>Nächste Schritte

* Eine Einführung zu Azure Event Grid finden Sie unter [Einführung in Azure Event Grid](overview.md).
* Weitere Informationen zum Erstellen eines Azure Event Grid-Abonnements finden Sie unter [Event Grid-Abonnementschema](subscription-creation-schema.md).
