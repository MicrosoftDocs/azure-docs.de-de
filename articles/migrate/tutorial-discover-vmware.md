---
title: Ermitteln von VMware-VMs mit der Azure Migrate-Serverbewertung
description: Hier erfahren Sie, wie Sie lokale virtuelle VMware-Computer mit dem Azure Migrate-Serverbewertungstool ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: ff82b90189e134db7d39b259c2c135489d14595d
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752786"
---
# <a name="tutorial-discover-vmware-vms-with-server-assessment"></a>Tutorial: Ermitteln virtueller VMware-Computer mit der Serverbewertung

Im Rahmen der Migration zu Azure ermitteln Sie den lokalen Bestand und die lokalen Workloads. 

In diesem Tutorial wird gezeigt, wie Sie lokale virtuelle VMware-Computer mit dem Azure Migrate- Serverbewertungstool und einer einfachen Azure Migrate-Appliance ermitteln. Sie stellen die Appliance als virtuellen VMware-Computer bereit, um kontinuierlich VM- und Leistungsmetadaten, auf virtuellen Computern ausgeführte Apps und VM-Abhängigkeiten zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten der VMware-Umgebung für die Ermittlung
> * Erstellen eines Azure Migrate-Projekts
> * Einrichten der Azure Migrate-Appliance
> * Starten der kontinuierlichen Ermittlung

> [!NOTE]
> In Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios beschrieben, und nach Möglichkeit werden dabei die Standardoptionen verwendet.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:


**Anforderung** | **Details**
--- | ---
**vCenter Server-/ESXi-Host** | Sie benötigen eine vCenter Server-Instanz mit Version 5.5, 6.0, 6.5 oder 6.7.<br/><br/> Die virtuellen Computer müssen auf einem ESXi-Host mit Version 5.5 oder höher gehostet werden.<br/><br/> Lassen Sie in der vCenter Server-Instanz eingehende Verbindungen über den TCP-Port 443 zu, damit die Appliance Bewertungsdaten sammeln kann.<br/><br/> Die Appliance stellt standardmäßig über Port 443 eine Verbindung mit vCenter her. Wenn die vCenter Server-Instanz an einem anderen Port lauscht, können Sie den Port beim Herstellen einer Verbindung zwischen Appliance und Server ändern, um die Ermittlung zu starten.<br/><br/> Stellen Sie sicher, dass auf dem EXSi-Server, auf dem die virtuellen Computer gehostet werden, eingehender Zugriff am TCP-Port 443 für die App-Ermittlung zugelassen ist.
**Appliance** | vCenter Server benötigt Ressourcen, um einen virtuellen Computer für die Azure Migrate-Appliance zuzuordnen:<br/><br/> - Windows Server 2016<br/><br/> - 32 GB RAM, acht vCPUs und ca. 80 GB Speicherplatz auf dem Datenträger<br/><br/> - Einen externen virtuellen Switch und Internetzugriff für den virtuellen Computer (direkt oder über einen Proxy)
**VMs** | Damit Sie dieses Tutorial absolvieren können, müssen virtuelle Windows-Computer unter Windows Server 2016, 2012 R2, 2012 oder 2008 R2 ausgeführt werden.<br/><br/> Virtuelle Linux-Computer müssen unter Red Hat Enterprise Linux 7/6/5, Ubuntu Linux 14.04/16.04, Debian 7/8, Oracle Linux 6/7 oder CentOS 5/6/7 ausgeführt werden.<br/><br/> Für virtuelle Computer müssen VMware-Tools (eine höhere Version als 10.2.0) installiert sein und ausgeführt werden.<br/><br/> Auf virtuellen Windows-Computern muss mindestens Windows PowerShell 2.0 installiert sein.


## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Azure Migrate-Projekts und Registrieren der Azure Migrate-Appliance benötigen Sie ein Konto mit den folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von Azure Active Directory-Apps

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-vmware/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Azure Migrate-Projekt erstellen möchten. 
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-vmware/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-vmware/assign-role.png)

7. Suchen Sie im Portal nach Benutzern, und wählen Sie unter **Dienste** die Option **Benutzer** aus.
8. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-vmware/register-apps.png)

9. Als Alternative kann der Mandantenadministrator/globale Administrator einem Konto die Rolle **Anwendungsentwickler** zuweisen, um die Registrierung von AAD-Apps zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-vmware"></a>Vorbereiten von VMware

Erstellen Sie in der vCenter Server-Instanz ein Konto, das von der Appliance für den Zugriff auf vCenter Server verwendet werden kann, und überprüfen Sie, ob die erforderlichen Ports geöffnet sind. Sie benötigen darüber hinaus ein Konto, das von der Appliance für den Zugriff auf virtuelle Computer verwendet werden kann. 

