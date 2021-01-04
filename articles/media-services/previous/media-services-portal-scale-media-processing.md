---
title: Skalieren der Medienverarbeitung mithilfe des Azure-Portals | Microsoft-Dokumentation
description: Dieses Tutorial führt Sie durch die Schritte zur Skalierung der Medienverarbeitung mithilfe des Azure-Portals.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: e500f733-68aa-450c-b212-cf717c0d15da
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: c637817e88e9258a0f80cf72f737b0deae753002
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "96012253"
---
# <a name="change-the-reserved-unit-type"></a>Ändern Sie den Typ reservierter Einheiten

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encoding-units.md)
> * [Portal](media-services-portal-scale-media-processing.md)
> * [REST](/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Übersicht

Ein Media Services-Konto ist dem Typ einer reservierten Einheit zugeordnet, der die Geschwindigkeit bestimmt, mit der Medienverarbeitungsaufgaben erfolgen. Sie können zwischen den folgenden reservierten Einheitentypen wählen: **S1**, **S2** oder **S3**. Derselbe Codierungsauftrag wird bei Verwendung der reservierten Einheit **S2** beispielsweise schneller ausgeführt als mit dem Typ **S1**.

Neben der Art der reservierten Einheit können Sie angeben, dass für Ihr Konto **reservierte Einheiten** (Reserved Units, RUs) bereitgestellt werden sollen. Die Anzahl bereitgestellter RUs bestimmt die Anzahl von Medienaufgaben, die gleichzeitig unter einem bestimmten Konto verarbeitet werden können.

>[!NOTE]
>RUs dienen der Parallelisierung der gesamten Medienverarbeitung, einschließlich der Indizierung von Aufträgen mit Azure Media Indexer. Allerdings erfolgt die Indizierung von Aufträgen im Gegensatz zur Codierung mit schnelleren reservierten Einheiten nicht schneller.

> [!IMPORTANT]
> Lesen Sie unbedingt das [Übersichtsthema](media-services-scale-media-processing-overview.md) , um weitere Informationen zum Skalieren der Medienverarbeitung zu erhalten.
> 
> 

## <a name="scale-media-processing"></a>Skalieren der Medienverarbeitung
Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten zu ändern:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihr Azure Media Services-Konto aus.
2. Wählen Sie im Fenster **Einstellungen** die Option **Reservierte Einheiten für Medien** aus.
   
    Verwenden Sie zum Ändern der Anzahl reservierter Einheiten für den ausgewählten Typ den Schieberegler unter **Reservierte Einheiten für Medien** am oberen Bildschirmrand.
   
    Zum Ändern von **RESERVIERTER EINHEITSTYP** klicken Sie auf die Leiste **Geschwindigkeit der reservierten Einheiten für die Verarbeitung**. Wählen Sie dann den benötigten Tarif aus: S1, S2 oder S3.
   
3. Klicken Sie auf die Schaltfläche "Speichern", um die Änderungen zu speichern.
   
    Die neuen reservierten Einheiten werden zugewiesen, wenn Sie auf SPEICHERN klicken.

## <a name="next-steps"></a>Nächste Schritte
Überprüfen Sie die Media Services-Lernpfade.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
