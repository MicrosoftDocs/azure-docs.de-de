---
title: Includedatei
description: Includedatei
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162720"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Die Daten können entweder im [Apache Avro](https://avro.apache.org/)-Format (Standardeinstellung) oder im JSON-Format (Vorschauversion) in den Blobspeicher geschrieben werden. 
>    
> Die Funktion zum Codieren im JSON-Format befindet sich in allen Regionen, in denen IoT Hub verfügbar ist, in der Vorschauphase (mit Ausnahme von „USA, Osten“ und „Europa, Westen“). Das Codierungsformat kann nur beim Konfigurieren des Blob Storage-Endpunkt festgelegt werden. Für bereits eingerichtete Endpunkte kann das Format nicht mehr geändert werden. Bei Verwendung der JSON-Codierung müssen Sie in den Nachrichtensystemeigenschaften den Inhaltstyp (contentType) auf „JSON“ und die Inhaltscodierung (contentEncoding) auf „UTF-8“ festlegen. 
>
> Ausführlichere Informationen zur Verwendung eines Blob Storage-Endpunkts finden Sie unter [Azure Blob Storage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>