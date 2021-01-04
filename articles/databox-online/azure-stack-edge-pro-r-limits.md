---
title: Grenzwerte für Azure Stack Edge Pro R | Microsoft-Dokumentation
description: Beschreibt für das System geltende Grenzwerte und empfohlene Größen für das Azure Stack Edge Pro R.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465680"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Grenzwerte für Azure Stack Edge Pro R

Berücksichtigen Sie die folgenden Grenzwerte beim Bereitstellen und Betreiben Ihrer Azure Stack Edge Pro R-Lösung.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Grenzwerte für den Azure Stack Edge Pro R-Dienst

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Gerätelimits für Azure Stack Edge Pro R

In der folgenden Tabelle sind die für Azure Stack Edge Pro R-Geräte geltenden Grenzwerte aufgeführt.

| BESCHREIBUNG | Wert |
|---|---|
|Nein. der Dateien pro Gerät |100 Mio. |
|Nein. Freigaben pro Container |1 |
|Maximale Anzahl von Freigabeendpunkten und REST-Endpunkten pro Gerät| 24 |
|Maximale Anzahl von mehrstufigen Speicherkonten pro Gerät| 24|
|Maximale in eine Freigabe geschriebene Dateigröße| 5 TB |
|Maximale Anzahl von Ressourcengruppen pro Gerät| 800 |

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto und Objekte

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md)
