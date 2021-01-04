---
title: Verschieben eines virtuellen Computers in eine andere Region (Azure Site Recovery)
description: Erfahren Sie, wie Sie Ihre virtuellen SQL Server-Computer in Azure von einer Region in eine andere migrieren können.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: migration
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/30/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 789554121af1c83d9077e6153ca9db01477bde25
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360151"
---
# <a name="move-a-sql-server-vm-to-another-region-within-azure-with-azure-site-recovery"></a>Verschieben einer SQL Server-VM in eine andere Azure-Region mit Azure Site Recovery
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In diesem Artikel erfahren Sie, wie Sie mit Azure Site Recovery Ihren virtuellen SQL Server-Computer (VM) in Azure von einer Region zu einer anderen migrieren. 

Zum Verschieben einer SQL Server-VM in eine andere Region sind folgende Schritte erforderlich:
1. [Vorbereitung](#prepare-to-move): Vergewissern Sie sich, dass sowohl die Quell-SQL Server-VM als auch die Zielregion angemessen für die Verschiebung vorbereitet sind. 
1. [Konfigurieren:](#configure-azure-site-recovery-vault) Sie können eine SQL Server-VM nur verschieben, wenn es sich um ein repliziertes Objekt im Azure Site Recovery-Tresor handelt. Sie müssen Ihre SQL Server-VM dem Azure Site Recovery-Tresor hinzufügen. 
1. [Testen:](#test-move-process) Zum Migrieren der SQL Server-VM muss ein Failover von der Quellregion in die replizierte Zielregion durchgeführt werden. Um sicherzustellen, dass die Verschiebung erfolgreich ist, müssen Sie zunächst überprüfen, ob für Ihre SQL Server-VM ein Failover in die Zielregion ausgeführt werden kann. Dadurch lassen sich Probleme erkennen und bei der eigentlichen Verschiebung vermeiden. 
1. [Verschieben:](#move-the-sql-server-vm) Nach dem erfolgreichen Testfailover wissen Sie, dass Sie die SQL Server-VM problemlos migrieren können, und können die VM in die Zielregion verschieben. 
1. [Bereinigen:](#clean-up-source-resources) Entfernen Sie die SQL Server-VM aus dem Tresor und alle verbleibenden nicht benötigten Ressourcen aus der Ressourcengruppe, um Abrechnungsgebühren zu vermeiden. 

## <a name="verify-prerequisites"></a>Überprüfen der Voraussetzungen 

- Vergewissern Sie sich, dass der Wechsel von der Quellregion in die Zielregion [unterstützt wird](../../../site-recovery/azure-to-azure-support-matrix.md#region-support).  
- Überprüfen Sie [Architektur und Komponenten des Szenarios](../../../site-recovery/azure-to-azure-architecture.md) sowie die [Supportbeschränkungen und -anforderungen](../../../site-recovery/azure-to-azure-support-matrix.md). 
- Überprüfen Sie die Kontoberechtigungen. Wenn Sie ein kostenloses Azure-Konto erstellt haben, sind Sie der Administrator Ihres Abonnements. Wenn Sie nicht der Abonnementadministrator sind, wenden Sie sich an den Administrator, damit dieser Ihnen die erforderlichen Berechtigungen zuweist. Um die Replikation für einen virtuellen Computer zu aktivieren und Daten mithilfe von Azure Site Recovery zu kopieren, benötigen Sie Folgendes: 
    - Berechtigungen zum Erstellen eines virtuellen Computers. Die integrierte Rolle *Mitwirkender für virtuelle Computer* verfügt über diese Berechtigungen. Dazu zählen unter anderem: 
        - Berechtigungen zum Erstellen eines virtuellen Computers in der ausgewählten Ressourcengruppe. 
        - Berechtigungen zum Erstellen eines virtuellen Computers im ausgewählten virtuellen Netzwerk. 
        - Berechtigungen zum Schreiben in das ausgewählte Speicherkonto. 
      - Berechtigungen zum Verwalten von Azure Site Recovery-Vorgängen. Die Rolle *Site Recovery-Mitwirkender* verfügt über alle Berechtigungen, die zum Verwalten von Site Recovery-Vorgängen in einem Recovery Services-Tresor erforderlich sind.  

## <a name="prepare-to-move"></a>Vorbereiten der Verschiebung
Bereiten Sie sowohl die Quell-SQL Server-VM als auch die Zielregion für die Verschiebung vor. 

### <a name="prepare-the-source-sql-server-vm"></a>Vorbereiten der Quell-SQL Server-VM

- Stellen Sie sicher, dass auf der SQL Server-VM, die Sie verschieben möchten, alle aktuellen Stammzertifikate vorhanden sind. Wenn die aktuellen Stammzertifikate nicht vorhanden sind, können die Daten aufgrund von Sicherheitseinschränkungen nicht in die Zielregion kopiert werden. 
- Installieren Sie alle aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf dem Computer vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung das Standardverfahren für Windows- und Zertifikatupdates in Ihrer Organisation durch. 
- Befolgen Sie bei virtuellen Linux-Computern die Anleitung Ihres Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste für den virtuellen Computer abzurufen. 
- Stellen Sie sicher, dass Sie zum Steuern der Netzwerkkonnektivität für die VM, die Sie verschieben möchten, keinen Authentifizierungsproxy verwenden. 
- Wenn die VM, die Sie verschieben möchten, keinen Zugriff auf das Internet hat oder einen Firewallproxy zum Steuern des ausgehenden Zugriffs verwendet, überprüfen Sie die Anforderungen. 
- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dies schließt unter anderem Lastenausgleichsmodule, Netzwerksicherheitsgruppen (NSGs) und öffentliche IP-Adressen ein. 

### <a name="prepare-the-target-region"></a>Vorbereiten der Zielregion

- Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren. 
- Stellen Sie sicher, dass Ihr Abonnement über ausreichende Ressourcen für VMs verfügt, deren Größe der Ihrer Quell-VMs entspricht. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wählt Site Recovery dieselbe oder eine möglichst ähnliche Größe für den virtuellen Zielcomputer aus. 
- Stellen Sie sicher, dass Sie für jede im Quellnetzwerklayout identifizierte Komponente eine Zielressource erstellen. Dieser Schritt ist wichtig, um sicherzustellen, dass Ihre VMs in der Zielregion über alle Funktionen und Features verfügen, die auch in der Quellregion verfügbar waren. 
    - Azure Site Recovery erkennt und erstellt automatisch ein virtuelles Netzwerk, wenn Sie die Replikation für den virtuellen Quellcomputer aktivieren. Sie können auch vorab ein Netzwerk erstellen und es der VM im Benutzerflow zum Aktivieren der Replikation zuweisen. Alle weiteren Ressourcen in der Zielregion müssen manuell erstellt werden.
- Informationen zum Erstellen der für Sie relevanten und am häufigsten verwendeten Netzwerkressourcen auf Grundlage der Konfiguration des virtuellen Quellcomputers finden Sie in der folgenden Dokumentation: 
    - [Netzwerksicherheitsgruppen](../../../virtual-network/tutorial-filter-network-traffic.md) 
    - [Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-internal-portal.md)
    - [Öffentliche IP-Adresse](../../../virtual-network/virtual-network-public-ip-address.md)
    - Informationen zu weiteren Netzwerkkomponenten finden Sie in der [Netzwerkdokumentation](../../../virtual-network/virtual-networks-overview.md).
- Erstellen Sie manuell ein nicht für die Produktion vorgesehenes Netzwerk in der Zielregion, wenn Sie die Konfiguration vor dem endgültigen Verschieben in die Zielregion testen möchten. Dieser Schritt wird empfohlen, weil dadurch das Produktionsnetzwerk möglichst wenig beeinträchtigt wird. 

## <a name="configure-azure-site-recovery-vault"></a>Konfigurieren des Azure Site Recovery-Tresors

In den folgenden Schritten wird erläutert, wie Sie Daten mithilfe von Azure Site Recovery in die Zielregion kopieren. Erstellen Sie den Recovery Services-Tresor in einer beliebigen anderen Region als der Quellregion. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Wählen Sie in der linken oberen Ecke des Navigationsbereichs **Ressource erstellen** aus. 
1. Wählen Sie **IT- und Verwaltungstools** und dann **Backup und Site Recovery** aus. 
1. Erstellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** eine neue Ressourcengruppe in der Zielregion, oder wählen Sie eine vorhandene Ressourcengruppe in der Zielregion aus. 
1. Geben Sie unter **Instanzdetails** einen Namen für den Tresor an, und wählen Sie dann in der Dropdownliste die **Zielregion** aus. 
1. Wählen Sie **Bewerten + erstellen** aus, um den Recovery Services-Tresor zu erstellen. 
1. Wählen Sie in der linken oberen Ecke des Navigationsbereichs **Alle Dienste** aus, und geben Sie im Suchfeld die Zeichenfolge `recovery services` ein. 
1. (Optional:) Wählen Sie den Stern neben **Recovery Services-Tresore** aus, um diese Option der Schnellnavigationsleiste hinzuzufügen. 
1. Wählen Sie **Recovery Services-Tresore** und dann den erstellten Recovery Services-Tresor aus. 
1. Wählen Sie im Bereich **Übersicht** die Option **Replizieren** aus. 

   ![Konfigurieren der Replikation](./media/move-sql-vm-different-region/configure-replication.png)

1. Wählen Sie **Quelle** und dann als Quelle **Azure** aus. Wählen Sie die entsprechenden Werte für die anderen Dropdownfelder aus, z. B. den Standort für Ihre Quell-VMs. Nur Ressourcengruppen in der Region **Quellstandort** werden im Feld **Quellressourcengruppe** angezeigt. 
1. Wählen Sie **Virtuelle Computer** und dann die VMs aus, die Sie migrieren möchten. Wählen Sie zum Speichern Ihrer VM-Auswahl **OK** aus. 
1. Wählen Sie **Einstellungen** und dann in der Dropdownliste den **Zielstandort** aus. Dies sollte die Ressourcengruppe sein, die Sie zuvor vorbereitet haben. 
1. Nachdem Sie die Replikation angepasst haben, wählen Sie **Zielressourcen erstellen** aus, um die Ressourcen am neuen Standort zu erstellen. 
1. Wählen Sie nach Abschluss der Erstellung der Ressource **Replikation aktivieren** aus, um die Replikation Ihrer SQL Server-VM von der Quell- in die Zielregion zu starten.
1. Sie können den Status der Replikation überprüfen, indem Sie zu Ihrem Wiederherstellungstresor navigieren, **Replizierte Elemente** auswählen und den **Status** Ihrer SQL Server-VM anzeigen. Der Status **Geschützt** gibt an, dass die Replikation abgeschlossen wurde. 

   ![Überprüfen des Replikationsstatus](./media/move-sql-vm-different-region/check-replication-status.png)

## <a name="test-move-process"></a>Testen des Verschiebungsvorgangs
In den folgenden Schritten wird gezeigt, wie Sie den Verschiebungsvorgang mithilfe von Azure Site Recovery testen. 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Recovery Services-Tresor**, und wählen Sie **Replizierte Elemente** aus. 
1. Wählen Sie die SQL Server-VM aus, die Sie verschieben möchten, vergewissern Sie sich, dass der **Replikationsstatus** als **Fehlerfrei** angezeigt wird, und wählen Sie dann **Failover testen** aus. 

   ![Testen des Failovers für Ihre VM](./media/move-sql-vm-different-region/test-failover-of-replicated-vm.png)

1. Wählen Sie auf der Seite **Testfailover** den Wiederherstellungspunkt **Letzte App-Konsistenz** für das Failover aus, da dies der einzige Momentaufnahmetyp ist, der Datenkonsistenz für SQL Server gewährleisten kann. 
1. Wählen Sie unter **Virtuelles Azure-Netzwerk** den Namen des virtuellen Netzwerks und dann **OK** aus, um das Failover zu testen. 
   
   >[!IMPORTANT]
   > Es wird empfohlen, ein separates Azure-VM-Netzwerk für den Failovertest zu verwenden. Verwenden Sie nicht das Produktionsnetzwerk, das beim Aktivieren der Replikation eingerichtet wurde und in das Sie Ihre VMs verschieben möchten. 

1. Navigieren Sie zum Überwachen des Fortschritts zu Ihrem Tresor, wählen Sie unter **Überwachung** die Option **Site Recovery-Aufträge** aus, und wählen Sie dann den derzeit ausgeführten **Testfailover**-Auftrag aus.

   ![Überwachen des Fortschritts des Failovertests](./media/move-sql-vm-different-region/monitor-failover-test-job.png)

1. Navigieren Sie nach Abschluss des Tests im Portal zu **Virtuelle Computer**, und überprüfen Sie den neu erstellten virtuellen Computer. Stellen Sie sicher, dass die SQL Server-VM ausgeführt wird, die richtige Größe aufweist und mit dem richtigen Netzwerk verbunden ist. 
1. Löschen Sie die VM, die im Rahmen des Tests erstellt wurde, da die Option **Failover** ausgegraut ist, bis die Ressourcen des Failovertests bereinigt wurden. Navigieren Sie zurück zum Tresor, wählen Sie **Replizierte Elemente**, anschließend die SQL Server-VM und dann **Testfailover bereinigen** aus. Erfassen und speichern Sie alle Beobachtungen im Zusammenhang mit dem Test im Abschnitt **Notizen**, und aktivieren Sie das Kontrollkästchen **Die Tests sind abgeschlossen. Löschen Sie die virtuellen Computer für das Testfailover**. Wählen Sie **OK** aus, um die Ressourcen nach dem Test zu bereinigen. 

   ![Bereinigen von Elementen nach dem Failovertest](./media/move-sql-vm-different-region/cleanup-test-items.png)

## <a name="move-the-sql-server-vm"></a>Verschieben der SQL Server-VM 
In den folgenden Schritten wird gezeigt, wie Sie die SQL Server-VM aus der Quellregion in die Zielregion verschieben. 

1. Navigieren Sie zum **Recovery Services**-Tresor, wählen Sie **Replizierte Elemente**, anschließend die VM und dann **Failover** aus. 

   ![Initiieren des Failovers](./media/move-sql-vm-different-region/initiate-failover.png)

1. Wählen Sie unter **Wiederherstellungspunkt** den Wiederherstellungspunkt **Letzte App-Konsistenz** aus. 
1. Aktivieren Sie das Kontrollkästchen **Shut down the machine before beginning failover** (Computer vor Beginn des Failovers herunterfahren). Site Recovery fährt die Quell-VM herunter, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn beim Herunterfahren ein Fehler auftritt. 
1. Wählen Sie **OK** aus, um das Failover zu starten.
1. Sie können den Failovervorgang auf der gleichen Seite **Site Recovery-Aufträge** überwachen, die Sie beim Überwachen des Failovertests im vorherigen Abschnitt verwendet haben. 
1. Vergewissern Sie sich nach Abschluss des Auftrags, dass die SQL Server-VM wie erwartet in der Zielregion angezeigt wird. 
1. Navigieren Sie zurück zum Tresor, wählen Sie **Replizierte Elemente**, anschließend die SQL Server-VM und dann **Commit** aus, um den Verschiebungsvorgang in die Zielregion abzuschließen. Warten Sie, bis der Commitauftrag abgeschlossen ist. 
1. Registrieren Sie Ihre SQL Server-VM mit der Erweiterung für den SQL-IaaS-Agent, um die Verwaltbarkeit von **virtuellen SQL-Computern** im Azure-Portal sowie die Nutzung von Features zu ermöglichen, die der Erweiterung zugeordnet sind. Weitere Informationen finden Sie unter [Registrieren der SQL Server-VM mit der Erweiterung für den SQL-IaaS-Agent](sql-agent-extension-manually-register-single-vm.md). 

  > [!WARNING]
  > SQL Server-Datenkonsistenz ist nur bei App-konsistenten Momentaufnahmen sichergestellt. Die Momentaufnahme **Letzte Verarbeitung** kann nicht für SQL Server-Failover verwendet werden, da mit einer Momentaufnahme der Wiederherstellung nach einem Absturz keine SQL Server-Datenkonsistenz gewährleistet werden kann. 

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen
Entfernen Sie die SQL Server-VM aus dem Tresor, und löschen Sie alle nicht benötigten zugehörigen Ressourcen, um Abrechnungsgebühren zu vermeiden. 

1. Navigieren Sie zurück zum **Site Recovery**-Tresor, wählen Sie **Replizierte Elemente** aus, und wählen Sie dann die SQL Server-VM aus. 
1. Wählen Sie **Replikation deaktivieren** aus. Wählen Sie einen Grund zum Deaktivieren des Schutzes aus, und wählen Sie dann **OK** aus, um die Replikation zu deaktivieren. 

   >[!IMPORTANT]
   > Führen Sie diesen Schritt unbedingt aus, damit Ihnen keine Gebühren für die Azure Site Recovery-Replikation in Rechnung gestellt werden. 

1. Wenn Sie die Ressourcen in der Quellregion nicht wiederverwenden möchten, löschen Sie alle relevanten Netzwerkressourcen und die zugehörigen Speicherkonten. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln: 

* [Übersicht über SQL Server auf einem virtuellen Windows-Computer](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Häufig gestellte Fragen zu SQL Server auf virtuellen Windows-Computern in Azure](frequently-asked-questions-faq.md)
* [Preisinformationen für Azure-VMs mit SQL Server](pricing-guidance.md)
* [SQL Server on Azure Virtual Machine release notes](doc-changes-updates-release-notes.md) (SQL Server auf virtuellem Azure-Computer – Versionshinweise)