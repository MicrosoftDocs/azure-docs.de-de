---
title: Hochladen von Inhalten in eine Medienobjekt-CLI
description: Das Azure CLI-Skript in diesem Thema zeigt, wie ein Media Services-Medienobjekt erstellt wird, in das Inhalt hochgeladen werden kann.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3923394e76d5ce4001d3652ae9cbc263df08e4e2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545614"
---
# <a name="create-an-asset"></a>Erstellen eines Medienobjekts

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In diesem Artikel erfahren Sie, wie Sie ein Media Services-Medienobjekt erstellen.  Sie verwenden ein Medienobjekt zum Speichern von Medieninhalten für die Codierung und das Streaming.  Weitere Informationen zu Media Services-Medienobjekten finden Sie unter [Medienobjekte in Azure Media Services v3](assets-concept.md).

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie die Schritte unter [Erstellen eines Media Services-Kontos](./create-account-howto.md) aus, um das erforderliche Media Services-Konto und die erforderliche Ressourcengruppe zum Erstellen eines Medienobjekts zu erstellen.

## <a name="methods"></a>Methoden

## <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>Verwenden von REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>Verwenden von cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>Nächste Schritte

[Media Services: Übersicht](media-services-overview.md)
