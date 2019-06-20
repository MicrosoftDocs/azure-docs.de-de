---
title: 'Authentifizierung und Sicherheitsmodell: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel beschreibt die Authentifizierung und das Sicherheitsmodell von Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 19b347423c28b4c615f90f325ead462b9d3e8e9e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60822580"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Azure Event Hubs: Authentifizierung und Sicherheitsmodell

Das Sicherheitsmodell von Azure Event Hubs erfüllt die folgenden Voraussetzungen:

* Nur Clients, die gültige Anmeldeinformationen bereitstellen, können Daten an einen Event Hub senden.
* Ein Client kann keinen anderen Client imitieren.
* Ein nicht autorisierter Client kann daran gehindert werden, Daten an einen Event Hub zu senden.

## <a name="client-authentication"></a>Clientauthentifizierung

Das Event Hubs-Sicherheitsmodell basiert auf einer Kombination aus [Shared Access Signature (SAS)](../service-bus-messaging/service-bus-sas.md)-Token und *Ereignisherausgebern*. Ein Ereignisherausgeber definiert einen virtuellen Endpunkt für einen Event Hub. Der Herausgeber kann nur zum Senden von Nachrichten an einen Event Hub verwendet werden. Es ist nicht möglich, von einem Herausgeber Nachrichten zu empfangen.

In der Regel setzt ein Event Hub einen Herausgeber pro Client ein. Alle Nachrichten, die an einen der Herausgeber eines Event Hubs gesendet werden, werden in die Warteschlange innerhalb dieses Event Hubs eingereiht. Herausgeber ermöglichen die präzise Access Control und Zugriffsdrosselung.

Jedem Event Hubs-Client wird ein eindeutiges Token zugewiesen, das auf den Client hochgeladen wird. Token werden so erzeugt, sodass jedes eindeutige Token Zugriff auf einen anderen eindeutigen Herausgeber gewährt. Ein Client, der ein Token besitzt, kann nur an einen Herausgeber senden und zu keinem anderen. Wenn mehrere Clients dasselbe Token gemeinsam nutzen, verwendet jeder von ihnen denselben Herausgeber.

Obwohl dies nicht empfohlen wird, ist es möglich, Geräte mit Token auszustatten, die direkten Zugriff auf einen Event Hub gewähren. Alle Geräte, die solche Token enthalten, können Nachrichten direkt an den Event Hub senden. Solche Geräte unterliegen keiner Drosselung. Darüber hinaus kann das Gerät nicht davon gesperrt werden, an den betreffenden Event Hub zu senden.

Alle Token werden mit einem SAS-Schlüssel signiert. Alle Token werden in der Regel mit demselben Schlüssel signiert. Clients kennen den Schlüssel nicht. Dadurch wird verhindert, dass Clients Token erzeugen.

### <a name="create-the-sas-key"></a>Erstellen des SAS-Schlüssels

Wenn Sie einen Event Hubs-Namespace erstellen, generiert der Dienst automatisch einen 256-Bit-SAS-Schlüssel mit dem Namen **RootManageSharedAccessKey**. Dieser Regel ist ein Paar aus primärem und sekundärem Schlüssel zugeordnet, das dem Namespace Sende-, Lausch- und Verwaltungsberechtigungen gewährt. Sie können auch zusätzliche Schlüssel erstellen. Es wird empfohlen, dass Sie einen Schlüssel erzeugen, der Berechtigungen zum Senden an den bestimmten Event Hub erteilt. Für den Rest dieses Themas wird davon ausgegangen, dass Sie diesen Schlüssel mit **EventHubSendKey** benannt haben.

Im folgenden Beispiel wird ein Nur-Senden-Schlüssel erstellt, wenn der Event Hub erstellt wird:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generieren von Token

