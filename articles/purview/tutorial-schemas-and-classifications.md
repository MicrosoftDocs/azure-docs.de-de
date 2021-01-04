---
title: 'Tutorial: Erkunden von Ressourcensätzen, Details, Schemas und Klassifizierungen in Azure Purview (Vorschau)'
description: In diesem Tutorial wird beschrieben, wie Sie Ressourcensätze, Ressourcendetails, Schemas und Klassifizierungen verwenden.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549760"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>Tutorial: Erkunden von Ressourcensätzen, Details, Schemas und Klassifizierungen in Azure Purview (Vorschau)

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial untersuchen Sie die Hauptkomponenten des Katalogs: Ressourcensätze, Ressourcendetails, Schemas und Klassifizierungen.

Dieses Tutorial ist *Teil 4 einer fünfteiligen Tutorialreihe*. Es enthält Informationen zur Verwaltung der Datengovernance in einem Datenbestand mithilfe von Azure Purview. Dieses Tutorial basiert auf den Schritten aus [Teil 3: Durchsuchen von Ressourcen in Azure Purview (Vorschau) und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Anzeigen von Ressourcensätzen
> * Anzeigen von Ressourcendetails
> * Bearbeiten des Schemas und Hinzufügen von Klassifizierungen

## <a name="prerequisites"></a>Voraussetzungen

* Abschließen von [Tutorial:  Durchsuchen von Ressourcen in Azure Purview (Vorschau) und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="view-resource-sets"></a>Anzeigen von Ressourcensätzen

Ein Ressourcensatz ist ein einzelnes Objekt im Katalog, das eine große Anzahl von physischen Objekten im Speicher darstellt. Die Objekte verfügen in der Regel über ein gemeinsames Schema und in den meisten Fällen über eine Benennungskonvention oder eine Ordnerstruktur. Beispielsweise lautet das Datumsformat *jjjj/mm/tt*. Weitere Informationen zu Ressourcensätzen finden Sie unter [Grundlegendes zu Ressourcensätzen](concept-resource-sets.md).

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Purview-Ressource, und wählen Sie **Open Purview Studio** (Purview Studio öffnen) aus. Sie werden automatisch zur Startseite von Purview Studio weitergeleitet.

2. Geben Sie im Feld **Search assets** (Ressourcen suchen) den Suchbegriff **contoso_staging_positivecashflow_ n** ein, und wählen Sie dann in den Suchergebnissen **Contoso_Staging_PositiveCashFlow_{N}.csv** aus.

## <a name="view-asset-details"></a>Anzeigen von Ressourcendetails

1. Auf der Registerkarte **Übersicht** werden eine **Beschreibung**, **Glossarbegriffe** und **Eigenschaften** (etwa **qualifiedName**) angezeigt.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="Screenshot: Registerkarte „Übersicht“ auf der Seite eines Ressourcensatzes":::

1. Beachten Sie unter **Eigenschaften** die folgenden beiden Felder:

   * **partitionCount**: Gibt die Anzahl der physischen Dateien an, die mit diesem Ressourcensatz verknüpft sind.
   * **schemaCount**: Gibt die Anzahl der Variationen des Schemas an, die in diesem Ressourcensatz gefunden wurden.

   Azure Purview füllt diese Eigenschaften innerhalb von 24 Stunden nach Abschluss der Überprüfung aus [Teil 1 dieser Tutorialreihe](tutorial-scan-data.md) auf.

1. Wählen Sie die Registerkarte **Kontakte** aus, um die Werte für **Experten** und **Besitzer** zu überprüfen.

## <a name="edit-the-schema-and-add-classifications"></a>Bearbeiten des Schemas und Hinzufügen von Klassifizierungen

1. Wählen Sie die Registerkarte **Schema** aus. Beachten Sie die Spaltennamen und die ihnen zugeordneten Klassifizierungen. Beachten Sie, dass die Eigenschaften bei der Überprüfung automatisch aufgefüllt wurden.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="Screenshot: Registerkarte „Schema“":::

1. Wählen Sie zum Bearbeiten der Ressource oben links die Schaltfläche **Bearbeiten** aus. Wählen Sie anschließend die Registerkarte **Schema** aus.

1. Fügen Sie eine **Beschreibung** für eine Spalte hinzu, oder benennen Sie die Spalte in einen benutzerfreundlicheren Namen um.

1. Fügen Sie eine Klassifizierung auf Ressourcenebene hinzu. Wählen Sie dazu das Dropdownmenü **Column level classification** (Klassifizierung auf Ressourcenebene) für einen Spaltennamen aus, für den keine Klassifizierung festgelegt ist.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="Seite zur Schemabearbeitung":::

1. Wenn Sie mit den Änderungen fertig sind, wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Anzeigen von Ressourcensätzen
> * Anzeigen von Ressourcendetails
> * Bearbeiten des Schemas und Hinzufügen von Klassifizierungen

Fahren Sie mit dem nächsten Tutorial fort, um weitere Informationen zum Glossar zu erhalten und zu erfahren, wie Sie neue Begriffe für Ressourcen definieren und importieren.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)](tutorial-import-create-glossary-terms.md)