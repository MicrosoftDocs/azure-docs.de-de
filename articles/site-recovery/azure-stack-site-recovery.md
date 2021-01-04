---
title: Replizieren virtueller Azure Stack-Computer in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Notfallwiederherstellung in Azure für virtuelle Azure Stack-Computer mit dem Azure Site Recovery-Dienst einrichten.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 36e11bfe5354644f9ef6603ffe20cb2e86074323
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016902"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replizieren virtueller Azure Stack-Computer in Azure

In diesem Artikel erfahren Sie, wie Sie die Notfallwiederherstellung für virtuelle Azure Stack-Computer in Azure mit dem [Azure Site Recovery-Dienst](site-recovery-overview.md) einrichten.

Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR). Der Dienst stellt sicher, dass Ihre VM-Workloads bei erwarteten und unerwarteten Ausfällen weiterhin verfügbar sind.

- Site Recovery orchestriert und verwaltet die Replikation virtueller Computer in Azure Storage.
- Bei einem Ausfall am primären Standort verwenden Sie Site Recovery für ein Failover zu Azure.
- Bei einem Failover werden aus den gespeicherten VM-Daten virtuelle Azure-Computer erstellt, und Benutzer können weiterhin auf Workloads zugreifen, die auf diesen virtuellen Azure-Computern ausgeführt werden.
- Wenn alles wieder betriebsbereit ist, können Sie ein Failback der virtuellen Azure-Computer zum primären Standort durchführen und wieder mit der Replikation in Azure Storage beginnen.


In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * **Schritt 1: Vorbereiten virtueller Azure Stack-Computer für die Replikation**. Vergewissern Sie sich, dass die virtuellen Computer die Site Recovery-Anforderungen erfüllen, und bereiten Sie die Installation des Site Recovery-Mobilitätsdiensts vor. Dieser Dienst wird auf jedem virtuellen Computer installiert, den Sie replizieren möchten.
> * **Schritt 2: Einrichten eines Recovery Services-Tresors**. Richten Sie einen Tresor für Site Recovery ein, und geben Sie an, was Sie replizieren möchten. Site Recovery-Komponenten und -Aktionen werden im Tresor konfiguriert und verwaltet.
> * **Schritt 3: Einrichten der Quellreplikationsumgebung**. Richten Sie einen Site Recovery-Konfigurationsserver ein. Der Konfigurationsserver ist ein einzelner virtueller Azure Stack-Computer, auf dem alle von Site Recovery benötigten Komponenten ausgeführt werden. Nachdem Sie den Konfigurationsserver eingerichtet haben, registrieren Sie ihn im Tresor.
> * **Schritt 4: Einrichten der Replikationszielumgebung**. Wählen Sie Ihr Azure-Konto sowie das zu verwendende Azure-Speicherkonto und -Netzwerk aus. Während der Replikation werden Daten der virtuellen Computer in Azure Storage kopiert. Nach dem Failover werden virtuelle Azure-Computer mit dem angegebenen Netzwerk verbunden.
> * **Schritt 5: Aktivieren der Replikation**. Konfigurieren Sie Replikationseinstellungen, und aktivieren Sie die Replikation für virtuelle Computer. Bei aktivierter Replikation wird der Mobilitätsdienst auf dem virtuellen Computer installiert. Site Recovery führt eine erste Replikation des virtuellen Computers aus, und anschließend beginnt die laufende Replikation.
> * **Schritt 6: Durchführen eines Notfallwiederherstellungsverfahrens**: Sobald die Replikation betriebsbereit ist, vergewissern Sie sich, dass das Failover erwartungsgemäß funktioniert. Dazu führen Sie ein Testverfahren durch. Zum Einleiten des Testverfahrens führen Sie ein Testfailover in Site Recovery aus. Das Testfailover hat keinerlei Auswirkungen auf Ihre Produktionsumgebung.

Wenn diese Schritte abgeschlossen sind, können Sie anschließend im Bedarfsfall ein vollständiges Failover zu Azure durchführen.