### <a name="create-an-account-to-access-vcenter"></a>Erstellen eines Kontos für den Zugriff auf vCenter

Richten Sie im vSphere-Webclient wie folgt ein Konto ein:

1. Wählen Sie unter Verwendung eines Kontos mit Administratorrechten im vSphere-Webclient **Verwaltung** aus.
2. Wählen Sie für den Zugriff unter **Einmaliges Anmelden** die Option **Benutzer und Gruppen** aus.
3. Fügen Sie unter **Benutzer** einen neuen Benutzer hinzu.
4. Geben Sie unter **Neuer Benutzer** die Kontodetails ein. Klicken Sie dann auf **OK**.
5. Wählen Sie unter **Globale Berechtigungen** das Benutzerkonto aus, und weisen Sie ihm die Rolle **Schreibgeschützt** zu. Klicken Sie dann auf **OK**.
6. Wählen Sie unter **Rollen** die Rolle **Schreibgeschützt** und unter **Berechtigungen** die Option **Gastvorgänge** aus. Diese Berechtigungen sind erforderlich, um auf den virtuellen Computern ausgeführte Apps zu ermitteln und VM-Abhängigkeiten zu analysieren.
 
    ![Kontrollkästchen zum Zulassen von Gastvorgängen für die Rolle „Schreibgeschützt“](./media/tutorial-discover-vmware/guest-operations.png)


### <a name="create-an-account-to-access-vms"></a>Erstellen eines Kontos für den Zugriff auf virtuelle Computer

Die Appliance greift auf virtuelle Computer zu, um Apps zu ermitteln und VM-Abhängigkeiten zu analysieren. Die Appliance installiert keine Agents auf virtuellen Computern.

1. Erstellen Sie ein lokales Administratorkonto, das von der Appliance zum Ermitteln von Apps und Abhängigkeiten auf virtuellen Windows-Computern verwendet werden kann.
2. Erstellen Sie für Linux-Computer ein Benutzerkonto mit root-Berechtigungen oder alternativ ein Benutzerkonto mit den folgenden Berechtigungen für Dateien vom Typ „/bin/netstat“ und „/bin/ls“: CAP_DAC_READ_SEARCH und CAP_SYS_PTRACE.

> [!NOTE]
> Azure Migrate unterstützt einen Satz an Anmeldeinformationen für die App-Ermittlung auf allen Windows-Servern und einen Satz an Anmeldeinformationen für die App-Ermittlung auf allen Linux-Computern.


## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Azure Migrate-Projekt ein.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-vmware/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde.

Das Tool **Azure Migrate- Serverbewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Serverbewertungstool](./media/tutorial-discover-vmware/added-tool.png)


## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Gehen Sie zum Einrichten der Appliance mithilfe einer OVA-Vorlage wie folgt vor:
- Geben Sie einen Appliancenamen ein, und generieren Sie einen Azure Migrate-Projektschlüssel im Portal.
- Herunterladen einer OVA-Vorlagendatei und Importieren der Datei in vCenter Server
- Erstellen der Appliance und Überprüfen der Verbindungsherstellung mit der Azure Migrate-Serverbewertung
- Führen Sie eine Erstkonfiguration für die Appliance aus, und registrieren Sie die Appliance beim Azure Migrate-Projekt unter Verwendung des Azure Migrate-Projektschlüssels.

> [!NOTE]
> Wenn Sie die Appliance aus irgendeinem Grund nicht mithilfe der Vorlage einrichten können, können Sie für die Einrichtung ein PowerShell-Skript verwenden. [Weitere Informationen](deploy-appliance-script.md#set-up-the-appliance-for-vmware)


### <a name="deploy-with-ova"></a>Bereitstellen mit OVA

Gehen Sie zum Einrichten der Appliance mithilfe einer OVA-Vorlage wie folgt vor:
- Geben Sie einen Appliancenamen ein, und generieren Sie einen Azure Migrate-Projektschlüssel im Portal.
- Herunterladen einer OVA-Vorlagendatei und Importieren der Datei in vCenter Server
- Erstellen der Appliance und Überprüfen der Verbindungsherstellung mit der Azure Migrate-Serverbewertung
- Führen Sie eine Erstkonfiguration für die Appliance aus, und registrieren Sie die Appliance beim Azure Migrate-Projekt unter Verwendung des Azure Migrate-Projektschlüssels.

### <a name="generate-the-azure-migrate-project-key"></a>Generieren des Azure Migrate-Projektschlüssels

1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Ja, mit VMware vSphere Hypervisor** aus.
3. Geben Sie in **1: Generieren eines Azure Migrate-Projektschlüssels** einen Namen für die Azure Migrate-Appliance an, die Sie für die Ermittlung von VMware-VMs einrichten möchten. Der Name muss alphanumerisch sein und darf höchstens 14 Zeichen enthalten.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Computer ermitteln“ nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Azure Migrate-Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

### <a name="download-the-ova-template"></a>Herunterladen der OVA-Vorlage

Wählen Sie in **2: Azure Migrate-Appliance herunterladen** die OVA-Datei aus, und klicken Sie auf **Herunterladen**. 


### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die OVA-Datei sicher ist:

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die OVA-Datei zu generieren:
  
   ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
   
   Beispielverwendung: ```C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```

3. Überprüfen Sie die aktuellen Applianceversionen und Hashwerte:

    - Öffentliche Azure-Cloud:
    
        **Algorithmus** | **Download** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140333) | e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

    - Azure Government:
    
        **Algorithmus** | **Download** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140337) | 2daaa2a59302bf911e8ef195f8add7d7c8352de77a9af0b860e2a627979085ca




