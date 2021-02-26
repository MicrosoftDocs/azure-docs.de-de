---
title: Verschieben von Azure-VMs zwischen Regionen mit Azure Resource Mover
description: Erfahren Sie, wie Sie Azure-VMs mit Azure Resource Mover in eine andere Region verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: d1ac17c93bdf95e36f68af678d2ee38b896ef1e7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979741"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Tutorial: Verschieben von Azure-VMs zwischen Regionen

In diesem Artikel erfahren Sie, wie Sie Azure-VMs und zugehörige Netzwerk-/Speicherressourcen mithilfe von [Azure Resource Mover](overview.md) in eine andere Azure-Region verschieben.
.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Prüfen der Voraussetzungen und Anforderungen
> * Auswählen der Ressourcen, die Sie verschieben möchten
> * Auflösen von Ressourcenabhängigkeiten
> * Vorbereiten und Verschieben der Quellressourcengruppe 
> * Vorbereiten und Verschieben der anderen Ressourcen
> * Verwerfen oder Committen der Verschiebung 
> * Optionales Entfernen von Ressourcen in der Quellregion nach der Verschiebung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz. 

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.

## <a name="prerequisites"></a>Voraussetzungen
**Anforderung** | **Beschreibung**
--- | ---
**Abonnementberechtigungen** | Vergewissern Sie sich, dass Sie *Besitzerzugriff* auf das Abonnement haben, das die zu verschiebenden Ressourcen enthält.<br/><br/> **Warum benötige ich Besitzerzugriff?** Wenn Sie zum ersten Mal eine Ressource für ein bestimmtes Quelle-Ziel-Paar in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird. Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender oder Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, Berechtigungen als *Besitzer* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
**VM-Support** |  Überprüfen Sie, ob die virtuellen Computer unterstützt werden, die Sie verschieben möchten.<br/><br/> - Informationen zu unterstützten virtuellen Windows-Computern finden Sie [hier](support-matrix-move-region-azure-vm.md#windows-vm-support).<br/><br/> - Informationen zu unterstützten virtuellen Linux-Computern und Kernel-Versionen finden Sie [hier](support-matrix-move-region-azure-vm.md#linux-vm-support).<br/><br/> - Überprüfen Sie die unterstützten Einstellungen für [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [Speicher](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) und [Netzwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
**Zielabonnement** | Das Abonnement in der Zielregion benötigt ein ausreichendes Kontingent zum Erstellen der Ressourcen, die Sie in die Zielregion verschieben. Wenn kein ausreichendes Kontingent vorhanden ist, [fordern Sie eine Heraufsetzung des Kontingents an](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Gebühren für die Zielregion** | Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie virtuelle Computer verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).
    

## <a name="prepare-vms"></a>Vorbereiten der VMs

1. Nachdem Sie sich vergewissert haben, dass die virtuellen Computer die Anforderungen erfüllen, müssen Sie sicherstellen, dass die virtuellen Computer, die Sie verschieben möchten, aktiviert sind. Alle VM-Datenträger, die in der Zielregion verfügbar sein sollen, müssen an den virtuellen Computer angefügt und initialisiert werden.
1. Stellen Sie sicher, dass die virtuellen Computer über die aktuellen vertrauenswürdigen Stammzertifikate und eine aktualisierte Zertifikatssperrliste (Certificate Revocation List, CRL) verfügen. Dazu ist Folgendes erforderlich:
    - Installieren Sie auf Windows-VMs die aktuellen Windows-Updates.
    - Befolgen Sie auf Linux-VMs die Anweisungen des Distributors, damit die Computer über die aktuellen Zertifikate und CRL verfügen. 
1. Erlauben Sie die ausgehende Verbindung von virtuellen Computern:
    - Lassen Sie den Zugriff auf die folgenden [URLs](support-matrix-move-region-azure-vm.md#url-access) zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden.
    - Wenn Sie Regeln für Netzwerksicherheitsgruppen (NSG) zum Steuern der ausgehenden Verbindung verwenden, erstellen Sie diese [Diensttagregeln](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Wählen Sie die Ressourcen aus 

Wählen Sie die Ressourcen aus, die Sie verschieben möchten.

- Alle unterstützten Ressourcentypen in Ressourcengruppen innerhalb der ausgewählten Quellregion werden angezeigt.
- Ressourcen, die bereits für das regionsübergreifende Verschieben hinzugefügt wurden, werden nicht angezeigt.
- Sie verschieben Ressourcen in eine Zielregion im gleichen Abonnement wie die Quellregion. Wenn Sie das Abonnement ändern möchten, können Sie dies nach dem Verschieben der Ressourcen tun.

1. Suchen Sie im Azure-Portal nach *Resource Mover*. Wählen Sie anschließend unter **Dienste** die Option **Azure Resource Mover** aus.

    ![Suchen nach Ergebnissen für Resource Mover im Azure-Portal](./media/tutorial-move-region-virtual-machines/search.png)

2. Klicken Sie in der **Übersicht** auf **Erste Schritte**.

    ![Schaltfläche zum Hinzufügen von Ressourcen zum Verschieben in eine andere Region](./media/tutorial-move-region-virtual-machines/get-started.png)

3. Wählen Sie unter **Ressourcen verschieben** > **Source + destination** (Quelle und Ziel) das Quellabonnement und die Region aus.
4. Wählen Sie unter **Ziel** die Region aus, in die Sie die virtuellen Computer verschieben möchten. Klicken Sie dann auf **Weiter**.

    ![Seite zum Auswählen der Quell- und Zielregion](./media/tutorial-move-region-virtual-machines/source-target.png)

6. Klicken Sie unter **Zu verschiebende Ressourcen** auf **Ressourcen auswählen**.
7. Wählen Sie unter **Ressourcen auswählen** den virtuellen Computer aus. Sie können nur Ressourcen hinzufügen, [die zum Verschieben unterstützt werden](#prepare-vms). Klicken Sie anschließend auf **Fertig**.

    ![Seite zum Auswählen von zu verschiebenden virtuellen Computern](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  Klicken Sie unter **Zu verschiebende Ressourcen** auf **Weiter**.
9. Überprüfen Sie unter **Überprüfen** die Quell- und Zieleinstellungen. 

    ![Seite zum Überprüfen der Einstellungen und zum Fortsetzen der Verschiebung](./media/tutorial-move-region-virtual-machines/review.png)
10. Klicken Sie auf **Fortsetzen**, um die Ressourcen hinzuzufügen.
11. Nachdem der Vorgang zum Hinzufügen erfolgreich abgeschlossen wurde, klicken Sie auf **Adding resources for move** (Zu verschiebende Ressource werden hinzugefügt) im Benachrichtigungssymbol.
12. Nachdem Sie auf die Benachrichtigung geklickt haben, überprüfen Sie die Ressourcen auf der Seite **Across regions** (Regionsübergreifend).

> [!NOTE]
> - Hinzugefügte Ressourcen haben den Status *Prepare pending* (Vorbereitung ausstehend).
> - Die Ressourcengruppe für die virtuellen Computer wird automatisch hinzugefügt.
> - Wenn Sie eine Ressource aus einer Sammlung für die Verschiebung entfernen möchten, hängt die Methode davon ab, an welchem Punkt im Verschiebevorgang Sie sich befinden. [Weitere Informationen](remove-move-resources.md)

## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten

1. Wenn für Ressourcen in der Spalte **Probleme** die Meldung *Abhängigkeiten überprüfen* angezeigt wird, klicken Sie auf die Schaltfläche **Abhängigkeiten überprüfen**. Der Überprüfungsprozess wird daraufhin begonnen.
2. Wenn Abhängigkeiten gefunden werden, klicken Sie auf **Abhängigkeiten hinzufügen**. 
3. Übernehmen Sie unter **Abhängigkeiten hinzufügen** die Standardoption **Alle Abhängigkeiten anzeigen**.

    - Bei Verwendung von „Alle Abhängigkeiten anzeigen“ werden alle direkten und indirekten Abhängigkeiten für eine Ressource durchlaufen. Für einen virtuellen Computer werden beispielsweise die NIC, das virtuelle Netzwerk, die Netzwerksicherheitsgruppen (NSGs) und Ähnliches angezeigt.
    - Bei Verwendung von „Show first level dependencies only“ (Nur Abhängigkeiten der ersten Ebene anzeigen) werden nur direkte Abhängigkeiten angezeigt. Für einen virtuellen Computer wird beispielsweise die NIC, aber nicht das virtuelle Netzwerk angezeigt.


4. Wählen Sie die abhängigen Ressourcen, die Sie hinzufügen möchten, und anschließend **Abhängigkeiten hinzufügen** aus. Überwachen Sie den Fortschritt in den Benachrichtigungen.

    ![Hinzufügen von Abhängigkeiten](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Überprüfen Sie die Abhängigkeiten erneut. 
    ![Seite zum Hinzufügen zusätzlicher Abhängigkeiten](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)



## <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe 

Bevor Sie virtuelle Computer vorbereiten und verschieben können, muss die VM-Ressourcengruppe in der Zielregion vorhanden sein. 

### <a name="prepare-to-move-the-source-resource-group"></a>Vorbereiten zum Verschieben der Quellressourcengruppe

Beim Vorbereitungsprozess generiert Resource Mover Azure Resource Manager-Vorlagen (ARM) mithilfe der Ressourcengruppeneinstellungen. Die Ressourcen innerhalb der Ressourcengruppe sind nicht betroffen.

Gehen Sie zur Vorbereitung wie folgt vor:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Vorbereiten** aus.
2. Klicken Sie unter **Ressourcen vorbereiten** auf **Vorbereiten**.

    ![Vorbereiten der Ressourcengruppe](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> Nachdem Sie die Ressourcengruppe vorbereitet haben, weist diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf. 

 
### <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe

Initiieren Sie die Verschiebung wie folgt:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Initiate Move** (Verschiebung initiieren) aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren). Die Ressourcengruppe wechselt in den Status *Initiate move in progress* (Verschiebung wird initiiert).
3. Nachdem die Verschiebung initiiert wurde, wird die Zielressourcengruppe basierend auf der generierten ARM-Vorlage erstellt. Die Quellressourcengruppe wechselt in den Status *Commit move pending* (Committen der Verschiebung ausstehend).

    ![Klicken auf die Schaltfläche „Verschieben“](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

So committen Sie die Verschiebung und schließen sie ab:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Commit move** (Verschiebung committen) aus.
2. Klicken Sie unter **Ressourcen verschieben** auf **Commit**.

> [!NOTE]
> Nach dem Committen der Verschiebung befindet sich die Quellressourcengruppe im Status *Delete source pending* (Löschen der Quelle ausstehend).

## <a name="prepare-resources-to-move"></a>Vorbereiten der Ressource zum Verschieben

Nach dem Verschieben der Quellressourcengruppe können Sie als Nächstes die Verschiebung anderer Ressourcen mit dem Status *Vorbereitung ausstehend* vorbereiten.

1. Vergewissern Sie sich unter **Regionsübergreifend**, dass sich die Ressourcen jetzt im Status *Vorbereitung ausstehend* befinden und keine Probleme mehr vorliegen. Führen Sie andernfalls eine erneute Überprüfung durch, und beheben Sie ggf. noch vorhandene Probleme.

    ![Seite mit den Ressourcen im Status „Prepare pending“ (Vorbereitung ausstehend)](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

2. Wenn Sie die Zieleinstellungen vor dem Verschieben bearbeiten möchten, wählen Sie den Link in der Spalte **Destination configuration** (Zielkonfiguration) der Ressource aus, und bearbeiten Sie die Einstellungen. Wenn Sie die Ziel-VM-Einstellungen bearbeiten, darf die Größe der Ziel-VM nicht kleiner sein als die Größe der Quell-VM.  

Nachdem die Quellressourcengruppe verschoben wurde, können Sie die Verschiebung der anderen Ressourcen vorbereiten.

3. Wählen Sie die Ressourcen aus, die Sie vorbereiten möchten. 

    ![Seite zur Auswahl der Vorbereitung für andere Ressourcen](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Wählen Sie **Vorbereiten** aus. 

> [!NOTE]
> - Während des Vorbereitens wird der Mobilitäts-Agent von Azure Site Recovery auf virtuellen Computern installiert, um diese zu replizieren.
> - VM-Daten werden in regelmäßigen Abständen in die Zielregion repliziert. Dies wirkt sich nicht auf die Quell-VM aus.
> - Beim Verschieben von Ressourcen werden ARM-Vorlagen für die anderen Quellressourcen generiert.
> - Nachdem Sie die Ressourcen vorbereitet haben, weisen diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

![Seite mit den Ressourcen im Status „Initiate move pending“ (Initiieren der Verschiebung ausstehend)](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Initiieren der Verschiebung

Nachdem die Ressourcen vorbereitet wurden, können Sie die Verschiebung initiieren. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) aus. Klicken Sie anschließend auf **Initiate move** (Verschiebung initiieren).
2. Klicken Sie unter **Ressourcen verschieben** auf **Initiate Move** (Verschiebung initiieren).

    ![Klicken auf die Schaltfläche „Initiate Move“ (Verschiebung initiieren)](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.

> [!NOTE]
> - Bei virtuellen Computern werden Replikat-VMs in der Zielregion erstellt. Die Quell-VM wird heruntergefahren, und es kommt zu einer gewissen Downtime (in der Regel einige Minuten).
> - Resource Mover erstellt mithilfe der vorbereiteten ARM-Vorlagen andere Ressourcen neu. Dabei gibt es in der Regel keine Downtime.
> - Nachdem Sie die Ressourcen verschoben haben, weisen diese den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.

![Seite mit den Ressourcen im Status „Delete source pending“ (Löschen der Quelle ausstehend)](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


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

    ![Seite zum Committen von Ressourcen zum Abschließen der Verschiebung](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Verfolgen Sie den Fortschritt des Commits in der Benachrichtigungsleiste nach.

> [!NOTE]
> - Nach dem Committen der Verschiebung wird die Replikation der virtuellen Computer beendet. Der Commit hat keinen Einfluss auf die Quell-VM.
> - Der Commit wirkt sich nicht auf die Ressourcen des Quellnetzwerks aus.
> - Nach dem Committen der Verschiebung befinden sich die Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend).

![Seite mit den Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend)](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Konfigurieren der Einstellungen nach dem Verschieben

- Der Mobilitätsdienst wird auf virtuellen Computern nicht automatisch deinstalliert. Deinstallieren Sie ihn manuell, oder lassen Sie ihn installiert, wenn Sie planen, den Server erneut zu verschieben.
- Ändern Sie nach dem Verschieben die Regeln der rollenbasierten Zugriffssteuerung in Azure (Azure RBAC).


## <a name="delete-source-resources-after-commit"></a>Löschen von Quellressourcen nach einem Commit

Nach dem Verschieben können Sie die Ressourcen in der Quellregion löschen, dies ist jedoch optional. 

> [!NOTE]
> Einige Ressourcen (beispielsweise Schlüsseltresore und SQL Server-Server) können nicht über das Portal gelöscht werden und müssen auf der Eigenschaftenseite der jeweiligen Ressource gelöscht werden.

1. Klicken Sie unter **Regionsübergreifend** auf den Namen der Quellressource, die Sie löschen möchten.
2. Wählen Sie **Quelle löschen** aus.

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
> * Verschieben von Azure-VMs in eine andere Azure-Region
> * Verschieben von Ressourcen, die virtuellen Computern zugeordnet sind, in eine andere Region

Versuchen Sie als Nächstes, Azure SQL-Datenbanken und Pools für elastische Datenbanken in eine andere Region zu verschieben.

> [!div class="nextstepaction"]
> [Verschieben von Azure SQL-Ressourcen](./tutorial-move-region-sql.md)
