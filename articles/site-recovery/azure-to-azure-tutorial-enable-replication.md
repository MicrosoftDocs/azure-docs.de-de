---
title: 'Tutorial: Einrichten der Azure-VM-Notfallwiederherstellung mit Azure Site Recovery'
description: In diesem Tutorial erfahren Sie, wie Sie mit dem Site Recovery-Dienst die Notfallwiederherstellung für Azure-VMs in einer anderen Region einrichten.
ms.topic: tutorial
ms.date: 11/03/2020
ms.custom: mvc
ms.openlocfilehash: 6d07082b4a9c18461d5cc74de8844be803da7168
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922493"
---
# <a name="tutorial-set-up-disaster-recovery-for-azure-vms"></a>Tutorial: Einrichten der Notfallwiederherstellung für Azure-VMs

In diesem Tutorial wird erläutert, wie Sie die Notfallwiederherstellung für Azure-VMs mithilfe von [Azure Site Recovery](site-recovery-overview.md) einrichten. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Überprüfen der Azure-Einstellungen und -Berechtigungen
> * Vorbereiten der VMs, die Sie replizieren möchten
> * Erstellen eines Recovery Services-Tresors
> * Aktivieren der VM-Replikation

Wenn Sie die Replikation für eine VM aktivieren, um die Notfallwiederherstellung einzurichten, wird die Site Recovery Mobility Service-Erweiterung auf der VM installiert und die VM bei Azure Site Recovery registriert. Während der Replikation werden Schreibvorgänge auf dem VM-Datenträger an ein Cachespeicherkonto in der Quellregion gesendet. Daten werden von dort aus in die Zielregion gesendet, und aus den Daten werden Wiederherstellungspunkte generiert. Wenn Sie während der Notfallwiederherstellung ein Failover für eine VM ausführen, wird ein Wiederherstellungspunkt genutzt, um die VM in der Zielregion wiederherzustellen.

