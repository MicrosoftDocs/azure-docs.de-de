---
title: Verarbeiten umfangreicher Datasets mit Azure Data Factory und Azure Batch
description: Beschreibt, wie Sie große Datenmengen in einer Azure Data Factory-Pipeline verarbeiten, indem Sie die Parallelverarbeitungsfunktion von Azure Batch nutzen.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: c59e164d67a665a5e99cb5045b3b0cf38e33dc87
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364272"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Verarbeiten umfangreicher Datasets mit Azure Data Factory und Azure Batch
> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory, die allgemein verfügbar ist. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Benutzerdefinierte Aktivitäten in Data Factory](../transform-data-using-dotnet-custom-activity.md).

In diesem Artikel wird die Architektur einer Beispiellösung beschrieben, mit der umfangreiche Datasets auf automatische und geplante Weise verschoben und verarbeitet werden. Darüber hinaus enthält der Artikel eine umfassende exemplarische Vorgehensweise zur Implementierung der Lösung mit Azure Data Factory und Azure Batch.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Er ist länger als viele andere Artikel, weil sich die exemplarische Vorgehensweise auf eine vollständige Beispiellösung bezieht. Falls Sie mit Batch und Data Factory noch nicht vertraut sind, können Sie sich darüber informieren, wie diese Dienste funktionieren und zusammenarbeiten. Wenn Sie mit den Diensten schon vertraut sind und eine Lösung planen oder erstellen, können Sie sich auf den Abschnitt „Architektur“ des Artikels konzentrieren. Wenn Sie einen Prototyp oder eine Lösung entwickeln, sollten Sie den Schritt-für-Schritt-Anleitungen in der exemplarischen Vorgehensweise folgen. Wir freuen uns über Ihre Kommentare hierzu und darüber, wie Sie die Informationen nutzen.

Zunächst wird beschrieben, wie der Data Factory- und der Batch-Dienst Sie beim Verarbeiten großer Datasets in der Cloud unterstützen.     


## <a name="why-azure-batch"></a>Gründe für die Verwendung von Azure Batch
 Sie können mithilfe von Batch umfangreiche auf Parallelverarbeitung ausgelegte HPC-Anwendungen (High Performance Computing) effizient in der Cloud ausführen. Azure Batch ist ein Plattformdienst, mit dem rechenintensive Arbeitsschritte auf eine verwaltete Sammlung virtueller Computer angewendet werden. Der Dienst ermöglicht das automatische Skalieren von Computeressourcen, um die Anforderungen Ihrer Aufträge zu erfüllen.

Mit dem Batch-Dienst definieren Sie Azure-Computeressourcen, um Ihre Anwendungen parallel und bedarfsorientiert auszuführen. Sie können Aufträge bedarfsgesteuert oder gemäß einem Zeitplan ausführen. Es ist keine manuelle Erstellung, Konfiguration und Verwaltung eines HPC-Clusters, einzelner VMs, virtueller Netzwerke oder einer komplexen Infrastruktur für die Auftrags- und Aufgabenplanung erforderlich.

 Wenn Sie mit Batch nicht vertraut sind, helfen Ihnen die folgenden Artikel, die Architektur und Implementierung der in diesem Artikel beschriebenen Lösung zu verstehen:   

* [Grundlegendes zu Azure Batch](../../azure-sql/database/sql-database-paas-overview.md)
* [Übersicht über Azure Batch-Features](../../batch/batch-service-workflow-features.md)

Weitere Informationen zu Batch finden Sie auch in der [Batch-Dokumentation](../../batch/index.yml).

## <a name="why-azure-data-factory"></a>Gründe für die Verwendung von Azure Data Factory
Data Factory ist ein cloudbasierter Daten-Integrationsdienst, der das Verschieben und Transformieren von Daten organisiert und automatisiert. Mit Data Factory können Sie verwaltete Datenpipelines erstellen, die Daten aus lokalen und Clouddatenspeichern in einen zentralen Datenspeicher verschieben. Ein Beispiel ist Azure Blob Storage. Mit Data Factory können Sie Daten verarbeiten bzw. transformieren, indem Sie Dienste wie Azure HDInsight und Azure Machine Learning nutzen. Sie können Datenpipelines auch so planen, dass sie gemäß einem Zeitplan ausgeführt werden (z.B. stündlich, täglich und wöchentlich). Sie können die Pipelines auf einen Blick überwachen und verwalten, um Probleme zu erkennen und Maßnahmen zu ergreifen.

  Wenn Sie mit Data Factory nicht vertraut sind, helfen Ihnen die folgenden Artikel, die Architektur und Implementierung der in diesem Artikel beschriebenen Lösung zu verstehen:  

* [Einführung in Data Factory](data-factory-introduction.md)
* [Erstellen der ersten Datenpipeline](data-factory-build-your-first-pipeline.md)   

