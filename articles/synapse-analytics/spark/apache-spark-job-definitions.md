---
title: 'Tutorial: Erstellen einer Apache Spark-Auftragsdefinition in Synapse Studio'
description: In diesem Tutorial erfahren Sie, wie Sie Spark-Auftragsdefinitionen mithilfe von Azure Synapse Analytics erstellen und an einen Pool für Apache Spark für Azure Synapse Analytics übermitteln.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: b8c7792a09dd86e7d4ac043c572f69fc47ee6e63
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93307176"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Tutorial: Erstellen einer Apache Spark-Auftragsdefinition in Synapse Studio

In diesem Tutorial erfahren Sie, wie Sie Apache Spark-Auftragsdefinitionen mithilfe von Azure Synapse Studio erstellen und anschließend an einen serverlosen Apache Spark-Pool übermitteln.

Dieses Tutorial enthält die folgenden Aufgaben:
> [!div class="checklist"]
>
> - Erstellen einer Apache Spark-Auftragsdefinition für PySpark (Python)
> - Erstellen einer Apache Spark-Auftragsdefinition für Spark (Scala)
> - Erstellen einer Apache Spark-Auftragsdefinition für .NET Spark (C#/F#)
> - Übermitteln einer Apache Spark-Auftragsdefinition als Batchauftrag
> - Hinzufügen einer Apache Spark-Auftragsdefinition in einer Pipeline

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Anforderungen erfüllt sind bzw. dass Folgendes vorhanden ist:

* Ein Azure Synapse Analytics-Arbeitsbereich. Entsprechende Anweisungen finden Sie unter [Erstellen eines Arbeitsbereichs](../../machine-learning/how-to-manage-workspace.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#create-a-workspace).
* Ein serverloser Apache Spark-Pool
* Ein ADLS Gen2-Speicherkonto. Sie müssen der **Besitzer der Speicherblobdaten** des ADLS Gen2-Dateisystems sein, das Sie verwenden möchten. Andernfalls müssen Sie die Berechtigung manuell hinzufügen.
* Wenn Sie nicht den Standardspeicher des Arbeitsbereichs verwenden möchten, verknüpfen Sie das erforderliche ADLS Gen2-Speicherkonto in Synapse Studio. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Erstellen einer Apache Spark-Auftragsdefinition für PySpark (Python)

In diesem Abschnitt erstellen Sie eine Apache Spark-Auftragsdefinition für PySpark (Python).

1. Öffnen Sie [Azure Synapse Studio](https://web.azuresynapse.net/).

2. Sie können zu [Beispieldateien zum Erstellen von Apache Spark-Auftragsdefinitionen](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) navigieren, um **Beispieldateien für python.zip** herunterzuladen. Entpacken Sie anschließend das komprimierte Paket, und extrahieren Sie die Dateien **wordcount.py** und **shakespeare.txt**. 

     ![Beispieldateien](./media/apache-spark-job-definitions/sample-files.png)

3. Wählen Sie **Daten** -> **Verknüpft** -> **Azure Data Lake Storage Gen2** aus, und laden Sie **wordcount.py** und **shakespeare.txt** in das ADLS Gen2-Dateisystem hoch. 

     ![Python-Datei hochladen](./media/apache-spark-job-definitions/upload-python-file.png)

4. Wählen Sie den Hub **Entwickeln**, das Plussymbol (+) und anschließend **Spark-Auftragsdefinition** aus, um eine neue Spark-Auftragsdefinition zu erstellen. 

     ![Neue Definition für Python erstellen](./media/apache-spark-job-definitions/create-new-definition.png)

5. Wählen Sie in der Dropdownliste „Sprache“ im Hauptfenster der Apache Spark-Auftragsdefinition **PySpark (Python)** aus.

     ![Auswählen von Python](./media/apache-spark-job-definitions/select-python.png)

6. Tragen Sie Informationen für die Apache Spark-Auftragsdefinition ein. 

     |  Eigenschaft   | Beschreibung   |  
     | ----- | ----- |  
     |Auftragsdefinitionsname| Geben Sie einen Namen für Ihre Apache Spark-Auftragsdefinition ein. Dieser Name kann bis zur Veröffentlichung jederzeit aktualisiert werden. <br> Beispiel: `job definition sample`|
     |„Main definition file“ (Hauptdefinitionsdatei)| Die für den Auftrag verwendete Hauptdatei. Wählen Sie eine PY-Datei aus Ihrem Speicher aus. Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen. <br> Beispiel: `abfss://…/path/to/wordcount.py`|
     |Befehlszeilenargumente| Optionale Argumente für den Auftrag. <br> Beispiel: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Hinweis: Zwei Argumente für die Beispielauftragsdefinition werden durch ein Leerzeichen getrennt.*|
     |„Reference files“ (Referenzdateien)| Zusätzliche Dateien, die zu Referenzzwecken in der Hauptdefinitionsdatei verwendet werden. Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen. |
     |Spark-Pool| Der Auftrag wird an den ausgewählten Apache Spark-Pool übermittelt.|
     |Spark-Version| Die vom Apache Spark-Pool ausgeführte Version von Apache Spark.|
     |Ausführer| Die gewünschte Anzahl von Executors im angegebenen Apache Spark-Pool für den Auftrag.|
     |„Executor size“ (Executorgröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Executors im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|  
     |„Driver size“ (Treibergröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Treiber im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|

     ![Festlegen des Werts der Spark-Auftragsdefinition für Python](./media/apache-spark-job-definitions/create-py-definition.png)

7. Wählen Sie **Veröffentlichen** aus, um die Apache Spark-Auftragsdefinition zu speichern.

     ![Py-Definition veröffentlichen](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Erstellen einer Apache Spark-Auftragsdefinition für Apache Spark (Scala)

In diesem Abschnitt erstellen Sie eine Apache Spark-Auftragsdefinition für Apache Spark (Scala).

 1. Öffnen Sie [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Sie können zu [Beispieldateien zum Erstellen von Apache Spark-Auftragsdefinitionen](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) navigieren, um **Beispieldateien für scala.zip** herunterzuladen. Entpacken Sie anschließend das komprimierte Paket, und extrahieren Sie die Dateien **wordcount.jar** und **shakespeare.txt**. 
 
     ![Beispieldateien für Scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Wählen Sie **Daten** -> **Verknüpft** -> **Azure Data Lake Storage Gen2** aus, und laden Sie **wordcount.jar** und **shakespeare.txt** in das ADLS Gen2-Dateisystem hoch.
 
     ![Scala-Struktur vorbereiten](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Wählen Sie den Hub **Entwickeln**, das Plussymbol (+) und anschließend **Spark-Auftragsdefinition** aus, um eine neue Spark-Auftragsdefinition zu erstellen. (Die Beispielabbildung ist identisch mit der in Schritt 4 unter **Erstellen einer Apache Spark-Auftragsdefinition für PySpark (Python)** .)

 5. Wählen Sie in der Dropdownliste „Sprache“ im Hauptfenster der Apache Spark-Auftragsdefinition **Spark (Scala)** aus.

     ![Auswählen von Scala](./media/apache-spark-job-definitions/select-scala.png)

 6. Tragen Sie Informationen für die Apache Spark-Auftragsdefinition ein. Sie können die Beispielinformationen kopieren.

     |  Eigenschaft   | Beschreibung   |  
     | ----- | ----- |  
     |Auftragsdefinitionsname| Geben Sie einen Namen für Ihre Apache Spark-Auftragsdefinition ein. Dieser Name kann bis zur Veröffentlichung jederzeit aktualisiert werden. <br> Beispiel: `scala`|
     |„Main definition file“ (Hauptdefinitionsdatei)| Die für den Auftrag verwendete Hauptdatei. Wählen Sie eine JAR-Datei aus Ihrem Speicher aus. Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen. <br> Beispiel: `abfss://…/path/to/wordcount.jar`|
     |„Main class name“ (Name der Hauptklasse)| Der vollqualifizierte Bezeichner oder die Hauptklasse in der Hauptdefinitionsdatei. <br> Beispiel: `WordCount`|
     |Befehlszeilenargumente| Optionale Argumente für den Auftrag. <br> Beispiel: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Hinweis: Zwei Argumente für die Beispielauftragsdefinition werden durch ein Leerzeichen getrennt.* |
     |„Reference files“ (Referenzdateien)| Zusätzliche Dateien, die zu Referenzzwecken in der Hauptdefinitionsdatei verwendet werden. Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen.|
     |Spark-Pool| Der Auftrag wird an den ausgewählten Apache Spark-Pool übermittelt.|
     |Spark-Version| Die vom Apache Spark-Pool ausgeführte Version von Apache Spark.|
     |Ausführer| Die gewünschte Anzahl von Executors im angegebenen Apache Spark-Pool für den Auftrag.|  
     |„Executor size“ (Executorgröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Executors im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|
     |„Driver size“ (Treibergröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Treiber im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|

     ![Festlegen des Werts der Spark-Auftragsdefinition für Scala](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Wählen Sie **Veröffentlichen** aus, um die Apache Spark-Auftragsdefinition zu speichern.

      ![Scala-Definition veröffentlichen](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Erstellen einer Apache Spark-Auftragsdefinition für .NET Spark (C#/F#)

In diesem Abschnitt erstellen Sie eine Apache Spark-Auftragsdefinition für .NET Spark (C#/F#).
 1. Öffnen Sie [Azure Synapse Studio](https://web.azuresynapse.net/).

 2. Sie können zu [Beispieldateien zum Erstellen von Apache Spark-Auftragsdefinitionen](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) navigieren, um **Beispieldateien für dotnet.zip** herunterzuladen. Entpacken Sie anschließend das komprimierte Paket, und extrahieren Sie die Dateien **wordcount.zip** und **shakespeare.txt**. 

     ![Beispieldateien für dotnet](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Wählen Sie **Daten** -> **Verknüpft** -> **Azure Data Lake Storage Gen2** aus, und laden Sie **wordcount.zip** und **shakespeare.txt** in das ADLS Gen2-Dateisystem hoch.
 
     ![DotNet-Struktur vorbereiten](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Wählen Sie den Hub **Entwickeln**, das Plussymbol (+) und anschließend **Spark-Auftragsdefinition** aus, um eine neue Spark-Auftragsdefinition zu erstellen. (Die Beispielabbildung ist identisch mit der in Schritt 4 unter **Erstellen einer Apache Spark-Auftragsdefinition für PySpark (Python)** .)

 5. Wählen Sie in der Dropdownliste „Sprache“ im Hauptfenster der Apache Spark-Auftragsdefinition **.NET Spark (C#/F#)** aus.

     ![Auswählen von dotnet](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Tragen Sie Informationen für die Apache Spark-Auftragsdefinition ein. Sie können die Beispielinformationen kopieren.
    
     |  Eigenschaft   | Beschreibung   |  
     | ----- | ----- |  
     |Auftragsdefinitionsname| Geben Sie einen Namen für Ihre Apache Spark-Auftragsdefinition ein. Dieser Name kann bis zur Veröffentlichung jederzeit aktualisiert werden. <br> Beispiel: `dotnet`|
     |„Main definition file“ (Hauptdefinitionsdatei)| Die für den Auftrag verwendete Hauptdatei. Wählen Sie in Ihrem Speicher eine ZIP-Datei aus, die Ihre Anwendung vom Typ „.NET für Apache Spark“ enthält (ausführbare Hauptdatei, DLLs mit benutzerdefinierten Funktionen und andere erforderliche Dateien). Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen. <br> Beispiel: `abfss://…/path/to/wordcount.zip`|
     |„Main executable file“ (Ausführbare Hauptdatei)| Die ausführbare Hauptdatei in der ZIP-Datei der Hauptdefinition. <br> Beispiel: `WordCount`|
     |Befehlszeilenargumente| Optionale Argumente für den Auftrag. <br> Beispiel: `abfss://…/path/to/shakespeare.txt` `abfss://…/path/to/result` <br> *Hinweis: Zwei Argumente für die Beispielauftragsdefinition werden durch ein Leerzeichen getrennt.* |
     |„Reference files“ (Referenzdateien)| Zusätzliche Dateien, die von den Workerknoten zum Ausführen der Anwendung vom Typ „.NET für Apache Spark“ benötigt werden und nicht in der ZIP-Datei der Hauptdefinition enthalten sind (also abhängige JAR-Dateien, zusätzliche DLLs mit benutzerdefinierten Funktionen sowie andere Konfigurationsdateien). Sie können **Datei hochladen** auswählen, um die Datei in ein Speicherkonto hochzuladen.|
     |Spark-Pool| Der Auftrag wird an den ausgewählten Apache Spark-Pool übermittelt.|
     |Spark-Version| Die vom Apache Spark-Pool ausgeführte Version von Apache Spark.|
     |Ausführer| Die gewünschte Anzahl von Executors im angegebenen Apache Spark-Pool für den Auftrag.|  
     |„Executor size“ (Executorgröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Executors im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|
     |„Driver size“ (Treibergröße)| Die Anzahl von Kernen und die Menge an Arbeitsspeicher, die für Treiber im angegebenen Apache Spark-Pool für den Auftrag verwendet werden sollen.|

     ![Festlegen des Werts der Spark-Auftragsdefinition für DotNet](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Wählen Sie **Veröffentlichen** aus, um die Apache Spark-Auftragsdefinition zu speichern.

      ![DotNet-Definition veröffentlichen](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Übermitteln einer Apache Spark-Auftragsdefinition als Batchauftrag

Nachdem Sie eine Apache Spark-Auftragsdefinition erstellt haben, können Sie sie an einen Apache Spark-Pool übermitteln. Stellen Sie sicher, dass Sie der **Besitzer der Speicherblobdaten** des ADLS Gen2-Dateisystems sind, das Sie verwenden möchten. Andernfalls müssen Sie die Berechtigung manuell hinzufügen.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>Szenario 1: Übermitteln einer Apache Spark-Auftragsdefinition
 1. Öffnen Sie durch Auswählen ein Apache Spark-Auftragsdefinitionsfenster.

      ![Öffnen der zu übermittelnden Spark-Auftragsdefinition ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Wählen Sie die Schaltfläche **Übermitteln** aus, um Ihr Projekt an den ausgewählten Apache Spark-Pool zu übermitteln. Sie können die Registerkarte **Spark monitoring URL** (Spark-Überwachungs-URL) auswählen, um die Protokollabfrage der Apache Spark-Anwendung anzuzeigen.

    ![Auswählen der Schaltfläche „Übermitteln“, um die Spark-Auftragsdefinition zu übermitteln](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![Dialogfeld für die Spark-Übermittlung](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>Szenario 2: Anzeigen des Status der Apache Spark-Auftragsausführung

 1. Wählen Sie **Überwachen** und anschließend die Option **Apache Spark-Anwendungen** aus. Navigieren Sie zur übermittelten Apache Spark-Anwendung.

     ![Anzeigen der Spark-Anwendung](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Wählen Sie dann eine Apache Spark-Anwendung aus. Daraufhin wird das Auftragsfenster **SparkJobDefinition** angezeigt. Sie können den Status der Auftragsausführung in diesem Fenster anzeigen.
     
     ![Anzeigen der Protokollabfrage für die Spark-Anwendung](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>Szenario 3: Überprüfen der Ausgabedatei

 1. Wählen Sie **Daten** -> **Verknüpft** -> **Azure Data Lake Storage Gen2** (hozhaobdbj) aus, und öffnen Sie den zuvor erstellten Ordner **result**. Sie können zum Ordner „result“ navigieren und überprüfen, ob die Ausgabe generiert wurde.

     ![Anzeigen der Ausgabedatei](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Hinzufügen einer Apache Spark-Auftragsdefinition in einer Pipeline

In diesem Abschnitt fügen Sie eine Apache Spark-Auftragsdefinition in einer Pipeline hinzu.

 1. Öffnen Sie eine vorhandene Apache Spark-Auftragsdefinition.

 2. Wählen Sie das Symbol rechts oben in der Apache Spark-Auftragsdefinition und anschließend **Vorhandene Pipeline** oder **Neue Pipeline** aus. Weitere Informationen finden Sie auf der Seite „Pipeline“.

     ![Hinzufügen zur Pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Hinzufügen zur Pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie Azure Synapse Studio verwenden, um Power BI-Datasets zu erstellen und Power BI-Daten zu verwalten. Weitere Informationen finden Sie im Artikel [Schnellstart: Verknüpfen eines Power BI-Arbeitsbereichs mit einem Synapse-Arbeitsbereich](../quickstart-power-bi.md). 

