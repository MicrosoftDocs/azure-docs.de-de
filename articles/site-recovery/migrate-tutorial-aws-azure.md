---
title: Migrieren von virtuellen AWS-Computern zu Azure mit dem Azure Site Recovery-Dienst | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie unter Amazon Web Services (AWS) ausgeführte virtuelle Windows-Computer mithilfe von Azure Site Recovery zu Azure migrieren.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6d2b9c8dd8fb89e201cff5155b1dec0857204752
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66400048"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrieren von AWS-VMs (Amazon Web Services) zu Azure

In diesem Tutorial erfahren Sie, wie Sie virtuelle AWS-Computer (Amazon Web Services-VMs) mithilfe von Azure Site Recovery zu Azure-VMs migrieren. Beim Migrieren von AWS EC2-Instanzen zu Azure werden die VMs wie physische lokale Computer behandelt. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen der Voraussetzungen
> * Vorbereiten der Azure-Ressourcen
> * Vorbereiten der AWS EC2-Instanzen für die Migration
> * Bereitstellen eines Konfigurationsservers
> * Aktivieren der Replikation für VMs
> * Ausführen eines Testfailover aus, um die ordnungsgemäße Funktion sicherzustellen
> * Ausführen eines einmaligen Failovers in Azure

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
- Vergewissern Sie sich, dass auf den zu migrierenden virtuellen Computern eine unterstützte Betriebssystemversion ausgeführt wird. Unterstützte Versionen: 
  - Windows Server 2016 
  - Windows Server 2012 R2
  - Windows Server 2012 
  - 64-Bit-Version von Windows Server 2008 R2 SP1 oder höher
  - Red Hat Enterprise Linux 6.4 bis 6.10, 7.1 bis 7.6 (nur virtualisierte HVM-Instanzen)  *(Instanzen, auf denen RedHat PV-Treiber ausgeführt werden, werden nicht unterstützt.)*
  - CentOS 6.4 bis 6.10, 7.1 bis 7.6 (nur virtualisierte HVM-Instanzen)
 
- Der Mobilitätsdienst muss auf jeder VM installiert sein, die Sie replizieren möchten. 

    > [!IMPORTANT]
    > Site Recovery installiert diesen Dienst automatisch, wenn Sie die Replikation für die VM aktivieren. Für die automatische Installation müssen Sie ein Konto für die EC2-Instanzen vorbereiten, mit dem Site Recovery auf die VM zugreift. Sie können ein Domänenkonto oder ein lokales Konto verwenden. 
    > - Bei Linux-VMs muss auf dem Linux-Quellserver das root-Konto verwendet werden. 
    > - Wenn Sie kein Domänenkonto verwenden, deaktivieren Sie für Windows-VMs die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer:
    >
    >      Fügen Sie in der Registrierung unter **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** den DWORD-Eintrag **LocalAccountTokenFilterPolicy** hinzu, und legen Sie den Wert auf **1** fest.

- Eine separate EC2-Instanz, die Sie als Site Recovery-Konfigurationsserver verwenden können. Auf dieser Instanz muss Windows Server 2012 R2 ausgeführt werden.

## <a name="prepare-azure-resources"></a>Vorbereiten der Azure-Ressourcen

Sie müssen einige bestimmte Ressourcen in Azure vorbereitet haben, um die migrierten EC2-Instanzen verwenden zu können. Hierzu zählen ein Speicherkonto, ein Tresor und ein virtuelles Netzwerk.

### <a name="create-a-storage-account"></a>Speicherkonto erstellen

Images der replizierten Computer sind in Azure Storage gespeichert. Azure-VMs werden aus dem Speicher erstellt, wenn Sie ein Failover von einem lokalen Standort nach Azure ausführen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im Menü auf der linken Seite die Option **Ressource erstellen** > **Speicher** > **Speicherkonto**.
2. Geben Sie einen Namen für Ihr Speicherkonto ein. In diesen Tutorials verwenden wir den Namen **awsmigrated2017**. Für den Namen muss Folgendes gelten:
    - Er muss in Azure eindeutig sein.
    - Er muss zwischen 3 und 24 Zeichen lang sein.
    - Er darf nur Ziffern und Kleinbuchstaben enthalten.
3. Behalten Sie die Standardwerte für **Bereitstellungsmodell**, **Kontoart**, **Leistung** und **Sichere Übertragung erforderlich** bei.
5. Wählen Sie unter **Replikation** den **RA-GRS**-Standardwert aus.
6. Wählen Sie das Abonnement aus, das Sie für dieses Tutorial verwenden möchten.
7. Wählen Sie für **Ressourcengruppe** die Option **Neu erstellen** aus. In diesem Beispiel verwenden wir **migrationRG** als Ressourcengruppennamen.
8. Wählen Sie für **Ort** die Option **Europa, Westen** aus.
9. Wählen Sie **Erstellen**, um das Speicherkonto zu erstellen.

