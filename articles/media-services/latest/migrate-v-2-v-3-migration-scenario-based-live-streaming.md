---
title: Leitfaden zur Migration von Azure Media Services-Livestreaming
description: In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für das Livestreaming, der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 327673d0765fa807f782494562c995278be4782f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955291"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Szenariobasierter Migrationsleitfaden für das Livestreaming

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-4.svg)

Der Azure-Portal unterstützt jetzt die Einrichtung und Verwaltung von Liveereignissen.  Es wird empfohlen, dies während des Tests der Migration von V2 zu V3 auszuprobieren.

## <a name="test-the-v3-live-event-workflow"></a>Testen des V3-Workflows für Liveereignisse

> [!NOTE]
> Mit V2 erstellte Kanäle und Programme (die in V3 Liveereignissen und Liveausgaben zugeordnet sind) können nicht mehr mit der V3-API verwaltet werden. Es wird empfohlen, zu einem geeigneten Zeitpunkt, zu dem Sie den vorhandenen V2-Kanal beenden können, auf V3-Liveereignisse und Liveausgaben umzusteigen. Zurzeit gibt es keine Unterstützung für die nahtlose Migration eines kontinuierlichen 24/7-Livekanals zu den neuen V3-Liveereignissen. Daher müssen Sie eine wartungsbedingte Downtime einplanen, die für Ihre Zielgruppe und Ihr Unternehmen optimal geeignet ist.

Testen Sie die neue Übertragungsmethode von Liveereignissen mit Media Services, bevor Sie Ihre Inhalte von V2 nach V3 verschieben. Im Folgenden finden Sie die V3-Funktionen, mit denen Sie arbeiten und die Sie für die Migration in Erwägung ziehen sollten.

- Erstellen Sie ein neues V3-[Liveereignis](live-events-outputs-concept.md#live-events) für die Codierung. Sie können [die Codierungsvoreinstellungen 1080p und 720p](live-event-types-comparison.md#system-presets) aktivieren.
- Verwenden Sie anstelle von Programmen die Entität [Liveausgabe](live-events-outputs-concept.md#live-outputs).
- Erstellen Sie [Streaminglocators](streaming-locators-concept.md).
- Bewerten Sie Ihre Anforderungen an das [HLS- und DASH-Livestreaming](dynamic-packaging-overview.md).
- Wenn Ihre Liveereignisse schnell starten sollen, machen Sie sich mit den neuen Funktionen für den [Standbymodus](live-events-outputs-concept.md#standby-mode) vertraut.
- Wenn Sie bereits während Ihres Liveereignisses eine Transkription davon erstellen möchten, sehen Sie sich die neue Funktion der [Livetranskription](live-transcription.md) an.
- Erstellen Sie 24/7/365-Liveereignisse in V3, wenn Sie eine längere Streamingdauer benötigen.
- Verwenden Sie [Event Grid](monitor-events-portal-how-to.md), um Ihre Liveereignisse zu überwachen.

Die zugehörigen Schritte finden Sie in den nachstehenden Konzepten, Tutorials und Schrittanleitungen für Liveereignisse.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Konzepte, Tutorials und Schrittanleitungen für Liveereignisse

### <a name="concepts"></a>Konzepte

- [Livestreaming mit Azure Media Services v3](live-streaming-overview.md)
- [Liveereignisse und Liveausgaben in Media Services](live-events-outputs-concept.md)
- [Überprüfte lokale Livestreamingencoder](recommended-on-premises-live-encoders.md)
- [Verwenden von Timeshift und Liveausgaben zum Erstellen der bedarfsgesteuerten Videowiedergabe](live-event-cloud-dvr.md)
- [Livetranskription (Vorschau)](live-transcription.md)
- [Vergleich von Liveereignistypen](live-event-types-comparison.md)
- [Status und Abrechnung von Liveereignissen](live-event-states-billing.md)
- [Einstellungen für geringe Wartezeit bei Liveereignissen](live-event-latency.md)
- [Azure Media Services-Fehlercodes für Liveereignisse](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Tutorials und Schnellstarts

- [Tutorial: Livestreaming mit Media Services](stream-live-tutorial-with-api.md)
- [Erstellen eines Azure Media Services-Livestreams mit OBS](live-events-obs-quickstart.md)
- [Schnellstart: Hochladen, Codieren und Streamen von Inhalten mithilfe des Portals](manage-assets-quickstart.md)
- [Schnellstart: Erstellen eines Azure Media Services-Livestreams mit Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Beispiele

Sehen Sie sich auch den [Vergleich von V2- und V3-Code in den Codebeispielen](migrate-v-2-v-3-migration-samples.md) an.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
