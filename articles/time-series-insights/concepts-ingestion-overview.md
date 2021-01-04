---
title: Übersicht über Datenerfassung für Azure Time Series Insights Gen2 | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Datenerfassung für Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: seodec18
ms.openlocfilehash: 2b76490ab23cb14755f6f90a27ec8b176bb30a11
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780024"
---
# <a name="azure-time-series-insights-gen2-data-ingestion-overview"></a>Übersicht über die Datenerfassung für Azure Time Series Insights Gen2

Ihre Azure Time Series Insights Gen2-Umgebung enthält eine *Erfassungs-Engine*, mit der Zeitreihenstreamingdaten gesammelt, verarbeitet und gespeichert werden. Wenn Daten bei der oder den Ereignisquellen eintreffen, verarbeitet und speichert Azure Time Series Insights Gen2 diese in Quasi-Echtzeit.

[![Übersicht über die Datenerfassung](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Themen zur Erfassung

In den folgenden Artikeln wird die Datenverarbeitung einschließlich der empfohlenen Best Practices ausführlich behandelt:

* Informieren Sie sich über [Ereignisquellen](./concepts-streaming-ingestion-event-sources.md) und Empfehlungen zum Auswählen eines Zeitstempels für die Ereignisquelle.

* Lesen Sie, welche [Datentypen](./concepts-supported-data-types.md) unterstützt werden.

* Erfahren Sie, wie die Erfassungs-Engine eine Reihe von [Regeln](./concepts-json-flattening-escaping-rules.md) auf Ihre JSON-Eigenschaften anwendet, um Ihre Speicherkontospalten zu erstellen.

* Überprüfen Sie die [Durchsatzbeschränkungen](./concepts-streaming-ingress-throughput-limits.md) in Ihrer Umgebung, um die erforderliche Skalierung zu planen.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich als Nächstes über [Ereignisquellen](./concepts-streaming-ingestion-event-sources.md) für Ihre Azure Time Series Insights Gen2-Umgebung.
