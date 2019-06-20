---
title: Medienanalyse mithilfe des Azure-Portals | Microsoft Docs
description: In diesem Thema wird beschrieben, wie Sie Medien mit Media Analytics-Medienprozessoren (MPs) über das Azure-Portal verarbeiten.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61131756"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Medienanalyse mithilfe des Azure-Portals 
> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Übersicht
Azure Media Services Media Analytics ist eine Sammlung von Sprach- und Anzeigekomponenten (auf Unternehmensebene zu Compliance, Sicherheit und globaler Reichweite). Daraus können Organisationen und Unternehmen nutzbringende Einblicke in ihre Videodateien erhalten. Eine ausführlichere Übersicht über Azure Media Services Analytics finden Sie in [diesem Thema](media-services-analytics-overview.md). 

In diesem Thema wird beschrieben, wie Sie Medien mit Media Analytics-Medienprozessoren (MPs) über das Azure-Portal verarbeiten. Mit Media Analytics-MPs werden MP4- oder JSON-Dateien erzeugt. Wenn ein Medienprozessor eine MP4-Datei erstellt hat, können Sie die Datei progressiv herunterladen. Wenn ein Medienprozessor eine JSON-Datei erstellt hat, können Sie die Datei aus Azure Blob Storage herunterladen. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Auswählen eines zu analysierenden Assets
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie im Fenster **Einstellungen** die Option **Assets**.  
   
    ![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Wählen Sie das Asset aus, das Sie analysieren möchten, und klicken Sie auf die Schaltfläche **Analysieren**.
   
    ![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. Wählen Sie im Fenster **Medienobjekt mit Media Analytics verarbeiten** den Prozessor aus. 
   
    Im restlichen Teil dieses Artikels wird beschrieben, wie und wofür Sie die einzelnen Prozessoren verwenden können. 
5. Wählen Sie **Erstellen**, um einen Auftrag zu starten.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Mit dem Medienprozessor (MP) **Azure Media Indexer** können Sie Mediendateien und Inhalte durchsuchbar machen sowie Untertitelspuren und Schlüsselwörter generieren. Diese Abschnitte enthalten einige Details zu Optionen, die Sie für diesen MP angeben können.

![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Sprache
Die natürliche Sprache, die in der Multimediadatei erkannt werden soll. Beispiel: Englisch oder Spanisch. 

### <a name="captions"></a>Untertitel
Sie können ein Untertitelformat auswählen, das für Ihre Inhalte generiert wird. Ein Indizierungsauftrag kann Untertiteldateien in den folgenden Formaten generieren:  

* **SAMI**
* **TTML**
* **WebVTT**

Untertiteldateien in diesen Formaten können verwendet werden, um Hörgeschädigten Audio- und Videodateien zugänglich zu machen.

### <a name="aib-file"></a>AIB-Datei
Wählen Sie diese Option, wenn Sie die Audioindex-Blobdatei zur Verwendung mit dem benutzerdefinierten SQL Server-IFilter generieren möchten. Weitere Informationen finden Sie in [diesem](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) Blog.

### <a name="keywords"></a>Schlüsselwörter
Wählen Sie diese Option, wenn Sie eine XML-Schlagwortdatei generieren möchten. Die XML-Schlagwortdatei enthält Stichwörter, die gemeinsam mit Frequenz- und Offsetinformationen aus dem Sprachinhalt extrahiert werden.

### <a name="job-name"></a>Auftragsname
Ein Anzeigename, über den Sie den Auftrag identifizieren können. In [diesem Artikel](media-services-portal-check-job-progress.md) wird beschrieben, wie Sie den Status eines Auftrags überwachen können. 

### <a name="output-file"></a>Ausgabedatei
Ein Anzeigename, über den Sie den ausgegebenen Inhalt identifizieren können. 

### <a name="speed"></a>Geschwindigkeit
Geben Sie die Geschwindigkeit an, mit der das Eingabevideo beschleunigt werden soll. Die Ausgabe ist eine stabilisierte Zeitrafferwiedergabe der Videoeingabe.

### <a name="job-name"></a>Auftragsname
Ein Anzeigename, über den Sie den Auftrag identifizieren können. In [diesem Artikel](media-services-portal-check-job-progress.md) wird beschrieben, wie Sie den Status eines Auftrags überwachen können. 

### <a name="output-file"></a>Ausgabedatei
Ein Anzeigename, über den Sie den ausgegebenen Inhalt identifizieren können. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Mit dem Medienprozessor (MP) **Azure Media Face Detector** können Sie Bewegungen zählen und nachverfolgen und sogar Teilnahme sowie Reaktion der Zielgruppe anhand von Gesichtsausdrücken einschätzen. Dieser Dienst enthält zwei Funktionen: 

* **Gesichtserkennung**
  
    Die Gesichtserkennung sucht und verfolgt menschliche Gesichter in einem Video. Mehrere Gesichter können erkannt und anschließend nachverfolgt werden, während sie sich bewegen, wobei Metadaten zu Zeit und Ort in einer JSON-Datei zurückgegeben werden. Während der Nachverfolgung wird versucht, dem gleichen Gesicht eine konsistente ID zuzuweisen, während die Person sich auf dem Bildschirm bewegt, auch wenn sie verdeckt wird oder kurzzeitig nicht im Frame ist.
  
  > [!NOTE]
  > Diese Dienste führen keine Gesichtserkennung durch. Eine Person, die zu lange nicht im Frame ist oder verdeckt wird, erhält bei ihrer Rückkehr eine neue ID.
  > 
  > 
* **Emotionenerkennung**
  
    Emotionenerkennung ist eine optionale Komponente des Gesichtserkennungs-Medienprozessors, die eine Analyse verschiedener emotionaler Attribute in den Gesichtern liefert, einschließlich Glück, Trauer, Angst, Wut und anderer Emotionen. 

![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Erkennungsmodus
Einer der folgenden Modi kann vom Prozessor verwendet werden:

* Gesichtserkennung
* Emotionserkennung nach Gesicht
* Erkennung aggregierter Emotionen

### <a name="job-name"></a>Auftragsname
Ein Anzeigename, über den Sie den Auftrag identifizieren können. In [diesem Artikel](media-services-portal-check-job-progress.md) wird beschrieben, wie Sie den Status eines Auftrags überwachen können. 

### <a name="output-file"></a>Ausgabedatei
Ein Anzeigename, über den Sie den ausgegebenen Inhalt identifizieren können. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Mit dem Medienprozessor (MP) **Azure Media Motion Detector** können Sie effizient Ihrem Interesse entsprechende Abschnitte innerhalb eines ansonsten langen und ereignislosen Videos identifizieren. Mit Bewegungserkennung können in Aufnahmen statischer Kameras Abschnitte des Videos identifiziert werden, wo Bewegung auftritt. Es wird eine JSON-Datei generiert, die Metadaten mit Zeitstempeln und der Umgebungsregion des Ereignisses enthält.

Diese für Sicherheitsvideoaufnahmen konzipierte Technologie kann Bewegung in relevante Ereignisse und falsch-positive Ergebnisse – z. B. Schatten und Beleuchtungsänderungen – kategorisieren. So können Sie Sicherheitswarnungen aus Kameraaufnahmen generieren, ohne unzählige irrelevante Ereignisse auswerten zu müssen, und sind in der Lage, Momente von Interesse aus sehr langen Überwachungsvideos zu extrahieren.

![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Dieser Prozessor kann Ihnen dabei helfen, Zusammenfassungen von langen Videos durch das automatische Auswählen von interessanten Ausschnitten aus dem Quellvideo zu erstellen. Dies ist hilfreich, wenn Sie einen schnellen Überblick darüber bieten wollen, was man in einem langen Video zu sehen bekommt. Ausführliche Informationen und Beispiele finden Sie unter [Verwenden von Azure Media-Videominiaturansichten zum Erstellen einer Videozusammenfassung](media-services-video-summarization.md)

![Analysieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Auftragsname
Ein Anzeigename, über den Sie den Auftrag identifizieren können. In [diesem Artikel](media-services-portal-check-job-progress.md) wird beschrieben, wie Sie den Status eines Auftrags überwachen können. 

### <a name="output-file"></a>Ausgabedatei
Ein Anzeigename, über den Sie den ausgegebenen Inhalt identifizieren können. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
Dieser Prozessor hilft Ihnen, potenzielle jugendgefährdende und rassistische Inhalte in Videos zu erkennen. Der Prozessor erkennt automatisch Einstellungen und Keyframes im Video. Er wertet Keyframes auf potenzielle jugendgefährdende oder rassistische Inhalte aus und schlägt basierend auf Standardschwellenwerten Überprüfungen vor. Ausführliche Informationen und Beispiele finden Sie unter [Verwenden von Azure Media Content Moderator zum Erkennen möglicher jugendgefährdender und rassistischer Inhalte](media-services-content-moderation.md)

![Moderieren von Videos](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Verwenden Sie „2.0“.

### <a name="mode"></a>Mode
Die Version 2.0 ignoriert die `Mode`-Einstellung.

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Media Services-Lernpfade an.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
