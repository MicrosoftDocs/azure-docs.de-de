---
title: Azure Relay-Porteinstellungen | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu Azure Relay-Portwerten.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2018
ms.author: spelluru
ms.openlocfilehash: 9d11179a8518ebf48f68f8607f94e0253d4edb80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789925"
---
# <a name="azure-relay-port-settings"></a>Azure Relay-Porteinstellungen

Die folgende Tabelle beschreibt die erforderliche Konfiguration für Portwerte für Azure Relay.

## <a name="hybrid-connections"></a>Hybridverbindungen

Hybrid Connections verwendet WebSockets an Port 443 mit SSL als zugrunde liegenden Transportmechanismus, der ausschließlich **HTTPS** verwendet. 

## <a name="wcf-relays"></a>WCF-Relays
  
|Bindung|Transportsicherheit|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (Client)|Ja|HTTPS| 
|" |Nein|HTTP|  
|[BasicHttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (Dienste)|Sie können das|9351/HTTP|  
|[NetEventRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (Client)|Ja|9351/HTTPS|  
|" |Nein|9350/HTTP|  
|[NetEventRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (Dienst)|Sie können das|9351/HTTP|  
|[NetTcpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (Client/Dienst)|Sie können das|5671/9352/HTTP (9352/9353, wenn eine Hybridverbindung verwendet)|  
|[NetOnewayRelayBinding Class](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (Client)|Ja|9351/HTTPS|  
|" |Nein|9350/HTTP|  
|[NetOnewayRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (Dienst)|Sie können das|9351/HTTP|  
|[WebHttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (Client)|Ja|HTTPS|  
|" |Nein|HTTP|  
|[WebHttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (Client)|Sie können das|9351/HTTP|  
|[WS2007HttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (Client)|Ja|HTTPS|  
|" |Nein|HTTP|  
|[WS2007HttpRelayBinding-Klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (Dienst)|Sie können das|9351/HTTP|

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Azure Relay finden Sie unter diesen Links:
* [Was ist Azure Relay?](relay-what-is-it.md)
* [Relay – Häufig gestellte Fragen](relay-faq.md)