### <a name="create-the-appliance-vm"></a>Erstellen der Appliance-VM

Importieren Sie die heruntergeladene Datei, und erstellen Sie eine VM.

1. Klicken Sie in der vSphere-Clientkonsole auf **Datei** > **Deploy OVF Template** (OVF-Vorlage bereitstellen).
2. Geben Sie im Assistenten zum Bereitstellen der OVF-Vorlage unter **Quelle** den Speicherort der OVA-Datei an.
3. Geben Sie unter **Name** und **Standort** einen Anzeigenamen für die VM an. Wählen Sie das Inventarobjekt aus, in dem die VM gehostet wird.
5. Geben Sie unter **Host/Cluster** den Host oder Cluster an, auf bzw. in dem die VM ausgeführt wird.
6. Geben Sie unter **Speicher** das Speicherziel für die VM an.
7. Geben Sie unter **Datenträgerformat** den Typ und die Größe des Datenträgers an.
8. Geben Sie unter **Netzwerkzuordnung** das Netzwerk an, mit dem die VM eine Verbindung herstellt. Das Netzwerk muss über eine Internetverbindung verfügen, um Metadaten an die Azure Migrate-Serverbewertung senden zu können.
9. Überprüfen Sie die Einstellungen, und klicken Sie dann auf **Fertig stellen**.


### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance-VM eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.


### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

> [!NOTE]
> Wenn Sie die Appliance mithilfe eines [PowerShell-Skripts](deploy-appliance-script.md) und nicht mit der heruntergeladenen OVA einrichten, sind die ersten beiden Schritte in diesem Verfahren nicht relevant.

1. Klicken Sie in der vSphere-Clientkonsole mit der rechten Maustaste auf den virtuellen Computer, und wählen Sie **Konsole öffnen** aus.
2. Geben Sie die Sprache, die Zeitzone und das Kennwort für die Appliance an.
3. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der VM herstellen kann, und öffnen Sie die URL der Appliance-Web-App: **https://*Appliancename oder IP-Adresse*: 44368**.

   Alternativ können Sie auch auf dem Appliancedesktop die App-Verknüpfung auswählen, um die App zu öffnen.
1. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
   - **Konnektivität**: Die App überprüft, ob die VM über Internetzugriff verfügt. Falls die VM einen Proxy verwendet, gehen Sie wie folgt vor:
     - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
     - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
     - Es werden nur HTTP-Proxys unterstützt.
     - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
   - **Uhrzeitsynchronisierung**: Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
   - **Updates installieren**: Die Appliance stellt sicher, dass die neuesten Updates installiert sind. Nachdem die Prüfung abgeschlossen ist, können Sie auf **Appliancedienste anzeigen** klicken, um den Status und die Versionen der auf der Appliance ausgeführten Komponenten anzuzeigen.
   - **Install VDDK** (VDDK installieren): Die Appliance überprüft, ob das VMware vSphere-VDDK (Virtual Disk Development Kit) installiert ist. Laden Sie bei Bedarf VDDK 6.7 von VMware herunter, und extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei am angegebenen Ort auf der Appliance, wie unter **Installationsanweisungen** beschrieben.

     Das VDDK wird von der Azure Migrate-Servermigration verwendet, um Computer bei der Migration zu Azure zu replizieren. 
