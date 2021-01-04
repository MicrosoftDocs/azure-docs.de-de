---
title: include file
description: include file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 4a879c4041fe317955a07eda9dd8a3ef9f542275
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998833"
---
Sie können die folgenden Parametertypen für das auslösende Blob verwenden:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Erfordert die inout-Bindung `direction` in *function.json* oder `FileAccess.ReadWrite` in einer C#-Klassenbibliothek.

Wenn Sie versuchen, eine Bindung an einen der Storage SDK-Typen herzustellen, und eine Fehlermeldung erhalten, stellen Sie sicher, dass ein Verweis auf [die richtige Storage SDK-Version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x) vorliegt.

Eine Bindung mit `string` oder `Byte[]` wird nur bei kleinen Blobs empfohlen, da der gesamte Blobinhalt in den Arbeitsspeicher geladen wird. Im Allgemeinen ist es günstiger, die Typen `Stream` oder `CloudBlockBlob` zu verwenden. Weitere Informationen finden Sie unter [Nebenläufigkeit und Arbeitsspeichernutzung](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) weiter unten in diesem Artikel.