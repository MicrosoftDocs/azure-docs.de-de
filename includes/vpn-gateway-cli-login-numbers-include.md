---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 52f828b9ba7bd286184b44a3d843c2cf8c75c592
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755904"
---
1. Melden Sie sich mit dem Befehl [az login](/cli/azure/) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm. Weitere Informationen zum Anmelden finden Sie unter [Erste Schritte mit der Azure CLI](/cli/azure/get-started-with-azure-cli).

   ```azurecli
   az login
   ```
2. Listen Sie die Abonnements für das Konto auf, wenn Sie über mehr als ein Azure-Abonnement verfügen.

   ```azurecli
   az account list --all
   ```
3. Geben Sie das Abonnement an, das Sie verwenden möchten.

   ```azurecli
   az account set --subscription <replace_with_your_subscription_id>
   ```
