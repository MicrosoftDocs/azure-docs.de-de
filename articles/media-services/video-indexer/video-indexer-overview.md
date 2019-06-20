---
title: Was ist Video Indexer?
titlesuffix: Azure Media Services
description: Dieses Thema bietet eine Übersicht über den Video Indexer-Dienst.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2c72c7c493c0a887adab147054c725a2e1c0659f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799138"
---
# <a name="what-is-video-indexer"></a>Was ist Video Indexer?

Microsoft Video Indexer ist eine Cloudanwendung, die auf Azure Media Analytics, Azure Search, Cognitive Services (beispielsweise Gesichtserkennungs-API, Microsoft Translator, Maschinelles Sehen-API und Custom Speech Service) basiert. Sie ermöglicht Ihnen, mithilfe der folgenden Video- und Audiomodelle von Video Indexer Erkenntnisse aus Ihren Videos zu gewinnen:
  
## <a name="video-insights"></a>Erkenntnisse aus Videoaufnahmen

- **Gesichtserkennung:** erkennt und gruppiert im Video gezeigte Gesichter.
- **Prominentenerkennung:** Video Indexer kann automatisch mehr als eine Million Prominente erkennen, z. B. wichtige Politiker/Politikerinnen, Schauspieler/Schauspielerinnen, Athleten/Athletinnen, Forscher/Forscherinnen und Führungskräfte aus dem Wirtschafts- und Technologiebereich. Die Daten zu diesen Prominenten sind auch auf verschiedenen bekannten Websites zu finden, z.B. IMDB und Wikipedia.
- **Kontobasierte Gesichtsidentifikation:** Video Indexer trainiert ein Modell für ein bestimmtes Konto. Gesichter im Video werden dann auf Grundlage des trainierten Modells erkannt. Weitere Informationen finden Sie unter [Anpassen eines Personenmodells mit der Video Indexer-Website](customize-person-model-with-website.md) und [Anpassen eines Personenmodells mit der Video Indexer-API](customize-person-model-with-api.md).
- **Extraktion von Gesichtern als Miniaturbild** („bestes Gesicht“): Identifiziert automatisch das am besten aufgenommene Gesicht in der jeweiligen Gruppe von Gesichtern (basierend auf Qualität, Größe und frontaler Position) und extrahiert es als Bildobjekt.
- **Optische Zeichenerkennung** (OCR): extrahiert im Video angezeigten Text.
- **Moderation visueller Inhalte:** erkennt nicht jugendfreie bzw. anzügliche visuelle Inhalte.
- **Identifikation von Beschriftungen:** identifiziert angezeigte visuelle Objekte und Aktionen.
- **Szenensegmentierung**: Bestimmt basierend auf visuellen Hinweisen, wann sich eine Szene im Video ändert. Eine Szene stellt ein einzelnes Ereignis dar und besteht aus einer Reihe von aufeinanderfolgenden Aufnahmen, die semantisch zusammenhängen. 
- **Szenenwechselerkennung**: Bestimmt basierend auf visuellen Hinweisen, wann ein Szenenwechsel im Video erfolgt. Eine Aufnahme (Szenenwechsel) umfasst ist eine Reihe von Bildern, die von derselben Filmkamera aufgenommen wurden. Weitere Informationen finden Sie unter [Szenen, Aufnahmen und Keyframes](scenes-shots-keyframes.md).
- **Erkennung schwarzer Frames:** erkennt schwarze Frames im Video.
- **Extraktion von Keyframes:** erkennt stabile Keyframes in einem Video.
- **Durchlaufender Abspann**: Identifiziert den Anfang und das Ende des durchlaufenden Abspanns am Ende von Fernsehsendungen und Filmen.

## <a name="audio-insights"></a>Erkenntnisse aus Audioaufnahmen

- **Automatische Sprachenerkennung:** Die Lösung identifiziert automatisch die vorherrschend gesprochene Sprache. Unterstützte Sprachen sind Englisch, Spanisch, Französisch, Deutsch, Italienisch, Chinesisch (vereinfacht), Japanisch, Russisch und brasilianisches Portugiesisch. Englisch wird verwendet, wenn die Sprache nicht erkannt wird.
- **Audiotranskription:** konvertiert Sprache in Text in 12 Sprachen und lässt Erweiterungen zu. Unterstützt werden Arabisch, Chinesisch (vereinfacht), Deutsch, Englisch, Französisch, Italienisch, Japanisch, brasilianisches Portugiesisch, Spanisch, Russisch, Hindi und Koreanisch.
- **Untertitelung:** erstellt Untertitel in drei Formaten: VTT, TTML und SRT.
- **Verarbeitung von zwei Kanälen:** erkennt automatisch ein getrenntes Transkript und sorgt für eine Zusammenführung auf einer einzelnen Zeitachse.
- **Rauschunterdrückung:** bereinigt (basierend auf Skype-Filtern) Telefonaudio oder verrauschte Aufnahmen.
- **Transkriptanpassung** (CRIS): ermöglicht das Trainieren benutzerdefinierter Spracherkennungsmodelle zur Erstellung branchenspezifischer Transkripte. Weitere Informationen finden Sie unter [Anpassen eines Sprachmodells mit der Video Indexer-Website](customize-language-model-with-website.md) und [Anpassen eines Sprachmodells mit den Video Indexer-APIs](customize-language-model-with-api.md).
- **Sprecheraufzählung:** kann erkennen und zuordnen, welcher Sprecher wann was gesagt hat.
- **Sprecherstatistiken:** bietet Statistiken zum Verhältnis zwischen Sprechern und ihrem Anteil an der Konversation.
- **Moderation von Textinhalten:** erkennt im Transkript des Audios anstößigen Text.
- **Audioeffekte:** erkennt Audioeffekte wie Händeklatschen, Sprache und Stille.
- **Emotionserkennung:** Erkennt Emotionen basierend auf Sprache (was gesagt wird) und Tonalität der Stimme (wie etwas gesagt wird).  Bei den Emotionen kann es sich um Freude, Trauer, Wut oder Angst handeln.
- **Übersetzung:** erstellt Übersetzungen des Audiotranskripts in 54 Sprachen.

