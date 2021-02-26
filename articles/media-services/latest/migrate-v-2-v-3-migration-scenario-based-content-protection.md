---
title: Migrationsleitfaden für den Inhaltsschutz
description: In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für den Inhaltsschutz, der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940118"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Szenariobasierter Migrationsleitfaden für den Inhaltsschutz

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-4.svg)

In diesem Artikel finden Sie einen szenariobasierten Migrationsleitfaden für den Inhaltsschutz, der Sie bei der Migration von Azure Media Services V2 zu V3 unterstützt.

## <a name="protect-content-in-v3-api"></a>Inhaltsschutz in der V3-API

Verwenden Sie die Unterstützung für Funktionen mit [mehreren Schlüsseln](design-multi-drm-system-with-access-control.md) in der neuen V3-API.

Die zugehörigen Schritte finden Sie in den nachstehenden Konzepten, Tutorials und Schrittanleitungen zum Inhaltsschutz.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Konzepte, Tutorials und Schrittanleitungen zum Inhaltsschutz

### <a name="concepts"></a>Konzepte

- [Schützen Sie Ihren Inhalt mit der dynamischen Verschlüsselung von Media Services](content-protection-overview.md)
- [Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md)
- [Media Services v3 mit PlayReady-Lizenzvorlage](playready-license-template-overview.md)
- [Übersicht zu Media Services v3 mit Widevine-Lizenzvorlage](widevine-license-template-overview.md)
- [Anforderungen und Konfiguration der Apple FairPlay-Lizenz](fairplay-license-overview.md)
- [Streamingrichtlinien](streaming-policy-concept.md)
- [Richtlinien für Inhaltsschlüssel](content-key-policy-concept.md)

### <a name="tutorials"></a>Lernprogramme

[Schnellstart: Verschlüsseln von Inhalten mithilfe des Portals](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Anleitungen

- [Abrufen eines Signaturschlüssels aus der vorhandenen Richtlinie](get-content-key-policy-dotnet-howto.md)
- [FairPlay-Offlinestreaming für iOS mit Media Services v3](offline-fairplay-for-ios.md)
- [Widevine-Offlinestreaming für Android mit Media Services v3](offline-widevine-for-android.md)
- [Offlinestreaming mit PlayReady für Windows 10 mit Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Beispiele

Sehen Sie sich auch den [Vergleich von V2- und V3-Code in den Codebeispielen](migrate-v-2-v-3-migration-samples.md) an.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]