---
title: C#-Tutorial zum Aufrufen von Cognitive Services-APIs in einer Indizierungspipeline – Azure Search
description: Dieses Tutorial führt Sie schrittweise durch ein Beispiel für das Extrahieren von Daten, natürliche Sprache und KI-basierter Bildverarbeitung in der Azure Search-Indizierung für die Extrahierung und Transformation von Daten.
manager: eladz
author: MarkHeff
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: maheff
ms.openlocfilehash: 7aab7f75e6489fcaea1ecafee34823ad546a6b48
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244375"
---
# <a name="c-tutorial-call-cognitive-services-apis-in-an-azure-search-indexing-pipeline"></a>C#-Tutorial: Aufrufen von Cognitive Services-APIs in einer Azure Search-Indizierungspipeline

In diesem Tutorial lernen Sie die Mechanismen des Programmierens von Datenanreicherung in Azure Search mithilfe von *kognitiven Qualifikationen* kennen. Qualifikationen werden durch die Verarbeitung von natürlicher Sprache und Bildanalysefunktionen in Cognitive Services unterstützt. Durch die Zusammenstellung und Konfiguration von Qualifikationsgruppen können Sie Text und Textdarstellungen eines Bilds oder einer gescannten Dokumentdatei extrahieren. Sie können außerdem Sprachen, Entitäten, Schlüsselbegriffe und mehr erkennen. Das Endergebnis besteht in reichhaltigen zusätzlichen Inhalten in einem Azure Search-Index, die von einer KI-unterstützten Indizierungspipeline erstellt wurden.

In diesem Tutorial verwenden Sie das .NET SDK für die folgenden Aufgaben:

> [!div class="checklist"]
> * Erstellen einer Indizierungspipeline, die Beispieldaten auf dem Weg in einen Index anreichert
> * Anwenden von integrierten Fähigkeiten: optische Zeichenerkennung (OCR), Textzusammenführung, Spracherkennung, Textaufteilung, Entitätserkennung, Schlüsselwortextraktion
> * Erfahren Sie, wie Qualifikationen miteinander verkettet werden, indem die Eingänge den Ausgängen in einer Qualifikationsgruppe zugeordnet werden
> * Ausführen von Anforderungen und Überprüfen von Ergebnissen
> * Zurücksetzen des Index und der Indexer für die weitere Entwicklung

Die Ausgabe ist ein durchsuchbarer Volltextindex auf Azure Search. Sie können den Index mit weiteren Standardfunktionen erweitern, wie etwa [Synonymen](search-synonyms.md), [Bewertungsprofilen](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [Analysetools](search-analyzers.md) und [Filtern](search-filters.md).

In diesem Tutorial wird der kostenlose Dienst verwendet. Die Anzahl kostenloser Transaktionen ist allerdings auf 20 Dokumente pro Tag beschränkt. Falls Sie dieses Tutorial mehrmals am gleichen Tag ausführen möchten, verwenden Sie einen kleineren Dateisatz, um mehr Ausführungen zu ermöglichen.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie eine kostenpflichtige Cognitive Services-Ressource verwenden. Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial werden die folgenden Dienste, Tools und Daten verwendet. 

+ [Erstellen Sie ein Azure-Speicherkonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) zum Speichern der Beispieldaten. Stellen Sie sicher, dass sich das Speicherkonto in der gleichen Region wie Azure Search befindet.

+ Die [Beispieldaten](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) bestehen aus einem kleinen Satz Dateien verschiedenen Typs. 

+ [Installieren Sie Visual Studio](https://visualstudio.microsoft.com/) für die Verwendung als IDE.

+ [Erstellen Sie einen Azure Search-Dienst](search-create-service-portal.md), oder suchen Sie in Ihrem aktuellen Abonnement [nach einem vorhandenen Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). In diesem Tutorial können Sie einen kostenlosen Dienst verwenden.

## <a name="get-a-key-and-url"></a>Abrufen eines Schlüssels und einer URL

Für die Interaktion mit Ihrem Azure Search-Dienst benötigen Sie die Dienst-URL und einen Zugriffsschlüssel. Hierfür wird jeweils ein Suchdienst erstellt. Wenn Sie Azure Search also Ihrem Abonnement hinzugefügt haben, können Sie diese Schritte ausführen, um die erforderlichen Informationen zu erhalten:

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com/), und rufen Sie auf der Seite **Übersicht** Ihres Suchdiensts die URL ab. Ein Beispiel für einen Endpunkt ist `https://mydemo.search.windows.net`.

1. Rufen Sie unter **Einstellungen** > **Schlüssel** einen Administratorschlüssel ab, um Vollzugriff auf den Dienst zu erhalten. Es gibt zwei austauschbare Administratorschlüssel – diese wurden zum Zweck der Geschäftskontinuität bereitgestellt, falls Sie einen Rollover für einen Schlüssel durchführen müssen. Für Anforderungen zum Hinzufügen, Ändern und Löschen von Objekten können Sie den primären oder den sekundären Schlüssel verwenden.

   ![Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels](media/search-fiddler/get-url-key.png "Abrufen eines HTTP-Endpunkts und Zugriffsschlüssels")

Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

## <a name="prepare-sample-data"></a>Vorbereiten der Beispieldaten

Die Anreicherungspipeline lädt per Pull aus Azure-Datenquellen herunter. Quelldaten müssen von einem unterstützten Datenquellentyp eines [Azure Search-Indexers](search-indexer-overview.md) stammen. Azure Table Storage wird für die kognitive Suche nicht unterstützt. Für diese Übung verwenden wir Blobspeicher, um verschiedene Inhaltstypen anschaulich vorzustellen.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), navigieren Sie zu Ihrem Azure-Speicherkonto, klicken Sie auf **BLOBs** und dann auf **+ Container**.

