---
title: include file
description: include file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f4af8a150b062526f08c1d15581ec26e2fe12d8c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992900"
---
- Inkrementelle Momentaufnahmen können derzeit nicht zwischen Abonnements verschoben werden.
- Sie können zurzeit nur SAS-URIs mit jeweils bis zu fünf Momentaufnahmen einer bestimmten Momentaufnahmefamilie generieren.
- Sie können keine inkrementelle Momentaufnahme für einen bestimmten Datenträger außerhalb des Abonnements dieses Datenträgers erstellen.
- Bis zu sieben inkrementelle Momentaufnahmen pro Datenträger können alle fünf Minuten erstellt werden.
- Für einen einzelnen Datenträger können insgesamt 200 inkrementelle Momentaufnahmen erstellt werden.
- Sie können die Änderungen zwischen erstellten Momentaufnahmen nicht abrufen, wenn zwischen diesen eine Änderung der Größe des übergeordneten Datenträgers vorgenommen wurde, die die Grenze von 4 TB überschreitet. Sie haben beispielsweise eine inkrementelle Momentaufnahme „a“ erstellt, während die Größe eines Datenträgers 2 TB betrug. Nun haben Sie die Größe des Datenträgers auf 6 TB erweitert und dann eine weitere inkrementelle Momentaufnahme „b“ erstellt. Die Änderungen zwischen Momentaufnahme „a“ und Momentaufnahme „b“ können nicht abgerufen werden. Sie müssen die vollständige Kopie der Momentaufnahme „b“ herunterladen, die nach der Größenänderung erstellt wurde. Anschließend können Sie die Änderungen zwischen Momentaufnahme „b“ und später erstellten Momentaufnahmen abrufen. 
