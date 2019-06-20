---
title: Erweitern von U-SQL-Skripts mit Python in Azure Data Lake Analytics
description: Hier erfahren Sie, wie Sie Python-Code in U-SQL-Skripts unter Verwendung von Azure Data Lake Analytics ausführen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: 0a49cbdb4caf474d0628fea3679ce712d37886e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813401"
---
# <a name="extend-u-sql-scripts-with-python-code-in-azure-data-lake-analytics"></a>Erweitern von U-SQL-Skripts mit Python-Code in Azure Data Lake Analytics

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass die Python-Erweiterungen in Ihrem Azure Data Lake Analytics-Konto installiert sind.

* Wechseln Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
* Klicken Sie im linken Menü unter **ERSTE SCHRITTE** auf **Beispielskripts**.
* Klicken Sie auf **U-SQL-Erweiterungen installieren** und dann auf **OK**.

## <a name="overview"></a>Übersicht 

Mit Python-Erweiterungen für U-SQL können Entwickler Python-Code per Massively Parallel Processing ausführen. Im folgenden Beispiel werden die grundlegenden Schritte veranschaulicht:

* Verwenden der `REFERENCE ASSEMBLY`-Anweisung zum Aktivieren von Python-Erweiterungen für das U-SQL-Skript
* Verwenden des `REDUCE`-Vorgangs zum Partitionieren der Eingabedaten für einen Schlüssel
* Die Python-Erweiterungen für U-SQL enthalten einen integrierten Reducer (`Extension.Python.Reducer`), mit dem Python-Code auf jedem Vertex ausgeführt wird, der dem Reducer zugewiesen ist.
* Das U-SQL-Skript enthält den eingebetteten Python-Code mit der Funktion „`usqlml_main`“, für die ein Pandas-DataFrame als Eingabe akzeptiert und ein Pandas-DataFrame als Ausgabe zurückgegeben wird.

--

    REFERENCE ASSEMBLY [ExtPython];

    DECLARE @myScript = @"
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ";

    @t  = 
        SELECT * FROM 
           (VALUES
               ("D1","T1","A1","@foo Hello World @bar"),
               ("D2","T2","A2","@baz Hello World @beer")
           ) AS 
               D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer(pyScript:@myScript);

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();

## <a name="how-python-integrates-with-u-sql"></a>Integration von Python in U-SQL

### <a name="datatypes"></a>Datentypen

* Zeichenfolgen- und numerische Spalten von U-SQL werden unverändert zwischen Pandas und U-SQL konvertiert
* U-SQL-NULL-Werte werden in und aus „`NA`“-Pandas-Werten konvertiert

### <a name="schemas"></a>Schemas

* Indexvektoren in Pandas werden in U-SQL nicht unterstützt. Alle Eingabedatenrahmen in der Python-Funktion haben stets einen numerischen 64-Bit-Index von 0 bis zur Anzahl der Zeilen minus 1. 
* U-SQL-Datasets können doppelte Spaltennamen haben
* U-SQL-Dataset-Spaltennamen, die keine Zeichenfolgen sind. 

### <a name="python-versions"></a>Python-Versionen
Nur Python 3.5.1 (für Windows kompiliert) wird unterstützt. 

### <a name="standard-python-modules"></a>Python-Standardmodule
Alle Python-Standardmodule sind enthalten.

### <a name="additional-python-modules"></a>Zusätzliche Python-Module
Neben den Python-Standardbibliotheken sind noch weitere häufig verwendete Python-Bibliotheken enthalten:

    pandas
    numpy
    numexpr

### <a name="exception-messages"></a>Ausnahmemeldungen
Derzeit wird eine Ausnahme in Python-Code als generischer Scheitelpunktfehler angezeigt. In Zukunft wird mit U-SQL-Auftragsfehlermeldungen die Python-Ausnahmemeldung angezeigt.

### <a name="input-and-output-size-limitations"></a>Einschränkungen der Ein- und Ausgabegröße
Jedem Scheitelpunkt ist eine begrenzte Menge an Arbeitsspeicher zugewiesen. Derzeit beträgt der Grenzwert 6 GB pro AU. Da die DataFrames für die Ein- und Ausgabe im Arbeitsspeicher im Python-Code vorhanden sein müssen, darf die Gesamtgröße für die Ein- und Ausgabe nicht mehr als 6 GB betragen.

## <a name="see-also"></a>Weitere Informationen
* [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Verwenden von U-SQL-Funktionen für Azure Data Lake Analytics-Aufträge](data-lake-analytics-use-window-functions.md)
* [Verwenden von Azure Data Lake Tools für Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
