---
title: Verschieben von Azure IaaS-VMs in eine andere Azure-Region mithilfe des Azure Site Recovery-Diensts | Microsoft-Dokumentation
description: Verwenden Sie Azure Site Recovery, um virtuelle Azure IaaS-Computer von einer Azure-Region zu einer anderen zu migrieren.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 0d446be664d695af946d46abc48389d4f7be92cd
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65791040"
---
# <a name="move-azure-vms-to-another-region"></a>Verschieben virtueller Azure-Computer in eine andere Region

Es gibt verschiedene Szenarien, in denen Sie Ihre vorhandenen virtuellen Azure IaaS-Computer (Virtual Machines, VMs) aus einer Region in eine andere verschieben möchten. So möchten Sie z. B. die Zuverlässigkeit und Verfügbarkeit Ihrer vorhandenen VMs erhöhen, um die Verwaltbarkeit zu verbessern, oder Sie möchten die VMs aus Governancegründen verschieben. Weitere Informationen finden Sie in der [Übersicht über das Verschieben virtueller Azure-Computer](azure-to-azure-move-overview.md). 

Mit dem [Azure Site Recovery](site-recovery-overview.md)-Dienst können Sie die Notfallwiederherstellung von lokalen Computern und Azure-VMs zwecks Business Continuity und Disaster Recovery (BCDR) verwalten und orchestrieren. Sie können Azure Site Recovery auch verwenden, um das Verschieben von Azure-VMs in eine sekundäre Region zu verwalten.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> 
> * Überprüfen der Voraussetzungen für das Verschieben
> * Vorbereiten der virtuellen Quellcomputer und der Zielregion
> * Kopieren der Daten und Aktivieren der Replikation
> * Testen der Konfiguration und Durchführen der Verschiebung
> * Löschen der Ressourcen in der Quellregion
> 
> [!NOTE]
> In diesem Tutorial wird aufgezeigt, wie Sie Azure-VMs unverändert aus einer Region in eine andere Region verschieben. Wenn Sie die Verfügbarkeit durch Verschieben von VMs in einer Verfügbarkeitsgruppe auf an Zonen angeheftete VMs in einer anderen Region verbessern müssen, lesen Sie das [Tutorial „Verschieben virtueller Azure-Computer in Verfügbarkeitszonen“](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich die Azure-VMs in der Azure-Region befinden, aus der Sie verschieben möchten.
- Überprüfen Sie, ob die von Ihnen ausgewählte [Kombination aus Quell- und Zielregion unterstützt wird](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), und treffen Sie eine fundierte Entscheidung bezüglich der Zielregion.
- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](azure-to-azure-architecture.md) verstehen.
- Lesen Sie die [Einschränkungen und Anforderungen der Unterstützung](azure-to-azure-support-matrix.md).
- Überprüfen Sie die Kontoberechtigungen. Wenn Sie ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements. Wenn Sie nicht der Abonnementadministrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Um die Replikation für einen virtuellen Computer zu aktivieren und Daten mithilfe von Azure Site Recovery zu kopieren, benötigen Sie Folgendes:

    * Berechtigungen zum Erstellen einer VM in Azure-Ressourcen. Die integrierte Rolle „Mitwirkender für virtuelle Computer“ verfügt über diese Berechtigungen, zu den die folgenden gehören:
        - Berechtigung zum Erstellen einer VM in der ausgewählten Ressourcengruppe
        - Berechtigung zum Erstellen einer VM im ausgewählten virtuellen Netzwerk
        - Berechtigung zum Schreiben in das ausgewählte Speicherkonto

    * Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen. Die Rolle „Site Recovery-Mitwirkender“ verfügt über alle Berechtigungen, die zum Verwalten von Site Recovery-Vorgängen in einem Recovery Services-Tresor erforderlich sind.

## <a name="prepare-the-source-vms"></a>Vorbereiten der virtuellen Quellcomputer

1. Stellen Sie sicher, dass alle aktuellen Stammzertifikate auf den virtuellen Azure-Computern vorhanden sind, die Sie verschieben möchten. Wenn die aktuellen Stammzertifikate nicht auf dem virtuellen Computer vorhanden sind, können die Daten aufgrund von Sicherheitseinschränkungen nicht in die Zielregion kopiert werden.

    - Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung den Standardprozess für Windows Update und Zertifikatupdates in Ihrer Organisation durch.
    - Befolgen Sie bei virtuellen Linux-Computern die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste für den virtuellen Computer abzurufen.
