---
title: 'Hochladen eines Dokuments: Custom Translator'
titleSuffix: Azure Cognitive Services
description: Mithilfe des Dokumentuploadfeatures können Sie parallele Dokumente zu Trainingszwecken hochladen. Parallele Dokumente sind Dokumentpaare, bei denen ein Dokument die Übersetzung des anderen enthält. Das eine Dokument des Paars enthält also die Sätze in der Ausgangssprache und das andere die entsprechenden Sätze in der Zielsprache.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 1fa786bee960f71e4109041d935757a0d1edd75e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386935"
---
# <a name="upload-a-document"></a>Hochladen eines Dokuments

In [Custom Translator](https://portal.customtranslator.azure.ai) können Sie parallele Dokumente hochladen, um Ihre Übersetzungsmodelle zu trainieren. [Parallele Dokumente](what-are-parallel-documents.md) sind Dokumentpaare, bei denen ein Dokument die Übersetzung des anderen enthält. Das eine Dokument des Paars enthält also die Sätze in der Ausgangssprache und das andere die entsprechenden Sätze in der Zielsprache.

Bevor Sie Ihre Dokumente hochladen, lesen Sie den [Leitfaden für Dokumentformate und Namenskonventionen](document-formats-naming-convention.md), um sicherzustellen, dass Ihr Dateiformat in Custom Translator unterstützt wird.

## <a name="how-to-upload-document"></a>Hochladen eines Dokuments

Klicken Sie im Portal [Custom Translator](https://portal.customtranslator.azure.ai) auf die Registerkarte „Dokumente“, um zur Dokumentenseite zu gelangen.

![Link zum Hochladen von Dokumenten](media/how-to/how-to-upload-1.png)


1.  Klicken Sie auf der Dokumentenseite auf die Schaltfläche „Dateien hochladen“.

    ![Seite für Dokumentuploads](media/how-to/how-to-upload-2.png)

2.  Geben Sie die folgenden Informationen in das Dialogfeld ein:

    a.  Dokumenttyp:

    -  Training: Diese Dokumente werden für den Trainingssatz verwendet.
    -  Optimierung: Diese Dokumente werden für den Optimierungssatz verwendet.
    -  Test: Diese Dokumente werden für den Testsatz verwendet.
    -  Ausdruckswörterbuch: Diese Dokumente werden für das Ausdruckswörterbuch verwendet.
    -  Satzwörterbuch: Diese Dokumente werden für das Satzwörterbuch verwendet.

    b.  Language pair (Sprachpaar)

    c.  Override document if exists (Dokument überschreiben, falls vorhanden): Aktivieren Sie dieses Kontrollkästchen, wenn bereits vorhandene Dokumente gleichen Namens überschrieben werden sollen.

    d.  Füllen Sie den relevanten Abschnitt für parallele Daten oder für Kombinationsdaten aus.

    -  Parallele Daten:
        -  Quelldatei: Wählen Sie die Quellsprachdatei auf Ihrem lokalen Computer aus.
        -  Zieldatei: Wählen Sie die Zielsprachdatei auf Ihrem lokalen Computer aus.
        -  Dokumentname: Nur beim Hochladen paralleler Dateien relevant.

    - Kombinationsdaten:
        -  Kombinationsdatei: Wählen Sie die Kombinationsdatei auf Ihrem lokalen Computer aus. Die Kombinationsdatei enthält sowohl die Sätze in der Ausgangssprache als auch die Sätze in der Zielsprache. Für Kombinationsdateien wird eine [Namenskonvention](document-formats-naming-convention.md) benötigt.

    e.  Klicken Sie auf „Hochladen“.

    ![Dialogfeld für Dokumentuploads](media/how-to/how-to-upload-dialog.png)

3.  Ihre Dokumente werden verarbeitet, und es wird versucht, Sätze zu extrahieren. Sie können auf „View upload progress“ (Uploadstatus anzeigen) klicken, um den Verarbeitungsstatus Ihrer Dokumente zu überprüfen.

    ![Verarbeitungsdialogfeld für Dokumentuploads](media/how-to/how-to-upload-processing-dialog.png)

4.  Auf dieser Seite werden der Status sowie alle ggf. auftretenden Fehler für die einzelnen Dateien des Uploads angezeigt. Auf der Registerkarte „Upload history“ (Uploadverlauf) können Sie jederzeit einen älteren Uploadstatus anzeigen.

    ![Verlaufsdialogfeld für Dokumentuploads](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Anzeigen des Uploadverlaufs

Auf der Seite mit dem Uploadverlauf können Sie Verlaufsdetails aller Dokumentuploads wie Dokumenttyp, Sprachpaar, Uploadstatus und Ähnliches anzeigen.

1. Klicken Sie im Portal [Custom Translator](https://portal.customtranslator.azure.ai) auf die Registerkarte „Upload History“ (Uploadverlauf), um den Verlauf anzuzeigen.

    ![Registerkarte mit dem Uploadverlauf](media/how-to/how-to-upload-history-1.png)

2. Auf dieser Seite wird der Status aller bisherigen Uploads angezeigt. Die Uploads sind absteigend nach Aktualität sortiert. Für jeden Upload werden Dokumentname, Uploadstatus, Uploaddatum, Anzahl der hochgeladenen Dateien, Art der hochgeladenen Dateien und Sprachpaar der Datei angezeigt.

    ![Seite mit dem Uploadverlauf](media/how-to/how-to-document-history-2.png)

3. Klicken Sie auf einen beliebigen Datensatz des Uploadverlaufs. Auf der Detailseite des Uploadverlaufs können Sie die hochgeladenen Dateien, den Uploadstatus der Datei, die Sprache der Datei und ggf. die Fehlermeldung anzeigen (sofern bei dem Upload ein Fehler aufgetreten ist).

## <a name="next-steps"></a>Nächste Schritte

- Verwenden Sie die [Dokumentdetailseite](how-to-view-document-details.md), um die Liste mit den extrahierten Sätzen zu überprüfen.
- [Trainieren eines Modells](how-to-train-model.md)
