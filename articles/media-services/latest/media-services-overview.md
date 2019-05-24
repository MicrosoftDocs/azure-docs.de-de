---
title: 'Azure Media Services v3: Übersicht | Microsoft Docs'
description: Dieser Artikel bietet einen allgemeinen Überblick über Media Services und enthält Links zu Artikeln, die weitere Details enthalten.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, streamen, übertragen, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/13/2019
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: b07675e25c0380921e24059ff0107fcfe1bb3873
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602094"
---
# <a name="azure-media-services-v3-overview"></a>Azure Media Services v3: Übersicht

Azure Media Services ist eine cloudbasierte Plattform für die Erstellung von Lösungen, die Videostreaming in Broadcastqualität ermöglichen, Barrierefreiheit und Verteilung optimieren, Inhalte analysieren und vieles mehr. Ganz gleich, ob Sie ein Anwendungsentwickler sind oder in einem Callcenter, bei einer Regierungsbehörde oder in einem Unterhaltungsunternehmen arbeiten, Media Services unterstützt Sie bei der Erstellung von Anwendungen, die Medienerlebnisse von herausragender Qualität für ein breites Publikum auf den beliebtesten mobilen Geräten und Browsern von heute bieten. 

> [!NOTE]
> Derzeit können Sie das Azure-Portal nicht für die Verwaltung von v3-Ressourcen verwenden. Verwenden Sie die [REST-API](https://aka.ms/ams-v3-rest-ref), die [Befehlszeilenschnittstelle](https://aka.ms/ams-v3-cli-ref) oder eines der unterstützten [SDKs](media-services-apis-overview.md#sdks).

## <a name="what-can-i-do-with-media-services"></a>Wie kann ich Media Services nutzen?

Media Services ermöglicht es Ihnen, eine Vielzahl von Medienworkflows in der Cloud zu erstellen. Im Folgenden finden Sie einige Beispiele dafür, was mit Media Services erreicht werden kann.  

* Übermitteln Sie Videos in verschiedenen Formaten, damit sie auf einer Vielzahl von Browsern und Geräten wiedergegeben werden können. Für die bedarfsgesteuerte und Livestreamingbereitstellung für verschiedene Clients (mobile Geräte, TV, PC usw.) müssen die Video- und Audioinhalte ordnungsgemäß codiert und verpackt werden. Informationen zum Bereitstellen und Streamen solcher Inhalte finden Sie unter [Schnellstart: Codieren und Streamen von Dateien](stream-files-dotnet-quickstart.md).
* Streamen Sie Livesportereignisse an ein großes Onlinepublikum, z.B. Fußball, Baseball, Uni- und Schulsport und vieles mehr. 
* Übertragen Sie öffentliche Versammlungen und Veranstaltungen, z.B. in Rathäusern, bei Stadtratssitzungen oder von Gesetzgebungsorganen.
* Analysieren Sie aufgezeichnete Videos oder Audioinhalte. Um beispielsweise eine höhere Kundenzufriedenheit zu erreichen, können Unternehmen Sprache-in-Text extrahieren und Suchindizes und Dashboards erstellen. Sie können dann Daten zu häufigen Beschwerden und ihren Ursachen sowie andere relevante Daten extrahieren.
* Erstellen Sie einen Abonnementvideodienst, und streamen Sie durch DRM geschützte Inhalte, wenn ein Kunde (z.B. ein Filmstudio) den Zugriff und die Nutzung von urheberrechtlich geschützten Werken einschränken muss.
* Stellen Sie Offlineinhalte für die Wiedergabe in Flugzeugen, Zügen und Autos bereit. Möglicherweise muss ein Kunde Inhalte zur Wiedergabe auf sein Smartphone oder Tablet herunterladen, wenn er eine Trennung vom Netzwerk erwartet.
* Implementieren Sie eine E-Learning-Videoplattform mit Azure Media Services und [Azure Cognitive Services-APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai) für Sprache-in-Text-Untertitel, Übersetzung in mehrere Sprachen usw. 
* Verwenden Sie Azure Media Services zusammen mit [Azure Cognitive Services-APIs](https://docs.microsoft.com/azure/#pivot=products&panel=ai), um Untertitel zu Videos hinzuzufügen und so ein breiteres Publikum anzusprechen (z.B. Menschen mit Hörbehinderungen oder Menschen, die in einer anderen Sprache mitlesen möchten).
* Aktivieren Sie Azure CDN, um eine umfassende Skalierung zu erreichen und hohe Auslastungen besser zu bewältigen (z.B. den Beginn einer Produkteinführung). 

## <a name="how-can-i-get-started-with-v3"></a>Wie kann ich erste Schritte mit v3 ausführen? 

Hier erfahren Sie, wie Sie mit Media Services Videos v3 Inhalte codieren und packen sowie Videos on Demand streamen, live übertragen und analysieren. Anhand von Tutorials, API-Referenzen und anderem Dokumentationsmaterial wird gezeigt, wie Sie Video- oder Audiostreams (bedarfsgesteuert oder live) sicher und mit Skalierung für Millionen von Benutzern übermitteln.

> [!TIP]
> Machen Sie sich zunächst mit Folgendem vertraut, bevor Sie mit der Entwicklung beginnen:<br/>* [Grundlegende Konzepte](concepts-overview.md) (beinhaltet wichtige Konzepte wie Verpacken, Codierung, Schutz usw.)<br/>* [Entwickeln mit Media Services v3-APIs](media-services-apis-overview.md) (Informationen zum Zugreifen auf APIs, Namenskonventionen usw.)

### <a name="quickstarts"></a>Schnellstarts  

Die Schnellstartanleitungen enthalten grundlegende Informationen für Einsteiger, mit denen neue Kunden Media Services schnell ausprobieren können.

* [Streamen von Videodateien: .NET](stream-files-dotnet-quickstart.md)
* [Streamen von Videodateien: CLI](stream-files-cli-quickstart.md)
* [Streamen von Videodateien: Node.js](stream-files-nodejs-quickstart.md)
    
### <a name="tutorials"></a>Lernprogramme 

Die Tutorials enthalten szenariobasierte Verfahren für einige der wichtigsten Media Services-Aufgaben.

* [Codieren von Remotedateien und Streamen von Videos: REST](stream-files-tutorial-with-rest.md)
* [Codieren von hochgeladenen Dateien und Streamen von Videos: .NET](stream-files-tutorial-with-api.md)
* [Livestreaming: .NET](stream-live-tutorial-with-api.md)
* [Analysieren Ihres Videos: .NET](analyze-videos-tutorial-with-api.md)
* [Dynamische AES-128-Verschlüsselung: .NET](protect-with-aes128.md)
    
### <a name="how-to-guides"></a>Anleitungen

Die Artikel enthalten Codebeispiele, um die Ausführung einer Aufgabe zu veranschaulichen. In diesem Abschnitt finden Sie zahlreiche Beispiele. Hier nur eine kleine Auswahl:

* [Erstellen eines Azure Media Services-Kontos](create-account-cli-how-to.md)
* [Zugriff auf Azure Media Services-API mit Azure CLI](access-api-cli-how-to.md)
* [Codieren mit HTTPS als Auftragseingabe: .NET](job-input-from-http-how-to.md)  
* [Überwachen von Ereignissen: Portal](monitor-events-portal-how-to.md)
* [Dynamische Verschlüsselung mit Multi-DRM: .NET](protect-with-drm.md) 
* [Codieren mit einer benutzerdefinierten Transformation: CLI](custom-preset-cli-howto.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Fragen stellen, Feedback geben, Updates abrufen

Im Artikel [Azure Media Services-Community](media-services-community.md) finden Sie verschiedene Möglichkeiten, Fragen zu stellen, Feedback zu geben und Updates zu Media Services zu bekommen.

## <a name="next-steps"></a>Nächste Schritte

[Kenntnisse grundlegender Konzepte](concepts-overview.md)

