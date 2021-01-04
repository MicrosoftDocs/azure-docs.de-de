---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/30/2019
ms.author: alkohli
ms.openlocfilehash: 87c28b906b1f810857fee86ba8a94a0834e1de68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028256"
---
In diesem Abschnitt werden die Grenzwerte für den Azure Storage-Dienst und die erforderlichen Benennungskonventionen für Azure Files, Azure-Blockblobs und Azure-Seitenblobs beschrieben, sofern für den Azure Stack Edge-/Data Box Gateway-Dienst zutreffend. Machen Sie sich sorgfältig mit den Speichergrenzwerten vertraut, und beachten Sie alle Empfehlungen.

Aktuelle Informationen zu Grenzwerten für den Azure Storage-Dienst und bewährte Vorgehensweisen für die Benennung von Freigaben, Containern und Dateien finden Sie hier:

- [Benennen und Referenzieren von Containern, Blobs und Metadaten](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Verstehen von Blockblobs, Anfügeblobs und Seitenblobs](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Wenn Dateien oder Verzeichnisse die Grenzwerte des Azure Storage-Diensts überschreiten oder den Benennungskonventionen für Azure Files/-Blobs nicht entsprechen, werden diese Dateien oder Verzeichnisse über den Azure Stack Edge-/Data Box Gateway-Dienst nicht in Azure Storage erfasst.