---
title: Einrichten eines Kapazitätspools für Azure NetApp Files | Microsoft-Dokumentation
description: Beschreibt die Vorgehensweise zum Einrichten eines Kapazitätspools für die Volumeerstellung.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60691097"
---
# <a name="set-up-a-capacity-pool"></a>Einrichten eines Kapazitätspools

Nach dem Einrichten eines Kapazitätspools können Sie Volumes in diesem Pool erstellen.  

## <a name="before-you-begin"></a>Voraussetzungen 

Sie müssen bereits ein NetApp-Konto erstellt haben.   

[Erstellen eines NetApp-Kontos](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Schritte 

1. Navigieren Sie zum Blatt für die Verwaltung Ihres NetApp-Kontos, und klicken Sie anschließend im Navigationsbereich auf **Kapazitätspools**.  
    
    ![Navigieren zu einem Kapazitätspool](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Klicken Sie auf **+ Pools hinzufügen**, um einen neuen Kapazitätspool zu erstellen.   
    Das Fenster für den neuen Kapazitätspool wird angezeigt.

3. Geben Sie für den neuen Kapazitätspool folgende Informationen an:  
   * **Name**  
     Geben Sie den Namen für den Kapazitätspool an.  
     Der Name des Kapazitätspools muss für jedes NetApp-Konto eindeutig sein.

   * **Servicelevel**   
     In diesem Feld wird die gewünschte Leistung des Kapazitätspools angezeigt.  
     Geben Sie die Dienstebene für den Kapazitätspool an: [**Premium**](azure-netapp-files-service-levels.md#Premium) oder [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Größe**     
     Geben Sie die Größe des Kapazitätspools an, den Sie erwerben.        
     Die Mindestgröße eines Kapazitätspools beträgt 4 TiB. Die Poolgröße kann auf ein Vielfaches von 4 TiB festgelegt werden.   
      
     ![Neuer Kapazitätspool](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Klicken Sie auf **OK**.

## <a name="next-steps"></a>Nächste Schritte 

- [Dienstebenen für Azure NetApp Files](azure-netapp-files-service-levels.md)
- Die Preise der verschiedenen Dienstebenen finden Sie unter [Preise für Azure NetApp Files (Vorschauversion)](https://azure.microsoft.com/pricing/details/storage/netapp/).
- [Delegieren eines Subnetzes für Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
