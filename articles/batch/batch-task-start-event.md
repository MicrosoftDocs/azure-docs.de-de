---
title: 'Azure Batch: Taskstartereignis | Microsoft-Dokumentation'
description: Referenz zum Batch-Taskstartereignis.
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
ms.openlocfilehash: d50a0a7082e409084fd966370934a638ca9bb013
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60549868"
---
# <a name="task-start-event"></a>Taskstartereignis

 Dieses Ereignis wird einmal ausgegeben, nachdem ein Task vom Scheduler für den Start auf einem Computeknoten geplant wurde. Wenn dieser Task wiederholt oder erneut in die Warteschlange gestellt wird, wird dieses Ereignis erneut für denselben Task ausgegeben, doch der Wiederholungszähler und die Systemtaskversion werden entsprechend aktualisiert.


 Das folgende Beispiel zeigt den Text eines Taskstartereignisses.

```
{
    "jobId": "job-0000000001",
    "id": "task-5",
    "taskType": "User",
    "systemTaskVersion": 0,
    "nodeInfo": {
        "poolId": "pool-001",
        "nodeId": "tvm-257509324_1-20160908t162728z"
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 2
    },
    "executionInfo": {
        "retryCount": 0
    }
}
```

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|jobId|string|Die ID des Auftrags, der den Task enthält.|
|id|string|Die ID des Tasks.|
|taskType|string|Der Typ des Tasks. Entweder „JobManager“, was bedeutet, dass dies ein Auftrags-Manager-Task ist, oder „User“, was bedeutet, dass dies nicht der Fall ist.|
|systemTaskVersion|Int32|Dies ist der interne Wiederholungszähler für einen Task. Der Batch-Dienst kann intern einen Task wiederholen, um vorübergehende Probleme zu berücksichtigen. Bei diesen Problemen kann es sich um interne Planungsfehler oder Versuche handeln, Computeknoten mit einem fehlerhaften Status wiederherzustellen.|
|[nodeInfo](#nodeInfo)|Komplexer Typ|Enthält Informationen zu den Computeknoten, auf dem der Task ausgeführt wurde.|
|[multiInstanceSettings](#multiInstanceSettings)|Komplexer Typ|Gibt an, dass der Task ein Task mit mehreren Instanzen ist, für den mehrere Computeknoten erforderlich sind.  Details finden Sie unter [multiInstanceSettings](https://docs.microsoft.com/rest/api/batchservice/get-information-about-a-task).|
|[constraints](#constraints)|Komplexer Typ|Die Ausführungseinschränkungen, die für diesen Task gelten.|
|[executionInfo](#executionInfo)|Komplexer Typ|Enthält Informationen zur Ausführung des Tasks.|

###  <a name="nodeInfo"></a> nodeInfo

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|poolId|string|Die ID des Pools, auf den der Task angewendet wurde.|
|nodeId|string|Die ID des Knotens, auf dem der Task ausgeführt wurde.|

###  <a name="multiInstanceSettings"></a> multiInstanceSettings

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|numberOfInstances|Int|Die Anzahl der Computeknoten, die vom Task benötigt werden.|

###  <a name="constraints"></a> Einschränkungen

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|maxTaskRetryCount|Int32|Gibt an, wie oft der Task maximal wiederholt werden kann. Der Batch-Dienst wiederholt einen Task, wenn sein Exitcode ungleich null ist.<br /><br /> Beachten Sie, dass dieser Wert die Anzahl der Wiederholungen ausdrücklich steuert. Der Batch-Dienst wiederholt den Task einmal und kann ihn anschließend bis zu diesem Grenzwert wiederholen. Wenn beispielsweise die maximale Anzahl von Wiederholungsversuchen 3 ist, versucht der Batch-Dienst einen Task bis zu viermal (ein erster Versuch und drei Wiederholungsversuche).<br /><br /> Wenn die maximale Anzahl von Wiederholungsversuchen 0 ist, wiederholt der Batch-Dienst Tasks nicht.<br /><br /> Wenn die maximale Anzahl von Wiederholungsversuchen -1 ist, wiederholt der Batch-Dienst Tasks unbegrenzt.<br /><br /> Der Standardwert ist 0 (keine Wiederholungsversuche).|

###  <a name="executionInfo"></a> executionInfo

|Elementname|Type|Notizen|
|------------------|----------|-----------|
|retryCount|Int32|Die Häufigkeit, mit der der Task vom Batch-Dienst wiederholt wurde. Der Vorgang wird wiederholt, wenn der Exitcode ungleich null ist, und zwar bis zum angegebenen Wert von „MaxTaskRetryCount“.|
