---
title: Automatisierte Empfehlungen zu Leistung, Kosten und Sicherheit für Azure Cosmos DB
description: Erfahren Sie, wie Sie angepasste Empfehlungen für Azure Cosmos DB in Bezug auf Leistung, Kosten, Sicherheit und weitere Aspekte basierend auf Ihren Workloadmustern anzeigen.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087824"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Automatisierte Empfehlungen in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Alle Clouddienste, einschließlich Azure Cosmos DB, erhalten häufig Updates mit neuen Features, Funktionen und Verbesserungen. Es ist wichtig, Ihre Anwendung immer mit den neuesten Leistungs- und Sicherheitsupdates zu versorgen. Das Azure-Portal bietet angepasste Empfehlungen, die es Ihnen ermöglichen, die Leistung Ihrer Anwendung zu maximieren. Die Advisor-Engine von Azure Cosmos DB analysiert kontinuierlich den Nutzungsverlauf Ihrer Azure Cosmos DB-Ressourcen und stellt basierend auf Ihren Workloadmustern Empfehlungen bereit. Die Empfehlungen decken Bereiche wie Partitionierung, Indizierung, Netzwerk, Sicherheit usw. ab. Mit diesen angepassten Empfehlungen können Sie die Leistung Ihrer Anwendung verbessern.

## <a name="view-recommendations"></a>Anzeigen von Empfehlungen

Sie können Empfehlungen für Azure Cosmos DB folgendermaßen anzeigen:

- Eine Möglichkeit ist die Verwendung der Registerkarte „Benachrichtigungen“. Wenn eine neue Empfehlung vorhanden ist, wird eine Meldungsleiste angezeigt. Melden Sie sich bei Ihrem [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem Azure Cosmos-Konto. Öffnen Sie in Ihrem Azure Cosmos-Konto den Bereich **Benachrichtigungen** , und klicken Sie dann auf die Registerkarte **Empfehlungen**. Sie können die Meldung auswählen und Empfehlungen anzeigen.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Anzeigen von Empfehlungen im Azure Cosmos DB-Bereich":::

- Sie können Empfehlungen auch im [Azure Advisor](../advisor/advisor-overview.md) finden, wo sie in Kategorien wie Kosten, Sicherheit, Zuverlässigkeit, Leistung und Betriebsoptimierung eingeteilt sind. Sie können bestimmte Abonnements auswählen und nach Ressourcentyp filtern. Dieser lautet **Azure Cosmos DB-Konten**.  Wenn Sie eine bestimmte Empfehlung auswählen, werden die Aktionen angezeigt, die Sie ausführen können, um Ihre Workloads zu verbessern.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Anzeigen von Empfehlungen im Bereich „Azure Advisor“":::

Nicht alle im Bereich „Azure Cosmos DB“ angezeigten Empfehlungen stehen auch im Azure Advisor zur Verfügung und umgekehrt. Je nach Typ passen Empfehlungen in den Azure Advisor-Bereich, in den Azure Cosmos DB-Bereich oder in beide.

Derzeit unterstützt Azure Cosmos DB Empfehlungen in den folgenden Bereichen. Jede Empfehlung enthält einen Link zum relevanten Abschnitt der Dokumentation, um Ihnen die nächsten Schritte zu erleichtern.

## <a name="sdk-usage-recommendations"></a>Empfehlungen zur SDK-Verwendung

In dieser Kategorie erkennt der Advisor die Verwendung einer älteren SDK-Version und empfiehlt ein Upgrade auf eine neuere Version, damit Sie von den neuesten Fehlerbehebungen und Leistungsverbesserungen profitieren können. Derzeit sind die folgenden SDK-spezifischen Empfehlungen verfügbar:

|Name  |BESCHREIBUNG  |
|---------|---------|
| Alter Spark-Connector | Erkennt die Verwendung alter Versionen des Spark-Connectors und empfiehlt ein Upgrade. |
| Altes .NET SDK | Erkennt die Verwendung alter Versionen des .NET SDK und empfiehlt ein Upgrade. |
| Altes Java SDK | Erkennt die Verwendung alter Versionen des Java-Connectors und empfiehlt ein Upgrade. |

## <a name="indexing-recommendations"></a>Empfehlungen zur Indizierung

In dieser Kategorie erkennt der Advisor den Indizierungsmodus, die Indizierungsrichtlinie sowie indizierte Pfade und empfiehlt eine Änderung, wenn die aktuelle Konfiguration die Abfrageleistung beeinträchtigt. Derzeit sind die folgenden indizierungsspezifischen Empfehlungen verfügbar:

|Name  |BESCHREIBUNG  |
|---------|---------|
| Verzögerte Indizierung | Erkennt die Verwendung des verzögerten Indizierungsmodus und empfiehlt stattdessen die Verwendung des konsistenten Modus. Der Zweck des verzögerten Indizierungsmodus von Azure Cosmos DB ist begrenzt. Dieser Modus kann sich in einigen Situationen auf die Aktualität auswirken, daher wird der konsistente Indizierungsmodus empfohlen. |
| Zusammengesetzte Indizierung| Erkennt diejenigen Konten, in denen Abfragen von zusammengesetzter Indizes profitieren können, und empfiehlt deren Verwendung. Zusammengesetzte Indizes können die Leistung und den Durchsatzverbrauch einiger Abfragen ganz erheblich verbessern.|
| Standardmäßige Indizierungsrichtlinie mit vielen indizierten Pfaden | Erkennt Container, die mit standardmäßiger Indizierung mit vielen indizierten Pfaden ausgeführt werden, und empfiehlt eine Anpassung der Indizierungsrichtlinie.|
| ORDER BY-Abfragen mit hohem RU/s-Verbrauch| Erkennt Container, die ORDER BY-Abfragen mit hohem RU/s-Verbrauch ausgeben, und empfiehlt die Erkundung von zusammengesetzten Indizes.|
| MongoDB 3.6-Konten ohne Index und mit hohem RU/s-Verbrauch| Erkennt die Azure Cosmos DB-API für MongoDB mit Containern der Version 3.6, die Abfragen mit hohem RU/s-Verbrauch ausgeben, und empfiehlt das Hinzufügen von Indizes.|

## <a name="cost-optimization-recommendations"></a>Empfehlungen zur Kostenoptimierung

In dieser Kategorie erkennt der Advisor die RU/s-Nutzung und stellt fest, dass Sie den Preis optimieren können, indem Sie einige Änderungen an Ihren Ressourcen vornehmen oder ein anderes Preismodell nutzen. Derzeit sind die folgenden Empfehlungen zur Kostenoptimierung verfügbar:

|Name  |BESCHREIBUNG  |
|---------|---------|
| Reservierte Kapazität | Erkennt den Verbrauch von RU/s und empfiehlt reservierte Instanzen für Benutzer, die davon profitieren können. |
| Inaktive Container | Erkennt Container, die seit mehr als 30 Tagen nicht mehr verwendet wurden, und empfiehlt, den Durchsatz für diese Container zu reduzieren oder sie zu löschen.|
| Neue Abonnements mit hohem Durchsatz | Erkennt neue Abonnements mit Konten, die ungewöhnlich viele RU/s pro Tag verbrauchen, und sendet diesen eine Benachrichtigung. Diese Benachrichtigung dient hauptsächlich dazu, neuen Kunden ins Bewusstsein zu rufen, dass Azure Cosmos DB mit einem auf bereitgestelltem Durchsatz basierenden Modell arbeitet, nicht mit einem nutzungsbasierten Modell. |

## <a name="migration-recommendations"></a>Empfehlungen zur Migration

In dieser Kategorie erkennt der Advisor, dass Sie Legacyfeatures verwenden, und empfiehlt eine Migration, sodass Sie von der enormen Skalierbarkeit und weiteren Vorteilen von Azure Cosmos DB profitieren können. Derzeit sind die folgenden migrationsspezifischen Empfehlungen verfügbar:

|Name  |BESCHREIBUNG  |
|---------|---------|
| Nicht partitionierte Container | Erkennt Container mit fester Größe, deren Speicherplatz sich der Obergrenze nähert, und empfiehlt, diese zu partitionierten Containern zu migrieren.|

## <a name="query-usage-recommendations"></a>Empfehlungen zur Verwendung von Abfragen

In dieser Kategorie erkennt der Advisor die Ausführung einer Abfrage und ermittelt, dass die Abfrageleistung durch einige Änderungen optimiert werden kann. Derzeit sind die folgenden verwendungsspezifischen Empfehlungen verfügbar:

|Name  |BESCHREIBUNG  |
|---------|---------|
| Abfragen mit fester Seitengröße | Erkennt Abfragen, die mit einer festen Seitengröße ausgegeben werden, und empfiehlt die Verwendung des Werts „-1“ (kein Grenzwert bei der Seitengröße) anstatt der Definition eines bestimmten Werts. Diese Option reduziert die Anzahl von Netzwerkroundtrips, die zum Abrufen aller Ergebnisse erforderlich sind. |

## <a name="next-steps"></a>Nächste Schritte

* [Optimieren der Abfrageleistung in Azure Cosmos DB](sql-api-query-metrics.md)
* [Behandeln von Abfrageproblemen](troubleshoot-query-performance.md) bei Verwendung von Azure Cosmos DB
