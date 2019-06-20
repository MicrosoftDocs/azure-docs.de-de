---
title: Einrichten der Notfallwiederherstellung für eine Dynamics AX-Bereitstellung mit mehreren Ebenen mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Notfallwiederherstellung für Dynamics AX mithilfe von Azure Site Recovery einrichten.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: b97bf56c23dfa96acf7cb5af5ac28b4270de117d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61281432"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Einrichten der Notfallwiederherstellung für eine Dynamics AX-Anwendung mit mehreren Ebenen   




 Dynamics AX ist eine der bei Unternehmen beliebtesten ERP-Lösungen zur standortübergreifenden Standardisierung von Prozessen, zum Verwalten von Ressourcen und zum Vereinfachen der Konformität. Da die Anwendung für eine Organisation unternehmenskritisch ist, muss sie in einem Notfall schnellstmöglich wieder in Betrieb genommen werden können.

Dynamics AX bietet derzeit keine vorkonfigurierten Funktionen zur Notfallwiederherstellung. Dynamics AX besteht aus vielen Serverkomponenten wie z.B. Windows Application Object Server, Azure Active Directory, Azure SQL-Datenbank, SharePoint Server und Reporting Services. Das manuelle Verwalten der Notfallwiederherstellung jeder Komponente ist nicht nur teuer, sondern auch fehleranfällig.

In diesem Artikel wird erläutert, wie Sie mithilfe von [Azure Site Recovery](site-recovery-overview.md) eine Lösung zur Notfallwiederherstellung für Ihre Dynamics AX-Anwendung erstellen können. Außerdem werden geplante und nicht geplante Testfailover mit einem durch einen Klick aktivierbaren Wiederherstellungsplan, unterstützte Konfigurationen und Voraussetzungen behandelt.



## <a name="prerequisites"></a>Voraussetzungen

Das Implementieren der Notfallwiederherstellung für Dynamics AX mit Site Recovery erfordert folgende Voraussetzungen:

• Richten Sie eine lokale Bereitstellung von Dynamics AX ein.

• Erstellen Sie einen Site Recovery-Tresor in einem Azure-Abonnement.

• Wenn Azure Ihr Wiederherstellungsstandort ist, führen Sie das Tool Azure Virtual Machine Readiness Assessment auf den virtuellen Computern aus. Diese müssen mit den Diensten Azure Virtual Machines und Site Recovery kompatibel sein.

## <a name="site-recovery-support"></a>Site Recovery-Unterstützung

Für die Erstellung dieses Artikels wurden virtuelle VMware-Computer mit Dynamics AX 2012 R3 unter Windows Server 2012 R2 Enterprise verwendet. Da die Site Recovery-Replikation anwendungsunabhängig ist, gelten die hier angegebenen Empfehlungen voraussichtlich für die folgenden Szenarien.

### <a name="source-and-target"></a>Quelle und Ziel

**Szenario** | **Am sekundären Standort** | **In Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**VMware** | Ja | Ja
**Physischer Server** | Ja | Ja

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Aktivieren der Notfallwiederherstellung der Dynamics AX-Anwendung mithilfe von Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Schützen Sie Ihre Dynamics AX-Anwendung
Um die vollständige Replikation und Wiederherstellung der Anwendung aktivieren zu können, muss jede Komponente von Dynamics AX geschützt werden.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Einrichten der Replikation von Active Directory und DNS

Damit die Dynamics AX-Anwendung funktioniert, muss Active Directory am Standort der Notfallwiederherstellung vorhanden sein. Basierend auf der Komplexität der lokalen Umgebung des Kunden werden die folgenden beiden Optionen empfohlen.

**Option 1:**

Der Kunde verfügt über eine geringe Anzahl von Anwendungen und über einen einzelnen Domänencontroller für den gesamten lokalen Standort und plant die gleichzeitige Ausführung eines Failovers für den gesamten Standort. Wir empfehlen die Verwendung der Site Recovery-Replikation zum Replizieren des Domänencontrollercomputers an einem sekundären Standort (gilt sowohl für Standort-zu-Standort- als auch für Standort-zu-Azure-Szenarien).

**Option 2**

