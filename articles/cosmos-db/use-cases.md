---
title: Allgemeine Anwendungsfälle und Szenarien für Azure Cosmos DB
description: 'Erfahren Sie mehr über die fünf wichtigsten Anwendungsfälle für Azure Cosmos DB: benutzergenerierte Inhalte, Ereignisprotokollierung, Katalogdaten, Benutzereinstellungsdaten und das Internet der Dinge (Internet of Things, IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 04de6dce6cbab4dc0716ae841707b1d61d6bc375
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93073756"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Häufige Anwendungsfälle für Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dieser Artikel bietet eine Übersicht über verschiedene häufige Anwendungsfälle für Azure Cosmos DB.  Die Empfehlungen in diesem Artikel dienen als Ausgangspunkt für die Entwicklung Ihrer Anwendung mit Cosmos DB.

Nach Lesen dieses Artikels können Sie die folgenden Fragen beantworten: 

* Was sind häufige Anwendungsfälle für Azure Cosmos DB?
* Welche Vorteile bietet die Verwendung von Azure Cosmos DB für Verkaufsanwendungen?
* Welche Vorteile bietet die Verwendung von Azure Cosmos DB als Datenspeicher für Systeme für das Internet der Dinge (Internet of Things, IoT)?
* Welche Vorteile bietet die Verwendung von Azure Cosmos DB für Web- und Mobilanwendungen?

## <a name="introduction"></a>Einführung

[Azure Cosmos DB](../cosmos-db/introduction.md) ist die schnelle NoSQL-Datenbank von Microsoft mit offenen APIs für jede Größenordnung. Mit diesem Dienst können Kunden Durchsatz und Speicher in beliebig vielen geografischen Regionen bereitstellen und flexibel skalieren. Azure Cosmos DB ist der erste weltweit verteilte Datenbankdienst auf dem Markt, der umfassende [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/cosmos-db/) bereitstellt, die Durchsatz, Latenz, Verfügbarkeit und Konsistenz beinhalten.

