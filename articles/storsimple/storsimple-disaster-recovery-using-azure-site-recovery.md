---
title: Automatisieren der StorSimple-Dateifreigabe-DR mit Azure Site Recovery | Microsoft-Dokumentation
description: Beschreibt die Schritte und bewährte Methoden zum Erstellen einer Lösung zur Notfallwiederherstellung für auf Microsoft Azure StorSimple-Speicher gehostete Dateifreigaben.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: 23049a2c-055e-4d0e-b8f5-af2a87ecf53f
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: alkohli
ms.openlocfilehash: e2d89718d953f05b3e5500db412ac8ac03bfa00b
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301947"
---
# <a name="automated-disaster-recovery-solution-using-azure-site-recovery-for-file-shares-hosted-on-storsimple"></a>Automatische Lösung zur Notfallwiederherstellung mithilfe von Azure Site Recovery für auf StorSimple gehostete Dateifreigaben

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Übersicht
Microsoft Azure StorSimple ist eine Hybridcloud-Speicherlösung, die sich mit der Komplexität von unstrukturierten Daten befasst, die bei Datenfreigabe häufig auftreten. StorSimple verwendet den Cloudspeicher als eine Erweiterung der lokalen Lösung und verteilt die Daten automatisch auf die Ebenen des lokalen Speichers und des Cloudspeichers. Durch den integrierten Datenschutz mit lokalen Momentaufnahmen und Cloudmomentaufnahmen wird keine ausgedehnte Speicherinfrastruktur mehr benötigt.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md) ist ein auf Azure basierender Dienst, der Funktionen für die Notfallwiederherstellung (Disaster Recovery; DR) bietet, indem die Replikation, das Failover und die Wiederherstellung virtueller Computer koordiniert wird. Azure Site Recovery unterstützt eine Reihe von Replikationstechnologien, um für virtuelle Computer und Anwendungen eine durchgängig die Replikation, den Schutz und ein reibungsloses Failover auf private/öffentliche Clouds bzw. auf gehostete Clouds zu bieten.

Mithilfe von Azure Site Recovery, der Replikation von virtuellen Computern und StorSimple-Funktionen für Cloudmomentaufnahmen können Sie die gesamte Dateiserverumgebung schützen. Im Falle einer Unterbrechung können Sie durch einen Mausklick Ihre Dateifreigaben in nur wenigen Minuten auf Azure online stellen.

In diesem Dokument wird ausführlich erläutert, wie Sie eine Lösung für die Notfallwiederherstellung für Ihre Dateifreigabe auf dem StorSimple-Speicher erstellen und ein geplantes Failover, ein ungeplantes Failover und ein Testfailover mithilfe eines mit einem Klick aktivierbaren Wiederherstellungsplans ausführen. Im Wesentlichen wird gezeigt, wie Sie den Wiederherstellungsplan in Ihrem Azure Site Recovery-Tresor ändern können, um StorSimple-Failover während Notfallszenarien zu aktivieren. Darüber hinaus werden unterstützte Konfigurationen und Voraussetzungen beschrieben. In diesem Dokument wird davon ausgegangen, dass Sie mit den Grundlagen der Azure Site Recovery- und StorSimple-Architekturen vertraut sind.

## <a name="supported-azure-site-recovery-deployment-options"></a>Unterstütze Azure Site Recovery-Bereitstellungsoptionen
Kunden können Dateiserver als physische Server oder virtuelle Computer (VMs) bereitstellen, die auf Hyper-V oder VMware laufen. Anschließend können Sie einen Teil des StoreSimple-Speichers abzweigen, um einen Bereich für den Dateiaustausch zu schaffen. Azure Site Recovery kann jeweils physische und virtuelle Bereitstellungen entweder auf einem sekundären Standort oder auf Azure schützen. Dieses Dokument behandelt die Details einer DR-Lösung mit Azure als Wiederherstellungsstandort für eine Dateiserver-VM, die auf Hyper-V-VM und mit Dateifreigaben auf dem StorSimple-Speicher gehostet wird. Andere Szenarien, in denen sich die Dateiserver-VM auf einer VMware-VM oder einem physischen Computer befindet, können auf ähnliche Weise implementiert werden.

## <a name="prerequisites"></a>Voraussetzungen
Für das Implementieren einer Lösung zur Notfallwiederherstellung, die Azure Site Recovery für auf dem StorSimple-Speicher gehostete Dateifreigaben verwendet, gelten die folgenden Voraussetzungen:

   - Eine lokale Windows Server 2012 R2-Dateiserver-VM, die auf Hyper-V oder VMware oder einem physischen Computer gehostet wird
   - Ein lokales StorSimple-Speichergerät, das bei Azure StorSimple Manager registriert ist
   - StorSimple Cloud Appliance-Version, die im Azure StorSimple Manager erstellt wurde Die Anwendung kann in einem heruntergefahrenen Zustand beibehalten werden.
   - Dateifreigaben, die auf den vom StorSimple-Speichergerät konfigurierten Volumes gehostet werden.
   - [Ein Azure Site Recovery Services-Tresor](/azure/site-recovery/hyper-v-vmm-azure-tutorial) , der in einem Microsoft Azure-Abonnement erstellt wurde.

