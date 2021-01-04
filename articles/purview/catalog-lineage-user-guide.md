---
title: Leitfaden zur Datenkatalogherkunft (Vorschauversion)
description: Dieser Artikel enthält eine Übersicht über das Feature für „Katalogherkunft“ von Azure Purview.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: a319dbce2502f35272cf9b70da2022f581d64275
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550882"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure Purview: Leitfaden zur Datenkatalogherkunft

Dieser Artikel enthält eine Übersicht über das Datenherkunft-Feature im Azure Purview-Datenkatalog.

## <a name="background"></a>Hintergrund

Ein Plattformfeature von Azure Purview ist die Möglichkeit zum Anzeigen der Herkunft für Datasets, die von Datenprozessen erstellt werden. In Systemen wie Data Factory, Data Share und Power BI wird die Herkunft von Daten erfasst, während diese verschoben werden. Die benutzerdefinierte Berichterstellung zur Herkunft wird auch für Atlas-Hooks und die REST-API unterstützt.

## <a name="lineage-collection"></a>Erfassung der Herkunftsinformationen 
 Metadaten, die in Azure Purview für Datensysteme von Unternehmen erfasst werden, werden verknüpft, um umfassende Informationen zur Datenherkunft anzuzeigen. Datensysteme, bei denen Herkunftsinformationen in Purview erfasst werden, lassen sich grob in drei Kategorien unterteilen, die im Folgenden beschrieben werden.

### <a name="data-processing-system"></a>Datenverarbeitungssystem
Datenintegrations- und ETL-Tools können die Herkunftsinformationen zur Ausführungszeit per Pushvorgang an Azure Purview übertragen. Zu dieser Kategorie von Datensystemen gehören Tools wie Data Factory, Data Share, Synapse, Azure Databricks usw. Die Datenverarbeitungssysteme verweisen auf Datasets als Quelle unterschiedlicher Datenbanken und Speicherlösungen für die Erstellung von Zieldatasets. Die Datenverarbeitungssysteme, die in Bezug auf die Herkunft derzeit in Purview integriert sind, sind unten in der Tabelle aufgeführt.