1. Wenn Sie möchten, können Sie während der Konfiguration der Appliance jederzeit **Voraussetzungen erneut ausführen**, um zu prüfen, ob die Appliance alle Voraussetzungen erfüllt.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Azure Migrate-Projektschlüssel** ein. Wenn Sie den Schlüssel nicht haben, wechseln Sie zu **Serverbewertung > Ermitteln > Vorhandene Appliances verwalten**, wählen Sie den Appliancenamen aus, den Sie bei der Generierung des Schlüssels angegeben haben, und kopieren Sie den entsprechenden Schlüssel.
1. Klicken Sie auf **Anmelden**. Auf einer neuen Browserregisterkarte wird eine Azure-Anmeldeaufforderung geöffnet. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Melden Sie sich auf dem neuen Tab mit Ihrem Azure-Benutzernamen und -Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Kehren Sie nach erfolgreicher Anmeldung zur Web-App zurück. 
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen Berechtigungen für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.



## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Die Appliance muss eine Verbindung mit der vCenter Server-Instanz herstellen, um die Konfigurations- und Leistungsdaten der VMs zu ermitteln.

1. Wählen Sie in **Schritt 1: Geben Sie die vCenter Server-Anmeldeinformationen** ein, klicken Sie auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, fügen Sie **Benutzername** und **Kennwort** für das vCenter Server-Konto hinzu, das die Appliance verwendet, um VMs auf der vCenter Server-Instanz zu ermitteln.
    - Sie sollten im vorherigen Tutorial ein Konto mit den erforderlichen Berechtigungen eingerichtet haben.
    - Wenn Sie die Ermittlung auf bestimmte VMware-Objekte (vCenter Server-Rechenzentren, Cluster, einen Ordner mit Clustern, Hosts, einen Ordner mit Hosts oder einzelne VMs) begrenzen möchten, hilft Ihnen die Anleitung in [diesem Artikel](set-discovery-scope.md) beim Einschränken des von Azure Migrate genutzten Kontos weiter.
1. Geben Sie in **Step 2: Geben Sie vCenter Server-Details** ein, klicken Sie auf **Ermittlungsquelle hinzufügen**, um den Anzeigenamen für die Anmeldeinformationen aus der Dropdownliste auszuwählen, geben Sie den **IP-Adresse/FQDN**-Wert der vCenter Server-Instanz an. Sie können für **Port** den Standardwert (443) beibehalten oder einen benutzerdefinierten Port angeben, den vCenter Server überwacht, und dann auf **Speichern** klicken.
1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zum vCenter Server mit den angegebenen Anmeldeinformationen zu überprüfen, und zeigt den **Überprüfungszustand** in der Tabelle für die vCenter Server-IP-Adresse/FQDN an.
1. Sie können die Verbindung zu vCenter Server jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Wählen Sie in **Schritt 3: Geben Sie VM-Anmeldeinformationen an, um installierte Anwendungen zu ermitteln und eine Abhängigkeitszuordnung ohne Agent** durchzuführen, klicken Sie auf **Anmeldeinformationen hinzufügen**, und geben Sie das Betriebssystem, für das die Anmeldeinformationen bereitgestellt werden, einen Anzeigenamen für die Anmeldeinformationen sowie den **Benutzernamen** und das **Kennwort** an. Klicken Sie anschließend auf **Speichern**.

    - Hier können Sie optional Anmeldeinformationen hinzufügen, wenn Sie ein Konto erstellt haben, das für das [Feature „Anwendungsermittlung“](how-to-discover-applications.md) oder das [Feature „Abhängigkeitsanalyse ohne Agent“](how-to-create-group-machine-dependencies-agentless.md) verwendet werden soll.
    - Wenn Sie diese Features nicht verwenden möchten, können Sie auf den Schieberegler klicken, um den Schritt zu überspringen. Sie können die Absicht jederzeit später rückgängig machen.
    - Sehen Sie sich die Anmeldeinformationen für [Anwendungsermittlung](migrate-support-matrix-vmware.md#application-discovery-requirements) oder für die [Abhängigkeitsanalyse ohne Agent](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) an.

5. Klicken Sie auf **Ermittlung starten**, um die VM-Ermittlung zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für die vCenter Server-IP-Adresse/FQDN in der Tabelle überprüfen.

Die Ermittlung funktioniert wie folgt:
- Es dauert ca. 15 Minuten, bis die ermittelten VM-Metadaten im Portal angezeigt werden.
- Die Ermittlung installierter Anwendungen, Rollen und Features dauert etwas. Die Dauer hängt davon ab, wie viele virtuelle Computer ermittelt werden. Bei 500 VMs dauert es ungefähr ein Stunde, bis der Anwendungsbestand im Azure Migrate-Portal angezeigt wird.


## <a name="next-steps"></a>Nächste Schritte

- [Bewerten Sie virtuelle VMware-Computer](./tutorial-assess-vmware-azure-vm.md) für die Migration zu virtuellen Azure-Computern.
- [Informieren Sie sich über die Daten](migrate-appliance.md#collected-data---vmware), die von der Appliance im Zuge der Ermittlung gesammelt werden.