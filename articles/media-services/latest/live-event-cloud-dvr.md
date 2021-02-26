---
title: Verwenden von Timeshift zum Erstellen der bedarfsgesteuerten Videowiedergabe
description: In diesem Artikel erfahren Sie, wie Sie mit Timeshift und Liveausgaben Livestreams aufzeichnen und bedarfsgesteuerte Wiedergaben erstellen können.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: a2bb876c164f0df56a8b7f3c4a3666ff306e9416
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955935"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Verwenden von Timeshift und Liveausgaben zum Erstellen der bedarfsgesteuerten Videowiedergabe

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services verhält sich ein [Liveausgabe](/rest/api/media/liveoutputs)-Objekt wie ein digitaler Videorekorder, der Ihren Livestream erfasst und in einem Medienobjekt (Asset) in Ihrem Media Services-Konto aufzeichnet. Der aufgezeichnete Inhalt wird in dem Container gespeichert, der durch die [Asset](/rest/api/media/assets)-Ressource definiert ist (der Container befindet sich in dem Azure Storage-Konto, das Ihrem Konto zugeordnet ist). Die Liveausgabe ermöglicht Ihnen auch, einige der Eigenschaften des ausgehenden Livestreams zu steuern, etwa welcher Anteil des Streams in der Archivaufzeichnung verwahrt wird (beispielsweise die Kapazität des Cloud-DVR) und wann Zuschauer mit der Wiedergabe des Livestreams beginnen können. Das Archiv auf dem Datenträger ist ein kreisförmiges „Archivfenster“, das nur die Menge an Inhalten enthält, die in der **archiveWindowLength**-Eigenschaft der Liveausgabe angegeben ist. Inhalt außerhalb dieses Fensters wird automatisch aus dem Speichercontainer entfernt und ist nicht wiederherstellbar. Der archiveWindowLength-Wert entspricht einer ISO-8601-Zeitspanne (z. B. PTHH:MM:SS), die die Kapazität des DVRs angibt. Der Wert kann von mindestens 1 Minute bis zu einem Höchstwert von 25 Stunden eingestellt werden.

