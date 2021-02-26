---
title: Verwalten eines manuellen QoS-Kapazitätspools für Azure NetApp Files | Microsoft-Dokumentation
description: Hier wird beschrieben, wie ein Kapazitätspool verwaltet wird, für den der QoS-Typ „Manuell“ verwendet wird. Dabei wird erläutert, wie ein manueller QoS-Kapazitätspool eingerichtet und wie ein Kapazitätspool für die Verwendung des QoS-Typs „Manuell“ geändert wird.
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
ms.topic: how-to
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 566cc3b1192d632bbffb8f9ef091f291b4bcc6e6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581147"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Verwalten eines manuellen QoS-Kapazitätspools

In diesem Artikel wird beschrieben, wie ein Kapazitätspool verwendet wird, für den der QoS-Typ „Manuell“ verwendet wird.  

Informationen zu den Überlegungen zu den QoS-Typen finden Sie unter [Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) und [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md).  

## <a name="register-the-feature"></a>Registrieren der Funktion
Die Funktion „QoS-Typ ‚Manuell‘“ befindet sich derzeit in der Vorschauphase. Wenn Sie diese Funktion zum ersten Mal verwenden, müssen Sie sie zuerst registrieren.
  
1.  Registrieren Sie die Funktion:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Überprüfen Sie den Status der Funktionsregistrierung: 

    > [!NOTE]
    > Der **RegistrationState** kann für bis zu 60 Minuten den Status `Registering` aufweisen, bevor der Wechsel in `Registered` erfolgt. Warten Sie, bis der Status **Registriert** lautet, bevor Sie fortfahren.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Sie können auch die [Azure CLI-Befehle](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Einrichten eines neuen manuellen QoS-Kapazitätspools 

So erstellen Sie einen neuen Kapazitätspool mit dem QoS-Typ „Manuell“:

1. Führen Sie die Schritte unter [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md) durch.  

2. Wählen Sie im Fenster „Neuer Kapazitätspool“ den QoS-Typ **Manuell** aus.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Ändern eines Kapazitätspools für die Verwendung des QoS-Typs „Manuell“

Sie können einen Kapazitätspool, für den derzeit der QoS-Typ „Automatisch“ verwendet wird, so ändern, dass der QoS-Typ „Manuell“ verwendet wird.  

> [!IMPORTANT]
> Beim Festlegen des QoS-Kapazitätstyps auf „Manuell“ handelt es sich um eine permanente Änderung. Das bedeutet, dass ein QoS-Kapazitätspool vom Typ „Manuell“ nicht auf den QoS-Kapazitätspooltyp „Automatisch“ umgestellt werden kann.  
> Während der Konvertierung wird der Durchsatz möglicherweise auf die Durchsatzlimits für Volumes des manuellen QoS-Typs begrenzt. Weitere Informationen finden Sie unter [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits).

1. Navigieren Sie zum Blatt für die Verwaltung Ihres NetApp-Kontos, und klicken Sie auf **Kapazitätspools**, um vorhandene Kapazitätspools anzuzeigen.   
 
2.  Klicken Sie auf den Kapazitätspool, bei dem Sie die Einstellung für den QoS-Typ in „Manuell“ ändern möchten.

3.  Klicken Sie auf **Change QoS type** (QoS-Typ ändern). Legen Sie für **New QoS Type** (Neuer QoS-Typ) die Einstellung **Manuell** fest. Klicken Sie auf **OK**. 

![Ändern des QoS-Typs](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Überwachen des Durchsatzes eines manuellen QoS-Kapazitätspools  

Es sind Metriken verfügbar, die Sie beim Überwachen des Lese- und Schreibdurchsatzes eines Volumes unterstützen.  Informationen hierzu finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Ändern des zugewiesenen Durchsatzes eines manuellen QoS-Volumes 

Wenn ein Volume in einem manuellen QoS-Kapazitätspool enthalten ist, können Sie den zugewiesenen Volumedurchsatz nach Bedarf ändern.

1. Wählen Sie auf der Seite **Volumes** das Volume aus, für das Sie den Durchsatz ändern möchten.   

2. Klicken Sie auf **Change throughput** (Durchsatz ändern). Geben Sie den gewünschten **Durchsatz (MiB/s)** an. Klicken Sie auf **OK**. 

    ![Ändern des QoS-Durchsatzes](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
* [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md)
* [Überlegungen zur Leistung für Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Problembehandlung bei Kapazitätspools](troubleshoot-capacity-pools.md)
* [Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
* [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Erstellen eines NFS-Volumes](azure-netapp-files-create-volumes.md)
* [Erstellen eines SMB-Volumes](azure-netapp-files-create-volumes-smb.md)
* [Erstellen eines Dual-Protokoll-Volumes](create-volumes-dual-protocol.md)
