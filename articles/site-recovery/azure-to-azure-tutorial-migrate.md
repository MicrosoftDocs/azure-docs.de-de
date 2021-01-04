---
title: Verschieben von Azure-VMs in eine andere Azure-Region mit Azure Site Recovery
description: Verwenden Sie Azure Site Recovery, um virtuelle Azure-Computer aus einer Azure-Region in eine andere zu verschieben.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394831"
---
# <a name="move-vms-to-another-azure-region"></a>Verschieben von VMs in eine andere Azure-Region

Es gibt Szenarien, in denen Sie Ihre vorhandenen virtuellen Azure IaaS-Computer (Virtual Machines, VMs) aus einer Region in eine andere verschieben möchten. So möchten Sie z. B. die Zuverlässigkeit und Verfügbarkeit Ihrer vorhandenen VMs erhöhen, um die Verwaltbarkeit zu verbessern, oder Sie möchten die VMs aus Governancegründen verschieben. Weitere Informationen finden Sie in der [Übersicht über das Verschieben virtueller Azure-Computer](azure-to-azure-move-overview.md). 

Sie können den [Azure Site Recovery](site-recovery-overview.md)-Dienst verwenden, um Azure-VMs in eine sekundäre Region zu verschieben.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Überprüfen der Voraussetzungen für das Verschieben
> * Vorbereiten der virtuellen Quellcomputer und der Zielregion
> * Kopieren der Daten und Aktivieren der Replikation
> * Testen der Konfiguration und Durchführen der Verschiebung
> * Löschen der Ressourcen in der Quellregion


> [!IMPORTANT]
> Zum Verschieben von Azure-VMs in eine andere Region empfehlen wir den Einsatz von [Azure Resource Mover](../resource-mover/tutorial-move-region-virtual-machines.md). Resource Mover befindet sich derzeit in der öffentlichen Vorschau und bietet folgende Features:
> - Eine einzelne Anlaufstelle für das regionsübergreifende Verschieben von Ressourcen.
> - Kürzere Verschiebungszeit und verringerte Komplexität. Alles Nötige ist an einem einzelnen Ort.
> - Ein einfaches und konsistentes Verfahren zum Verschieben verschiedener Arten von Azure-Ressourcen.
> - Eine einfache Möglichkeit zum Erkennen von Abhängigkeiten zwischen Ressourcen, die Sie verschieben möchten. Dies hilft Ihnen, zusammenhängende Ressourcen gemeinsam zu verschieben, damit nach der Verschiebung in der Zielregion alles wie erwartet funktioniert.
> - Automatische Bereinigung der Ressourcen in der Quellregion, falls Sie sie nach dem Verschieben löschen möchten.
> - Tests. Sie können eine Verschiebung ausprobieren und sie dann verwerfen, wenn Sie keine vollständige Verschiebung durchführen möchten.



