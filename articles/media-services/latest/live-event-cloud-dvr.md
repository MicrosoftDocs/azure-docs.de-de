---
title: Azure Media Services-Liveereignis und ein Cloud-DVR | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, was eine Liveausgabe ist und wie ein Cloud-DVR verwendet wird.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/14/2019
ms.author: juliako
ms.openlocfilehash: 4dd14587ec7e1473953981c1ef8c32c59eb9a1d6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322334"
---
# <a name="using-a-cloud-dvr"></a>Verwenden eines Cloud-DVR

[Liveausgaben](https://docs.microsoft.com/rest/api/media/liveoutputs) ermöglichen es Ihnen, die Eigenschaften des ausgehenden Livestreams zu steuern, etwa wie viel des Streams aufgezeichnet wird (z.B. die Kapazität des Cloud-DVR) und ob die Zuschauer mit der Wiedergabe des Livestreams beginnen können. Die Beziehung zwischen einem **Liveereignis** und der zugehörigen **Liveausgabe** ist ähnlich wie bei einer traditionellen Fernsehsendung. Hierbei strahlt ein Sender ein fortlaufendes Videosignal aus (**Liveereignis**), während eine Aufzeichnung für einen bestimmten Zeitabschnitt wie beispielsweise die abendliche Nachrichtensendung zwischen 20:00 und 20:15 Uhr programmiert wird (**Liveausgabe**). Sie können Fernsehsendungen mit einem digitalen Videorecorder (DVR) aufnehmen – die entsprechende Funktion in Liveereignissen wird über die ArchiveWindowLength-Eigenschaft verwaltet. Es handelt sich um eine ISO-8601-Zeitspanne (z.B. PTHH:MM:SS), die die Kapazität des Digitalrecorders angibt. Diese kann von mindestens 3 Minuten auf bis zu maximal 25 Stunden eingestellt werden.

## <a name="live-output"></a>Liveausgabe

Der Wert **ArchiveWindowLength** legt fest, wie weit zurück ein Zuschauer von der aktuellen Liveposition aus suchen kann.  **ArchiveWindowLength** legt außerdem fest, auf welche Länge Clientmanifeste anwachsen können.

Angenommen, Sie streamen ein Fußballspiel, und der Wert für **ArchiveWindowLength** ist nur auf 30 Minuten festgelegt. Ein Zuschauer, der 45 Minuten nach Spielbeginn damit beginnt, das Ereignis zu verfolgen, kann höchstens eine Suche bis zur 15-Minuten-Markierung durchführen. Ihre **Liveausgabe** für das Spiel wird fortgesetzt, bis das **Liveereignis** beendet wird. Der Inhalt außerhalb von **ArchiveWindowLength** wird jedoch fortlaufend aus dem Speicher verworfen und ist nicht wiederherstellbar. In diesem Beispiel würden die Videodaten zwischen dem Ereignisstart und der 15-Minuten-Markierung von Ihrem DVR und aus dem Container im Blobspeicher für das [Medienobjekt](https://docs.microsoft.com/rest/api/media/assets) gelöscht werden. Das Archiv ist nicht wiederherstellbar und wird aus dem Container im Azure-Blobspeicher gelöscht.

Jeder **Liveausgabe** ist ein **Medienobjekt** zugeordnet, das zum Aufzeichnen des Videos im zugeordneten Azure Blob Storage-Container verwendet wird. Zum Veröffentlichen der Liveausgabe müssen Sie einen **Streaminglocator** für das zugehörige **Medienobjekt** erstellen. Nach dem Erstellen eines [Streaminglocators](https://docs.microsoft.com/rest/api/media/streaminglocators) können Sie eine Streaming-URL erstellen, die Sie Ihren Zuschauern bereitstellen.

Ein **Liveereignis** unterstützt bis zu drei gleichzeitig ausgeführte **Liveausgaben**, sodass Sie maximal drei Aufzeichnungen/Archive aus einem Livestream erstellen können. Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Angenommen, Sie müssen rund um die Uhr einen linearen Livefeed senden. Sie möchten jedoch den ganzen Tag über „Aufzeichnungen“ der unterschiedlichen Programme erstellen, die Sie Ihren Kunden auf Abruf zur Nachbetrachtung anbieten können. Für dieses Szenario erstellen Sie zunächst eine primäre Liveausgabe mit einem kurzen Archivfenster von maximal einer Stunde. Dies ist der primäre Livestream, in den sich die Zuschauer einschalten können. Sie erstellen einen **Streaminglocator** für diese **Liveausgabe** und veröffentlichen ihn als „Livefeed“ in Ihrer Anwendung oder auf Ihrer Website. Während das **Liveereignis** ausgeführt wird, können Sie programmgesteuert eine zweite parallele **Liveausgabe** zu Beginn eines Programms erstellen (oder 5 Minuten früher, um Material zum späteren Schneiden bereitzustellen). Diese zweite **Liveausgabe** kann 5 Minuten nach Programmende gelöscht werden. Mit diesem zweiten **Medienobjekt** können Sie einen neuen **Streaminglocator** erstellen, um dieses Programm als On-Demand-Medienobjekt in Ihrem Anwendungskatalog bereitzustellen. Sie können diesen Vorgang für andere Programmgrenzen oder für Highlights, die Sie als On-Demand-Videos freischalten möchten, mehrfach wiederholen, während der „Livefeed“ für die erste **Liveausgabe** weiterhin den linearen Feed sendet. 

> [!NOTE]
> **Liveausgaben** werden bei der Erstellung gestartet und beim Löschen beendet. Wenn Sie die **Liveausgabe** löschen, werden das zugrunde liegende **Medienobjekt** und dessen Inhalt nicht gelöscht. 
>
> Wenn Sie das Medienobjekt der **Liveausgabe** mit einem **Streaminglocator** veröffentlicht haben, ist das **Liveereignis** (bis zur DVR-Fensterlänge) weiterhin bis zu Ablauf oder Löschung des **Streaminglocators** sichtbar, je nachdem, was zuerst eintritt.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)

