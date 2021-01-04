---
title: Datenherkunft in Azure Purview (Vorschau)
description: Beschreibt die Konzepte für die Datenherkunft.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: ceb30cee194994e5b31db312f191194932c937b4
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550436"
---
# <a name="data-lineage-in-azure-purview-data-catalog-client"></a>Datenherkunft in Azure Purview Data Catalog-Client

Dieser Artikel bietet eine Übersicht über die Datenherkunft im Azure Purview Data Catalog. Außerdem wird erläutert, wie Datensysteme in den Katalog integriert werden können, um Datenherkunft zu erfassen. Purview kann die Herkunft für Daten in unterschiedlichen Teilen der Datenumgebung ihrer Organisation und auf unterschiedlichen Vorbereitungsstufen erfassen, einschließlich:

- Von verschiedenen Plattformen bereitgestellte Rohdaten
- Transformierte und vorbereitete Daten
- Von Visualisierungsplattformen verwendete Daten.

## <a name="use-cases"></a>Anwendungsfälle

Unter Datenherkunft wird im Allgemeinen der Lebenszyklus verstanden, der den Ursprung der Daten und ihre Bewegung in der Datenumgebung im Laufe der Zeit umfasst. Sie wird für verschiedene Arten von rückblickenden Szenarios wie z. B. Problembehandlung, Ablaufverfolgung zum Ermitteln der Grundursache in Datenpipelines und Debuggen verwendet. Die Herkunft wird auch für Datenqualitätsanalyse, Compliance und „Was-wäre-wenn“-Szenarios verwendet, die häufig als Auswirkungsanalyse bezeichnet werden. Die visuelle Darstellung der Herkunft soll die Bewegung der Daten von der Quelle zum Ziel einschließlich der Umwandlung der Daten zeigen. Aufgrund der Komplexität der meisten Unternehmensdatenumgebungen sind diese Sichten u. U. ohne gewisse Konsolidierung oder Maskierung peripherer Datenpunkte schwer zu verstehen.

## <a name="lineage-experience-in-azure-purview-data-catalog"></a>Herkunftsumgebung im Azure Purview Data Catalog

Der Purview Data Catalog stellt eine Verbindung mit anderen Datenverarbeitungs-, Speicher- und Analysesystemen her, um Herkunftsinformationen zu extrahieren. Die Informationen werden kombiniert, um eine generische, szenariospezifische Herkunftsumgebung im Katalog darzustellen.

:::image type="content" source="media/concept-lineage/lineage-end-end.png" alt-text="End-to-End-Herkunftsanzeige von Daten, die vom Blobspeicher in das Power BI-Dashboard kopiert werden":::

Ihre Datenumgebung kann neben Visualisierungssystemen Systeme umfassen, die Datenextrahierung, Transformation (ETL/ELT-Systeme) und Analyse durchführen. Jedes der Systeme erfasst umfasst statische und operative Metadaten, die den Status und die Qualität der Daten innerhalb der Systemgrenze beschreiben. In einem Datenkatalog dient die Herkunft zum Extrahieren der Verschiebungs-, Transformations- und Betriebsmetadaten aus den einzelnen Datensystemen mit dem niedrigsten möglichen Aggregationsintervall.

Das folgende Beispiel ist ein typischer Anwendungsfall von Daten, die über mehrere Systeme übertragen werden, wobei der Data Catalog mit jedem der Systeme für die Herkunft eine Verbindung herstellen würde.

- Data Factory kopiert Daten aus einer lokalen/rohen Zone in eine Zielzone in der Cloud. 
- Mithilfe von Notebooks verarbeiten Datenverarbeitungssysteme wie Synapse und Databricks Daten und transformieren sie aus der Zielzone in eine kuratierte Zone.
- Die Daten werden in analytischen Modellen weiterverarbeitet, um eine optimale Abfrageleistung und Aggregation zu erzielen. 
- Datenvisualisierungssysteme nutzen die Datasets und verarbeiten sie über ihr Metamodell, um ein BI-Dashboard, ML-Experimente usw. zu erstellen.

## <a name="lineage-granularity"></a>Granularität der Herkunft

In diesem Abschnitt werden die Details der Granularität der Herkunft erläutert, wobei die Herkunftsinformationen von einem Datenkatalog gesammelt werden. Diese Granularität kann abhängig vom Datensystem variieren.

### <a name="entity-level-lineage-sources--process--targets"></a>Herkunft auf Entitätsebene: Quelle(n) > Prozess > Ziel(e) 

- Die Herkunft wird als Diagramm dargestellt, das in der Regel Quell- und Zielentitäten in Datenspeichersystemen enthält, die durch einen Prozess verbunden sind, der von einem Computesystem aufgerufen wird. 
- Datensysteme stellen eine Verbindung mit dem Datenkatalog her, um ein eindeutiges Objekt zu generieren und zu melden, das auf das physische Objekt des zugrunde liegenden Datensystems verweist. Beispiel: Gespeicherte SQL-Prozedur, Notebooks usw.
- Herkunft mit hoher Genauigkeit mit zusätzlichen Metadaten wie z. B. Besitz wird erfasst, um die Herkunft in einem lesbaren Format für Quell- und Zielentitäten anzuzeigen. Beispiel: Herkunft auf der Hive-Tabellenebene anstelle von Partitionen oder Dateiebene.

### <a name="column-or-attribute-level-lineage"></a>Herkunft auf Spalten- oder Attributebene

Identifizieren Sie Attribute einer Quellentität, die zum Erstellen oder Ableiten von Attributen in der Zielentität verwendet wird. Der Name des Quellattributs könnte in einem Ziel beibehalten oder geändert werden. Systeme wie ADF können eine „Eins-zu-eins“-Kopie von einer lokalen Umgebung in der Cloud erstellen. Beispiel: `Table1/ColumnA -> Table2/ColumnA`.

### <a name="process-execution-status"></a>Prozessausführungsstatus

Zur Unterstützung der Analyse der Grundursache und Datenqualitätsszenarios erfassen wir den Ausführungsstatus der Aufträge in Datenverarbeitungssystemen. Diese Anforderung hat nichts mit dem Ersetzen der Überwachungsfunktionen anderer Datenverarbeitungssysteme zu tun noch das Ziel, sie zu ersetzen. 

## <a name="summary"></a>Zusammenfassung

Herkunft ist ein wichtiges Feature des Purview Data Catalog, um Qualitäts-, Vertrauensstellungs- und Überwachungsszenarios zu unterstützen. Ein Datenkatalog soll dazu dienen, ein stabiles Framework zu erstellen, in dem alle Datensysteme in Ihrer Umgebung natürlich eine Verbindung mit der Herkunft herstellen und über sie berichten können. Sobald die Metadaten verfügbar sind, kann der Datenkatalog die von Datensystemen bereitgestellten Metadaten für Data Governance-Anwendungsfälle zusammenbringen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md)
* [Schnellstart: Erstellen eines Azure Purview-Kontos unter Verwendung von Azure PowerShell/Azure CLI](create-catalog-powershell.md)
* [Schnellstart: Verwenden von Purview Studio](use-purview-studio.md)