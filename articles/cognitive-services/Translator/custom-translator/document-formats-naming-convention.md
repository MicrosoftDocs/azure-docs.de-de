---
title: Dokumentformate und Namenskonventionen – Custom Translator
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie mehr über Dokumentformate und Namenskonventionen in Custom Translator. Mithilfe dieses Konzepts können Sie die Namen von Dokumenten besser verwalten und Namenskonflikte vermeiden.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: 29b74aeaaae0bcfd5f6ae4a3c38cc00176292899
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66386910"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Leitfaden für Dokumentformate und Namenskonventionen

Jede Datei, die in Custom Translator verwendet wird, muss mindestens **vier** Zeichen umfassen.

Diese Tabelle zeigt alle unterstützten Dateiformate, die Sie zum Erstellen Ihres Übersetzungssystems verwenden können:

| Format            | Erweiterungen   | BESCHREIBUNG                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF             | .xlf, .xliff | Ein Format für parallele Dokumente, das auch Translation Memory-Systeme exportiert. Die verwendeten Sprachen werden in der Datei definiert.                                                                                                                                                              |
| TMX               | .tmx         | Ein Format für parallele Dokumente, das auch Translation Memory-Systeme exportiert. Die verwendeten Sprachen werden in der Datei definiert.                                                                                                                                                              |
| ZIP               | .zip         | Dies ist ein Archivdateiformat.                                                                                                                                                                                                        |
| LocStudio         | .lcl         | Ein Microsoft-Format für parallele Dokumente.                                                                                                                                                                                                                                      |
| Microsoft Word    | .docx        | Das Format von Microsoft Word.                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | .pdf         | Dies ist das Portable Document Format von Adobe Acrobat.                                                                                                                                                                                                                                                |
| HTML              | .html, .htm  | HTML-Dokument                                                                                                                                                                                                                                                                  |
| Textdatei         | .txt         | Mit UTF-16 oder UTF-8 codierte Textdateien. Der Dateiname darf keine japanischen Zeichen enthalten.                                                                                                                                                                                        |
| Ausgerichtete Textdatei | .align       | Sie können die spezielle Erweiterung `.ALIGN` verwenden, wenn Sie wissen, dass alle Sätze im Dokumentenpaar einander genau zugeordnet sind. Wenn Sie eine `.ALIGN`-Datei angeben, ordnet Custom Translator die Sätze nicht für Sie aus. |
| Excel-Datei        | .xlsx        | Excel-Datei (2013 oder höher). Die erste Zeile in der Tabellenkalkulation muss der Sprachcode sein.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Wörterbuchformate

Custom Translator unterstützt alle Für Wörterbücher-Dateiformate, die für das Trainingsset unterstützt werden. Wenn Sie ein Wörterbuch im Excel-Format verwenden, achten Sie darauf, dass die erste Zeile der Tabellenkalkulation den Sprachcode enthält.

## <a name="zip-file-formats"></a>ZIP-Dateiformate

Dokumente können in einer einzigen ZIP-Datei gruppiert und hochgeladen werden. Custom Translator unterstützt die ZIP-Dateiformate ZIP, GZ und TGZ.

Jedes Dokument in der ZIP-Datei mit der Erweiterung TXT, HTML, HTM, PDF, DOCX, ALIGN muss dieser Benennungskonvention befolgen:

{Dokumentname}\_{Sprachcode}, wobei {Dokumentname} der Name Ihres Dokuments ist, {Sprachcode} die ISO-Sprach-ID (zwei Zeichen), die angibt, dass das Dokument Sätze in dieser Sprache enthält. Dem Sprachcode muss ein Unterstrich (_) vorausgehen.

Um beispielsweise zwei parallele Dokumente in einer ZIP-Datei für ein System Englisch–Spanisch hochzuladen, muss die Dateien „data_en“ und „data_es“ heißen.

Translation Memory-Dateien (TMX, XLF, XLIFF, LCL, XLSX) müssen keiner spezifischen Sprachbenennungskonvention folgen.  

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr darüber, wie Sie [Projekte](workspace-and-project.md#what-is-a-custom-translator-project) erstellen und verwalten.
