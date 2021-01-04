---
title: Verschieben von Azure-VMs in Verfügbarkeitszonen in einer anderen Region mit Azure Resource Mover
description: Erfahren Sie, wie Sie Azure-VMs mit Azure Resource Mover in andere Verfügbarkeitszonen verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 88006fb354af2673496c6476090d7f73c8a005e6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95542999"
---
# <a name="move-azure-vms-to-an-availability-zone-in-another-region"></a>Verschieben von Azure-VMs in eine Verfügbarkeitszone in einer anderen Region

In diesem Artikel erfahren Sie, wie Sie Azure-VMs (und zugehörige Netzwerk-/Speicherressourcen) mithilfe von [Azure Resource Mover](overview.md) in eine Verfügbarkeitszone in einer anderen Azure-Region verschieben.

[Azure-Verfügbarkeitszonen](../availability-zones/az-overview.md#availability-zones) tragen dazu bei, Ihre Azure-Bereitstellung vor Rechenzentrumsausfällen zu schützen. Jede Verfügbarkeitszone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz sind in allen [aktivierten Regionen](../availability-zones/az-region.md) mindestens drei separate Zonen vorhanden. Mit Resource Mover können Sie Folgendes verschieben:

- Eine Einzelinstanz-VM in eine Verfügbarkeitszone/Verfügbarkeitsgruppe in der Zielregion.
- Eine VM in einer Verfügbarkeitsgruppe in eine Verfügbarkeitszone/Verfügbarkeitsgruppe in der Zielregion.
- Eine VM in einer Verfügbarkeitszone der Quellregion in eine Verfügbarkeitszone in der Zielregion.


> [!IMPORTANT]
> Azure Resource Mover ist derzeit als öffentliche Vorschauversion verfügbar.

Wenn Sie VMs in eine andere Verfügbarkeitszone in der gleichen Region verschieben möchten, [lesen Sie diesen Artikel](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md).

## <a name="prerequisites"></a>Voraussetzungen

- *Besitzerzugriff* auf das Abonnement, in dem sich die zu verschiebenden Ressourcen befinden.
    - Wenn Sie zum ersten Mal eine Ressource für eine bestimmte Quelle-Ziel-Zuordnung in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird.
    - Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender oder Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, Berechtigungen als *Besitzer* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
- Das Abonnement benötigt ein ausreichendes Kontingent zum Erstellen der Quellressourcen in der Zielregion. Wenn kein ausreichendes Kontingent vorhanden ist, fordern Sie eine Heraufsetzung des Kontingents an. [Weitere Informationen](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie virtuelle Computer verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).
    


## <a name="check-vm-requirements"></a>Prüfen der VM-Anforderungen

1. Überprüfen Sie, ob die virtuellen Computer unterstützt werden, die Sie verschieben möchten.

    - [Überprüfen](support-matrix-move-region-azure-vm.md#windows-vm-support) Sie die unterstützten Windows-VMs.
    - [Überprüfen](support-matrix-move-region-azure-vm.md#linux-vm-support) Sie die unterstützten Linux-VMs und Kernel-Versionen.
    - Überprüfen Sie die unterstützten [Compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [Speicher-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) und [Netzwerkeinstellungen](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
2. Überprüfen Sie, ob die virtuellen Computer, die Sie verschieben möchten, aktiviert sind.
3. Stellen Sie sicher, dass die VMs über die neuesten vertrauenswürdigen Stammzertifikate und eine aktualisierte Zertifikatssperrliste (Certificate Revocation List, CRL) verfügen. 
    - Installieren Sie auf VMs, die Windows ausführen, die neuesten Windows-Updates.
    - Befolgen Sie auf VMs, auf denen Linux ausgeführt wird, den Leitfaden für den Linux-Distributoren, um sicherzustellen, dass der Computer über die neuesten Zertifikate und die neueste Zertifikatssperrliste verfügt. 
4. Erlauben Sie die ausgehende Verbindung von virtuellen Computern:
    - Lassen Sie den Zugriff auf die folgenden [URLs](support-matrix-move-region-azure-vm.md#url-access) zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden.
    - Wenn Sie Regeln für Netzwerksicherheitsgruppen (NSG) zum Steuern der ausgehenden Verbindung verwenden, erstellen Sie diese [Diensttagregeln](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Auswählen der zu verschiebenden Ressourcen

Wählen Sie die Ressourcen aus, die Sie verschieben möchten.

- Sie können einen beliebigen unterstützten Ressourcentyp in Ressourcengruppen in der ausgewählten Quellregion auswählen.
- Sie verschieben Ressourcen in eine Zielregion im Abonnement der Quellregion. Wenn Sie das Abonnement ändern möchten, können Sie dies nach dem Verschieben der Ressourcen tun.

1. Suchen Sie im Azure-Portal nach Resource Mover. Wählen Sie anschließend unter **Dienste** die Option **Azure Resource Mover** aus.

    ![Suchen nach Resource Mover](./media/move-region-availability-zone/search.png)

2. Wählen Sie in der **Übersicht** die Option **Erste Schritte** aus.

    ![Schaltfläche für erste Schritte](./media/move-region-availability-zone/get-started.png)

3. Wählen Sie unter **Ressourcen verschieben** > **Source + destination** (Quelle und Ziel) das Quellabonnement und die Region aus.
4. Wählen Sie unter **Ziel** die Region aus, in die Sie die virtuellen Computer verschieben möchten. Klicken Sie dann auf **Weiter**.

     ![Seite zum Angeben des Quell- und Zielabonnements bzw. der -region](./media/move-region-availability-zone/source-target.png)

6. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Ressourcen auswählen**.
7. Wählen Sie unter **Ressourcen auswählen** den virtuellen Computer aus. Sie können nur Ressourcen hinzufügen, die zum Verschieben unterstützt werden. Klicken Sie anschließend auf **Fertig**. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Weiter**.

    ![Seite zum Auswählen von zu verschiebenden virtuellen Computern](./media/move-region-availability-zone/select-vm.png)
8. Überprüfen Sie unter **Review + Add** (Überprüfen und hinzufügen) die Quell- und Zieleinstellungen.

    ![Seite zum Überprüfen der Einstellungen und zum Fortsetzen der Verschiebung](./media/move-region-availability-zone/review.png)

9. Klicken Sie auf **Fortsetzen**, um die Ressourcen hinzuzufügen.
10. Nachdem der Vorgang zum Hinzufügen erfolgreich abgeschlossen wurde, klicken Sie auf **Adding resources for move** (Zu verschiebende Ressource werden hinzugefügt) im Benachrichtigungssymbol.

    ![Meldung in Benachrichtigungen](./media/move-region-availability-zone/notification.png)

Nachdem Sie auf die Benachrichtigung geklickt haben, werden Ressourcen auf der Seite **Across regions** (Regionsübergreifend) angezeigt.

> [!NOTE]
> Nachdem Sie auf die Benachrichtigung geklickt haben, werden Ressourcen auf der Seite **Across regions** (Regionsübergreifend) im Status *Prepare pending* (Ausstehende vorbereiten) angezeigt.
> - Wenn Sie eine Ressource aus einer Sammlung für die Verschiebung entfernen möchten, hängt die Methode davon ab, an welchem Punkt im Verschiebevorgang Sie sich befinden. [Weitere Informationen](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten

1. Wenn für Ressourcen in der Spalte **Probleme** die Meldung *Abhängigkeiten überprüfen* angezeigt wird, klicken Sie auf die Schaltfläche **Abhängigkeiten überprüfen**. Der Überprüfungsvorgang beginnt.
2. Wenn Abhängigkeiten gefunden werden, klicken Sie auf **Abhängigkeiten hinzufügen**. 
3. Wählen Sie unter **Abhängigkeiten hinzufügen** die abhängigen Ressourcen und **Abhängigkeiten hinzufügen** aus. Überwachen Sie den Fortschritt in den Benachrichtigungen.

    ![Schaltfläche zum Hinzufügen von Abhängigkeiten](./media/move-region-availability-zone/add-dependencies.png)

3. Fügen Sie bei Bedarf zusätzliche Abhängigkeiten hinzu, und überprüfen Sie die Abhängigkeiten erneut. 

    ![Seite zum Hinzufügen zusätzlicher Abhängigkeiten](./media/move-region-availability-zone/add-additional-dependencies.png)

4. Überprüfen Sie auf der Seite **Across regions** (Regionsübergreifend), ob sich die Ressourcen jetzt im Status *Prepare pending* (Vorbereitung ausstehend) befinden und keine Probleme mehr aufweisen.

    ![Seite mit den Ressourcen im Status „Prepare pending“ (Vorbereitung ausstehend)](./media/move-region-availability-zone/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe 

Bevor Sie virtuelle Computer vorbereiten und verschieben können, muss die Quellressourcengruppe in der Zielregion vorhanden sein. 

### <a name="prepare-to-move-the-source-resource-group"></a>Vorbereiten zum Verschieben der Quellressourcengruppe

Gehen Sie zur Vorbereitung wie folgt vor:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Vorbereiten** aus.
2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.

    ![Schaltfläche zum Vorbereiten der Quellressourcengruppe](./media/move-region-availability-zone/prepare-resource-group.png)

    Beim Vorbereitungsprozess generiert Resource Mover Azure Resource Manager-Vorlagen (ARM) mithilfe der Ressourcengruppeneinstellungen. Die Ressourcen innerhalb der Ressourcengruppe sind nicht betroffen.

> [!NOTE]
>  Nachdem Sie die Ressourcengruppe vorbereitet haben, weist diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf. 

![Status zum Initiieren des Zustands „Ausstehend“](./media/move-region-availability-zone/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe

Initiieren Sie die Verschiebung wie folgt:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Initiate Move** (Verschiebung initiieren) aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren). Die Ressourcengruppe wechselt in den Status *Initiate move in progress* (Verschiebung wird initiiert).
3. Nachdem die Verschiebung initiiert wurde, wird die Zielressourcengruppe basierend auf der generierten ARM-Vorlage erstellt. Die Quellressourcengruppe wechselt in den Status *Commit move pending* (Committen der Verschiebung ausstehend).

![Status „Verschiebung committen“](./media/move-region-availability-zone/commit-move-pending.png)

So committen Sie die Verschiebung und schließen sie ab:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Commit move** (Verschiebung committen) aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Commit**.

> [!NOTE]
> Nach dem Committen der Verschiebung befindet sich die Quellressourcengruppe im Status *Delete source pending* (Löschen der Quelle ausstehend).


## <a name="add-a-target-availability-zone"></a>Hinzufügen der Zielverfügbarkeitszone

Bevor wir die restlichen Ressourcen verschieben, legen wir eine Zielverfügbarkeitszone für die VM fest.

1. Klicken Sie auf der Seite **Across regions** (Regionsübergreifend) auf den Link in der Spalte **Destination configuration** (Zielkonfiguration) der VM, die Sie verschieben.

    ![VM-Eigenschaften](./media/move-region-availability-zone/select-vm-settings.png)

3. Geben Sie unter **Configuration settings** (Konfigurationseinstellungen) die Einstellung für die Ziel-VM an. Sie können eine VM in der Zielregion wie folgt konfigurieren:
    -  Erstellen Sie eine neue äquivalente Ressource in der Zielregion. Mit Ausnahme der von Ihnen angegebenen Einstellungen wird die Zielressource mit denselben Einstellungen wie die Quelle erstellt.
    - Wählen Sie eine vorhandene VM in der Zielregion aus. 

4. Wählen Sie unter **Zones** (Zonen) die Zone aus, in der Sie die VM platzieren möchten, wenn sie verschoben wird.

    Änderungen werden nur für die Ressource vorgenommen, die Sie bearbeiten. Sie müssen alle abhängigen Ressourcen separat aktualisieren.

5. Geben Sie unter **SKU** die [Azure-Ebene](..//virtual-machines/sizes.md) an, die Sie der Ziel-VM zuweisen möchten.
6. Wählen Sie unter **Verfügbarkeitsgruppe** eine Verfügbarkeitsgruppe aus, wenn die Ziel-VM innerhalb einer Verfügbarkeitsgruppe in der Verfügbarkeitszone ausgeführt werden soll.
7. Klicken Sie auf **Save changes** (Änderungen speichern).

    ![VM-Einstellungen](./media/move-region-availability-zone/vm-settings.png)


## <a name="prepare-resources-to-move"></a>Vorbereiten der Ressource zum Verschieben

Nachdem die Quellressourcengruppe verschoben wurde, können Sie die Verschiebung der anderen Ressourcen vorbereiten.

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Ressourcen aus, die Sie vorbereiten möchten. 

    ![Seite zur Auswahl der Vorbereitung für andere Ressourcen](./media/move-region-availability-zone/prepare-other.png)

2. Wählen Sie **Vorbereiten** aus. 

> [!NOTE]
> - Während des Vorbereitens wird der Mobilitäts-Agent von Azure Site Recovery auf VMs für die Replikation installiert.
> - VM-Daten werden in regelmäßigen Abständen in die Zielregion repliziert. Dies wirkt sich nicht auf die Quell-VM aus.
> - Beim Verschieben von Ressourcen werden ARM-Vorlagen für die anderen Quellressourcen generiert.
> - Nachdem Sie die Ressourcen vorbereitet haben, weisen diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

![Seite mit den Ressourcen im Status „Initiate move pending“ (Initiieren der Verschiebung ausstehend)](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Initiieren der Verschiebung

Nachdem die Ressourcen vorbereitet wurden, können Sie die Verschiebung initiieren. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) aus. Klicken Sie anschließend auf **Initiate move** (Verschiebung initiieren).
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren).

    ![Seite zum Initiieren der Verschiebung von Ressourcen](./media/move-region-availability-zone/initiate-move.png)

3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.


> [!NOTE]
> - Bei virtuellen Computern werden Replikat-VMs in der Zielregion erstellt. Die Quell-VM wird heruntergefahren, und es kommt zu einer gewissen Downtime (in der Regel einige Minuten).
> - Resource Mover erstellt mithilfe der vorbereiteten ARM-Vorlagen andere Ressourcen neu. Dabei gibt es in der Regel keine Downtime.
> - Nachdem Sie Ressourcen vorbereitet haben, weisen diese den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.


![Seite zum Anzeigen von Ressourcen im Status „Commit move pending“ (Committen der Verschiebung ausstehend)](./media/move-region-availability-zone/resources-commit-move-pending.png)

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
> Nachdem Sie die Ressourcen verworfen haben, weisen diese für VMs den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

## <a name="commit-the-move"></a>Committen der Verschiebung

Wenn Sie die Verschiebung abschließen möchten, führen Sie einen Commit aus. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Commit move pending* (Committen der Verschiebung ausstehend) aus, und klicken Sie auf **Commit move** (Verschiebung committen).
2. Klicken Sie unter **Commit resources** (Ressourcen committen) auf **Commit**.

    ![Seite zum Committen von Ressourcen zum Abschließen der Verschiebung](./media/move-region-availability-zone/commit-resources.png)

3. Verfolgen Sie den Fortschritt des Commits in der Benachrichtigungsleiste nach.

> [!NOTE]
> - Nach dem Committen der Verschiebung wird die Replikation der virtuellen Computer beendet. Der Commit hat keinen Einfluss auf die Quell-VM.
> - Der Commit wirkt sich nicht auf die Ressourcen des Quellnetzwerks aus.
> - Nach dem Committen der Verschiebung befinden sich die Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend).

![Seite mit den Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend)](./media/move-region-availability-zone/delete-source-pending.png)

## <a name="configure-settings-after-the-move"></a>Konfigurieren der Einstellungen nach dem Verschieben

Der Mobilitätsdienst wird auf virtuellen Computern nicht automatisch deinstalliert. Deinstallieren Sie ihn manuell, oder lassen Sie ihn installiert, wenn Sie planen, den Server erneut zu verschieben.


## <a name="delete-source-resources-after-commit"></a>Löschen von Quellressourcen nach einem Commit

Nach dem Verschieben können Sie die Ressourcen in der Quellregion löschen, dies ist jedoch optional.

1. Klicken Sie unter **Across regions** (Regionsübergreifend) auf den Namen der einzelnen Quellressourcen, die Sie löschen möchten.
2. Wählen Sie auf der Seite „Eigenschaften“ der ausgewählten Ressource die Option **Löschen** aus.

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

[Informationen](about-move-process.md) zum Verschiebungsvorgang.