---
title: Präsentieren eines Jupyter-Notebooks als Bildschirmpräsentation auf Azure
description: Konfigurieren von Zellen für den Präsentationsmodus in einem Jupyter-Notebook und anschließendes Vorführen der Präsentation mithilfe der RISE-Erweiterung.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 405fe71676de311ed7e59ea72798ff4fd2db0f62
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237404"
---
# <a name="run-a-notebook-slideshow"></a>Ausführen einer Notebook-Bildschirmpräsentation

Azure Notebooks ist mit der Jupyter/IPython-Präsentationserweiterung (RISE) vorkonfiguriert, die Ihnen die direkte Vorführung eines Notebooks als Bildschirmpräsentation ermöglicht. Bei einer Bildschirmpräsentation werden die Zellen normalerweise jeweils einzeln in einem Schriftgrad angezeigt, der für die Vorführung auf großen Bildschirmen geeignet ist, wobei die Ausführung des Codes trotzdem möglich bleibt, so dass kein Wechsel auf einen separaten Democomputer erforderlich ist.

Die folgende Abbildung zeigt die Notebook-Standardansicht, in der Markdown und Codezellen zusammen zu sehen sind:

![Ein Notebook in der Standardansicht](media/slideshow/slideshow-notebook-view.png)

Wenn Sie eine Bildschirmpräsentation starten, wird die erste Zelle vergrößert, so dass sie die gesamte Browserfläche ausfüllt. Dabei dienen das **X** oben links zum Beenden der Bildschirmpräsentation, das **?** unten links zur Anzeige von Tastenkombinationen und die Pfeile unten rechts zur Navigation unter den Folien:

![Ein Notebook im Präsentationsmodus](media/slideshow/slideshow-slide-view.png)

Das Vorbereiten eines Notebooks für eine Bildschirmpräsentation umfasst zwei Hauptaktivitäten:

1. Da Markdownzellen mit großem Schriftgrad dargestellt werden, sind einige Inhalte möglicherweise in der Präsentation nicht sichtbar. Normalerweise beschränken Sie daher die Textmenge in den einzelnen Zellen; eine Überschrift aus vier bis sechs Zeilen funktioniert üblicherweise gut. Wenn Sie mehr Text darstellen müssen, teilen Sie diese Informationen auf mehrere Zellen auf.

2. Konfigurieren Sie das Verhalten der einzelnen Zellen in der Präsentation mithilfe der Präsentationssymbolleiste der Zelle. Das Verhalten der Navigationsschaltflächen wird durch den Zelltyp bestimmt.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie einer Bildschirmpräsentation

Wenn Sie ein Notebook zufällig auswählen und es für eine Bildschirmpräsentation verwenden, werden Sie normalerweise feststellen, dass die Reihenfolge der Zellen ungeordnet ist und viel vom Inhalt unter der Unterkante des Browserfensters liegt. Um daraus eine funktionierende Präsentation zu machen, müssen Sie jeder Zelle mithilfe der Zellensymbolleiste „Slideshow“ (Präsentation) einen Präsentationstyp zuweisen:

1. Wählen Sie im Menü **View** (Ansicht) **Cell Toolbar** (Zellensymbolleiste)  > **Slideshow** (Präsentation) aus:

    ![Aktivieren der Zellensymbolleiste „Slideshow“ (Präsentation)](media/slideshow/slideshow-view-cell-toolbar.png)

1. Rechts oben in jeder Zelle des Notebooks wird jetzt eine Dropdownliste **Slide Type** (Folientyp) angezeigt:

    ![Zellensymbolleiste „Slideshow“ (Präsentation)](media/slideshow/slideshow-cell-toolbar.png)

1. Wählen Sie für jede Zelle einen von fünf Typen aus:

    ![Präsentationstypen für Zellen](media/slideshow/slideshow-cell-slide-types.png)

    | Folientyp | Verhalten |
    | --- | --- |
    | - (nicht festgelegt) | Die Zelle wird mit der vorhergehenden Zelle angezeigt, was häufig nicht der gewünschte Effekt in einer Bildschirmpräsentation ist. |
    | Slide (Folie) | Die Zelle stellt eine Hauptfolie dar, zu der mithilfe der linken und rechten Pfeile der Navigationssteuerung navigiert wird. |
    | Sub-slide (Unterfolie) | Die Zelle ist „unterhalb“ einer Hauptfolie angeordnet, und die Navigation zu ihr erfolgt mithilfe des Abwärtspfeils der Navigationssteuerung. Mit dem Aufwärtspfeil wird zur Hauptfolie zurückgekehrt. Unterfolien werden für Sekundärmaterial verwendet, das im Hauptpfad einer Präsentation übersprungen werden kann, aber bei Bedarf bequem verfügbar ist. |
    | Fragment | Der Zelleninhalt wird über den nach unten zeigenden Navigationspfeil im Kontext der vorhergehenden Folie oder Unterfolie dargestellt (mit dem Pfeil nach oben werden Fragmente entfernt). Sie können ein Fragment in einer Codezelle verwenden, um den Code innerhalb einer Folie darzustellen, oder mit mehreren Fragmenten Textaufzählungszeichen nacheinander erscheinen lassen (siehe dazu das Beispiel im nächsten Abschnitt). Da Fragmente auf der aktuellen Folie aufbauen, werden überzählige Fragmente unterhalb des Browserfensters dargestellt, sind also nicht sichtbar. |
    | Überspringen | Die Zelle wird in der Bildschirmpräsentation angezeigt. |
    | Notizen | Diese sind in Zellen als Referentenanmerkungen enthalten und werden in der Bildschirmpräsentation nicht angezeigt. |

