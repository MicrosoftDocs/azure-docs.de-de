---
title: Dienstebenen für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt die Durchsatzleistung für die Dienstebenen von Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: b-juche
ms.openlocfilehash: 1f9c427045c9d42f6a11cc4bcc798cfc47a4428c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65523107"
---
# <a name="service-levels-for-azure-netapp-files"></a>Dienstebenen für Azure NetApp Files
Dienstebenen sind ein Attribut eines Kapazitätspools. Dienstebenen werden vom maximal zulässigen Durchsatz für ein Volume im Kapazitätspool auf Grundlage des Kontingents, das dem Volumen zugewiesen ist, bestimmt und unterschieden.

## <a name="supported-service-levels"></a>Unterstützte Dienstebenen

Azure NetApp Files unterstützt drei Dienstebenen: *Ultra*, *Premium* und *Standard*. 

* <a name="Ultra"></a>Ultra-Speicher

    Die Speicherebene „Ultra“ stellt bis zu 128 MiB/s an Durchsatz pro 1 TiB zugewiesenem Volumekontingent zur Verfügung. 

* <a name="Premium"></a>Storage Premium

    Die Speicherebene „Premium“ stellt bis zu 64 MiB/s an Durchsatz pro 1 TiB zugewiesenem Volumekontingent zur Verfügung. 

* <a name="Standard"></a>Storage Standard

    Die Speicherebene „Standard“ stellt bis zu 16 MiB/s an Durchsatz pro 1 TiB zugewiesenem Volumekontingent zur Verfügung.

## <a name="throughput-limits"></a>Durchsatzlimits

Die Durchsatzbegrenzung für ein Volume wird von einer Kombination der folgenden Faktoren bestimmt:
* Die Dienstebene des Kapazitätspools, zu dem das Volume gehört
* Das Kontingent, das dem Volume zugewiesen ist  

Dieses Konzept ist in der untenstehenden Abbildung dargestellt:

![Darstellung einer Dienstebene](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

Im Beispiel 1 oben wird einem Volume aus einem Kapazitätspool mit der Speicherebene „Premium“, der ein 2 TiB-Kontingent zugewiesen ist, eine Durchsatzbegrenzung von 128 MiB/s (2 TiB × 64 MiB/s) zugewiesen. Dieses Szenario gilt unabhängig von der Größe des Kapazitätspools oder dem tatsächlichen Volumeverbrauch.

Im Beispiel 2 oben wird einem Volume aus einem Kapazitätspool mit der Speicherebene „Premium“, der ein 100 GiB-Kontingent zugewiesen ist, eine Durchsatzbegrenzung von 6.25 MiB/s (0.09765625 TiB × 64 MiB/s) zugewiesen. Dieses Szenario gilt unabhängig von der Größe des Kapazitätspools oder dem tatsächlichen Volumeverbrauch.

## <a name="next-steps"></a>Nächste Schritte

- Die Preise der verschiedenen Dienstebenen finden Sie unter [Preise für Azure NetApp Files (Vorschauversion)](https://azure.microsoft.com/pricing/details/storage/netapp/).
- Weitere Informationen zur Berechnung des Kapazitätsverbrauchs in einem Kapazitätspool finden Sie unter [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md). 
- [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)