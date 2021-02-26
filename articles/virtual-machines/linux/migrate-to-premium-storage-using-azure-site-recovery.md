---
title: Migrieren Ihrer Linux-VMs zu Azure Storage Premium mithilfe von Azure Site Recovery
description: Migrieren Sie Ihre vorhandenen virtuellen Computer mithilfe von Site Recovery zu Azure Storage Premium. Storage Premium bietet Datenträgerunterstützung für hohe Leistung mit geringer Latenz für E/A-intensive Workloads, die auf virtuellen Azure-Computern ausgeführt werden.
author: luywang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: e00496ad623d534e1fbdcb60f22a1e36f77c4212
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99806171"
---
# <a name="use-site-recovery-to-migrate-to-premium-storage"></a>Migrieren zu Storage Premium mithilfe von Site Recovery

[Azure SSD Premium](../disks-types.md) bietet Datenträgerunterstützung mit hoher Leistung und geringer Wartezeit für virtuelle Computer (VMs), auf denen E/A-intensive Workloads ausgeführt werden. In diesem Leitfaden erfahren Sie, wie Sie Ihre VM-Datenträger mithilfe von [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) von einem Standardspeicherkonto zu einem Storage Premium-Konto migrieren.

Site Recovery ist ein Azure-Dienst, der zu Ihrer Strategie für die Geschäftskontinuität und Notfallwiederherstellung beiträgt, indem er die Replikation von lokalen physischen Servern und virtuellen Computern in die Cloud (Azure) oder in ein sekundäres Datencenter orchestriert. Wenn es an Ihrem primären Standort zu Ausfällen kommt, wird ein Failover zum sekundären Standort durchgeführt, um die Verfügbarkeit von Anwendungen und Workloads zu erhalten. Wenn wieder Normalbetrieb herrscht, führen Sie das Failback zum primären Standort durch. 

Site Recovery verfügt über Testfailover, mit denen Übungen zur Notfallwiederherstellung unterstützt werden, ohne dass sich dies auf Produktionsumgebungen auswirkt. Sie können Failover mit minimalem Datenverlust (je nach Replikationshäufigkeit) für unerwartete Notfälle ausführen. Für die Migration zu Storage Premium können Sie ein [Failover in Site Recovery](../../site-recovery/site-recovery-failover.md) verwenden, um Zieldatenträger zu einem Storage Premium-Konto zu migrieren.

Wir empfehlen die Migration zu Storage Premium mithilfe von Site Recovery, da es bei dieser Option nur zu minimalen Ausfallzeiten kommt. Außerdem entfallen bei dieser Option das manuelle Kopieren von Datenträgern und das Erstellen neuer virtueller Computer. Mit Site Recovery werden Ihre Datenträger bei einem Failover systematisch kopiert und neue VMs erstellt. 

Site Recovery unterstützt verschiedene Failoverarten mit minimalen oder ganz ohne Ausfallzeiten. Informationen zur Planung von Ausfallzeiten und zur Einschätzung von Datenverlusten finden Sie unter [Failover in Site Recovery](../../site-recovery/site-recovery-failover.md). Wenn Sie [die Vorbereitung zum Herstellen der Verbindung mit Azure-VMs nach dem Failover durchführen](../../site-recovery/vmware-azure-tutorial.md), sollten Sie nach dem Failover eine RDP-Verbindung mit dem virtuellen Azure-Computer herstellen können.

![Diagramm zur Notfallwiederherstellung][1]

## <a name="azure-site-recovery-components"></a>Azure Site Recovery-Komponenten

Für dieses Migrationsszenario sind folgende Site Recovery-Komponenten relevant:

* Ein **Konfigurationsserver** ist eine Azure-VM, mit der die Kommunikation koordiniert wird und die Datenreplikations- und Wiederherstellungsvorgänge verwaltet werden. Auf diesem virtuellen Computer führen Sie eine einzelne Setupdatei aus, um den Konfigurationsserver und eine zusätzliche Komponente (den so genannten Prozessserver) als Replikationsgateway zu installieren. Informieren Sie sich über die [Voraussetzungen für den Konfigurationsserver](../../site-recovery/vmware-azure-tutorial.md). Der Konfigurationsserver wird nur einmal konfiguriert und kann dann bei allen Migrationen zur gleichen Region verwendet werden.

