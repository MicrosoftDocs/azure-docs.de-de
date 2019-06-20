---
title: Für Azure Data Box Edge geltende Einschränkungen | Microsoft-Dokumentation
description: Beschreibt für das System geltende Einschränkungen und empfohlene Größen für Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: b454b563cdb870ca8f07a45b796dc6b1e272502d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924597"
---
# <a name="azure-data-box-edge-limits"></a>Für Azure Data Box Edge geltende Einschränkungen

Berücksichtigen Sie die folgenden Einschränkungen beim Bereitstellen und Betreiben Ihrer Microsoft Azure Data Box Edge-Lösung.

## <a name="data-box-edge-service-limits"></a>Einschränkungen für den Data Box Edge-Dienst

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-edge-device-limits"></a>Einschränkungen für Data Box Edge-Geräte

In der folgenden Tabelle werden die für ein Data Box Edge-Gerät geltenden Einschränkungen beschrieben.

| BESCHREIBUNG | Wert |
|---|---|
|Nein. der Dateien pro Gerät |100 Mio. |
|Nein. der Freigaben pro Gerät |24 |
|Nein. Freigaben pro Container |1 |
|Maximale in eine Freigabe geschriebene Dateigröße| 5 TB |

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto und Objekte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
