---
title: Neustarten eines virtuellen Computers in einem Lab in Azure DevTest Labs | Microsoft-Dokumentation
description: Informationen zum Neustart eines virtuellen Computers in Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 34c08a79abf6acb5ae8582ecd0743a890d850fc8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60864396"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Neustarten eines virtuellen Computers in einem Lab in Azure DevTest Labs
Sie können einen virtuellen Computer mithilfe der Schritte in diesem Artikel schnell und problemlos neu starten. Beachten Sie Folgendes vor dem Neustart eines virtuellen Computers:

- Der virtuelle Computer muss aktiv sein, damit die Neustartfunktion aktiviert werden kann.
- Wenn ein Benutzer mit einem aktiven virtuellen Computer verbunden ist, wenn der Neustart ausgeführt wird, muss die Verbindung mit dem virtuellen Computer nach dem Neustart erneut hergestellt werden.
- Wenn beim Neustart des virtuellen Computers ein Artefakt angewendet wird, erhalten Sie eine Warnung, dass das Artefakt möglicherweise nicht angewendet wird.

    ![Warnung beim Neustart während der Anwendung von Artefakten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Wenn der virtuelle Computer beim Anwenden eines Artefakts angehalten wurde, können Sie die Funktion zum Neustart des virtuellen Computers als potenzielle Möglichkeit zur Behebung des Problems verwenden.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Schritte zum Neustarten eines virtuellen Computers in einem Lab in Azure DevTest Labs
1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das Lab aus, das den neu zu startenden virtuellen Computer enthält.
1. Wählen Sie im linken Bereich die Option **Meine virtuellen Computer** aus.
1. Wählen Sie in der Liste der virtuellen Computer einen aktiven virtuellen Computer aus.
1. Wählen Sie am Anfang des Bereichs zur Verwaltung virtueller Computer die Option **Neu starten** aus.

    ![Schaltfläche „Neu starten“ für virtuellen Computer](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Überwachen Sie den Status des Neustarts, indem Sie das Symbol **Benachrichtigungen** am oberen rechten Rand des Fensters auswählen.

    ![Anzeigen des Status für den Neustart des virtuellen Computers](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Sie können auch einen aktiven virtuellen Computer neu starten, indem Sie dessen Auslassungspunkte (...) in der Liste von **Meine virtuellen Computer** auswählen.

![Neustarten virtueller Computer mithilfe der Auslassungspunkte](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nächste Schritte
* Nachdem der virtuelle Computer neu gestartet wurde, können Sie die Verbindung mit diesem wiederherstellen, indem sie im Verwaltungsbereich **Verbinden** auswählen.
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
