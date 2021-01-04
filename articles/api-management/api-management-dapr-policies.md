---
title: Azure API Management-Richtlinien für die Dapr-Integration | Microsoft-Dokumentation
description: Erfahren Sie mehr über Azure API Management-Richtlinien für die Interaktion mit Dapr-Microservices-Erweiterungen.
author: vladvino
ms.author: vlvinogr
ms.date: 10/23/2020
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 16788e3f547c5848893ba3867da4291c45b04408
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335484"
---
# <a name="api-management-dapr-integration-policies"></a>API Management-Richtlinien für die Integration von Dapr

Dieses Thema enthält Informationen zu den API Management-Richtlinien für die Integration von Dapr. Dapr ist eine portierbare Runtime zur Erstellung zustandsloser und zustandsbehafteter, auf Microservices basierender Anwendungen mit jeder Sprache bzw. jedem Framework. Sie stellt die gängigen Microservice-Muster wie Dienstermittlung und -aufruf mit integrierter Retry-Logik, Veröffentlichen und Abonnieren mit At-Least-Once-Übermittlungssemantik oder steckbare Bindungsressourcen als Code bereit, um die Komposition mithilfe externer Dienste zu erleichtern. Ausführliche Informationen und Anleitungen für den Einstieg in Dapr finden Sie unter [dapr.io](https://dapr.io). Weitere Informationen zum Hinzufügen und Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](api-management-howto-policies.md).

> [!CAUTION]
> Die Richtlinien, auf die in diesem Artikel verwiesen wird, befinden sich in der öffentlichen Vorschau und unterliegen den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!IMPORTANT]
> Die Richtlinien, auf die in diesem Artikel verwiesen wird, funktionieren nur in der [selbstgehosteten Version des API Management-Gateways](self-hosted-gateway-overview.md) mit aktivierter Dapr-Unterstützung.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Aktivieren der Dapr-Unterstützung im selbstgehosteten Gateway

