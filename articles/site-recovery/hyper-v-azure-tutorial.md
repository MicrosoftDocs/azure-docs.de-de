---
title: Einrichten der Notfallwiederherstellung für lokale Hyper-V-VMs (ohne VMM) in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Notfallwiederherstellung für lokale Hyper-V-VMs (ohne VMM) in Azure mit Azure Site Recovery einrichten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 9d45e0f0c759685f9d35285ee7718585d5961333
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399416"
---
# <a name="set-up-disaster-recovery-of-on-premises-hyper-v-vms-to-azure"></a>Einrichten der Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure

Der Dienst [Azure Site Recovery](site-recovery-overview.md) verwaltet und orchestriert die Replikation, das Failover und das Failback von lokalen Computern sowie Azure-VMs und unterstützt dadurch Ihre Strategie zur Notfallwiederherstellung.

Dies ist das dritte Tutorial in einer Reihe. Hierin erfahren Sie, wie Sie die Notfallwiederherstellung von lokalen Hyper-V-VMs in Azure einrichten. Dieses Tutorial betrifft Hyper-V-VMs, die nicht mit dem Microsoft System Center Virtual Machine Manager (VMM) verwaltet werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Wählen Sie die Replikationsquelle und das Replikationsziel aus.
> * Richten Sie die Quellreplikationsumgebung, einschließlich der lokalen Komponenten zur Sitewiederherstellung, und die Zielreplikationsumgebung ein.
> * Erstellen einer Replikationsrichtlinie
> * Aktivieren der Replikation für eine VM

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert. Sie verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade. Ausführliche Anweisungen finden Sie in den Artikeln im Abschnitt **Schrittanleitungen** der [Dokumentation zur Sitewiederherstellung](https://docs.microsoft.com/azure/site-recovery).

## <a name="before-you-begin"></a>Voraussetzungen

Dies ist das dritte Tutorial in einer Reihe. Es wird davon ausgegangen, dass Sie bereits die Aufgaben in den vorherigen Tutorials durchgearbeitet haben:

1. [Vorbereiten von Azure](tutorial-prepare-azure.md)
2. [Vorbereiten lokaler Hyper-V-Instanzen](tutorial-prepare-on-premises-hyper-v.md)

## <a name="select-a-replication-goal"></a>Auswählen eines Replikationsziels

1. Navigieren Sie im Azure-Portal zu **Recovery Services-Tresore**, und wählen Sie den Tresor aus. Im vorherigen Tutorial wurde der Tresor **ContosoVMVault** vorbereitet.
2. Wählen Sie in **Erste Schritte** zuerst **Sitewiederherstellung** und anschließend **Infrastruktur vorbereiten** aus.
3. Wählen Sie unter **Schutzziel** > **Wo befinden sich Ihre Computer?** die Option **Lokal** aus.
4. Wählen Sie unter **Wohin möchten Sie Ihre Computer replizieren?** die Option **Nach Azure** aus.
5. Wählen Sie unter **Sind Ihre Computer virtualisiert?** **Ja, mit Hyper-V** aus.
6. Wählen Sie unter **Are you using System Center VMM to manage your Hyper-V hosts?** (Verwenden Sie System Center VMM für die Verwaltung Ihrer Hyper-V-Hosts?) die Option **Nein** aus.
7. Klicken Sie auf **OK**.

    ![Replikationsziel](./media/hyper-v-azure-tutorial/replication-goal.png)

## <a name="confirm-deployment-planning"></a>Bestätigen der Bereitstellungsplanung

1. Falls Sie eine umfangreiche Bereitstellung planen, laden Sie unter **Bereitstellungsplanung** über den auf der Seite bereitgestellten Link den Bereitstellungsplaner für Hyper-V herunter. Weitere Informationen zur Hyper-V-Bereitstellungsplanung finden Sie [hier](hyper-v-deployment-planner-overview.md).
2. In diesem Tutorial wird der Bereitstellungsplaner nicht benötigt. Wählen Sie unter **Haben Sie die Bereitstellungsplanung abgeschlossen?** die Option **Wird später durchgeführt** und dann **OK** aus.

    ![Bereitstellungsplanung](./media/hyper-v-azure-tutorial/deployment-planning.png)

## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

Zum Einrichten der Quellumgebung erstellen Sie eine Hyper-V-Site und fügen dieser Site die Hyper-V-Hosts mit virtuellen Computern hinzu, die Sie dort replizieren möchten. Anschließend müssen Sie den Azure Site Recovery-Anbieter und den Azure Recovery Services-Agent herunterladen und auf den einzelnen Hosts installieren sowie die Hyper-V-Site im Tresor registrieren.

1. Wählen Sie unter **Infrastruktur vorbereiten** **Quelle** aus.
2. Wählen Sie unter **Quelle vorbereiten** **+Hyper-V-Site** aus.
3. Geben Sie unter **Hyper-V-Site erstellen** einen Namen für die Site an. Hier wird **ContosoHyperVSite** verwendet.

    ![Hyper-V-Standort](./media/hyper-v-azure-tutorial/hyperv-site.png)

4. Wählen Sie nach Erstellung der Site unter **Quelle vorbereiten** > **Schritt 1: Hyper-V-Site auswählen** die Site aus, die Sie erstellt haben.
5. Wählen Sie **+ Hyper-V-Server** aus.

    ![Hyper-V-Server](./media/hyper-v-azure-tutorial/hyperv-server.png)

6. Laden Sie das Installationsprogramm für den Microsoft Azure Site Recovery-Anbieter herunter.
7. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen diesen Schlüssel für die Anbieterinstallation. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Herunterladen von Anbieter- und Registrierungsschlüssel](./media/hyper-v-azure-tutorial/download.png)
    

