---
title: 'Gewusst wie: Browsen im Datenkatalog'
description: Dieser Artikel enthält eine Übersicht über das Browsen im Azure Purview-Datenkatalog basierend auf dem Ressourcentyp.
author: hrasheed-msft
ms.author: hrasheed
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: b8cdbbc29472ae10920c347dde308c352bf0b68a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550355"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Browsen im Azure Purview-Datenkatalog

In diesem Artikel wird beschrieben, wie Sie Daten in Ihrem Azure Purview-Datenkatalog ermitteln, indem Sie den hierarchischen Namespace der Datenquelle verwenden.

## <a name="browse-experience"></a>Suchoberfläche

Ein Datenconsumer kann Daten ermitteln, indem er den vertrauten hierarchischen Namespace für die einzelnen Datenquellen in einer Explorer-Ansicht nutzt. Nachdem die Datenquelle registriert und überprüft wurde, werden von der Datenzuordnung Informationen zur Struktur (hierarchischer Namespace) der Datenquelle extrahiert. Diese Informationen werden verwendet, um die Suchoberfläche zur Datenermittlung zu erstellen.

Beispielsweise können Sie in Azure Data Lake Storage Gen 2 ein Dataset mit dem Namen *DateDimension* im Ordner *Dimensions* leicht finden. Sie können die Option „Nach Ressourcentyp durchsuchen“ verwenden, um zum ADLS Gen 2-Speicherkonto zu navigieren. Greifen Sie anschließend auf „Dienst“ > „Container“ > „Ordner“ zu, um zum entsprechenden Ordner *Dimensions* zu gelangen, unter dem die Tabelle *DateDimension* angezeigt wird.

Für jede entsprechende Datenquelle ist eine native Suchoberfläche mit einem hierarchischen Namespace vorhanden.

## <a name="browse-the-data-catalog-by-asset-type"></a>Durchsuchen des Datenkatalogs anhand des Ressourcentyps

1. Sie können Datenressourcen durchsuchen, indem Sie auf der Startseite die Option **Nach Ressourcentyp durchsuchen** auswählen.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview-Startseite" border="true":::

1. Auf der Seite **Browse asset types** (Ressourcentypen durchsuchen) sind die Kacheln nach Datenquelle kategorisiert. Wählen Sie die entsprechende Kachel aus, um die Ressourcen der einzelnen Datenquellen weiter zu erkunden.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="Seite „Browse asset types“ (Ressourcentypen durchsuchen)" border="true":::

1. Auf der nächsten Seite sind unter dem von Ihnen ausgewählten Datentyp die Ressourcen der obersten Ebene aufgelistet. Wählen Sie eine der Ressourcen aus, um ihren Inhalt eingehender zu untersuchen.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="Seite für Suche anhand des Ressourcentyps: Beispiel für Azure Storage-Konto" border="true":::

1. Die Explorer-Ansicht wird geöffnet. Wählen Sie im linken Bereich die Ressource aus, um die Suche zu starten. Untergeordnete Ressourcen werden im rechten Bereich der Seite aufgelistet.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="Explorer-Ansicht" border="true":::

1. Wählen Sie ganz rechts den Namen oder die Schaltfläche mit den Auslassungszeichen aus, um die Details einer Ressource anzuzeigen.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="Auswählen der Auslassungszeichen-Schaltfläche zum Anzeigen der Seite mit den Ressourcendetails" border="true":::

## <a name="view-related-data-assets"></a>Anzeigen verknüpfter Datenobjekte

Wenn Sie sich auf der Seite mit den Ressourcendetails befinden, können Sie auch verwandte Datenressourcen anzeigen. Beispielsweise können Sie zum übergeordneten Ordner von *DateDimension* navigieren, um den Ordner *Dimensions* anzuzeigen, oder auch zum Container, um die in der Hierarchie enthaltenen Ressourcen anzuzeigen.

1. Wählen Sie auf der Seite mit den Ressourcendetails die Registerkarte **Verwandt** aus, um Informationen zu verwandten Ressourcen anzuzeigen.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="Registerkarte „Verwandt“" border="true":::

1. Die vollständige Hierarchie der aktuellen Ressource wird auf der linken Seite angezeigt.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="Hierarchische Struktur" border="true":::

1. Wählen Sie in der Hierarchie eine beliebige Ebene aus, um die unmittelbar untergeordneten Ressourcen dieser Ebene aufzulisten.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="Auswählen einer anderen Hierarchie" border="true":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen, Importieren und Exportieren von Glossarbegriffen](how-to-create-import-export-glossary.md)
- [Verwalten von Begriffsvorlagen für ein Unternehmensglossar](how-to-manage-term-templates.md)
