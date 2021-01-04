---
title: Untersuchen von Daten auf einem virtuellen SQL Server-Computer – Team Data Science-Prozess
description: Durchsuchen und Verarbeiten von Daten sowie Erstellen von Funktionen mit Python oder SQL auf einem virtuellen SQL Server-Computer in Azure
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314627"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Verarbeiten von Daten auf einer SQL Server-VM in Azure
In diesem Dokument werden das Durchsuchen von Daten und das Generieren von Funktionen aus Daten auf einer SQL Server-VM in Azure beschrieben. Dieses Ziel kann über eine Datenanalyse mithilfe von SQL oder über die Verwendung einer Programmiersprache wie Python erreicht werden.

> [!NOTE]
> Bei den SQL-Beispielanweisungen in diesem Dokument wird davon ausgegangen, dass die Daten auf einem SQL Server gespeichert sind. Wenn dies nicht der Fall ist, finden Sie im Cloud Data Science-Prozess Anweisungen zum Verschieben der Daten zu einer SQL Server-Instanz.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Mit SQL
Im folgenden Abschnitt werden Möglichkeiten der Datenanalyse mit SQL beschrieben:

1. [Durchsuchen von Daten](#sql-dataexploration)
2. [Generieren von Funktionen](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Durchsuchen von Daten
Hier finden Sie einige SQL-Beispielskripts, die zum Durchsuchen von Daten auf einem SQL Server verwendet werden können.

> [!NOTE]
> Für ein praktisches Beispiel können Sie das [NYC Taxi-Dataset](https://www.andresmh.com/nyctaxitrips/) verwenden. Eine vollständige exemplarische Vorgehensweise finden Sie im IPython Notebook-Beispiel unter [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb).
> 
> 

1. Abrufen der Anzahl der Vorkommen pro Tag:
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. Abrufen der Ebenen in einer Kategoriespalte:
   
    `select  distinct <column_name> from <databasename>`
3. Abrufen der Anzahl von Ebenen in Kombination zweier kategorischer Spalten: 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Abrufen der Verteilung der numerischen Spalten:
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Generieren von Funktionen
In diesem Abschnitt werden Methoden zum Generieren von Funktionen mithilfe von SQL beschrieben.  

1. [Anzahlbasierte Funktionsgenerierung](#sql-countfeature)
2. [Gruppenbasierte Funktionsgenerierung](#sql-binningfeature)
3. [Einführen von Funktionen aus einer einzelnen Spalte](#sql-featurerollout)

> [!NOTE]
> Wenn Sie zusätzliche Funktionen generieren, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Anzahlbasierte Funktionsgenerierung
Anhand der folgenden Beispiele werden zwei Methoden zur Generierung von Zählfunktionen demonstriert. Die erste Methode verwendet eine bedingte Summe und die zweite die WHERE-Klausel. Diese Ergebnisse können dann mit der ursprünglichen Tabelle (über Primärschlüsselspalten) zusammengeführt werden, um die Anzahlfunktionen zusammen mit den ursprünglichen Daten verwenden zu können.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Gruppenbasierte Funktionsgenerierung
Das folgende Beispiel zeigt, wie Sie klassifizierte Funktionen erstellen, indem Sie eine numerische Spalte, die stattdessen als Funktion verwendet wird, klassifizieren (mit fünf Containern):

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```

### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>Einführen von Funktionen aus einer einzelnen Spalte
In diesem Abschnitt wird gezeigt, wie Sie eine einzelne Spalte in eine Tabelle einführen, um zusätzliche Funktionen zu generieren. Im Beispiel wird davon ausgegangen, dass die Tabelle, aus der Sie Funktionen generieren, die Spalten "latitude" und "longitude" enthält.

Es folgt eine kurze Einführung in Positionsdaten mit Längen- und Breitengrad (aus Stackoverflow: [How to measure the accuracy of latitude and longitude?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)(Gewusst wie: Messen der Genauigkeit von Längen- und Breitengraden)). Dieser Leitfaden ist zum Verständnis hilfreich, bevor der Standort als ein oder mehrere Features einbezogen wird:

* Das Vorzeichen gibt an, ob sich die Position im Norden, Süden, Osten oder Westen auf dem Globus befindet.
* Ein Hunderterwert ungleich null gibt an, dass der Längengrad und nicht der Breitengrad verwendet wird!
* Die Zehnerstelle gibt eine Position auf ca. 1.000 km Genauigkeit an. Damit verfügen Sie über nützliche Informationen zum Kontinent oder Ozean.
* Die Einheitenstelle (ein Dezimalzeichen) gibt die Position auf etwa 111 km (60 nautische Meilen, etwa 69 Meilen) genau an. Sie kann Ihnen eine ungefähre Vorstellung davon geben, in welchem großen Staat oder Land Sie sich befinden.
* Die erste Dezimalstelle gibt einen Wert von etwa 11,1 km Genauigkeit an: Sie können damit die Position von einer großen Stadt von der einer benachbarten großen Stadt unterscheiden.
* Die zweite Dezimalstelle bietet eine Genauigkeit von ca. 1,1 km: Damit können Sie Dörfer voneinander trennen.
* Die dritte Dezimalstelle ist auf etwa 110 m genau: Sie können so große landwirtschaftliche Felder oder Industriegebiete identifizieren.
* Die vierte Dezimalstelle ist auf etwa 11 m genau: Sie können so einzelne Grundstücke unterscheiden. Dies ist vergleichbar mit der typischen Genauigkeit eines nicht korrigierten GPS-Geräts ohne Störungen.
* Die fünfte Dezimalstelle ist auf etwa 1,1 m genau: Damit können Sie einzelne Strukturen voneinander unterscheiden. Eine Genauigkeit auf dieser Stufe lässt sich mit kommerziellen GPS-Geräten nur mit einer differenziellen Korrektur erreichen.
* Die sechste Dezimalstelle bietet eine Genauigkeit von etwa 0,11 m: Damit können Sie Strukturen detailgetreu erkennen, um Landschaften zu planen oder Straßen zu bauen. Dies sollte mehr als ausreichend für die Nachverfolgung der Bewegungen von Gletschern und Flüssen sein. Erreicht wird diese Genauigkeit nur durch umfangreiche GPS-Maßnahmen, z. B. differenziell korrigiertes GPS.

Die Positionsinformationen können wie folgt in Funktionen umgewandelt werden, wobei die Informationen zu Region, Standort und Stadt getrennt werden. Sie können auch einen REST-Endpunkt wie die Bing Maps-API unter [Find a Location by Point](/bingmaps/rest-services/locations/find-a-location-by-point) (Suchen eines Standorts nach Punkt) aufrufen, um Informationen über Region/Bezirk abzurufen.

```sql
select 
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Diese positionsbasierten Funktionen können dann wie oben beschrieben zum Generieren weiterer Zählfunktionen verwendet werden. 

> [!TIP]
> Sie können die Datensätze programmgesteuert mithilfe Ihrer bevorzugten Sprache einfügen. Möglicherweise müssen Sie die Daten in Blöcken einfügen, um die Effizienz des Schreibvorgangs zu verbessern. (Ein Beispiel zur Vorgehensweise unter Verwendung von „pyodbc“ finden Sie unter [A HelloWorld sample to access SQLServer with python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python) [HelloWorld-Beispiel für den SQL Server-Zugriff mit Python].) (Ein Beispiel zur Vorgehensweise unter Verwendung von „pyodbc“ finden Sie unter [Hilfsprogramm BCP](/sql/tools/bcp-utility).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning
Die neu generierte Funktion kann als Spalte einer vorhandenen Tabelle hinzugefügt oder in einer neuen Tabelle gespeichert und für Machine Learning mit der ursprünglichen Tabelle zusammengeführt werden. Sie können, wie unten dargestellt, mit dem [Daten importieren][import-data]-Modul in Azure Machine Learning Funktionen generieren oder darauf zugreifen, sofern sie bereits vorhanden sind:

![azureml-Reader][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Mit einer Programmiersprache wie Python
Die Verwendung von Python zum Durchsuchen von Daten und zum Generieren von Funktionen mit Daten in SQL Server ähnelt der Datenverarbeitung in Azure-Blobs mit Python, die unter [Verarbeiten von Azure-Blobdaten in Ihrer Data Science-Umgebung](data-blob.md) beschrieben ist. Laden Sie die Daten aus der Datenbank zur weiteren Verarbeitung in einen Pandas-Datenrahmen. In diesem Abschnitt werden das Herstellen einer Verbindung mit der Datenbank und das Laden der Daten in den DataFrame beschrieben.

Das folgende Format für die Verbindungszeichenfolge kann verwendet werden, um aus Python mit „pyodbc“ eine Verbindung mit einer SQL Server-Datenbank herzustellen (ersetzen Sie „servername“, „dbname“, „username“ und „password“ durch Ihre Daten):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Die [Pandas-Bibliothek](https://pandas.pydata.org/) in Python bietet eine Vielzahl an Datenstrukturen und Datenanalysetools für die Datenbearbeitung zur Python-Programmierung. Der folgende Code liest die aus einer SQL Server-Datenbank zurückgegebenen Ergebnisse in ein Pandas-DataFrame ein:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Sie können nun mit dem Pandas-Datenrahmen arbeiten. Die Vorgehensweise wird im Artikel [Verarbeiten von Azure-Blob-Daten in einer Data Science-Umgebung](data-blob.md) beschrieben.

## <a name="azure-data-science-in-action-example"></a>Beispiel für Azure Data Science in Aktion
Ein umfassendes Beispiel für den Azure Data Science Process anhand eines öffentlichen Datasets finden Sie unter [Azure Data Science Process in Aktion](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data