1. Zu Anfang ist es hilfreich, für jede Zelle **Slide** (Folie) auszuwählen. Anschließend können Sie die Präsentation ausführen und Anpassungen vornehmen.

### <a name="example-fragment-cells-for-bullet-items"></a>Beispiel: Fragmentieren von Zellen, um Aufzählungszeichen darzustellen

Um Aufzählungszeichen nacheinander auf einer Folie anzuzeigen, platzieren Sie die Folienüberschrift in einer Markdownzelle vom Typ **Slide** (Folie), und platzieren Sie dann jedes Aufzählungszeichen in einer separaten Markdownzelle vom Typ **Fragment**:

![Beispiel zum Erstellen mehrerer Markdownzellen für Elemente mit Aufzählungszeichen](media/slideshow/slideshow-fragments.png)

Da Fragmente in der Bildschirmpräsentation mit größerem vertikalem Abstand als Aufzählungszeichen innerhalb der gleichen Zelle dargestellt werden, können Sie möglicherweise nicht sehr viele Aufzählungszeichen verwenden.

## <a name="run-the-slideshow"></a>Ausführen der Bildschirmpräsentation

1. Wenn Sie Markdownzellen bearbeitet haben, achten Sie darauf, sie auszuführen, um ihre HTML zu erhalten, andernfalls werden sie in der Präsentation *als* Markdown dargestellt.

1. Nachdem Sie den **Folientyp** für jede Zelle konfiguriert haben, wählen Sie aus, mit welcher Zelle die Präsentation beginnen soll, und wählen Sie dann in der Hauptsymbolleiste die Schaltfläche **Enter/Exit RISE Slideshow** (RISE-Präsentation starten/beenden) aus:

    ![Schaltfläche „Enter/Exit RISE Slideshow“ (RISE-Präsentation starten/beenden) auf der Hauptsymbolleiste](media/slideshow/slideshow-start.png)

1. Um zwischen Folien und Fragmenten zu navigieren, verwenden Sie die Pfeile nach rechts und links in der Navigationssteuerung. Der Text im Steuerelement stellt eine Zahl dar, die *Folie.Unterfolie* bedeutet.

    ![Navigationssteuerung in der Präsentation](media/slideshow/slideshow-navigation-control.png)

1. Um zwischen Folien und Unterfolien sowie Fragmenten zu navigieren, verwenden Sie die Pfeile nach oben und unten, wenn sie aktiviert sind:

    ![Navigationssteuerung in der Präsentation für Unterfolien](media/slideshow/slideshow-navigation-control-subslide.png)

1. Verwenden Sie in einer Codezelle die Wiedergabeschaltfläche, um den Code auszuführen; die Ausgabe wird auf der Folie angezeigt:

    ![Schaltfläche „Wiedergabe“ zum Ausführen einer Codezelle](media/slideshow/slideshow-run-code-cell.png)

    ![Die Ausgabe der Codezelle wird in der Bildschirmpräsentation angezeigt](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Die Zellausgabe wird in einer Bildschirmpräsentation als Teil der Zelle angesehen. Wenn Sie eine Zelle entweder in der Notebook- oder in der Präsentationsansicht ausführen, wird die Ausgabe ebenfalls in der anderen Ansicht angezeigt. Um die Ausgabe zu löschen, verwenden Sie den Befehl **Cell** (Zelle)  > **Current Outputs** (Aktuelle Ausgaben)  > **Clear** (Löschen, für die aktuelle Zelle) oder **Cell** (Zelle)  > **All Outputs**  (Alle Ausgaben)  > **Clear** (Löschen, für alle Zellen).

1. Wenn Sie die Präsentation abgeschlossen haben, kehren Sie mit dem **X** zur Notebookansicht zurück.

## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Konfigurieren und Verwalten von Projekten](configure-manage-azure-notebooks-projects.md)
- [Gewusst wie: Installieren von Paketen aus einem Notebook](install-packages-jupyter-notebook.md)
- [Gewusst wie: Arbeiten mit Datendateien](work-with-project-data-files.md)
- [Gewusst wie: Zugreifen auf Datenressourcen](access-data-resources-jupyter-notebooks.md)
