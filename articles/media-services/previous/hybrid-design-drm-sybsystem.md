---
title: Hybriddesign von DRM-Subsystemen mit Azure Media Services | Microsoft-Dokumentation
description: In diesem Thema wird das Hybriddesign von DRM-Subsystemen mit Azure Media Services erläutert.
services: media-services
documentationcenter: ''
author: willzhan
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: willzhan;juliako
ms.openlocfilehash: 5c86a49cd9dc26f724de12ed2e5e77e645e4ab53
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466594"
---
# <a name="hybrid-design-of-drm-subsystems"></a>Hybriddesign des DRM-Subsystems 

In diesem Thema wird das Hybriddesign von DRM-Subsystemen mit Azure Media Services erläutert.

## <a name="overview"></a>Übersicht

Azure Media Services bietet Unterstützung für die folgenden drei DRM-Systeme:

* PlayReady
* Widevine (modular)
* FairPlay

Die DRM-Unterstützung umfasst die DRM-Verschlüsselung (dynamische Verschlüsselung) und die Lizenzbereitstellung, wobei Azure Media Player alle 3 DRMs als Browser-Player-SDK unterstützt.

Eine ausführliche Beschreibung von DRM/CENC-Subsystementwurf und -implementierung finden Sie in dem Dokument [CENC mit mehreren DRM-Systemen und Access Control: Referenzentwurf und -implementierung in Azure und Azure Media Services](media-services-cenc-with-multidrm-access-control.md).

Wir bieten zwar vollständige Unterstützung für drei DRM-Systeme, doch manchmal müssen Kunden zusätzlich zu den Azure Media Services verschiedene Teile der eigenen Infrastruktur/Subsysteme verwenden, um ein hybrides DRM-Subsystem zu erstellen.

Die folgenden Fragen werden häufig von Kunden gestellt:

* „Kann ich meine eigenen DRM-Lizenzserver verwenden?“ (In diesem Fall haben Kunden in eine DRM-Lizenzserverfarm mit eingebetteter Geschäftslogik investiert).
* „Kann ich nur die DRM-Lizenzbereitstellung in Azure Media Services verwenden, ohne Inhalt in AMS zu hosten?“

## <a name="modularity-of-the-ams-drm-platform"></a>Modularität der AMS-DRM-Plattform

Im Rahmen einer umfassenden Cloudvideoplattform sieht Azure Media Services-DRM ein Design mit Flexibilität und Modularität vor. Sie können Azure Media Services mit einer der folgenden, in der Tabelle unten beschriebenen verschiedenen Kombinationen verwenden (eine Erläuterung der in der Tabelle verwendete Schreibweise folgt). 

|**Hosten von Inhalt und Ursprung**|**Inhaltsverschlüsselung**|**DRM-Lizenzbereitstellung**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|Drittanbieter|
|AMS|Drittanbieter|AMS|
|AMS|Drittanbieter|Drittanbieter|
|Drittanbieter|Drittanbieter|AMS|

### <a name="content-hosting--origin"></a>Hosten von Inhalt und Ursprung

* AMS: Videomedienobjekt wird in AMS gehostet, und Streaming erfolgt über AMS-Streamingendpunkte (aber nicht notwendigerweise dynamische Paketerstellung).
* Drittanbieter: Video wird auf einer Drittanbieter-Streamingplattform außerhalb AMS gehostet und bereitgestellt.

### <a name="content-encryption"></a>Inhaltsverschlüsselung

* AMS: Inhaltsverschlüsselung wird dynamisch/bei Bedarf durch dynamische AMS-Verschlüsselung ausgeführt.
* Drittanbieter: Inhaltsverschlüsselung erfolgt außerhalb von AMS mithilfe eines Vorverarbeitungsworkflows.

### <a name="drm-license-delivery"></a>DRM-Lizenzbereitstellung

* AMS: DRM-Lizenz wird über den AMS-Lizenzbereitstellungsdienst bereitgestellt.
* Drittanbieter: DRM-Lizenz wird von einem Drittanbieter-DRM-Lizenzserver außerhalb AMS bereitgestellt.

## <a name="configure-based-on-your-hybrid-scenario"></a>Konfigurieren auf der Basis Ihres Hybridszenarios

### <a name="content-key"></a>Inhaltsschlüssel

Durch Konfiguration eines Inhaltsschlüssels können Sie die folgenden Attribute sowohl der dynamischen AMS-Verschlüsselung als auch des AMS-Lizenzbereitstellungsdiensts steuern:

* Der für die dynamische DRM-Verschlüsselung verwendete Inhaltsschlüssel.
* Von Lizenzbereitstellungsdiensten bereitzustellender DRM-Lizenzinhalt: Rechte, Inhaltsschlüssel und Einschränkungen.
* Typ der **Inhaltsschlüssel-Autorisierungsrichtlinien-Einschränkung**: offen, IP-Adresse oder Token-Einschränkung.
* Wenn Typ **token** der **Inhaltsschlüssel-Autorisierungsrichtlinien-Einschränkung** verwendet wird, muss die **Inhaltsschlüssel-Autorisierungsrichtlinien-Einschränkung** erfüllt sein, bevor eine Lizenz ausgestellt wird.

### <a name="asset-delivery-policy"></a>Übermittlungsrichtlinie für Medienobjekte

Durch Konfiguration einer Übermittlungsrichtlinie für Medienobjekte können Sie die folgenden vom dynamischen AMS-Objekt-Manager und der dynamischen Verschlüsselung eines AMS-Streamingendpunkts verwendeten Attribute steuern:

* Kombination aus Streamingprotokoll und DRM-Verschlüsselung, z.B. DASH unter CENC (PlayReady und Widevine), Smooth Streaming unter PlayReady, HLS unter Widevine oder PlayReady.
* Die standardmäßigen/eingebetteten Lizenzbereitstellungs-URLs für alle beteiligten DRMs.
* Gibt an, ob Lizenzerwerbs-URLs (LA_URLs) in DASH MPD- oder HLS-Wiedergabeliste eine Abfragezeichenfolge der Schlüssel-ID (KID) für Widevine und FairPlay enthalten.

## <a name="scenarios-and-samples"></a>Szenarien und Beispiele

Gemäß der Erläuterungen im vorherigen Abschnitt verwenden die folgenden fünf Hybridszenarien entsprechende Kombinationen der Konfiguration von **Inhaltsschlüssel**-**Übermittlungsrichtlinie für Medienobjekte**  (die in der letzten Spalte erwähnten Beispiele folgen auf die Tabelle):

|**Hosten von Inhalt und Ursprung**|**DRM-Verschlüsselung**|**DRM-Lizenzbereitstellung**|**Inhaltsschlüssel konfigurieren**|**Konfigurieren der Übermittlungsrichtlinie für Medienobjekte**|**Beispiel**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|Ja|Ja|Beispiel 1|
|AMS|AMS|Drittanbieter|Ja|Ja|Beispiel 2|
|AMS|Drittanbieter|AMS|Ja|Nein|Beispiel 3|
|AMS|Drittanbieter|Außerhalb|Nein|Nein|Beispiel 4|
|Drittanbieter|Drittanbieter|AMS|Ja|Nein|    

In den Beispielen funktioniert der PlayReady-Schutz für DASH und Smooth Streaming. Die Video-URLs unten sind Smooth Streaming-URLs. Um die entsprechenden DASH-URLs zu erhalten, fügen Sie nur „(format=mpd-time-csf)“ an. Sie können den [Azure Media-Testplayer](https://aka.ms/amtest) zum Testen in einem Browser verwenden. Damit können Sie konfigurieren, welches Streamingprotokoll unter welcher Technologie verwendet werden soll. IE11 und Microsoft Edge unter Windows 10 unterstützen PlayReady über EME. Weitere Informationen finden Sie unter [Azure Media Test Tool](https://blogs.msdn.microsoft.com/playready4/2016/02/28/azure-media-test-tool/).

### <a name="sample-1"></a>Beispiel 1

* Quell-URL (Basis): https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest 
* PlayReady-LA_URL (DASH und Smooth Streaming): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 
* Widevine-LA_URL (DASH): https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4 
* FairPlay-LA_URL (HLS): https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8 

### <a name="sample-2"></a>Beispiel 2

* Quell-URL (Basis): https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady-LA_URL (DASH und Smooth Streaming): http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx 

### <a name="sample-3"></a>Beispiel 3

* Quell-URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH und Smooth Streaming): https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/ 

### <a name="sample-4"></a>Beispiel 4

* Quell-URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady-LA_URL (DASH und Smooth Streaming): https://willzhan12.cloudapp.net/playready/rightsmanager.asmx 

## <a name="summary"></a>Zusammenfassung

Insgesamt sind Azure Media Services-DRM-Komponenten flexibel, und Sie können sie bei ordnungsgemäßer Konfiguration der Inhaltsschlüssel und Objektübermittlungsrichtlinie, wie in diesem Thema beschrieben, in einem Hybridszenario verwenden.

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Media Services-Lernpfade an.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

