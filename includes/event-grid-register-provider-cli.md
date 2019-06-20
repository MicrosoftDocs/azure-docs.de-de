---
title: include file
description: include file
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ace22a6896a121f5cd8af838c7b0e427bd0287dc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177773"
---
## <a name="enable-event-grid-resource-provider"></a>Aktivieren des Event Grid-Ressourcenanbieters

Wenn Sie zuvor in Ihrem Azure-Abonnement noch nicht Event Grid verwendet haben, sollten Sie den Event Grid-Ressourcenanbieter registrieren. Führen Sie den folgenden Befehl aus, um den Anbieter zu registrieren:

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

Es kann einen Moment dauern, bis die Registrierung abgeschlossen ist. Führen Sie Folgendes aus, um den Status zu überprüfen:

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

Wenn `registrationState` `Registered` ist, können Sie den Vorgang fortsetzen.