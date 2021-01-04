---
title: 'Preprocess Text: Modulreferenz'
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie mit dem Modul Preprocess Text im Azure Machine Learning-Designer Text bereinigen und vereinfachen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/16/2020
ms.openlocfilehash: 366b30df677a5b74bc7d70e1aea60e05b4df0152
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659282"
---
# <a name="preprocess-text"></a>Preprocess Text

In diesem Artikel wird ein Modul im Azure Machine Learning-Designer beschrieben.

Verwenden Sie das Modul **Preprocess Text** (Text vorverarbeiten), um Text zu bereinigen und zu vereinfachen. Es unterstützt diese gängigen Textverarbeitungsvorgänge:

* Entfernen von Stoppwörtern
* Verwenden regulärer Ausdrücke zum Suchen nach und Ersetzen von spezifischen Zielzeichenfolgen
* Lemmatisierung, bei der mehrere verwandte Wörter in eine einzelne kanonische Form konvertiert werden
* Kasusnormalisierung
* Entfernen bestimmter Klassen von Zeichen, z.B. Ziffern, Sonderzeichen und Sequenzen von wiederholten Zeichen (etwa „aaaa“)
* Identifizierung und Entfernung von E-Mails und URLs

Das Modul **Preprocess Text** (Text vorverarbeiten) unterstützt zurzeit nur Englisch.

## <a name="configure-text-preprocessing"></a>Konfigurieren der Textvorverarbeitung  

1.  Fügen Sie das Modul **Preprocess Text** (Text vorverarbeiten) zu Ihrer Pipeline in Azure Machine Learning hinzu. Sie finden dieses Modul unter **Textanalyse**.

1. Stellen Sie eine Verbindung mit einem Dataset her, das mindestens eine Spalte mit Text enthält.

1. Wählen Sie die Sprache aus der Dropdownliste **Language** (Sprache) aus.

1. **Text column to clean** (Zu bereinigende Textspalte): Wählen Sie die Spalte aus, die Sie vorverarbeiten möchten.

1. **Remove stop words** (Stoppwörter entfernen): Wählen Sie diese Option aus, wenn Sie eine vordefinierte Liste mit Stoppwörtern auf die Textspalte anwenden möchten. 

    Stoppwortlisten sind sprachabhängig und anpassbar.

1. **Lemmatization** (Lemmatisierung): Wählen Sie diese Option aus, wenn Sie möchten, dass Wörter in ihrer kanonischen Form dargestellt werden. Diese Option ist nützlich, um die Anzahl eindeutiger Vorkommen von anderweitig ähnlichen Texttoken zu verringern.

    Der Lemmatisierungsprozess ist hochgradig sprachabhängig.

1. **Detect sentences** (Sätze erkennen): Wählen Sie diese Option aus, wenn das Modul beim Durchführen einer Analyse eine Satzbegrenzungsmarkierung einfügen soll.

    Dieses Modul verwendet drei senkrechte Striche (`|||`), um das Satzendezeichen darzustellen.

1. Führen Sie optionale Suchen- und Ersetzen-Vorgänge mithilfe regulärer Ausdrücke aus. Der reguläre Ausdruck wird vor allen anderen integrierten Optionen zuerst verarbeitet.

    * **Custom regular expression** (Benutzerdefinierter regulärer Ausdruck): Definieren Sie den Text, nach dem Sie suchen.
    * **Custom replacement string** (Benutzerdefinierte Ersatzzeichenfolge): Definieren Sie einen einzelnen Ersatzwert.

1. **Normalize case to lowercase** (Groß-/Kleinschreibung in Kleinbuchstaben normalisieren): Wählen Sie diese Option aus, wenn Sie ASCII-Großbuchstaben in Kleinbuchstaben umwandeln möchten.

    Wenn Zeichen nicht normalisiert werden, wird das gleiche Wort in Groß- und Kleinbuchstaben als zwei verschiedene Wörter betrachtet.

