---
title: Verwenden eines Event Hubs aus der Apache Kafka-App – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Azure Event Hubs-Unterstützung für Apache Kafka.
services: event-hubs
documentationcenter: .net
author: shvija
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 8bf381e7c66e06bbaa140ed865f0f7c9b4f001af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821696"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Verwenden von Azure Event Hubs aus Apache Kafka-Anwendungen
Event Hubs stellt einen Kafka-Endpunkt bereit, der von Ihren vorhandenen Kafka-basierten Anwendungen als Alternative zum Betreiben eines eigenen Kafka-Clusters verwendet werden kann. Event Hubs unterstützt [Apache Kafka-Protokoll 1.0 und höher](https://kafka.apache.org/documentation/) und funktioniert mit Ihren vorhandenen Kafka-Anwendungen, einschließlich MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Was bietet Event Hubs für Kafka?

Event Hubs für Kafka bietet einen auf Azure Event Hubs aufbauenden Protokollheader, der binärkompatibel mit Kafka ab Version 1.0 ist – und zwar sowohl zum Lesen aus Kafka-Themen als auch zum Schreiben in diese. Sie können den Kafka-Endpunkt aus Ihrer Anwendung ohne Codeänderung, aber mit einer minimalen Konfigurationsänderung verwenden. Sie aktualisieren die Verbindungszeichenfolge in den Konfigurationen, um auf den Kafka-Endpunkt zu verweisen, der von Ihrem Event Hub bereitgestellt wird, anstatt auf Ihren Kafka-Cluster zu verweisen. Nun können Sie Ereignisse aus Ihren Anwendungen, die das Kafka-Protokoll verwenden, in Event Hubs streamen. Diese Integration unterstützt auch Frameworks wie [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), das derzeit als Vorschauversion verfügbar ist. 

Konzeptionell sind Kafka und Event Hubs nahezu identisch: Es handelt sich bei beiden um partitionierte Protokolle für das Streaming von Daten. In der folgenden Tabelle werden die Konzepte von Kafka und Event Hubs einander zugeordnet.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Konzeptionelle Zuordnung zwischen Kafka und Event Hubs

| Kafka-Konzept | Event Hubs-Konzept|
| --- | --- |
| Cluster | Namespace |
| Thema | Event Hub |
| Partition | Partition|
| Consumergruppe | Consumergruppe |
| Offset | Offset|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Hauptunterschiede zwischen Kafka und Event Hubs

Während es sich bei [Apache Kafka](https://kafka.apache.org/) um Software handelt, die Sie an jedem Speicherort ausführen können, ist Event Hubs ein Clouddienst ähnlich Azure Blob Storage. Es sind keine Server oder Netzwerke zu verwalten und keine Broker zu konfigurieren. Sie erstellen einen Namespace (einen FQDN, in dem sich Ihre Themen befinden) und erstellen Event Hubs oder Themen in diesem Namespace. Weitere Informationen zur Verwendung von Event Hubs und Namespaces finden Sie unter [Event Hubs-Features](event-hubs-features.md#namespace). Als Clouddienst verwendet Event Hubs eine einzelne stabile virtuelle IP-Adresse als Endpunkt, damit Clients keine Informationen über die Broker oder die Computer in einem Cluster benötigen. 

Die Skalierung in Event Hubs wird dadurch gesteuert, wie viele Durchsatzeinheiten Sie erwerben. Dabei berechtigt jede Durchsatzeinheit Sie zu 1 MB pro Sekunde oder 1.000 eingehenden Ereignissen pro Sekunde. Standardmäßig wird Event Hubs mit dem Feature [Automatische Vergrößerung](event-hubs-auto-inflate.md) über Durchsatzeinheiten zentral hochskaliert, wenn Sie Ihr Limit erreichen. Dieses Feature funktioniert auch mit Event Hubs für Kafka. 

### <a name="security-and-authentication"></a>Sicherheit und Authentifizierung

Azure Event Hubs erfordert SSL oder TLS für sämtliche Kommunikation und verwendet Shared Access Signatures (SAS) für die Authentifizierung. Diese Anforderung gilt auch für einen Kafka-Endpunkt in Event Hubs. Aus Gründen der Kompatibilität mit Kafka verwendet Event Hubs SASL PLAIN für die Authentifizierung und SASL SSL für die Transportsicherheit. Weitere Informationen zur Sicherheit in Event Hubs finden Sie unter [Event Hubs – Authentifizierung und Sicherheit](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Andere verfügbare Event Hubs-Features für Kafka

Event Hubs für Kafka ermöglicht Ihnen, mit einem Protokoll zu schreiben und mit einem anderen zu lesen, sodass Ihre aktuellen Kafka-Produzenten weiterhin über Kafka veröffentlichen können. Sie können außerdem Leser mit Event Hubs hinzufügen, z.B. Azure Stream Analytics oder Azure Functions. Darüber hinaus funktionieren Event Hubs-Features wie das [Erfassen](event-hubs-capture-overview.md) und die [geografische Notfallwiederherstellung](event-hubs-geo-dr.md) auch mit Event Hubs für Kafka.

## <a name="features-that-are-not-yet-supported"></a>Noch nicht unterstützte Features 

Aufstellung der Kafka-Features, die noch nicht unterstützt werden:

*   Idempotenter Produzent
*   Transaktion
*   Komprimierung
*   Größenbasierte Aufbewahrung
*   Protokollkomprimierung
*   Hinzufügen von Partitionen zu einem vorhandenen Thema
*   Unterstützung der HTTP-Kafka-API
*   Kafka Streams

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde eine Einführung in Event Hubs für Kafka vermittelt. Weitere Informationen finden Sie unter den folgenden Links:

- [Erstellen von Kafka-fähigen Event Hubs](event-hubs-create-kafka-enabled.md)
- [Streamen von Ereignissen in Event Hubs aus Kafka-Anwendungen](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Spiegeln eines Kafka-Brokers in einem Kafka-fähigen Event Hub](event-hubs-kafka-mirror-maker-tutorial.md)
- [Verbinden von Apache Spark mit einem Kafka-fähigen Event Hub](event-hubs-kafka-spark-tutorial.md)
- [Verbinden von Apache Flink mit einem Kafka-fähigen Event Hub](event-hubs-kafka-flink-tutorial.md)
- [Integrieren von Kafka Connect in einen Kafka-fähigen Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Verbinden von Akka Streams mit einem Kafka-fähigen Event Hub](event-hubs-kafka-akka-streams-tutorial.md)
- [Erkunden von Beispielen auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


