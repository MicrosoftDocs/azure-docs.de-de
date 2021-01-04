---
title: Verwenden eines Event Hubs aus der Apache Kafka-App – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zur Azure Event Hubs-Unterstützung für Apache Kafka.
ms.topic: article
ms.date: 09/25/2020
ms.openlocfilehash: d9aa8af30d5ef5e1a985e4d73a9d4a8921ac7d45
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369589"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Verwenden von Azure Event Hubs aus Apache Kafka-Anwendungen
Event Hubs bietet einen mit den Apache Kafka®-Producer- und -Consumer-APIs kompatiblen Endpunkt, der von den meisten vorhandenen Apache Kafka-Clientanwendungen als Alternative zum Ausführen Ihres eigenen Apache Kafka-Clusters verwendet werden kann. Event Hubs unterstützt Apache Kafka-Producer- und -Consumer-API-Clients mit Version 1.0 oder höher.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Was bietet Event Hubs für Kafka?

Die Funktion „Event Hubs für Apache Kafka“ bietet einen Protokollheader über Azure Event Hubs, der protokollkompatibel mit Apache Kafka-Clients ist, die für Apache Kafka-Serverversionen 1.0 oder höher erstellt wurden, und der sowohl das Lesen aus als auch das Schreiben in Event Hubs unterstützt, was Apache Kafka-Themen entspricht. 

Sie können den Event Hubs Kafka-Endpunkt in Ihren Anwendungen häufig ohne Codeänderungen im Vergleich zum vorhandenen Kafka-Setup verwenden und nur die Konfiguration ändern: Aktualisieren Sie die Verbindungszeichenfolge in den Konfigurationen, um auf den Kafka-Endpunkt zu verweisen, der von Ihrem Event Hub bereitgestellt wird, anstatt auf Ihren Kafka-Cluster zu verweisen. Nun können Sie Ereignisse aus Ihren Anwendungen, die das Kafka-Protokoll verwenden, in Event Hubs streamen. 

Konzeptionell sind sich Kafka und Event Hubs sehr ähnlich: Beide sind partitionierte Protokolle, die für das Streamen von Daten erstellt wurden, wobei der Client steuert, welcher Teil des beibehaltenen Protokolls gelesen werden soll. In der folgenden Tabelle werden die Konzepte von Kafka und Event Hubs einander zugeordnet.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Konzeptionelle Zuordnung zwischen Kafka und Event Hubs

| Kafka-Konzept | Event Hubs-Konzept|
| --- | --- |
| Cluster | Namespace |
| Thema | Event Hub |
| Partition | Partition|
| Consumergruppe | Consumergruppe |
| Offset | Offset|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Hauptunterschiede zwischen Kafka und Event Hubs

