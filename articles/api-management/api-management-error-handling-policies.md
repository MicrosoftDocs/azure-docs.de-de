---
title: Fehlerbehandlung bei Azure API Management-Richtlinien | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie auf Fehlerbedingungen reagieren, die bei der Verarbeitung von Anforderungen in Azure API Management auftreten können.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 87693caa5343e359bb3ab424de489c2270bbca62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704437"
---
# <a name="error-handling-in-api-management-policies"></a>Fehlerbehandlung bei API Management-Richtlinien

Indem sie ein `ProxyError`-Objekt bereitstellen, können Herausgeber mit Azure API Management auf Fehlerbedingungen reagieren, die bei der Verarbeitung von Anforderungen im Proxy auftreten können. Der Zugriff auf das Objekt `ProxyError` erfolgt über die Eigenschaft [context.LastError](api-management-policy-expressions.md#ContextVariables) und kann von Richtlinien im Richtlinienabschnitt `on-error` verwendet werden. Dieser Artikel dient als Referenz für die Fehlerbehandlungsfunktionen in Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Fehlerbehandlung in API Management

 Die Richtlinien in Azure API Management sind in die Abschnitte `inbound`, `backend`, `outbound` und `on-error` unterteilt, wie im folgenden Beispiel gezeigt.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
Während der Verarbeitung einer Anforderung werden integrierte Schritte zusammen mit Richtlinien ausgeführt, die im Bereich der Anforderung liegen. Wenn ein Fehler auftritt, wechselt die Verarbeitung sofort zum Richtlinienabschnitt `on-error`.  
Der Richtlinienabschnitt `on-error` kann in jedem Bereich verwendet werden. Zudem können API-Herausgeber benutzerdefiniertes Verhalten konfigurieren, wie etwa das Protokollieren des Fehlers in Event Hubs oder das Erstellen einer neuen Antwort, die an den Aufrufer zurückgegeben werden soll.  
  
> [!NOTE]
>  Der Abschnitt `on-error` ist standardmäßig nicht in den Richtlinien vorhanden. Um einer Richtlinie den Abschnitt `on-error` hinzuzufügen, navigieren Sie im Richtlinien-Editor zur gewünschten Richtlinie, und fügen Sie sie hinzu. Weitere Informationen zum Konfigurieren von Richtlinien finden Sie unter [Richtlinien in API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Falls kein Abschnitt `on-error` vorhanden sein sollte, erhält der Aufrufer bei einer auftretenden Fehlerbedingung die HTTP-Antwortnachricht 400 oder 500.  
  
### <a name="policies-allowed-in-on-error"></a>Im On-error-Abschnitt zulässige Richtlinien

 Die folgenden Richtlinien können im Richtlinienabschnitt `on-error` verwendet werden.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>lastError

 Wenn ein Fehler auftritt und das Steuerelement zum Richtlinienabschnitt `on-error` wechselt, wird der Fehler in der Eigenschaft [context.LastError](api-management-policy-expressions.md#ContextVariables) gespeichert. Diese kann von Richtlinien im Abschnitt `on-error` aufgerufen werden. LastError weist folgende Eigenschaften auf.  
  
| NAME       | Type   | BESCHREIBUNG                                                                                               | Erforderlich |
|------------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| `Source`   | Zeichenfolge | Benennt das Element, in dem der Fehler aufgetreten ist. Kann entweder der Schrittname einer Richtlinie oder einer integrierten Pipeline sein.     | Ja      |
| `Reason`   | Zeichenfolge | Computerfreundlicher Fehlercode, der bei der Fehlerbehandlung verwendet werden kann.                                       | Nein       |
| `Message`  | Zeichenfolge | Für Menschen lesbare Fehlerbeschreibung.                                                                         | Ja      |
| `Scope`    | Zeichenfolge | Name des Bereichs, in dem der Fehler aufgetreten ist. Kann einer der Werte „global“, „product“, „api“ oder „operation“ sein. | Nein       |
| `Section`  | Zeichenfolge | Name des Abschnitts, in dem der Fehler aufgetreten ist. Mögliche Werte: „inbound“, „backend“, „outbound“ oder „on-error“.       | Nein       |
| `Path`     | Zeichenfolge | Gibt geschachtelte Richtlinien an, z.B. „choose[3]/when[2]“.                                                        | Nein       |
| `PolicyId` | Zeichenfolge | Sofern vom Kunden festgelegt, der Wert des Attributs `id` in der Richtlinie, in der der Fehler aufgetreten ist.             | Nein       |

> [!TIP]
> Über „context.Response.StatusCode“ können Sie auf den Statuscode zugreifen.  

> [!NOTE]
> Alle Richtlinien verfügen über ein optionales `id`-Attribut, das dem Stammelement der Richtlinie hinzugefügt werden kann. Wenn dieses Attribut beim Auftreten einer Fehlerbedingung in einer Richtlinie vorhanden ist, kann der Wert des Attributs mithilfe der Eigenschaft `context.LastError.PolicyId` abgerufen werden.

## <a name="predefined-errors-for-built-in-steps"></a>Vordefinierte Fehler für integrierte Schritte  
 Die folgenden Fehler sind für Fehlerbedingungen, die während der Auswertung der integrierten Verarbeitungsschritte auftreten können, vordefiniert.  
  
| `Source`        | Bedingung                                 | `Reason`                  | `Message`                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| Konfiguration | URI entspricht keiner API bzw. keinem Vorgang | OperationNotFound       | Es kann keine eingehende Anforderung für einen Vorgang gefunden werden.                                                                      |
| authorization | Abonnementschlüssel nicht bereitgestellt             | SubscriptionKeyNotFound | Der Zugriff wurde aufgrund eines fehlenden Abonnementschlüssels verweigert. Stellen Sie sicher, dass der Abonnementschlüssel beim Senden von Anforderungen an diese API enthalten ist. |
| authorization | Wert des Abonnementschlüssels ungültig         | SubscriptionKeyInvalid  | Der Zugriff wurde aufgrund eines ungültigen Abonnementschlüssels verweigert. Stellen Sie sicher, dass Sie einen gültigen Schlüssel für ein aktives Abonnement bereitstellen.            |
  
## <a name="predefined-errors-for-policies"></a>Vordefinierte Fehler für Richtlinien  
 Die folgenden Fehler sind für Fehlerbedingungen vordefiniert, die bei der Richtlinienauswertung auftreten können.  
  
| `Source`       | Bedingung                                                       | `Reason`                    | `Message`                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| rate-limit   | Übertragungsratenlimit überschritten                                             | RateLimitExceeded         | Das Übertragungsratenlimit wurde überschritten.                                                                                                               |
| quota        | Kontingent überschritten                                                  | QuotaExceeded             | Das Aufrufvolumenkontigent ist erschöpft. Das Kontingent wird in xx:xx:xx aufgefüllt. –oder– Das Bandbreitenkontingent ist erschöpft. Das Kontingent wird in xx:xx:xx aufgefüllt. |
| jsonp        | Wert des Rückrufparameters ungültig (enthält falsche Zeichen) | CallbackParameterInvalid  | Der Wert des Rückrufparameters {callback-parameter-name} ist kein gültiger JavaScript-Bezeichner.                                          |
| ip-filter    | Fehler beim Analysieren der IP-Adresse des Aufrufers aus Anforderung                          | FailedToParseCallerIP     | Fehler beim Ermitteln der IP-Adresse des Aufrufers. Der Zugriff wurde verweigert.                                                                        |
| ip-filter    | IP-Adresse des Aufrufers nicht in Liste zulässiger IP-Adressen                                | CallerIpNotAllowed        | Die IP-Adresse des Aufrufers {Ip-Adresse} ist nicht zulässig. Der Zugriff wurde verweigert.                                                                        |
| ip-filter    | IP-Adresse des Aufrufers in Liste blockierter IP-Adressen                                    | CallerIpBlocked           | Die IP-Adresse des Aufrufers ist blockiert. Der Zugriff wurde verweigert.                                                                                         |
| check-header | Erforderlicher Header nicht angegeben oder Wert fehlt               | HeaderNotFound            | Header {header-name} wurde nicht in der Anforderung gefunden. Der Zugriff wurde verweigert.                                                                    |
| check-header | Erforderlicher Header nicht angegeben oder Wert fehlt               | HeaderValueNotAllowed     | Der Wert {header-value} des Headers {header-name} ist nicht zulässig. Der Zugriff wurde verweigert.                                                          |
| validate-jwt | JWT fehlt in Anforderung                                 | TokenNotFound             | Der JSON Web Token (JWT) wurde nicht in der Anforderung gefunden. Der Zugriff wurde verweigert.                                                                                         |
| validate-jwt | Fehler bei der Signaturüberprüfung                                     | TokenSignatureInvalid     | <Meldung von JWT-Bibliothek\>. Der Zugriff wurde verweigert.                                                                                          |
| validate-jwt | Ungültige Zielgruppe                                                | TokenAudienceNotAllowed   | <Meldung von JWT-Bibliothek\>. Der Zugriff wurde verweigert.                                                                                          |
| validate-jwt | Ungültiger Aussteller                                                  | TokenIssuerNotAllowed     | <Meldung von JWT-Bibliothek\>. Der Zugriff wurde verweigert.                                                                                          |
| validate-jwt | Token abgelaufen                                                   | TokenExpired              | <Meldung von JWT-Bibliothek\>. Der Zugriff wurde verweigert.                                                                                          |
| validate-jwt | Signaturschlüssel wurde nicht von ID aufgelöst                            | TokenSignatureKeyNotFound | <Meldung von JWT-Bibliothek\>. Der Zugriff wurde verweigert.                                                                                          |
| validate-jwt | Erforderliche Ansprüche aus Token fehlen                          | TokenClaimNotFound        | Im JWT fehlen folgenden Ansprüche: <c1\>, <c2\>, ... Der Zugriff wurde verweigert.                                                            |
| validate-jwt | Konflikt bei Anspruchswerten                                           | TokenClaimValueNotAllowed | Der Wert {claim-value} des Anspruchs {claim-name} ist nicht zulässig. Der Zugriff wurde verweigert.                                                             |
| validate-jwt | Andere Überprüfungsfehler                                       | JwtInvalid                | <Meldung von JWT-Bibliothek\>.                                                                                                          |

## <a name="example"></a>Beispiel

Das Festlegen einer API-Richtlinie auf:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

und das Senden einer nicht autorisierten Anforderung führt zu der folgenden Antwort:

![Fehlerantwort „Nicht autorisiert“](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Richtlinien finden Sie unter:

+ [Richtlinien in Azure API Management](api-management-howto-policies.md)
+ [Transform and protect your API](transform-api.md) (Transformieren und Schützen von APIs)
+ Unter [Richtlinien für die API-Verwaltung](api-management-policy-reference.md) finden Sie eine komplette Liste der Richtlinienanweisungen und der zugehörigen Einstellungen.
+ [API Management policy samples](policy-samples.md) (API Management-Richtlinienbeispiele)   
