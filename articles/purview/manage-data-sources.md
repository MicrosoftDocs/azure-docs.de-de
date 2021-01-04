---
title: Verwalten von Datenquellen in Azure Purview (Vorschau)
description: Erfahren Sie, wie Sie neue Datenquellen registrieren, Sammlungen von Datenquellen verwalten und Quellen in Azure Purview (Vorschau) anzeigen können.
author: mamccrea
ms.author: mamccrea
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 382c79cf77cb22d3cad1af41d6daa662fb00df4c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96781163"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Verwalten von Datenquellen in Azure Purview (Vorschau)

In diesem Artikel erfahren Sie, wie Sie neue Datenquellen registrieren, Sammlungen von Datenquellen verwalten und Quellen in Azure Purview (Vorschau) anzeigen können. Azure Purview unterstützt die folgenden Datenquellen:

* Lokaler SQL Server
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob Storage
* Azure-Daten-Explorer
* Azure SQL-Datenbank
* Verwaltete Azure SQL-Datenbank-Instanz
* Azure Synapse Analytics (ehemals SQL DW)
* Azure Cosmos DB
* Power BI
* Teradata (Preview)

## <a name="register-a-new-source"></a>Registrieren einer neuen Quelle

Verwenden Sie die folgenden Schritte, um eine neue Quelle zu registrieren.

1. Öffnen Sie Purview Studio, und wählen Sie die Kachel **Quellen registrieren** aus.

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure Purview Studio":::

1. Wählen Sie **Registrieren** und dann einen Quelltyp aus. In diesem Beispiel wird Azure Blob Storage verwendet. Wählen Sie **Weiter**.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="Auswählen eines Datenquellentyps auf der Seite „Quellen registrieren“":::

1. Füllen Sie das Formular auf der Seite **Quellen registrieren** aus. Wählen Sie einen Namen für Ihre Quelle aus, und geben Sie die entsprechenden Informationen ein. Wenn Sie **Aus Azure-Abonnement** als Kontoauswahlmethode ausgewählt haben, werden die Quellen in Ihrem Abonnement in einer Dropdownliste angezeigt. Alternativ können Sie Ihre Quellinformationen auch manuell eingeben.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="Formular für Datenquelleninformationen":::

1. Wählen Sie **Fertig stellen** aus.

## <a name="view-sources"></a>Anzeigen von Quellen

Sie können alle registrierten Quellen auf der Registerkarte **Quellen** von Azure Purview Studio anzeigen. Es gibt zwei Ansichtsarten: Kartenansicht und Listenansicht.

### <a name="map-view"></a>Kartenansicht

In der Kartenansicht werden alle Ihre Quellen und Sammlungen angezeigt. In der folgenden Abbildung gibt es eine Azure Blob Storage-Quelle. Von jeder Quellenkachel aus können Sie die Quelle bearbeiten, eine neue Überprüfung starten oder Quellendetails anzeigen.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Kartenansicht der Azure Purview-Datenquelle":::

### <a name="list-view"></a>Listenansicht

In der Listenansicht wird eine sortierbare Liste der Quellen angezeigt. Bewegen Sie den Mauszeiger über die Quelle, um Optionen zum Bearbeiten, Löschen einer Überprüfung oder zum Starten einer neuen Überprüfung anzuzeigen.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Listenansicht der Azure Purview-Datenquelle":::

## <a name="manage-collections"></a>Verwalten von Sammlungen

Sie können Ihre Datenquellen in Sammlungen gruppieren. Um eine neue Sammlung zu erstellen, wählen Sie **+ Neue Sammlung** auf der Seite *Quellen* von Azure Purview Studio aus. Benennen Sie die Sammlung, und wählen Sie *Keine* als übergeordnetes Element aus. Die neue Sammlung wird in der Kartenansicht angezeigt.

Um Quellen zu einer Sammlung hinzuzufügen, wählen Sie den Stift zum **Bearbeiten** für die Quelle und dann eine Sammlung aus dem Dropdownmenü **Sammlung auswählen** aus.

Weisen Sie zum Erstellen einer Hierarchie von Sammlungen entsprechende Sammlungen auf höherer Ebene als übergeordnete Sammlungen zu untergeordneten Sammlungen zu. In der folgenden Abbildung ist *Fabrikam* ein übergeordnetes Element der Sammlung *Finance* (Finanzen), die eine Azure Blob Storage-Datenquelle enthält. Sie können Sammlungen erweitern oder reduzieren, indem Sie auf den Kreis klicken, der dem Pfeil zwischen den Ebenen angefügt ist.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Eine Hierarchie von Sammlungen in Azure Purview Studio":::

Sie können Quellen aus einer Hierarchie entfernen, indem Sie *Keine* für das übergeordnete Element auswählen. Nicht übergeordnete Quellen werden in der Kartenansicht in einem gepunkteten Feld gruppiert, ohne dass sie durch Pfeile mit übergeordneten Elementen verbunden sind.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie verschiedene Datenquellen registrieren und überprüfen können:

* [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)
* [Power BI-Mandant](register-scan-power-bi-tenant.md)
* [Azure SQL-Datenbank](register-scan-azure-sql-database.md)
