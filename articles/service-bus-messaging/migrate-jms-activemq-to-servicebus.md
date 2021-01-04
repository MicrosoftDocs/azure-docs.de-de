---
title: Migrieren von JMS-Anwendungen (Java Message Service) von Apache ActiveMQ zu Azure Service Bus | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie vorhandene JMS-Anwendungen, die mit Apache ActiveMQ interagieren, für die Interaktion mit Azure Service Bus migrieren.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2020
ms.author: aschhab
ms.custom: devx-track-java
ms.openlocfilehash: b8408dde86d1902cf5b4899c4783c9dd185449ee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515745"
---
# <a name="migrate-existing-java-message-service-jms-20-applications-from-apache-activemq-to-azure-service-bus"></a>Migrieren vorhandener JMS 2.0-Anwendungen (Java Message Service) von Apache ActiveMQ zu Azure Service Bus

In diesem Artikel wird erläutert, wie eine vorhandene JMS 2.0-Anwendung (Java Message Service), die mit einem JMS-Broker interagiert, so geändert wird, dass sie stattdessen mit Azure Service Bus interagiert. Insbesondere wird im Artikel die Migration von Apache ActiveMQ oder Amazon MQ behandelt.

Azure Service Bus unterstützt Java 2-, Enterprise Edition- und Spring-Workloads, die die JMS 2.0-API über AMQP (Advanced Message Queueing Protocol) nutzen.

## <a name="before-you-start"></a>Vorbereitung

### <a name="differences-between-azure-service-bus-and-apache-activemq"></a>Unterschiede zwischen Azure Service Bus und Apache ActiveMQ

Sowohl Azure Service Bus als auch Apache ActiveMQ sind Nachrichtenbroker, die als JMS-Anbieter fungieren, sodass Clientanwendungen Nachrichten senden und empfangen können. Beide ermöglichen Punkt-zu-Punkt-Semantik mit Warteschlangen und Veröffentlichen-Abonnieren-Semantik mit Themen und Abonnements. 

Dennoch gibt es einige Unterschiede zwischen den beiden Nachrichtenbrokern, wie in der folgenden Tabelle zu sehen ist:

| Category | ActiveMQ | Azure Service Bus |
| --- | --- | --- |
| Anwendungstiering | Gruppiert monolithisch | Zweistufig <br> (Gateway und Back-End) |
| Protokollunterstützung | <ul> <li>AMQP</li> <li> STOMP </li> <li> OpenWire </li> </ul> | AMQP |
| Bereitstellungsmodus | <ul> <li> Infrastructure-as-a-Service (IaaS), lokal </li> <li> Amazon MQ (verwaltete Platform as a Service) </li> | Verwaltete Platform as a Service (PaaS) |
| Nachrichtengröße | Vom Benutzer konfigurierbar | 1 MB (Premium-Tarif) |
| Hochverfügbarkeit | Vom Kunden verwaltet | Von der Plattform verwaltet |
| Notfallwiederherstellung | Vom Kunden verwaltet | Von der Plattform verwaltet | 

### <a name="current-supported-and-unsupported-features"></a>Zurzeit unterstützte und nicht unterstützte Funktionen

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

### <a name="considerations"></a>Überlegungen

Die zweistufige Natur von Azure Service Bus bietet verschiedene Funktionen für Geschäftskontinuität (Hochverfügbarkeit und Notfallwiederherstellung). Bei der Verwendung von JMS-Features gibt es jedoch einige Überlegungen zu beachten.

#### <a name="service-upgrades"></a>Dienstupgrades

Bei Service Bus-Upgrades und -Neustarts werden temporäre Warteschlangen und Themen gelöscht. Wenn Ihre Anwendung gegenüber dem Verlust von Daten in temporären Warteschlangen oder Themen empfindlich ist, verwenden Sie keine temporären Warteschlangen oder Themen. Verwenden Sie stattdessen dauerhafte Warteschlangen, Themen und Abonnements.

#### <a name="data-migration"></a>Datenmigration