| Datenverarbeitungssystem | Unterstützter Bereich |
| ---------------------- | ------------|
| Azure Data Factory | [Kopieraktivität](how-to-link-azure-data-factory.md#data-factory-copy-data-support) <br> [Datenflussaktivität](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [Aktivität „SSIS-Paket ausführen“](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [Momentaufnahme freigeben](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Datenspeichersysteme
Datenbanken und Speicherlösungen, z. B. SQL Server, Teradata und SAP, verfügen über Abfragemodule zum Transformieren von Daten per Skriptsprache. Die Informationen zur Datenherkunft aus gespeicherten Prozeduren werden in Purview gesammelt und mit den Herkunftsinformationen anderer Systeme verknüpft.

| Datenspeichersystem | Unterstützter Bereich |
| ---------------------- | ------------|
| Teradata | Gespeicherte Prozeduren

### <a name="data-analytics--reporting-systems"></a>Datenanalyse- und Berichterstellungssysteme
Datensysteme wie Azure ML und Power BI melden die Informationen zur Herkunft an Azure Purview. Von diesen Systemen werden die Datasets aus Speichersystemen verwendet und mit dem zugehörigen Metamodell verarbeitet, um das BI-Dashboard, ML-Experimente usw. zu erstellen.

| Datenanalyse- und Berichterstellungssystem | Unterstützter Bereich |
| ---------------------- | ------------|
| Power BI | [Datasets, Datenflüsse, Berichte und Dashboards](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Erste Schritte für Herkunft

Die Herkunft in Purview umfasst Datasets und Prozesse. Datasets werden auch als Knoten bezeichnet, während für Prozesse auch die Bezeichnung „Edges“ verwendet wird:

* **Dataset (Knoten)** : Ein Dataset (strukturiert oder unstrukturiert), das als Eingabe für einen Prozess bereitgestellt wird. Eine SQL-Tabelle, ein Azure-Blob und Dateien (z. B. vom Typ CSV und XML) werden beispielsweise als Datasets angesehen. Im Bereich „Herkunft“ von Purview werden Datasets als rechteckige Felder dargestellt.

* **Prozess (Edge)** : Eine Aktivität oder Transformation, die für ein Dataset durchgeführt wird, wird als Prozess bezeichnet. Beispiele hierfür sind eine ADF-Kopieraktivität, eine Data Share-Momentaufnahme usw. Im Bereich „Herkunft“ von Purview werden Prozesse als Felder mit abgerundeten Ecken dargestellt.

Führen Sie die folgenden Schritte aus, um für eine Ressource in Purview auf die Informationen zur Herkunft zuzugreifen:

1. Navigieren Sie im Azure-Portal zur [Seite mit den Azure Purview-Konten](https://aka.ms/purviewportal).

1. Wählen Sie in der Liste Ihr Azure Purview-Konto und dann auf der Seite **Übersicht** die Option **Launch Purview account** (Purview-Konto starten) aus.

1. Suchen Sie auf der **Startseite** von Azure Purview nach einem Datasetnamen oder dem Prozessnamen, z. B. ADF-Kopier- oder Datenflussaktivität. Drücken Sie anschließend die EINGABETASTE.

1. Wählen Sie in den Suchergebnissen die Ressource und dann die Registerkarte **Herkunft** aus.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Screenshot: Auswählen der Registerkarte „Herkunft“" border="true":::

## <a name="asset-level-lineage"></a>Herkunft auf Ressourcenebene

Azure Purview unterstützt die Anzeige von Herkunftsinformationen auf Ressourcenebene für die Datasets und Prozesse. Navigieren Sie zum Anzeigen der Herkunftsinformationen auf Ressourcenebene zur Registerkarte **Herkunft** der aktuellen Ressource im Katalog. Wählen Sie den aktuellen Datasetressourcen-Knoten aus. Standardmäßig wird im linken Bereich die Liste mit den Spalten der Daten angezeigt.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Screenshot: Auswählen der Spaltenanzeige auf der Seite „Herkunft“" border="true":::

## <a name="column-level-lineage"></a>Herkunft auf Spaltenebene

Azure Purview unterstützt die Anzeige von Herkunftsinformationen auf Spaltenebene für die Datasets. Navigieren Sie zum Anzeigen der Herkunftsinformationen auf Spaltenebene zur Registerkarte **Herkunft** für die aktuelle Ressource im Katalog, und führen Sie die hier angegebenen Schritte aus:

1. Wählen Sie auf der Registerkarte „Herkunft“ im linken Bereich jeweils das Kontrollkästchen neben einer Spalte aus, für die die Informationen zur Datenherkunft angezeigt werden sollen.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Screenshot: Auswählen von Spalten für die Anzeige auf der Seite „Herkunft“" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

1. Bewegen Sie den Mauszeiger im linken Bereich auf eine ausgewählte Spalte oder auf das Dataset in der Canvas für die Herkunft, um die Spaltenzuordnung anzuzeigen. Alle Spalteninstanzen werden hervorgehoben.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Screenshot: Bewegen des Mauszeigers auf einen Spaltennamen zum Hervorheben des Spaltenflusses eines Datenherkunftspfads" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

1. Falls die Spaltenanzahl zu hoch für die Anzeige im linken Bereich ist, können Sie die Filteroption verwenden, um eine bestimmte Spalte anhand des Namens auszuwählen. Die Alternative besteht darin, mit der Maus durch die Liste zu scrollen.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Screenshot: Filtern von Spalten nach Spaltenname auf der Seite „Herkunft“" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

1. Wenn die Canvas für die Herkunft weitere Knoten und Edges enthält, können Sie den Filter nutzen, um Datenressourcen oder Prozessknoten anhand des Namens auszuwählen. Alternativ können Sie das Fenster „Herkunft“ schwenken, indem Sie die Maus verwenden.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Screenshot: Anzeigen von Datenressourcenknoten anhand des Namens auf der Seite „Herkunft“" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

1. Verwenden Sie die Umschaltfläche im linken Bereich, um die Liste mit den Datasets auf der Canvas für die Herkunft hervorzuheben. Wenn Sie die Umschaltfläche auf „Aus“ festlegen, werden alle Ressourcen angezeigt, die mindestens eine der ausgewählten Spalten enthalten. Wenn Sie die Umschaltfläche auf „Ein“ festlegen, werden nur Datasets angezeigt, die alle Spalten enthalten.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Screenshot: Verwenden der Umschaltfläche zum Filtern der Liste mit den Knoten auf der Seite „Herkunft“" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

1. Sie können für eine Ressource jeweils die Option **Switch to asset** (Zu Ressource wechseln) auswählen, um die entsprechenden Metadaten in der Ansicht „Herkunft“ anzuzeigen. Dies ist eine effektive Möglichkeit, um über die Ansicht „Herkunft“ auf eine andere Ressource im Katalog zuzugreifen.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Screenshot: Auswählen von „Switch to asset“ (Zu Ressource wechseln) für eine Herkunftsdatenressource" lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

1. Bei beliebten Datasets kann die Canvas für die Herkunft unübersichtlich werden. Aus Gründen der Übersichtlichkeit werden in der Standardansicht für eine Ressource jeweils nur fünf Herkunftsebenen angezeigt. Die restlichen Herkunftsinformationen können angezeigt werden, indem Sie in der Canvas für die Herkunft auf die Blasen klicken. Datenconsumer können in der Canvas Ressourcen, die nicht von Interesse sind, auch ausblenden. Zur weiteren Verbesserung der Übersichtlichkeit können Sie oben in der Canvas für die Herkunftsinformationen die Umschaltfläche **More Lineage** (Mehr Herkunftsdaten) auf „Aus“ festlegen. Dies bewirkt, dass in der Canvas für die Herkunftsinformationen alle Blasen ausgeblendet werden.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Screenshot: Umschaltfläche „More lineage“ (Mehr Herkunftsdaten)" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

1. Verwenden Sie die intelligenten Schaltflächen der Canvas für die Herkunftsinformationen, um eine optimale Darstellung der Herkunftsinformationen zu erhalten. Optionen wie „Automatisches Layout“, „Mit Zoom anpassen“, „Vergrößern/Verkleinern“, „Vollbild“ und „Navigationskarte“ sind verfügbar, um im Katalog für die Herkunftsinformationen für eine immersive Benutzeroberfläche zu sorgen.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Screenshot: Auswählen der intelligenten Schaltflächen für „Herkunft“" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Nächste Schritte

* [Link zu Azure Data Factory für Herkunft](how-to-link-azure-data-factory.md)
* [Link zu Azure Data Share für Herkunft](how-to-link-azure-data-share.md)