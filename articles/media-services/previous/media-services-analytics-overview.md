---
title: Media Analytics auf der Media Services-Plattform | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über die öffentliche Vorschau von Media Analytics, einer Sammlung von Diensten in den Bereichen Sprache und maschinelles Sehen auf Unternehmensniveau, Compliance, Sicherheit und globale Reichweite.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: c56e3781-8510-4f7f-b5ff-a218c1bb6f4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: milanga;juliako;johndeu
ms.openlocfilehash: ceaf4d3db71d99c3e87157f9847312fdf4000026
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991791"
---
# <a name="media-analytics-on-the-media-services-platform"></a>Media Analytics auf der Media Services-Plattform 

## <a name="overview"></a>Übersicht
Immer mehr Organisationen sehen in Video das bevorzugte Medium zum Schulen ihrer Mitarbeiter, Binden ihrer Kunden und Dokumentieren von Geschäftsfunktionen. Cloud Computing bietet eine Möglichkeit zum Speichern und Streamen großer Mediendateien sowie zum Zugreifen auf diese. Wenn die Videothek des Unternehmens jedoch wächst, benötigt sie ein ebenso effektives Verfahren zum Extrahieren von Erkenntnisse aus dem Inhalt. 

Für diesen zunehmenden Bedarf bietet Azure Media Services das Tool Azure Media Analytics an. Media Analytics ist eine Sammlung aus Sprach- und Bildanalysekomponenten, mit denen Organisationen und Unternehmen anhand von Videodateien Erkenntnisse gewinnen, aus denen sich umsetzbare Maßnahmen ableiten lassen. Media Analytics basiert auf den Kernkomponenten der Media Services-Plattform und kann daher die skalierte Medienverarbeitung ab dem ersten Tag bewältigen.

Mit Media Analytics können Entwickler schnell erweiterte Videofunktionen in Anwendungen integrieren. Da Tool bietet in Unternehmensumgebungen volle Skalierung mit Compliance, Sicherheit und globaler Reichweite – wie von großen Organisationen gefordert.

Im folgenden Diagramm werden Media Analytics und andere wichtige Bestandteile der Media Services-Plattform dargestellt. 

![VoD-Workflow](./media/media-services-analytics-overview/media-services-analytics-overview01.png)

Media Analytics-Medienprozessoren generieren MP4- oder JSON-Dateien. Wenn ein Medienprozessor eine MP4-Datei erstellt, können Sie die Datei progressiv herunterladen. Wenn ein Medienprozessor eine JSON-Datei erstellt, können Sie die Datei aus Azure Blob Storage herunterladen. 

## <a name="media-analytics-services"></a>Media Analytics-Dienste

### <a name="indexer"></a>Indexer
Mit Azure Media Indexer können Sie Inhalte durchsuchbar machen und Untertitelspuren generieren. Verglichen mit der vorherigen Version führt Azure Media Indexer 2 Preview eine schnellere Indizierung durch und bietet eine breitere Sprachunterstützung. Zu den unterstützten Sprachen zählen Englisch, Spanisch, Französisch, Deutsch, Italienisch, Chinesisch, Portugiesisch und Arabisch. Ausführliche Informationen und Beispiele finden Sie unter [Verarbeiten von Videos mit Azure Media Indexer 2](media-services-process-content-with-indexer2.md).
### <a name="motion-detector"></a>Motion Detector
Sie können Motion Detector verwenden, um Bewegungen in einem Video mit unbewegtem Hintergrund zu erkennen. Dies ermöglicht die Überprüfung auf falsche Positive bei Bewegungsereignissen, die von Überwachungskameras erkannt wurden. Ausführliche Informationen und Beispiele finden Sie unter [Bewegungserkennung mit Azure Media Analytics](media-services-motion-detection.md).
### <a name="face-detector"></a>Face Detector
Mithilfe von Face Detector können Sie die Gesichter von Personen und ihre Emotionen wie Glück, Trauer und Überraschung erkennen. Dies dient mehreren unten beschriebenen professionellen Anwendungen, inklusive dem Aggregieren und Analysieren der Reaktionen von Personen, die an einem Ereignis teilnehmen. Ausführliche Informationen und Beispiele finden Sie unter [Gesichts- und Gesichtsausdruckerkennung mit Azure Media Analytics](media-services-face-and-emotion-detection.md).
### <a name="video-summarization"></a>Videozusammenfassung
Die Videozusammenfassung kann Ihnen dabei helfen, Zusammenfassungen von langen Videos durch das automatische Auswählen von interessanten Ausschnitten aus dem Quellvideo zu erstellen. Dies ist hilfreich, wenn Sie eine schnelle Übersicht darüber bereitstellen möchten, was man in einem langen Video zu sehen bekommt. Ausführliche Informationen und Beispiele finden Sie unter [Verwenden von Azure Media-Videovorschau zum Erstellen einer Videozusammenfassung](media-services-video-summarization.md).
### <a name="optical-character-recognition"></a>Optische Zeichenerkennung
Azure Media OCR (Optical Character Recognition, Optische Zeichenerkennung) dient zum Umwandeln von Textinhalten in Videodateien in bearbeitbaren und durchsuchbaren digitalen Text. Anschließend können Sie die Extraktion aussagekräftiger Metadaten aus dem Videosignal Ihrer Medien automatisieren.
### <a name="scalable-face-redaction"></a>Skalierbare Gesichtsbearbeitung
Azure Media Redactor ist ein Media Analytics-Medienprozessor, der eine skalierbare Gesichtsbearbeitung in der Cloud ermöglicht. Mit der Gesichtsbearbeitung können Sie Ihr Video ändern, um Gesichter von ausgewählten Personen unscharf anzuzeigen und so unkenntlich zu machen. Sie können den Gesichtsbearbeitungsdienst beispielsweise bei Nachrichten nutzen, wenn es um die öffentliche Sicherheit geht. Die Bearbeitung von Material mit einer Länge von einigen Minuten, das mehrere Gesichter enthält, kann bei manueller Vorgehensweise Stunden dauern. Mit diesem Dienst sind für den Prozess der Gesichtsbearbeitung aber nur einige einfache Schritte erforderlich. Weitere Informationen finden Sie im Artikel [Bearbeiten von Gesichtern mit Azure Media Analytics](media-services-face-redaction.md).
### <a name="content-moderation"></a>Inhaltsmoderation
Azure Content Moderator bietet Ihnen hardwareunterstützte Moderation für Ihre Videos. Sie möchten beispielsweise mögliche jugendgefährdende und rassistische Inhalte in Videos erkennen und die gekennzeichneten Inhalte von Ihren menschlichen Moderationsteams überprüfen lassen. Die manuelle Moderation von Videos auf unerwünschten Inhalt ist zeitaufwändig und teuer. Mit diesem Dienst und zugehörigen Überprüfungstools kombinieren Sie hardwareunterstützte Moderation mit den Fähigkeiten der involvierten Personen, um effiziente und kostengünstige Ergebnisse zu erzielen. Weitere Informationen finden Sie im Artikel [Verwenden von Azure Media Content Moderator zum Erkennen möglicher jugendgefährdender und rassistischer Inhalte](media-services-content-moderation.md).

