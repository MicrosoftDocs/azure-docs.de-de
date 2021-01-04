---
title: Notfallwiederherstellung für eine SharePoint-Anwendung mit mehreren Ebenen mithilfe von Azure Site Recovery
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Site Recovery-Funktionen die Notfallwiederherstellung für eine SharePoint-Anwendung mit mehreren Ebenen einrichten.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: 08e971e52f994ec5fa5663708fa9f173daf33d80
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013750"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Einrichten der Notfallwiederherstellung für eine SharePoint-Anwendung mit mehreren Ebenen mithilfe von Azure Site Recovery

In diesem Artikel wird ausführlich beschrieben, wie Sie eine SharePoint-Anwendung mithilfe von [Azure Site Recovery](site-recovery-overview.md) schützen können.


## <a name="overview"></a>Übersicht

Microsoft SharePoint ist eine leistungsstarke Anwendung, die es Gruppen oder Abteilungen ermöglicht, sich zu organisieren, zusammenzuarbeiten und Informationen zu teilen. SharePoint bietet Intranetportale, Dokumenten- und Dateiverwaltung, Möglichkeiten zur Zusammenarbeit, soziale Netzwerke, Extranets, Websites, Unternehmenssuche und Business Intelligence. Des Weiteren verfügt es über Funktionen zur System- und Prozessintegration sowie zur Workflowautomatisierung. Organisationen nutzen es in der Regel als eine Ebene-1-Anwendung für Ausfallzeiten und Datenverluste.

Microsoft SharePoint bietet derzeit keine vorgefertigten Funktionen zur Notfallwiederherstellung. Unabhängig vom Typ und Umfang eines Notfalls umfasst die Wiederherstellung ein Standby-Rechenzentrum, in dem die Farm wiederhergestellt werden kann. Standby-Rechenzentren werden für Szenarios benötigt, in denen lokale redundante Systeme und Sicherungen nach einem Ausfall im primären Rechenzentrum nicht wiederhergestellt werden können.

Eine gute Notfallwiederherstellungslösung sollte die Modellierung von Wiederherstellungsplänen für komplexe Anwendungsarchitekturen wie etwa SharePoint ermöglichen. Sie sollte außerdem die Möglichkeit besitzen, benutzerdefinierte Schritte zur Durchführung von Anwendungszuordnungen zwischen unterschiedlichen Ebenen hinzuzufügen. So können Sie im Falle eines Notfalls mit nur einem Klick einen Failover mit einem niedrigeren RTO-Wert durchführen.

In diesem Artikel wird ausführlich beschrieben, wie Sie eine SharePoint-Anwendung mithilfe von [Azure Site Recovery](site-recovery-overview.md) schützen können. Wir stellen die bewährten Methoden zum Replizieren einer SharePoint-Anwendung mit drei Ebenen in Azure, Durchführen einer Übung für die Notfallwiederherstellung und Durchführen eines Failovers für die Anwendung in Azure vor.

Das folgende Video zeigt das Wiederherstellen einer Anwendung mit mehreren Ebenen in Azure.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie mit den folgenden Verfahren vertraut sind:

1. [Replizieren von virtuellen VMware-Computern in Azure mithilfe von Site Recovery](./vmware-azure-tutorial.md)
2. [Entwerfen Ihres Netzwerks für die Notfallwiederherstellung](./concepts-on-premises-to-azure-networking.md)
3. [Testfailover in Azure in Site Recovery](site-recovery-test-failover-to-azure.md)
4. [Failover in Site Recovery](site-recovery-failover.md)
5. Replizieren eines Domänencontrollers unter [Schützen von Active Directory und DNS mit Azure Site Recovery](site-recovery-active-directory.md)
6. Replizieren von SQL Server unter [Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-Architektur

SharePoint kann auf einem oder mehreren Servern mit mehrstufigen Topologien und Serverrollen bereitgestellt werden, um einen Farmentwurf zu implementieren, der bestimmte Zielsetzungen erfüllt. Eine typische große SharePoint-Serverfarm mit hohen Anforderungen, die eine große Anzahl gleichzeitiger Benutzer und eine Vielzahl von Inhaltselementen unterstützt, verwendet die Gruppierung von Diensten als Teil ihrer Skalierbarkeitsstrategie. Bei diesem Ansatz müssen Dienste auf dedizierten Servern ausgeführt, diese Dienste gruppiert und die Server anschließend als Gruppe horizontal hochskaliert werden. Die folgende Topologie veranschaulicht die Gruppierung von Diensten und Servern für eine SharePoint-Serverfarm mit drei Ebenen. Eine ausführliche Anleitung zu verschiedenen SharePoint-Topologien finden Sie in der SharePoint-Dokumentation und den Architekturen der Produktlinie. Weitere Informationen zur Bereitstellung von SharePoint 2013 finden Sie in [diesem Dokument](/SharePoint/sharepoint-server).



![Bereitstellungsmuster 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Site Recovery-Unterstützung

Site Recovery ist anwendungsunabhängig und sollte mit allen Versionen von SharePoint funktionieren, die auf einem unterstützten Computer ausgeführt werden. Für die Erstellung dieses Artikels wurden virtuelle VMware-Computer mit Windows Server 2012 R2 Enterprise verwendet. SharePoint 2013 Enterprise Edition sowie SQL Server 2014 Enterprise Edition wurden verwendet.

### <a name="source-and-target"></a>Quelle und Ziel

**Szenario** | **Am sekundären Standort** | **In Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Physischer Server** | Ja | Ja
**Azure** | Nicht verfügbar | Ja


### <a name="things-to-keep-in-mind"></a>Bitte beachten

Wenn Sie einen freigegebenen, datenträgerbasierten Cluster als beliebige Ebene in Ihrer Anwendung verwenden, können Sie die Site Recovery-Replikation zum Replizieren dieser virtuellen Computer nicht verwenden. Verwenden Sie stattdessen die native Replikation der Anwendung, und führen Sie anschließend mithilfe eines [Wiederherstellungsplans](site-recovery-create-recovery-plans.md) ein Failover für alle Ebenen durch.

## <a name="replicating-virtual-machines"></a>Replizieren von virtuellen Computern

Folgen Sie [dieser Anleitung](./vmware-azure-tutorial.md), um die Replikation der virtuellen Computer in Azure zu starten.

* Sobald die Replikation abgeschlossen ist, wählen Sie für jeden virtuellen Computer der einzelnen Ebenen dieselbe Verfügbarkeitsgruppe unter „Repliziertes Element > Einstellungen > Eigenschaften > Compute und Netzwerk“ aus. Wenn Ihre Webebene beispielsweise über drei virtuelle Computer (VMs) verfügt, stellen Sie sicher, dass alle drei VMs als Teil derselben Verfügbarkeitsgruppe in Azure konfiguriert sind.

    ![Festlegen-Availability-Set](./media/site-recovery-sharepoint/select-av-set.png)

* Eine Anleitung zum Schutz von Active Directory und DNS finden Sie im Dokument [Schützen von Active Directory und DNS](site-recovery-active-directory.md).

* Eine Anleitung zum Schutz von Datenbankebenen auf SQL Server finden Sie im Dokument [Schützen von SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Netzwerkkonfiguration

### <a name="network-properties"></a>Netzwerkeigenschaften

* Konfigurieren Sie die Netzwerkeinstellungen für virtuelle Computer der Anwendungs- und Webebene im Azure-Portal so, dass die VMs nach dem Failover mit dem richtigen Notfallwiederherstellungsnetzwerk verbunden werden.

    ![Auswählen des Netzwerks](./media/site-recovery-sharepoint/select-network.png)


* Geben Sie bei Verwendung einer statischen IP-Adresse die IP an, die vom virtuellen Computer im Feld **Ziel-IP** genutzt werden soll.

    ![Festlegen der statischen IP-Adresse](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>Routing von DNS und Datenverkehr

[Erstellen Sie ein Traffic Manager-Profil vom Typ „Priorität“](../traffic-manager/quickstart-create-traffic-manager-profile.md) im Azure-Abonnement für Websites mit Internetzugriff. Konfigurieren Sie anschließend Ihr DNS- und Traffic Manager-Profil folgendermaßen:


| **Where** | **Quelle** | **Ziel**|
| --- | --- | --- |
| Öffentliche DNS | Öffentliche DNS für SharePoint-Websites <br/><br/> Beispiel: sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| Lokale DNS | sharepointonprem.contoso.com | Öffentliche IP-Adresse in der lokalen Farm |


[Erstellen Sie die primären und die Wiederherstellungs-Endpunkte](../traffic-manager/traffic-manager-configure-priority-routing-method.md) im Traffic Manager-Profil. Verwenden Sie den externen Endpunkt als lokalen Endpunkt und die öffentliche IP-Adresse als Azure-Endpunkt. Vergewissern Sie sich, dass die Priorität für den lokalen Endpunkt höher festgelegt ist.

Hosten Sie eine Testseite an einem bestimmten Port (z.B. 800) auf der SharePoint-Webebene, damit der Traffic Manager nach einem Failover automatisch die Verfügbarkeit erkennt. Damit umgehen Sie das Problem, dass Sie auf keinem Ihrer SharePoint-Websites eine anonyme Authentifizierung aktivieren können.

[Konfigurieren Sie das Traffic Manager-Profil](../traffic-manager/traffic-manager-configure-priority-routing-method.md) mit den folgenden Einstellungen:

* Routingmethode: „Priorität“
* DNS-Gültigkeitsdauer (TTL): „30 Sekunden“
* Überwachungseinstellungen für Endpunkt: Wenn Sie die anonyme Authentifizierung aktivieren können, können Sie einen bestimmten Website-Endpunkt angeben. Andernfalls können Sie eine Testseite an einem bestimmten Port (z.B. 800) verwenden.

## <a name="creating-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans

Ein Wiederherstellungsplan ermöglicht die Sequenzierung des Failovers verschiedener Ebenen in einer Anwendung mit mehreren Ebenen, damit die Anwendungskonsistenz gewahrt wird. Führen Sie die unten angegebenen Schritte aus, während Sie einen Wiederherstellungsplan für eine Webanwendung mit mehreren Ebenen erstellen. Lesen Sie die [weiteren Informationen](site-recovery-runbook-automation.md#customize-the-recovery-plan) zur Erstellung eines Wiederherstellungsplans.

### <a name="adding-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

1. Erstellen Sie einen Wiederherstellungsplan durch Hinzufügen der virtuellen Computer der Anwendungs- und Webebene.
2. Klicken Sie auf „Anpassen“, um die virtuellen Computer zu gruppieren. Standardmäßig gehören alle virtuellen Computer der „Gruppe 1“ an.

    ![Anpassen des Wiederherstellungsplans](./media/site-recovery-sharepoint/rp-groups.png)

3. Erstellen Sie eine weitere Gruppe (Gruppe 2), und verschieben Sie die virtuellen Computer der Webebene in die neue Gruppe. Ihre virtuellen Computer der Anwendungsebene sollten nun „Gruppe 1“ angehören, die VMs der Webebene „Gruppe 2“. So wird sichergestellt, dass die VMs der Anwendungsebene vor denen der Webebene gestartet werden.


### <a name="adding-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan

Sie können die am häufigsten verwendeten Azure Site Recovery-Skripts in Ihrem Automation-Konto bereitstellen, indem Sie auf die Schaltfläche „In Azure bereitstellen“ klicken. Wenn Sie ein beliebiges veröffentlichtes Skript verwenden, vergewissern Sie sich, dass Sie die Anleitung im Skript befolgen.

[![In Azure bereitstellen](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Fügen Sie der „Gruppe 1“ ein Skript als vorausgehende Aktion hinzu, um für die SQL-Verfügbarkeitsgruppe ein Failover durchzuführen. Verwenden Sie das Skript „ASR-SQL-FailoverAG“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript genau, und nehmen Sie die erforderlichen Änderungen im Skript vor.

    ![Hinzufügen-AG-Skript-Schritt-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Hinzufügen-AG-Skript-Schritt-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Fügen Sie ein Skript als nachfolgende Aktion hinzu, um den Failover-VMs der Webebene (Gruppe 2) einen Lastenausgleich anzufügen. Verwenden Sie das Skript „ASR-AddSingleLoadBalancer“ aus den Beispielskripts. Befolgen Sie die Anleitung im Skript genau, und nehmen Sie die erforderlichen Änderungen im Skript vor.

    ![Hinzufügen-LB-Skript-Schritt 1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Hinzufügen-LB-Skript-Schritt-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Fügen Sie einen manuellen Schritt zur Aktualisierung der DNS-Einträge hinzu, um auf die neue Farm in Azure zu verweisen.

    * Für Websites mit Internetzugriff sind keine DNS-Updates nach dem Failover erforderlich. Führen Sie die im Abschnitt „Netzwerkanleitung“ beschriebenen Schritte aus, um den Traffic Manager zu konfigurieren. Wenn Sie das Traffic Manager-Profil wie im vorherigen Abschnitt beschrieben eingerichtet haben, fügen Sie ein Skript hinzu, um den Dummy-Port (im Beispiel 800) im Azure-VM zu öffnen.

    * Fügen Sie bei internen Websites einen manuellen Schritt zur Aktualisierung des DNS-Eintrags hinzu, um auf die IP-Adresse des Lastenausgleichs des neuen virtuellen Computers mit Webebene zu verweisen.

4. Fügen Sie einen manuellen Schritt zur Wiederherstellung der Suchanwendung aus einer Sicherung hinzu, oder starten Sie einen neuen Suchdienst.

5. Zur Wiederherstellung der Suchdienstanwendung aus einer Sicherung, führen Sie die unten genannten Schritte aus.

    * Bei dieser Methode wird davon ausgegangen, dass vor Eintreten des Notfalls eine Sicherung der Suchdienstanwendung durchgeführt wurde und dass die Sicherung am Notfallwiederherstellungsstandort verfügbar ist.
    * Dies können Sie problemlos erreichen, indem Sie die Sicherung zeitlich planen (z.B. einmal täglich) und eine Kopierprozedur verwenden, um die Sicherung am Notfallwiederherstellungsstandort zu platzieren. Kopierprozeduren können Skriptprogramme wie z.B. AzCopy (Azure Copy) oder das Einrichten einer DFSR (Distributed File Services Replication) umfassen.
    * Nachdem die SharePoint-Farm nun ausgeführt wird, gehen Sie zu „Zentraladministration“, „Sichern und Wiederherstellen“, und wählen Sie „Wiederherstellen“ aus. Bei der Wiederherstellung wird der angegebene Sicherungsspeicherort abgefragt (möglicherweise müssen Sie den Wert aktualisieren). Wählen Sie die Sicherung der Suchdienstanwendung aus, die Sie wiederherstellen möchten.
    * Die Suche wird wiederhergestellt. Beachten Sie, dass bei der Wiederherstellung dieselbe Topologie (gleiche Anzahl von Servern) und dieselben Laufwerkbuchstaben, die diesen Servern zugewiesen werden, erwartet werden. Weitere Informationen finden Sie im Dokument [Restore Search service application in SharePoint 2013](/SharePoint/administration/restore-a-search-service-application) (Wiederherstellen der Suchdienstanwendung in SharePoint 2013).


6. Zum Starten einer neuen Suchdienstanwendung, führen Sie die unten genannten Schritte aus.

    * Bei dieser Methode wird davon ausgegangen, dass eine Sicherung der Suchverwaltungsdatenbank am Notfallwiederherstellungsstandort verfügbar ist.
    * Da die anderen Datenbanken der Suchdienstanwendung nicht repliziert werden, müssen Sie diese neu erstellen. Gehen Sie dazu zu „Zentraladministration“, und löschen Sie die Suchdienstanwendung. Löschen Sie die Indexdateien auf allen Servern, die den Suchindex hosten.
    * Erstellen Sie die Suchdienstanwendung erneut. Dadurch werden auch die Datenbanken neu erstellt. Es wird empfohlen, ein Skript zum erneuten Erstellen dieser Dienstanwendung vorzubereiten, da nicht alle Aktionen über die grafische Benutzeroberfläche ausgeführt werden können. Das Festlegen des Speicherorts des Indexlaufwerks sowie das Konfigurieren der Suchtopologie sind beispielsweise nur mithilfe von SharePoint PowerShell-Cmdlets möglich. Verwenden Sie das Windows PowerShell-Cmdlet „Restore-SPEnterpriseSearchServiceApplication“, und geben Sie die im Protokoll enthaltene und replizierte Suchverwaltungsdatenbank „Search_Service__DB“ an. Dieses Cmdlet liefert Suchkonfiguration, Schema, verwaltete Eigenschaften, Regeln und Quellen und erstellt einen Standardsatz der anderen Komponenten.
    * Sobald die Suchdienstanwendung neu erstellt wurde, müssen Sie eine vollständige Durchforstung für jede Inhaltsquelle starten, um den Suchdienst wiederherzustellen. Einige Analyseinformationen aus der lokalen Farm gehen verloren, wie z.B. Suchempfehlungen.

7. Nachdem Sie alle Schritte durchgeführt haben, speichern Sie den Wiederherstellungsplan. Der endgültige Wiederherstellungsplan sieht folgendermaßen aus:

    ![Gespeicherter Wiederherstellungsplan](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Durchführen eines Testfailovers
Befolgen Sie [diese Anleitung](site-recovery-test-failover-to-azure.md), um ein Testfailover durchzuführen.

1.  Wechseln Sie zum Azure-Portal, und wählen Sie Ihren Recovery Services-Tresor aus.
2.  Klicken Sie auf den Wiederherstellungsplan, der für die SharePoint-Anwendung erstellt wurde.
3.  Klicken Sie auf „Testfailover“.
4.  Wählen Sie den Wiederherstellungspunkt und das virtuelle Azure-Netzwerk aus, um den Prozess für das Testfailover zu starten.
5.  Wenn die sekundäre Umgebung aktiv ist, können Sie Ihre Überprüfungen durchführen.
6.  Klicken Sie nach Abschluss der Überprüfungen auf „Cleanup test failover“ (Testfailover bereinigen). Die Testfailover-Umgebung ist nun bereinigt.

Eine Anleitung zum Ausführen des Testfailovers für AD und DNS finden Sie im Dokument [Test failover considerations for AD and DNS](site-recovery-active-directory.md#test-failover-considerations) (Überlegungen zum Testfailover für AD und DNS).

Eine Anleitung zum Ausführen des Testfailovers für SQL Always ON-Verfügbarkeitsgruppen finden Sie im Dokument [Performing Application DR with Azure Site Recovery and doing Test failover](site-recovery-sql.md#disaster-recovery-of-an-application) (Durchführen einer Anwendungs-DR mit Azure Site Recovery und Testfailover).

## <a name="doing-a-failover"></a>Durchführen eines Failovers
Befolgen Sie [diese Anleitung](site-recovery-failover.md), um ein Failover durchzuführen.

1.  Wechseln Sie zum Azure-Portal, und wählen Sie Ihren Recovery Services-Tresor aus.
2.  Klicken Sie auf den Wiederherstellungsplan, der für die SharePoint-Anwendung erstellt wurde.
3.  Klicken Sie auf „Failover“.
4.  Wählen Sie einen Wiederherstellungspunkt aus, um den Failoverprozess zu starten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Replizieren anderer Anwendungen](site-recovery-workload.md) mit Site Recovery.
