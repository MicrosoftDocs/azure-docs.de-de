---
title: Anleitung zur Behandlung von Problemen bei Livestreaming | Microsoft Docs
description: Dieses Thema bietet Empfehlungen zum Behandeln von Problemen bei Livestreaming.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f502e3228274840d23b9f52512280fc0d9f0553b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544693"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>Anleitung zur Behandlung von Problemen bei Livestreaming  

Dieser Artikel bietet Empfehlungen zum Behandeln von Problemen bei Livestreaming.

## <a name="issues-related-to-on-premises-encoders"></a>Probleme im Zusammenhang mit lokalen Encodern
Dieser Abschnitt enthält Empfehlungen zum Beheben von Problemen im Zusammenhang mit lokalen Encodern, die zum Senden eines Single-Bitrate-Datenstroms an für Livecodierung aktivierte AMS-Kanäle konfiguriert sind.

### <a name="problem-would-like-to-see-logs"></a>Problem: Anzeigen von Protokollen
* **Mögliches Problem**: Encoderprotokolle, mit denen möglicherweise Fehler behoben werden können, werden nicht gefunden.
  
  * **Telestream Wirecast:** Normalerweise finden Sie Protokolle unter „C:\Users\{Benutzername}\AppData\Roaming\Wirecast\“. 
  * **Elemental Live:** Links zu Protokollen finden Sie im Verwaltungsportal. Klicken Sie auf **Statistiken** und dann auf **Protokolle**. Auf der Seite **Protokolldateien** sehen Sie eine Liste der Protokolle für alle LiveEvent-Elemente. Wählen Sie das Protokoll für Ihre aktuelle Sitzung aus. 
  * **Flash Media Live Encoder:** Sie finden das **Protokollverzeichnis** auf der Registerkarte **Encoding Log** (Codierungsprotokoll).

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>Problem: Es ist keine Option zum Ausgeben eines progressiven Datenstroms vorhanden.
* **Mögliches Problem**: Der verwendete Encoder führt kein automatisches Deinterlacing aus. 
  
    **Schritte zur Problembehandlung:** Suchen Sie auf der Encoder-Benutzeroberfläche nach einer Option für Deinterlacing. Sobald Deinterlacing aktiviert ist, suchen Sie erneut nach Einstellungen für die progressive Ausgabe. 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>Problem: Ich habe verschiedene Encoderausgabeeinstellungen ausprobiert und kann immer noch keine Verbindung herstellen.
* **Mögliches Problem**: Der Azure-Codierungskanal wurde nicht ordnungsgemäß zurückgesetzt. 
  
    **Schritte zur Problembehandlung:** Stellen Sie sicher, dass der Encoder keine Pushübertragungen an AMS mehr ausführt, beenden Sie den Kanal, und setzen Sie ihn zurück. Sobald er erneut ausgeführt wird, versuchen Sie mit den neuen Einstellungen, den Encoder zu verbinden. Wenn das Problem damit immer noch nicht behoben ist, erstellen Sie einen vollkommen neuen Kanal. Manchmal werden Kanäle nach mehreren fehlgeschlagenen Versuchen beschädigt.  
* **Mögliches Problem**: Die GOP-Größe oder die Keyframeeinstellungen sind nicht optimal. 
  
    **Schritte zur Problembehandlung:** Die empfohlene GOP-Größe bzw. das empfohlene Keyframeintervall beträgt zwei Sekunden. Manche Encoder berechnen diese Einstellung als Anzahl von Bildern und andere als Anzahl von Sekunden. Beispiel:  Bei der Ausgabe mit 30 FPS beträgt die GOP-Größe 60 Frames; dies entspricht 2 Sekunden.  
* **Mögliches Problem**: Der Datenstrom wird durch geschlossene Ports blockiert. 
  
    **Schritte zur Problembehandlung:** Vergewissern Sie sich beim Streamen per RTMP in den Firewall- und/oder Proxyeinstellungen, dass die ausgehenden Ports 1935 und 1936 geöffnet sind. 

> [!NOTE]
> Wenn Sie nach dem Ausführen der Problembehandlungsschritte immer noch nicht streamen können, senden Sie über das klassische Azure-Portal ein Supportticket.
> 
> 

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

