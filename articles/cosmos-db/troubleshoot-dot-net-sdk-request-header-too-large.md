---
title: Behandeln von Problemen im Zusammenhang mit zu großem Anforderungsheader oder mit ungültigen Anforderungen (400) in Azure Cosmos DB
description: Erfahren Sie, wie Sie Ausnahmen diagnostizieren und beheben, die auf einen zu großen Anforderungsheader zurückzuführen sind.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340514"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnostizieren und Behandeln von Problemen im Zusammenhang mit der Azure Cosmos DB-Meldung, dass der Anforderungsheader zu groß ist
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Die Meldung, dass der Anforderungsheader zu groß ist, wird mit dem HTTP-Fehlercode 400 ausgelöst. Dieser Fehler tritt auf, wenn der Anforderungsheader zu groß geworden ist und die maximal zulässige Größe übersteigt. Es wird empfohlen, die neueste Version des SDK zu verwenden. Verwenden Sie mindestens die Version 3.x oder 2.x, da der Ausnahmemeldung in diesen Versionen eine Verfolgung der Headergröße hinzugefügt wird.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die Meldung über einen zu großen Anforderungsheader tritt auf, wenn das Sitzungs- oder Fortsetzungstoken zu groß ist. In den folgenden Abschnitten werden jeweils die Ursache und die Lösung des Problems in den einzelnen Kategorien beschrieben.

### <a name="session-token-is-too-large"></a>Sitzungstoken zu groß

#### <a name="cause"></a>Ursache:
Eine ungültige Anforderung mit dem Code 400 tritt höchstwahrscheinlich auf, da das Sitzungstoken zu groß ist. Wenn die folgenden Aussagen zutreffen, ist das Sitzungstoken zu groß:

* Der Fehler tritt bei Punktvorgängen ohne Fortsetzungstoken auf (beispielsweise beim Erstellen, Lesen und Aktualisieren).
* Die Ausnahme tritt auf, ohne dass Änderungen an der Anwendung vorgenommen wurden. Die Größe des Sitzungstokens nimmt mit der steigenden Anzahl von Partitionen im Container zu. Die Anzahl von Partitionen steigt, wenn die Datenmenge zunimmt oder der Durchsatz erhöht wird.

#### <a name="temporary-mitigation"></a>Vorübergehende Lösung: 
Starten Sie Ihre Clientanwendung neu, um alle Sitzungstoken zurückzusetzen. Das Sitzungstoken erreicht irgendwann wieder die Größe, durch die das Problem verursacht wurde. Verwenden Sie daher die Lösung im nächsten Abschnitt, um dieses Problem vollständig zu vermeiden.

#### <a name="solution"></a>Lösung:
1. Befolgen Sie die Anweisungen in den Artikeln zu Leistungstipps für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) oder [.NET Version 2](performance-tips.md). Konvertieren Sie die Anwendung so, dass der Direktverbindungsmodus mit TCP (Transmission Control Protocol) verwendet wird. Im Direktverbindungsmodus mit TCP besteht keine Headergrößenbeschränkung wie bei HTTP, sodass dieses Problem vermieden wird. Verwenden Sie die neueste Version des SDK. Diese enthält eine Korrektur für Abfragevorgänge, wenn die Dienstinteroperabilität nicht verfügbar ist.
1. Sollte der Direktverbindungsmodus mit TCP für Ihre Workload nicht in Frage kommen, ändern Sie die [Konsistenzebene des Clients](how-to-manage-consistency.md). Das Sitzungstoken wird nur für die Sitzungskonsistenz verwendet. Dies ist die Standardkonsistenzebene für Azure Cosmos DB. Von anderen Konsistenzebenen wird das Sitzungstoken nicht verwendet.

### <a name="continuation-token-is-too-large"></a>Fortsetzungstoken zu groß

#### <a name="cause"></a>Ursache:
Eine ungültige Anforderung mit dem Code 400 tritt bei Abfragevorgängen mit einem Fortsetzungstoken auf, bei denen dieses Fortsetzungstoken zu groß geworden ist, oder wenn verschiedene Abfragen unterschiedliche Fortsetzungstoken aufweisen.
    
#### <a name="solution"></a>Lösung:
1. Befolgen Sie die Anweisungen in den Artikeln zu Leistungstipps für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) oder [.NET Version 2](performance-tips.md). Konvertieren Sie die Anwendung so, dass der Direktverbindungsmodus mit TCP verwendet wird. Im Direktverbindungsmodus mit TCP besteht keine Headergrößenbeschränkung wie bei HTTP, sodass dieses Problem vermieden wird. 
1. Sollte der Direktverbindungsmodus mit TCP für Ihre Workload nicht in Frage kommen, legen Sie die Option `ResponseContinuationTokenLimitInKb` fest. Diese Option befindet sich unter `FeedOptions` (v2) bzw. unter `QueryRequestOptions` (v3).

## <a name="next-steps"></a>Nächste Schritte
* [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB
* Weitere Informationen zu Leistungsrichtlinien für [.NET Version 3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET Version 2](performance-tips.md)
