---
title: 'Regionsübergreifende Volumereplikation mit Azure NetApp Files: Anforderungen und Überlegungen | Microsoft-Dokumentation'
description: Beschreibt die Anforderungen und Überlegungen zur Verwendung der regionsübergreifenden Volumereplikationsfunktionalität von Azure NetApp Files.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 7b664dcd1cb12808960ffacf91c6d02d58632c4e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95243136"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Regionsübergreifende Replikation: Anforderungen und Überlegungen 

Beachten Sie die folgenden Anforderungen und Überlegungen zur [Verwendung der regionsübergreifenden Volumereplikation](cross-region-replication-create-peering.md)sfunktion von Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Anforderungen und Überlegungen 

* Das Features für die regionsübergreifende Replikation befindet sich derzeit in der öffentlichen Vorschauversion. Sie müssen eine Wartelistenanforderung für den Zugriff auf das Feature über die [Seite für Wartelistenanforderungen für die regionsübergreifende Replikation von Azure NetApp Files-Volumes](https://aka.ms/anfcrrpreviewsignup) übermitteln. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie das Feature für die regionsübergreifende Replikation verwenden.
* Die Azure NetApp Files-Replikation ist nur in bestimmten festen Regionspaaren verfügbar. Siehe [Unterstützte Regionspaare](cross-region-replication-introduction.md#supported-region-pairs). 
* SMB-Volumes werden zusammen mit NFS-Volumes unterstützt. Die Replikation von SMB-Volumes erfordert eine Active Directory-Verbindung in den Quell- und Ziel-NetApp-Konten. Die AD-Zielverbindung muss Zugriff auf die DNS-Server oder ADDS-Domänencontroller haben, die aus dem delegierten Subnetz in der Zielregion erreichbar sind. Weitere Informationen finden Sie unter [Anforderungen für Active Directory-Verbindungen](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Das Zielkonto muss sich in einer anderen Region als das Quellvolume befinden. Sie können auch ein vorhandenes NetApp-Konto in einer anderen Region auswählen.  
* Die Azure NetApp Files-Replikation unterstützt derzeit keine mehreren Abonnements. Alle Replikationen müssen unter einem einzelnen Abonnement ausgeführt werden.
* Sie können maximal bis zu fünf Volumes innerhalb eines einzelnen Abonnements pro Region für die Replikation einrichten. Sie können ein Supportticket öffnen, um eine Erhöhung des Standardkontingents von fünf Replikationszielvolumes (pro Abonnement in einer Region) anzufordern. 
* Es kann bis zu fünf Minuten dauern, bis in der Oberfläche eine neu hinzugefügte Momentaufnahme auf dem Quellvolume angezeigt wird.  
* Kaskadierende und nach innen/außen auffächernde Topologien werden nicht unterstützt.
* Das Konfigurieren der Volumereplikation für Quellvolumes, die aus einer Momentaufnahme erstellt wurden, wird derzeit nicht unterstützt.
* Nachdem Sie die regionsübergreifende Replikation eingerichtet haben, erstellt der Replikationsprozess *Snapmirror-Momentaufnahmen*, um Verweise zwischen dem Quellvolume und dem Zielvolume bereitzustellen. Snapmirror-Momentaufnahmen werden automatisch zyklisch durchlaufen, wenn für jede inkrementelle Übertragung eine neue erstellt wird. Sie können Snapmirror-Momentaufnahmen erst löschen, nachdem die Replikationsbeziehung und das Volume gelöscht wurden. 
* Sie können manuelle Momentaufnahmen auf dem Quellvolume einer Replikationsbeziehung löschen, wenn die Replikationsbeziehung aktiv oder unterbrochen ist, und auch nachdem die Replikationsbeziehung gelöscht wurde. Sie können manuelle Momentaufnahmen für das Zielvolume erst löschen, wenn die Replikationsbeziehung unterbrochen wurde.
* Sie können nicht auf eine Momentaufnahme zurücksetzen, die vor dem Erstellen des Zielvolumes der Replikation erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen der Volumereplikation](cross-region-replication-create-peering.md)
* [Anzeigen des Integritätsstatus der Replikationsbeziehung](cross-region-replication-display-health-status.md)
* [Verwalten der Notfallwiederherstellung](cross-region-replication-manage-disaster-recovery.md)
* [Metriken für die Volumereplikation](azure-netapp-files-metrics.md#replication)
* [Löschen von Volumereplikationen oder Volumes](cross-region-replication-delete.md)
* [Problembehandlung für die regionsübergreifende Replikation](troubleshoot-cross-region-replication.md)


