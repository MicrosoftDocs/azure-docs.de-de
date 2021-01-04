---
title: Verschieben von Ressourcen in eine andere Region mit Azure Resource Mover
description: Erfahren Sie, wie Sie Ressourcen in einer Ressourcengruppe mit Azure Resource Mover in eine andere Region verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 34064fe3fe88a34b0dd2430d7adec3ebcb17ebcc
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95528226"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Regionsübergreifendes Verschieben von Ressourcen (aus Ressourcengruppe)

In diesem Artikel erfahren Sie, wie Sie Ressourcen in einer bestimmten Ressourcengruppe in eine andere Azure-Region verschieben. In der Ressourcengruppe wählen Sie die Ressourcen aus, die Sie verschieben möchten. Anschließend verschieben Sie sie mithilfe von [Azure Resource Mover](overview.md).

> [!IMPORTANT]
> Azure Resource Mover ist derzeit als öffentliche Vorschauversion verfügbar.


## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen *Besitzerzugriff* auf das Abonnement, in dem sich die zu verschiebenden Ressourcen befinden.
    - Wenn Sie zum ersten Mal eine Ressource für eine bestimmte Quelle-Ziel-Zuordnung in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird.
    - Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender oder Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, Berechtigungen als *Besitzer* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
- Das Abonnement benötigt ein ausreichendes Kontingent zum Erstellen der Quellressourcen in der Zielregion. Wenn kein ausreichendes Kontingent vorhanden ist, fordern Sie eine Heraufsetzung des Kontingents an. [Weitere Informationen](../azure-resource-manager/management/azure-subscription-service-limits.md)
- Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie virtuelle Computer verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).
- Überprüfen Sie, ob die zu verschiebenden Ressourcen von Resource Mover unterstützt werden:
    - Azure-VMs und zugehörige Datenträger
    - NICs
    - Verfügbarkeitsgruppen
    - Virtuelle Azure-Netzwerke
    - Öffentliche IP-Adressen
    - Netzwerksicherheitsgruppen (NSGs)
    - Interne und öffentliche Load Balancer
    - Azure SQL-Datenbanken und Pools für elastische Datenbanken


## <a name="check-vm-requirements"></a>Prüfen der VM-Anforderungen