### <a name="install-the-provider"></a>Installieren des Anbieters

Installieren Sie die heruntergeladene Setupdatei (AzureSiteRecoveryProvider.exe) auf jedem Hyper-V-Host, den Sie der Hyper-V-Site hinzufügen möchten. Setup installiert Azure Site Recovery-Anbieter und den Recovery Services-Agent auf jedem Hyper-V-Host.

1. Führen Sie die Setupdatei aus.
2. Wählen Sie unter Setup-Assistent für den Azure Site Recovery-Anbieter > **Microsoft Update** aus, um über Microsoft Update nach Anbieterupdates zu suchen.
3. Akzeptieren Sie unter **Installation** den standardmäßigen Installationsspeicherort für den Anbieter und den Agent, und wählen Sie **Installieren** aus.
4. Wählen Sie nach der Installation unter Microsoft Azure Site Recovery-Registrierungs-Assistent > **Tresoreinstellungen** **Durchsuchen** aus, und wählen Sie in **Schlüsseldatei** die Tresorschlüsseldatei aus, die Sie heruntergeladen haben.
5. Geben Sie das Azure Site Recovery-Abonnement, den Tresornamen (**ContosoVMVault**) und den Hyper-V-Standort (**ContosoHyperVSite**) an, zu dem der Hyper-V-Server gehört.
6. Wählen Sie unter **Proxyeinstellungen** die Option **Direkt mit Azure Site Recovery verbinden (ohne Proxyserver)** aus.
7. Wählen Sie nach der Registrierung des Servers im Tresor unter **Registrierung** **Fertig stellen** aus.

Azure Site Recovery ruft die Metadaten vom Hyper-V-Server ab, und der Server wird in **Site Recovery-Infrastruktur** > **Hyper-V-Hosts** angezeigt. Dieser Vorgang kann bis zu 30 Minuten dauern.

#### <a name="install-the-provider-on-a-hyper-v-core-server"></a>Installieren des Anbieters auf einem Hyper-V Core-Server

Wenn Sie einen Hyper-V Core Server betreiben, laden Sie die Setupdatei herunter, und führen Sie diese Schritte aus:

