---
title: Synapse Studio-Notebooks
description: In diesem Artikel erfahren Sie, wie Sie Azure Synapse Studio-Notebooks erstellen und entwickeln, um Datenvorbereitung und -visualisierung durchzuführen.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/19/2020
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: bfb822481e00e84fa17bd4b1fcb58c1d4b89a124
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450865"
---
# <a name="create-develop-and-maintain-synapse-studio-notebooks-in-azure-synapse-analytics"></a>Erstellen, Entwickeln und Verwalten von Synapse Studio-Notebooks in Azure Synapse Analytics

Ein Synapse Studio-Notebook ist eine Weboberfläche, mit der Sie Dateien erstellen können, die Livecode, Visualisierungen und beschreibenden Text enthalten. Notebooks sind ein guter Ausgangspunkt, um Ideen zu überprüfen und schnelle Experimente zu verwenden, um Erkenntnisse aus Ihren Daten zu gewinnen. Notebooks werden auch häufig bei der Datenvorbereitung, Datenvisualisierung, Machine Learning und andere Big Data-Szenarien verwendet.

Mit einem Azure Synapse Studio-Notebook können Sie:

* Ohne Einrichtungsaufwand sofort loslegen.
* Daten mit integrierten Sicherheitsfeatures auf Unternehmensniveau schützen.
* Daten in Rohformaten (CSV, TXT, JSON usw.), verarbeiteten Dateiformaten (Parquet, Delta Lake, ORC usw.) und tabellarischen SQL-Datendateien gegen Spark und SQL analysieren.
* Produktiv sein mit erweiterten Funktionen zur Dokumenterstellung und integrierter Datenvisualisierung.

In diesem Artikel wird beschrieben, wie Sie Notebooks in Azure Synapse Studio verwenden.

## <a name="preview-of-the-new-notebook-experience"></a>Vorschau auf die neue Notebookumgebung
Das Synapse-Team brachte die neue Notebookkomponente in Synapse Studio ein, um den Kunden von Microsoft eine einheitliche Notebookumgebung zu bieten und die Erkennbarkeit, Produktivität, Freigabe und Zusammenarbeit zu maximieren. Die neue Notebookumgebung ist für die Vorschau bereit. Aktivieren Sie die Schaltfläche für **Previewfunktionen** in der Symbolleiste des Notebooks. Die nachfolgende Tabelle zeigt einen Featurevergleich zwischen dem vorhandenen Notebook (dem so genannten „klassischen Notebook“) und dem neuen Notebook der Vorschau.  

|Funktion|Klassisches Notebook|Notebook der Vorschau|
|--|--|--|
|%run| Nicht unterstützt | &#9745;|
|%history| Nicht unterstützt |&#9745;
|%load| Nicht unterstützt |&#9745;|
|%%html| Nicht unterstützt |&#9745;|
|Drag & Drop zum Verschieben einer Zelle| Nicht unterstützt |&#9745;|
|Persistente Display()-Ausgabe|&#9745;| Nicht verfügbar |
|Alles abbrechen| &#9745;| Nicht verfügbar|
|Alle Zellen oberhalb ausführen|&#9745;| Nicht verfügbar |
|Alle Zellen unterhalb ausführen|&#9745;| Nicht verfügbar |
|Textzelle mit Symbolleisten-Schaltflächen formatieren|&#9745;| Nicht verfügbar |
|Rückgängigmachen des Zellenvorgangs| &#9745;| Nicht verfügbar |


## <a name="create-a-notebook"></a>Erstellen eines Notebooks

Ein Notebook kann auf zwei Arten erstellt werden. Sie können ein neues Notebook erstellen oder ein vorhandenes Notebook aus dem **Objekt-Explorer** in einen Azure Synapse-Arbeitsbereich importieren. Azure Synapse Studio-Notebooks können Jupyter Notebook IPYNB-Standarddateien erkennen.

![Erstellen und Importieren von Notebooks](./media/apache-spark-development-using-notebooks/synapse-create-import-notebook-2.png)

## <a name="develop-notebooks"></a>Entwickeln von Notebooks

Notebooks bestehen aus Zellen, bei denen es sich um einzelne Codeblöcke oder Text handelt, die unabhängig oder als Gruppe ausgeführt werden können.

### <a name="add-a-cell"></a>Hinzufügen einer Zelle

