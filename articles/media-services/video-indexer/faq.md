---
title: 'Häufig gestellte Fragen zu Video Indexer: Azure'
titlesuffix: Azure Media Services
description: Antworten auf häufig gestellte Fragen zu Video Indexer
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: f20d718d0b1d3bbdf117e502a380897c79a7905f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799501"
---
# <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

In diesem Artikel finden Sie Antworten auf häufig gestellte Fragen zu Video Indexer.

## <a name="general-questions"></a>Allgemeine Fragen

### <a name="what-is-video-indexer"></a>Was ist Video Indexer?

Video Indexer ist ein KI-Dienst, der Teil von Microsoft Azure Media Services ist. Video Indexer bietet eine Orchestrierung von mehreren Machine Learning-Modellen, mit denen Sie problemlos detaillierte Informationen aus einer Videodatei extrahieren können. Video Indexer nutzt mehrere Kanäle einer Videodatei, um erweiterte und genaue Einblicke zu bieten: Audio, Sprache und visuelle Elemente. Die Erkenntnisse von Video Indexer können in vielerlei Hinsicht hilfreich sein, etwa für ein leichteres Auffinden und Nutzen von Inhalten, das Aufdecken neuer Vermarktungsmöglichkeiten oder das Erstellen neuer Benutzererlebnisse. Video Indexer bietet eine webbasierte Oberfläche für Tests, Konfiguration und die Anpassung von Modellen in Ihrem Konto. Entwickler können Video Indexer mithilfe einer REST-basierten API in ein Produktionssystem integrieren. 

### <a name="what-can-i-do-with-video-indexer"></a>Welche Möglichkeiten bietet Video Indexer?

Einige der Vorgänge, die Video Indexer mit Mediendateien ausführen kann:

* Identifizieren und Extrahieren von Sprache und Identifizieren von Sprechern
* Identifizieren und Extrahieren von Text auf dem Bildschirm in Videos
* Erkennen von Objekten in Videodateien
* Identifizieren von Marken (z.B. Microsoft) in Audiospuren und Text auf dem Bildschirm in Videos
* Erfassen und Erkennen von Gesichtern aus einer Prominentendatenbank und einer benutzerdefinierten Datenbank mit Gesichtern
* Extrahieren von Themen, die in Audio- und Videoinhalten behandelt, aber nicht unbedingt erwähnt werden
* Erstellen von Untertiteln aus Audiospuren

