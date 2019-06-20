---
title: Prozess für kognitive Suche, Datenextrahierung und KI-Verarbeitung natürlicher Sprache – Azure Search
description: Inhaltsextrahierung, Verarbeitung natürlicher Sprache (NLP, Natural Language Processing) und Bildverarbeitung zum Erstellen durchsuchbarer Inhalte in der Azure Search-Indizierung mithilfe von kognitiven Fähigkeiten und KI-Algorithmen.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: overview
ms.date: 05/28/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 8af927bee11d66c473707b603951fa693f6840e3
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299029"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Was ist die „kognitive Suche“ in Azure Search?

Kognitive Suche ist eine KI-Funktion in Azure Search zum Extrahieren von Text aus Bildern, Blobs und anderen unstrukturierten Datenquellen – so wird der Inhalt erweitert und kann noch ergiebiger in einem Azure Search-Index durchsucht werden. Extrahierung und Erweiterung werden über *kognitive Fähigkeiten* implementiert, die einer Indizierungspipeline angefügt sind. KI-Erweiterungen werden auf folgende Weise unterstützt: 

+ Zu den Fähigkeiten in Bezug auf die **Verarbeitung natürlicher Sprache** gehören [Entitätserkennung](cognitive-search-skill-entity-recognition.md), Spracherkennung, [Schlüsselbegriffserkennung](cognitive-search-skill-language-detection.md), [Textbearbeitung](cognitive-search-skill-keyphrases.md) und [Stimmungserkennung](cognitive-search-skill-sentiment.md). Mithilfe dieser Fähigkeiten kann unstrukturierter Text neue Formen annehmen, die als durchsuchbare und filterbare Felder in einem Index zugeordnet sind.

+ Die **Bildverarbeitungsfähigkeiten** umfassen [Optical Character Recognition (OCR)](cognitive-search-skill-ocr.md) und die Identifizierung von [visuellen Features](cognitive-search-skill-image-analysis.md), z.B. Gesichtserkennung, Bildinterpretation, Bilderkennung (berühmte Personen und Wahrzeichen) oder Attribute wie Farben oder Bildausrichtung. Sie können Textdarstellungen von Bildinhalt erstellen, indem Sie alle Abfragefunktionen von Azure Search verwenden.

![Diagramm der Pipeline für Kognitive Suche](./media/cognitive-search-intro/cogsearch-architecture.png "Übersicht über die Pipeline für Kognitive Suche")

