---
title: Automatisieren der Migration zahlreicher virtueller Computer zu Azure | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Skripts verwenden, um eine große Anzahl virtueller Computer mithilfe von Azure Site Recovery zu migrieren.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: f90140e9464ee72e9ceae8ca140bd060c51aade8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60597110"
---
# <a name="scale-migration-of-vms-using-azure-site-recovery"></a>Skalieren der Migration virtueller Computer mithilfe von Azure Site Recovery

In diesem Artikel wird erläutert, wie Sie mithilfe von Skripts und Azure Site Recovery eine große Anzahl virtueller Computer migrieren. Diese Skripts stehen auf GitHub im Repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) (Azure PowerShell-Beispiele) zum Download zur Verfügung. Die Skripts können zum Migrieren von virtuellen VMware-, AWS- und GCP-Computern sowie von physischen Servern zu Azure verwendet werden sowie die Migration auf verwaltete Datenträger unterstützen. Sie können mit diesen Skripts auch virtuelle Hyper-V-Computer migrieren, wenn Sie diese als physische Server migrieren. Die Skripts nutzen Azure Site Recovery-PowerShell. Eine entsprechende Dokumentation finden Sie [hier](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Aktuelle Einschränkungen:
- Die Angabe der statischen IP-Adresse wird nur für den primären Netzwerkadapter des virtuellen Zielcomputers unterstützt.
- Die Skripts akzeptieren keine Eingaben im Zusammenhang mit Azure-Hybridvorteil. Sie müssen die Eigenschaften des replizierten virtuellen Computers manuell im Portal aktualisieren.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie beginnen, müssen Sie folgende Schritte ausführen:
- Vergewissern Sie sich, dass der Site Recovery-Tresor in Ihrem Azure-Abonnement erstellt wurde.
- Vergewissern Sie sich, dass der Konfigurationsserver und der Prozessserver in der Quellumgebung installiert sind und der Tresor die Umgebung ermitteln kann.
- Vergewissern Sie sich, dass eine Replikationsrichtlinie erstellt und dem Konfigurationsserver zugeordnet wurde.
- Stellen Sie sicher, dass Sie dem Konfigurationsserver (der zum Replizieren der lokalen virtuellen Computer verwendet wird) das VM-Administratorkonto hinzugefügt haben.
- Vergewissern Sie sich, dass die Zielartefakte in Azure erstellt wurden.
    - Zielressourcengruppe
    - Zielspeicherkonto (und dessen Ressourcengruppe): Erstellen Sie ein Storage Premium-Konto, wenn Sie die Migration auf Premium-verwaltete Datenträger planen.
    - Cachespeicherkonto (und dessen Ressourcengruppe): Erstellen Sie ein Storage Standard-Konto in derselben Region wie der Tresor.
    - Virtuelles Zielnetzwerk für das Failover (und seine Ressourcengruppe)
    - Zielsubnetz
    - Virtuelles Zielnetzwerk für das Testfailover (und seine Ressourcengruppe)
    - Verfügbarkeitsgruppe (sofern erforderlich)
    - Zielnetzwerksicherheitsgruppe und ihre Ressourcengruppe
- Stellen Sie sicher, dass die Eigenschaften des virtuellen Zielcomputers festgelegt sind:
    - Name des virtuellen Zielcomputers
    - Größe des virtuellen Zielcomputers in Azure (kann mit einer Azure Migrate-Bewertung festgelegt werden)
    - Private IP-Adresse des primären Netzwerkadapters auf dem virtuellen Computer
- Laden Sie die Skripts aus dem GitHub-Repository [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) (Azure PowerShell-Beispiele) herunter.

### <a name="csv-input-file"></a>CSV-Eingabedatei
Wenn alle Voraussetzungen erfüllt sind, müssen Sie eine CSV-Datei mit Daten für jeden Quellcomputer erstellen, den Sie migrieren möchten. Die CSV-Eingabedatei muss eine Kopfzeile mit den Eingabedetails und eine Zeile mit Details für die einzelnen Computer enthalten, die migriert werden müssen. Alle Skripts können mit der gleichen CSV-Datei verwendet werden. Eine CSV-Beispielvorlage finden Sie im Skriptordner.

### <a name="script-execution"></a>Skriptausführung
Wenn die CSV-Datei bereit ist, können Sie zur Migration der lokalen virtuellen Computer die folgenden Schritte ausführen:

**Schritt** | **Skriptname** | **Beschreibung**
--- | --- | ---
1 | asr_startmigration.ps1 | Aktivieren Sie die Replikation für alle in der CSV-Datei aufgeführten virtuellen Computer. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jeden virtuellen Computer.
2 | asr_replicationstatus.ps1 | Überprüfen Sie den Status der Replikation. Das Skript erstellt eine CSV-Datei mit dem Status für jeden virtuellen Computer.
3 | asr_updateproperties.ps1 | Aktualisieren Sie mit diesem Skript die Zieleigenschaften der virtuellen Computer (Compute- und Netzwerkeigenschaften), sobald die virtuellen Computer repliziert/geschützt wurden.
4 | asr_propertiescheck.ps1 | Überprüfen Sie, ob die Eigenschaften ordnungsgemäß aktualisiert wurden.
5 | asr_testmigration.ps1 |  Starten Sie das Testfailover für die in der CSV-Datei aufgeführten virtuellen Computer. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jeden virtuellen Computer.
6 | asr_cleanuptestmigration.ps1 | Wenn Sie die virtuellen Computer, für die ein Testfailover ausgeführt wurde, manuell überprüft haben, können Sie mit diesem Skript die virtuellen Computer mit Testfailover bereinigen.
7 | asr_migration.ps1 | Führen Sie ein ungeplantes Failover für die in der CSV-Datei aufgeführten virtuellen Computer aus. Das Skript erstellt eine CSV-Ausgabe mit den Auftragsdetails für jeden virtuellen Computer. Das Skript fährt die lokalen virtuellen Computer nicht herunter, bevor es das Failover auslöst. Zur Gewährleistung der Anwendungskonsistenz wird empfohlen, die virtuellen Computer vor der Skriptausführung manuell herunterzufahren.
8 | asr_completemigration.ps1 | Führen Sie den Commitvorgang für die virtuellen Computer aus, und löschen Sie die Azure Site Recovery-Entitäten.
9 | asr_postmigration.ps1 | Wenn Sie den Netzwerkadaptern nach dem Failover Netzwerksicherheitsgruppen hinzufügen möchten, können Sie dazu dieses Skript verwenden. Es weist jedem Netzwerkadapter auf dem virtuellen Zielcomputer eine Netzwerksicherheitsgruppe zu.

## <a name="how-to-migrate-to-managed-disks"></a>Migrieren zu verwalteten Datenträgern
Das Skript migriert die virtuellen Computer standardmäßig zu den verwalteten Datenträgern in Azure. Wenn es sich bei dem angegebenen Zielspeicherkonto um ein Storage Premium-Konto handelt, werden nach der Migration Premium-verwaltete Datenträger erstellt. Das Cachespeicherkonto kann weiterhin ein Standardkonto sein. Wenn das Zielspeicherkonto ein Storage Standard-Konto ist, werden nach der Migration Standarddatenträger erstellt. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Migrieren von Servern zu Azure mithilfe von Azure Site Recovery finden Sie unter [Migrieren von lokalen Computern zu Azure](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure).
