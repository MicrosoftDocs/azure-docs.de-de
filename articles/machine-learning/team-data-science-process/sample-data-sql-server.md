---
title: 'Datenstichproben in SQLServer in Azure: Team Data Science-Prozess'
description: Stichproben für SQL Server-Daten in Azure unter Verwendung von SQL oder der Programmiersprache Python. die anschließend in Azure Machine Learning verschoben werden.
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
ms.openlocfilehash: 7ac1fc5688dad3406041f36ff858e6fd27c7272f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321865"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Erstellen von Datenstichproben aus SQL Server in Azure

Dieser Artikel beschreibt die Erstellung von Stichproben für SQL Server-Daten in Azure unter Verwendung von SQL oder der Programmiersprache Python. Es wird gezeigt, wie erfasste Daten in Azure Machine Learning verschoben werden, indem Sie sie in einer Datei speichern, diese in einen Azure-Blob hochladen und anschließend in Azure Machine Learning Studio lesen.

Die Python-Stichprobe verwendet die [pyodbc](https://code.google.com/p/pyodbc/)-ODBC-Bibliothek für die Verbindung mit SQL Server auf Azure und die [Pandas](https://pandas.pydata.org/)-Bibliothek zum Ausführen der Stichprobe.

> [!NOTE]
> Beim SQL-Beispielcode in diesem Dokument wird davon ausgegangen, dass die Daten auf einem SQL Server in Azure gespeichert sind. Wenn dies nicht der Fall ist, finden Sie im Thema [Verschieben von Daten zu SQL Server auf einem virtuellen Azure-Computer](move-sql-server-virtual-machine.md) Anweisungen zum Verschieben von Daten zu SQL Server unter Azure.
> 
> 

**Warum eine Datenstichprobe entnehmen?**
Wenn das zu analysierende Dataset groß ist, sollten Sie in der Regel eine Komprimierung der Daten durchführen, um eine geringere aber immer noch repräsentative Größe zu erhalten. Die Stichprobenentnahme erleichtert das Verständnis der Daten, das Durchsuchen und die Funktionsverarbeitung. Die Funktion besteht innerhalb des [Team Data Science-Prozesses (TDSP)](./index.yml) darin, schnell Prototypen der Funktionen zur Datenverarbeitung und Machine Learning-Modelle zu erstellen.

Dieser Stichprobentask ist ein Schritt im [Team Data Science-Prozess (TDSP)](./index.yml).

## <a name="using-sql"></a><a name="SQL"></a>Mit SQL
In diesem Abschnitt werden verschiedene Methoden beschrieben, um mit SQL einfache Stichproben der Daten in der Datenbank zu extrahieren. Wählen Sie eine Methode, die für Ihre Datengröße und -verteilung geeignet ist.

Die beiden folgenden Beispiele zeigen, wie Sie mit `newid` in SQL Server eine Stichprobe erstellen. Die von Ihnen gewählte Methode hängt davon ab, wie zufällig die Stichprobe sein soll (im folgenden Beispielcode wird angenommen, dass „pk_id“ ein automatisch generierter Primärschlüssel ist).

1. Beispiel mit geringerer Randomisierung

    ```sql
    select  * from <table_name> where <primary_key> in 
    (select top 10 percent <primary_key> from <table_name> order by newid())
    ```

2. Beispiel mit höherer Randomisierung 

    ```sql
    SELECT * FROM <table_name>
    WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)
    ```

Sie können auch mit TABLESAMPLE eine Stichprobe der Daten erstellen. Diese Option ist möglicherweise besser, wenn Ihre Daten sehr umfangreich sind (vorausgesetzt, dass die Daten auf unterschiedlichen Seiten nicht korrelieren), damit die Abfrage in einem angemessenen Zeitraum abgeschlossen wird.

```sql
SELECT *
FROM <table_name> 
TABLESAMPLE (10 PERCENT)
```

> [!NOTE]
> Sie können Funktionen aus diesen Stichprobendaten durchsuchen und generieren, indem Sie sie in einer neuen Tabelle speichern.
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning
Sie können die Beispielabfragen oben direkt im Modul [Import Data][import-data] von Azure Machine Learning verwenden, um Stichproben aus den Daten dynamisch zu erstellen und in einem Azure Machine Learning-Experiment zu verwenden. Hier ist ein Screenshot der Verwendung des Reader-Moduls zum Lesen der erfassten Daten:

![reader sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>Mit der Programmiersprache Python
In diesem Abschnitt wird veranschaulicht, wie die [pyodbc-Bibliothek](https://code.google.com/p/pyodbc/) in Python für das Herstellen einer ODBC-Verbindung mit einer SQL Server-Datenbank verwendet wird. Die Datenbankverbindungszeichenfolge lautet wie folgt (ersetzen Sie „servername“, „dbname“, „username“ und „password“ durch die entsprechenden Werte Ihrer Konfiguration):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

Die [Pandas](https://pandas.pydata.org/) -Bibliothek in Python bietet eine Vielzahl von Datenstrukturen und Datenanalysetools für die Datenbearbeitung durch Python-Programmierung. Der folgende Code liest eine Datenstichprobe von 0,1 % aus einer Tabelle in einer Azure SQL-Datenbank in ein Pandas-DataFrame:

```python
import pandas as pd

# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)
```

Sie können jetzt mit den erfassten Daten im Pandas-DataFrame arbeiten. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Herstellen einer Verbindung mit Azure Machine Learning
Mit dem folgenden Beispielcode können Sie die Stichprobendaten in einer Datei speichern und in ein Azure-Blob hochladen. Die Daten im Blob können mit dem Modul [Import Data][import-data] direkt in ein Azure Machine Learning-Experiment gelesen werden. Die Schritte lauten wie folgt: 

1. Schreiben Sie den Pandas-DataFrame in eine lokale Datei:

    ```python
    dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
    ```

2. Laden Sie die lokale Datei in das Azure-Blob hoch:

    ```python
    from azure.storage import BlobService
    import tables

    STORAGEACCOUNTNAME= <storage_account_name>
    LOCALFILENAME= <local_file_name>
    STORAGEACCOUNTKEY= <storage_account_key>
    CONTAINERNAME= <container_name>
    BLOBNAME= <blob_name>

    output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
    localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory

    try:

    #perform upload
    output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)

    except:            
        print ("Something went wrong with uploading blob:"+BLOBNAME)
    ```

3. Lesen Sie die Daten (wie in der folgenden Abbildung dargestellt) mit dem Azure Machine Learning-Modul [Import Data][import-data] aus dem Azure-Blob ein:

![Reader-Blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>Der Team Data Science-Prozess in Aktion: Beispiel
Eine exemplarische Vorgehensweise zur Verwendung des Team Data Science-Prozesses mit einem öffentlichen Dataset finden Sie unter [Der Team Data Science-Prozess in Aktion: Verwenden von SQL Server](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: /azure/machine-learning/studio-module-reference/import-data