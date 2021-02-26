---
title: Erstellen von Berichten zu Angriffsvektoren
description: Angriffsvektorberichte bieten eine grafische Darstellung einer Risikokette von Geräten mit Sicherheitslücken.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e9960fc2120add845be8feda9bafdef95a9b5f94
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522324"
---
# <a name="attack-vector-reporting"></a>Berichte zu Angriffsvektoren

## <a name="about-attack-vector-reports"></a>Informationen zu Berichten zu Angriffsvektoren

Angriffsvektorberichte bieten eine grafische Darstellung einer Risikokette von Geräten mit Sicherheitslücken. Durch diese Sicherheitslücken kann ein Angreifer Zugriff auf wichtige Netzwerkgeräte erhalten. Der Angriffsvektorsimulator berechnet Angriffsvektoren in Echtzeit und analysiert alle Angriffsvektoren für ein bestimmtes Ziel.

Das Arbeiten mit dem Angriffsvektor ermöglicht es Ihnen, die Wirkung von Entschärfungsaktivitäten in der Angriffssequenz zu bewerten. Sie können dann beispielsweise bestimmen, ob ein Systemupgrade dem Angreifer durch Unterbrechen der Angriffskette den Weg abschneidet oder ob ein alternativer Angriffsweg bestehen bleibt. Diese Informationen helfen Ihnen bei der Priorisierung von Wartungs- und Entschärfungsaktivitäten.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Darstellung Ihrer Benachrichtigungen im Kontrollcenter.":::

> [!NOTE]
> Administratoren und Sicherheitsanalysten können die in diesem Abschnitt beschriebenen Verfahren ausführen.

## <a name="create-an-attack-vector-report"></a>Erstellen eines Berichts zu Angriffsvektoren

So erstellen Sie eine Angriffsvektorsimulation:

1. Wählen Sie im seitlichen Menü das :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="Pluszeichen"::: aus, um eine Simulation hinzuzufügen.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Die Angriffsvektorsimulation.":::

2. Geben Sie Simulationseigenschaften ein:

   - **Name**: Name der Simulation.

   - **Maximum vectors** (Maximale Anzahl Vektoren): Die maximale Anzahl der Vektoren in einer einzelnen Simulation.

   - **Show in Device map** (In der Geräteübersicht anzeigen): Den Angriffsvektor als Vektor Filter in der Geräteübersicht darstellen.

   - **All Source devices** (Alle Quellgeräte): Der Angriffsvektor berücksichtigt alle Geräte als Angriffsquelle.

   - **Attack Source** (Angriffsquelle): Der Angriffsvektor berücksichtigt nur die angegebenen Geräte als Angriffsquelle.

   - **All Target devices** (Alle Zielgeräte): Der Angriffsvektor berücksichtigt alle Geräte als Angriffsziel.

   - **Attack Target** (Angriffsziel): Der Angriffsvektor berücksichtigt nur die angegebenen Geräte als Angriffsziel.

   - **Exclude devices** (Geräte ausschließen): Die angegebenen Geräte werden aus der Simulation des Angriffsvektors ausgeschlossen.

   - **Exclude Subnets** (Subnetze ausschließen): Die angegebenen Subnetze werden aus der Simulation des Angriffsvektors ausgeschlossen.

3. Wählen Sie **Add Simulation** (Simulation hinzufügen) aus. Die Simulation wird zur Liste der Simulationen hinzugefügt.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Hinzufügen einer neuen Simulation":::

4. Wählen Sie :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: aus, wenn Sie die Simulation bearbeiten möchten.

   Wählen Sie :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: aus, wenn Sie die Simulation löschen möchten.

   Wählen Sie :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: aus, wenn Sie die Simulation als Favorit markieren möchten.

5. Eine Liste der Angriffsvektoren wird zusammen mit einer Vektorbewertung (bezogen auf 100) und dem Quellgerät sowie dem Zielgerät des Angriffs angezeigt. Wählen Sie einen bestimmten Angriff aus, um eine grafische Darstellung der Angriffsvektoren anzuzeigen.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Angriffsvektoren.":::

## <a name="next-steps"></a>Nächste Schritte

[Berichte zu Angriffsvektoren](how-to-create-attack-vector-reports.md)


