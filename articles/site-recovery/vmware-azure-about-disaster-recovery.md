---
title: Informationen zur Notfallwiederherstellung von virtuellen VMware-Computern in Azure mithilfe von Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Notfallwiederherstellung von virtuellen VMware-Computern mit dem Dienst Azure Site Recovery.
author: raynew
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 5/30/2019
ms.author: raynew
ms.openlocfilehash: a00c129126886bd71c82940aa340a8db29cf7a0e
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417799"
---
# <a name="about-disaster-recovery-of-vmware-vms-to-azure"></a>Informationen zur Notfallwiederherstellung von virtuellen VMware-Computern in Azure

Dieser Artikel bietet eine Übersicht über die Notfallwiederherstellung von lokalen virtuellen VMware-Computern in Azure mit dem Dienst [Azure Site Recovery](site-recovery-overview.md).

## <a name="what-is-bcdr"></a>Was ist BCDR?

Eine Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) hilft Ihnen, Ihren Geschäftsbetrieb am Laufen zu halten. Mit einer BCDR-Strategie bleiben Daten bei geplanten und unerwarteten Ausfallzeiten gesichert und verfügbar. Zudem wird sichergestellt, dass Anwendungen weiter ausgeführt werden. Neben den Features der BCDR-Plattform wie z.B. Regionspaaren und der Speicherung mit Hochverfügbarkeit bietet Azure auch Recovery Services als Bestandteil Ihrer BCDR-Lösung. Recovery Services umfasst Folgendes: 

- [Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) sichert lokale Daten und Daten von virtuellen Azure-Computern. Sie können Dateien und Ordner, bestimmte Workloads oder einen vollständigen virtuellen Computer sichern. 
- [Azure Site Recovery](site-recovery-overview.md) bietet Datenresilienz und Notfallwiederherstellung für Anwendungen und Workloads, die auf lokalen Computern oder virtuellen Azure-IaaS-Computern ausgeführt werden. Site Recovery orchestriert die Replikation und führt bei Ausfällen ein Failover in Azure aus. Zudem wird die Wiederherstellung aus Azure an Ihrem primären Standort durchgeführt. 

## <a name="how-does-site-recovery-do-disaster-recovery"></a>Wie wird die Notfallwiederherstellung mithilfe von Site Recovery durchgeführt?

1. Nach der Vorbereitung von Azure und Ihres lokalen Standorts müssen Sie die Replikation für die lokalen Computer einrichten und aktivieren.
2. Site Recovery orchestriert die erste Replikation des Computers entsprechend Ihren Richtlinieneinstellungen.
3. Nach der ersten Replikation repliziert Site Recovery Deltaänderungen in Azure. 
4. Nachdem die Replikation wie erwartet durchgeführt wurde, führen Sie ein Notfallwiederherstellungsverfahren durch.
    - Durch dieses Verfahren kann sichergestellt werden, dass das Failover im Realfall wie erwartet durchgeführt wird.
    - Bei dem Verfahren wird ein Testfailover durchgeführt, ohne Ihre Produktionsumgebung zu beeinträchtigen.
5. Bei einem Ausfall führen Sie ein vollständiges Failover in Azure durch. Sie können ein Failover für einen einzelnen Computer durchführen, oder Sie können einen Wiederherstellungsplan erstellen, mit dem das Failover für mehrere Computer gleichzeitig durchgeführt wird.
6. Beim Failover werden virtuelle Azure-Computer aus den VM-Daten in Managed Disks oder Speicherkonten erstellt. Benutzer können weiterhin über den virtuellen Azure-Computer auf Anwendungen und Workloads zugreifen.
7. Wenn der lokale Standort wieder verfügbar ist, erfolgt das Failback aus Azure.
8. Nachdem ein Failback durchgeführt wurde und Sie wieder über Ihren primären Standort arbeiten, starten Sie erneut die Replikation der lokalen virtuellen Computer in Azure.


## <a name="how-do-i-know-if-my-environment-is-suitable-for-disaster-recovery-to-azure"></a>Wie kann ich feststellen, ob meine Umgebung für die Notfallwiederherstellung in Azure geeignet ist?

Mit Site Recovery können alle Workloads repliziert werden, die auf einem unterstützten virtuellen VMware-Computer oder physischen Server ausgeführt werden. Folgendes müssen Sie in Ihrer Umgebung überprüfen:

- Führen Sie bei einer Replikation von virtuellen VMware-Computern die richtigen Versionen der VMware-Virtualisierungsserver aus? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#on-premises-virtualization-servers) überprüfen.
- Werden die Computer, die Sie replizieren möchten, unter einem unterstützten Betriebssystem ausgeführt? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#replicated-machines) überprüfen.
- Werden für die Linux-Notfallwiederherstellung auf den Computern ein unterstütztes Dateisystem und ein unterstützter Gastspeicher ausgeführt? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) überprüfen.
- Entsprechen die Computer, die Sie replizieren möchten, den Azure-Anforderungen? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#azure-vm-requirements) überprüfen.
- Wird Ihre Netzwerkkonfiguration unterstützt? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#network) überprüfen.
- Wird Ihre Speicherkonfiguration unterstützt? Dies können Sie [hier](vmware-physical-azure-support-matrix.md#storage) überprüfen.


## <a name="what-do-i-need-to-set-up-in-azure-before-i-start"></a>Was muss ich zur Vorbereitung in Azure einrichten?

Sie müssen Folgendes in Azure vorbereiten:

1. Überprüfen Sie, ob Ihr Azure-Konto über Berechtigungen zum Erstellen von virtuellen Computern in Azure verfügt.
2. Erstellen Sie ein Azure-Netzwerk, dem virtuelle Azure-Computer hinzugefügt werden, wenn sie nach einem Failover von Speicherkonten oder Managed Disks erstellt werden.
3. Richten Sie einen Azure Recovery Services-Tresor für Site Recovery ein. Der Tresor befindet sich im Azure-Portal und dient zum Bereitstellen, Konfigurieren, Orchestrieren, Überwachen und für die Problembehandlung Ihrer Site Recovery-Bereitstellung.

*Benötigen Sie weitere Hilfe?*

Erfahren Sie, wie Sie Azure einrichten, indem Sie [Ihr Konto überprüfen](tutorial-prepare-azure.md#verify-account-permissions), ein [Netzwerk](tutorial-prepare-azure.md#set-up-an-azure-network) erstellen und [einen Tresor einrichten](tutorial-prepare-azure.md#create-a-recovery-services-vault).



## <a name="what-do-i-need-to-set-up-on-premises-before-i-start"></a>Was muss ich zur Vorbereitung lokal einrichten?

In der lokalen Umgebung müssen Sie Folgendes einrichten:

1. Sie müssen mehrere Konten einrichten:

    - Wenn Sie virtuelle VMware-Computer replizieren, ist ein Konto erforderlich, über das in Site Recovery auf vCenter-Server- oder vSphere ESXi-Hosts zugegriffen werden kann, um virtuelle Computer automatisch zu ermitteln.
    - Ein Konto ist erforderlich, um den Site Recovery Mobility Service-Agent auf allen physischen und virtuellen Computern zu installieren, die Sie replizieren möchten.

2. Sie müssen die Kompatibilität Ihrer VMware-Infrastruktur überprüfen, wenn dies noch nicht erfolgt ist.
3. Vergewissern Sie sich, dass Sie nach einem Failover eine Verbindung mit virtuellen Azure-Computern herstellen können. Sie richten RDP auf lokalen Windows-Computern oder SSH auf Linux-Computern ein.

*Benötigen Sie weitere Hilfe?*
- Bereiten Sie Konten für die [automatische Ermittlung](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) und für die [Installation des Mobility Service](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-mobility-service-installation) vor.
- [Überprüfen Sie](vmware-azure-tutorial-prepare-on-premises.md#check-vmware-requirements), ob Ihre VMware-Einstellungen kompatibel sind.
- [Treffen Sie die entsprechenden Vorbereitungen](vmware-azure-tutorial-prepare-on-premises.md#prepare-to-connect-to-azure-vms-after-failover), damit Sie in Azure nach einem Failover eine Verbindung herstellen können.
- Weitere ausführliche Informationen zum Einrichten von IP-Adressen für virtuelle Azure-Computer nach einem Failover finden Sie in [diesem Artikel](concepts-on-premises-to-azure-networking.md).

## <a name="how-do-i-set-up-disaster-recovery"></a>Wie richte ich die Notfallwiederherstellung ein?

Nachdem Sie die lokale und Azure-Infrastruktur eingerichtet haben, können Sie die Notfallwiederherstellung einrichten.

1. Informationen zu den Komponenten, die Sie bereitstellen müssen, finden Sie unter [VMware in der Architektur für die Azure-Replikation](vmware-azure-architecture.md) und [Physische Server in der Architektur für die Azure-Replikation](physical-azure-architecture.md). Es gibt verschiedene Komponenten, daher ist es wichtig, sich damit vertraut zu machen, wie sie miteinander verbunden sind.
2. **Quellumgebung**: Als ersten Schritt bei der Bereitstellung richten Sie die Quellumgebung für die Replikation ein. Sie geben an, was Sie replizieren möchten und wohin die Daten repliziert werden sollen.
3. **Konfigurationsserver**: Sie müssen einen Konfigurationsserver in der lokalen Quellumgebung einrichten:
    - Der Konfigurationsserver ist ein einzelner lokaler Computer. Für die VMware-Notfallwiederherstellung wird empfohlen, diesen als virtuellen VMware-Computer bereitzustellen, der über eine heruntergeladene OVF-Vorlage bereitgestellt werden kann.
    - Der Konfigurationsserver koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure.
    - Auf dem Konfigurationsservercomputer werden verschiedene andere Komponenten ausgeführt.
        - Der Prozessserver empfängt, optimiert und sendet Replikationsdaten an ein Cachespeicherkonto in Azure. Der Prozessserver verarbeitet auch die automatische Installation des Mobility Service auf den Computern, die Sie replizieren möchten, und führt auf VMware-Servern die automatische Ermittlung von virtuellen Computern durch.
        - Der Masterzielserver verarbeitet die Replikationsdaten während des Failbacks von Azure.
    - Die Einrichtung umfasst die Registrierung des Konfigurationsservers im Tresor, das Herunterladen von MySQL Server und VMware PowerCLI sowie die Angabe der für die automatische Ermittlung und die Installation des Mobility Service erstellten Konten.
4. **Zielumgebung**: Sie richten die Azure-Zielumgebung ein, indem Sie Ihr Azure-Abonnement und die Netzwerkeinstellungen angeben.
5. **Replikationsrichtlinie**: Sie geben an, wie die Replikation erfolgen soll. Zu den erforderlichen Einstellungen gehört z.B. die Angabe, wie oft Wiederherstellungspunkte erstellt und gespeichert werden und ob anwendungskonsistente Momentaufnahmen erstellt werden sollen.
6. **Aktivieren Sie die Replikation**. Sie aktivieren die Replikation für lokale Computer. Wenn Sie ein Konto für die Installation des Mobility Service erstellt haben, wird dieser installiert, wenn Sie die Replikation für einen Computer aktivieren. 

*Benötigen Sie weitere Hilfe?*

- Eine kurze exemplarische Vorgehensweise hierfür finden Sie im [VMware-Tutorial](vmware-azure-tutorial.md) und in der [exemplarischen Vorgehensweise für physische Server](physical-azure-disaster-recovery.md).
- [Erfahren Sie mehr](vmware-azure-set-up-source.md) über das Einrichten der Quellumgebung.
- [Erfahren Sie mehr](vmware-azure-deploy-configuration-server.md) über die Anforderungen für den Konfigurationsserver und das Einrichten des Konfigurationsservers mit einer OVF-Vorlage für die VMware-Replikation. Wenn Sie aus einem bestimmten Grund keine Vorlage verwenden können oder wenn Sie physische Server replizieren, [befolgen Sie diese Anweisungen](physical-azure-set-up-source.md#set-up-the-source-environment).
- [Erfahren Sie mehr](vmware-azure-set-up-target.md) über die Zieleinstellungen.
- [Hier erhalten Sie weitere Informationen](vmware-azure-set-up-replication.md) zum Einrichten einer Replikationsrichtlinie.
- [Erfahren Sie](vmware-azure-enable-replication.md), wie Sie die Replikation aktivieren und Datenträger von der Replikation [ausschließen](vmware-azure-exclude-disk.md).


## <a name="something-went-wrong-how-do-i-troubleshoot"></a>Es sind Fehler aufgetreten. Wie kann ich die Problembehandlung durchführen?

- Versuchen Sie in einem ersten Schritt, [Ihre Umgebung zu überwachen](site-recovery-monitor-and-troubleshoot.md), um den Status von replizierten Elementen, Aufträgen und Infrastrukturproblemen zu überprüfen und alle Fehler zu identifizieren.
- Wenn Sie die erste Replikation nicht abschließen können oder wenn die laufende Replikation nicht wie erwartet durchgeführt wird, finden Sie [in diesem Artikel](vmware-azure-troubleshoot-replication.md) häufig auftretende Fehler und Tipps zur Problembehandlung.
- Wenn Probleme bei der automatischen Installation des Mobility Service auf den Computern, die Sie replizieren möchten, aufgetreten sind, finden Sie häufig auftretende Fehler in [diesem Artikel](vmware-azure-troubleshoot-push-install.md).
- Wenn das Failover nicht wie erwartet ausgeführt wird, finden Sie häufig auftretende Fehler in [diesem Artikel](site-recovery-failover-to-azure-troubleshoot.md).
- Wenn das Failback nicht funktioniert, überprüfen Sie, ob das entsprechende Problem [in diesem Artikel](vmware-azure-troubleshoot-failback-reprotect.md) aufgeführt ist.



## <a name="next-steps"></a>Nächste Schritte

Nachdem die Replikation eingerichtet ist, sollten Sie ein [Notfallwiederherstellungsverfahren durchführen](tutorial-dr-drill-azure.md), um sicherzustellen, dass das Failover wie erwartet ausgeführt wird. 
