---
title: Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung für SAP NetWeaver-Anwendungsbereitstellungen mit Azure Site Recovery einrichten.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 0848738b71a605d8baf049847daa3ae2428a7abe
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793674"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen

Die meisten großen und mittleren SAP-Bereitstellungen weisen eine Form von Notfallwiederherstellungslösung auf. Die Bedeutung robuster und testfähiger Notfallwiederherstellungslösungen ist weiter gewachsen, da immer mehr Kerngeschäftsprozesse in Anwendungen wie SAP verschoben werden. Azure Site Recovery wurde mit SAP-Anwendungen getestet und integriert. Azure Site Recovery übersteigt die Möglichkeiten der meisten lokalen Notfallwiederherstellungslösungen, und dies zu geringeren Gesamtkosten (TCO) als Lösungen von Mitbewerbern.

Mit Azure Site Recovery haben Sie folgende Möglichkeiten:
* Ermöglichen Sie den **Schutz von lokal ausgeführten SAP NetWeaver-Produktionsanwendungen und Anwendungen, die nicht von NetWeaver stammen**, indem Sie die Komponenten in Azure replizieren.
* Ermöglichen Sie den **Schutz von in Azure ausgeführten SAP NetWeaver-Produktionsanwendungen und Anwendungen, die nicht von NetWeaver stammen**, indem Sie die Komponenten in einem anderen Azure-Datencenter replizieren.
* **Vereinfachen Sie die Cloudmigration** mithilfe von Azure Site Recovery zum Migrieren Ihrer SAP-Bereitstellung nach Azure.
* **Vereinfachen Sie SAP-Projektupgrades, Testläufe und Prototyperstellung**, indem Sie bei Bedarf einen Produktionsklon zum Testen von SAP-Anwendungen erstellen.

In diesem Artikel wird beschrieben, wie Sie SAP NetWeaver-Anwendungsbereitstellungen mit [Azure Site Recovery](site-recovery-overview.md) schützen. Der Artikel umfasst bewährte Methoden für den Schutz einer SAP NetWeaver-Bereitstellung mit drei Ebenen in Azure durch die Replikation mit Azure Site Recovery in ein anderes Azure-Rechenzentrum. Beschrieben werden die unterstützten Szenarien und Konfigurationen sowie das Ausführen von Testfailovern (Übungen zur Notfallwiederherstellung) als auch von tatsächlichen Failovern.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie beginnen, stellen Sie sicher, dass Sie wissen, wie Sie die folgenden Aufgaben ausführen:

* [Replizieren eines virtuellen Computers in Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Entwerfen eines Netzwerks für die Wiederherstellung](site-recovery-azure-to-azure-networking-guidance.md)
* [Ausführen eines Testfailovers in Azure](azure-to-azure-walkthrough-test-failover.md)
* [Ausführen eines Failovers in Azure](site-recovery-failover.md)
* [Replizieren eines Domänencontrollers](site-recovery-active-directory.md)
* [Replizieren von SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Unterstützte Szenarien
Mit Azure Site Recovery können Sie eine Notfallwiederherstellungslösung in folgenden Szenarien implementieren:
* SAP-Systeme, die in einem Azure-Rechenzentrum ausgeführt werden, das in ein anderes Azure-Rechenzentrum repliziert wird (Azure-zu-Azure-Notfallwiederherstellung). Weitere Informationen finden Sie unter [Architektur der Azure-zu-Azure-Replikation](https://aka.ms/asr-a2a-architecture).
* Lokal auf VMware-Servern (oder physischen Servern) ausgeführte SAP-Systeme, die in einen Standort für die Notfallwiederherstellung in einem Azure-Rechenzentrum repliziert werden (VMware-zu-Azure-Notfallwiederherstellung). Dieses Szenario erfordert einige zusätzliche Komponenten. Weitere Informationen finden Sie unter [VMware in der Architektur für die Azure-Replikation](https://aka.ms/asr-v2a-architecture).
* Lokal unter Hyper-V ausgeführte SAP-Systeme, die in einen Standort für die Notfallwiederherstellung in einem Azure-Rechenzentrum repliziert werden (Hyper-V-zu-Azure-Notfallwiederherstellung). Dieses Szenario erfordert einige zusätzliche Komponenten. Weitere Informationen finden Sie unter [Architektur der Replikation von Hyper-V in Azure](https://aka.ms/asr-h2a-architecture).

In diesem Artikel wird das Szenario der **Azure-zu-Azure**-Notfallwiederherstellung verwendet, um die SAP-Notfallwiederherstellungsfunktionen von Azure Site Recovery zu veranschaulichen. Da die Replikation mit Site Recovery nicht anwendungsspezifisch ist, gilt der beschriebene Prozess vermutlich auch für andere Szenarien.

### <a name="required-foundation-services"></a>Erforderliche Grunddienste
In dem in diesem Artikel besprochenen Szenario werden die folgenden Foundation-Dienste bereitgestellt:
* Azure ExpressRoute oder Azure VPN Gateway
* Mindestens ein Active Directory-Domänencontroller und ein DNS-Server unter Azure

Es wird empfohlen, diese Infrastruktur einzurichten, bevor Sie Site Recovery bereitstellen.

## <a name="reference-sap-application-deployment"></a>SAP-Anwendungsbereitstellung als Referenz

Anhand dieser Referenzarchitektur wird gezeigt, wie SAP NetWeaver in einer Windows-Umgebung in Azure mit Hochverfügbarkeit ausgeführt werden kann.  Diese Architektur wird mit virtuellen Computern bestimmter Größen bereitgestellt, die an die Anforderungen Ihres Unternehmens angepasst werden können.

![Diagramm eines typischen SAP-Bereitstellungsmusters](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Überlegungen zur Notfallwiederherstellung

Zur Notfallwiederherstellung muss es möglich sein, ein Failover zu einer sekundären Region ausführen zu können. Für jede Ebene wird eine andere Strategie genutzt, um per Notfallwiederherstellung für den erforderlichen Schutz zu sorgen.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Virtuelle Computer mit SAP Web Dispatcher-Pool 
Die Web Dispatcher-Komponente wird als Lastenausgleichsmodul für SAP-Datenverkehr zwischen den SAP-Anwendungsservern verwendet. Um Hochverfügbarkeit für die Web Dispatcher-Komponente zu erreichen, wird Azure Load Balancer dazu verwendet, das parallele Web Dispatcher-Setup in einer Roundrobin-Konfiguration für HTTP(S)-Datenverkehrsverteilung zwischen den verfügbaren Web Dispatcher-Instanzen im Lastenausgleichspool zu implementieren. Dies wird mithilfe von Azure Site Recovery (ASR) repliziert, und Automatisierungsskripts werden verwendet, um das Lastenausgleichsmodul in der Notfallwiederherstellungregion zu konfigurieren. 

#### <a name="vms-running-application-servers-pool"></a>Virtuelle Computer mit Anwendungsserverpool
Um Anmeldegruppen für ABAP-Anwendungsserver zu verwalten, wird die SMLG-Transaktion verwendet. In ihr wird die Lastenausgleichsfunktion im Nachrichtenserver der Central Services verwendet, um Arbeitslast im SAP-Anwendungsserverpool für SAPGUIs und RFC-Datenverkehr zu verteilen. Dies wird mithilfe von Azure Site Recovery repliziert. 

#### <a name="vms-running-sap-central-services-cluster"></a>Virtuelle Computer mit SAP Central Services-Cluster
In dieser Referenzarchitektur wird Central Services auf virtuellen Computern auf der Logikschicht ausgeführt. Die Central Services-Komponente ist ein möglicher Single Point of Failure (SPOF), wenn sie auf einem einzelnen virtuellen Computer bereitgestellt wird – dies ist die typische Bereitstellung, wenn Hochverfügbarkeitnicht erforderlich ist.<br>

Soll eine Hochverfügbarkeitslösung implementiert werden, kann ein Cluster mit freigegebenen Datenträgern oder ein Dateifreigabecluster verwendet werden. Um virtuelle Computer für einen Cluster mit freigegebenen Datenträgern zu konfigurieren, verwenden Sie Windows Server-Failovercluster. Cloudzeuge wird als ein Quorumzeuge empfohlen. 
 > [!NOTE]
 > Mit Azure Site Recovery wird der Cloudzeuge nicht repliziert. Aus diesem Grund wird empfohlen, den Cloudzeugen in der Notfallwiederherstellungregion bereitzustellen.

Zur Unterstützung der Failoverclusterumgebung führt [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) die Funktion zur Clustererstellung der freigegebenen Volumes aus, indem unabhängige Datenträger repliziert werden, die sich im Besitz der Clusterknoten befinden. Azure selbst unterstützt keine freigegebenen Datenträger und erfordert daher Lösungen, die von SIOS bereitgestellt werden. 

Eine weitere Möglichkeit zur Verwaltung von Clustering besteht darin, ein Dateifreigabecluster zu implementieren. [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) hat kürzlich das Bereitstellungsmuster von Central Services so geändert, dass über einen UNC-Pfad auf die globalen „/sapmnt“-Verzeichnisse zugegriffen wird. Es ist aber weiterhin ratsam sicherzustellen, dass die UNC-Freigabe für „/sapmnt“ hochverfügbar ist. Dies lässt sich in der Central Services-Instanz erreichen, indem Windows Server-Failovercluster mit Scale Out File Server (SOFS) und der Storage Spaces Direct-Funktion (S2D, „Direkte Speicherplätze“) in Windows Server 2016 verwendet wird. 
 > [!NOTE]
 > Derzeit unterstützt Azure Site Recovery nur die Replikation absturzkonsistenter Punkte virtueller Maschinen mithilfe direkter Speicherplätze und passiver Knoten von SIOS Datakeeper.


## <a name="disaster-recovery-considerations"></a>Überlegungen zur Notfallwiederherstellung

Mit Azure Site Recovery können Sie das Failover einer vollständigen SAP-Bereitstellung in allen Azure-Regionen orchestrieren.
Nachfolgend sind die Schritte zum Einrichten der Notfallwiederherstellung angegeben. 

1. Replizieren von virtuellen Computern 
2. Entwerfen eines Netzwerks für die Wiederherstellung
3.  Replizieren eines Domänencontrollers
4.  Replizieren der Datenbankebene 
5.  Ausführen eines Testfailovers 
6.  Ausführen eines Failovers 

Nachfolgend ist die Empfehlung für eine Notfallwiederherstellung der einzelnen Ebenen angegeben, die in diesem Beispiel verwendet werden. 

 **SAP-Ebenen** | **Empfehlung**
 --- | ---
**SAP Web Dispatcher-Pool** |  Replikation mithilfe von Site Recovery 
**SAP-Anwendungsserverpool** |  Replikation mithilfe von Site Recovery 
**SAP Central Services-Cluster** |  Replikation mithilfe von Site Recovery 
**Virtuelle Active Directory-Computer** |  Active Directory-Replikation 
**SQL-Datenbank-Server** |  SQL Always On-Replikation

## <a name="replicate-virtual-machines"></a>Replizieren von virtuellen Computern

Zu Beginn des Replizieren aller virtuellen Computer für SAP-Anwendungen in ein Azure-Notfallwiederherstellungsrechenzentrum folgen Sie der Anleitung unter [Replikation eines virtuellen Computers in Azure](azure-to-azure-walkthrough-enable-replication.md).


* Eine Anleitung zum Schutz von Active Directory und DNS finden Sie im Dokument [Schützen von Active Directory und DNS](site-recovery-active-directory.md).

* Eine Anleitung zum Schutz von Datenbankebenen auf SQL Server finden Sie im Dokument [Schützen von SQL Server](site-recovery-active-directory.md).

## <a name="networking-configuration"></a>Netzwerkkonfiguration

Wenn Sie eine statische IP-Adresse verwenden, können Sie die IP-Adresse angeben, die Sie dem virtuellen Computer zuordnen möchten. Um die IP-Adresse festzulegen, wechseln Sie zu **Einstellungen für Compute und Netzwerk** > **Netzwerkschnittstellenkarte**.

![Screenshot, der zeigt, wie eine private IP-Adresse im Bereich „Site Recovery-Netzwerkschnittstellenkarte“ festgelegt wird](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans
Ein Wiederherstellungsplan unterstützt die Sequenzierung der verschiedenen Ebenen in einer Anwendung mit mehreren Ebenen während eines Failovers. Die Sequenzierung trägt zur Wahrung der Anwendungskonsistenz bei. Beim Erstellen eines Wiederherstellungsplans für eine Webanwendung mit mehreren Ebenen führen Sie die unter [Erstellen eines Wiederherstellungsplans mit Site Recovery](site-recovery-create-recovery-plans.md) beschriebenen Schritte aus.

### <a name="adding-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

1.  Erstellen Sie einen Wiederherstellungsplan, indem Sie den Anwendungsserver, Web Dispatcher und virtuelle Computer mit SAP Central Services hinzufügen.
2.  Klicken Sie auf „Anpassen“, um die virtuellen Computer zu gruppieren. Standardmäßig gehören alle virtuellen Computer der „Gruppe 1“ an.



### <a name="add-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan
Für die korrekte Funktionsweise Ihrer Anwendungen kann es erforderlich sein, nach dem Failover bzw. während eines Testfailovers einige Vorgänge auf den virtuellen Azure-Computern durchzuführen. Sie können einige Vorgänge nach einem Failover automatisieren. Durch Hinzufügen entsprechender Skripts zum Wiederherstellungsplan können Sie beispielsweise den DNS-Eintrag aktualisieren sowie Bindungen und Verbindungen ändern.


Sie können die am häufigsten verwendeten Azure Site Recovery-Skripts in Ihrem Automation-Konto bereitstellen, indem Sie auf die Schaltfläche „In Azure bereitstellen“ klicken. Wenn Sie ein beliebiges veröffentlichtes Skript verwenden, vergewissern Sie sich, dass Sie die Anleitung im Skript befolgen.

[![In Azure bereitstellen](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Fügen Sie der „Gruppe 1“ ein Skript als vorausgehende Aktion hinzu, um für die SQL-Verfügbarkeitsgruppe ein Failover durchzuführen. Verwenden Sie das Skript „ASR-SQL-FailoverAG“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript genau, und nehmen Sie die erforderlichen Änderungen im Skript vor.
2. Fügen Sie ein Skript als nachfolgende Aktion hinzu, um den Failover-VMs der Webebene (Gruppe 1) einen Lastenausgleich anzufügen. Verwenden Sie das Skript „ASR-AddSingleLoadBalancer“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript genau, und nehmen Sie die erforderlichen Änderungen im Skript vor.

![SAP-Wiederherstellungsplan](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
3.  Wählen Sie **Testfailover** aus.
4.  Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
5.  Wenn die sekundäre Umgebung aktiv ist, können Sie Überprüfungen durchführen.
6.  Klicken Sie nach Abschluss der Überprüfungen auf **Testfailover bereinigen**, um die Failoverumgebung zu bereinigen.

Weitere Informationen finden Sie unter [Testfailover nach Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Ausführen eines Failovers

1.  Wählen Sie im Azure-Portal Ihren Recovery Services-Tresor aus.
2.  Wählen Sie den Wiederherstellungsplan aus, den Sie für die SAP-Anwendungen erstellt haben.
3.  Wählen Sie **Failover** aus.
4.  Wählen Sie den Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

Weitere Informationen finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zum Erstellen einer Lösung für die Notfallwiederherstellung für SAP NetWeaver-Bereitstellungen mithilfe von Site Recovery finden Sie unter den herunterladbaren Whitepapers [SAP NetWeaver: Erstellen einer Notfallwiederherstellungslösung mit Azure Site Recovery](https://aka.ms/asr_sap). Das Whitepaper behandelt auch Empfehlungen für unterschiedliche SAP-Architekturen, listet unterstützte Anwendungen und VM-Typen für SAP in Azure auf und beschreibt Testplanoptionen für Ihre Notfallwiederherstellungslösung.
* Erfahren Sie mehr über das [Replizieren anderer Workloads](site-recovery-workload.md) mit Site Recovery.
