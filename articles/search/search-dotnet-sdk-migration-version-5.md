---
title: 'Upgrade auf Azure Search .NET SDK Version 5: Azure Search'
description: Migrieren Sie Code von älteren Versionen zu Azure Search .NET SDK Version 5. Hier finden Sie Informationen zu Neuheiten und erfahren, welche Änderungen am Code erforderlich sind.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 8382884b4ce2965dee4acf191f82eb012b670713
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65147489"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-5"></a>Upgrade auf Version 5 des Azure Search .NET SDK

Wenn Sie die Version „4.0-preview“ oder eine frühere Version des [Azure Search .NET SDK](https://aka.ms/search-sdk) verwenden, unterstützt dieser Artikel Sie beim Upgrade Ihrer Anwendung auf Version 5.

Eine allgemeinere exemplarische Vorgehensweise für das SDK sowie Beispiele finden Sie unter [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md).

Version 5 des Azure Search .NET SDK enthält einige Änderungen zu früheren Versionen. Dabei handelt es sich hauptsächlich um kleinere Änderungen, sodass zum Ändern Ihres Codes nur ein geringer Aufwand erforderlich sein sollte. Anweisungen zum Ändern Ihres Codes, um die neue SDK-Version zu verwenden, finden Sie unter [Schritte zum Upgrade](#UpgradeSteps) .

> [!NOTE]
> Wenn Sie Version „2.0-preview“ oder eine frühere Version verwenden, sollten Sie zuerst ein Upgrade auf Version 3 und anschließend auf Version 5 durchführen. Anweisungen finden Sie unter [Upgrade auf Version 3 des Azure Search .NET SDK](search-dotnet-sdk-migration.md).
>
> Ihre Azure Search-Dienstinstanz unterstützt mehrere REST-API-Versionen, einschließlich der neuesten. Sie können auch ältere Versionen weiterhin verwenden, aber es wird empfohlen, den Code zur neuesten Version zu migrieren. Wenn Sie die REST-API verwenden, müssen Sie die API-Version bei jeder Anforderung über den api-version-Parameter angeben. Wenn Sie das .NET SDK verwenden, legt die Version des verwendeten SDK die Version der REST-API fest. Wenn Sie ein älteres SDK verwenden, können Sie diesen Code weiterhin ohne Änderungen ausführen, selbst wenn der Dienst aktualisiert wird, um eine neuere Version der API zu unterstützen.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>Neuerungen in Version 5
Version 5 des Azure Search .NET SDK ist für die neueste allgemein verfügbare Version der Azure Search-REST-API (2017-11-11) konzipiert. Dadurch können neue Features von Azure Search in einer .NET-Anwendung verwendet werden. Hierzu zählen etwa folgende:

* [Synonyme](search-synonyms.md).
* Sie können jetzt programmgesteuert im Ausführungsverlauf des Indexers auf Warnungen zugreifen (weitere Einzelheiten finden Sie unter der Eigenschaft `Warning` von `IndexerExecutionResult` in der [.NET-Referenz](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)).
* Unterstützung für .NET Core 2.
* Eine neue Paketstruktur unterstützt, dass nur die Teile des SDK verwendet werden, die Sie benötigen (weitere Einzelheiten finden Sie unter [Wichtige Änderungen in Version 5](#ListOfChanges)).

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Schritte zum Upgrade
Aktualisieren Sie zunächst die NuGet-Referenz für `Microsoft.Azure.Search` , indem Sie entweder die NuGet-Paket-Manager-Konsole verwenden oder mit der rechten Maustaste auf die Projektverweise klicken und in Visual Studio „NuGet-Pakete verwalten...“ auswählen.

Nachdem NuGet die neuen Pakete und deren Abhängigkeiten heruntergeladen hat, erstellen Sie Ihr Projekt neu. Je nachdem, wie Ihr Code strukturiert ist, verläuft die Neuerstellung erfolgreich. Wenn dies der Fall ist, sind Sie startbereit!

Wenn beim Erstellen ein Fehler auftritt, wird etwa folgende Buildfehlermeldung angezeigt:

    The name 'SuggesterSearchMode' does not exist in the current context

Der nächste Schritt besteht darin, diesen Buildfehler zu beheben. Unter [Wichtige Änderungen in Version 5](#ListOfChanges) finden Sie Informationen zur Fehlerursache und -behebung.

Beachten Sie, dass Sie aufgrund von Änderungen des Azure Search .NET SDK an der Paketerstellung Ihre Anwendung neu erstellen müssen, um Version 5 verwenden zu können. Diese Änderungen werden unter [Wichtige Änderungen in Version 5](#ListOfChanges) ausführlich behandelt.

Möglicherweise werden zusätzliche Buildwarnungen im Zusammenhang mit veralteten Methoden oder Eigenschaften angezeigt. Die Warnung enthält Anweisungen dazu, was Sie anstelle der veralteten Funktion verwenden sollten. Wenn Ihre Anwendung beispielsweise die Methode `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` verwendet, müsste Ihnen folgende Warnung angezeigt werden: „Dieses Verhalten ist jetzt standardmäßig aktiviert, ein Aufrufen der Methode ist daher nicht mehr erforderlich.“

Sobald Sie alle Buildfehler und -warnungen behoben haben, können Sie Änderungen an Ihrer Anwendung vornehmen, um die neue Funktionalität zu nutzen. Neue Features im SDK werden unter [Neuerungen in Version 5](#WhatsNew) ausführlich behandelt.

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>Wichtige Änderungen in Version 5

### <a name="new-package-structure"></a>Neue Paketstruktur

Die grundlegendste wichtige Änderung in Version 5 besteht darin, dass die Assembly `Microsoft.Azure.Search` und die zugehörigen Inhalte in vier separate Assemblys unterteilt wurden, die jetzt in Form von vier separaten NuGet-Paketen verteilt werden:

 - `Microsoft.Azure.Search`: Dies ist ein Metapaket, das alle anderen Azure Search-Pakete als Abhängigkeiten enthält. Wenn Sie ein Upgrade von einer früheren Version des SDK durchführen, müsste es ausreichen, wenn Sie nur für dieses Paket ein Upgrade durchführen und das Paket neu erstellen, um die neue Version verwenden zu können.
 - `Microsoft.Azure.Search.Data`: Verwenden Sie dieses Paket, wenn Sie eine .NET-Anwendung mit Azure Search entwickeln und Sie nur Dokumente in Ihren Indizes abfragen oder aktualisieren müssen. Wenn Sie auch Indizes, Synonymzuordnungen oder andere Servicelevelressourcen erstellen oder aktualisieren müssen, verwenden Sie stattdessen das `Microsoft.Azure.Search`-Paket.
 - `Microsoft.Azure.Search.Service`: Verwenden Sie dieses Paket, wenn Sie in .NET eine Automatisierung zum Verwalten von Azure Search-Indizes, Synonymzuordnungen, Indexern, Datenquellen oder anderen Ressourcen auf Dienstebene entwickeln. Wenn Sie nur Dokumente in Ihren Indizes abfragen oder aktualisieren müssen, verwenden Sie stattdessen das `Microsoft.Azure.Search.Data`-Paket. Wenn Sie alle Funktionen von Azure Search benötigen, verwenden Sie stattdessen das `Microsoft.Azure.Search`-Paket.
 - `Microsoft.Azure.Search.Common`: Gängige Typen, die von Azure Search .NET-Bibliotheken benötigt werden. Es sollte nicht erforderlich sein, dass Sie dieses Paket direkt in Ihrer Anwendung verwenden; es soll lediglich als Abhängigkeit verwendet werden.
 
Dies ist genau genommen eine wichtige Änderung, da viele Typen zwischen Assemblys verschoben wurden. Deshalb ist es erforderlich, dass Sie Ihre Anwendung neu erstellen, damit ein Upgrade auf Version 5 des SDK durchgeführt werden kann.

Version 5 enthält nur einige wenige wichtige Änderungen, die neben der Neuerstellung Ihrer Anwendung ggf. auch Codeänderungen erfordern.

### <a name="change-to-suggesters"></a>Änderung für die Vorschlagsfunktion 

Der Konstruktor `Suggester` verfügt nicht mehr über den Parameter `enum` für `SuggesterSearchMode`. Diese Enumeration hatte nur einen einzelnen Wert und war daher überflüssig. Sollte diese Änderung bei Ihnen zu Buildfehlern führen, entfernen Sie einfach die Verweise auf den Parameter `SuggesterSearchMode`.

### <a name="removed-obsolete-members"></a>Veraltete Member wurden entfernt.

Unter Umständen treten im Zusammenhang mit Methoden oder Eigenschaften Buildfehler auf, die in früheren Versionen als veraltet markiert waren und nun in Version 5 entfernt wurden. Solche Fehler lassen sich wie folgt beheben:

- Wenn Sie die Methode `IndexingParametersExtensions.IndexStorageMetadataOnly` verwendet haben, verwenden Sie stattdessen `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)`.
- Wenn Sie die Methode `IndexingParametersExtensions.SkipContent` verwendet haben, verwenden Sie stattdessen `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)`.

### <a name="removed-preview-features"></a>Entfernte Vorschaufeatures

Hinweis für Benutzer, die ein Upgrade von Version „4.0-preview“ auf Version 5 durchführen: Die JSON-Array- und CSV-Analyseunterstützung für Blob-Indexer wurde entfernt, da sich diese Features noch in der Vorschauversion befinden. Im Einzelnen wurden folgende Methoden der `IndexingParametersExtensions`-Klasse entfernt:

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Falls bei Ihrer Anwendung eine harte Abhängigkeit von diesen Features besteht, können Sie kein Upgrade auf Version 5 des Azure Search .NET SDK durchführen. Sie können weiterhin Version „4.0-preview“ verwenden. Beachten Sie jedoch, dass die Verwendung von Vorschau-SDKs in Produktionsanwendungen **nicht empfehlenswert** ist. Vorschaufeatures dienen ausschließlich zur Evaluierung und ändern sich unter Umständen.

## <a name="conclusion"></a>Zusammenfassung
Weitere Informationen zur Verwendung des Azure Search-.NET SDK finden Sie unter [Hilfe und Anleitung zu .NET](search-howto-dotnet-sdk.md).

Wir freuen uns auf Ihr Feedback zum SDK! Wenn Probleme auftreten, können Sie sich gerne über [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) an uns wenden. Wenn Sie einen Fehler finden, können Sie das Problem im [Azure .NET SDK-GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/issues)melden. Stellen Sie sicher, dass Sie dem Titel des Problems „[Azure Search]“ voranstellen.

Vielen Dank für die Nutzung von Azure Search!