1. [Erstellen Sie einen Blobcontainer](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) für die Beispieldaten. Sie können die öffentliche Zugriffsebene auf beliebige gültige Werte festlegen. In diesem Tutorial wird davon ausgegangen, dass der Containername „basic-demo-data-pr“ lautet.

1. Nachdem der Container erstellt wurde, öffnen Sie ihn, und wählen Sie auf der Befehlsleiste die Option **Hochladen** aus, um die [Beispieldaten](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) hochzuladen.

   ![Quelldateien in Azure Blob Storage](./media/cognitive-search-quickstart-blob/sample-data.png)

1. Nachdem die Beispieldateien geladen wurden, rufen Sie den Containernamen und eine Verbindungszeichenfolge für Ihren Blobspeicher ab. Sie können dazu im Azure-Portal zu Ihrem Speicherkonto navigieren, **Zugriffsschlüssel** auswählen und dann das Feld **Verbindungszeichenfolge** kopieren.

   Bei der Verbindungszeichenfolge sollte es sich um eine URL ähnlich dem folgenden Beispiel handeln:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

Es gibt andere Möglichkeiten zum Angeben der Verbindungszeichenfolge, etwa das Bereitstellen einer Shared Access Signature (SAS). Weitere Informationen über Anmeldeinformationen für Datenquellen finden Sie unter [Indizieren von Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#Credentials).

## <a name="set-up-your-environment"></a>Einrichten der Umgebung

Öffnen Sie zunächst Visual Studio, und erstellen Sie ein neues Konsolen-App-Projekt, das mit .NET Core ausgeführt werden kann.

### <a name="install-nuget-packages"></a>Installieren von NuGet-Paketen

Das [.NET SDK für Azure Search](https://aka.ms/search-sdk) besteht aus wenigen Clientbibliotheken, mit denen Sie sowohl Ihre Indizes, Datenquellen, Indexer und Qualifikationsgruppen verwalten als auch Dokumente hochladen und verwalten sowie Abfragen ausführen können, ohne die Details von HTTP und JSON berücksichtigen zu müssen. Alle diese Clientbibliotheken werden als NuGet-Pakete verteilt.

Für dieses Projekt müssen Sie Version 9 des NuGet-Pakets `Microsoft.Azure.Search` und das neueste NuGet-Paket `Microsoft.Extensions.Configuration.Json` installieren.

Installieren Sie das NuGet-Paket `Microsoft.Azure.Search` direkt über die Paket-Manager-Konsole in Visual Studio. Wählen Sie zum Öffnen der Paket-Manager-Konsole **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole** aus. Um den auszuführenden Befehl zu erhalten, navigieren Sie zur Seite des NuGet-Pakets [Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search), wählen Sie Version 9 aus, und kopieren Sie den Paket-Manager-Befehl. Führen Sie diesen Befehl in der Paket-Manager-Konsole aus.

Wählen Sie zum Installieren des NuGet-Pakets `Microsoft.Extensions.Configuration.Json` in Visual Studio **Extras** > **NuGet-Paket-Manager** > **NuGet-Pakete für Projektmappe verwalten** aus. Wählen Sie „Durchsuchen“ aus, und suchen Sie nach dem NuGet-Paket `Microsoft.Extensions.Configuration.Json`. Wenn Sie es gefunden haben, wählen Sie das Paket und dann Ihr Projekt aus. Vergewissern Sie sich, dass die Version die neueste stabile Version ist, und wählen Sie dann „Installieren“ aus.

## <a name="add-azure-search-service-information"></a>Hinzufügen von Azure Search-Dienstinformationen

Um eine Verbindung mit dem Azure Search-Dienst herzustellen, müssen Sie Ihrem Projekt die Suchdienstinformationen hinzufügen. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** > **Neues Element** aus. Geben Sie der Datei den Namen `appsettings.json`, und wählen Sie **Hinzufügen** aus. 

Diese Datei muss in Ihr Ausgabeverzeichnis eingefügt werden. Klicken Sie dazu mit der rechten Maustaste auf `appsettings.json`, und wählen Sie **Eigenschaften** aus. Ändern Sie den Wert von **In Ausgabeverzeichnis kopieren** in **Neuere Kopie**.

Kopieren Sie den folgenden JSON-Code in die neue JSON-Datei.

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "SearchServiceQueryApiKey": "Put your query API key here",
  "AzureBlobConnectionString": "Put your Azure Blob connection string here",
}
```

Fügen Sie Ihren Suchdienst und die Informationen Ihres Blob Storage-Kontos hinzu.

Sie können Ihre Suchdienstinformationen im Azure-Portal auf der Seite Ihres Search-Kontos abrufen. Der Kontoname wird auf der Hauptseite angezeigt, und die Schlüssel finden Sie durch Auswählen von **Schlüssel**.

Sie erhalten die Blob-Verbindungszeichenfolge, indem Sie im Azure-Portal zu Ihrem Speicherkonto navigieren, **Zugriffsschlüssel** auswählen und dann das Feld **Verbindungszeichenfolge** kopieren.

## <a name="add-namespaces"></a>Hinzufügen von Namespaces

In diesem Tutorial werden viele verschiedene Typen aus unterschiedlichen Namespaces verwendet. Um diese Typen zu verwenden, fügen Sie in `Program.cs` Folgendes hinzu.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;
```

## <a name="create-a-client"></a>Erstellen eines Clients

Erstellen Sie eine Instanz der `SearchServiceClient`-Klasse.

```csharp
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();
SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` erstellt ein neues `SearchServiceClient`-Element mit Werten, die in der Konfigurationsdatei der Anwendung (appsettings.json) gespeichert sind.

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> Die `SearchServiceClient`-Klasse verwaltet die Verbindungen mit Ihrem Suchdienst. Um zu vermeiden, dass zu viele Verbindungen geöffnet werden, sollten Sie nur eine Instanz von `SearchServiceClient` in Ihrer Anwendung freigeben, sofern dies möglich ist. Die zugehörigen Methoden sind threadsicher und ermöglichen diese Freigabe.
> 
> 

## <a name="create-a-data-source"></a>Erstellen einer Datenquelle

Erstellen Sie eine neue `DataSource`-Instanz, indem Sie `DataSource.AzureBlobStorage` aufrufen. Sie müssen für `DataSource.AzureBlobStorage` den Namen der Datenquelle, die Verbindungszeichenfolge und den Namen des Blobcontainers angeben.

Obwohl sie in diesem Tutorial nicht zum Einsatz kommt, wird auch eine Richtlinie für vorläufiges Löschen definiert, mit der gelöschte Blobs anhand des Werts einer Spalte für vorläufiges Löschen identifiziert werden. Bei der folgenden Richtlinie wird ein Blob als gelöscht angesehen, wenn es über die Metadateneigenschaft `IsDeleted` mit dem Wert `true` verfügt.

```csharp
DataSource dataSource = DataSource.AzureBlobStorage(
    name: "demodata",
    storageConnectionString: configuration["AzureBlobConnectionString"],
    containerName: "basic-demo-data-pr",
    deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
        softDeleteColumnName: "IsDeleted",
        softDeleteMarkerValue: "true"),
    description: "Demo files to demonstrate cognitive search capabilities.");
```

Nachdem Sie nun das Objekt `DataSource` initialisiert haben, erstellen Sie die Datenquelle. `SearchServiceClient` verfügt über eine `DataSources`-Eigenschaft. Diese Eigenschaft stellt alle Methoden bereit, die Sie benötigen, um Azure Search-Datenquellen zu erstellen, aufzulisten, zu aktualisieren oder zu löschen.

Bei einer erfolgreichen Anforderung gibt die Methode die Datenquelle zurück, die erstellt wurde. Wenn ein Problem mit der Anforderung auftritt, z. B. ein ungültiger Parameter vorhanden ist, löst die Methode eine Ausnahme aus.

```csharp
try
{
    serviceClient.DataSources.CreateOrUpdate(dataSource);
}
catch (Exception e)
{
    // Handle the exception
}
```

Da es sich hier um Ihre erste Anforderung handelt, überprüfen Sie im Azure-Portal, ob die Datenquelle in Azure Search erstellt wurde. Überprüfen Sie auf der Dashboard-Seite des Suchdiensts, ob die Kachel „Datenquellen“ einen neuen Eintrag aufweist. Möglicherweise müssen Sie einige Minuten warten, bis die Portalseite aktualisiert wurde.

  ![Kachel „Datenquellen“ im Portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Kachel „Datenquellen“ im Portal")

## <a name="create-a-skillset"></a>Erstellen eines Skillsets

In diesem Abschnitt definieren Sie eine Reihe von Anreicherungsschritten, die Sie auf Ihre Daten anwenden möchten. Jeder Anreicherungsschritt wird als *Qualifikation* und der Satz von Anreicherungsschritten als *Qualifikationsgruppe* bezeichnet. Dieses Tutorial verwendet für die Qualifikationsgruppe [vordefinierte kognitive Qualifikationen](cognitive-search-predefined-skills.md):

+ Mit der Qualifikation für die [optische Zeichenerkennung](cognitive-search-skill-ocr.md) (OCR) wird gedruckter und handschriftlicher Text in Bilddateien erkannt.

+ Mit der [Textzusammenführung](cognitive-search-skill-textmerger.md) wird Text aus einer Sammlung von Feldern in einem einzigen Feld konsolidiert.

+ [Spracherkennung](cognitive-search-skill-language-detection.md), um die Sprache der Inhalte zu bestimmen.

+ Mit der [Textaufteilung](cognitive-search-skill-textsplit.md) werden große Inhalte vor dem Aufrufen der Qualifikationen zur Schlüsselbegriffserkennung und Entitätserkennung in kleinere Teile aufgeteilt. Für die Schlüsselbegriffserkennung und Entitätserkennung werden Eingaben von maximal 50.000 Zeichen akzeptiert. Für einige der Beispieldateien ist eine Aufteilung erforderlich, um diesen Grenzwert zu erfüllen.

+ [Entitätserkennung](cognitive-search-skill-entity-recognition.md), um die Namen von Organisationen aus Inhalten im Blobcontainer zu extrahieren.

+ [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), um die wichtigsten Schlüsselbegriffe herauszuziehen.

Während der Erstverarbeitung bricht Azure Search jedes Dokument auf, um die Inhalte aus verschiedenen Dateiformaten zu lesen. Gefundener Text, der aus der Quelldatei stammt, wird in einem generierten Feld ```content``` gespeichert, einem für jedes Dokument. Legen Sie daher die Eingabe auf ```"/document/content"``` fest, um diesen Text zu verwenden. 

Ausgaben können einem Index zugeordnet, als Eingabe einer Downstream-Qualifikation verwendet oder in beider Weise zugleich eingesetzt werden, wie etwa bei Sprachcode. Im Index ist ein Sprachcode zu Filterungszwecken nützlich. Als Eingabe wird ein Sprachcode von Qualifikationen zur Textanalyse verwendet, um die Linguistikregeln über Wörtertrennung zu informieren.

Weitere Informationen zu den Grundlagen von Qualifikationsgruppen finden Sie unter [How to define a skillset](cognitive-search-defining-skillset.md) (Definieren von Qualifikationsgruppen).

### <a name="ocr-skill"></a>OCR-Qualifikation

Die Qualifikation **OCR** extrahiert Text aus Bildern. Bei dieser Qualifikation wird davon ausgegangen, dass ein Feld „normalized_images“ vorhanden ist. Um dieses Feld zu generieren, legen Sie später in diesem Tutorial die ```"imageAction"```-Konfiguration in der Indexerdefinition auf ```"generateNormalizedImages"``` fest.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "image",
    source: "/document/normalized_images/*"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "text",
    targetName: "text"));

