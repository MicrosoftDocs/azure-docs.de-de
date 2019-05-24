---
author: laurenhughes
ms.service: batch
ms.topic: include
ms.date: 11/09/2018
ms.author: lahugh
ms.openlocfilehash: bd51eabcff412de4928c682683b2a69b3e82e601
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66127357"
---
### <a name="retrieve-output-files"></a>Abrufen von Ausgabedateien

Sie können das Azure-Portal verwenden, um die ausgegebenen MP3-Dateien herunterzuladen, die durch die ffmpeg-Aufgaben generiert werden. 

1. Klicken Sie auf **Alle Dienste** > **Speicherkonten** und anschließend auf den Namen Ihres Speicherkontos.
2. Klicken Sie auf **Blobs** > *Ausgabe*.
3. Klicken Sie mit der rechten Maustaste auf eine der ausgegebenen MP3-Dateien, und klicken Sie anschließend auf **Herunterladen**. Folgen Sie den Anweisungen in Ihrem Browser, um die Datei zu öffnen oder zu speichern.

![Herunterladen der Ausgabedatei](./media/batch-common-tutorial-download/download.png)

Die Dateien können auch programmgesteuert aus den Computeknoten oder aus dem Speichercontainer heruntergeladen werden. Dies wird in diesem Beispiel allerdings nicht gezeigt.