* Der **Prozessserver** ist ein Replikationsgateway und hat folgende Aufgaben: 

  1. Er empfängt Replikationsdaten von virtuellen Quellcomputern.
  2. Er Optimiert die Daten mit Caching, Komprimierung und Verschlüsselung.
  3. Er sendet die Daten an ein Speicherkonto. 

  Außerdem wickelt er die Pushinstallation des Mobilitätsdiensts auf Quell-VMs ab und führt die automatische Ermittlung von Quell-VMs durch. Auf dem Konfigurationsserver ist der Standardprozessserver installiert. Sie können zusätzliche eigenständige Prozessserver bereitstellen, um Ihre Bereitstellung zu skalieren. Informieren Sie sich über die [bewährten Methoden für die Bereitstellung von Prozessservern](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) und die [Bereitstellung zusätzlicher Prozessserver](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Der Prozessserver wird nur einmal konfiguriert und kann dann bei allen Migrationen zur gleichen Region verwendet werden.

* Der **Mobilitätsdienst** ist eine Komponente, die auf jedem virtuellen Standardcomputer bereitgestellt wird, den Sie replizieren möchten. Er erfasst die Datenschreibvorgänge auf der Standard-VM und sendet sie an den Prozessserver. Informationen zu den Voraussetzungen für replizierte Computer finden Sie [hier](../../site-recovery/vmware-azure-tutorial.md).

Die folgende Grafik veranschaulicht die Interaktion der einzelnen Komponenten:

![Interaktion der Azure Site Recovery-Komponenten][15]

> [!NOTE]
> Site Recovery unterstützt die Migration von Speicherplätze-Datenträgern nicht.

Informationen zu weiteren Komponenten für andere Szenarien finden Sie unter [Szenarioarchitektur](../../site-recovery/vmware-azure-tutorial.md).

## <a name="azure-essentials"></a>Wichtige Azure-Komponenten

Azure-Anforderungen für dieses Migrationsszenario:

* Ein Azure-Abonnement.
* Ein Azure Storage Premium-Konto zum Speichern replizierter Daten
* Ein virtuelles Azure-Netzwerk, mit dem virtuelle Computer eine Verbindung herstellen, wenn sie im Rahmen eines Failovers erstellt werden. Das virtuelle Azure-Netzwerk muss sich in der Region befinden, in der Site Recovery ausgeführt wird.
* Ein Azure-Speicherkonto vom Typ „Standard“ zum Speichern von Replikationsprotokollen. Hierbei kann es sich um das gleiche Speicherkonto handeln, das auch für die zu migrierenden VM-Datenträger verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

* Kenntnis der relevanten Migrationsszenariokomponenten aus dem vorherigen Abschnitt
* Planung der Ausfallzeit anhand der Informationen unter [Failover in Site Recovery](../../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Setup- und Migrationsschritte

Sie können Site Recovery verwenden, um Azure IaaS-VMs zwischen Regionen oder innerhalb derselben Region zu migrieren. Die folgende Anleitung basiert auf dem Artikel [Replizieren von VMware-VMs in Azure mit Site Recovery](../../site-recovery/vmware-azure-tutorial.md) und wurde für dieses Migrationsszenario angepasst. Verwenden Sie zusätzlich zu der Anleitung in diesem Artikel die Links zu den ausführlichen Schritten.

### <a name="step-1-create-a-recovery-services-vault"></a>Schritt 1: Erstellen eines Recovery Services-Tresors

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Wählen Sie **Ressource erstellen** > **Verwaltung** > **Backup** und **Site Recovery (OMS)** aus. Alternativ können Sie auch **Durchsuchen** > **Recovery Services-Tresor** > **Hinzufügen** auswählen. 
3. Geben Sie eine Region für die Replikation virtueller Computer an. Wählen Sie für eine Migration in derselben Region die Region aus, in der sich Ihre Quell-VMs und Quellspeicherkonten befinden. 

### <a name="step-2-choose-your-protection-goals"></a>Schritt 2: Auswählen der Schutzziele 

1. Öffnen Sie auf der VM, auf der Sie den Konfigurationsserver installieren möchten, das [Azure-Portal](https://portal.azure.com).
2. Navigieren Sie zu **Recovery Services-Tresore** > **Einstellungen** > **Site Recovery** > **Schritt 1: Vorbereiten der Infrastruktur** > **Schutzziel**.

   ![Navigieren zum Bereich „Schutzziel“][2]

3. Wählen Sie unter **Schutzziel** in der Dropdownliste die Option **To Azure** (In Azure). Wählen Sie in der zweiten Dropdownliste die Option **Nicht virtualisiert/Andere** und anschließend **OK** aus.

   ![Bereich „Schutzziel“ mit ausgefüllten Feldern][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>Schritt 3: Einrichten der Quellumgebung (Konfigurationsserver)

1. Navigieren Sie zu **Infrastruktur vorbereiten** > **Quelle vorbereiten** > **Server hinzufügen**, um das **einheitliche Setup von Azure Site Recovery** und den Tresorregistrierungsschlüssel herunterzuladen. 
 
   Sie benötigen den Tresorregistrierungsschlüssel zum Ausführen des einheitlichen Setups. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

   ![Navigieren zum Bereich „Server hinzufügen“][4]

2. Fügen Sie im Bereich **Server hinzufügen** einen Konfigurationsserver hinzu.

   ![Bereich „Server hinzufügen“ mit ausgewähltem Konfigurationsserver][5]

3. Führen Sie auf dem virtuellen Computer, den Sie als Konfigurationsserver verwenden, das einheitliche Setup aus, um den Konfigurationsserver und den Prozessserver zu installieren. Die Vorgehensweise für die Installation wird auf [diesen Screenshots](../../site-recovery/vmware-azure-tutorial.md) veranschaulicht. Bei den für dieses Migrationsszenario angegebenen Schritten helfen Ihnen die folgenden Screenshots weiter.

   1. Wählen Sie unter **Vorbereitung** die Option **Install the configuration server and process server** (Konfigurationsserver und Prozessserver installieren) aus.

      ![Seite „Vorbereitung“][6]

   2. Navigieren Sie unter **Registrierung** zu dem Registrierungsschlüssel, den Sie aus dem Tresor heruntergeladen haben, und wählen Sie ihn aus.

      ![Registrierungsseite][7]

   3. Wählen Sie unter **Umgebungsdetails** aus, ob Sie VMware-VMs replizieren werden. Wählen Sie für dieses Migrationsszenario die Option **Nein**.

      ![Seite „Umgebungsdetails“][8]

4. Gehen Sie nach Abschluss der Installation im Fenster **Microsoft Azure Site Recovery Configuration Server** (Microsoft Azure Site Recovery-Konfigurationsserver) wie folgt vor:
 
   1. Verwenden Sie die Registerkarte **Konten verwalten**, um das Konto zu erstellen, das von Site Recovery für die automatische Ermittlung verwendet werden kann. (Beim Szenario zum Schützen von physischen Computern ist das Einrichten des Kontos nicht relevant. Sie benötigen aber mindestens ein Konto, um einen der folgenden Schritte zu ermöglichen. In diesem Fall können Sie das Konto und Kennwort beliebig benennen.) 
   2. Verwenden Sie die Registerkarte **Vault Registration** (Tresorregistrierung), um die Datei mit den Tresoranmeldeinformationen hochzuladen.

      ![Registerkarte „Tresorregistrierung“][9]

### <a name="step-4-set-up-the-target-environment"></a>Schritt 4: Einrichten der Zielumgebung

Wählen Sie **Infrastruktur vorbereiten** > **Ziel** aus, und geben Sie das Bereitstellungsmodell an, das Sie für virtuelle Computer nach dem Failover verwenden möchten. Sie können je nach Szenario die Option **Klassisch** oder **Resource Manager** wählen.

![Bereich „Ziel“][10]

Site Recovery prüft, ob Sie über ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke verfügen. 

> [!NOTE]
> Wenn Sie ein Storage Premium-Konto für replizierte Daten verwenden, müssen Sie ein zusätzliches Standardspeicherkonto zum Speichern von Replikationsprotokollen einrichten.

### <a name="step-5-set-up-replication-settings"></a>Schritt 5: Einrichten der Replikationseinstellungen

Führen Sie die Schritte unter [Einrichten der Replikationseinstellungen](../../site-recovery/vmware-azure-tutorial.md) aus, um sich zu vergewissern, dass Ihr Konfigurationsserver erfolgreich der von Ihnen erstellten Replikationsrichtlinie zugeordnet wurde.

### <a name="step-6-plan-capacity"></a>Schritt 6: Planen der Kapazität

1. Verwenden Sie den [Capacity Planner](../../site-recovery/site-recovery-capacity-planner.md), um Ihre Netzwerkbandbreite, Speicheranforderungen und anderen Anforderungen genau einschätzen und Ihren Replikationsbedarf erfüllen zu können. 
2. Wählen Sie anschließend unter **Haben Sie die Kapazitätsplanung abgeschlossen?** die Option **Ja, ist abgeschlossen** aus.

   ![Feld zur Bestätigung des Abschlusses der Kapazitätsplanung][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>Schritt 7: Installieren des Mobilitätsdiensts und Aktivieren der Replikation

1. Sie können für Ihre virtuellen Quellcomputer die [Pushinstallation](../../site-recovery/vmware-azure-tutorial.md) wählen oder den [Mobilitätsdienst manuell auf Ihren virtuellen Quellcomputern installieren](../../site-recovery/vmware-azure-install-mobility-service.md). Die Anforderungen für die Pushinstallation sowie den Pfad für die manuelle Installation finden Sie unter dem angegebenen Link. Bei Verwendung einer manuellen Installation müssen Sie unter Umständen eine interne IP-Adresse verwenden, um den Konfigurationsserver zu finden.

   ![Seite mit den Konfigurationsserverdetails][12]

   Der virtuelle Computer, für den ein Failover ausgeführt wurde, verfügt über zwei temporäre Datenträger: Der eine stammt vom primären virtuellen Computer, der andere wird im Zuge der Bereitstellung des virtuellen Computers in der Wiederherstellungsregion erstellt. Installieren Sie den Mobilitätsdienst vor dem Aktivieren der Replikation, um den temporären Datenträger vor der Replikation auszuschließen. Weitere Informationen zum Ausschließen temporärer Datenträger finden Sie unter [Ausschließen von Datenträgern von der Replikation](../../site-recovery/vmware-azure-tutorial.md).

2. Aktivieren Sie die Replikation:
   1. Wählen Sie **Anwendung replizieren** > **Quelle** aus. Wählen Sie nach der erstmaligen Aktivierung der Replikation im Tresor die Option **+Replizieren** aus, um die Replikation für weitere Computer zu aktivieren.
   2. Richten Sie in Schritt 1 **Quelle** als Ihren Prozessserver ein.
   3. Geben Sie in Schritt 2 das Bereitstellungsmodell für die Zeit nach dem Failover, ein Storage Premium-Konto als Migrationsziel, ein Standardspeicherkonto zum Speichern von Protokollen und ein virtuelles Netzwerk als Failoverziel an.
   4. Fügen Sie in Schritt 3 geschützte virtuelle Computer nach IP-Adresse hinzu. (Unter Umständen benötigen Sie eine interne IP-Adresse, um sie zu finden.)
   5. Konfigurieren Sie in Schritt 4 die Eigenschaften, indem Sie die Konten auswählen, die Sie zuvor auf dem Prozessserver eingerichtet haben.
   6. Wählen Sie in Schritt 5 die Replikationsrichtlinie aus, die Sie zuvor in „Schritt 5: Einrichten der Replikationseinstellungen“ erstellt haben.
   7. Klicken Sie auf **OK**.

   > [!NOTE]
   > Wenn die Zuordnung einer Azure-VM aufgehoben und die VM neu gestartet wird, ist nicht sichergestellt, dass sie die gleiche IP-Adresse erhält. Falls sich die IP-Adresse des Konfigurationsservers/Prozessservers oder des geschützten virtuellen Azure-Computers ändert, funktioniert die Replikation in diesem Szenario unter Umständen nicht ordnungsgemäß.

   ![Bereich „Replikation aktivieren“ mit ausgewählter Quelle][13]

Für das Entwerfen Ihrer Azure Storage-Umgebung empfehlen wir Ihnen, für jede VM einer Verfügbarkeitsgruppe separate Speicherkonten zu verwenden. Es empfiehlt sich, die bewährte Methode auf der Speicherebene anzuwenden, um [mehrere Speicherkonten für die einzelnen Verfügbarkeitsgruppen zu verwenden](../manage-availability.md). Das Verteilen von VM-Datenträgern auf mehrere Speicherkonten trägt zur Verbesserung der Speicherverfügbarkeit bei, und der E/A-Aufwand wird auf die gesamte Azure-Speicherinfrastruktur verteilt.

Falls sich Ihre virtuellen Computer in einer Verfügbarkeitsgruppe befinden, empfehlen wir dringend, mehrere virtuelle Computer mehrfach zu migrieren, anstatt Datenträger aller virtuellen Computer in einem einzelnen Speicherkonto zu replizieren. Dadurch teilen sich die virtuellen Computer in der gleichen Verfügbarkeitsgruppe nicht ein einzelnes Speicherkonto. Richten Sie im Bereich **Replikation aktivieren** nacheinander jeweils ein Zielspeicherkonto für die einzelnen virtuellen Computer ein.
 
Sie können je nach Ihren Anforderungen ein Bereitstellungsmodell für die Zeit nach dem Failover wählen. Wenn Sie Azure Resource Manager als Bereitstellungsmodell für die Zeit nach dem Failover auswählen, können Sie ein Failover für einen virtuellen Computer (Resource Manager) auf einen virtuellen Computer (Resource Manager) oder ein Failover eines virtuellen Computers (klassisch) auf einen virtuellen Computer (Resource Manager) ausführen.

### <a name="step-8-run-a-test-failover"></a>Schritt 8: Ausführen eines Testfailovers

Prüfen Sie, ob die Replikation abgeschlossen ist. Wählen Sie hierzu Ihre Site Recovery-Instanz und anschließend **Einstellungen** > **Replizierte Elemente** aus. Der Status und der Prozentsatz Ihres Replikationsprozesses werden angezeigt. 

Führen Sie nach Abschluss des ersten Replikationsvorgangs ein Testfailover aus, um Ihre Replikationsstrategie zu überprüfen. Ausführliche Schritte für ein Testfailover finden Sie unter [Ausführen eines Testfailovers](../../site-recovery/vmware-azure-tutorial.md). 

> [!NOTE]
> Vergewissern Sie sich zuvor, dass Ihre virtuellen Computer und Ihre Replikationsstrategie die Anforderungen erfüllen. Weitere Informationen zum Ausführen eines Testfailovers finden Sie unter [Testfailover auf Azure in Site Recovery](../../site-recovery/site-recovery-test-failover-to-azure.md).

Der Status Ihres Testfailovers wird unter **Einstellungen** > **Aufträge** > *NAME_IHRES_FAILOVERPLANS* angezeigt. Der Bereich enthält eine Aufschlüsselung der Schritte und Ergebnisse (Erfolg/Fehler). Wenn für das Testfailover in einem beliebigen Schritt ein Fehler auftritt, können Sie den Schritt auswählen, um die Fehlermeldung zu überprüfen. 

### <a name="step-9-run-a-failover"></a>Schritt 9: Ausführen eines Failovers

Führen Sie nach Abschluss des Testfailovers ein Failover durch, um Ihre Datenträger zu Storage Premium zu migrieren und die VM-Instanzen zu replizieren. Befolgen Sie die ausführlichen Schritte in [Ausführen eines Failovers](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Wählen Sie unbedingt **Virtuelle Computer herunterfahren und die aktuellen Daten synchronisieren** aus. Diese Option gibt an, dass Site Recovery die geschützten virtuellen Computer herunterfahren und die Daten synchronisieren soll, damit das Failover mit der neuesten Version der Daten erfolgt. Wenn Sie diese Option nicht auswählen oder der Vorgang nicht erfolgreich ausgeführt werden kann, wird das Failover auf der Grundlage des neuesten verfügbaren Wiederherstellungspunkts für den virtuellen Computer ausgeführt. 

Site Recovery erstellt eine VM-Instanz, deren Typ einem Storage Premium-fähigen virtuellen Computer entspricht oder gleicht. Sie können sich über die Leistung und den Preis verschiedener VM-Instanzen unter [Virtuelle Windows-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) und [Virtuelle Linux-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) informieren.

## <a name="post-migration-steps"></a>Schritte nach der Migration

1. **Konfigurieren Sie replizierte VMs in der Verfügbarkeitsgruppe (falls zutreffend)** . Für Site Recovery wird das Migrieren von VMs zusammen mit der Verfügbarkeitsgruppe nicht unterstützt. Wählen Sie je nach Bereitstellungstyp Ihrer replizierten VM eine der folgenden Vorgehensweisen:
   * Für eine VM, die mit dem klassischen Bereitstellungsmodell erstellt wurde: Fügen Sie die VM zur Verfügbarkeitsgruppe im Azure-Portal hinzu. Ausführliche Schritte finden Sie unter [Fügen Sie einer Verfügbarkeitsgruppe einen vorhandenen virtuellen Computer hinzu](/previous-versions/azure/virtual-machines/linux/classic/configure-availability-classic).
   * Für eine VM, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurde: Speichern Sie Ihre Konfiguration der VM. Löschen Sie dann die VMs, und erstellen Sie sie in der Verfügbarkeitsgruppe neu. Verwenden Sie hierfür das Skript unter [Set Azure Resource Manager VM Availability Set](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) (Festlegen der Azure Resource Manager-VM-Verfügbarkeitsgruppe). Machen Sie sich vor dem Ausführen dieses Skripts mit dessen Einschränkungen vertraut, und planen Sie Ihre Ausfallzeit.

2. **Löschen Sie alte VMs und Datenträger**. Stellen Sie sicher, dass die Premium-Datenträger mit den Quelldatenträgern konsistent sind und die neuen virtuellen Computer die gleiche Funktion erfüllen wie die virtuellen Quellcomputer. Löschen Sie den virtuellen Computer, und löschen Sie die Datenträger aus Ihren Quellspeicherkonten im Azure-Portal. Falls der Datenträger aufgrund eines Problems nicht gelöscht wird, obwohl Sie den virtuellen Computer gelöscht haben, lesen Sie [Problembehandlung bei Speicherressourcen-Löschfehlern](../troubleshooting/storage-resource-deletion-errors.md).

3. **Bereinigen Sie die Azure Site Recovery-Infrastruktur**. Wenn Site Recovery nicht mehr benötigt wird, können Sie die dazugehörige Infrastruktur bereinigen. Löschen Sie replizierte Objekte, den Konfigurationsserver, die Wiederherstellungsrichtlinie und anschließend den Azure Site Recovery-Tresor.

## <a name="troubleshooting"></a>Problembehandlung

* [Überwachung und Problembehandlung für den Schutz von virtuellen Computern und physischen Servern](../../site-recovery/site-recovery-monitor-and-troubleshoot.md)
* [Frageseite von Microsoft Q&A (Fragen und Antworten) für Microsoft Azure Site Recovery](/answers/topics/azure-site-recovery.html)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu bestimmten Migrationsszenarien für virtuelle Computer finden Sie in den folgenden Ressourcen:

* [Migrate Azure Virtual Machines between Storage Accounts (in englischer Sprache)](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hochladen einer virtuellen Festplatte mit Linux](upload-vhd.md)
* [Migrating Virtual Machines from Amazon AWS to Microsoft Azure (in englischer Sprache)](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Lesen Sie außerdem die folgenden Ressourcen, um mehr über Azure Storage und Azure Virtual Machines zu erfahren:

* [Azure Storage (in englischer Sprache)](https://azure.microsoft.com/documentation/services/storage/)
* [Dokumentation zu virtuellen Computern](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Auswählen eines Datenträgertyps für virtuelle IaaS-Computer](../disks-types.md)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png