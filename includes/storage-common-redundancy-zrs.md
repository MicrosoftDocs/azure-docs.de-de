---
title: Includedatei
description: Includedatei
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66113772"
---
Zonenredundanter Speicher (ZRS) repliziert Ihre Daten synchron über drei Speichercluster in einer Region. Jeder Speichercluster ist physisch unabhängig von den anderen und befindet sich in einer eigenen Verfügbarkeitszone. Jede Verfügbarkeitszone – und der darin enthaltene ZRS-Cluster – ist autonom und enthält separate Hilfsprogramme und Netzwerkfeatures.

Wenn Sie Ihre Daten in einem Speicherkonto mit der ZRS-Replikation speichern, können Sie weiter auf Ihre Daten zugreifen und diese verwalten, falls eine Verfügbarkeitszone nicht mehr verfügbar ist. ZRS bietet eine herausragende Leistung bei geringer Latenz. ZRS ermöglicht die gleichen [Skalierbarkeitsziele](../articles/storage/common/storage-scalability-targets.md) wie [lokal redundanter Speicher (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Erwägen Sie die Verwendung von ZRS für Szenarien, für die Konsistenz, Dauerhaftigkeit und Hochverfügbarkeit erforderlich sind. Auch wenn eine Verfügbarkeitszone aufgrund eines Ausfalls oder einer Naturkatastrophe nicht mehr verfügbar ist, ist bei ZRS für das jeweilige Jahr in Bezug auf Speicherobjekte eine Dauerhaftigkeit von mindestens 99,9999999999% (12 Neunen) sichergestellt.

Weitere Informationen zu Verfügbarkeitszonen finden Sie unter [Übersicht über Verfügbarkeitszonen](https://docs.microsoft.com/azure/availability-zones/az-overview).