---
title: Wiederholungslogik im Media Services SDK für .NET | Microsoft Docs
description: Dieses Thema enthält eine Übersicht über die Wiederholungslogik im Media Services SDK für .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61094644"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Wiederholungslogik im Media Services SDK für .NET  

Bei der Arbeit mit Microsoft Azure-Diensten können vorübergehende Fehler auftreten. Wenn ein vorübergehender Fehler auftritt, ist der Vorgang in der Regel nach ein paar Wiederholungsversuchen erfolgreich. Das Media Services SDK für .NET implementiert die Wiederholungslogik zum Behandeln von vorübergehenden Fehlern, die mit Ausnahmen und Fehlern in Verbindung stehen, die durch Webanforderungen, das Ausführen von Abfragen, das Speichern von Änderungen und Speichervorgänge hervorgerufen werden.  Standardmäßig führt das Media Services SDK für .NET vier Wiederholungsversuche aus, bevor es die Ausnahme zu Ihrer Anwendung erneut auslöst. Der Code in Ihrer Anwendung muss diese Ausnahme dann ordnungsgemäß behandeln.  

 Im folgenden finden Sie einen kurzen Leitfaden zu den Richtlinien für die Webanforderung, den Speicher, die Abfrage und SaveChange:  

* Die Speicherrichtlinie wird fürBlob Storage-Vorgänge verwendet (Uploads oder Downloads von Medienobjektdateien).  
* Die Webanforderungsrichtlinie wird für allgemeine Webanforderungen verwendet (z.B. zum Erhalt eines Authentifizierungstokens und zum Auflösen des Clusterendpunkts der Benutzer).  
* Die Abfragerichtlinie wird zum Abfragen von Entitäten von REST verwendet (z.B. mediaContext.Assets.Where(…)).  
* Die SaveChanges-Richtlinie wird für alle Vorgänge verwendet, die Daten innerhalb des Diensts ändern (z.B. Erstellen einer Entität, die eine Entität aktualisiert, Aufrufen einer Dienstfunktion für einen Vorgang).  
  
  In diesem Thema werden Ausnahmetypen und Fehlercodes aufgelistet, die vom Media Services SDK für die .NET-Wiederholungslogik verarbeitet werden.  

## <a name="exception-types"></a>Ausnahmetypen
Die folgende Tabelle beschreibt die Ausnahmen, die das Media Services SDK für .NET verarbeitet oder für einige Vorgänge nicht verarbeitet, die möglicherweise vorübergehende Fehler auslösen könnten.  

| Ausnahme | Webanforderung | Storage | Abfragen | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Weitere Informationen finden Sie im Abschnitt [WebException-Statuscodes](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus). |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Weitere Informationen finden Sie unter [HTTP-Fehlerstatuscodes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nein |Ja |Ja |Ja |
| DataServiceQueryException<br/> Weitere Informationen finden Sie unter [HTTP-Fehlerstatuscodes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nein |Ja |Ja |Ja |
| DataServiceRequestException<br/> Weitere Informationen finden Sie unter [HTTP-Fehlerstatuscodes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nein |Ja |Ja |Ja |
| DataServiceTransportException |Nein |Nein |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Nein |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Nein |Ja |Nein |Nein |
| IOException |Nein |Ja |Nein |Nein |

### <a name="WebExceptionStatus"></a> WebException status codes
Die folgende Tabelle zeigt, für welche WebException-Fehlercodes die Wiederholungslogik implementiert ist. Die [WebExceptionStatus](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx)-Enumeration definiert die Statuscodes.  

| Status | Webanforderung | Storage | Abfragen | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NameResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| SendFailure |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Nein |
| ConnectionClosed |Ja |Ja |Ja |Nein |
| KeepAliveFailure |Ja |Ja |Ja |Nein |
| UnknownError |Ja |Ja |Ja |Nein |
| ReceiveFailure |Ja |Ja |Ja |Nein |
| RequestCanceled |Ja |Ja |Ja |Nein |
| Timeout |Ja |Ja |Ja |Nein |
| ProtocolError <br/>Die Wiederholung bei ProtocolError wird von der HTTP-Statuscodebehandlung gesteuert. Weitere Informationen finden Sie unter [HTTP-Fehlerstatuscodes](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ja |Ja |Ja |Ja |

### <a name="HTTPStatusCode"></a> HTTP-Fehlerstatuscodes
Wenn Abfrage- und SaveChanges-Vorgänge DataServiceClientException, DataServiceQueryException, oder DataServiceQueryException ausgeben, wird der HTTP-Fehlerstatuscode in der StatusCode-Eigenschaft zurückgegeben.  Die folgende Tabelle zeigt, für welche Fehlercodes die Wiederholungslogik implementiert ist.  

| Status | Webanforderung | Storage | Abfragen | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nein |Ja |Nein |Nein |
| 403 |Nein |Ja<br/>Behandeln von Wiederholungen mit längeren Wartezeiten. |Nein |Nein |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Nein |
| 502 |Ja |Ja |Ja |Nein |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Nein |

Wenn Sie einen Blick auf die tatsächliche Implementierung s Media Services SDKs für die .NET-Wiederholungslogik werfen möchten, gehen Sie unter [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

