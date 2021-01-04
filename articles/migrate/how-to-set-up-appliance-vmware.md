---
title: Einrichten einer Azure Migrate-Appliance für VMware
description: In diesem Artikel erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Bewertung und Migration von VMware-VMs einrichten.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: cc479f8e4dc87155721b25fab2ba522880b0ac4f
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751056"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Einrichten einer Appliance für VMware-VMs

In diesem Artikel erfahren Sie, wie Sie eine Azure Migrate-Appliance für die Bewertung mithilfe des Tools [Azure Migrate-Serverbewertung](migrate-services-overview.md#azure-migrate-server-assessment-tool) und für die Migration ohne Agent mithilfe des Tools [Azure Migrate-Servermigration](migrate-services-overview.md#azure-migrate-server-migration-tool) einrichten.

Die [Azure Migrate-Appliance](migrate-appliance.md) ist eine einfache Appliance, die von Azure Migrate-Serverbewertung und Azure Migrate-Servermigration verwendet wird, um lokale VMware-VMS zu ermitteln, VM-Metadaten/Leistungsdaten an Azure zu senden und für die Replikation von VMware-VMs während der Migration ohne Agent.

Sie können die Appliance mithilfe verschiedener Methoden bereitstellen:

- Einrichten auf einer VMware-VM mit einer heruntergeladenen OVA-Vorlage. Dies ist die in diesem Artikel beschriebene Methode.
- Einrichten auf einer VMware-VM oder einem physischen Computer mit einem PowerShell-Installationsskript. [Diese Methode](deploy-appliance-script.md) sollte verwendet werden, wenn Sie keine VM mithilfe einer OVA-Vorlage einrichten können oder wenn Sie in Azure Government arbeiten.

Überprüfen Sie nach der Erstellung der Appliance, ob diese eine Verbindung mit der Azure Migrate-Serverbewertung herstellen kann. Führen Sie dann die erstmalige Konfiguration durch, und registrieren Sie sie für das Azure Migrate-Projekt.


## <a name="appliance-deployment-ova"></a>Bereitstellung der Appliance (OVA)

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


   ![Auswahloptionen für „Computer ermitteln“](./media/tutorial-assess-vmware/servers-discover.png)


   ![Auswahloptionen für „Schlüssel generieren“](./media/tutorial-assess-vmware/generate-key-vmware.png)

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die OVA-Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die OVA-Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung: ```C:\>C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256```
3. Für die aktuellste Applianceversion muss der generierte Hash den unten angegebenen [Einstellungen](./tutorial-discover-vmware.md#verify-security) entsprechen.



## <a name="create-the-appliance-vm"></a>Erstellen der Appliance-VM

Importieren Sie die heruntergeladene Datei, und erstellen Sie eine VM.

1. Klicken Sie in der vSphere-Clientkonsole auf **Datei** > **Deploy OVF Template** (OVF-Vorlage bereitstellen).
![Menübefehl zum Bereitstellen einer OVF-Vorlage](./media/tutorial-assess-vmware/deploy-ovf.png)

2. Geben Sie im Assistenten zum Bereitstellen der OVF-Vorlage unter **Quelle** den Speicherort der OVA-Datei an.
3. Geben Sie unter **Name** und **Standort** einen Anzeigenamen für die VM an. Wählen Sie das Inventarobjekt aus, in dem die VM gehostet wird.
5. Geben Sie unter **Host/Cluster** den Host oder Cluster an, auf bzw. in dem die VM ausgeführt wird.
6. Geben Sie unter **Speicher** das Speicherziel für die VM an.
7. Geben Sie unter **Datenträgerformat** den Typ und die Größe des Datenträgers an.
8. Geben Sie unter **Netzwerkzuordnung** das Netzwerk an, mit dem die VM eine Verbindung herstellt. Das Netzwerk muss über eine Internetverbindung verfügen, um Metadaten an die Azure Migrate-Serverbewertung senden zu können.
9. Überprüfen Sie die Einstellungen, und klicken Sie dann auf **Fertig stellen**.


## <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

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
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account) für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.


## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Die Appliance muss eine Verbindung mit der vCenter Server-Instanz herstellen, um die Konfigurations- und Leistungsdaten der VMs zu ermitteln.

1. Wählen Sie in **Schritt 1: Geben Sie die vCenter Server-Anmeldeinformationen** ein, klicken Sie auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, fügen Sie **Benutzername** und **Kennwort** für das vCenter Server-Konto hinzu, das die Appliance verwendet, um VMs auf der vCenter Server-Instanz zu ermitteln.
    - Sie sollten im [vorherigen Tutorial](./tutorial-discover-vmware.md#create-an-account-to-access-vcenter) ein Konto mit den erforderlichen Berechtigungen eingerichtet haben.
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

Lesen Sie die Tutorials zur [VMware-Bewertung](./tutorial-assess-vmware-azure-vm.md) und zur [Migration ohne Agent](tutorial-migrate-vmware.md).