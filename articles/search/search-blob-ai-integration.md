---
title: Verwenden von KI zum Anreichern von Blobinhalten
titleSuffix: Azure Cognitive Search
description: In diesem Artikel erfahren Sie mehr über die Analysefunktionen für natürliche Sprache und Bilder in Azure Cognitive Search sowie darüber, wie diese Vorgänge auf Inhalte angewendet werden können, die in Azure-Blobs gespeichert sind.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 3d427d80e502eed0825165e640acc0755515c5b0
ms.sourcegitcommit: 983eb1131d59664c594dcb2829eb6d49c4af1560
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222047"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Verwenden von KI zum Verarbeiten und Analysieren von Blobinhalten in Azure Cognitive Search

Inhalte in Azure Blob Storage, die aus Bildern oder einem langen, nicht differenzierten Text bestehen, können eine Deep Learning-Analyse durchlaufen, um nützliche Informationen für Downstreamanwendungen zu ermitteln und zu extrahieren. Durch die Verwendung der [KI-Anreicherung](cognitive-search-concept-intro.md) können Sie Folgendes erreichen:

+ Extrahieren von Text aus Bildern mithilfe der optischen Zeichenerkennung (OCR)
+ Erzeugen einer Szenenbeschreibung oder von Tags aus einem Foto
+ Erkennen von Sprache und Übersetzen von Text in andere Sprachen
+ Rückschließen auf eine Struktur mithilfe der Erkennung von Entitäten, indem Verweise auf Personen, Datumsangaben, Orte oder Organisationen gesucht werden

Obwohl Sie möglicherweise nur eine dieser KI-Funktionen benötigen, ist es üblich, mehrere davon in derselben Pipeline zu kombinieren (z. B. das Extrahieren von Text aus einem gescannten Bild und das anschließende Auffinden aller darin referenzierten Datumsangaben und Orte). Es ist auch üblich, eine benutzerdefinierte KI oder Machine-Learning-Verarbeitung in Form von zukunftsweisenden externen Paketen oder internen Modellen zu integrieren, die auf Ihre Daten und Anforderungen zugeschnitten sind.

Sie können die KI-Anreicherung zwar auf jede Datenquelle anwenden, die von einem Suchindexer unterstützt wird, aber Blobs sind die am häufigsten verwendeten Strukturen in einer Anreicherungspipeline. Ergebnisse werden in einen Suchindex für die Volltextsuche übernommen, oder an Azure Storage zurückgesendet, um neue Anwendungserfahrungen zu unterstützen, die das Erkunden von Daten für Ermittlungs- oder Analyseszenarien umfassen. 

In diesem Artikel betrachten wir die KI-Anreicherung aus einem breiten Blickwinkel, sodass Sie den gesamten Prozess schnell erfassen können, von der Transformation von Rohdaten in Blobs bis hin zu abfragbaren Informationen in einem Suchindex oder einem Wissensspeicher.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Was es bedeutet, Blobdaten mittels KI „anzureichern“

*KI-Anreicherung* ist Teil der Indizierungsarchitektur von Azure Cognitive Search, die Machine Learning-Modelle von Microsoft oder benutzerdefinierte Lernmodelle, die Sie bereitstellen, integriert. Sie hilft Ihnen bei der Implementierung von End-to-End-Szenarien, in denen Sie Blobs verarbeiten müssen (sowohl vorhandene als auch neue, wenn sie eingehen oder aktualisiert werden), beim Entschlüsseln und Öffnen aller Dateiformate, um Bilder und Text zu extrahieren, beim Extrahieren der gewünschten Informationen mittels verschiedener KI-Funktionen sowie bei deren Indizierung in einem Suchindex für schnelles Suchen, Abrufen und Erkunden. 

Als Eingaben fungieren Ihre Blobs in einem einzelnen Container in Azure Blob Storage. Blobs können aus nahezu beliebigen Arten von Text- oder Bilddaten bestehen. 

Die Ausgabe ist stets ein Suchindex, der für schnelles Suchen, Abrufen und Erkunden von Texten in Clientanwendungen genutzt wird. Darüber hinaus kann die Ausgabe auch ein [*Wissensspeicher*](knowledge-store-concept-intro.md) sein, der angereicherte Dokumente in Azure-Blobs oder Azure-Tabellen für die Downstreamanalyse in Tools wie Power BI oder in Data-Science-Workloads projiziert.

