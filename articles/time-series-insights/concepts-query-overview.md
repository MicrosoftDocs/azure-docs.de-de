---
title: Abfragen von Daten – Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Konzepte für die Datenabfrage und Übersicht über die REST-API in Azure Time Series Insights Gen2.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: 5682cf9856ad8969d930d72f53e888b03ff6ae75
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020860"
---
# <a name="querying-data-from-azure-time-series-insights-gen2"></a>Abfragen von Daten in Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 ermöglicht das Abfragen von Daten für Ereignisse und Metadaten, die in der Umgebung gespeichert sind, über öffentliche Oberflächen-APIs. Diese APIs werden auch im [Azure Time Series Insights TSI-Explorer](./concepts-ux-panels.md) verwendet.

Drei primäre API-Kategorien sind in Azure Time Series Insights Gen2 verfügbar:

* **Umgebungs-APIs**: Diese APIs ermöglichen Abfragen der Azure Time Series Insights Gen2-Umgebung selbst. Sie ermöglichen das Zusammenstellen der Liste der Umgebungen, auf die der Aufrufer Zugriff hat, sowie von Umgebungsmetadaten.
* **Zeitreihenmodellabfrage (TSM-Q)-APIs**: Ermöglicht die Vorgänge Erstellen, Lesen, Aktualisieren und Löschen (Create, Read, Update, Delete, CRUD) mit Metadaten, die im Zeitreihenmodell der Umgebung gespeichert sind. Diese können verwendet werden, um auf die Instanzen, Typen und Hierarchien zuzugreifen und sie zu bearbeiten.
* **Zeitreihenabfrage (TSQ)-APIs**: Ermöglicht das Abrufen von Telemetrie- oder Ereignisdaten, wie sie vom Quellanbieter aufgezeichnet werden, sowie leistungsstarke Berechnungen und Aggregationen der Daten mithilfe von erweiterten Skalar- und Aggregatfunktionen.

Azure Time Series Insights Gen2 verwendet eine umfassende zeichenfolgenbasierte Ausdruckssprache, [Time Series Expression, TSX (Zeitreihenausdrucksprache)](/rest/api/time-series-insights/reference-time-series-expression-syntax), um Berechnungen in [Zeitreihenvariablen](./concepts-variables.md) auszudrücken.

## <a name="azure-time-series-insights-gen2-apis-overview"></a>Übersicht über Azure Time Series Insights Gen2-APIs

Die folgenden Kern-APIs werden unterstützt.

