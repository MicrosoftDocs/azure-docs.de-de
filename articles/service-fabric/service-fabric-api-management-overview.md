---
title: Azure Service Fabric mit Übersicht über API Management | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Einführung in die Verwendung von Azure API Management als Gateway für Ihre Service Fabric-Anwendungen.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 96176149-69bb-4b06-a72e-ebbfea84454b
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: vturecek
ms.openlocfilehash: 0dac2730bcc13b979de6a8faaaa53c0aaf15e902
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60621885"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Service Fabric mit Azure API Management-Übersicht

Cloudanwendungen benötigen normalerweise ein Front-End-Gateway, um für Benutzer, Geräte oder andere Anwendungen einen zentralen Eingangspunkt bereitzustellen. In Service Fabric kann ein Gateway ein beliebiger zustandsloser Dienst sein, z.B. eine [ASP.NET Core-Anwendung](service-fabric-reliable-services-communication-aspnetcore.md), oder ein anderer Dienst, der für den Eingang von Datenverkehr ausgelegt ist, z.B. [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), [IoT Hub](https://docs.microsoft.com/azure/iot-hub/) oder [Azure API Management](https://docs.microsoft.com/azure/api-management/).

Dieser Artikel enthält eine Einführung in die Verwendung von Azure API Management als Gateway für Ihre Service Fabric-Anwendungen. API Management ist direkt in Service Fabric integriert, sodass Sie APIs mit einem umfassenden Satz von Routingregeln für Ihre Service Fabric-Back-End-Dienste veröffentlichen können. 

## <a name="availability"></a>Verfügbarkeit

> [!IMPORTANT]
> Dieses Feature ist auf den Ebenen **Premium** und **Entwickler** von API Management aufgrund der erforderlichen Unterstützung virtueller Netzwerke verfügbar.

## <a name="architecture"></a>Architecture

Für eine allgemeine Service Fabric-Architektur wird eine einseitige Webanwendung verwendet, die HTTP-Aufrufe für Back-End-Dienste durchführt, von denen HTTP-APIs verfügbar gemacht werden. Unter [Service Fabric Getting Started Sample](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) (Service Fabric-Beispiel für die ersten Schritte) finden Sie ein Beispiel für diese Architektur.

In diesem Szenario dient ein zustandsloser Webdienst als Gateway für die Service Fabric-Anwendung. Bei diesem Ansatz ist es erforderlich, einen Webdienst zu schreiben, mit dem HTTP-Anforderungen über einen Proxy an Back-End-Dienste gesendet werden können. Dies ist im folgenden Diagramm dargestellt:

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-web-app-stateless-gateway]

Wenn die Komplexität von Anwendungen zunimmt, gilt dies auch für die Gateways, die eine API im Vordergrund einer Vielzahl von Back-End-Diensten darstellen müssen. Azure API Management ist so konzipiert, dass komplexe APIs mit Routingregeln, Zugriffssteuerung, Ratenbegrenzung, Überwachung, Ereignisprotokollierung und Zwischenspeicherung von Antworten mit minimalem Aufwand für Sie verarbeitet werden können. Azure API Management unterstützt Service Fabric-Dienstermittlung, -Partitionsauflösung und -Replikatauswahl, um Anforderungen auf intelligente Weise direkt an Back-End-Dienste in Service Fabric leiten zu können, damit Sie kein eigenes zustandsloses API-Gateway schreiben müssen. 

In diesem Szenario wird die Webbenutzeroberfläche über einen Webdienst bereitgestellt, während HTTP-API-Aufrufe per Azure API Management verwaltet und weitergeleitet werden. Dies ist im folgenden Diagramm dargestellt:

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-apim-web-app]

## <a name="application-scenarios"></a>Anwendungsszenarien

Dienste können in Service Fabric entweder zustandslos oder zustandsbehaftet sein, und für die Partitionierung können drei Schemas verwendet werden: „singleton“, „int-64 range“ und „named“. Für die Auflösung von Dienstendpunkten ist die Identifizierung einer bestimmten Partition einer bestimmten Dienstinstanz erforderlich. Beim Auflösen des Endpunkts eines Diensts muss sowohl der Dienstinstanzname (z.B. `fabric:/myapp/myservice`) als auch die bestimmte Partition des Diensts angegeben werden (mit Ausnahme einer Singleton-Partition).

