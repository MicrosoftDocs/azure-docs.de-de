---
title: Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs | Microsoft-Dokumentation
description: Informationen zum Speichern von Geheimnissen in einer Azure Key Vault-Instanz und zu deren Verwendung beim Erstellen einer VM, Formel oder Umgebung.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 17469d3602935715d570a496e12b6680269ff465
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622907"
---
# <a name="store-secrets-in-a-key-vault-in-azure-devtest-labs"></a>Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs
Möglicherweise möchten Sie bei der Verwendung von Azure DevTest Labs ein komplexes Geheimnis eingeben: das Kennwort für Ihre Windows-VM, den öffentlichen SSH-Schlüssel für Ihre Linux-VM oder das persönliche Zugriffstoken zum Klonen Ihres Git-Repositorys über ein Artefakt. Geheimnisse sind in der Regel lang und weisen zufällige Zeichen auf. Aus diesem Grund kann ihre Eingabe schwierig und unpraktisch sein, insbesondere dann, wenn Sie das gleiche Geheimnis mehrmals verwenden.

Um dieses Problem zu lösen und auch Ihre Geheimnisse an einem sicheren Ort zu speichern, unterstützt DevTest Labs das Speichern von Geheimnissen in einer [Azure Key Vault-Instanz](../key-vault/key-vault-overview.md). Wenn ein Benutzer zum ersten Mal ein Geheimnis speichert, erstellt der DevTest Labs-Dienst automatisch einen Schlüsseltresor in der gleichen Ressourcengruppe, die das Lab enthält, und speichert das Geheimnis im Schlüsseltresor. DevTest Labs erstellt für jeden Benutzer einen separaten Schlüsseltresor. 

## <a name="save-a-secret-in-azure-key-vault"></a>Speichern eines Geheimnisses in Azure Key Vault
Um Ihr Geheimnis in Azure Key Vault zu speichern, führen Sie die folgenden Schritte aus:

1. Wählen Sie im linken Menü **Meine Geheimnisse** aus.
2. Geben Sie einen **Namen** für das Geheimnis ein. Sie sehen diesen Namen beim Erstellen einer VM, Formel oder Umgebung in der Dropdownliste. 
3. Geben Sie das Geheimnis als **Wert** ein.

    ![Speichern des Geheimnisses](media/devtest-lab-store-secrets-in-key-vault/store-secret.png)

## <a name="use-a-secret-from-azure-key-vault"></a>Verwenden eines Geheimnisses aus Azure Key Vault
Wenn Sie ein Geheimnis zum Erstellen einer VM, Formel oder Umgebung eingeben müssen, können Sie es entweder manuell eingeben oder ein gespeichertes Geheimnis aus dem Schlüsseltresor auswählen. Um ein in Ihrem Schlüsseltresor gespeichertes Geheimnis zu verwenden, führen Sie die folgenden Aktionen aus:

1. Wählen Sie **Gespeichertes Geheimnis verwenden** aus. 
2. Wählen Sie Ihr Geheimnis aus der Dropdownliste für **Geheimnis auswählen** aus. 

    ![Verwenden eines Geheimnisses im virtuellen Computer](media/devtest-lab-store-secrets-in-key-vault/secret-store-pick-a-secret.png)

## <a name="use-a-secret-in-an-azure-resource-manager-template"></a>Verwenden eines Geheimnisses in einer Azure Resource Manager-Vorlage
Sie können den Namen Ihres Geheimnisses in einer Azure Resource Manager-Vorlage angeben, die zum Erstellen eines virtuellen Computers verwendet wird, wie im folgenden Beispiel gezeigt:

![Verwenden eines Geheimnisses in Formel oder Umgebung](media/devtest-lab-store-secrets-in-key-vault/secret-store-arm-template.png)

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md) 
- [Verwalten von Azure DevTest Labs-Formeln](devtest-lab-manage-formulas.md)
- [Erstellen von Umgebungen mit mehreren virtuellen Computern und PaaS-Ressourcen mit Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md)
