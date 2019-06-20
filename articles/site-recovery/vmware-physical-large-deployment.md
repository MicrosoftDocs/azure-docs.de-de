---
title: Einrichten der Notfallwiederherstellung in Azure für eine hohe Anzahl von VMware-VMs oder physischen Servern mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mit Azure Site Recovery die Notfallwiederherstellung in Azure für eine hohe Anzahl von lokalen VMware-VMs oder physischen Servern einrichten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: raynew
ms.openlocfilehash: e96aafe61c0d8547ffca9e97bfd9e90c9529155f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237278"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Einrichten der skalierbaren Notfallwiederherstellung für VMware-VMs/physische Server

In diesem Artikel wird beschrieben, wie Sie mit dem [Azure Site Recovery](site-recovery-overview.md)-Dienst die Notfallwiederherstellung in Azure für eine hohe Anzahl (> 1000) von lokalen VMware-VMs oder physischen Servern in Ihrer Produktionsumgebung einrichten.


## <a name="define-your-bcdr-strategy"></a>Definieren Ihrer BCDR-Strategie

Im Rahmen Ihrer BCDR-Strategie (Business Continuity & Disaster Recovery) definieren Sie RPOs (Recovery Point Objectives) und RTOs (Recovery Time Objectives) für Ihre Business-Apps und -Workloads. Mit einem RTO werden die Zeitdauer und der Servicelevel gemessen, innerhalb derer eine Business-App oder ein Prozess wiederhergestellt werden und verfügbar sein muss, damit Continuity-Probleme vermieden werden.
- Site Recovery bietet die fortlaufende Replikation für VMware-VMs und physische Server sowie eine [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) für RTO.
- Wenn Sie beim Planen einer umfangreichen Notfallwiederherstellung für VMware-VMs überlegen, welche Azure-Ressourcen Sie benötigen, können Sie einen RTO-Wert angeben, der für Kapazitätsberechnungen verwendet wird.


## <a name="best-practices"></a>Bewährte Methoden

Einige allgemeine bewährte Methoden für die umfangreiche Wiederherstellung. Diese bewährten Methoden werden in den folgenden Abschnitten des Dokuments detaillierter beschrieben.

- **Ermitteln der Zielanforderungen**: Schätzen Sie vor dem Einrichten der Notfallwiederherstellung die Kapazität für horizontales Skalieren und die Ressourcenanforderungen in Azure ein.
- **Planen der Site Recovery-Komponenten**: Überlegen Sie, welche Site Recovery-Komponenten (Konfigurationsserver, Prozessserver) Sie benötigen, um die geschätzte Kapazität zu erfüllen.
- **Einrichten von einem oder mehreren Prozessservern mit horizontaler Skalierung**: Verwenden Sie nicht den Prozessserver, der standardmäßig auf dem Konfigurationsserver ausgeführt wird. 
- **Ausführen der neuesten Updates**: Das Site Recovery-Team veröffentlicht regelmäßig neue Versionen der Site Recovery-Komponenten. Stellen Sie sicher, dass Sie die neuesten Versionen verwenden. Hierfür können Sie die [Neuerungen](site-recovery-whats-new.md) verfolgen, um über Updates auf dem Laufenden zu bleiben, und [Updates aktivieren und installieren](service-updates-how-to.md), sobald sie veröffentlicht werden.
- **Proaktive Überwachung**: Wenn Sie die Notfallwiederherstellung einrichten und ausführen, sollten Sie den Status und die Integrität der replizierten Computer und der Infrastrukturressourcen proaktiv überwachen.
- **Übungen zur Notfallwiederherstellung**: Sie sollten regelmäßig Übungen zur Notfallwiederherstellung durchführen. Diese haben keine Auswirkungen auf Ihre Produktionsumgebung, können aber sicherstellen, dass ein Failover zu Azure im Bedarfsfall wie erwartet funktioniert.



