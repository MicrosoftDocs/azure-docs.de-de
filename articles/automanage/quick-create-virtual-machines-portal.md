---
title: 'Schnellstart: Aktivieren der automatischen Azure-Verwaltung für virtuelle Computer über das Azure-Portal'
description: Hier erfahren Sie, wie Sie über das Azure-Portal schnell die automatische Verwaltung für virtuelle Computer auf einem neuen oder vorhandenen virtuellen Computer aktivieren.
author: ju-shim
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 09/04/2020
ms.author: jushiman
ms.openlocfilehash: 69f43b626bb50171ceaca1b7a8761bec040d1dd6
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897227"
---
# <a name="quickstart-enable-azure-automanage-for-virtual-machines-in-the-azure-portal"></a>Schnellstart: Aktivieren der automatischen Azure-Verwaltung für virtuelle Computer im Azure-Portal

Beginnen Sie mit der automatischen Azure-Verwaltung für virtuelle Computer, indem Sie über das Azure-Portal die automatische Verwaltung für einen neuen oder vorhandenen virtuellen Computer aktivieren.


## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die virtuellen Computer möglich, die in diesem Tutorial verwendet werden. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.

> [!IMPORTANT]
> Sie müssen über die Rolle **Mitwirkender** für die Ressourcengruppe verfügen, die Ihre virtuellen Computer enthält, um Automanage mithilfe eines vorhandenen Automanage-Kontos zu aktivieren. Wenn Sie automatische Verwaltung mit einem neuen Konto für die automatische Verwaltung aktivieren, benötigen Sie die folgenden Berechtigungen: Rolle **Besitzer** oder **Mitwirkender** sowie Rollen vom Typ **Benutzerzugriffsadministrator** für Ihr Abonnement.


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.


## <a name="enable-automanage-for-vms-on-an-existing-vm"></a>Aktivieren der automatischen Verwaltung für virtuelle Computer auf einem vorhandenen virtuellen Computer

1. Suchen Sie über die Suchleiste nach **Automatische Verwaltung – Best Practices für Azure-VMs** , und wählen Sie die Option aus.

2. Wählen Sie **Für vorhandene VM aktivieren** aus.

    :::image type="content" source="media\quick-create-virtual-machine-portal\zero-vm-list-view.png" alt-text="Für vorhandene VM aktivieren":::

3. Gehen Sie auf dem Blatt **Computer auswählen** wie folgt vor:
    1. Filtern Sie die Liste der virtuellen Computer nach Ihrem **Abonnement** und nach der **Ressourcengruppe**.
    1. Aktivieren Sie das Kontrollkästchen für jeden virtuellen Computer, den Sie integrieren möchten.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-select-machine.png" alt-text="Auswählen eines vorhandenen virtuellen Computers aus der Liste verfügbarer virtueller Computer":::

4. Klicken Sie unter **Configuration Profile** (Konfigurationsprofil) auf **Browse and change profiles and preferences** (Profile und Einstellungen durchsuchen und ändern).

    :::image type="content" source="media\quick-create-virtual-machine-portal\existing-vm-quick-create.png" alt-text="Browse and change profiles and preferences (Profile und Einstellungen durchsuchen und ändern)":::

5. Gehen Sie auf dem Blatt **Konfigurationsprofil und -einstellungen auswählen** wie folgt vor:
    1. Wählen Sie auf der linken Seite ein Profil aus: *Dev/Test* für Tests, *Prod* für die Produktion.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Navigieren zum Produktionskonfigurationsprofil":::

6. Klicken Sie auf die Schaltfläche **Aktivieren**.


## <a name="enable-automanage-for-vms-on-a-new-vm"></a>Aktivieren der automatischen Verwaltung für virtuelle Computer auf einem neuen virtuellen Computer

