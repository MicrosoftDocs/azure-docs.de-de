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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017065"
---
# <a name="service-levels-for-azure-netapp-files"></a>Dienstebenen für Azure NetApp Files
Dienstebenen sind ein Attribut eines Kapazitätspools. Dienstebenen werden vom maximal zulässigen Durchsatz für ein Volume im Kapazitätspool auf Grundlage des Kontingents, das dem Volumen zugewiesen ist, bestimmt und unterschieden.

## <a name="supported-service-levels"></a>Unterstützte Dienstebenen

Azure NetApp Files unterstützt drei Dienstebenen: *Ultra*, *Premium* und *Standard*. 

* <a name="Ultra"></a>Ultra-Speicher

    Die Speicherebene „Ultra“ stellt einen Durchsatz von bis zu 128 MiB/s pro 1 TiB bereitgestellter Kapazität zur Verfügung. 

* <a name="Premium"></a>Storage Premium

    Die Speicherebene „Premium“ stellt einen Durchsatz von bis zu 64 MiB/s pro 1 TiB bereitgestellter Kapazität zur Verfügung. 

* <a name="Standard"></a>Storage Standard

    Die Speicherebene „Standard“ stellt einen Durchsatz von bis zu 16 MiB/s pro 1 TiB bereitgestellter Kapazität zur Verfügung.

## <a name="throughput-limits"></a>Durchsatzlimits

Die Durchsatzbegrenzung für ein Volume wird von einer Kombination der folgenden Faktoren bestimmt:
* Die Dienstebene des Kapazitätspools, zu dem das Volume gehört
* Das Kontingent, das dem Volume zugewiesen ist  
* Dem QoS-Typ (*Auto* oder *Manuell*) des Kapazitätspools  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Beispiele zur Durchsatzbegrenzung für Volumes in einem automatischen QoS-Kapazitätspool

Das folgende Diagramm zeigt Beispiele zur Durchsatzbegrenzung für Volumes in einem automatischen QoS-Kapazitätspool:

![Darstellung einer Dienstebene](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* Im Beispiel 1 wird einem Volume von einem automatischen QoS aus einem Kapazitätspool mit der Speicherebene „Premium“, der ein 2 TiB-Kontingent zugewiesen ist, eine Durchsatzbegrenzung von 128 MiB/s (2 TiB × 64 MiB/s) zugewiesen. Dieses Szenario gilt unabhängig von der Größe des Kapazitätspools oder dem tatsächlichen Volumeverbrauch.

* Im Beispiel 2 wird einem Volume von einem automatischen QoS aus einem Kapazitätspool mit der Speicherebene „Premium“, der ein 100 GiB-Kontingent zugewiesen ist, eine Durchsatzbegrenzung von 6,25 MiB/s (0,09765625 TiB × 64 MiB/s) zugewiesen. Dieses Szenario gilt unabhängig von der Größe des Kapazitätspools oder dem tatsächlichen Volumeverbrauch.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Beispiele zur Durchsatzbegrenzung für Volumes in einem manuellen QoS-Kapazitätspool 

Wenn Sie Kapazitätspool mit dem QoS-Typ „Manuell“ verwenden, können Sie die Kapazität und den Durchsatz für ein Volume separat zuweisen. Wenn Sie ein Volume in einem manuellen QoS-Kapazitätspool erstellen, können Sie den Durchsatzwert (MiB/s) angeben. Der Gesamtdurchsatz, der Volumes in einem manuellen QoS-Kapazitätspool zugewiesen wird, richtet sich nach der Größe des Pools und dem Servicelevel. Er ist begrenzt durch (Größe des Kapazitätspools in TiB x Servicelevel-Durchsatz/TiB). Ein Kapazitätspool mit 10 TiB und dem Servicelevel „Ultra“ verfügt beispielsweise über eine Gesamtdurchsatzkapazität von 1280 MiB/s (10 TiB x 128 MiB/s/TiB), die für die Volumes verfügbar ist.

Für ein SAP HANA-System kann dieser Kapazitätspool beispielsweise zum Erstellen der folgenden Volumes verwendet werden. Jedes Volume stellt die individuelle Größe und den individuellen Durchsatz bereit, um die Anforderungen Ihrer Anwendung zu erfüllen:

* SAP HANA-Datenvolume: Größe 4 TiB mit bis zu 704 MiB/s
* SAP HANA-Protokollvolume: Größe 0,5 TiB mit bis zu 256 MiB/s
* SAP HANA-Freigabevolume: Größe 1 TiB mit bis zu 64 MiB/s
* SAP HANA-Sicherungsvolume: Größe 4,5 TiB mit bis zu 256 MiB/s

Das folgende Diagramm veranschaulicht die Szenarien für die SAP HANA-Volumes:

![Szenarien für SAP HANA-QoS-Volumes](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Nächste Schritte

- [Seite mit den Preisen von Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md) 
- [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
- [Vereinbarung zum Servicelevel (SLA) für Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md) 
- [Servicelevelziele für regionsübergreifende Replikation](cross-region-replication-introduction.md#service-level-objectives)
