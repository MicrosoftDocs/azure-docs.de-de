---
title: Skalieren der Medienverarbeitung durch Hinzufügen von Codierungseinheiten – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit .NET Codierungseinheiten hinzufügen.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 33f7625a-966a-4f06-bc09-bccd6e2a42b5
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako;milangada;
ms.openlocfilehash: 65b647a375ecbe70f7f29af5b09827f1c34dcd6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61217165"
---
# <a name="how-to-scale-encoding-with-net-sdk"></a>Skalieren der Codierung mit .NET SDK
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-scale-media-processing.md)
> * [.NET](media-services-dotnet-encoding-units.md)
> * [REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Übersicht
> [!IMPORTANT]
> Lesen Sie unbedingt die [Übersicht](media-services-scale-media-processing-overview.md), um weitere Informationen zum Skalieren der Medienverarbeitung zu erhalten.
> 
> 

Führen Sie folgende Schritte aus, um den Typ reservierter Einheiten und die Anzahl reservierter Einheiten für die Codierung mit dem .NET SDK zu ändern:

    IEncodingReservedUnit encodingS1ReservedUnit = _context.EncodingReservedUnits.FirstOrDefault();
    encodingS1ReservedUnit.ReservedUnitType = ReservedUnitType.Basic; // Corresponds to S1
    encodingS1ReservedUnit.Update();
    Console.WriteLine("Reserved Unit Type: {0}", encodingS1ReservedUnit.ReservedUnitType);

    encodingS1ReservedUnit.CurrentReservedUnits = 2;
    encodingS1ReservedUnit.Update();

    Console.WriteLine("Number of reserved units: {0}", encodingS1ReservedUnit.CurrentReservedUnits);

## <a name="opening-a-support-ticket"></a>Öffnen eines Supporttickets

Standardmäßig kann jedes Media Services-Konto auf bis zu 10 reservierte S2- oder S3-Einheiten für Medien (MRUs) oder 25 S1-MRUs und 5 für das bedarfsgesteuerte Streaming reservierte Einheiten skaliert werden. Sie können einen höheren Grenzwert durch Öffnen eines Supporttickets anfordern.

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

