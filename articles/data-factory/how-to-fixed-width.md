---
title: Verarbeiten von Textdateien mit fester Länge mithilfe von Mapping Data Flows in Azure Data Factory
description: Erfahren Sie, wie Sie Textdateien mit fester Länge in Azure Data Factory mithilfe von Mapping Data Flows verarbeiten.
author: balakreshnan
ms.service: data-factory
ms.topic: conceptual
ms.date: 8/18/2019
ms.author: makromer
ms.openlocfilehash: 2297364e8f1a531b4512f2497bc4d99963d61179
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386202"
---
# <a name="process-fixed-length-text-files-by-using-data-factory-mapping-data-flows"></a>Verarbeiten von Textdateien mit fester Länge mithilfe von Data Factory Mapping Data Flows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Mithilfe von Mapping Data Flows in Microsoft Azure Data Factory können Sie Daten aus Textdateien mit fester Breite transformieren. In der folgenden Aufgabe definieren Sie ein Dataset für eine Textdatei ohne Trennzeichen und richten dann Unterteilungen in Teilzeichenfolgen basierend auf der Ordinalposition ein.

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1. Wählen Sie **+Neue Pipeline** aus, um eine neue Pipeline zu erstellen.

2. Fügen Sie eine Datenflussaktivität für die Verarbeitung von Dateien mit fester Breite hinzu:

    ![Pipeline mit fester Breite](media/data-flow/fwpipe.png)

3. Wählen Sie in der Datenflussaktivität die Option für eine **neue Mapping Data Flow-Instanz** aus.

4. Fügen Sie eine Transformation für Quelle, abgeleitete Spalte, Auswahl und Senke hinzu:

    ![Datenfluss mit fester Breite](media/data-flow/fw2.png)

5. Konfigurieren Sie die Quellentransformation so, dass ein neues Dataset vom Typ „Durch Trennzeichen getrennter Text“ verwendet wird.

6. Legen Sie keine Spaltentrennzeichen oder -überschriften fest.

   Als Nächstes werden die Ausgangspunkte und Längen für die Inhalte dieser Datei festgelegt:

    ```
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    1234567813572468
    ```

7. Auf der Registerkarte **Projektion** der Quellentransformation wird eine Zeichenfolgenspalte namens *Column_1* angezeigt.

8. Erstellen Sie in der abgeleiteten Spalte eine neue Spalte.

9. Geben Sie den Spalten einfache Namen wie etwa *col1*.

10. Geben Sie im Ausdrucks-Generator Folgendes ein:

    ```substring(Column_1,1,4)```

    ![Abgeleitete Spalte](media/data-flow/fwderivedcol1.png)

11. Wiederholen Sie Schritt 10 für alle zu analysierenden Spalten.

12. Wählen Sie die Registerkarte **Untersuchen** aus, um die neuen Spalten anzuzeigen, die generiert werden:

    ![Untersuchen](media/data-flow/fwinspect.png)

13. Verwenden Sie die Auswahltransformation, um alle Spalten zu entfernen, die Sie nicht für die Transformation benötigen:

    ![Auswahltransformation](media/data-flow/fwselect.png)

14. Verwenden Sie „Senke“, um die Daten in einen Ordner auszugeben:

    ![Senke mit fester Breite](media/data-flow/fwsink.png)

    Die Ausgabe sieht wie folgt aus:

    ![Ausgabe mit fester Breite](media/data-flow/fxdoutput.png)

  Die Daten mit fester Breite sind nun in jeweils vier Zeichen unterteilt und „Col1“, „Col2“, „Col3“, „Col4“ usw. zugeordnet. Basierend auf dem vorherigen Beispiel werden die Daten auf vier Spalten aufgeteilt.

## <a name="next-steps"></a>Nächste Schritte

* Erstellen Sie die restliche Datenflusslogik mithilfe von Mapping Data Flow-[Transformationen](concepts-data-flow-overview.md).
