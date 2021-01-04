---
title: Hinzufügen eines Referenzdatasets zu Ihrer Umgebung – Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Ihrer Azure Time Series Insights-Umgebung ein Verweisdataset zum Ergänzen der Daten hinzufügen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001829"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Erstellen eines Referenzdatasets für Ihre Azure Time Series Insights Gen1-Umgebung über das Azure-Portal

> [!CAUTION]
> Dies ist ein Artikel zu Azure Time Series Insights Gen1.

In diesem Artikel wird beschrieben, wie Sie Ihrer Azure Time Series Insights-Umgebung ein Verweis-DataSet hinzufügen. Verweisdaten lassen sich praktisch mit Ihren Quelldaten verknüpfen, um die Werte zu erweitern.

Ein Verweisdataset ist eine Sammlung von Elementen, die die Ereignisse aus Ihrer Ereignisquelle ergänzen. Die Azure Time Series Insights-Erfassungs-Engine verknüpft jedes Ereignis aus Ihrer Ereignisquelle mit der entsprechenden Datenzeile in Ihrem Referenzdataset. Dieses ergänzte Ereignis ist dann für Abfragen verfügbar. Diese Verknüpfung basiert auf den in Ihrem Verweisdataset definierten Primärschlüsselspalten.

Verweisdaten werden nicht rückwirkend verknüpft. Somit werden nur aktuelle und künftige eingehende Daten nach dem Konfigurieren und Hochladen abgeglichen und dem Verweisdataset hinzugefügt.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Informationen zum Time Series Insights-Verweisdatenmodell.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Hinzufügen eines Verweis-DataSets

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie die vorhandene Azure Time Series Insights-Umgebung. Wählen Sie im Azure-Portal im Menü auf der linken Seite **Alle Ressourcen** aus. Wählen Sie Ihre Azure Time Series Insights-Umgebung aus.