Es gibt mehrere Möglichkeiten, um Ihrem Notebook eine neue Zelle hinzuzufügen.

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

1. Erweitern Sie die Schaltfläche oben links **+ Zelle**, und wählen Sie **Codezelle hinzufügen** oder **Textzelle hinzufügen** aus.

    ![Hinzufügen-Zelle-mit-Schaltfläche-Zelle](./media/apache-spark-development-using-notebooks/synapse-add-cell-1.png)

2. Zeigen Sie mit dem Mauszeiger auf den Bereich zwischen zwei Zellen, und wählen Sie **Code hinzufügen** oder **Text hinzufügen** aus.

    ![Hinzufügen-Zelle-Zwischenraum](./media/apache-spark-development-using-notebooks/synapse-add-cell-2.png)

3. Verwenden Sie [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode). Drücken Sie **A**, um eine Zelle oberhalb der aktuellen Zelle einzufügen. Drücken Sie **B**, um eine Zelle unterhalb der aktuellen Zelle einzufügen.


# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

1. Erweitern Sie oben links die Schaltfläche **+ Zelle**, und wählen Sie **Codezelle** oder **Markdown-Zelle** aus.
    ![add-azure-notebook-cell-with-cell-button](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-1.png)
2. Wählen Sie das Pluszeichen am Anfang einer Zelle und anschließend **Codezelle** oder **Markdown-Zelle** aus.

    ![add-azure-notebook-cell-between-space](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-add-cell-2.png)

3. Verwenden Sie [aznb-Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode). Drücken Sie **A**, um eine Zelle oberhalb der aktuellen Zelle einzufügen. Drücken Sie **B**, um eine Zelle unterhalb der aktuellen Zelle einzufügen.

---

### <a name="set-a-primary-language"></a>Festlegen einer primären Sprache

Azure Synapse Studio-Notebooks unterstützen vier Apache Spark-Sprachen:

* PySpark (Python)
* Spark (Scala)
* SparkSQL
* .NET für Apache Spark (C#)

Sie können die primäre Sprache für neu hinzugefügte Zellen in der Dropdownliste in der oberen Befehlsleiste festlegen.

   ![Standard-Synapse-Sprache](./media/apache-spark-development-using-notebooks/synapse-default-language.png)

### <a name="use-multiple-languages"></a>Verwenden mehrerer Sprachen

Sie können in einem Notebook mehrere Sprachen verwenden, indem Sie den richtigen Magic-Befehl für die Sprache am Anfang einer Zelle angeben. In der folgenden Tabelle werden die Magic-Befehle zum Wechseln von Zellensprachen aufgelistet.

|Magic-Befehl |Sprache | BESCHREIBUNG |  
|---|------|-----|
|%%pyspark| Python | Eine **Python**-Abfrage im Spark-Kontext ausführen.  |
|%%spark| Scala | Eine **Scala**-Abfrage im Spark-Kontext ausführen.  |  
|%%sql| SparkSQL | Eine **SparkSQL**-Abfrage im Spark-Kontext ausführen.  |
|%%csharp | .NET für Spark (C#) | Führen Sie eine Abfrage vom Typ **.NET für Spark (C#)** für Spark-Kontext aus. |

Die folgende Abbildung zeigt ein Beispiel dafür, wie Sie eine PySpark-Abfrage mit dem Magic-Befehl **%%pyspark** schreiben können oder eine SparkSQL-Abfrage mit dem Magic-Befehl **%%sql** in einem **Spark (Scala)** -Notebook. Beachten Sie, dass die primäre Sprache für das Notebook auf PySpark festgelegt ist.

   ![Synapse-Spark-Magic-Befehle](./media/apache-spark-development-using-notebooks/synapse-spark-magics.png)

### <a name="use-temp-tables-to-reference-data-across-languages"></a>Verwenden von temporären Tabellen zum Verweisen auf Daten in verschiedenen Sprachen

Sie können nicht direkt auf Daten oder Variablen in verschiedenen Sprachen einem Synapse Studio-Notebook verweisen. In Spark kann auf eine temporäre Tabelle in verschiedenen Sprachen verwiesen werden. Im Folgenden finden Sie ein Beispiel, wie Sie einen `Scala`-Datenrahmen in `PySpark` und `SparkSQL` mittels einer temporären Spark-Tabelle als Problemumgehung lesen können.

1. Lesen Sie in Zelle 1 einen Datenrahmen von einem SQL-Poolconnector mithilfe von Scala, und erstellen Sie eine temporäre Tabelle.

   ```scala
   %%scala
   val scalaDataFrame = spark.read.sqlanalytics("mySQLPoolDatabase.dbo.mySQLPoolTable")
   scalaDataFrame.createOrReplaceTempView( "mydataframetable" )
   ```

2. In Zelle 2 fragen Sie die Daten mithilfe von Spark SQL ab.
   
   ```sql
   %%sql
   SELECT * FROM mydataframetable
   ```

3. In Zelle 3 verwenden Sie die Daten in PySpark.

   ```python
   %%pyspark
   myNewPythonDataFrame = spark.sql("SELECT * FROM mydataframetable")
   ```

### <a name="ide-style-intellisense"></a>IDE-artiges IntelliSense

Azure Synapse Studio-Notebooks sind in den Monaco-Editor integriert, um den Zellen-Editor mit IDE-artigem IntelliSense auszustatten. Syntaxhervorhebung, Fehlermarkierungen und automatische Codevervollständigungen helfen Ihnen dabei, Code zu schreiben und Probleme schneller zu erkennen.

Die IntelliSense-Funktionen befinden sich in unterschiedlichen Stadien der Entwicklung für verschiedene Sprachen. In der folgenden Tabelle können Sie sehen, was unterstützt wird.

|Languages| Syntaxhervorhebung | Syntaxfehlermarkierungen  | Codevervollständigung für Syntax | Codevervollständigung für Variablen| Codevervollständigung für Systemfunktionen| Codevervollständigung für Benutzerfunktionen| Intelligenter Einzug | Codefaltung|
|--|--|--|--|--|--|--|--|--|
|PySpark (Python)|Ja|Ja|Ja|Ja|Ja|Ja|Ja|Ja|
|Spark (Scala)|Ja|Ja|Ja|Ja|-|-|-|Ja|
|SparkSQL|Ja|Ja|-|-|-|-|-|-|
|.NET für Spark (C#)|Ja|-|-|-|-|-|-|-|

### <a name="format-text-cell-with-toolbar-buttons"></a>Textzelle mit Symbolleisten-Schaltflächen formatieren

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Sie können die Formatschaltflächen auf der Textzellen-Symbolleiste verwenden, um allgemeine Markdown-Aktionen durchzuführen. Dies umfasst das Formatieren von Text als fett oder kursiv, das Einfügen von Codeausschnitten, das Einfügen einer unsortierten Liste, das Einfügen einer sortierten Liste sowie das Einfügen von Bildern aus URLs.

  ![Synapse-Text-Zelle-Symbolleiste](./media/apache-spark-development-using-notebooks/synapse-text-cell-toolbar.png)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Die Symbolleiste mit den Formatschaltflächen ist für das Notebook der Vorschau noch nicht verfügbar. 

---

### <a name="undo-cell-operations"></a>Rückgängigmachen von Zellenvorgängen

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Wählen Sie die Schaltfläche **Rückgängig**, oder drücken Sie **STRG+Z**, um den letzten Zellenvorgang aufzuheben. Sie können jetzt die bis zu letzten 20 zurückliegenden Zellenaktionen rückgängig machen. 

   ![Synapse-Rückgängig-Zellen](./media/apache-spark-development-using-notebooks/synapse-undo-cells.png)
# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Das Rückgängigmachen des Zellenvorgangs ist für das Notebook der Vorschau noch nicht verfügbar. 

---

### <a name="move-a-cell"></a>Verschieben einer Zelle

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Wählen Sie die Auslassungspunkte (...) aus, um ganz rechts auf das Menü mit zusätzlichen Zellenaktionen zuzugreifen. Wählen Sie dann **Zelle nach oben verschieben** oder **Zelle nach unten verschieben** aus, um die aktuelle Zelle zu verschieben. 

Sie können auch [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode) verwenden. Drücken Sie **STRG+ALT+↑**, um die aktuelle Zelle nach oben zu verschieben. Drücken Sie **STRG+ALT+↓**, um die aktuelle Zelle nach unten zu verschieben.

   ![eine-Zelle-verschieben](./media/apache-spark-development-using-notebooks/synapse-move-cells.png)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Klicken Sie auf die linke Seite einer Zelle, und ziehen Sie sie an die gewünschte Position. 
    ![Synapse-Zellen-Verschieben](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-drag-drop-cell.gif)

---

### <a name="delete-a-cell"></a>Löschen einer Zelle

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Um eine Zelle zu löschen, wählen Sie die Auslassungspunkte (...) aus, um ganz rechts auf das Menü mit zusätzlichen Zellenaktionen zuzugreifen, und wählen Sie dann **Zelle löschen** aus. 

Sie können auch [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode) verwenden. Drücken Sie **D,D**, um die aktuelle Zelle zu löschen.
  
   ![eine-Zelle-löschen](./media/apache-spark-development-using-notebooks/synapse-delete-cell.png)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Wählen Sie zum Löschen einer Zelle die Schaltfläche „Löschen“ auf der rechten Seite der Zelle. 

Sie können auch [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode) verwenden. Drücken Sie **UMSCHALT+D**, um die aktuelle Zelle zu löschen. 

   ![azure-notebook-delete-a-cell](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-delete-cell.png)

---

### <a name="collapse-a-cell-input"></a>Reduzieren einer Zelleneingabe

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Wählen Sie am unteren Rand der aktuellen Zelle die Pfeilschaltfläche aus, um sie zu reduzieren. Um sie zu erweitern, wählen Sie die Pfeilschaltfläche aus, während die Zelle reduziert ist.

   ![Zelleneingabe-reduzieren](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-input.gif)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Wählen Sie die Auslassungspunkte für **Weitere Befehle** (...) auf der Zellensymbolleiste und dann **Eingabe** aus, um die Eingabe der aktuellen Zelle zu reduzieren. Um sie zu erweitern, wählen Sie **Eingabe ausgeblendet** aus, während die Zelle reduziert ist.

   ![azure-notebook-collapse-cell-input](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-input.gif)

---

### <a name="collapse-a-cell-output"></a>Reduzieren einer Zellenausgabe

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Wählen Sie links oben in der aktuellen Zellenausgabe die Schaltfläche **Ausgabe reduzieren** aus, um sie zu reduzieren. Um sie zu erweitern, wählen Sie **Zellenausgabe anzeigen** aus, während die Zellenausgabe reduziert ist.

   ![Zellenausgabe-reduzieren](./media/apache-spark-development-using-notebooks/synapse-collapse-cell-output.gif)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Wählen Sie die Auslassungspunkte für **Weitere Befehle** (...) auf der Zellensymbolleiste und dann **Ausgabe** aus, um die Ausgabe der aktuellen Zelle zu reduzieren. Um sie zu erweitern, wählen Sie dieselbe Schaltfläche aus, während die Ausgabe der Zelle ausgeblendet ist.

   ![azure-notebook-collapse-cell-output](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-collapse-cell-output.gif)


---

## <a name="run-notebooks"></a>Ausführen von Notebooks

Sie können die Codezellen in Ihrem Notebook einzeln oder alle gleichzeitig ausführen. Status und Fortschritt jeder Zelle werden im Notebook dargestellt.

### <a name="run-a-cell"></a>Ausführen einer Zelle

Es gibt mehrere Methoden, um den Code in einer Zelle auszuführen.

1. Zeigen Sie mit dem Mauszeiger auf die Zelle, die Sie ausführen möchten, und wählen Sie die Schaltfläche **Zelle ausführen** aus, oder drücken Sie **STRG+EINGABE**.

   ![Zelle-1-ausführen](./media/apache-spark-development-using-notebooks/synapse-run-cell.png)
  
2. Verwenden Sie [Tastenkombinationen im Befehlsmodus](#shortcut-keys-under-command-mode). Drücken Sie **UMSCHALT+EINGABE**, um die aktuelle Zelle auszuführen und die Zelle darunter auszuwählen. Drücken Sie **ALT+EINGABE**, um die aktuelle Zelle auszuführen und darunter eine neue Zelle einzufügen.

---

### <a name="run-all-cells"></a>Ausführen aller Zellen
Wählen Sie die Schaltfläche **Alle ausführen** aus, um alle Zellen im aktuellen Notebook nacheinander auszuführen.

   ![alle-Zellen-ausführen](./media/apache-spark-development-using-notebooks/synapse-run-all.png)


# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

### <a name="run-all-cells-above-or-below"></a>Ausführen aller darüber- oder darunterliegenden Zellen

Um ganz rechts auf das Menü mit zusätzlichen Zellenaktionen zuzugreifen, wählen Sie die Auslassungspunkte ( **...** ) aus. Wählen Sie dann **Zellen oberhalb ausführen** aus, um alle Zellen oberhalb der aktuellen nacheinander auszuführen. Wählen Sie **Zellen unterhalb ausführen** aus, um alle Zellen unterhalb der aktuellen nacheinander auszuführen.

   ![Zellen-oberhalb-oder-unterhalb-ausführen](./media/apache-spark-development-using-notebooks/synapse-run-cells-above-or-below.png)


### <a name="cancel-all-running-cells"></a>Abbrechen aller ausgeführten Zellen
Wählen Sie die Schaltfläche **Alle abbrechen** aus, um die ausgeführten oder in der Warteschlange wartenden Zellen abzubrechen. 
   ![cancel-all-cells](./media/apache-spark-development-using-notebooks/synapse-cancel-all.png) 

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Das Abbrechen aller aktiven Zellen ist für das Notebook der Vorschau noch nicht verfügbar. 

---



### <a name="reference-notebook"></a>Referenznotebook

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Wird nicht unterstützt.

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Sie können den Magic-Befehl ```%run <notebook path>``` verwenden, um im Kontext des aktuellen Notebooks auf ein anderes Notebook zu verweisen. Alle im Referenznotebook definierten Variablen sind im aktuellen Notebook verfügbar. Der Magic-Befehl ```%run``` unterstützt geschachtelte Aufrufe, aber keine rekursiven Aufrufe. Sie erhalten eine Ausnahme, wenn die Anweisungstiefe größer als fünf ist. Der Befehl ```%run``` unterstützt derzeit nur die Übergabe eines Notebookpfads als Parameter. 

---


### <a name="cell-status-indicator"></a>Zellenstatusindikator

Unterhalb der Zelle wird ein schrittweiser Zellenausführungsstatus angezeigt, damit Sie den aktuellen Fortschritt verfolgen können. Nachdem die Ausführung der Zelle abgeschlossen wurde, wird eine Ausführungszusammenfassung mit Gesamtdauer und Endzeit angezeigt, die zur späteren Bezugnahme dort auch aufbewahrt wird.

![Zellenstatus](./media/apache-spark-development-using-notebooks/synapse-cell-status.png)

### <a name="spark-progress-indicator"></a>Spark-Statusanzeige

Ein Azure Synapse Studio-Notebook ist vollständig Spark-basiert. Codezellen werden im serverlosen Apache Spark-Pool remote ausgeführt. Eine Spark-Auftragsstatusanzeige wird mit einem Statusbalken in Echtzeit angezeigt, um Ihnen den Status der Auftragsausführung zu verdeutlichen.
Anhand der Anzahl der Aufgaben pro Auftrag oder Phase können Sie die parallele Ebene Ihres Spark-Auftrags identifizieren. Sie können außerdem die Spark-Benutzeroberfläche eines spezifischen Auftrags (oder einer Phase) erweitern, indem Sie den Link im Auftragsnamen (oder Phasennamen) auswählen.


![Spark-Statusanzeige](./media/apache-spark-development-using-notebooks/synapse-spark-progress-indicator.png)

### <a name="spark-session-config"></a>Spark-Sitzungskonfiguration

Sie können in **Sitzung konfigurieren** die Timeoutdauer sowie die Anzahl und Größe der Executors angeben, die für die aktuelle Spark-Sitzung gelten sollen. Sie müssen die Spark-Sitzung neu starten, damit die Konfigurationsänderungen wirksam werden. Alle zwischengespeicherten Notebook-Variablen werden gelöscht.

[![session-management](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png)](./media/apache-spark-development-using-notebooks/synapse-azure-notebook-spark-session-management.png#lightbox)

#### <a name="spark-session-config-magic-command"></a>Magic-Befehl config für Spark-Sitzung
Sie können auch Spark-Sitzungseinstellungen über einen **%%configure**-Magic-Befehl angeben. Die Spark-Sitzung muss neu gestartet werden, damit die Einstellungen wirksam werden. Wir empfehlen Ihnen, **%%configure** am Anfang Ihres Notebooks auszuführen. Hier ist ein Beispiel. Die vollständige Liste der gültigen Parameter finden Sie unter https://github.com/cloudera/livy#request-body. 

```
%%configure -f
{
    to config the session.
    "driverMemory":"2g",
    "driverCores":3,
    "executorMemory":"2g",
    "executorCores":2,
    "jars":["myjar1.jar","myjar.jar"],
    "conf":{
        "spark.driver.maxResultSize":"10g"
    }
}
```


## <a name="bring-data-to-a-notebook"></a>Einfügen von Daten in ein Notebook

Sie können Daten aus Azure Blob Storage, Azure Data Lake Store Gen 2 und dem SQL-Pool laden, wie in den folgenden Codebeispielen gezeigt.

### <a name="read-a-csv-from-azure-data-lake-store-gen2-as-a-spark-dataframe"></a>Lesen einer CSV-Datei aus Azure Data Lake Store Gen2 als Spark-Datenrahmen

```python
from pyspark.sql import SparkSession
from pyspark.sql.types import *
account_name = "Your account name"
container_name = "Your container name"
relative_path = "Your path"
adls_path = 'abfss://%s@%s.dfs.core.windows.net/%s' % (container_name, account_name, relative_path)

df1 = spark.read.option('header', 'true') \
                .option('delimiter', ',') \
                .csv(adls_path + '/Testfile.csv')

```

#### <a name="read-a-csv-from-azure-blob-storage-as-a-spark-dataframe"></a>Lesen einer CSV-Datei aus Azure Blob Storage als Spark-Datenrahmen

```python

from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)

df = spark.read.option("header", "true") \
            .option("delimiter","|") \
            .schema(schema) \
            .csv(wasbs_path)
```

### <a name="read-data-from-the-primary-storage-account"></a>Lesen von Daten aus einem primären Speicherkonto

Sie können auf Daten im primären Speicherkonto direkt zugreifen. Es besteht keine Notwendigkeit, die geheimen Schlüssel bereitzustellen. Klicken Sie im Daten-Explorer mit der rechten Maustaste auf eine Datei, und wählen Sie **Neues Notebook** aus, um ein neues Notebook mit einem automatisch generierten Datenextraktor anzuzeigen.

![Daten-in-Zelle](./media/apache-spark-development-using-notebooks/synapse-data-to-cell.png)

## <a name="save-notebooks"></a>Speichern von Notebooks

Sie können ein einzelnes Notebook oder alle Notebooks in Ihrem Arbeitsbereich speichern.

1. Um an einem einzelnen Notebook vorgenommene Änderungen zu speichern, wählen Sie auf der Notebook-Befehlsleiste die Schaltfläche **Veröffentlichen** aus.

   ![Notebook-veröffentlichen](./media/apache-spark-development-using-notebooks/synapse-publish-notebook.png)

2. Um alle Notebooks in Ihrem Arbeitsbereich zu speichern, wählen Sie die Schaltfläche **Alle veröffentlichen** auf der Befehlsleiste des Arbeitsbereichs aus. 

   ![alle-veröffentlichen](./media/apache-spark-development-using-notebooks/synapse-publish-all.png)

In den Notebook-Eigenschaften können Sie konfigurieren, ob die Zellenausgabe beim Speichern eingeschlossen werden soll.

   ![Notebook-Eigenschaften](./media/apache-spark-development-using-notebooks/synapse-notebook-properties.png)

## <a name="magic-commands"></a>Magic-Befehle
Sie können vertraute Jupyter-Magic-Befehle in Azure Synapse Studio-Notebooks verwenden. In der folgenden Liste finden Sie die derzeit verfügbaren Magic-Befehle. [Teilen Sie uns Ihre Anwendungsfälle auf GitHub mit](https://github.com/MicrosoftDocs/azure-docs/issues/new), damit wir weitere Magic-Befehle erstellen können, um Ihre Anforderungen zu erfüllen.

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Verfügbare Zeilen-Magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit)

Verfügbare Zeilen-Magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages),[%%configure](#spark-session-config-magic-command)



# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Verfügbare Zeilen-Magics: [%lsmagic](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-lsmagic), [%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%history](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-history), [%run](#reference-notebook), [%load](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-load)

Verfügbare Zeilen-Magics: [%%time](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-time), [%%timeit](https://ipython.readthedocs.io/en/stable/interactive/magics.html#magic-timeit), [%%capture](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-capture), [%%writefile](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-writefile), [%%sql](#use-multiple-languages), [%%pyspark](#use-multiple-languages), [%%spark](#use-multiple-languages), [%%csharp](#use-multiple-languages), [%%html](https://ipython.readthedocs.io/en/stable/interactive/magics.html#cellmagic-html), [%%configure](#spark-session-config-magic-command)

--- 

## <a name="integrate-a-notebook"></a>Integrieren eines Notebooks

### <a name="add-a-notebook-to-a-pipeline"></a>Hinzufügen eines Notebooks zu einer Pipeline

Wählen Sie in der oberen rechten Ecke **Zur Pipeline hinzufügen** aus, um ein Notebook zu einer vorhandenen Pipeline hinzuzufügen oder eine neue Pipeline zu erstellen.

![Hinzufügen eines Notebooks zur Pipeline](./media/apache-spark-development-using-notebooks/add-to-pipeline.png)

### <a name="designate-a-parameters-cell"></a>Festlegen einer Parameterzelle

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Klicken Sie zum Parametrisieren Ihres Notebooks auf die Auslassungspunkte (...), um auf das Menü für zusätzliche Zellenaktionen ganz rechts zuzugreifen. Klicken Sie dann auf **Parameterzelle umschalten**, um die Zelle als Parameterzelle festzulegen.

![toggle-parameter](./media/apache-spark-development-using-notebooks/toggle-parameter-cell.png)

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

Um Ihr Notebook zu parametrisieren, wählen Sie die Auslassungspunkte (...) aus, um auf der Zellensymbolleiste auf **Weitere Befehle** zuzugreifen. Klicken Sie dann auf **Parameterzelle umschalten**, um die Zelle als Parameterzelle festzulegen.

![azure-notebook-toggle-parameter](./media/apache-spark-development-using-notebooks/azure-notebook-toggle-parameter-cell.png)

---

Azure Data Factory sucht nach der Parameterzelle und behandelt diese Zelle als Standard für die Parameter, die zur Ausführungszeit übermittelt werden. Die Ausführungs-Engine fügt eine neue Zelle mit Eingabeparametern unter der Parameterzelle hinzu, um die Standardwerte zu überschreiben. Wenn keine Parameterzelle angegeben ist, wird die Zelle ganz oben im Notebook eingefügt.


### <a name="assign-parameters-values-from-a-pipeline"></a>Zuweisen von Parameterwerten über eine Pipeline

Sobald Sie ein Notebook mit Parametern erstellt haben, können Sie dieses über eine Pipeline mit der Azure Synapse Analytics-Aktivität ausführen. Nachdem Sie die Aktivität zu Ihrem Pipelinecanvas hinzugefügt haben, können Sie die Parameterwerte auf der Registerkarte **Einstellungen** im Abschnitt **Basisparameter** festlegen. 

![Zuweisen eines Parameters](./media/apache-spark-development-using-notebooks/assign-parameter.png)

Beim Zuweisen von Parameterwerten können Sie die [Pipelineausdruckssprache](../../data-factory/control-flow-expression-language-functions.md) oder [Systemvariablen](../../data-factory/control-flow-system-variables.md) verwenden.



## <a name="shortcut-keys"></a>Tastenkombinationen

Ähnlich wie Jupyter-Notebooks verfügen Azure Synapse Studio-Notebooks über eine modale Benutzeroberfläche. Mit der Tastatur werden unterschiedliche Aktionen ausgeführt, je nachdem, in welchem Modus sich die Notebook-Zelle befindet. Synapse Studio-Notebooks unterstützen die folgenden zwei Modi für eine bestimmte Codezelle: Befehlsmodus und Bearbeitungsmodus.

1. Eine Zelle befindet sich im Befehlsmodus, wenn Sie kein Textcursor zur Eingabe auffordert. Wenn sich eine Zelle im Befehlsmodus befindet, können Sie das Notebook als Ganzes bearbeiten, aber keine Eingaben in einzelne Zellen vornehmen. Sie wechseln in den Befehlsmodus, indem Sie `ESC` drücken oder mit der Maus außerhalb des Editor-Bereichs einer Zelle klicken.

   ![Befehlsmodus](./media/apache-spark-development-using-notebooks/synapse-command-mode-2.png)

2. Der Bearbeitungsmodus wird durch einen Textcursor angezeigt, der Sie zur Eingabe im Editor-Bereich auffordert. Wenn sich eine Zelle im Bearbeitungsmodus befindet, können Sie etwas in die Zelle eingeben. Sie wechseln in den Bearbeitungsmodus, indem Sie `Enter` drücken oder mit der Maus auf den Editor-Bereich einer Zelle klicken.
   
   ![Bearbeitungsmodus](./media/apache-spark-development-using-notebooks/synapse-edit-mode-2.png)

### <a name="shortcut-keys-under-command-mode"></a>Tastenkombinationen im Befehlsmodus

# <a name="classical-notebook"></a>[Klassisches Notebook](#tab/classical)

Mithilfe der folgenden Tastenkombinationen können Sie in Azure Synapse-Notebooks leichter navigieren und Code ausführen.

| Aktion |Tastenkombinationen für Synapse Studio-Notebooks  |
|--|--|
|Aktuelle Zelle ausführen und die darunter auswählen | UMSCHALT+EINGABE |
|Aktuelle Zelle ausführen und darunter einfügen | ALT+EINGABE |
|Zelle darüber auswählen| Nach oben |
|Zelle darunter auswählen| Nach unten |
|Zelle oberhalb einfügen| Ein |
|Zelle unterhalb einfügen| B |
|Ausgewählte Zellen nach oben erweitern| UMSCHALT+NACH-OBEN |
|Ausgewählte Zellen nach unten erweitern| UMSCHALT+NACH-UNTEN|
|Zelle nach oben verschieben| STRG+ALT+↑ |
|Zelle nach unten verschieben| STRG+ALT+↓ |
|Ausgewählte Zellen löschen| D, D |
|In den Bearbeitungsmodus wechseln| EINGABETASTE |

# <a name="preview-notebook"></a>[Notebook der Vorschau](#tab/preview)

| Aktion |Tastenkombinationen für Synapse Studio-Notebooks  |
|--|--|
|Aktuelle Zelle ausführen und die darunter auswählen | UMSCHALT+EINGABE |
|Aktuelle Zelle ausführen und darunter einfügen | ALT+EINGABE |
|Aktuelle Zelle ausführen| STRG+EINGABE |
|Zelle darüber auswählen| Nach oben |
|Zelle darunter auswählen| Nach unten |
|Vorherige Zelle auswählen| K |
|Nächste Zelle auswählen| J |
|Zelle oberhalb einfügen| Ein |
|Zelle unterhalb einfügen| B |
|Ausgewählte Zellen löschen| UMSCHALT+D |
|In den Bearbeitungsmodus wechseln| EINGABETASTE |

---

### <a name="shortcut-keys-under-edit-mode"></a>Tastenkombinationen im Bearbeitungsmodus


Mithilfe der folgenden Tastenkombinationen können Sie in Azure Synapse-Notebooks leichter navigieren und Code ausführen, während sie sich im Bearbeitungsmodus befinden.

| Aktion |Tastenkombinationen für Synapse Studio-Notebooks  |
|--|--|
|Cursor nach oben verschieben | Nach oben |
|Cursor nach unten verschieben|Nach unten|
|Rückgängig|STRG+Z|
|Wiederholen|STRG+Y|
|Auskommentieren/Auskommentierung aufheben|STRG+/|
|Wort davor löschen|STRG+RÜCKTASTE|
|Wort danach löschen|STRG+DELETE|
|Zum Anfang der Zelle wechseln|STRG+POS1|
|Zum Ende der Zelle wechseln |STRG+ENDE|
|Ein Wort nach links wechseln|STRG+NACH-LINKS|
|Ein Wort nach rechts wechseln|STRG+NACH-RECHTS|
|Alle auswählen|STRG+A|
|Einziehen| STRG+]|
|Einzug entfernen|STRG+[|
|In den Befehlsmodus wechseln| Esc |

---

## <a name="next-steps"></a>Nächste Schritte
- [Beispiele für Synapse-Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Schnellstart: Erstellen eines Apache Spark-Pools in Azure Synapse Analytics mithilfe von Webtools](../quickstart-apache-spark-notebook.md)
- [Was ist Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [Verwenden von .NET für Apache Spark mit Azure Synapse Analytics](spark-dotnet.md)
- [Dokumentation zu .NET für Apache Spark](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
