---
title: Verwalten von Begriffsvorlagen für ein Unternehmensglossar
description: Erfahren Sie, wie Sie Begriffsvorlagen für das Unternehmensglossar in einem Azure Purview-Datenkatalog verwalten können.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/04/2020
ms.openlocfilehash: b1b811d0817d5e23adc208da14719d64d53830dd
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551051"
---
# <a name="how-to-manage-term-templates-for-business-glossary"></a>Verwalten von Begriffsvorlagen für ein Unternehmensglossar

Azure Purview ermöglicht Ihnen das Erstellen eines Glossars mit Begriffen, die zur besseren Nutzung Ihrer Daten wichtig sind. Jeder neue Begriff, der Ihrem Purview-Datenkatalogglossar hinzugefügt wird, setzt auf einer Begriffsvorlage auf, die die Felder für den Begriff bestimmt. In diesem Artikel ist beschrieben, wie eine Begriffsvorlage und benutzerdefinierte Attribute erstellt werden, die Glossarbegriffen zugeordnet werden können.

## <a name="manage-term-templates-and-custom-attributes"></a>Verwalten von Begriffsvorlagen und benutzerdefinierten Attributen

Mit Begriffsvorlagen können Sie benutzerdefinierte Attribute erstellen, diese in einer Gruppe zusammenstellen und eine Vorlage anwenden, während Sie Begriffe erstellen. Sobald ein Begriff erstellt wurde, kann die Vorlage, die dem Begriff zugeordnet ist, nicht mehr geändert werden.

1. Wählen Sie auf der Seite **Glossarbegriffe** die Option **Begriffsvorlagen verwalten** aus.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-templates.png" alt-text="Screenshot von „Glossarbegriffe“ > Schaltfläche „Begriffsvorlagen verwalten“":::

2. Auf der Seite werden sowohl System- als auch benutzerdefinierte Attribute angezeigt. Wählen Sie die Registerkarte **Benutzerdefiniert** aus, um Begriffsvorlagen zu erstellen oder zu bearbeiten.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/manage-term-custom.png" alt-text="Screenshot von „Glossarbegriffe“ > Seite „Begriffsvorlagen verwalten“":::

3. Wählen Sie **+ Neue Begriffsvorlage** aus, und geben Sie den Namen und die Beschreibung für die Vorlage ein.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-term-template.png" alt-text="Screenshot von „Glossarbegriffe“ > „Begriffsvorlagen verwalten“ > „Neue Begriffsvorlage“":::

4. Wählen Sie **+ Neues Attribut** aus, um ein neues benutzerdefiniertes Attribut für die Begriffsvorlage zu erstellen. Geben Sie den Namen und die Beschreibung für das Attribut ein. Der Name des benutzerdefinierten Attributs muss innerhalb einer Begriffsvorlage eindeutig sein, kann jedoch in unterschiedlichen Vorlagen identisch verwendet werden.

   Wählen Sie den Feldtyp in der Liste der Optionen **Text**, **Einzelauswahl**, **Mehrfachauswahl** oder **Datum** aus. Für ein Feld des Typs „Text“ können Sie auch eine Standardwertzeichenfolge angeben.  Das jeweilige Attribut kann auch als **Erforderlich** gekennzeichnet werden.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/new-attribute.png" alt-text="Screenshot von „Glossarbegriffe“ > Seite „Neues Attribut“":::

5. Nachdem alle benutzerdefinierten Attribute erstellt wurden, wählen Sie **Vorschau** aus, um die Reihenfolge der benutzerdefinierten Attribute zu ändern. Sie können benutzerdefinierte Attribute per Drag &amp; Drop in die gewünschte Reihenfolge bringen.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/preview-term-template.png" alt-text="Screenshot von „Glossarbegriffe“ > Seite „Vorschau der Begriffsvorlage anzeigen“":::

6. Sobald alle benutzerdefinierten Attribute definiert sind, wählen Sie **Erstellen** aus, um eine Begriffsvorlage mit benutzerdefinierten Attributen zu erstellen.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/create-term-template.png" alt-text="Screenshot von „Glossarbegriffe“ > „Neue Begriffsvorlage“, Schaltfläche „Erstellen“":::

7. Vorhandene benutzerdefinierte Attribute können als abgelaufen gekennzeichnet werden, indem **Als abgelaufen markieren** aktiviert wird. Sobald das Attribut abgelaufen ist, kann es nicht erneut aktiviert werden. Das abgelaufene Attribut wird für vorhandene Begriffe abgeblendet. Für zukünftige neue Begriffe, die mit dieser Begriffsvorlage erstellt werden, wird das Attribut, das als abgelaufen gekennzeichnet wurde, nicht mehr angezeigt.

   :::image type="content" source="./media/how-to-manage-glossary-term-templates/expired-attribute.png" alt-text="Screenshot von „Glossarbegriffe“ > „Attribut bearbeiten“, um es als abgelaufen zu markieren":::

## <a name="next-steps"></a>Nächste Schritte

[Tutorial: Erstellen und Importieren von Glossarbegriffen in Azure Purview (Vorschau)](tutorial-import-create-glossary-terms.md). Hier finden Sie weitere Informationen.
