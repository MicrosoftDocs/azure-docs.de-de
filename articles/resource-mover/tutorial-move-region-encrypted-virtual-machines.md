---
title: Verschieben verschlüsselter virtueller Azure-Computer zwischen Regionen mit Azure Resource Mover
description: Hier erfahren Sie, wie Sie verschlüsselte virtuelle Azure-Computer mit Azure Resource Mover in eine andere Region verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0bc70e14e341d9681c75933455eae6b0278724ca
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981872"
---
# <a name="tutorial-move-encrypted-azure-vms-across-regions"></a>Tutorial: Verschieben verschlüsselter virtueller Azure-Computer zwischen Regionen

In diesem Artikel erfahren Sie, wie Sie verschlüsselte virtuelle Azure-Computer mithilfe von [Azure Resource Mover](overview.md) in eine andere Azure-Region verschieben. Mit Verschlüsselung ist Folgendes gemeint:

- Virtuelle Computer mit Datenträgern, für die Azure Disk Encryption aktiviert ist. [Weitere Informationen](../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- Oder: Virtuelle Computer, bei denen kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMKs) für die Verschlüsselung ruhender Daten (serverseitige Verschlüsselung) verwendet werden. [Weitere Informationen](../virtual-machines/disks-enable-customer-managed-keys-portal.md)


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen 
> * Bei virtuellen Computern, für die Azure Disk Encryption aktiviert ist: Kopieren von Schlüsseln und Geheimnissen aus dem Schlüsseltresor der Quellregion in den Schlüsseltresor der Zielregion
> * Vorbereiten der Verschiebung von virtuellen Computern und Auswählen zu verschiebender Ressourcen in der Quellregion
> * Auflösen von Ressourcenabhängigkeiten
> * Bei virtuellen Computern, für die Azure Disk Encryption aktiviert ist: Manuelles Zuweisen des Zielschlüsseltresors. Bei virtuellen Computern mit serverseitiger Verschlüsselung und kundenseitig verwalteten Schlüsseln: Manuelles Zuweisen eines Datenträgerverschlüsselungssatzes in der Zielregion.
> * Verschieben des Schlüsseltresors und/oder des Datenträgerverschlüsselungssatzes
> * Vorbereiten und Verschieben der Quellressourcengruppe 
> * Vorbereiten und Verschieben der anderen Ressourcen
> * Verwerfen oder Committen der Verschiebung 
> * Optionales Entfernen von Ressourcen in der Quellregion nach der Verschiebung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz. 

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.

## <a name="prerequisites"></a>Voraussetzungen

