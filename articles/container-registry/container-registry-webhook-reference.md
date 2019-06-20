---
title: Referenz zu Azure Container Registry-Webhookschemas
description: Referenz zur JSON-Nutzlast von Webhookanforderungen für Azure Container Registry.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/05/2019
ms.author: danlep
ms.openlocfilehash: 4c0845b9cf5194ecbd0ab813997e17e070840f44
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331340"
---
# <a name="azure-container-registry-webhook-reference"></a>Referenz zu Azure Container Registry-Webhooks

Sie können für Ihre Containerregistrierung [Webhooks konfigurieren](container-registry-webhook.md), die Ereignisse generieren, wenn dafür bestimmte Aktionen durchgeführt werden. Aktivieren Sie beispielsweise Webhooks, die ausgelöst werden, wenn ein Containerimage oder Helm-Diagramm per Pushvorgang an eine Registrierung gesendet oder gelöscht wird. Wenn ein Webhook ausgelöst wird, sendet Azure Container Registry eine HTTP- oder HTTPS-Anforderung mit Informationen zum Ereignis an einen von Ihnen angegebenen Endpunkt. Ihr Endpunkt kann den Webhook dann verarbeiten und entsprechend agieren.

In den folgenden Abschnitten wird das Schema von Webhookanforderungen, die von unterstützten Ereignissen generiert werden, ausführlich beschrieben. Die Ereignisabschnitte enthalten das Nutzlastschema für den Ereignistyp, eine Beispiel-Anforderungsnutzlast und mindestens einen Beispielbefehl zum Auslösen des Webhooks.