OcrSkill ocrSkill = new OcrSkill(
    description: "Extract text (plain and structured) from image",
    context: "/document/normalized_images/*",
    inputs: inputMappings,
    outputs: outputMappings,
    defaultLanguageCode: OcrSkillLanguage.En,
    shouldDetectOrientation: true);
```

### <a name="merge-skill"></a>Qualifikation: Zusammenführung

In diesem Abschnitt erstellen Sie eine Qualifikation für das **Zusammenführen**, mit der das Feld mit dem Dokumentinhalt mit dem von der OCR-Qualifikation erstellten Text zusammengeführt wird.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/content"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "itemsToInsert",
    source: "/document/normalized_images/*/text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "offsets",
    source: "/document/normalized_images/*/contentOffset"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "mergedText",
    targetName: "merged_text"));

MergeSkill mergeSkill = new MergeSkill(
    description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    insertPreTag: " ",
    insertPostTag: " ");
```

### <a name="language-detection-skill"></a>Qualifikation „Sprachenerkennung“

Die Qualifikation **Sprachenerkennung** erkennt die Sprache von Eingabetexten und meldet einen einzigen Sprachcode für jedes Dokument, das mit der Anforderung übermittelt wurde. Wir verwenden die Ausgabe der Qualifikation **Sprachenerkennung** als Teil der Eingabe für die Qualifikation **Textaufteilung**.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "languageCode",
    targetName: "languageCode"));

LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
    description: "Detect the language used in the document",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="text-split-skill"></a>Qualifikation „Textaufteilung“

Die folgende Qualifikation **Textaufteilung** teilt Text nach Seiten und beschränkt die Seitenlänge auf 4.000 Zeichen, die mit `String.Length` gemessen werden. Der Algorithmus versucht, den Text in Blöcke aufzuteilen, die höchstens `maximumPageLength` groß sind. Dabei versucht der Algorithmus, Sätze an Satzgrenzen zu teilen, sodass die Größe der Blöcke etwas kleiner als `maximumPageLength` sein kann.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/merged_text"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "textItems",
    targetName: "pages"));

SplitSkill splitSkill = new SplitSkill(
    description: "Split content into pages",
    context: "/document",
    inputs: inputMappings,
    outputs: outputMappings,
    textSplitMode: TextSplitMode.Pages,
    maximumPageLength: 4000);
```

### <a name="entity-recognition-skill"></a>Qualifikation „Entitätserkennung“

Diese `EntityRecognitionSkill`-Instanz ist auf den Erkennungskategorietyp `organization` festgelegt. Die Qualifikation **Entitätserkennung** kann auch die Kategorientypen `person` und `location` erkennen.

Beachten Sie, dass das Feld „context“ mit einem Sternchen auf ```"/document/pages/*"``` festgelegt ist, d. h. der Anreicherungsschritt wird für jede Seite unter ```"/document/pages"``` aufgerufen.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
    
List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "organizations",
    targetName: "organizations"));

List<EntityCategory> entityCategory = new List<EntityCategory>();
entityCategory.Add(EntityCategory.Organization);
    
EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
    description: "Recognize organizations",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings,
    categories: entityCategory,
    defaultLanguageCode: EntityRecognitionSkillLanguage.En);
```

