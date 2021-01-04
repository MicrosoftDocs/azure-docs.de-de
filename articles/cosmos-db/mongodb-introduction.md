---
title: Einführung in die Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie Azure Cosmos DB verwenden können, um große Datenmengen mithilfe der Azure Cosmos DB-API für MongoDB zu speichern und abzufragen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 11/25/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 8205ecab3abfc7a944c12db1aca2bf594f6cd98f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349451"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) ist ein global verteilter Datenbankdienst von Microsoft mit mehreren Modellen für unternehmenskritische Anwendungen. Azure Cosmos DB bietet [sofort einsetzbare globale Verteilung](distribute-data-globally.md), [flexible Skalierung von Durchsatz und Speicher](partitioning-overview.md) weltweit, Wartezeiten im einstelligen Millisekundenbereich beim 99. Perzentil sowie garantierte Hochverfügbarkeit, gestützt durch [branchenführende Vereinbarungen zum Servicelevel](https://azure.microsoft.com/support/legal/sla/cosmos-db/) (SLAs, Service Level Agreements). Azure Cosmos DB [indiziert automatisch Daten](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), sodass Sie sich nicht mit der Schema- und Indexverwaltung befassen müssen. Es unterstützt mehrere Datenmodelle – Dokumente, Schlüssel-Werte, Diagramme und spaltenorientierte Datenmodelle. Der Azure Cosmos DB-Dienst implementiert Wire Protocols (Übertragungsprotokolle) für gängige NoSQL-APIs, einschließlich Cassandra, MongoDB, Gremlin und Azure Table Storage. Dies ermöglicht es Ihnen, mithilfe vertrauter NoSQL-Clienttreiber und -tools mit Ihrer Cosmos-Datenbank zu interagieren.

> [!NOTE]
> Der [serverlose Kapazitätsmodus](serverless.md) ist nun in der MongoDB-API von Azure Cosmos DB verfügbar.

## <a name="wire-protocol-compatibility"></a>Wire Protocol-Kompatibilität

Azure Cosmos DB implementiert das Wire Protocol für MongoDB. Diese Implementierung ermöglicht eine transparente Kompatibilität mit nativen MongoDB-Client-SDKs, -Treibern und -Tools. Azure Cosmos DB hostet die MongoDB-Datenbank-Engine. Die Details zu den von MongoDB unterstützten Features finden Sie hier: 
- [API von Azure Cosmos DB für Version 3.6 der Mongo DB-Engine](mongodb-feature-support-36.md)
- [API von Azure Cosmos DB für Version 3.2 der Mongo DB-Engine](mongodb-feature-support.md)

Die mithilfe der Azure Cosmos DB-API für MongoDB erstellten neuen Konten sind standardmäßig mit MongoDB Wire Protocol Version 3.6 kompatibel. Alle MongoDB-Clienttreiber, die diese Protokollversionen unterstützen, sollten nativ eine Verbindung mit Cosmos DB herstellen können.

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="MongoDB-API von Azure Cosmos DB" border="false":::

## <a name="key-benefits"></a>Hauptvorteile

[hier](introduction.md) werden die wichtigsten Vorteile von Cosmos DB als vollständig verwaltete, global verteilte DaaS (Database-as-a-Service) beschrieben. Zudem bietet Cosmos DB durch die native Implementierung der Wire Protocols von gängigen NoSQL-APIs die folgenden Vorteile:

* Einfaches Migrieren Ihrer Anwendung zu Cosmos DB unter Beibehaltung wesentlicher Teile Ihrer Anwendungslogik.
* Aufrechterhalten der Portierbarkeit und Unabhängigkeit von Cloudanbietern Ihrer Anwendung.
* Verfügbarkeit von branchenführenden, finanziell abgesicherten SLAs für die gängigen NoSQL-APIs, die von Cosmos DB unterstützt werden.
* Elastischen Skalieren des bereitgestellten Durchsatzes und Speichers für Ihre Cosmos-Datenbanken basierend auf Ihrem Bedarf. Sie zahlen nur für den benötigten Durchsatz und Speicher. Dies führt zu erheblichen Kosteneinsparungen.
* Globale, sofort einsatzbereite Verteilung mit Replikation von Schreibvorgängen in mehreren Regionen.

## <a name="cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API für MongoDB

Folgen Sie den Anweisungen in den Schnellstarts, um ein Azure Cosmos-Konto zu erstellen und Ihre vorhandene MongoDB-Anwendung zur Verwendung von Azure Cosmos DB zu migrieren oder eine neue Anwendung zu erstellen:

* [Migrieren einer vorhandenen Node.js-MongoDB-Web-App](create-mongodb-nodejs.md)
* [Erstellen einer Web-App mit der Azure Cosmos DB-API für MongoDB und dem .NET SDK](create-mongodb-dotnet.md)
* [Erstellen einer Konsolen-App mit der Azure Cosmos DB-API für MongoDB und dem Java SDK](create-mongodb-java.md)

## <a name="next-steps"></a>Nächste Schritte

Hier einige Hinweise, um Ihnen den Einstieg zu erleichtern:

* Im Tutorial [Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB](connect-mongodb-account.md) erfahren Sie, wie Sie Ihre Kontoverbindungszeichenfolge abrufen.
* Im Tutorial zum [Verwenden von Studio 3T mit Azure Cosmos DB](mongodb-mongochef.md) erfahren Sie, wie Sie eine Verbindung zwischen Ihrer Cosmos-Datenbank und einer MongoDB-App in Studio 3T erstellen.
* Folgen Sie den Anweisungen im Tutorial zum [Importieren von MongoDB-Daten in Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json), um Ihre vorhandenen Daten in eine Cosmos-Datenbank zu importieren.
* Stellen Sie mithilfe von [Robo 3T](mongodb-robomongo.md) eine Verbindung mit einem Cosmos-Konto her.
* Erfahren Sie, wie Sie [Leseeinstellungen für global verteilte Apps konfigurieren](../cosmos-db/tutorial-global-distribution-mongodb.md).
* Die Lösungen für häufige Fehler finden Sie in unserem [Leitfaden zur Problembehandlung](mongodb-troubleshoot.md).


<sup>Hinweis: Dieser Artikel beschreibt ein Feature von Azure Cosmos DB, das Wire Protocol-Kompatibilität mit MongoDB-Datenbanken bietet. Microsoft führt keine MongoDB-Datenbanken aus, um diesen Dienst bereitzustellen. Azure Cosmos DB ist kein Partner von MongoDB, Inc.</sup>