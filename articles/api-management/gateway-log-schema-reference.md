---
title: 'Referenz: Azure API Management-Ressourcenprotokoll'
description: Schemareferenz für das GatewayLogs-Ressourcenprotokoll von Azure API Management
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 064ac21e01239f7be3ddca9e48089ce880c6af58
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379901"
---
# <a name="reference-api-management-resource-log-schema"></a>Referenz: API Management-Ressourcenprotokollschema

Dieser Artikel enthält eine Schemareferenz für das GatewayLogs-Ressourcenprotokoll von Azure API Management. Protokolleinträge beinhalten auch Felder im [allgemeinen Schema der obersten Ebene](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema).

Informationen zum Aktivieren der Sammlung des Ressourcenprotokolls in API Management finden Sie unter [Überwachen von veröffentlichten APIs](api-management-howto-use-azure-monitor.md#resource-logs).

## <a name="gatewaylogs-schema"></a>GatewayLogs-Schema

Die folgenden Eigenschaften werden für jede API-Anforderung protokolliert:

| Eigenschaft  | type | BESCHREIBUNG |
| ------------- | ------------- | ------------- |
| method | Zeichenfolge | HTTP-Methode der eingehenden Anforderung |
| url | Zeichenfolge | URL der eingehenden Anforderung |
| responseCode | integer | Statuscode der an einen Client gesendeten HTTP-Antwort |
| responseSize | integer | Anzahl der Bytes, die während der Anforderungsverarbeitung an einen Client gesendet werden | 
| cache | Zeichenfolge | Status der API Management-Cachenutzung bei der Anforderungsverarbeitung (Treffer, Fehler, Keiner) | 
| apiId | Zeichenfolge | API-Entitätsbezeichner für die aktuelle Anforderung | 
| operationId | Zeichenfolge | Vorgangsentitätsbezeichner für die aktuelle Anforderung | 
| clientProtocol | Zeichenfolge | HTTP-Protokollversion der eingehenden Anforderung |
| clientTime | integer | Anzahl von Millisekunden für alle Client-E/A-Vorgänge (Verbindungsherstellung sowie Senden und Empfangen von Bytes) | 
| apiRevision | Zeichenfolge | API-Revision für die aktuelle Anforderung | 
| clientTlsVersion| Zeichenfolge | Verwendete TLS-Version des Clients, der die Anforderung sendet |
| lastError | Objekt | Details zum letzten Fehler bei der Anforderungsverarbeitung (im Falle einer nicht erfolgreichen Anforderung) | 
| backendMethod | Zeichenfolge | HTTP-Methode der an ein Back-End gesendeten Anforderung |
| backendUrl | Zeichenfolge | URL der an ein Back-End gesendeten Anforderung |
| backendResponseCode | integer | Code der von einem Back-End empfangenen HTTP-Antwort |
| backedProtocol | Zeichenfolge | HTTP-Protokollversion der an ein Back-End gesendeten Anforderung |
| backendTime | integer | Anzahl von Millisekunden für alle Back-End-E/A-Vorgänge (Verbindungsherstellung sowie Senden und Empfangen von Bytes) | 


## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Überwachung von APIs in API Management finden Sie unter [Überwachen von veröffentlichten APIs](api-management-howto-use-azure-monitor.md).
* Weitere Informationen zum allgemeinen und dienstspezifischen Schema für Azure-Ressourcenprotokolle finden Sie [hier](../azure-monitor/platform/resource-logs-schema.md).