1. Extrahieren Sie die Dateien aus „AzureSiteRecoveryProvider.exe“ in einem lokalen Verzeichnis, indem Sie diesen Befehl ausführen:

    `AzureSiteRecoveryProvider.exe /x:. /q`
 
2. Führen Sie `.\setupdr.exe /i`aus. Die Ergebnisse werden in „%Programdata%\ASRLogs\DRASetupWizard.log“ protokolliert.

3. Registrieren Sie den Server mithilfe dieses Befehls:

    ```
    cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
    ```

## <a name="set-up-the-target-environment"></a>Einrichten der Zielumgebung

Wählen Sie Zielressourcen aus, und überprüfen Sie sie:

1. Klicken Sie auf **Infrastruktur vorbereiten** > **Ziel**.
2. Wählen Sie das Abonnement und die Ressourcengruppe **ContosoRG** aus, in denen Sie die Azure-VMs nach dem Failover erstellen möchten.
3. Wählen Sie das **Resource Manager**-Bereitstellungsmodell aus.

Site Recovery prüft, ob Sie über ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke verfügen.

## <a name="set-up-a-replication-policy"></a>Einrichten einer Replikationsrichtlinie

1. Wählen Sie **Infrastruktur vorbereiten** > **Replikationseinstellungen** >  **+Erstellen und zuordnen** aus.
2. Geben Sie unter **Richtlinie erstellen und zuordnen**einen Richtliniennamen an. Hier wird **ContosoReplicationPolicy** verwendet.
3. In diesem Tutorial behalten wir die Standardwerte bei:
    - **Kopierhäufigkeit** gibt an, wie oft Deltadaten nach der ersten Replikation repliziert werden. Die Standardhäufigkeit ist alle fünf Minuten.
    - **Aufbewahrungszeitraum des Wiederherstellungspunkts** gibt an, dass Wiederherstellungspunkte zwei Stunden lang aufbewahrt werden.
    - **App-konsistente Momentaufnahmehäufigkeit** gibt an, dass Wiederherstellungspunkte mit anwendungskonsistenten Momentaufnahmen jede Stunde erstellt werden sollen.
    - **Startzeit der ersten Replikation** gibt an, dass die erste Replikation sofort beginnen soll.
4. Nachdem die Richtlinie erstellt wurde, wählen Sie **OK** aus. Wenn Sie eine neue Richtlinie erstellen, wird sie der angegebenen Hyper-V-Site automatisch zugeordnet. In diesem Tutorial ist das **ContosoHyperVSite**.

    ![Replikationsrichtlinie](./media/hyper-v-azure-tutorial/replication-policy.png)

## <a name="enable-replication"></a>Aktivieren der Replikation

1. Wählen Sie unter **Anwendung replizieren** **Quelle** aus.
2. Wählen Sie unter **Quelle** den Standort **ContosoHyperVSite** aus. Wählen Sie anschließend **OK** aus.
3. Überprüfen Sie unter **Ziel** das Ziel (Azure), das Tresorabonnement und das Bereitstellungsmodell **Resource Manager**.
4. Wählen Sie bei Verwendung der Tutorialeinstellungen das Speicherkonto **contosovmsacct1910171607**, das im vorherigen Tutorial für replizierte Daten erstellt wurde. Wählen Sie außerdem das Netzwerk **ContosoASRnet** aus, in dem sich die Azure-VMs nach dem Failover befinden werden.
5. Wählen Sie unter **Virtuelle Computer** > **Auswählen** die VM aus, die Sie replizieren möchten. Wählen Sie anschließend **OK** aus.

   Sie können den Fortschritt der Aktion **Schutz aktivieren** unter **Aufträge** > **Site Recovery-Aufträge** verfolgen. Nach der Ausführung des Auftrags **Schutz abschließen** ist die erste Replikation abgeschlossen und der virtuelle Computer bereit zum Failover.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Durchführen eines Notfallwiederherstellungsverfahrens](tutorial-dr-drill-azure.md)
