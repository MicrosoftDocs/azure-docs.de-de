---
title: include file
description: include file
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 11/03/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2b7881e7fa1ccbcec1325b2af0a570c86b0585a8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017489"
---
Wenn Sie die Beispielgeräteanwendung später in diesem Tutorial ausführen, benötigen Sie die folgenden Konfigurationswerte:

* ID-Bereich: Navigieren Sie in Ihrer IoT Central-Anwendung zu **Verwaltung > Geräteverbindung**. Notieren Sie sich den Wert für **ID-Bereich**.
* Gruppenprimärschlüssel: Navigieren Sie in Ihrer IoT Central-Anwendung zu **Verwaltung > Geräteverbindung > SAS-IoT-Devices**. Notieren Sie sich den Wert für den SAS-**Primärschlüssel** (Shared Access Signature).

Verwenden Sie Cloud Shell, um einen Geräteschlüssel auf der Grundlage des soeben von Ihnen abgerufenen SAS-Gruppenschlüssels zu generieren:

```azurecli-interactive
az extension add --name azure-iot
az iot central device compute-device-key  --device-id sample-device-01 --pk <the group SAS primary key value>
```

Notieren Sie sich den generierten Geräteschlüssel. Sie verwenden ihn später in diesem Tutorial.