Azure API Management kann mit einer beliebigen Kombination aus zustandslosen Diensten, zustandsbehafteten Diensten und Partitionierungsschema verwendet werden.

## <a name="send-traffic-to-a-stateless-service"></a>Senden von Datenverkehr an einen zustandslosen Dienst

Im einfachsten Fall wird Datenverkehr an die Instanz eines zustandslosen Diensts weitergeleitet. Zu diesem Zweck enthält ein API Management-Vorgang eine Richtlinie für die Verarbeitung von eingehendem Datenverkehr mit einem Service Fabric-Back-End, das einer bestimmten Instanz eines zustandslosen Diensts auf dem Service Fabric-Back-End zugeordnet ist. Anforderungen, die an diesen Dienst gesendet werden, werden an ein zufälliges Replikat der Instanz eines zustandslosen Diensts geleitet.

#### <a name="example"></a>Beispiel
Im folgenden Szenario enthält eine Service Fabric-Anwendung einen zustandslosen Dienst mit dem Namen `fabric:/app/fooservice`, der eine interne HTTP-API verfügbar macht. Der Dienstinstanzname ist bekannt und kann direkt in der API Management-Richtlinie für die Verarbeitung von eingehendem Datenverkehr hartcodiert werden. 

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>Senden von Datenverkehr an einen zustandsbehafteten Dienst

Ähnlich wie beim Szenario mit dem zustandslosen Dienst kann Datenverkehr auch an die Instanz eines zustandsbehafteten Diensts weitergeleitet werden. In diesem Fall enthält ein API Management-Vorgang eine Richtlinie für die Verarbeitung von eingehendem Datenverkehr mit einem Service Fabric-Back-End, das eine Anforderung einer bestimmten Partition einer bestimmten Instanz des *zustandsbehafteten* Diensts zuordnet. Die Partition, der die einzelnen Anforderungen jeweils zugeordnet werden, wird mithilfe einer Lambda-Methode berechnet. Hierfür wird eine Eingabe der eingehenden HTTP-Anforderung verwendet, z.B. ein Wert im URL-Pfad. Die Richtlinie kann so konfiguriert werden, dass Anforderungen nur an das primäre Replikat oder für Lesevorgänge an ein zufälliges Replikat gesendet werden.

#### <a name="example"></a>Beispiel

Im folgenden Szenario enthält eine Service Fabric-Anwendung einen partitionierten zustandsbehafteten Dienst mit dem Namen `fabric:/app/userservice`, der eine interne HTTP-API verfügbar macht. Der Dienstinstanzname ist bekannt und kann direkt in der API Management-Richtlinie für die Verarbeitung von eingehendem Datenverkehr hartcodiert werden.  

Der Dienst wird mit dem Int64-Partitionsschema partitioniert, das über zwei Partitionen und einen Schlüsselbereich von `Int64.MinValue` bis `Int64.MaxValue` verfügt. Mit der Back-End-Richtlinie wird ein Partitionsschlüssel innerhalb dieses Bereichs berechnet, indem der `id`-Wert, der im URL-Anforderungspfad angegeben ist, in eine 64-Bit-Ganzzahl konvertiert wird. Es kann hier aber ein beliebiger Algorithmus verwendet werden, um den Partitionsschlüssel zu berechnen. 

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>Senden von Datenverkehr an mehrere zustandslose Dienste

Bei erweiterten Szenarien können Sie einen API Management-Vorgang definieren, bei dem Anforderungen mehr als einer Dienstinstanz zugeordnet werden. In diesem Fall enthält jeder Vorgang eine Richtlinie, bei der Anforderungen einer bestimmten Dienstinstanz zugeordnet werden. Dies erfolgt basierend auf Werten aus der eingehenden HTTP-Anforderung, z.B. dem URL-Pfad oder der Abfragezeichenfolge, und bei zustandsbehafteten Diensten aus der Partition in der Dienstinstanz. 

Zu diesem Zweck enthält ein API Management-Vorgang eine Richtlinie für die Verarbeitung von eingehendem Datenverkehr mit einem Service Fabric-Back-End, das einer Instanz eines zustandslosen Diensts auf dem Service Fabric-Back-End zugeordnet ist – basierend auf Werten, die aus der eingehenden HTTP-Anforderung abgerufen werden. Anforderungen an eine Dienstinstanz werden an ein zufälliges Replikat der Dienstinstanz gesendet.

