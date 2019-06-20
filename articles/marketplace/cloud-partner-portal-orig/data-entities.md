---
title: Datenentitäten
description: Eine Übersicht über Datenentitäten.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a382f9b3ce08bba266311c2cc1d5f868f1bc3143
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934927"
---
# <a name="data-entities"></a>Datenentitäten

In diesem Artikel finden Sie die Definitionen der Datenentitäten und eine Übersicht über diese. Er enthält Informationen zu den Funktionen von Datenentitäten, die unterstützten Szenarien, die verwendeten Kategorien und die Methoden zu ihrer Erstellung.

## <a name="overview"></a>Übersicht

Eine Datenentität ist eine Abstraktion der physischen Implementierung von Datenbanktabellen. In normalisierten Tabellen könnte beispielsweise ein Großteil der Daten zu jedem Kunden in einer Kundentabelle gespeichert werden, während die übrigen Daten auf verknüpfte Tabellen verteilt sind. In diesem Fall wird die Datenentität für das Kundenkonzept als eine denormalisierte Ansicht angezeigt, in der jede Zeile alle Daten aus der Kundentabelle und den zugehörigen Tabellen enthält. Eine Datenentität kapselt ein Geschäftskonzept in ein Format, das die Entwicklung und Integration vereinfacht. Durch die Abstraktion können Datenentitäten die Anwendungsentwicklung und -anpassung vereinfachen. Zu einem späteren Zeitpunkt umfasst diese Abstraktion auch Anwendungscode aus unvermeidlichen Änderungen an den physischen Tabellen für die einzelnen Versionen.

Zusammenfassung: Datenentitäten bieten eine konzeptionelle Abstraktion und Kapselung (denormalisierte Ansicht) der zugrunde liegenden Tabellenschemas zur Darstellung von Schlüsseldatenkonzepten und Funktionen.

## <a name="capabilities"></a>Funktionen

Eine Datenentität bietet die folgenden Funktionen:

- Sie ersetzt die abweichenden und fragmentierten Konzepte AXD, Entitäten des Daten-Import/Export-Frameworks (DIXF) und Aggregatabfragen durch ein einzelnes Konzept.
- Sie bietet einen zentralen Stapel zur Erfassung von Geschäftslogik für Szenarien wie das Importieren/Exportieren, die Integration und die Programmierbarkeit.
- Sie stellt den primären Mechanismus zum Exportieren und Importieren von Datenpaketen für das Application Lifecycle Management (ALM) und Demodatenszenarien dar.
- Sie kann als OData-Dienste verfügbar gemacht und dann in synchronen Integrationsszenarien in Tabellenform und in Microsoft Office-Integrationen verwendet werden.

Weitere Informationen finden Sie unter [Datenentitäten](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities).