Weitere Informationen und weitere Video Indexer-Funktionen finden Sie in der [Übersicht](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Wie mache ich mich mit der Nutzung von Video Indexer vertraut?

Mit der kostenlosen Testversion von Video Indexer stehen Ihnen 600 Minuten in der webbasierten Oberfläche und 2.400 Minuten über die API zur Verfügung. [Melden Sie sich bei der webbasierten Oberfläche von Video Indexer an](https://www.videoindexer.ai/), und testen Sie den Dienst selbst. Dazu können Sie eine beliebige Webidentität verwenden und müssen kein Azure-Abonnement einrichten. 

Wenn Sie Video- und Audiodateien in großem Umfang indizieren möchten, können Sie Video Indexer mit einem kostenpflichtigen Microsoft Azure-Abonnement verknüpfen. Weitere Preisinformationen finden Sie auf der Seite mit der [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

Weitere Informationen zu den ersten Schritten finden Sie unter [Erste Schritte](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Benötige ich Programmierkenntnisse, um Video Indexer zu verwenden?

Über die webbasierte Oberfläche von Video Indexer können Sie Ihr Konto **ohne Codierung**  auswerten, konfigurieren und verwalten.  Wenn Sie komplexere Anwendungen entwickeln möchten, können Sie Video Indexer mithilfe der [Video Indexer-API](https://api-portal.videoindexer.ai/) in Ihre eigene Anwendungen, Websites oder [benutzerdefinierten Workflows mit serverlosen Technologien wie Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) sowie Azure Functions integrieren.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Benötige ich Kenntnisse im maschinellen Lernen, um Video Indexer zu verwenden?

Nein, Video Indexer ermöglicht die Integration mehrerer Machine Learning-Modelle in eine Pipeline. Bei der Indizierung einer Video- oder Audiodatei über Video Indexer wird ein vollständiger Satz von Erkenntnissen abgerufen und in eine freigegebene Zeitachse extrahiert. Dazu sind aufseiten des Kunden keine Kenntnisse im maschinellen Lernen oder von Algorithmen erforderlich.

### <a name="what-media-formats-does-video-indexer-support"></a>Welche Medienformate werden von Video Indexer unterstützt?

Video Indexer unterstützt die gängigsten Medienformate. Einzelheiten hierzu finden Sie in der Liste der [Standardformate von Azure Media Encoder](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats).

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Wie lade ich eine Mediendatei in Video Indexer hoch?

Im webbasierten Video Indexer-Portal können Sie über das Dialogfeld zum Hochladen von Dateien eine Mediendatei hochladen. Alternativ dazu können Sie auch auf eine URL zeigen, welche die Quelldatei direkt hostet (siehe [Beispiel](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). URLs, die Medieninhalte mithilfe eines iFrames oder Einbindungscodes hosten, funktionieren nicht (siehe [Beispiel](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). Über die Video Indexer-API müssen Sie nun die Eingabedatei über eine URL oder ein Bytearray angeben. Uploads über eine URL mithilfe der API sind auf 10 GB beschränkt, haben jedoch kein Zeitlimit. Weitere Informationen finden Sie in dieser [ausführlichen Anleitung](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Wie lange dauert das Extrahieren von Informationen aus Mediendateien mit Video Indexer?

Die Zeit, die Video Indexer für das Indizieren einer Video- oder Audiodatei benötigt – entweder mithilfe der Video Indexer-API oder über die webbasierte Video Indexer-Oberfläche – hängt von mehreren Parametern ab. Dazu zählen die Länge und Qualität der Datei, die Anzahl der in der Datei identifizierten Informationen, die Anzahl der [reservierten Einheiten](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) und die Aktivierung oder Deaktivierung des [Streamingendpunkts](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview). Es wird ein Testlauf mit mehreren Dateien Ihrer eigenen Inhalte empfohlen, um die durchschnittliche Dauer einschätzen zu können.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Kann ich mit Video Indexer angepasste Workflows zur Automatisierung von Prozessen erstellen?

Ja, Video Indexer lässt sich in serverlose Technologie wie Logic Apps, Flow und [Azure Functions](https://azure.microsoft.com/services/functions/) integrieren. Weitere Informationen zu den [Logic Apps](https://azure.microsoft.com/services/logic-apps/)- und [Flow](https://flow.microsoft.com/en-us/)-Connectors für Video Indexer finden Sie [hier](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>In welchen Azure-Regionen steht Video Indexer zur Verfügung?

Eine Übersicht über die Azure-Regionen, in denen Video Indexer verfügbar ist, finden Sie auf der Seite [Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

### <a name="what-is-the-sla-for-video-indexer"></a>Welche Vereinbarung zum Servicelevel (SLA) gilt für Video Indexer?

Die SLA von Azure Media Services gelten für Video Indexer. Sie finden sie auf der [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/)-Seite. Die SLA gilt nur für kostenpflichtige Video Indexer-Konten, nicht für die kostenlose Testversion.

## <a name="privacy-questions"></a>Fragen zum Datenschutz

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Werden die von Video Indexer indizierten Video- und Audiodateien gespeichert?

Ja, sofern Sie die Dateien nicht mithilfe der Video Indexer-Website oder -API aus Video Indexer löschen, werden Ihre Video- und Audiodateien gespeichert. Bei der kostenlosen Testversion werden die Video- und Audiodateien, die Sie indizieren, in der Azure-Region „USA, Osten“ gespeichert. Andernfalls werden Ihre Video- und Audiodateien im Speicherkonto für Ihr Azure-Abonnement gespeichert.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Kann ich meine im Video Indexer-Portal gespeicherten Dateien löschen?

Ja, Sie können Ihre Video- und Audiodateien sowie Metadaten und Erkenntnisse, die von Video Indexer daraus extrahiert wurden, jederzeit löschen. Nach dem Löschen einer Datei aus Video Indexer sind die Datei sowie die zugehörigen Metadaten und Erkenntnisse endgültig aus Video Indexer entfernt. Wenn Sie jedoch eine eigene Sicherungslösung im Azure-Speicher implementiert haben, verbleibt die Datei in Ihrem Azure-Speicher.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Kann ich den Benutzerzugriff auf mein Video Indexer-Konto steuern?

Ja, nur Kontoadministratoren können Personen für ihre jeweiligen Konten ein- und ausladen sowie Bearbeitungsrechte und schreibgeschützten Zugriff zuweisen.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Wer hat Zugriff auf meine Video- und Audiodateien, die von Video Indexer indiziert und/oder gespeichert wurden, sowie auf die extrahierten Metadaten und Erkenntnisse?

Auf Ihre Video- oder Audioinhalte mit der Datenschutzeinstellung „Öffentlich“ kann jeder zugreifen, der über den Link zu Ihren Video- oder Audioinhalten und den zugehörigen Erkenntnissen verfügt. Auf Video- oder Audioinhalte mit der Datenschutzeinstellung „Privat“ können nur Benutzer zugreifen, die dazu eingeladen wurden. Die Datenschutzeinstellung Ihrer Inhalte gilt auch für die von Video Indexer extrahierten Metadaten und Erkenntnisse. Die Datenschutzeinstellung wird beim Hochladen Ihrer Video- oder Audiodatei festgelegt. Sie können die Datenschutzeinstellung auch nach der Indizierung ändern.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Welchen Zugriff hat Microsoft auf meine Video- und Audiodateien, die von Video Indexer indiziert und/oder gespeichert wurden, sowie auf die extrahierten Metadaten und Erkenntnisse?

Den [Azure Online Services-Nutzungsbedingungen](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) entsprechend sind Sie Eigentümer Ihrer Inhalte, und Microsoft greift nur gemäß den OST und der Microsoft-Datenschutzerklärung auf Ihre Inhalte sowie auf die von Video Indexer daraus extrahierten Metadaten und Erkenntnisse zu.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Stehen die benutzerdefinierten Modelle, die ich in meinem Video Indexer-Konto erstelle, für andere Konten zur Verfügung?

 Nein, die benutzerdefinierten Modelle, die Sie in Ihrem Konto erstellen, stehen keinem anderen Konto zur Verfügung. Video Indexer ermöglicht derzeit die Erstellung benutzerdefinierter [Marken-](customize-brands-model-overview.md), [Sprach-](customize-language-model-overview.md) und [Personen](customize-person-model-overview.md)modelle in Ihrem Konto. Diese Modelle stehen nur in dem Konto zur Verfügung, in dem Sie sie erstellt haben.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Bleiben die von Video Indexer indizierten Inhalte in der Azure-Region, in der ich Video Indexer verwende?

Ja, der Inhalt und die gewonnenen Informationen befinden sich weiterhin in der entsprechenden Azure-Region. Eine Ausnahme stellt die manuelle Konfiguration, die mehrere Azure-Regionen verwendet, im Azure-Abonnement dar. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Wie lautet die Datenschutzrichtlinie für Video Indexer?

Video Indexer ist Teil der [Datenschutzbestimmungen von Microsoft](https://privacy.microsoft.com/privacystatement). Die Datenschutzerklärung erläutert die von Microsoft verarbeiteten personenbezogenen Daten sowie die Art und die Zwecke der Verarbeitung dieser Daten durch Microsoft. Weitere Informationen zum Datenschutz finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Über welche Zertifizierungen verfügt Video Indexer?

Video Indexer hat derzeit die SOC-Zertifizierung. Die Zertifizierung von Video Indexer finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Fragen zu APIs

### <a name="what-apis-does-video-indexer-offer"></a>Welche APIs stellt Video Indexer bereit?

Die Video Indexer-API ermöglicht Indizierung, das Extrahieren von Metadaten, Anlagenverwaltung, Übersetzung, Einbettung, Anpassung von Modelle und vieles mehr. Ausführlichere Informationen zur Verwendung der Video Indexer-API finden Sie im [Video Indexer-Entwicklerportal](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Welche Client-SDKs stellt Video Indexer bereit?

Derzeit werden keine Client-SDKs angeboten. Das Video Indexer-Team arbeitet an den SDKs, und ihre baldige Bereitstellung ist geplant.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Wie kann ich mich mit der Video Indexer-API vertraut machen?

Befolgen Sie das [Tutorial: Erste Schritte mit der Video Indexer-API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Was ist der Unterschied zwischen der Video Indexer-API und der Azure Media Services v3-API?

Derzeit bestehen einige Überlappungen bei den von der Video Indexer-API und der Azure Media Services v3-API gebotenen Features. Weitere Informationen zum Vergleichen der beiden Dienste finden Sie [hier](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Was ist ein API-Zugriffstoken und warum benötige ich es?

Die Video Indexer-API enthält eine Autorisierungs-API und eine Operations-API. Die Autorisierungs-API enthält Aufrufe, über die Sie Zugriffstoken erhalten. Jeder Aufruf der Operations-API sollte einem Zugriffstoken zugeordnet sein und mit dem Autorisierungsbereich des Aufrufs übereinstimmen.

Zugriffstoken sind bei der Verwendung von Video Indexer-APIs aus Sicherheitsgründen erforderlich. Dadurch wird sichergestellt, dass alle Aufrufe von Ihnen oder zugriffsberechtigten Personen auf Ihr Konto stammen. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Was ist der Unterschied zwischen Zugriffstoken für Konten, Benutzer und Videos?

* Kontoebene: Mit Zugriffstoken auf Kontoebene können Sie Vorgänge auf der Kontoebene oder der Videoebene durchführen. Beispiele dafür sind das Hochladen von Videos, Auflisten aller Videos, Abrufen von Videoinformationen usw.
* Benutzerebene: Mit Zugriffstoken auf Benutzerebene können Sie Vorgänge auf der Benutzerebene durchführen. Ein Beispiel hierfür ist das Abrufen von zugeordneten Konten.
* Videoebene: Mit Zugriffstoken auf Videoebene können Sie Vorgänge für ein bestimmtes Video durchführen. Beispiele hierfür sind das Abrufen von Videoinformationen, Herunterladen von Untertiteln, Abrufen von Widgets usw.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Wie oft muss das Zugriffstoken erneuert werden? Nach welcher Zeit laufen Zugriffstoken ab?

Zugriffstoken laufen nach einer Stunde ab. Daher müssen Sie einmal pro Stunde ein neues Zugriffstoken generieren. 

## <a name="billing-questions"></a>Fragen zur Abrechnung

### <a name="how-much-does-video-indexer-cost"></a>Wie viel kostet Video Indexer?

Für Video Indexer gilt ein einfaches nutzungsbasiertes Bezahlungsmodell, das auf der Dauer des eingegebenen Inhalts beruht, den Sie indizieren. Zusätzliche Gebühren können für Codierung, Streaming, Speicher, Netzwerknutzung und reservierte Einheiten für Medien anfallen. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/).

### <a name="when-am-i-billed-for-using-video-indexer"></a>Wann wird mir die Nutzung von Video Indexer in Rechnung gestellt?

Beim Senden eines Videos, das indiziert werden soll, definiert der Benutzer die gewünschte Indizierung als Videoanalyse, als Audioanalyse oder beides. Dadurch wird bestimmt, welche SKUs in Rechnung gestellt werden. Wenn während der Verarbeitung ein schwerwiegender Fehler auftritt, wird als Antwort darauf ein Fehlercode zurückgegeben. In solch einem Fall erfolgt keine Abrechnung.  Ein schwerwiegender Fehler kann durch einen Fehler in unserem Code oder durch einen kritischen Fehler in einer internen Abhängigkeit des Diensts verursacht werden. Fehler wie falsche Identifizierung oder Erkenntnisextraktion gelten nicht als schwerwiegend, und es wird eine Antwort zurückgegeben. In jedem Fall, in dem eine gültige Antwort (ohne Fehlercode) zurückgegeben wird, erfolgt eine Abrechnung.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Bietet Video Indexer eine kostenlose Testversion an?

Ja, Video Indexer stellt eine kostenlose Testversion bereit, die einen vollständigen Dienst sowie Zugriff auf alle API-Funktionen umfasst. Das Kontingent für Benutzer der webbasierten Oberfläche beträgt 600 Minuten, das für API-Benutzer beträgt 2.400 Minuten. 

## <a name="next-steps"></a>Nächste Schritte

[Übersicht](video-indexer-overview.md)
