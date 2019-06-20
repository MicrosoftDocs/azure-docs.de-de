---
title: Anzeigen und Verwenden der Azure Resource Manager-Vorlage eines virtuellen Computers | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die Azure Resource Manager-Vorlage eines virtuellen Computers verwenden, um andere virtuelle Computer zu erstellen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a759d9ce-655c-4ac6-8834-cb29dd7d30dd
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: spelluru
ms.openlocfilehash: 533770d98b146dea01e91e1249115c4b5c074b3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101563"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Erstellen von virtuellen Computern mit einer Azure Resource Manager-Vorlage 

Wenn Sie einen virtuellen Computer (VM) in DevTest Labs über das [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) erstellen, können Sie die Azure Resource Manager-Vorlage anzeigen, bevor Sie die VM speichern. Die Vorlage kann dann als Basis für die Erstellung von weiteren Lab-VMs mit den gleichen Einstellungen verwendet werden.

In diesem Artikel wird beschrieben, inwiefern sich Resource Manager-Vorlagen für mehrere VMs und für eine einzelne VM unterscheiden, und es wird gezeigt, wie Sie eine Vorlage beim Erstellen einer VM anzeigen und speichern.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Resource Manager-Vorlagen für mehrere VMs und einzelne VMs
Es gibt zwei Möglichkeiten zum Erstellen von VMs in DevTest Labs mit einer Resource Manager-Vorlage: Stellen Sie die Ressource „Microsoft.DevTestLab/labs/virtualmachines“ oder die Ressource „Microsoft.Compute/virtualmachines“ bereit. Diese Ressourcen werden jeweils in unterschiedlichen Szenarien genutzt und benötigen unterschiedliche Berechtigungen.

- Mit Resource Manager-Vorlagen, für die der Ressourcentyp „Microsoft.DevTestLab/labs/virtualmachines“ verwendet wird (wie in der „resource“-Eigenschaft der Vorlage deklariert), können einzelne Lab-VMs bereitgestellt werden. Jede VM wird in der DevTest Labs-Liste mit den virtuellen Computern dann als einzelner Eintrag angezeigt:

   ![Liste mit VMs als einzelne Einträge in der DevTest Labs-Liste mit den virtuellen Computern](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Diese Art von Resource Manager-Vorlage kann mit dem Azure PowerShell-Befehl **New-AzResourceGroupDeployment** oder dem Azure CLI-Befehl **az group deployment create** bereitgestellt werden. Hierfür sind Administratorberechtigungen erforderlich, sodass Benutzer, denen eine DevTest Labs-Benutzerrolle zugewiesen ist, die Bereitstellung nicht durchführen können. 

- Mit Resource Manager-Vorlagen, für die der Ressourcentyp „Microsoft.Compute/virtualmachines“ verwendet wird, können mehrere VMs als Einzelumgebung in der DevTest Labs-Liste mit den virtuellen Computern bereitgestellt werden:

   ![Liste mit VMs als einzelne Einträge in der DevTest Labs-Liste mit den virtuellen Computern](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   VMs derselben Umgebung können zusammen verwaltet werden und verfügen über den gleichen Lebenszyklus. Benutzer, denen eine DevTest Labs-Benutzerrolle zugewiesen ist, können mit diesen Vorlagen Umgebungen erstellen, sofern der Administrator das Lab entsprechend konfiguriert hat.

Im restlichen Teil dieses Artikels werden Resource Manager-Vorlagen beschrieben, für die „Microsoft.DevTestLab/labs/virtualmachines“ verwendet wird. Sie werden von Lab-Administratoren zum Automatisieren der Erstellung von Lab-VMs (z.B. abrufbare VMs) oder der Generierung von Gold-Images (z.B. Imagefactorys) verwendet.

Unter [Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) finden Sie zuverlässige und leicht anzuwendende Richtlinien und Vorschläge zum Erstellen einer Azure Resource Manager-Vorlage.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Anzeigen und Speichern der Resource Manager-Vorlage eines virtuellen Computers
1. Führen Sie die Schritte unter [Erstellen Ihres ersten virtuellen Computers in einem Lab in Azure DevTest Labs](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) aus, um mit dem Erstellen eines virtuellen Computers zu beginnen.
1. Geben Sie die erforderlichen Informationen für Ihren virtuellen Computer ein, und fügen Sie alle gewünschten Artefakte für den virtuellen Computer hinzu.
1. Wählen Sie unten im Fenster zum Konfigurieren der Einstellungen die Option **View ARM template** (ARM-Vorlage anzeigen).

   ![Anzeigen der Schaltfläche „View ARM template“ (ARM-Vorlage anzeigen)](./media/devtest-lab-use-arm-template/devtestlab-lab-view-rm-template.png)
1. Kopieren und speichern Sie die Resource Manager-Vorlage, damit Sie sie später zum Erstellen eines anderen virtuellen Computers verwenden können.

   ![Speichern der Resource Manager-Vorlage zur späteren Verwendung](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

Nachdem Sie die Resource Manager-Vorlage gespeichert haben, müssen Sie den Parameterabschnitt der Vorlage aktualisieren, bevor Sie sie verwenden können. Sie können die Datei „parameter.json“ erstellen, mit der nur die Parameter außerhalb der eigentlichen Resource Manager-Vorlage angepasst werden. 

![Anpassen der Parameter mit einer JSON-Datei](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Die Resource Manager-Vorlage kann jetzt zum [Erstellen einer VM](devtest-lab-create-environment-from-arm.md) verwendet werden.

### <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über das [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md).
* [Bereitstellen einer Resource Manager-Vorlage zum Erstellen einer VM](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Sehen Sie sich im [öffentlichen DevTest Labs-GitHub-Repository](https://github.com/Azure/azure-quickstart-templates) weitere Resource Manager-Schnellstartvorlagen für die DevTest Labs-Automatisierung an.