Dazwischen befindet sich die Pipelinearchitektur selbst. Die Pipeline basiert auf den [*Indexern*](search-indexer-overview.md), denen Sie ein [*Skillset*](cognitive-search-working-with-skillsets.md) zuweisen können, das aus einer oder mehreren *Skills* (Fähigkeiten) besteht, die die KI bereitstellen. Der Zweck der Pipeline besteht darin, *angereicherte Dokumente* zu erzeugen, die als Rohdateninhalt in die Pipeline eingehen, aber zusätzliche Struktur, Kontext und Informationen beim Durchlaufen der Pipeline aufnehmen. Angereicherte Dokumente werden während der Indizierung genutzt, um invertierte Indizes und andere Strukturen zu erstellen, die bei der Volltextsuche oder Erkundung und Analyse verwendet werden.

## <a name="required-resources"></a>Erforderliche Ressourcen

Zusätzlich zu Azure Blob Storage und Azure Cognitive Search benötigen Sie einen weiteren Dienst oder Mechanismus, der die KI bereitstellt:

+ Für eine integrierte KI wird Cognitive Search mit der API für maschinelles Sehen sowie der API für die Verarbeitung natürlicher Sprache von Azure Cognitive Services integriert. Sie können [eine Cognitive Services-Ressource anfügen](cognitive-search-attach-cognitive-services.md), um die optische Zeichenerkennung (Optical Character Recognition, OCR), die Bildanalyse oder die Verarbeitung natürlicher Sprache (Sprachenerkennung, Textübersetzung, Entitätserkennung, Schlüsselbegriffserkennung) hinzuzufügen. 

+ Für eine benutzerdefinierte KI, die Azure-Ressourcen verwendet, können Sie einen benutzerdefinierten Skill definieren, der die externe Funktion oder das gewünschte Modell umschließt, das Sie verwenden möchten. [Benutzerdefinierte Skills](cognitive-search-custom-skill-interface.md) können Code verwenden, der von Azure Functions, Azure Machine Learning, der Azure-Formularerkennung oder einer anderen Ressource bereitgestellt wird, die über HTTPS erreichbar ist.

+ Für eine benutzerdefinierte Azure-fremde KI muss das Modell oder Modul für einen Indexer über HTTP zugänglich sein.

Wenn nicht alle Dienste sofort verfügbar sind, starten Sie direkt auf der Portalseite des Speicherkontos. Klicken Sie auf der linken Navigationsseite unter **Blob-Dienst** auf **Azure Cognitive Search hinzufügen**, um einen neuen Dienst zu erstellen oder einen bestehenden auszuwählen. 

Nachdem Sie Ihrem Speicherkonto Azure Cognitive Search hinzugefügt haben, können Sie den Standardprozess zum Anreichern von Daten in einer beliebigen Azure-Datenquelle befolgen. Wir empfehlen für eine einfache erste Einführung in die KI-Anreicherung den Assistenten **Daten importieren** in Azure Cognitive Search. Sie können eine Cognitive Services-Ressource während des Workflows anfügen. Dieser Schnellstart führt Sie durch diese Schritte: [Erstellen einer KI-Anreicherungspipeline im Portal](cognitive-search-quickstart-blob.md). 

In den folgenden Abschnitten sehen Sie sich die Komponenten und Workflows genauer an.

## <a name="use-a-blob-indexer"></a>Verwenden eines Blobindexers

KI-Anreicherung ist ein Add-On für eine Indizierungspipeline, und in Azure Cognitive Search bauen diese Pipelines auf einem *Indexer* auf. Ein Indexer ist ein datenquellenabhängiger Subdienst mit interner Logik zum Nehmen von Stichproben von Daten, Lesen von Metadaten, Abrufen von Daten und Serialisieren von Daten aus nativen Formaten in JSON-Dokumente zum anschließenden Import. Indexer werden oft alleine, getrennt von KI, für den Import verwendet, doch wenn Sie eine KI-Anreicherungspipeline erstellen möchten, benötigen Sie einen Indexer und ein begleitendes Skillset. In diesem Abschnitt konzentrieren wir uns auf den Indexer, während sich der nächste Abschnitt mit Skillsets beschäftigt.

