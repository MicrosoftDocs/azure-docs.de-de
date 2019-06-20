---
title: Erstellen einer Azure Media Services-Auftragseingabe aus einer lokalen Datei | Microsoft-Dokumentation
description: In diesem Thema wird gezeigt, wie eine Auftragseingabe aus einer lokalen Datei erstellen.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 3eb16034cc6507944ca7bebb59893e0d72a6f4c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322582"
---
# <a name="create-a-job-input-from-a-local-file"></a>Erstellen einer Auftragseingabe aus einer lokalen Datei

Wenn Sie in Media Services v3 Aufträge zur Verarbeitung Ihrer Videos übermitteln, müssen Sie Media Services mitteilen, wo sich das Eingabevideo befindet. Das Eingabevideo kann als Media Service-Medienobjekt gespeichert werden. In diesem Fall erstellen Sie ein Eingabemedienobjekt basierend auf einer Datei (die lokal oder in Azure Blob Storage gespeichert ist). In diesem Thema wird gezeigt, wie Sie eine Auftragseingabe aus einer lokalen Datei erstellen. Ein vollständiges Beispiel finden Sie in diesem [GitHub-Beispiel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-Beispiel

Der folgende Code zeigt, wie Sie ein Eingabemedienobjekt erstellen und es als Eingabe für den Auftrag nutzen können. Die „CreateInputAsset“-Funktion führt die folgenden Aktionen aus:

* Das Medienobjekt erstellen
* Eine nicht schreibgeschützte [SAS-URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) für den [Container](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) des Medienobjekts im Speicher abrufen
* Die Datei in den Container im Speicher mithilfe der SAS-URL hochladen

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Auftragsfehlercodes

Weitere Informationen finden Sie unter [Fehlercodes](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Auftragseingabe aus einer HTTPS-URL](job-input-from-http-how-to.md)
