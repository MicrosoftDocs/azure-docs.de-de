---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/16/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 3076065d23d4701711c0ecb60349e77ee5c9b7c8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178306"
---
Azure Storage unterstützt die Verschlüsselung ruhender Daten mit von Microsoft verwalteten Schlüsseln oder vom Kunden verwalteten Schlüsseln. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.

Verwenden Sie den Azure Key Vault, um Ihre Schlüssel zu verwalten und die Schlüsselnutzung zu überwachen. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. Das Speicherkonto und der Schlüsseltresor müssen sich in derselben Region befinden, dürfen aber zu verschiedenen Abonnements gehören. Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../articles/key-vault/key-vault-overview.md)
