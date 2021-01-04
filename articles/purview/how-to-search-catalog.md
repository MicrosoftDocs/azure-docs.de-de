---
title: 'Gewusst wie: Suchen im Datenkatalog'
description: Dieser Artikel enthält eine Übersicht dazu, wie in einem Datenkatalog gesucht wird.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550425"
---
# <a name="search-the-azure-purview-data-catalog"></a>Suchen im Azure Purview-Datenkatalog

In diesem Artikel ist beschrieben, wie Sie die verschiedenen Suchfunktionen im Azure Purview-Datenkatalog verwenden.

## <a name="search-the-catalog-for-assets"></a>Durchsuchen des Katalogs nach Objekten (Assets)

Die Schritte zum Ausführen einer Assetsuche sehen wie folgt aus:

1. [Öffnen Sie das Dialogfeld zur Assetsuche](#open-the-asset-search-dialog), indem Sie **Katalog durchsuchen** auswählen.
1. [Geben Sie Suchbegriffe](#enter-search-terms) ein, um Assets mit Merkmalen zu finden, die den Begriffen entsprechen.
1. [Legen Sie Schnellfilter fest](#set-quick-filters), um die Suche einzugrenzen.
1. [Starten Sie die Suche](#start-the-search), und navigieren Sie zu den Suchergebnissen.

Es spielt keine Rolle, ob Sie Schnellfilter festlegen, bevor oder nachdem Sie Suchbegriffe eingegeben haben.

Sind weder Suchbegriffe noch Filter vorhanden, umfassen die Suchergebnisse alle Assets.

### <a name="open-the-asset-search-dialog"></a>Öffnen des Dialogfelds zur Assetsuche

Öffnen Sie das Dialogfeld zur Assetsuche, indem Sie **Katalog durchsuchen** auswählen.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="Unter „Katalog durchsuchen“ gibt es einen linken Bereich mit Suchfiltern und einen rechten Bereich mit zuletzt verwendeten Suchvorgängen." border="true":::

Im Dialogfeld für Suchvorgänge werden Schnellfilter, der Suchverlauf und eine Liste der zuletzt verwendeten Assets (Objekte) angezeigt.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="Die Suchliste befindet sich im rechten Bereich unter „Katalog durchsuchen“." border="true":::

### <a name="enter-search-terms"></a>Eingeben von Suchbegriffen

Geben Sie mindestens einen Suchbegriff in **Katalog durchsuchen** ein. Während Ihrer Eingabe werden übereinstimmende Suchbegriffe aus den letzten Suchvorgängen in **Ihre neuesten Suchabfragen** aufgelistet, vorgeschlagene übereinstimmende Suchbegriffe werden in **Suchvorschläge** aufgelistet und übereinstimmende Datenassets werden unter **Assetvorschläge** aufgelistet.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="Screenshot mit den Ergebnisse einer Suche, die in das Feld „Katalog durchsuchen“ eingegeben wurde":::

Die Suchergebnisse enthalten nur Assets mit mindestens einem der Merkmale, die den Suchbegriffen entsprechen. Zu diesen Merkmalen gehören Assetname, Assettyp, Klassifizierungen und Kontakte.

#### <a name="types-of-search-criteria"></a>Typen von Suchkriterien

Azure Purview unterstützt die folgenden Typen von Suchkriterien.

> [!Note]
> Geben Sie boolesche Operatoren (**AND**, **OR**, **NOT**) immer vollständig in Großbuchstaben an. Ansonsten spielen weder die Groß-/Kleinschreibung noch zusätzliche Leerzeichen eine Rolle.

- **Struktur**: Dokumente finden, die **Struktur** enthalten.
- **Struktur und Datenbank**: Dokumente finden, die genau **Struktur und Datenbank** enthalten.
- **Struktur OR Datenbank**: Dokumente finden, die **Struktur** oder **Datenbank** oder beides enthalten.
- **Struktur AND Datenbank**, **Struktur && Datenbank**: Dokumente finden, die sowohl **Struktur** als auch **Datenbank** enthalten.
- **Struktur AND (Datenbank OR Warehouse**): Dokumente finden, die **Struktur** und entweder **Datenbank** oder **Warehouse** oder beides enthalten.
- **Struktur NOT Datenbank**: Dokumente finden, die **Struktur**, aber nicht **Datenbank** enthalten.
- **Strukt**: Dokumente finden, die ein Wort enthalten, das mit **Strukt** beginnt. Beispielsweise **Strukt**, **Struktur**, **Strukturleiste** (* ist ein Platzhalterzeichen, das einer beliebigen Anzahl von Zeichen entspricht).

### <a name="set-quick-filters"></a>Festlegen von Schnellfiltern

Die Liste der Suchergebnisse basiert auf den Suchbegriffen, die Sie in **Katalog durchsuchen** eingeben, sowie auf den Werten, die Sie für die Schnellfilter auswählen.

Ein Schnellfilter schränkt die Liste der Suchergebnisse auf Assets ein, die einen ausgewählten Wert eines Merkmals haben. Ein solcher Filter hat eine Dropdownliste und ein Textfeld. In der Dropdownliste werden Werte des Merkmals angezeigt, die sich in den *aktuellen* Suchergebnisse befinden. Neben jedem Wert in der Liste wird die Anzahl der Assets in den aktuellen Suchergebnissen angezeigt, die diesen Wert haben. Wenn Sie einen Wert in der Liste auswählen, werden die Suchergebnisse auf Assets mit diesem Wert beschränkt. Sie können nur einen Wert auswählen.

Die aktuellen Suchergebnisse, die zum Erstellen der Dropdownliste verwendet werden, sind durch folgende Elemente bestimmt:

- Die Suchbegriffe, die in **Katalog durchsuchen** eingegeben sind 
- Die Werte, die in den Schnellfiltern ausgewählt sind

Es folgt ein Beispiel für den Schnellfilter „Assettyp“.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Beispiel für den Schnellfilter „Assettyp“" border="true":::

Sie können Text in das Textfeld eingeben, um die Werte in der Dropdownliste auf Werte zu beschränken, die dem Text vollständig oder teilweise entsprechen. Beispiele zur Verwendung des Textfelds finden Sie unter [Suchschnellfilter: Filtern nach Assettyp](#search-quick-filter-filter-by-asset-type) und [Suchschnellfilter: Filtern nach Klassifizierung](#search-quick-filter-filter-by-classification).

#### <a name="search-quick-filter-filter-by-asset-type"></a>Suchschnellfilter: Filtern nach Assettyp

Um nach dem Assettyp zu filtern, verwenden Sie den Schnellfilter **Assettyp**. In der Dropdownliste werden die in den aktuellen Suchergebnissen gefundenen Assettypen angezeigt, wie sie durch die Suchbegriffe und die Schnellfilter bestimmt sind. Für jeden Typ wird die Anzahl der Assets dieses Typs angezeigt.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="Die Schnellfilter für den Assettyp sind aufgelistet. Für jeden Assettyp wird die entsprechende Anzahl angezeigt." border="true":::

Wählen Sie einen Assettyp aus, um die Suchergebnisse auf Assets dieses Typs zu beschränken. Sie können nur einen Typ auswählen.

Sollen nur Assettypen angezeigt werden, deren Namen einer Zeichenfolge entsprechen, geben Sie die Zeichenfolge in das Textfeld ein. Sollen beispielsweise nur Ressourcentypen angezeigt werden, die **sql** im Namen haben, geben Sie **sql** ein.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="Im Bereich „Schnellfilter“ ist „sql“ für „Assettyp“ angegeben. Die Liste der Assets (Objekte), die „sql“ im Namen enthalten, besteht aus drei Einträgen." border="true":::

Wählen Sie einen Assettyp aus, um die Suchergebnisse auf Assets dieses Typs zu beschränken. Sie können nur einen Typ auswählen.

#### <a name="search-quick-filter-filter-by-classification"></a>Suchschnellfilter: Filtern nach Klassifizierung

Um nach der Assetklassifizierung zu filtern, verwenden Sie den Schnellfilter **Klassifizierung**. In der Dropdownliste werden die Klassifizierungen angezeigt, die mindestens einem Asset in den aktuellen Suchergebnissen zugewiesen wurden, wie dies durch die Suchbegriffe und die Schnellfilter bestimmt ist. Für jede Klassifizierung wird die Anzahl der Assets angezeigt, denen diese Klassifizierung zugewiesen ist.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="Die Schnellfilter für Klassifizierung sind hervorgehoben." border="true":::

Wählen Sie eine Klassifizierung aus, um die Suchergebnisse auf Assets einzuschränken, denen diese Klassifizierung zugewiesen ist. Sie können nur eine Klassifizierung auswählen.

Sollen nur Klassifizierungen angezeigt werden, deren Namen einer Zeichenfolge entsprechen, geben Sie die Zeichenfolge in das Textfeld ein. Sollen beispielsweise nur Klassifizierungen angezeigt werden, die **number** in ihren Namen enthalten, geben Sie **number** ein.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="Im Bereich „Schnellfilter“ ist „number“ als Klassifizierung angegeben, und die aufgelisteten Klassifizierungen enthalten alle diesen Wert." border="true":::

Wählen Sie eine Klassifizierung aus, um die Suchergebnisse auf Assets einzuschränken, denen diese Klassifizierung zugewiesen wurde. Es ist nicht möglich, mehrere Klassifizierungen auszuwählen.

#### <a name="search-quick-filter-filter-by-contacts"></a>Suchschnellfilter: Filtern nach Kontakten

Eine *Kontakt* ist eine Person, die einem Asset (Objekt) als Besitzer oder Experte zugewiesen ist. Wenn Sie Assetdetails anzeigen, werden die Kontakte auf der Registerkarte **Kontakte** angezeigt.

Es gibt zwei Möglichkeiten, nach Assets zu suchen, denen ein bestimmter Kontakt zugewiesen ist.

- Geben Sie den gesamten Kontaktnamen oder einen Teil des Namens in **Katalog durchsuchen** ein, und führen Sie eine Suche aus. Die Suchergebnisse enthalten Assets, die Kontakte haben, deren Namen Ihren Suchbegriffen entsprechen.
- Wählen Sie den gewünschten Kontakt im Schnellfilter **Kontakt** aus, und führen Sie eine Suche aus.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="Im Bereich „Schnellfilter“ ist für „Kontakt“ der Wert „Vishal“ angegeben. Im Vorschlagsbereich ist ein Vorschlag vorhanden." border="true":::

## <a name="search-example"></a>Suchbeispiel

Betrachten wir ein hypothetisches Beispiel, um zu sehen, wie die Suchbegriffe und Schnellfilter interagieren, um die Suchergebnisse zu bestimmen. Insbesondere überwachen wir die Anzahl für den Assettyp **Azure Blob Storage**.

- Die erste Suche wird ohne eingegebene Suchbegriffe und ohne für die Schnellfilter ausgewählte Werte ausgeführt. Bei der Suche werden alle Assets im Katalog gefunden. Die Suchergebnisliste und der Schnellfilter **Ressourcentyp** enthalten Folgendes:

    - Die Suchergebnisliste enthält 164.230 Assets. Dies sind alle Assets im Katalog.
    - In der Dropdownliste **Assettyp** sind 43 Einträge enthalten. Dabei handelt es sich um alle Assettypen im Katalog. Da jedes Asset nur genau einen Typ hat, ist die Summe der Anzahl der 43 Assettypen gleich 164.230.
    - Der Assettyp **Azure Blob Storage** ist der erste Eintrag in der Dropdownliste des Schnellfilters **Assettyp**. Die Werte sind nach Anzahl geordnet, die größte zuerst, sodass **Azure Blob Storage** der häufigste Assettyp ist. Seine Anzahl beträgt 118.174.

- Geben Sie nun **parquet** in **Katalog durchsuchen** ein, und führen eine weitere Suche aus. Die Suchergebnisse enthalten nur Assets mit Merkmalen, die **parquet** entsprechen. Dadurch werden alle Anzahlen wie folgt verringert:

    - Die Suchergebnisliste enthält 493 Assets. Nur 493 der 164.230 Assets im Katalog haben Merkmale, die „parquet“ entsprechen.
    - In der Dropdownliste **Assettyp** sind 15 Einträge enthalten. Jedes der 493 Assets hat einen dieser 15 Typen, und die Summe der Anzahlen der 15 Typen beträgt 493.
    - Es gibt 456 Assets des Typs **Azure Blob Storage**. Die anderen 37 Assets (493 minus 456) haben einen der anderen 14 Typen.

- Wir sehen uns nun die Dropdownliste eines anderen Schnellfilters an, **Klassifizierung**:

    - Es gibt 12 Klassifizierungen für die 493 Assets in der Suchergebnisliste. Die Anzahlen der 493 Assets ergeben summiert nicht 493, da einem Asset eine beliebige Anzahl von Klassifizierungen zugewiesen werden kann.
    - Die Klassifizierung **Name der Person** ist 36 Assets zugewiesen, mehr als jede andere Klassifizierung.

- Wählen Sie die Klassifizierung **Name der Person** aus. Die Suchergebnisliste wird auf 36 Assets verringert, was zu erwarten war, da die Anzahl für **Name der Person** gleich 36 ist. Keines dieser Ergebnisse hat den Typ **Azure Blob Storage**.

Daraus lässt sich schließen, dass es kein Asset mit dem Typ **Azure Blob Storage** gibt, das **parquet** entspricht und die Klassifizierung **Name der Person** hat.

## <a name="start-the-search"></a>Starten der Suche

Wenn Sie suchen, werden die Suchbegriffe, die Sie in **Katalog durchsuchen** eingeben, mit den Assetmerkmalen abgeglichen. Zu diesen Merkmalen gehören Name, Typ, Klassifizierung und Kontakte. Die Assets mit übereinstimmenden Merkmalen werden in der Suchergebnisliste angezeigt, es sei denn, sie werden durch einen Schnellfilter ausgeschlossen.

Nachdem Sie die Suchbegriffe eingegeben und die Schnellfilter festgelegt haben, starten Sie die Suche auf eine der folgenden Arten:

- Soll anhand der von Ihnen eingegebenen Begriffe gesucht werden, wählen Sie das Suchsymbol (:::image type="icon" source="./media/how-to-search-catalog/search-icon.png":::) aus, drücken Sie die **EINGABETASTE**, oder wählen Sie **Suchergebnisse anzeigen** aus.
- Wenn Sie mit den Begriffen aus einer vorherigen Suche suchen möchten, wählen Sie diese in **Ihre neuesten Suchabfragen** aus.
- Wenn Sie mit vorgeschlagenen Begriffen suchen möchten, wählen Sie diese in **Suchvorschläge** aus.

Wählen Sie ein Asset in **Assetvorschläge** aus, um direkt zur Detailseite für das Asset zu gelangen. Es wird keine Suche ausgeführt.

Die Ergebnisliste für Vorschläge und Benutzersuchabfragen kann geringfügig unterschiedlich sein. Die Ergebnisse in der Vorschlagsliste basieren auf Fuzzyübereinstimmungen, wogegen benutzerinitiierte Suchergebnisse auf exakten Übereinstimmungen basieren.

Wenn Sie eine Suche ausführen, wird die Seite **Suchergebnisse** angezeigt, und auf dieser Seite werden die von der Suche gefundenen Assets aufgelistet.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Screenshot mit den Suchergebnissen für einen Suchwert von „contoso“":::

Wählen Sie einen Assetnamen aus, um Details zu dem Asset anzuzeigen.

Verwenden Sie die Steuerelemente am unteren Rand einer Suchergebnisseite, um zu anderen Suchergebnisseiten zu navigieren.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="Screenshot, der zeigt, wie Sie durch die Suchergebnisseiten navigieren können":::

### <a name="sort-search-results"></a>Sortieren von Suchergebnissen

Verwenden Sie **Sortieren nach**, um die Suchergebnisse nach **Relevanz** oder **Name** zu sortieren.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="Screenshot, der zeigt, wie Suchergebnisse sortiert werden können. In diesem Beispiel ist die Dropdownliste „Sortieren nach“ auf „Relevanz“ festgelegt.":::

### <a name="search-results-dynamic-filters"></a>Dynamische Filter für Suchergebnisse

Der Bereich **Filter** auf der Seite **Suchergebnisse** enthält Filter, mit denen die Assets (Objekte) in der Suchergebnisliste dynamisch gefiltert werden können. Die Filterung ist insofern dynamisch, als zusätzliche Filter über Filterauswahlen angezeigt werden können.

Die dynamischen Filter haben ein Kontrollkästchen für jeden Wert in der Dropdownliste. Verwenden Sie diese Kontrollkästchen, um nach beliebig vielen Werten zu filtern.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>Dynamische Filter für Suchergebnisse: Filtern nach Assettyp

Wenn Sie in der Dropdownliste **Assettyp** einen Assettyp auswählen, werden dynamische Filter angezeigt, mit denen Sie Ihre Suchergebnisse zusätzlich eingrenzen können. Die Filter variieren je nach ausgewähltem Ressourcentyp. Wenn Sie z. B. **Azure SQL-Datenbank** auswählen, werden dynamische Filter für Server, Datenbank und Schema angezeigt. Die Werte in diesen Filtern stammen von den Assets in den Suchergebnissen des ausgewählten Typs.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Das Filterelement „Azure SQL-Datenbank“ ist das einzige „Assettyp“-Element, das ausgewählt ist. Außerdem ist ein Suchergebnis dieses Assettyps hervorgehoben." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>Dynamische Filter für Suchergebnisse: Filtern nach Klassifizierung

Jede Klassifizierung in der Liste **Klassifizierungs** gilt für mindestens ein Element in der Suchergebnisliste. Wählen Sie mindestens eine Klassifizierung aus, um die Suchergebnisse auf Assets der ausgewählten Klassifizierungen einzugrenzen.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="Der „Klassifizierung“-Filter für „Suchergebnisse“ ist hervorgehoben." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>Bearbeiten und Löschen von Suchergebnisfiltern

Um einen Filter zu entfernen, deaktivieren Sie das Kontrollkästchen neben dem Namen des Filters.

### <a name="recently-accessed-assets"></a>Zuletzt verwendete Assets

Im **Zuletzt verwendet**-Abschnitt im erweiterten Suchfeld werden die zuletzt verwendeten Assets angezeigt (sofern es welche gibt).

- Wählen Sie **Alle anzeigen** im Abschnitt **Zuletzt verwendet** aus, um die vollständige Liste der zuletzt verwendeten Assets anzuzeigen.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="Screenshot mit dem „Zuletzt verwendet“-Abschnitt des erweiterten Suchfelds":::

   Eine Liste der zuletzt verwendeten Assets wird angezeigt.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="Screenshot mit einer Liste der zuletzt verwendeten Assets":::

- Wenn Sie nach Name filtern möchten, geben Sie eine Suchzeichenfolge in **Nach Name filtern** ein.

- Um Elemente aus der Liste zu entfernen, wählen Sie diese über deren Kontrollkästchen aus, und wählen Sie dann **Entfernen** aus.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="Screenshot, der zeigt, wie Elemente aus einer Liste von zuletzt verwendeten Assets entfernt werden":::

- Möchten Sie die gesamte Liste löschen, wählen Sie **Löschen** aus.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="Screenshot, der zeigt, wie eine Liste von zuletzt verwendeten Assets gelöscht wird":::

### <a name="search-assets"></a>Assets suchen

Viele Seiten außer der **Startseite** haben im oberen Bereich das Feld **Assets suchen**. Hier ist beispielsweise eine Seite mit Assetdetails zu sehen, für die **Assets suchen** hervorgehoben ist.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="Screenshot einer Seite mit Assetdetails, für die „Assets suchen“ hervorgehoben ist":::

Wählen Sie **Assets suchen** aus, um ein Suchfeld wie das zu starten, das Sie über **Katalog durchsuchen** auf der **Startseite** erhalten, und das dieselben Funktionen hat.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="Screenshot mit einem erweiterten „Assets suchen“-Feld":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen, Importieren und Exportieren von Glossarbegriffen](how-to-create-import-export-glossary.md)
- [Verwalten von Begriffsvorlagen für ein Unternehmensglossar](how-to-manage-term-templates.md)
