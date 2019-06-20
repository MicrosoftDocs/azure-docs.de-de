---
title: AMQP 1.0 in anforderungs-/antwortbasierten Azure Service Bus-Vorgängen | Microsoft-Dokumentation
description: Liste der anforderungs-/antwortbasierten Vorgänge in Microsoft Azure Service Bus
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: c22ba0b57ed1161e1f7e2082d2ba21f27b656da1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60402682"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 in Microsoft Azure Service Bus: anforderungs-/antwortbasierte Vorgänge

In diesem Artikel wird die Liste der anforderungs-/antwortbasierten Vorgänge in Microsoft Azure Service Bus definiert. Diese Informationen basieren auf dem Entwurf der AMQP Management Version 1.0.  
  
Eine ausführliche Anleitung zum AMQP 1.0-Wire-Protokoll, die erläutert, wie Service Bus die technische Spezifikation für OASIS AMQP implementiert und darauf aufbaut, finden Sie unter [AMQP 1.0 in Azure Service Bus und Event Hubs – Protokollleitfaden][AMQP 1.0 – Protokollleitfaden].  
  
## <a name="concepts"></a>Konzepte  
  
### <a name="entity-description"></a>Entitätsbeschreibung  

Eine Entitätsbeschreibung verweist entweder auf ein Service Bus-Objekt der [QueueDescription-Klasse](/dotnet/api/microsoft.servicebus.messaging.queuedescription), der [TopicDescription-Klasse](/dotnet/api/microsoft.servicebus.messaging.topicdescription) oder auf eines der [SubscriptionDescription-Klasse](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription).  
  
### <a name="brokered-message"></a>Im Broker gespeicherte Nachricht  

Stellt eine Nachricht in Service Bus dar, die einer AMQP-Nachricht zugeordnet ist. Die Zuordnung wird im [Service Bus AMQP-Protokollhandbuch](service-bus-amqp-protocol-guide.md) definiert.  
  
## <a name="attach-to-entity-management-node"></a>Hinzufügen zum Entitätsverwaltungsknoten  

Alle in diesem Dokument beschriebenen Vorgänge folgen einem Anforderungs-/Antwortmuster, beziehen sich auf eine Entität und erfordern das Hinzufügen zu einem Entitätsverwaltungsknoten.  
  
### <a name="create-link-for-sending-requests"></a>Erstellen eines Links zum Senden von Anforderungen  

Erstellt einen Link zum Verwaltungsknoten zum Senden von Anforderungen  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Erstellen eines Links zum Empfangen von Antworten  

Erstellt einen Link zum Empfangen von Antworten vom Verwaltungsknoten  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Übertragen einer Anforderungsnachricht  

Überträgt eine Anforderungsnachricht  
Ein Transaktionsstatus kann optional für Vorgänge hinzugefügt werden, die Transaktionen unterstützen.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Empfangen einer Antwortnachricht  

Empfängt die Antwortnachricht über den Antwortlink  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Die Antwortnachricht hat folgende Form:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adresse der Service Bus-Entität  

Service Bus-Entitäten müssen wie folgt adressiert werden:  
  
|Entitätstyp|Adresse|Beispiel|  
|-----------------|-------------|-------------|  
|queue|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|Thema|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|Abonnement|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Nachrichtenvorgänge  
  
### <a name="message-renew-lock"></a>Erneuerung der Nachrichtensperrung auf Nachrichtenebene  

Verlängert den Sperrzustand der Nachricht um die in der Entitätsbeschreibung angegebene Zeitspanne  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
 Der Nachrichtentext der Anforderung muss aus einem amqp-value-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|UUID-Array|Ja|Zu erneuernde Token für die Nachrichtensperre|  

> [!NOTE]
> Sperrtoken stellen die `DeliveryTag`-Eigenschaft für empfangene Nachrichten dar. Im folgenden Beispiel im [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) werden diese abgerufen. Das Token kann auch in den „DeliveryAnnotations“ als „x-opt-lock-token“ angezeigt werden. Dies ist jedoch nicht garantiert, und das `DeliveryTag` sollte bevorzugt werden. 
> 
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem amqp-value-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|expirations|Timestamp-Array|Ja|Neues Verfallsdatum für Token der Nachrichtensperre gemäß dem Sperrtoken der Anfrage.|  
  
### <a name="peek-message"></a>Nachricht einsehen  

