---
title: Referenz des Moduls „Feature Hashing“
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Feature Hashing im Azure Machine Learning-Designer Textdaten in Features zerlegen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd48b32afee320aa3d252540d566317c374c73a8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420783"
---
# <a name="feature-hashing-module-reference"></a>Referenz des Moduls „Feature Hashing“

In diesem Artikel wird ein Modul beschrieben, das in Azure Machine Learning-Designer enthalten ist.

Verwenden Sie das Modul „Feature Hashing“, um einen Datenstrom aus englischem Text in eine Reihe von ganzzahligen Merkmalen umzuwandeln. Anschließend können Sie diese gehashte Merkmalssammlung an einen Machine Learning-Algorithmus übergeben, um ein Textanalysemodell zu trainieren.

Die in diesem Modul bereitgestellte Feature Hashing-Funktionalität basiert auf dem nimbusml-Framework. Weitere Informationen finden Sie unter [NgramHash-Klasse](/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest&preserve-view=true).

## <a name="what-is-feature-hashing"></a>Was ist Feature Hashing?

Feature Hashing funktioniert, indem eindeutige Token in Integerwerte umgewandelt werden. Diese Funktion verarbeitet die genauen Zeichenfolgen, die Sie als Eingabe bereitstellen, und führt keine linguistische Analyse oder Vorverarbeitung aus. 

Nehmen Sie z.B. eine Reihe von einfachen Sätzen wie diese an, gefolgt von einer Stimmungsbewertung. Angenommen, Sie möchten diesen Text verwenden, um ein Modell zu erstellen.

|Benutzertext|Stimmung|
|--------------|---------------|
|I loved this book (Ich mochte dieses Buch)|3|
|I hated this book (Ich habe dieses Buch gehasst)|1|
|This book was great (Dieses Buch war großartig)|3|
|I love books (Ich liebe Bücher)|2|

Intern erstellt das Modul „Feature Hashing“ ein Wörterbuch mit N-Grammen. Die Liste der Bigramme für dieses Dataset würde beispielsweise folgendermaßen aussehen:

|Ausdruck (Bigramme)|Häufigkeit|
|------------|---------------|
|This book (Dieses Buch)|3|
|I loved (Ich mochte)|1|
|I hated (Ich habe gehasst)|1|
|I love (Ich liebe)|1|

Sie können die Größe der N-Gramme mithilfe der Eigenschaft **N-grams** steuern. Bei der Auswahl von Bigrammen werden auch Unigramme berechnet. Das Wörterbuch würde auch einzelne Begriffe wie die folgenden enthalten:

|Begriff (Unigramme)|Häufigkeit|
|------------|---------------|
|book (Buch)|3|
|I|3|
|books (Bücher)|1|
|was (war)|1|

Nachdem das Wörterbuch erstellt ist, konvertiert das Modul „Feature Hashing“ die Wörterbuchbegriffe in Hashwerte. Anschließend wird berechnet, ob ein Merkmal in jedem Fall verwendet wurde. Für jede Textzeile gibt das Modul eine Reihe von Spalten aus, eine Spalte für jedes gehashte Merkmal.

Nach dem Hashing können die Merkmalsspalten beispielsweise wie folgt aussehen:

|Rating|Hashing feature 1 (Hashingmerkmal 1)|Hashing feature 2 (Hashingmerkmal 2)|Hashing feature 3 (Hashingmerkmal 3)|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Wenn der Wert in der Spalte 0 ist, hat die Zeile kein gehashtes Merkmal enthalten.
* Wenn der Wert 1 ist, hat die Zeile das Merkmal enthalten.

Mit Feature Hashing können Sie Textdokumente variabler Länge als numerische Merkmalsvektoren gleicher Länge darstellen, um die Dimensionen zu verringern. Wenn Sie versuchen würden, die Textspalte für das Training so zu verwenden, wie sie ist, würde sie als kategorische Merkmalsspalte mit vielen verschiedenen Werten behandelt.

Numerische Ausgaben ermöglichen auch die Verwendung gängiger Machine Learning-Methoden wie Klassifizierung, Clustering und Informationsabruf. Da Nachschlagevorgänge ganzzahlige Hashes anstelle von Zeichenfolgenvergleichen verwenden können, ist das Abrufen der Merkmalsgewichtungen auch wesentlich schneller.

## <a name="configure-the-feature-hashing-module"></a>Konfigurieren des Moduls „Feature Hashing“

1.  Fügen Sie das Modul „Feature Hashing“ zu Ihrer Pipeline im Designer hinzu.