Die kognitiven Fähigkeiten in Azure Search basieren auf Machine Learning-Modellen in Cognitive Services-APIs: [Maschinelles Sehen](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) und [Textanalyse](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Die Verarbeitung von natürlicher Sprache und Bildern wird während der Phase der Datenerfassung angewendet, wobei die Ergebnisse zu einem Teil einer Dokumentkomposition in einem durchsuchbaren Index in Azure Search werden. Daten stammen aus einem Azure-Dataset und werden dann über eine Indizierungspipeline übertragen, indem jeweils die erforderlichen [integrierten Fähigkeiten](cognitive-search-predefined-skills.md) verwendet werden. Die Architektur ist erweiterbar. Falls die integrierten Fähigkeiten also nicht ausreichen, können Sie [benutzerdefinierte Fähigkeiten](cognitive-search-create-custom-skill-example.md) erstellen und anfügen, um die benutzerdefinierte Verarbeitung zu integrieren. Beispiele hierfür sind ein benutzerdefiniertes Entitätsmodul oder ein benutzerdefinierter Dokumentenklassifizierer, das bzw. der auf eine bestimmte Domäne ausgerichtet ist, z.B. Finanzen, wissenschaftliche Veröffentlichungen oder Medizin.

> [!NOTE]
> Wenn Sie den Umfang erweitern, indem Sie die Verarbeitungsfrequenz erhöhen oder weitere Dokumente oder KI-Algorithmen hinzufügen, müssen Sie [eine kostenpflichtige Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md). Gebühren fallen beim Aufrufen von APIs in Cognitive Services sowie für die Bildextraktion im Rahmen der Dokumentaufschlüsselungsphase in Azure Search an. Für die Textextraktion aus Dokumenten fallen keine Gebühren an.
>
> Die Ausführung integrierter Qualifikationen wird nach dem bestehenden [nutzungsbasierten Preis für Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) berechnet. Die Preise für die Bildextraktion werden auf der [Preisseite von Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) beschrieben.
## <a name="components-of-cognitive-search"></a>Komponenten von Cognitive Search

Cognitive Search-Pipelines basieren auf [Azure Search-*Indexern*](search-indexer-overview.md), die Datenquellen durchforsten und End-to-End-Indexverarbeitung bieten. Die Fähigkeitengruppen werden jetzt an Indexer angefügt. Dabei werden Dokumente gemäß der von Ihnen definierten Fähigkeiten abgefangen und angereichert. Nach der Indizierung können Sie über Suchanforderungen über sämtliche [von Azure Search unterstützte Abfragetypen](search-query-overview.md) auf Inhalte zugreifen.  Wenn Sie mit Indexern noch nicht vertraut sind, werden Ihnen in diesem Abschnitt die erforderlichen Schritte erläutert.

### <a name="step-1-connection-and-document-cracking-phase"></a>Schritt 1: Phase der Entschlüsselung von Verbindung und Dokument

Am Anfang der Pipeline befindet sich unstrukturierter Text oder Nicht-Text-Inhalt (z.B. Bilder und gescannte Dokumente als JPEG-Dateien). Die Daten müssen in einem Azure-Datenspeicherdienst enthalten sein, auf den ein Indexer Zugriff hat. Indexer können Quelldokumente entschlüsseln, um Text aus Quelldaten zu extrahieren.

![Phase der Entschlüsselung von Dokumenten](./media/cognitive-search-intro/document-cracking-phase-blowup.png "Dokumententschlüsselung")

 Zu den unterstützten Quellen zählen Azure Blob Storage, Azure Table Storage, Azure SQL-Datenbank und Azure Cosmos DB. Textbasierte Inhalte können aus den folgenden Dateitypen extrahiert werden: PDF-, Word-, PowerPoint- und CSV-Dateien. Eine vollständige Liste finden Sie unter [Unterstützte Formate](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Schritt 2: Phase der kognitiven Fähigkeiten und Anreicherung

Die Anreicherung erfolgt durch *kognitive Fähigkeiten*, die atomische Vorgänge durchführen. Sobald Sie über Textinhalte aus einer PDF-Datei verfügen, können Sie beispielsweise die Entitätserkennung, die Sprachenerkennung oder die Schlüsselbegriffserkennung anwenden, um neue Felder in Ihrem Index zu erstellen, die nativ in der Quelle nicht verfügbar sind. Die Auflistung der in Ihrer Pipeline verwendeten Fähigkeiten wird als *Fähigkeitengruppe* bezeichnet.  

![Anreicherungsphase](./media/cognitive-search-intro/enrichment-phase-blowup.png "enrichment phase")

Eine Fähigkeitengruppe basiert auf [vordefinierten kognitiven Fähigkeiten](cognitive-search-predefined-skills.md) oder [benutzerdefinierte Fähigkeiten](cognitive-search-create-custom-skill-example.md), die von Ihnen bereitgestellt und mit der Fähigkeitengruppe verbunden werden. Eine Fähigkeitengruppe kann minimal oder höchst komplex sein. Sie bestimmt nicht nur den Verarbeitungstyp, sondern auch die Reihenfolge der Vorgänge. Eine Fähigkeitengruppe bietet zusammen mit den als Bestandteil eines Indexers definierten Feldzuordnungen eine umfassende Beschreibung der Anreicherungspipeline. Weitere Informationen zum Zusammensetzen all dieser Teile finden Sie unter [Definieren einer Fähigkeitengruppe](cognitive-search-defining-skillset.md).

Die Pipeline generiert intern eine Sammlung angereicherter Dokumente. Sie können entscheiden, welche Teile der angereicherten Dokumente indizierbaren Feldern in Ihrem Suchindex zugeordnet werden sollen. Wenn Sie beispielsweise die Fähigkeiten der Schlüsselbegriffserkennung und der Entitätserkennung angewendet haben, werden diese neuen Felder Bestandteil des angereicherten Dokuments und können Feldern in Ihrem Index zugeordnet werden. Weitere Informationen zu Eingabe-/Ausgabeformationen finden Sie unter [Anmerkungen](cognitive-search-concept-annotations-syntax.md).

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Hinzufügen eines knowledgeStore-Elements zum Speichern von Anreicherungen

Die [REST-API-Version 2019-05-06 des Search-Diensts](search-api-preview.md) erweitert Qualifikationsgruppen um eine knowledgeStore-Definition, die eine Azure Storage-Verbindung und Projektionen bereitstellt, die beschreiben, wie die Anreicherungen gespeichert werden. 

Das Hinzufügen eines Wissensspeichers zu einer Qualifikationsgruppe bietet Ihnen die Möglichkeit, eine Darstellung Ihrer Anreicherungen für andere Szenarien als die Volltextsuche zu projizieren. Weitere Informationen finden Sie unter [Was sind Wissensspeicher?](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Schritt 3: Suchindex und abfragebasierter Zugriff

Nach Abschluss der Verarbeitung verfügen Sie über einen Suchindex mit angereicherten Dokumenten, die mit Azure Search im Volltext durchsucht werden können. Entwickler und Benutzer greifen über das [Abfragen des Indexes](search-query-overview.md) auf den von der Pipeline generierten angereicherten Inhalt zu. 

![Index mit Suchsymbol](./media/cognitive-search-intro/search-phase-blowup.png "Index with search icon")

Der Index ist vergleichbar mit beliebigen anderen Indizes, die für Azure Search erstellt werden können: Sie können ihn durch benutzerdefinierte Analysen ergänzen, Fuzzysuchabfragen aufrufen, gefilterte Suchvorgänge hinzufügen oder zur Umgestaltung der Suchergebnisse mit Bewertungsprofilen experimentieren.

Indizes werden über ein Indexschema generiert, das die Felder, Attribute und weitere Konstrukte definiert, die an einen bestimmten Index angefügt wurden, wie z.B. Bewertungsprofile und Synonymzuordnungen. Nachdem ein Index definiert und aufgefüllt wurde, können Sie die Indizierung inkrementell durchführen, um neue und aktualisierte Quelldokumente zu übernehmen. Für bestimmte Änderungen ist eine vollständige Neuerstellung erforderlich. Sie sollten ein kleines Dataset verwenden, bis der Schemaentwurf stabil ist. Weitere Informationen finden Sie unter [Neuerstellen eines Indexes](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Wichtige Features und Konzepte

| Konzept | BESCHREIBUNG| Links |
|---------|------------|-------|
| Fähigkeitengruppe | Eine benannte allgemeine Ressource mit einer Sammlung von Fähigkeiten. Eine Fähigkeitengruppe in der Anreicherungspipeline. Sie wird während der Indizierung durch einen Indexer aufgerufen. | [Definieren einer Fähigkeitengruppe](cognitive-search-defining-skillset.md) |
| Kognitive Fähigkeit | Eine atomische Transformation in einer Anreicherungspipeline. Häufig handelt es sich dabei um eine Komponente, die sich mit der Extrahierung oder der Erschließung einer Struktur befasst und folglich Ihr Verständnis der Eingabedaten ergänzt. Die Ausgabe ist fast immer textbasiert. Bei der Verarbeitung wird auf die Verarbeitung natürlicher Sprache oder die Bildbearbeitung zurückgegriffen, bei der Text aus Bildeingaben extrahiert oder generiert wird. Die Ausgabe aus einer Fähigkeit kann einem Feld in einem Index zugeordnet werden oder als Eingabe für eine Downstream-Anreicherung verwendet werden. Eine Fähigkeit ist entweder vordefiniert und wird von Microsoft bereitgestellt, oder sie ist benutzerdefiniert und wird von Ihnen erstellt und bereitgestellt. | [Vordefinierte Skills](cognitive-search-predefined-skills.md) |
| Extrahieren von Daten | Deckt ein breites Verarbeitungsspektrum ab. In Bezug auf Cognitive Search wird die Fähigkeit zur Erkennung von Entitäten jedoch zumeist zum Extrahieren von Daten (einer Entität) aus einer Quelle verwendet, die diese Informationen nicht nativ bereitstellt. | [Die kognitive Qualifikation „Entitätserkennung“](cognitive-search-skill-entity-recognition.md)| 
| Bildverarbeitung | Erschließt sich Text aus einem Bild, z.B. die Fähigkeit der Erkennung eines Wahrzeichens oder des Extrahierens von Text aus einem Bild. Typische Beispiele dafür sind OCR für das Löschen von Zeichen aus einer Datei mit einem gescannten Dokument (JPEG) oder das Erkennen eines Straßennamens auf einem Foto mit einem Straßenschild. | [Fähigkeit: Bildanalyse](cognitive-search-skill-image-analysis.md) oder [Fähigkeit: OCR](cognitive-search-skill-ocr.md)
| Verarbeitung natürlicher Sprache | Textverarbeitung für Einblicke und Informationen zu Texteingaben. Sprachenerkennung, Standpunktanalyse und Schlüsselbegriffserkennung sind Fähigkeiten, die in den Bereich der Verarbeitung natürlicher Sprache fallen.  | [Fähigkeit: Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), [Fähigkeit: Sprachenerkennung](cognitive-search-skill-language-detection.md), [Fähigkeit: Standpunktanalyse](cognitive-search-skill-sentiment.md) |
| Dokumententschlüsselung | Der Prozess des Extrahierens oder Erstellens von Textinhalt aus Nicht-Text-Quellen während der Indizierung. OCR (Optical Character Recognition) ist ein Beispiel hierfür. In der Regel bezieht sich dies jedoch auf die Kernfunktionalität des Indexers, da dieser Inhalte aus Anwendungsdateien extrahiert. Sowohl die Datenquelle, die den Speicherort der Quelldatei bereitstellt, als auch die Indexerdefinition, die Feldzuordnungen zur Verfügung stellt, stellen Schlüsselfaktoren bei der Dokumententschlüsselung dar. | Weitere Informationen finden Sie unter [Indexer](search-indexer-overview.md). |
| Strukturierung | Textfragmente werden zu einer größeren Struktur konsolidiert, oder größere Textblöcke werden zur weiteren Downstream-Verarbeitung in eine überschaubare Größe aufgeteilt. | [Fähigkeit: Strukturierung](cognitive-search-skill-shaper.md), [Fähigkeit: Textzusammenführung](cognitive-search-skill-textmerger.md), [Fähigkeit: Textunterteilung](cognitive-search-skill-textsplit.md) |
| Angereicherte Dokumente | Eine vorübergehende interne Struktur, die bei der Verarbeitung generiert wird. Die endgültige Ausgabe spiegelt sich in einem Suchindex wider. Eine Skillset bestimmt, welche Anreicherungen vorgenommen werden. Feldzuordnungen bestimmen, welche Datenelemente zum Index hinzugefügt werden. Optional können Sie einen Wissensspeicher erstellen, um angereicherte Dokumente mit Tools wie dem Storage-Explorer, Power BI oder einem anderen Tool, das mit Azure Blob Storage verbunden ist, zu speichern und zu untersuchen. | Informationen finden Sie im Thema zum [Wissensspeicher (Vorschau)](knowledge-store-concept-intro.md). |
| Indexer |  Ein Crawler, mit dem durchsuchbare Daten und Metadaten aus einer externen Datenquelle extrahiert werden und mit dem ein Index basierend auf Feld-zu-Feld-Zuordnungen zwischen dem Index und Ihrer Datenquelle zur Dokumententschlüsselung aufgefüllt wird. Bei Cognitive Search-Anreicherungen ruft der Indexer eine Fähigkeitengruppe auf und enthält die Feldzuordnungen, durch welche die Ausgabe der Anreicherung Zielfeldern im Index zugeordnet wird. Die Indexerdefinition enthält sämtliche Anweisungen und Verweise für Pipelinevorgänge. Die Pipeline wird bei Ausführung des Indexers aufgerufen. | [Indexer](search-indexer-overview.md) |
| Data source  | Ein von einem Indexer verwendetes Objekt zum Verbinden mit einer externen Datenquelle von unterstützten Typen in Azure. | Weitere Informationen finden Sie unter [Indexer](search-indexer-overview.md). |
| Index | Ein persistenter Suchindex in Azure Search, der aus einem Indexschema erstellt wurde, das die Feldstruktur und -verwendung definiert. | [Indizes in Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Wo beginne ich?

**Schritt 1: [Erstellen einer Azure Search-Ressource](search-create-service-portal.md)** 

**Schritt 2: Ausprobieren einiger Schnellstarts und Beispiele, um praktische Erfahrungen zu sammeln**

+ [Schnellstart (Portal)](cognitive-search-quickstart-blob.md)
+ [Tutorial (HTTP-Anforderungen)](cognitive-search-tutorial-blob.md)
+ [Beispiel für benutzerdefinierte Fähigkeiten (C#)](cognitive-search-create-custom-skill-example.md)

Es empfiehlt sich, zu Lernzwecken den kostenlosen Dienst zu verwenden. Beachten Sie jedoch, dass die Anzahl kostenloser Transaktionen auf 20 Dokumente pro Tag beschränkt ist. Wenn Sie sowohl die Schnellstartanleitung als auch das Tutorial am gleichen Tag ausführen möchten, verwenden Sie einen kleineren Dateisatz (zehn Dokumente).

**Schritt 3: Überprüfen der API**

Sie können in REST `api-version=2019-05-06` in Anforderungen oder dem .NET SDK verwenden. 

Bei diesem Schritt werden die REST-APIs zum Erstellen einer Cognitive Search-Lösung verwendet. Es werden nur zwei APIs für Cognitive Search hinzugefügt oder erweitert. Andere APIs haben die gleiche Syntax wie die allgemein verfügbaren Versionen.

| REST-API | BESCHREIBUNG |
|-----|-------------|
| [Erstellen der Datenquelle](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | Eine Ressource, die eine externe Datenquelle identifiziert, welche Quelldaten zum Erstellen angereicherter Dokumente bereitstellt.  |
| [Erstellen einer Qualifikationsgruppe (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Eine Ressource, welche die Verwendung von [vordefinierten Fähigkeiten](cognitive-search-predefined-skills.md) und [benutzerdefinierten kognitiven Fähigkeiten](cognitive-search-custom-skill-interface.md) in einer Anreicherungspipeline während der Indizierung koordiniert. |
| [Index erstellen](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ein Schema zur Beschreibung eines Azure Search-Indexes. Felder im Index werden Feldern in den Quelldaten zugeordnet oder Feldern, die während der Anreicherungsphase erstellt wurden (z.B. ein von der Entitätserkennung erstelltes Feld für Organisationsnamen). |
| [Erstellen eines Indexers (API-Version 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Eine Ressource, die während der Indizierung verwendete Komponenten definiert: Dazu zählen eine Datenquelle, eine Fähigkeitengruppe, Feldzuordnungen der Quelle, intermediäre Datenstrukturen für den Zielindex und der Index selbst. Die Ausführung des Indexers ist Auslöser für die Datenerfassung und -anreicherung. Die Ausgabe ist ein Suchindex basierend auf dem Indexschema, der mit Quelldaten aufgefüllt wird, die durch Qualifikationsgruppen angereichert werden.  |

**Checkliste: Typischer Workflow**

1. Fügen Sie Teilmengen Ihrer Azure-Quelldaten in eine repräsentative Stichprobe ein. Die Indizierung braucht Zeit. Daher sollten Sie mit einem kleinen, repräsentativen Dataset beginnen, das Sie anschließend schrittweise aufbauen, während sich Ihre Lösung entwickelt.

1. Erstellen Sie ein [Datenquellenobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) in Azure Search, um eine Verbindungszeichenfolge für den Datenabruf bereitzustellen.

1. Erstellen Sie mithilfe von Anreicherungsschritten [Fähigkeitengruppen](https://docs.microsoft.com/rest/api/searchservice/create-skillset).

1. Definieren Sie das [Indexschema](https://docs.microsoft.com/rest/api/searchservice/create-index). Die *Fields*-Auflistung enthält Felder aus Quelldaten. Sie sollten zudem zusätzliche Felder durch Stubs ersetzen, um generierte Werte für Inhalte zu speichern, die während der Anreicherung erstellt werden.

1. Definieren Sie den [Indexer](https://docs.microsoft.com/rest/api/searchservice/create-skillset), der auf die Datenquelle, die Fähigkeitengruppen und den Index verweist.

1. Fügen Sie *outputFieldMappings* innerhalb des Indexers hinzu. In diesem Abschnitt wird die Ausgabe aus der Fähigkeitengruppe (in Schritt 3) den Eingabefeldern im Indexschema (in Schritt 4) zugeordnet.

1. Senden Sie die gerade erstellte Anforderung *Indexer erstellen* (eine POST-Anforderung mit einer Indexerdefinition im Anforderungstext), um den Indexer in Azure Search darzustellen. Dieser Schritt befasst sich mit der Ausführung des Indexers durch Aufrufen der Pipeline.

1. Führen Sie zum Auswerten von Ergebnissen und zum Ändern von Code Abfragen aus, um Fähigkeitengruppen, Schemas oder die Indexerkonfiguration zu aktualisieren.

1. [Setzen Sie den Indexer zurück](search-howto-reindex.md), bevor Sie die Pipeline neu erstellen.

Weitere Informationen zu bestimmten Fragen oder Problemen finden Sie unter [Problembehandlungstipps](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Nächste Schritte

+ [Dokumentation zu Cognitive Search](cognitive-search-resources-documentation.md)
+ [Schnellstart: Testen der kognitiven Suche anhand einer exemplarischen Vorgehensweise zu Portalfunktionen](cognitive-search-quickstart-blob.md)
+ [Tutorial: Informationen zum Aufrufen von APIs der kognitiven Suche](cognitive-search-tutorial-blob.md)
+ [Übersicht über Wissensspeicher](knowledge-store-concept-intro.md)
+ [Wissensspeicher: exemplarische Vorgehensweise](knowledge-store-howto.md)
