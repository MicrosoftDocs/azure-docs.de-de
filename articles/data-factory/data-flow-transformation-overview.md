---
title: Zuordnungsdatenfluss – Übersicht über Transformationen
description: Eine Übersicht über die verschiedenen Transformationen, die im Zuordnungsdatenfluss zur Verfügung stehen
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: ba2ff2b5f108d7910958b374cc4b602a84a3f53d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367910"
---
# <a name="mapping-data-flow-transformation-overview"></a>Zuordnungsdatenfluss – Übersicht über Transformationen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

In der nachstehenden Liste sind die Transformationen aufgeführt, die im Zuordnungsdatenfluss derzeit unterstützt werden. Klicken Sie auf die einzelnen Transformationen, um deren Konfigurationsdetails zu erfahren.

| Name | Category | BESCHREIBUNG |
| ---- | -------- | ----------- |
| [Aggregat](data-flow-aggregate.md) | Schemamodifizierer | Definieren Sie verschiedene Arten von Aggregationen wie SUM, MIN, MAX und COUNT, gruppiert nach vorhandenen oder berechneten Spalten. | 
| [Zeilenänderung](data-flow-alter-row.md) | Zeilenmodifizierer | Legen Sie Richtlinien für Einfüge-, Lösch-, Aktualisierungs- und Upsertvorgänge in Zeilen fest. |
| [Bedingtes Teilen](data-flow-conditional-split.md) | Mehrere Eingaben/Ausgaben | Leiten Sie Datenzeilen auf der Grundlage übereinstimmender Bedingungen an unterschiedliche Datenströme weiter. |
| [Abgeleitete Spalte](data-flow-derived-column.md) | Schemamodifizierer | Generieren Sie neue Spalten, oder ändern Sie vorhandene Felder mithilfe der Ausdruckssprache für Datenflüsse. | 
| [Exists](data-flow-exists.md) | Mehrere Eingaben/Ausgaben | Überprüfen Sie, ob Ihre Daten in einer anderen Quelle oder einem anderen Datenstrom vorhanden sind. | 
| [Filter](data-flow-filter.md) | Zeilenmodifizierer | Filtern Sie eine Zeile auf der Grundlage einer Bedingung. |
| [Vereinfachen](data-flow-flatten.md) | Schemamodifizierer |  Wählen Sie Arraywerte in hierarchischen Strukturen wie JSON aus, und lösen Sie sie in einzelne Zeilen auf. |
| [Join](data-flow-join.md) | Mehrere Eingaben/Ausgaben |  Kombinieren Sie Daten aus zwei Quellen oder Datenströmen. |
| [Suche](data-flow-lookup.md) | Mehrere Eingaben/Ausgaben | Verweisen Sie auf Daten aus einer anderen Quelle. |
| [Neuer Branch](data-flow-new-branch.md) | Mehrere Eingaben/Ausgaben | Wenden Sie mehrere Vorgänge und Transformationen auf denselben Datenstrom an. |
| [Pivotieren](data-flow-pivot.md) | Schemamodifizierer | Eine Aggregation, bei der die unterschiedlichen Zeilenwerte einer oder mehrerer Gruppierungsspalten in einzelne Spalten transformiert wurden. |
| [Rang](data-flow-rank.md) | Schemamodifizierer | Generieren einer geordneten Rangfolge basierend auf Sortierbedingungen |
| [Auswählen](data-flow-select.md) | Schemamodifizierer | Aliasspalten und Datenstromnamen sowie das Löschen oder Neuanordnen von Spalten |
| [Senke](data-flow-sink.md) | - | Ein endgültiges Ziel für Ihre Daten |
| [Sort](data-flow-sort.md) | Zeilenmodifizierer | Sortieren von eingehenden Zeilen im aktuellen Datenstrom |
| [Quelle](data-flow-source.md) | - | Eine Datenquelle für den Datenfluss |
| [Ersatzschlüssel](data-flow-surrogate-key.md) | Schemamodifizierer | Hinzufügen eines inkrementellen, nicht geschäftlichen beliebigen Schlüsselwerts |
| [Union](data-flow-union.md) | Mehrere Eingaben/Ausgaben | Vertikales Kombinieren mehrerer Datenströme |
| [Entpivotieren](data-flow-unpivot.md) | Schemamodifizierer | Pivotieren von Spalten in Zeilenwerte |
| [Fenster](data-flow-window.md) | Schemamodifizierer |  Definieren Sie fensterbasierte Aggregationen von Spalten in Ihren Datenströmen. |
