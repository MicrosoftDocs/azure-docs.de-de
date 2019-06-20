---
title: Benutzerdefiniertes Caching in Azure API Management
description: Erfahren Sie, wie Sie Elemente durch einen Schlüssel in Azure API Management zwischenspeichern.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780820"
---
# <a name="custom-caching-in-azure-api-management"></a>Benutzerdefiniertes Caching in Azure API Management
Der Azure API Management-Dienst verfügt über eine integrierte Unterstützung für das [HTTP-Antwort-Caching](api-management-howto-cache.md) mit der Ressourcen-URL als Schlüssel. Der Schlüssel kann durch Anforderungsheader mithilfe der `vary-by` -Eigenschaften geändert werden. Dies ist nützlich, wenn ganze HTTP-Antworten (auch als Darstellungen bezeichnet) zwischengespeichert werden sollen. Die neuen [cache-lookup-value](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) und [cache-store-value](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) bieten die Möglichkeit zum Speichern und Abrufen von zufälligen Datenelementen aus Richtliniendefinitionen. Die neuen Richtlinien [send-request](/azure/api-management/api-management-advanced-policies#SendRequest) -Richtlinie, da Sie nun Antworten von externen Diensten zwischenspeichern können.

## <a name="architecture"></a>Architecture
Der API Management-Dienst verwendet einen freigegebenen Einzelmandanten-Datencache, sodass Sie beim Skalieren auf mehrere Einheiten weiterhin Zugriff auf die gleichen zwischengespeicherten Daten erhalten. Wenn Sie jedoch mit einer Bereitstellung arbeiten, die mehrere Regionen umfasst, gibt es in allen Regionen unabhängige Caches. Es ist wichtig, den Cache nicht als Datenspeicher zu behandeln, der als einzige Quelle für einzelne Informationen fungiert. Wenn dies der Fall ist und Sie sich später dazu entschieden haben, die Vorteile der Bereitstellung in mehreren Regionen zu nutzen, könnten Kunden mit Benutzern, die in mehreren Regionen unterwegs sind, den Zugriff auf die zwischengespeicherten Daten verlieren.

## <a name="fragment-caching"></a>Fragment-Caching
Es gibt bestimmte Fälle, in denen die zurückgegebenen Antworten Daten enthalten, die teuer zu bestimmen sind und dennoch für einen angemessenen Zeitraum aktuell bleiben. Nehmen Sie beispielsweise einen Dienst, der von einer Fluggesellschaft erstellt wurde, um Informationen zu Flugreservierungen, dem Status von Flügen usw. bereitzustellen. Wenn der Benutzer am Punkteprogramm der Fluggesellschaft teilnimmt, sind zusätzlich dazu Informationen zum aktuellen Status und den gesammelten Meilen vorhanden. Diese benutzerbezogenen Informationen können in einem separaten System gespeichert werden; es kann aber auch wünschenswert sein, dass sie in den Antworten enthalten sind, die zum Flugstatus und den Reservierungen zurückgegeben werden. Dies kann durch einen Prozess namens Fragment-Caching erreicht werden. Die primäre Darstellung kann vom Ursprungsserver zurückgegeben werden, indem irgendein Token verwendet wird, das angibt, wo die benutzerbezogenen Informationen eingefügt werden sollen. 

Betrachten Sie die folgende JSON-Antwort aus einer Back-End-API:

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

Und die sekundäre Ressource unter `/userprofile/{userid}` , die folgendermaßen aussieht:

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Um die geeigneten Benutzerinformationen zu bestimmen, die eingeschlossen werden sollen, muss API Management zunächst den Endbenutzer identifizieren. Dieser Mechanismus hängt von der Implementierung ab. Als Beispiel verwende ich den `Subject`-Anspruch eines `JWT`-Tokens. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management speichert diesen `enduserid`-Wert zur späteren Verwendung in einer Kontextvariablen. Im nächsten Schritt wird festgestellt, ob eine vorherige Anforderung bereits die Benutzerinformationen abgerufen und im Cache gespeichert hat. Hierzu verwendet API Management die Richtlinie `cache-lookup-value`.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Wenn kein Eintrag im Cache vorhanden ist, der dem Schlüsselwert entspricht, wird keine `userprofile` -Kontextvariable erstellt. API Management überprüft den Erfolg der Suche mithilfe der Ablaufsteuerungsrichtlinie `choose`.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Wenn die Kontextvariable `userprofile` nicht vorhanden ist, muss API Management eine HTTP-Anforderung ausführen, um die Variable abzurufen.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management verwendet `enduserid`, um die URL in der Profilressource zu erstellen. Nachdem API Management die Antwort empfangen hat, wird der Textkörper aus der Antwort extrahiert und in einer Kontextvariablen gespeichert.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Um eine erneute HTTP-Anforderung durch API Management zu vermeiden, wenn derselbe Benutzer eine weitere Anforderung sendet, kann das Benutzerprofil im Cache gespeichert werden.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management speichert den Wert mit genau demselben Schlüssel im Cache, mit dem API Management ursprünglich versucht hat, ihn abzurufen. Die Dauer, die API Management zum Speichern des Werts auswählt, sollte darauf basieren, wie oft die Informationen geändert werden und wie tolerant Benutzer mit veralteten Informationen umgehen. 

Bitte beachten Sie, dass das Abrufen aus dem Cache noch immer eine prozessunabhängige Netzwerkanforderung ist, was die Dauer unter Umständen um Dutzende Millisekunden verlängert. Die Vorteile ergeben sich dann, wenn es im Vergleich dazu länger dauert, die Benutzerprofilinformationen zu bestimmen, wenn Datenbankabfragen oder aggregierte Informationen aus mehreren Back-Ends benötigt werden.

Als letzter Schritt im Prozess wird die zurückgegebene Antwort mit den Benutzerprofilinformationen aktualisiert.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Sie können die Anführungszeichen als Teil des Tokens hinzufügen, sodass die Antwort weiterhin ein gültiges JSON-Format besitzt, auch wenn sie nicht ersetzt werden muss.  

Nachdem Sie alle Schritte miteinander kombiniert haben, ist das Endergebnis eine Richtlinie, die wie folgt aussieht:

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Dieser Ansatz des Zwischenspeicherns wird hauptsächlich für Websites verwendet, bei denen HTML auf der Serverseite verwendet wird, sodass sie als einzelne Seite gerendert werden können. Er kann aber auch für APIs nützlich sein, bei denen Clients kein clientseitiges HTTP-Caching durchführen können oder es wünschenswert ist, diese Verantwortung nicht auf den Client zu übertragen.

Dieselbe Art von Fragment-Caching kann mithilfe eines Redis-Cachingservers auf den Back-End-Webservern durchgeführt werden. Allerdings ist es nützlich, für diesen Vorgang den API Management-Dienst zu nutzen, wenn die zwischengespeicherten Fragmente aus anderen Back-Ends als die primären Antworten stammen.

## <a name="transparent-versioning"></a>Transparente Versionsverwaltung
Es ist üblich, dass mehrere unterschiedliche Versionen von API-Implementierungen zu jedem Zeitpunkt unterstützt werden. Grund dafür kann zum Beispiel sein, dass verschiedene Umgebungen (z.B. Entwicklungs-, Test- oder Produktionsumgebung) unterstützt werden sollen, oder um ältere Versionen der API zu unterstützen, damit API-Benutzer Zeit zum Migrieren auf neuere Versionen haben. 

Statt die Cliententwickler dazu anzuhalten, die URLs von `/v1/customers` in `/v2/customers` zu ändern, besteht ein Ansatz darin, in den Benutzerprofildaten die aktuell verwendete API-Version zu speichern und die entsprechende Back-End-URL aufzurufen. Um die richtige Back-End-URL zu bestimmen, die einen bestimmten Client aufruft, müssen einige Konfigurationsdaten abgefragt werden. Durch Zwischenspeichern der Konfigurationsdaten kann API Management die durch diese Suche verursachten Leistungseinbußen minimieren.

Der erste Schritt besteht darin, den für die Konfiguration der gewünschten Version verwendeten Bezeichner zu bestimmen. In diesem Beispiel habe ich die Version dem Abonnement-Produktschlüssel zugeordnet. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Anschließend führt API Management eine Cachesuche durch, um festzustellen, ob die gewünschte Clientversion bereits abgerufen wurde.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Dann überprüft API Management, ob die gewünschte Version im Cache gefunden wurde.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Falls dem nicht so ist, ruft API Management die Clientversion ab.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Wir extrahieren den Text aus der Antwort.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Wie speichern sie für die zukünftige Verwendung im Cache.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Schließlich aktualisieren wir die Back-End-URL, sodass die Version des Diensts ausgewählt wird, die vom Client gewünscht ist.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Die vollständige Richtlinie sieht wie folgt aus:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Dass API-Benutzer transparent steuern können, auf welche Back-End-Version Clients zugreifen können, ohne Clients aktualisieren und erneut bereitstellen zu müssen, ist eine elegante Lösung, die viele Schwierigkeiten bei der API-Versionsverwaltung beseitigt.

## <a name="tenant-isolation"></a>Mandantenisolation
In größeren Bereitstellungen mit mehreren Mandanten erstellen einige Unternehmen separate Mandantengruppen auf unterschiedlichen Bereitstellungen von Back-End-Hardware. Dies minimiert die Anzahl der Kunden, die von Hardwareproblemen auf dem Back-End betroffen sind. Darüber hinaus können neue Softwareversionen phasenweise eingeführt werden. Im Idealfall sollte diese Back-End-Architektur für API-Benutzer transparent sein. Dies kann auf eine ähnliche Weise wie die transparente Versionsverwaltung geschehen, denn sie basiert auf der gleichen Technik der Bearbeitung der Back-End-URL mithilfe des Konfigurationsstatus pro API-Schlüssel.  

Anstatt eine bevorzugte Version der API für jeden Abonnement-Schlüssel zurückzugeben, geben Sie einen Bezeichner zurück, der einen Mandanten mit der zugewiesenen Hardwaregruppe verknüpft. Dieser Bezeichner kann zur Erstellung der entsprechenden Back-End-URL verwendet werden.

## <a name="summary"></a>Zusammenfassung
Die Freiheit, den Azure API Management-Cache zum Speichern aller Art von Daten zu verwenden, ermöglicht den effizienten Zugriff auf Konfigurationsdaten, was sich auf die Art und Weise auswirkt, wie eine eingehende Anforderung verarbeitet wird. Der Cache kann auch zum Speichern von Datenfragmenten genutzt werden, was die aus einer Back-End-API zurückgegebenen Antworten erweitern kann.
