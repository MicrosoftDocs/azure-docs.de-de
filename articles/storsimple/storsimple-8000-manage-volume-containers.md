---
title: Verwalten von Volumecontainern für Geräte der StorSimple 8000-Serie
description: Beschreibt, wie Sie die Seite „Volumecontainer“ des StorSimple-Geräte-Manager-Diensts zum Hinzufügen, Ändern oder Löschen eines Volumecontainers verwenden können.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545281"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Verwalten von StorSimple-Volumecontainern mithilfe des StorSimple-Geräte-Manager-Diensts

## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie Sie den StorSimple-Geräte-Manager-Dienst zum Erstellen und Verwalten von StorSimple-Volumecontainern verwenden.

Ein Volumecontainer in einem Microsoft Azure StorSimple-Gerät enthält ein oder mehrere Volumes, die gemeinsame Einstellungen für das Speicherkonto, die Verschlüsselung und die Bandbreitenauslastung haben. Ein Gerät kann mehrere Volumecontainer für alle Volumes aufweisen. 

Ein Volumecontainer verfügt über die folgenden Attribute:

* **Volumes** : Die mehrstufigen oder lokalen StorSimple-Volumes, die im Volumecontainer enthalten sind. 
* **Verschlüsselung** – Ein Verschlüsselungsschlüssel, der für jeden Volumecontainer definiert werden kann. Mit diesem Schlüssel werden die vom StorSimple-Gerät an die Cloud gesendeten Daten verschlüsselt. Zusammen mit dem vom Benutzer eingegebenen Schlüssel wird ein AES-256-Bit-Schlüssel (gemäß militärischen Sicherheitsstandards) verwendet. Zum Schutz Ihrer Daten wird empfohlen, immer die Cloudspeicherverschlüsselung zu aktivieren.
* **Speicherkonto** – Das Azure-Speicherkonto, das zum Speichern der Daten verwendet wird. Alle Volumes in einem Volumecontainer nutzen dieses Speicherkonto gemeinsam. Sie können ein Speicherkonto aus einer vorhandenen Liste auswählen oder beim Erstellen des Volumecontainers ein neues Konto erstellen und dann die Anmeldeinformationen für das Konto angeben.
* **Cloudbandbreite** – Die vom Gerät beanspruchte Bandbreite, wenn Daten vom Gerät an die Cloud gesendet werden. Sie können die Bandbreite steuern, indem Sie beim Erstellen dieses Containers einen Wert von 1-1.000 MBit/s angeben. Wenn das Gerät die gesamte verfügbare Bandbreite nutzen soll, legen Sie dieses Feld auf **Unbegrenzt** fest. Sie können auch eine Bandbreitenvorlage erstellen und anwenden, um Bandbreite basierend auf einem Zeitplan zuzuweisen.

In den folgenden Verfahren wird erläutert, wie Sie das Blatt **Volumecontainer** verwenden, um die folgenden allgemeinen Vorgänge durchzuführen:

* Hinzufügen eines Volumecontainers
* Ändern eines Volumecontainers
* Löschen eines Volumecontainers

## <a name="add-a-volume-container"></a>Hinzufügen eines Volumecontainers
Führen Sie die folgenden Schritte aus, um einen Volumecontainer hinzuzufügen.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Ändern eines Volumecontainers
Führen Sie die folgenden Schritte aus, um einen Volumecontainer zu ändern.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Löschen eines Volumecontainers
In einem Volumecontainer sind Volumes enthalten. Er kann nur gelöscht werden, nachdem alle darin enthaltenen Volumes gelöscht wurden. Führen Sie die folgenden Schritte aus, um einen Volumecontainer zu löschen.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwalten von StorSimple-Volumes](storsimple-8000-manage-volumes-u2.md). 
* Erfahren Sie mehr [Verwalten von StorSimple-Geräten mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md).

