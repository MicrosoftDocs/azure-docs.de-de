---
title: Dynamisches Ändern des Servicelevels eines Volumes für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt, wie Sie den Servicelevel eines Volumes dynamisch ändern.
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
ms.date: 11/12/2020
ms.author: b-juche
ms.openlocfilehash: e5219e1c87221ade8da68c21209f41b4d6139be2
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579078"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamisches Ändern der Dienstebene eines Volumes

> [!IMPORTANT] 
> * Die Registrierung für die öffentliche Vorschau dieses Features wurde bis auf Weiteres ausgesetzt. 
> * Das dynamische Ändern des Servicelevels eines Replikationszielvolumes wird derzeit nicht unterstützt.

Sie können den Servicelevel eines vorhandenen Volumes ändern, indem Sie das Volume in einen anderen Kapazitätspool verschieben, der den von Ihnen für das Volume gewünschten [Servicelevel](azure-netapp-files-service-levels.md) verwendet. Diese direkte Änderung des Servicelevels für das Volume erfordert keine Migration von Daten. Auch der Zugriff auf das Volume wird hiervon nicht beeinträchtigt.  

Diese Funktion ermöglicht es Ihnen, die Anforderungen Ihrer Workload nach Bedarf zu erfüllen.  Sie können ein vorhandenes Volume so ändern, dass es einen höheren Servicelevel verwendet, um die Leistung zu verbessern, oder so dass es einen niedrigeren Servicelevel verwendet, um die Kosten zu optimieren. Wenn sich das Volume beispielsweise derzeit in einem Kapazitätspool befindet, der den Servicelevel *Standard* verwendet, und Sie möchten, dass das Volume den Servicelevel *Premium* verwendet, können Sie das Volume dynamisch in einen Kapazitätspool verschieben, der den Servicelevel *Premium* verwendet.  

Der Kapazitätspool, in den Sie das Volume verschieben möchten, muss bereits vorhanden sein. Der Kapazitätspool kann andere Volumes enthalten.  Wenn Sie das Volume in einen ganz neuen Kapazitätspool verschieben möchten, müssen Sie [den Kapazitätspool erstellen](azure-netapp-files-set-up-capacity-pool.md), bevor Sie das Volume verschieben.  

## <a name="considerations"></a>Überlegungen

* Nachdem das Volume in einen anderen Kapazitätspool verschoben wurde, haben Sie keinen Zugriff mehr auf die Aktivitätsprotokolle und Volumenmetriken des vorherigen Volumes. Das Volume beginnt in dem neuen Kapazitätspool mit neuen Aktivitätsprotokollen und Metriken.

* Wenn Sie ein Volume in einen Kapazitätspool mit einem höheren Servicelevel verschieben (z. B. eine Verschiebung vom Servicelevel *Standard* nach *Premium* oder  *Ultra*), müssen Sie mindestens sieben Tage warten, bevor Sie dieses Volume wieder in einen Kapazitätspool mit einem niedrigeren Servicelevel *zurück* verschieben können (z. B. eine Verschiebung von *Ultra* nach *Premium* oder *Standard*).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Verschieben eines Volumes in einen anderen Kapazitätspool

1.  Klicken Sie auf der Seite „Volumes“ mit der rechten Maustaste auf das Volume, dessen Servicelevel Sie ändern möchten. Wählen Sie **Pool ändern** aus.

    ![Klicken Sie mit der rechten Maustaste auf das Volume.](../media/azure-netapp-files/right-click-volume.png)

2. Wählen Sie im Fenster „Pool ändern“ den Kapazitätspool aus, in den Sie das Volume verschieben möchten. 

    ![Pool ändern](../media/azure-netapp-files/change-pool.png)

3.  Klicken Sie auf **OK**.


## <a name="next-steps"></a>Nächste Schritte  

* [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