**Anforderung** |**Details**
--- | ---
**Abonnementberechtigungen** | Überprüfen Sie, ob Sie *Besitzerzugriff* auf das Abonnement haben, das die zu verschiebenden Ressourcen enthält.<br/><br/> **Warum benötige ich Besitzerzugriff?** Wenn Sie zum ersten Mal eine Ressource für ein bestimmtes Quelle-Ziel-Paar in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird. Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender und Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, *Besitzerberechtigungen* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
**VM-Support** | Überprüfen Sie, ob die virtuellen Computer unterstützt werden, die Sie verschieben möchten.<br/><br/> - Informationen zu unterstützten virtuellen Windows-Computern finden Sie [hier](support-matrix-move-region-azure-vm.md#windows-vm-support).<br/><br/> - Informationen zu unterstützten virtuellen Linux-Computern und Kernel-Versionen finden Sie [hier](support-matrix-move-region-azure-vm.md#linux-vm-support).<br/><br/> - Überprüfen Sie die unterstützten Einstellungen für [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [Speicher](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) und [Netzwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
**Schlüsseltresoranforderungen (Azure Disk Encryption)** | Wenn Azure Disk Encryption für virtuelle Computer aktiviert ist, ist neben dem Schlüsseltresor in der Quellregion auch ein Schlüsseltresor in der Zielregion erforderlich. [Erstellen Sie einen Schlüsseltresor.](../key-vault/general/quick-create-portal.md)<br/><br/> Für die Schlüsseltresore in der Quell- und Zielregion benötigen Sie folgende Berechtigungen:<br/><br/> - Schlüsselberechtigungen: Schlüsselverwaltungsvorgänge (Abrufen, Auflisten), kryptografische Vorgänge (Entschlüsseln und Verschlüsseln)<br/><br/> - Geheimnisberechtigungen: Verwaltungsvorgänge für Geheimnisse (Abrufen, Auflisten und Festlegen)<br/><br/> - Zertifikat (Auflisten und Abrufen)
**Datenträgerverschlüsselungssatz (serverseitige Verschlüsselung mit CMK)** | Wenn Sie virtuelle Computer mit serverseitiger Verschlüsselung unter Verwendung eines kundenseitig verwalteten Schlüssels (Customer-Managed Key, CMK) verwenden, benötigen Sie neben dem Datenträgerverschlüsselungssatz in der Quellregion auch einen Datenträgerverschlüsselungssatz in der Zielregion. [Erstellen Sie einen Datenträgerverschlüsselungssatz.](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set)<br/><br/> Wenn Sie für kundenseitig verwaltete Schlüssel HSM-Schlüssel verwenden, wird das Verschieben zwischen Regionen nicht unterstützt.
**Kontingent für die Zielregion** | Das Abonnement benötigt ein ausreichendes Kontingent zum Erstellen der Ressourcen, die Sie in die Zielregion verschieben. Wenn kein ausreichendes Kontingent vorhanden ist, [fordern Sie eine Heraufsetzung des Kontingents an](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Gebühren für die Zielregion** | Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie virtuelle Computer verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).


## <a name="verify-key-vault-permissions-azure-disk-encryption"></a>Überprüfen der Schlüsseltresorberechtigungen (Azure Disk Encryption)

Wenn Sie virtuelle Computer verschieben möchten, für die Azure Disk Encryption aktiviert ist, überprüfen Sie die Berechtigungen in den Schlüsseltresoren der Quell- und Zielregion, um sicherzustellen, dass verschlüsselte virtuelle Computer erwartungsgemäß verschoben werden können, und legen Sie bei Bedarf entsprechende Berechtigungen fest. 

1. Öffnen Sie im Azure-Portal den Schlüsseltresor in der Quellregion.
2. Wählen Sie unter **Einstellungen** die Option **Zugriffsrichtlinien** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png" alt-text="Schaltfläche zum Öffnen der Zugriffsrichtlinien für den Schlüsseltresor" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/key-vault-access-policies.png":::

3. Sollten keine Benutzerberechtigungen vorhanden sein, wählen Sie **Zugriffsrichtlinie hinzufügen** aus, und geben Sie die Berechtigungen an. Ist für das Benutzerkonto bereits eine Richtlinie vorhanden, legen Sie unter **Benutzer** die Berechtigungen fest.

    - Wenn für virtuelle Computer, die Sie verschieben möchten, Azure Disk Encryption (ADE) aktiviert ist, wählen Sie unter **Schlüsselberechtigungen** > **Schlüsselverwaltungsvorgänge** die Berechtigungen **Abrufen** und **Auflisten** aus, sofern sie noch nicht ausgewählt sind.
    - Wenn Sie kundenseitig verwaltete Schlüssel (Customer-Managed Keys, CMKs) verwenden, um Datenträgerverschlüsselungsschlüssel für die Verschlüsselung ruhender Daten (serverseitige Verschlüsselung) zu verschlüsseln, wählen Sie unter **Schlüsselberechtigungen** > **Schlüsselverwaltungsvorgänge** die Berechtigungen **Abrufen** und **Auflisten** aus. Wählen Sie außerdem unter **Kryptografische Vorgänge** die Berechtigungen **Entschlüsseln** und **Verschlüsseln** aus.
 
    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/set-vault-permissions.png" alt-text="Dropdownliste zum Auswählen von Schlüsseltresorberechtigungen" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/set-vault-permissions.png":::

4. Wählen Sie unter **Geheimnisberechtigungen** > **Verwaltungsvorgänge für Geheimnisse** die Berechtigungen **Abrufen**, **Auflisten** und **Festlegen** aus. 
5. Wenn Sie Berechtigungen für ein neues Benutzerkonto zuweisen, wählen Sie unter **Prinzipal auswählen** den Benutzer aus, dem Sie Berechtigungen zuweisen möchten.
6. Vergewissern Sie sich unter **Zugriffsrichtlinien**, dass **Azure Disk Encryption für Volumeverschlüsselung** aktiviert ist.
7. Wiederholen Sie die Schritte für den Schlüsseltresor in der Zielregion.


### <a name="copy-the-keys-to-the-destination-key-vault"></a>Kopieren der Schlüssel in den Zielschlüsseltresor

Die Verschlüsselungsgeheimnisse und -schlüssel aus dem Quellschlüsseltresor müssen mithilfe eines von uns bereitgestellten Skripts in den Zielschlüsseltresor kopiert werden.

- Das Skript wird in PowerShell ausgeführt. Wir empfehlen, die neueste PowerShell-Version zu verwenden.
- Für das Skript sind folgende Module erforderlich:
    - Az.Compute
    - Az.KeyVault (Version 3.0.0)
    - Az.Accounts (Version 2.2.3)

Führen Sie die folgenden Schritte aus:

1. Navigieren Sie auf GitHub zu dem [Skript](https://raw.githubusercontent.com/AsrOneSdk/published-scripts/master/CopyKeys/CopyKeys.ps1).
2. Kopieren Sie den Inhalt des Skripts in eine lokale Datei, und nennen Sie sie *Copy-keys.ps1*.
3. Führen Sie das Skript aus.
4. Melden Sie sich bei Azure an.
5. Wählen Sie im Popupelement **Benutzereingabe** das Quellabonnement, die Ressourcengruppe und den virtuellen Quellcomputer aus. Wählen Sie anschließend den Zielort sowie die Zieltresore für die Datenträger- und Schlüsselverschlüsselung aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/script-input.png" alt-text="Popupelement für die Eingabe der Skriptwerte" :::


6. Nach Abschluss der Skriptausführung wird auf dem Bildschirm angezeigt, dass „CopyKeys“ erfolgreich war.

## <a name="prepare-vms"></a>Vorbereiten der VMs

1. Nachdem Sie sich vergewissert haben, dass die virtuellen Computer die [Anforderungen](#prerequisites) erfüllen, müssen Sie sicherstellen, dass die virtuellen Computer, die Sie verschieben möchten, aktiviert sind. Alle VM-Datenträger, die in der Zielregion verfügbar sein sollen, müssen an den virtuellen Computer angefügt und initialisiert werden.
3. Vergewissern Sie sich, dass die virtuellen Computer über die neuesten vertrauenswürdigen Stammzertifikate sowie über eine aktualisierte Zertifikatssperrliste (Certificate Revocation List, CRL) verfügen. Dazu ist Folgendes erforderlich:
    - Installieren Sie auf Windows-VMs die aktuellen Windows-Updates.
    - Befolgen Sie auf Linux-VMs die Anweisungen des Distributors, damit die Computer über die aktuellen Zertifikate und CRL verfügen. 
4. Lassen Sie ausgehende Konnektivität von virtuellen Computern zu:
    - Lassen Sie den Zugriff auf die folgenden [URLs](support-matrix-move-region-azure-vm.md#url-access) zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden.
    - Wenn Sie Regeln für Netzwerksicherheitsgruppen (NSG) zum Steuern der ausgehenden Verbindung verwenden, erstellen Sie diese [Diensttagregeln](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Auswählen der zu verschiebenden Ressourcen


- Sie können einen beliebigen unterstützten Ressourcentyp aus einer beliebigen Ressourcengruppe in der ausgewählten Quellregion auswählen.  
- Sie verschieben Ressourcen in eine Zielregion, die dem gleichen Abonnement angehört wie die Quellregion. Wenn Sie das Abonnement ändern möchten, können Sie dies nach dem Verschieben der Ressourcen tun.

Gehen Sie zum Auswählen von Ressourcen wie folgt vor:

1. Suchen Sie im Azure-Portal nach *Resource Mover*. Wählen Sie anschließend unter **Dienste** die Option **Azure Resource Mover** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/search.png" alt-text="Suchergebnisse für Resource Mover im Azure-Portal" :::

2. Klicken Sie unter **Übersicht** auf **Regionsübergreifend verschieben**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png" alt-text="Schaltfläche zum Hinzufügen von Ressourcen, die in eine andere Region verschoben werden sollen" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/move-across-regions.png":::

3. Wählen Sie unter **Ressourcen verschieben** > **Source + destination** (Quelle und Ziel) das Quellabonnement und die Region aus.
4. Wählen Sie unter **Ziel** die Region aus, in die Sie die virtuellen Computer verschieben möchten. Klicken Sie dann auf **Weiter**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/source-target.png" alt-text="Seite zum Auswählen der Quell- und Zielregion" :::

5. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Ressourcen auswählen**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-resources.png" alt-text="Schaltfläche zum Auswählen der zu verschiebenden Ressourcen" :::

6. Wählen Sie unter **Ressourcen auswählen** die virtuellen Computer aus. Sie können nur Ressourcen hinzufügen, deren Verschiebung [unterstützt](#prepare-vms) wird. Klicken Sie anschließend auf **Fertig**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-vm.png" alt-text="Seite zum Auswählen der zu verschiebenden virtuellen Computer" :::

    > [!NOTE]
    >  In diesem Tutorial werden ein virtueller Computer mit serverseitiger Verschlüsselung (rayne-vm) und kundenseitig verwaltetem Schlüssel sowie ein virtueller Computer mit aktivierter Datenträgerverschlüsselung (rayne-vm-ade) ausgewählt.

7.  Klicken Sie unter **Zu verschiebende Ressourcen** auf **Weiter**.
8. Überprüfen Sie unter **Überprüfen** die Quell- und Zieleinstellungen. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/review.png" alt-text="Seite zum Überprüfen der Einstellungen und zum Fortsetzen der Verschiebung" :::

9. Klicken Sie auf **Fortsetzen**, um die Ressourcen hinzuzufügen.
10. Wählen Sie das Benachrichtigungssymbol aus, um den Status zu überwachen. Wählen Sie nach erfolgreichem Abschluss des Hinzufügens in den Benachrichtigungen die Angabe **Zu verschiebende Ressourcen wurden hinzugefügt.** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png" alt-text="Benachrichtigung zur Bestätigung des erfolgreichen Hinzufügens von Ressourcen" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/added-resources-notification.png":::
    
    
11. Nachdem Sie auf die Benachrichtigung geklickt haben, überprüfen Sie die Ressourcen auf der Seite **Across regions** (Regionsübergreifend).

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-prepare-pending.png" alt-text="Seiten mit hinzugefügten Ressourcen und ausstehender Vorbereitung" :::

> [!NOTE]
> - Von Ihnen hinzugefügte Ressourcen haben den Status *Vorbereitung ausstehend*.
> - Die Ressourcengruppe für die virtuellen Computer wird automatisch hinzugefügt.
> - Wenn Sie die Einträge für **Zielkonfiguration** ändern, um eine Ressource zu verwenden, die bereits in der Zielregion vorhanden ist, wird der Ressourcenstatus auf *Commit ausstehend* festgelegt, da für die betreffende Ressource keine Verschiebung initiiert werden muss.
> - Wenn Sie eine hinzugefügte Ressource entfernen möchten, hängt die Methode davon ab, an welchem Punkt im Verschiebevorgang Sie sich befinden. [Weitere Informationen](remove-move-resources.md)


## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten

1. Wenn für Ressourcen in der Spalte **Probleme** die Meldung *Abhängigkeiten überprüfen* angezeigt wird, wählen Sie die Schaltfläche **Abhängigkeiten überprüfen** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png" alt-text="Schaltfläche zum Überprüfen der Abhängigkeiten" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/check-dependencies.png":::

    Der Überprüfungsprozess wird daraufhin begonnen.
2. Wenn Abhängigkeiten gefunden werden, klicken Sie auf **Abhängigkeiten hinzufügen**.  

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png" alt-text="Schaltfläche zum Hinzufügen von Abhängigkeiten" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/add-dependencies.png":::


3. Übernehmen Sie unter **Abhängigkeiten hinzufügen** die Standardoption **Alle Abhängigkeiten anzeigen**.

    - Bei Verwendung von **Alle Abhängigkeiten anzeigen** werden alle direkten und indirekten Abhängigkeiten für eine Ressource durchlaufen. Für einen virtuellen Computer werden beispielsweise die NIC, das virtuelle Netzwerk, die Netzwerksicherheitsgruppen (NSGs) und Ähnliches angezeigt.
    - Bei Verwendung von **Show first level dependencies only** (Nur Abhängigkeiten der ersten Ebene anzeigen) werden nur direkte Abhängigkeiten angezeigt. Für einen virtuellen Computer wird beispielsweise die NIC, aber nicht das virtuelle Netzwerk angezeigt.
 
4. Wählen Sie die abhängigen Ressourcen, die Sie hinzufügen möchten, und anschließend **Abhängigkeiten hinzufügen** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png" alt-text="Auswählen von Abhängigkeiten aus der Abhängigkeitenliste" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-dependencies.png":::

5. Überprüfen Sie die Abhängigkeiten erneut. 

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png" alt-text="Seite für die erneute Überprüfung" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/validate-again.png":::

## <a name="assign-destination-resources"></a>Zuweisen von Zielressourcen

Verschlüsselungsbezogene Zielressourcen müssen manuell zugewiesen werden.

- Wenn Sie einen virtuellen Computer mit Azure Disk Encryption (ADE) verschieben, wird der Schlüsseltresor in der Zielregion als Abhängigkeit angezeigt.
- Wenn Sie einen virtuellen Computer mit serverseitiger Verschlüsselung und benutzerdefinierten Schlüsseln (Customer-Managed Keys, CMKs) verschieben, wird der Datenträgerverschlüsselungssatz in der Zielregion als Abhängigkeit angezeigt. 
- Da in diesem Tutorial ein virtueller Computer, für den ADE aktiviert ist, und ein virtueller Computer mit kundenseitig verwaltetem Schlüssel verschoben werden, wird sowohl der Zielschlüsseltresor als auch der Datenträgerverschlüsselungssatz als Abhängigkeit angezeigt.

Gehen Sie zur manuellen Zuweisung wie folgt vor:

1. Wählen Sie im Eintrag für den Datenträgerverschlüsselungssatz in der Spalte **Zielkonfiguration** die Option **Ressource nicht zugewiesen** aus.
2. Wählen Sie unter **Konfigurationseinstellungen** den Ziel-Datenträgerverschlüsselungssatz aus. Wählen Sie anschließend **Änderungen speichern** aus.
3. Sie können für die Ressource, die Sie ändern, „Speichern und Abhängigkeiten überprüfen“ auswählen oder die Änderungen einfach nur speichern und später alle Änderungen auf einmal überprüfen.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png" alt-text="Seite zum Auswählen des Datenträgerverschlüsselungssatzes in der Zielregion" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/select-destination-set.png":::

    Nach dem Hinzufügen der Zielressource ändert sich der Status des Datenträgerverschlüsselungssatzes in *Commit für Verschiebung ausstehend*.
3. Wählen Sie im Eintrag für den Schlüsseltresor in der Spalte **Zielkonfiguration** die Option **Ressource nicht zugewiesen** aus. Wählen Sie unter **Konfigurationseinstellungen** den Zielschlüsseltresor aus. Speichern Sie die Änderungen. 

Sowohl der Status des Datenträgerverschlüsselungssatzes als auch der Status des Schlüsseltresors lautet nun *Commit für Verschiebung ausstehend*.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png" alt-text="Seite zum Auswählen der Vorbereitung für andere Ressourcen" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-other-resources.png":::

Gehen Sie wie folgt vor, um die Verschiebung der Verschlüsselungsressourcen zu committen und den Prozess abzuschließen.

1. Wählen Sie unter **Regionsübergreifend** die Ressource (Datenträgerverschlüsselungssatz oder Schlüsseltresor) und anschließend **Commit für Verschiebung ausführen** aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Commit**.

> [!NOTE]
> Nach dem Committen der Verschiebung hat die Ressource den Status *Löschung der Quelle ausstehend*.


## <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe 

Bevor Sie virtuelle Computer vorbereiten und verschieben können, muss die VM-Ressourcengruppe in der Zielregion vorhanden sein. 

### <a name="prepare-to-move-the-source-resource-group"></a>Vorbereiten zum Verschieben der Quellressourcengruppe

Beim Vorbereitungsprozess generiert Resource Mover Azure Resource Manager-Vorlagen (ARM) mithilfe der Ressourcengruppeneinstellungen. Die Ressourcen innerhalb der Ressourcengruppe sind nicht betroffen.

Gehen Sie zur Vorbereitung wie folgt vor:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Vorbereiten** aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png" alt-text="Vorbereiten der Ressourcengruppe" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/prepare-resource-group.png":::

2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.

> [!NOTE]
> Nachdem Sie die Ressourcengruppe vorbereitet haben, weist diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf. 

 
### <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe

Initiieren Sie die Verschiebung wie folgt:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Initiate Move** (Verschiebung initiieren) aus.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png" alt-text="Schaltfläche zum Initiieren der Verschiebung" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/initiate-move-resource-group.png":::

2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren). Die Ressourcengruppe wechselt in den Status *Initiate move in progress* (Verschiebung wird initiiert).   
3. Nachdem die Verschiebung initiiert wurde, wird die Zielressourcengruppe basierend auf der generierten ARM-Vorlage erstellt. Die Quellressourcengruppe wechselt in den Status *Commit move pending* (Committen der Verschiebung ausstehend).

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png" alt-text="Überprüfen des Status „Commit für Verschiebung ausstehend“" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-commit-move-pending.png":::

So committen Sie die Verschiebung und schließen sie ab:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Commit move** (Verschiebung committen) aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Commit**.

> [!NOTE]
> Nach dem Committen der Verschiebung befindet sich die Quellressourcengruppe im Status *Delete source pending* (Löschen der Quelle ausstehend).

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png" alt-text="Überprüfen des Status „Löschung der Quelle ausstehend“" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resource-group-delete-move-pending.png":::

## <a name="prepare-resources-to-move"></a>Vorbereiten der Ressource zum Verschieben

Nach dem Verschieben der Verschlüsselungsressourcen und der Quellressourcengruppe können Sie als Nächstes die Verschiebung anderer Ressourcen mit dem Status *Vorbereitung ausstehend* vorbereiten.


1. Führen Sie unter **Regionsübergreifend** eine erneute Überprüfung durch, und beheben Sie ggf. vorhandene Probleme.
2. Wenn Sie die Zieleinstellungen vor dem Verschieben bearbeiten möchten, wählen Sie den Link in der Spalte **Destination configuration** (Zielkonfiguration) der Ressource aus, und bearbeiten Sie die Einstellungen. Wenn Sie die Ziel-VM-Einstellungen bearbeiten, darf die Größe der Ziel-VM nicht kleiner sein als die Größe der Quell-VM.
3. Wählen Sie für Ressourcen mit dem Status *Vorbereitung ausstehend*, die Sie verschieben möchten, die Option **Vorbereiten** aus.
3. Wählen Sie unter **Ressourcen vorbereiten** die Option **Vorbereiten** aus.

    - Während des Vorbereitens wird der Mobilitäts-Agent von Azure Site Recovery auf virtuellen Computern installiert, um diese zu replizieren.
    - VM-Daten werden in regelmäßigen Abständen in die Zielregion repliziert. Dies wirkt sich nicht auf die Quell-VM aus.
    - Beim Verschieben von Ressourcen werden ARM-Vorlagen für die anderen Quellressourcen generiert.

Nachdem Sie die Ressourcen vorbereitet haben, weisen diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png" alt-text="Seite mit Ressourcen im Status „Einleitung der Verschiebung ausstehend“" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-initiate-move-pending.png":::



## <a name="initiate-the-move"></a>Initiieren der Verschiebung

Nachdem die Ressourcen vorbereitet wurden, können Sie die Verschiebung initiieren. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) aus. Klicken Sie anschließend auf **Initiate move** (Verschiebung initiieren).
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren).
3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.

    - Bei virtuellen Computern werden Replikat-VMs in der Zielregion erstellt. Die Quell-VM wird heruntergefahren, und es kommt zu einer gewissen Downtime (in der Regel einige Minuten).
    - Resource Mover erstellt mithilfe der vorbereiteten ARM-Vorlagen andere Ressourcen neu. Dabei gibt es in der Regel keine Downtime.
    - Nachdem Sie die Ressourcen verschoben haben, weisen diese den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.

:::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" alt-text="Seite mit Ressourcen im Status „Commit für Verschiebung ausstehend“" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move-pending.png" :::


## <a name="discard-or-commit"></a>Verwerfen oder committen?

Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie einen Commit für die Verschiebung durchführen oder sie verwerfen möchten. 

- **Verwerfen**: Sie können eine Verschiebung verwerfen, wenn Sie Tests durchführen und die Quellressource letztendlich nicht verschoben werden soll. Beim Verwerfen der Verschiebung wird die Ressource in den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) zurückgesetzt.
- **Commit**: Beim Commit wird die Verschiebung in die Zielregion abgeschlossen. Nach dem Commit hat eine Quellressource den Status *Delete source pending* (Löschen der Quelle ausstehend), und Sie können entscheiden, ob sie gelöscht werden soll.


## <a name="discard-the-move"></a>Verwerfen der Verschiebung 

Sie können eine Verschiebung wie folgt verwerfen:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Commit move pending* (Committen der Verschiebung ausstehend) aus, und klicken Sie auf **Discard move** (Verschiebung verwerfen).
2. Klicken Sie unter **Discard move** (Verschiebung verwerfen) auf **Verwerfen**.
3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.


> [!NOTE]
> Nachdem Sie die Ressourcen verworfen haben, weisen die virtuellen Computer den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

## <a name="commit-the-move"></a>Committen der Verschiebung

Wenn Sie die Verschiebung abschließen möchten, führen Sie einen Commit aus. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Commit move pending* (Committen der Verschiebung ausstehend) aus, und klicken Sie auf **Commit move** (Verschiebung committen).
2. Klicken Sie unter **Commit resources** (Ressourcen committen) auf **Commit**.

    :::image type="content" source="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" alt-text="Seite zum Committen von Ressourcen, um die Verschiebung abzuschließen" lightbox="./media/tutorial-move-region-encrypted-virtual-machines/resources-commit-move.png" :::

3. Verfolgen Sie den Fortschritt des Commits in der Benachrichtigungsleiste nach.

> [!NOTE]
> - Nach dem Committen der Verschiebung wird die Replikation der virtuellen Computer beendet. Der Commit hat keinen Einfluss auf die Quell-VM.
> - Der Commit wirkt sich nicht auf die Ressourcen des Quellnetzwerks aus.
> - Nach dem Committen der Verschiebung befinden sich die Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend).



## <a name="configure-settings-after-the-move"></a>Konfigurieren der Einstellungen nach dem Verschieben

- Der Mobilitätsdienst wird auf virtuellen Computern nicht automatisch deinstalliert. Deinstallieren Sie ihn manuell, oder lassen Sie ihn installiert, wenn Sie planen, den Server erneut zu verschieben.
- Ändern Sie nach dem Verschieben die Regeln der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC).

