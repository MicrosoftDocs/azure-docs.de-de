---
title: include file
description: include file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518058"
---
In diesem Artikel wird die Leistung von Datenträgern verdeutlicht und beschrieben, wie Sie Azure Virtual Machines und Azure-Datenträger kombinieren. Es wird auch beschrieben, wie Sie Engpässe für Ihre Datenträger-E/A diagnostizieren können und welche Änderungen Sie zur Leistungsoptimierung vornehmen können.

## <a name="how-does-disk-performance-work"></a>Wie funktioniert die Datenträgerleistung?
Virtuelle Azure-Computer weisen Leistungsgrenzwerte für E/A-Vorgänge pro Sekunde (Disk Input/Output Operations per Second, IOPS) und Durchsatz auf, die auf dem Typ und der Größe des virtuellen Computers basieren. Betriebssystemdatenträger und reguläre Datenträger können an virtuelle Computer angefügt werden. Die Datenträger verfügen über eigene Grenzwerte für IOPS und Durchsatz.

Die Leistung Ihrer Anwendung wird begrenzt, wenn sie eine höhere IOPS- oder Durchsatzmenge anfordert, als für die virtuellen Computer oder angefügten Datenträger zugeordnet ist. Bei einer Begrenzung ist die Leistung der Anwendung nicht optimal. Dies kann negative Auswirkungen haben, z. B. eine höhere Latenz. Wir gehen nun einige Beispiele durch, um dieses Konzept zu verdeutlichen. Es geht nur um IOPS, um die Beispiele einfach zu halten. Dieselbe Logik gilt aber auch für den Durchsatz.

## <a name="disk-io-capping"></a>Obergrenzen der Datenträger-E/A

**Setup:**

- Standard_D8s_v3
  - Nicht zwischengespeicherte IOPS: 12.800
- E30-Betriebssystemdatenträger
  - IOPS: 500
- Zwei E30-Datenträger × 2
  - IOPS: 500

![Diagramm: Begrenzung für Datenträgerebene](media/vm-disk-performance/disk-level-throttling.jpg)

Die auf dem virtuellen Computer ausgeführte Anwendung stellt eine Anforderung, für die 10.000 IOPS für den virtuellen Computer erforderlich sind. Alle Vorgänge sind für den virtuellen Computer zulässig, da er den Typ „Standard_D8s_v3“ aufweist und bis zu 12.800 IOPS ausführen kann.

Die 10.000 IOPS-Anforderungen werden in drei verschiedene Anforderungen für die verschiedenen Datenträger unterteilt:

- 1\.000 IOPS werden vom Betriebssystemdatenträger angefordert.
- 4\.500 IOPS werden von jedem regulären Datenträger angefordert.

Bei allen angefügten Datenträgern handelt es sich um E30-Datenträger, die nur 500 IOPS verarbeiten können. Sie antworten daher jeweils mit 500 IOPS. Die Leistung der Anwendung wird durch die angeschlossenen Datenträger begrenzt, und es können nur 1.500 IOPS verarbeitet werden. Für die Anwendung können bei höchster Leistung 10.000 IOPS erzielt werden, wenn leistungsstärkere Datenträger verwendet werden, z. B. P30-Datenträger vom Typ „SSD Premium“.

## <a name="virtual-machine-io-capping"></a>Obergrenzen der E/A der virtuellen Computer

**Setup:**

- Standard_D8s_v3
  - Nicht zwischengespeicherte IOPS: 12.800
- P30-Betriebssystemdatenträger
  - IOPS: 5.000
- Zwei P30-Datenträger × 2
  - IOPS: 5.000

![Diagramm: Obergrenzen der VM-Ebene](media/vm-disk-performance/vm-level-throttling.jpg)

Die auf dem virtuellen Computer ausgeführte Anwendung sendet eine Anforderung, für die 15.000 IOPS erforderlich sind. Leider ist der virtuelle Computer vom Typ „Standard_D8s_v3“ nur für die Verarbeitung von 12.800 IOPS vorgesehen. Die Anwendung ist durch die VM-Grenzwerte begrenzt und muss die zugeteilten 12.800 IOPS zuordnen.

Diese angeforderten 12.800 IOPS werden in drei verschiedene Anforderungen für die verschiedenen Datenträger unterteilt:

- 4\.267 IOPS werden vom Betriebssystemdatenträger angefordert.
- 4\.266 IOPS werden von jedem regulären Datenträger angefordert.

Bei allen angefügten Datenträgern handelt es sich um P30-Datenträger, die 5.000 IOPS verarbeiten können. Sie antworten also mit den angeforderten Mengen.
