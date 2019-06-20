---
title: Hinzufügen eines Git-Repositorys zu einem Lab in Azure DevTest Labs | Microsoft Docs
description: Erfahren Sie, wie Sie ein GitHub- oder Azure DevOps Services-Git-Repository für Ihre benutzerdefinierte Artefaktquelle in Azure DevTest Labs hinzufügen.
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5d7665cbfdf855e194f61910f0c8ee2bce5469b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60311716"
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Hinzufügen eines Git-Repositorys zum Speichern von benutzerdefinierten Artefakten und Resource Manager-Vorlagen

Sie können für die VMs in Ihrem Lab [benutzerdefinierte Artefakte erstellen](devtest-lab-artifact-author.md) oder [eine benutzerdefinierte Testumgebung mithilfe von Azure Resource Manager-Vorlagen erstellen](devtest-lab-create-environment-from-arm.md). Sie müssen ein privates Git-Repository für die Artefakte oder Resource Manager-Vorlagen, die Ihr Team erstellt, hinzufügen. Das Repository kann auf [GitHub](https://github.com) oder in [Azure DevOps Services](https://visualstudio.com) gehostet werden.

Wir bieten ein [GitHub-Repository von Artefakten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), die Sie in unveränderter oder angepasster Form für Ihre Labs bereitstellen können. Beim Anpassen oder Erstellen eines Artefakts können Sie das Artefakt nicht im öffentlichen Repository speichern. Sie müssen ein eigenes privates Repository für benutzerdefinierte Artefakte und die von Ihnen erstellten Artefakte erstellen. 

Wenn Sie eine VM erstellen, können Sie die Resource Manager-Vorlage speichern, bei Bedarf anpassen und später verwenden, um weitere VMs zu erstellen. Sie müssen Ihr eigenes privates Repository zum Speichern der benutzerdefinierten Resource Manager-Vorlagen erstellen.  

* Anweisungen zum Erstellen eines GitHub-Repositorys finden Sie unter [GitHub Bootcamp](https://help.github.com/categories/bootcamp/)(in englischer Sprache).
* Anweisungen zum Erstellen eines Azure DevOps Services-Projekts mit einem Git-Repository finden Sie unter [Herstellen einer Verbindung mit Azure DevOps Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

Die folgende Abbildung zeigt ein mögliches Beispiel für ein Repository mit Artefakten in GitHub:  

![Beispiel: GitHub-Artefaktrepository](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Abrufen von Repository- und Anmeldeinformationen
Bevor Sie Ihrem Lab ein Repository hinzufügen können, rufen Sie zunächst wichtige Informationen aus dem Repository ab. In den folgenden Abschnitten wird beschrieben, wie Sie die erforderlichen Informationen für Repositorys abrufen, die auf GitHub oder in Azure DevOps Services gehostet werden.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Abrufen der Klon-URL und des persönlichen Zugriffstokens für das GitHub-Repository

1. Navigieren Sie zur Startseite des GitHub-Repositorys, das die Artefakt- oder Resource Manager-Vorlagendefinitionen enthält.
2. Wählen Sie **Clone or download**aus.
3. Um die URL in die Zwischenablage zu kopieren, klicken Sie auf die Schaltfläche **HTTPS-Klon-URL**. Speichern Sie die URL für die spätere Verwendung.
4. Wählen Sie das Profilbild in der oberen rechten Ecke von GitHub aus, und wählen Sie **Einstellungen**.
5. Wählen Sie im Menü **Personal settings** auf der linken Seite auf **Personal access tokens**.
6. Wählen Sie **Generate new token**.
7. Geben Sie auf der Seite **Neues persönliches Zugriffstoken** unter **Tokenbeschreibung** eine Tokenbeschreibung ein. Übernehmen Sie die Standardelemente unter **Bereiche auswählen**, und wählen Sie dann **Token generieren** aus.
8. Speichern Sie das generierte Token. Das Token benötigen Sie später.
9. Schließen Sie GitHub.   
10. Fahren Sie mit dem Abschnitt [Verbinden des Labs mit dem Repository](#connect-your-lab-to-the-repository) fort.

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Abrufen der Klon-URL und des persönlichen Zugriffstokens für Azure Repos

1. Wechseln Sie zur Startseite Ihrer Teamsammlung (z.B. https://contoso-web-team.visualstudio.com) ), und wählen Sie dann das Projekt aus.
2. Wählen Sie auf der Startseite des Projekts **Code**.
3. Um die Klon-URL für das Projekt anzuzeigen, wählen Sie auf der Seite **Code** die Option **Klonen** aus.
4. Speichern Sie die URL. Sie benötigen die URL später.
5. Um ein persönliches Zugriffstoken zu erstellen, wählen Sie im Dropdownmenü Ihres Benutzerkontos **Mein Profil**.
6. Wählen Sie auf der Seite mit den Profilinformationen **Sicherheit**.
7. Wählen Sie auf der Registerkarte **Sicherheit** die Option **Hinzufügen** aus.
8. Gehen Sie auf der Seite **Persönliches Zugriffstoken erstellen** folgendermaßen vor:
   1. Geben Sie eine **Beschreibung** für das Token ein.
   2. Wählen Sie **180 Tagen** in der Liste **Läuft ab in** aus.
   3. Wählen Sie **Alle verfügbaren Konten** in der Liste **Konten** aus.
   4. Wählen Sie die Option **Alle Bereiche** aus.
   5. Wählen Sie **Token erstellen** aus.
9. Das neue Token wird in der Liste **Persönliche Zugriffstokens** angezeigt. Wählen Sie **Token kopieren**, und speichern Sie den Tokenwert für die spätere Verwendung.
10. Fahren Sie mit dem Abschnitt [Verbinden des Labs mit dem Repository](#connect-your-lab-to-the-repository) fort.

## <a name="connect-your-lab-to-the-repository"></a>Verbinden des Labs mit dem Repository
1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Weitere Dienste** und dann in der Liste der Dienste **DevTest Labs** aus.
3. Wählen Sie in der Liste mit den Labs Ihr Lab aus. 
4. Wählen Sie **Konfiguration und Richtlinien** > **Repositorys** >  **+ Hinzufügen** aus.

    ![Schaltfläche zum Hinzufügen eines Repositorys](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Geben Sie auf der zweiten Seite **Repositorys** Folgendes an:
   1. **Name**: Geben Sie einen Namen für das Repository ein.
   2. **Git-Klon-URL**. Geben Sie die Git-HTTPS-Klon-URL ein, die Sie zuvor aus GitHub oder Azure DevOps Services kopiert haben.
   3. **Branch**. Um zu Ihren Definitionen zu gelangen, geben Sie den Branch an.
   4. **Persönliches Zugriffstoken**. Geben Sie das persönliche Zugriffstoken ein, das Sie zuvor aus GitHub oder Azure DevOps Services abgerufen haben.
   5. **Ordnerpfade**. Geben Sie mindestens einen Ordnerpfad relativ zur Klon-URL an, der die Artefakt- oder Resource Manager-Vorlagendefinitionen enthält. Wenn Sie ein Unterverzeichnis angeben, müssen Sie den Schrägstrich im Ordnerpfad angeben.

      ![Bereich „Repositorys“](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Wählen Sie **Speichern** aus.

### <a name="related-blog-posts"></a>Verwandte Blogbeiträge
* [Beheben von Problemen mit fehlerhaften Artefakten in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Einbinden einer VM in eine vorhandene Active Directory-Domäne mithilfe einer Resource Manager-Vorlage in DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Ihr privates Git-Repository erstellt haben, können Sie nach Bedarf eine oder beide der folgenden Aktionen ausführen:
* Speichern Sie Ihre [benutzerdefinierten Artefakte](devtest-lab-artifact-author.md). Sie können sie später für die Erstellung neuer VMs verwenden.
* [Erstellen Sie Umgebungen mit mehreren VMs und PaaS-Ressourcen mit Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md). Anschließend können Sie die Vorlagen in Ihrem privaten Repository speichern.

Wenn Sie einen virtuellen Computer erstellen, können Sie überprüfen, ob die Artefakte oder Vorlagen Ihrem Git-Repository hinzugefügt werden. Sie sind sofort in der Liste der Artefakte oder Vorlagen verfügbar. Der Name Ihres privaten Repositorys wird in der Spalte angezeigt, die die Quelle angibt. 
