---
title: Verschieben von Azure-VMs in eine andere Region mit Azure Site Recovery
description: Verwenden Sie Azure Site Recovery, um virtuelle Azure IaaS-Computer von einer Azure-Region zu einer anderen zu migrieren.
services: site-recovery
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: 5ae930240872c00c8dbb45857e4e77d82766eadf
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398061"
---
# <a name="move-azure-vms-to-another-azure-region"></a>Verschieben von Azure-VMs in eine andere Azure-Region

Vielleicht möchten Sie Azure Infrastructure-as-a-Service-VMs (IaaS) von einer Region in eine andere verschieben, um die Zuverlässigkeit, Verfügbarkeit, Verwaltung oder Governance zu verbessern. In diesem Tutorial erfahren Sie, wie Sie VMs mithilfe von Azure Site Recovery in eine andere Region verschieben. Sie lernen Folgendes:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Vorbereiten der virtuellen Quellcomputer
> * Vorbereiten der Zielregion
> * Kopieren von Daten in die Zielregion
> * Testen der Konfiguration
> * Durchführen der Verschiebung
> * Verwerfen der Ressourcen in der Quellregion


> [!IMPORTANT]
> In diesem Artikel erfahren Sie, wie Sie Azure-VMs *ohne Änderungen* zwischen Azure-Regionen verschieben. Wenn Sie die Verfügbarkeit Ihrer Infrastruktur durch Verschieben von VMs in Verfügbarkeitszonen verbessern möchten, finden Sie unter [Verschieben virtueller Azure-Computer in Verfügbarkeitszonen](move-azure-vms-avset-azone.md) weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass in der Azure-Quellregion, *aus* der Sie VMs verschieben möchten, Azure-VMs vorhanden sind.
- Überprüfen Sie, ob die von Ihnen ausgewählte [Kombination von Quell- und Zielregion unterstützt wird](./azure-to-azure-support-matrix.md#region-support), und wählen Sie die Zielregion sorgfältig aus.
- Stellen Sie sicher, dass Sie die [Architektur und die Komponenten des Szenarios](azure-to-azure-architecture.md) verstehen.
- Lesen Sie die [Einschränkungen und Anforderungen der Unterstützung](azure-to-azure-support-matrix.md).
- Überprüfen Sie die Kontoberechtigungen. Wenn Sie gerade ein kostenloses Azure-Konto erstellt haben, sind *Sie* der Administrator Ihres Abonnements. Falls Sie nicht der Administrator sind, bitten Sie den Administrator, Ihnen die erforderlichen Berechtigungen zuzuweisen:
  -  Um die Replikation für eine VM zu aktivieren und Daten mithilfe von Site Recovery in das Ziel zu kopieren, benötigen Sie Berechtigungen zum Erstellen einer VM in Ihren Azure-Ressourcen. Die integrierte Rolle „Mitwirkender für virtuelle Computer“ verfügt über diese Berechtigungen. Mit den Berechtigungen können Sie folgende Aufgaben ausführen:
        - Erstellen einer VM in der ausgewählten Ressourcengruppe
        - Erstellen einer VM im ausgewählten virtuellen Netzwerk
        - Schreiben in das ausgewählte Speicherkonto

  - Sie benötigen außerdem Berechtigungen zum Verwalten von Site Recovery-Vorgängen. Die Rolle „Site Recovery-Mitwirkender“ verfügt über alle Berechtigungen, die zum Verwalten von Site Recovery-Vorgängen in einem Azure Recovery Services-Tresor erforderlich sind.

## <a name="prepare-the-source-vms"></a>Vorbereiten der virtuellen Quellcomputer

1. Überprüfen Sie, ob die Azure-VMs, die Sie verschieben möchten, über die aktuellen Stammzertifikate verfügen. Wenn dies nicht der Fall ist, kann das Kopieren von Daten in die Zielregion aufgrund von Sicherheitseinschränkungen nicht aktiviert werden.

    - Installieren Sie die aktuellen Windows-Updates auf Windows-VMs, damit alle vertrauenswürdigen Stammzertifikate auf den Computern vorhanden sind. Führen Sie in einer nicht verbundenen Umgebung den Standardprozess für Windows Update und Zertifikatupdates in Ihrer Organisation durch.
    - Befolgen Sie bei Linux-VMs die Anleitung des Linux-Distributors, um die aktuellen vertrauenswürdigen Stammzertifikate und die Zertifikatsperrliste abzurufen.
2. Stellen Sie sicher, dass Sie keinen Authentifizierungsproxy zum Steuern der Netzwerkkonnektivität für die VMs verwenden, die Sie verschieben möchten.
3. Falls eine VM, die Sie verschieben möchten, keinen Zugriff auf das Internet hat und einen Firewallproxy zum Steuern des ausgehenden Zugriffs verwendet, überprüfen Sie die [Anforderungen](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).
4. Dokumentieren Sie zur Überprüfung das Quellnetzwerklayout und alle Ressourcen, die Sie derzeit verwenden, einschließlich, aber nicht beschränkt auf Lastenausgleichsmodule, Netzwerksicherheitsgruppen und öffentliche IP-Adressen.

## <a name="prepare-the-target-region"></a>Vorbereiten der Zielregion

1. Vergewissern Sie sich, dass Ihr Azure-Abonnement das Erstellen von VMs in der für die Notfallwiederherstellung verwendeten Zielregion zulässt. Wenden Sie sich ggf. an den Support, um das erforderliche Kontingent zu aktivieren.

2. Stellen Sie sicher, dass Ihr Abonnement über ausreichende Ressourcen verfügt, um die Quell-VMs zu unterstützen. Wenn Sie Site Recovery verwenden, um Daten in die Zielregion zu kopieren, wird die gleiche oder eine möglichst ähnliche verfügbare Größe für die Ziel-VMs ausgewählt.

3. Stellen Sie sicher, dass Sie für jede im Quellnetzwerklayout identifizierte Komponente eine Zielressource erstellen. Dadurch wird sichergestellt, dass Ihre VMs in der Zielregion über alle Funktionen und Features verfügen, die sie in der Quellregion hatten.

   Azure Site Recovery erkennt und erstellt automatisch ein virtuelles Netzwerk und Speicherkonto, wenn Sie die Replikation für den virtuellen Quellcomputer aktivieren. Sie können diese Ressourcen auch vorab erstellen und sie beim Aktivieren der Replikation der VM zuweisen. Alle anderen Ressourcen in der Zielregion müssen jedoch manuell erstellt werden. Informationen zum Erstellen der am häufigsten verwendeten Netzwerkressourcen auf Grundlage der Quell-VM-Konfiguration finden Sie in den folgenden Dokumenten:

   - [Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md)
   - [Load Balancer](../load-balancer/index.yml)
   - [Öffentliche IP-Adresse](../virtual-network/virtual-network-public-ip-address.md)
    
   Informationen zu anderen Netzwerkkomponenten finden Sie in der [Dokumentation zum Azure-Netzwerk](../index.yml?pivot=products&panel=network). 

4. Wenn Sie die Konfiguration vor dem Verschieben testen möchten, [erstellen Sie manuell ein nicht für die Produktion vorgesehenes Netzwerk](../virtual-network/quick-create-portal.md) in der Zielregion. Das Testen der Einrichtung verursacht nur minimale Beeinträchtigungen in der Produktionsumgebung und wird empfohlen.
    
## <a name="copy-data-to-the-target-region"></a>Kopieren von Daten in die Zielregion
In den folgenden Schritten werden Daten mithilfe von Azure Site Recovery in die Zielregion kopiert.

### <a name="create-the-vault-in-any-region-except-the-source"></a>Erstellen des Tresors in einer beliebigen Region außer der Quellregion

1. Melden Sie sich unter [Azure-Portal](https://portal.azure.com) > **Recovery Services** an.
2. Wählen Sie **Ressource erstellen** > **Verwaltungstools** > **Backup & Site Recovery** aus.
3. Geben Sie im Feld **Name** den Anzeigenamen **ContosoVMVault** ein. Wenn Sie mehrere Abonnements haben, wählen Sie das gewünschte aus.
4. Erstellen Sie die Ressourcengruppe **ContosoRG**.
5. Geben Sie eine Azure-Region an. Informationen zu den unterstützten Regionen finden Sie unter [Site Recovery Preise](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Klicken Sie unter „Recovery Services-Tresore“ auf **Übersicht** > **ConsotoVMVault** >  **+Replizieren**.
7. Wählen Sie unter **Quelle** die Option **Azure** aus.
8. Wählen Sie unter **Quellstandort** die Azure-Quellregion aus, in der Ihre VMs derzeit ausgeführt werden.
9. Wählen Sie das Azure Resource Manager-Bereitstellungsmodell aus. Wählen Sie dann das **Quellabonnement** und die **Quellressourcengruppe** aus.
10. Wählen Sie **OK** , um die Einstellungen zu speichern.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Aktivieren der Replikation für Azure-VMs und Starten des Kopiervorgangs für die Daten

Site Recovery ruft eine Liste der VMs ab, die dem Abonnement und der Ressourcengruppe zugeordnet sind.

1. Wählen Sie die VM aus, die Sie verschieben möchten, und klicken Sie dann auf **OK**.
2. Wählen Sie unter **Einstellungen** die Option **Notfallwiederherstellung** aus.
3. Wählen Sie unter **Notfallwiederherstellung konfigurieren** > **Zielregion** die Zielregion für die Replikation aus.
4. Verwenden Sie die Standardzielressourcen oder die Ressourcen, die Sie vorab erstellt haben.
5. Wählen Sie **Replikation aktivieren** aus, um den Auftrag zu starten.


 

## <a name="test-the-configuration"></a>Testen der Konfiguration


1. Wechseln Sie zum Tresor. Wählen Sie unter **Einstellungen** > **Replizierte Elemente** die VM aus, die Sie in die Zielregion verschieben möchten. Wählen Sie dann **Testfailover** aus.
2. Wählen Sie unter **Testfailover** einen Wiederherstellungspunkt für das Failover aus:

   - **Letzte Verarbeitung** : Führt ein Failover des virtuellen Computers auf den letzten Wiederherstellungspunkt aus, der vom Site Recovery-Dienst verarbeitet wurde. Der Zeitstempel wird angezeigt. Bei dieser Option wird der RTO-Wert (Recovery Time Objective) niedrig gehalten, da keine Zeit für die Verarbeitung von Daten aufgewendet wird.
   - **Letzter anwendungskonsistenter Zeitpunkt** : Führt ein Failover aller VMs auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
   - **Benutzerdefiniert** : Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

3. Wählen Sie das virtuelle Azure-Zielnetzwerk aus, in das die Azure-VMs zum Testen der Konfiguration verschoben werden sollen.

   > [!IMPORTANT]
   > Wir empfehlen, für das Testfailover ein separates Azure-VM-Netzwerk zu verwenden und nicht das Produktionsnetzwerk in der Zielregion.

4. Wählen Sie zum Testen der Verschiebung **OK** aus. Wählen Sie zum Überwachen des Status die VM aus, um ihre **Eigenschaften** anzuzeigen. Alternativ können Sie den Auftrag **Testfailover** im Tresor auswählen. Wählen Sie dann **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge** aus.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Stellen Sie sicher, dass die VM ausgeführt wird, die passende Größe hat und mit dem entsprechenden Netzwerk verbunden ist.
6. Wählen Sie zum Löschen der VM, die Sie zum Testen erstellt haben, die Option **Testfailover bereinigen** für das replizierte Element aus. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Test.

## <a name="perform-the-move-and-confirm"></a>Durchführen der Verschiebung und Bestätigen

1. Navigieren Sie unter **Einstellungen** > **Replizierte Elemente** zum Tresor, wählen Sie die VM und dann **Failover** aus.
1. Wählen Sie unter **Failover** die Option **Neueste** aus. 
2. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Site Recovery versucht, die Quell-VM herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird jedoch auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
3. Überprüfen Sie nach Abschluss des Auftrags, ob die VM wie erwartet in der Azure-Zielregion angezeigt wird.
4. Klicken Sie unter **Replizierte Elemente** mit der rechten Maustaste auf die VM, und wählen Sie **Commit ausführen** aus. Dadurch wird die Verschiebung abgeschlossen. Warten Sie, bis der Commitauftrag abgeschlossen ist.

## <a name="discard-the-resources-from-the-source-region"></a>Verwerfen der Ressourcen in der Quellregion

- Navigieren Sie zur jeweiligen VM, und wählen Sie **Replikation deaktivieren** aus. Dadurch wird der Prozess zum Kopieren der Daten für den virtuellen Computer angehalten.

  > [!IMPORTANT]
  > Führen Sie diesen Schritt aus, damit Ihnen nach der Verschiebung keine Gebühren für die Site Recovery-Replikation in Rechnung gestellt werden.

Wenn Sie die Quellressourcen nicht wiederverwenden möchten, gehen Sie wie folgt vor:

1. Löschen Sie alle relevanten Netzwerkressourcen in der Quellregion, die Sie in Schritt 4 [Vorbereiten der Quell-VMs](#prepare-the-source-vms) aufgelistet haben.
2. Löschen Sie das entsprechende Speicherkonto in der Quellregion.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie erfahren, wie Sie Azure-VMs in eine andere Azure-Region verschieben. Nun können Sie die Notfallwiederherstellung für diese VMs konfigurieren.

> [!div class="nextstepaction"]
> [Einrichten der Notfallwiederherstellung nach der Migration](azure-to-azure-quickstart.md)