Azure Cosmos DB ist eine weltweit verteilte Datenbank mit mehreren Modellen, die in einer Vielzahl von Anwendungen und Anwendungsfällen verwendet wird. Sie eignet sich für alle [serverlosen](https://azure.com/serverless) Anwendungen, die kurze Antwortzeiten im Millisekundenbereich benötigen sowie schnell und weltweit skaliert werden müssen. Es unterstützt mehrere Datenmodelle (Schlüssel-Wert, Dokumente, Graphen und Spalten) sowie viele APIs für den nativen und erweiterbaren Datenzugriff, einschließlich [Azure Cosmos DB-API für MongoDB](mongodb-introduction.md), [SQL-API](./introduction.md), [Gremlin-API](graph-introduction.md) und [Tabellen-API](table-introduction.md). 

Aufgrund der folgenden Attribute eignet sich Azure Cosmos DB besonders gut für Hochleistungsanwendungen mit weltweiten Zielen.

* Azure Cosmos DB partitioniert Ihre Daten systemintern, um Hochverfügbarkeit und Skalierbarkeit zu gewährleisten. Azure Cosmos DB bietet für alle Konten mit einer einzelnen Region und für alle Konten mit mehreren Regionen und gelockerter Konsistenz eine Garantie von 99,99 Prozent in puncto Verfügbarkeit, Durchsatz und geringer Wartezeit und garantiert eine Leseverfügbarkeit von 99,999 Prozent für alle Datenbankkonten mit mehreren Regionen.
* Azure Cosmos DB verfügt über SSD-gestützten Speicher mit niedriger Latenz und kurzen Antwortzeiten im Millisekundenbereich.
* Da Azure Cosmos DB Konsistenzebenen wie „eventual (letztlich)“, „consistent prefix (Präfixkonsistenz)“, „session (Sitzung)“ und „bounded-staleness (begrenzte Veraltung)“ unterstützt, werden vollständige Flexibilität und ein optimales Preis-Leistungsverhältnis ermöglicht. Kein Datenbankdienst bietet so viel Flexibilität bei der Ebenenkonsistenz wie Azure Cosmos DB. 
* Azure Cosmos DB weist ein flexibles, datenfreundliches Preismodell auf, das Speicher und Durchsatz unabhängig voneinander misst.
* Mit dem reservierten Durchsatzmodell in Azure Cosmos DB können Sie mit der Anzahl von Lese-und Schreibvorgängen anstelle von CPU-Leistung, Arbeitsspeicher oder IOPS-Wert der verwendeten Hardware arbeiten.
* Das Design von Azure Cosmos DB ermöglicht Ihnen auch die Anpassung an sehr hohe Abfragevolumen (Milliarden Abfragen pro Tag).

Diese Attribute sind vorteilhaft im Hinblick auf Web-, Mobil-, Gaming- und IoT-Anwendungen, die niedrige Antwortzeiten benötigen und große Mengen von Lese- und Schreibvorgängen bewältigen müssen.

## <a name="iot-and-telematics"></a>IoT und Telematik

IoT-Anwendungsfälle weisen einige gemeinsame Muster hinsichtlich der Erfassung, Verarbeitung und Speicherung von Daten auf.  Erstens müssen diese Systeme große Mengen von Daten aus Gerätesensoren verschiedenster Gebietsschemata erfassen. Zum Zweiten verarbeiten und analysieren diese Systeme Daten, um in Echtzeit Erkenntnisse aus diesen Daten zu gewinnen. Die Daten werden dann für die Batch-Analyse im Cold Storage archiviert. Microsoft Azure bietet umfassende Dienste, die für IoT-Anwendungsfälle eingesetzt werden können. Zu diesen Diensten gehören Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight und Power BI. 

:::image type="content" source="./media/use-cases/iot.png" alt-text="IoT-Referenzarchitektur für Azure Cosmos DB" border="false":::

Azure Event Hubs bietet einen hohen Durchsatz bei geringer Latenz und kann daher große Datenmengen erfassen und verarbeiten. Erfasste Daten, die für Erkenntnisse in Echtzeit verarbeitet werden müssen, können zur Echtzeitanalyse an Azure Stream Analytics weitergeleitet werden. Daten können für Ad-hoc-Abfragen in Azure Cosmos DB geladen werden. Sobald die Daten in Azure Cosmos DB geladen sind, können sie abgefragt werden. Darüber hinaus können neue Daten und Änderungen an vorhandenen Daten im Änderungsfeed gelesen werden. Beim Änderungsfeed handelt es sich um ein beständiges und nur zum Anfügen bestimmtes Protokoll, in dem Änderungen an Cosmos-Containern in sequenzieller Reihenfolge gespeichert werden. Alle Daten oder nur die Änderungen an Daten in Azure Cosmos DB können im Rahmen von Echtzeitanalysen als Referenzdaten verwendet werden. Darüber hinaus können Daten durch Verknüpfen von Azure Cosmos DB-Daten mit HDInsight für Pig-, Hive- oder Map/Reduce-Aufträge weiter optimiert und verarbeitet werden.  Die optimierten Daten werden anschließend zur Berichterstellung wieder in Azure Cosmos DB geladen.   

Eine Beispiellösung für das Internet der Dinge unter Verwendung von Azure Cosmos DB, EventHubs und Storm finden Sie im [Beispielrepository „hdinsight-storm“ in GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Weitere Informationen zu den Azure-Angeboten für das Internet der Dinge finden Sie unter [Ihr eigenes Internet der Dinge](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Einzelhandel und Marketing
Azure Cosmos DB wird umfassend für die eigenen E-Commerce-Plattformen von Microsoft verwendet, auf denen der Windows Store und XBox Live ausgeführt werden. Der Dienst wird darüber hinaus im Einzelhandel zum Speichern von Katalogdaten und für die Ereignisherkunftsermittlung in Bestellabwicklungspipelines eingesetzt.

Zu den Anwendungsfällen für Katalogdaten gehören das Speichern und Abfragen eines Satzes von Attributen für Entitäten wie beispielsweise Personen, Orte und Produkte. Einige Beispiele für Katalogdaten sind Benutzerkonten, Produktkataloge, IoT-Geräteregistrierungen und Stücklistensysteme. Attribute für diese Daten können variieren und sich im Laufe der Zeit ändern, um geänderte Anwendungsanforderungen zu erfüllen.

Betrachten Sie als Beispiel einen Produktkatalog für einen Automobilzulieferer. Jedes Teil verfügt zusätzlich zu den Attributen, die allen Teilen gemeinsam sind, über eigene Attribute. Darüber hinaus können sich Attribute für ein bestimmtes Teil ändern, wenn ein neues Modell auf den Markt kommt. Azure Cosmos DB unterstützt flexible Schemas sowie hierarchische Daten und eignet sich daher gut zum Speichern von Produktkatalogdaten.

:::image type="content" source="./media/use-cases/product-catalog.png" alt-text="Einzelhandelskatalog-Referenzarchitektur für Azure Cosmos DB" border="false":::

Azure Cosmos DB wird häufig für die Ereignisherkunftsermittlung zur Unterstützung ereignisgesteuerter Architekturen mithilfe der Funktion für den [Änderungsfeed](change-feed.md) verwendet. Der Änderungsfeed ermöglicht Downstream-Microservices das zuverlässige und inkrementelle Lesen von Einfügungen und Updates (etwa Bestellereignisse) für eine Azure Cosmos DB-Instanz. Diese Funktionalität kann dazu genutzt werden, einen persistenten Ereignisspeicher als Nachrichtenbroker für Ereignisse mit wechselndem Status bereitzustellen und Bestellverarbeitungsworkflows zwischen zahlreichen Microservices (die als [serverlose Azure-Funktionen](https://azure.com/serverless) implementiert werden können) zu steuern.

:::image type="content" source="./media/use-cases/event-sourcing.png" alt-text="Referenzarchitektur für eine Azure Cosmos DB-Bestellpipeline" border="false":::

Darüber hinaus können Daten in Azure Cosmos DB für Big Data-Analysen über Apache Spark-Aufträge in HDInsight integriert werden. Ausführliche Informationen zum Spark-Connector für Azure Cosmos DB finden Sie unter [Beschleunigen der Big Data-Echtzeitanalyse mit dem Spark-Connector für Azure Cosmos DB](spark-connector.md).

## <a name="gaming"></a>Spiele
Die Datenbankebene ist eine wesentliche Komponente von Gaming-Anwendungen. Moderne Spiele führen Grafikberechnungen auf Clients (mobile Endgeräte/Konsolen) durch, verlassen sich aber auf die Cloud, die benutzerdefinierten und personalisierten Inhalte wie In-Game Statistiken, Integration von sozialen Medien sowie Highscore-Listen zur Verfügung stellt. Spiele erfordern häufig Wartezeiten von einzelnen Millisekunden für Lese- und Schreibvorgänge, um ein ansprechendes Spielerlebnis bereitzustellen. Eine Spieldatenbank muss schnell sein und enorme Spitzen bei Anforderungsraten während der Einführung neuer Spiele und Featureupdates verarbeiten können.

Azure Cosmos DB wird von Spielen wie [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/), von [Next Games](https://www.nextgames.com/) und von [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) verwendet. Azure Cosmos DB bietet Spieleentwicklern die folgenden Vorteile:

* Azure Cosmos DB ermöglicht eine elastische zentrale Hoch- oder Herunterskalierung der Leistung. So können Spiele die Profile und Statistiken von gleichzeitig aktiven Spielern mit nur einem API-Aufruf aktualisieren – ganz egal, ob es sich um mehrere Dutzend oder mehrere Millionen Spieler handelt.
* Azure Cosmos DB unterstützt Lese- und Schreibvorgänge in Millisekunden, um jede Verzögerung während des Spiels zu vermeiden.
* Die automatische Indizierung von Azure Cosmos DB ermöglicht die Filterung mehrerer verschiedener Eigenschaften in Echtzeit, also z.B. Spieler durch ihre internen Player-, GameCenter-, Facebook- oder Google-IDs zu lokalisieren oder sie auf Grundlage der Mitgliedschaft der Spielcharaktere in einer Gilde abzufragen. Dies ist möglich, ohne eine komplexe Indizierung oder Sharding-Infrastruktur erstellen zu müssen.
* Soziale Funktionen, einschließlich Chat-Nachrichten im Spiel, Mitgliedschaften in Gilden, bewältigte Aufgaben, Highscore-Listen und soziale Graphen, sind einfacher mit einem flexiblen Schema zu implementieren.
* Azure Cosmos DB benötigt als verwaltete Platform-as-a-Service (PaaS) minimalen Einrichtungs- und Verwaltungsaufwand, um schnelle Iteration zu ermöglichen und die Markteinführungszeit zu verkürzen.

:::image type="content" source="./media/use-cases/gaming.png" alt-text="Gaming-Referenzarchitektur für Azure Cosmos DB" border="false":::

## <a name="web-and-mobile-applications"></a>Webanwendungen und mobile Anwendungen
Azure Cosmos DB wird häufig in Web- und Mobilanwendungen verwendet und eignet sich gut für die Modellierung sozialer Interaktionen, die Integration in Drittanbieterdienste sowie zum Erstellen umfangreicher Personalisierungen. Mithilfe der Cosmos DB-SDKs können umfangreiche iOS- und Android-Anwendungen mit dem beliebten [Xamarin-Framework](mobile-apps-with-xamarin.md) erstellt werden.  

### <a name="social-applications"></a>Soziale Anwendungen
Ein häufiger Anwendungsfall für Azure Cosmos DB ist die Speicherung und Abfrage von benutzergenerierten Inhalten für Web- und Mobilanwendungen sowie Anwendungen für soziale Medien. Einige Beispiele für benutzergenerierte Inhalte sind Chatsitzungen, Tweets, Blogbeiträge, Bewertungen und Kommentare. Benutzergenerierte Inhalte in Anwendungen für soziale Medien sind eine Mischung aus Freitext, Eigenschaften, Tags und Beziehungen, die nicht durch eine starre Struktur begrenzt sind. Inhalte wie Chats, Kommentare und Beiträge können in Cosmos DB gespeichert werden, ohne dass hierzu Transformationen oder komplexe ORM-Ebenen (Object Relational Mapping) erforderlich sind.  Dateneigenschaften können beim Durchlaufen des Anwendungscodes ganz einfach hinzugefügt oder geändert werden, um die jeweiligen Anforderungen zu erfüllen. Auf diese Weise wird die Entwicklung erheblich beschleunigt.  

Anwendungen, die in soziale Drittanbieter-Netzwerke integriert werden, müssen auf Schemaänderungen dieser Netzwerke reagieren. Da Daten in Cosmos DB standardmäßig und automatisch indiziert werden, können Daten jederzeit abgefragt werden. Daher verfügen diese Anwendungen über die Flexibilität, Projektionen entsprechend ihren jeweiligen Anforderungen abzurufen.

Viele soziale Anwendungen werden auf globaler Ebene ausgeführt und können nicht vorhersagbare Nutzungsmuster aufweisen. Flexibilität bei der Skalierung des Datenspeichers ist von grundlegender Bedeutung, da die Anwendungsschicht in Abhängigkeit von der Nutzung skaliert werden muss.  Sie können den Speicher aufskalieren, indem Sie einem Cosmos DB-Konto weitere Datenpartitionen hinzufügen.  Darüber hinaus können Sie auch weitere Cosmos DB-Konten über mehrere Regionen hinweg erstellen. Informationen zur regionalen Verfügbarkeit von Cosmos DB-Diensten finden Sie unter [Azure-Region](https://azure.microsoft.com/regions/#services).

:::image type="content" source="./media/use-cases/apps-with-global-reach.png" alt-text="Diagramm der Web-App-Referenzarchitektur für Azure Cosmos DB" border="false":::

### <a name="personalization"></a>Personalisierung
Heute sind die modernen Anwendungen mit komplexen Ansichten und Funktionalitäten verbunden. Diese sind üblicherweise dynamisch und auf die Einstellungen bzw. die Stimmung des Benutzers oder auf Brandinganforderungen ausgerichtet. Daher müssen Anwendungen personalisierte Einstellungen effektiv abrufen können, um Benutzeroberflächenelemente und Funktionalitäten schnell darzustellen. 

JSON ist ein von Cosmos DB unterstütztes effektives Format zur Darstellung von Daten für das Benutzeroberflächenlayout. Es ist nicht nur unkompliziert, sondern kann auch problemlos von JavaScript interpretiert werden. Cosmos DB bietet optimierbare Konsistenzebenen, die schnelle Lesevorgänge sowie Schreibvorgänge mit geringer Latenz ermöglichen. Daher ist die Speicherung von Daten für das Benutzeroberflächenlayout einschließlich personalisierter Einstellungen als JSON-Dokument in Cosmos DB eine effektive Möglichkeit zur Übertragung dieser Daten.

:::image type="content" source="./media/use-cases/personalization.png" alt-text="Web-App-Referenzarchitektur für Azure Cosmos DB" border="false":::

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie für Ihre ersten Schritte mit Azure Cosmos DB die Schritte in den [Schnellstart](create-sql-api-dotnet.md)-Dokumenten aus. Darin werden das Erstellen eines Kontos und die ersten Schritte mit Cosmos DB beschrieben.

* Weitere Informationen zu Kunden, die Azure Cosmos DB verwenden, finden Sie auf der Seite mit [Fallstudien](https://azure.microsoft.com/case-studies/?service=cosmos-db).