Weitere Informationen zu Data Factory finden Sie auch in der [Data Factory-Dokumentation](/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Gemeinsame Verwendung von Data Factory und Batch
Data Factory umfasst integrierte Aktivitäten. Beispiel: Die Kopieraktivität dient beispielsweise zum Kopieren bzw. Verschieben von Daten aus einem Quell- in einen Zieldatenspeicher. Die Hive-Aktivität wird zum Verarbeiten von Daten mithilfe von Hadoop-Clustern (HDInsight) in Azure verwendet. Eine Liste mit unterstützten Transformationsaktivitäten finden Sie unter [Transformieren von Data in Azure Data Factory](data-factory-data-transformation-activities.md).

Sie können auch benutzerdefinierte.NET-Aktivitäten erstellen, um Daten mit Ihrer eigenen Logik zu verschieben oder zu verarbeiten. Sie können diese Aktivitäten in einem HDInsight-Cluster oder in einem Batch-Pool von VMs ausführen. Bei Verwendung von Azure Batch können Sie den Pool so konfigurieren, dass er basierend auf einer von Ihnen angegebenen Formel automatisch skaliert wird (d.h., dass VMs basierend auf der Workload hinzugefügt oder entfernt werden).     

## <a name="architecture-of-a-sample-solution"></a>Architektur einer Beispiellösung
  Die in diesem Artikel beschriebene Architektur ist für eine einfache Lösung gedacht. Sie ist aber auch für komplexe Szenarien relevant, z.B. Risikomodellierung durch Finanzdienstleister, Bildverarbeitung und -rendering sowie Genomanalysen.

Das Diagramm veranschaulicht, wie Data Factory die Datenverschiebung und -verarbeitung orchestriert. Es zeigt auch, wie Batch die Daten parallel verarbeitet. Laden Sie zum einfachen Nachschlagen das Diagramm herunter, und drucken Sie es (im A3-Format) aus. Informationen zum Zugriff auf das Diagramm und zu seinem Ausdruck finden Sie unter [HPC und Datenorchestrierung mit Batch und Data Factory](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagramm der Verarbeitung großer Datenmengen](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

Die folgende Liste enthält die grundlegenden Schritte des Prozesses. Die Lösung enthält Code und Erklärungen zum Erstellen der End-to-End-Lösung.

* **Konfigurieren von Azure Batch mit einem Pool von Computeknoten (VMs).** Sie können die Anzahl der Knoten und deren Größe angeben.

* **Erstellen einer Data Factory-Instanz**, die mit Entitäten konfiguriert wird, die Blob Storage, den Azure Batch-Computedienst, Ein-/Ausgabedaten und einen Workflow bzw. eine Pipeline mit Aktivitäten zum Verschieben und Transformieren von Daten darstellen.

* **Erstellen einer benutzerdefinierten .NET-Aktivität in der Data Factory-Pipeline.** Die Aktivität ist Ihr Benutzercode, der im Azure Batch-Pool ausgeführt wird.

* **Speichern großer Mengen von Eingabedaten als Blobs in Azure Storage.** Daten werden in logische Einheiten (in der Regel nach Zeit) aufgeteilt.

* **Data Factory kopiert Daten, die parallel zum sekundären Speicherort verarbeitet werden**.

* **Data Factory führt die benutzerdefinierte Aktivität mit dem von Batch zugeordneten Pool aus.** Data Factory kann Aktivitäten gleichzeitig ausführen. Jede Aktivität verarbeitet einen Slice von Daten. Die Ergebnisse werden im Speicher gespeichert.

* Nachdem alle Ergebnisse abgerufen werden, **verschiebt Data Factory die Ergebnisse für die Verteilung über eine App oder zur weiteren Verarbeitung durch andere Tools an einen dritten Speicherort**.

## <a name="implementation-of-the-sample-solution"></a>Implementierung der Beispiellösung
Die Beispiellösung ist absichtlich einfach gehalten. Sie soll veranschaulichen, wie Sie Data Factory und Batch zusammen zum Verarbeiten von Datasets verwenden. Die Lösung zählt, wie häufig der Suchbegriff „Microsoft“ in Eingabedateien vorkommt, die in einer Zeitreihe organisiert sind. Sie gibt anschließend die Anzahl der Ausgabedateien aus.

**Zeit:** Wenn Sie mit den Grundlagen von Azure, Data Factory und Batch vertraut sind und die unten angegebenen Voraussetzungen erfüllt haben, dauert das Erstellen dieser Lösung ca. ein bis zwei Stunden.

### <a name="prerequisites"></a>Voraussetzungen
#### <a name="azure-subscription"></a>Azure-Abonnement
Wenn Sie über kein Azure-Abonnement verfügen, können Sie schnell ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Azure-Speicherkonto
Sie nutzen ein Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie kein Speicherkonto haben, lesen Sie [Erstellen eines Speicherkontos](../../storage/common/storage-account-create.md). Die Beispiellösung verwendet Blob-Speicher.

#### <a name="azure-batch-account"></a>Azure Batch-Konto
Erstellen Sie im [Azure-Portal](https://portal.azure.com/) ein Batch-Konto. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Batch-Kontos](../../batch/batch-account-create-portal.md). Notieren Sie den Namen und Kontoschlüssel des Batch-Kontos. Sie können auch das Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/new-azbatchaccount) verwenden, um ein Batch-Konto zu erstellen. Unter [Verwalten von Batch-Ressourcen mit PowerShell-Cmdlets](../../batch/batch-powershell-cmdlets-get-started.md) erfahren Sie weitere Einzelheiten zur Verwendung dieses Cmdlets.

Die Beispiellösung verwendet Batch (indirekt über eine Data Factory-Pipeline) zum parallelen Verarbeiten von Daten in einem Computeknotenpool (einer verwalteten Sammlung von VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Azure Batch-Pool mit virtuellen Computern
Erstellen Sie einen Batch-Pool mit mindestens zwei Computeknoten.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) im linken Menü auf **Durchsuchen** und dann auf **Batch-Konten**.

1. Wählen Sie Ihr Batch-Konto aus, um das Blatt **Batch-Konto** zu öffnen.

1. Wählen Sie die Kachel **Pools** aus.

1. Klicken Sie auf dem Blatt **Pools** auf der Symbolleiste auf die Schaltfläche **Hinzufügen**, um einen Pool hinzuzufügen.

   a. Geben Sie eine ID für den Pool ein (**Pool-ID**). Notieren Sie die ID des Pools. Sie benötigen sie, wenn Sie die Data Factory-Lösung erstellen.

   b. Geben Sie **Windows Server 2012 R2** für die Einstellung **Betriebssystemfamilie** ein.

   c. Wählen Sie einen **Knotentarif** aus.

   d. Geben Sie **2** als Wert für die Einstellung **Zuordnung für Ziel** ein.

   e. Geben Sie **2** als Wert für die Einstellung **Max. Tasks pro Knoten** ein.

   f. Klicken Sie auf **OK**, um den Pool zu erstellen.

#### <a name="azure-storage-explorer"></a>Azure Storage-Explorer
Sie verwenden [Azure Storage-Explorer 6](https://azurestorageexplorer.codeplex.com/) oder [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (von ClumsyLeaf Software), um die Daten in Ihren Storage-Projekten zu überprüfen und zu ändern. Sie können auch die Daten in den Protokollen Ihrer in der Cloud gehosteten Anwendungen einsehen und ändern.

1. Erstellen Sie einen Container mit dem Namen **mycontainer** mit privatem Zugriff (ohne anonymen Zugriff).

1. Wenn Sie CloudXplorer verwenden, erstellen Sie Ordner und Unterordner mit der folgenden Struktur:

   ![Ordner- und Unterordnerstruktur](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder`und `outputfolder` sind in `mycontainer` Ordner der obersten Ebene. Der Ordner `inputfolder` enthält Unterordner mit Datums-/Uhrzeitstempeln (JJJJ-MM-TT-HH).

   Wenn Sie Storage-Explorer verwenden, müssen Sie im nächsten Schritt Dateien mit Namen wie `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` usw. hochladen. In diesem Schritt werden die Ordner automatisch erstellt.

1. Erstellen Sie eine Textdatei **file.txt** auf dem Computer mit Inhalt, der das Schlüsselwort **Microsoft** enthält. Ein Beispiel ist „benutzerdefinierte Aktivität testen Microsoft benutzerdefinierte Aktivität testen Microsoft“.

1. Laden Sie die Datei in die folgenden Eingangsordner in Blob Storage hoch:

   ![Eingabeordner](./media/data-factory-data-processing-using-batch/image4.png)

   Wenn Sie Storage-Explorer verwenden, laden Sie die Datei **file.txt** in **mycontainer** hoch. Klicken Sie auf der Symbolleiste auf **Kopieren**, um eine Kopie des Blobs zu erstellen. Ändern Sie im Dialogfeld **Blob kopieren** den **Namen des Zielblobs** in `inputfolder/2015-11-16-00/file.txt`. Wiederholen Sie diesen Schritt zum Erstellen von `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` usw. Mit dieser Aktion werden die Ordner automatisch erstellt.

1. Erstellen Sie einen weiteren Container mit dem Namen `customactivitycontainer`. Laden Sie die benutzerdefinierte Aktivität als ZIP-Datei in diesen Container hoch.

#### <a name="visual-studio"></a>Visual Studio
Installieren Sie Visual Studio 2012 oder höher, um eine benutzerdefinierte Azure Batch-Aktivität zu erstellen, die in der Data Factory-Lösung verwendet werden soll.

### <a name="high-level-steps-to-create-the-solution"></a>Allgemeine Schritte zum Erstellen der Lösung
1. Erstellen Sie eine benutzerdefinierte Aktivität, die die Logik für die Datenverarbeitung enthält.

1. Erstellen Sie eine Data Factory, die die benutzerdefinierte Aktivität verwendet.

### <a name="create-the-custom-activity"></a>Erstellen der benutzerdefinierten Aktivität
Die benutzerdefinierte Data Factory-Aktivität ist das Herzstück dieser Beispiellösung. Die Beispiellösung verwendet Azure Batch, um die benutzerdefinierte Aktivität auszuführen. Informationen zum Erstellen einer benutzerdefinierten Aktivität und zu deren Verwendung in einer Data Factory-Pipeline finden Sie unter [Verwenden benutzerdefinierter Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md).

Um eine benutzerdefinierte .NET-Aktivität zu erstellen, die Sie in einer Data Factory-Pipeline verwenden können, müssen Sie ein .NET-Klassenbibliotheksprojekt mit einer Klasse erstellen, die die „IDotNetActivity“-Schnittstelle implementiert. Diese Schnittstelle verfügt lediglich über eine Methode: „Execute“. Dies ist die Signatur der Methode:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

Die Methode verfügt über einige wichtige Komponenten, die Sie kennen müssen:

* Die Methode akzeptiert vier Parameter:

  * **LinkedServices**. Dieser Parameter ist eine aufzählbare Liste verknüpfter Dienste, die Ein- und Ausgabedatenquellen (z.B. Blob Storage) mit der Data Factory verknüpfen. In diesem Beispiel gibt es nur einen verknüpften Dienst des Typs „Azure Storage“, der sowohl für die Eingabe als auch die Ausgabe verwendet wird.
  * **Datasets**. Dieser Parameter ist eine aufzählbare Liste von Datasets. Sie können diesen Parameter zum Abrufen der Speicherorte und Schemas verwenden, die von den Eingabe- und Ausgabedatasets definiert werden.
  * **Aktivität**. Dieser Parameter stellt die aktuelle Compute-Entität dar. In diesem Fall ist dies ein Batch-Dienst.
  * **Protokollierungstool**. Mit dem Protokollierungstool können Sie Debugkommentare schreiben, die dann als das Protokoll „User“ für die Pipeline angezeigt werden.
* Die Methode gibt ein Wörterbuch zurück, das künftig zum Verketten benutzerdefinierter Aktivitäten verwendet werden kann. Diese Funktion ist noch nicht implementiert, sodass von der Methode nur ein leeres Wörterbuch zurückgegeben wird.

#### <a name="procedure-create-the-custom-activity"></a>Prozedur: Erstellen der benutzerdefinierten Aktivität
1. Erstellen Sie in Visual Studio ein .NET-Klassenbibliotheksprojekt.

   a. Starten Sie Visual Studio 2012/2013/2015.

   b. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

   c. Erweitern Sie **Vorlagen**, und wählen Sie **Visual C#\#** aus. In dieser exemplarischen Vorgehensweise verwenden Sie C\#, Sie können jedoch jede .NET-Sprache verwenden, um benutzerdefinierte Aktivität zu entwickeln.

   d. Wählen Sie in der Liste mit den Projekttypen auf der rechten Seite den Eintrag **Klassenbibliothek** aus.

   e. Geben Sie **MyDotNetActivity** als **Namen** ein.

   f. Wählen Sie **C:\\ADF** als **Speicherort**. Erstellen Sie den Ordner **ADF**, sofern noch nicht vorhanden.

   g. Klicken Sie auf **OK**, um das Projekt zu erstellen.

1. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.

1. Führen Sie in der Paket-Manager-Konsole den folgenden Befehl zum Importieren von „Microsoft.Azure.Management.DataFactories“ aus:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importieren Sie das NuGet-Paket **Azure Storage** in das Projekt. Sie benötigen dieses Paket, da Sie in diesem Beispiel die Blob Storage-API verwenden:

    ```powershell
    Install-Package Az.Storage
    ```
1. Fügen Sie die folgenden „using“-Anweisungen zur Quelldatei im Projekt hinzu:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Ändern Sie den Namen des Namespace in **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Ändern Sie den Namen der Klasse in **MyDotNetActivity**, und leiten Sie ihn, wie unten dargestellt, von der **IDotNetActivity**-Schnittstelle ab:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implementieren die **Execute**-Methode der **IDotNetActivity**-Schnittstelle der **MyDotNetActivity**-Klasse, bzw. fügen Sie sie hinzu. Kopieren Sie den folgenden Beispielcode in die Methode an. Im Abschnitt [Execute-Methode](#execute-method) erfahren Sie mehr über die in dieser Methode verwendete Logik.

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {

       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;

       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);

       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;

       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.

       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

       // Initialize the continuation token before using it in the do-while loop.
       BlobContinuationToken continuationToken = null;
       do
       {   // get the list of input blobs from the input storage client object.
           BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                    true,
                                    BlobListingDetails.Metadata,
                                    null,
                                    continuationToken,
                                    null,
                                    null);

           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

       } while (continuationToken != null);

       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

       folderPath = GetFolderPath(outputDataset);

       logger.Write("Writing blob to the folder: {0}", folderPath);

       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);

       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Fügen Sie die folgenden Helfermethoden zur Klasse hinzu. Diese Methoden werden von der **Execute** -Methode aufgerufen. Vor allem isoliert die **Calculate**-Methode den Code, der jeden Blob durchläuft.

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>
    private static string GetFolderPath(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
       if (dataArtifact == null || dataArtifact.Properties == null)
       {
           return null;
       }

       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }

       return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
       string output = string.Empty;
       logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
       foreach (IListBlobItem listBlobItem in Bresult.Results)
       {
           CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
           if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
           {
               string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
               logger.Write("input blob text: {0}", blobText);
               string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
               var matchQuery = from word in source
                                where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                select word;
               int wordCount = matchQuery.Count();
               output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
           }
       }
       return output;
    }
    ```
    Die „GetFolderPath“-Methode gibt den Pfad zum Ordner zurück, auf den das Dataset verweist, und die „GetFileName“-Methode gibt den Namen des Blobs oder der Datei zurück, auf das oder die das Dataset verweist.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    Die „Calculate“-Methode berechnet die Anzahl der Vorkommen des Stichworts „Microsoft“ in den Eingabedateien (Blobs im Ordner). Der Suchbegriff („Microsoft“) ist im Code hartcodiert.

1. Kompilieren Sie das Projekt. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.

1. Starten Sie Windows-Explorer, und wechseln Sie zum Ordner **bin\\debug** oder **bin\\release**. Die Ordnerwahl hängt vom Typ des Builds ab.

1. Erstellen Sie die ZIP-Datei **MyDotNetActivity.zip**, die alle Binärdateien im Ordner **\\bin\\Debug** enthält. Sie möchten ggf. die Datei „MyDotNetActivity.**pdb**“ einbeziehen, damit Sie zusätzliche Details wie z.B. die Zeilennummer im Quellcode erhalten, der das Problem bei einem Fehler verursacht hat.

   ![Die Ordnerliste „bin\Debug“](./media/data-factory-data-processing-using-batch/image5.png)

1. Laden Sie **MyDotNetActivity.zip** als Blob in den Blobcontainer `customactivitycontainer` in den Blob Storage hoch, den der verknüpfte Dienst „StorageLinkedService“ in „ADFTutorialDataFactory“ verwendet. Erstellen Sie den Blobcontainer `customactivitycontainer`, falls noch nicht vorhanden.

#### <a name="execute-method"></a>Execute-Methode
Dieser Abschnitt enthält weitere Details und Hinweise zum Code in der „Execute“-Methode.

1. Die Elemente zum Durchlaufen der Eingabesammlung finden Sie unter dem Namespace [Microsoft.WindowsAzure.Storage.Blob](/java/api/com.microsoft.azure.storage.blob) . Zum Durchlaufen der Blobsammlung müssen Sie die **BlobContinuationToken**-Klasse verwenden. Im Wesentlichen müssen Sie eine do-while-Schleife mit dem Token als Mechanismus zum Beenden der Schleife verwenden. Weitere Informationen finden Sie unter [Verwenden von Blob Storage in .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md). Eine einfache Schleife ist hier dargestellt:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,

                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.

     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

    } while (continuationToken != null);

    ```
   Weitere Informationen finden Sie in der Dokumentation für die [ListBlobsSegmented](/java/api/com.microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)-Methode.

1. Der Code für die Verwendung durch den Satz von Blobs geht logischerweise in die do-while-Schleife ein. In der **Execute**-Methode übergibt die do-while-Schleife die Liste der Blobs an eine Methode namens **Calculate**. Die Methode gibt eine Zeichenfolgenvariable mit dem Namen **output** zurück, die das Ergebnis des Durchlaufens aller Blobs im Segment ist.

   Sie gibt die Anzahl der Vorkommen des Suchbegriffs (Microsoft) im Blob zurück, das an die **Calculate**-Methode übergeben wurde.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Sobald die **Calculate**-Methode abgeschlossen ist, muss sie in ein neues Blob geschrieben werden. Für jede Gruppe von Blobs, die verarbeitet wurde, kann ein neues Blob mit den Ergebnissen geschrieben werden. Um ein neues Blob zu schreiben, müssen Sie zunächst das Ausgabedataset suchen.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. Der Code ruft auch die Hilfsmethode **GetFolderPath** zum Abrufen des Ordnerpfads (Name des Speichercontainers) auf.

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   Die „GetFolderPath“-Methode wandelt das „DataSet“-Objekt in ein „AzureBlobDataSet“-Objekt um, das über die Eigenschaft „FolderPath“ verfügt.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FolderPath;
    ```
1. Der Code ruft die **GetFileName** -Methode zum Abrufen des Dateinamens (Blob-Name) auf. Der Code ähnelt dem vorherigen Code zum Abrufen des Ordnerpfads.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

    return blobDataset.FileName;
    ```
1. Der Name der Datei wird durch Erstellen eines URI-Objekts geschrieben. Der URI-Konstruktor verwendet die **BlobEndpoint** -Eigenschaft, um den Containernamen zurückzugeben. Ordnerpfad und Dateiname werden hinzugefügt, um den URI für den Ausgabe-Blob zu erstellen.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Nachdem der Name der Datei geschrieben wurde, können Sie die Ausgabezeichenfolge aus der **Calculate**-Methode in ein neues Blob schreiben:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Erstellen der Data Factory
Sie haben im Abschnitt [Erstellen der benutzerdefinierten Aktivität](#create-the-custom-activity) eine benutzerdefinierte Aktivität erstellt sowie die ZIP-Datei mit Binärdateien und die PDB-Datei in einen Blobcontainer hochgeladen. In diesem Abschnitt erstellen Sie eine Data Factory mit einer Pipeline, die die benutzerdefinierte Aktivität verwendet.

Das Eingabedataset für die benutzerdefinierte Aktivität stellt die Blobs (Dateien) im Eingabeordner (`mycontainer\\inputfolder`) im Blobspeicher dar. Das Ausgabedataset für die Aktivität stellt die Ausgabeblobs im Ausgabeordner (`mycontainer\\outputfolder`) im Blobspeicher dar.

Legen Sie eine oder mehrere Dateien in den Eingabeordnern ab:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Legen Sie z.B. eine Datei (file.txt) mit dem folgenden Inhalt in jedem Ordner ab:

```
test custom activity Microsoft test custom activity Microsoft
```

Jeder Eingabeordner entspricht einem Slice in der Data Factory, auch wenn der Ordner zwei oder mehr Dateien enthält. Wenn jeder Slice von der Pipeline verarbeitet wird, durchläuft die benutzerdefinierte Aktivität alle Blobs im Eingabeordner für diesen Slice.

Sie sehen fünf Ausgabedateien mit dem gleichen Inhalt. Die Ausgabedatei vom Verarbeiten der Datei im Ordner „2015-11-16-00“ wird z. B. den folgenden Inhalt aufweisen:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Wenn Sie mehrere Dateien (file.txt, file2.txt, file3.txt) mit dem gleichen Inhalt im Eingabeordner ablegen, sehen Sie in der Ausgabedatei folgenden Inhalt. Jeder Ordner (2015-11-16-00 usw.) entspricht einem Slice in diesem Beispiel, obwohl der Ordner mehrere Eingabedateien hat.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

Die Ausgabedatei enthält jetzt drei Zeilen, jeweils eine für jede Datei (Blob) in dem Ordner, der dem Slice (2015-11-16-00) zugeordnet ist.

Bei jeder Aktivitätsausführung wird eine Aufgabe erstellt. In diesem Beispiel ist nur eine Aktivität in der Pipeline vorhanden. Wenn ein Slice von der Pipeline verarbeitet wird, wird die benutzerdefinierte Aktivität in Batch ausgeführt, um den Slice zu verarbeiten. Da es sich um fünf Slices handelt (jeder Slice kann mehrere Blobs oder Dateien enthalten), werden in Batch fünf Tasks erstellt. Wenn ein Task in Batch ausgeführt wird, ist es die benutzerdefinierte Aktivität, die ausgeführt wird.

Die folgende exemplarische Vorgehensweise enthält weitere Details.

#### <a name="step-1-create-the-data-factory"></a>Schritt 1: Erstellen der Data Factory
1. Gehen Sie nach der Anmeldung beim [Azure-Portal](https://portal.azure.com/) wie folgt vor:

   a. Klicken Sie im linken Menü auf **NEU**.

   b. Klicken Sie auf dem Blatt **Neu** auf **Daten + Analysen**.

   c. Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.

1. Geben Sie auf dem Blatt **Neue Data Factory** die Zeichenfolge **CustomActivityFactory** als Namen ein. Der Name der Data Factory muss global eindeutig sein. Wenn die Fehlermeldung „Data Factory-Name CustomActivityFactory nicht verfügbar“ angezeigt wird, ändern Sie den Namen der Data Factory. Verwenden Sie z.B. „IhrNameCustomActivityFactory“, und erstellen Sie die Data Factory erneut.

1. Klicken Sie auf **RESSOURCENGRUPPENNAME**. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine Ressourcengruppe.

1. Stellen Sie sicher, dass Sie zum Erstellen der Data Factory das richtige Abonnement und die richtige Region verwenden.

1. Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

1. Im Dashboard des Portals sehen Sie, dass die Data Factory erstellt wird.

1. Nachdem die Data Factory erfolgreich erstellt wurde, wird die Seite **Data Factory** mit dem Inhalt der Data Factory angezeigt.

   ![Seite „Data Factory“](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Schritt 2: Erstellen von verknüpften Diensten
Verknüpfte Dienste verknüpfen Datenspeicher oder Computedienste mit einer Data Factory. In diesem Schritt verknüpfen Sie Ihr Speicherkonto und Batch-Konto mit Ihrer Data Factory.

#### <a name="create-an-azure-storage-linked-service"></a>Erstellen eines verknüpften Azure Storage-Diensts
1. Klicken Sie auf dem Blatt **Data Factory** für **CustomActivityFactory** auf die Kachel **Verfassen und bereitstellen**. Der Data Factory-Editor wird eingeblendet.

1. Klicken Sie auf der Befehlsleiste auf **Neuer Datenspeicher**, und wählen Sie **Azure Storage**. Das JSON-Skript, mit dem Sie einen mit Azure Storage verknüpften Dienst im Editor erstellen, wird angezeigt.

   ![Neuer Datenspeicher](./media/data-factory-data-processing-using-batch/image7.png)

1. Ersetzen Sie **account name** durch den Namen Ihres Speicherkontos. Ersetzen Sie **account key** durch den Zugriffsschlüssel Ihres Speicherkontos. Weitere Informationen zum Abrufen der Speicherzugriffsschlüssel finden Sie unter [Verwalten von Speicherkonto-Zugriffsschlüsseln](../../storage/common/storage-account-keys-manage.md).

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

   ![Bereitstellen](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Verwenden eines mit Azure Batch verknüpften Diensts
In diesem Schritt erstellen Sie für Ihr Batch-Konto einen verknüpften Dienst, der zum Ausführen der benutzerdefinierten Data Factory-Aktivität verwendet wird.

1. Klicken Sie auf der Befehlsleiste auf **Neue Computeressource**, und wählen Sie **Azure Batch**. Das JSON-Skript, mit dem Sie einen mit Azure Batch verknüpften Dienst im Editor erstellen, wird angezeigt.

1. In der JSON-Datei:

   a. Ersetzen Sie **Kontoname** durch den Namen Ihres Batch-Kontos.

   b. Ersetzen Sie **Zugriffsschlüssel** durch den Zugriffsschlüssel des Batch-Kontos.

   c. Geben Sie die ID des Pools für die **poolName**-Eigenschaft ein. Für diese Eigenschaft können Sie entweder den Poolnamen oder die Pool-ID eingeben.

   d. Geben Sie die Batch-URI für die JSON-Eigenschaft **batchUri** ein.

      > [!IMPORTANT]
      > Die URL auf dem Blatt **Batch-Konto** hat folgendes Format: \<accountname\>.\<region\>.batch.azure.com. Für die **batchUri**-Eigenschaft im JSON-Skript müssen Sie a88"accountname** aus der URL entfernen. z. B. `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Blatt „Batch-Konto“](./media/data-factory-data-processing-using-batch/image9.png)

      Für die **poolName**-Eigenschaft können Sie auch die ID des Pools anstelle des Namens des Pools angeben.

      > [!NOTE]
      > Der Data Factory-Dienst unterstützt im Gegensatz zu HDInsight für Azure Batch keine bedarfsgesteuerte Option. In einer Data Factory können Sie nur Ihren eigenen Batch-Pool verwenden.
      >
      >

   e. Geben Sie **StorageLinkedService** for the **StorageLinkedService** -Element ein. Sie haben diesen verknüpften Dienst im vorherigen Schritt erstellt. Dieser Speicher wird als Stagingbereich für Dateien und Protokolle verwendet.

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

#### <a name="step-3-create-datasets"></a>Schritt 3: Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets zur Darstellung von Eingabe- und Ausgabedaten.

#### <a name="create-the-input-dataset"></a>Erstellen des Eingabedatasets
1. Klicken Sie im Data Factory-Editor auf der Symbolleiste auf die Schaltfläche **Neues Dataset**. Wählen Sie in der Dropdownliste **Azure Blob Storage** aus.

1. Ersetzen Sie das JSON-Skript im rechten Bereich durch den folgenden JSON-Ausschnitt:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           },
           "external": true,
           "policy": {}
       }
    }
    ```

    Im weiteren Verlauf dieser exemplarischen Vorgehensweise wird eine Pipeline mit der Startzeit „2015-11-16T00:00:00Z“ und der Endzeit „2015-11-16T05:00:00Z“ erstellt. Sie ist so eingerichtet, dass Daten stündlich erstellt werden, sodass sich fünf Eingabe-/Ausgabeslices (von **00**:00:00 -\> **05**:00:00) ergeben.

    **frequency** und **interval** für das Eingabedataset sind auf **Hour** und **1** festgelegt, was bedeutet, dass der Eingabeslice stündlich verfügbar ist.

    Die Startzeit für jeden Slice wird durch die Systemvariable **SliceStart** im obigen JSON-Ausschnitt dargestellt. Es folgen die Startzeiten für jeden Slice.

    | **Slice** | **Startzeit**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T **00**:00:00 |
    | 2         | 2015-11-16T **01**:00:00 |
    | 3         | 2015-11-16T **02**:00:00 |
    | 4         | 2015-11-16T **03**:00:00 |
    | 5         | 2015-11-16T **04**:00:00 |

    Der **folderPath** wird mit dem Jahr, Monat, Tag und der Uhrzeit der Slice-Startzeit berechnet (**SliceStart**). Hier erfahren Sie, wie ein Eingabeordner einem Slice zugeordnet wird.

    | **Slice** | **Startzeit**          | **Eingabeordner**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T **00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T **01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T **02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T **03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T **04**:00:00 | 2015-11-16-**04** |

1. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um die Tabelle **InputDataset** zu erstellen und bereitzustellen.

#### <a name="create-the-output-dataset"></a>Erstellen des Ausgabedatasets
In diesem Schritt erstellen Sie ein weiteres Dataset des Typs „AzureBlob“, um die Ausgabedaten darzustellen.

1. Klicken Sie im Data Factory-Editor auf der Symbolleiste auf die Schaltfläche **Neues Dataset**. Wählen Sie in der Dropdownliste **Azure Blob Storage** aus.

1. Ersetzen Sie das JSON-Skript im rechten Bereich durch den folgenden JSON-Ausschnitt:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
               "partitionedBy": [
                   {
                       "name": "slice",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy-MM-dd-HH"
                       }
                   }
               ]
           },
           "availability": {
               "frequency": "Hour",
               "interval": 1
           }
       }
    }
    ```

    Eine Ausgabedatei und ein Ausgabe-Blob wird für jeden Eingabeslice generiert. Im Folgenden sehen Sie, wie eine Ausgabedatei für jeden Slice benannt wird. Alle Ausgabedateien werden im Ausgabeordner `mycontainer\\outputfolder` generiert.

    | **Slice** | **Startzeit**          | **Ausgabedatei**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T **00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T **01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T **02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T **03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T **04**:00:00 | 2015-11-16-**04.txt** |

    Denken Sie daran, dass alle Dateien in einem Eingabeordner (z. B.: 2015-11-16-00) zu einem Slice mit der Startzeit 2015-11-16-00 gehören. Wenn dieser Slice verarbeitet wird, durchsucht die benutzerdefinierte Aktivität jede Datei und erzeugt eine Zeile in der Ausgabedatei mit der Anzahl der Vorkommen des Suchbegriffs „Microsoft“. Wenn drei Dateien im Ordner „2015-11-16-00“ vorhanden sind, werden in der Ausgabedatei „2015-11-16-00.txt“ drei Zeilen erstellt.