#### <a name="example"></a>Beispiel

In diesem Beispiel wird für jeden Benutzer einer Anwendung eine neue Instanz eines zustandslosen Diensts mit einem dynamisch generierten Namen erstellt, indem die folgende Formel verwendet wird:
 
- `fabric:/app/users/<username>`

  Jeder Dienst hat einen eindeutigen Namen, aber die Namen sind vorher nicht bekannt, da die Dienste als Antwort auf eine Benutzer- oder Administratoreingabe erstellt werden und daher nicht in APIM-Richtlinien oder Routingregeln hartcodiert werden können. Stattdessen wird der Name des Diensts, an den eine Anforderung gesendet werden soll, in der Definition der Back-End-Richtlinie über den Wert `name` generiert, der im URL-Anforderungspfad angegeben ist. Beispiel:

  - Eine Anforderung an `/api/users/foo` wird an die Dienstinstanz `fabric:/app/users/foo` weitergeleitet.
  - Eine Anforderung an `/api/users/bar` wird an die Dienstinstanz `fabric:/app/users/bar` weitergeleitet.

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>Senden von Datenverkehr an mehrere zustandsbehaftete Dienste

Ähnlich wie beim Beispiel für den zustandslosen Dienst können Anforderungen bei einem API Management-Vorgang mehr als einer Instanz eines **zustandsbehafteten** Diensts zugeordnet werden. In diesem Fall kann es sein, dass sie für jede Instanz eines zustandsbehafteten Diensts eine Partitionsauflösung durchführen müssen.

Zu diesem Zweck enthält ein API Management-Vorgang eine Richtlinie für die Verarbeitung von eingehendem Datenverkehr mit einem Service Fabric-Back-End, das einer Instanz eines zustandsbehafteten Diensts auf dem Service Fabric-Back-End zugeordnet ist – basierend auf Werten, die aus der eingehenden HTTP-Anforderung abgerufen werden. Zusätzlich zur Zuordnung einer Anforderung zu einer bestimmten Dienstinstanz kann die Anforderung auch einer bestimmten Partition innerhalb der Dienstinstanz zugeordnet werden, und optional entweder dem primären Replikat oder einem zufälligen sekundären Replikat innerhalb der Partition.

#### <a name="example"></a>Beispiel

In diesem Beispiel wird für jeden Benutzer der Anwendung eine neue Instanz eines zustandsbehafteten Diensts mit einem dynamisch generierten Namen erstellt, indem die folgende Formel verwendet wird:
 
- `fabric:/app/users/<username>`

  Jeder Dienst hat einen eindeutigen Namen, aber die Namen sind vorher nicht bekannt, da die Dienste als Antwort auf eine Benutzer- oder Administratoreingabe erstellt werden und daher nicht in APIM-Richtlinien oder Routingregeln hartcodiert werden können. Stattdessen wird der Name des Diensts, an den eine Anforderung gesendet werden soll, in der Definition der Back-End-Richtlinie über den Wert `name` generiert, der im URL-Anforderungspfad angegeben ist. Beispiel:

  - Eine Anforderung an `/api/users/foo` wird an die Dienstinstanz `fabric:/app/users/foo` weitergeleitet.
  - Eine Anforderung an `/api/users/bar` wird an die Dienstinstanz `fabric:/app/users/bar` weitergeleitet.

Jede Dienstinstanz wird außerdem mit dem Int64-Partitionsschema partitioniert, das über zwei Partitionen und einen Schlüsselbereich von `Int64.MinValue` bis `Int64.MaxValue` verfügt. Mit der Back-End-Richtlinie wird ein Partitionsschlüssel innerhalb dieses Bereichs berechnet, indem der `id`-Wert, der im URL-Anforderungspfad angegeben ist, in eine 64-Bit-Ganzzahl konvertiert wird. Es kann hier aber ein beliebiger Algorithmus verwendet werden, um den Partitionsschlüssel zu berechnen. 

![Service Fabric mit Azure API Management-Topologie – Übersicht][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie das [Tutorial](service-fabric-tutorial-deploy-api-management.md), um Ihren ersten Service Fabric-Cluster mit API Management einzurichten und Anforderungen per API Management an Ihre Dienste zu leiten.

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png