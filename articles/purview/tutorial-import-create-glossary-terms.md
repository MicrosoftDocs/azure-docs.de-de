---
title: 'Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)'
description: In diesem Tutorial erfahren Sie, wie Sie Glossarbegriffe erstellen, wie Sie Glossarbegriffe einer Ressource hinzufügen und wie Sie Glossarbegriffe importieren.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 4dc8dd84c22d094c87c82f5a920015f886780df2
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399807"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Ein Glossar ist ein wichtiges Tool für die Verwaltung und Strukturierung Ihres Katalogs. Sie erstellen Ihr Glossar, indem Sie neue Begriffe definieren oder eine Begriffsliste importieren und die Begriffe dann auf Ihre Ressourcen anwenden.

Dieses Tutorial ist *Teil 5 einer fünfteiligen Tutorialreihe*. Es enthält Informationen zur Verwaltung der Datengovernance in einem Datenbestand mithilfe von Azure Purview. Dieses Tutorial basiert auf den Schritten aus [Teil 4: Erkunden von Ressourcensätzen, Details, Schemas und Klassifizierungen in Azure Purview (Vorschau)](tutorial-schemas-and-classifications.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen von Glossarbegriffen
> * Hinzufügen von Glossarbegriffen zu einer Ressource
> * Importieren von Glossarbegriffen

## <a name="prerequisites"></a>Voraussetzungen

* Abschließen von [Tutorial: Erkunden von Ressourcensätzen, Details, Schemas und Klassifizierungen in Azure Purview (Vorschau)](tutorial-schemas-and-classifications.md).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-glossary-terms"></a>Glossarbegriffe erstellen

In dem Glossar können Sie geschäftsspezifische oder technische Begriffe erstellen. Außerdem können Sie Ihre Ressourcen mit Anmerkungen versehen, indem Sie Begriffe auf sie anwenden.

Im Anschluss finden Sie eine Zusammenfassung häufig verwendeter Felder auf der Seite für **Glossarbegriffe:**

* **Status:** Weisen Sie dem Begriff einen Status zu (**Entwurf**, **Genehmigt**, **Abgelaufen** oder **Warnung**).

* **Definition**: Geben Sie eine Definition des Begriffs ein.

* **Akronym**: Geben Sie eine abgekürzte Version des Begriffs ein. Verwenden Sie dabei jeweils den Anfangsbuchstaben der einzelnen Wörter.

* **Synonyme**: Wählen Sie andere Begriffe mit gleicher oder ähnlicher Definition aus.

* **Related Terms** (Verwandte Begriffe): Wählen Sie andere Begriffe im Glossar aus, die mit diesem Begriff zusammenhängen, aber eine andere Definition haben. Beispiele wären etwa technische Begriffe, die mit einem geschäftlichen Begriff zusammenhängen, ein Codename oder andere Begriffe, die diesem Begriff zuzuordnen sind.

Gehen Sie zum Erstellen eines Glossarbegriffs wie folgt vor:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Purview-Ressource, und wählen Sie **Open Purview Studio** (Purview Studio öffnen) aus. Sie werden automatisch zur Startseite von Purview Studio weitergeleitet.

1. Wählen Sie im linken Bereich **Glossar** aus, um die Seite **Glossarbegriffe** zu öffnen.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="Screenshot: Seite „Glossarbegriffe“":::

1. Wählen Sie **Neuer Begriff** > **Systemstandard** > **Weiter** aus.

1. Geben Sie auf der Registerkarte **Übersicht** der Seite **Neuer Begriff** unter **Name** den Namen für den neuen Begriff ein: *Financial* (Finanzen).

1. Geben Sie die **Definition** ein: *This term represents financial information for Contoso Inc.* (Finanzielle Informationen für Contoso Inc.)

1. Wählen Sie in der Dropdownliste **Status** die Option **Genehmigt** aus.

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="Screenshot: Erstellen eines neuen Glossarbegriffs":::

## <a name="add-glossary-terms-to-an-asset"></a>Hinzufügen von Glossarbegriffen zu einer Ressource

1. Suchen Sie mithilfe der in [Teil 1 dieser Tutorialreihe](tutorial-scan-data.md) vermittelten Schritte nach einer Ressource. Beispiel: **Contoso_CashFlow_{N}.csv**.

1. Wählen Sie auf der Detailseite der Ressource die Option **Bearbeiten** aus.

1. Wählen Sie auf der Registerkarte **Übersicht** in der Dropdownliste **Glossarbegriffe** die Option **Financial** (Finanzen) und anschließend **Speichern** aus.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="Screenshot: Hinzufügen eines Glossarbegriffs zu einer Ressource":::

1. Navigieren Sie auf der Registerkarte **Übersicht** zum Abschnitt **Glossarbegriffe**. Dort sehen Sie, dass der Begriff *Financial* (Finanzen) auf die Ressource angewendet wurde.

## <a name="import-glossary-terms"></a>Importieren von Glossarbegriffen

Wenn Sie Begriffe importieren oder mehrere bereits vorhandene Begriffe auf einmal aktualisieren möchten, können Sie eine vorab aufgefüllte Vorlage in Ihr Glossar hochladen.

In den folgenden Schritten werden Glossarbegriffe mithilfe einer CSV-Datei importiert:

1. Rufen Sie sich den Speicherort der Datei *StarterKitTerms.csv* aus dem Starter Kit ins Gedächtnis, das Sie im Rahmen von [Teil 1 dieser Tutorialreihe](tutorial-scan-data.md) heruntergeladen haben.

   Diese Datei enthält eine Liste vorab aufgefüllter Begriffe, die für Ihren Datenbestand relevant sind.

1. Wählen Sie zum Starten des Importvorgangs **Glossar** und anschließend **Import terms** (Begriffe importieren) aus.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="Screenshot: Importieren von Glossarbegriffen":::

1. Wählen Sie auf der Seite **Import terms** (Begriffe importieren) die Option **Systemstandard** und anschließend **Weiter** aus.

1. Wählen Sie **Durchsuchen** aus, navigieren Sie zu dem Speicherort, an den Sie *StarterKitTerms.csv* heruntergeladen haben, und wählen Sie **Öffnen** aus.

1. Wählen Sie **OK** aus, um mit dem Importieren der Bedingungen zu beginnen.

   Nach Abschluss des Importvorgangs werden die neuen Glossarbegriffe auf der Seite **Glossarbegriffe** angezeigt.

1. Sehen Sie sich jeden der neu importierten Begriffe an, um die jeweilige Definition zu überprüfen.

## <a name="create-custom-term-templates"></a>Erstellen benutzerdefinierter Begriffsvorlagen

In diesem Abschnitt erfahren Sie, wie Sie eine benutzerdefinierte Begriffsvorlage mit benutzerdefinierten Attributen erstellen und Daten mithilfe einer CSV-Vorlagendatei importieren.

Unter **Glossar** > **Manage term templates** (Begriffsvorlagen verwalten) > **System** finden Sie mehrere bereits vorhandene Systembegriffsvorlagen.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="Systembegriffsvorlagen":::

Wenn Sie eine neue benutzerdefinierte Begriffsvorlage erstellen möchten, führen Sie die folgenden Schritte aus:

1. Wählen Sie im linken Menü die Option **Glossar** aus.
1. Wählen Sie **Manage term templates** (Begriffsvorlagen verwalten) > **Benutzerdefiniert** > **New term template** (Neue Begriffsvorlage) aus.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="Erstellen einer neuen benutzerdefinierten Begriffsvorlage":::

Gehen Sie auf dem Bildschirm **New term template** (Neue Begriffsvorlage) wie folgt vor:

1. Geben Sie unter **Vorlagenname** einen Vorlagennamen ein: `Sensitivity level`.
1. Geben Sie eine Beschreibung ein. Beispiel: `Indicates the level of sensitivity for this data.`
1. Wählen Sie **+ New attribute** (+ Neues Attribut) aus, um ein Dialogfeld zum Hinzufügen von Attributen zu öffnen.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="Startbildschirm für neue Begriffsvorlage":::

1. Geben Sie auf dem Bildschirm **New attribute** (Neues Attribut) die folgenden Parameter ein:

   |Einstellung|Vorgeschlagener Wert|
   |---------|-----------|
   |Attributname|„is sensitive information“ (Vertrauliche Informationen)|
   |Feldtyp|dropdown|„Single choice“ (Einzelauswahl)|
   |„Mark as required“ (Als erforderlich markieren)|Aktivieren Sie dieses Kontrollkästchen.|
   |„+ Add a choice“ (+ Auswahloption hinzufügen)| Fügen Sie zwei Auswahloptionen hinzu: „Yes“ (Ja) und „No“ (Nein).|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="Hinzufügen eines neuen Attributs":::

1. Wiederholen Sie die obigen Schritte, um ein weiteres Attribut mit dem Namen `can be shared externally`hinzuzufügen. Wählen Sie nach dem Hinzufügen beider Attribute die Option **Erstellen** aus.

## <a name="import-terms-from-a-template"></a>Importieren von Begriffen aus einer Vorlage

Als Nächstes wird unter Verwendung der von Ihnen erstellten Vorlage **Sensitivity level** (Vertraulichkeitsstufe) ein Begriff importiert. 

1. Wählen Sie auf dem Bildschirm **Glossarbegriffe** die Option **Import terms** (Begriffe importieren) aus.

1. Wählen auf dem Bildschirm **Import terms** (Begriffe importieren) die Option **Sensitivity level** (Vertraulichkeitsstufe) aus. Wählen Sie **Weiter**.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="Auswählen von „Sensitivity level“ (Vertraulichkeitsstufe)":::

1. Die Begriffsvorlage für **Sensitivity level** (Vertraulichkeitsstufe) enthält standardmäßige Systemattribute sowie die neuen Attribute `can be shared externally` und `is sensitive information`, die von Ihnen hinzugefügt wurden. Wählen Sie die Option zum Herunterladen einer **CSV-Beispieldatei aus.**

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="Herunterladen einer CSV-Beispieldatei":::

1. Daraufhin wird eine Vorlagendatei heruntergeladen. Diese können Sie bearbeiten, um neue Glossarbegriffe mit Kundenattributen hochzuladen. Öffnen Sie die heruntergeladene CSV-Datei. Fügen Sie in der CSV-Datei neue Begriffe und entsprechende Werte als neue Zeilen hinzu.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="Erstellen eines Begriffs in der CSV-Datei":::

   In der Spalte **Related Terms** (Verwandte Begriffe) oder **Synonyms** (Synonyme) enthaltene Begriffe, die noch nicht vorhanden sind, werden erstellt, wenn die Datei hochgeladen wird. Bei der Erstellung wird die Vorlage **Systemstandard** verwendet.

1. Kehren Sie zum Hochladen der Datei zum Bildschirm **Import terms** (Begriffe importieren) zurück, und wählen Sie neben dem Feld **Select the completed .CSV file to upload** (Abgeschlossene CSV-Datei zum Hochladen auswählen) die Schaltfläche **Durchsuchen** aus. Wählen Sie im daraufhin angezeigten Dialogfeld Ihre Datei und anschließend **OK** aus.

1. Die neuen Begriffe werden nun auf dem Bildschirm **Glossarbegriffe** aufgeführt. Sie können Details zu den neuen Begriffen anzeigen, indem Sie in der Liste auf den Begriffsnamen klicken.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

> [!div class="checklist"]
>
> * Erstellen von Glossarbegriffen
> * Hinzufügen von Glossarbegriffen zu einer Ressource
> * Importieren von Glossarbegriffen

Im nächsten Artikel finden Sie Informationen zu verschiedenen Katalogerkenntnissen:

> [!div class="nextstepaction"]
> [Grundlegendes zu Erkenntnissen in Azure Purview](concept-insights.md)