1. Klicken Sie auf der Symbolleiste auf **Bereitstellen**, um das **Ausgabedataset** zu erstellen und bereitzustellen.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Schritt 4: Erstellen und Ausführen der Pipeline mit einer benutzerdefinierten Aktivität
In diesem Schritt erstellen Sie eine Pipeline mit einer einzigen Aktivität, nämlich der von Ihnen zuvor erstellten benutzerdefinierten Aktivität.

> [!IMPORTANT]
> Wenn Sie **file.txt** noch nicht in die Eingabeordner im Blobcontainer hochgeladen haben, erledigen Sie dies vor Erstellen der Pipeline. Die **IsPaused**-Eigenschaft ist im JSON-Code der Pipeline auf „False“ festgelegt, sodass die Pipeline sofort ausgeführt wird, da das **Startdatum** in der Vergangenheit liegt.
>
>

1. Klicken Sie im Data Factory-Editor auf der Befehlsleiste auf die Schaltfläche **Neue Pipeline**. Wenn Sie den Befehl nicht sehen, klicken Sie auf das Symbol mit den Auslassungszeichen, um ihn anzuzeigen.

1. Ersetzen Sie das JSON-Skript im rechten Bereich durch den folgenden JSON-Ausschnitt:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Beachten Sie folgende Punkte:

   * Die Pipeline enthält nur eine Aktivität, die den Typ **DotNetActivity** hat.
   * **AssemblyName** wird auf den Namen der DLL festgelegt: **MyDotnetActivity.dll**.
   * **EntryPoint** wird auf **MyDotNetActivityNS.MyDotNetActivity** festgelegt. Es ist im Grunde \<namespace\>.\<classname\> in Ihrem Code.
   * **PackageLinkedService** ist auf **StorageLinkedService** festgelegt, was auf den Blob Storage verweist, der die ZIP-Datei mit der benutzerdefinierten Aktivität enthält. Wenn Sie andere Speicherkonten für die Ein- und Ausgabedateien und die ZIP-Datei mit der benutzerdefinierten Aktivität verwenden, müssen Sie einen weiteren mit Azure Storage verknüpften Dienst erstellen. Dieser Artikel setzt voraus, dass Sie das gleiche Speicherkonto verwenden.
   * **PackageFile** wird auf **customactivitycontainer/MyCustomActivity.zip** festgelegt. Das entspricht dem Format \<containerforthezip\>/\<nameofthezip.zip\>.
   * Die benutzerdefinierte Aktivität verwendet **InputDataset** als Eingabe und **OutputDataset** als Ausgabe.
   * Die Eigenschaft **linkedServiceName** der benutzerdefinierten Aktivität zeigt auf **AzureBatchLinkedService**, was Data Factory mitteilt, dass die benutzerdefinierte Aktivität in Azure Batch ausgeführt werden muss.
   * Die Einstellung **Parallelität** ist wichtig. Wenn Sie den Standardwert 1 verwenden, auch wenn Sie über zwei oder mehr Computeknoten im Batch-Pool verfügen, werden die Slices nacheinander verarbeitet. Daher nutzen Sie nicht die Parallelverarbeitungsfunktion von Azure Batch. Wenn Sie **concurrency** (Parallelität) auf einen höheren Wert wie z. B. 2 festlegen, können zwei Slices (was zwei Aufgaben in Azure Batch entspricht) gleichzeitig verarbeitet werden. In diesem Fall werden die virtuellen Computer im Batch-Pool verwendet. Legen Sie daher Sie die „concurrency“-Eigenschaft entsprechend fest.
   * Nur eine Aufgabe (Slice) wird standardmäßig auf einer virtuellen Maschine zu einem beliebigen Zeitpunkt ausgeführt. Standardmäßig ist **Max. Tasks pro VM** für einen Batch-Pool auf 1 festgelegt ist. Als Teil der Voraussetzungen haben Sie einen Pool erstellt, bei dem diese Eigenschaft auf 2 festgelegt ist. Aus diesem Grund können zwei Data Factory-Slices auf einem virtuellen Computer gleichzeitig ausgeführt werden.
     - Die **isPaused**-Eigenschaft ist standardmäßig auf „false“ festgelegt. Die Pipeline wird in diesem Beispiel sofort ausgeführt, da die Slices in der Vergangenheit starten. Legen Sie diese Eigenschaft auf **true** fest, um die Pipeline anzuhalten, und legen Sie sie zum Neustart wieder auf **false** fest.
     -   Die Zeiten für **start** und **end** liegen fünf Stunden auseinander. Slices werden stündlich erstellt, sodass insgesamt fünf Slices von der Pipeline erstellt werden.

1. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

#### <a name="step-5-test-the-pipeline"></a>Schritt 5: Testen der Pipeline
In diesem Schritt testen Sie die Pipeline durch Ablegen von Dateien in die Eingangsordner. Beginnen Sie mit dem Testen der Pipeline mit einer Datei pro Eingabeordner.

1. Klicken Sie im Azure-Portal auf dem Blatt **Data Factory** auf **Diagramm**.

   ![Diagramm](./media/data-factory-data-processing-using-batch/image10.png)

1. Doppelklicken Sie in der Ansicht **Diagramm** auf das Eingabedataset **inputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. Das Blatt **InputDataset** mit allen fünf bereiten Slices. Beachten Sie die **SLICE STARTZEIT** und **SLICE-ENDZEIT** für jeden Slice.

   ![Start- und Endzeiten für Eingabeslices](./media/data-factory-data-processing-using-batch/image12.png)

1. Klicken Sie in der Ansicht **Diagramm** auf **OutputDataset**.

1. Die fünf Ausgabeslices werden mit dem Status **Bereit** angezeigt, wenn sie erstellt wurden.

   ![Start- und Endzeiten für Ausgabeslices](./media/data-factory-data-processing-using-batch/image13.png)

1. Verwenden Sie das Portal, um die den Slices zugeordneten Tasks anzuzeigen und zu ermitteln, auf welchem virtuellen Computer die Slices jeweils ausgeführt wurden. Ausführliche Informationen finden Sie im Abschnitt [Integration von Data Factory und Batch](#data-factory-and-batch-integration).

1. Die Ausgabedateien werden unter `mycontainer` in `outputfolder` in Ihrem Blob Storage angezeigt.

   ![Ausgabedateien in Azure Storage](./media/data-factory-data-processing-using-batch/image15.png)

   Fünf Ausgabedateien (eine für jeden Eingabeslice) werden angezeigt. Jede Ausgabedatei weist ähnlichen Inhalt wie die folgende Ausgabe auf:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   Das folgende Diagramm veranschaulicht, wie die Data Factory-Slices den Tasks in Batch zugeordnet werden. In diesem Beispiel wird ein Slice nur einmal ausgeführt.

   ![Diagramm der Slicezuordnung](./media/data-factory-data-processing-using-batch/image16.png)

1. Testen Sie nun mehrere Dateien in einem Ordner. Erstellen Sie die Dateien **file2.txt**, **file3.txt**, **file4.txt** und **file5.txt** mit dem gleichen Inhalt wie „file.txt“ im Ordner **2015-11-06-01**.

1. Löschen Sie im Ausgabeordner die Ausgabedatei **2015-11-16-01.txt**.

1. Klicken Sie auf dem Blatt **OutputDataset** mit der rechten Maustaste auf den Slice, für den die **SLICE-STARTZEIT** auf **11/16/2015 01:00:00 Uhr** festgelegt ist. Klicken Sie auf **Ausführen**, um den Slice erneut auszuführen/zu verarbeiten. Der Slice verfügt jetzt über fünf Dateien statt einer.

    ![Ausführen](./media/data-factory-data-processing-using-batch/image17.png)

1. Wenn der Slice ausgeführt wird und der Status **Bereit** lautet, überprüfen Sie den Inhalt der Ausgabedatei für diesen Slice (**2015-11-16-01.txt**). Die Ausgabedatei wird unter `mycontainer` in `outputfolder` in Ihrem Blob Storage angezeigt. Für jede Datei des Slice sollte eine Zeile vorhanden sein.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Wenn Sie die Ausgabedatei „2015-11-16-01.txt“ nicht gelöscht haben, bevor Sie es mit fünf Eingabedateien versuchen, sehen Sie eine Zeile aus der vorherigen Sliceausführung und fünf Zeilen aus der aktuellen Sliceausführung. Der Inhalt wird standardmäßig an die Ausgabedatei angefügt, sofern bereits vorhanden.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integration von Data Factory und Batch
Der Data Factory-Dienst erstellt in Batch einen Auftrag mit dem Namen `adf-poolname:job-xxx`.

![Batch-Aufträge](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Bei jeder Aktivitätsausführung eines Slices wird eine Aufgabe im Auftrag erstellt. Wenn zehn Slices zur Verarbeitung bereitstehen, werden zehn Tasks im Auftrag erstellt. Sie können mehrere Slices parallel ausführen, wenn Sie über mehrere Compute-Knoten im Pool verfügen. Wenn die maximale Anzahl der Tasks pro Computeknoten auf mehr als 1 festgelegt ist, können mehrere Slices auf dem gleichen Computeknoten ausgeführt werden.

Dieses Beispiel umfasst fünf Slices, also fünf Tasks in Batch. Wenn die **concurrency**-Eigenschaft im JSON-Code der Pipeline in der Data Factory auf **5** und **Max. Tasks pro VM** im Batch-Pool mit **zwei** VMs auf **2** festgelegt ist, werden die Tasks schnell ausgeführt. (Prüfen Sie die Start- und Endzeit von Tasks.)

Verwenden Sie das Portal, um den Batch-Auftrag und die den Slices zugeordneten Tasks anzuzeigen und zu ermitteln, auf welchem virtuellen Computer die Slices jeweils ausgeführt wurden.

![Tasks im Batch-Auftrag](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Debuggen der Pipeline
Das Debuggen umfasst einige grundlegende Verfahren.

1. Wenn der Eingabeslice nicht auf **Bereit** festgelegt ist, stellen Sie sicher, dass die Struktur des Eingabeordners ordnungsgemäß und die Datei „file.txt“ im Eingabeordner vorhanden ist.

   ![Eingabeordnerstruktur](./media/data-factory-data-processing-using-batch/image3.png)

1. Verwenden Sie in der **Execute-Method** e der benutzerdefinierten Aktivität das **IActivityLogger-Objekt**, um Informationen zu protokollieren, die beim Behandeln von Problemen hilfreich sind. Die protokollierten Nachrichten werden in der Datei \_user_0.log angezeigt.

   Klicken Sie auf dem Blatt **OutputDataset** auf den Slice, um das Blatt **Datenslice** für diesen Slice anzuzeigen. Unter **Aktivitätsausführungen** sollte genau eine Aktivitätsausführung für den Slice angezeigt werden. Wenn Sie auf der Befehlsleiste auf **Ausführen** klicken, können Sie für den gleichen Slice eine weitere Aktivität ausführen.

   Wenn Sie auf die Aktivitätsausführung klicken, wird das Blatt **Aktivitätsausführung – Details** mit einer Protokolldateienliste angezeigt. Protokollierte Nachrichten werden in der Datei „user\_0.log“ angezeigt. Tritt ein Fehler auf, werden drei Aktivitätsausführungen angezeigt, da die Anzahl von Wiederholungsversuchen in der Pipeline oder Aktivitäts-JSON auf „3“ festgelegt ist. Wenn Sie die Aktivitätsausführung auswählen, werden die Protokolldateien angezeigt, die Sie zum Behandeln von Fehlern überprüfen können.

   ![Blätter „OutputDataset“ und „Datenslice“](./media/data-factory-data-processing-using-batch/image18.png)

   Wählen Sie in der Liste der Protokolldateien **user-0.log** aus. Im rechten Bereich werden die Ergebnisse der Verwendung der **IActivityLogger.Write** -Methode angezeigt.

   ![Blatt „Aktivitätsausführung – Details“](./media/data-factory-data-processing-using-batch/image19.png)

   Prüfen Sie die Datei „system-0.log“ auf Systemfehlermeldungen und -ausnahmen.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Schließen Sie die **PDB-Datei** in die ZIP-Datei ein, sodass in den Fehlerdetails Informationen wie etwa die Aufrufliste zur Verfügung stehen, wenn ein Fehler auftritt.

1. Alle Dateien in der ZIP-Datei für die benutzerdefinierte Aktivität müssen sich auf der obersten Ebene befinden und dürfen keine Unterordner haben.

   ![Liste der ZIP-Dateien der benutzerdefinierten Aktivität](./media/data-factory-data-processing-using-batch/image20.png)

1. Stellen Sie sicher, dass **assemblyName** (MyDotNetActivity.dll), **entryPoint**(MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) und **packageLinkedService** (muss auf den Blob Storage verweisen, der die ZIP-Datei enthält) auf die richtigen Werte festgelegt sind.

1. Wenn Sie einen Fehler behoben haben und den Slice erneut verarbeiten möchten, klicken Sie auf dem Blatt **OutputDataset** mit der rechten Maustaste auf den Slice, und klicken Sie anschließend auf **Ausführen**.

   ![Blatt „OutputDataset“, Option „Ausführen“](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > In Ihrem Blob Storage finden Sie einen Container mit dem Namen `adfjobs`. Dieser Container wird nicht automatisch gelöscht. Sie können ihn jedoch nach dem Testen der Lösung problemlos löschen. Ebenso erstellt die Data Factory-Lösung einen Batch-Auftrag mit dem Namen `adf-\<pool ID/name\>:job-0000000001`. Sie können diesen Auftrag bei Belieben löschen, nachdem Sie die Lösung getestet haben.
   >
   >
1. Die benutzerdefinierte Aktivität verwendet nicht die Datei **app.config** aus dem Paket. Wenn Ihr Code also Verbindungszeichenfolgen aus der Konfigurationsdatei liest, funktioniert er während der Laufzeit nicht. Die bewährte Methode bei Verwendung von Batch ist das Aufbewahren von Geheimnissen in Azure Key Vault. Verwenden Sie dann einen zertifikatbasierten Dienstprinzipal, um den Schlüsseltresor zu schützen und das Zertifikat an den Batch-Pool zu verteilen. Die benutzerdefinierte .NET-Aktivität kann zur Laufzeit auf Geheimnisse im Schlüsseltresor zugreifen. Dabei handelt es sich um eine allgemeine Lösung, die an jede Art von Geheimnis angepasst werden kann, nicht nur an eine Verbindungszeichenfolge.

    Es gibt eine einfachere Problemumgehung, die aber keine bewährte Methode darstellt. Sie können einen mit einer SQL-Datenbank verknüpften Dienst mit Einstellungen für Verbindungszeichenfolgen erstellen. Anschließend können Sie ein Dataset erstellen, das den verknüpften Dienst verwendet und das Dataset als Dummy-Eingabedataset mit der benutzerdefinierten.NET-Aktivität verkettet. Sie können anschließend auf die Verbindungszeichenfolge des verknüpften Diensts im Code der benutzerdefinierten Aktivität zugreifen. Dies sollte zur Laufzeit problemlos funktionieren.  

#### <a name="extend-the-sample"></a>Erweitern des Beispiels
Sie können dieses Beispiel erweitern, um mehr über Data Factory- und Batch-Funktionen zu erfahren. Gehen Sie zum Verarbeiten von Slices in einem anderen Zeitbereich wie folgt vor:

1. Fügen Sie die folgenden Unterordner in `inputfolder` hinzu: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 und 2015-11-16-09. Platzieren Sie in diesen Ordnern Eingabedateien. Ändern Sie die Endzeit für die Pipeline von `2015-11-16T05:00:00Z` zu `2015-11-16T10:00:00Z`. Doppelklicken Sie in der **Diagrammansicht** auf **InputDataset**, und vergewissern Sie sich, dass die Eingabeslices bereit sind. Doppelklicken Sie auf **OuptutDataset**, um den Status der Ausgabeslices anzeigen. Wenn der Status **Bereit** lautet, überprüfen Sie den Ausgabeordner für die Ausgabedateien.

1. Erhöhen oder verringern Sie die **concurrency**-Einstellung, um zu verstehen, wie sie sich auf die Leistung Ihrer Lösung, insbesondere auf die in Azure Batch erfolgte Verarbeitung, auswirkt. Weitere Informationen zur Einstellung **Parallelität** finden Sie unter „Schritt 4: Erstellen und Ausführen der Pipeline mit einer benutzerdefinierten Aktivität“.

1. Erstellen Sie einen Pool mit einer höheren/niedrigeren **maximalen Anzahl an Aufgaben pro virtueller Maschine**. Aktualisieren Sie den mit Azure Batch verknüpften Dienst in der Data Factory-Lösung, um den neu erstellten Anwendungspool zu verwenden. Weitere Informationen zur Einstellung für die **maximalen Tasks pro virtuellem Computer** finden Sie unter „Schritt 4: Erstellen und Ausführen der Pipeline mit einer benutzerdefinierten Aktivität“.

1. Erstellen Sie einen Batch-Pool mit **automatischer Skalierung**. Das automatische Skalieren von Computeknoten in einem Batch-Pool ist die dynamische Anpassung der Verarbeitungsleistung, die von der Anwendung beansprucht wird.

    Mit dieser Beispielformel wird folgendes Verhalten erreicht. Nachdem der Pool erstellt wurde, wird er mit einer VM gestartet. Die Metrik „$PendingTasks“ legt die Anzahl der Tasks im ausgeführten und im aktiven (in der Warteschlange) Zustand fest. Die Formel sucht nach der durchschnittlichen Anzahl ausstehender Aufgaben in den letzten 180 Sekunden und legt TargetDedicated auf den entsprechenden Wert fest. Dadurch wird sichergestellt, dass TargetDedicated nie die Anzahl von 25 virtuellen Computern überschreitet. Wenn neue Aufgaben gesendet werden, wächst der Pool automatisch an. Sobald Aufgaben abgeschlossen sind, werden VMs nacheinander freigegeben, und ihre Anzahl wird durch die automatische Skalierung verringert. Werte für „startingNumberOfVMs“ und „maxNumberofVMs“ können entsprechend den jeweiligen Anforderungen angepasst werden.

    Formel für die automatische Skalierung:

    ```
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Weitere Informationen finden Sie unter [Automatisches Skalieren von Computeknoten in einem Batch-Pool](../../batch/batch-automatic-scaling.md).

   Wenn der Pool die Standardeinstellung für [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale) verwendet, kann es 15 bis 30 Minuten dauern, bis der Batch-Dienst den virtuellen Computer vorbereitet hat und die benutzerdefinierte Aktivität ausgeführt wird. Wenn der Pool eine andere Einstellung für „autoScaleEvaluationInterval“ nutzt, kann der Batch-Dienst „autoScaleEvaluationInterval“ plus 10 Minuten verwenden.

1. In der Beispiellösung der **Execute**-Methode ruft die **Calculate** -Methode auf, die einen Eingabedatenslice verarbeiten, um einen Ausgabedatenslice zu erzeugen. Sie können eine eigene Methode erstellen, um Eingabedaten zu verarbeiten und den Aufruf der **Calculate**-Methode in der **Execute**-Methode durch einen Aufruf Ihrer Methode zu ersetzen.

### <a name="next-steps-consume-the-data"></a>Nächste Schritte: Nutzung der Daten
Nachdem Sie Daten verarbeitet haben, können Sie sie mit Onlinetools wie Power BI nutzen. Hier erfahren Sie mehr über Power BI und die Verwendung in Azure:

* [Untersuchen eines Datasets in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Erste Schritte mit Power BI Desktop](/power-bi/fundamentals/desktop-getting-started)
* [Aktualisieren von Daten in Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure und Power BI: Grundlegende Übersicht](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>References
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Einführung in den Data Factory-Dienst](data-factory-introduction.md)
  * [Erste Schritte mit Data Factory](data-factory-build-your-first-pipeline.md)
  * [Verwenden von benutzerdefinierten Aktivitäten in der Data Factory-Pipeline](data-factory-use-custom-activities.md)
* [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

  * [Grundlegendes zu Azure Batch](../../azure-sql/database/sql-database-paas-overview.md)
  * [Übersicht über Batch-Funktionen](../../batch/batch-service-workflow-features.md)
  * [Erstellen und Verwalten eines Batch-Kontos im Azure-Portal](../../batch/batch-account-create-portal.md)
  * [Erste Schritte mit der Batch-Clientbibliothek für .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: /archive/blogs/windowshpc/azure-batch-explorer-sample-walkthrough