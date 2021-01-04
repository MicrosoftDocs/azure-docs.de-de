---
title: Klassifizierungsberichterstellung für Ihre Daten mithilfe von Purview-Erkenntnissen (Vorschau)
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Klassifizierungsberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/24/2020
ms.openlocfilehash: 553c33b3d5ea2e3f1ee81503cb69fe15db387af6
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745025"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Klassifizierungserkenntnisse zu Ihren Daten aus Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Purview-Klassifizierungserkenntnisberichte für Ihre Daten zugreifen, sie anzeigen und filtern können.

Unterstützte Datenquellen: Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (ehemals SQL DW), Azure SQL-Datenbank, Azure SQL Managed Instance, SQL Server

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> - Starten Ihres Purview-Kontos über Azure
> - Anzeigen von Klassifizierungserkenntnissen zu Ihren Daten
> - Ausführen eines Drilldowns zum Abrufen weiterer Klassifizierungsdetails zu Ihren Daten

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview-Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Füllen der entsprechenden Konten mit Testdaten

- Einrichten und Abschließen einer Überprüfung der Testdaten in den einzelnen Datenquellen 

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-classification-insights"></a>Verwenden von Purview-Klassifizierungserkenntnissen

In Azure Purview ähneln Klassifizierungen Betrefftags und werden zum Kennzeichnen und Identifizieren von Daten eines bestimmten Typs verwendet, die während der Überprüfung in Ihrer Datenumgebung gefunden werden.

Für Purview werden die gleichen Arten von vertraulichen Informationen wie in Microsoft 365 verwendet. Hiermit können Sie Ihre vorhandenen Sicherheitsrichtlinien und den Schutz auf Ihre gesamten Datenbestände erweitern.

> [!NOTE]
> Nachdem Sie Ihre Quelltypen überprüft haben, geben Sie den Erkenntnissen zur **Klassifizierungsbezeichnung** ein paar Stunden Zeit, um die neuen Ressourcen zu berücksichtigen.

**So zeigen Sie Klassifizierungserkenntnisse an**

