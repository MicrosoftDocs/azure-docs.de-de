---
title: Ermitteln physischer Server mit der Azure Migrate-Serverbewertung
description: Hier erfahren Sie, wie Sie lokale physische Server mit der Azure Migrate-Serverbewertung ermitteln.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: mvc
ms.openlocfilehash: 1263bc3ffe18aa951b3e5b61747c889d36acbab1
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96752809"
---
# <a name="tutorial-discover-physical-servers-with-server-assessment"></a>Tutorial: Ermitteln physischer Server mit der Serverbewertung

Im Rahmen der Migration zu Azure ermitteln Sie Ihre Server für die Bewertung und Migration.

In diesem Tutorial wird gezeigt, wie Sie lokale physische Server mit dem Azure Migrate- Serverbewertungstool und einer einfachen Azure Migrate-Appliance ermitteln. Sie stellen die Appliance als physischen Server bereit, um kontinuierlich Computer- und Leistungsmetadaten zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten von physischen Servern für die Ermittlung
> * Erstellen eines Azure Migrate-Projekts
> * Einrichten der Azure Migrate-Appliance
> * Starten der kontinuierlichen Ermittlung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

**Anforderung** | **Details**
--- | ---
**Appliance** | Sie benötigen einen Computer für die Ausführung der Azure Migrate-Appliance. Der Computer sollte über Folgendes verfügen:<br/><br/> - Installation von Windows Server 2016. _(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_<br/><br/> - 16 GB RAM, 8 virtuelle CPUs, ungefähr 80 GB Speicherplatz auf dem Datenträger<br/><br/> - eine statische oder dynamische IP-Adresse sowie Internetzugriff (entweder direkt oder über einen Proxy)
**Windows-Server** | Lassen Sie eingehende Verbindungen am WinRM-Port 5985 (HTTP) zu, sodass die Appliance Konfigurations- und Leistungsmetadaten pullen kann.
**Linux-Server** | Lassen Sie eingehende Verbindungen über Port 22 (TCP) zu.

## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Zum Erstellen eines Azure Migrate-Projekts und Registrieren der Azure Migrate-Appliance benötigen Sie ein Konto mit den folgenden Berechtigungen:
- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für ein Azure-Abonnement
- Berechtigungen zum Registrieren von Azure Active Directory-Apps

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, um nach dem Azure-Abonnement zu suchen](./media/tutorial-discover-physical/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Azure Migrate-Projekt erstellen möchten. 
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suchen nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-physical/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und das Konto aus (in unserem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-physical/assign-role.png)

7. Suchen Sie im Portal nach Benutzern, und wählen Sie unter **Dienste** die Option **Benutzer** aus.
8. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    ![Überprüfen unter „Benutzereinstellungen“, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-physical/register-apps.png)

9. Als Alternative kann der Mandantenadministrator/globale Administrator einem Konto die Rolle **Anwendungsentwickler** zuweisen, um die Registrierung von AAD-Apps zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-physical-servers"></a>Vorbereiten physischer Server

Richten Sie ein Konto ein, das von der Appliance für den Zugriff auf die physischen Server verwendet werden kann.

- Verwenden Sie für Windows-Server ein Domänenkonto für in die Domäne eingebundene Computer und ein lokales Konto für nicht in die Domäne eingebundene Computer. Das Benutzerkonto sollte diesen Gruppen hinzugefügt werden: Remoteverwaltungsbenutzer, Leistungsüberwachungsbenutzer und Leistungsprotokollbenutzer.
- Linux-Server: Sie benötigen ein root-Konto auf den Linux-Servern, die Sie ermitteln möchten. Alternativ dazu können Sie auch mithilfe der folgenden Befehle ein Konto mit den erforderlichen Funktionen festlegen, bei dem es sich nicht um das root-Konto handelt:

**Befehl** | **Zweck**
--- | --- |
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/fdisk <br></br> setcap CAP_DAC_READ_SEARCH+eip /sbin/fdisk _(wenn „/usr/sbin/fdisk“ nicht vorhanden ist)_ | Erfassen der Daten zur Datenträgerkonfiguration
setcap "cap_dac_override,cap_dac_read_search,cap_fowner,cap_fsetid,cap_setuid,<br>cap_setpcap,cap_net_bind_service,cap_net_admin,cap_sys_chroot,cap_sys_admin,<br>cap_sys_resource,cap_audit_control,cap_setfcap=+eip" /sbin/lvm | Erfassen der Daten zur Datenträgerleistung
setcap CAP_DAC_READ_SEARCH+eip /usr/sbin/dmidecode | Erfassen der BIOS-Seriennummer
chmod a+r /sys/class/dmi/id/product_uuid | Erfassen der BIOS-GUID


## <a name="set-up-a-project"></a>Einrichten eines Projekts

Richten Sie ein neues Azure Migrate-Projekt ein.

1. Wählen Sie im Azure-Portal **Alle Dienste** aus, und suchen Sie nach **Azure Migrate**.
2. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
3. Wählen Sie unter **Übersicht** die Option **Create project** (Projekt erstellen) aus.
5. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
6. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

   ![Felder für Projektname und Region](./media/tutorial-discover-physical/new-project.png)

7. Klicken Sie auf **Erstellen**.
8. Warten Sie einige Minuten, bis das Azure Migrate-Projekt bereitgestellt wurde.

Das Tool **Azure Migrate- Serverbewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

![Seite mit dem standardmäßig hinzugefügten Serverbewertungstool](./media/tutorial-discover-physical/added-tool.png)


## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Die Einrichtung der Appliance umfasst Folgendes:
- Geben Sie einen Appliancenamen ein, und generieren Sie einen Azure Migrate-Projektschlüssel im Portal.
- Herunterladen einer gezippten Datei mit dem Azure Migrate-Installationsskript aus dem Azure-Portal.
- Extrahieren der Inhalte aus der gezippten Datei. Starten der PowerShell-Konsole mit Administratorrechten.
- Ausführen des PowerShell-Skripts zum Starten der Appliancewebanwendung.
- Führen Sie eine Erstkonfiguration für die Appliance aus, und registrieren Sie die Appliance beim Azure Migrate-Projekt unter Verwendung des Azure Migrate-Projektschlüssels.

### <a name="generate-the-azure-migrate-project-key"></a>Generieren des Azure Migrate-Projektschlüssels

1. Klicken Sie unter **Migrationsziele** > **Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf **Ermitteln**.
2. Wählen Sie unter **Computer ermitteln** > **Sind Ihre Computer virtualisiert?** die Option **Physisch/Andere (AWS, GCP, Xen usw.)** aus.
3. Geben Sie in **1: Generieren eines Azure Migrate-Projektschlüssels** einen Namen für die Azure Migrate-Appliance an, die Sie für die Ermittlung von physischen oder virtuellen Servern einrichten möchten. Der Name muss alphanumerisch sein und darf höchstens 14 Zeichen enthalten.
1. Klicken Sie auf **Schlüssel generieren**, um mit der Erstellung der erforderlichen Azure-Ressourcen zu beginnen. Schließen Sie die Seite „Computer ermitteln“ nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein **Azure Migrate-Projektschlüssel** generiert.
1. Kopieren Sie den Schlüssel, da Sie ihn benötigen, um die Registrierung der Appliance während der Konfiguration abzuschließen.

### <a name="download-the-installer-script"></a>Herunterladen des Installationsskripts

Klicken Sie in **2: Azure Migrate-Appliance herunterladen** auf **Herunterladen**.


### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung für die öffentliche Cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Beispielverwendung für die Government-Cloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip SHA256 ```
3.  Überprüfen Sie die aktuellen Applianceversionen und Hashwerte:
    - Öffentliche Cloud:

        **Szenario** | **Herunterladen** _ | _ *Hashwert**
        --- | --- | ---
        Physisch (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140334) | ce5e6f0507936def8020eb7b3109173dad60fc51dd39c3bd23099bc9baaabe29

    - Azure Government:

        **Szenario** | **Herunterladen** _ | _ *Hashwert**
        --- | --- | ---
        Physisch (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140338) | ae132ebc574caf231bf41886891040ffa7abbe150c8b50436818b69e58622276
 

### <a name="run-the-azure-migrate-installer-script"></a>Ausführen des Azure Migrate-Installationsskripts
Das Installationsskript führt folgende Schritte aus:

- Installation der Agents und einer Webanwendung für die Ermittlung und Bewertung physischer Server.
- Installation von Windows-Rollen, darunter beispielsweise Windows-Aktivierungsdienst, IIS und PowerShell ISE.
- Download und Installation eines wiederbeschreibbaren IIS-Moduls. [Weitere Informationen](https://www.microsoft.com/download/details.aspx?id=7435)
- Aktualisierung eines Registrierungsschlüssels (HKLM) mit dauerhaften Einstellungsdetails für Azure Migrate.
- Erstellung der folgenden Dateien in diesem Pfad:
    - **Konfigurationsdateien**: %Programdata%\Microsoft Azure\Config
    - **Protokolldateien**: %Programdata%\Microsoft Azure\Logs

Führen Sie das Skript wie folgt aus:

1. Extrahieren Sie die gezippte Datei in einem Ordner auf dem Server, der die Appliance hostet.  Führen Sie das Skript nicht auf einem Computer auf einer vorhandenen Azure Migrate-Appliance aus.
2. Starten Sie PowerShell auf dem oben genannten Server mit Administratorberechtigungen (erhöhten Rechten).
3. Ändern Sie das PowerShell-Verzeichnis in den Ordner, in den die Inhalte der gezippten Datei extrahiert wurden, die Sie heruntergeladen haben.
4. Führen Sie das Skript mit dem Namen **AzureMigrateInstaller.ps1** aus, indem Sie den folgenden Befehl ausführen:

    - Öffentliche Cloud: 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```
    - Azure Government: 
    
        ``` PS C:\Users\Administrators\Desktop\AzureMigrateInstaller-Server-USGov>.\AzureMigrateInstaller.ps1 ```

    Das Skript startet die Appliancewebanwendung, nachdem es erfolgreich ausgeführt wurde.

Bei Problemen können Sie zum Troubleshooting unter „C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Zeitstempel</em>.log“ auf die Skriptprotokolle zugreifen.



### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass die Appliance-VM eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch.

1. Öffnen Sie in einem Browser auf einem beliebigen Computer, der eine Verbindung mit der Appliance herstellen kann, die URL der Appliance-Web-App: **https://*Name oder IP-Adresse der Appliance*: 44368**.

   Alternativ können Sie auch auf dem Desktop auf die App-Verknüpfung klicken, um die App zu öffnen.
2. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
1. Gehen Sie in der Web-App unter **Erforderliche Komponenten einrichten** wie folgt vor:
    - **Konnektivität**: Die App überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
        - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse im Format http://ProxyIPAddress oder http://ProxyFQDN) und den überwachenden Port anzugeben.
        - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
        - Es werden nur HTTP-Proxys unterstützt.
        - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung erneut auszulösen.
    - **Uhrzeitsynchronisierung**: Die Uhrzeit wird überprüft. Die Uhrzeit der Appliance muss mit der Internetzeit synchronisiert werden, damit die Ermittlung ordnungsgemäß funktioniert.
    - **Updates installieren**: Azure Migrate-Serverbewertung prüft, ob die neuesten Updates für die Appliance installiert sind. Nachdem die Prüfung abgeschlossen ist, können Sie auf **Appliancedienste anzeigen** klicken, um den Status und die Versionen der auf der Appliance ausgeführten Komponenten anzuzeigen.

### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten **Azure Migrate-Projektschlüssel** ein. Wenn Sie den Schlüssel nicht haben, wechseln Sie zu **Serverbewertung > Ermitteln > Vorhandene Appliances verwalten**, wählen Sie den Appliancenamen aus, den Sie bei der Generierung des Schlüssels angegeben haben, und kopieren Sie den entsprechenden Schlüssel.
1. Klicken Sie auf **Anmelden**. Auf einer neuen Browserregisterkarte wird eine Azure-Anmeldeaufforderung geöffnet. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.
1. Melden Sie sich auf dem neuen Tab mit Ihrem Azure-Benutzernamen und -Kennwort an.
   
   Die Anmeldung mit einer PIN wird nicht unterstützt.
3. Kehren Sie nach erfolgreicher Anmeldung zur Web-App zurück. 
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen]() für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.


## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie nun eine Verbindung zwischen der Appliance und den zu ermittelnden physischen Servern her, und starten Sie die Ermittlung.

1. Wählen Sie in **Schritt 1: Geben Sie Anmeldeinformationen für die Ermittlung von physischen oder virtuellen Windows- und Linux-Servern** ein, klicken Sie auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, fügen Sie **Benutzername** und **Kennwort** für einen Windows- oder Linux-Server hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Es werden mehrere Anmeldeinformationen für die Ermittlung physischer Server unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu physischen oder virtuellen Servern** auf **Ermittlungsquelle hinzufügen**, um den **IP-Adresse/FQDN**-Wert des Servers und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Serverdetails über **CSV importieren** bereitzustellen.


    - Wenn Sie **ein einzelnes Element hinzufügen**, können Sie den Betriebssystemtyp auswählen, den Anzeigenamen für die Anmeldeinformationen angeben, den **IP-Adresse/FQDN**-Wert des Servers hinzufügen und dann auf **Speichern** klicken.
    - Wenn Sie **mehrere Elemente hinzufügen**, können Sie mehrere Datensätze auf einmal hinzufügen, indem Sie den **IP-Adresse/FQDN**-Wert des Servers mit dem Anzeigenamen für die Anmeldeinformationen im Textfeld angeben.  **Überprüfen** Sie die hinzugefügten Datensätze, und klicken Sie auf **Speichern**.
    - Wenn Sie **CSV importieren** _(standardmäßig ausgewählt)_ auswählen, können Sie eine CSV-Vorlagendatei herunterladen, die Datei mit dem **IP-Adresse/FQDN**-Wert des Servers und einem Anzeigenamen für die Anmeldeinformationen auffüllen. Importieren Sie die Datei dann in die Appliance, **überprüfen** Sie die Datensätze in der Datei, und klicken Sie auf **Speichern**.

1. Wenn Sie auf „Speichern“ klicken, versucht die Appliance, die Verbindung zu den hinzugefügten Servern zu überprüfen, und zeigt den **Überprüfungszustand** in der Tabelle für jeden Server an.
    - Sollte bei der Überprüfung eines Servers ein Fehler auftreten, sehen Sie sich den Fehler an, indem Sie auf **Fehler bei der Überprüfung** in der Spalte „Status“ klicken. Beheben Sie das Problem, und wiederholen Sie die Überprüfung.
    - Um einen Server zu entfernen, klicken Sie auf **Löschen**.
1. Sie können die Verbindung zu Servern jederzeit vor Beginn der Ermittlung **erneut überprüfen**.
1. Klicken Sie auf **Ermittlung starten**, um die Ermittlung der erfolgreich überprüften Server zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus für jeden Server in der Tabelle überprüfen.


Daraufhin wird die Ermittlung gestartet. Es dauert ca. 2 Minuten pro Server, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.

## <a name="verify-servers-in-the-portal"></a>Überprüfen von Servern im Portal

Nach Abschluss der Ermittlung können Sie überprüfen, ob die Server im Portal angezeigt werden.

1. Öffnen Sie das Azure Migrate-Dashboard.
2. Klicken Sie unter **Azure Migrate – Server** > **Azure Migrate: Server Assessment** (Azure Migrate-Serverbewertung) auf das Symbol mit der Anzahl für **Ermittelte Server**.
## <a name="next-steps"></a>Nächste Schritte

- [Bewerten Sie physische Server](tutorial-assess-physical.md) für die Migration zu Azure.
- [Informieren Sie sich über die Daten](migrate-appliance.md#collected-data---physical), die von der Appliance im Zuge der Ermittlung gesammelt werden.