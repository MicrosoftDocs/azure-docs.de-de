---
title: 'Select Columns in Dataset (Spalten im Dataset auswählen): Modulreferenz'
titleSuffix: Azure Machine Learning service
description: Erfahren Sie, wie Sie das Modul „Select Columns in Dataset“ (Spalten im Dataset auswählen) in Azure Machine Learning Service verwenden, um eine Teilmenge von Spalten für nachfolgende Vorgänge auszuwählen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: e7407f62bd3401411d56076b298bd8cd134ece62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027642"
---
# <a name="select-columns-in-dataset-module"></a>Modul „Select Columns in Dataset“ (Spalten im Dataset auswählen)

In diesem Artikel wird ein Modul der grafischen Benutzeroberfläche (Vorschau) für Azure Machine Learning Service beschrieben.

Wählen Sie mit diesem Modul eine Teilmenge von Spalten aus, die in nachfolgenden Vorgängen verwendet werden sollen. Das Modul entfernt die Spalten nicht physisch aus dem Quelldatensatz, sondern erstellt eine Teilmenge von Spalten ähnlich der *Sicht* oder *Projektion* einer Datenbank.

Dieses Modul ist hilfreich, um die für einen nachfolgenden Vorgang verfügbaren Spalten einzuschränken. Alternativ können Sie damit das Dataset verkleinern, indem Sie nicht benötigte Spalten entfernen.

Die Spalten im Dataset werden in derselben Reihenfolge ausgegeben wie in den Originaldaten, auch wenn Sie diese in einer anderen Reihenfolge angeben.

## <a name="how-to-use"></a>Verwendung

Dieses Modul enthält keine Parameter. Verwenden Sie die Spaltenauswahl, um die ein- oder auszuschließenden Spalten auszuwählen.

### <a name="choose-columns-by-name"></a>Auswählen von Spalten nach Namen

Das Modul bietet mehrere Optionen für die Spaltenauswahl nach Namen: 

+ Filtern und suchen

    Klicken Sie auf die Option **BY NAME**.

    Wenn Sie ein bereits aufgefülltes Dataset verbunden haben, sollte eine Liste der verfügbaren Spalten angezeigt werden. Wenn dies nicht der Fall ist, müssen Sie ggf. Upstream-Module ausführen, um die Spaltenliste anzuzeigen.

    Um die Liste zu filtern, geben Sie einen Begriff in das Suchfeld ein. Wenn Sie z. B. den Buchstaben `w` in das Suchfeld eingeben, wird die Liste gefiltert und enthält die Spaltennamen, in denen der Buchstabe `w` vorkommt.

    Wählen Sie Spalten aus, und klicken Sie auf den nach rechts weisenden Pfeil, um die ausgewählten Spalten in die Liste im rechten Bereich zu verschieben.

    + Um einen fortlaufenden Bereich von Spaltennamen auszuwählen, drücken Sie **UMSCHALT+KLICK**.
    + Um der Auswahl einzelne Spalten hinzuzufügen, drücken Sie **STRG+KLICK**.

    Klicken Sie auf das Häkchen, um die Auswahl zu speichern und zu schließen.

+ Verwenden von Namen in Kombination mit weiteren Regeln

    Klicken Sie auf die Option **WITH RULES**.
    
    Wählen Sie eine Regel aus, z. B. für das Anzeigen von Spalten eines bestimmten Datentyps.

    Klicken Sie auf die Namen einzelner Spalten dieses Typs, um sie der Auswahlliste hinzuzufügen.

+ Eingeben oder Einfügen einer durch Trennzeichen getrennten Liste von Spaltennamen

    Wenn Sie über ein großes Dataset verfügen, ist es möglicherweise einfacher, Indizes oder generierte Namenslisten zu verwenden, statt Spalten einzeln auszuwählen. Wenn Sie die Liste vorab vorbereitet haben:

    1. Klicken Sie auf die Option **WITH RULES**. 
    2. Wählen Sie **No columns** (Keine Spalten) und **Include** (Einschließen) aus, und klicken Sie dann in das Textfeld mit dem roten Ausrufezeichen. 
    3. Fügen oder geben Sie eine durch Trennzeichen getrennte Liste von Spaltennamen ein, die zuvor überprüft wurden. Das Modul kann nicht gespeichert werden, wenn eine Spalte einen ungültigen Namen aufweist. Überprüfen Sie deshalb die Namen im Vorfeld.
    
    Sie können diese Methode auch verwenden, um eine Liste von Spalten anhand ihrer Indexwerte anzugeben. 