> [!NOTE]
> In diesem Tutorial wird aufgezeigt, wie Sie Azure-VMs unverändert aus einer Region in eine andere Region verschieben. Wenn Sie die Verfügbarkeit durch Verschieben von VMs in einer Verfügbarkeitsgruppe auf an Zonen angeheftete VMs in einer anderen Region verbessern müssen, lesen Sie das [Tutorial „Verschieben virtueller Azure-Computer in Verfügbarkeitszonen“](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich die Azure-VMs in der Azure-Region befinden, aus der Sie verschieben möchten.
- Überprüfen Sie, ob die von Ihnen ausgewählte [Kombination aus Quell- und Zielregion unterstützt wird](./azure-to-azure-support-matrix.md#region-support), und treffen Sie eine fundierte Entscheidung bezüglich der Zielregion.
- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](azure-to-azure-architecture.md) verstehen.
- Lesen Sie die [Einschränkungen und Anforderungen der Unterstützung](azure-to-azure-support-matrix.md).
- Überprüfen Sie die Kontoberechtigungen. Wenn Sie ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements. Wenn Sie nicht der Abonnementadministrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Um die Replikation für einen virtuellen Computer zu aktivieren und Daten mithilfe von Azure Site Recovery zu kopieren, benötigen Sie Folgendes:

    - Berechtigungen zum Erstellen einer VM in Azure-Ressourcen. Die integrierte Rolle „Mitwirkender für virtuelle Computer“ verfügt über diese Berechtigungen, zu den die folgenden gehören:
    - Berechtigung zum Erstellen einer VM in der ausgewählten Ressourcengruppe
    - Berechtigung zum Erstellen einer VM im ausgewählten virtuellen Netzwerk
    - Berechtigung zum Schreiben in das ausgewählte Speicherkonto
    
    - Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen. Die Rolle „Site Recovery-Mitwirkender“ verfügt über alle Berechtigungen, die zum Verwalten von Site Recovery-Vorgängen in einem Recovery Services-Tresor erforderlich sind.

- Stellen Sie sicher, dass alle aktuellen Stammzertifikate auf den virtuellen Azure-Computern vorhanden sind, die Sie verschieben möchten. Wenn die aktuellen Stammzertifikate nicht auf dem virtuellen Computer vorhanden sind, können die Daten aufgrund von Sicherheitseinschränkungen nicht in die Zielregion kopiert werden.

- Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung den Standardprozess für Windows Update und Zertifikatupdates in Ihrer Organisation durch.
    
- Befolgen Sie bei virtuellen Linux-Computern die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste für den virtuellen Computer abzurufen.
- Stellen Sie sicher, dass Sie keinen Authentifizierungsproxy zum Steuern der Netzwerkkonnektivität für die VMs verwenden, die Sie verschieben möchten.

- Wenn der virtuelle Computer, den Sie verschieben möchten, keinen Zugriff auf das Internet hat oder einen Firewallproxy zum Steuern des ausgehenden Zugriffs verwendet, [überprüfen Sie die Anforderungen](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dies schließt unter anderem Lastenausgleichsmodule, Netzwerksicherheitsgruppen (NSGs) und öffentliche IP-Adressen ein.

- Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um VMs zu unterstützen, deren Größe der Ihrer Quell-VMs entspricht. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wählt Site Recovery dieselbe oder eine möglichst ähnliche Größe für den virtuellen Zielcomputer aus.

- Stellen Sie sicher, dass Sie für jede im Quellnetzwerklayout identifizierte Komponente eine Zielressource erstellen. Dieser Schritt ist wichtig, um sicherzustellen, dass Ihre VMs in der Zielregion über alle Funktionen und Features verfügen, die auch in der Quellregion verfügbar waren.

     > [!NOTE] 
     > Azure Site Recovery erkennt und erstellt automatisch ein virtuelles Netzwerk, wenn Sie die Replikation für den virtuellen Quellcomputer aktivieren. Sie können auch vorab ein Netzwerk erstellen und es dem virtuellen Computer im Benutzerflow zum Aktivieren der Replikation zuweisen. Alle anderen Ressourcen müssen (wie weiter unten angegeben) manuell in der Zielregion erstellt werden.

    Informationen zum Erstellen der für Sie relevanten und am häufigsten verwendeten Netzwerkressourcen auf Grundlage der Konfiguration des virtuellen Quellcomputers finden Sie in der folgenden Dokumentation:
    - [Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md)
    - [Load Balancer](../load-balancer/index.yml)
    -  [Öffentliche IP-Adresse](../virtual-network/virtual-network-public-ip-address.md)
    - Informationen zu anderen Netzwerkkomponenten finden Sie in der [Netzwerkdokumentation](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Vorbereiten
Die folgenden Schritte zeigen, wie Sie den virtuellen Computer mithilfe von Azure Site Recovery als Lösung auf das Verschieben vorbereiten. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Erstellen des Tresors in einer beliebigen Region außer der Quellregion

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Geben Sie im Suchfeld „Recovery Services“ ein, und klicken Sie auf „Recovery Services-Tresore“.
1. Klicken Sie im Menü „Recovery Services-Tresore“ auf „+ Hinzufügen“.
1. Geben Sie in **Name** den Anzeigenamen **ContosoVMVault** ein. Wenn Sie mehrere Abonnements haben, wählen Sie das gewünschte aus.
1. Erstellen Sie die Ressourcengruppe **ContosoRG**.
1. Geben Sie eine Azure-Region an. Eine Liste der unterstützten Regionen finden Sie in den Informationen zur geografischen Verfügbarkeit unter [Azure Site Recovery – Preise](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Wählen Sie unter **Recovery Services-Tresore** die Optionen **ContosoVMVault** > **Replizierte Elemente** >  **+Replizieren** aus.
1. Wählen Sie in der Dropdownliste **Azure Virtual Machines** aus.
1. Wählen Sie unter **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
1. Wählen Sie das Ressourcen-Manager-Bereitstellungsmodell. Wählen Sie dann das **Quellabonnement** und die **Quellressourcengruppe** aus.
1. Wählen Sie **OK** , um die Einstellungen zu speichern.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivieren der Replikation für Azure-VMs und Starten des Kopiervorgangs für die Daten

Site Recovery ruft eine Liste der VMs ab, die dem Abonnement und der Ressourcengruppe zugeordnet sind.

1. Wählen Sie im nächsten Schritt den virtuellen Computer aus, den Sie verschieben möchten, und wählen Sie dann **OK** aus.
1. Wählen Sie unter **Einstellungen** die Option **Notfallwiederherstellung** aus.
1. Wählen Sie unter **Notfallwiederherstellung konfigurieren** > **Zielregion** die Zielregion für die Replikation aus.
1. Übernehmen Sie für dieses Tutorial die anderen Standardwerte.
1. Wählen Sie **Replikation aktivieren** aus. Durch diesen Schritt wird ein Auftrag gestartet, um die Replikation des virtuellen Computers zu aktivieren.



## <a name="move"></a>Move

Die folgenden Schritte zeigen, wie die Verschiebung in die Zielregion durchgeführt wird.

1. Wechseln Sie zum Tresor. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** den virtuellen Computer aus, und wählen Sie dann **Failover** aus.
2. Wählen Sie unter **Failover** die Option **Neueste** aus.
3. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VM herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
4. Überprüfen Sie nach Abschluss des Auftrags, ob der virtuelle Computer wie erwartet in der Azure-Zielregion angezeigt wird.


## <a name="discard"></a>Verwerfen 

Wenn Sie den verschobenen virtuellen Computer überprüft haben und Änderungen am Point of Failover vornehmen müssen oder einen vorherigen Punkt wiederherstellen möchten, klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Wiederherstellungspunkt ändern** aus. In diesem Schritt haben Sie die Möglichkeit, einen anderen Wiederherstellungspunkt anzugeben und ein Failover auf diesen Punkt auszuführen. 


## <a name="commit"></a>Commit 

Wenn Sie den verschobenen virtuellen Computer überprüft haben und die Änderung committen möchten, klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Committen** aus. Durch diesen Schritt wird der Prozess zum Verschieben in die Zielregion abgeschlossen. Warten Sie, bis der Commitauftrag abgeschlossen ist.

## <a name="clean-up"></a>Bereinigung

Die folgenden Schritte dienen zur Bereinigung der Quellregion und der zugehörigen Ressourcen, die für die Verschiebung verwendet wurden.

Führen Sie für alle Ressourcen, die für die Verschiebung verwendet wurden, den folgenden Schritt aus:

- Wechseln Sie zum virtuellen Computer. Wählen Sie **Replikation deaktivieren** aus. Durch diesen Schritt wird der Prozess zum Kopieren der Daten für den virtuellen Computer beendet.

   > [!IMPORTANT]
   > Führen Sie diesen Schritt unbedingt aus, damit Ihnen keine Gebühren für die Azure Site Recovery-Replikation in Rechnung gestellt werden.

Führen Sie diese zusätzlichen Schritte aus, wenn Sie die Quellressourcen nicht wiederverwenden möchten:

1. Löschen Sie alle relevanten Netzwerkressourcen in der Quellregion, die Sie unter [Voraussetzungen](#prerequisites) festgelegt haben.
1. Löschen Sie das entsprechende Speicherkonto in der Quellregion.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen virtuellen Azure-Computer in eine andere Azure-Region verschoben. Jetzt können Sie die Notfallwiederherstellung für den verschobenen virtuellen Computer konfigurieren.

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung nach der Migration](azure-to-azure-quickstart.md)
