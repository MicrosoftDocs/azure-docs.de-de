---
title: include file
description: include file
services: batch
author: dlepow
ms.service: batch
ms.topic: include
ms.date: 05/04/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b5bb5da2b68bae36353b81a04993814b395ce67b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178445"
---
> [!NOTE]
> Beim Erstellen eines Batchkontos können Sie zwischen zwei Modi der *Poolzuordnung* wählen: **Benutzerabonnement** und **Batchdienst**. In den meisten Fällen sollten Sie den standardmäßigen Batchdienstmodus wählen, in dem Pools im Hintergrund von Azure verwalteten Abonnements zugeordnet werden. Im alternativen Benutzerabonnementmodus werden virtuelle Batchcomputer und andere Ressourcen direkt in Ihrem Abonnement erstellt, wenn ein Pool erstellt wird. Der Benutzerabonnementmodus ist erforderlich, wenn Sie mithilfe von [reservierten Azure VM-Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) Batchpools erstellen möchten. Wenn Sie ein Batch-Konto im Benutzerabonnementmodus erstellen möchten, müssen Sie auch Ihr Abonnement in Azure Batch registrieren und das Konto einer Azure Key Vault-Instanz zuordnen.