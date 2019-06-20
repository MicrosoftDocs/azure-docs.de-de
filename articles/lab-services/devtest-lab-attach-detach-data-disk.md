---
title: Anfügen oder Trennen eines Datenträgers an einen bzw. von einem virtuellen Computer in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Datenträger an einen virtuellen Computer in Azure DevTest Labs anfügen oder von diesem trennen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 2e168867ed342fb0b0545b5fdc330ba790f78de0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60304438"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Anfügen oder Trennen eines Datenträgers an einen oder von einem virtuellen Computer in Azure DevTest Labs
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) verwaltet die Speicherkonten, die VM-Datenträgern zugeordnet sind. Ein Benutzer muss lediglich einen neuen Datenträger an eine VM anfügen sowie Typ und Größe, die er benötigt, angeben – der Datenträger wird dann automatisch von Azure erstellt und verwaltet. Anschließend kann der Datenträger von der VM getrennt und später wieder an dieselbe VM oder an eine andere VM, die diesem Benutzer gehört, angefügt werden.

Diese Funktionalität ist besonders für die Verwaltung von Speicher oder Software außerhalb der einzelnen virtuellen Computer praktisch. Befindet sich der Speicher oder die Software bereits auf einem Datenträger, kann dieser einfach an eine VM angefügt, von dieser getrennt und wieder an eine VM des Benutzers, dem dieser Datenträger gehört, angefügt werden.

## <a name="attach-a-data-disk"></a>Datenträger anfügen
Bevor Sie einen Datenträger an eine VM anfügen, lesen Sie diese Tipps:

- Die Größe der VM bestimmt, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Sie können nur einen Datenträger an eine gegenwärtig ausgeführte VM anfügen. Bevor Sie versuchen, einen Datenträger anzufügen, stellen Sie sicher, dass die VM ausgeführt wird.

### <a name="attach-a-new-disk"></a>Anfügen eines neuen Datenträgers
Führen Sie die folgenden Schritte durch, um einen neuen verwalteten Datenträger zu erstellen und an eine VM in Azure DevTest Labs anzufügen:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das gewünschte Lab aus. 
1. Wählen Sie in der Liste **Meine virtuellen Computer** eine gegenwärtig ausgeführte VM aus.
1. Klicken Sie im Menü auf der linken Seite auf **Datenträger**.

    ![Auswählen von Datenträgern für einen virtuellen Computer](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png)
1. Wählen Sie **Neuen anfügen** aus, um einen neuen Datenträger zu erstellen und an die VM anzufügen.

    ![Anfügen eines neuen Datenträgers an einen virtuellen Computer](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Füllen Sie den Bereich **Neuen Datenträger anfügen** aus, indem Sie Name, Typ und Größe für den Datenträger angeben.

    ![Ausfüllen des Formulars „Neuen Datenträger anfügen“](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Klicken Sie auf **OK**.

Nach einem kurzen Augenblick wird der neue Datenträger erstellt, an die VM angefügt und in der Liste der **DATENTRÄGER** für die jeweilige VM angezeigt.

### <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers
Führen Sie die folgenden Schritte durch, um einen bereits vorhandenen Datenträger an eine gegenwärtig ausgeführte VM anzufügen. 

1. Wählen Sie eine ausgeführte VM aus, an die Sie erneut einen Datenträger anfügen möchten.
1. Klicken Sie im Menü auf der linken Seite auf **Datenträger**.
1. Wählen Sie **Vorhandenen anfügen** aus, um einen vorhandenen Datenträger an die VM anzufügen.

    ![Anfügen eines vorhandenen Datenträgers an einen virtuellen Computer](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing2.png)

1. Klicken Sie im Bereich **Vorhandenen Datenträger anfügen** auf „OK“.

    ![Anfügen eines vorhandenen Datenträgers an einen virtuellen Computer](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Nach einem kurzen Augenblick wird der Datenträger an die VM angefügt und in der Liste der **DATENTRÄGER** für diese VM angezeigt.

## <a name="detach-a-data-disk"></a>Trennen eines Datenträgers
Wenn Sie einen Datenträger, der einer VM angefügt ist, nicht mehr benötigen, können Sie ihn mühelos trennen. Bei dem Trennvorgang wird der Datenträger aus der VM entfernt, jedoch zur späteren Verwendung im Speicher beibehalten.

Wenn Sie die vorhandenen Daten auf dem Datenträger erneut verwenden möchten, können Sie ihn erneut an denselben virtuellen Computer oder an einen anderen anfügen.

### <a name="detach-from-the-vms-management-pane"></a>Trennen eines Datenträgers über den Verwaltungsbereich einer VM
1. Wählen Sie aus der Liste der virtuellen Computer eine VM aus, an die ein Datenträger angefügt wurde.
1. Klicken Sie im Menü auf der linken Seite auf **Datenträger**.

    ![Auswählen von Datenträgern für einen virtuellen Computer](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-data-disk.png) 
1. Wählen Sie in der Liste der **DATENTRÄGER** den Datenträger aus, der getrennt werden soll.
1. Wählen Sie im oberen Bereich mit den Details zum Datenträger **Trennen** aus.

    ![Trennen eines Datenträgers](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Wählen Sie **Ja** aus, um zu bestätigen, dass der Datenträger getrennt werden sollen.

Der Datenträger wird getrennt und steht zum Anfügen an eine andere VM zur Verfügung. 
### <a name="detach-from-the-labs-main-pane"></a>Trennen eines Datenträgers über den Lab-Hauptbereich
1. Wählen Sie im Lab-Hauptbereich **Meine Datenträger** aus.

    ![Zugreifen auf Lab-Datenträger](./media/devtest-lab-attach-detach-data-disk/devtest-lab-my-data-disks.png)
1. Klicken Sie mit der rechten Maustaste auf den zu trennenden Datenträger, oder klicken Sie auf die Auslassungspunkte (...), und wählen Sie **Trennen** aus.

    ![Trennen eines Datenträgers](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Wählen Sie **Ja** aus, um zu bestätigen, dass der Datenträger getrennt werden sollen.

   > [!NOTE]
   > Wenn ein Datenträger bereits getrennt ist, können Sie festlegen, dass er aus der Liste der verfügbaren Datenträger entfernt werden soll. Klicken Sie hierzu auf **Löschen**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Durchführen eines Upgrades für einen nicht verwalteten Datenträger
Wenn Sie bereits eine VM besitzen, die nicht verwaltete Datenträger verwendet, können Sie die VM problemlos für die Verwendung von verwalteten Datenträgern konfigurieren. Bei diesem Prozess werden sowohl der Betriebssystemdatenträger als auch alle anderen angefügten Datenträger konvertiert.

Um ein Upgrade für einen nicht verwalteten Datenträger durchzuführen, führen Sie die in diesem Artikel erläuterten Schritte zum [Trennen des Datenträgers](#detach-a-data-disk) von einer nicht verwalteten VM durch. [Fügen Sie den Datenträger erneut an eine verwaltete VM an](#attach-an-existing-disk), damit automatisch ein Upgrade für den nicht verwalteten Datenträger auf einen verwalteten Datenträger durchgeführt wird.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Datenträger für [abrufbare VMs](devtest-lab-add-claimable-vm.md#unclaim-a-vm) verwaltet werden.