## <a name="architecture"></a>Aufbau

![Das Diagramm zeigt Recovery Services-Tresore für zwei Mandanten in Clouds, die Mandantenabonnements in einer gemeinsamen Azure Stack-Infrastruktur zugeordnet sind.](./media/azure-stack-site-recovery/architecture.png)

**Location** | **Komponente** |**Details**
--- | --- | ---
**Konfigurationsserver** | Wird auf einem einzelnen virtuellen Azure Stack-Computer ausgeführt. | In jedem Abonnement richten Sie einen virtuellen Computer mit dem Konfigurationsserver ein. Auf diesem virtuellen Computer werden die folgenden Site Recovery-Komponenten ausgeführt:<br/><br/> – Konfigurationsserver: Koordiniert die Kommunikation zwischen der lokalen Umgebung und Azure und verwaltet die Datenreplikation. – Prozessserver: Fungiert als Replikationsgateway. Er empfängt Replikationsdaten, optimiert sie durch Zwischenspeicherung, Komprimierung und Verschlüsselung und sendet sie an Azure Storage.<br/><br/> Wenn virtuelle Computer, die Sie replizieren möchten, die nachfolgend genannten Grenzwerte überschreiten, können Sie einen separaten eigenständigen Prozessserver einrichten. [Weitere Informationen](vmware-azure-set-up-process-server-scale.md)
**Mobilitätsdienst** | Auf jedem virtuellen Computer installiert, den Sie replizieren möchten. | In den Schritten in diesem Artikel wird ein Konto so vorbereitet, dass der Mobilitätsdienst bei aktivierter Replikation automatisch auf einem virtuellen Computer installiert wird. Wenn Sie den Dienst nicht automatisch installieren möchten, gibt es eine Reihe anderer Methoden, die Sie verwenden können. [Weitere Informationen](vmware-azure-install-mobility-service.md)
**Azure** | In Azure benötigen Sie einen Recovery Services-Tresor, ein Speicherkonto und ein virtuelles Netzwerk. |  Replizierte Daten werden im Speicherkonto gespeichert. Bei einem Failover werden virtuelle Azure-Computer zum Azure-Netzwerk hinzugefügt.


Die Replikation funktioniert wie folgt:

1. Im Tresor geben Sie die Quelle und das Ziel der Replikation an, richten den Konfigurationsserver ein, erstellen eine Replikationsrichtlinie und aktivieren die Replikation.
2. Der Mobilitätsdienst wird auf dem Computer installiert (sofern Sie Pushinstallation verwendet haben), und Computer beginnen mit der Replikation gemäß der Replikationsrichtlinie.
3. Eine erste Kopie der Serverdaten wird in Azure Storage repliziert.
4. Die Replikation von Deltaänderungen in Azure beginnt, nachdem die erste Replikation abgeschlossen wurde. Nachverfolgte Änderungen für einen Computer werden in einer HRL-Datei gespeichert.
5. Der Konfigurationsserver orchestriert die Replikationsverwaltung mit Azure (ausgehender Port HTTPS 443).
6. Der Prozessserver empfängt Daten von Quellcomputern, optimiert und verschlüsselt sie und sendet sie an Azure Storage (ausgehender Port 443).
7. Replizierte Computer kommunizieren mit dem Konfigurationsserver (eingehender Port HTTPS 443) zur Replikationsverwaltung. Computer senden Replikationsdaten an den Prozessserver (eingehender Port HTTPS 9443 – kann geändert werden).
8. Der Datenverkehr wird auf öffentlichen Endpunkten des Azure-Speichers über das Internet repliziert. Alternativ hierzu können Sie das öffentliche Peering von Azure ExpressRoute verwenden. Das Replizieren von Datenverkehr über ein Site-to-Site-VPN von einem lokalen Standort nach Azure wird nicht unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

Zum Einrichten dieses Szenarios benötigen Sie Folgendes:

**Anforderung** | **Details**
--- | ---
**Azure-Abonnementkonto** | Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.
**Azure-Kontoberechtigungen** | Das verwendete Azure-Konto benötigt Berechtigungen für Folgendes:<br/><br/> – Erstellen eines Recovery Service-Tresors<br/><br/> – Erstellen eines virtuellen Computers in der Ressourcengruppe und dem virtuellen Netzwerk, die Sie für das Szenario verwenden<br/><br/> – Schreiben in das angegebene Speicherkonto<br/><br/> Beachten Sie dabei Folgendes:<br/><br/> – Wenn Sie ein Konto erstellen, sind Sie der Administrator Ihres Abonnements und können alle Aktionen durchführen.<br/><br/> – Falls Sie ein vorhandenes Abonnement verwenden und nicht der Administrator sind, müssen Sie mit dem Administrator zusammenarbeiten, damit er Ihnen Berechtigungen vom Typ „Besitzer“ oder „Mitwirkender“ zuweist.<br/><br/> – Wenn Sie detailliertere Berechtigungen benötigen, lesen Sie [diesen Artikel](site-recovery-role-based-linked-access-control.md).
**Virtueller Azure Stack-Computer** | Sie benötigen einen virtuellen Azure Stack-Computer im Mandantenabonnement, der als Site Recovery-Konfigurationsserver bereitgestellt wird.


### <a name="prerequisites-for-the-configuration-server"></a>Voraussetzungen für den Konfigurationsserver

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Schritt 1: Vorbereiten virtueller Azure Stack-Computer

### <a name="verify-the-operating-system"></a>Überprüfen des Betriebssystems

Stellen Sie sicher, dass auf den virtuellen Computern eines der in der Tabelle genannten Betriebssysteme ausgeführt wird.


**Betriebssystem** | **Details**
--- | ---
**64-Bit-Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (ab SP1)
**CentOS** | 5.2 bis 5.11, 6.1 bis 6.9, 7.0 bis 7.3
**Ubuntu** | 14.04 LTS Server, 16.04 LTS Server. Überprüfen Sie [unterstützte Kernels](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions).

### <a name="prepare-for-mobility-service-installation"></a>Vorbereitung für die Installation des Mobilitätsdiensts

Auf jedem virtuellen Computer, den Sie replizieren möchten, muss der Mobilitätsdienst installiert sein. Damit der Prozessserver den Dienst bei aktivierter Replikation automatisch auf dem virtuellen Computer installiert, überprüfen Sie die Einstellungen für den virtuellen Computer.

#### <a name="windows-machines"></a>Windows-Computer

- Sie benötigen Netzwerkkonnektivität zwischen dem virtuellen Computer, auf dem Sie die Replikation aktivieren möchten, und dem Computer, auf dem der Prozessserver ausgeführt wird (standardmäßig ist dies der virtuelle Computer mit dem Konfigurationsserver).
- Sie benötigen ein Konto mit Administratorrechten (Domäne oder lokal) auf dem Computer, für den Sie die Replikation aktivieren.
    - Sie geben dieses Konto beim Einrichten von Site Recovery an. Anschließend verwendet der Prozessserver bei aktivierter Replikation dieses Konto zum Installieren des Mobilitätsdiensts.
    - Dieses Konto wird nur von Site Recovery für die Pushinstallation und zum Aktualisieren des Mobilitätsdiensts verwendet.
    - Wenn Sie kein Domänenkonto verwenden, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren:
        - Erstellen Sie in der Registrierung unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System“ den DWORD-Wert **LocalAccountTokenFilterPolicy**.
        - Legen Sie den Wert auf 1 fest.
        - Geben Sie dazu an der Eingabeaufforderung Folgendes ein: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- Lassen Sie in der Windows-Firewall auf dem virtuellen Computer, den Sie replizieren möchten, die Datei- und Druckerfreigabe sowie WMI zu.
    - Dazu führen Sie **wf.msc** aus, um die Windows-Firewall-Konsole zu öffnen. Klicken Sie mit der rechten Maustaste auf **Eingehende Regeln** > **Neue Regel**. Wählen Sie **Vordefiniert** und dann in der Liste **Datei- und Druckerfreigabe** aus. Schließen Sie den Assistenten ab, wählen Sie das Zulassen der Verbindung und dann **Fertig stellen** aus.
    - Bei Domänencomputern können Sie zu diesem Zweck ein Gruppenrichtlinienobjekt verwenden.