[![Zeitreihenabfrage (TSQ): Übersicht](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Umgebungs-APIs

* [„Umgebungen abrufen“-API](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment): Gibt die Liste der Umgebungen zurück, auf die der Benutzer zum Zugriff autorisiert ist.
* [„Umgebungsverfügbarkeit abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/getavailability): Gibt die Verteilung der Anzahl von Ereignissen über den Ereigniszeitstempel `$ts` zurück. Diese API hilft dabei, zu ermitteln, ob Ereignisse in der Umgebung vorhanden sind, indem die Anzahl von Ereignissen (sofern vorhanden) unterteilt in Zeitintervalle zurückgegeben wird.
* [„Ereignisschema abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/geteventschema): Gibt die Ereignisschema-Metadaten für einen bestimmten Suchzeitraum zurück. Diese API hilft beim Abrufen aller Metadaten und Eigenschaften, die im Schema für den angegebenen Suchzeitraum zur Verfügung stehen.

## <a name="time-series-model-query-tsm-q-apis"></a>Zeitreihenmodellabfrage (TSM-Q)-APIs

Die meisten dieser APIs unterstützen die Batchausführung, um Batch-CRUD-Vorgänge für mehrere Entitäten von Zeitreihenmodellen zu ermöglichen:

* [„Modelleinstellungen“-API](/rest/api/time-series-insights/reference-model-apis): Ermöglicht *GET* und *PATCH* für den Standardtyp und den Modellnamen der Umgebung.
* [Typen-API](/rest/api/time-series-insights/reference-model-apis#types-api): Ermöglicht CRUD mit Zeitreihentypen und deren zugeordneten Variablen.
* [Hierarchien-API](/rest/api/time-series-insights/reference-model-apis#hierarchies-api): Ermöglicht CRUD mit Zeitreihenhierarchien und deren zugeordneten Feldpfaden.
* [Instanzen-API](/rest/api/time-series-insights/reference-model-apis#instances-api): Ermöglicht CRUD mit Zeitreiheninstanzen und deren zugeordneten Instanzenfeldern. Zusätzlich unterstützt die Instanzen-API die folgenden Vorgänge:
  * [Suchen](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search): Ruft eine unvollständige Liste der Treffer bei der Suche nach Zeitreiheninstanzen basierend auf Instanzattributen ab.
  * [Vorschlagen](/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest): Sucht eine unvollständige Liste der Treffer bei der Suche nach Zeitreiheninstanzen basierend auf Instanzattributen und schlägt sie vor.

## <a name="time-series-query-tsq-apis"></a>Zeitreihenabfrage (TSQ)-APIs

Diese APIs sind in beiden Speichern (warm und kalt) unserer mehrschichtigen Speicherlösung verfügbar. Parameter zum Abfragen der URL werden verwendet, um den [Speichertyp](/rest/api/time-series-insights/dataaccessgen2/query/execute#uri-parameters) anzugeben, für den die Abfrage ausgeführt werden soll:

* [„Ereignisse abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents): Ermöglicht das Abfragen und Abrufen von unformatierten Ereignissen und den zugehörigen Zeitstempeln, wie sie in Azure Time Series Insights Gen2 vom Quellanbieter aufgezeichnet werden. Diese API ermöglicht das Abrufen von Rohereignissen für eine bestimmte Zeitreihen-ID und Suchzeitspanne. Diese API unterstützt die Paginierung, um das vollständige Antwortdataset für die ausgewählte Eingabe abzurufen.

  > [!IMPORTANT]

  > * Im Rahmen der [bevorstehenden Änderungen für JSON-Vereinfachungs- und -Escaperegeln](./ingestion-rules-update.md) werden Arrays mit dem Typ **Dynamic** gespeichert. Nutzlasteigenschaften, die mit diesem Typ gespeichert werden, sind **NUR über die Ereignisabruf-API zugänglich**.

* [„Reihe abrufen“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries): Ermöglicht das Abfragen und Abrufen berechneter Werte und der zugehörigen Ereigniszeitstempel durch Anwenden von Berechnungen, die durch Variablen in unformatierten Ereignissen definiert werden. Diese Variablen können im Zeitreihenmodell definiert oder in der Abfrage bereitgestellt werden. Diese API unterstützt die Paginierung, um das vollständige Antwortdataset für die ausgewählte Eingabe abzurufen.

* [„Reihe aggregieren“-API](/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries): Ermöglicht das Abfragen und Abrufen aggregierter Werte und der zugehörigen Intervallzeitstempel durch Anwenden von Berechnungen, die durch Variablen in unformatierten Ereignissen definiert werden. Diese Variablen können im Zeitreihenmodell definiert oder in der Abfrage bereitgestellt werden. Diese API unterstützt die Paginierung, um das vollständige Antwortdataset für die ausgewählte Eingabe abzurufen.
  
  Diese API gibt für eine angegebene Suchzeitspanne und ein angegebenes Intervall eine aggregierte Antwort pro Intervall pro Variable für eine Zeitreihen-ID zurück. Die Anzahl der Intervalle im Antwortdataset wird durch Zählen der Epochenticks (Anzahl der Millisekunden seit Beginn der Unix-Epoche am 1. Januar 1970) und die Division der Ticks durch die in der Abfrage angegebene Intervallzeitspanne berechnet.

  Die im Antwortdataset zurückgegebenen Zeitstempel stellen die linken Intervallgrenzen dar, nicht die Stichprobenereignisse im Intervall.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über verschiedene Variablen, die im [Zeitreihenmodell](./concepts-model-overview.md) definiert werden können.
* Erfahren Sie mehr über das Abfragen von Daten im [Azure Time Series Insights-Explorer](./concepts-ux-panels.md).