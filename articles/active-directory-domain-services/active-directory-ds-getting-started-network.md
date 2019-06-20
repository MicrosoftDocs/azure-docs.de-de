---
title: 'Azure Active Directory Domain Services: Erste Schritte | Microsoft-Dokumentation'
description: Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: mstephen
ms.openlocfilehash: 65cc63b32afcc565f1901c4df2893ad103ec0da3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234911"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Aktivieren von Azure Active Directory Domain Services mithilfe des Azure-Portals


## <a name="before-you-begin"></a>Voraussetzungen
Informationen finden Sie unter [Netzwerkaspekte für die Azure Active Directory Domain Services](network-considerations.md).


## <a name="task-2-configure-network-settings"></a>Aufgabe 2: Konfigurieren der Netzwerkeinstellungen
Die nächste Konfigurationsaufgabe besteht in der Erstellung eines virtuellen Azure-Netzwerks und eines dedizierten Subnetzes. Azure Active Directory Domain Services wird dann in diesem Subnetz Ihres virtuellen Netzwerks aktiviert. Sie können auch ein bestehendes virtuelles Netzwerk auswählen, in dem Sie das dedizierte Subnetz erstellen.

1. Klicken Sie auf **Virtual Network** (Virtuelles Netzwerk), um ein virtuelles Netzwerk auszuwählen.
    > [!NOTE]
    > **Klassische virtuelle Netzwerke werden für neue Bereitstellungen nicht unterstützt.** Klassische virtuelle Netzwerke werden für neue Bereitstellungen nicht unterstützt. Vorhandene verwaltete Domänen, die in klassischen virtuellen Netzwerken bereitgestellt wurden, werden weiterhin unterstützt. Microsoft ermöglicht es Ihnen, eine vorhandene verwaltete Domäne aus einem klassischen virtuellen Netzwerk in ein virtuelles Resource Manager-Netzwerk zu migrieren.
    >

2. Auf der Seite **Virtuelles Netzwerk wählen** sehen Sie alle bestehenden virtuellen Netzwerke. Sie können nur die virtuellen Netzwerke sehen, die zu der Ressourcengruppe und dem Azure-Speicherort gehören, die Sie auf der Seite **Grundlagen** des Assistenten ausgewählt haben.
3. Wählen Sie dann das virtuelle Netzwerk aus, in dem Azure AD Domain Services aktiviert werden sollen. Sie können ein vorhandenes virtuelles Netzwerk auswählen oder ein neues erstellen.

   > [!TIP]
   > **Nach dem Aktivieren der Azure AD Domain Services können Sie Ihre verwaltete Domäne nicht mehr in ein anderes virtuelles Netzwerk verschieben.** Wählen Sie das rechte virtuelle Netzwerk aus, um Ihre verwaltete Domäne zu aktivieren. Nach der Erstellung einer verwalteten Domäne können Sie sie nicht in ein anderes virtuelles Netzwerk verschieben, ohne die verwaltete Domäne zu löschen. Bevor Sie fortfahren, sollten Sie den Artikel [Netzwerkaspekte für die Azure AD Domain Services](network-considerations.md) lesen.  
   >

4. **Virtuelles Netzwerk erstellen:** Klicken Sie auf **Neu erstellen**, wenn Sie ein neues virtuelles Netzwerk erstellen möchten. Verwenden Sie ein dediziertes Subnetz für Azure AD Domain Services. Erstellen Sie zum Beispiel ein Subnetz mit dem Namen „DomainServices“, um es für andere Administratoren verständlich zu machen, was innerhalb des Subnetzes bereitgestellt wird. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![Auswählen eines virtuellen Netzwerks](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Stellen Sie sicher, dass Sie einen Adressraum auswählen, der in dem privaten IP-Adressraum liegt. IP-Adressen, die Sie nicht besitzen und die sich im öffentlichen Adressraum befinden, führen zu Fehlern in Azure AD Domain Services.

5. **Vorhandenes virtuelles Netzwerk:** Wenn Sie ein vorhandenes virtuelles Netzwerk verwenden möchten, [erstellen Sie ein dediziertes Subnetz mithilfe der Erweiterung für virtuelle Netzwerke](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet), und wählen Sie dieses Subnetz anschließend aus. Klicken Sie auf **Virtuelles Netzwerk**, um ein virtuelles Netzwerk auszuwählen. Klicken Sie auf **Subnetz**, um das dedizierte Subnetz in dem bestehenden virtuellen Netzwerk auszuwählen, in dem Ihre neue verwaltete Domäne aktiviert werden soll. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ![Auswählen eines Subnetzes innerhalb des virtuellen Netzwerks](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Richtlinien für das Auswählen eines Subnetzes**
   > 1. Verwenden Sie ein dediziertes Subnetz für Azure AD Domain Services. Stellen Sie für dieses Subnetz keine weiteren virtuellen Computer bereit. Diese Konfiguration ermöglicht es Ihnen, Netzwerksicherheitsgruppen (NSGs) für Ihre Workloads bzw. virtuellen Computer zu konfigurieren, ohne Ihre verwaltete Domäne zu unterbrechen. Einzelheiten dazu finden Sie unter [networking considerations for Azure Active Directory Domain Services (Netzwerkaspekte für Azure Active Directory Domain Services)](network-considerations.md).
   > 2. Wählen Sie für die Bereitstellung von Azure AD Domain Services nicht das Gatewaysubnetz, weil es sich dabei um eine nicht unterstützte Konfiguration handelt.
   > 3. Im Adressraum des gewählten Subnetzes müssen mindestens drei bis fünf IP-Adressen verfügbar sein.

6. Wenn Sie fertig sind, klicken Sie auf **OK**, um zur Seite **Administratorgruppe** des Assistenten zu gelangen.


## <a name="next-step"></a>Nächster Schritt
[Task 3: configure administrative group and enable Azure AD Domain Services (Aufgabe 3: Konfigurieren einer administrativen Gruppe und Aktivieren von Azure AD Domain Services)](active-directory-ds-getting-started-admingroup.md)
