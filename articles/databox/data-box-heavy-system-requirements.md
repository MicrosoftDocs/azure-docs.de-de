---
title: Systemanforderungen für Microsoft Azure Data Box Heavy | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Software- und Netzwerkanforderungen für Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427738"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Systemanforderungen für Azure Data Box Heavy (Vorschau)

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box Heavy und die Clients beschrieben, die sich mit dem Gerät verbinden. Sie sollten die Informationen sorgfältig lesen, bevor Sie Ihre Data Box Heavy bereitstellen. Später können Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen.

Systemanforderungen:

* **Softwareanforderungen für Hosts, die sich mit der Data Box Heavy verbinden**: Beschreibt die unterstützten Plattformen, Browser für die lokale Webbenutzeroberfläche, SMB-Clients und alle zusätzlichen Anforderungen an Hosts, die sich mit der Data Box verbinden können.
* **Netzwerkanforderungen für Data Box Heavy**: Enthält Informationen zu den Netzwerkanforderungen für einen optimalen Betrieb der Data Box Heavy.

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

Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Zur Erzielung der höchstmöglichen Kopiergeschwindigkeit können zwei 40-GbE-Verbindungen (jeweils eine pro Knoten) parallel genutzt werden. Falls Sie über keine 40-GbE-Verbindung verfügen, sollten Sie mindestens zwei Verbindungen mit 10 GbE verwenden (jeweils eine pro Knoten).

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen Ihrer Azure Data Box](data-box-deploy-ordered.md)
