---
title: Datenabfragen in Azure Time Series Insights Preview | Microsoft-Dokumentation
description: Datenabfragen in Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: bbf682df2df7a8cdc9fedb36aa4244fc5c0e9488
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244004"
---
# <a name="data-querying"></a>Datenabfragen

Azure Time Series Insights Preview ermöglicht das Abfragen von Daten für Ereignisse und Metadaten, die in der Umgebung gespeichert sind, über öffentliche Oberflächen-APIs. Diese APIs werden auch im [Time Series Insights Preview-Explorer](./time-series-insights-update-explorer.md) verwendet.

Drei primäre API-Kategorien sind in Time Series Insights verfügbar:

* **Umgebungs-APIs**: Ermöglichen Abfragen der Time Series Insights-Umgebung selbst. Beispiele für Abfragen sind die Liste der Umgebungen, auf die der Aufrufer Zugriff hat, sowie Umgebungsmetadaten.

* **Zeitreihenmodellabfrage (TSM-Q)-APIs**: Ermöglicht das Erstellen, Lesen, Aktualisieren und Löschen von Vorgängen mit Metadaten, die im Umgebungsteil des Zeitreihenmodells gespeichert sind. Beispiele sind Instanzen, Typen und Hierarchien.

* **Zeitreihenabfrage (TSQ)-APIs**: Ermöglichen das Abrufen von Ereignisdaten, während sie vom Quellenanbieter aufgezeichnet werden. Diese APIs können Vorgänge zum Transformieren und Kombinieren sowie Berechnungen mit Zeitreihendaten ausführen.

Die [Zeitreihenausdrucksprache (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx) ist eine leistungsstarke vierte Kategorie. Sie verwendet Zeitreihenmodelle, um die Zusammensetzung komplexer Berechnungen zu ermöglichen.

## <a name="azure-time-series-insights-preview-core-apis"></a>Kern-APIs von Azure Time Series Insights Preview

Die folgenden Kern-APIs werden unterstützt.

[ ![Zeitreihenabfrage (TSQ): Übersicht](media/v2-update-tsq/tsq.png)](media/v2-update-tsq/tsq.png#lightbox)

## <a name="environment-apis"></a>Umgebungs-APIs

Die folgenden Umgebungs-APIs sind verfügbar:

* [„Umgebung abrufen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environments-api): Gibt die Liste der Umgebungen zurück, auf die der Benutzer zum Zugriff autorisiert ist.
* [„Umgebungsverfügbarkeit abrufen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-environment-availability-api): Gibt die Verteilung der Anzahl von Ereignissen über den Ereigniszeitstempel `$ts` zurück. Diese API hilft dabei zu bestimmen, ob Ereignisse in dem Zeitstempel vorhanden sind, indem die Anzahl von Ereignissen, sofern welche vorhanden sind, zurückgegeben wird.
* [„Ereignisschema abrufen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-env#get-event-schema-api): Gibt die Ereignisschema-Metadaten für einen bestimmten Suchzeitraum zurück. Diese API hilft beim Abrufen aller Metadaten und Eigenschaften, die im Schema für den angegebenen Suchzeitraum zur Verfügung stehen.

## <a name="time-series-model-query-tsm-q-apis"></a>Zeitreihenmodellabfrage (TSM-Q)-APIs

Die folgenden Zeitreihenmodellabfrage-APIs sind verfügbar:

* [„Modelleinstellungen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api): Ermöglicht das Abrufen und Patchen des Standardtyps und des Modellnamens der Umgebung.
* [Typen-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api): Ermöglicht CRUD mit Zeitreihentypen und deren zugeordneten Variablen.
* [Hierarchien-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api): Ermöglicht CRUD mit Zeitreihenhierarchien und deren zugeordneten Feldpfaden.
* [Instanzen-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api): Ermöglicht CRUD mit Zeitreiheninstanzen und deren zugeordneten Instanzenfeldern.

## <a name="time-series-query-tsq-apis"></a>Zeitreihenabfrage (TSQ)-APIs

Die folgenden Zeitreihenabfrage-APIs sind verfügbar:

* [„Ereignisse abrufen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-events-api): Ermöglicht das Abfragen und Abrufen von Time Series Insights-Daten aus Ereignissen, während sie in Time Series Insights vom Quellenanbieter aufgezeichnet werden.

* [„Reihe abrufen“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#get-series-api): Ermöglicht das Abfragen und Abrufen von Time Series Insights-Daten aus erfassten Ereignissen, indem bei der Übertragung aufgezeichnete Daten verwendet werden. Die zurückgegebenen Werte, basieren auf den Variablen, die im Modell definiert oder inline angegeben wurden.

    >[!NOTE]
    > Die Aggregationsklausel wird ignoriert, auch wenn sie in einem Modell oder inline angegeben wurde.

  Die „Reihe abrufen“-API gibt einen Zeitreihenwert für jede Variable in jedem Intervall zurück. Ein Zeitreihenwert ist ein Format, das Time Series Insights für die JSON-Ausgabe aus einer Abfrage verwendet. Die zurückgegebenen Werte, basieren auf der Time Series-ID und dem Satz von Variablen, die bereitgestellt wurden.

* [„Reihe aggregieren“-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#aggregate-series-api): Ermöglicht das Abfragen und Abrufen von Time Series Insights-Daten aus erfassten Ereignissen, indem Stichproben aus aufgezeichneten Daten entnommen und diese aggregiert werden.

  Die „Reihe aggregieren“-API gibt einen Zeitreihenwert für jede Variable in jedem Intervall zurück. Die Werte basieren auf der Time Series-ID und dem Satz von Variablen, die bereitgestellt wurden. Die „Reihe aggregieren“-API erreicht eine Verringerung unter Verwendung der Variablen, die im Zeitreihenmodell gespeichert sind oder inline bereitgestellt werden, um Daten zu aggregieren oder daraus Stichproben zu entnehmen.

  Unterstützte Aggregationstypen sind: `Min`, `Max`, `Sum`, `Count`, `Average`

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Datenspeicherung und Dateneingang](./time-series-insights-update-storage-ingress.md) in Azure Time Series Insights Preview.

- Lesen Sie den Artikel über [Datenmodellierung](./time-series-insights-update-tsm.md) in Time Series Insights Preview.

- Entdecken Sie [bewährte Methoden, wenn Sie eine Time Series-ID auswählen](./time-series-insights-update-how-to-id.md).