## <a name="gather-capacity-planning-information"></a>Sammeln von Informationen zur Kapazitätsplanung

Sammeln Sie Informationen über Ihre lokale Umgebung, um die Kapazitätsanforderungen für Ihr Ziel (Azure) zu bewerten und zu schätzen.
- Für VMware führen Sie hierzu den Bereitstellungsplaner für VMware-VMs aus.
- Für physische Server sammeln Sie die Informationen manuell.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Ausführen des Bereitstellungsplaners für VMware-VMs

Mithilfe des Bereitstellungsplaners können Sie Informationen über Ihre lokale VMware-Umgebung sammeln.

- Führen Sie den Bereitstellungsplaner in einer Phase aus, die typische Änderungen Ihrer VMs beinhaltet. Auf diese Weise erzielen Sie genauere Schätzungen und Empfehlungen.
- Es wird empfohlen, den Bereitstellungsplaner auf dem Konfigurationsserver auszuführen, da der Planer den Durchsatz des Servers berechnet, auf dem er ausgeführt wird. [Erfahren Sie mehr](site-recovery-vmware-deployment-planner-run.md#get-throughput) über das Messen des Durchsatzes.
- Wenn Sie noch keinen Konfigurationsserver eingerichtet haben:
    - Hier [erhalten Sie eine Übersicht](vmware-physical-azure-config-process-server-overview.md) über die Site Recovery-Komponenten.
    - [Richten Sie einen Konfigurationsserver ein](vmware-azure-deploy-configuration-server.md), auf dem Sie den Bereitstellungsplaner ausführen können.

Führen Sie den Planer anschließend wie folgt aus:

1. [Erfahren Sie mehr](site-recovery-deployment-planner.md) den Bereitstellungsplaner. Sie können die neueste Version über das Portal oder [direkt herunterladen](https://aka.ms/asr-deployment-planner).
2. Überprüfen Sie die [Voraussetzungen](site-recovery-deployment-planner.md#prerequisites) und die [neuesten Updates](site-recovery-deployment-planner-history.md) für den Bereitstellungsplaner. Anschließend können Sie das Tool [herunterladen und extrahieren](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool).
3. Die [Ausführung des Bereitstellungsplaners](site-recovery-vmware-deployment-planner-run.md) sollte auf dem Konfigurationsserver erfolgen.
4. [Generieren Sie einen Bericht](site-recovery-vmware-deployment-planner-run.md#generate-report), um Schätzungen und Empfehlungen zusammenzufassen.
5. Analysieren Sie die [Empfehlungen im Bericht](site-recovery-vmware-deployment-planner-analyze-report.md) und die [Kostenschätzungen](site-recovery-vmware-deployment-planner-cost-estimation.md).

>[!NOTE]
> Standardmäßig ist das Tool so konfiguriert, dass für bis zu 1000 VMs Profile und Berichte erstellt werden. Sie können diesen Grenzwert ändern, indem Sie den Schlüsselwert „MaxVMsSupported“ in der Datei „ASRDeploymentPlanner.exe.config“ erhöhen.

## <a name="plan-target-azure-requirements-and-capacity"></a>Planen der Zielanforderungen und -kapazität (Azure)

Mithilfe der erfassten Schätzungen und Empfehlungen können Sie Zielressourcen und die Kapazität planen. Wenn Sie den Bereitstellungsplaner für VMware-VMs ausgeführt haben, können Sie zur Unterstützung einige [Empfehlungen im Bericht](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) verwenden.

- **Kompatible VMs**: Ermitteln Sie hiermit die Anzahl der VMs, die für die Notfallwiederherstellung in Azure bereit sind. Die Empfehlungen zur Netzwerkbandbreite und zu Azure-Kernen beruhen auf dieser Zahl.
- **Erforderliche Netzwerkbandbreite**: Beachten Sie, welche Bandbreite für die Deltareplikation kompatibler VMs erforderlich ist. 
    - Beim Ausführen des Planers geben Sie den gewünschten RPO-Wert in Minuten an. In den Empfehlungen wird aufgeführt, welche Bandbreite erforderlich ist, um diesen RPO-Wert in 100 % und in 90 % der Fälle einzuhalten. 
    - Bei den Empfehlungen für die Netzwerkbandbreite wird die Bandbreite berücksichtigt, die für die gesamte Anzahl der im Planer empfohlenen Konfigurations- und Prozessserver erforderlich ist.
- **Erforderliche Azure-Kerne**: Beachten Sie die Anzahl der Kerne, die auf Grundlage der Anzahl kompatibler VMs in der Azure-Zielregion erforderlich sind. Wenn nicht genügend Kerne verfügbar sind, kann Site Recovery beim Failover nicht die erforderlichen Azure-VMs erstellen.
- **Empfohlene VM-Batchgröße**: Die empfohlene Batchgröße beruht auf der Fähigkeit, die Erstreplikation für den Batch standardmäßig innerhalb von 72 Stunden abzuschließen und gleichzeitig den RPO-Wert zu 100 % zu erfüllen. Der Stundenwert kann geändert werden.

Sie können diese Empfehlungen verwenden, um Azure-Ressourcen, die Netzwerkbandbreite und die VM-Batchverarbeitung zu planen.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planen von Azure-Abonnements und -Kontingenten

Wir möchten sicherstellen, dass die verfügbaren Kontingente im Zielabonnement für einen Failover ausreichen.

**Aufgabe** | **Details** | **Aktion**
--- | --- | ---
**Überprüfen der Kerne** | Wenn die Kerne im verfügbaren Kontingent zum Zeitpunkt des Failovers die Gesamtzielanzahl nicht erreichen oder übersteigen, schlagen die Failover fehl. | Überprüfen Sie für VMware-VMs, ob im Zielabonnement genügend Kerne verfügbar sind, um die Empfehlung des Bereitstellungsplaners für Kerne zu erfüllen.<br/><br/> Überprüfen Sie bei physischen Servern, ob die Azure-Kerne die manuellen Schätzungen erfüllen.<br/><br/> Klicken Sie zum Überprüfen von Kontingenten im Azure-Portal unter **Abonnement** auf **Nutzung + Kontingente**.<br/><br/> [Erfahren Sie mehr](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) über das Erhöhen von Kontingenten.
**Überprüfen von Failover-Grenzwerten** | Die Anzahl von Failovern darf die Failover-Grenzwerte von Site Recovery nicht überschreiten. |  Wenn die Failover die Grenzwerte überschreiten, können Sie Abonnements hinzufügen und Failover für mehrere Abonnements durchführen oder das Kontingent für ein Abonnement erhöhen. 


### <a name="failover-limits"></a>Failover-Grenzwerte

Die Grenzwerte geben die Anzahl der Failover an, die von Site Recovery innerhalb einer Stunde unterstützt wird. Hierbei wird von drei Datenträgern pro Computer ausgegangen.

Was bedeutet Einhaltung? Um eine Azure-VM zu starten, ist für Azure erforderlich, dass einige Treiber den Status „Bootstart“ aufweisen und Dienste wie DHCP für den automatischen Start festgelegt sind.
- Auf Computern, die diese Voraussetzungen einhalten, sind diese Einstellungen bereits festgelegt.
- Bei Computern unter Windows können Sie die Einhaltung proaktiv prüfen und bei Bedarf selbst festlegen. [Weitere Informationen](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010)
- Bei Linux-Computern wird die Einhaltung der Vorgaben erst zum Zeitpunkt des Failovers festgelegt.

**Hält der Computer die Voraussetzungen für Azure ein?** | **Azure-VM-Grenzwerte (Failover bei verwalteten Datenträgern)**
--- | --- 
Ja | 2000
Nein | 1000

- Bei Grenzwerten wird davon ausgegangen, dass in der Zielregion für das Abonnement derzeit nur eine minimale Anzahl von Vorgängen ausgeführt wird.
- Einige Azure-Regionen sind kleiner und weisen möglicherweise etwas niedrigere Grenzwerte auf.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planen von Infrastruktur und VM-Konnektivität

Nach dem Failover auf Azure müssen Ihre Workloads genauso funktionieren wie bei der lokalen Ausführung, und die Benutzer müssen in der Lage sein, auf die auf Azure-VMs ausgeführten Workloads zugreifen zu können.

- [Erfahren Sie mehr](site-recovery-active-directory.md#test-failover-considerations) über einen Failover Ihrer lokalen Active Directory- oder DNS-Infrastruktur zu Azure.
- [Erfahren Sie mehr](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) über die Vorbereitung einer Verbindung mit Azure-VMs nach einem Failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Planen der Quellenkapazität und der Anforderungen

Es ist wichtig, dass Sie über eine ausreichende Anzahl von Konfigurationsservern und Prozessservern für horizontales Skalieren verfügen, um die Kapazitätsanforderungen zu erfüllen. Beginnen Sie bei der umfangreichen Bereitstellung mit einem einzelnen Konfigurationsserver und einem einzelnen Prozessserver für horizontales Skalieren. Wenn Sie die vorgeschriebenen Grenzwerte erreichen, fügen Sie zusätzliche Server hinzu.

>[!NOTE]
> Für VMware-VMs finden Sie im Bereitstellungsplaner einige Empfehlungen zu den erforderlichen Konfigurations- und Prozessservern. Es wird empfohlen, die in den folgenden Verfahren enthaltenen Tabellen zu verwenden, statt der Empfehlung des Bereitstellungsplaners zu folgen. 


## <a name="set-up-a-configuration-server"></a>Einrichten eines Konfigurationsservers
 
Die Kapazität des Konfigurationsservers wird durch die Anzahl der Computer beeinflusst, die eine Replikation ausführen, und nicht durch die Datenänderungsrate. Um zu ermitteln, ob Sie zusätzliche Konfigurationsserver benötigen, verwenden Sie diese definierten VM-Grenzwerte.

**CPU** | **Memory** | **Cachedatenträger** | **Grenzwert für replizierte Computer**
 --- | --- | --- | ---
8 vCPUs<br> 2 Sockets * 4 Kerne mit 2,5 GHz | 16 GB | 600 TB | Bis zu 550 Computer<br> Es wird angenommen, dass jeder Computer über drei Datenträger mit jeweils 100 GB verfügt.

- Diese Grenzwerte basieren auf einem Konfigurationsserver, der mithilfe einer OVF-Vorlage eingerichtet wird.
- Für die Grenzwerte wird angenommen, dass Sie nicht den Prozessserver verwenden, der standardmäßig auf dem Konfigurationsserver ausgeführt wird.

Wenn Sie einen neuen Konfigurationsserver hinzufügen müssen, befolgen Sie diese Anweisungen:

- [Einrichten eines Konfigurationsservers](vmware-azure-deploy-configuration-server.md) für die Notfallwiederherstellung von virtuellen VMware-Computern mithilfe einer OVF-Vorlage.
- [Manuelles Einrichten eines Konfigurationsservers](physical-azure-set-up-source.md) für physische Server oder für VMware-Bereitstellungen, bei denen keine OVF-Vorlage verwendet werden kann.

Beachten Sie beim Einrichten eines Konfigurationsservers Folgendes:

- Wenn Sie einen Konfigurationsserver einrichten, sollten Sie das Abonnement und den Tresor berücksichtigen, in dem er sich befindet, da diese nach der Einrichtung nicht geändert werden sollten. Falls es nötig wird, den Tresor zu ändern, müssen Sie den Konfigurationsserver vom Tresor trennen und erneut registrieren. Dadurch wird die Replikation von virtuellen Computern im Tresor beendet.
- Wenn Sie einen Konfigurationsserver mit mehreren Netzwerkadaptern einrichten möchten, sollte dies während der Einrichtung erfolgen. Nach der Registrierung des Konfigurationsservers im Tresor ist dies nicht möglich.

## <a name="set-up-a-process-server"></a>Einrichten eines Prozessservers

Die Kapazität des Prozessservers wird durch Datenänderungsraten beeinflusst und nicht durch die Anzahl der Computer, die für Replikation aktiviert sind.

- Bei umfangreichen Bereitstellungen sollten Sie immer über mindestens einen Prozessserver für horizontales Skalieren verfügen.
- Um zu ermitteln, ob Sie zusätzliche Server benötigen, verwenden Sie die folgende Tabelle.
- Es wird empfohlen, einen Server mit der höchsten Spezifikation hinzuzufügen. 


**CPU** | **Memory** | **Cachedatenträger** | **Datenänderungsrate**
 --- | --- | --- | --- 
12 vCPUs<br> 2 Sockets * 6 Kerne mit 2,5 GHz | 24 GB | 1 GB | Bis zu 2 TB pro Tag

Richten Sie den Prozessserver wie folgt ein:

1. Überprüfen Sie die [Voraussetzungen](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Installieren Sie den Server im [Portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui) oder über die [Befehlszeile](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line).
3. Konfigurieren Sie replizierte Computer zur Verwendung des neuen Servers. Wenn Sie bereits über Computer verfügen, die Replikation ausführen:
    - Sie können eine gesamte Prozessserverworkload auf den neuen Prozessserver [verschieben](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server).
    - Alternativ können Sie spezifische virtuelle Computer auf den neuen Prozessserver [verschieben](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).



## <a name="enable-large-scale-replication"></a>Aktivieren der umfangreichen Replikation

Nachdem Sie die Kapazität geplant und die erforderlichen Komponenten und die Infrastruktur bereitgestellt haben, aktivieren Sie die Replikation für eine große Anzahl von virtuellen Computern.

1. Sortieren Sie Computer in Batches. Sie aktivieren die Replikation für die virtuellen Computer in einem Batch und fahren dann mit dem nächsten Batch fort.

    - Für VMware-VMs können Sie die [empfohlene VM-Batchgröße](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) im Bericht des Bereitstellungsplaners verwenden.
    - Für physische Computer wird empfohlen, Batches auf Grundlage von Computern zu identifizieren, die über eine ähnliche Größe und Datenmenge verfügen, und auf Grundlage des verfügbaren Netzwerkdurchsatzes. Das Ziel dabei ist, Computer in einem Batch zu gruppieren, die ihre erste Replikation wahrscheinlich in ungefähr derselben Zeitspanne abschließen.
    
2. Wenn die Rate der Datenträgeränderungen für einen Computer hoch ist oder die Grenzwerte im Bereitstellungsplaner überschreitet, können Sie nicht kritische Dateien, die nicht repliziert werden müssen (z.B. Logdumps oder temporäre Dateien) vom Computer verschieben. Bei VMware-VMs können Sie diese Dateien auf einen separaten Datenträger verschieben. [Schließen Sie diesen Datenträger dann von der Replikation aus](vmware-azure-exclude-disk.md).
3. Bevor Sie die Replikation aktivieren, überprüfen Sie, ob die Computer die [Replikationsanforderungen](vmware-physical-azure-support-matrix.md#replicated-machines) erfüllen.
4. Konfigurieren Sie eine Replikationsrichtlinie für [VMware-VMs](vmware-azure-set-up-replication.md#create-a-policy) oder [physische Server](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Aktivieren Sie die Replikation für [VMware-VMs](vmware-azure-enable-replication.md) oder [physische Server](physical-azure-disaster-recovery.md#enable-replication). Damit wird die erste Replikation für die ausgewählten Computer gestartet.

## <a name="monitor-your-deployment"></a>Überwachen der Bereitstellung

Nachdem Sie die Replikation für den ersten Batch von virtuellen Computern gestartet haben, beginnen Sie wie folgt mit der Überwachung Ihrer Bereitstellung:  

1. Weisen Sie einen Administrator für die Notfallwiederherstellung zu, der den Integritätsstatus der replizierten Computer überwacht.
2. [Überwachen Sie Ereignisse](site-recovery-monitor-and-troubleshoot.md) für replizierte Elemente und die Infrastruktur.
3. [Überwachen Sie die Integrität](vmware-physical-azure-monitor-process-server.md) der Prozessserver für horizontales Skalieren.
4. Registrieren Sie sich für den Erhalt von [E-Mail-Benachrichtigungen](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) über Ereignisse, um die Überwachung zu erleichtern.
5. Führen Sie regelmäßige [Übungen zur Notfallwiederherstellung](site-recovery-test-failover-to-azure.md) durch, um sicherzustellen, dass alles wie erwartet funktioniert.


## <a name="plan-for-large-scale-failovers"></a>Planen umfangreicher Failover

Bei einem Notfall müssen Sie möglicherweise einen Failover für eine große Anzahl von Computern/Workloads zu Azure durchführen. Bereiten Sie derartige Ereignisse wie folgt vor.

Sie können sich wie folgt im Voraus für Failover vorbereiten:

- [Bereiten Sie Ihre Infrastruktur und die VMs vor](#plan-infrastructure-and-vm-connectivity), sodass Ihre Workloads nach dem Failover verfügbar sind und Ihre Benutzer auf die Azure-VMs zugreifen können.
- Beachten Sie die [Failover Grenzwerte](#failover-limits) weiter oben in diesem Dokument. Stellen Sie sicher, dass die Failover innerhalb dieser Grenzwerte liegen.
- Führen Sie regelmäßige [Übungen zur Notfallwiederherstellung](site-recovery-test-failover-to-azure.md) durch. Die Übungen helfen Ihnen bei Folgendem:
    - Ermitteln von Lücken in Ihrer Bereitstellung vor dem Failover
    - Schätzen des End-to-End-RTOs für Ihre Apps
    - Schätzen des End-to-End-RPOs für Ihre Workloads
    - Identifizieren von Konflikten mit dem IP-Adressbereich
    - Beim Ausführen von Übungen wird empfohlen, keine Produktionsnetzwerke sowie nicht dieselben Subnetznamen wie in den Produktions- und Testnetzwerken zu verwenden und die Testfailover nach jeder Übung zu bereinigen.

Für das Ausführen eines umfangreichen Failovers wird Folgendes empfohlen:

1. Erstellen Sie Wiederherstellungspläne für Workloadfailover.
    - Jeder Wiederherstellungsplan kann einen Failover von bis zu 50 Computern auslösen.
    - [hier](recovery-plan-overview.md) .
2. Fügen Sie Wiederherstellungsplänen Azure Automation-Runbookskripts hinzu, um alle manuellen Aufgaben in Azure zu automatisieren. Zu den typischen Aufgaben gehören die Konfiguration von Lastenausgleichsmodulen, das Aktualisieren von DNS usw. [Weitere Informationen](site-recovery-runbook-automation.md)
2. Bereiten Sie Windows-Computer vor dem Failover vor, sodass sie der Azure-Umgebung entsprechen. [Failover-Grenzwerte](#plan-azure-subscriptions-and-quotas) sind für Computer höher, die die Vorgaben einhalten. [Erfahren Sie mehr](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) über Runbooks.
4.  Lösen Sie einen Failover mit dem PowerShell-Cmdlet [Start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) zusammen mit einem Wiederherstellungsplan aus.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überwachen von Site Recovery](site-recovery-monitor-and-troubleshoot.md)