Informationen zur Konfiguration von Webhooks für Ihre Azure Container Registry finden Sie unter [Verwenden von Azure Container Registry-Webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhookanforderungen

### <a name="http-request"></a>HTTP-Anforderung

Ein ausgelöster Webhook sendet eine HTTP-`POST`-Anforderung an den URL-Endpunkt, den Sie beim Konfigurieren des Webhooks angegeben haben.

### <a name="http-headers"></a>HTTP-Header

Webhookanforderungen enthalten als `Content-Type` die Angabe `application/json`, wenn Sie keinen benutzerdefinierten `Content-Type`-Header für Ihren Webhook angegeben haben.

Über die benutzerdefinierten Header hinaus, die Sie für den Webhook ggf. angeben, werden der Anforderung keine weiteren Header hinzugefügt.

## <a name="push-event"></a>Push-Ereignis

Auslösung per Webhook, wenn ein Containerimage per Pushvorgang in ein Repository übertragen wird.

### <a name="push-event-payload"></a>Nutzlast des Push-Ereignisses

|Element|Type|BESCHREIBUNG|
|-------------|----------|-----------|
|`id`|string|Die ID des Webhookereignisses.|
|`timestamp`|DateTime|Der Zeitpunkt, zu dem das Webhookereignis ausgelöst wurde.|
|`action`|string|Die Aktion, die das Webhookereignis ausgelöst hat.|
|[Ziel](#target)|Komplexer Typ|Das Ziel des Ereignisses, von dem das Webhookereignis ausgelöst wurde.|
|[Anforderung](#request)|Komplexer Typ|Die Anforderung, die das Webhookereignis generiert hat.|

### <a name="target"></a>Ziel

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`mediaType`|string|Der MIME-Typ des Objekts, auf das verwiesen wird.|
|`size`|Int32|Die Byte-Anzahl des Inhalts. Entspricht dem Feld „Length“ (Länge).|
|`digest`|string|Zusammenfassung des Inhalts gemäß Definition in der HTTP-API-Spezifikation der Registrierung (V2).|
|`length`|Int32|Die Byte-Anzahl des Inhalts. Entspricht dem Feld „Size“ (Größe).|
|`repository`|string|Der Name des Repositorys.|
|`tag`|string|Der Tagname des Images.|

### <a name="request"></a>Anforderung

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`id`|string|Die ID der Anforderung, die das Ereignis initiiert hat.|
|`host`|string|Der extern zugängliche Hostname der Registrierungsinstanz, der im HTTP-Hostheader von eingehenden Anforderungen angegeben ist.|
|`method`|string|Die Anforderungsmethode, die das Ereignis generiert hat.|
|`useragent`|string|Der Benutzer-Agent-Header der Anforderung.|

### <a name="payload-example-image-push-event"></a>Nutzlastbeispiel: Image-Pushereignis

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Beispiel: [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/)-Befehl, mit dem das **push**-Webhookereignis für das Image ausgelöst wird:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagramm-Push-Ereignis

Auslösung per Webhook, wenn ein Helm-Diagramm per Pushvorgang in ein Repository übertragen wird.

### <a name="chart-push-event-payload"></a>Nutzlast: Diagramm-Push-Ereignis

|Element|Type|BESCHREIBUNG|
|-------------|----------|-----------|
|`id`|string|Die ID des Webhookereignisses.|
|`timestamp`|DateTime|Der Zeitpunkt, zu dem das Webhookereignis ausgelöst wurde.|
|`action`|string|Die Aktion, die das Webhookereignis ausgelöst hat.|
|[Ziel](#helm_target)|Komplexer Typ|Das Ziel des Ereignisses, von dem das Webhookereignis ausgelöst wurde.|

### <a name="helm_target"></a>Ziel

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`mediaType`|string|Der MIME-Typ des Objekts, auf das verwiesen wird.|
|`size`|Int32|Die Byte-Anzahl des Inhalts.|
|`digest`|string|Zusammenfassung des Inhalts gemäß Definition in der HTTP-API-Spezifikation der Registrierung (V2).|
|`repository`|string|Der Name des Repositorys.|
|`tag`|string|Tagname des Diagramms.|
|`name`|string|Diagrammname.|
|`version`|string|Diagrammversion.|

### <a name="payload-example-chart-push-event"></a>Nutzlastbeispiel: Diagramm-Pushereignis

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Beispiel: Befehl der [Azure-Befehlszeilenschnittstelle](/cli/azure/acr), mit dem das **chart_push**-Webhookereignis ausgelöst wird:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Ereignis löschen

Wird per Webhook ausgelöst, wenn ein Image-Repository oder Manifest gelöscht wird. Wird nicht ausgelöst, wenn ein Tag gelöscht wird.

### <a name="delete-event-payload"></a>Ereignisnutzlast löschen

|Element|Type|BESCHREIBUNG|
|-------------|----------|-----------|
|`id`|string|Die ID des Webhookereignisses.|
|`timestamp`|DateTime|Der Zeitpunkt, zu dem das Webhookereignis ausgelöst wurde.|
|`action`|string|Die Aktion, die das Webhookereignis ausgelöst hat.|
|[Ziel](#delete_target)|Komplexer Typ|Das Ziel des Ereignisses, von dem das Webhookereignis ausgelöst wurde.|
|[Anforderung](#delete_request)|Komplexer Typ|Die Anforderung, die das Webhookereignis generiert hat.|

### <a name="delete_target"></a> Ziel

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`mediaType`|string|Der MIME-Typ des Objekts, auf das verwiesen wird.|
|`digest`|string|Zusammenfassung des Inhalts gemäß Definition in der HTTP-API-Spezifikation der Registrierung (V2).|
|`repository`|string|Der Name des Repositorys.|

### <a name="delete_request"></a> Anforderung

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`id`|string|Die ID der Anforderung, die das Ereignis initiiert hat.|
|`host`|string|Der extern zugängliche Hostname der Registrierungsinstanz, der im HTTP-Hostheader von eingehenden Anforderungen angegeben ist.|
|`method`|string|Die Anforderungsmethode, die das Ereignis generiert hat.|
|`useragent`|string|Der Benutzer-Agent-Header der Anforderung.|

### <a name="payload-example-image-delete-event"></a>Nutzlastbeispiel: Image-Löschereignis

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Beispiel: Befehle der [Azure-Befehlszeilenschnittstelle](/cli/azure/acr), mit denen ein **delete**-Ereigniswebhook ausgelöst wird:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Diagramm-Löschereignis

Auslösung per Webhook, wenn ein Helm-Diagramm oder ein Repository gelöscht wird. 

### <a name="chart-delete-event-payload"></a>Nutzlast: Diagramm-Löschereignis

|Element|Type|BESCHREIBUNG|
|-------------|----------|-----------|
|`id`|string|Die ID des Webhookereignisses.|
|`timestamp`|DateTime|Der Zeitpunkt, zu dem das Webhookereignis ausgelöst wurde.|
|`action`|string|Die Aktion, die das Webhookereignis ausgelöst hat.|
|[Ziel](#chart_delete_target)|Komplexer Typ|Das Ziel des Ereignisses, von dem das Webhookereignis ausgelöst wurde.|

### <a name="chart_delete_target"></a> Ziel

|Element|Type|BESCHREIBUNG|
|------------------|----------|-----------|
|`mediaType`|string|Der MIME-Typ des Objekts, auf das verwiesen wird.|
|`size`|Int32|Die Byte-Anzahl des Inhalts.|
|`digest`|string|Zusammenfassung des Inhalts gemäß Definition in der HTTP-API-Spezifikation der Registrierung (V2).|
|`repository`|string|Der Name des Repositorys.|
|`tag`|string|Tagname des Diagramms.|
|`name`|string|Diagrammname.|
|`version`|string|Diagrammversion.|

### <a name="payload-example-chart-delete-event"></a>Nutzlastbeispiel: Diagramm-Löschereignis

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Beispiel: Befehl der [Azure-Befehlszeilenschnittstelle](/cli/azure/acr), mit dem das **chart_delete**-Webhookereignis ausgelöst wird:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von Azure Container Registry-Webhooks](container-registry-webhook.md)