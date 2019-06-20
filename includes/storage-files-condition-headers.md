---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/17/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0486b595bffd18b06d54e8377b24deab04e2aa93
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178159"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>Fehler „ConditionHeadersNotSupported“ bei einer Webanwendung, die Azure Files über Browser verwendet

Wenn versucht wird, über eine Anwendung, die bedingte Header verwendet (z.B. ein Webbrowser), auf in Azure Files gehostete Inhalte zuzugreifen, schlägt der Zugriff fehl, und der Fehler „ConditionHeadersNotSupported“ wird angezeigt.

![Fehler „ConditionHeadersNotSupported“](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>Ursache

Bedingte Header werden noch nicht unterstützt. Anwendungen, die diese Header implementieren, müssen die vollständige Datei jedes Mal anfordern, wenn sie darauf zugreifen.

### <a name="workaround"></a>Problemumgehung

Wenn eine neue Datei hochgeladen wird, lautet der Wert für die Eigenschaft „cache-control“ standardmäßig „no-cache“. Um zu erzwingen, dass die Anwendung die Datei jedes Mal anfordert, muss der Wert für die Eigenschaft „cache-control“ der Datei von „no-cache“ auf „no-cache, no-store, must-revalidate“ aktualisiert werden. Dies kann über [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) geschehen.

![Storage-Explorer-Inhalt – Änderung bei „cache“](media/storage-files-condition-headers/storage-explorer-cache.png)