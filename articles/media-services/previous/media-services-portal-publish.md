---
title: Veröffentlichen von Inhalten im Azure-Portal | Microsoft-Dokumentation
description: Dieses Tutorial führt Sie durch die Schritte zur Veröffentlichung Ihrer Inhalte im Azure-Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 2639d47a6c9d8da53c9d6bccde0d317698d5d9fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61127855"
---
# <a name="publish-content-in-the-azure-portal"></a>Veröffentlichen von Inhalten im Azure-Portal  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Übersicht
> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Damit Sie Benutzern eine URL zum Streamen oder Herunterladen Ihrer Inhalte zur Verfügung stellen können, müssen Sie zunächst einen Locator erstellen, um das Medienobjekt zu veröffentlichen. Locator bieten Zugriff auf Medienobjektdateien. Azure Media Services unterstützt zwei Arten von Locators: 

* **Streaminglocators (OnDemandOrigin)** . Streaminglocators werden für adaptives Streaming verwendet. Beispiele für adaptives Streaming sind Apple HTTP Live Streaming (HLS), Microsoft Smooth Streaming und Dynamic Adaptive Streaming über HTTP (DASH oder MPEG-DASH). Für die Erstellung eines Streaminglocators muss Ihr Medienobjekt eine ISM-Datei enthalten. Beispiel: http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Progressive Locators (Shared Access Signature, SAS)** . Progressive Locators werden verwendet, um Videos per progressivem Download bereitzustellen.

Eine HLS-Streaming-URL können Sie erstellen, indem Sie *(format=m3u8-aapl)* an die URL anhängen:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Eine Streaming-URL für die Wiedergabe von Smooth Streaming-Medienobjekten hat das folgende Format:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Eine MPEG-DASH-Streaming-URL können Sie erstellen, indem Sie *(format=mpd-time-csf)* an die URL anhängen:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Eine SAS-URL hat das folgende Format:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Weitere Informationen finden Sie unter [Übermitteln von Inhalten an Kunden](media-services-deliver-content-overview.md).

> [!NOTE]
> Locators, die vor März 2015 über das Azure-Portal erstellt wurden, laufen nach zwei Jahren ab.  
> 
> 

Das Ablaufdatum eines Locators können Sie mithilfe einer [REST-API](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) oder [.NET-API](https://go.microsoft.com/fwlink/?LinkID=533259) aktualisieren. 

> [!NOTE]
> Wenn Sie das Ablaufdatum eines SAS-Locators aktualisieren, ändert sich auch die URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>So verwenden Sie das Portal zum Veröffentlichen eines Objekts
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie **Einstellungen** > **Ressourcen**. Wählen Sie das Objekt aus, das Sie veröffentlichen möchten.
3. Wählen Sie die Schaltfläche **Veröffentlichen** aus.
4. Wählen Sie den Locatortyp aus.
5. Wählen Sie **Hinzufügen**.
   
    ![Veröffentlichen des Videos](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Die URL wird der Liste mit den **veröffentlichten URLs** hinzugefügt.

## <a name="play-content-in-the-portal"></a>Wiedergeben von Inhalten im Portal
Sie können Ihr Video in einem Inhaltsplayer im Azure-Portal testen.

Wählen Sie das Video und anschließend die Schaltfläche **Wiedergabe** aus.

![Wiedergeben des Videos im Azure-Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Folgende Überlegungen sollten berücksichtigt werden:

* Vergewissern Sie sich, dass das Video veröffentlicht wurde.
* Der Media Player des Azure-Portals verwendet für die Wiedergabe den Standard-Streamingendpunkt. Wenn Sie einen anderen Streamingendpunkt für die Wiedergabe verwenden möchten, können Sie die URL markieren, kopieren und in einen anderen Player einfügen. So können Sie Ihr Video beispielsweise mit dem [Azure Media Player](https://amsplayer.azurewebsites.net/azuremediaplayer.html) testen.
* Der Streamingendpunkt, von dem aus das Streaming erfolgen soll.  

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nächste Schritte
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