### <a name="create-a-vault"></a>Erstellen eines Tresors

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Alle Dienste**. Suchen Sie nach **Recovery Services-Tresoren**, und wählen Sie anschließend die Option aus.
2. Wählen Sie auf der Seite mit den Azure Recovery Services-Tresoren die Option **Hinzufügen**.
3. Geben Sie unter **Name** den Namen **myVault** ein.
4. Wählen Sie unter **Abonnement** das gewünschte Abonnement aus.
4. Wählen Sie für **Ressourcengruppe** die Option **Vorhandene verwenden** und dann **migrationRG** aus.
5. Wählen Sie für **Ort** die Option **Europa, Westen** aus.
5. Wählen Sie **An Dashboard anheften**, um über das Dashboard schnell auf den neuen Tresor zugreifen zu können.
7. Wählen Sie **Erstellen**, wenn Sie fertig sind.

Navigieren Sie zu **Dashboard** > **Alle Ressourcen**, um den neuen Tresor anzuzeigen. Der neue Tresor wird auch auf der Hauptseite **Recovery Services-Tresore** angezeigt.

### <a name="set-up-an-azure-network"></a>Richten Sie ein Azure-Netzwerk ein

Wenn die Azure-VMs nach der Migration (Failover) erstellt werden, werden sie mit diesem Azure-Netzwerk verknüpft.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Ressource erstellen** > **Netzwerk** >
   **Virtuelles Netzwerk**.
3. Geben Sie unter **Name** den Namen **myMigrationNetwork** ein.
4. Übernehmen Sie den Standardwert für **Adressbereich**.
5. Wählen Sie unter **Abonnement** das gewünschte Abonnement aus.
6. Wählen Sie für **Ressourcengruppe** die Option **Vorhandene verwenden** und dann **migrationRG** aus.
7. Wählen Sie für **Ort** die Option **Europa, Westen** aus.
8. Behalten Sie unter **Subnetz** die Standardwerte für **Name** and **IP-Bereich** bei.
9. Lassen Sie die Option **Dienstendpunkte** deaktiviert.
10. Wählen Sie **Erstellen**, wenn Sie fertig sind.

## <a name="prepare-the-infrastructure"></a>Vorbereiten der Infrastruktur

Wählen Sie im Azure-Portal auf Ihrer Tresorseite im Abschnitt **Erste Schritte** die Option **Site Recovery** und anschließend **Infrastruktur vorbereiten**. Führen Sie die folgenden Schritte aus.

### <a name="1-protection-goal"></a>1: Schutzziel

Wählen Sie auf der Seite **Schutzziel** die folgenden Werte aus:

|    |  |
|---------|-----------|
| Wo befinden sich die Computer? |Wählen Sie **Lokal**.|
| Wohin möchten Sie Ihre Computer replizieren? |Wählen Sie **To Azure** (In Azure).|
| Sind Ihre Computer virtualisiert? |Wählen Sie **Nicht virtualisiert/Andere**.|

Wählen Sie anschließend **OK**, um zum nächsten Abschnitt zu wechseln.

### <a name="2-select-deployment-planning"></a>2: Auswählen der Bereitstellungsplanung

Wählen Sie unter **Haben Sie die Bereitstellungsplanung abgeschlossen?** die Option **Wird später durchgeführt**, und wählen Sie dann **OK**.

### <a name="3-prepare-source"></a>3: Vorbereiten der Quelle

Wählen Sie auf der Seite **Quelle vorbereiten** die Option **+ Konfigurationsserver**.