1. Klicken Sie auf die Seite **Übersicht**. Erweitern Sie den Abschnitt **Essentials** am oberen Rand der Seite, suchen Sie die **URL des Time Series Insights-Explorers**, und öffnen Sie den Link.  

   [![Abschnitt „Essentials“ erweitern](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Zeigen Sie den Explorer für Ihre Azure Time Series Insights-Umgebung an.

1. Erweitern Sie die Umgebungsauswahl im Azure Times Series Insights-Explorer. Wählen Sie die aktive Umgebung aus. Klicken Sie oben rechts auf der Explorer-Seite auf das Symbol für Verweisdaten.

   [![Hinzufügen von Verweisdaten](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Klicken Sie auf die Schaltfläche **+ Dataset hinzufügen**, um ein neues Dataset hinzuzufügen.

   [![Hinzufügen eines Datasets](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Wählen Sie auf der Seite **Neues Verweisdataset** das Format der Daten aus:

   - Wählen Sie **CSV** für kommagetrennte Daten aus. Die erste Zeile als Kopfzeile behandelt.
   - Wählen Sie **JSON-Array** für Daten im JSON-Format (JavaScript Object Notation) aus.

   [![Auswählen eines Datenformats](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox).

1. Geben Sie die Daten mit einer der folgenden Methoden an:

   - Fügen Sie die Daten in den Text-Editor ein. Klicken Sie dann auf die Schaltfläche **Referenzdaten analysieren**.
   - Klicken Sie auf die Schaltfläche **Datei auswählen**, um Daten aus einer lokalen Textdatei hinzuzufügen.

   Fügen Sie beispielsweise CSV-Daten ein: [![Eingefügte CSV-Daten](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Fügen Sie beispielsweise JSON-Arraydaten ein: [![Eingefügte JSON-Daten](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Falls beim Analysieren der Datenwerte ein Fehler auftritt, wird er unten auf der Seite in Rot angezeigt. Beispiel: `CSV parsing error, no rows extracted`

1. Nachdem die Daten analysiert wurden, wird ein Datenraster mit den Spalten und Zeilen und den entsprechenden Daten angezeigt. Überprüfen Sie das Datenraster auf seine Richtigkeit.

   [![Überprüfen der Verweisdaten](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Überprüfen Sie in jeder Spalte den angenommenen Datentyp, und ändern Sie ihn bei Bedarf.  Klicken Sie in der Spaltenüberschrift auf das Symbol für den Datentyp: **#** : Double (numerische Daten), **T|F**: Boolesch, **Abc**: Zeichenfolge.

   [![Auswählen von Datentypen in den Spaltenüberschriften.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. Benennen Sie die Spaltenüberschriften ggf. um. Der Name der Schlüsselspalte ist für die Verknüpfung mit der entsprechenden Eigenschaft in Ihrer Ereignisquelle erforderlich.

   > [!IMPORTANT]
   > Stellen Sie sicher, dass die Namen der Referenzdaten-Spaltennamen exakt mit dem Ereignisnamen der eingehenden Daten übereinstimmen. Dabei muss auch die Groß- und Kleinschreibung beachtet werden. Die Namen der Spalten, bei denen es sich nicht um Schlüsselspalten handelt, werden dazu verwendet, die eingehenden Daten mit den entsprechenden Referenzdatenwerten zu ergänzen.

1. Geben Sie im Feld **Filter the rows...** (Zeilen filtern...) einen Wert ein, um bei Bedarf bestimmte Zeilen zu überprüfen. Der Filter ist praktisch für die Überprüfung von Daten, wird beim Hochladen der Daten jedoch nicht angewendet.

1. Geben Sie dem Dataset einen Namen, indem Sie oberhalb des Datenrasters einen Namen ins Feld **Datasetname** eingeben.

    [![Benennen des Datasets.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Legen Sie die Spalte **Primärschlüssel** im Dataset fest. Wählen Sie dazu einen Wert aus der Dropdownliste oberhalb des Datenrasters aus.

    [![Auswählen der Schlüsselspalte(n).](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Optional)** Wählen Sie die Schaltfläche **+** aus, um eine Sekundärschlüsselspalte als zusammengesetzten Primärschlüssel hinzuzufügen. Wenn Sie die Auswahl rückgängig machen müssen, wählen Sie den leeren Wert in der Dropdownliste aus, um den Sekundärschlüssel zu entfernen.

1. Klicken Sie zum Hochladen der Daten auf die Schaltfläche **Upload rows** (Zeilen hochladen).

    [![Hochladen von Zeilen und Bestätigen von Daten.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Auf der Seite wird der abgeschlossene Upload bestätigt und die Meldung **Das Dataset wurde erfolgreich hochgeladen.** angezeigt.

    > [!WARNING]
    > Spalten oder Eigenschaften, die von Referenzdatasets gemeinsam verwendet werden, zeigen einen Uploadfehler vom Typ **Doppelter Eigenschaftsname** an. Der Fehler verhindert nicht, dass die Referenzdatasets erfolgreich hochgeladen werden. Der Fehler kann durch Kombinieren der Zeilen entfernt werden, die den doppelt vorhandenen Eigenschaftsnamen verwenden.

1. Wählen Sie **Zeile hinzufügen**, **Massenimport von Zeilen** oder **Spalte hinzufügen** aus, um bei Bedarf weitere Referenzdatenwerte hinzuzufügen.

    [![Hinzufügen einer Zeile, Massenimport von Zeilen oder Hinzufügen einer Spalte.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Für jede Zeile, die einen eindeutigen Schlüssel mit einer anderen Zeile gemeinsam verwendet, werden die Spalten von der letzten hinzugefügten Zeile, die den eindeutigen Schlüssel ebenfalls verwendet, überschrieben.

   > [!NOTE]
   > Hinzugefügte Zeilen müssen **nicht** *gleichwertig* sein: Sie können weniger, größere oder abweichende Spalten als die anderen Einträge im Referenzdataset aufweisen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten Sie Verweisdaten](time-series-insights-manage-reference-data-csharp.md) programmgesteuert.

- Die vollständige API-Referenz finden Sie im Dokument zur [Verweisdaten-API](/rest/api/time-series-insights/gen1-reference-data-api).