1. Sie können auch die folgenden Typen von Zeichen oder Zeichensequenzen aus dem verarbeiteten Ausgabetext entfernen:

    * **Remove numbers** (Zahlen entfernen): Wählen Sie diese Option aus, um alle numerischen Zeichen für die angegebene Sprache zu entfernen. Identifikationsnummern sind vom Fachgebiet und von der Sprache abhängig. Wenn numerische Zeichen ein integraler Bestandteil eines bekannten Worts sind, wird die Zahl möglicherweise nicht entfernt. Weitere Informationen finden Sie unter [Technische Hinweise](#technical-notes).
    
    * **Remove special characters** (Sonderzeichen entfernen): Verwenden Sie diese Option, um alle nicht alphanumerischen Sonderzeichen zu entfernen.
    
    * **Remove duplicate characters** (Doppelte Zeichen entfernen): Wählen Sie diese Option aus, um zusätzliche Zeichen in Sequenzen zu entfernen, die sich mehr als zwei Mal wiederholen. Beispielsweise würde eine Sequenz wie „aaaaa“ in „aa“ reduziert.
    
    * **Remove email addresses** (E-Mail-Adressen entfernen): Wählen Sie diese Option aus, um eine beliebige Sequenz im Format `<string>@<string>` zu entfernen.  
    * **Remove URLs** (URLs entfernen): Wählen Sie diese Option aus, um alle Sequenzen zu entfernen, die die folgenden URL-Präfixe enthalten: `http`, `https`, `ftp`, `www`
    
1. **Expand verb contractions** (Verbverschmelzungen erweitern): Diese Option gilt nur für Sprachen, die Verbverschmelzungen verwenden (zurzeit nur für Englisch). 

    Wenn Sie diese Option auswählen, können Sie beispielsweise die Phrase *„wouldn't stay there“* durch *„would not stay there“* ersetzen.

1. **Normalize backslashes to slashes** (Umgekehrte Schrägstriche in Schrägstriche normalisieren): Wählen Sie diese Option aus, um alle Instanzen von `\\``/` zuzuordnen.

1. **Split tokens on special characters** (Token für Sonderzeichen aufteilen): Wählen Sie diese Option aus, wenn Sie Wörter bei Zeichen wie `&`, `-` usw. aufteilen möchten. Mit dieser Option können auch Sonderzeichen verringert werden, wenn sie mehr als zwei Mal wiederholt werden. 

    Beispielsweise würde die Zeichenfolge `MS---WORD` in drei Token aufgeteilt: `MS`, `-` und `WORD`.

1. Übermitteln Sie die Pipeline.

## <a name="technical-notes"></a>Technische Hinweise

Für das Modul **preprocess-text** in Studio (klassisch) und im Designer werden unterschiedliche Sprachmodelle genutzt. Für den Designer wird ein mehrstufiges Modell mit CNN-Training von [spaCy](https://spacy.io/models/en) verwendet. Bei der Verwendung unterschiedlicher Modelle werden verschiedene Tokenizer und Verfahren für die Satzteilmarkierung genutzt, und dies führt zu anderen Ergebnissen.

Nachstehend sind einige Beispiele aufgeführt:

| Konfiguration | Ausgabeergebnis |
| --- | --- |
|Mit Auswahl aller Optionen </br> Erläuterung: </br> Für Fälle wie „3test“ in „WC-3 3test 4test“ wird vom Designer das gesamte Wort „3test“ entfernt. Der Grund ist, dass das Token „3test“ von der Satzteilmarkierung in diesem Kontext als Zahl markiert wird und vom Modul entsprechend entfernt wird.| :::image type="content" source="./media/module/preprocess-text-all-options-selected.png" alt-text="Mit Auswahl aller Optionen" border="True"::: |
|Nur Auswahl von `Removing number` </br> Erläuterung: </br> „3test“ und „4-EC“ werden vom Tokenizer des Designers nicht unterteilt und als gesamte Token behandelt. Daher werden die Zahlen in diesen Wörtern nicht entfernt.| :::image type="content" source="./media/module/preprocess-text-removing-numbers-selected.png" alt-text="Nur Auswahl von „Removing number“" border="True"::: |

Sie können auch einen regulären Ausdruck verwenden, um angepasste Ergebnisse auszugeben:

| Konfiguration | Ausgabeergebnis |
| --- | --- |
|Mit Auswahl aller Optionen </br> Benutzerdefinierter regulärer Ausdruck: `(\s+)*(-|\d+)(\s+)*` </br> Benutzerdefinierte Ersatzzeichenfolge: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-all-options-selected.png" alt-text="Mit Auswahl aller Optionen und regulärem Ausdruck" border="True"::: |
|Nur Auswahl von `Removing number` </br> Benutzerdefinierter regulärer Ausdruck: `(\s+)*(-|\d+)(\s+)*` </br> Benutzerdefinierte Ersatzzeichenfolge: `\1 \2 \3`| :::image type="content" source="./media/module/preprocess-text-regular-expression-removing-numbers-selected.png" alt-text="Auswahl von „Removing number“ und regulärer Ausdruck" border="True"::: |


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich den [Satz der verfügbaren Module](module-reference.md) für Azure Machine Learning an. 