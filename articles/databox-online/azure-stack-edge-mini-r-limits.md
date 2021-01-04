---
title: Grenzwerte für Azure Stack Edge Mini R | Microsoft-Dokumentation
description: Beschreibt für das System geltende Grenzwerte und empfohlene Größen für das Azure Stack Edge Mini R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465745"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Grenzwerte für Azure Stack Edge Mini R


Berücksichtigen Sie die folgenden Grenzwerte beim Bereitstellen und Betreiben Ihrer Azure Stack Edge Mini R-Lösung.

## <a name="azure-stack-edge-service-limits"></a>Grenzwerte für den Azure Stack Edge-Dienst

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Gerätelimits für Azure Stack Edge Mini R

In der folgenden Tabelle sind die für Azure Stack Edge Mini R-Geräte geltenden Grenzwerte aufgeführt.

| BESCHREIBUNG | Begrenzung|
|---|---:|
|Nein. der Dateien pro Gerät | 100 Mio. <!--check with devs-->|
|Nein. Freigaben pro Container | 1|
|Maximale Anzahl von Freigabeendpunkten und REST-Endpunkten pro Gerät| 24 |
|Maximale Anzahl von mehrstufigen Speicherkonten pro Gerät| 24|
|Maximale in eine Freigabe geschriebene Dateigröße| 500 GB|
|Maximale Anzahl von Ressourcengruppen pro Gerät| 800|

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto und Objekte

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md)
