---
title: Regionale Verfügbarkeit von Azure Media Services | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die regionale Verfügbarkeit von Microsoft Azure Media Services-Features und -Diensten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: cf84e45ff43643c7e9a983b867194a7920aa1b57
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351843"
---
# <a name="media-services-regional-availability"></a>Regionale Verfügbarkeit von Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. Sehen Sie sich die neuste Version – [Media Services v3](../latest/media-services-overview.md) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Mit Microsoft Azure Media Services (AMS) können Sie Video- oder Audioinhalte auf sichere Weise hochladen, speichern, codieren, verpacken und bedarfsgesteuert oder als Livestream auf verschiedenen Clients (wie Fernsehern, PCs und mobilen Geräten) bereitstellen.

AMS steht in mehreren Regionen auf der ganzen Welt bereit und bietet Ihnen damit Flexibilität bei der Auswahl des Standorts für die Erstellung Ihrer Anwendungen. Dieser Artikel bietet eine Übersicht über die regionale Verfügbarkeit von Microsoft Azure Media Services-Features und -Diensten.

Weitere Informationen zur gesamten globalen Azure-Infrastruktur finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="ams-accounts"></a>AMS-Konten

Mithilfe der Informationen unter [Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) können Sie bestimmen, ob Media Services in einer bestimmten Region verfügbar ist.

## <a name="streaming-endpoints"></a>Streamingendpunkte

Media Services-Kunden haben die Wahl zwischen einem **Standard** -Streamingendpunkt und einem **Premium** -Streamingendpunkt.

|Name|Status|Region
|---|---|---|
|Standard|Allgemein verfügbar|All|
|Premium|Allgemein verfügbar|All|

## <a name="live-encoding"></a>Live Encoding

In allen Regionen verfügbar außer: „Deutschland“, „Brasilien, Süden“, „Indien, Westen“, „Indien, Süden“ und „Indien, Mitte“.

## <a name="encoding-media-processors"></a>Medienprozessoren für die Codierung

AMS bietet zwei On-Demand-Encoder: **Media Encoder Standard** und **Media Encoder Premium Workflow**. Weitere Informationen finden Sie unter [Azure On-Demand Media Encoder – Überblick und Vergleich](media-services-encode-asset.md).

|Medienprozessorname|Status|Regions
|---|---|---|
|Media Encoder Standard|Allgemein verfügbar|All|
|Media Encoder Premium Workflow|Allgemein verfügbar|Alle mit Ausnahme von China|

## <a name="analytics-media-processors"></a>Analytics-Medienprozessoren

Media Analytics ist eine Sammlung aus Sprach- und Bildanalysekomponenten, mit denen Organisationen und Unternehmen anhand von Videodateien Erkenntnisse gewinnen, aus denen sich umsetzbare Maßnahmen ableiten lassen. Weitere Informationen finden Sie unter [Azure Media Services Analytics – Übersicht](./legacy-components.md).

> [!NOTE]
> Einige Analytics-Medienprozessoren werden außer Betrieb genommen. Die entsprechenden Termine finden Sie im Thema [Legacykomponenten](legacy-components.md).

|Medienprozessorname|Status|Region
|---|---|---|
|Azure Media Face Detector|Vorschau|All|
|Azure Media Indexer|Allgemein verfügbar|All|
|Azure Media Motion Detector|Vorschau|All|
|Azure Media OCR|Vorschau|All|
|Azure Media Redactor|Allgemein verfügbar|All|
|Azure Media Video Thumbnails|Vorschau|All|

## <a name="protection"></a>Schutz

Microsoft Azure Media Services ermöglicht die Sicherung Ihrer Medien ab dem Zeitpunkt, an dem sie Ihren Computer durch Speicherung, Verarbeitung und Übermittlung verlassen. Weitere Informationen finden Sie unter [Schützen von Inhalten – Übersicht](media-services-content-protection-overview.md).

|Verschlüsselung|Status|Regions|
|---|---|---| 
|Storage|Allgemein verfügbar|All|
|AES-128-Schlüssel|Allgemein verfügbar|All|
|FairPlay|Allgemein verfügbar|All|
|PlayReady|Allgemein verfügbar|All|
|Widevine|Allgemein verfügbar|Alle außer Deutschland, US-Regierung und China.

> [!NOTE]
> Widevine ist ein von Google Inc. bereitgestellter Dienst, der den Vertragsbedingungen und der Datenschutzrichtlinie von Google, Inc. unterliegt.

## <a name="reserved-units-rus"></a>Reservierte Einheiten (RUs)

Die Anzahl der bereitgestellten reservierten Einheiten bestimmt die Anzahl der Medienaufgaben, die gleichzeitig unter einem angegebenen Konto verarbeitet werden können.

In allen Regionen verfügbar.

## <a name="reserved-unit-ru-type"></a>Art der reservierten Einheit (RU)

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Medienverarbeitungsaufgaben erfolgen. Sie können zwischen den folgenden Typen von reservierten Einheiten auswählen: S1, S2 oder S3.

|Name des RU-Typs|Status|Regions
|---|---|---|
|S1|Allgemein verfügbar|All|
|S2|Allgemein verfügbar|Alle außer „Brasilien, Süden“ und „Indien, Westen“|
|S3|Allgemein verfügbar|Alle außer „Indien, Westen“|

## <a name="next-steps"></a>Nächste Schritte

[Migrieren zu Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Feedback geben

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]