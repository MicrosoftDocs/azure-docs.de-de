---
title: Erstellen und Verschlüsseln eines virtuellen Linux-Computers mit dem Azure-Portal
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe des Azure-Portals einen virtuellen Linux-Computer erstellen und verschlüsseln.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 10/02/2019
ms.openlocfilehash: 13e6077ee9bd24cdfc9a7c1f405199989546680f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896020"
---
# <a name="quickstart-create-and-encrypt-a-virtual-machine-with-the-azure-portal"></a>Schnellstart: Erstellen und Verschlüsseln eines virtuellen Linux-Computers mit dem Azure-Portal

Virtuelle Azure-Computer (VMs) können über das Azure-Portal erstellt werden. Das Azure-Portal ist eine browserbasierte Benutzeroberfläche, über die Sie virtuelle Computer und die dazugehörigen Ressourcen erstellen können. In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um einen virtuellen Linux-Computer (VM) unter Ubuntu 18.04 LTS bereitzustellen, einen Schlüsseltresor zum Speichern von Verschlüsselungsschlüsseln zu erstellen und den virtuellen Computer zu verschlüsseln.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Klicken Sie links oben im Azure-Portal auf **Ressource erstellen** .
1. Wählen Sie auf der Seite „Neu“ unter „Beliebt“ die Option **Ubuntu Server 18.04 LTS** aus.
1. Stellen Sie sicher, dass auf der Registerkarte „Grundlagen“ unter „Projektdetails“ das richtige Abonnement ausgewählt ist.
1. Wählen Sie für „Ressourcengruppe“ die Option **Neu erstellen** aus. Geben Sie als Name *myResourceGroup* ein, und wählen Sie **OK** aus.
1. Geben Sie unter **Name des virtuellen Computers** den Namen *MyVM* ein.
1. Wählen Sie unter **Region** die Option *(USA) USA, Osten* aus.
1. Stellen Sie sicher, dass als **Größe** die Option *Standard D2s v3* festgelegt ist.
1. Wählen Sie unter **Administratorkonto** für **Authentifizierungstyp** die Option *Kennwort* aus. Geben Sie einen Benutzernamen und ein Kennwort ein.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-linux-vm-creation.png" alt-text="Bildschirm: Erstellung der Linux-VM":::

    > [!WARNING]
    > Auf der Registerkarte „Datenträger“ befindet sich unter **Datenträgeroptionen** das Feld „Verschlüsselungstyp“. Mit diesem Feld werden Verschlüsselungsoptionen für [Verwaltete Datenträger](../managed-disks-overview.md) und CMK, aber **nicht** für Azure Disk Encryption angegeben.
    >
    > Um Verwirrung zu vermeiden, empfiehlt es sich, die Registerkarte *Datenträger* bei diesem Tutorial vollständig zu überspringen.

1. Wählen Sie die Registerkarte „Verwaltung“ aus, und vergewissern Sie sich, dass Sie ein Diagnosespeicherkonto besitzen. Falls Sie keine Speicherkonten besitzen, wählen Sie *Neu erstellen* aus, geben Sie den Namen *myStorageAccount* für Ihr Speicherkonto ein, und wählen Sie „OK“ aus.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-vm-creation-storage.png" alt-text="Bildschirm: Erstellung der Linux-VM":::

1. Klicken Sie auf „Überprüfen und erstellen“.
1. Auf der Seite zum **Erstellen eines virtuellen Computers** werden die Details des virtuellen Computers angezeigt, den Sie erstellen möchten. Klicken Sie auf **Erstellen** , wenn Sie so weit sind.

Die Bereitstellung des virtuellen Computers dauert ein paar Minuten. Fahren Sie nach Abschluss der Bereitstellung mit dem nächsten Abschnitt fort.

## <a name="encrypt-the-virtual-machine"></a>Verschlüsseln des virtuellen Computers

1. Wählen Sie nach Abschluss der Bereitstellung des virtuellen Computers die Option **Zu Ressource wechseln** aus.
1. Wählen Sie auf der linken Seitenleiste **Datenträger** aus.
1. Wählen Sie auf der oberen Leiste **Zusätzliche Einstellungen** aus.
1. Wählen Sie unter **Verschlüsselungseinstellungen** > **Zu verschlüsselnde Datenträger** die Option **Datenträger für Betriebssystem und Daten** aus.

    :::image type="content" source="../media/disk-encryption/portal-quickstart-disks-to-encryption.png" alt-text="Bildschirm: Erstellung der Linux-VM":::

1. Wählen Sie unter **Verschlüsselungseinstellungen** die Option **Wählen Sie einen Schlüsseltresor und einen Schlüssel für die Verschlüsselung aus** aus.
1. Wählen Sie im Fenster zum **Auswählen des Schlüssels aus Azure Key Vault** die Option **Neue erstellen** aus.

    :::image type="content" source="../media/disk-encryption/portal-qs-keyvault-create.png" alt-text="Bildschirm: Erstellung der Linux-VM":::

1. Wählen Sie links von **Schlüsseltresor und Schlüssel** die Option **Klicken Sie hier, um einen Schlüssel auszuwählen.** aus.
1. Wählen Sie unter **Auswählen des Schlüssels aus Azure Key Vault** unterhalb des Felds **Schlüsseltresor** die Option **Neu erstellen** aus.
1. Vergewissern Sie sich, dass auf dem Bildschirm **Schlüsseltresor erstellen** die Ressourcengruppe *myResourceGroup* angegeben ist, und geben Sie einen Namen für Ihren Schlüsseltresor an.  Jeder Schlüsseltresor in Azure muss einen eindeutigen Namen haben.
1. Aktivieren Sie auf der Registerkarte **Zugriffsrichtlinien** das Kontrollkästchen **Azure Disk Encryption für Volumeverschlüsselung** .

    :::image type="content" source="../media/disk-encryption/portal-quickstart-keyvault-enable.png" alt-text="Bildschirm: Erstellung der Linux-VM":::

1. Klicken Sie auf **Überprüfen + erstellen** .  
1. Nachdem der Schlüsseltresor die Überprüfung bestanden hat, wählen Sie **Erstellen** aus. Sie gelangen zurück zum Fenster zum **Auswählen eines Schlüssels aus Azure Key Vault** .
1. Lassen Sie das Feld **Schlüssel** leer, und wählen Sie **Auswählen** aus.
1. Klicken Sie oben im Verschlüsselungsfenster auf **Speichern** . In einem Popup werden Sie gewarnt, dass der virtuelle Computer neu gestartet wird. Klicken Sie auf **Ja** .

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Ressourcengruppe, virtueller Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und dann „Löschen“ aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor, der für Verschlüsselungsschlüssel aktiviert wurde, und einen virtuellen Computer erstellt und den virtuellen Computer für die Verschlüsselung aktiviert.  

> [!div class="nextstepaction"]
> [Übersicht über Azure Disk Encryption](disk-encryption-overview.md)