Während es sich bei [Apache Kafka](https://kafka.apache.org/) um Software handelt, die Sie in der Regel installieren und betreiben müssen, ist Event Hubs ein vollständig verwalteter, cloudbasierter Dienst. Es gibt keine Server, Datenträger oder Netzwerke, die verwaltet und überwacht werden müssen, und keine Broker, die berücksichtigt oder konfiguriert werden müssen. Sie erstellen einen Namespace, d. h. einen Endpunkt mit einem vollqualifizierten Domänennamen, und erstellen dann Event Hubs (Themen) in diesem Namespace. 

Weitere Informationen zur Verwendung von Event Hubs und Namespaces finden Sie unter [Event Hubs-Features](event-hubs-features.md#namespace). Als Clouddienst verwendet Event Hubs eine einzelne stabile virtuelle IP-Adresse als Endpunkt, damit Clients keine Informationen über die Broker oder die Computer in einem Cluster benötigen. Auch wenn Event-Hubs dasselbe Protokoll implementiert, bedeutet dieser Unterschied, dass der gesamte Kafka-Datenverkehr für alle Partitionen vorhersehbar durch diesen einen Endpunkt weitergeleitet wird, anstatt Firewallzugriff für alle Broker eines Clusters zu erfordern.   

Die Skalierung in Event Hubs hängt davon ab, wie viele Durchsatzeinheiten Sie erwerben, wobei jede Durchsatzeinheit zu 1 Megabyte pro Sekunde oder 1.000 eingehenden Ereignissen pro Sekunde und doppelt so vielen ausgehenden Ereignissen berechtigt. Event Hubs kann die Durchsatzeinheiten automatisch hochskalieren, wenn Sie die Durchsatzgrenze erreichen, wenn Sie die Funktion [Automatische Vergrößerung](event-hubs-auto-inflate.md) verwenden. Dieses Feature funktioniert auch mit der Apache Kafka-Protokollunterstützung.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Ist Apache Kafka die richtige Lösung für Ihre Workload?

Beim Entwickeln von Anwendungen mit Apache Kafka ist es auch hilfreich zu verstehen, dass Azure Event Hubs Teil einer Sammlung von Diensten ist, die auch [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) und [Azure Event Grid](../event-grid/overview.md) umfasst. 

Während einige Anbieter kommerzieller Distributionen von Apache Kafka den Eindruck erwecken möchten, dass Apache Kafka eine Universallösung für alle Anforderungen an eine Messagingplattform ist, sieht die Realität jedoch so aus, dass Apache Kafka beispielsweise nicht das [ Competing-Consumer](/azure/architecture/patterns/competing-consumers)-Warteschlangenmuster implementiert, keine Unterstützung für [publish-subscribe](/azure/architecture/patterns/publisher-subscriber) auf einer Ebene bietet, die Abonnenten Zugriff auf eingehende Nachrichten auf der Grundlage von vom Server ausgewerteten Regeln erlaubt, die keine einfachen Offsets sind, und keine Möglichkeiten besitzt, den Lebenszyklus eines Auftrags nachzuverfolgen, der durch eine Nachricht initiiert wurde oder fehlerhafte Nachrichten in eine Warteschlange für unzustellbare Nachrichten zu verschieben, die alle für viele Messagingszenarien in Unternehmen grundlegend sind.

Um die Unterschiede zwischen Mustern zu verstehen und um zu erfahren, welches Muster am besten von welchem Dienst abgedeckt wird, lesen Sie den Leitfaden [Asynchrone Messagingoptionen in Azure](/azure/architecture/guide/technology-choices/messaging). Als Benutzer von Apache Kafka werden Sie vielleicht feststellen, dass die Kommunikationspfade, die Sie bisher mit Kafka realisiert haben, mit weitaus geringerer Grundkomplexität und dennoch leistungsfähigeren Funktionen realisiert werden können, wenn Sie entweder Event Grid oder Service Bus verwenden. 

Wenn Sie bestimmte Funktionen von Apache Kafka benötigen, die nicht über die Event Hubs für Apache Kafka-Schnittstelle verfügbar sind, oder wenn Ihr Implementierungsmuster die [Event Hubs-Kontingente](event-hubs-quotas.md) überschreitet, können Sie auch einen [nativen Apache Kafka-Cluster in Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) ausführen.  

## <a name="security-and-authentication"></a>Sicherheit und Authentifizierung
Jedes Mal, wenn Sie Ereignisse/Daten von einem Event Hub für Kafka veröffentlichen oder nutzen, versucht Ihr Client, auf die Event Hubs-Ressourcen zuzugreifen. Sie möchten sicherstellen, dass der Zugriff auf die Ressourcen durch eine autorisierte Entität erfolgt. Wenn Sie das Apache Kafka-Protokoll mit ihren Clients verwenden, können Sie die Konfiguration für Authentifizierung und Verschlüsselung mithilfe der SASL-Mechanismen festlegen. Wenn Sie Event Hubs für Kafka verwenden, ist die TLS-Verschlüsselung erforderlich (da alle Daten während der Übertragung an Event Hubs TLS-verschlüsselt sind). Sie können dies durch Angeben der Option SASL_SSL in Ihrer Konfigurationsdatei erreichen. 

Azure Event Hubs bietet mehrere Optionen, um den Zugriff auf Ihre sicheren Ressourcen zu autorisieren. 

- OAuth 2.0
- Shared Access Signature (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs ist in Azure Active Directory (Azure AD) integriert, das einen mit **OAuth 2.0** kompatiblen zentralisierten Autorisierungsserver bereitstellt. Mit Azure AD können Sie rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) verwenden, um Ihren Clientidentitäten fein differenzierte Berechtigungen zu erteilen. Sie können dieses Feature in Kombination mit Ihren Kafka-Clients verwenden, indem Sie **SASL_SSL** als Protokoll und **OAUTHBEARER** als Mechanismus angeben. Details zu den Azure-Rollen und -Ebenen, die den Umfang des Zugriffs definieren, finden Sie unter [Autorisieren des Zugriffs mit Azure AD](authorize-access-azure-active-directory.md).

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Shared Access Signature (SAS)
Event Hubs bietet darüber hinaus **Shared Access Signatures (SAS)** für den delegierten Zugriff auf Event Hubs für Kafka-Ressourcen. Die Autorisierung des Zugriffs mithilfe des tokenbasierten OAuth 2.0-Mechanismus sorgt für überlegene Sicherheit und einfache Verwendung im Vergleich mit SAS. Durch die integrierten Rollen kann außerdem die Notwendigkeit einer ACL-basierten Autorisierung entfallen, die vom Benutzer gewartet und verwaltet werden muss. Sie können dieses Feature in Kombination mit Ihren Kafka-Clients verwenden, indem Sie **SASL_SSL** als Protokoll und **PLAIN** als Mechanismus angeben. 

```properties
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersetzen Sie `{YOUR.EVENTHUBS.CONNECTION.STRING}` durch die Verbindungszeichenfolge für Ihren Event Hubs-Namespace. Anweisungen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen einer Event Hubs-Verbindungszeichenfolge](event-hubs-get-connection-string.md). Hier sehen Sie eine Beispielkonfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

> [!NOTE]
> Wenn die SAS-Authentifizierung bei Kafka-Clients verwendet wird, werden die eingerichteten Verbindungen beim erneuten Generieren des SAS-Schlüssels nicht getrennt. 


#### <a name="samples"></a>Beispiele 
Ein **Tutorial** mit schrittweisen Anleitungen zum Erstellen eines Event Hubs und den Zugriff darauf mithilfe von SAS oder OAuth finden Sie unter [Schnellstart: Datenstreaming mit Event Hubs mithilfe des Kafka-Protokolls](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Weitere **Beispiele** , die die Verwendung von OAuth mit Event Hubs für Kafka veranschaulichen, finden Sie unter [Beispiele auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Weitere Event Hubs-Funktionen 

Die Funktion „Event Hubs für Apache Kafka“ ist eines der drei Protokolle, die gleichzeitig in Azure Event Hubs verfügbar sind und ergänzt HTTP und AMQP. Sie können mit jedem dieser Protokolle schreiben und mit einem der anderen lesen, sodass Ihre aktuellen Apache Kafka-Producer die Veröffentlichung über Apache Kafka fortsetzen können. Der Leser kann jedoch von der nativen Integration in die AMQP-Schnittstelle von Event Hubs profitieren, z. B. Azure Stream Analytics oder Azure Functions. Umgekehrt können Sie Azure Event Hubs problemlos als Zielendpunkt in AMQP-Routingnetzwerke integrieren und dennoch Daten über Apache Kafka-Integrationen lesen.  

Darüber hinaus arbeiten Event Hubs-Funktionen wie [Aufzeichnen](event-hubs-capture-overview.md), die eine äußerst kosteneffiziente Langzeitarchivierung über Azure Blob Storage und Azure Data Lake Storage ermöglichen, und [Geo Disaster-Recovery](event-hubs-geo-dr.md) ebenfalls mit der Event Hubs für Kafka-Funktion zusammen.

## <a name="apache-kafka-feature-differences"></a>Unterschiede der Apache Kafka-Funktionen 

Das Ziel von Event Hubs für Apache Kafka ist es, Anwendungen, die in die Apache Kafka-API eingebunden sind und ansonsten von einem Apache Kafka-Cluster unterstützt werden müssten, Zugriff auf die Funktionen von Azure Event Hubs zu ermöglichen. 

Wie [oben](#is-apache-kafka-the-right-solution-for-your-workload) erläutert, bieten die Azure-Messagingdienste eine reichhaltige und robuste Abdeckung für eine Vielzahl von Messagingszenarien, und obwohl die folgenden Funktionen derzeit nicht durch Unterstützung von Event Hubs für die Apache Kafka-API unterstützt werden, weisen wir darauf hin, wo und wie die gewünschte Funktion verfügbar ist.

### <a name="transactions"></a>Transaktionen

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) verfügt über robuste Transaktionsunterstützung, die den Empfang und die Abwicklung von Nachrichten und Sitzungen ermöglicht, während ausgehende Nachrichten, die sich aus der Nachrichtenverarbeitung ergeben, unter dem Konsistenzschutz einer Transaktion an mehrere Zielentitäten gesendet werden. Der Funktionssatz ermöglicht nicht nur die genau einmalige Verarbeitung jeder Nachricht in einer Sequenz, sondern vermeidet auch das Risiko, dass ein anderer Consumer versehentlich die gleichen Nachrichten erneut verarbeitet, wie es bei Apache Kafka der Fall wäre. Service Bus ist der empfohlene Dienst für transaktionale Nachrichtenworkloads.

### <a name="compression"></a>Komprimierung

Die clientseitige [Komprimierungsfunktion](https://cwiki.apache.org/confluence/display/KAFKA/Compression) von Apache Kafka komprimiert einen Batch von mehreren Nachrichten zu einer einzigen Nachricht auf der Producerseite und dekomprimiert den Batch auf der Consumerseite. Der Apache Kafka-Broker behandelt den Batch als spezielle Nachricht.

Diese Funktion steht im grundlegenden Widerspruch zum Multi-Protokollmodell von Azure Event Hubs, das es ermöglicht, dass Nachrichten (auch solche, die in Batches gesendet werden) einzeln vom Broker und über jedes Protokoll abgerufen werden können. 

Die Nutzlast eines Event Hub-Ereignisses ist ein Bytestream, und der Inhalt kann mit einem Algorithmus Ihrer Wahl komprimiert werden. Das Apache Avro-Codierungsformat unterstützt Komprimierung nativ.

### <a name="log-compaction"></a>Protokollkomprimierung

Apache Kafka-Protokollkomprimierung ist eine Funktion, mit der alle bis auf den letzten Datensatz jedes Schlüssels aus einer Partition entfernt werden können, wodurch ein Apache Kafka-Thema effektiv in einen Schlüssel-Wert-Speicher umgewandelt wird, bei dem der letzte hinzugefügte Wert den vorherigen Wert überschreibt. Das Schlüssel-Wert-Speichermuster wird selbst bei häufigen Updates wesentlich besser von Datenbankdiensten wie [Azure Cosmos DB](../cosmos-db/introduction.md) unterstützt.

Die Protokollkomprimierungsfunktion wird von den Clientframeworks von Kafka Connect und Kafka Streams verwendet.

### <a name="kafka-streams"></a>Kafka Streams

Kafka Streams ist eine Clientbibliothek für Stream Analytics, die Teil des Apache Kafka-Open-Source-Projekts, aber vom Apache Kafka-Ereignisdatenstrom-Broker getrennt ist. 

Der häufigste Grund, warum Azure Event Hubs-Kunden Kafka Streams-Unterstützung verwenden möchten, besteht darin, dass sie am Produkt „ksqlDB“ von Confluent interessiert sind. „ksqlDB“ ist ein proprietäres Shared-Source-Projekt, das [so lizenziert ist](https://github.com/confluentinc/ksql/blob/master/LICENSE), dass kein Anbieter, der „Software-as-a-Service, Platform-as-a-Service, Infrastructure-as-a-Service oder andere ähnliche Onlinedienste anbietet, die mit Confluent-Produkten oder -Diensten konkurrieren“, „ksqlDB“-Unterstützung verwenden oder anbieten darf. In der Praxis müssen Sie, wenn Sie ksqlDB verwenden, entweder Kafka selbst ausführen, oder Sie müssen die Cloudangebote von Confluent nutzen. Die Lizenzierungsbedingungen können sich auch auf Azure-Kunden auswirken, die Dienste für einen Zweck anbieten, der von der Lizenz ausgeschlossen wird.

Als eigenständige Lösung und ohne ksqlDB verfügt Kafka Streams über weniger Funktionen als viele alternative Frameworks und Dienste, von denen die meisten über integrierte SQL-Streamingschnittstellen verfügen. Sie alle sind heute in Azure Event Hubs integriert:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure Synapse Analytics (über Event Hubs Capture)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md)

Die aufgeführten Dienste und Frameworks können im Allgemeinen Ereignisströme und Referenzdaten direkt aus einer Vielzahl von Quellen über Adapter erfassen. Kafka Streams kann nur Daten aus Apache Kafka erfassen, und Ihre Analyseprojekte sind daher an Apache Kafka gebunden. Wenn Sie Daten aus anderen Quellen verwenden möchten, müssen Sie diese Daten zuerst mit dem Kafka Connect-Framework in Apache Kafka importieren.

Wenn Sie das Kafka Streams-Framework in Azure verwenden müssen, stellt [Apache Kafka in HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) diese Option bereit. Apache Kafka in HDInsight bietet die vollständige Kontrolle über alle Konfigurationsaspekte von Apache Kafka und ist gleichzeitig vollständig in verschiedene Aspekte der Azure-Plattform integriert, von der Platzierung von Fehler-/Updatedomänen über Netzwerkisolierung bis hin zu Überwachungsintegration. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde eine Einführung in Event Hubs für Kafka vermittelt. Weitere Informationen finden Sie unter [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md).