---
title: Protokollunterstützung für HTTP-Header in Azure Front Door Service | Microsoft-Dokumentation
description: Dieser Artikel beschreibt HTTP-Headerprotokolle, die Front Door Service unterstützt.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: 92e8435e4336c68982e4becc2a95f99b2c776c0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60736638"
---
# <a name="protocol-support-for-http-headers-in-azure-front-door-service"></a>Protokollunterstützung für HTTP-Header in Azure Front Door Service
Dieser Artikel beschreibt das Protokoll, das Front Door Service mit Teilen des Aufrufpfads unterstützt (siehe Abbildung). Die folgenden Abschnitte enthalten weitere Informationen zu HTTP-Headern, die von Front Door Service unterstützt werden.

![HTTP-Headerprotokoll von Azure Front Door Service][1]

>[!IMPORTANT]
>Front Door Service zertifiziert keine HTTP-Header, die hier nicht dokumentiert sind.

## <a name="client-to-front-door-service"></a>Client zu Front Door Service
Front Door Service akzeptiert die meisten Header aus der eingehenden Anforderung, ohne sie zu ändern. Einige reservierte Header werden aus der eingehenden Anforderung entfernt, falls gesendet, einschließlich der Header mit dem X-FD-*-Präfix.

## <a name="front-door-service-to-backend"></a>Front Door Service zu Back-End

Front Door Service enthält Header aus einer eingehenden Anforderung, sofern sie nicht aufgrund von Einschränkungen entfernt werden. Front Door fügt außerdem die folgenden Header hinzu:

| Header  | Beispiel und Beschreibung |
| ------------- | ------------- |
| Via |  Via: 1.1 Azure </br> Front Door fügt die HTTP-Version des Clients, gefolgt von *Azure*, als Wert für den Via-Header hinzu. Dies gibt die HTTP-Version des Clients an, und dass Front Door für die Anforderung zwischen dem Client und dem Back-End als Empfänger zwischengeschaltet war.  |
| X-Azure-ClientIP | X-Azure-ClientIP: 127.0.0.1 </br> Stellt die Client-IP-Adresse dar, die mit der zu verarbeitenden Anforderung verknüpft ist. Beispiel: Eine Anforderung von einem Proxy wird möglicherweise dem X-Forwarded-For-Header hinzugefügt, um die IP-Adresse des ursprünglichen Aufrufers anzugeben. |
| X-Azure-SocketIP |  X-Azure-SocketIP: 127.0.0.1 </br> Stellt die Socket-IP-Adresse dar, die mit der TCP-Verbindung verknüpft ist, von der die aktuelle Anforderung stammt. Die Client-IP-Adresse einer Anforderung entspricht möglicherweise nicht der Socket-IP-Adresse, da sie durch einen Benutzer willkürlich überschrieben werden kann.|
| X-Azure-Ref |  X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz </br> Dies ist eine eindeutige Referenzzeichenfolge, die eine von Front Door verarbeitete Anforderung identifiziert. Sie wird zum Durchsuchen von Zugriffsprotokollen verwendet und ist zur Problembehandlung wichtig.|
| X-Azure-RequestChain |  X-Azure-RequestChain: hops=1 </br> Diesen Header verwendet Front Door, um Anforderungsschleifen zu erkennen, und Benutzer sollten dafür keine Abhängigkeit erstellen. |
| X-Forwarded-For | X-Forwarded-For: 127.0.0.1 </br> Das HTTP-Headerfeld „X-Forwarded-For (XFF)“ identifiziert oft die Ursprungs-IP-Adresse eines Clients, der über einen HTTP-Proxy oder Lastverteiler eine Verbindung zu einem Webserver herstellt. Falls ein XFF-Header vorhanden ist, fügt Front Door die Socket-IP-Adresse des Clients daran an, oder fügt den XFF-Header mit der Socket-IP-Adresse des Clients hinzu. |
| X-Forwarded-Host | X-Forwarded-Host: contoso.azurefd.net </br> Das X-Forwarded-Host-HTTP-Headerfeld ist eine gängige Methode zum Identifizieren des ursprünglich vom Client im Host-HTTP-Anforderungsheader angeforderten Hosts. Dies liegt daran, dass der Hostname aus Front Door für den Back-End-Server, der die Anforderung verarbeitet, abweichen kann. |
| X-Forwarded-Proto | X-Forwarded-Proto: http </br> Das X-Forwarded-Proto-HTTP-Headerfeld wird häufig zum Identifizieren des Ursprungsprotokolls einer HTTP-Anforderung verwendet, da Front Door basierend auf der Konfiguration über HTTPS mit dem Back-End kommunizieren könnte. Dies gilt auch, wenn eine HTTP-Anforderung an den Reverseproxy gesendet wird. |

## <a name="front-door-service-to-client"></a>Front Door Service zu Client

Vom Back-End an Front Door gesendete Header werden auch an den Client weitergeleitet. Die folgenden Header werden von Front Door an Clients gesendet.

| Header  | Beispiel |
| ------------- | ------------- |
| X-Azure-Ref |  *X-Azure-Ref: 0zxV+XAAAAABKMMOjBv2NT4TY6SQVjC0zV1NURURHRTA2MTkANDM3YzgyY2QtMzYwYS00YTU0LTk0YzMtNWZmNzA3NjQ3Nzgz* </br> Dies ist eine eindeutige Referenzzeichenfolge, die eine von Front Door verarbeitete Anforderung identifiziert. Dies ist wichtig für die Problembehandlung, da sie verwendet wird, um Zugriffsprotokolle zu durchsuchen.|

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Azure Front Door Service-Instanz](quickstart-create-front-door.md)
- [Übersicht über die Routingarchitektur](front-door-routing-architecture.md)

<!--Image references-->
[1]: ./media/front-door-http-headers-protocol/front-door-protocol-summary.png