Ermöglicht das Einsehen von Nachrichten ohne Sperren  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|lang|Ja|Die Sequenznummer, von der aus das Einsehen gestartet wird|  
|`message-count`|int|Ja|Die maximale Anzahl von einsehbaren Nachrichten|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – verfügt über weitere Nachrichten<br /><br /> 204: Kein Inhalt – keine weiteren Nachrichten|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|Cloud an das Gerät|Liste der Zuordnungen|Ja|Liste der Nachrichten, in denen jede Zuordnung eine Nachricht darstellt.|  
  
Die Zuordnung, die eine Nachricht darstellt, muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|message|Byte-Array|Ja|AMQP 1.0 Wire-Encoding-Nachricht|  
  
### <a name="schedule-message"></a>Nachricht planen  

Plant Nachrichten Dieser Vorgang unterstützt die Transaktion.
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|Cloud an das Gerät|Liste der Zuordnungen|Ja|Liste der Nachrichten, in denen jede Zuordnung eine Nachricht darstellt.|  
  
Die Zuordnung, die eine Nachricht darstellt, muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|message-id|Zeichenfolge|Ja|`amqpMessage.Properties.MessageId` als Zeichenfolge|  
|session-id|Zeichenfolge|Nein|`amqpMessage.Properties.GroupId as string`|  
|partition-key|Zeichenfolge|Nein|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|Zeichenfolge|Nein|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|message|Byte-Array|Ja|AMQP 1.0 Wire-Encoding-Nachricht|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Long-Array|Ja|Sequenznummer der geplanten Nachrichten. Sequenznummer wird zum Abbrechen verwendet.|  
  
### <a name="cancel-scheduled-message"></a>Geplante Nachricht abbrechen  

Bricht geplante Nachrichten ab  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Long-Array|Ja|Sequenznummern der abzubrechenden, geplanten Nachrichten|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Long-Array|Ja|Sequenznummer der geplanten Nachrichten. Sequenznummer wird zum Abbrechen verwendet.|  
  
## <a name="session-operations"></a>Sitzungsvorgänge  
  
### <a name="session-renew-lock"></a>Erneuerung der Nachrichtensperrung auf Sitzungsebene  

Verlängert den Sperrzustand der Nachricht um die in der Entitätsbeschreibung angegebene Zeitspanne  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|session-id|Zeichenfolge|Ja|Sitzungs-ID|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – verfügt über weitere Nachrichten<br /><br /> 204: Kein Inhalt – keine weiteren Nachrichten|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|expiration|timestamp|Ja|Neuer Ablauf|  
  
### <a name="peek-session-message"></a>Sitzungsnachricht einsehen  

Ermöglicht das Einsehen von Sitzungsnachrichten ohne Sperren  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|from-sequence-number|lang|Ja|Die Sequenznummer, von der aus das Einsehen gestartet wird|  
|message-count|int|Ja|Die maximale Anzahl von einsehbaren Nachrichten|  
|session-id|Zeichenfolge|Ja|Sitzungs-ID|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – verfügt über weitere Nachrichten<br /><br /> 204: Kein Inhalt – keine weiteren Nachrichten|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine Zuordnung zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|Cloud an das Gerät|Liste der Zuordnungen|Ja|Liste der Nachrichten, in denen jede Zuordnung eine Nachricht darstellt.|  
  
 Die Zuordnung, die eine Nachricht darstellt, muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|message|Byte-Array|Ja|AMQP 1.0 Wire-Encoding-Nachricht|  
  
### <a name="set-session-state"></a>Sitzungsstatus festlegen  

Legt den Status einer Sitzung fest  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|session-id|Zeichenfolge|Ja|Sitzungs-ID|  
|session-state|Byte-Array|Ja|Nicht transparente Binärdaten|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
### <a name="get-session-state"></a>Sitzungsstatus abrufen  

Ruft den Status einer Sitzung ab  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|session-id|Zeichenfolge|Ja|Sitzungs-ID|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|session-state|Byte-Array|Ja|Nicht transparente Binärdaten|  
  
### <a name="enumerate-sessions"></a>Sitzungen aufzählen  

