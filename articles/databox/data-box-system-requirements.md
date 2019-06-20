---
title: Systemanforderungen für Microsoft Azure Data Box | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Software- und Netzwerkanforderungen für Ihre Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 3bce5dd1dd34c53276c5486cc255c4cd93bb6080
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242162"
---
# <a name="azure-data-box-system-requirements"></a>Systemanforderungen für Azure Data Box

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box und die Clients beschrieben, die sich mit der Data Box verbinden. Sie sollten die Informationen sorgfältig lesen, bevor Sie Ihre Data Box bereitstellen. Später können Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

* **Softwareanforderungen für Hosts, die sich mit der Data Box verbinden**: Beschreibt die unterstützten Plattformen, Browser für die lokale Webbenutzeroberfläche, SMB-Clients und alle zusätzlichen Anforderungen an Hosts, die sich mit der Data Box verbinden können.
* **Netzwerkanforderungen für die Data Box**: Liefert Informationen zu den Netzwerkanforderungen für einen optimalen Betrieb der Data Box.


## <a name="software-requirements"></a>Softwareanforderungen

Die Softwareanforderungen umfassen Informationen zu den unterstützten Betriebssystemen, unterstützten Browsern für die lokale Webbenutzeroberfläche und SMB-Clients.

### <a name="supported-operating-systems-for-clients"></a>Unterstützte Betriebssysteme für Clients

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Unterstützte Dateisysteme für Linux-Clients

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Unterstützte Speichertypen

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Unterstützte Webbrowser

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Netzwerkanforderungen

Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen. Falls keine 10-GbE-Verbindung verfügbar ist, kann zum Kopieren der Daten eine 1-GbE-Datenverbindung verwendet werden, die Geschwindigkeit der Kopiervorgänge wird dadurch jedoch beeinträchtigt.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen Ihrer Azure Data Box](data-box-deploy-ordered.md)
