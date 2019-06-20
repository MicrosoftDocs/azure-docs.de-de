---
title: Azure Media Services – Übersicht | Microsoft-Dokumentation
description: Dieses Thema bietet eine Übersicht über Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: juliako
ms.openlocfilehash: 7f0a071012685ee192d4f866396a5953d0a4508c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937253"
---
# <a name="azure-media-services-overview"></a>Azure Media Services – Übersicht 

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 3](../latest/media-services-overview.md)
> * [Version 2](media-services-overview.md)

> [!NOTE]
> Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).

Microsoft Azure Media Services (AMS) ist eine erweiterbare, cloudbasierte Plattform, die Entwicklern das Erstellen von skalierbaren Medienverwaltungslösungen und Bereitstellungsanwendungen ermöglicht. Media Services basiert auf REST-APIs, mit denen Sie auf sichere Weise Video- oder Audioinhalte hochladen, speichern, codieren und verpacken können – sowohl für eine bedarfsgesteuerte als auch für eine auf Livestreaming basierende Bereitstellung auf verschiedenen Clients (z.B. TV, PC und mobile Geräte).

Sie können mithilfe von Media Services vollständige End-to-End-Workflows erstellen. Es ist auch möglich, Drittanbieterkomponenten für einige Elemente Ihres Workflows zu nutzen. Beispielsweise können Sie die Codierung mit einem Encoder eines Drittanbieters durchführen. Anschließend sorgen Sie mit Media Services für Upload, Schutz, Paketierung und Bereitstellung. Sie können Ihre Inhalte live streamen oder sie bei Bedarf bereitstellen. 

## <a name="prerequisites"></a>Voraussetzungen

Um mit Azure Media Services loszulegen, sollten Sie Folgendes haben:

* Ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](https://azure.microsoft.com).
* Ein Azure Media Services-Konto. Weitere Informationen finden Sie unter [Erstellen von Konten](media-services-portal-create-account.md).
* (Optional) Eine eingerichtete Entwicklungsumgebung. Wählen Sie .NET oder REST-API für Ihre Entwicklungsumgebung. Weitere Informationen finden Sie unter [Einrichten der Umgebung](media-services-dotnet-how-to-use.md).

    Erfahren Sie außerdem, wie Sie programmgesteuert eine [Verbindung mit AMS-API](media-services-use-aad-auth-to-access-ams-api.md) herstellen können.
* Einen Standard- oder Premium-Streamingendpunkt im Status „Gestartet“.  Weitere Informationen finden Sie unter [Verwalten von Streamingendpunkten](media-services-portal-manage-streaming-endpoints.md).

## <a name="sdks-and-tools"></a>SDKs und Tools

Zum Entwickeln von Media Services-Lösungen können Sie folgende Komponenten verwenden:

* [Media Services-REST-API](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference)
* Eines der verfügbaren Client-SDKs:
    * Azure Media Services SDK für .NET
    
        * [NuGet-Paket](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub-Quellcode](https://github.com/Azure/azure-sdk-for-media-services)
    * [Azure SDK für Java](https://github.com/Azure/azure-sdk-for-java)
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php)
    * [Azure Media Services für Node.js](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Dies ist eine nicht von Microsoft stammende Version des Node.js SDK. Sie wird von einer Community verwaltet und bietet derzeit keine 100 %-ige Abdeckung der AMS-APIs).
* Vorhandene Tools:
    * [Azure-Portal](https://portal.azure.com/)
    * [Azure Media Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Azure Media Services Explorer [AMSE] ist eine WinForms-/C#-Anwendung für Windows)

> [!NOTE]
> Informationen zum Abrufen der aktuellen Version des Java SDK sowie zu den ersten Schritten beim Entwickeln mit Java finden Sie unter [Erste Schritte mit dem Java-Client-SDK für Azure Media Services](https://docs.microsoft.com/azure/media-services/media-services-java-how-to-use). <br/>
> Wenn Sie das aktuelle PHP SDK für Media Services herunterladen möchten, suchen Sie im [Packagist-Repository](https://packagist.org/packages/microsoft/windowsazure#v0.5.7) nach Version 0.5.7 des Microsoft-/WindowAzure-Pakets.  

## <a name="code-samples"></a>Codebeispiele

Im Katalog mit **Azure-Codebeispielen** stehen mehrere Codebeispiele zur Verfügung: [Azure Media Services-Codebeispiele](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Konzepte

Azure Media Services-Konzepte finden Sie unter [Konzepte](media-services-concepts.md).

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Unterstützte Szenarien und rechenzentrumsübergreifende Verfügbarkeit von Media Services

Ausführliche Informationen finden Sie unter [AMS scenarios and availability of features and services across data centers](scenarios-and-availability.md) (AMS-Szenarien und rechenzentrumsübergreifende Verfügbarkeit von Funktionen und Diensten).

## <a name="service-level-agreement-sla"></a>Vereinbarung zum Servicelevel (SLA)

Weitere Informationen finden Sie im [Microsoft Azure-SLA](https://azure.microsoft.com/support/legal/sla/).

Weitere Informationen zur Verfügbarkeit in Datencentern finden Sie im Abschnitt [Verfügbarkeit](scenarios-and-availability.md#availability).

## <a name="support"></a>Support

[Azure-Support](https://azure.microsoft.com/support/options/) bietet Supportoptionen für Azure, Media Services eingeschlossen.

## <a name="provide-feedback"></a>Feedback geben

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
