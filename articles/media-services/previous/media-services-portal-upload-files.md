---
title: Hochladen von Dateien in ein Media Services-Konto über das Azure-Portal | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie über das Azure-Portal Dateien in ein Media Services-Konto hochladen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: 3ebeb3c601dd3f734265d49d60728056561928be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61127806"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Hochladen von Dateien in ein Media Services-Konto über das Azure-Portal 

> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

In Azure Media Services laden Sie Ihre digitalen Dateien in ein Medienobjekt hoch. Das Medienobjekt kann Videos, Audiodateien, Bilder, Miniaturansichtssammlungen, Texttitel und Untertiteldateien (und die dazugehörigen Metadaten) enthalten. Nachdem die Dateien hochgeladen wurden, werden Ihre Inhalte zur weiteren Verarbeitung und für das Streaming sicher in der Cloud gespeichert.

Für Media Services gilt in Bezug auf die Verarbeitung von Dateien eine maximale Dateigröße. Ausführliche Informationen zu den Grenzwerten für die Dateigröße finden Sie unter [Kontingente und Einschränkungen](media-services-quotas-and-limitations.md).

Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="upload-files"></a>Hochladen von Dateien
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie die Schaltfläche **Hochladen**.
   
    ![Hochladen von Dateien](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Das Fenster **Upload a video asset** (Videoobjekt hochladen) wird angezeigt.
   
   > [!NOTE]
   > Die Dateigröße für den Videoupload ist für Media Services nicht begrenzt.
 
3. Navigieren Sie auf Ihrem Computer zu dem Video, das Sie hochladen möchten. Wählen Sie das Video und anschließend **OK** aus.  
   
    Der Upload beginnt. Der Status wird unter dem Dateinamen angezeigt.  

Nach Abschluss des Uploadvorgangs wird das neue Medienobjekt im Bereich **Assets** (Medienobjekte) angezeigt. 

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über das [Codieren von hochgeladenen Medienobjekten](media-services-portal-encode.md).

* Sie können mithilfe von Azure Functions auch einen Codierungsauftrag auslösen, wenn eine Datei im konfigurierten Container eingeht. Weitere Informationen finden Sie im Beispiel unter [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Integrieren von Azure Media Services in Azure Functions und Logic Apps).


