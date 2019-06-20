---
title: Azure Media Services-Streamingendpunkte – Übersicht | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht über Azure Media Services-Streamingendpunkte.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a45e2af6f2cb9c105c084585a03a6de615fa1397
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64573045"
---
# <a name="streaming-endpoints-overview"></a>Streamingendpunkte – Übersicht  

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In Microsoft Azure Media Services (AMS) stellt ein **Streamingendpunkt** einen Streamingdienst dar, der Inhalte zur weiteren Verteilung direkt für eine Clientwiedergabeanwendung oder einen Content Delivery Network (CDN) bereitstellen kann. Media Services bietet auch eine nahtlose Integration von Azure CDN. Der ausgehende Stream des StreamingEndpoint-Diensts kann ein Livestream oder ein bei Bedarf abgerufenes Video oder ein progressiver Download Ihres Medienobjekts in Ihrem Media Services-Konto sein. Jedes Azure Media Services-Konto enthält einen Standard-StreamingEndpoint. Zusätzliche StreamingEndpoints können unter dem Konto erstellt werden. Es gibt zwei Versionen von StreamingEndpoint, 1.0 und 2.0. Ab dem 10. Januar 2017 gehört zu allen neu erstellten AMS-Konten **standardmäßig** StreamingEndpoint in Version 2.0. Zusätzliche Streamingendpunkte, die Sie diesem Konto hinzufügen, haben auch die Version 2.0. Diese Änderung hat keine Auswirkung auf vorhandene Konten. Vorhandenen Streamingendpunkte haben die Version 1.0 und können auf Version 2.0 aktualisiert werden. Durch diese Änderung ergeben sich Änderungen am Verhalten, an der Abrechnung und an Features (weitere Informationen finden Sie im nachstehenden Abschnitt **Streamingtypen und -versionen**).

