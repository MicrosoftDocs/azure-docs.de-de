---
title: Diagnostizieren von Artefaktfehlern auf einem virtuellen Azure DevTest Labs-Computer | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Probleme aufgrund von Artefaktfehlern in Azure DevTest Labs behandeln.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2019
ms.author: spelluru
ms.openlocfilehash: 29af70a2713e7b4aebf611d8f2b547e38c6c5d3d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60623105"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnostizieren von Artefaktfehlern im Lab 
Nachdem Sie ein Artefakt erstellt haben, können Sie überprüfen, ob es erfolgreich war oder nicht. Artefaktprotokolle in Azure DevTest Labs bieten Informationen, die Ihnen beim Diagnostizieren eines Artefaktfehlers helfen. Zum Anzeigen der Artefaktprotokollinformationen für eine Windows-VM stehen Ihnen eine Reihe von Optionen zur Verfügung:

* Im Azure-Portal
* Auf der VM

> [!NOTE]
> Um sicherzustellen, dass Fehler ordnungsgemäß bestimmt und erläutert werden, ist es wichtig, dass das Artefakt die richtige Struktur aufweist. Informationen dazu, wie ein Artefakt ordnungsgemäß erstellt wird, finden Sie unter [Erstellen benutzerdefinierter Artefakte](devtest-lab-artifact-author.md). Ein Beispiel eines ordnungsgemäß strukturierten Artefakts ist das Artefakt zum [Testen von Parametertypen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Problembehandlung bei Artefaktfehlern im Azure-Portal

1. Wählen Sie im Azure-Portal in der Liste der Ressourcen Ihr Lab aus.
2. Wählen Sie die Windows-VM mit dem Artefakt, das Sie untersuchen möchten.
3. Im linken Bereich unter **ALLGEMEIN** wählen Sie **Artefakte** aus. Eine Liste von Artefakten, die dieser VM zugeordnet sind, wird angezeigt. Der Name des Artefakts und der Artefaktstatus werden angegeben.

   ![Artefaktstatus](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Wählen Sie ein Artefakt mit dem Status **Fehler** aus. Das Artefakt wird geöffnet. Es wird eine ausführliche Meldung mit Details zum Fehler des Artefakts angezeigt.

   ![Artefaktfehlermeldung](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Beheben von Artefaktfehlern innerhalb des virtuellen Computers

1. Melden Sie sich bei der VM mit dem Artefakt an, das Sie untersuchen möchten.
2. Navigieren Sie zu „C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status“, wobei *1.9* die Azure-Versionsnummer der benutzerdefinierten Skripterweiterung ist.

   ![Die Statusdatei](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Öffnen Sie die **status**-Datei.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Verwandte Blogbeiträge
* [Einbinden einer VM in eine vorhandene Active Directory-Domäne mithilfe einer Resource Manager-Vorlage in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [ein Git-Repository einem Lab hinzufügen](devtest-lab-add-artifact-repo.md).

