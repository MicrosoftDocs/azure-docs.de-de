---
title: include file
description: include file
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 08458bd170707b28c69fdad1d8aa115a7ad245a5
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558181"
---
> [!NOTE]
> Der Befehl `az webapp up` bewirkt Folgendes:
>
>- Erstellen einer [Standardressourcengruppe](/cli/azure/group?view=azure-cli-latest#az-group-create)
>
>- Erstellen eines standardmäßigen [App Service-Plans](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)
>
>- [Erstellen einer App](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) mit dem angegebenen Namen
>
>- [Bereitstellen von ZIP-Dateien](../articles/app-service/deploy-zip.md) aus dem aktuellen Arbeitsverzeichnis für die App
>