Zählt die Sitzungen in einer Nachrichtenentität auf  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|timestamp|Ja|Filtern Sie, um nur nach einem bestimmten Zeitpunkt aktualisierte Sitzungen einzuschließen.|  
|skip|int|Ja|Überspringen von Sitzungen|  
|top|int|Ja|Maximale Sitzungsanzahl|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – verfügt über weitere Nachrichten<br /><br /> 204: Kein Inhalt – keine weiteren Nachrichten|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|skip|int|Ja|Anzahl der übersprungenen Sitzungen, wenn der Statuscode 200 lautet|  
|sessions-ids|Zeichenfolgen-Arrays|Ja|Array von Sitzungs-IDs, wenn der Statuscode 200 lautet|  
  
## <a name="rule-operations"></a>Regelvorgänge  
  
### <a name="add-rule"></a>Regel hinzufügen  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|rule-name|Zeichenfolge|Ja|Regelname, außer Abonnement- und Themennamen|  
|rule-description|map|Ja|Die Regelbeschreibung entsprechend den Angaben im nächsten Abschnitt|  
  
Die Zuordnung **rule-description** muss die folgenden Einträge enthalten, in denen **sql-filter** und **correlation-filter** sich gegenseitig ausschließen:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|sql-filter|map|Ja|`sql-filter`, wie im nächsten Abschnitt angegeben|  
|correlation-filter|map|Ja|`correlation-filter`, wie im nächsten Abschnitt angegeben|  
|sql-rule-action|map|Ja|`sql-rule-action`, wie im nächsten Abschnitt angegeben|  
  
Die Zuordnung „sql-filter“ muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|expression|Zeichenfolge|Ja|SQL-Filterausdruck|  
  
Die Zuordnung **correlation-filter** muss mindestens einen der folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|correlation-id|Zeichenfolge|Nein||  
|message-id|Zeichenfolge|Nein||  
|zu|Zeichenfolge|Nein||  
|reply-to|Zeichenfolge|Nein||  
|label|Zeichenfolge|Nein||  
|session-id|Zeichenfolge|Nein||  
|reply-to-session-id|Zeichenfolge|Nein||  
|Inhaltstyp|Zeichenfolge|Nein||  
|properties|map|Nein|Zuordnung zu Service Bus [BrokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)|  
  
Die Zuordnung **sql-rule-action** muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|expression|Zeichenfolge|Ja|SQL-Aktionsausdruck.|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
### <a name="remove-rule"></a>Regel entfernen  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|rule-name|Zeichenfolge|Ja|Regelname, außer Abonnement- und Themennamen|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
### <a name="get-rules"></a>Regeln zum Abrufen

#### <a name="request"></a>Anforderung

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:

|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  

Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|top|int|Ja|Die Anzahl der auf der Seite abzurufenden Regeln.|  
|skip|int|Ja|Die Anzahl der auszulassenden Regeln. Definiert den Startindex (+ 1) auf der Liste der Regeln. | 

#### <a name="response"></a>response

Die Antwortnachricht umfasst die folgenden Eigenschaften:

|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|Regeln| Zuordnungsarray|Ja|Regelnarray. Jede Regel wird durch eine Zuordnung dargestellt.|

Jeder Zuordnungseintrag im Array enthält die folgenden Eigenschaften:

|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|rule-description|Array beschriebener Objekte|Ja|`com.microsoft:rule-description:list` mit AMQP beschriebener Code 0x0000013700000004| 

`com.microsoft.rule-description:list` ist ein Array beschriebener Objekte. Das Array umfasst Folgendes:

|Index|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
| 0 | Array beschriebener Objekte | Ja | `filter` wie unten angegeben. |
| 1 | Array beschriebener Objekte | Ja | `ruleAction` wie unten angegeben. |
| 2 | Zeichenfolge | Ja | Name der Regel. |

`filter` kann einen der folgenden Typen aufweisen:

| Deskriptorname | Deskriptorcode | Wert |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | SQL-Filter |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korrelationsfilter |
| `com.microsoft:true-filter:list` | 0x000001370000007 | True-Filter, der 1=1 darstellt |
| `com.microsoft:false-filter:list` | 0x000001370000008 | False-Filter, der 1=0 darstellt |

`com.microsoft:sql-filter:list` ist ein beschriebenes Array, das Folgendes enthält:

|Index|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
| 0 | Zeichenfolge | Ja | SQL-Filterausdruck |

`com.microsoft:correlation-filter:list` ist ein beschriebenes Array, das Folgendes enthält:

