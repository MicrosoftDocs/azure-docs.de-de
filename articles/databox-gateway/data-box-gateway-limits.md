---
title: Für Azure Data Box Gateway geltende Einschränkungen | Microsoft-Dokumentation
description: Beschreibt für das System geltende Einschränkungen und empfohlene Größen für Microsoft Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581028"
---
# <a name="azure-data-box-gateway-limits"></a>Für Azure Data Box Gateway geltende Einschränkungen

Berücksichtigen Sie die folgenden Einschränkungen beim Bereitstellen und Betreiben Ihrer Microsoft Azure Data Box Gateway-Lösung.

## <a name="data-box-gateway-service-limits"></a>Für den Data Box Gateway-Dienst geltende Einschränkungen

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Für das Data Box Gateway-Gerät geltende Einschränkungen

In der folgenden Tabelle werden die für das Data Box Gateway-Gerät geltenden Einschränkungen beschrieben.

| BESCHREIBUNG | Wert |
|---|---|
|Nein. der Dateien pro Gerät |100 Mio. <br> Für jeweils 25 Millionen hinzugefügte Dateien (maximales Limit 100 Millionen) sollten Sie 2 TB Speicherplatz auf dem Datenträger, 8 GB RAM und 4 CPU-Kerne hinzufügen. |
|Nein. der Freigaben pro Gerät |24 |
|Nein. von Freigaben pro Azure-Speichercontainer |1 |
|Maximale in eine Freigabe geschriebene Dateigröße|Für ein virtuelles Gerät mit 2 TB beträgt die maximale Dateigröße 500 GB. <br> Die maximale Dateigröße steigt mit der Größe des Datenträgers im vorhergehenden Verhältnis an, bis ein Maximum von 5 TB erreicht wird. |

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto und Objekte

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
