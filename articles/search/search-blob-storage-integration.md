---
title: Hinzufügen der Volltextsuche zu Azure Blob Storage – Azure Search
description: Durchforsten Sie Textinhalte in Azure Blob Storage nach Azure Search-Indizierung im Code mithilfe der HTTP-REST-API.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b6bb70e4c56adb162006d2597d301c73b12d2a8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65540888"
---
# <a name="searching-blob-storage-with-azure-search"></a>Durchsuchen von Blob Storage mit Azure Search

Das Durchsuchen verschiedenster in Azure Blob Storage gespeicherter Inhalte kann ein schwer zu lösendes Problem sein. Allerdings können Sie den Inhalt Ihrer Blobs mit nur wenigen Klicks indizieren und durchsuchen, und zwar mit Azure Search. Das Durchsuchen von Blob Storage erfordert das Bereitstellen eines Azure Search-Dienstes. Die verschiedenen Dienstgrenzwerte und -tarife von Azure Search finden Sie auf der [Übersicht der Preise](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Was ist Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) ist ein Suchdienst, der es Entwicklern leicht macht, stabile Möglichkeiten zur Volltextsuche in Web- und Mobilanwendungen bereitzustellen. Azure Search macht als Dienst das Verwalten einer Suchinfrastruktur überflüssig und bietet gleichzeitig eine [Betriebszeit-SLA von 99,9 %](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Unternehmensdokumentformate für Indizierung und Suche
Mit der Unterstützung für die [Dokumentextrahierung](https://aka.ms/azsblobindexer) in Azure Blob Storage können Sie folgende Inhalte indizieren:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Durch Extrahieren von Text und Metadaten aus diesen Dateitypen können Sie mehrere Dateiformate mit einer einzelnen Abfrage durchsuchen. 

## <a name="search-through-your-blob-metadata"></a>Durchsuchen Ihrer Blob-Metadaten
Das Indizieren von benutzerdefinierten Metadaten sowie von Systemeigenschaften für jedes Blob ist ein häufig auftretendes Szenario, mit dem Sie Blobs jedes Inhaltstyps unkompliziert durchsehen können. Auf diese Weise werden die Informationen für alle Blobs unabhängig vom Dokumenttyp indiziert. Sie können dann den gesamten Blob Storage-Inhalt sortieren und filtern sowie Facets bilden.

[Weitere Informationen zum Indizieren von Blob-Metadaten.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Bildersuche
Die Volltextsuche, die Facettennavigation und die Sortierungsfunktionen von Azure Search können auch auf die Metadaten von in Blobs gespeicherten Bildern angewendet werden.

Kognitive Suche enthält Bildverarbeitungsqualifikationen wie [Optische Zeichenerkennung (OCR)](cognitive-search-skill-ocr.md) und Identifizierung von [visuellen Features](cognitive-search-skill-image-analysis.md), die es ermöglichen, den in den einzelnen Bildern gefundenen visuellen Inhalt zu indizieren.

## <a name="index-and-search-through-json-blobs"></a>Indizieren und Durchsuchen von JSON-Blobs
Azure Search kann so konfiguriert werden, dass es strukturierten Inhalt aus Blobs extrahiert, die eine JSON-Datei enthalten. Azure Search kann aus JSON-Blobs lesen und den strukturierten Inhalt in den entsprechenden Felder in einem Azure Search-Dokument analysieren. Azure Search kann außerdem Blobs annehmen, die ein Array von JSON-Objekten enthalten und jedes Element mit einem separaten Azure Search-Dokument verknüpfen.

Die JSON-Analyse kann derzeit nicht über das Portal konfiguriert werden. [Erfahren Sie mehr zur JSON-Analyse in Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Schnellstart
Azure Search kann Blobs direkt über die Blob Storage-Portalseite hinzugefügt werden.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicken Sie auf **Azure Search hinzufügen**, um einen Vorgang zu starten, bei dem Sie einen vorhandenen Azure Search-Dienst auswählen oder einen neuen Dienst erstellen können. Wenn Sie einen neuen Dienst erstellen, verlassen Sie den Portalbereich Ihres Speicherkontos. Sie können zurück zur Storage-Portalseite navigieren, und erneut die Option **Azure Search hinzufügen** auswählen, um den vorhandenen Dienst auszuwählen.

## <a name="next-steps"></a>Nächste Schritte
In der vollständigen [Dokumentation](https://aka.ms/azsblobindexer) erfahren Sie mehr zum Azure Search-Blobindexer.
