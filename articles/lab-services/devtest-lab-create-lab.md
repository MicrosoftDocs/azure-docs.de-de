---
title: Erstellen eines Labs in Azure DevTest Labs | Microsoft Docs
description: Erstellen eines Labs in Azure DevTest Labs für virtuelle Computer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8b6d3e70-6528-42a4-a2ef-449575d0f928
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: c54b97bdf69908f32015631a9e527c6e289d1d2a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60202414"
---
# <a name="create-a-lab-in-azure-devtest-labs"></a>Erstellen eines Labs in Azure DevTest Labs
Ein Lab in Azure DevTest Labs ist die Infrastruktur, die eine Gruppe von Ressourcen wie z.B. virtuelle Computer (VMs) umfasst, mit der Sie diese Ressourcen besser verwalten können, indem Sie Einschränkungen und Kontingente angeben. Dieser Artikel beschreibt das Erstellen eines Labs mit dem Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen
Zum Erstellen eines Labs benötigen Sie Folgendes:

* Ein Azure-Abonnement. Weitere Informationen zu den Azure-Kaufoptionen finden Sie unter [Azure erwerben](https://azure.microsoft.com/pricing/purchase-options/) oder [Kostenlose Testversion (1 Monat)](https://azure.microsoft.com/pricing/free-trial/). Zum Erstellen des Labs müssen Sie der Besitzer des Abonnements sein.

## <a name="steps-to-create-a-lab-in-azure-devtest-labs"></a>Schritte zum Erstellen eines Labs in Azure DevTest Labs
Die folgenden Schritte beschreiben, wie Sie mithilfe des Azure-Portals ein Lab in Azure DevTest Labs erstellen. 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Klicken Sie im Hauptmenü auf der linken Seite auf **Alle Dienste** (am Anfang der Liste). Wählen Sie im Abschnitt **DEVOPS** das Sternchen (*) neben **DevTest Labs** aus. Mit dieser Aktion wird **DevTest Labs** dem linken Navigationsmenü hinzugefügt, sodass Sie beim nächsten Mal einfach darauf zugreifen können. 

    ![All services (Alle Dienste) – Auswählen von „DevTest Labs“](./media/devtest-lab-create-lab/all-services-select.png)
2. Wählen Sie nun **DevTest Labs** im linken Navigationsmenü aus. Wählen Sie auf der Symbolleiste die Option **Hinzufügen** aus. 
   
    ![Hinzufügen eines Labs](./media/devtest-lab-create-lab/add-lab-button.png)
1. Führen Sie auf der Seite **DevTest Lab erstellen** die folgenden Schritte aus: 
    1. Geben Sie einen **Namen** für das Lab ein.
    2. Wählen Sie das **Abonnement** aus, das mit dem Lab verknüpft werden soll.
    3. Geben Sie einen **Namen für die Ressourcengruppe** für das Lab ein. 
    4. Wählen Sie einen **Speicherort** für das Lab aus.
    4. Geben Sie mithilfe von **Auto-shutdown** (Automatisch herunterfahren) an, ob Sie das automatische Herunterfahren für alle virtuellen Computer im Lab aktivieren und die entsprechenden Parameter festlegen möchten. Die Funktion zum automatischen Herunterfahren dient in erster Linie zum Kostensparen. Mit dieser Funktion können Sie angeben, wann der virtuelle Computer automatisch heruntergefahren werden soll. Nach dem Erstellen des Labs können Sie die Einstellungen zum automatischen Herunterfahren ändern, indem Sie die Schritte im Artikel [Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md#set-auto-shutdown) ausführen.
    1. Geben Sie **NAME** und **WERT** für **Tags** ein, wenn Sie ein benutzerdefiniertes Tagging erstellen möchten, das jeder im Lab erstellten Ressource hinzugefügt werden soll. Tags sind nützlich zum Verwalten und Organisieren von Lab-Ressourcen nach Kategorie. Weitere Informationen zu Tags, z.B. zum Hinzufügen von Tags nach der Erstellung des Labs, finden Sie unter [Hinzufügen von Tags zu einem Lab](devtest-lab-add-tag.md).
    6. Wählen Sie **Automatisierungsoptionen**, um Azure Resource Manager-Vorlagen für die Konfigurationsautomatisierung abzurufen. 
    7. Klicken Sie auf **Erstellen**. Sie können den Status der Lab-Erstellung im Bereich **Benachrichtigungen** überwachen. 
    
        ![Erstellen eines Lab-Abschnitts für DevTest Labs](./media/devtest-lab-create-lab/create-devtestlab-blade.png)
    8. Wählen Sie nach Abschluss der Erstellung die Option **Zu Ressource wechseln** in der Benachrichtigung aus. Alternativ können Sie die Seite **DevTest Labs** aktualisieren, um das neu erstellte Lab in der Liste der Labs anzuzeigen.  Wählen Sie das Lab in der Liste aus. Die Startseite des Labs wird angezeigt. 

        ![Startseite für das Lab](./media/devtest-lab-create-lab/lab-home-page.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nächste Schritte
Nachdem das Lab erstellt wurde, sollten Sie ggf. die folgenden Schritte ausführen:

* [Sicherer Zugriff auf ein Lab](devtest-lab-add-devtest-user.md)
* [Festlegen von Labrichtlinien](devtest-lab-set-lab-policy.md)
* [Erstellen einer Labvorlage](devtest-lab-create-template.md)
* [Erstellen benutzerdefinierter Artefakte für Ihre VMs](devtest-lab-artifact-author.md)
* [Hinzufügen eines virtuellen Computers zu einem Lab](devtest-lab-add-vm.md)

