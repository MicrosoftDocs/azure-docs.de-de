---
title: Grundlegendes zu Suchfunktionen in Azure Purview (Vorschau)
description: In diesem Artikel wird erläutert, wie Azure Purview die Datenermittlung durch Suchfunktionen ermöglicht.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: af8ec9e0aac38240c7da92edd614892ff65712e2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550415"
---
# <a name="understand-search-features-in-azure-purview"></a>Grundlegendes zu Suchfunktionen in Azure Purview

Dieser Artikel bietet eine Übersicht über die Sucherfahrung	 in Azure Purview. Die Suche ist eine zentrale Plattformfunktion von Purview, die die Datenermittlungs- und Datenverwendungs-Governanceerfahrung in einer Organisation unterstützt.

## <a name="search"></a>Suchen

Die Purview-Sucherfahrung wird durch einen verwalteten Suchindex unterstützt. Nachdem eine Datenquelle bei Purview registriert wurde, werden die zugehörigen Metadaten vom Suchdienst indiziert, um eine einfache Ermittlung zu ermöglichen. Der Index stellt Suchrelevanzfunktionen bereit und vervollständigt Suchanforderungen, indem Millionen von Metadatenressourcen abgefragt werden. Die Suche unterstützt Sie dabei, die Daten zu ermitteln, zu verstehen und zu verwenden, um den optimalen Nutzen aus ihnen zu ziehen.

Die Suchfunktion in Purview ist ein dreistufiger Prozess:

1. Im Suchfeld wird der Verlauf mit den zuletzt verwendeten Schlüsselwörtern und Ressourcen angezeigt.
1. Wenn Sie mit der Tastatureingabe beginnen, schlägt die Suche die passenden Schlüsselwörter und Ressourcen vor. 
1. Die Suchergebnisseite wird mit Ressourcen angezeigt, die mit dem eingegebenen Schlüsselwort übereinstimmen.

## <a name="reduce-the-time-to-discover-data"></a>Reduzieren der zum Ermitteln von Daten aufgewendeten Zeit

Die Datenermittlung ist der erste Schritt einer Datenanalyse- oder Datengovernanceworkload für Datenconsumer. Die Datenermittlung kann zeitaufwändig sein, da Sie möglicherweise nicht wissen, wo Sie die gewünschten Daten finden. Auch nachdem Sie die Daten gefunden haben, haben Sie möglicherweise Zweifel, ob Sie die Daten als vertrauenswürdig einstufen und eine Abhängigkeit davon akzeptieren können. 

Die Suche in Azure Purview soll den Datenermittlungsprozess beschleunigen, indem Gesten bereitgestellt werden, um die relevanten Daten schnell zu finden.

## <a name="recent-search-and-suggestions"></a>Aktuelle Suche und Vorschläge

Möglicherweise arbeiten Sie viele Male gleichzeitig an mehreren Projekten. Um die Fortsetzung vorheriger Projekte zu vereinfachen, ermöglicht die Purview-Suche, die aktuellen Suchschlüsselwörter und Vorschläge anzuzeigen. Außerdem können Sie zum Verwalten des aktuellen Suchverlaufs in der Suchfeld-Dropdownliste **Alle anzeigen** auswählen.

## <a name="filters"></a>Filter

Filter (auch als *Facetten* bezeichnet) sind so konzipiert, dass sie die Suche ergänzen. Filter werden auf der Suchergebnisseite angezeigt. Die Filter auf der Suchergebnisseite umfassen Klassifizierung, Vertraulichkeitsbezeichnung, Datenquelle und Besitzer. Benutzer können bestimmte Werte in einem Filter auswählen, um nur übereinstimmende Datenressourcen anzuzeigen und das Suchergebnis auf die übereinstimmenden Ressourcen zu beschränken.

## <a name="hit-highlighting"></a>Treffermarkierung

Übereinstimmende Schlüsselwörter auf der Suchergebnisseite werden hervorgehoben, um die Ursache für das Zurückgeben einer Datenressource durch die Suche zu vereinfachen. Die Schlüsselwortübereinstimmung kann in mehreren Feldern wie z. B. Name, Beschreibung und Besitzer der Datenressource auftreten.

Selbst bei aktivierter Treffermarkierung ist u. U. nicht offensichtlich, warum eine Datenressource in der Suche enthalten ist. Alle Eigenschaften werden standardmäßig durchsucht. Darum könnte eine Datenressource aufgrund einer Übereinstimmung mit einer Eigenschaft auf Spaltenebene zurückgegeben werden. Da mehrere Benutzer die Datenressourcen mit eigenen Klassifizierungen und Beschreibungen versehen können, werden nicht alle Metadaten in der Liste der Suchergebnisse angezeigt.

## <a name="sort"></a>Sortieren

Benutzer haben zwei Optionen, um die Suchergebnisse zu sortieren. Sie können nach dem Namen der Ressource oder standardmäßiger Suchrelevanz sortieren.

## <a name="search-relevance"></a>Suchrelevanz

Relevanz ist die Standardsortierreihenfolge auf der Suchergebnisseite. Die Suchrelevanz findet Dokumente, die das Suchschlüsselwort enthalten (einige oder alle). Ressourcen, die viele Instanzen des Suchschlüsselworts enthalten, werden höher eingestuft. Außerdem werden benutzerdefinierte Bewertungsmechanismen ständig optimiert, um die Suchrelevanz zu verbessern.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md)
* [Schnellstart: Erstellen eines Azure Purview-Kontos unter Verwendung von Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Schnellstart: Verwenden von Purview Studio](use-purview-studio.md)
