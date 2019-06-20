---
title: Einrichten der Notfallwiederherstellung für Hyper-V-VMs zwischen lokalen Standorten mit Azure Site Recovery | Microsoft-Dokumentation
description: Informationen zum Einrichten der Notfallwiederherstellung für Hyper-V-VMs zwischen Ihren lokalen Standorten mit Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 067040349a5d435860492497dddbf39aad2635eb
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398405"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Einrichten der Notfallwiederherstellung für Hyper-V-VMs an einem sekundären lokalen Standort

Der Dienst [Azure Site Recovery](site-recovery-overview.md) unterstützt Ihre Strategie zur Notfallwiederherstellung, indem Replikation, Failover und Failback von lokalen Computern und virtuellen Azure-Computern (Virtual Machines, VMs) verwaltet und orchestriert werden.

Dieser Artikel veranschaulicht das Einrichten der Notfallwiederherstellung an einem sekundären Standort für lokale Hyper-V-VMs, die in System Center Virtual Machine Manager-Clouds (VMM) verwaltet werden. In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Vorbereiten lokaler VMM-Server und Hyper-V-Hosts
> * Erstellen eines Recovery Services-Tresors für Site Recovery 
> * Einrichten der Quell- und Zielumgebungen für die Replikation 
> * Einrichten der Netzwerkzuordnung 
> * Erstellen einer Replikationsrichtlinie
> * Aktivieren der Replikation für eine VM

## <a name="prerequisites"></a>Voraussetzungen

So führen Sie dieses Szenario durch

- Überprüfen Sie [Architektur und Komponenten des Szenarios](hyper-v-vmm-architecture.md).
- Stellen Sie sicher, dass VMM-Server und Hyper-V-Hosts den [Supportanforderungen](hyper-v-vmm-secondary-support-matrix.md) entsprechen.
- Überprüfen Sie, ob die virtuellen Computer, die Sie replizieren möchten, die Anforderungen für [Unterstützung replizierter Computer](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support) erfüllen.
- Bereiten Sie VMM-Server für die Netzwerkzuordnung vor.

### <a name="prepare-for-network-mapping"></a>Bereiten Sie sich auf die Netzwerkzuordnung vor

Die [Netzwerkzuordnung](hyper-v-vmm-network-mapping.md) ist die Zuordnung zwischen lokalen VMM-VM-Netzwerken in Quell- und Zielclouds. Die Zuordnung ermöglicht Folgendes:

- Nach dem Failover werden Verbindungen von VMs mit geeigneten Ziel-VM-Netzwerken hergestellt. 
- Replikat-VMs werden optimal auf Ziel-Hyper-V-Hostservern platziert. 
- Wenn Sie die Netzwerkzuordnung nicht konfigurieren, werden nach einem Failover keine Verbindungen von Replikat-VMs mit einem VM-Netzwerk hergestellt.

Bereiten Sie VMM wie folgt vor:

1. Stellen Sie sicher, dass [logische VMM-Netzwerke](https://docs.microsoft.com/system-center/vmm/network-logical) auf den Quell- und Ziel-VMM-Servern vorhanden sind.
    - Das logische Netzwerk auf dem Quellserver sollte der Quellcloud zugeordnet sein, in der sich Hyper-V-Hosts befinden.
    - Das logische Netzwerk auf dem Zielserver sollte der Zielcloud zugeordnet werden.
1. Stellen Sie sicher, dass [VM-Netzwerke](https://docs.microsoft.com/system-center/vmm/network-virtual) auf den Quell- und Ziel-VMM-Servern vorhanden sind. VM-Netzwerke sollten an jedem Standort mit dem logischen Netzwerk verknüpft werden.
2. Stellen Sie die Verbindung der virtuellen Computer auf den Hyper-V-Quellhosts mit dem VM-Quellnetzwerk her. 


## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Wählen eines Schutzziels

Wählen Sie aus, was Sie replizieren möchten und wohin die Daten repliziert werden sollen.

1. Klicken Sie auf **Site Recovery** > **Schritt 1: Vorbereiten der Infrastruktur** > **Schutzziel**.
2. Wählen Sie **Auf Wiederherstellungsstandort** und anschließend **Yes, with Hyper-V** (Ja, mit Hyper-V) aus.
3. Wählen Sie **Ja** aus, um anzugeben, dass Sie die Hyper-V-Hosts mithilfe von VMM verwalten.
4. Wählen Sie **Ja** aus, falls Sie über einen sekundären VMM-Server verfügen. Wenn Sie die Replikation zwischen Clouds auf einem einzelnen VMM-Server bereitstellen, klicken Sie auf **Nein**. Klicken Sie dann auf **OK**.


## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

Installieren Sie den Azure Site Recovery-Anbieter auf den VMM-Servern, und ermitteln und registrieren Sie die Server im Tresor.

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Quelle**.
2. Klicken Sie unter **Quelle vorbereiten** auf **+ VMM**, um einen VMM-Server hinzuzufügen.
3. Überprüfen Sie unter **Server hinzufügen**, ob unter **Servertyp** die Option **System Center VMM-Server** angezeigt wird.
4. Laden Sie die Installationsdatei für den Azure Site Recovery-Anbieter herunter.
5. Laden Sie den Registrierungsschlüssel herunter. Sie benötigen ihn, wenn Sie den Anbieter installieren. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Quelle einrichten](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installieren Sie den Anbieter auf jedem VMM-Server. Sie müssen nicht explizit etwas auf Hyper-V-Hosts installieren.

### <a name="install-the-azure-site-recovery-provider"></a>Installieren des Azure Site Recovery-Anbieters

1. Führen Sie die Anbietersetupdatei auf jedem VMM-Server aus. Falls VMM in einem Cluster bereitgestellt wird, gehen Sie bei der erstmaligen Installation wie folgt vor:
    -  Installieren Sie den Anbieter auf einem aktiven Knoten, und schließen Sie die Installation ab, um den VMM-Server im Tresor zu registrieren.
    - Installieren Sie den Anbieter anschließend auf den anderen Knoten. Auf allen Clusterknoten sollte die gleiche Version des Anbieters ausgeführt werden.
2. Das Setup führt einige Voraussetzungsüberprüfungen durch und fordert die Berechtigung zum Beenden des VMM-Diensts an. Der VMM-Dienst wird nach Abschluss des Setups automatisch neu gestartet. Bei der Installation in einem VMM-Cluster werden Sie aufgefordert, die Clusterrolle zu beenden.
3. Unter **Microsoft Update** können Sie Updates aktivieren, damit Updates für den Anbieter gemäß Ihrer Microsoft Update-Richtlinie installiert werden.
4. Ändern Sie unter **Installation** den Speicherort für die Installation, oder akzeptieren Sie den Standardspeicherort, und klicken Sie auf **Installieren**.
5. Klicken Sie nach Abschluss der Installation auf **Registrieren**, um den Server im Tresor zu registrieren.

    ![Installationsspeicherort](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. Prüfen Sie unter **Tresorname**den Namen des Tresors, in dem der Server registriert wird. Klicken Sie auf **Weiter**.
7. Geben Sie unter **Proxyverbindung** an, wie der auf dem VMM-Server ausgeführte Anbieter eine Verbindung mit Azure herstellen soll.
   - Der Anbieter kann entweder eine direkte oder eine proxybasierte Internetverbindung herstellen. Geben Sie ggf. Proxyeinstellungen an.
   - Bei Verwendung eines Proxys wird automatisch ein ausführendes VMM-Konto (DRAProxyAccount) mit den angegebenen Proxyanmeldeinformationen erstellt. Konfigurieren Sie den Proxyserver so, dass dieses Konto erfolgreich authentifiziert werden kann. Die Einstellungen für ausführende Konten können über die VMM-Konsole unter **Einstellungen** > **Sicherheit** > **Ausführende Konten** geändert werden.
   - Starten Sie den VMM-Dienst neu, um die Änderungen zu übernehmen.
8. Wählen Sie unter **Registrierungsschlüssel** den Schlüssel aus, den Sie heruntergeladen und auf den VMM-Server kopiert haben.
9. Die Verschlüsselungseinstellung ist für dieses Szenario nicht relevant. 
10. Geben Sie unter **Servername**einen Anzeigenamen ein, um den VMM-Server im Tresor zu identifizieren. Geben Sie in einem Cluster den Rollennamen des VMM-Clusters an.
11. Wählen Sie unter **Cloudmetadaten synchronisieren** aus, ob Sie Metadaten für alle Clouds auf dem VMM-Server synchronisieren möchten. Diese Aktion muss für jeden VMM-Server nur einmal ausgeführt werden. Wenn Sie nicht alle Clouds synchronisieren möchten, lassen Sie diese Einstellung deaktiviert. Sie können jede Cloud einzeln in der VMM-Konsole in den Cloudeigenschaften synchronisieren.
12. Klicken Sie auf **Weiter** , um den Prozess abzuschließen. Nach der Registrierung werden die Metadaten von Azure Site Recovery vom VMM-Server abgerufen. Der Server wird im Tresor unter **Server** > **VMM-Server** angezeigt.
13. Sobald der Server im Tresor angezeigt wird, wählen Sie unter **Quelle** > **Quelle vorbereiten** den VMM-Server sowie die Cloud aus, in der sich der Hyper-V-Host befindet. Klicken Sie dann auf **OK**.


## <a name="set-up-the-target-environment"></a>Einrichten der Zielumgebung

Wählen Sie VMM-Zielserver und Cloud aus:

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**, und wählen Sie den VMM-Zielserver aus.
2. VMM-Clouds, die mit Site Recovery synchronisiert werden, werden angezeigt. Wählen Sie die Zielcloud aus.

   ![Ziel](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Einrichten einer Replikationsrichtlinie

Bevor Sie beginnen, stellen Sie sicher, dass alle Hosts, die die Richtlinie verwenden, über dasselbe Betriebssystem verfügen. Wenn auf Hosts unterschiedliche Versionen von Windows Server ausgeführt werden, benötigen Sie mehrere Replikationsrichtlinien.

1. Klicken Sie zum Erstellen einer neuen Replikationsrichtlinie auf **Infrastruktur vorbereiten** > **Replikationseinstellungen** >  **+Erstellen und zuordnen**.
2. Geben Sie unter **Richtlinie erstellen und zuordnen**einen Richtliniennamen an. Quell- und Zieltyp müssen jeweils **Hyper-V**lauten.
3. Wählen Sie unter **Hyper-V-Hostversion** aus, welches Betriebssystem auf dem Host ausgeführt wird.
4. Geben Sie unter **Authentifizierungstyp** und **Authentifizierungsport** an, wie der Datenverkehr zwischen dem primären und dem Hyper-V-Wiederherstellungs-Hostserver authentifiziert wird.
    - Wählen Sie **Zertifikat** aus, sofern Sie nicht über eine funktionsfähige Kerberos-Umgebung verfügen. Azure Site Recovery konfiguriert automatisch Zertifikate für die HTTPS-Authentifizierung. Sie müssen gar nichts manuell durchführen.
    - Standardmäßig werden die Ports 8083 und 8084 (für Zertifikate) in der Windows-Firewall auf den Hyper-V-Hostservern geöffnet.
    - Wenn Sie **Kerberos**auswählen, wird ein Kerberos-Ticket für die gegenseitige Authentifizierung der Hostserver verwendet. Kerberos ist nur für Hyper-V-Hostserver relevant, die unter Windows Server 2012 R2 oder höher laufen.
1. Geben Sie unter **Kopierhäufigkeit**an, wie oft Sie Deltadaten nach der ersten Replikation replizieren möchten (alle 30 Sekunden, nach 5 Minuten oder nach 15 Minuten).
2. Geben Sie unter **Aufbewahrungszeitraum des Wiederherstellungspunkts** die Größe des Aufbewahrungszeitfensters für die einzelnen Wiederherstellungspunkte in Stunden an. Replizierte Computer können innerhalb eines Zeitfensters an einem beliebigen Punkt wiederhergestellt werden.
3. Geben Sie unter **App-konsistente Momentaufnahmehäufigkeit** an, wie häufig (1 bis 12 Stunden) Wiederherstellungspunkte erstellt werden sollen, die anwendungskonsistente Momentaufnahmen enthalten. Hyper-V verwendet zwei Arten von Momentaufnahmen:
    - **Standardmomentaufnahme**: Bietet eine inkrementelle Momentaufnahme des gesamten virtuellen Computers.
    - **App-konsistente Momentaufnahme**: Erstellt eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb des virtuellen Computers. VSS (Volume Shadow Copy Service, Volumeschattenkopie-Dienst) stellt sicher, dass Apps zum Zeitpunkt der Momentaufnahme konsistent sind. Aktivieren anwendungskonsistenter Momentaufnahmen, wirkt sich auf Quell-VMs auf die App-Leistung aus. Legen Sie einen Wert fest, der geringer ist als die Anzahl der konfigurierten zusätzlichen Wiederherstellungspunkte.
4. Geben Sie unter **Datenübertragungskomprimierung** an, ob übertragene Replikationsdaten komprimiert werden sollen.
5. Wählen Sie **Replikat-VM löschen** aus, um anzugeben, dass die Replikat-VM gelöscht werden sollte, wenn Sie den Schutz für die Quell-VM deaktivieren. Wenn Sie diese Einstellung aktivieren, und Sie deaktivieren den Schutz für die Quell-VMs, wird der virtuelle Computer aus der Site Recovery-Konsole entfernt, die Site Recovery-Einstellungen für die VMM werden aus der VMM-Konsole entfernt, und das Replikat wird gelöscht.
6. Wenn Sie über das Netzwerk replizieren, geben Sie unter **Methode für anfängliche Replikation** an, ob die erste Replikation sofort gestartet werden soll oder ob Sie sie planen möchten. Um Netzwerkbandbreite zu sparen, sollte der Zeitraum außerhalb Ihrer Spitzenzeiten liegen. Klicken Sie dann auf **OK**.

     ![Replikationsrichtlinie](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Die neue Richtlinie wird automatisch der VMM-Cloud zugeordnet. Klicken Sie unter **Replikationsrichtlinie** auf **OK**. 


## <a name="enable-replication"></a>Aktivieren der Replikation

1. Klicken Sie auf **Replicate application (Anwendung replizieren)**  > **Quelle**. 
2. Wählen Sie auf dem Blatt **Quelle** den VMM-Server und die Cloud aus, in denen sich die Hyper-V-Hosts befinden, die Sie replizieren möchten. Klicken Sie dann auf **OK**.
3. Überprüfen Sie in **Ziel** den sekundären VMM-Server und die Cloud.
4. Wählen Sie unter **Virtuelle Computer** in der Liste die VMs aus, die Sie schützen möchten.


Sie können den Fortschritt der Aktion **Schutz aktivieren** unter **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nach der Ausführung des Auftrags **Schutz abschließen** ist die erste Replikation abgeschlossen und der virtuelle Computer bereit zum Failover.

## <a name="next-steps"></a>Nächste Schritte

[Durchführen eines Notfallwiederherstellungsverfahrens](hyper-v-vmm-test-failover.md)