Mit Azure Media Services wurden der Entität „Streamingendpunkt“ folgende Eigenschaften hinzugefügt: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. Eine ausführliche Übersicht über diese Eigenschaften finden Sie [hier](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

Beim Erstellen eines Azure Media Services-Kontos wird ein Standard-Streamingendpunkt im Zustand **Beendet** erstellt. Der Standard-Streamingendpunkt kann nicht gelöscht werden. Abhängig von der Verfügbarkeit von Azure CDN in der Zielregion bietet der neu erstellte Standard-Streamingendpunkt standardmäßig eine Integration mit dem CDN-Anbieter „StandardVerizon“. 
                
> [!NOTE]
> Vor dem Starten des Streamingendpunkts kann die Azure CDN-Integration deaktiviert werden. Der `hostname` und die Streaming-URL ändern sich nicht, und zwar unabhängig davon, ob Sie CDN aktivieren oder nicht.

Dieses Thema bietet einen Überblick über die wichtigsten Funktionen, die von Streamingendpunkten bereitgestellt werden.

## <a name="naming-conventions"></a>Benennungskonventionen

Für den Standardendpunkt: `{AccountName}.streaming.mediaservices.windows.net`

Für alle zusätzlichen Endpunkte: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Streamingtypen und -versionen

### <a name="standardpremium-types-version-20"></a>Standard-/Premium-Typen (Version 2.0)

Ab der Media Services-Version vom Januar 2017 gibt es zwei Streamingtypen: **Standard** (Vorschau) und **Premium**. Diese Typen sind Teil der Streamingendpunktversion „2.0“.


|Type|BESCHREIBUNG|
|--------|--------|  
|**Standard**|Der standardmäßige Streamingendpunkt ist ein **Standard**-Typ, kann aber durch Anpassen der Streamingeinheiten in den Typ „Premium“ geändert werden.|
|**Premium** |Diese Option eignet sich für professionelle Szenarien, die eine höhere Skalierung oder mehr Steuerung erfordern. Zum Typ **Premium** wechseln Sie, indem Sie Streamingeinheiten anpassen.<br/>Dedizierte Streamingendpunkte sind in einer isolierten Umgebung vorhanden und konkurrieren nicht um Ressourcen.|

Für Kunden, die Inhalte für große Zielgruppen im Internet bereitstellen möchten, empfehlen wir, ein CDN für den Streamingendpunkt zu aktivieren.

Ausführlichere Informationen finden Sie im nachfolgenden Abschnitt [Vergleich der Streamingtypen](#comparing-streaming-types).

### <a name="classic-type-version-10"></a>Klassischer Typ (Version 1.0)

Für Benutzer, die vor der Version vom 10. Januar 2017 AMS-Konten erstellt haben, verfügen Sie über den **klassischen** Typ von Streamingendpunkt. Dieser Typ ist Teil der Streamingendpunktversion „1.0“.

Wenn Ihr Streamingendpunkt der **Version 1.0** mindestens eine Premium-Streamingeinheit aufweist, handelt es sich um den Premium-Streamingendpunkt, der ohne zusätzliche Konfigurationsschritte alle AMS-Features bietet (genau wie der Typ **Standard/Premium**.

>[!NOTE]
>**Klassische** Streamingendpunkte (Version 1.0 ohne Streameinheit) bieten eingeschränkte Features und keine SLA. Es wird empfohlen, zum Typ **Standard** zu migrieren, um sich eine bessere Umgebung zu sichern und Features wie die dynamische Paketerstellung oder Verschlüsselung zu nutzen, die der Typ **Standard** bietet. Um zum Typ **Standard** zu migrieren, wechseln Sie zum [Azure-Portal](https://portal.azure.com/) und wählen **Standard abonnieren** aus. Weitere Informationen zur Migration finden Sie im Abschnitt [Migration](#migration-between-types).
>
>Beachten Sie, dass dieser Vorgang nicht rückgängig gemacht werden kann und Auswirkungen auf die Kosten hat.
>
 
## <a name="comparing-streaming-types"></a>Vergleichen von Streamingtypen

### <a name="versions"></a>Versionen

|Type|StreamingEndpointVersion|ScaleUnits|CDN|Abrechnung|
|--------------|----------|-----------------|-----------------|-----------------|
|Klassisch|1.0|0|Nicht verfügbar|Kostenlos|
|Standardstreamingendpunkt (Vorschau)|2.0|0|Ja|Kostenpflichtig|
|Premium-Streamingeinheiten|1.0|>0|Ja|Kostenpflichtig|
|Premium-Streamingeinheiten|2.0|>0|Ja|Kostenpflichtig|

### <a name="features"></a>Features

Feature|Standard|Premium
---|---|---
Erste 15 Tage kostenlos <sup>1</sup>| Ja |Nein
Throughput |Bis zu 600 MBit/s; kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.|200 Mbit/s pro Streamingeinheit. Kann einen deutlich höheren effektiven Durchsatz bereitstellen, wenn ein CDN verwendet wird.
CDN|Azure CDN, CDN eines Drittanbieters oder kein CDN.|Azure CDN, CDN eines Drittanbieters oder kein CDN.
Die Abrechnung erfolgt anteilsmäßig| Täglich|Täglich
Dynamische Verschlüsselung|Ja|Ja
Dynamische Paketerstellung|Ja|Ja
Skalieren|Automatische Skalierung bis zum Zieldurchsatz.|Zusätzliche Streamingeinheiten.
IP-Filterung/G20/benutzerdefinierter Host <sup>2</sup>|Ja|Ja
Progressiver Download|Ja|Ja
Empfohlene Verwendung |Für den Großteil der Streamingszenarien empfohlen.|Professionelle Nutzung. 

<sup>1</sup> Die kostenlose Testversion gilt nur für neu erstellte Media Services-Konten und den Standardstreamingendpunkt.<br/>
<sup>2</sup> Wird nur direkt für den Streamingendpunkt verwendet, wenn das CDN nicht für den Endpunkt aktiviert ist.<br/>

SLA-Informationen finden Sie unter [Preise und SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Migration zwischen Typen

From | Zu | Aktion
---|---|---
Klassisch|Standard|Muss abonnieren
Klassisch|Premium| Skalierung (Zusätzliche Streamingeinheiten)
Standard/Premium|Klassisch|Nicht verfügbar (bei Streamingendpunktversion 1.0. Es ist zulässig, zu „Klassisch“ zu wechseln, wenn die Einstellung „scaleunits“ auf „0“ festgelegt ist)
Standard (mit/ohne CDN)|Premium mit identischen Konfigurationen|Zulässig im Zustand **Gestartet**. (über Azure-Portal)
Premium (mit/ohne CDN)|Standard mit identischen Konfigurationen|Zulässig im Zustand **Gestartet** (über Azure-Portal)
Standard (mit/ohne CDN)|Premium mit anderen Konfigurationen|Zulässig im Zustand **Beendet** (über Azure-Portal) Im Zustand „Wird ausgeführt“ nicht zulässig.
Premium (mit/ohne CDN)|Standard mit anderen Konfigurationen|Zulässig im Zustand **Beendet** (über Azure-Portal) Im Zustand „Wird ausgeführt“ nicht zulässig.
Version 1.0 mit mindestens einer Streamingeinheit mit CDN|Standard-/Premium ohne CDN|Zulässig im Zustand **Beendet**. Nicht zulässig im Zustand **Gestartet**.
Version 1.0 mit mindestens einer Streamingeinheit mit CDN|Standard mit/ohne CDN|Zulässig im Zustand **Beendet**. Nicht zulässig im Zustand **Gestartet**. CDN mit Version 1.0 wird gelöscht und ein neues erstellt und gestartet.
Version 1.0 mit mindestens einer Streamingeinheit mit CDN|Premium mit/ohne CDN|Zulässig im Zustand **Beendet**. Nicht zulässig im Zustand **Gestartet**. Klassisches CDN wird gelöscht und ein neues erstellt und gestartet.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