### <a name="key-phrase-extraction-skill"></a>Qualifikation „Schlüsselbegriffserkennung“

Wie die `EntityRecognitionSkill`-Instanz, die gerade erstellt wurde, wird auch die Qualifikation **Schlüsselbegriffserkennung** für jede Seite des Dokuments aufgerufen.

```csharp
List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
inputMappings.Add(new InputFieldMappingEntry(
    name: "text",
    source: "/document/pages/*"));
inputMappings.Add(new InputFieldMappingEntry(
    name: "languageCode",
    source: "/document/languageCode"));

List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
outputMappings.Add(new OutputFieldMappingEntry(
    name: "keyPhrases",
    targetName: "keyPhrases"));

KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
    description: "Extract the key phrases",
    context: "/document/pages/*",
    inputs: inputMappings,
    outputs: outputMappings);
```

### <a name="build-and-create-the-skillset"></a>Aufbauen und Erstellen der Qualifikationsgruppe

Erstellen Sie die `Skillset` mithilfe der Qualifikationen, die Sie erstellt haben.

```csharp
List<Skill> skills = new List<Skill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

Skillset skillset = new Skillset(
    name: "demoskillset",
    description: "Demo skillset",
    skills: skills);
```

Erstellen Sie die Qualifikationsgruppe in Ihrem Suchdienst.

```csharp
try
{
    serviceClient.Skillsets.CreateOrUpdate(skillset);
}
catch (Exception e)
{
    // Handle exception
}
```

## <a name="create-an-index"></a>Erstellen eines Index

In diesem Abschnitt definieren Sie das Indexschema, indem Sie angeben, welche Felder in den durchsuchbaren Index aufgenommen und mit welchen Suchattributen die einzelnen Felder versehen werden sollen. Felder besitzen einen Typ und können Attribute annehmen, die bestimmen, wie das Feld verwendet wird (durchsuchbar, sortierbar usw.). Feldname in einem Index müssen nicht exakt mit den Feldnamen in der Quelle übereinstimmen. In einem späteren Schritt fügen Sie in einem Indexer Feldzuordnungen hinzu, um die Quell- und Zielfelder zu verbinden. Definieren Sie für diesen Schritt den Index mit Feldbenennungskonventionen, die für Ihre Suchanwendung angemessen sind.