Die Beziehung zwischen einem Liveereignis und der zugehörigen Liveausgabe ist ähnlich wie bei einer traditionellen Fernsehsendung, bei der ein Kanal (Liveereignis) einen konstanten Videodatenstrom darstellt und eine Aufzeichnung (Liveausgabe) auf ein bestimmtes Zeitsegment (z. B. Abendnachrichten von 18:30 bis 19:00 Uhr) ausgerichtet ist. Sobald der Stream an das Liveereignis übertragen wird, können Sie das Streamingereignis starten, indem Sie ein Medienobjekt, eine Liveausgabe und einen Streaminglocator erstellen. Durch die Liveausgabe wird der Datenstrom archiviert und über den [Streamingendpunkt](/rest/api/media/streamingendpoints) für die Zuschauer verfügbar gemacht. Sie können mehrere Liveausgaben (maximal bis zu drei) mit unterschiedlichen Archivlängen und Einstellungen für ein Liveereignis erstellen. Informationen zum Workflow für das Livestreaming finden Sie im Abschnitt [Allgemeine Schritte](live-streaming-overview.md#general-steps).

## <a name="using-a-dvr-during-an-event"></a>Verwenden eines DVRs während eines Ereignisses

In diesem Abschnitt wird erläutert, wie ein DVR während eines Ereignisses verwendet werden kann, um zu steuern, welche Teile des Datenstroms für „Zurückspulen“ verfügbar sind.

Der Wert von `archiveWindowLength` bestimmt, wie weit zurück in der Zeit ein Zuschauer von der aktuellen Liveposition aus gehen kann. Der `archiveWindowLength`-Wert legt außerdem fest, auf welche Länge Clientmanifeste anwachsen können.

Angenommen, Sie streamen ein Fußballspiel, und der Wert für `ArchiveWindowLength` ist auf nur 30 Minuten festgelegt. Ein Zuschauer, der 45 Minuten nach Spielbeginn damit beginnt, das Ereignis zu verfolgen, kann höchstens eine Suche bis zur 15-Minuten-Markierung durchführen. Ihre Liveausgaben für das Spiel werden fortgesetzt, bis das Liveereignis beendet wird. Inhalte, die außerhalb von „archiveWindowLength“ liegen, werden kontinuierlich aus dem Speicher entfernt und sind nicht wiederherstellbar. In diesem Beispiel würden die Videodaten zwischen dem Ereignisstart und der 15-Minuten-Markierung von Ihrem DVR und aus dem Container im Blobspeicher für das Medienobjekt gelöscht werden. Das Archiv ist nicht wiederherstellbar und wird aus dem Container im Azure-Blobspeicher gelöscht.

Ein Liveereignis unterstützt bis zu drei gleichzeitig aktive Liveausgaben (Sie können maximal drei Aufzeichnungen/Archive gleichzeitig aus einem Livestream erstellen). Auf diese Weise können Sie verschiedene Teile eines Ereignisses nach Bedarf veröffentlichen und archivieren. Angenommen, Sie müssen rund um die Uhr einen linearen Livefeed senden. Sie möchten jedoch den ganzen Tag über „Aufzeichnungen“ der unterschiedlichen Programme erstellen, die Sie Ihren Kunden auf Abruf zur Nachbetrachtung anbieten können. Für dieses Szenario erstellen Sie zunächst eine primäre Liveausgabe mit einem kurzen Archivfenster von maximal einer Stunde. Dies ist der primäre Livestream, in den sich die Zuschauer einschalten können. Sie erstellen einen Streaminglocator für diese Liveausgabe und veröffentlichen ihn als „Livefeed“ in Ihrer App oder auf Ihrer Website. Während das Liveereignis ausgeführt wird, können Sie programmgesteuert eine zweite parallele Liveausgabe zu Beginn eines Programms erstellen (oder 5 Minuten früher, um Material zum späteren Schneiden bereitzustellen). Diese zweite Liveausgabe kann 5 Minuten nach Programmende gelöscht werden. Mit diesem zweiten Medienobjekt können Sie einen neuen Streaminglocator erstellen, um dieses Programm als On-Demand-Medienobjekt im Katalog Ihrer App bereitzustellen. Sie können diesen Vorgang für andere Programmgrenzen oder für Highlights, die Sie als On-Demand-Videos freischalten möchten, mehrfach wiederholen, während der „Livefeed“ für die erste Liveausgabe weiterhin den linearen Feed sendet.

## <a name="creating-an-archive-for-on-demand-playback"></a>Erstellen eines Archivs für bedarfsgesteuerte Wiedergabe (On-Demand-Wiedergabe)

Das Medienobjekt, in dem die Liveausgabe archiviert wird, wird automatisch zu einem bedarfsgesteuerten Medienobjekt, wenn die Liveausgabe gelöscht wird. Sie müssen alle Liveausgaben löschen, bevor ein Liveereignis angehalten werden kann. Sie können ein optionales [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body)-Flag verwenden, um Liveausgaben beim Anhalten automatisch zu entfernen.

Auch nach dem Beenden und Löschen des Ereignisses können die Benutzer archivierte Inhalte als Video auf Abruf streamen, solange Sie das Medienobjekt nicht löschen. Ein Medienobjekt kann nicht gelöscht werden, wenn es von einem Ereignis verwendet wird. Zuerst muss das betreffende Ereignis gelöscht werden.

Wenn Sie das Medienobjekt der Liveausgabe mit einem Streaminglocator veröffentlicht haben, ist das Liveereignis (bis zur DVR-Fensterlänge) weiterhin bis zum Ablauf oder der Löschung des Streaminglocators sichtbar, je nachdem, welches Ereignis zuerst eintritt.

Weitere Informationen finden Sie unter

- [Übersicht über das Livestreaming](live-streaming-overview.md)
- [Tutorial zu Livestreaming](stream-live-tutorial-with-api.md)

> [!NOTE]
> Wenn Sie die Liveausgabe löschen, werden das zugrunde liegende Medienobjekt und dessen Inhalt nicht gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen von Subclips für ein Video](subclip-video-rest-howto.md)
* [Definieren von Filtern für Ihre Medienobjekte](filters-dynamic-manifest-rest-howto.md)