1. Verbinden Sie das Dataset, das den zu analysierenden Text enthält.

    > [!TIP]
    > Da Feature Hashing keine lexikalischen Vorgänge wie Stammformreduktion oder Trunkierung durchführt, können Sie manchmal bessere Ergebnisse erzielen, indem Sie den jeweiligen Text vorverarbeiten, bevor Sie Feature Hashing anwenden. 

1. Legen Sie **Target columns** (Zielspalten) auf die Textspalten fest, die in gehashte Merkmale konvertiert werden sollen. Bedenken Sie Folgendes:

    * Die Spalten müssen den Datentyp „string“ aufweisen.
    
    * Die Auswahl mehrerer Textspalten kann erhebliche Auswirkungen auf die Featuredimensionalität haben. So reicht beispielsweise die Anzahl der Spalten für einen 10-Bit-Hash von 1.024 für eine einzelne Spalte bis 2.048 für zwei Spalten.

1. Verwenden Sie **Hashing bitsize** (Hashing-Bitgröße), um die Anzahl von Bits anzugeben, die verwendet werden sollen, wenn Sie die Hashtabelle erstellen.
    
    Die Standardbitgröße ist 10. Dieser Wert ist für viele Probleme angemessen. Möglicherweise benötigen Sie mehr Platz, um Konflikte abhängig von der Größe des N-Gramme-Vokabulars im Trainingstext zu vermeiden.
    
1. Geben Sie für **N-grams** (N-Gramme) eine Zahl ein, die die maximale Länge der N-Gramme definiert, die dem Trainingswörterbuch hinzugefügt werden sollen. Ein N-Gramm ist eine Sequenz von *N* Wörtern, die als eindeutige Einheit behandelt wird.

    Wenn Sie z.B. 3 eingeben, werden Unigramme, Bigramme und Trigramme erstellt.

1. Übermitteln Sie die Pipeline.

## <a name="results"></a>Ergebnisse

Nach Abschluss der Verarbeitung gibt das Modul ein transformiertes Dataset aus, in dem die ursprüngliche Textspalte in mehrere Spalten umgewandelt wurde. Jede Spalte entspricht einem Merkmal im Text. Je nachdem, wie signifikant das Wörterbuch ist, kann das sich ergebende Dataset sehr groß sein:

|Column name 1 (Spaltenname 1)|Column type 2 (Spaltentyp 2)|
|-------------------|-------------------|
|USERTEXT (Benutzertext)|Ursprüngliche Datenspalte|
|SENTIMENT (Stimmung)|Ursprüngliche Datenspalte|
|USERTEXT - Hashing feature 1 (Hashingmerkmal 1)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature 2 (Hashingmerkmal 2)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature n (Hashingmerkmal n)|Gehashte Merkmalsspalte|
|USERTEXT - Hashing feature 1024 (Hashingmerkmal 1024)|Gehashte Merkmalsspalte|

Nachdem Sie das transformierte Dataset erstellt haben, können Sie es als Eingabe für das Modul „Train Model“ (Modell trainieren) verwenden.
 
## <a name="best-practices"></a>Bewährte Methoden

Die folgenden bewährten Methoden können Ihnen dabei helfen, das Modul „Feature Hashing“ optimal zu nutzen:

* Fügen Sie, um den Eingabetext vorzuverarbeiten, ein Modul „Preprocess Text“ (Text vorverarbeiten) hinzu, bevor Sie Feature Hashing verwenden. 

* Fügen Sie nach dem Modul „Feature Hashing“ ein Modul „Select Columns“ (Spalten auswählen) hinzu, um die Textspalten aus dem Ausgabedataset zu entfernen. Sie benötigen die Textspalten nicht mehr, nachdem die Hashmerkmale generiert wurden.
    
* Erwägen Sie die Verwendung dieser Textvorverarbeitungsoptionen, um die Ergebnisse zu vereinfachen und die Genauigkeit zu verbessern:

    * Wörtertrennung
    * Stoppwortentfernung
    * Kasusnormalisierung
    * Entfernen von Interpunktions- und Sonderzeichen
    * Wortstammerkennung  

Der optimale Satz von Vorverarbeitungsmethoden, der in einer Lösung zum Einsatz kommt, hängt vom Fachgebiet, dem Vokabular und den Geschäftsanforderungen ab. Experimentieren Sie mit Ihren Daten, um zu ermitteln, welche Textverarbeitungsmethoden am effektivsten sind.

## <a name="next-steps"></a>Nächste Schritte
            
Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning an.