#### <a name="linux-machines"></a>Linux-Computer

- Stellen Sie sicher, dass zwischen dem Linux-Computer und dem Prozessserver eine Netzwerkkonnektivität besteht.
- Auf dem Computer, für den Sie die Replikation aktivieren, benötigen Sie ein Konto, das ein Root-Benutzer auf dem Linux-Quellserver ist:
    - Sie geben dieses Konto beim Einrichten von Site Recovery an. Anschließend verwendet der Prozessserver bei aktivierter Replikation dieses Konto zum Installieren des Mobilitätsdiensts.
    - Dieses Konto wird nur von Site Recovery für die Pushinstallation und zum Aktualisieren des Mobilitätsdiensts verwendet.
- Vergewissern Sie sich, dass die Datei „/etc/hosts“ auf dem Linux-Quellserver Einträge enthält, mit denen die Namen des lokalen Hosts den IP-Adressen zugeordnet werden, die allen Netzwerkkarten zugeordnet sind.
- Installieren Sie die neuesten openssh-, openssh-server- und openssl-Pakete auf dem Computer, den Sie replizieren möchten.
- Stellen Sie sicher, dass Secure Shell (SSH) auf Port 22 aktiviert ist und ausgeführt wird.
- Aktivieren Sie das SFTP-Subsystem und die Kennwortauthentifizierung in der sshd_config-Datei:
    1. Dazu melden Sie sich als Root-Benutzer an.
    2. Suchen Sie in der Datei „/etc/ssh/sshd_config“ nach der Zeile, die mit **PasswordAuthentication** beginnt. Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert in **yes**.
    3. Suchen Sie die Zeile, die mit **Subsystem** beginnt, und heben Sie die Auskommentierung der Zeile auf.

        ![Linux-Mobilitätsdienst](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Starten Sie den SSHD-Dienst neu.


### <a name="note-the-vm-private-ip-address"></a>Notieren der privaten IP-Adresse des virtuellen Computers

Die IP-Adresse für jeden zu replizierenden Computer finden Sie wie folgt:

1. Klicken Sie im Azure Stack-Portal auf den virtuellen Computer.
2. Klicken Sie im Menü **Ressource** auf **Netzwerkschnittstellen**.
3. Notieren Sie sich die private IP-Adresse.

    ![Private IP-Adresse](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Schritt 2: Erstellen eines Tresors und Auswählen eines Replikationsziels

1. Wählen Sie im Azure-Portal die Option **Ressource erstellen** > **Verwaltungstools** > **Backup und Site Recovery** aus.
2. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.
3. Erstellen Sie unter **Ressourcengruppe** eine Ressourcengruppe, oder wählen Sie eine Ressourcengruppe aus. Wir verwenden **ContosoRG**.
4. Geben Sie unter **Standort** die Azure-Region ein. verwenden wir **Europa, Westen**.
5. Wählen Sie **An Dashboard anheften** > **Erstellen**, um über das Dashboard schnell auf den Tresor zuzugreifen.

   ![Erstellen eines neuen Tresors](./media/azure-stack-site-recovery/new-vault-settings.png)

   Der neue Tresor wird unter **Dashboard** > **Alle Ressourcen** und auf der Hauptseite **Recovery Services-Tresore** angezeigt.

### <a name="select-a-replication-goal"></a>Auswählen eines Replikationsziels

1. Geben Sie unter **Recovery Services-Tresore** einen Tresornamen an. Wir verwenden **ContosoVMVault**.
2. Wählen Sie unter **Erste Schritte** die Option „Site Recovery“ aus. Klicken Sie anschließend auf **Infrastruktur vorbereiten**.
3. Wählen Sie in **Schutzziel** > **Wo befinden sich Ihre Computer?** die Option **Lokal** aus.
4. Wählen Sie in **Wohin möchten Sie Ihre Computer replizieren?** die Option **Nach Azure** aus.
5. Wählen Sie unter **Sind Ihre Computer virtualisiert?** die Option **Nicht virtualisiert/Andere** aus. Klicken Sie anschließend auf **OK**.

    ![Schutzziel](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Schritt 3: Einrichten der Quellumgebung

Richten Sie den Konfigurationsservercomputer ein, registrieren Sie ihn im Tresor, und ermitteln Sie Computer, die Sie replizieren möchten.

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Quelle**.
2. Klicken Sie in **Quelle vorbereiten** auf **+Konfigurationsserver**.

    ![Screenshot: Dialogfeld „+Konfigurationsserver“ mit der Meldung „Klicken Sie oben in der Befehlsleiste auf "+Konfigurationsserver", um einen Server in Ihrer Quellumgebung einzurichten und bei diesem Tresor zu registrieren.“](./media/azure-stack-site-recovery/plus-config-srv.png)

3. Überprüfen Sie unter **Server hinzufügen**, ob unter **Servertyp** die Option **Konfigurationsserver** angezeigt wird.
5. Laden Sie die Installationsdatei für das einheitliche Setup von Site Recovery herunter.
6. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen den Registrierungsschlüssel, wenn Sie das einheitliche Setup ausführen. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Screenshot: Dialogfeld „Server hinzufügen“, in dem „Servertyp“ auf „Konfigurationsserver“ festgelegt und die Schaltfläche „Tresorregistrierungsschlüssel herunterladen“ hervorgehoben ist](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Ausführen des einheitlichen Setups von Azure Site Recovery

Zum Installieren und Registrieren des Konfigurationsservers stellen Sie eine RDP-Verbindung mit dem virtuellen Computer her, den Sie für den Konfigurationsserver verwenden möchten, und führen Sie das einheitliche Setup aus.

Bevor Sie beginnen, stellen Sie sicher, dass die Uhr [mit einem Zeitserver auf dem virtuellen Computer synchronisiert ist](/windows-server/networking/windows-time-service/windows-time-service-top). Die Installation schlägt fehl, wenn die Zeit um mehr als fünf Minuten von der lokalen Zeit abweicht.

Nun installieren Sie den Konfigurationsserver:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Der Konfigurationsserver kann auch über die Befehlszeile installiert werden. [Weitere Informationen](physical-manage-configuration-server.md#install-from-the-command-line)
>
> Es kann 15 Minuten oder länger dauern, bis der Kontoname im Portal angezeigt wird. Klicken Sie zur sofortigen Aktualisierung auf **Konfigurationsserver** > **_Servername_ *_ > _* Server aktualisieren**.

## <a name="step-4-set-up-the-target-environment"></a>Schritt 4: Einrichten der Zielumgebung

Wählen Sie Zielressourcen aus, und überprüfen Sie sie.

1. Wählen Sie unter **Infrastruktur vorbereiten** > **Ziel** das gewünschte Azure-Abonnement aus.
2. Geben Sie das Ziel-Bereitstellungsmodell an.
3. Site Recovery prüft, ob Sie über ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke verfügen. Wenn diese nicht gefunden werden, müssen Sie mindestens ein Speicherkonto und ein virtuelles Netzwerk erstellen, um den Assistenten abzuschließen.


## <a name="step-5-enable-replication"></a>Schritt 5: Aktivieren der Replikation

### <a name="create-a-replication-policy"></a>Erstellen einer Replikationsrichtlinie

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Replikationseinstellungen**.
2. Geben Sie unter **Replikationsrichtlinie erstellen** einen Richtliniennamen an.
3. Geben Sie in **RPO-Schwellenwert** den RPO-Grenzwert (Recovery Point Objective) an.
    - Wiederherstellungspunkte für replizierte Daten werden gemäß der festgelegten Zeit erstellt.
    - Diese Einstellung hat keine Auswirkung auf die Replikation, die fortlaufend ist. Es wird einfach eine Warnung ausgegeben, wenn der Schwellenwert erreicht ist, ohne dass ein Wiederherstellungspunkt erstellt wurde.
4. Geben Sie unter **Aufbewahrungszeitraum des Wiederherstellungspunkts** an, wie lange die einzelnen Wiederherstellungspunkte aufbewahrt werden. Replizierte virtuelle Computer können für jeden Punkt im angegebenen Zeitfenster wiederhergestellt werden.
5. Geben Sie unter **App-konsistente Momentaufnahmehäufigkeit** an, wie häufig anwendungskonsistente Momentaufnahmen erstellt werden.

    - Eine App-konsistente Momentaufnahme ist eine Zeitpunkt-Momentaufnahme der Anwendungsdaten innerhalb des virtuellen Computers.
    - VSS (Volume Shadow Copy Service, Volumeschattenkopie-Dienst) stellt sicher, dass Apps auf dem virtuellen Computer zum Zeitpunkt der Momentaufnahme konsistent sind.
6. Wählen Sie **OK** aus, um die Richtlinie zu erstellen.


### <a name="confirm-deployment-planning"></a>Bestätigen der Bereitstellungsplanung

Sie können diesen Schritt zum jetzigen Zeitpunkt überspringen. Klicken Sie in der Dropdownliste **Bereitstellungsplanung** auf **Ja, ist abgeschlossen**.



### <a name="enable-replication"></a>Aktivieren der Replikation

Vergewissern Sie sich, dass Sie alle Aufgaben unter [Schritt 1: Vorbereiten des Computers](#step-1-prepare-azure-stack-vms) abgeschlossen haben. Dann aktivieren Sie die Replikation wie folgt:

1. Klicken Sie auf **Anwendung replizieren** > **Quelle**.
2. Wählen Sie unter **Quelle** den Konfigurationsserver aus.
3. Wählen Sie unter **Computertyp** die Option **Physische Computer** aus.
4. Wählen Sie den Prozessserver (Konfigurationsserver) aus. Klicken Sie dann auf **OK**.
5. Wählen Sie unter **Ziel** das Abonnement und die Ressourcengruppe aus, in dem bzw. der Sie die virtuellen Computer nach dem Failover erstellen möchten. Wählen Sie das Bereitstellungsmodell aus, das für die virtuellen Computer verwendet werden soll, für die ein Failover durchgeführt wurde.
6. Wählen Sie das Azure-Speicherkonto aus, in dem die replizierten Daten gespeichert werden sollen.
7. Wählen Sie das Azure-Netzwerk und das Subnetz aus, mit dem virtuelle Azure-Computer, die nach einem Failover erstellt werden, eine Verbindung herstellen sollen.
8. Wählen Sie die Option **Jetzt für die ausgewählten Computer konfigurieren** aus, um die Netzwerkeinstellung auf alle Computer anzuwenden, die geschützt werden sollen. Wählen Sie **Später konfigurieren** aus, wenn Sie das Azure-Netzwerk für jeden Computer separat auswählen möchten.
9. Klicken Sie unter **Physische Computer** auf **+Physischer Computer**. Geben Sie Name, IP-Adresse und BS-Typ der einzelnen Computer ein, die Sie replizieren möchten.

    - Verwenden Sie die interne IP-Adresse des Computers.
    - Wenn Sie die öffentliche IP-Adresse angeben, funktioniert die Replikation möglicherweise nicht wie erwartet.

10. Wählen Sie unter **Eigenschaften** > **Eigenschaften konfigurieren** das Konto aus, das der Prozessserver zum automatischen Installieren von Mobility Service auf dem Computer verwenden soll.
11. Überprüfen Sie unter **Replikationseinstellungen** > **Replikationseinstellungen konfigurieren**, ob die richtige Replikationsrichtlinie ausgewählt ist.
12. Klicken Sie auf **Replikation aktivieren**.
13. Verfolgen Sie den Fortschritt des Auftrags **Schutz aktivieren** unter **Einstellungen** > **Aufträge** > **Site Recovery-Aufträge**. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der Computer bereit für das Failover.

> [!NOTE]
> Site Recovery installiert Mobility Service, wenn die Replikation für einen virtuellen Computer aktiviert wird.
>
> Es kann länger als 15 Minuten dauern, bis die Änderungen wirksam und im Portal angezeigt werden.
>
> Überprüfen Sie zur Überwachung der hinzugefügten virtuellen Computer den Zeitpunkt der letzten Ermittlung für virtuelle Computer unter **Konfigurationsserver** > **Letzter Kontakt um**. Wenn Sie virtuelle Computer hinzufügen möchten, ohne auf die geplante Ermittlung zu warten, markieren Sie den Konfigurationsserver (wählen Sie ihn aber nicht aus), und klicken Sie auf **Aktualisieren**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Schritt 6: Durchführen eines Notfallwiederherstellungsverfahrens

Sie führen ein Testfailover zu Azure aus, um sicherzustellen, dass alles wie erwartet funktioniert. Dieses Failover hat keine Auswirkung auf Ihre Produktionsumgebung.

### <a name="verify-machine-properties"></a>Überprüfen von Computereigenschaften

Bevor Sie ein Testfailover ausführen, überprüfen Sie die Computereigenschaften, und stellen Sie sicher, dass sie die [Azure-Anforderungen](vmware-physical-azure-support-matrix.md#azure-vm-requirements) erfüllen. Sie können Eigenschaften wie folgt anzeigen und ändern:

1. Klicken Sie unter **Geschützte Elemente** auf **Replizierte Elemente** > VM.
2. Im Bereich **Repliziertes Element** finden Sie eine Zusammenfassung der Informationen zu virtuellen Computern, den Integritätsstatus sowie die neuesten verfügbaren Wiederherstellungspunkte. Klicken Sie auf **Eigenschaften**, um weitere Details anzuzeigen.
3. Unter **Compute und Netzwerk** ändern Sie Einstellungen nach Bedarf.

    - Sie können den Namen des virtuellen Azure-Computers, die Ressourcengruppe, Zielgröße, [Verfügbarkeitsgruppe](../virtual-machines/windows/tutorial-availability-sets.md) und Einstellungen verwalteter Datenträger ändern.
    - Sie können auch Netzwerkeinstellungen anzeigen und ändern. Dazu gehören das Netzwerk/Subnetz, mit dem der virtuelle Azure-Computer nach dem Failover verbunden wird, und die IP-Adresse, die dem virtuellen Computer zugewiesen wird.
1. Unter **Datenträger** finden Sie Informationen über das Betriebssystem und die Datenträger auf dem virtuellen Computer.


### <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

Beim Durchführen eines Testfailovers geschieht Folgendes:

1. Eine Überprüfung der erforderlichen Komponenten wird ausgeführt, um sicherzustellen, dass alle Bedingungen für ein Failover erfüllt sind.
2. Beim Failover werden die Daten gemäß dem angegebenen Wiederherstellungspunkt verarbeitet:
    - **Letzte Verarbeitung**: Das Failover des Computers wird auf den letzten Wiederherstellungspunkt ausgeführt, der von Site Recovery verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
    - **Letzter anwendungskonsistenter Zeitpunkt**: Das Failover des Computers wird auf den letzten app-konsistenten Wiederherstellungspunkt ausgeführt.
    - **Benutzerdefiniert**: Wählen Sie den für das Failover verwendeten Wiederherstellungspunkt aus.

3. Anhand der verarbeiteten Daten wird ein virtueller Azure-Computer erstellt.
4. Bei einem Testfailover können virtuelle Azure-Computer, die während des Testverfahrens erstellt wurden, automatisch bereinigt werden.

Führen Sie ein Testfailover für einen virtuellen Computer wie folgt aus:

1. Klicken Sie unter **Einstellungen** > **Replizierte Elemente** auf die VM > **+Testfailover**.
2. Bei dieser exemplarischen Vorgehensweise wird der Wiederherstellungspunkt **Letzte Verarbeitung** verwendet.
3. Wählen Sie unter **Testfailover** das Azure-Zielnetzwerk aus.
4. Klicken Sie auf **OK**, um den Failovervorgang zu starten.
5. Verfolgen Sie den Fortschritt, indem Sie auf den virtuellen Computer klicken, um die Eigenschaften zu öffnen. Alternativ klicken Sie auf den Auftrag **Testfailover** unter *Tresorname* > **Einstellungen** > **Aufträge** >**Site Recovery-Aufträge**.
6. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Prüfen Sie, ob der virtuelle Computer die richtige Größe hat, mit dem richtigen Netzwerk verbunden ist und ausgeführt wird.
7. Sie sollten nun eine Verbindung mit der replizierten VM in Azure herstellen können. [Weitere Informationen](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)
8. Klicken Sie zum Löschen der während des Testfailovers erstellten Azure-VMs auf **Testfailover bereinigen** für die VM. Speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover.

## <a name="fail-over-and-fail-back"></a>Failover und Failback

Nachdem Sie die Replikation eingerichtet und ein Testverfahren zur Sicherstellung der Funktionsfähigkeit ausgeführt haben, können Sie bei Bedarf ein Failover von Computern zu Azure durchführen.

Wenn Sie nach dem Failover eine Verbindung mit dem Computer in Azure herstellen möchten, führen Sie die Schritte zum [Vorbereiten der Verbindung](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) aus, bevor Sie ein Failover durchführen.

Dann führen Sie ein Failover wie folgt aus:


1. Klicken Sie in **Einstellungen** > **Replizierte Elemente** auf den Computer und dann auf **Failover**.
2. Wählen Sie den gewünschten Wiederherstellungspunkt aus.
3. Wählen Sie unter **Testfailover** das Azure-Zielnetzwerk aus.
4. Wählen Sie **Computer vor Beginn des Failovers herunterfahren** aus. Bei dieser Einstellung versucht Site Recovery, den Quellcomputer herunterzufahren, bevor das Failover gestartet wird. Das Failover wird jedoch auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich ist.
5. Klicken Sie auf **OK**, um den Failovervorgang zu starten. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
6. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt. Wenn Sie eine Verbindung nach dem Failover vorbereitet haben, prüfen Sie, ob der virtuelle Computer die richtige Größe hat, mit dem richtigen Netzwerk verbunden ist und ausgeführt wird.
7. Nachdem Sie den virtuellen Computer überprüft haben, klicken Sie auf **Commit**, um das Failover abzuschließen. Dadurch werden alle verfügbaren Wiederherstellungspunkte gelöscht.

> [!WARNING]
> Brechen Sie ein Failover in Bearbeitung nicht ab: Bevor das Failover gestartet wird, wird die VM-Replikation beendet. Wenn Sie ein Failover in Bearbeitung abbrechen, wird das Failover beendet, die Replikation der VM wird jedoch nicht erneut durchgeführt.


### <a name="fail-back-to-azure-stack"></a>Failback zu Azure Stack

Wenn der primäre Standort wieder betriebsbereit ist, können Sie ein Failback von Azure zu Azure Stack durchführen. Führen Sie dazu die [hier](/azure-stack/operator/site-recovery-failback?view=azs-2005) beschriebenen Schritte aus.

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden virtuelle Azure Stack-Computer in Azure repliziert. Nach dem Einrichten der Replikation wurde ein Notfallwiederherstellungsverfahren durchgeführt, um sicherzustellen, dass der Failover zu Azure erwartungsgemäß funktioniert. Der Artikel enthielt außerdem Schritte zum Ausführen eines vollständigen Failovers zu Azure und eines Failbacks zu Azure Stack.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Failback können Sie den virtuellen Computer erneut schützen und die Replikation in Azure wieder starten. Dazu wiederholen Sie die Schritte in diesem Artikel.