Im Rahmen der Migration und Änderung von Clientanwendungen für die Interaktion mit Azure Service Bus werden die in ActiveMQ gespeicherten Daten nicht zu Service Bus migriert. Möglicherweise benötigen Sie eine benutzerdefinierte Anwendung, die ActiveMQ-Warteschlangen, -Themen und -Abonnements entfernt und die Nachrichten in Warteschlangen, Themen und Abonnements von Service Bus erneut wiedergibt.

#### <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Die von Azure Active Directory unterstützte rollenbasierte Zugriffssteuerung in Azure (Role Based Access Control, RBAC) ist der bevorzugte Authentifizierungsmechanismus für Service Bus. Da Azure RBAC oder die anspruchsbasierte Authentifizierung derzeit von Apache QPID JMS nicht unterstützt werden, sollten Sie jedoch für die Authentifizierung SAS-Schlüssel verwenden.

## <a name="pre-migration"></a>Vor der Migration

### <a name="version-check"></a>Versionsüberprüfung

Beim Schreiben der JMS-Anwendungen verwenden Sie die folgenden Komponenten und Versionen: 

| Komponente | Version |
|---|---|
| JMS-API (Java Message Service) | 1.1 oder höher |
| AMQP-Protokoll | 1.0 |

### <a name="ensure-that-amqp-ports-are-open"></a>Stellen Sie sicher, dass AMQP-Ports geöffnet sind.

Service Bus unterstützt die Kommunikation über das AMQP-Protokoll. Aktivieren Sie zu diesem Zweck die Kommunikation über die Ports 5671 (AMQP) und 443 (TCP). Abhängig davon, wo die Clientanwendungen gehostet werden, benötigen Sie möglicherweise ein Supportticket, um die Kommunikation über diese Ports zuzulassen.

> [!IMPORTANT]
> Service Bus unterstützt nur das AMQP 1.0-Protokoll.

### <a name="set-up-enterprise-configurations"></a>Einrichten von Unternehmenskonfigurationen

Service Bus bietet verschiedene Features für Unternehmenssicherheit und Hochverfügbarkeit. Weitere Informationen finden Sie unter 

  * [VNET-Dienstendpunkte](service-bus-service-endpoints.md)
  * [Firewall](service-bus-ip-filtering.md)
  * [Dienstseitige Verschlüsselung mit kundenseitig verwaltetem Schlüssel (BYOK)](configure-customer-managed-key.md)
  * [Private Endpunkte](private-link-service.md)
  * [Authentifizierung und Autorisierung](service-bus-authentication-and-authorization.md)

### <a name="monitoring-alerts-and-tracing"></a>Überwachung, Warnungen und Ablaufverfolgung

Für jeden Service Bus-Namespace veröffentlichen Sie Metriken in Azure Monitor. Sie können diese Metriken für Warnungen und die dynamische Skalierung von Ressourcen verwenden, die dem Namespace zugeordnet sind.

Weitere Informationen zu den verschiedenen Metriken und zum Einrichten von Warnungen für diese finden Sie unter [Service Bus-Metriken in Azure Monitor](service-bus-metrics-azure-monitor.md). Sie finden auch zu [clientseitiger Ablaufverfolgung für Datenvorgänge](service-bus-end-to-end-tracing.md) und [Betriebs-/Diagnoseprotokollierung für Verwaltungsvorgänge](service-bus-diagnostic-logs.md) weitere Informationen.

### <a name="metrics---new-relic"></a>Metriken – New Relic