1. Wechseln Sie zum **Azure Purview**-[Instanzbildschirm im Azure-Portal](https://aka.ms/purviewportal), und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kontokachel **Purview starten** aus.

1. Wählen Sie in Purview das Menüelement **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: auf der linken Seite aus, um auf Ihren **Erkenntnisse**-Bereich zuzugreifen.

1. Im Bereich **Erkenntnisse** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: wählen Sie **Klassifizierung** aus, um den Bericht zu den Purview-**Klassifizierungserkenntnissen** anzuzeigen.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="Klassifizierungserkenntnisbericht" lightbox="media/insights/select-classification-labeling.png":::

   Die Hauptseite für **Klassifizierungserkenntnisse** zeigt die folgenden Bereiche an:

   |Bereich  |BESCHREIBUNG  |
   |---------|---------|
   |**Übersicht über Quellen mit Klassifizierungen**     |Zeigt Kacheln mit folgenden Informationen an: <br>- Die Anzahl der in Ihren Daten gefundenen Abonnements <br>- Die Anzahl der in Ihren Daten gefundenen eindeutigen Klassifizierungen <br>- Die Anzahl der gefundenen klassifizierten Quellen <br>- Die Anzahl der gefundenen klassifizierten Dateien <br>- Die Anzahl der gefundenen klassifizierten Tabellen         |
   |**Hauptquellen mit klassifizierten Daten (letzte 30 Tage)**     |Zeigt den Trend über die letzten 30 Tage für die Anzahl der gefundenen Quellen mit klassifizierten Daten.            |
   |**Hauptklassifizierungskategorien nach Quellen**     |Zeigt die Anzahl der gefundenen Quellen nach Klassifizierungskategorie an, z. B. **Financial** (Finanzen) oder **Government** (Behörden).      |
   |**Hauptklassifizierungen für Dateien**     |Zeigt die Hauptklassifizierungen an, die auf Dateien in Ihren Daten angewendet werden, z. B. Kreditkartennummern oder nationale Identifikationsnummern.         |
   |**Hauptklassifizierungen für Tabellen**     | Zeigt die Hauptklassifizierungen an, die auf Tabellen in Ihren Daten angewendet werden, z. B. Informationen zur persönlichen Identifikation. |   
   |  **Klassifizierungsaktivität** <br>(Dateien und Tabellen) |  Zeigt separate Graphen für Dateien und Tabellen an, die jeweils die Anzahl der klassifizierten Dateien oder Tabellen über den ausgewählten Zeitraum anzeigen. <br>**Standard:** 30 Tage<br>Wählen Sie den Filter **Zeit** oberhalb der Graphen aus, um einen anderen Zeitrahmen für die Anzeige auszuwählen.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>Drilldown zu Klassifizierungserkenntnissen

Wählen Sie in einer der folgenden Graphen zu **Klassifizierungserkenntnissen** den Link **Mehr anzeigen** aus, um weitere Details zu erhalten:

- **Hauptklassifizierungskategorien nach Quellen**
- **Hauptklassifizierungen für Dateien**
- **Hauptklassifizierungen für Tabellen**
- **Klassifizierungsaktivität > Klassifizierungsdaten**

Beispiel:

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="Alle Klassifizierungen anzeigen" lightbox="media/insights/view-classifications.png":::

Führen Sie einen der folgenden Schritte aus, um weitere Informationen zu erhalten:

|Option  |BESCHREIBUNG  |
|---------|---------|
|**Filtern der Daten**     |  Verwenden Sie die Filter oberhalb des Rasters, um die angezeigten Daten zu filtern, einschließlich des Klassifizierungsnamens, des Abonnementnamens oder des Quellentyps. <br><br>Wenn Sie sich bezüglich des genauen Klassifizierungsnamens nicht sicher sind, können Sie einen Teil oder den ganzen Namen in das Feld **Nach Schlüsselwort filtern** eingeben.       |
|**Sortieren des Rasters** |Wählen Sie einen Spaltenheader aus, um das Raster nach dieser Spalte zu sortieren. | 
|**Spalten bearbeiten**     |  Wenn Sie mehr oder weniger Spalten in Ihrem Raster anzeigen möchten, wählen Sie **Spalten bearbeiten** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: und dann die Spalten aus, die Sie anzeigen oder deren Reihenfolge Sie ändern möchten.   |
|**Weitere Detailinformationen anzeigen**     | Um einen Drilldown für eine bestimmte Klassifizierung auszuführen, wählen Sie einen Namen in der Spalte **Klassifizierung** aus, um den Bericht **Klassifizierung nach Quelle** anzuzeigen. <br><br>In diesem Bericht werden Daten für die ausgewählte Klassifizierung angezeigt, einschließlich Quellenname, Quellentyp, Abonnement-ID und der Anzahl der klassifizierten Dateien und Tabellen.      |
|**Ressourcen durchsuchen**     |  Um die mit einer bestimmten Klassifizierung oder Quelle gefundenen Ressourcen zu durchsuchen, wählen Sie je nach dem betrachteten Bericht eine Klassifizierung oder Quelle und dann **Ressourcen durchsuchen** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: oberhalb der Filter aus. <br><br>Die Suchergebnisse zeigen alle gefundenen klassifizierten Ressourcen für den gewählten Filter an.  Weitere Informationen finden Sie unter [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md).       |
| | |

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Azure Purview-Erkenntnisberichte
> [!div class="nextstepaction"]
> [Glossarerkenntnisse](glossary-insights.md)

> [!div class="nextstepaction"]
> [Überprüfungserkenntnisse](scan-insights.md)

> [!div class="nextstepaction"]
> [Vertraulichkeitsbezeichnungs-Erkenntnisse](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [Dateierweiterungs-Erkenntnisse](file-extension-insights.md)