Melden Sie sich [hier](https://aka.ms/automanageportalnextstep) beim Azure-Portal an, um einen neuen virtuellen Computer zu erstellen und automatische Verwaltung zu aktivieren.

1. Führen Sie die Schritte zur Erstellung unter [Schnellstart: Erstellen eines virtuellen Windows-Computers im Azure-Portal](..\virtual-machines\windows\quick-create-portal.md) aus.

2. Nach der Bereitstellung des virtuellen Computers gelangen Sie zur Seite mit dem Bereitstellungsstatus. Dort finden Sie ganz unten den Bereich **Nächste Schritte** mit Empfehlungen für die nächsten Schritte.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-next-steps.png" alt-text="Abschnitt „Nächste Schritte“ unten auf der Bereitstellungsseite":::

3. Wählen Sie unter **Nächste Schritte** die Option **Best Practices zum Aktivieren der automatischen VM-Verwaltung** aus.

4. Auf der Seite **Automatische Verwaltung – Best Practices für Azure-VMs** wird **Computer** automatisch mit Ihrem neu erstellten virtuellen Computer aufgefüllt.

    :::image type="content" source="media\quick-create-virtual-machine-portal\create-new-enable-overview.png" alt-text="Der neu erstellte virtuelle Computer wird als ausgewählter Computer angezeigt.":::

5. Klicken Sie unter **Configuration Profile** (Konfigurationsprofil) auf **Browse and change profiles and preferences** (Profile und Einstellungen durchsuchen und ändern).

6. Gehen Sie auf dem Blatt **Konfigurationsprofil und -einstellungen auswählen** wie folgt vor:
    1. Wählen Sie auf der linken Seite ein Profil aus: *Dev/Test* für Tests, *Prod* für die Produktion.
    1. Klicken Sie auf die Schaltfläche **Auswählen**.

    :::image type="content" source="media\quick-create-virtual-machine-portal\browse-production-profile.png" alt-text="Navigieren zum Produktionskonfigurationsprofil":::

7. Klicken Sie auf die Schaltfläche **Aktivieren**.

## <a name="disable-automanage-for-vms"></a>Deaktivieren der automatischen Verwaltung für virtuelle Computer

Sie können die Verwendung der automatischen Azure-Verwaltung für virtuelle Computer schnell beenden, indem Sie die automatische Verwaltung deaktivieren.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Deaktivieren der automatischen Verwaltung auf einem virtuellen Computer":::

1. Navigieren Sie zur Seite **Automatische Verwaltung – Best Practices für Azure-VMs** , auf der alle automatisch verwalteten virtuellen Computer aufgeführt sind.
1. Aktivieren Sie das Kontrollkästchen neben dem virtuellen Computer, den Sie deaktivieren möchten.
1. Klicken Sie auf die Schaltfläche **Automatische Verwaltung deaktivieren**.
1. Lesen Sie die Meldung im angezeigten Popupelement sorgfältig durch, bevor Sie dem **Deaktivieren** zustimmen.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie eine neue Ressourcengruppe erstellt haben, um die automatische Azure-Verwaltung für virtuelle Computer zu testen, und die Ressourcengruppe nicht mehr benötigen, können Sie sie löschen. Wenn Sie die Gruppe löschen, werden auch der virtuelle Computer und alle Ressourcen in der Ressourcengruppe gelöscht.

Die automatische Azure-Verwaltung erstellt Standardressourcengruppen zum Speichern von Ressourcen. Überprüfen Sie die Ressourcengruppen mit der Namenskonvention „DefaultResourceGroupRegionName“ und „AzureBackupRGRegionName“, um alle Ressourcen zu bereinigen.

1. Wählen Sie die **Ressourcengruppe** aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Löschen** aus.
1. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und wählen Sie dann **Löschen** aus.


## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die automatische Azure-Verwaltung für virtuelle Computer aktiviert. 

Erfahren Sie, wie Sie angepasste Einstellungen erstellen und anwenden können, wenn Sie die automatische Verwaltung auf virtuellen Computern aktivieren. 

> [!div class="nextstepaction"]
> [Automatische Azure-Verwaltung für virtuelle Computer: Benutzerdefiniertes Konfigurationsprofil](virtual-machines-custom-preferences.md)