## <a name="delete-source-resources-after-commit"></a>Löschen von Quellressourcen nach einem Commit

Nach dem Verschieben können Sie die Ressourcen in der Quellregion löschen, dies ist jedoch optional. 

1. Wählen Sie unter **Regionsübergreifend** die einzelnen Quellressourcen aus, die Sie löschen möchten. Wählen Sie anschließend **Quelle löschen** aus.
2. Überprüfen Sie unter **Quelle löschen** die zu löschenden Elemente, und geben Sie unter **Löschvorgang bestätigen** den Text **Ja** ein. Die Aktion kann nicht rückgängig gemacht werden. Prüfen Sie daher sorgfältig.
3. Wählen Sie nach Eingabe von **Ja** die Option **Quelle löschen** aus.

> [!NOTE]
>  Im Portal für die Ressourcenverschiebung können keine Ressourcengruppen, Schlüsseltresore oder SQL Server-Server gelöscht werden. Diese Elemente müssen einzeln über die Eigenschaftenseite der jeweiligen Ressource gelöscht werden.


## <a name="delete-additional-resources-created-for-move"></a>Löschen zusätzlicher, für das Verschieben erstellter Ressourcen

Nach dem Verschieben können Sie die Sammlung für die Verschiebung und die erstellten Site Recovery-Ressourcen manuell löschen.