Falls Azure Ihr Wiederherstellungsstandort ist, führen Sie zusätzlich das [Bereitschaftsbewertungstool für virtuelle Computer](https://azure.microsoft.com/downloads/vm-readiness-assessment/) auf VMs aus, um sicherzustellen, dass diese mit Azure VMs und Azure Site Recovery Services kompatibel sind.

Stellen Sie sicher, dass Sie Ihre StorSimple Cloud Appliance, Ihr Automation-Konto und Ihr Speicherkonto/ Ihre Speicherkonten in der gleichen Region erstellt haben, um Latenzprobleme zu vermeiden (die möglicherweise zu höheren Kosten führen).

## <a name="enable-dr-for-storsimple-file-shares"></a>Aktivieren von DR für StorSimple-Dateifreigaben
Jede Komponente der lokalen Umgebung muss geschützt werden, um die vollständige Replikation und Wiederherstellung zu aktivieren. In diesem Abschnitt werden folgende Themen beschrieben:
    
   - Einrichten der Replikation von Active Directory und DNS (optional)
   - Verwenden von Azure Site Recovery zum Aktivieren des Schutzes der Dateiserver-VM
   - Aktivieren des Schutzes von StorSimple-Volumes
   - Konfigurieren des Netzwerks

### <a name="set-up-active-directory-and-dns-replication-optional"></a>Einrichten der Replikation von Active Directory und DNS (optional)
Wenn Sie die Computer, auf denen Active Directory und DNS ausgeführt wird, so schützen möchten, dass diese auf dem DR-Standort verfügbar sind, müssen Sie sie explizit schützen (sodass auf die Dateiserver nach dem Failover mit der entsprechenden Authentifizierung zugegriffen werden kann). Basierend auf der Komplexität der lokalen Umgebung des Kunden gibt es zwei empfohlene Optionen.

#### <a name="option-1"></a>Option 1:
Falls der Kunde über eine kleine Anzahl von Anwendungen und über einen einzelnen Domänencontroller für die gesamte lokale Umgebung verfügt, und ein Failover für den gesamten Standorts ausführt, empfehlen wir die Verwendung der Azure Site Recovery-Replikation zum Replizieren des Computers mit dem Domänencontroller an einen sekundären Standort (gilt sowohl für die Standort-zu-Standort- als auch für die Standort-zu-Azure-Replikation).

#### <a name="option-2"></a>Option 2:
Wenn der Kunde über eine große Anzahl von Anwendungen verfügt und eine Active Directory-Gesamtstruktur ausführt, und ein Failover für einige Anwendungen gleichzeitig ausführt, empfehlen wir die Einrichtung eines zusätzlichen Domänencontrollers am DR-Standort (entweder ein sekundärer Standort oder in Azure).

Weitere Anleitungen zur Bereitstellung eines Domänencontrollers am DR-Standort finden Sie unter [Schützen von Active Directory und DNS mit Azure Site Recovery](../site-recovery/site-recovery-active-directory.md). Für den Rest dieses Dokuments wird davon ausgegangen, dass ein Domänencontroller am DR-Standort verfügbar ist.

### <a name="use-azure-site-recovery-to-enable-protection-of-the-file-server-vm"></a>Verwenden von Azure Site Recovery zum Aktivieren des Schutzes der Dateiserver-VM
Dieser Schritt erfordert, dass Sie die lokale Dateiserverumgebung vorbereiten, einen Azure Site Recovery-Tresor erstellen und vorbereiten und den Dateischutz des virtuellen Computers aktivieren.

#### <a name="to-prepare-the-on-premises-file-server-environment"></a>So bereiten Sie die lokale Dateiserverumgebung vor
1. Legen Sie die **Benutzerkontensteuerung** auf **Nie benachrichtigen** fest. Dies ist erforderlich, damit Sie Azure-Automatisierungsskripts zum Verbinden der iSCSI-Ziele nach dem Failover durch Azure Site Recovery verwenden können.
   
   1. Drücken Sie die Windows-Taste „+Q“, und suchen Sie nach **UAC**.  
   1. Wählen Sie **Einstellungen der Benutzerkontensteuerung ändern** aus.  
   1. Ziehen Sie den Balken nach unten zu **Nie benachrichtigen**.  
   1. Klicken Sie auf **OK**, und wählen Sie dann **Ja** aus, wenn Sie dazu aufgefordert werden.  
   
      ![Einstellungen der Benutzerkontensteuerung](./media/storsimple-disaster-recovery-using-azure-site-recovery/image1.png) 

1. Installieren Sie den VM-Agent auf jedem der Dateiserver-VMs. Dies ist erforderlich, damit Sie Azure-Automatisierungsskripte auf VMs ausführen können, für die ein Failover ausgeführt wurde.
   
   1. Geben Sie beim [Herunterladen des Agent](https://aka.ms/vmagentwin) als Ziel `C:\\Users\\<username>\\Downloads` an.
   1. Öffnen Sie Windows PowerShell im Administratormodus (als Administrator ausführen), und geben Sie anschließend den folgenden Befehl ein, um zum Downloadpfad zu navigieren:  
         `cd C:\\Users\\<username>\\Downloads\\WindowsAzureVmAgent.2.6.1198.718.rd\_art\_stable.150415-1739.fre.msi`
         
         > [!NOTE]
         > Der Dateiname kann sich je nach Version ändern.
      
1. Klicken Sie auf **Weiter**.
1. Akzeptieren Sie die **Nutzungsbedingungen**, und klicken Sie anschließend auf **Weiter**.
1. Klicken Sie auf **Fertig stellen**.
1. Erstellen Sie Dateifreigaben mithilfe von Volumes, die aus dem StorSimple-Speicher stammen. Weitere Informationen finden Sie unter [Verwalten von Volumes mithilfe des StorSimple Manager-Diensts](./index.yml).
   
   1. Klicken Sie auf Ihren lokalen VMs die Windows-Taste „+Q“, und suchen Sie nach **iSCSI**.
   1. Wählen Sie den **iSCSI-Initiator** aus.
   1. Wählen Sie die Registerkarte **Konfiguration** aus, und kopieren Sie den Initiatornamen.
   1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.
   1. Wählen Sie die Registerkarte **StorSimple** aus, und wählen Sie anschließend den StorSimple Manager-Dienst aus, der das physische Gerät enthält.
   1. Erstellen Sie einen oder mehrere Volumecontainer, und erstellen Sie Volume(s). (Diese Volumes dienen der Dateifreigabe(n) auf den Dateiserver-VMs). Kopieren Sie den Initiatornamen, und vergeben Sie einen passenden Namen für die Access Control-Datensätze, wenn Sie die Volumes erstellen.
   1. Wählen Sie die Registerkarte **Konfigurieren** aus, und notieren Sie sich die IP-Adresse des Geräts.
   1. Wechseln Sie auf Ihren lokalen VMs erneut zum **iSCSI-Initiator** , und geben Sie die IP-Adresse im Abschnitt „Schnellverbindung“ ein. Klicken Sie auf **Schnellverbindung** (das Gerät sollte jetzt verbunden werden).
   1. Öffnen Sie das Azure-Portal, und wählen Sie die Registerkarte **Volumes und Geräte** aus. Klicken Sie auf **Automatische Konfiguration**. Das Volume, das Sie erstellt haben, sollte angezeigt werden.
   1. Wählen Sie im Portal die Registerkarte **Geräte** aus, und wählen Sie anschließend **Create a New Virtual Device** (Erstellen eines neuen virtuellen Geräts) aus. (Dieses virtuelle Gerät wird verwendet, wenn ein Failover auftritt). Dieses neue virtuelle Gerät kann im Offlinestatus beibehalten werden, um zusätzliche Kosten zu vermeiden. Wechseln Sie im Portal zum Abschnitt **Virtual Machines**, und beenden Sie das virtuelle Gerät, um es offline zu schalten.
   1. Wechseln Sie zurück zu den lokalen VMs, und öffnen Sie Datenträgerverwaltung (drücken Sie die Windows-Taste „+ X“, und wählen Sie **Datenträgerverwaltung**).
   1. Sie sehen einige zusätzliche Datenträger (abhängig von der Anzahl von Volumes, die Sie erstellt haben). Klicken Sie mit der rechten Maustaste auf den ersten Datenträger, wählen Sie **Datenträger initialisieren** aus und anschließend **OK**. Klicken Sie mit der rechten Maustaste auf den Abschnitt **Nicht zugeordnet**, wählen Sie **Neues einfaches Volumen** aus, weisen Sie ihm einen Laufwerkbuchstaben zu, und beenden Sie den Assistenten.
   1. Wiederholen Sie Schritt 1 für alle Datenträger. Sie sehen jetzt alle Datenträger auf **diesem PC** im Windows-Explorer.
   1. Verwenden Sie die Rolle „Datei- und Speicherdienste“, um Dateifreigaben auf diesen Volumes zu erstellen.

#### <a name="to-create-and-prepare-an-azure-site-recovery-vault"></a>So erstellen Sie einen Azure Site Recovery-Tresor und bereiten diesen vor
Sehen Sie sich die [Azure Site Recovery-Dokumentation](/azure/site-recovery/) an, um die ersten Schritte mit Azure Site Recovery auszuführen, bevor Sie die Dateiserver-VM schützen.

#### <a name="to-enable-protection"></a>So aktivieren Sie den Schutz
1. Trennen Sie das iSCSI-Ziel/die iSCSI-Ziele von den lokalen VMs, die Sie durch Azure Site Recovery schützen möchten:
   
   1. Drücken Sie die Windows-Taste „+Q“, und suchen Sie nach **iSCSI**.
   1. Wählen Sie **iSCSI-Initiator einrichten** aus.
   1. Trennen Sie das StorSimple-Gerät,für das Sie zuvor eine Verbindung hergestellt haben. Alternativ können Sie während der Aktivierung des Schutzes den Dateiserver für ein paar Minuten abschalten.
      
   > [!NOTE]
   > Dies bewirkt, dass die Dateifreigaben vorübergehend nicht verfügbar sind.
   
1. [Aktivieren Sie den Schutz für virtuelle Computer](/azure/site-recovery/hyper-v-azure-tutorial) der Dateiserver-VM im Azure Site Recovery-Portal.
1. Wenn die erste Synchronisierung beginnt, können Sie die Verbindung mit dem Ziel wieder herstellen. Wechseln Sie zum iSCSI-Initiator, wählen Sie das StorSimple-Gerät, und klicken Sie auf **Verbinden**.
1. Wenn die Synchronisierung abgeschlossen und der Status der VM **Geschützt** ist, wählen Sie die VM und die Registerkarte **Konfigurieren** aus, und aktualisieren Sie entsprechend das Netzwerk der VM (die VM[s], für die ein Failover ausgeführt wurde, werden Teil dieses Netzwerks). Falls das Netzwerk nicht angezeigt wird, bedeutet dies, dass die Synchronisierung immer noch ausgeführt wird.

### <a name="enable-protection-of-storsimple-volumes"></a>Aktivieren des Schutzes von StorSimple-Volumes
Wenn Sie die Option **Standardsicherung für dieses Volume aktivieren** für die StorSimple-Volumes nicht ausgewählt haben, gehen Sie im StorSimple Manager-Dienst unter **Sicherungsrichtlinien**, und erstellen Sie eine passende Sicherungsrichtlinie für alle Volumes. Es wird empfohlen die Häufigkeit der Sicherungen auf die Recovery Point Objective (RPO) festzulegen, die Sie für die Anwendung haben möchten.

### <a name="configure-the-network"></a>Konfigurieren des Netzwerks
Konfigurieren Sie Netzwerkeinstellungen für die Dateiserver-VM in Azure Site Recovery, sodass die VM-Netzwerke nach dem Failover an dem richtigen DR-Netzwerk angefügt sind.

Sie können den virtuellen Computer auf der Registerkarte **Replizierte Elemente** auswählen, um die Netzwerkeinstellungen wie in der folgenden Abbildung gezeigt zu konfigurieren.

![Compute und Netzwerk](./media/storsimple-disaster-recovery-using-azure-site-recovery/image2.png)

## <a name="create-a-recovery-plan"></a>Erstellen eines Wiederherstellungsplans
Sie können einen Wiederherstellungsplan in ASR erstellen, um den Failoverprozess der Dateifreigaben zu automatisieren. Wenn eine Störung auftritt, können Sie die Dateifreigaben in wenigen Minuten mit nur einem einzigen Klick anzeigen. Sie benötigen ein Azure Automation-Konto, um diese Automatisierung zu aktivieren.

#### <a name="to-create-an-automation-account"></a>So erstellen Sie ein Automation-Konto
1. Navigieren Sie zu Azure-Portal &gt; **Automation**.
1. Klicken Sie auf die Schaltfläche **+ Hinzufügen**. Dadurch wird das unten angezeigte Blatt geöffnet.
   
   ![Automation-Konto hinzufügen](./media/storsimple-disaster-recovery-using-azure-site-recovery/image11.png)
   
   - Name: Geben Sie ein neues Automation-Konto ein.
   - Abonnement: Wählen Sie ein Abonnement aus.
   - Ressourcengruppe: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.
   - Speicherort: Wählen Sie einen Speicherort im gleichen geografischen Raum oder in der gleichen Region aus, in dem/der die StorSimple Cloud Appliance und die Speicherkonten erstellt wurden.
   - Erstellen eines ausführenden Azure-Kontos: Wählen Sie die Option **Ja** aus.
   
1. Wechseln Sie zum Automation-Konto, klicken Sie auf **Runbooks** &gt; **Katalog durchsuchen**, um alle erforderlichen Runbooks in das Automation-Konto zu importieren.
1. Fügen Sie die folgenden Runbooks hinzu, indem Sie das Tag **Notfallwiederherstellung** im Katalog suchen:
   
   - Bereinigen von StorSimple-Volumes nach dem Testfailover (TFO)
   - Failover für StorSimple-Volumecontainer
   - Bereitstellen von Volumes auf einem StorSimple-Gerät nach einem Failover
   - Deinstallieren der benutzerdefinierten Skripterweiterung in Azure-VM
   - Starten von StorSimple Virtual Appliance
   
      ![Katalog durchsuchen](./media/storsimple-disaster-recovery-using-azure-site-recovery/image3.png)
   
1. Veröffentlichen Sie alle Skripts, indem Sie das Runbook im Automation-Konto auswählen. Klicken Sie dann auf **Bearbeiten** &gt; **Veröffentlichen** und dann in der Bestätigungsmeldung auf **Ja**. Nach diesem Schritt wird die Registerkarte **Runbook** wie folgt angezeigt:
   
   ![Runbooks](./media/storsimple-disaster-recovery-using-azure-site-recovery/image4.png)
   
1. Klicken Sie im Automation-Konto auf **Variablen** &gt; **Variable hinzufügen**, und fügen Sie die folgenden Variablen hinzu. Sie können diese Ressourcen verschlüsseln. Diese Variablen sind spezifisch für den Wiederherstellungsplan. Wenn der Name des Wiederherstellungsplans (den Sie im nächsten Schritt erstellen) TestPlan lautet, müssen Ihre Variablen TestPlan-StorSimRegKey, TestPlan-AzureSubscriptionName usw. heißen.

   - **BaseUrl:** Die URL des Resource Managers für die Azure-Cloud. Rufen Sie sie mithilfe des Cmdlets **Get-AzEnvironment | Select-Object Name, ResourceManagerUrl** ab.
   - _RecoveryPlanName_ **-ResourceGroupName**: Die Resource Manager-Gruppe, die über die StorSimple-Ressource verfügt.
   - _RecoveryPlanName_ **-ManagerName**: Die StorSimple-Ressource, die über das StorSimple-Gerät verfügt.
   - _RecoveryPlanName_ **-DeviceName**: Das StorSimple-Gerät, für das ein Failover ausgeführt werden muss.
   - _RecoveryPlanName_ **-DeviceIpAddress**: Die IP-Adresse des Geräts (diese finden Sie auf der Registerkarte **Geräte** im Abschnitt „StorSimple-Geräte-Manager“ &gt; **Einstellungen**&gt;  **Netzwerk** &gt; **Gruppe „DNS-Einstellungen“** ).
   - _RecoveryPlanName_ **-VolumeContainers**: Eine durch Trennzeichen getrennte Zeichenfolge von Volumecontainern, die sich auf dem Gerät befinden, und für die ein Failover ausgeführt werden muss, z.B. „volcon1“, „volcon2“, „volcon3“.
   - _RecoveryPlanName_ **-TargetDeviceName**: Die StorSimple Cloud Appliance, auf die das Failover für Container ausgeführt werden soll.
   - _RecoveryPlanName_ **-TargetDeviceIpAddress**: Die IP-Adresse des Zielgeräts (diese finden Sie im Abschnitt **Virtueller Computer** &gt; **Gruppe „Einstellungen“** &gt; **Registerkarte „Netzwerk“** ).
   - _RecoveryPlanName_ **-StorageAccountName**: Der Name des Speicherkontos, in dem das Skript (das auf der VM, für die ein Failover ausgeführt wurde, ausgeführt werden muss) gespeichert wird. Dies kann ein beliebiges Speicherkonto sein, das über genügend Speicherplatz verfügt, um das Skript zeitweise zu speichern.
   - _RecoveryPlanName_ **-StorageAccountKey**: Der Zugriffsschlüssel für das oben genannte Speicherkonto.
   - _RecoveryPlanName_ **-VMGUIDS**: Beim Schützen einer VM weist Azure Site Recovery allen VMs eine eindeutige ID zu, die die Details zur VM enthält, für die ein Failover ausgeführt wurde. Wählen Sie zum Abrufen von VMGUID die Registerkarte **Recovery Services** aus, und klicken Sie anschließend auf **Geschütztes Element** &gt; **Schutzgruppen** &gt; **Computer** &gt; **Eigenschaften**. Wenn Sie mehrere VMs haben, fügen Sie die GUIDs als durch Trennzeichen getrennte Zeichenfolge hinzu.

     Wenn der Name des Wiederherstellungsplans beispielsweise fileServerpredayRP lautet, sollten die Registerkarten **Variablen**, **Verbindungen** und **Zertifikate** wie folgt aussehen, nachdem Sie alle Ressourcen hinzugefügt haben.

      ![Objekte](./media/storsimple-disaster-recovery-using-azure-site-recovery/image5.png)

1. Laden Sie das Runbookmodul der StorSimple 8000er Serie in Ihr Automation-Konto. Befolgen Sie die nachstehenden Schritte, um ein Modul hinzuzufügen:
   
   1. Öffnen Sie PowerShell, erstellen Sie einen neuen Ordner, und wechseln Sie in das Verzeichnis im neuen Ordner.
      
      ```
            mkdir C:\scripts\StorSimpleSDKTools
            cd C:\scripts\StorSimpleSDKTools
      ```
   1. Laden Sie die NuGet-CLI unter dem gleichen Ordner in Schritt 1 herunter.
      Unterschiedliche nuget.exe-Versionen sind in in den [NuGet-Downloads](https://www.nuget.org/downloads) verfügbar. Jeder Downloadlink verweist direkt auf eine EXE-Datei, führen Sie die Datei also nicht über den Browser aus, sondern klicken Sie mit der rechten Maustaste auf die Datei, und speichern Sie sie auf Ihrem Computer.
      
      ```
            wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
      ```
      
   1. Laden Sie das abhängige SDK herunter.
      
      ```
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
            C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
      ```
      
   1. Erstellen Sie ein Azure Automation-Runbookmodul für die Geräteverwaltung der StorSimple 8000er Serie. Verwenden Sie die nachstehenden Befehle, um eine Automation-Modul-ZIP-Datei zu erstellen.
         
      ```powershell
            # set path variables
            $downloadDir = "C:\scripts\StorSimpleSDKTools"
            $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

            #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
            mkdir "$moduleDir"

            copy "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
            copy "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
            copy "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

            #Don't change the name of the Archive
            compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
      ```
         
   1. Importieren Sie die ZIP-Datei des Azure Automation-Moduls (Microsoft.Azure.Management.StorSimple8000Series.zip), die Sie im Schritt zuvor erstellt haben. Wählen Sie dafür das Automation-Konto aus, klicken Sie unter „Gemeinsame Ressourcen“ auf **Module** und anschließend auf **Modul hinzufügen**.
   
   Nachdem Sie das Modul der StorSimple 8000er Serie importiert haben, sollte die Registerkarte **Module** in etwa so aussehen:
   
      ![Module](./media/storsimple-disaster-recovery-using-azure-site-recovery/image12.png)

1. Klicken Sie auf den Abschnitt **Recovery Services** , und wählen Sie den Azure Site Recovery-Tresor aus, den Sie zuvor erstellt haben.
1. Wählen Sie die Option **Wiederherstellungspläne (Site Recovery)** aus der Gruppe **Verwalten** aus, und erstellen Sie einen neuen Wiederherstellungsplan wie folgt:
   
   - Klicken Sie auf die Schaltfläche **+ Plan wiederherstellen**, um das folgende Blatt zu öffnen.
      
      ![Wiederherstellungsplan erstellen](./media/storsimple-disaster-recovery-using-azure-site-recovery/image6.png)
      
   - Geben Sie einen Namen für den Wiederherstellungsplan ein, und wählen Sie die Modellwerte „Quelle“, „Ziel“ und „Bereitstellung“ aus.
   
   - Wählen Sie die VMs aus der Schutzgruppe aus, die im Wiederherstellungsplan enthalten sein sollen, und klicken Sie dann auf **OK**.
   
   - Wählen Sie den zuvor erstellten Wiederherstellungsplan aus, klicken Sie auf die Schaltfläche **Anpassen**, um die Ansicht zur Anpassung des Wiederherstellungsplans zu öffnen.
   
   - Klicken Sie mit der rechten Maustaste auf **Alle Gruppen heruntergefahren**, und klicken Sie dann auf **Vorausgehende Aktion hinzufügen**.
   
   - Das Blatt „Aktion einfügen“ wird geöffnet. Geben Sie einen Namen ein, wählen Sie die Option **Primärseite** in der Option „Ausführungsort“ und dann „Automation-Konto“ aus (in dem Sie die Runbooks hinzugefügt haben). Wählen Sie anschließend das Runbook **Failover-StorSimple-Volume-Containers** aus.
   
   - Klicken Sie mit der rechten Maustaste auf **Gruppe 1: Start**, und klicken Sie auf die Option **Geschützte Elemente hinzufügen**. Wählen Sie dann die virtuellen Computer aus, die im Wiederherstellungsplan geschützt werden sollen, und klicken Sie dann auf die Schaltfläche **OK**. Optional, falls bereits virtuelle Computer ausgewählt sind.
   
   - Klicken Sie mit der rechten Maustaste auf **Gruppe 1: Start**, und klicken Sie dann auf die Option **Nachfolgende Aktion**. Fügen Sie dann alle folgenden Skripts hinzu:  
      
      - Das Runbook „Start-StorSimple-Virtual-Appliance“  
      - Das Runbook „Fail over-StorSimple-volume-containers“  
      - Das Runbook „Mount-volumes-after-failover“  
      - Das Runbook „Uninstall-custom-script-extension“  
        
   - Fügen Sie eine manuelle Aktion nach dem oben genannten 4 Skripts im gleichen Abschnitt **Group 1: Post-steps** (Gruppe 1: nachfolgende Schritte). Diese Aktion ist der Punkt, an dem Sie überprüfen können, dass alles ordnungsgemäß funktioniert. Diese Aktion muss nur als Teil des Testfailovers hinzugefügt werden (aktivieren Sie also nur das Kontrollkästchen **Testfailover**).
    
   - Fügen Sie nach der manuellen Aktion das **Bereinigungsskript** mithilfe der gleichen Prozedur hinzu, die Sie für die anderen Runbooks verwendet haben. **Speichern** Sie den Wiederherstellungsplan.
    
   > [!NOTE]
   > Wenn Sie ein Testfailover ausführen, sollten Sie beim Schritt der manuellen Aktion alles überprüfen, da die StorSimple-Volumes, die auf dem Zielgerät geklont wurden, als Teil der Bereinigung nach dem Abschließen der manuellen Aktion gelöscht werden.
       
      ![Wiederherstellungsplan](./media/storsimple-disaster-recovery-using-azure-site-recovery/image7.png)

## <a name="perform-a-test-failover"></a>Ausführen eines Testfailovers
Finden Sie im Begleithandbuch [Active Directory-DR-Lösung](../site-recovery/site-recovery-active-directory.md) Überlegungen in Bezug auf Active Directory während des Testfailovers. Die lokale Einrichtung wird überhaupt nicht gestört, wenn das Testfailover auftritt. Die StorSimple-Volumes, die der lokalen VM angefügt wurden, werden auf die StorSimple Cloud Appliance auf Azure geklont. Eine VM für Testzwecke wird in Azure geladen und die geklonten Volumes der VM angefügt.

#### <a name="to-perform-the-test-failover"></a>So führen Sie das Testfailover aus
1. Wählen Sie im Azure-Portal Ihren Site Recovery-Tresor aus.
1. Klicken Sie auf den Wiederherstellungsplan, der für die Dateiserver-VM erstellt wurde.
1. Klicken Sie auf **Testfailover**.
1. Wählen Sie das virtuelle Azure-Netzwerk aus, mit dem virtuelle Azure-Computer nach dem Failover verbunden werden.
   
   ![Failover starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image8.png)
   
1. Klicken Sie auf **OK**, um den Failovervorgang zu starten. Sie können den Fortschritt verfolgen, indem Sie auf den virtuellen Computer klicken, um die Eigenschaften zu öffnen. Alternativ können Sie unter „Tresorname“ &gt; **Aufträge** &gt; **Site Recovery-Aufträge** auf den Auftrag **Testfailover** klicken.
1. Nach Abschluss des Failovers sollte der Azure-Replikatcomputer im Azure-Portal unter **Virtuelle Computer** angezeigt werden. Sie können Ihre Überprüfungen durchführen.
1. Nachdem die Überprüfungen abgeschlossen sind, klicken Sie auf **Überprüfungen abgeschlossen**. Dadurch werden die StorSimple-Volumes entfernt und die StorSimple Cloud Appliance heruntergefahren.
1. Klicken Sie anschließend auf dem Wiederherstellungsplan auf **Cleanup-Test-Failover** (Testfailover bereinigen). Erfassen und speichern Sie unter „Hinweise“ alle Beobachtungen im Zusammenhang mit dem Testfailover. Dadurch wird der während des Testfailovers erstellte virtuelle Computer gelöscht.

## <a name="perform-a-planned-failover"></a>Ausführen eines geplanten Failovers
   Bei einem geplanten Failover wird die lokale Dateiserver-VM ordnungsgemäß heruntergefahren, und eine Cloudsicherungs-Momentaufnahme der Volumes auf dem StorSimple-Gerät wird erzeugt. Failover für die StorSimple-Volumes werden auf das virtuelle Gerät ausgeführt. Eine Replikat-VM wird in Azure aktiviert, und die Volumes werden der VM angefügt.

#### <a name="to-perform-a-planned-failover"></a>So führen Sie ein geplantes Failover aus
1. Wählen Sie im Azure-Portal den Tresor **Recovery Services** &gt; **Wiederherstellungspläne (Site Recovery)** &gt; **Wiederherstellungsplanname** aus, der für den virtuellen Computer des Dateiservers erstellt wurde.
1. Klicken Sie auf dem Blatt „Wiederherstellungsplan“ auf **Mehr** &gt; **Geplantes Failover**.

   ![Screenshot: Hervorgehobene Optionen „Geplantes Failover“ und „Failover“](./media/storsimple-disaster-recovery-using-azure-site-recovery/image9.png)
1. Wählen Sie auf dem Blatt **Geplanten Failover bestätigen** den Quell- und den Zielspeicheror und das Zielnetzwerk aus, und klicken Sie auf das Häkchen-Symbol ✓, um den Failoverprozess zu starten.
1. Nach der Erstellung der virtuellen Replikatcomputer weist deren Status darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.
1. Nach Abschluss der Replikation werden die virtuellen Computer am sekundären Standort gestartet.

## <a name="perform-a-failover"></a>Ausführen eines Failovers
Während eines ungeplanten Failovers wird ein Failover für die StorSimple-Volumes auf das virtuelle Gerät ausgeführt. Eine Replikat-VM wird in Azure aktiviert, und die Volumes werden der VM angefügt.

#### <a name="to-perform-a-failover"></a>So führen Sie ein Failover aus
1. Wählen Sie im Azure-Portal den Tresor **Recovery Services** &gt; **Wiederherstellungspläne (Site Recovery)** &gt; **Wiederherstellungsplanname** aus, der für den virtuellen Computer des Dateiservers erstellt wurde.
1. Klicken Sie auf dem Blatt „Wiederherstellungsplan“ auf **Mehr** &gt; **Failover**.
1. Wählen Sie auf dem Blatt **Failover bestätigen** den Quell- und Zielort aus.
1. Wählen Sie **Virtuelle Computer herunterfahren und die aktuellen Daten synchronisieren** aus, damit Site Recovery den geschützten virtuellen Computer herunterfährt und die Daten synchronisiert, sodass das Failover mit der neuesten Version der Daten erfolgt.
1. Nach dem Failover weist der Status der virtuellen Computer darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.


## <a name="perform-a-failback"></a>Ein Failback durchführen
Während eines Failbacks wird für die StorSimple-Volumecontainer ein Failover auf die physischen Geräte durchgeführt, nachdem die Sicherung erstellt wurde.

#### <a name="to-perform-a-failback"></a>So führen Sie ein Failback durch
1. Wählen Sie im Azure-Portal den Tresor **Recovery Services** &gt; **Wiederherstellungspläne (Site Recovery)** &gt; **Wiederherstellungsplanname** aus, der für den virtuellen Computer des Dateiservers erstellt wurde.
1. Klicken Sie auf dem Blatt „Wiederherstellungsplan“ auf **Mehr** &gt; **Geplantes Failover**.
1. Wählen Sie den Quell- und Zielort und dann die entsprechenden Optionen für die Datensynchronisierung und die Erstellung des virtuellen Computers aus.
1. Klicken Sie auf die Schaltfläche **OK**, um den Failbackprozess zu starten.
   
   ![Failback starten](./media/storsimple-disaster-recovery-using-azure-site-recovery/image10.png)

## <a name="best-practices"></a>Bewährte Methoden
### <a name="capacity-planning-and-readiness-assessment"></a>Kapazitätsplanung und Readiness Assessment
#### <a name="hyper-v-site"></a>Hyper-V-Standort
Verwenden Sie das [Benutzertool Capacity Planner](https://www.microsoft.com/download/details.aspx?id=39057) , um den Server, Arbeitsspeicher und die Netzwerkinfrastruktur für die Hyper-V-Replikatumgebung zu erstellen.

#### <a name="azure"></a>Azure
Sie können das Tool [Azure Virtual Machine Readiness Assessment](https://azure.microsoft.com/downloads/vm-readiness-assessment/) auf VMs ausführen, um sicherzustellen, dass diese mit Azure VMs und Azure Site Recovery Services kompatibel sind. Das Readiness Assessment-Tool überprüft die VM-Konfigurationen und gibt eine Warnung aus, wenn die Konfigurationen mit Azure inkompatibel sind. Es gibt z.B. eine Warnung aus, wenn ein Laufwerk C: größer als 127 GB ist.

Die Kapazitätsplanung besteht aus mindestens zwei wichtigen Prozessen:

   - Zuordnung von lokalen Hyper-V-VMs zu Azure-VM-Größen (z.B. A6, A7, A8 und A9)
   - Bestimmung der benötigten Internet-Bandbreite

## <a name="limitations"></a>Einschränkungen
- Derzeit kann nur für ein StorSimple-Gerät ein Failover ausgeführt werden (auf eine einzelne StorSimple Cloud Appliance). Das Szenario eines Dateiservers, der mehrere StorSimple-Geräte umfasst, wird noch nicht unterstützt.
- Wenn Sie eine Fehlermeldung erhalten, während Sie den Schutz für eine VM aktivieren, stellen Sie sicher, dass Sie die Verbindung mit den iSCSI-Zielen getrennt haben.
- Für alle Volumecontainer, die aufgrund von Volumecontainer übergreifenden Sicherheitsregeln gruppiert wurden, wird ein Failover ausgeführt.
- Für alle Volumes in Volumecontainern, die Sie ausgewählt haben, wird ein Failover ausgeführt.
- Für Volumes, die zusammen mehr als 64 TB ergeben, kann kein Failover ausgeführt werden, da die maximale Kapazität einer einzelnen StorSimple Cloud Appliance 64 TB beträgt.
- Bereinigen Sie nicht die VMs, wenn das geplante/ungeplante Failover fehlschlägt, und die VMs in Azure erstellt werden. Führen Sie stattdessen ein Failback aus. Wenn Sie die virtuellen Computer löschen, können die lokalen VMs nicht wieder aktiviert werden.
- Wenn Sie nach einem Failover die Volumes nicht sehen können, wechseln Sie zu den VMs, öffnen Sie die Datenträgerverwaltung, lesen Sie die Datenträger erneut ein, und stellen Sie sie online.
- In einigen Fällen können die Laufwerkbuchstaben am DR-Standort von den lokalen abweichen. In diesem Fall müssen Sie das Problem manuell beheben, nachdem das Failover abgeschlossen ist.
- Zeitlimitüberschreitung des Failoverauftrags: Für das StorSimple-Skript tritt ein Timeout auf, wenn das Failover von Volumecontainern mehr Zeit benötigt, als das Azure Site Recovery-Limit pro Skript (derzeit 120 Minuten).
- Zeitlimitüberschreitung des Sicherungsauftrags: Für das StorSimple-Skript tritt ein Timeout auf, wenn die Sicherung von Volumecontainern mehr Zeit benötigt, als das Azure Site Recovery-Limit pro Skript (derzeit 120 Minuten).
   
  > [!IMPORTANT]
  > Führen Sie die Sicherung manuell vom Azure-Portal aus, und führen Sie anschließend den Wiederherstellungsplan erneut aus.
   
- Zeitlimitüberschreitung des Klonauftrags: Für das StorSimple-Skript tritt ein Timeout auf, wenn das Klonen von Volumecontainern mehr Zeit benötigt, als das Azure Site Recovery-Limit pro Skript (derzeit 120 Minuten).
- Zeitsynchronisierungsfehler: Für die StorSimple Skripts tritt ein Fehler auf, der besagt, dass die Sicherungen nicht erfolgreich ausgeführt wurden, auch wenn die Sicherung im Portal erfolgreich war. Eine mögliche Ursache dafür ist, dass die Zeit der StorSimple Appliance nicht mehr mit der aktuellen Zeit in der Zeitzone synchronisiert ist.
   
  > [!IMPORTANT]
  > Synchronisieren Sie die Zeit der Appliance mit der aktuellen Zeit in der Zeitzone.
   
- Fehler beim Appliancefailover: Das StorSimple-Skript kann fehlschlagen, wenn ein Failover auf eine Appliance besteht, wenn der Wiederherstellungplan ausgeführt wird.
   
  > [!IMPORTANT]
  > Wiederholen Sie den Wiederherstellungsplan nach Abschluss des Appliancefailovers.


## <a name="summary"></a>Zusammenfassung
Sie können mithilfe von Azure Site Recovery einen vollständig automatisierten Plan für die Notfallwiederherstellung für eine Dateiserver-VM erstellen, die über Dateifreigaben verfügt, die auf StorSimple gehostet werden. Bei einer Störung können Sie das Failover von überall aus in Sekundenschnelle einleiten und die Anwendung binnen weniger Minuten wieder in Betrieb nehmen.