### <a name="choose-by-type"></a>Auswählen nach Typ

Wenn Sie die Option **WITH RULES** verwenden, können Sie mehrere Bedingungen auf die Spaltenauswahlen anwenden. Beispielsweise kann es vorkommen, dass Sie nur Featurespalten eines numerischen Datentyps benötigen.

Die Option **BEGIN WITH** ermittelt Ihren Startpunkt und ist wichtig zum Verständnis der Ergebnisse. 

+ Bei Auswahl der Option **ALL COLUMNS** werden der Liste alle Spalten hinzugefügt. Anschließend müssen Sie die Option **Exclude** (Ausschließen) verwenden, um Spalten zu *entfernen*, die bestimmte Bedingungen erfüllen. 

    Sie können zunächst mit allen Spalten beginnen und dann Spalten nach Namen oder Typ entfernen.

+ Wenn Sie die Option **NO COLUMNS** auswählen, ist die Liste der Spalten am Anfang leer. Als Nächstes geben Sie Bedingungen an, um der Liste Spalten *hinzuzufügen*. 

    Wenn Sie mehrere Regeln anwenden, ist jede Bedingung **additiv**. Angenommen, Sie beginnen ohne Spalten und fügen dann eine Regel hinzu, um alle numerischen Spalten abzurufen. Im Dataset mit Automobilpreisen erhalten Sie daraufhin 16 Spalten. Klicken Sie anschließend auf das **+** -Zeichen, um eine neue Bedingung hinzuzufügen, und wählen Sie dann **Include all features** (Alle Features einschließen) aus. Das resultierende Dataset umfasst alle numerischen Spalten sowie alle Featurespalten einschließlich einiger textbasierten Featurespalten.

### <a name="choose-by-column-index"></a>Auswählen nach Spaltenindex

Der Spaltenindex bezieht sich auf die Reihenfolge der Spalte innerhalb des ursprünglichen Datasets.

+ Spalten werden sequenziell beginnend mit 1 nummeriert.  
+ Um einen Bereich von Spalten abzurufen, verwenden Sie einen Bindestrich. 
+ Offene Spezifikationen wie `1-` oder `-3` sind nicht zulässig.
+ Doppelte Indexwerte (oder Spaltennamen) sind nicht zulässig und können einen Fehler verursachen.

Beispiel: Wenn Ihr Dataset mindestens acht Spalten enthält, können Sie eines der folgenden Beispiele einfügen, um mehrere nicht zusammenhängende Spalten zurückzugeben: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

Das letzte Beispiel verursacht zwar keinen Fehler, gibt jedoch eine einzelne Instanz der Spalte `4` zurück.



### <a name="change-order-of-columns"></a>Ändern der Reihenfolge von Spalten

Bei der Option **Allow duplicates and preserve column order in selection** (Duplikate zulassen und Spaltenreihenfolge in der Auswahl beibehalten) beginnen Sie mit einer leeren Liste. Anschließend fügen Sie nach Namen oder Index ausgewählte Spalten hinzu. Im Unterschied zu anderen Optionen, die Spalten immer in ihrer „natürlichen Reihenfolge“ zurückgeben, gibt diese Option die Spalten in der von Ihnen benannten oder aufgelisteten Reihenfolge aus. 

Beispiel: In einem Dataset, das die Spalten Col1, Col2, Col3 und Col4 enthält, können Sie die Reihenfolge der Spalten umkehren und die Spalte 2 auslassen, indem Sie eine der folgenden Listen angeben:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Gruppe der verfügbaren Module](module-reference.md) für Azure Machine Learning Service an. 