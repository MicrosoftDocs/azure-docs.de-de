---
title: 'Azure Communication Services: Bekannte Probleme'
description: Erfahren Sie mehr über bekannte Probleme mit Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 10/03/2020
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: e9e4b747d9d0ab39a1d0ecef6cf45e4cc0f9e2c5
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628115"
---
# <a name="known-issues-azure-communication-services"></a>Bekannte Probleme: Azure Communication Services

Dieser Artikel enthält Informationen zu bekannten Problemen mit Azure Communication Services.

## <a name="video-streaming-quality-on-chromeandroid"></a>Videostreamingqualität unter Chrome/Android 

Die Videostreamingleistung kann unter Chrome/Android beeinträchtigt sein.

### <a name="possible-causes"></a>Mögliche Ursachen
Die Qualität von Remotestreams hängt von der Auflösung des clientseitigen Renderers ab, der zum Initiieren dieses Streams verwendet wurde. Wenn ein Empfänger einen Remotestream abonniert, bestimmt er seine eigene Auflösung basierend auf den clientseitigen Rendererdimensionen des Senders.

## <a name="bluetooth-headset-microphones-are-not-detected"></a>Mikrofone von Bluetooth-Headsets werden nicht erkannt.

Möglicherweise treten Probleme beim Herstellen einer Verbindung zwischen Ihrem Bluetooth-Headset und einem Communication Services-Anruf auf.

### <a name="possible-causes"></a>Mögliche Ursachen
Es gibt keine Option zum Auswählen von Bluetooth-Mikrofonen unter iOS.


## <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Ein wiederholter Wechsel von Videogeräten kann dazu führen, dass das Videostreaming vorübergehend angehalten wird.

Das Wechseln zwischen Videogeräten kann dazu führen, dass Ihr Videostream angehalten wird, während der Stream vom ausgewählten Gerät erfasst wird.

### <a name="possible-causes"></a>Mögliche Ursachen
Das Streaming von und Wechseln zwischen Mediengeräten ist rechenintensiv. Ein häufiger Wechsel kann zu Leistungsbeeinträchtigungen führen. Entwicklern wird empfohlen, einen Gerätestream zu beenden, bevor sie einen anderen starten.