Um die Dapr-Unterstützung im selbstgehosteten Gateway zu aktivieren, fügen Sie die folgenden [Dapr-Anmerkungen](https://github.com/dapr/docs/blob/master/README.md) zur [Kubernetes-Bereitstellungsvorlage](how-to-deploy-self-hosted-gateway-kubernetes.md) hinzu, wobei Sie „app-name“ durch den gewünschten Namen ersetzen. Eine ausführliche exemplarische Vorgehensweise zum Einrichten und Verwenden von API Management mit Dapr ist [hier](https://aka.ms/apim/dapr/walkthru) verfügbar.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Distributed Application Runtime-Integrationsrichtlinien (Dapr)

-  [Anforderung an einen Dienst senden](api-management-dapr-policies.md#invoke): Verwendet eine Dapr-Runtime für die Lokalisierung eines Dapr-Microservice und die zuverlässige Kommunikation mit ihm. Weitere Informationen zum Dienstaufruf in Dapr enthält die Beschreibung in dieser [INFO](https://github.com/dapr/docs/blob/master/README.md#service-invocation)-Datei.
-  [Nachricht an Pub/Sub-Thema senden](api-management-dapr-policies.md#pubsub): Verwendet die Dapr-Runtime, um eine Nachricht in einem Veröffentlichungs-/Abonnementthema zu veröffentlichen. Weitere Informationen zu Veröffentlichen/Abonnieren-Nachrichten in Dapr enthält die Beschreibung in dieser [INFO](https://github.com/dapr/docs/blob/master/README.md)-Datei.
-  [Ausgabebindung auslösen](api-management-dapr-policies.md#bind): Verwendet eine Dapr-Runtime zum Aufrufen eines externen Systems über eine Ausgabebindung. Weitere Informationen zu Bindungen in Dapr enthält die Beschreibung in dieser [INFO](https://github.com/dapr/docs/blob/master/README.md)-Datei.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Anforderung an einen Dienst senden

Diese Richtlinie legt die Ziel-URL für die aktuelle Anforderung auf `http://localhost:3500/v1.0/invoke/{app-id}/method/{method-name}` fest und ersetzt dabei Vorlagenparameter durch Werte, die in der Richtlinienanweisung angegeben sind.

Die Richtlinie geht davon aus, dass Dapr in einem Sidecar-Container in demselben Pod wie das Gateway ausgeführt wird. Beim Empfangen der Anforderung führt die Dapr-Runtime die Dienstermittlung und den eigentlichen Aufruf aus, einschließlich einer etwaigen Protokollübersetzung zwischen HTTP und gRPC, Wiederholungsversuchen, verteilter Ablaufverfolgung und Fehlerbehandlung.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" />
```

### <a name="examples"></a>Beispiele

#### <a name="example"></a>Beispiel

Im folgenden Beispiel wird das Aufrufen der Methode mit dem Namen „back“ für den Microservice mit dem Namen „echo“ gezeigt. Die Richtlinie `set-backend-service` legt die Ziel-URL fest. Die Richtlinie `forward-request` versendet die Anforderung an die Dapr-Runtime, die sie an den Microservice übermittelt.

Die Richtlinie `forward-request` wird hier der besseren Verständlichkeit halber gezeigt. Die Richtlinie wird in der Regel über das Schlüsselwort `base` aus dem globalen Bereich „geerbt“.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemente

| Element             | Beschreibung  | Erforderlich |
|---------------------|--------------|----------|
| set-backend-service | Stammelement | Ja      |

### <a name="attributes"></a>Attributes

| attribute        | Beschreibung                     | Erforderlich | Standard |
|------------------|---------------------------------|----------|---------|
| backend-id       | Muss auf „dapr“ festgelegt werden           | Ja      | N/V     |
| dapr-app-id      | Der Name des Ziel-Microservice. Wird dem Parameter [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) Parameter in Dapr zugeordnet.| Ja | N/V |
| dapr-method      | Der Name der Methode oder eine URL, die auf dem Ziel-Microservice aufgerufen werden soll. Wird dem Parameter [method-name](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) in Dapr zugeordnet.| Ja | – |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte** : inbound
- **Richtlinienbereiche:** alle Bereiche

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Nachricht an Pub/Sub-Thema senden

Diese Richtlinie weist das API Management-Gateway an, eine Nachricht an ein Veröffentlichen/Abonnieren-Thema von Dapr zu senden. Die Richtlinie erreicht dies durch eine HTTP POST-Anforderung an `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}`, bei der Vorlagenparameter ersetzt werden und in der Richtlinienanweisung angegebener Inhalt hinzugefügt wird.

Die Richtlinie geht davon aus, dass die Dapr-Runtime in einem Sidecar-Container in demselben Pod wie das Gateway ausgeführt wird. Die Dapr-Runtime implementiert die Pub/Sub-Semantik.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic=”topic-name” ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid” content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Beispiele

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt, wie der Text der aktuellen Anforderung an das [Thema](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) „new“ der Pub/Sub-[Komponente](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) „orders“ gesendet wird. Die von der Dapr-Runtime empfangene Antwort wird im Eintrag „dapr-response“ der Sammlung „Variables“ im Objekt [context](api-management-policy-expressions.md#ContextVariables) gespeichert.

Wenn die Dapr-Runtime beispielsweise das Zielthema nicht finden kann und mit einem Fehler antwortet, wird der Abschnitt „on-error“ ausgelöst. Die von der Dapr-Runtime empfangene Antwort wird wörtlich an die aufrufende Funktion zurückgegeben. Andernfalls wird die Standardantwort `200 OK` zurückgegeben.

Der Abschnitt „backend“ ist leer, und die Anforderung wird nicht an das Back-End weitergeleitet.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemente

| Element             | Beschreibung  | Erforderlich |
|---------------------|--------------|----------|
| publish-to-dapr     | Stammelement | Ja      |

### <a name="attributes"></a>Attributes

| attribute        | Beschreibung                     | Erforderlich | Standard |
|------------------|---------------------------------|----------|---------|
| pubsub-name      | Der Name der PubSub-Zielkomponente. Wird dem Parameter [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) in Dapr zugeordnet. Falls nicht vorhanden, muss der Attributwert __Thema__ die Form `pubsub-name/topic-name` haben.    | Nein       | Keine    |
| topic            | Der Name des Themas. Wird dem Parameter [topic](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) in Dapr zugeordnet.               | Ja      | –     |
| ignore-error     | Wenn dieses Attribut auf `true` festgelegt ist, wird die Richtlinie angewiesen, nicht den Abschnitt [„on-error“](api-management-error-handling-policies.md) auszulösen, wenn ein Fehler von der Dapr-Runtime empfangen wird | Nein | `false` |
| response-variable-name | Name des Eintrags in der Sammlung [Variables](api-management-policy-expressions.md#ContextVariables), der zum Speichern der Antwort von der Dapr-Runtime verwendet werden soll | Nein | Keine |
| timeout | Zeit (in Sekunden), wie lange auf die Antwort der Dapr-Runtime gewartet werden soll. Der Wert kann im Bereich von 1 bis 240 Sekunden liegen. | Nein | 5 |
| Vorlage | Vorlagen-Engine, das für das Transformieren des Nachrichteninhalts verwendet werden soll. „Liquid“ ist der einzige unterstützte Wert. | Nein | Keine |
| content-type | Art des Nachrichteninhalts. „application/json“ ist der einzige unterstützte Wert. | Nein | Keine |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** inbound, outbound, on-error
- **Richtlinienbereiche:** alle Bereiche

## <a name="trigger-output-binding"></a><a name="bind"></a> Ausgabebindung auslösen

Diese Richtlinie weist das API Management-Gateway an, eine ausgehende Dapr-[Bindung](https://github.com/dapr/docs/blob/master/README.md) auszulösen. Die Richtlinie erreicht dies durch eine HTTP POST-Anforderung an `http://localhost:3500/v1.0/bindings/{{bind-name}}`, bei der Vorlagenparameter ersetzt werden und in der Richtlinienanweisung angegebener Inhalt hinzugefügt wird.

Die Richtlinie geht davon aus, dass die Dapr-Runtime in einem Sidecar-Container in demselben Pod wie das Gateway ausgeführt wird. Die Dapr-Runtime ist dafür verantwortlich, die externe Ressource aufzurufen, die durch die Bindung dargestellt wird.

### <a name="policy-statement"></a>Richtlinienanweisung

```xml
<invoke-dapr-binding name=”bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template=”Liquid content-type="application/json">
    <metadata>
        <item key=”item-name”><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Beispiele

#### <a name="example"></a>Beispiel

Das folgende Beispiel zeigt das Auslösen einer ausgehenden Bindung namens „external-systems“ mit dem Vorgangsnamen „create“, Metadaten, die aus zwei Schlüssel-Wert-Elementen namens „source“ und „client-ip“ bestehen, sowie den Text, der der ursprünglichen Anforderung entstammt. Die von der Dapr-Runtime empfangene Antwort wird im Eintrag „bind-response“ der Sammlung „Variables“ im Objekt [context](api-management-policy-expressions.md#ContextVariables) erfasst.

Wenn die Dapr-Runtime aus einem beliebigen Grund fehlschlägt und mit einem Fehler antwortet, wird der Abschnitt „on-error“ ausgelöst, und die von der Dapr-Runtime erhaltene Antwort wird wörtlich an die aufrufende Funktion zurückgegeben. Andernfalls wird die Standardantwort `200 OK` zurückgegeben.

Der Abschnitt „backend“ ist leer, und die Anforderung wird nicht an das Back-End weitergeleitet.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elemente

| Element             | Beschreibung  | Erforderlich |
|---------------------|--------------|----------|
| invoke-dapr-binding | Stammelement | Ja      |
| metadata            | Bindung spezifischer Metadaten in Form von Schlüssel-Wert-Paaren. Der Eigenschaft [metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) in Dapr zugeordnet. | Nein |
| Daten            | Inhalt der Nachricht. Der Eigenschaft [data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) in Dapr zugeordnet. | Nein |


### <a name="attributes"></a>Attributes

| attribute        | Beschreibung                     | Erforderlich | Standard |
|------------------|---------------------------------|----------|---------|
| name            | Name der Zielbindung. Muss dem Namen der Bindungen entsprechen, die in Dapr [definiert](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) sind.           | Ja      | N/V     |
| operation       | Name des Zielvorgangs (bindungsspezifisch). Der Eigenschaft [operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) in Dapr zugeordnet. | Nein | Keine |
| ignore-error     | Wenn dieses Attribut auf `true` festgelegt ist, wird die Richtlinie angewiesen, nicht den Abschnitt [„on-error“](api-management-error-handling-policies.md) auszulösen, wenn ein Fehler von der Dapr-Runtime empfangen wird | Nein | `false` |
| response-variable-name | Name des Eintrags in der Sammlung [Variables](api-management-policy-expressions.md#ContextVariables), der zum Speichern der Antwort von der Dapr-Runtime verwendet werden soll | Nein | Keine |
| timeout | Zeit (in Sekunden), wie lange auf die Antwort der Dapr-Runtime gewartet werden soll. Der Wert kann im Bereich von 1 bis 240 Sekunden liegen. | Nein | 5 |
| Vorlage | Vorlagen-Engine, das für das Transformieren des Nachrichteninhalts verwendet werden soll. „Liquid“ ist der einzige unterstützte Wert. | Nein | Keine |
| content-type | Art des Nachrichteninhalts. „application/json“ ist der einzige unterstützte Wert. | Nein | Keine |

### <a name="usage"></a>Verwendung

Diese Richtlinie kann in den folgenden [Abschnitten](./api-management-howto-policies.md#sections) und [Bereichen](./api-management-howto-policies.md#scopes) von Richtlinien verwendet werden.

- **Richtlinienabschnitte:** inbound, outbound, on-error
- **Richtlinienbereiche:** alle Bereiche