## <a name="audio-and-video-insights-multi-channels"></a>Erkenntnisse aus Audio- und Videoaufnahmen (mehrere Kanäle)

Bei Indizierung nach einem Kanal steht ein Teilergebnis für die folgenden Modelle zur Verfügung:

- **Extraktion von Schlüsselwörtern:** extrahiert Stichwörter aus Sprache und sichtbarem Text.
- **Extrahieren von Marken:** extrahiert Marken aus Sprache und sichtbarem Text.
- **Themenerschließung:** zieht in den Transkripten einen Rückschluss auf Hauptthemen. Eine IPTC-Taxonomie erster Ebene ist eingeschlossen.
- **Artefakte**: Extrahiert für jedes der Modelle eine umfangreiche Menge von Artefakten mit höherem Detailgrad.
- **Standpunktanalyse:** erkennt anhand von Sprache und sichtbarem Text positive, negative und neutrale Stimmungen.
 
Nachdem Video Indexer die Verarbeitung und Analyse abgeschlossen hat, können Sie die Erkenntnisse aus dem Video überprüfen, zusammenstellen, suchen und veröffentlichen.

Ganz gleich, ob Sie ein Inhaltsmanager oder ein Entwickler sind – der Video Indexer-Dienst kann all Ihren Anforderungen gerecht werden. Inhaltsverantwortliche können den Dienst über das Video Indexer-Webportal nutzen, ohne eine einzige Codezeile schreiben zu müssen. Weitere Informationen finden Sie unter [Tutorial: Registrieren und Hochladen Ihres ersten Videos](video-indexer-get-started.md). Entwickler können APIs zur bedarfsorientierten Verarbeitung von Inhalten nutzen. Weitere Informationen finden Sie unter [Verwenden der Video Indexer-REST-API](video-indexer-use-apis.md). Der Dienst bietet Kunden auch die Möglichkeit, mithilfe von Widgets Videodatenströme und extrahierte Erkenntnisse in ihren eigenen Anwendungen zu veröffentlichen. Weitere Informationen finden Sie unter [Embed visual widgets in your application](video-indexer-embed-widgets.md) (Einbetten von visuellen Widgets in Ihre Anwendung).

Sie können sich mit einem vorhandenen AAD-, LinkedIn-, Facebook-, Google- oder MSA-Konto für den Dienst registrieren. Weitere Informationen finden Sie unter [Getting Started](video-indexer-get-started.md) (Erste Schritte).

## <a name="scenarios"></a>Szenarien

Im Folgenden finden Sie einige Szenarien, in denen Video Indexer nützlich sein kann.

- Suche: Aus dem Video extrahierte Erkenntnisse können verwendet werden, um die Suchfunktion für eine Videobibliothek zu verbessern. Beispielsweise kann die Indizierung von gesprochenem Text und Gesichtern die Suche nach Stellen in einem Video ermöglichen, in denen eine Person bestimmte Wörter benutzt hat oder zwei Personen zusammen gezeigt werden. Die Suche auf Grundlage solcher Erkenntnisse ist für Nachrichtenagenturen, Bildungseinrichtungen, Rundfunkanstalten, Besitzer von Unterhaltungsinhalten, Branchenanwendungen und im Allgemeinen für alle Branchen von Nutzen, die über eine Videobibliothek verfügen, in der Benutzer eine Suche durchführen müssen.
- Inhaltserstellung: Aus Videos extrahierte Erkenntnisse und Unterstützung beim effektiven Erstellen von Inhalten wie Trailer, Inhalte von sozialen Medien, Nachrichtenclips usw. aus vorhandenen Inhalten im Unternehmensarchiv. 
- Monetarisierung: Video Indexer kann Sie dabei unterstützen, den kommerziellen Wert von Videos zu verbessern. Beispielsweise können Unternehmen aus Branchen, die auf Werbeeinnahmen angewiesen sind (beispielsweise Nachrichtenmedien, soziale Medien usw.) wirkungsvollere Anzeigen schalten, indem sie die extrahierten Erkenntnisse als zusätzliche Signale für den Anzeigenserver nutzen (die Präsentation einer Sportschuhanzeige ist z. B. während eines Fußballspiels sinnvoller als bei einem Schwimmwettkampf).
- Kundenbindung: Die aus Videos gewonnenen Erkenntnisse können genutzt werden, um die Kundenbindung zu verbessern, indem für die Benutzer relevante Stellen in einem Video präsentiert werden. Angenommen, in einem Lehrvideo mit mathematischen Themen wird in den ersten 30 Minuten über Kugeln und in den nächsten 30 Minuten über Pyramiden gesprochen. Ein Schüler/Student, der Informationen zu Pyramiden sucht, würde mehr profitieren, wenn das Video an der 30-Minuten-Markierung positioniert wird.

## <a name="next-steps"></a>Nächste Schritte

Sind Sie bereit für die ersten Schritte mit Video Indexer? Weitere Informationen finden Sie in den folgenden Artikeln:

- [Tutorial: Registrieren und Hochladen Ihres ersten Videos](video-indexer-get-started.md)
- [Verarbeiten von Inhalten mit der Video Indexer-REST-API](video-indexer-use-apis.md)
- [Einbetten von visuellen Widgets in Ihre Anwendung](video-indexer-embed-widgets.md)