## <a name="common-scenarios"></a>Häufige Szenarios
Media Analytics hilft Organisationen und Unternehmen bei der Gewinnung von Erkenntnissen aus Videos und bei der effektiven Verwaltung großer Mengen von Videoinhalten. Mögliche Szenarien:

* **Callcenter:** Trotz des Aufkommens sozialer Medien sind Kundencallcenter weiterhin für einen hohen Prozentsatz der Transaktionen im Kundendienst zuständig. In diesen Audiodaten ist eine große Menge an Kundeninformationen enthalten, die analysiert werden kann, um eine höhere Kundenzufriedenheit zu erzielen. Mithilfe von Media Indexer können Organisationen Text extrahieren und Suchindizes und Dashboards erstellen. Sie können dann Daten zu häufigen Beschwerden und ihren Ursachen sowie andere relevante Daten extrahieren.
* **Moderation benutzergenerierter Inhalte:** Viele Organisationen – von Nachrichtenkanälen bis zu Polizeibehörden – verfügen über Öffentlichkeitsportale, in denen sie benutzergenerierte Medien wie Videos und Bilder akzeptieren. Bei unerwarteten Ereignissen kann die Menge der Inhalte eskalieren. In diesen Szenarien ist sehr schwierig, eine effektive manuelle Auswertung des Inhalts auf Angemessenheit durchzuführen. Kunden können sich darauf verlassen, dass der Inhaltsmoderationsdienst sich auf angemessene Inhalte konzentriert.

## <a name="media-analytics-media-processors"></a>Media Analytics-Medienprozessoren
In diesem Abschnitt sind alle Media Analytics-Medienprozessoren (MP) aufgeführt. Zudem wird gezeigt, wie Sie mithilfe von .NET oder REST ein MP-Objekt abrufen.

### <a name="mp-names"></a>MP-Namen
* Azure Media Indexer 2 Preview
* Azure Media Indexer
* Azure Media Face Detector
* Azure Media Motion Detector
* Azure Media Video Thumbnails
* Azure Media OCR
* Azure Media Content Moderator

### <a name="net"></a>.NET
Die folgende Funktion verwendet einen der angegebenen MP-Namen und gibt ein MP-Objekt zurück.

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
            .Where(p => p.Name == mediaProcessorName)
            .ToList()
            .OrderBy(p => new Version(p.Version))
            .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }


### <a name="rest"></a>REST
Anforderung:

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20OCR' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <token>
    x-ms-version: 2.12
    Host: media.windows.net

Antwort:

    . . .

    {  
       "odata.metadata":"https://media.windows.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:074c3899-d9fb-448f-9ae1-4ebcbe633056",
             "Description":"Azure Media OCR",
             "Name":"Azure Media OCR",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

## <a name="demos"></a>Demos
Siehe [Azure Media Analytics-Demos](https://azuremedialabs.azurewebsites.net/demos/Analytics.html).

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Verwandte Artikel
Siehe [Media Services Analytics – Ankündigung](https://azure.microsoft.com/blog/introducing-azure-media-analytics/).

<!-- Images -->

[overview]: ./media/media-services-video-on-demand-workflow/media-services-video-on-demand.png

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