> [!NOTE]
> Tutorials enthalten Anweisungen mit den einfachsten Standardeinstellungen. Falls Sie die Azure-VM-Notfallwiederherstellung mit angepassten Einstellungen einrichten möchten, lesen Sie [diesen Artikel](azure-to-azure-how-to-enable-replication.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, bevor Sie mit diesem Tutorial beginnen:

- [Überprüfen Sie die unterstützten Regionen](azure-to-azure-support-matrix.md#region-support). Sie können die Notfallwiederherstellung für Azure-VMs zwischen zwei beliebigen Regionen innerhalb desselben geografischen Gebiets einrichten.
- Sie benötigen mindestens eine Azure-VM. Vergewissern Sie sich, dass [Windows](azure-to-azure-support-matrix.md#windows)- oder [Linux](azure-to-azure-support-matrix.md#replicated-machines---linux-file-systemguest-storage)-VMs unterstützt werden.
- Überprüfen Sie die VM-Anforderungen in Bezug auf [Compute](azure-to-azure-support-matrix.md#replicated-machines---compute-settings), [Speicher](azure-to-azure-support-matrix.md#replicated-machines---storage) und [Netzwerk](azure-to-azure-support-matrix.md#replicated-machines---networking).
- In diesem Tutorial wird davon ausgegangen, dass VMs nicht verschlüsselt sind. Falls Sie die Notfallwiederherstellung für verschlüsselte VMs einrichten möchten, folgen Sie den Anweisungen in [diesem Artikel](azure-to-azure-how-to-enable-replication-ade-vms.md).

## <a name="check-azure-settings"></a>Überprüfen der Azure-Einstellungen

Überprüfen Sie die Berechtigungen und Einstellungen in der Zielregion.

### <a name="check-permissions"></a>Überprüfen der Berechtigungen

Ihr Azure-Konto muss über Berechtigungen zum Erstellen eines Recovery Services-Tresors und zum Erstellen von VMs in der Zielregion verfügen.

- Wenn Sie gerade ein kostenloses Azure-Abonnement erstellt haben, sind Sie der Kontoadministrator, und es ist keine weitere Aktion erforderlich.
- Sollten Sie nicht der Administrator sein, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist.
    - **Erstellen eines Tresors**: Administrator- oder Besitzerberechtigungen für das Abonnement 
    - **Verwalten von Site Recovery-Vorgängen im Tresor**: Integrierte Azure-Rolle *Site Recovery-Mitwirkender*
    - **Erstellen von Azure-VMs in der Zielregion**: Integrierte Rolle *Mitwirkender für virtuelle Computer* oder spezifische Berechtigungen für folgende Aktionen:
        - Erstellen einer VM im ausgewählten virtuellen Netzwerk
        - Schreiben in ein Azure-Speicherkonto
        - Schreiben auf einen verwalteten Azure-Datenträger

### <a name="verify-target-settings"></a>Überprüfen der Zieleinstellungen

Wenn Sie während der Notfallwiederherstellung ein Failover von der Quellregion ausführen, werden VMs in der Zielregion erstellt. 

Überprüfen Sie, ob Ihr Abonnement über ausreichende Ressourcen in der Zielregion verfügt. Sie müssen in der Lage sein, VMs mit Größen zu erstellen, die den VMs in der Quellregion entsprechen. Wenn Sie die Notfallwiederherstellung einrichten, wählt Site Recovery dieselbe (oder eine möglichst ähnliche) Größe für die Ziel-VM aus.


## <a name="prepare-vms"></a>Vorbereiten der VMs

Stellen Sie sicher, dass die VMs über ausgehende Konnektivität und die aktuellen Stammzertifikate verfügen. 


### <a name="set-up-vm-connectivity"></a>Einrichten der VM-Konnektivität

VMs, die Sie replizieren möchten, benötigen ausgehende Netzwerkkonnektivität.

> [!NOTE]
> Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.

#### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:

| **Name**                  | **Kommerziell**                               | **Behörden**                                 | **Beschreibung** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| Replikation               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Ermöglicht die Kommunikation der VM mit Site Recovery |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |

#### <a name="outbound-connectivity-for-ip-address-ranges"></a>Ausgehende Konnektivität für IP-Adressbereiche

Wenn Sie Netzwerksicherheitsgruppen (NSGs) zum Steuern der Konnektivität verwenden, erstellen Sie auf dem Diensttag basierende NSG-Regeln, die ausgehende HTTPS-Verbindungen mit Port 443 für folgende [Diensttags](../virtual-network/service-tags-overview.md#available-service-tags) (Gruppen von IP-Adressen) zulassen:

**Tag** | **Zulassen** 
--- | ---
Storage-Tag  |Ermöglicht das Schreiben von Daten von der VM in das Cachespeicherkonto.   
Azure AD-Tag | Ermöglicht den Zugriff auf alle IP-Adressen, die zu Azure AD gehören.   
EventsHub-Tag | Ermöglicht den Zugriff auf die Site Recovery-Überwachung.  
AzureSiteRecovery-Tag | Ermöglicht den Zugriff auf den Site Recovery-Dienst in beliebigen Regionen.   
GuestAndHybridManagement-Tag | Ermöglicht die Durchführung von automatischen Upgrades des Mobilitäts-Agents von Site Recovery, der auf VMs mit Aktivierung für die Replikation ausgeführt wird.

[Weitere Informationen](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags) zu erforderlichen Tags und Beispiele für das Tagging.

### <a name="verify-vm-certificates"></a>Überprüfen von VM-Zertifikaten

Überprüfen Sie, ob die VMs über die aktuellen Stammzertifikate verfügen. Andernfalls kann die VM aufgrund von Sicherheitseinschränkungen nicht bei Site Recovery registriert werden.

- **Virtuelle Windows-Computer:** Installieren Sie alle aktuellen Windows-Updates auf der VM, damit alle vertrauenswürdigen Stammzertifikate darauf vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung Ihre Standardprozesse für Windows Update und Zertifikatupdates durch.
- **Virtuelle Linux-Computer:** Befolgen Sie die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste abzurufen.

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Erstellen Sie einen Recovery Services-Tresor in einer beliebigen Region, jedoch nicht in der Quellregion, aus der Sie VMs replizieren möchten.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Geben Sie *Wiederherstellung* in das Suchfeld ein. Wählen Sie unter **Dienste** die Option **Recovery Services-Tresore** aus.

    ![Suchen nach Recovery Services-Tresoren](./media/azure-to-azure-tutorial-enable-replication/search.png)

3. Wählen Sie unter **Recovery Services-Tresore** die Option **Hinzufügen** aus.
4. Wählen Sie unter **Recovery Services-Tresor erstellen** > **Grundlagen** das Abonnement aus, in dem Sie den Tresor erstellen möchten.
5. Wählen Sie unter **Ressourcengruppe** eine vorhandene Ressourcengruppe für den Tresor aus, oder erstellen Sie eine neue Ressourcengruppe.
6. Geben Sie unter **Tresorname** einen Anzeigenamen an, mit dem der Tresor identifiziert wird.
7. Wählen Sie unter **Region** die Region aus, in der sich der Tresor befinden soll. [Überprüfen Sie die unterstützten Regionen](https://azure.microsoft.com/pricing/details/site-recovery/).
8. Klicken Sie auf **Überprüfen + erstellen**.

   ![Tresoreinstellungen auf der Seite zum Erstellen eines neuen Tresors](./media/azure-to-azure-tutorial-enable-replication/vault-basics.png)

9. Wählen Sie unter **Überprüfen + erstellen** die Option **Erstellen** aus.

10. Die Bereitstellung des Tresors wird gestartet. Überwachen Sie den Fortschritt in den Benachrichtigungen.
11. Wählen Sie nach der Bereitstellung des Tresors **An Dashboard anheften** aus, um ihn für die Kurzübersicht zu speichern. Wählen Sie **Zu Ressource wechseln** aus, um den neuen Tresor zu öffnen. 
    
    ![Schaltflächen zum Öffnen des Tresors nach der Bereitstellung und Anheften an das Dashboard](./media/azure-to-azure-tutorial-enable-replication/vault-deploy.png)

### <a name="enable-site-recovery"></a>Aktivieren von Site Recovery

Wählen Sie in den Tresoreinstellungen **Site Recovery aktivieren** aus.

![Option zum Aktivieren von Site Recovery im Tresor](./media/azure-to-azure-tutorial-enable-replication/enable-site-recovery.png)

## <a name="enable-replication"></a>Aktivieren der Replikation

Wählen Sie die Quelleinstellungen aus, und aktivieren Sie die VM-Replikation. 

### <a name="select-source-settings"></a>Auswählen der Quelleinstellungen

1. Wählen Sie im Tresor auf der Seite **Site Recovery** unter **Azure-VMs** die Option **Replikation aktivieren** aus.

    ![Option zum Aktivieren der Replikation für Azure-VMs](./media/azure-to-azure-tutorial-enable-replication/enable-replication.png)

2. Wählen Sie unter **Quelle**> **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
3. Übernehmen Sie unter **Bereitstellungsmodell für virtuelle Azure-Computer** die Standardeinstellung **Resource Manager**.
4. Wählen Sie unter **Quellabonnement** das Abonnement aus, in dem die VMs ausgeführt werden. Sie können ein beliebiges Abonnement auswählen, das sich im gleichen Azure Active Directory-Mandanten (AD) befindet wie der Tresor.
5. Wählen Sie unter **Quellressourcengruppe** die Ressourcengruppe aus, die die VMs enthält.
6. Übernehmen Sie für **Notfallwiederherstellung zwischen Verfügbarkeitszonen?** die Standardeinstellung **Nein**.

     ![Quelle einrichten](./media/azure-to-azure-tutorial-enable-replication/source.png)

7. Wählen Sie **Weiter** aus.

### <a name="select-the-vms"></a>Auswählen der virtuellen Computer

Site Recovery ruft die VMs ab, die dem ausgewählten Abonnement/der Ressourcengruppe zugeordnet sind.

1. Wählen Sie unter **Virtuelle Computer** die VMs aus, die Sie für die Notfallwiederherstellung aktivieren möchten.

     ![Seite zum Auswählen der VMs für die Replikation](./media/azure-to-azure-tutorial-enable-replication/select-vm.png)

2. Wählen Sie **Weiter** aus.

### <a name="review-replication-settings"></a>Überprüfen der Replikationseinstellungen

1. Überprüfen Sie unter **Replikationseinstellungen** die Einstellungen. Site Recovery erstellt Standardeinstellungen/-richtlinien für die Zielregion. Für die Zwecke dieses Tutorials verwenden wir die Standardeinstellungen.
2. Wählen Sie **Replikation aktivieren** aus.

    ![Seite zum Anpassen der Einstellungen und Aktivieren der Replikation](./media/azure-to-azure-tutorial-enable-replication/enable-vm-replication.png)   

3. Verfolgen Sie den Fortschritt der Replikation in den Benachrichtigungen nach.

     ![Nachverfolgen des Fortschritts in den Benachrichtigungen](./media/azure-to-azure-tutorial-enable-replication/notification.png) ![Nachverfolgen der Benachrichtigung zur erfolgreichen Replikation](./media/azure-to-azure-tutorial-enable-replication/notification-success.png)

4. Die VMs, die Sie aktivieren, werden im Tresor auf der Seite **Replizierte Elemente** angezeigt.

    ![VM auf der Seite „Replizierte Elemente“](./media/azure-to-azure-tutorial-enable-replication/replicated-items.png)


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Notfallwiederherstellung für eine Azure-VM aktiviert. Führen Sie nun einen Übungslauf durch, um zu überprüfen, ob die Notfallwiederherstellung wie erwartet funktioniert.

> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](azure-to-azure-tutorial-dr-drill.md)
