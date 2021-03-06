---
title: 'Azure CLI-Skriptbeispiel: Erstellen einer Transformation'
description: Transformationen beschreiben einen einfachen Workflow von Aufgaben zur Verarbeitung Ihrer Video- oder Audiodateien (der häufig als „Rezept“ bezeichnet wird). Das Azure CLI-Skript in diesem Artikel zeigt, wie eine Transformation erstellt wird.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 75b45067be49475ecd2e07aed9c4479b147fdd29
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490266"
---
# <a name="create-a-transform"></a>Erstellen einer Transformation

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Das Azure CLI-Skript in diesem Artikel zeigt, wie eine Transformation erstellt wird. Transformationen beschreiben einen einfachen Workflow von Aufgaben zur Verarbeitung Ihrer Video- oder Audiodateien (der häufig als „Rezept“ bezeichnet wird). Sie müssen immer überprüfen, ob eine Transformation mit dem gewünschten Namen und „Rezept“ bereits vorhanden ist. Wenn dies der Fall ist, sollten Sie diese erneut verwenden.

## <a name="prerequisites"></a>Voraussetzungen

[Erstellen Sie ein Media Services-Konto.](./account-create-how-to.md)

## <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli/)

> [!NOTE]
> Sie können nur einen Pfad zur benutzerdefinierten JSON-Datei mit der Standard-Encoder-Voreinstellung für [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) angeben. Sehen Sie sich das Beispiel unter [Codieren mit einer benutzerdefinierten Transformation: CLI](transform-custom-preset-cli-how-to.md) an.
>
> Bei der Verwendung von [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) können Sie keinen Dateinamen übergeben.

## <a name="example-script"></a>Beispielskript

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-transform/Create-Transform.sh "Create a transform")]

## <a name="rest"></a>[REST](#tab/rest/)

[!INCLUDE [task general transform creation](./includes/task-create-basic-audio-rest.md)]

---

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
