---
title: Erstellen, Importieren und Exportieren von Glossarbegriffen
description: Erfahren Sie, wie Sie Glossarbegriffe in Azure Purview erstellen, importieren und exportieren.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/02/2020
ms.openlocfilehash: a693761bcecab87e343014127ad37077c2569e21
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550345"
---
# <a name="how-to-create-import-and-export-glossary-terms"></a>Erstellen, Importieren und Exportieren von Glossarbegriffen

In diesem Artikel ist beschrieben, wie Sie einen Unternehmensglossarbegriff im Azure Purview-Datenkatalog erstellen und Glossarbegriffe über CSV-Dateien importieren und exportieren.

## <a name="create-a-new-term"></a>Erstellen eine neuen Begriffs

Führen Sie die folgenden Schritte aus, um einen neuen Glossarbegriff zu erstellen:

1. Wählen Sie das Glossarsymbol im linken Navigationsbereich auf der Startseite aus, um zur Seite mit der Begriffsliste zu gelangen.

2. Wählen Sie auf der Seite **Glossarbegriffe** die Option **+ Neuer Begriff** aus. Es wird eine Seite geöffnet, auf der die Vorlage **Systemstandard** ausgewählt ist. Wählen Sie die Vorlage aus, mit der Sie den Glossarbegriff erstellen möchten, und wählen Sie **Weiter** aus.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-with-default-template.png" alt-text="Screenshot zum Erstellen eines neuen Begriffs" border="true":::

3. Geben Sie Ihrem neuen Begriff einen Namen. Dieser Name muss im Katalog eindeutig sein. Für den Namen des Begriffs wird die Groß-/Kleinschreibung beachtet. Dies bedeutet, dass Sie im Katalog einen Begriff namens **Beispiel** und einen Begriff namens **beispiel** haben können.

4. Fügen Sie eine **Definition** hinzu.

5. Legen Sie den **Status** für den Begriff fest. Ein neuer Begriff hat standardmäßig den Status **Entwurf**.

   :::image type="content" source="media/how-to-create-import-export-glossary/new-term-options.png" alt-text="Screenshot der Statusoptionen" border="true":::

   Diese Statuskennzeichnungen sind Metadaten, die mit dem Begriff verknüpft sind. Derzeit können Sie für jeden Begriff die folgenden Status festlegen:

   - **Entwurf**: Dieser Begriff ist noch nicht offiziell implementiert.
   - **Genehmigt**: Dieser Begriff ist ein offizieller/Standard-/genehmigter Begriff.
   - **Abgelaufen:** Dieser Begriff sollte nicht mehr verwendet werden.
   - **Warnung**: Dieser Begriff erfordert Aufmerksamkeit.

6. Fügen Sie **Ressourcen** und ein **Akronym** hinzu. Wenn der Begriff Bestandteil einer Hierarchie ist, können Sie auf der Registerkarte „Übersicht“ übergeordnete Begriffe in **Übergeordnet** hinzufügen.

7. Fügen Sie **Synonyme** und **Verwandte Begriffe** auf der Registerkarte „Verknüpft“ hinzu.

   :::image type="content" source="media/how-to-create-import-export-glossary/related-tab.png" alt-text="Screenshot von „Neuer Begriff“ > Registerkarte „Verknüpft“" border="true":::

8. Wählen Sie optional die Registerkarte **Kontakte** aus, um dem Begriff Experten und Stewards hinzuzufügen.

9. Wählen Sie **Erstellen** aus, um den Begriff zu erstellen.

## <a name="import-terms-into-the-glossary"></a>Importieren von Begriffen in das Glossar

Mit dem Azure Purview-Datenkatalog wird eine CSV-Vorlagendatei bereitgestellt, mit der Sie Ihre Begriffe in Ihr Glossar importieren können.

Sie können die Begriffe in den Katalog importieren. Die doppelten Begriffe in der Datei werden überschrieben.

Beachten Sie, dass für die Begriffsnamen die Groß-/Kleinschreibung beachtet wird. Beispielsweise können `Sample` und `saMple` im selben Glossar vorhanden sein.

### <a name="to-import-terms-follow-these-steps"></a>Importieren von Begriffen

1. Wenn Sie sich auf der Seite **Glossarbegriffe** befinden, wählen Sie **Begriffe importieren** aus.

2. Der Vorlagenseite für Begriffe wird geöffnet. Verknüpfen Sie die Begriffsvorlage mit der Art von CSV-Datei, die Sie importieren möchten.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-term-template-for-import.png" alt-text="Screenshot der Seite „Glossarbegriffe“, Schaltfläche „Begriffe importieren“":::

3. Laden Sie die CSV-Vorlage herunter, und verwenden Sie diese, um die Begriffe einzugeben, die Sie hinzufügen möchten.

   > [!Important]
   > Das System unterstützt nur das Importieren von Spalten, die in der Vorlage verfügbar sind. Die Vorlage „Systemstandard“ enthält alle Standardattribute.
   > Benutzerdefinierte Begriffsvorlagen enthalten sowohl die Standardattribute als auch zusätzliche benutzerdefinierte Attribute, die in der Vorlage definiert sind. Daher unterscheidet sich die CSV-Datei je nach ausgewählter Begriffsvorlage sowohl hinsichtlich der Gesamtzahl von Spalten als auch hinsichtlich der Spaltennamen. Sie können die Datei nach dem Hochladen auch auf Probleme überprüfen.

   :::image type="content" source="media/how-to-create-import-export-glossary/select-file-for-import.png" alt-text="Screenshot der Seite „Glossarbegriffe“, zu importierende Datei auswählen":::

4. Nachdem Sie die CSV-Datei ausgefüllt haben, wählen Sie die zu importierende Datei aus, und wählen Sie dann **OK** aus.

5. Das System lädt die Datei hoch und fügt ihrem Katalog alle Begriffe hinzu.

## <a name="export-terms-from-glossary-with-custom-attributes"></a>Exportieren von Begriffen aus einem Glossar mit benutzerdefinierten Attributen

Sie können Begriffe aus dem Glossar exportieren, solange die ausgewählten Begriffe zur selben Begriffsvorlage gehören.

1. Wenn Sie sich im Glossar befinden, ist die Schaltfläche **Exportieren** standardmäßig deaktiviert. Nachdem Sie die Begriffe ausgewählt haben, die Sie exportieren möchten, ist die Schaltfläche **Exportieren** aktiviert, wenn die ausgewählten Begriffe zur selben Vorlage gehören.

2. Wählen Sie **Exportieren** aus, um die ausgewählten Begriffe herunterzuladen.

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)](tutorial-import-create-glossary-terms.md). Hier finden Sie weitere Informationen.
