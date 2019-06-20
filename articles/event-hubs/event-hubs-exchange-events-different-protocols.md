---
title: 'Austauschen von Ereignissen zwischen Apps, die unterschiedliche Protokolle verwenden: Azure Event Hubs | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, wie Consumer und Producer, die unterschiedliche Protokolle verwenden (AMQP, Apache Kafka und HTTPS), über Azure Event Hubs Ereignisse austauschen können.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: e704a2595130a2a815388447ac482ab96789d64a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60821765"
---
# <a name="exchange-events-between-consumers-and-producers-that-use-different-protocols-amqp-kafka-and-https"></a>Austauschen von Ereignissen zwischen Consumern und Producern, die unterschiedliche Protokolle verwenden: AMQP, Kafka und HTTPS
Azure Event Hubs unterstützt drei Protokolle für Consumer und Producer: AMQP, Kafka und HTTPS. Bei jedem dieser Protokolle wird eine Nachricht auf eigene Weise dargestellt, daher stellt sich die folgende Frage: Wenn eine Anwendung Ereignisse an einen Event Hub mit einem Protokoll sendet und diese mit einem anderen Protokoll nutzt, wie sehen dann die verschiedenen Komponenten und Werte der Ereignisse aus, wenn sie beim Consumer eingehen? In diesem Artikel werden bewährte Methoden für Producer und Consumer erörtert, um sicherzustellen, dass die Werte innerhalb eines Ereignisses von der Consumer-Anwendung richtig interpretiert werden.

Die Empfehlungen in diesem Artikel beziehen sich insbesondere auf die folgenden Clients, wobei die bei der Entwicklung der Codeausschnitte verwendeten Versionen jeweils angegeben sind:

* Kafka-Java-Client (Version 1.1.1 von https://www.mvnrepository.com/artifact/org.apache.kafka/kafka-clients)
* Microsoft Azure Event Hubs-Client für Java (Version 1.1.0 von https://github.com/Azure/azure-event-hubs-java)
* Microsoft Azure Event Hubs-Client für .NET (Version 2.1.0 von https://github.com/Azure/azure-event-hubs-dotnet)
* Microsoft Azure Service Bus (Version 5.0.0 von https://www.nuget.org/packages/WindowsAzure.ServiceBus)
* HTTPS (unterstützt nur Producer)

Das Verhalten anderer AMQP-Clients kann leicht abweichen. AMQP weist ein sorgfältig definiertes Typsystem aus, die Besonderheiten bei der Serialisierung sprachspezifischer Typen in und aus diesem Typsystem hängen jedoch vom Client ab, ebenso die Art, wie der Client Zugriff auf die Komponenten einer AMQP-Nachricht gewährt.

## <a name="event-body"></a>Ereignistext
Auf allen Microsoft-AMQP-Clients wird der Ereignistext als nicht interpretierter Bytebehälter dargestellt. Eine Produceranwendung übergibt eine Bytesequenz an den Client, und eine Consumeranwendung empfängt dieselbe Sequenz vom Client. Die Interpretation der Bytesequenz erfolgt innerhalb des Anwendungscodes.

Beim Senden eines Ereignisses über HTTPS ist der Ereignistext der POSTed-Inhalt, der auch als nicht interpretierte Bytes verarbeitet wird. Es ist einfach, den gleichen Zustand in einem Kafka-Producer oder Kafka-Consumer zu erreichen, indem der bereitgestellte ByteArraySerializer und der ByteArrayDeserializer wie im folgenden Code veranschaulicht verwendet werden:

### <a name="kafka-byte-producer"></a>Kafka-Producer: byte[]

```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, ByteArraySerializer.class.getName());

final KafkaProducer<Long, byte[]> producer = new KafkaProducer<Long, byte[]>(properties);

final byte[] eventBody = new byte[] { 0x01, 0x02, 0x03, 0x04 };
ProducerRecord<Long, byte[]> pr =
    new ProducerRecord<Long, byte[]>(myTopic, myPartitionId, myTimeStamp, eventBody);
```

### <a name="kafka-byte-consumer"></a>Kafka-Consumer: byte[]
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, ByteArrayDeserializer.class.getName());

final KafkaConsumer<Long, byte[]> consumer = new KafkaConsumer<Long, byte[]>(properties);

ConsumerRecord<Long, byte[]> cr = /* receive event */
// cr.value() is a byte[] with values { 0x01, 0x02, 0x03, 0x04 }
```

Dieser Code erstellt eine transparente Bytepipeline zwischen den beiden Hälften der Anwendung und ermöglicht dem Anwendungsentwickler, die manuelle Serialisierung und Deserialisierung in jeder gewünschten Form durchzuführen, einschließlich Deserialisierungsentscheidungen zur Laufzeit, z.B. basierend auf Typ- oder Senderinformationen in vom Benutzer festgelegten Eigenschaften in einem Ereignis.

Anwendungen mit einem einzelnen, festgelegten Ereignistexttyp können möglicherweise andere Kafka-Serialisierungsmodule und -Deserialisierungsmodule zum transparenten Konvertieren von Daten verwenden. Nehmen wir als Beispiel eine Anwendung, die JSON verwendet. Die Erstellung und Interpretation der JSON-Zeichenfolge erfolgt auf Anwendungsebene. Auf Event Hubs-Ebene ist der Ereignistext immer eine Zeichenfolge, eine Bytesequenz, die Zeichen in der UTF-8-Codierung darstellt. In diesem Fall kann der Kafka-Producer oder -Consumer den bereitgestellten StringSerializer oder StringDeserializer wie im folgenden Code gezeigt nutzen:

### <a name="kafka-utf-8-string-producer"></a>Kafka-Producer: UTF-8-Zeichenfolge
```java
final Properties properties = new Properties();
// add other properties
properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

final KafkaProducer<Long, String> producer = new KafkaProducer<Long, String>(properties);

final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
ProducerRecord<Long, String> pr =
    new ProducerRecord<Long, String>(myTopic, myPartitionId, myTimeStamp, exampleJson);
```

### <a name="kafka-utf-8-string-consumer"></a>Kafka-Consumer: UTF-8-Zeichenfolge
```java
final Properties properties = new Properties();
// add other properties
properties.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());

final KafkaConsumer<Long, String> consumer = new KafkaConsumer<Long, String>(properties);

ConsumerRecord<Long, Bytes> cr = /* receive event */
final String receivedJson = cr.value();
```

Für die AMQP-Seite umfassen Java und .NET integrierte Methoden zum Konvertieren von Zeichenfolgen in und aus UTF-8-Bytesequenzen. Auf den Microsoft-AMQP-Clients werden Ereignisse als Klasse mit dem Namen „EventData“ dargestellt. In den folgenden Beispielen wird veranschaulicht, wie Sie eine UTF-8-Zeichenfolge in einen EventData-Ereignistext in einem AMQP-Producer serialisieren und wie Sie einen EventData-Ereignistext in eine UTF-8-Zeichenfolge in einem AMQP-Consumer deserialisieren.

### <a name="java-amqp-utf-8-string-producer"></a>Java-AMQP-Producer: UTF-8-Zeichenfolge
```java
final String exampleJson = "{\"name\":\"John\", \"number\":9001}";
final EventData ed = EventData.create(exampleJson.getBytes(StandardCharsets.UTF_8));
```

### <a name="java-amqp-utf-8-string-consumer"></a>Java-AMQP-Consumer: UTF-8-Zeichenfolge
```java
EventData ed = /* receive event */
String receivedJson = new String(ed.getBytes(), StandardCharsets.UTF_8);
```

### <a name="c-net-utf-8-string-producer"></a>C# .NET-Producer: UTF-8-Zeichenfolge
```csharp
string exampleJson = "{\"name\":\"John\", \"number\":9001}";
EventData working = new EventData(Encoding.UTF8.GetBytes(exampleJson));
```

### <a name="c-net-utf-8-string-consumer"></a>C# .NET-Consumer: UTF-8-Zeichenfolge
```csharp
EventData ed = /* receive event */

// getting the event body bytes depends on which .NET client is used
byte[] bodyBytes = ed.Body.Array;  // Microsoft Azure Event Hubs Client for .NET
// byte[] bodyBytes = ed.GetBytes(); // Microsoft Azure Service Bus

string receivedJson = Encoding.UTF8.GetString(bodyBytes);
```

Da Kafka eine Open-Source-Plattform ist, kann der Anwendungsentwickler die Implementierung aller Serialisierungs- und Deserialisierungsmodule überprüfen und Code implementieren, mit dem eine kompatible Bytesequenz auf AMQP-Seite generiert oder verarbeitet wird.

## <a name="event-user-properties"></a>Benutzereigenschaften für Ereignisse

Vom Benutzer festgelegte Eigenschaften können von AMQP-Clients (in den Microsoft-AMQP-Clients als Eigenschaften bezeichnet) sowie von Kafka (hier als Header bezeichnet) festgelegt und abgerufen werden. HTTPS-Sender können Benutzereigenschaften in einem Ereignis festlegen, indem sie sie als HTTP-Header im POST-Vorgang angeben. In Kafka werden jedoch sowohl Ereignistexte als auch Ereignisheaderwerte als Bytesequenzen verarbeitet. In AMQP-Clients weisen Eigenschaftswerte dagegen Typen auf, die durch Codierung der Eigenschaftswerte entsprechend dem AMQP-Typsystem kommuniziert werden.

HTTPS ist ein besonderer Fall. Zum Zeitpunkt des Sendevorgangs werden alle Eigenschaftswerte als UTF-8-Text dargestellt. Der Event Hubs-Dienst führt eine begrenzte Anzahl von Interpretationen durch, um entsprechende Eigenschaftswerte in AMQP-codierte 32-Bit- und 64-Bit-Integer mit Vorzeichen, 64-Bit-Gleitkommazahlen und boolesche Werte zu konvertieren. Alle Eigenschaftswerte, die keinem dieser Typen entsprechen, werden als Zeichenfolge behandelt.

Die Mischung dieser Ansätze zur Eigenschaftstypisierung bedeutet, dass ein Kafka-Consumer die AMQP-codierte Rohbytesequenz sieht, einschließlich der AMQP-Typinformationen. Ein AMQP-Consumer sieht dagegen die vom Kafka-Producer gesendete nicht typisierte Bytesequenz, die von der Anwendung interpretiert werden muss.

Verwenden Sie für Kafka-Consumer, die Eigenschaften von AMQP- oder HTTPS-Producern empfangen, die AmqpDeserializer-Klasse, die nach dem Modell anderer Deserialisierungsmodule im Kafka-Ökosystem erstellt wurde. Die Typinformationen in den AMQP-codierten Bytesequenzen werden so interpretiert, dass die Datenbytes in einen Java-Typ deserialisiert werden.

Als bewährte Methode wird empfohlen, in Nachrichten, die über AMQP oder HTTPS gesendet werden, eine Eigenschaft einzufügen. Anhand dieser Eigenschaft kann der Kafka-Consumer bestimmen, ob für Headerwerte eine AMQP-Deserialisierung erforderlich ist. Der Wert der Eigenschaft ist nicht wichtig. Es ist lediglich ein bekannter Name erforderlich, den der Kafka-Consumer in der Liste der Header suchen und sein Verhalten entsprechend anpassen kann.

### <a name="amqp-to-kafka-part-1-create-and-send-an-event-in-c-net-with-properties"></a>AMQP in Kafka, Teil 1: Erstellen und Senden eines Ereignisses in C# (.NET) mit Eigenschaften
```csharp
// Create an event with properties "MyStringProperty" and "MyIntegerProperty"
EventData working = new EventData(Encoding.UTF8.GetBytes("an event body"));
working.Properties.Add("MyStringProperty", "hello");
working.Properties.Add("MyIntegerProperty", 1234);

// BEST PRACTICE: include a property which indicates that properties will need AMQP deserialization
working.Properties.Add("AMQPheaders", 0);
```

### <a name="amqp-to-kafka-part-2-use-amqpdeserializer-to-deserialize-those-properties-in-a-kafka-consumer"></a>AMQP in Kafka, Teil 2: Verwenden von AmqpDeserializer zum Deserialisieren dieser Eigenschaften in einem Kafka-Consumer
```java
final AmqpDeserializer amqpDeser = new AmqpDeserializer();

ConsumerRecord<Long, Bytes> cr = /* receive event */
final Header[] headers = cr.headers().toArray();

final Header headerNamedMyStringProperty = /* find header with key "MyStringProperty" */
final Header headerNamedMyIntegerProperty = /* find header with key "MyIntegerProperty" */
final Header headerNamedAMQPheaders = /* find header with key "AMQPheaders", or null if not found */

// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // The deserialize() method requires no prior knowledge of a property's type.
    // It returns Object and the application can check the type and perform a cast later.
    Object propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyStringProperty.value());
    if (propertyOfUnknownType instanceof String) {
        final String propertyString = (String)propertyOfUnknownType;
        // do work here
    }
    propertyOfUnknownType = amqpDeser.deserialize("topicname", headerNamedMyIntegerProperty.value());
    if (propertyOfUnknownType instanceof Integer) {
        final Integer propertyInt = (Integer)propertyOfUnknownType;
        // do work here
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Wenn die Anwendung den erwarteten Typ für eine Eigenschaft kennt, stehen Deserialisierungsmethoden zur Verfügung, die anschließend keine Umwandlung erfordern, jedoch einen Fehler auslösen, wenn die Eigenschaft nicht den erwarteten Typ aufweist.

### <a name="amqp-to-kafka-part-3-a-different-way-of-using-amqpdeserializer-in-a-kafka-consumer"></a>AMQP in Kafka, Teil 3: andere Möglichkeit der Verwendung von AmqpDeserializer in einem Kafka-Consumer
```java
// BEST PRACTICE: detect whether AMQP deserialization is needed
if (headerNamedAMQPheaders != null) {
    // Property "MyStringProperty" is expected to be of type string.
    try {
        final String propertyString = amqpDeser.deserializeString(headerNamedMyStringProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a string
    }

    // Property "MyIntegerProperty" is expected to be a signed integer type.
    // The method returns long because long can represent the value range of all AMQP signed integer types.
    try {
        final long propertyLong = amqpDeser.deserializeSignedInteger(headerNamedMyIntegerProperty.value());
        // do work here
    }
    catch (IllegalArgumentException e) {
        // property was not a signed integer
    }
} else {
    /* event sent via Kafka, interpret header values the Kafka way */
}
```

Die andere Richtung ist komplizierter, da die von einem Kafka-Producer festgelegten Header von einem AMQP-Consumer immer als Rohbytes gesehen werden (vom Typ „org.apache.qpid.proton.amqp.Binary“ für den Microsoft Azure Event Hubs-Client für Java oder `System.Byte[]` für Microsoft .NET-AMQP-Clients). Der einfachste Weg ist die Verwendung eines der von Kafka angegebenen Serialisierungsmodule, um die Bytes für die Headerwerte auf Seite des Kafka-Producers zu generieren und dann einen kompatiblen Deserialisierungscode auf Seite des AMQP-Consumers zu schreiben.

Wie bei der Umwandlung von AMQP in Kafka empfiehlt sich als bewährte Methode, in Nachrichten, die über Kafka gesendet werden, eine Eigenschaft einzufügen. Anhand der Eigenschaft kann der AMQP-Consumer bestimmen, ob für Headerwerte eine Deserialisierung erforderlich ist. Der Wert der Eigenschaft ist nicht wichtig. Es ist lediglich ein bekannter Name erforderlich, den der AMQP-Consumer in der Liste der Header suchen und sein Verhalten entsprechend anpassen kann. Wenn der Kafka-Producer nicht geändert werden kann, ist es auch möglich, in der Consumer-Anwendung zu überprüfen, ob der Eigenschaftswert einen binären oder Bytetyp aufweist, und zu versuchen, die Deserialisierung basierend auf dem Typ durchzuführen.

### <a name="kafka-to-amqp-part-1-create-and-send-an-event-from-kafka-with-properties"></a>Kafka in AMQP, Teil 1: Erstellen und Senden eines Ereignisses über Kafka mit Eigenschaften
```java
final String topicName = /* topic name */
final ProducerRecord<Long, String> pr = new ProducerRecord<Long, String>(topicName, /* other arguments */);
final Headers h = pr.headers();

// Set headers using Kafka serializers
IntegerSerializer intSer = new IntegerSerializer();
h.add("MyIntegerProperty", intSer.serialize(topicName, 1234));

LongSerializer longSer = new LongSerializer();
h.add("MyLongProperty", longSer.serialize(topicName, 5555555555L));

ShortSerializer shortSer = new ShortSerializer();
h.add("MyShortProperty", shortSer.serialize(topicName, (short)22222));

FloatSerializer floatSer = new FloatSerializer();
h.add("MyFloatProperty", floatSer.serialize(topicName, 1.125F));

DoubleSerializer doubleSer = new DoubleSerializer();
h.add("MyDoubleProperty", doubleSer.serialize(topicName, Double.MAX_VALUE));

StringSerializer stringSer = new StringSerializer();
h.add("MyStringProperty", stringSer.serialize(topicName, "hello world"));

// BEST PRACTICE: include a property which indicates that properties will need deserialization
h.add("RawHeaders", intSer.serialize(0));
```

### <a name="kafka-to-amqp-part-2-manually-deserialize-those-properties-in-c-net"></a>Kafka in AMQP, Teil 2: manuelles Deserialisieren dieser Eigenschaften in C# (.NET)
```csharp
EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.Properties.ContainsKey("RawHeaders"))
{
    // Kafka serializers send bytes in big-endian order, whereas .NET on x86/x64 is little-endian.
    // Therefore it is frequently necessary to reverse the bytes before further deserialization.

    byte[] rawbytes = ed.Properties["MyIntegerProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    int myIntegerProperty = BitConverter.ToInt32(rawbytes, 0);

    rawbytes = ed.Properties["MyLongProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    long myLongProperty = BitConverter.ToInt64(rawbytes, 0);

    rawbytes = ed.Properties["MyShortProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    short myShortProperty = BitConverter.ToInt16(rawbytes, 0);

    rawbytes = ed.Properties["MyFloatProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    float myFloatProperty = BitConverter.ToSingle(rawbytes, 0);

    rawbytes = ed.Properties["MyDoubleProperty"] as System.Byte[];
    if (BitConverter.IsLittleEndian)
    {
            Array.Reverse(rawbytes);
    }
    double myDoubleProperty = BitConverter.ToDouble(rawbytes, 0);

    rawbytes = ed.Properties["MyStringProperty"] as System.Byte[];
string myStringProperty = Encoding.UTF8.GetString(rawbytes);
}
```

### <a name="kafka-to-amqp-part-3-manually-deserialize-those-properties-in-java"></a>Kafka in AMQP, Teil 3: manuelles Deserialisieren dieser Eigenschaften in Java
```java
final EventData ed = /* receive event */

// BEST PRACTICE: detect whether manual deserialization is needed
if (ed.getProperties().containsKey("RawHeaders")) {
    byte[] rawbytes =
        ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyIntegerProperty")).getArray();
    int myIntegerProperty = 0;
    for (byte b : rawbytes) {
        myIntegerProperty <<= 8;
        myIntegerProperty |= ((int)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyLongProperty")).getArray();
    long myLongProperty = 0;
    for (byte b : rawbytes) {
        myLongProperty <<= 8;
        myLongProperty |= ((long)b & 0x00FF);
    }

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyShortProperty")).getArray();
    short myShortProperty = (short)rawbytes[0];
    myShortProperty <<= 8;
    myShortProperty |= ((short)rawbytes[1] & 0x00FF);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyFloatProperty")).getArray();
    int intbits = 0;
    for (byte b : rawbytes) {
        intbits <<= 8;
        intbits |= ((int)b & 0x00FF);
    }
    float myFloatProperty = Float.intBitsToFloat(intbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyDoubleProperty")).getArray();
    long longbits = 0;
    for (byte b : rawbytes) {
        longbits <<= 8;
        longbits |= ((long)b & 0x00FF);
    }
    double myDoubleProperty = Double.longBitsToDouble(longbits);

    rawbytes = ((org.apache.qpid.proton.amqp.Binary)ed.getProperties().get("MyStringProperty")).getArray();
String myStringProperty = new String(rawbytes, StandardCharsets.UTF_8);
}
```

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Daten an Kafka-fähige Event Hubs gestreamt werden können, ohne dass Sie Protokollclients ändern oder eigene Cluster betreiben müssen. Weitere Informationen zu Event Hubs und Event Hubs für Kafka finden Sie in folgenden Artikeln:  

* [Informationen zu Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informationen zu Event Hubs für Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Weitere Beispiele zu Event Hubs für Kafka auf GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
* Verwenden Sie [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) zum [Streamen von Ereignissen aus der lokalen Kafka-Instanz an Kafka-fähige Event Hubs in der Cloud](event-hubs-kafka-mirror-maker-tutorial.md).
* Erfahren Sie, wie Sie [native Kafka-Anwendungen](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md) oder [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md) zum Streamen an Kafka-fähige Event Hubs verwenden.
