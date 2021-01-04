---
title: Löschen von Volumereplikationen oder Volumes für die regionsübergreifende Azure NetApp Files-Replikation | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie eine Replikationsverbindung gelöscht werden kann, die zwischen den Quell- und Zielvolumes nicht länger benötigt wird.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249891"
---
# <a name="delete-volume-replications-or-volumes"></a>Löschen von Volumereplikationen oder Volumes

In diesem Artikel wird beschrieben, wie Sie Volumereplikationen löschen. Außerdem erfahren Sie, wie Sie das Quell- oder Zielvolume löschen.

## <a name="delete-volume-replications"></a>Löschen von Volumereplikationen

Sie können die Replikationsverbindung zwischen den Quell- und Zielvolumes beenden, indem Sie die Volumereplikation löschen. Sie müssen die Replikation vom Zielvolume aus löschen. Mit dem Löschvorgang wird nur die Autorisierung für die Replikation entfernt. Damit wird weder das Quell- noch das Zielvolume entfernt. 

1. Stellen Sie sicher, dass das Replikationspeering unterbrochen wurde, bevor Sie die Volumereplikation löschen. So unterbrechen Sie das Replikationspeering 

    1. Wählen Sie das Volume *Ziel* aus. Klicken Sie unter „Speicherdienst“ auf **Replikation**.  

    2.  Überprüfen Sie die folgenden Felder, bevor Sie fortfahren:  
        * Stellen Sie sicher, dass bei „Spiegelungsstatus“ ***Gespiegelt** _ angezeigt wird.   
            Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Spiegelungsstatus“ _Nicht initialisiert* angezeigt wird.
        * Stellen Sie sicher, dass bei „Beziehungsstatus“ ***Im Leerlauf** _ angezeigt wird.   
            Fahren Sie mit der Unterbrechung des Replikationspeerings nicht fort, wenn bei „Beziehungsstatus“ _Übertragung wird ausgeführt* angezeigt wird.   

        Weitere Informationen finden Sie unter [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md). 

    3.  Klicken Sie auf **Peering unterbrechen**.  

    4.  Geben Sie **Ja** ein, wenn Sie dazu aufgefordert werden, und klicken Sie auf **Unterbrechen**. 

        ![Unterbrechen des Replikationspeerings](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Wenn Sie die Volumereplikation löschen möchten, wählen Sie aus dem Quell- oder Zielvolume die Option **Replikation** aus.  

2. Klicken Sie auf **Löschen**.    

3. Bestätigen Sie den Löschvorgang, indem Sie **Ja** eingeben und auf **Löschen** klicken.   

    ![Replikation löschen](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Löschen von Quell- oder Zielvolumes

Wenn Sie das Quell- oder Zielvolume löschen möchten, müssen Sie die folgenden Schritte in der beschriebenen Reihenfolge ausführen. Andernfalls tritt der Fehler `Volume with replication cannot be deleted` auf.  

1. [Löschen Sie die Volumereplikation](#delete-volume-replications) vom Zielvolume aus.   

2. Löschen Sie das Ziel- oder Quellvolume nach Bedarf, indem Sie mit der rechten Maustaste auf den Volumenamen klicken und **Löschen** auswählen.   

    ![Screenshot des Kontextmenüs für ein Volume](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Regionsübergreifende Replikation](cross-region-replication-introduction.md)
* [Regionsübergreifende Replikation: Anforderungen und Überlegungen](cross-region-replication-requirements-considerations.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)

