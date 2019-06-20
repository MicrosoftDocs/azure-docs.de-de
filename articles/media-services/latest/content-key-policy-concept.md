---
title: 'Richtlinien für Inhaltsschlüssel in Media Services: Azure | Microsoft-Dokumentation'
description: In diesem Artikel wird erläutert, was Richtlinien für Inhaltsschlüssel sind und wie sie in Azure Media Services verwendet werden.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/22/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68778cea51144ec33efd4d5843a51b489ea17ca4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155736"
---
# <a name="content-key-policies"></a>Richtlinien für Inhaltsschlüssel

Mit Media Services können Sie Ihre zu übermittelnden Live- und On-Demand-Inhalte dynamisch mit Advanced Encryption Standard (AES-128) oder einem der drei wichtigsten DRM-Systeme verschlüsseln: Microsoft PlayReady, Google Widevine und Apple FairPlay. Media Services bietet auch einen Dienst für die Übermittlung von AES-Schlüsseln und DRM-Lizenzen (PlayReady, Widevine und FairPlay) an autorisierte Clients.

Um Verschlüsselungsoptionen für Ihren Datenstrom festzulegen, müssen Sie die [Streamingrichtlinie](streaming-policy-concept.md) erstellen und sie mit Ihrem [Streaminglocator](streaming-locators-concept.md) verknüpfen. Sie müssen eine [Richtlinie für den Inhaltsschlüssel](https://docs.microsoft.com/rest/api/media/contentkeypolicies) erstellen, mit der konfiguriert wird, wie der Inhaltsschlüssel (der sicheren Zugriff auf Ihre [Ressourcen](assets-concept.md) bereitstellt) an Endclients übermittelt wird. Die **Richtlinie für Inhaltsschlüssel** wird Ihrem **Streaminglocator** zugeordnet. Sie müssen die Anforderungen (Einschränkungen) für die Richtlinie für den Inhaltsschlüssel festlegen, die erfüllt sein müssen, damit Schlüssel mit der angegebenen Konfiguration an Clients übermittelt werden. 

Es wird empfohlen, Media Services Inhaltsschlüssel automatisch generieren zu lassen. Normalerweise würde ein langlebiger Schlüssel verwendet und das Vorhandensein der Richtlinie mithilfe von **Get** überprüft. Um den Schlüssel abzurufen, müssen Sie eine separate Aktionsmethode aufrufen, um geheime Schlüssel oder Anmeldeinformationen abzurufen, wie im folgenden Beispiel gezeigt.

**Richtlinien für Inhaltsschlüssel** sind aktualisierbar. Es empfiehlt sich beispielsweise, die Richtlinie zu aktualisieren, wenn Sie eine Schlüsselrotation ausführen müssen. Sie können auch den primären Verifizierungsschlüssel und die Liste der alternativen Verifizierungsschlüssel in der vorhandenen Richtlinie ändern. Es dauert bis zu 15 Minuten, die Schlüsselbereitstellungscaches zu aktualisieren und die aktualisierte Richtlinie zu übernehmen. 

> [!IMPORTANT]
> * Eigenschaften von **Inhaltssschlüsselrichtlinien** vom Datetime-Typ liegen immer im UTC-Format vor.
> * Sie sollten eine begrenzte Sammlung von Richtlinien für Ihr Media Services-Konto erstellen und diese für Ihre Streaminglocators wiederverwenden, wenn dieselben Optionen benötigt werden. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen](limits-quotas-constraints.md).

## <a name="example"></a>Beispiel

Um den Schlüssel abzurufen, verwenden Sie **GetPolicyPropertiesWithSecretsAsync**, wie im folgenden Beispiel gezeigt.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="filtering-ordering-paging"></a>Filterung, Sortierung, Paging

Informationen finden Sie unter [Filterung, Sortierung, Paginierung von Media Services-Entitäten](entities-overview.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der dynamischen AES-128-Verschlüsselung und des Schlüsselübermittlungsdiensts](protect-with-aes128.md)
* [Verwenden der dynamischen DRM-Verschlüsselung und des Lizenzbereitstellungsdiensts](protect-with-drm.md)
* [EncodeHTTPAndPublishAESEncrypted](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore/EncodeHTTPAndPublishAESEncrypted)
