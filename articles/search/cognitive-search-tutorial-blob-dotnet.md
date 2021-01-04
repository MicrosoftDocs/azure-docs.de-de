---
title: C#-Tutorial zur Verwendung von KI in Azure-Blobs
titleSuffix: Azure Cognitive Search
description: Hier wird Schritt für Schritt ein Beispiel für die Textextraktion und die Verarbeitung natürlicher Sprache anhand des Inhalts von Blobspeicher mit C# beschrieben, indem Azure Cognitive Search und das .NET SDK verwendet werden.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: da7a80842bec68fde8cc44401bb04c2dd061741f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787957"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Tutorial: Durch KI generierter durchsuchbarer Inhalt aus Azure-Blobs mit dem .NET SDK

Wenn Sie in Azure Blob Storage über unstrukturierten Text oder Bilder verfügen, können Sie mithilfe einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) Informationen extrahieren und neue Inhalte erstellen, die für die Volltextsuche oder in Knowledge Mining-Szenarios verwendet werden können. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Entwicklungsumgebung
> * Definieren einer Pipeline für Suchen über Blobs mithilfe der optischen Zeichenerkennung, Spracherkennung sowie Entitäts- und Schlüsselworterkennung
> * Ausführen der Pipeline zum Aufrufen von Transformationen sowie zum Erstellen und Laden eines Suchindexes
> * Erkunden Sie Ergebnisse per Volltextsuche und umfangreicher Abfragesyntax.

Sollten Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="overview"></a>Übersicht

In diesem Tutorial werden C# und die Clientbibliothek **Azure.Search.Documents** verwendet, um eine Datenquelle, einen Index, einen Indexer und ein Skillset zu erstellen.

Das Skillset verwendet integrierte Skills, die auf Cognitive Services-APIs basieren. Die Schritte in der Pipeline umfassen die optische Zeichenerkennung (OCR) für Bilder, die Spracherkennung für Text sowie die Schlüsselwortextraktion und die Entitätserkennung (Organisationen). Neue Informationen werden in neuen Feldern gespeichert, die Sie in Abfragen, Facets und Filtern nutzen können.

