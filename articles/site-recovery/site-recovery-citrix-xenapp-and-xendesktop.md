---
title: Einrichten der Notfallwiederherstellung für eine Citrix XenDesktop- und XenApp-Bereitstellung mit mehreren Ebenen mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie die Notfallwiederherstellung für Citrix XenDesktop- und XenApp-Bereitstellungen mithilfe von Azure Site Recovery einrichten.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61038232"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>Einrichten der Notfallwiederherstellung für eine Citrix XenDesktop- und XenApp-Bereitstellung mit mehreren Ebenen



Bei Citrix XenDesktop handelt es sich um eine Desktop-Virtualisierungslösung, bei der Desktops und Anwendungen als On-Demand-Dienst für beliebige Benutzer an beliebigen Standorten bereitgestellt werden. Mit der FlexCast-Übermittlungstechnologie kann XenDesktop schnell und sicher Anwendungen und Desktops für Benutzer bereitstellen.
Zurzeit bietet Citrix XenApp keine Funktionen für die Notfallwiederherstellung.

Bei einer guten Lösung für die Notfallwiederherstellung sollte die Modellierung von Wiederherstellungsplänen für die obigen komplexen Anwendungsarchitekturen möglich sein. Darüber hinaus sollten Sie angepasste Schritte hinzufügen können, um Anwendungszuordnungen zwischen unterschiedlichen Ebenen durchzuführen und so eine sichere Single-Click-Lösung zu haben, falls es in einem Notfall zu einem niedrigeren RTO-Wert kommt.

Dieses Dokument enthält detaillierte Schrittanleitungen zum Erstellen einer Notfallwiederherstellungslösung für Ihre lokalen Citrix XenApp-Bereitstellungen auf Hyper-V- und VMware vSphere-Plattformen. In diesem Dokument werden das Durchführen eines Testfailovers (Disaster Recovery Drill) und eines ungeplanten Failovers in Azure mithilfe von Wiederherstellungsplänen sowie die unterstützten Konfigurationen und erforderlichen Komponenten beschrieben.


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie mit den folgenden Verfahren vertraut sind:

1. [Replizieren von virtuellen VMware-Computern in Azure mithilfe von Site Recovery](site-recovery-vmware-to-azure.md)
1. [Entwerfen Ihres Netzwerks für die Notfallwiederherstellung](site-recovery-network-design.md)
1. [Testfailover in Azure in Site Recovery](site-recovery-test-failover-to-azure.md)
1. [Failover in Site Recovery](site-recovery-failover.md)
1. Replizieren eines Domänencontrollers unter [Schützen von Active Directory und DNS mit Azure Site Recovery](site-recovery-active-directory.md)
1. Replizieren von SQL Server unter [Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Bereitstellungsmuster

Eine Citrix XenApp- und XenDesktop-Farm weist in der Regel das folgende Bereitstellungsmuster auf:

**Bereitstellungsmuster**

Citrix XenApp- und XenDesktop-Bereitstellung mit AD DNS-Server, SQL-Datenbank-Server, Citrix Delivery Controller, StoreFront-Server, XenApp Master (VDA), Citrix XenApp License Server

![Bereitstellungsmuster 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery-Unterstützung

Für diesen Artikel wurden für das DR-Setup Citrix-Bereitstellungen auf virtuellen VMware-Computern verwendet, die mit vSphere 6.0/System Center VMM 2012 R2 verwaltet werden.

### <a name="source-and-target"></a>Quelle und Ziel

**Szenario** | **Am sekundären Standort** | **In Azure**
--- | --- | ---
**Hyper-V** | Nicht zutreffend | Ja
**VMware** | Nicht zutreffend | Ja
**Physischer Server** | Nicht zutreffend | Ja

### <a name="versions"></a>Versionen
Kunden können XenApp-Komponenten als virtuelle Computer unter Hyper-V oder VMware oder als physische Server bereitstellen. Mit Azure Site Recovery können physische und virtuelle Bereitstellungen in Azure geschützt werden.
Da XenApp 7.7 und höher in Azure unterstützt wird, kann nur für Bereitstellungen mit diesen Versionen ein Failover in Azure für die Notfallwiederherstellung oder Migration ausgeführt werden.

### <a name="things-to-keep-in-mind"></a>Bitte beachten

1. Schutz und Wiederherstellung von lokalen Bereitstellungen mithilfe von Computern mit Serverbetriebssystemen zum Übermitteln von in XenApp veröffentlichten Apps und Desktops werden unterstützt.

2. Schutz und Wiederherstellung von lokalen Bereitstellungen mit Computern mit Desktopbetriebssystemen zum Übermitteln von Desktop VDI für virtuelle Clientdesktops, einschließlich Windows 10, werden nicht unterstützt. Dies liegt daran, dass Site Recovery die Wiederherstellung von Computern mit Desktopbetriebssystemen nicht unterstützt.  Außerdem werden einige Betriebssysteme von virtuellen Clientdesktops (z.B. Windows 7) für die Lizenzierung in Azure noch nicht unterstützt. Weitere Informationen zur Lizenzierung für Client-/Serverdesktopcomputer in Azure finden Sie [hier](https://azure.microsoft.com/pricing/licensing-faq/).

3.  Azure Site Recovery kann keine lokalen MCS- oder PVS-Klone replizieren und schützen.
Sie müssen diese Klone mithilfe der Azure RM-Bereitstellung über den Delivery Controller neu erstellen.

4. NetScaler kann nicht mit Azure Site Recovery geschützt werden, da NetScaler auf FreeBSD basiert und Azure Site Recovery den Schutz des FreeBSD-Betriebssystems nicht unterstützt. Sie müssen nach dem Failover in Azure ein neues NetScaler-Gerät aus dem Azure Marketplace bereitstellen und konfigurieren.


## <a name="replicating-virtual-machines"></a>Replizieren von virtuellen Computern

Die folgenden Komponenten der Citrix XenApp-Bereitstellung müssen geschützt werden, um Replikation und Wiederherstellung zu aktivieren.

* Schutz des AD DNS-Servers
* Schutz des SQL-Datenbank-Servers
* Schutz von Citrix Delivery Controller
* Schutz des StoreFront-Servers
* Schutz von XenApp Master (VDA)
* Schutz des Citrix XenApp-Lizenzservers


**AD DNS-Serverreplikation**

Unter [Schützen von Active Directory und DNS mit Azure Site Recovery](site-recovery-active-directory.md) finden Sie Informationen zum Replizieren und Konfigurieren eines Domänencontrollers in Azure.

**Replikation des SQL-Datenbank-Servers**

Unter [Schützen von SQL Server mit der Notfallwiederherstellung von SQL Server und Azure Site Recovery](site-recovery-sql.md) finden Sie technische Informationen zu den empfohlenen Optionen für den Schutz von SQL-Servern.

Führen Sie [diese Schritte](site-recovery-vmware-to-azure.md) aus, um mit der Replikation der virtuellen Computer der anderen Komponenten zu beginnen.

![Schutz von XenApp-Komponenten](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Einstellungen für Compute und Netzwerk**

Sobald die Computer geschützt sind (Status wird unter „Replizierte Elemente“ als „Geschützt“ angezeigt), müssen die Einstellungen für „Compute und Netzwerk“ konfiguriert werden.
Unter „Compute und Netzwerk“ > „Compute-Eigenschaften“ können Sie den Namen und die Zielgröße des virtuellen Azure-Computers angeben.
Ändern Sie ggf. den Namen, damit er die Azure-Anforderungen erfüllt. Sie können auch Informationen zum Zielnetzwerk, zum Subnetz sowie zu der IP-Adresse anzeigen und hinzufügen, die der Azure-VM zugewiesen wird.

Beachten Sie Folgendes:

* Sie können die Ziel-IP-Adresse festlegen. Wenn Sie keine Adresse angeben, wird für den Computer, für den das Failover durchgeführt wurde, DHCP verwendet. Wenn Sie eine Adresse festlegen, die beim Failover nicht verfügbar ist, funktioniert das Failover nicht. Dieselbe Ziel-IP-Adresse kann für das Testfailover verwendet werden, wenn die Adresse im Testfailover-Netzwerk verfügbar ist.

* Für den AD/DNS-Server können Sie durch das Beibehalten der lokalen Adresse die gleiche Adresse wie der DNS-Server für das virtuelle Azure-Netzwerk angeben.

Die Anzahl der Netzwerkkarten hängt von der Größe ab, die Sie für den virtuellen Zielcomputer angeben. Hierbei gilt Folgendes:

*   Wenn die Anzahl der Netzwerkadapter des Quellcomputers maximal der Anzahl der Netzwerkadapter entspricht, die für die Größe des Zielcomputers zulässig ist, hat der Zielcomputer die gleiche Anzahl von Netzwerkadaptern wie der Quellcomputer.
*   Wenn die Anzahl der Netzwerkadapter für den virtuellen Quellcomputer die maximal zulässige Anzahl für die Größe des Zielcomputers übersteigt, wird die Anzahl verwendet, die maximal für die Größe des Zielcomputers zulässig ist.
* Wenn beispielsweise ein Quellcomputer zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe vier Netzwerkadapter unterstützt, erhält der Zielcomputer zwei Netzwerkadapter. Wenn der Quellcomputer dagegen zwei Netzwerkadapter besitzt und der Zielcomputer aufgrund seiner Größe nur einen Adapter unterstützt, erhält der Zielcomputer nur einen Adapter.
*   Wenn der virtuelle Computer über mehrere Netzwerkadapter verfügt, werden alle mit dem gleichen Netzwerk verbunden.
*   Wenn der virtuelle Computer über mehrere Netzwerkadapter verfügt, wird der erste Adapter, der in der Liste angezeigt wird, zum Standardnetzwerkadapter auf dem virtuellen Azure-Computer.


## <a name="creating-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans

Nach dem Aktivieren der Replikation für die virtuellen Computer der XenApp-Komponente besteht der nächste Schritt darin, einen Wiederherstellungsplan zu erstellen.
In einem Wiederherstellungsplan werden virtuelle Computer mit ähnlichen Anforderungen für Failover und Wiederherstellung gruppiert.  

**Schritte zum Erstellen eines Wiederherstellungsplans**

1. Fügen Sie die virtuellen Computer der XenApp-Komponente im Wiederherstellungsplan hinzu.
2. Klicken Sie auf „Wiederherstellungspläne“ > „+Wiederherstellungsplan“. Geben Sie einen aussagekräftigen Namen für den Wiederherstellungsplan ein.
3. Virtuelle VMware-Computer: Wählen Sie als Quelle den VMware-Prozessserver, als Ziel Microsoft Azure und als Bereitstellungsmodell Ressourcen-Manager aus, und klicken Sie auf „Elemente auswählen“.
4. Virtuelle Hyper-V-Computer: Wählen Sie als Quelle den VMM-Server, als Ziel Microsoft Azure und als Bereitstellungsmodell Ressourcen-Manager aus. Klicken Sie auf „Elemente auswählen“, und wählen Sie dann die virtuellen Computer der XenApp-Bereitstellung aus.

### <a name="adding-virtual-machines-to-failover-groups"></a>Hinzufügen von virtuellen Computern zu Failovergruppen

Wiederherstellungspläne können angepasst werden, um Failovergruppen für eine bestimmte Startreihenfolge, Skripts oder manuelle Aktionen hinzuzufügen. Die folgenden Gruppen müssen dem Wiederherstellungsplan hinzugefügt werden.

1. Failovergruppe 1: AD-DNS
2. Failovergruppe 2: SQL Server-VMs
2. Failovergruppe 3: Virtueller VDA-Masterimage-Computer
3. Failovergruppe 4: Delivery Controller und virtuelle Computer für StoreFront-Server


### <a name="adding-scripts-to-the-recovery-plan"></a>Hinzufügen von Skripts zum Wiederherstellungsplan

Skripts können vor oder nach einer bestimmten Gruppe in einem Wiederherstellungsplan ausgeführt werden. Manuelle Aktionen können ebenfalls eingeschlossen und während des Failovers ausgeführt werden.

Unten wird der angepasste Wiederherstellungsplan dargestellt:

1. Failovergruppe 1: AD-DNS
2. Failovergruppe 2: SQL Server-VMs
3. Failovergruppe 3: Virtueller VDA-Masterimage-Computer

   >[!NOTE]     
   >Die Schritte 4, 6 und 7 mit manuellen oder Skriptaktionen gelten nur für eine lokale XenApp-Umgebung mit MCS/PVS-Katalogen.

4. Manuelle oder Skriptaktion für Gruppe 3: Fahren Sie den virtuellen Master-VDA-Computer herunter.
Nach dem Failover zu Azure befindet sich der virtuelle Master-VDA-Computer in einem ausführbaren Status. Zum Erstellen von neuen MCS-Katalogen mithilfe von Azure-Hostingfunktionen muss sich der virtuelle VDA-Mastercomputer im Zustand „Beendet“ (Zuordnung aufgehoben) befinden. Fahren Sie den virtuellen Computer über das Azure-Portal herunter.

5. Failovergruppe 4: Delivery Controller und virtuelle Computer für StoreFront-Server
6. Manuelle oder Skriptaktion 1 für Gruppe 3:

    ***Azure RM-Hostverbindung hinzufügen***

    Erstellen Sie eine Azure-Hostverbindung auf dem Delivery Controller-Computer, um neue MCS-Kataloge in Azure bereitzustellen. Führen Sie die in [diesem Artikel](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/) beschriebenen Schritte aus.

7. Manuelle oder Skriptaktion 2 für Gruppe 3:

    ***MCS-Kataloge in Azure erneut erstellen***

    Die vorhandenen MCS- oder PVS-Klone am primären Standort werden nicht in Azure repliziert. Sie müssen diese Klone mithilfe des replizierten VDA-Masters und der Azure-Bereitstellung über den Delivery Controller neu erstellen. Führen Sie die in diesem [Artikel](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) erläuterten Schritte aus, um MCS-Kataloge in Azure zu erstellen.

![Wiederherstellungsplan für XenApp-Komponenten](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Sie können [diese Scripts](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) verwenden, um DNS mit den neuen IP-Adressen der virtuellen Computer zu aktualisieren, für die ein Failover ausgeführt wurde, oder um bei Bedarf einen Lastenausgleich für den Computer anzufügen, für den ein Failover ausgeführt wurde.


## <a name="doing-a-test-failover"></a>Durchführen eines Testfailovers

Befolgen Sie [diese Anleitung](site-recovery-test-failover-to-azure.md), um ein Testfailover durchzuführen.

![Wiederherstellungsplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Durchführen eines Failovers

Befolgen Sie [diese Anleitung](site-recovery-failover.md), wenn Sie ein Failover durchführen.

## <a name="next-steps"></a>Nächste Schritte

In [diesem Whitepaper](https://aka.ms/citrix-xenapp-xendesktop-with-asr) finden Sie weitere Informationen zum Replizieren von Citrix XenApp- und XenDesktop-Bereitstellungen. Informieren Sie sich weiter über das [Replizieren anderer Anwendungen](site-recovery-workload.md) mit Site Recovery.
