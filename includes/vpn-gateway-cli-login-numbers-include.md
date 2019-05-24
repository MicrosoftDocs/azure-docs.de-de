---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 21dbec52dded5a195cc764741ab9e8d79737c549
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121186"
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