Der Kunde verfügt über eine große Anzahl von Anwendungen und führt eine Active Directory-Gesamtstruktur aus und plant die gleichzeitige Ausführung eines Failovers für einige Anwendungen. Es wird empfohlen, dass Sie einen zusätzlichen Domänencontroller am Standort für die Notfallwiederherstellung einrichten (einen sekundären Standort oder in Azure).

 Weitere Informationen finden Sie unter [Verfügbarmachen eines Domänencontrollers am Standort einer Notfallwiederherstellung](site-recovery-active-directory.md). Für den Rest dieses Dokuments wird davon ausgegangen, dass ein Domänencontroller am Standort der Notfallwiederherstellung verfügbar ist.

### <a name="2-set-up-sql-server-replication"></a>2. Einrichten der SQL Server-Replikation
Eine technische Anleitung zur empfohlenen Option zum Schutz der SQL-Schicht finden Sie unter [Replizieren von Anwendungen mit SQL Server und Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Aktivieren des Schutzes für den Dynamics AX-Client und die virtuellen Application Object Server-Computer
Führen Sie relevante Konfigurationen für Site Recovery durch, je nachdem wo die virtuellen Computer bereitgestellt werden – auf [Hyper-V](site-recovery-hyper-v-site-to-azure.md) oder auf [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Es wird empfohlen, dass Sie die ausfallsichere Häufigkeit auf 15 Minuten festlegen.
>

Die folgende Abbildung zeigt den Schutzstatus der virtuellen Computer der Dynamics-Komponente in einem Standort-zu-Azure-Schutzszenario in VMware.

![Geschützte Elemente](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurieren der Netzwerkeinstellungen
**Konfigurieren der Compute- und Netzwerkeinstellungen für virtuelle Computer**

Konfigurieren Sie die Netzwerkeinstellungen für den Dynamics AX-Client und die virtuellen Application Object Server-Computer in Site Recovery so, dass die VM-Netzwerke nach dem Failover mit dem richtigen Notfallwiederherstellungsnetzwerk verbunden werden. Stellen Sie sicher, dass das Netzwerk der Notfallwiederherstellung auf der SQL-Schicht geroutet werden kann.

Sie können den virtuellen Computer in den replizierten Elementen auswählen, um die Netzwerkeinstellungen wie in der folgenden Abbildung gezeigt zu konfigurieren:

* Wählen Sie für Application Object Server-Server die richtige Verfügbarkeitsgruppe aus.

* Geben Sie bei Verwendung einer statischen IP-Adresse im Textfeld **Ziel-IP** die IP an, die vom virtuellen Computer genutzt werden soll.

    ![Netzwerkeinstellungen](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Erstellen eines Wiederherstellungsplans

Sie können einen Wiederherstellungsplan in Site Recovery erstellen, um den Failoverprozess zu automatisieren. Fügen Sie dem Wiederherstellungsplan eine Anwendungs- und eine Webschicht hinzu. Sortieren Sie diese nach verschiedenen Gruppen, damit das Front-End vor der Anwendungsschicht beendet wird.

1. Wählen Sie den Site Recovery-Tresor in Ihrem Abonnement aus, und wählen Sie dann die Kachel **Wiederherstellungspläne** aus.

2. Wählen Sie **+ Wiederherstellungsplan** aus, und geben Sie einen Namen an.

3. Wählen Sie die **Quelle** und das **Ziel** aus. Das Ziel kann Azure oder ein sekundärer Standort sein. Wenn Sie Azure auswählen, müssen Sie ein Bereitstellungsmodell angeben.

    ![Wiederherstellungsplan erstellen](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Wählen Sie den Anwendungsobjektserver und die Client-VMs für den Wiederherstellungsplan aus, und wählen Sie ✓.

    ![Auswählen von Elementen](./media/site-recovery-dynamics-ax/selectvms.png)

    Beispiel für einen Wiederherstellungsplan:

    ![Details eines Wiederherstellungsplans](./media/site-recovery-dynamics-ax/recoveryplan.png)

Sie können den Wiederherstellungsplan für die Dynamics AX-Anwendung anpassen, indem Sie die folgenden Schritte hinzufügen. Die Abbildung oben zeigt den vollständigen Wiederherstellungsplan, nachdem Sie alle Schritte hinzugefügt haben.


* **Schritte für das SQL Server-Failover:** Informationen zu SQL Server-spezifischen Wiederherstellungsschritten finden Sie unter [Replizieren von Anwendungen mit SQL Server und Azure Site Recovery](site-recovery-sql.md).

* **Failovergruppe 1:** Führen Sie ein Failover der virtuellen Application Object Server-Computer aus.
Stellen Sie sicher, dass der gewählte Wiederherstellungspunkt sich so nah wie möglich, aber nicht vor dem PIT der Datenbank befindet.

* **Skript:** Fügen Sie einen Lastenausgleich hinzu (nur E/A).
Fügen Sie (über Azure Automation) hinter der Application Object Server-VM-Gruppe ein Skript hinzu, um einen Lastenausgleich hinzuzufügen. Sie können ein Skript in diese Aufgabe einfügen. Weitere Informationen finden Sie unter [How to add a load balancer for multitier application disaster recovery](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/) (Hinzufügen eines Lastenausgleichs für die Notfallwiederherstellung für Anwendungen mit mehreren Schichten).

* **Failovergruppe 2:** Führen Sie ein Failover für die virtuellen Dynamics AX-Clientcomputer aus. Durchführen eines Failovers für VMs der Webschicht im Rahmen des Wiederherstellungsplans.


### <a name="perform-a-test-failover"></a>Ausführen eines Testfailovers

Weitere spezifische Informationen zu Active Directory während des Testfailovers finden Sie im Begleithandbuch „Lösung für Notfallwiederherstellung für Active Directory“.

Weitere spezifische Informationen zu SQL Server während des Testfailovers finden Sie unter [Replizieren von Anwendungen mit SQL Server und Azure Site Recovery](site-recovery-sql.md).

1. Wählen Sie im Azure-Portal Ihren Site Recovery-Tresor aus.

2. Wählen Sie den Wiederherstellungsplan aus, der für Dynamics AX erstellt wurde.

3. Wählen Sie **Testfailover** aus.

4. Wählen Sie das virtuelle Netzwerk aus, um den Testfailoverprozess zu starten.

5. Nachdem die sekundäre Umgebung aktiv ist, können Sie Ihre Überprüfungen durchführen.

6. Wählen Sie nach Abschluss der Überprüfungen die Option **Validierung abgeschlossen** aus. Die Umgebung für das Testfailover wird dann bereinigt.

Weitere Informationen zum Ausführen eines Testfailovers finden Sie unter [Testfailover in Azure in Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Ausführen eines Failovers

1. Wählen Sie im Azure-Portal Ihren Site Recovery-Tresor aus.

2. Wählen Sie den Wiederherstellungsplan aus, der für Dynamics AX erstellt wurde.

3. Wählen Sie **Failover** und dann **Failover** aus.

4. Wählen Sie das Zielnetzwerk aus, und wählen Sie dann **✓**, um den Failoverprozess zu starten.

Weitere Informationen zum Ausführen eines Failovers finden Sie unter [Failover in Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Ein Failback durchführen

Spezifische Überlegungen zu SQL Server während des Failbacks finden Sie unter [Replizieren von Anwendungen mit SQL Server und Azure Site Recovery](site-recovery-sql.md).

1. Wählen Sie im Azure-Portal Ihren Site Recovery-Tresor aus.

2. Wählen Sie den Wiederherstellungsplan aus, der für Dynamics AX erstellt wurde.

3. Wählen Sie **Failover** und dann **Failover** aus.

4. Wählen Sie **Richtung ändern** aus.

5. Wählen Sie die entsprechenden Optionen aus: Datensynchronisierung und VM-Erstellung.

6. Wählen Sie **✓**, um den Failbackprozess zu starten.


Weitere Informationen zum Ausführen eines Failbacks finden Sie unter [Failback für virtuelle VMware-Computer von Azure zur lokalen Infrastruktur](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Zusammenfassung
Mithilfe von Site Recovery können Sie für Active Directory einen vollständig automatisierten Notfallwiederherstellungsplan für Ihre Dynamics AX-Anwendung erstellen. Bei einer Störung können Sie das Failover von überall aus in Sekundenschnelle einleiten und die Anwendung binnen weniger Minuten wieder in Betrieb nehmen.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Schutz von Unternehmensworkloads mit Site Recovery finden Sie unter [Welche Workloads können mit Azure Site Recovery geschützt werden?](site-recovery-workload.md).