Sie können die Metriken von ActiveMQ Metriken in Azure Service Bus zuordnen. Weitere Informationen finden Sie in den folgenden Artikeln auf der New Relic-Website:

  * [New Relic-Metriken für ActiveMQ/Amazon MQ](https://docs.newrelic.com/docs/integrations/amazon-integrations/aws-integrations-list/aws-mq-integration)
  * [New Relic-Metriken für Azure Service Bus](https://docs.newrelic.com/docs/integrations/microsoft-azure-integrations/azure-integrations-list/azure-service-bus-monitoring-integration)

> [!NOTE]
> Derzeit verfügt New Relic nicht über direkte nahtlose Integration mit ActiveMQ, es sind jedoch Metriken für Amazon MQ verfügbar. Da Amazon MQ von ActiveMQ abgeleitet ist, ordnet die nachstehende Tabelle die New Relic-Metriken von Amazon MQ Azure Service Bus zu.
>

|Metrikgruppierung| Metrik für Amazon MQ/ActiveMQ | Azure Service Bus-Metrik |
|------------|---------------------------|--------------------------|
|Broker|`CpuUtilization`|`CPUXNS`|
|Broker|`MemoryUsage`|`WSXNS`|
|Broker|`CurrentConnectionsCount`|`activeConnections`|
|Broker|`EstablishedConnectionsCount`|`activeConnections` + `connectionsClosed`|
|Broker|`InactiveDurableTopicSubscribersCount`|Verwenden von Metriken für Abonnements|
|Broker|`TotalMessageCount`|Verwenden der Warteschlangen-/Thema-/Abonnementebene `activeMessages`|
|Warteschlange/Thema|`EnqueueCount`|`incomingMessages`|
|Warteschlange/Thema|`DequeueCount`|`outgoingMessages`|
|Warteschlange|`QueueSize`|`sizeBytes`|



## <a name="migration"></a>Migration

Führen Sie die Schritte in den nächsten Abschnitten aus, um Ihre vorhandene JMS 2.0-Anwendung für die Interaktion mit Service Bus zu migrieren.

### <a name="export-the-topology-from-activemq-and-create-the-entities-in-service-bus-optional"></a>Exportieren der Topologie aus ActiveMQ und Erstellen der Entitäten in Service Bus (optional)

Um sicherzustellen, dass die Clientanwendungen nahtlos eine Verbindung mit Service Bus herstellen können, migrieren Sie die Topologie (einschließlich Warteschlangen, Themen und Abonnements) von Apache ActiveMQ zu Service Bus.

> [!NOTE]
> Für JMS-Anwendungen erstellen Sie Warteschlangen, Themen und Abonnements als Laufzeitvorgang. Die meisten JMS-Anbieter (Nachrichtenbroker) bieten die Möglichkeit, sie zur Laufzeit zu erstellen. Daher wird dieser Exportschritt als optional eingestuft. Um sicherzustellen, dass Ihre Anwendung über die Berechtigungen zum Erstellen der Topologie zur Laufzeit verfügt, verwenden Sie die Verbindungszeichenfolge mit den SAS-Berechtigungen `Manage`.

Gehen Sie dazu folgendermaßen vor:

1. Verwenden Sie die [ActiveMQ-Befehlszeilentools](https://activemq.apache.org/activemq-command-line-tools-reference), um die Topologie zu exportieren.
1. Erstellen Sie die gleiche Topologie mit einer [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) erneut.
1. Führen Sie die Azure Resource Manager-Vorlage aus.


### <a name="import-the-maven-dependency-for-service-bus-jms-implementation"></a>Importieren der Maven-Abhängigkeit für die Service Bus JMS-Implementierung

Um eine nahtlose Verbindung mit Service Bus sicherzustellen, fügen Sie das Paket `azure-servicebus-jms` wie folgt der Maven-Datei `pom.xml` als Abhängigkeit hinzu:

```xml
<dependencies>
...
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus-jms</artifactId>
    </dependency>
...
</dependencies>
```

### <a name="application-server-configuration-changes"></a>Änderungen der Anwendungsserverkonfiguration

Dieser Teil ist für den Anwendungsserver angepasst, auf dem Ihre Clientanwendungen gehostet werden, die eine Verbindung mit ActiveMQ herstellen.

#### <a name="tomcat"></a>Tomcat

Hier beginnen Sie mit der für ActiveMQ spezifischen Konfiguration, wie in der Datei `/META-INF/context.xml` gezeigt.

```XML
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="org.apache.activemq.ActiveMQConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        brokerURL="tcp://localhost:61616"
        brokerName="LocalActiveMQBroker"
        useEmbeddedBroker="false"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQTopic"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.activemq.command.ActiveMQQueue"
        factory="org.apache.activemq.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

Dies wird wie folgt angepasst, um auf Service Bus zu zeigen:

```xml
<Context antiJARLocking="true">
    <Resource
        name="jms/ConnectionFactory"
        auth="Container"
        type="com.microsoft.azure.servicebus.jms.ServiceBusJmsConnectionFactory"
        description="JMS Connection Factory"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        connectionString="<INSERT YOUR SERVICE BUS CONNECTION STRING HERE>"/>

    <Resource name="jms/topic/MyTopic"
        auth="Container"
        type="org.apache.qpid.jms.JmsTopic"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO"/>
    <Resource name="jms/queue/MyQueue"
        auth="Container"
        type="org.apache.qpid.jms.JmsQueue"
        factory="org.apache.qpid.jms.jndi.JNDIReferenceFactory"
        physicalName="MY.TEST.FOO.QUEUE"/>
</Context>
```

#### <a name="spring-applications"></a>Spring-Anwendungen

##### <a name="update-the-applicationproperties-file"></a>Aktualisieren Sie die Datei `application.properties`.

Wenn Sie eine Spring Boot-Anwendung zum Herstellen einer Verbindung mit ActiveMQ verwenden, sollten Sie die ActiveMQ-spezifischen Eigenschaften aus der Datei `application.properties` entfernen.

```properties
spring.activemq.broker-url=<ACTIVEMQ BROKER URL>
spring.activemq.user=<ACTIVEMQ USERNAME>
spring.activemq.password=<ACTIVEMQ PASSWORD>
```

Fügen Sie dann der Datei `application.properties` die für Service Bus spezifischen Eigenschaften hinzu.

```properties
azure.servicebus.connection-string=Endpoint=myEndpoint;SharedAccessKeyName=mySharedAccessKeyName;SharedAccessKey=mySharedAccessKey
```

##### <a name="replace-activemqconnectionfactory-with-servicebusjmsconnectionfactory"></a>Ersetzen Sie `ActiveMQConnectionFactory` durch `ServiceBusJmsConnectionFactory`.

Der nächste Schritt besteht im Ersetzen der Instanz von `ActiveMQConnectionFactory` durch `ServiceBusJmsConnectionFactory`.

> [!NOTE] 
> Die tatsächlichen Codeänderungen sind spezifisch für die Anwendung und die Art der Verwaltung von Abhängigkeiten, das nachstehende Beispiel enthält jedoch eine Anleitung zu den sinnvollen Änderungen.
>

Zuvor haben Sie möglicherweise ein `ActiveMQConnectionFactory`-Objekt wie folgt instanziiert:

```java

String BROKER_URL = "<URL of the hosted ActiveMQ broker>";
ConnectionFactory factory = new ActiveMQConnectionFactory(BROKER_URL);

Connection connection = factory.createConnection();
connection.start();

```

Nun ändern Sie dies, sodass ein `ServiceBusJmsConnectionFactory`-Objekt wie folgt instanziiert wird:

```java

ServiceBusJmsConnectionFactorySettings settings = new ServiceBusJmsConnectionFactorySettings();
String SERVICE_BUS_CONNECTION_STRING = "<Service Bus Connection string>";

ConnectionFactory factory = new ServiceBusJmsConnectionFactory(SERVICE_BUS_CONNECTION_STRING, settings);

Connection connection = factory.createConnection();
connection.start();

```

## <a name="post-migration"></a>Nach der Migration

Da Sie die Anwendung nun so geändert haben, dass sie mit dem Senden von Nachrichten an und Empfangen von Nachrichten von Service Bus beginnt, sollten Sie überprüfen, ob sie erwartungsgemäß funktioniert. Wenn das erledigt ist, können Sie den Anwendungsstapel weiter verfeinern und modernisieren.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie [Spring Boot-Starter für Azure Service Bus JMS](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-azure-service-bus) für die nahtlose Integration mit Service Bus.

Weitere Informationen zu Service Bus-Messaging und JMS finden Sie unter:

* [Service Bus JMS](service-bus-java-how-to-use-jms-api-amqp.md)
* [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
* [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md)