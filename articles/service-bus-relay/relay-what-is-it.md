---
title: Was ist Azure Relay? | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Übersicht über den Azure Relay-Dienst. Mit diesem Dienst können Sie Cloudanwendungen entwickeln, die in Ihrem Unternehmensnetzwerk ausgeführte lokale Dienste nutzen, ohne eine Firewallverbindung zu öffnen oder grundlegende Änderungen an Ihrer Netzwerkinfrastruktur vorzunehmen.
services: service-bus-relay
author: spelluru
manager: ''
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: 47fbce7ea26bcb7224fe2624d593d85cd178d610
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60420305"
---
# <a name="what-is-azure-relay"></a>Was ist Azure Relay?
Mit dem Azure Relay-Dienst können Sie Dienste, die in Ihrem Unternehmensnetzwerk ausgeführt werden, auf sichere Weise in der öffentlichen Cloud verfügbar machen. Dazu müssen Sie weder einen Port in Ihrer Firewall öffnen noch grundlegende Änderungen an der Netzwerkinfrastruktur Ihres Unternehmens vornehmen. 

Der Relaydienst unterstützt die folgenden Szenarien zwischen lokalen Diensten und Anwendungen, die in der Cloud oder einer anderen lokalen Umgebung ausgeführt werden. 

- Herkömmliche unidirektionale Peer-zu-Peer-Kommunikation vom Typ „Anforderung/Antwort“ 
- Ereignisverteilung auf Internetebene für Veröffentlichen/Abonnieren-Szenarien 
- Bidirektionale und ungepufferte Socketkommunikation über Netzwerkgrenzen hinweg

Azure Relay unterscheidet sich von Technologie für die Integration auf Netzwerkebene, z.B. VPN. Für ein Azure-Relay kann als Bereich ein einzelner Anwendungsendpunkt auf einem einzelnen Computer festgelegt werden. Die VPN-Technologie hat deutlich größere Auswirkungen, da eine Änderung der Netzwerkumgebung erforderlich ist. 

## <a name="basic-flow"></a>Grundlegender Ablauf
Die grundlegenden Schritte des Datenübertragungsmusters mit Relay sind:

1. Ein lokaler Dienst stellt über einen ausgehenden Port eine Verbindung mit dem Relaydienst her. 
2. Es wird ein bidirektionaler Socket für die Kommunikation über eine bestimmte Adresse erstellt. 
3. Der Client kann mit dem lokalen Dienst dann durch das Senden von Datenverkehr an den Relaydienst unter Verwendung dieser Adresse kommunizieren. 
4. Der Relaydienst *vermittelt* anschließend Daten an den lokalen Dienst über den bidirektionalen Socket, der für den Client festgelegt ist. Der Client benötigt keine direkte Verbindung mit dem lokalen Dienst. Er muss den Standort des Diensts nicht kennen. Außerdem müssen für den lokalen Dienst auch keine eingehenden Ports in der Firewall geöffnet sein.


## <a name="features"></a>Features 
Azure Relay verfügt über zwei Funktionen:

- [Hybridverbindungen](#hybrid-connections): Verwenden die offenen Standardwebsockets, die plattformübergreifende Szenarios zulassen.
- WCF-Relays: Verwenden Windows Communication Foundation, um Remoteprozeduraufrufe zuzulassen. WCF-Relay ist das ältere Relayangebot, das viele Kunden bereits mit ihren WCF-Programmiermodellen verwenden.

## <a name="hybrid-connections"></a>Hybridverbindungen

Das Feature „Hybridverbindungen“ in Azure Relay ist eine sichere Weiterentwicklung des Relay-Features mit offenem Protokoll, das bereits vorhanden war. Sie können es auf jeder Plattform und mit jeder Sprache verwenden. Das Feature „Hybridverbindungen“ in Azure Relay basiert auf HTTP- und WebSockets-Protokollen. Sie können damit über WebSockets oder HTTP(S) Anforderungen senden und Antworten empfangen. Dieses Feature ist mit der WebSocket-API in gängigen Webbrowsern kompatibel. 

Ausführliche Informationen zum Hybridverbindungsprotokoll finden Sie im [Leitfaden zum Hybridverbindungsprotokoll](relay-hybrid-connections-protocol.md). Sie können Hybridverbindungen mit einer beliebigen WebSockets-Bibliothek für alle Runtimes bzw. Sprachen verwenden.

> [!NOTE]
> Hybridverbindungen von Azure Relay ersetzt das alte Feature für Hybridverbindungen von BizTalk Services. Das Feature „Hybridverbindungen“ in BizTalk Services basierte auf Azure Service Bus WCF Relay. Die Funktion „Hybridverbindungen“ in Azure Relay ist eine Ergänzung des bereits vorhandenen WCF Relay-Features. Diese beiden Dienstfunktionen (WCF Relay und Hybridverbindungen) sind im Azure Relay-Dienst parallel vorhanden. Sie teilen sich ein allgemeines Gateway, sind jedoch andererseits verschiedene Implementierungen.

## <a name="wcf-relay"></a>WCF-Relay
WCF Relay funktioniert mit dem vollständigen .NET Framework und für WCF. Sie erstellen eine Verbindung zwischen dem lokalen Dienst und dem Relaydienst mithilfe einer Sammlung von WCF-Bindungen vom Typ „Relay“. Die Relaybindungen werden neuen Transportbindungselementen zugeordnet, die entwickelt wurden, um WCF-Kanalkomponenten zu erstellen, die in Service Bus in der Cloud integriert werden. Weitere Informationen finden Sie unter [Verwenden von Azure Relay-WCF-Relays mit .NET](relay-wcf-dotnet-get-started.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Hybridverbindungen und WCF-Relay
Hybridverbindungen und WCF Relay aktivieren beide die sichere Verbindung mit Objekten, die innerhalb eines Unternehmensnetzwerks vorhanden sind. Welcher Ansatz verwendet wird, richtet sich nach den jeweiligen Anforderungen. Dies ist in der folgenden Tabelle beschrieben:

|  | WCF-Relay | Hybridverbindungen |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **Standardbasierte offene Protokolle** | |x |
| **RPC-Programmiermodelle** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architektur: Verarbeiten eingehender Relayanforderungen
Im folgenden Diagramm ist dargestellt, wie eingehende Relayanforderungen vom Azure Relay-Dienst verarbeitet werden:

![Verarbeiten eingehender WCF Relay-Anforderungen](./media/relay-what-is-it/ic690645.png)

1. Der lauschende Client sendet eine Lauschanforderung an den Azure Relay-Dienst. Der Azure Load Balancer leitet die Anforderung an einen der Gatewayknoten weiter. 
2. Der Azure Relay-Dienst erstellt im Gatewayspeicher ein Relayelement. 
3. Der sendende Client sendet eine Anforderung zum Herstellen einer Verbindung mit dem lauschenden Dienst. 
4. Das Gateway, das die Anforderung empfängt, sucht im Gatewayspeicher nach dem Relay. 
5. Das Gateway leitet die Verbindungsanforderung an das richtige Gateway, das im Gatewayspeicher angegeben ist. 
6. Das Gateway sendet eine Anforderung an den lauschenden Client, damit dieser einen temporären Kanal mit dem Gatewayknoten erstellt, der dem sendenden Client am nächsten liegt. 
7. Der lauschende Client erstellt einen temporären Kanal zu dem Gateway, das dem sendenden Client am nächsten liegt. Nachdem nun zwischen Clients eine Verbindung über ein Gateway besteht, können die Clients untereinander Nachrichten austauschen. 
8. Das Gateway leitet alle Nachrichten vom lauschenden Client an den sendenden Client weiter. 
9. Das Gateway leitet alle Nachrichten vom sendenden Client an den lauschenden Client weiter.  

## <a name="next-steps"></a>Nächste Schritte
* [Erste Schritte mit .NET-WebSockets](relay-hybrid-connections-dotnet-get-started.md)
* [Erste Schritte mit .NET-HTTP-Anforderungen](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Erste Schritte mit Node-WebSockets](relay-hybrid-connections-node-get-started.md)
* [Erste Schritte mit Node-HTTP-Anforderungen](relay-hybrid-connections-http-requests-node-get-started.md)
* [Relay – häufig gestellte Fragen](relay-faq.md)