1. Verwenden Sie eine EC2-Instanz, die unter Windows Server 2012 R2 ausgeführt wird, um einen Konfigurationsserver zu erstellen, und registrieren Sie ihn bei Ihrem Recovery-Tresor.
2. Konfigurieren Sie den Proxy auf der VM mit der EC2-Instanz, die Sie als Konfigurationsserver verwenden, damit der Zugriff auf die [Dienst-URLs](site-recovery-support-matrix-to-azure.md) möglich ist.
3. Laden Sie das [Microsoft Azure Site Recovery Unified Setup](https://aka.ms/unifiedinstaller_wus) (Einheitliches Setup von Microsoft Azure Site Recovery) herunter. Sie können es auf den lokalen Computer herunterladen und anschließend auf die VM kopieren, die Sie als Konfigurationsserver verwenden.
4. Wählen Sie die Schaltfläche **Herunterladen**, um den Schlüssel für die Tresorregistrierung herunterzuladen. Kopieren Sie die heruntergeladene Datei auf die VM, die Sie als Konfigurationsserver verwenden.
5. Klicken Sie auf der VM mit der rechten Maustaste auf das heruntergeladene Installationsprogramm für das einheitliche Setup von Microsoft Azure Site Recovery, und wählen Sie dann **Als Administrator ausführen**.

    1. Wählen Sie unter **Vorbereitung** die Option **Install the configuration server and process server** (Konfigurationsserver und Prozessserver installieren) und dann **Weiter**.
    2. Wählen Sie unter **Third-Party Software License** (Drittanbietersoftware-Lizenz) die Option **I accept the third-party license agreement** (Ich akzeptiere die Drittanbieter-Lizenzvereinbarung) und dann **Weiter**.
    3. Wählen Sie unter **Registrierung** die Option **Durchsuchen**, und navigieren Sie dann zu dem Ort, an dem Sie die Schlüsseldatei für die Tresorregistrierung abgelegt haben. Klicken Sie auf **Weiter**.
    4. Wählen Sie unter **Interneteinstellungen** die Option **Connect to Azure Site Recovery without a proxy server** (Ohne Proxyserver mit Azure Site Recovery verbinden) und dann **Weiter**.
    5. Auf der Seite **Prüfung der erforderlichen Komponenten** werden verschiedene Komponenten überprüft. Wählen Sie **Weiter**, wenn der Vorgang abgeschlossen ist.
    6. Geben Sie unter **MySQL Configuration** (MySQL-Konfiguration) die erforderlichen Kennwörter ein, und wählen Sie anschließend **Weiter**.
    7. Wählen Sie unter **Umgebungsdetails** die Option **Nein**. Es ist nicht erforderlich, VMware-Computer zu schützen. Klicken Sie anschließend auf **Weiter**.
    8. Wählen Sie unter **Installationspfad** die Option **Weiter**, um den Standardwert zu übernehmen.
    9. Wählen Sie unter **Netzwerkauswahl** die Option **Weiter**, um den Standardwert zu übernehmen.
    10. Wählen Sie unter **Zusammenfassung** die Option **Installieren**.
    11. Unter **Installationsfortschritt** werden Informationen zum Installationsvorgang angezeigt. Wählen Sie **Fertig stellen**, wenn der Vorgang abgeschlossen ist. In einem Fenster wird eine Meldung zu einem Neustart angezeigt. Klicken Sie auf **OK**. Als Nächstes wird in einem Fenster eine Meldung zur Passphrase für die Konfigurationsserververbindung angezeigt. Kopieren Sie die Passphrase in die Zwischenablage, und speichern Sie sie an einem sicheren Ort.
6. Führen Sie auf der VM die Datei „cspsconfigtool.exe“ aus, um mindestens ein Verwaltungskonto auf dem Konfigurationsserver zu erstellen. Vergewissern Sie sich, dass die Verwaltungskonten über Administratorrechte auf den EC2-Instanzen verfügen, die migriert werden sollen.

Wenn Sie mit dem Einrichten des Konfigurationsservers fertig sind, können Sie zurück zum Portal wechseln und den Server auswählen, den Sie als **Konfigurationsserver** erstellt haben. Klicken Sie auf **OK**, um fortzufahren mit 3: Vorbereiten des Ziels.

### <a name="4-prepare-target"></a>4: Vorbereiten des Ziels

In diesem Abschnitt geben Sie Informationen zu den Ressourcen ein, die Sie weiter oben in diesem Tutorial unter [Vorbereiten von Azure-Ressourcen](#prepare-azure-resources) erstellt haben.

1. Wählen Sie unter **Abonnement** das Azure-Abonnement aus, das Sie für das Tutorial [Vorbereiten von Azure](tutorial-prepare-azure.md) verwendet haben.
2. Wählen Sie als Bereitstellungsmodell die Option **Resource Manager** aus.
3. Site Recovery überprüft, ob Sie über ein oder mehrere kompatible Azure-Speicherkonten und -Netzwerke verfügen. Dies sollten die Ressourcen sein, die Sie weiter oben in diesem Tutorial unter [Vorbereiten der Azure-Ressourcen](#prepare-azure-resources) erstellt haben.
4. Wenn Sie fertig sind, wählen Sie **OK**.

### <a name="5-prepare-replication-settings"></a>5: Vorbereiten der Replikationseinstellungen

Bevor Sie die Replikation aktivieren können, müssen Sie eine Replikationsrichtlinie erstellen.

1. Wählen Sie **+ Replicate and Associate** (+ Replizieren und zuordnen).
2. Geben Sie unter **Name** den Namen **myReplicationPolicy** ein.
3. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie anschließend **OK**, um die Richtlinie zu erstellen. Die neue Richtlinie wird dem Konfigurationsserver automatisch zugeordnet.

Wenn Sie alle fünf Abschnitte unter **Infrastruktur vorbereiten** abgeschlossen haben, können Sie **OK** wählen.

## <a name="enable-replication"></a>Aktivieren der Replikation

Aktivieren Sie die Replikation für jede VM, die migriert werden soll. Wenn die Replikation aktiviert ist, wird der Mobilitätsdienst von Site Recovery automatisch installiert.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Wählen Sie auf der Seite für Ihren Tresor unter **Erste Schritte** die Option **Site Recovery**.
2. Wählen Sie unter **Für lokale Computer und Azure-VMs** die Option **Schritt 1: Replizieren der Anwendung** aus. Geben Sie auf den Seiten des Assistenten die folgenden Informationen an. Wählen Sie auf einer Seite jeweils **OK**, wenn Sie fertig sind:
   - 1: Konfigurieren der Quelle

     |  |  |
     |-----|-----|
     | Quelle: | Wählen Sie **Lokal**.|
     | Quellpfad:| Geben Sie den Namen Ihrer Konfigurationsserver-EC2-Instanz ein.|
     |Computertyp: | Wählen Sie **Physische Computer**.|
     | Prozessserver: | Wählen Sie den Konfigurationsserver in der Dropdownliste aus.|

   - 2: Konfigurieren des Ziels

     |  |  |
     |-----|-----|
     | Ziel: | Übernehmen Sie den Standardwert.|
     | Abonnement: | Wählen Sie das Abonnement aus, das Sie verwendet haben.|
     | Ressourcengruppe nach Failover:| Verwenden Sie die Ressourcengruppe, die Sie unter [Vorbereiten der Azure-Ressourcen](#prepare-azure-resources) erstellt haben.|
     | Bereitstellungsmodell nach dem Failover: | Wählen Sie **Ressourcen-Manager**.|
     | Speicherkonto: | Wählen Sie das Speicherkonto aus, das Sie unter [Vorbereiten der Azure-Ressourcen](#prepare-azure-resources) erstellt haben.|
     | Azure-Netzwerk: | Wählen Sie **Jetzt für die ausgewählten Computer konfigurieren**.|
     | Azure-Netzwerk nach Failover: | Wählen Sie das Netzwerk aus, das Sie unter [Vorbereiten der Azure-Ressourcen](#prepare-azure-resources) erstellt haben.|
     | Subnetz: | Wählen Sie in der Dropdownliste die Option **Standard**.|

   - 3: Auswählen von physischen Computern

     Wählen Sie **Physischer Computer**, und geben Sie dann die Werte für **Name**, **IP-Adresse** und **Betriebssystemtyp** der EC2-Instanz ein, die Sie migrieren möchten. Klicken Sie auf **OK**.

   - 4: Konfigurieren von Eigenschaften

     Wählen Sie das Konto aus, das Sie auf dem Konfigurationsserver erstellt haben, und wählen Sie anschließend **OK**.

   - 5: Konfigurieren der Replikationseinstellungen

     Vergewissern Sie sich, dass in der Dropdownliste die Replikationsrichtlinie **myReplicationPolicy** ausgewählt ist, und wählen Sie anschließend **OK**.

3. Wählen Sie **Replikation aktivieren**, wenn der Assistent abgeschlossen ist.

Navigieren Sie zu **Überwachung und Berichte** > **Aufträge** > **Site Recovery-Aufträge**, um den Status des Auftrags **Schutz aktivieren** zu verfolgen. Nachdem der Auftrag **Schutz abschließen** ausgeführt wurde, ist der Computer bereit für das Failover.        

Wenn Sie die Replikation für eine VM aktivieren, kann es länger als 15 Minuten dauern, bis die Änderungen wirksam werden und im Portal sichtbar sind.

## <a name="run-a-test-failover"></a>Ausführen eines Testfailovers

Beim Durchführen eines Testfailovers treten die folgenden Ereignisse auf:

- Eine Überprüfung der erforderlichen Komponenten wird ausgeführt, um sicherzustellen, dass alle Bedingungen für ein Failover erfüllt sind.
- Durch das Failover werden die Daten verarbeitet, sodass eine Azure-VM erstellt werden kann. Wenn Sie den letzten Wiederherstellungspunkt auswählen, wird ein Wiederherstellungspunkt auf der Grundlage der Daten erstellt.
- Eine Azure-VM wird anhand der im vorherigen Schritt verarbeiteten Daten erstellt.

Führen Sie das Testfailover im Portal aus:

1. Wechseln Sie auf der Seite für Ihren Tresor zu **Geschützte Elemente** > **Replizierte Elemente**. Wählen Sie die VM aus, und wählen Sie anschließend die Option **Testfailover**.
2. Wählen Sie einen Wiederherstellungspunkt für das Failover aus:
    - **Letzte Verarbeitung**: Führt ein Failover der VM auf den letzten Wiederherstellungspunkt aus, der von Site Recovery verarbeitet wurde. Der Zeitstempel wird angezeigt. Mit dieser Option wird keine Zeit für die Verarbeitung von Daten aufgewendet, und der RTO-Wert (Recovery Time Objective) wird niedrig gehalten.
    - **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover aller virtuellen Computer auf den letzten App-konsistenten Wiederherstellungspunkt aus. Der Zeitstempel wird angezeigt.
    - **Benutzerdefiniert**: Wählen Sie einen beliebigen Wiederherstellungspunkt aus.

3. Wählen Sie unter **Testfailover** das Azure-Zielnetzwerk aus, mit dem Azure-VMs nach dem Failover verbunden werden. Dies sollte das Netzwerk sein, das Sie unter [Vorbereiten der Azure-Ressourcen](#prepare-azure-resources) erstellt haben.
4. Wählen Sie **OK**, um den Failovervorgang zu starten. Wählen Sie zum Überwachen des Status die VM aus, um ihre Eigenschaften anzuzeigen. Sie können auch auf der Seite für Ihren Tresor den Auftrag **Testfailover** auswählen. Wählen Sie hierzu **Überwachung und Berichte** > **Aufträge** >  **Site Recovery-Aufträge**.
5. Nach Abschluss des Failovers wird der virtuelle Azure-Replikatcomputer im Azure-Portal angezeigt. Wählen Sie zum Anzeigen der VM die Option **Virtuelle Computer**. Vergewissern Sie sich, dass die VM die richtige Größe hat, mit dem richtigen Netzwerk verbunden ist und ausgeführt wird.
6. Sie sollten nun eine Verbindung mit der replizierten VM in Azure herstellen können.
7. Wählen Sie zum Löschen der während des Testfailovers erstellten Azure-VMs im Wiederherstellungsplan die Option **Testfailover bereinigen**. Erfassen und speichern Sie unter **Notizen** alle Beobachtungen im Zusammenhang mit dem Testfailover.

In einigen Szenarien ist für das Failover eine zusätzliche Verarbeitung erforderlich. Die Verarbeitung dauert etwa 8 bis 10 Minuten.

## <a name="migrate-to-azure"></a>Migrieren zu Azure

Führen Sie ein tatsächliches Failover für die EC2-Instanzen durch, um sie zu Azure-VMs zu migrieren:

1. Wählen Sie unter **Geschützte Elemente** > **Replizierte Elemente** die AWS-Instanzen und dann die Option **Failover**.
2. Wählen Sie unter **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Wählen Sie den letzten Wiederherstellungspunkt aus, und starten Sie das Failover. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt.
1. Stellen Sie sicher, dass die VM unter **Replizierte Elemente** angezeigt wird.
2. Klicken Sie jeweils mit der rechten Maustaste auf eine VM, und wählen Sie dann die Option **Migration abschließen**. Die folgenden Schritte werden ausgeführt:

   - Dadurch wird der Migrationsvorgang abgeschlossen, die Replikation für die AWS-VM wird beendet, und die Site Recovery-Abrechnung für die VM wird eingestellt.
   - In diesem Schritt werden die Replikationsdaten entfernt. Die migrierten virtuellen Computer werden nicht gelöscht. 

     ![Abschließen der Migration](./media/migrate-tutorial-aws-azure/complete-migration.png)

> [!WARNING]
> *Brechen Sie ein Failover in Bearbeitung nicht ab*. Bevor das Failover gestartet wird, wird die VM-Replikation beendet. Wenn Sie ein in Bearbeitung befindliches Failover abbrechen, wird das Failover beendet, aber die Replikation der VM wird nicht erneut durchgeführt.  


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie AWS EC2-Instanzen zu Azure-VMs migrieren. Wenn Sie mehr über Azure-VMs erfahren möchten, fahren Sie mit den Tutorials für Windows-VMs fort.

> [!div class="nextstepaction"]
> [Azure-Tutorials zu virtuellen Windows-Computern](../virtual-machines/windows/tutorial-manage-vm.md)