In dieser Übung werden die folgenden Felder und Feldtypen verwendet:

| Feldnamen: | `id`       | Inhalt   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| field-types: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


### <a name="create-demoindex-class"></a>Erstellen der DemoIndex-Klasse

Die Felder für diesen Index werden mithilfe einer Modellklasse definiert. Jede Eigenschaft der Modellklasse verfügt über Attribute, die das suchbezogene Verhalten des entsprechenden Indexfelds bestimmen. 

Wir fügen die Modellklasse einer neuen C#-Datei hinzu. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** > **Neues Element** und dann „Klasse“ aus. Geben Sie der Datei den Namen `DemoIndex.cs`, und wählen Sie dann **Hinzufügen** aus.

Geben Sie unbedingt auch an, dass Sie die Typen aus den Namespaces `Microsoft.Azure.Search` und `Microsoft.Azure.Search.Models` verwenden möchten.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
```

Fügen Sie `DemoIndex.cs` die folgende Modellklassendefinition hinzu, und fügen Sie sie in denselben Namespace ein, in dem Sie den Index erstellen.

```csharp
// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public class DemoIndex
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsSearchable, IsSortable]
    public string Id { get; set; }

    [IsSearchable]
    public string Content { get; set; }

    [IsSearchable]
    public string LanguageCode { get; set; }

    [IsSearchable]
    public string[] KeyPhrases { get; set; }

    [IsSearchable]
    public string[] Organizations { get; set; }
}
```

Nach dem Definieren einer Modellklasse können Sie nun in `Program.cs` ganz problemlos eine Indexdefinition erstellen. Der Name für diesen Index lautet „demoindex“.

```csharp
var index = new Index()
{
    Name = "demoindex",
    Fields = FieldBuilder.BuildForType<DemoIndex>()
};
```

Sie werden während der Tests möglicherweise feststellen, dass Sie mehr als einmal versuchen, den Index zu erstellen. Aus diesem Grund sollten Sie vor dem Versuch, den Index zu erstellen, überprüfen, ob er nicht bereits vorhanden ist.

```csharp
try
{
    bool exists = serviceClient.Indexes.Exists(index.Name);

    if (exists)
    {
        serviceClient.Indexes.Delete(index.Name);
    }

    serviceClient.Indexes.Create(index);
}
catch (Exception e)
{
    // Handle exception
}
```

Weitere Informationen zum Definieren eines Index finden Sie unter [Index erstellen (Azure Search REST-API)](https://docs.microsoft.com/rest/api/searchservice/create-index).

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>Erstellen eines Indexers, Zuordnen von Feldern und Ausführen von Transformationen

Bisher haben Sie eine Datenquelle, eine Qualifikationsgruppe und einen Index erstellt. Diese drei Komponenten werden Teil eines [Indexers](search-indexer-overview.md), der jedes einzelne Stück per Pull in einen einzelnen mehrstufigen Vorgang herunterlädt. Um diese in einem Indexer zusammenzuführen, müssen Sie Feldzuordnungen definieren.

+ fieldMappings werden vor der Qualifikationsgruppe verarbeitet, und die Quellfelder der Datenquelle werden Zielfeldern in einem Index zugeordnet. Wenn die Feldnamen und -typen auf beiden Seiten gleich sind, ist keine Zuordnung erforderlich.

+ outputFieldMappings werden nach der Qualifikationsgruppe verarbeitet. Es wird auf nicht vorhandene sourceFieldNames verwiesen, bis diese per Dokumententschlüsselung oder Anreicherung erstellt werden. targetFieldName ist ein Feld in einem Index.

Neben dem Verknüpfen von Ein- und Ausgaben können Sie auch Feldzuordnungen nutzen, um Datenstrukturen zu vereinfachen. Weitere Informationen finden Sie unter [Zuordnen angereicherter Felder zu einem durchsuchbaren Index](cognitive-search-output-field-mapping.md).

```csharp
IDictionary<string, object> config = new Dictionary<string, object>();
config.Add(
    key: "dataToExtract",
    value: "contentAndMetadata");
config.Add(
    key: "imageAction",
    value: "generateNormalizedImages");

List<FieldMapping> fieldMappings = new List<FieldMapping>();
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "metadata_storage_path",
    targetFieldName: "id",
    mappingFunction: new FieldMappingFunction(
        name: "base64Encode")));
fieldMappings.Add(new FieldMapping(
    sourceFieldName: "content",
    targetFieldName: "content"));

