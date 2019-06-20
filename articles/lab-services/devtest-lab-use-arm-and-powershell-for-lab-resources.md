---
title: Automatisches Erstellen oder Ändern von Labs mit Azure Resource Manager-Vorlagen mit PowerShell | Microsoft-Dokumentation
description: Informationen zum automatischen Erstellen oder Ändern von Labs in einem DevTest Lab mithilfe von Azure Resource Manager-Vorlagen mit PowerShell
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622456"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Automatisches Erstellen oder Ändern von Labs mit Azure Resource Manager-Vorlagen und PowerShell

DevTest Labs stellen eine Vielzahl von Azure Resource Manager-Vorlagen und PowerShell-Skripts bereit, mit denen Sie schnell und automatisch neue Labs erstellen oder vorhandene Labs ändern und diese Ressourcen dann bereitstellen können.

In diesem Artikel wird das Verfahren zum Verwenden dieser Vorlagen und Skripts beschrieben, um das Erstellen, Ändern und Bereitstellen Ihrer Labs zu automatisieren. In diesem Artikel wird auch erläutert, wo Sie weitere Informationen zur Verwendung von PowerShell finden, um häufige Aufgaben in DevTest Labs auszuführen.

## <a name="step-1-gather-your-templates-and-scripts"></a>Schritt 1: Suchen der Vorlagen und Skripts
Sie finden vordefinierte [Azure Resource Manager-Vorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) und [PowerShell-Skripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) im öffentlichen [GitHub-Repository](https://github.com/Azure/azure-devtestlab). Diese können Sie unverändert übernehmen oder an Ihre Anforderungen anpassen und in einem eigenen [privaten Git-Repository](devtest-lab-add-artifact-repo.md) speichern.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Schritt 2: Bearbeiten der Azure Resource Manager-Vorlagen
Folgen Sie den Schritten unter [Erstellen Ihrer ersten Azure Resource Manager-Vorlage](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template), wenn Sie noch nie eine Vorlage erstellt haben.

Unter [Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices) finden Sie weitere zuverlässige und leicht anzuwendende Richtlinien und Vorschläge zum Erstellen einer Azure Resource Manager-Vorlage. Normalerweise verwenden Sie eine der angegebenen Vorgehensweisen oder eines der Beispiele, um die Vorlage Ihren Wünschen entsprechend zu modifizieren.

## <a name="step-3-deploy-resources-with-powershell"></a>Schritt 3: Bereitstellen von Ressourcen mit PowerShell
Nachdem Sie die Vorlagen und Skripts angepasst haben, führen Sie die erforderlichen Schritte zum [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) aus. Der Artikel enthält allgemeine Informationen zur Verwendung von Azure PowerShell mit Azure Resource Manager-Vorlagen, um Ressourcen in Azure bereitzustellen.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Allgemeine Aufgaben, die in DevTest Labs mithilfe von PowerShell ausgeführt werden können
Es gibt zahlreiche andere allgemeine Aufgaben, die mithilfe von PowerShell automatisiert werden können. In den folgenden Abschnitten der Dokumentation werden die erforderlichen Schritte zum Ausführen dieser Aufgaben aufgeführt.

* [Erstellen eines benutzerdefinierten Images aus einer VHD-Datei mithilfe von PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Hochladen von VHD-Dateien in das Speicherkonto des Labs mithilfe von PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Hinzufügen eines externen Benutzers zu einem Lab mit PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Erstellen einer benutzerdefinierten Labrolle mithilfe von PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nächste Schritte
* Hier finden Sie Informationen zum Erstellen eines [privaten Git-Repositorys](devtest-lab-add-artifact-repo.md), in dem Sie Ihre angepassten Vorlagen oder Skripts speichern können.
* Sehen Sie sich die [Azure Resource Manager-Vorlagen aus dem Katalog mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) an.
