---
title: Verwenden von Azure Media Clipper im Portal | Microsoft-Dokumentation
description: Erstellen von Clips mit Azure Media Clipper im Azure-Portal
services: media-services
keywords: Clip;Schneiden;Subclip;Codierung;Medien
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 8c88caefb0909da55de87116a23fa520c1679cc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61465826"
---
# <a name="create-clips-with-azure-media-clipper-in-the-portal"></a>Erstellen von Clips im Portal mit Azure Media Clipper  

Sie können Azure Media Clipper im Portal verwenden, um Clips aus Medienobjekten in Ihren Media Services-Konten zu erstellen. Navigieren Sie zunächst zu Ihrem Media Services-Konto im Portal. Wählen Sie dann die Registerkarte **Subclip** aus.

Auf der Registerkarte **Subclip** können Sie damit beginnen, Clips zusammenzustellen. Im Portal lädt Clipper MP4-Dateien mit Einzelbitrate, MP4-Dateien mit Mehrfachbitrate und Livearchive, die mit einem gültigen Streamingfaktor veröffentlicht wurden. Nicht veröffentlichte Medienobjekte werden nicht geladen.

Clipper ist zurzeit als öffentliche Vorschauversion verfügbar. Wechseln Sie zu dieser [Seite der öffentlichen Vorschauversion](https://portal.azure.com/?feature.subclipper=true), um im Azure-Portal auf Clipper zuzugreifen.

Die folgende Abbildung veranschaulicht die Clipper Landing Page in Ihrem Media Services-Konto: ![Azure Media Clipper im Azure-Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-portal.png)

## <a name="producing-clips"></a>Erstellen von Clips
Um einen Clip zu erstellen, ziehen Sie ein Medienobjekt per Drag & Drop auf die Clipoberfläche. Wenn die Zeitenmarkierungen bekannt sind, können Sie sie manuell auf der Oberfläche eingeben. Andernfalls geben Sie das Medienobjekt wieder oder verschieben die Wiedergabeposition, um die gewünschte Start- und Endmarkierung zu finden. Wenn keine Markierungen für Start- oder Endzeit bereitgestellt wurde, startet der Clip am Anfang bzw. wird bis zum Ende des Eingabemedienobjekts wiedergegeben.

Um mit der gewünschten Genauigkeit zu navigieren – basierend auf Frames oder einer Gruppe von Bildern (GOP, Group of Pictures) –, verwenden Sie die Schaltflächen „Einen Frame vor“/„Eine GOP vor“ oder „Einen Frame zurück“/„Eine GOP zurück“. Um mehrere Medienobjekte zu schneiden, ziehen Sie mehrere Medienobjekte per Drag & Drop aus dem Auswahlbereich für Medienobjekte auf die Clipoberfläche. Sie können Medienobjekte auf der Oberfläche auswählen und in der gewünschten Reihenfolge neu anordnen. Der Auswahlbereich für Medienobjekte bietet Metadaten zu Dauer, Typ und Auflösung für jedes Medienobjekt. Wenn Sie mehrere Medienobjekte verketten, berücksichtigen Sie die Quellauflösung jeder Eingabedatei. Wenn sich die Quellauflösungen unterscheiden, wird die Eingabe mit der niedrigeren Auslösung hochskaliert, um der Auflösung des Medienobjekts mit der höchsten Auflösung zu entsprechen. Um die Ausgabe des Schnittauftrags in der Vorschau anzuzeigen, wählen Sie die Schaltfläche für die Vorschau aus. Der Clip wird zwischen den ausgewählten Start- und Endzeiten wiedergegeben.

## <a name="producing-dynamic-manifest-filters"></a>Erstellen von dynamischen Manifestfiltern
[Dynamische Manifestfilter](https://azure.microsoft.com/blog/dynamic-manifest/) beschreiben einen Satz von Regeln, die auf Manifestattributen und Medienobjekt-Zeitachsen basieren. Diese Regeln legen fest, wie sich der Streamingendpunkt auf die Wiedergabeliste der Ausgabe auswirkt (Manifest). Mithilfe des Filters können die Segmente geändert werden, die für die Wiedergabe gestreamt werden. Die von Clipper erstellten Filter sind lokal und spezifisch für das Quellmedienobjekt. Im Gegensatz zu gerenderten Clips sind Filter keine neuen Medienobjekte und erfordern keinen Codierungsauftrag für die Erstellung. Sie können über das [.NET SDK](https://docs.microsoft.com/azure/media-services/media-services-dotnet-dynamic-manifest) oder die [REST-API](https://docs.microsoft.com/azure/media-services/media-services-rest-dynamic-manifest) schnell erstellt werden, bieten jedoch nur GOP-Genauigkeit. In der Regel weisen Medienobjekte, die für das Streaming codiert wurden, eine GOP-Größe von zwei Sekunden auf.

Navigieren Sie zur Registerkarte **Medienobjekte**, und wählen Sie das gewünschte Objekt aus, um einen dynamischen Manifestfilter zu erstellen. Wählen Sie die Schaltfläche **Subclip** für das Hauptmenü aus. Wählen Sie im Menü „Erweiterte Einstellungen“ den dynamischen Manifestfilter als Schnittmodus aus. Sie können dann die gleichen Schritte ausführen, mit denen Sie einen gerenderten Clip produzieren, um den Filter zu erstellen. Filter können nur über ein einziges Medienobjekt erstellt werden.

Die folgende Abbildung veranschaulicht Clipper im Modus mit dynamischem Manifestfilter im Azure-Portal: ![Azure Media Clipper im Modus mit dynamischem Manifestfilter im Azure-Portal](media/media-services-azure-media-clipper-portal/media-services-azure-media-clipper-filter.PNG)

## <a name="submitting-clipping-jobs"></a>Übermitteln von Schnittaufträgen
Wenn Sie die Zusammenstellung des Clips abgeschlossen haben, wählen Sie die Schaltfläche zum Übermitteln von Aufträgen, um den zugehörigen Schnittauftrag oder den dynamischen Manifestaufruf zu initiieren.

## <a name="next-steps"></a>Nächste Schritte
Um mit der Verwendung von Azure Media Clipper zu beginnen, lesen Sie den Artikel mit den [ersten Schritten](media-services-azure-media-clipper-getting-started.md), um Informationen zum Bereitstellen des Widgets zu erhalten.