1. Überprüfen Sie, ob die virtuellen Computer unterstützt werden, die Sie verschieben möchten.

    - [Überprüfen](support-matrix-move-region-azure-vm.md#windows-vm-support) Sie die unterstützten Windows-VMs.
    - [Überprüfen](support-matrix-move-region-azure-vm.md#linux-vm-support) Sie die unterstützten Linux-VMs und Kernel-Versionen.
    - Überprüfen Sie die unterstützten [Compute-](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [Speicher-](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) und [Netzwerkeinstellungen](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
2. Stellen Sie sicher, dass die VMs über die aktuellen vertrauenswürdigen Stammzertifikate und eine aktualisierte Zertifikatssperrliste (Certificate Revocation List, CRL) verfügen. 
    - Installieren Sie auf VMs, die Windows ausführen, die neuesten Windows-Updates.
    - Befolgen Sie auf VMs, auf denen Linux ausgeführt wird, den Leitfaden für den Linux-Distributoren, um sicherzustellen, dass der Computer über die neuesten Zertifikate und die neueste Zertifikatssperrliste verfügt. 
3. Erlauben Sie die ausgehende Verbindung von virtuellen Computern:
    - Lassen Sie den Zugriff auf die folgenden [URLs](support-matrix-move-region-azure-vm.md#url-access) zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden.
    - Wenn Sie Regeln für Netzwerksicherheitsgruppen (NSG) zum Steuern der ausgehenden Verbindung verwenden, erstellen Sie diese [Diensttagregeln](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Auswählen der zu verschiebenden Ressourcen

Wählen Sie die Ressourcen aus, die Sie verschieben möchten. Sie verschieben Ressourcen in eine Zielregion im Abonnement der Quellregion. Wenn Sie das Abonnement ändern möchten, können Sie dies nach dem Verschieben der Ressourcen tun.

1. Öffnen Sie im Azure-Portal die relevante Ressourcengruppe.
2. Wählen Sie auf der Seite „Ressourcengruppe“ die Ressourcen aus, die Sie verschieben möchten.
3. Wählen Sie **Verschieben** > **In eine andere Region verschieben** aus.

    ![Auswahl zum Verschieben von Ressourcen in eine andere Region](./media/move-region-within-resource-group/select-move-region.png)
    
4. Wählen Sie unter **Quelle und Ziel** die Zielregion aus, in die Sie die Ressourcen verschieben möchten. Wählen Sie **Weiter** aus.


    ![Seite „Quelle und Ziel“ zum Auswählen der Zielregion](./media/move-region-within-resource-group/source-target.png)


7. Wählen Sie unter **Zu verschiebende Ressourcen** die Option **Weiter** aus.  
8. Wählen Sie unter **Ressourcen auswählen** die Ressource aus, die Sie verschieben möchten. Sie können nur Ressourcen hinzufügen, die zum Verschieben unterstützt werden. Wählen Sie dann **Fertig** aus.
9. Wählen Sie unter **Ressourcen verschieben** die Option **Weiter** aus. 
10. Überprüfen Sie unter **Überprüfen und hinzufügen** die Details der Quelle und des Ziels.
11. Bestätigen Sie, dass Sie verstanden haben, dass Metadaten zu den verschobenen Ressourcen in einer zu diesem Zweck erstellten Ressourcengruppe gespeichert werden, und dass Sie Resource Mover erlauben, eine vom System verwaltete Identität für den Zugriff auf die Abonnementressourcen zu erstellen.
1. Wählen Sie **Fortsetzen** aus, um die Ressourcen hinzuzufügen.

    ![Zusammenfassungsseite zum Überprüfen von Details und Fortsetzen der Verschiebung](./media/move-region-within-resource-group/summary.png)    

11. Der Vorgang zum Hinzufügen von Ressourcen wird gestartet. Nachdem der Vorgang abgeschlossen ist, zeigen die Benachrichtigungen, dass Ressourcen hinzugefügt wurden und die Bereitstellung erfolgreich war.
13. Wählen Sie in den Benachrichtigungen **Ressourcen für Verschiebevorgang werden hinzugefügt** aus.

    ![In Benachrichtigungen angezeigte Meldung](./media/move-region-within-resource-group/notification.png)    


14. Nachdem Sie die Benachrichtigung ausgewählt haben, werden die von Ihnen ausgewählten Ressourcen einer Verschiebungssammlung im Azure Resource Mover-Hub hinzugefügt.  Resource Mover unterstützt Sie beim Überprüfen von Abhängigkeiten und startet dann den Verschiebevorgang von Ressourcen in die Zielregion.

## <a name="resolve-dependencies"></a>Auflösen von Abhängigkeiten

Die zu verschiebenden Ressourcen werden auf der Seite **Across regions** (Regionsübergreifend) mit dem Status *Prepare pending* (Ausstehende vorbereiten) angezeigt. Starten Sie die Überprüfung wie folgt:

1. Wenn für Ressourcen in der Spalte **Issues** (Probleme) die Meldung *Validate dependencies* (Abhängigkeiten überprüfen) angezeigt wird, klicken Sie auf die Schaltfläche **Validate dependencies** (Abhängigkeiten überprüfen). Der Überprüfungsprozess wird daraufhin begonnen.

    ![Schaltfläche zum Überprüfen von Abhängigkeiten](./media/move-region-within-resource-group/validate-dependencies.png)

2. Wenn Abhängigkeiten gefunden werden, wählen Sie **Add dependencies** (Abhängigkeiten hinzufügen) aus. 
3. Wählen Sie unter **Abhängigkeiten hinzufügen** die abhängigen Ressourcen und **Abhängigkeiten hinzufügen** aus. Überwachen Sie den Fortschritt in den Benachrichtigungen.

    ![Schaltfläche zum Hinzufügen von Abhängigkeiten](./media/move-region-within-resource-group/add-dependencies.png)

3. Fügen Sie bei Bedarf zusätzliche Abhängigkeiten hinzu, und überprüfen Sie die Abhängigkeiten bei Bedarf. Wählen Sie **Aktualisieren** aus, um sicherzustellen, dass Ressourcen einen aktuellen Status anzeigen.

4. Überprüfen Sie auf der Seite **Across regions** (Regionsübergreifend), ob sich die Ressourcen jetzt im Status *Prepare pending* (Vorbereitung ausstehend) befinden und keine Probleme mehr aufweisen.

    ![Seite zum Anzeigen des Status „Prepare pending“ (Ausstehende vorbereiten) für alle Ressourcen](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe 

Bevor Sie Ressourcen vorbereiten und verschieben können, muss die Quellressourcengruppe in der Zielregion vorhanden sein. 

### <a name="prepare-to-move-the-source-resource-group"></a>Vorbereiten zum Verschieben der Quellressourcengruppe

Gehen Sie zur Vorbereitung wie folgt vor:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Vorbereiten** aus.
2. Wählen Sie unter **Prepare resources** (Ressourcen vorbereiten) die Option **Prepare** (Vorbereiten) aus.
1. 
    ![Schaltfläche zum Vorbereiten der Quellressourcengruppe](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Beim Vorbereitungsprozess generiert Resource Mover Azure Resource Manager-Vorlagen (ARM) mithilfe der Ressourcengruppeneinstellungen. Die Ressourcen innerhalb der Ressourcengruppe sind nicht betroffen.
    
> [!NOTE]
>  Nachdem Sie die Ressourcengruppe vorbereitet haben, weist diese den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf. Aktualisieren Sie die Ansicht, um den aktuellen Status anzuzeigen.

![Status zum Initiieren des Zustands „Ausstehend“](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Verschieben der Quellressourcengruppe

Initiieren Sie die Verschiebung wie folgt:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Quellressourcengruppe und anschließend **Initiate Move** (Verschiebung initiieren) aus.
2. Wählen Sie unter **Ressourcen verschieben** die Option **Initiate Move** (Verschiebung initiieren) aus. Die Ressourcengruppe wechselt in den Status *Initiate move in progress* (Verschiebung wird initiiert).
3. Nachdem die Verschiebung initiiert wurde, wird die Zielressourcengruppe basierend auf der generierten ARM-Vorlage erstellt. Die Quellressourcengruppe wechselt in den Status *Commit move pending* (Committen der Verschiebung ausstehend).

![Status „Verschiebung committen“](./media/move-region-availability-zone/commit-move-pending.png)

So committen Sie die Verschiebung und schließen sie ab:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) die Ressourcengruppe und anschließend **Commit move** (Verschiebung committen) aus.
2. Wählen Sie unter **Ressourcen verschieben** die Option **Commit** (Committen) aus.

> [!NOTE]
> Nach dem Committen der Verschiebung befindet sich die Quellressourcengruppe im Status *Delete source pending* (Löschen der Quelle ausstehend).

## <a name="modify-target-settings"></a>Ändern von Zieleinstellungen

Wenn Sie keine Quellressource verschieben möchten, können Sie eine der folgenden Aktionen ausführen:

- Erstellen Sie eine Ressource in der Zielregion mit dem gleichen Namen und den gleichen Einstellungen wie die Ressource in der Quellregion.
- Erstellen Sie eine neue äquivalente Ressource in der Zielregion. Mit Ausnahme der von Ihnen angegebenen Einstellungen wird die Zielressource mit denselben Einstellungen wie die Quelle erstellt.
- Verwenden Sie eine vorhandene Ressource in der Zielregion.

Ändern Sie eine Einstellung wie folgt:

1. Um eine Einstellung zu ändern, wählen Sie den Eintrag in der Spalte **Zielkonfiguration** für die Ressource aus.
2. Geben Sie auf der Seite **Zielkonfiguration** die Zieleinstellungen an, die Sie verwenden möchten.
    Änderungen werden nur für die Ressource vorgenommen, die Sie bearbeiten. Sie müssen alle abhängigen Ressourcen separat aktualisieren.   
    
Welche Einstellungen Sie ändern, hängt vom Ressourcentyp ab. [Erfahren Sie mehr](modify-target-settings.md) über die Bearbeitung von Zieleinstellungen.

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

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) aus. Wählen Sie dann **Initiate move** (Verschiebung initiieren) aus.
2. Wählen Sie unter **Ressourcen verschieben** die Option **Initiate Move** (Verschiebung initiieren) aus.

    ![Auswählen der Schaltfläche „Initiate Move“ (Verschiebung initiieren)](./media/move-region-within-resource-group/initiate-move.png)

3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.


> [!NOTE]
> - Bei virtuellen Computern werden Replikat-VMs in der Zielregion erstellt. Die Quell-VM wird heruntergefahren, und es kommt zu einer gewissen Downtime (in der Regel einige Minuten).<br/>
> - Resource Mover erstellt mithilfe der vorbereiteten ARM-Vorlagen andere Ressourcen neu. Dabei gibt es in der Regel keine Downtime.<br/> 
> - Für Load Balancer werden NAT-Regeln nicht kopiert. Erstellen Sie diese in der Zielregion, nachdem Sie die Verschiebung committet haben.
> - Für öffentliche IP-Adressen wird die DNS-Namenbezeichnung nicht kopiert. Erstellen Sie die Bezeichnung neu, nachdem Sie die Verschiebung committet haben.
> - Nachdem Sie Ressourcen vorbereitet haben, weisen diese den Status *Commit move pending* (Committen der Verschiebung ausstehend) auf.


## <a name="discard-or-commit"></a>Verwerfen oder committen?

Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie einen Commit für die Verschiebung durchführen oder sie verwerfen möchten. 

- **Verwerfen**: Sie können eine Verschiebung verwerfen, wenn Sie Tests durchführen und die Quellressource letztendlich nicht verschoben werden soll. Beim Verwerfen der Verschiebung wird die Ressource in den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) zurückgesetzt.
- **Commit**: Beim Commit wird die Verschiebung in die Zielregion abgeschlossen. Nach dem Commit hat eine Quellressource den Status *Delete source pending* (Löschen der Quelle ausstehend), und Sie können entscheiden, ob sie gelöscht werden soll.


## <a name="discard-the-move"></a>Verwerfen der Verschiebung 

Sie können eine Verschiebung wie folgt verwerfen:

1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Commit move pending* (Committen der Verschiebung ausstehend) aus, und wählen Sie **Discard move** (Verschiebung verwerfen) aus.
2. Wählen Sie unter **Discard move** (Verschiebung verwerfen) **Discard** (Verwerfen) aus.
3. Überwachen Sie den Fortschritt der Verschiebung in der Benachrichtigungsleiste.
4. Wenn Benachrichtigungen anzeigen, dass die Verschiebung erfolgreich war, wählen Sie **Aktualisieren** aus. 

> [!NOTE]
> Nachdem Sie die Ressourcen verworfen haben, weisen diese für VMs den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) auf.

## <a name="commit-the-move"></a>Committen der Verschiebung

Wenn Sie die Verschiebung abschließen möchten, führen Sie einen Commit aus. 


1. Wählen Sie unter **Across regions** (Regionsübergreifend) Ressourcen mit dem Status *Commit move pending* (Committen der Verschiebung ausstehend) aus, und wählen Sie **Commit move** (Verschiebung committen) aus.
2. Wählen Sie unter **Commit resources** (Ressourcen committen) die Option **Commit** (Committen) aus.

    ![Seite zum Committen von Ressourcen zum Abschließen der Verschiebung](./media/move-region-within-resource-group/commit-resources.png)

3. Verfolgen Sie den Fortschritt des Commits in der Benachrichtigungsleiste nach.

> [!NOTE]
> - Nach dem Committen der Verschiebung wird die Replikation der virtuellen Computer beendet. Der Commit hat keinen Einfluss auf die Quell-VM.
> - Der Commit wirkt sich nicht auf die Ressourcen des Quellnetzwerks aus.
> - Nach dem Committen der Verschiebung befinden sich die Ressourcen im Status *Delete source pending* (Löschen der Quelle ausstehend).

## <a name="configure-settings-after-the-move"></a>Konfigurieren der Einstellungen nach dem Verschieben

1. Da DNS-Namenbezeichnungen nicht für öffentliche IP-Adressen kopiert werden, navigieren Sie nach dem Verschieben zu den Zielressourcen, und aktualisieren Sie die Bezeichnung. 
2. Da NAT-Regeln nicht kopiert werden, navigieren Sie für interne Lastenausgleichsmodule zu den Ressourcen, die in der Zielregion erstellt wurden, und aktualisieren Sie die NAT-Regeln.
3. Der Mobilitätsdienst wird auf virtuellen Computern nicht automatisch deinstalliert. Deinstallieren Sie ihn manuell, oder lassen Sie ihn installiert, wenn Sie planen, den Server erneut zu verschieben.
## <a name="delete-source-resources-after-commit"></a>Löschen von Quellressourcen nach einem Commit

Nach dem Verschieben können Sie die Ressourcen in der Quellregion löschen, dies ist jedoch optional. 

1. Wählen Sie unter **Across regions** (Regionsübergreifend) den Namen der einzelnen Quellressourcen aus, die Sie löschen möchten.
2. Wählen Sie auf der Seite „Eigenschaften“ der ausgewählten Ressource die Option **Löschen** aus.

## <a name="delete-additional-resources-created-for-move"></a>Löschen zusätzlicher, für das Verschieben erstellter Ressourcen

Nach dem Verschieben können Sie die Sammlung für die Verschiebung und die erstellten Site Recovery-Ressourcen manuell löschen.

- Die Sammlung für die Verschiebung ist standardmäßig ausgeblendet. Blenden Sie ausgeblendete Ressourcen ein, um sie anzuzeigen.
- Der Cachespeicher verfügt über eine Sperre, die zunächst gelöscht werden muss.

Gehen Sie beim Löschen wie folgt vor: 

1. Suchen Sie die Ressourcen in der Ressourcengruppe ```RegionMoveRG-<sourceregion>-<target-region>``` in der Quellregion.
2. Überprüfen Sie, ob alle VMs und anderen Quellressourcen in der Verschiebungssammlung verschoben/gelöscht wurden. Dadurch wird sichergestellt, dass sie nicht von ausstehenden Ressourcen verwendet werden.
2. Löschen der Ressourcen:

    - Der Name der Sammlung für die Verschiebung ist ```movecollection-<sourceregion>-<target-region>```.
    - Der Name des Cachespeichers lautet ```resmovecache<guid>```.
    - Der Tresorname ist ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Nächste Schritte


[Informationen](about-move-process.md) zum Verschiebungsvorgang.