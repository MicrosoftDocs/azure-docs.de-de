---
title: Manueller QoS-Kapazitätspool für Azure NetApp Files | Microsoft-Dokumentation
description: Hier finden Sie eine Einführung in den manuellen QoS-Kapazitätspool sowie Verweise auf weitere Informationen.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 13acee8b21adf946192544afcea17b4a8d9b9ec9
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581124"
---
# <a name="manual-qos-capacity-pool"></a>Manueller QoS-Kapazitätspool

Dieser Artikel bietet eine Einführung in die Funktionalität des manuellen QoS-Kapazitätspools (Quality of Service).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Unterschiede zwischen manuellem QoS und automatischem QoS

Der [QoS-Typ](azure-netapp-files-understand-storage-hierarchy.md#qos_types) ist ein Attribut eines Kapazitätspools. Azure NetApp Files bietet zwei QoS-Typen von Kapazitätspools – automatisch (Standard) und manuell.  

In einem *manuellen* QoS-Kapazitätspool können Sie Kapazität und Durchsatz für ein Volume separat zuweisen. Informationen zum Mindest- und Höchstdurchsatz finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits). Der Gesamtdurchsatz aller Volumes, die mit einem Kapazitätspool mit dem QoS-Typ „Manuell“ erstellt werden, ist durch den Gesamtdurchsatz des Pools begrenzt. Dieser wird anhand der Kombination von Poolgröße und Serviceleveldurchsatz ermittelt. 

In einem *automatischen* QoS-Kapazitätspool wird der Durchsatz den Volumes im Pool automatisch zugewiesen. Dies erfolgt proportional zum Größenkontingent, das den Volumes zugewiesen ist.  

Informationen zu den Überlegungen zu den QoS-Typen finden Sie unter [Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) und [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md).

## <a name="example-of-using-manual-qos"></a>Beispiel für die Verwendung von manuellem QoS

Wenn Sie einen manuellen QoS-Kapazitätspool mit z. B. einem SAP HANA-System, einer Oracle-Datenbankinstanz oder anderen Workloads verwenden, die mehrere Volumes erfordern, kann der Kapazitätspool verwendet werden, um diese Anwendungsvolumes zu erstellen.  Jedes Volume kann die individuelle Größe und den individuellen Durchsatz bereitstellen, um die Anwendungsanforderungen zu erfüllen.  Ausführliche Informationen zu den Vorteilen finden Sie unter [Beispiele zur Durchsatzbegrenzung für Volumes in einem manuellen QoS-Kapazitätspool](azure-netapp-files-service-levels.md#throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool).  

## <a name="how-to-specify-the-manual-qos-type"></a>Angeben des manuellen QoS-Typs

Wenn Sie [einen Kapazitätspool erstellen](azure-netapp-files-set-up-capacity-pool.md), können Sie angeben, dass für den Kapazitätspool der manuelle QoS-Typ verwendet werden soll.  Sie können auch [einen vorhandenen Kapazitätspool ändern](manage-manual-qos-capacity-pool.md#change-to-qos), sodass der manuelle QoS-Typ verwendet wird. 

Beim Festlegen des QoS-Kapazitätstyps auf „Manuell“ handelt es sich um eine permanente Änderung. Das bedeutet, dass ein QoS-Kapazitätspool vom Typ „Manuell“ nicht auf den QoS-Kapazitätspooltyp „Automatisch“ umgestellt werden kann. 

Wenn Sie den manuellen QoS-Typ verwenden, müssen Sie [die Funktion registrieren](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten eines manuellen QoS-Kapazitätspools](manage-manual-qos-capacity-pool.md)
* [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
* [Speicherhierarchie](azure-netapp-files-understand-storage-hierarchy.md) 
* [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Erstellen eines NFS-Volumes](azure-netapp-files-create-volumes.md)
* [Erstellen eines SMB-Volumes](azure-netapp-files-create-volumes-smb.md)
* [Erstellen eines Dual-Protokoll-Volumes](create-volumes-dual-protocol.md)
* [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md)
* [Problembehandlung bei Kapazitätspools](troubleshoot-capacity-pools.md)