Blobs in Azure Storage werden mithilfe des [Blobindexers](search-howto-indexing-azure-blob-storage.md) indiziert. Sie können diesen Indexer mit dem Assistenten **Daten importieren**, mit einer REST-API oder einem SDK aufrufen. Ein Blobindexer wird aufgerufen, wenn es sich bei der vom Indexer verwendeten Datenquelle um einen Azure-Blobcontainer handelt. Sie können einen Teil Ihrer Blobs indizieren, indem Sie ein virtuelles Verzeichnis erstellen, das Sie dann als Parameter übergeben können, oder indem Sie nach einer Dateityperweiterung filtern.

Ein Indexer übernimmt die Dokumententschlüsselung und öffnet ein Blob, um den Inhalt zu inspizieren. Nach dem Herstellen einer Verbindung mit der Datenquelle ist dies der erste Schritt in der Pipeline. Bei Blobdaten werden an dieser Stelle PDF-Dateien, Office-Dokumente, Bilder und andere Inhaltstypen erkannt. Die Dokumententschlüsselung mit Textextraktion erfolgt gebührenfrei. Dokumententschlüsselung mit Bildextraktion wird entsprechen den Gebühren berechnet, die Sie auf der [Preisseite](https://azure.microsoft.com/pricing/details/search/) finden.

Obwohl alle Dokumente entschlüsselt werden, erfolgt die Anreicherung nur, wenn Sie die dazu erforderlichen Skills (Fähigkeiten) explizit bereitstellen. Wenn Ihre Pipeline beispielsweise ausschließlich aus Bildanalyse besteht, wird Text in Ihrem Container bzw. in den Dokumenten ignoriert.

Der Blobindexer verfügt über Konfigurationsparameter und unterstützt die Nachverfolgung von Änderungen, wenn die zugrunde liegenden Daten genügend Informationen liefern. Weitere Informationen finden Sie unter [Konfigurieren eines Blobindexers](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Hinzufügen von KI-Komponenten

Mit KI-Anreicherung sind Module gemeint, die nach Mustern oder Merkmalen suchen und einen Vorgang anschließend entsprechend ausführen. Anschauliche Beispiele hierfür sind Gesichtserkennung in Fotos, Textbeschreibungen von Fotos, das Erkennen von Schlüsselaussagen in einem Dokument und OCR (oder das Erkennen von gedrucktem oder handschriftlichem Text in Binärdateien).

In Azure Cognitive Search sind *Skills* die einzelnen Komponenten der KI-Verarbeitung, die Sie eigenständig oder in Kombination mit anderen Skills verwenden können. 

+ Integrierte Skills werden von Cognitive Services durch Bildanalyse, basierend auf maschinellem Sehen, und die Verarbeitung natürlicher Sprache auf der Grundlage von Textanalyse unterstützt. Die komplette Liste finden Sie unter [Integrierte Qualifikationen für die Inhaltsanreicherung](cognitive-search-predefined-skills.md).

+ Benutzerdefinierte Skills sind benutzerdefinierter Code, der von einer [Schnittstellendefinition](cognitive-search-custom-skill-interface.md) umschlossen wird, die die Integration in die Pipeline ermöglicht. Bei Kundenlösungen ist es gängige Praxis, beides zu verwenden, wobei benutzerdefinierte Skills Open-Source-, Drittanbieter- oder Erstanbieter-KI-Module bereitstellen.

Ein *Skillset* ist die Sammlung von Skills (Fertigkeiten), die in einer Pipeline verwendet wird. Es wird aufgerufen, nachdem der Inhalt durch die Dokumententschlüsselungsphase verfügbar gemacht worden ist. Ein Indexer kann exakt ein Skillset nutzen, wobei dieses Skillset aber unabhängig von einem Indexer existiert, sodass Sie es in anderen Szenarien wiederverwenden können.

Benutzerdefinierte Skills können komplex klingen, sind aber im Hinblick auf die Implementierung einfach und unkompliziert sein. Wenn Sie vorhandene Pakete besitzen, die Musterabgleichs- oder Klassifizierungsmodelle bereitstellen, kann der Inhalt, den Sie aus Blobs extrahieren, zur Verarbeitung an diese Modelle übergeben werden. Da die KI-Anreicherung Azure-basiert ist, sollte sich Ihr Modell ebenfalls in Azure befinden. Einige gängige Hostingmethoden umfassen unter anderem die Verwendung von [Azure Functions](cognitive-search-create-custom-skill-example.md) oder [Containern](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Integrierte Skills, die von Cognitive Services unterstützt werden, erfordern einen [angefügten Cognitive Services](cognitive-search-attach-cognitive-services.md) All-in-One-Abonnementschlüssel, der Ihnen Zugriff auf die Ressource gewährt. Ein All-in-One-Schlüssel bietet Ihnen Bildanalyse, Spracherkennung, Textübersetzung und Textanalyse. Andere integrierte Skills sind Funktionen von Azure Cognitive Search und erfordern keinen zusätzlichen Dienst oder Schlüssel. Text Shaper, Textteilung und Textzusammenführung sind Beispiele für unterstützende Skills, die manchmal beim Entwerfen der Pipeline erforderlich sind.

Wenn Sie nur benutzerdefinierte Skills und integrierte Hilfsprogrammskills verwenden, besteht weder eine Abhängigkeit von Cognitive Services, noch gehen Kosten damit einher.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Nutzen von mit KI angereicherte Ausgaben in Downstreamlösungen

Die Ausgabe der KI-Anreicherung ist entweder ein Suchindex in Azure Cognitive Search oder ein [Wissensspeicher](knowledge-store-concept-intro.md) in Azure Storage.

In Azure Cognitive Search wird ein Suchindex für die interaktive Erkundung mithilfe von Freitext- und gefilterten Abfragen in einer Client-App verwendet. Mittels KI erstellte, angereicherte Dokumente sind in JSON formatiert und werden auf dieselbe Weise indiziert, wie alle Dokumente in Azure Cognitive Search indiziert werden, wobei alle von einem Indexer bereitgestellten Vorteile genutzt werden. Beispielsweise verweist der Blobindexer während der Indizierung auf Konfigurationsparameter und -einstellungen, um jegliche Feldzuordnungen oder Änderungserkennungslogiken zu verwenden. Solche Einstellungen stehen für reguläre Indizierungs- sowie für mit KI angereicherte Workloads vollständig zur Verfügung. Nach der Indizierung, wenn Inhalte in Azure Cognitive Search gespeichert sind, können Sie umfangreiche Abfragen und Filterausdrücke erstellen, um Ihre Inhalte zu verstehen.

In Azure Storage stellt sich ein Wissensspeicher auf zwei Arten dar: als Blobcontainer oder als Tabellen im Tabellenspeicher. 

+ Ein Blobcontainer erfasst angereicherte Dokumente in ihrer Gesamtheit, was nützlich ist, wenn Sie an andere Prozesse übergeben möchten. 

+ Im Gegensatz dazu kann Tabellenspeicher physische Projektionen angereicherter Dokumente aufnehmen. Sie können Slices oder Ebenen von angereicherten Dokumenten erstellen, die bestimmte Teile ein- bzw. ausschließen. Für die Analyse in Power BI werden die Tabellen im Azure-Tabellenspeicher zur Datenquelle für die weitere Visualisierung und Erkundung.

Ein angereichertes Dokument am Ende der Pipeline unterscheidet sich von der ursprünglichen Eingabeversion durch das Vorhandensein zusätzlicher Felder, die neue Informationen enthalten, die während der Anreicherung extrahiert oder generiert wurden. Daher können Sie mit einer Kombination aus ursprünglichen und erstellten Inhalten arbeiten, unabhängig von der verwendeten Ausgabestruktur.

## <a name="next-steps"></a>Nächste Schritte

Es gibt noch viele weitere Anwendungsmöglichkeiten für die KI-Anreicherung, um Ihre Daten in Azure Storage optimal auszuwerten, einschließlich verschiedener Arten der Kombination von Cognitive Services und der Erstellung benutzerdefinierter Skills für Fälle, in denen kein Cognitive Service für das Szenario vorhanden ist. Weitere Informationen finden Sie unter den folgenden Links.

+ [Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Einrichten eines Blobindexers (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Übersicht über KI-Anreicherung (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Erstellen eines Skillsets (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Zuordnen von Knoten in einer Anmerkungsstruktur (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)