- Die Sammlung für die Verschiebung ist standardmäßig ausgeblendet. Blenden Sie ausgeblendete Ressourcen ein, um sie anzuzeigen.
- Der Cachespeicher verfügt über eine Sperre, die zunächst gelöscht werden muss.

Gehen Sie beim Löschen wie folgt vor: 
1. Suchen Sie die Ressourcen in der Ressourcengruppe ```RegionMoveRG-<sourceregion>-<target-region>```.
2. Überprüfen Sie, ob alle virtuellen Computer und andere Quellressourcen in der Quellregion verschoben oder gelöscht wurden. Dadurch wird sichergestellt, dass sie nicht von ausstehenden Ressourcen verwendet werden.
2. Löschen der Ressourcen:

    - Der Name der Sammlung für die Verschiebung ist ```movecollection-<sourceregion>-<target-region>```.
    - Der Name des Cachespeichers lautet ```resmovecache<guid>```.
    - Der Tresorname ist ```ResourceMove-<sourceregion>-<target-region>-GUID```.
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial führen Sie Folgendes durch:

> [!div class="checklist"]
> * Verschieben verschlüsselter virtueller Azure-Computer und ihrer abhängigen Ressourcen in eine andere Azure-Region


Versuchen Sie als Nächstes, Azure SQL-Datenbanken und Pools für elastische Datenbanken in eine andere Region zu verschieben.

> [!div class="nextstepaction"]
> [Verschieben von Azure SQL-Ressourcen](./tutorial-move-region-sql.md)