List<FieldMapping> outputMappings = new List<FieldMapping>();
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/organizations/*",
    targetFieldName: "organizations"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/pages/*/keyPhrases/*",
    targetFieldName: "keyPhrases"));
outputMappings.Add(new FieldMapping(
    sourceFieldName: "/document/languageCode",
    targetFieldName: "languageCode"));

Indexer indexer = new Indexer(
    name: "demoindexer",
    dataSourceName: dataSource.Name,
    targetIndexName: index.Name,
    description: "Demo Indexer",
    skillsetName: skillSet.Name,
    parameters: new IndexingParameters(
        maxFailedItems: -1,
        maxFailedItemsPerBatch: -1,
        configuration: config),
    fieldMappings: fieldMappings,
    outputFieldMappings: outputMappings);

try
{
    bool exists = serviceClient.Indexers.Exists(indexer.Name);

    if (exists)
    {
        serviceClient.Indexers.Delete(indexer.Name);
    }

    serviceClient.Indexers.Create(indexer);
}
catch (Exception e)
{
    // Handle exception
}
```

Planen Sie ein, dass die Erstellung des Indexers ein wenig Zeit in Anspruch nimmt. Das Dataset ist zwar klein, Analysequalifikationen sind aber rechenintensiv. Einige Qualifikationen, wie etwa die Bildanalyse, weisen lange Ausführungszeiten auf.

> [!TIP]
> Die Pipeline wird durch Erstellen eines Indexers aufgerufen. Wenn beim Zugriff auf die Daten, dem Zuordnen von Ein- und Ausgaben oder der Reihenfolge der Vorgänge Probleme bestehen, äußern sie sich in dieser Phase.

### <a name="explore-creating-the-indexer"></a>Untersuchen der Indexererstellung

Der Code legt ```"maxFailedItems"``` auf -1 fest, womit die Index-Engine angewiesen wird, Fehler beim Datenimport zu ignorieren. Dies ist nützlich, weil die Demodatenquelle nur wenige Dokumente enthält. Für eine größere Datenquelle sollten Sie den Wert größer als 0 festlegen.

Beachten Sie auch, dass ```"dataToExtract"``` auf ```"contentAndMetadata"``` festgelegt ist. Diese Anweisung weist den Indexer an, die Inhalte aus verschiedenen Dateiformaten sowie die den einzelnen Dateien zugeordneten Metadaten automatisch zu extrahieren.

Wenn die Inhalte extrahiert werden, können Sie `imageAction` darauf festlegen, Text aus in der Datenquelle gefundenen Bildern zu extrahieren. Die Festlegung von ```"imageAction"``` auf die Konfiguration ```"generateNormalizedImages"``` in Kombination mit den Qualifikationen OCR und Textzusammenführung weist den Indexer an, Text aus den Bildern zu extrahieren (beispielsweise das Wort „Stop“ aus einem Stoppschild) und ihn als Teil des Inhaltsfelds einzubetten. Dieses Verhalten betrifft sowohl die in den Dokumenten eingebetteten Bilder (denken Sie etwa an Bilder in PDF-Dateien) als auch die in der Datenquelle gefundenen Bilder, z.B. eine JPG-Datei.

## <a name="check-indexer-status"></a>Überprüfen des Indexerstatus

Nachdem der Indexer definiert wurde, wird er automatisch ausgeführt, wenn Sie die Anforderung senden. Abhängig von den kognitiven Qualifikationen, die Sie definiert haben, kann die Indizierung länger als erwartet dauern. Um herauszufinden, ob der Indexer noch ausgeführt wird, verwenden Sie die `GetStatus`-Methode.

```csharp
try
{
    IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

    switch (demoIndexerExecutionInfo.Status)
    {
        case IndexerStatus.Error:
            Console.WriteLine("Indexer has error status");
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
catch (Exception e)
{
    // Handle exception
}
```

`IndexerExecutionInfo` stellt den aktuellen Status und den Ausführungsverlauf eines Indexers dar.

Warnungen sind bei bestimmten Kombinationen aus Quelldatei und Qualifikation häufig und weisen nicht immer auf ein Problem hin. Im Rahmen dieses Tutorials sind die Warnungen gutartig (z.B. keine Texteingaben aus den JPEG-Dateien).
 
## <a name="query-your-index"></a>Abfragen Ihres Index

Sie können nach dem Abschluss der Indizierung Abfragen ausführen, die die Inhalte einzelner Felder zurückgeben. Standardmäßig gibt Azure Search die obersten 50 Ergebnisse zurück. Die Beispieldaten sind klein, so dass die Standardeinstellung gut funktioniert. Beim Arbeiten mit größeren Datensets müssen Sie jedoch möglicherweise Parameter in die Abfragezeichenfolge aufnehmen, um mehr Ergebnisse zurückzugeben. Anweisungen finden Sie unter [How to page results in Azure Search](search-pagination-page-layout.md) (Seitenweise Ausgabe von Ergebnissen in Azure Search).

Fragen Sie als Überprüfungsschritt den Index nach allen Feldern ab.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` erstellt ein neues `SearchIndexClient`-Element mit Werten, die in der Konfigurationsdatei der Anwendung (appsettings.json) gespeichert sind. Beachten Sie, dass nicht der Administratorschlüssel, sondern der Abfrage-API-Schlüssel des Suchdiensts verwendet wird.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Die Ausgabe ist das Indexschema mit dem Namen, dem Typ und den Attributen für jedes Feld.

Senden Sie eine zweite Abfrage nach `"*"`, um alle Inhalte eines einzelnen Felds zurückzugeben, z.B. `organizations`.

```csharp
SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

Wiederholen Sie das Verfahren in dieser Übung für weitere Felder: „content“, „languageCode“, „keyPhrases“ und „organizations“. Mithilfe von `$select` können Sie unter Einsatz einer durch Trennzeichen getrennten Liste mehrere Felder zurückgeben.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Zurücksetzen und erneut ausführen

In den frühen, experimentellen Phasen der Entwicklung besteht der praktikabelste Ansatz für den Übergang von einer Entwurfsphase zur nächsten darin, die Objekt aus Azure Search zu löschen und Ihrem Code zu erlauben, sie neu zu erstellen. Ressourcennamen sind eindeutig. Wenn Sie ein Objekt löschen, können Sie es unter dem gleichen Namen neu erstellen.

In diesem Tutorial wurde überprüft, ob Indexer oder Indizes vorhanden waren. Wenn sie bereits vorhanden waren, wurden sie gelöscht, damit Sie Ihren Code erneut ausführen konnten.

Sie können auch das Portal verwenden, um Indizes, Indexer und Qualifikationsgruppen zu löschen.

In dem Maß, da Ihr Code reift, kann es sinnvoll sein, die Neuerstellungsstrategie neu zu definieren. Weitere Informationen finden  Sie unter [Neuerstellen eines Indexes](search-howto-reindex.md).

## <a name="takeaways"></a>Wesentliche Punkte

Dieses Tutorial veranschaulicht die grundlegenden Schritte zum Erstellen einer erweiterten Indizierungspipeline durch Erstellung von Komponenten: eine Datenquelle, eine Qualifikationsgruppe, ein Index und ein Indexer.

Es wurden [vordefinierte Qualifikationen](cognitive-search-predefined-skills.md) im Zusammenhang mit der Qualifikationsgruppendefinition und den Mechanismen zum Verketten von Qualifikationen mithilfe von Eingängen und Ausgängen vorgestellt. Sie haben darüber hinaus erfahren, dass `outputFieldMappings` in der Indexerdefinition erforderlich ist, um angereicherte Werte aus der Pipeline in einen durchsuchbaren Index in einem Azure Search-Dienst weiterzuleiten.

Ferner haben Sie erfahren, wie die Ergebnisse getestet werden und das System für weitere Entwicklungsschritte zurückgesetzt wird. Sie haben gelernt, dass das Ausgeben von Abfragen auf den Index die von der angereicherten Indizierungspipeline erstellte Ausgabe zurückgibt. Darüber hinaus haben Sie die Überprüfung des Indexerstatus und das Löschen von Objekten vor der erneuten Ausführung einer Pipeline gelernt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Die schnellste Möglichkeit, das System nach einem Tutorial aufzuräumen, besteht im Löschen der Ressourcengruppe, die den Azure Search-Dienst und den Azure Blob-Dienst enthält. Unter der Annahme, dass Sie beide Dienste in der gleichen Gruppe platziert haben, löschen Sie nun einfach die Ressourcengruppe, um endgültig ihren gesamten Inhalt zu löschen, einschließlich der Dienste und aller gespeicherten Inhalte, die Sie für dieses Tutorial erstellt haben. Im Portal finden Sie den Namen der Ressourcengruppe auf der Seite „Übersicht“ der einzelnen Dienste.

## <a name="next-steps"></a>Nächste Schritte

Anpassen oder Erweitern der Pipeline mit benutzerdefinierten Qualifikationen. Das Erstellen einer benutzerdefinierten Qualifikation die Sie einer Qualifikationsgruppe hinzufügen, ermöglicht Ihnen, eigene, von Ihnen selbst erstellte Text- oder Bildanalysen einzubeziehen.

> [!div class="nextstepaction"]
> [Beispiel: Erstellen einer benutzerdefinierten Qualifikation](cognitive-search-create-custom-skill-example.md)