Sie können Token mit dem SAS-Schlüssel generieren. Sie dürfen nur ein Token pro Client erzeugen. Token können dann mit dem folgenden Verfahren erstellt werden. Alle Token werden mit dem Schlüssel **EventHubSendKey** generiert. Jedes Token erhält einen eindeutigen URI. Der resource-Parameter entspricht dem URI-Endpunkt des Diensts (Event Hub in diesem Fall).

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Beim Aufrufen dieser Methode sollte der URI als `https://<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`angegeben werden. Der URI ist für alle Token nahezu identisch, mit Ausnahme von `PUBLISHER_NAME`, der für jedes Token unterschiedlich sein sollte. Im Idealfall stellt `PUBLISHER_NAME` die ID des Clients dar, der dieses Token erhält.

Diese Methode generiert ein Token mit der folgenden Struktur:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Die Tokengültigkeitsdauer wird als Zeit in Sekunden ab dem 1. Januar 1970 angegeben. Es folgt ein Beispiel für ein Token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

In der Regel müssen die Token eine Lebensdauer haben, die der Lebensdauer des Clients entspricht oder diese übersteigt. Wenn der Client die Möglichkeit hat, ein neues Token zu erhalten, können Token mit einer kürzeren Lebensdauer verwendet werden.

### <a name="sending-data"></a>Senden von Daten

Sobald die Token erstellt wurden, wird jedem Client sein eigenes eindeutiges Token bereitgestellt.

Wenn der Client Daten an einen Event Hub sendet, markiert er seine Sendeanforderung mit dem Token. Um Lauschangriffe und Diebstahl von Token von einem Angreifer zu verhindern, muss die Kommunikation zwischen dem Client und dem Event Hub über einen verschlüsselten Kanal erfolgen.

### <a name="blacklisting-clients"></a>Eintragen von Clients in eine schwarze Liste

Wenn ein Token von einem Angreifer gestohlen wurde, kann der Angreifer den Client imitieren, dessen Token gestohlen wurde. Wird ein Client in eine schwarze Liste eingetragen, wird dieser Client als nicht verwendbar gekennzeichnet, bis er ein neues Token erhalten hat, das einen anderen Herausgeber verwendet.

## <a name="authentication-of-back-end-applications"></a>Authentifizierung von Back-End-Anwendungen

Um Back-End-Anwendungen zu authentifizieren, die von Event Hubs-Clients generierte Daten nutzen, setzen Event Hubs ein Sicherheitsmodell ähnlich dem Modell ein, das für Service Bus-Themen verwendet wird. Eine Event Hubs-Consumergruppe entspricht einem Abonnement für ein Service Bus-Thema. Ein Client kann eine Consumergruppe erstellen, wenn die Anforderung zum Erstellen der Consumergruppe von einem Token begleitet wird, der Berechtigungen zum Verwalten des Event Hubs gewährt, oder für den Namespace, zu dem der Event Hub gehört. Ein Client kann Daten aus einer Consumergruppe nutzen, wenn die Empfangsanforderung von einem Token begleitet wird, die der Gruppe, dem Event Hub oder dem Namespace, zu dem das Event Hub gehört, Empfangsrechte erteilt.

Die aktuelle Version des Service Bus unterstützt nicht die SAS-Regeln für einzelne Abonnements. Dasselbe gilt für Event Hubs-Consumergruppen. SAS-Support wird in Zukunft für beide Funktionen hinzugefügt werden.

In Ermangelung einer SAS-Authentifizierung für einzelne Consumergruppen können Sie SAS-Schlüssel nutzen, um alle Consumergruppen mit einem gemeinsamen Schlüssel zu sichern. Dieser Ansatz ermöglicht einer Anwendung, Daten von allen Consumergruppen eines Event Hubs zu verwenden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Event Hubs finden Sie unter den folgenden Themen:

* [Übersicht über Event Hubs]
* [Overview of Shared Access Signatures (Übersicht von Shared Access Signatures)]
* [Beispielanwendungen mit Event Hubs]

[Übersicht über Event Hubs]: event-hubs-what-is-event-hubs.md
[Beispielanwendungen mit Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Overview of Shared Access Signatures (Übersicht von Shared Access Signatures)]: ../service-bus-messaging/service-bus-sas.md

