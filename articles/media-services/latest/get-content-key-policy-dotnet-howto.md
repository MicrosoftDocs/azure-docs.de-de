---
title: Abrufen eines Signaturschlüssels aus der vorhandenen Richtlinie mit Media Services V3 .NET SDK – Azure | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie Sie mit Media Services V3 .NET SDK einen Signaturschlüssel aus der vorhandenen Richtlinie abrufen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322633"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Abrufen eines Signaturschlüssels aus der vorhandenen Richtlinie

Eines der wichtigsten Entwurfsprinzipien der v3-API ist es, die API sicherer zu machen. v3-APIs geben bei einem **Get**- oder **List**-Vorgang keine geheimen Schlüssel oder Anmeldeinformationen zurück. Die ausführliche Erläuterung finden Sie hier: Weitere Informationen finden Sie unter [RBAC and Media Services accounts (RBAC und Media Services-Konten)](rbac-overview.md).

Anhand des Beispiels in diesem Artikel wird veranschaulicht, wie .NET verwendet wird, um einen Signaturschlüssel aus der vorhandenen Richtlinie abzurufen. 
 
## <a name="download"></a>Download 

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das vollständige .NET-Beispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Die Richtlinie „ContentKeyPolicy“ mit Beispiel für Geheimnisse befindet sich im Ordner [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Abrufen von „ContentKeyPolicy“ mit Geheimnissen 

Um den Schlüssel abzurufen, verwenden Sie **GetPolicyPropertiesWithSecretsAsync**, wie im folgenden Beispiel gezeigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Nächste Schritte

[Entwurf eines Multi-DRM-Inhaltsschutzsystems mit Zugriffssteuerung](design-multi-drm-system-with-access-control.md) 