1. Stellen Sie sicher, dass Sie keinen Authentifizierungsproxy zum Steuern der Netzwerkkonnektivität für die VMs verwenden, die Sie verschieben möchten.
1. Wenn der virtuelle Computer, den Sie verschieben möchten, keinen Zugriff auf das Internet hat oder einen Firewallproxy zum Steuern des ausgehenden Zugriffs verwendet, [überprüfen Sie die Anforderungen](azure-to-azure-tutorial-enable-replication.md#set-up-outbound-network-connectivity-for-vms).
1. Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dies schließt unter anderem Lastenausgleichsmodule, Netzwerksicherheitsgruppen (NSGs) und öffentliche IP-Adressen ein.

## <a name="prepare-the-target-region"></a>Vorbereiten der Zielregion

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

1. Stellen Sie sicher, dass Ihr Abonnement über ausreichend Ressourcen verfügt, um VMs zu unterstützen, deren Größe der Ihrer Quell-VMs entspricht. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wählt Site Recovery dieselbe oder eine möglichst ähnliche Größe für den virtuellen Zielcomputer aus.

1. Stellen Sie sicher, dass Sie für jede im Quellnetzwerklayout identifizierte Komponente eine Zielressource erstellen. Dieser Schritt ist wichtig, um sicherzustellen, dass Ihre VMs in der Zielregion über alle Funktionen und Features verfügen, die auch in der Quellregion verfügbar waren.

    > [!NOTE]
    > Azure Site Recovery erkennt und erstellt automatisch ein virtuelles Netzwerk, wenn Sie die Replikation für den virtuellen Quellcomputer aktivieren. Sie können auch vorab ein Netzwerk erstellen und es dem virtuellen Computer im Benutzerflow zum Aktivieren der Replikation zuweisen. Alle anderen Ressourcen müssen (wie weiter unten angegeben) manuell in der Zielregion erstellt werden.

     Informationen zum Erstellen der für Sie relevanten und am häufigsten verwendeten Netzwerkressourcen auf Grundlage der Konfiguration des virtuellen Quellcomputers finden Sie in der folgenden Dokumentation:

   - [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
   - [Load Balancer](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
   - [Öffentliche IP-Adresse](../virtual-network/virtual-network-public-ip-address.md)
    
     Informationen zu anderen Netzwerkkomponenten finden Sie in der [Netzwerkdokumentation](https://docs.microsoft.com/azure/#pivot=products&panel=network).

1. [Erstellen Sie manuell ein nicht für die Produktion vorgesehenes Netzwerk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) in der Zielregion, wenn Sie die Konfiguration vor dem endgültigen Verschieben in die Zielregion testen möchten. Dieser Schritt wird empfohlen, weil dadurch das Produktionsnetzwerk möglichst wenig beeinträchtigt wird.

## <a name="copy-data-to-the-target-region"></a>Kopieren von Daten in die Zielregion
In den folgenden Schritten wird erläutert, wie Sie Daten mithilfe von Azure Site Recovery in die Zielregion kopieren.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Erstellen des Tresors in einer beliebigen Region außer der Quellregion

1. Melden Sie sich unter [Azure-Portal](https://portal.azure.com) > **Recovery Services** an.
1. Wählen Sie **Ressource erstellen** > **Verwaltungstools** > **Backup & Site Recovery** aus.
1. Geben Sie in **Name** den Anzeigenamen **ContosoVMVault** ein. Wenn Sie mehrere Abonnements haben, wählen Sie das gewünschte aus.
1. Erstellen Sie die Ressourcengruppe **ContosoRG**.
1. Geben Sie eine Azure-Region an. Eine Liste der unterstützten Regionen finden Sie in den Informationen zur geografischen Verfügbarkeit unter [Azure Site Recovery – Preise](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Wählen Sie unter **Recovery Services-Tresore** die Optionen **Übersicht** > **ContosoVMVault** > **+ Replizieren** aus.
1. Wählen Sie unter **Quelle** die Option **Azure**.
1. Wählen Sie unter **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
1. Wählen Sie das Ressourcen-Manager-Bereitstellungsmodell. Wählen Sie dann das **Quellabonnement** und die **Quellressourcengruppe** aus.
1. Wählen Sie **OK**, um die Einstellungen zu speichern.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivieren der Replikation für Azure-VMs und Starten des Kopiervorgangs für die Daten

Site Recovery ruft eine Liste der VMs ab, die dem Abonnement und der Ressourcengruppe zugeordnet sind.

1. Wählen Sie im nächsten Schritt den virtuellen Computer aus, den Sie verschieben möchten, und wählen Sie dann **OK** aus.
1. Wählen Sie unter **Einstellungen** die Option **Notfallwiederherstellung** aus.
1. Wählen Sie unter **Notfallwiederherstellung konfigurieren** > **Zielregion** die Zielregion für die Replikation aus.
1. Übernehmen Sie für dieses Tutorial die anderen Standardwerte.
1. Wählen Sie **Replikation aktivieren** aus. Durch diesen Schritt wird ein Auftrag gestartet, um die Replikation des virtuellen Computers zu aktivieren.

    ![Aktivieren der Replikation](media/tutorial-migrate-azure-to-azure/settings.png)

## <a name="test-the-configuration"></a>Testen der Konfiguration

1. Wechseln Sie zum Tresor. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** den virtuellen Computer aus, den Sie in die Zielregion verschieben möchten, und wählen Sie dann **+ Testfailover** aus.
1. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt für das Failover aus:

   - **Letzte Verarbeitung**: Führt ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt aus, der vom Site Recovery-Dienst verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
   - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover aller virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert**: Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

1. Wählen Sie das virtuelle Azure-Zielnetzwerk aus, in das die Azure-VMs zum Testen der Konfiguration verschoben werden sollen.
    > [!IMPORTANT]
    > Es wird empfohlen, ein separates Azure-VM-Netzwerk für das Testfailover zu verwenden. Verwenden Sie nicht das Produktionsnetzwerk, das beim Aktivieren der Replikation eingerichtet wurde und in das Sie Ihre VMs verschieben möchten.
1. Klicken Sie zum Testen der Verschiebung auf **OK**. Klicken Sie zum Überwachen des Fortschritts auf die VM, um die Eigenschaften aufzurufen. Alternativ können Sie auf den Auftrag **Testfailover** unter dem Tresornamen und dann auf **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** klicken.
1. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass der virtuelle Computer ausgeführt wird, die richtige Größe aufweist und mit dem entsprechenden Netzwerk verbunden ist.
1. Wenn Sie den beim Testen der Verschiebung erstellten virtuellen Computer löschen möchten, klicken Sie für das replizierte Element auf **Testfailover bereinigen**. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Test.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Durchführen und Bestätigen der Verschiebung in die Zielregion

1. Wechseln Sie zum Tresor. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** den virtuellen Computer aus, und wählen Sie dann **Failover** aus.
1. Wählen Sie unter **Failover** die Option **Neueste** aus.
1. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VM herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
1. Überprüfen Sie nach Abschluss des Auftrags, ob der virtuelle Computer wie erwartet in der Azure-Zielregion angezeigt wird.
1. Klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie dann **Commit** aus. Durch diesen Schritt wird der Prozess zum Verschieben in die Zielregion abgeschlossen. Warten Sie, bis der Commitauftrag abgeschlossen ist.

## <a name="delete-the-resource-in-the-source-region"></a>Löschen der Ressource in der Quellregion

Wechseln Sie zum virtuellen Computer. Wählen Sie **Replikation deaktivieren** aus. Durch diesen Schritt wird der Prozess zum Kopieren der Daten für den virtuellen Computer beendet.

> [!IMPORTANT]
> Führen Sie diesen Schritt unbedingt aus, damit Ihnen keine Gebühren für die Azure Site Recovery-Replikation in Rechnung gestellt werden.

Führen Sie die folgenden Schritte aus, wenn Sie die Quellressourcen nicht wiederverwenden möchten:

1. Löschen Sie alle relevanten Netzwerkressourcen in der Quellregion, die Sie in Schritt 4 des Abschnitts [Vorbereiten der virtuellen Quellcomputer](#prepare-the-source-vms) aufgelistet haben.
1. Löschen Sie das entsprechende Speicherkonto in der Quellregion.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen virtuellen Azure-Computer in eine andere Azure-Region verschoben. Jetzt können Sie die Notfallwiederherstellung für den verschobenen virtuellen Computer konfigurieren.

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung nach der Migration](azure-to-azure-quickstart.md)