|Index (falls vorhanden)|Werttyp|Wertinhalt|  
|---------|----------------|--------------|
| 0 | Zeichenfolge | Korrelations-ID |
| 1 | Zeichenfolge | Nachrichten-ID |
| 2 | Zeichenfolge | To |
| 3 | Zeichenfolge | Antworten an |
| 4 | Zeichenfolge | Bezeichnung |
| 5 | Zeichenfolge | Sitzungs-ID |
| 6 | Zeichenfolge | Antwort an Sitzungs-ID|
| 7 | Zeichenfolge | Inhaltstyp |
| 8 | Map | Zuordnung der Anwendung definierter Eigenschaften |

`ruleAction` kann einer der folgenden Typen sein:

| Deskriptorname | Deskriptorcode | Wert |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Aktion bei leerer Regel – keine Regelaktion vorhanden |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | SQL-Regelaktion |

`com.microsoft:sql-rule-action:list` ist ein Array von beschrieben Objekten, deren erster Eintrag eine Zeichenfolge ist, die den Ausdruck der SQL-Regelaktion enthält.

## <a name="deferred-message-operations"></a>Vorgänge zurückgestellter Nachrichten  
  
### <a name="receive-by-sequence-number"></a>Nach Sequenznummer empfangen  

Empfängt Vorgänge zurückgestellter Nachrichten nach Sequenznummer  
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|sequence-numbers|Long-Array|Ja|Sequenznummern|  
|receiver-settle-mode|ubyte|Ja|**Behandlungsrichtline** des Empfängers den Angaben in AMQP Core v1.0 entsprechend.|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|  
  
Der Nachrichtentext der Antwort muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|Cloud an das Gerät|Liste der Zuordnungen|Ja|Liste der Nachrichten, in denen jede Zuordnung eine Nachricht darstellt|  
  
Die Zuordnung, die eine Nachricht darstellt, muss die folgenden Einträge enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|lock-token|uuid|Ja|Token sperren, wenn `receiver-settle-mode` 1 beträgt|  
|message|Byte-Array|Ja|AMQP 1.0 Wire-Encoding-Nachricht|  
  
### <a name="update-disposition-status"></a>Dispositionsstatus aktualisieren  

Aktualisiert den Dipositionsstatus zurückgestellter Nachrichten Dieser Vorgang unterstützt Transaktionen.
  
#### <a name="request"></a>Anforderung  

Die Anforderungsnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|operation|Zeichenfolge|Ja|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Nein|Timeout des für „operation“ zuständigen Servers in Millisekunden|  
  
Der Nachrichtentext der Anforderung muss aus einem **amqp-value**-Abschnitt bestehen, der eine **Zuordnung** zu den folgenden Einträgen enthält:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|disposition-status|Zeichenfolge|Ja|abgeschlossen<br /><br /> Vorzeitig beendet<br /><br /> Angehalten|  
|lock-tokens|UUID-Array|Ja|Sperrtoken-Nachricht, um den Dispositionsstatus zu aktualisieren.|  
|deadletter-reason|Zeichenfolge|Nein|Kann festgelegt werden, wenn der Dispositionsstatus auf **angehalten** festgelegt wird.|  
|deadletter-description|Zeichenfolge|Nein|Kann festgelegt werden, wenn der Dispositionsstatus auf **angehalten** festgelegt wird.|  
|properties-to-modify|map|Nein|Liste zu ändernder Eigenschaften der im Broker gespeicherten Service Bus-Nachrichten.|  
  
#### <a name="response"></a>response  

Die Antwortnachricht muss die folgenden Anwendungseigenschaften enthalten:  
  
|Schlüssel|Werttyp|Erforderlich|Wertinhalt|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ja|HTTP-Antwortcode [RFC2616]<br /><br /> 200: OK – Erfolg, andernfalls fehlgeschlagen|  
|statusDescription|Zeichenfolge|Nein|Beschreibung des Status|

## <a name="next-steps"></a>Nächste Schritte

Klicken Sie auf die folgenden Links, um mehr über AMQP und Service Bus zu erfahren:

* [Übersicht über Service Bus AMQP]
* [AMQP 1.0 – Protokollleitfaden]
* [AMQP in Service Bus für Windows Server]

[Übersicht über Service Bus AMQP]: service-bus-amqp-overview.md
[AMQP 1.0 – Protokollleitfaden]: service-bus-amqp-protocol-guide.md
[AMQP in Service Bus für Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)
