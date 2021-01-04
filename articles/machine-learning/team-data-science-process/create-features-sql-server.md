---
title: 'Erstellen von Features in SQL Server mithilfe von SQL und Python: Team Data Science-Prozess'
description: Generieren von Features für Daten, die in einer SQL Server-VM in Azure gespeichert sind, mit SQL und Python (Teil des Team Data Science-Prozesses).
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
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322042"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Erstellen von Features für Daten in SQL Server mithilfe von SQL und Python
Dieses Dokument veranschaulicht das Generieren von Features für Daten auf einem virtuellen SQL Server-Computer in Azure, die dazu beitragen, dass Algorithmen effizienter aus den Daten lernen können. Für diese Aufgabe können Sie SQL oder eine Programmiersprache wie Python verwenden. Beide Herangehensweisen werden hier vorgestellt.

Dieser Task ist ein Schritt im [Team Data Science-Prozess (TDSP)](./index.yml).

> [!NOTE]
> Für ein praktisches Beispiel können Sie das [NYC Taxi-DataSet](https://www.andresmh.com/nyctaxitrips/) verwenden. Eine vollständige Beschreibung finden Sie im IPython Notebook-Beispiel unter [NYC Data wrangling using IPython Notebook and SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb).
> 
> 

## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie Folgendes abgeschlossen haben:

* Sie haben ein Azure-Speicherkonto erstellt. Anweisungen finden Sie unter [Erstellen eines Azure-Speicherkontos](../../storage/common/storage-account-create.md).
* Ihre Daten sind in SQL Server gespeichert. Falls nicht, finden Sie unter [Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning](move-sql-azure.md) Anweisungen zum Verschieben von Daten.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Generieren von Funktionen mit SQL
In diesem Abschnitt werden Methoden zum Generieren von Funktionen mithilfe von SQL beschrieben.  

* [Anzahlbasierte Funktionsgenerierung](#sql-countfeature)
* [Gruppenbasierte Funktionsgenerierung](#sql-binningfeature)
* [Einführen von Funktionen aus einer einzelnen Spalte](#sql-featurerollout)

> [!NOTE]
> Wenn Sie zusätzliche Funktionen generieren, können Sie diese als Spalten in der vorhandenen Tabelle hinzufügen oder eine neue Tabelle mit den zusätzlichen Funktionen und einem Primärschlüssel erstellen, die dann mit der ursprünglichen Tabelle zusammengeführt werden kann.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Anzahlbasierte Funktionsgenerierung
In diesem Abschnitt werden zwei Methoden zur Generierung von Anzahlfunktionen demonstriert. Die erste Methode verwendet eine bedingte Summe und die zweite die "where"-Klausel. Diese neuen Features können dann mit der ursprünglichen Tabelle (über Primärschlüsselspalten) zusammengeführt werden, um die Anzahlfunktionen zusammen mit den ursprünglichen Daten verwenden zu können.

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

Es folgt eine kurze Einführung in Positionsdaten mit Längen- und Breitengrad (aus Stackoverflow: `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Nachfolgend finden Sie einige wichtige Aspekte zu Positionsdaten, die Sie kennen sollten, bevor Sie Funktionen aus dem Feld erstellen:

* Das Vorzeichen gibt an, ob sich die Position im Norden, Süden, Osten oder Westen auf dem Globus befindet.
* Eine vor dem Komma dreistellige Zahl ungleich Null gibt den Längengrad an. Breitengrade werden nicht verwendet.
* Die Zehnerstelle gibt eine Position auf ca. 1.000 km Genauigkeit an. Damit wissen Sie, welcher Kontinent oder welcher Ozean gemeint ist.
* Die Einheitenstelle (ein Dezimalzeichen) gibt die Position auf etwa 111 km (60 nautische Meilen, etwa 69 Meilen) genau an. Damit wissen Sie ungefähr das Land/die Region.
* Die erste Dezimalstelle gibt einen Wert von etwa 11,1 km Genauigkeit an: Sie können damit die Position von einer großen Stadt von der einer benachbarten großen Stadt unterscheiden.
* Die zweite Dezimalstelle bietet eine Genauigkeit von ca. 1,1 km: Damit können Sie Dörfer voneinander trennen.
* Die dritte Dezimalstelle ist auf etwa 110 m genau: Sie können so große landwirtschaftliche Felder oder Industriegebiete identifizieren.
* Die vierte Dezimalstelle ist auf etwa 11 m genau: Sie können so einzelne Grundstücke unterscheiden. Dies ist vergleichbar mit der typischen Genauigkeit eines nicht korrigierten GPS-Geräts ohne Störungen.
* Die fünfte Dezimalstelle ist auf etwa 1,1 m genau: Damit können Sie einzelne Strukturen voneinander unterscheiden. Eine Genauigkeit auf dieser Stufe lässt sich mit kommerziellen GPS-Geräten nur mit einer differenziellen Korrektur erreichen.
* Die sechste Dezimalstelle bietet eine Genauigkeit von etwa 0,11 m: Mit dieser Stufe können Sie Strukturen detailgetreu erkennen, um Landschaften zu planen oder Straßen zu bauen. Dies sollte mehr als ausreichend für die Nachverfolgung der Bewegungen von Gletschern und Flüssen sein. Erreicht wird dieses Ziel nur durch umfangreiche GPS-Maßnahmen, z. B. differenziell korrigiertes GPS.

Die Positionsinformationen können in Funktionen umgewandelt werden, indem die Informationen zu Region, Standort und Stadt herausisoliert werden. Sie können auch einen REST-Endpunkt wie die Bing Maps-API aufrufen (Informationen über Region/Bezirk können Sie unter `https://msdn.microsoft.com/library/ff701710.aspx` abrufen).

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
> Sie können die Datensätze programmgesteuert mithilfe Ihrer bevorzugten Sprache einfügen. Möglicherweise müssen Sie die Daten in Blöcken einfügen, um die Effizienz der Schreibvorgänge zu erhöhen. [Hier finden Sie ein Beispiel für diesen Vorgang unter Verwendung von pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Eine weitere Möglichkeit zum Einfügen von Daten in die Datenbank bietet das [Hilfsprogramm BCP](/sql/tools/bcp-utility).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning
Die neu generierte Funktion kann als Spalte einer vorhandenen Tabelle hinzugefügt oder in einer neuen Tabelle gespeichert und für Machine Learning mit der ursprünglichen Tabelle zusammengeführt werden. Sie können wie unten dargestellt mit dem [Import Data](/azure/machine-learning/studio-module-reference/import-data) -Modul in Azure ML Features generieren oder, falls sie bereits vorhanden sind, darauf zugreifen:

![Azure ML-Reader](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Mit einer Programmiersprache wie Python
Die Verwendung von Python zum Generieren von Funktionen aus Daten in SQL Server ähnelt der Datenverarbeitung in Azure-Blobs mit Python. Einen Vergleich finden Sie unter [Verarbeiten von Azure-Blobdaten in Ihrer Data Science-Umgebung](data-blob.md). Laden Sie die Daten aus der Datenbank zur weiteren Verarbeitung in einen Pandas-Datenrahmen. Dieser Abschnitt beschreibt das Herstellen einer Verbindung mit der Datenbank und das Laden der Daten in den Datenrahmen.

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

Sie können nun mit dem Pandas-DataFrame arbeiten, wie unter [Erstellen von Features für Azure Blob Storage-Daten mithilfe von Panda](./explore-data-blob.md) beschrieben.