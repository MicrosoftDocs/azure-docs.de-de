---
title: 'ML Studio (Classic): Importieren von Trainingsdaten – Azure'
description: Erfahren Sie, wie Sie Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio (klassisch) importieren. Erfahren Sie, welche Datentypen und Datenformate unterstützt werden.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/01/2019
ms.openlocfilehash: c69ed7b9e2aaa7113637868e1c1329ed2962b931
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302772"
---
# <a name="import-your-training-data-into-azure-machine-learning-studio-classic-from-various-data-sources"></a>Importieren von Trainingsdaten aus verschiedenen Datenquellen in Azure Machine Learning Studio (klassisch)

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Um Ihre eigenen Daten in Machine Learning Studio (klassisch) zum Entwickeln und Trainieren einer Predictive Analytics-Lösung zu verwenden, können Sie Daten aus folgenden Quellen verwenden: 

* **Lokale Datei:** Laden Sie lokale Daten von der Festplatte im Voraus, um damit ein Datasetmodul in Ihrem Arbeitsbereich zu erstellen.
* **Onlinedatenquellen:** Greifen Sie mithilfe des Moduls [Daten importieren][import-data] auf Daten aus einer von mehreren Onlinequellen zu, während Ihr Experiment ausgeführt wird.
* **Machine Learning Studio-Experiment (klassisch):** Verwenden Sie Daten, die als Dataset in Machine Learning Studio (klassisch) gespeichert wurden.
* [**SQL Server-Datenbank**](use-data-from-an-on-premises-sql-server.md): Verwenden Sie Daten aus einer SQL Server-Datenbank, ohne Daten manuell kopieren zu müssen.

> [!NOTE]
> Es gibt eine Reihe von Beispiel-Datasets in Machine Learning Studio (klassisch), die Sie als Trainingsdaten verwenden können. Weitere Informationen finden Sie unter [Verwenden von Beispieldatasets in Azure Machine Learning Studio (klassisch)](use-sample-datasets.md).

## <a name="prepare-data"></a>Vorbereiten von Daten

Machine Learning Studio (klassisch) ist für die Nutzung von rechteckigen oder tabellarischen Daten vorgesehen, z. B. Textdaten, bei denen es sich um mit Trennzeichen versehene oder strukturierte Daten aus einer Datenbank handelt. Unter Umständen können nicht rechteckige Daten verwendet werden.

Ihre Daten sollten relativ problemfrei sein, bevor Sie sie in Studio (klassisch) importieren. Beispielsweise sollten Sie vermeiden, dass Zeichenfolgen nicht in Anführungszeichen eingeschlossen sind.

In Studio (klassisch) sind jedoch Module verfügbar, mit denen Sie nach dem Importieren Ihrer Daten bestimmte Änderungen an den Daten im Experiment vornehmen können. Abhängig vom verwendeten Machine Learning-Algorithmen müssen Sie möglicherweise entscheiden, wie Sie mit Datenstrukturproblemen wie fehlenden Werten und geringen Datenmengen umgehen möchten. Es gibt Module, die dabei helfen können. Suchen Sie im Abschnitt **Data Transformation** der Modulpalette nach Modulen, die diese Funktionen ausführen.

Zu jedem Zeitpunkt des Experiments können Sie die von einem Modul erstellten Daten anzeigen oder herunterladen, indem Sie auf den Ausgabeport klicken. Je nach Modul können verschiedene Downloadoptionen verfügbar sein, oder Sie können die Daten in Ihrem Webbrowser in Studio (klassisch) anzeigen.

## <a name="supported-data-formats-and-data-types"></a>Unterstützte Datenformate und Datentypen

Sie können eine Reihe von Datentypen in das Experiment importieren, je nachdem, welchen Mechanismus Sie zum Importieren von Daten verwenden und woher die Daten stammen:

* Nur-Text (.txt)
* Durch Trennzeichen getrennte Werte (CSV) mit einer Kopfzeile (.csv) oder ohne (.nh.csv)
* Durch Tabulator getrennte Werte (TSV) mit einer Kopfzeile (.tsv) oder ohne (.nh.tsv)
* Excel-Datei
* Azure-Tabelle
* Hive-Tabelle
* SQL-Datenbanktabelle
* OData-Werte
* SVMLight-Daten (.svmlight) (Formatinformationen finden Sie in der [SVMLight-Definition](http://svmlight.joachims.org/) (in englischer Sprache))
* ARFF-Daten (Attribute Relation File Format) (.arff) (Formatinformationen finden Sie in der [ARFF-Definition](https://www.cs.waikato.ac.nz/ml/weka/arff.html) (in englischer Sprache))
* ZIP-Datei (.zip)
* R-Objektdatei oder -Arbeitsbereichsdatei (.RData)

Wenn Sie Daten in einem Format wie z.B. ARFF importieren, das Metadaten enthält, verwendet Studio (klassisch) diese Metadaten, um die Kopfzeile und den Datentyp der einzelnen Spalten zu definieren.

Wenn Sie Daten im TSV- oder CSV-Format importieren, das diese Metadaten nicht enthält, leitet Studio (klassisch) den Datentyp für jede Spalte durch Entnahme von Datenstichproben ab. Wenn die Daten zudem keine Spaltenüberschriften aufweisen, legt Studio (klassisch) Standardnamen fest.

Sie können die Überschriften und Datentypen für Spalten mit dem Modul [Edit Metadata][edit-metadata] (Metadaten bearbeiten) explizit angeben oder ändern.

Die folgenden Datentypen werden von Studio (klassisch) erkannt:

* String
* Integer
* Double
* Boolean
* Datetime
* TimeSpan

Von Studio wird ein interner Datentyp namens **Datentabelle** verwendet, um Daten zwischen Modulen zu übergeben. Mit dem Modul [Convert to Dataset][convert-to-dataset] (In Dataset konvertieren) können Sie Daten explizit in das data table-Format konvertieren.

Jedes Modul, das andere Formate als data table akzeptiert, konvertiert die Daten im Hintergrund vor der Übergabe an das nächste Modul in das data table-Format.

Bei Bedarf können Sie das data table-Format mit anderen Konvertierungsmodulen wieder in die Formate CSV, TSV, ARFF oder SVMLight konvertieren.
Suchen Sie im Abschnitt *Data Format Conversions* (Datenformatkonvertierungen) der Modulpalette nach Modulen, von denen diese Funktionen ausgeführt werden.

## <a name="data-capacities"></a>Datenkapazitäten

Module in Machine Learning Studio (klassisch) unterstützen in normalen Anwendungsfällen Datasets bis zu einer Größe von 10 GB an dichten numerischen Daten. Wenn ein Modul mehrere Eingaben akzeptiert, entspricht der Wert von 10 GB der Summe aller Eingabegrößen. Sie können Teile größerer Datasets übernehmen, indem Sie Abfragen aus Hive oder Azure SQL-Datenbank verwenden, oder Sie können die Vorverarbeitung per Lernen nach Anzahl vor dem Importieren der Daten nutzen.  

Die folgenden Typen von Daten können während der Featurenormalisierung in größere Datasets erweitert werden und sind auf weniger als 10 GB beschränkt:

* Platzsparend
* Kategorisch
* Zeichenfolgen
* Binärdaten

Die folgenden Module sind auf Datasets mit einer Größe von unter 10 GB beschränkt:

* Empfohlene Module
* Modul „Synthetic Minority Oversampling Technique (SMOTE)“
* Skriptingmodule: R, Python, SQL
* Module, bei denen die Größe der Ausgabedaten die der Eingabedaten überschreiten kann, z.B. Join oder Feature-Hashing.
* Kreuzvalidierung, Tune Model Hyperparameters, Ordinal Regression und One-vs-All Multiclass, wenn eine sehr große Anzahl von Iterationen durchgeführt wird.

Laden Sie die Daten für Datasets, die größer als einige GB sind, in Azure Storage oder Azure SQL-Datenbank hoch, oder verwenden Sie Azure HDInsight, anstatt die Daten direkt aus einer lokalen Datei hochzuladen.

Informationen zu Bilddaten finden Sie in der Modulreferenz [Import Images](/azure/machine-learning/studio-module-reference/import-images#bkmk_Notes) (Importieren von Bildern).

## <a name="import-from-a-local-file"></a>Importieren aus einer lokalen Datei

Sie können eine Datendatei von der Festplatte zur Verwendung als Trainingsdaten in Studio (klassisch) hochladen. Wenn Sie eine Datei importieren, erstellen Sie ein Datasetmodul, das direkt in Experimenten in Ihrem Arbeitsbereich verwendet werden kann.

Sie können wie folgt Daten von einer lokalen Festplatte importieren:

1. Klicken Sie unten im Fenster von Studio (klassisch) auf **+NEU**.
2. Wählen Sie **DATASET** und **FROM LOCAL FILE** aus.
3. Navigieren Sie im Dialogfeld **Upload a new dataset** (Neues Dataset hochladen) zu der Datei, die Sie hochladen möchten.
4. Geben Sie einen Namen ein, identifizieren Sie den Datentyp, und geben Sie optional eine Beschreibung ein. Eine Beschreibung wird empfohlen: Damit können Sie beliebige Merkmale der Daten erfassen, die Sie bei zukünftiger Nutzung der Daten berücksichtigen möchten.
5. Mit dem Kontrollkästchen **This is the new version of an existing dataset** können Sie ein vorhandenes Dataset mit neuen Daten aktualisieren. Aktivieren Sie dazu dieses Kontrollkästchen, und geben Sie dann den Namen eines vorhandenen Datasets ein.

![Hochladen eines neuen Datasets](./media/import-data/upload-dataset-from-local-file.png)

Die Dauer für das Hochladen hängt von der Größe Ihrer Daten und der Geschwindigkeit der Verbindung mit dem Dienst ab. Wenn Sie wissen, dass das Hochladen der Datei eine lange Zeit dauern kann, können Sie andere Dinge in Studio (klassisch) erledigen, während Sie warten. Wenn Sie den Browser jedoch vor Abschluss des Datenuploads schließen, tritt beim Hochladen ein Fehler auf.

Sobald Ihre Daten hochgeladen wurden, werden sie in einem Datasetmodul gespeichert und stehen für alle Experimente im Arbeitsbereich zur Verfügung.

Wenn Sie ein Experiment bearbeiten, finden Sie die hochgeladenen Datasets in der Modulpalette unter der Liste **Gespeicherte Datasets** in der Liste **Meine Datasets**. Sie können das Dataset in den Experimentbereich ziehen und dort ablegen, wenn Sie es für weitere Analysen und maschinelles Lernen verwenden möchten.

## <a name="import-from-online-data-sources"></a>Importieren von Onlinedatenquellen

Mit dem Modul [Import Data][import-data] kann Ihr Experiment Daten aus verschiedenen Onlinedatenquellen importieren, während es ausgeführt wird.

> [!NOTE]
> Dieser Artikel bietet allgemeine Informationen zum Modul [Import Data][import-data]. Ausführlichere Informationen zu den Datentypen, auf die Sie zugreifen können, zu Formaten, Parametern und Antworten auf häufig gestellte Fragen finden Sie im Modulreferenzthema zum Modul [Import Data][import-data].

Über das Modul [Import Data][import-data] können Sie auf Daten aus einer von mehreren Onlinedatenquellen zugreifen, während Ihr Experiment ausgeführt wird:

* Web-URL über HTTP
* Hadoop über HiveQL
* Azure Blob Storage
* Azure-Tabelle
* Azure SQL-Datenbank. SQL Managed Instance oder SQL Server
* Datenfeedanbieter (derzeit OData)
* Azure Cosmos DB

Da auf diese Trainingsdaten zugegriffen wird, während das Experiment ausgeführt wird, stehen sie nur in diesem Experiment zur Verfügung. Im Vergleich dazu stehen Daten, die in Datasetmodulen gespeichert sind, allen Experimenten in Ihrem Arbeitsbereich zur Verfügung.

Um in Ihrem (klassischen) Studio-Experiment auf Onlinedatenquellen zuzugreifen, fügen Sie dem Experiment das Modul [Import Data][import-data] hinzu. Wählen Sie dann **Launch Import Data Wizard** (Datenimport-Assistenten starten) unter **Eigenschaften** aus, um eine ausführliche Anleitung zum Auswählen und Konfigurieren der Datenquelle zu erhalten. Alternativ können Sie **Datenquelle** unter **Eigenschaften** manuell auswählen und die erforderlichen Parameter für den Datenzugriff angeben.

In der folgenden Tabelle werden die unterstützten Onlinedatenquellen beschrieben. Diese Tabelle enthält auch die unterstützten Dateiformate und die Parameter für den Zugriff auf die Daten.

> [!IMPORTANT]
> Derzeit können die Module [Import Data][import-data] und [Export Data][export-data] Daten nur in Azure-Speicher lesen und schreiben, der mit dem klassischen Bereitstellungsmodell erstellt wurde. Das heißt, dass der neue Azure Blob Storage-Kontotyp, der eine „heiße“ oder „kalte“ Speicherzugriffsebene bietet, noch nicht unterstützt wird.
>
> Im Allgemeinen sollten Azure-Speicherkonten, die Sie ggf. erstellt haben, bevor diese Dienstoption verfügbar war, nicht betroffen sein.
> Wenn Sie ein neues Konto erstellen müssen, wählen Sie **Klassisch** als Bereitstellungsmodell, oder verwenden Sie Resource Manager, und wählen Sie für **Kontoart** die Option **Allgemein** anstelle von **Blobspeicher**.
>
> Weitere Informationen finden Sie unter [Azure Blob Storage: „Heiße“ und „kalte“ Speicherebenen](../../storage/blobs/storage-blob-storage-tiers.md).

### <a name="supported-online-data-sources"></a>Unterstützte Onlinedatenquellen
Das **Import Data**-Modul von Azure Machine Learning Studio (klassisch) unterstützt die folgenden Datenquellen:

| Data source | BESCHREIBUNG | Parameter |
| --- | --- | --- |
| Web URL via HTTP |Liest Daten im CSV-Format (mit Trennzeichen getrennte Werte), im TSV-Format (mit Tabulatoren getrennte Werte), im ARFF-Format (Attribute-Relation File Format) und SVM-light-Format (Support Vector Machines) aus allen Web-URLs, die HTTP verwenden. |<b>URL</b>: Gibt den vollständigen Namen der Datei (einschließlich Website-URL und Dateiname) mit beliebiger Erweiterung an. <br/><br/><b>Datenformat:</b> Gibt eines der unterstützten Datenformate an: CSV, TSV, ARFF oder SVM-light. Wenn die Daten eine Kopfzeile haben, wird sie für das Zuweisen von Spaltennamen verwendet. |
| Hadoop/HDFS |Liest Daten aus verteiltem Speicher in Hadoop. Sie geben die gewünschten Daten an, indem Sie HiveQL, eine SQL-ähnliche Abfragesprache, verwenden. HiveQL kann auch für die Aggregation von Daten und die Datenfilterung verwendet werden, bevor Sie die Daten in Studio (klassisch) hinzufügen. |<b>Hive database query</b>: Gibt die Hive-Abfrage zum Generieren der Daten an.<br/><br/><b>HCatalog server URI</b> : Gibt den Namen Ihres Clusters im Format *&lt;Ihr Clustername&gt;.azurehdinsight.net* an.<br/><br/><b>Hadoop user account name</b>: Gibt den Hadoop-Benutzerkontonamen an, der zum Bereitstellen des Clusters verwendet wird.<br/><br/><b>Hadoop user account password</b> : Gibt die Anmeldeinformationen an, die beim Bereitstellen des Clusters verwendet werden. Weitere Informationen finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).<br/><br/><b>Location of output data</b>: Gibt an, ob die Daten in einem HDFS (Hadoop Distributed File System) oder in Azure gespeichert werden. <br/><ul>Wenn Sie die Ausgabedaten in HDFS speichern, geben Sie den HDFS-Server-URI an. (Achten Sie darauf, den Namen des HDInsight-Clusters ohne das Präfix HTTPS:// zu verwenden). <br/><br/>Wenn Sie Ausgabedaten in Azure speichern, müssen Sie den Namen des Azure-Speicherkontos, den Speicherzugriffsschlüssel und Speichercontainernamen angeben.</ul> |
| SQL database |Liest Daten, die in einer Azure SQL-Datenbank-, SQL Managed Instance- oder einer SQL Server-Datenbank-Instanz gespeichert sind, die in einer Azure-VM ausgeführt wird. |<b>Database server name</b>: Gibt den Namen des Servers an, auf dem die Datenbank ausgeführt wird.<br/><ul>Geben Sie bei Azure SQL-Datenbank den Servernamen ein, der generiert wird. Dieser hat meist das Format *&lt;Generierter_Bezeichner&gt;.database.windows.net*. <br/><br/>Geben Sie Folgendes ein, wenn eine SQL Server-Instanz auf einem virtuellen Azure-Computer gehostet wird: *tcp:&lt;DNS-Name des virtuellen Computers&gt;, 1433*.</ul><br/><b>Database name </b>: Gibt den Namen der Datenbank auf dem Server an. <br/><br/><b>Server user account name</b>: Gibt einen Benutzernamen für ein Konto an, das über Zugriffsberechtigungen für die Datenbank verfügt. <br/><br/><b>Server user account password</b>: Gibt das Kennwort für das Benutzerkonto an.<br/><br/><b>Database query</b>: Geben Sie eine SQL-Anweisung ein, die die Daten beschreibt, die Sie lesen möchten. |
| On-premises SQL database |Liest Daten, die in einer SQL-Datenbank gespeichert sind. |<b>Data gateway</b>: Gibt den Namen des Datenverwaltungsgateways an, das auf einem Computer installiert ist, auf dem es auf Ihre SQL Server-Datenbank zugreifen kann. Informationen zum Einrichten des Gateways finden Sie unter [Durchführen von Analysen mit Azure Machine Learning Studio (Classic) mit einer SQL Server-Datenbank](use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Database server name</b>: Gibt den Namen des Servers an, auf dem die Datenbank ausgeführt wird.<br/><br/><b>Database name </b>: Gibt den Namen der Datenbank auf dem Server an. <br/><br/><b>Server user account name</b>: Gibt einen Benutzernamen für ein Konto an, das über Zugriffsberechtigungen für die Datenbank verfügt. <br/><br/><b>User name and password</b>: Klicken Sie auf <b>Enter values</b>, und geben Sie Ihre Datenbank-Anmeldeinformationen ein. Je nach Ihrer SQL Server-Konfiguration können Sie die integrierte Windows-Authentifizierung oder die SQL Server-Authentifizierung verwenden.<br/><br/><b>Database query</b>: Geben Sie eine SQL-Anweisung ein, die die Daten beschreibt, die Sie lesen möchten. |
| Azure Table |Liest Daten aus dem Tabellenspeicherdienst in Azure Storage.<br/><br/>Wenn große Mengen von Daten selten gelesen werden, verwenden Sie den Azure-Tabellenspeicherdienst. Dieser Dienst bietet eine flexible, nicht relationale (NoSQL), überaus skalierbare, kostengünstige und hochverfügbare Speicherlösung. |Die Optionen im **Import Data**-Modul ändern sich je nachdem, ob Sie auf öffentliche Informationen oder ein privates Speicherkonto zugreifen, das Anmeldeinformationen erfordert. Dies wird anhand der Option <b>Authentication Type</b> bestimmt, die den Wert „PublicOrSAS“ oder „Account“ haben kann, der jeweils über einen eigenen Satz von Parametern verfügen kann. <br/><br/><b>Öffentlicher oder SAS-URI (Shared Access Signature)</b>: Die Parameter lauten wie folgt:<br/><br/><ul><b>Table URI</b>: Gibt die öffentliche URL oder SAS-URL für die Tabelle an.<br/><br/><b>Gibt die Zeilen an, die nach Eigenschaftennamen durchsucht werden sollen</b>: Die Werte sind <i>TopN</i> zum Ermitteln der angegebenen Anzahl von Zeilen und <i>ScanAll</i> zum Abrufen aller Zeilen der Tabelle. <br/><br/>Wenn die Daten homogen und vorhersagbar sind, wird empfohlen, *TopN* auszuwählen und eine Zahl für N einzugeben. Für große Tabellen kann dies zu kürzeren Lesedauern führen.<br/><br/>Wenn die Daten mit Sätzen von Eigenschaften strukturiert sind, die abhängig von der Tiefe und Position der Tabelle variieren, wählen Sie die Option *ScanAll*, um alle Zeilen zu überprüfen. Dadurch wird die Integrität der resultierenden Eigenschafts- und Metadatenkonvertierung sichergestellt.<br/><br/></ul><b>Privates Speicherkonto</b>: Die Parameter lauten wie folgt: <br/><br/><ul><b>Kontoname</b>: Gibt den Namen des Kontos an, das die zu lesende Tabelle enthält.<br/><br/><b>Account key</b>: Gibt den Speicherschlüssel an, der dem Konto zugeordnet ist.<br/><br/><b>Table name</b> : Gibt den Namen der Tabelle an, die die zu lesenden Daten enthält.<br/><br/><b>Rows to scan for property names</b>: Die Werte sind <i>TopN</i> zum Ermitteln der angegebenen Anzahl von Zeilen und <i>ScanAll</i> zum Abrufen aller Zeilen der Tabelle.<br/><br/>Wenn die Daten homogen und vorhersagbar sind, wird empfohlen, *TopN* auszuwählen und eine Zahl für N einzugeben. Für große Tabellen kann dies zu kürzeren Lesedauern führen.<br/><br/>Wenn die Daten mit Sätzen von Eigenschaften strukturiert sind, die abhängig von der Tiefe und Position der Tabelle variieren, wählen Sie die Option *ScanAll*, um alle Zeilen zu überprüfen. Dadurch wird die Integrität der resultierenden Eigenschafts- und Metadatenkonvertierung sichergestellt.<br/><br/> |
| Azure Blob Storage |Liest im Blob-Dienst in Azure Storage gespeicherte Daten, so z. B. Bilder, unstrukturierten Text oder Binärdaten.<br/><br/>Mithilfe des Blob-Diensts können Sie Daten öffentlich verfügbar machen oder Anwendungsdaten privat speichern. Über HTTP oder HTTPS-Verbindungen können Sie von überall auf Ihre Daten zugreifen. |Die Optionen im **Import Data** -Modul ändern sich je nachdem, ob Sie auf öffentliche Informationen oder ein privates Speicherkonto zugreifen, das Anmeldeinformationen erfordert. Dies wird anhand der Option <b>Authentication Type</b> bestimmt, die den Wert "PublicOrSAS" oder "Account" haben kann.<br/><br/><b>Öffentlicher oder SAS-URI (Shared Access Signature)</b>: Die Parameter lauten wie folgt:<br/><br/><ul><b>URI:</b> Gibt die öffentliche URL oder SAS-URL für das Speicherblob an.<br/><br/><b>File Format</b>: Gibt das Format der Daten im Blob-Dienst an. Die unterstützten Formate sind CSV, TSV und ARFF.<br/><br/></ul><b>Privates Speicherkonto</b>: Die Parameter lauten wie folgt: <br/><br/><ul><b>Kontoname</b>: Gibt den Namen des Kontos an, das das zu lesende Blob enthält.<br/><br/><b>Account key</b>: Gibt den Speicherschlüssel an, der dem Konto zugeordnet ist.<br/><br/><b>Path to container, directory, or blob </b>: Gibt den Namen des Blobs an, das die zu lesenden Daten enthält.<br/><br/><b>Blob file format</b>: Gibt das Format der Daten im Blobdienst an. Die unterstützten Datenformate sind CSV, TSV, ARFF, CSV mit einer angegebenen Codierung und Excel. <br/><br/><ul>Wenn das Format CSV oder TSV ist, müssen Sie angeben, ob die Datei eine Kopfzeile enthält.<br/><br/>Sie können die Excel-Option zum Lesen von Daten aus Excel-Arbeitsmappen verwenden. Bei der Option <i>Excel data format</i> geben Sie an, ob die Daten in einem Excel-Arbeitsblattbereich oder einer Excel-Tabelle enthalten sind. Bei der Option <i>Excel sheet or embedded table</i> geben Sie den Namen des Blatts oder der Tabelle an, das bzw. die Sie lesen möchten.</ul><br/> |
| Data Feed Provider |Liest Daten aus einem unterstützten Feedanbieter. Derzeit wird nur das OData-Format (Open Data Protocol) unterstützt. |<b>Data content type</b>: Gibt das OData-Format an.<br/><br/><b>Source URL</b>: Gibt die vollständige URL für den Datenfeed an. <br/>Beispielsweise liest die folgende URL aus der Northwind-Beispieldatenbank: https://services.odata.org/northwind/northwind.svc/ |

## <a name="import-from-another-experiment"></a>Importieren aus einem anderen Experiment

Hin und wieder wird ein Zwischenergebnis aus einem Experiment benötigt, um es als Teil eines anderen Experiments zu verwenden. Dazu speichern Sie das Modul als Dataset:

1. Klicken Sie auf die Ausgabe des Moduls, die Sie als Dataset speichern möchten.
2. Klicken Sie auf **Save as Dataset**.
3. Wenn Sie dazu aufgefordert werden, geben Sie einen Namen und eine Beschreibung ein, mit denen das Dataset leicht wiederzuerkennen ist.
4. Klicken Sie auf das Häkchen **OK** .

Wenn der Speichervorgang abgeschlossen ist, ist das Dataset für die Verwendung in allen Experimenten in Ihrem Arbeitsbereich verfügbar. Sie finden es in der Liste **Saved Datasets** in der Modulpalette.

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen von Azure Machine Learning Studio-Webdiensten, die Module zum Importieren und Exportieren von Daten verwenden](web-services-that-use-import-export-modules.md)


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[export-data]: /azure/machine-learning/studio-module-reference/export-data


<!-- Module References -->
[convert-to-dataset]: /azure/machine-learning/studio-module-reference/convert-to-dataset
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[import-data]: /azure/machine-learning/studio-module-reference/import-data