---
title: Diagnose beim Azure Service Fabric-Reverseproxy | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Anforderungsverarbeitung beim Reverseproxy überwachen und diagnostizieren.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: c9c8c649208cff95f4ee515d39cc8cca3e2c64bf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60726841"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Überwachen und Diagnostizieren der Anforderungsverarbeitung beim Reverseproxy

Ab Version 5.7 von Service Fabric können Reverseproxyereignisse gesammelt werden. Die Ereignisse sind in zwei Kanälen verfügbar, einem nur mit Fehlerereignissen im Zusammenhang mit der Anforderungsverarbeitung beim Reverseproxy und einem zweiten mit ausführlichen Ereignissen mit Einträgen für erfolgreiche und fehlerhafte Anforderungen.

Informationen zum Aktivieren der Ereignissammlung von diesen Kanälen in lokalen und Azure Service Fabric-Clustern finden Sie unter [Sammeln von Reverseproxyereignissen](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

## <a name="troubleshoot-using-diagnostics-logs"></a>Problembehandlung mithilfe der Diagnoseprotokolle
Hier sind einige Beispiele zum Interpretieren der allgemeinen Fehlerprotokolle, die auftreten können:

1. Reverseproxy gibt Antwortstatuscode 504 (Timeout) zurück.

    Ein möglicher Grund hierfür ist, dass der Dienst nicht innerhalb des Zeitlimits für die Anforderung antwortet.
   Im ersten Ereignis unten werden die Details der empfangenen Anforderung beim Reverseproxy protokolliert. 
   Das zweite Ereignis gibt an, dass beim Weiterleiten der Anforderung an den Dienst ein Fehler aufgetreten ist: „Interner Fehler = ERROR_WINHTTP_TIMEOUT“. 

    Die Nutzlast beinhaltet:

   * **traceId**: Mit dieser GUID können alle Ereignisse für eine einzelne Anforderung korreliert werden. In den folgenden zwei Ereignissen ist traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271**. Dies zeigt an, das sie zu derselben Anforderung gehören.
   * **requestUrl**: die URL (Reverseproxy-URL), an die die Anforderung gesendet wurde.
   * **verb**: HTTP-Verb.
   * **remoteAddress**: Adresse des Clients, der die Anforderung gesendet hat.
   * **resolvedServiceUrl**: Dienstendpunkt-URL, in die die eingehende Anforderung aufgelöst wurde. 
   * **errorDetails**: weitere Informationen über den Fehler.

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. Reverseproxy gibt Antwortstatuscode 404 (Nicht gefunden) zurück. 
    
    Im Folgenden finden Sie ein Beispielereignis, bei dem der Reverseproxy 404 zurückgibt, da er den passenden Dienstendpunkt nicht finden konnte.
    Die interessanten Nutzlasteinträge sind in diesem Fall:
   * **processRequestPhase**: Gibt die Phase während der Anforderungsverarbeitung an, in der der Fehler aufgetreten ist: ***TryGetEndpoint***, d.h. beim Versuch, den Dienstendpunkt für die Weiterleitung abzurufen. 
   * **errorDetails**: Listet die Endpunktsuchkriterien auf. Hier sehen Sie, dass der angegebene listenerName = **FrontEndListener** war, während der Replikatendpunkt nur einen Listener mit dem Namen **OldListener** enthält.
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     Ein weiteres Beispiel, in dem der Reverseproxy den Fehler „404 (Nicht gefunden)“ zurückgeben kann, sieht wie folgt aus: Der „ApplicationGateway\Http“-Konfigurationsparameter **SecureOnlyMode** ist auf „true“ festgelegt, wobei der Reverseproxy auf **HTTPS** lauscht, aber alle Replikatendpunkte sind unsicher (lauschen auf „HTTP“).
     Der Reverseproxy gibt 404 zurück, da er keinen Endpunkt finden kann, der auf „HTTPS“ lauscht und an den die Anforderung weitergeleitet werden kann. Durch Analysieren der Parameter in der Ereignisnutzlast kann das Problem weiter eingegrenzt werden:
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. Bei der Anforderung an den Reverseproxy tritt ein Timeoutfehler auf. 
    Die Ereignisprotokolle enthalten ein Ereignis mit den Details zur empfangenen Anforderung (hier nicht dargestellt).
    Das nächste Ereignis zeigt, dass der Dienst mit Statuscode 404 geantwortet hat, und der Reverseproxy initiiert eine erneute Auflösung. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Beim Sammeln aller Ereignisse sehen Sie eine Ereigniskette jedes Versuchs der Auflösung und Weiterleitung.
    Das letzte Ereignis in der Reihe zeigt, dass die Anforderungsverarbeitung zu einem Timeoutfehler führt. Außerdem wird die Anzahl der erfolgreichen Auflösungsversuche angegeben.
    
    > [!NOTE]
    > Es wird empfohlen, die Ereignissammlung über den ausführlichen Kanal standardmäßig deaktiviert zu lassen und sie bei Bedarf für die Problembehandlung zu aktivieren.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Wenn die Sammlung nur für kritische Ereignisse/Fehlerereignisse aktiviert ist, sehen Sie ein Ereignis mit den Details zum Timeout und der Anzahl der Auflösungsversuche. 
    
    Bei Diensten, die einen Statuscode 404 zurück an den Benutzer senden, sollte der Header „X-ServiceFabric“ in der Antwort hinzugefügt werden. Nach dem Hinzufügen des Headers zur Antwort leitet der Reverseproxy den Statuscode weiter an den Client.  

4. Fälle, in denen der Client die Anforderung getrennt hat.

    Das folgende Ereignis wird aufgezeichnet, wenn der Reverseproxy die Antwort an den Client weiterleitet, der Client jedoch die Verbindung trennt:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. Reverseproxy gibt „404 FABRIC_E_SERVICE_DOES_NOT_EXIST“ zurück

    Der Fehler „FABRIC_E_SERVICE_DOES_NOT_EXIST“ wird zurückgegeben, wenn das URI-Schema für den Dienstendpunkt im Dienstmanifest nicht angegeben ist.

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    Geben Sie zum Beheben des Problems das URI-Schema im Manifest an.
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> Ereignisse im Zusammenhang mit der Verarbeitung von Websocketanforderungen werden derzeit nicht protokolliert. Diese Funktion wird in der nächsten Version hinzugefügt.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Aktivieren der Protokollsammlung in Azure-Clustern unter [Ereignisaggregation und Sammlung mit Microsoft Azure-Diagnose](service-fabric-diagnostics-event-aggregation-wad.md).
* Anzeigen von Service Fabric-Ereignisse in Visual Studio unter [Lokale Überwachung und Diagnose](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Beispiele für Azure Resource Manager-Vorlagen zum Konfigurieren eines sicheren Reverseproxys mit den unterschiedlichen Optionen für die Dienstzertifikatüberprüfung finden Sie unter [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Konfigurieren des Reverseproxys für die Verbindung mit sicheren Diensten).
* Weitere Informationen erhalten Sie unter [Service Fabric-Reverseproxy](service-fabric-reverseproxy.md).
