---
title: 'Azure Batch: Ereignis zum Abschluss des Löschvorgangs von Pools | Microsoft-Dokumentation'
description: Referenz zum Batch-Ereignis zum Abschluss des Löschvorgangs von Pools.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60775762"
---
# <a name="pool-delete-complete-event"></a>Ereignis zum Abschluss des Löschvorgangs von Pools

 Dieses Ereignis wird ausgegeben, wenn ein Löschvorgang eines Pools abgeschlossen wurde.

 Das folgende Beispiel zeigt den Text eines Ereignisses zum Abschluss des Löschvorgangs von Pools.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Element|Typ|Notizen|
|-------------|----------|-----------|
|id|string|Die ID des Pools.|
|startTime|DateTime|Die Startzeit des Löschvorgangs des Pools.|
|endTime|DateTime|Die Endzeit des Löschvorgangs des Pools.|

## <a name="remarks"></a>Anmerkungen
Weitere Informationen zu Status und Fehlercodes für den Löschvorgang des Pools finden Sie unter [Löschen eines Pools aus einem Konto](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).