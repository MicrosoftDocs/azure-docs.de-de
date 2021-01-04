---
title: Azure Cosmos DB-BulkExecutor-Bibliothek – Übersicht
description: Ausführen von Massenvorgängen in Azure Cosmos DB über Massenimport- und Massenaktualisierungs-APIs, die von der BulkExecutor-Bibliothek zur Verfügung gestellt werden.
author: tknandu
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/28/2019
ms.author: ramkris
ms.reviewer: sngun
ms.openlocfilehash: 211fc85f97069fcf3251048a074d625e777f8e7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100472"
---
# <a name="azure-cosmos-db-bulk-executor-library-overview"></a>Azure Cosmos DB-BulkExecutor-Bibliothek – Übersicht
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Azure Cosmos DB ist ein schneller, flexibler und global verteilter Datenbankdienst, der so konzipiert ist, dass er sich elastisch aufskalieren lässt und damit Folgendes unterstützt: 

* Hohe Durchsätze für Lese- und Schreibvorgänge (Millionen von Vorgängen pro Sekunde).  
* Speichern großer Mengen von Transaktions- und Vorgangsdaten (Hunderte von Terabytes oder sogar noch mehr) mit vorhersagbarer Latenz im Millisekundenbereich.  

Die BulkExecutor-Bibliothek hilft Ihnen, diesen massiven Durchsatz und Speicherplatz zu nutzen. Die BulkExecutor -Bibliothek ermöglicht es Ihnen, Massenvorgänge in Azure Cosmos DB durch Massenimport- und Massenaktualisierungs-APIs durchzuführen. In den folgenden Abschnitten erfahren Sie mehr über die Funktionen der BulkExecutor-Bibliothek. 

> [!NOTE] 
> Zurzeit unterstützt die BulkExecutor-Bibliothek Import- und Updatevorgänge, und die Bibliothek wird nur von Azure Cosmos DB-SQL API- und Gremlin API-Konten unterstützt.

> [!IMPORTANT]
> Die Bulk Executor-Bibliothek wird für [serverlose](serverless.md) Konten derzeit nicht unterstützt. Wir empfehlen Ihnen, unter .NET die [Unterstützung von Massenvorgängen](https://devblogs.microsoft.com/cosmosdb/introducing-bulk-support-in-the-net-sdk/) zu verwenden, die in Version 3 des SDK verfügbar ist.
 
## <a name="key-features-of-the-bulk-executor-library"></a>Wichtigste Funktionen der BulkExecutor-Bibliothek  
 
* Die Bibliothek reduziert die Computeressourcen auf Clientseite, die zur vollständigen Nutzung des einem Container zugeordneten Durchsatzes erforderlich sind. Eine Singlethreadanwendung, die Daten über die Massenimport-API schreibt, erzielt einen zehnmal höheren Schreibdurchsatz im Vergleich zu einer Multithreadanwendung, die Daten parallel schreibt und gleichzeitig die CPU des Clientcomputers vollständig nutzt.  

* Die Bibliothek verarbeitet die Begrenzung der Anforderungsrate, Anforderungstimeouts und andere vorübergehende Ausnahmen auf effiziente Weise, sodass Benutzer nicht mehr mühsam die Anwendungslogik dafür schreiben müssen.  

* Zudem bietet sie einen vereinfachten Mechanismus, mit dem Anwendungen Massenvorgänge zum Aufskalieren ausführen können. Eine einzige BulkExecutor-Instanz, die auf einem virtuellen Azure-Computer ausgeführt wird, kann mehr als 500.000 Anforderungseinheiten/Sek. verbrauchen, und Sie können eine höhere Durchsatzrate erzielen, indem Sie zusätzliche Instanzen auf einzelnen virtuellen Clientcomputern hinzufügen.  
 
* Dank einer Architektur für die horizontale Skalierung kann eine solche Instanz innerhalb einer Stunde mehr als ein Terabyte an Daten importieren.  

* Vorhandene Daten in Azure Cosmos-Containern können per Massenvorgang in Form von Patches aktualisiert werden. 
 
## <a name="how-does-the-bulk-executor-operate"></a>Wie funktioniert BulkExecutor? 

Wenn ein Massenvorgang zum Importieren oder Aktualisieren von Dokumenten mit einem Batch von Entitäten ausgelöst wird, werden die Dokumente anfänglich entsprechend dem Schlüsselbereich der zugehörigen Azure Cosmos DB-Partition in Buckets gemischt. In jedem Bucket, der einem Partitionsschlüsselbereich entspricht, werden die Dokumente in Minibatches aufgeteilt, und jedes Minibatch ist eine Nutzlast, für die auf Serverseite ein Commit ausgeführt wird. Die BulkExecutor-Bibliothek verfügt über integrierte Optimierungen für die gleichzeitige Ausführung dieser Minibatches sowohl innerhalb der Partitionsschlüsselbereiche als auch übergreifend über diese Bereiche. Die folgende Abbildung zeigt, wie BulkExecutor Daten in Batches für verschiedene Partitionsschlüssel aufteilt:  

:::image type="content" source="./media/bulk-executor-overview/bulk-executor-architecture.png" alt-text="Bulk Executor-Architektur" :::

Die BulkExecutor-Bibliothek stellt sicher, dass der Durchsatz, der einer Sammlung zugeordnet ist, optimal genutzt wird. Sie verwendet für jeden Azure Cosmos DB-Partitionsschlüsselbereich einen  [AIMD-Steuerungsmechanismus für Überlastungen](https://tools.ietf.org/html/rfc5681), um Ratenbegrenzungen und Zeitüberschreitungen effizient zu verarbeiten. 

## <a name="next-steps"></a>Nächste Schritte 
  
* Erfahren Sie mehr darüber, indem Sie die Beispielanwendungen ausprobieren, die die BulkExecutor-Bibliothek in [.NET](bulk-executor-dot-net.md) und [Java](bulk-executor-java.md) nutzen.  
* Sehen Sie sich die Informationen und Versionsanmerkungen zum BulkExecutor SDK in [.NET](sql-api-sdk-bulk-executor-dot-net.md) und [Java](sql-api-sdk-bulk-executor-java.md) an.
* Die BulkExecutor-Bibliothek ist in den Cosmos DB Spark-Connector integriert. Weitere Informationen finden Sie im Artikel [Spark-Connector für Azure Cosmos DB](spark-connector.md).  
* Die BulkExecutor-Bibliothek ist auch in eine neue Version des [Azure Cosmos DB-Connectors](../data-factory/connector-azure-cosmos-db.md) integriert, damit Azure Data Factory Daten kopieren kann.