## <a name="prerequisites"></a>Voraussetzungen

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [NuGet-Paket „Azure.Search.Documents 11.x“](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> In diesem Tutorial können Sie den kostenlosen Suchdienst verwenden. Der kostenlose Suchdienst ist auf drei Indizes, drei Indexer und drei Datenquellen beschränkt. In diesem Tutorial wird davon jeweils eine Instanz erstellt. Vergewissern Sie sich zunächst, dass Ihr Dienst über genügend freie Kapazität für die neuen Ressourcen verfügt.

## <a name="download-sample-data"></a>Herunterladen von Beispieldaten

Die Beispieldaten bestehen aus 14 Dateien mit gemischten Inhaltstypen, die Sie in einem späteren Schritt in Azure Blob Storage hochladen.

1. Öffnen Sie [diesen OneDrive-Ordner](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4), und klicken Sie links oben auf **Herunterladen** , um die Dateien auf Ihren Computer zu kopieren. 

1. Klicken Sie mit der rechten Maustaste auf die ZIP-Datei, und wählen Sie **Alle extrahieren** aus. Es stehen 14 Dateien verschiedener Art zur Verfügung. In dieser Übung werden sieben davon verwendet.

Sie können auch den Quellcode für dieses Tutorial herunterladen. Der Quellcode befindet sich im Ordner **tutorial-ai-enrichment/v11** im Repository [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1\. Erstellen der Dienste

In diesem Tutorial werden Azure Cognitive Search für Indizierungsvorgänge und Abfragen, Cognitive Services auf dem Back-End für die KI-Anreicherung und Azure Blob Storage für die Datenbereitstellung verwendet. Dieses Tutorial bleibt unter der kostenlosen Zuteilung von 20 Transaktionen pro Indexer pro Tag für Cognitive Services. Aus diesem Grund müssen Sie nur den Such- und den Speicherdienst erstellen.

Erstellen Sie diese beiden Dienste nach Möglichkeit in derselben Region und Ressourcengruppe, um eine möglichst große Nähe zu erreichen und die Verwaltung zu vereinfachen. In der Praxis kann sich Ihr Azure Storage-Konto in einer beliebigen Region befinden.

### <a name="start-with-azure-storage"></a>Azure Storage

1. [Melden Sie sich beim Azure-Portal](https://portal.azure.com/) an, und klicken Sie auf **+ Ressource erstellen** .

1. Suchen Sie nach *Speicherkonto* , und wählen Sie das Speicherkonto-Angebot von Microsoft aus.

   ![Erstellen eines Speicherkontos](media/cognitive-search-tutorial-blob/storage-account.png "Speicherkonto erstellen")

1. Auf der Registerkarte „Grundlagen“ sind folgende Angaben erforderlich. Übernehmen Sie bei allen anderen Optionen die Standardeinstellungen.

   * **Ressourcengruppe** . Sie können entweder eine vorhandene Ressourcengruppe auswählen oder eine neue Ressourcengruppe erstellen. Verwenden Sie jedoch für alle Dienste die gleiche Gruppe, um die Dienste gemeinsam verwalten zu können.

   * **Speicherkontoname** : Falls Sie über mehrere Ressourcen des gleichen Typs verfügen, geben Sie zur Unterscheidung den Typ und die Region an (Beispiel: *blobstoragewestus* ). 

   * **Standort** . Wählen Sie nach Möglichkeit den gleichen Standort aus, der auch für Azure Cognitive Search und Cognitive Services verwendet wird. Bei Verwendung eines einzelnen Standorts fallen keine Bandbreitengebühren an.

   * **Kontoart** : Verwenden Sie die Standardeinstellung *StorageV2 (allgemein, Version 2)* .

1. Klicken Sie zum Erstellen des Diensts auf **Überprüfen + erstellen** .

1. Klicken Sie nach der Erstellung auf **Go to the resource** (Zur Ressource), um die Übersichtsseite zu öffnen.

1. Klicken Sie auf den Dienst **Blobs** .

1. Klicken Sie auf **+ Container** , um einen Container zu erstellen, und nennen Sie ihn *cog-search-demo* .

1. Wählen Sie *cog-search-demo* aus, und klicken Sie auf **Hochladen** , um den Ordner zu öffnen, in dem Sie die Downloaddateien gespeichert haben. Wählen Sie alle 14 Dateien aus, und klicken Sie auf **OK** , um den Upload durchzuführen.

   ![Hochladen der Beispieldateien](media/cognitive-search-quickstart-blob/sample-data.png "Hochladen der Beispieldateien")

1. Rufen Sie eine Verbindungszeichenfolge ab, bevor Sie Azure Storage verlassen, um in Azure Cognitive Search eine Verbindung herstellen zu können. 

   1. Wechseln Sie zurück zur Übersichtsseite Ihres Speicherkontos. (Wir haben *blobstoragewestus* als Beispiel verwendet.) 

   1. Wählen Sie im linken Navigationsbereich die Option **Zugriffsschlüssel** aus, und kopieren Sie eine der Verbindungszeichenfolgen. 

   Die Verbindungszeichenfolge ist eine URL, die in etwa wie folgt aussieht:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Speichern Sie die Verbindungszeichenfolge im Editor. Sie wird später bei der Einrichtung der Datenquellenverbindung benötigt.

### <a name="cognitive-services"></a>Cognitive Services

Die KI-Anreicherung basiert auf Cognitive Services (einschließlich Textanalyse und maschinellem Sehen für die Verarbeitung von natürlicher Sprache und Bildern). Bei der Erstellung eines echten Prototyps oder Projekts würden Sie an dieser Stelle Cognitive Services bereitstellen (in der gleichen Region wie Azure Cognitive Search), um eine Verknüpfung mit Indizierungsvorgängen zu ermöglichen.

In dieser Übung können Sie die Ressourcenbereitstellung allerdings überspringen, da Azure Cognitive Search im Hintergrund eine Verbindung mit Cognitive Services herstellen kann und 20 kostenlose Transaktionen pro Indexerausführung ermöglicht. Da in diesem Tutorial 14 Transaktionen verwendet werden, ist die kostenlose Zuteilung ausreichend. Planen Sie bei umfangreicheren Projekten die Bereitstellung von Cognitive Services im S0-Tarif (nutzungsbasierte Bezahlung). Weitere Informationen finden Sie unter [Anfügen einer Cognitive Services-Ressource an eine Qualifikationsgruppe in Azure Search](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Azure Cognitive Search ist die dritte Komponente, die Sie [im Portal erstellen](search-create-service-portal.md) können, oder Sie [suchen nach einem vorhandenen Suchdienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in Ihrem Abonnement.

Im Rahmen dieser exemplarischen Vorgehensweise können Sie den Free-Tarif verwenden. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Kopieren eines Administrator-API-Schlüssels und einer URL für Azure Cognitive Search

Für die Interaktion mit dem Azure Cognitive Search-Dienst benötigen Sie die Dienst-URL und einen Zugriffsschlüssel. Ein Suchdienst wird mit beidem erstellt. Gehen Sie daher wie folgt vor, um die erforderlichen Informationen zu erhalten, falls Sie Azure Cognitive Search Ihrem Abonnement hinzugefügt haben:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Kopieren Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   Rufen Sie auch den Abfrageschlüssel ab. Es empfiehlt sich, Abfrageanforderungen mit schreibgeschütztem Zugriff auszugeben.

   ![Abrufen des Dienstnamens sowie der Administrator- und Abfrageschlüssel](media/search-get-started-javascript/service-name-and-keys.png)

Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="2---set-up-your-environment"></a>2\. Einrichten Ihrer Umgebung

Öffnen Sie zunächst Visual Studio, und erstellen Sie ein neues Konsolen-App-Projekt, das mit .NET Core ausgeführt werden kann.

### <a name="install-azuresearchdocuments"></a>Installieren von Azure.Search.Documents

Das [.NET SDK für Azure Cognitive Search](/dotnet/api/overview/azure/search) umfasst eine Clientbibliothek, mit der Sie sowohl Ihre Indizes, Datenquellen, Indexer und Skillsets verwalten als auch Dokumente hochladen und verwalten sowie Abfragen ausführen können, ohne die Details von HTTP und JSON berücksichtigen zu müssen. Diese Clientbibliothek wird als NuGet-Paket bereitgestellt.

Installieren Sie für dieses Projekt Version 11 oder höher von `Azure.Search.Documents` sowie die neueste Version von `Microsoft.Extensions.Configuration`.

1. Klicken Sie in Visual Studio auf **Extras** > **NuGet-Paket-Manager** > **NuGet-Pakete für Projektmappe verwalten...** .

1. Suchen Sie nach [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Wählen Sie die neueste Version aus, und klicken Sie auf **Installieren** .

1. Wiederholen Sie die vorherigen Schritte, um [Microsoft.Extensions.Configuration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) und [Microsoft.Extensions.Configuration.Json](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) zu installieren.

### <a name="add-service-connection-information"></a>Hinzufügen von Informationen zur Dienstverbindung

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus. 

1. Geben Sie der Datei den Namen `appsettings.json`, und wählen Sie **Hinzufügen** aus. 

1. Fügen Sie diese Datei in Ihr Ausgabeverzeichnis ein.
    1. Klicken Sie mit der rechten Maustaste auf `appsettings.json`, und wählen Sie **Eigenschaften** aus. 
    1. Ändern Sie den Wert von **In Ausgabeverzeichnis kopieren** in **Kopieren, wenn neuer** .

1. Kopieren Sie den folgenden JSON-Code in die neue JSON-Datei.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Fügen Sie Ihren Suchdienst und die Informationen Ihres Blob Storage-Kontos hinzu. Denken Sie daran, dass Sie diese Informationen über die Schritte zur Dienstbereitstellung im vorherigen Abschnitt erhalten können.

Geben Sie für **SearchServiceUri** die vollständige URL ein.

### <a name="add-namespaces"></a>Hinzufügen von Namespaces

Fügen Sie in `Program.cs` die folgenden Namespaces hinzu.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Erstellen eines Clients

Erstellen Sie unter `Main` eine Instanz der `SearchIndexClient`- und der `SearchIndexerClient`-Klasse.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Die Clients stellen eine Verbindung mit Ihrem Suchdienst her. Sie sollten nach Möglichkeit nur eine Instanz in Ihrer Anwendung freigeben, um zu vermeiden, dass zu viele Verbindungen geöffnet werden. Die Methoden sind threadsicher und ermöglichen diese Freigabe.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Hinzufügen einer Funktion zum Beenden des Programms während eines Fehlers

Dieses Tutorial soll die einzelnen Schritte der Indizierungspipeline veranschaulichen. Bei einem kritischen Problem, das verhindert, dass das Programm die Datenquelle, das Skillset, den Index oder den Indexer erstellt, wird eine Fehlermeldung ausgegeben und das Programm beendet, sodass der Fehler ermittelt und behoben werden kann.

Fügen Sie `ExitProgram` zu `Main` hinzu, um Szenarien zu behandeln, in denen das Programm beendet werden muss.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3\. Erstellen der Pipeline

In Azure Cognitive Search erfolgt die KI-Verarbeitung während der Indizierung (oder Datenerfassung). In diesem Teil der exemplarischen Vorgehensweise werden vier Objekte erstellt: Datenquelle, Indexdefinition, Skillset und Indexer. 

### <a name="step-1-create-a-data-source"></a>Schritt 1: Erstellen einer Datenquelle

`SearchIndexerClient` verfügt über eine [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename)-Eigenschaft, die Sie auf ein `SearchIndexerDataSourceConnection`-Objekt festlegen können. Dieses Objekt stellt alle Methoden bereit, die Sie benötigen, um Azure Cognitive Search-Datenquellen zu erstellen, aufzulisten, zu aktualisieren oder zu löschen.

Erstellen Sie eine neue `SearchIndexerDataSourceConnection`-Instanz, indem Sie `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` aufrufen. Der folgende Code erstellt eine Datenquelle vom Typ `AzureBlob`.

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Bei einer erfolgreichen Anforderung gibt die Methode die Datenquelle zurück, die erstellt wurde. Wenn ein Problem mit der Anforderung auftritt, z. B. ein ungültiger Parameter vorhanden ist, löst die Methode eine Ausnahme aus.

Fügen Sie nun unter `Main` eine Zeile zum Aufrufen der Funktion `CreateOrUpdateDataSource` hinzu, die Sie gerade hinzugefügt haben.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Erstellen Sie das Projekt, und führen Sie es aus. Da es sich hier um Ihre erste Anforderung handelt, überprüfen Sie im Azure-Portal, ob die Datenquelle in Azure Cognitive Search erstellt wurde. Überprüfen Sie auf der Übersichtsseite des Suchdiensts, ob die Liste „Datenquellen“ ein neues Element enthält. Möglicherweise müssen Sie einige Minuten warten, bis die Portalseite aktualisiert wurde.

  ![Datenquellenkachel im Portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Datenquellenkachel im Portal")

### <a name="step-2-create-a-skillset"></a>Schritt 2: Erstellen eines Skillsets

In diesem Abschnitt definieren Sie eine Reihe von Anreicherungsschritten, die Sie auf Ihre Daten anwenden möchten. Jeder Anreicherungsschritt wird als *Skill* und die Anreicherungsschritte zusammen als *Skillset* bezeichnet. In diesem Tutorial werden für die Qualifikationsgruppe [integrierte kognitive Qualifikationen](cognitive-search-predefined-skills.md) verwendet:

* Mit der Qualifikation für die [optische Zeichenerkennung](cognitive-search-skill-ocr.md) (OCR) wird gedruckter und handschriftlicher Text in Bilddateien erkannt.

* Mit der [Textzusammenführung](cognitive-search-skill-textmerger.md) wird Text aus einer Sammlung von Feldern in einem einzigen Feld konsolidiert.

* [Spracherkennung](cognitive-search-skill-language-detection.md), um die Sprache der Inhalte zu bestimmen.

* Mit der [Textaufteilung](cognitive-search-skill-textsplit.md) werden große Inhalte vor dem Aufrufen der Qualifikationen zur Schlüsselbegriffserkennung und Entitätserkennung in kleinere Teile aufgeteilt. Für die Schlüsselbegriffserkennung und Entitätserkennung werden Eingaben von maximal 50.000 Zeichen akzeptiert. Für einige der Beispieldateien ist eine Aufteilung erforderlich, um diesen Grenzwert zu erfüllen.

* [Entitätserkennung](cognitive-search-skill-entity-recognition.md), um die Namen von Organisationen aus Inhalten im Blobcontainer zu extrahieren.

* [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), um die wichtigsten Schlüsselbegriffe herauszuziehen.

Während der ersten Verarbeitung entschlüsselt Azure Cognitive Search jedes Dokument, um die Inhalte aus verschiedenen Dateiformaten zu extrahieren. Aus der Quelldatei stammender Text wird für jedes Dokument jeweils im generierten Feld `content` gespeichert. Legen Sie daher die Eingabe auf `"/document/content"` fest, um diesen Text zu verwenden. Bildinhalte werden in einem generierten `normalized_images`-Feld platziert, das in einem Skillset als `/document/normalized_images/*` angegeben wird.

Ausgaben können einem Index zugeordnet, als Eingabe einer Downstream-Qualifikation verwendet oder in beider Weise zugleich eingesetzt werden, wie etwa bei Sprachcode. Im Index ist ein Sprachcode zu Filterungszwecken nützlich. Als Eingabe wird ein Sprachcode von Qualifikationen zur Textanalyse verwendet, um die Linguistikregeln über Wörtertrennung zu informieren.

Weitere Informationen zu den Grundlagen von Qualifikationsgruppen finden Sie unter [How to define a skillset](cognitive-search-defining-skillset.md) (Definieren von Qualifikationsgruppen).

### <a name="ocr-skill"></a>OCR-Qualifikation

Die Qualifikation **OCR** extrahiert Text aus Bildern. Bei dieser Qualifikation wird davon ausgegangen, dass ein Feld „normalized_images“ vorhanden ist. Um dieses Feld zu generieren, legen Sie später in diesem Tutorial die ```"imageAction"```-Konfiguration in der Indexerdefinition auf ```"generateNormalizedImages"``` fest.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Qualifikation: Zusammenführung

In diesem Abschnitt erstellen Sie einen Skill für das **Zusammenführen** , mit dem das Feld mit dem Dokumentinhalt mit dem vom OCR-Skill erstellten Text zusammengeführt wird.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Qualifikation „Sprachenerkennung“

Die Qualifikation **Sprachenerkennung** erkennt die Sprache von Eingabetexten und meldet einen einzigen Sprachcode für jedes Dokument, das mit der Anforderung übermittelt wurde. Wir verwenden die Ausgabe der Qualifikation **Sprachenerkennung** als Teil der Eingabe für die Qualifikation **Textaufteilung** .

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Qualifikation „Textaufteilung“

Die folgende Qualifikation **Textaufteilung** teilt Text nach Seiten und beschränkt die Seitenlänge auf 4.000 Zeichen, die mit `String.Length` gemessen werden. Der Algorithmus versucht, den Text in Blöcke aufzuteilen, die höchstens `maximumPageLength` groß sind. Dabei versucht der Algorithmus, Sätze an Satzgrenzen zu teilen, sodass die Größe der Blöcke etwas kleiner als `maximumPageLength` sein kann.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Qualifikation „Entitätserkennung“

Diese `EntityRecognitionSkill`-Instanz ist auf den Erkennungskategorietyp `organization` festgelegt. Die Qualifikation **Entitätserkennung** kann auch die Kategorientypen `person` und `location` erkennen.

Beachten Sie, dass das Feld „context“ mit einem Sternchen auf ```"/document/pages/*"``` festgelegt ist, d. h. der Anreicherungsschritt wird für jede Seite unter ```"/document/pages"``` aufgerufen.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Qualifikation „Schlüsselbegriffserkennung“

Wie die `EntityRecognitionSkill`-Instanz, die gerade erstellt wurde, wird auch die Qualifikation **Schlüsselbegriffserkennung** für jede Seite des Dokuments aufgerufen.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Aufbauen und Erstellen der Qualifikationsgruppe

Erstellen Sie die `Skillset` mithilfe der Qualifikationen, die Sie erstellt haben.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Fügen Sie `Main` die folgenden Zeilen hinzu.

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>Schritt 3: Erstellen eines Index

In diesem Abschnitt definieren Sie das Indexschema, indem Sie angeben, welche Felder in den durchsuchbaren Index aufgenommen und mit welchen Suchattributen die einzelnen Felder versehen werden sollen. Felder besitzen einen Typ und können Attribute annehmen, die bestimmen, wie das Feld verwendet wird (durchsuchbar, sortierbar usw.). Feldname in einem Index müssen nicht exakt mit den Feldnamen in der Quelle übereinstimmen. In einem späteren Schritt fügen Sie in einem Indexer Feldzuordnungen hinzu, um die Quell- und Zielfelder zu verbinden. Definieren Sie für diesen Schritt den Index mit Feldbenennungskonventionen, die für Ihre Suchanwendung angemessen sind.

In dieser Übung werden die folgenden Felder und Feldtypen verwendet:

| Feldnamen | Feldtypen |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>Erstellen der DemoIndex-Klasse

Die Felder für diesen Index werden mithilfe einer Modellklasse definiert. Jede Eigenschaft der Modellklasse verfügt über Attribute, die das suchbezogene Verhalten des entsprechenden Indexfelds bestimmen. 

Wir fügen die Modellklasse einer neuen C#-Datei hinzu. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** > **Neues Element** und dann „Klasse“ aus. Geben Sie der Datei den Namen `DemoIndex.cs`, und wählen Sie dann **Hinzufügen** aus.

Geben Sie unbedingt auch an, dass Sie die Typen aus den Namespaces `Azure.Search.Documents.Indexes` und `System.Text.Json.Serialization` verwenden möchten.

Fügen Sie `DemoIndex.cs` die folgende Modellklassendefinition hinzu, und fügen Sie sie in denselben Namespace ein, in dem Sie den Index erstellen.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Nach dem Definieren einer Modellklasse können Sie nun in `Program.cs` ganz problemlos eine Indexdefinition erstellen. Der Name für diesen Index lautet `demoindex`. Falls bereits ein Index mit diesem Namen vorhanden ist, wird er gelöscht.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Während der Tests stellen Sie möglicherweise fest, dass Sie mehr als einmal versuchen, den Index zu erstellen. Aus diesem Grund sollten Sie vor dem Versuch, den Index zu erstellen, überprüfen, ob er nicht bereits vorhanden ist.

Fügen Sie `Main` die folgenden Zeilen hinzu.

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Fügen Sie die folgende using-Anweisung hinzu, um den mehrdeutigen Verweis zu korrigieren.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Weitere Informationen zu Indexkonzepten finden Sie unter [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Schritt 4: Erstellen und Ausführen eines Indexers

Bisher haben Sie eine Datenquelle, eine Qualifikationsgruppe und einen Index erstellt. Diese drei Komponenten werden Teil eines [Indexers](search-indexer-overview.md), der jedes einzelne Stück per Pull in einen einzelnen mehrstufigen Vorgang herunterlädt. Um diese in einem Indexer zusammenzuführen, müssen Sie Feldzuordnungen definieren.

* fieldMappings werden vor der Qualifikationsgruppe verarbeitet, und die Quellfelder der Datenquelle werden Zielfeldern in einem Index zugeordnet. Wenn die Feldnamen und -typen auf beiden Seiten gleich sind, ist keine Zuordnung erforderlich.

* outputFieldMappings werden nach der Qualifikationsgruppe verarbeitet. Es wird auf nicht vorhandene sourceFieldNames verwiesen, bis diese per Dokumententschlüsselung oder Anreicherung erstellt werden. targetFieldName ist ein Feld in einem Index.

Neben dem Verknüpfen von Ein- und Ausgaben können Sie auch Feldzuordnungen nutzen, um Datenstrukturen zu vereinfachen. Weitere Informationen finden Sie unter [Zuordnen angereicherter Felder zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Fügen Sie `Main` die folgenden Zeilen hinzu.

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Die Indexerverarbeitung nimmt erwartungsgemäß einige Zeit in Anspruch. Das Dataset ist zwar klein, Analysequalifikationen sind aber rechenintensiv. Einige Qualifikationen, wie etwa die Bildanalyse, weisen lange Ausführungszeiten auf.

> [!TIP]
> Die Pipeline wird durch Erstellen eines Indexers aufgerufen. Wenn beim Zugriff auf die Daten, dem Zuordnen von Ein- und Ausgaben oder der Reihenfolge der Vorgänge Probleme bestehen, äußern sie sich in dieser Phase.

### <a name="explore-creating-the-indexer"></a>Untersuchen der Indexererstellung

Der Code legt `"maxFailedItems"` auf -1 fest, womit die Index-Engine angewiesen wird, Fehler beim Datenimport zu ignorieren. Dies ist nützlich, weil die Demodatenquelle nur wenige Dokumente enthält. Für eine größere Datenquelle sollten Sie den Wert größer als 0 festlegen.

Beachten Sie auch, dass `"dataToExtract"` auf `"contentAndMetadata"` festgelegt ist. Diese Anweisung weist den Indexer an, die Inhalte aus verschiedenen Dateiformaten sowie die den einzelnen Dateien zugeordneten Metadaten automatisch zu extrahieren.

Wenn die Inhalte extrahiert werden, können Sie `imageAction` darauf festlegen, Text aus in der Datenquelle gefundenen Bildern zu extrahieren. Die Festlegung von `"imageAction"` auf die Konfiguration `"generateNormalizedImages"` in Kombination mit den Qualifikationen OCR und Textzusammenführung weist den Indexer an, Text aus den Bildern zu extrahieren (beispielsweise das Wort „Stop“ aus einem Stoppschild) und ihn als Teil des Inhaltsfelds einzubetten. Dieses Verhalten betrifft sowohl die in den Dokumenten eingebetteten Bilder (denken Sie etwa an Bilder in PDF-Dateien) als auch die in der Datenquelle gefundenen Bilder, z.B. eine JPG-Datei.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4\. Überwachen der Indizierung

Nachdem der Indexer definiert wurde, wird er automatisch ausgeführt, wenn Sie die Anforderung senden. Abhängig von den kognitiven Qualifikationen, die Sie definiert haben, kann die Indizierung länger als erwartet dauern. Um herauszufinden, ob der Indexer noch ausgeführt wird, verwenden Sie die `GetStatus`-Methode.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` stellt den aktuellen Status und den Ausführungsverlauf eines Indexers dar.

Warnungen sind bei bestimmten Kombinationen aus Quelldatei und Qualifikation häufig und weisen nicht immer auf ein Problem hin. Im Rahmen dieses Tutorials sind die Warnungen gutartig (z.B. keine Texteingaben aus den JPEG-Dateien).

Fügen Sie `Main` die folgenden Zeilen hinzu.

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5\. Suchen

In den Azure Cognitive Search-Tutorials für Konsolen-Apps wird in der Regel eine Verzögerung von zwei Sekunden vor dem Ausführen von Abfragen hinzugefügt, die Ergebnisse zurückgeben. Da die Anreicherung allerdings einige Minuten dauert, schließen Sie die Konsolen-App und verwenden stattdessen einen anderen Ansatz.

Die einfachste Option ist der [Such-Explorer](search-explorer.md) im Portal. Sie können zuerst eine leere Abfrage, die alle Dokumente zurückgibt, oder eine gezieltere Suche durchführen, die neuen, von der Pipeline erstellten Feldinhalt zurückgibt. 

1. Klicken Sie im Azure-Portal auf der Übersichtsseite für die Suche auf **Indizes** .

1. Suchen Sie in der Liste nach **`demoindex`** . Diese sollte 14 Dokumente enthalten. Wenn die Anzahl der Dokumente 0 (null) ist, wird der Indexer entweder noch ausgeführt, oder die Seite wurde noch nicht aktualisiert. 

1. Klicken Sie auf **`demoindex`** (Zur Kasse). Der Such-Explorer ist die erste Registerkarte.

1. Der Inhalt kann durchsucht werden, sobald das erste Dokument geladen wurde. Führen Sie eine Abfrage ohne Angabe durch, indem Sie auf **Suchen** klicken, um zu überprüfen, ob Inhalt vorhanden ist. Diese Abfrage gibt alle derzeit indizierten Dokumente zurück und gibt Ihnen so eine Vorstellung davon, was der Index enthält.

1. Geben Sie als Nächstes die folgende Zeichenfolge ein, um bessere Ergebnisse zu erzielen: `search=*&$select=id, languageCode, organizations`.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekte aus Azure Cognitive Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

Im Beispielcode dieses Tutorials wird eine Überprüfung auf vorhandene Objekte durchgeführt. Diese werden dann gelöscht, damit Sie Ihren Code erneut ausführen können. Sie können auch das Portal verwenden, um Indizes, Indexer, Datenquellen und Qualifikationsgruppen zu löschen.

## <a name="takeaways"></a>Wesentliche Punkte

Dieses Tutorial veranschaulicht die grundlegenden Schritte zum Erstellen einer erweiterten Indizierungspipeline durch Erstellung von Komponenten: eine Datenquelle, eine Qualifikationsgruppe, ein Index und ein Indexer.

Es wurden [integrierte Qualifikationen](cognitive-search-predefined-skills.md) im Zusammenhang mit der Skillsetdefinition und den Mechanismen zur Verkettung von Qualifikationen mithilfe von Ein- und Ausgängen vorgestellt. Sie haben darüber hinaus erfahren, dass `outputFieldMappings` in der Indexerdefinition erforderlich ist, um angereicherte Werte aus der Pipeline an einen durchsuchbaren Index in einem Azure Cognitive Search-Dienst weiterzuleiten.

Ferner haben Sie erfahren, wie die Ergebnisse getestet werden und das System für weitere Entwicklungsschritte zurückgesetzt wird. Sie haben gelernt, dass das Ausgeben von Abfragen auf den Index die von der angereicherten Indizierungspipeline erstellte Ausgabe zurückgibt. Darüber hinaus haben Sie die Überprüfung des Indexerstatus und das Löschen von Objekten vor der erneuten Ausführung einer Pipeline gelernt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie in Ihrem eigenen Abonnement arbeiten, ist es ratsam, nach Abschluss eines Projekts die nicht mehr benötigten Ressourcen zu entfernen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Sie können entweder einzelne Ressourcen oder aber die Ressourcengruppe löschen, um den gesamten Ressourcensatz zu entfernen.

Ressourcen können im Portal über den Link „Alle Ressourcen“ oder „Ressourcengruppen“ im linken Navigationsbereich gesucht und verwaltet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit allen Objekten einer KI-Anreicherungspipeline vertraut gemacht haben, können Sie sich weiter über Skillsetdefinitionen und einzelne Qualifikationen informieren.

> [!div class="nextstepaction"]
> [Erstellen eines Skillsets](cognitive-search-defining-skillset.md)