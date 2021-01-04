---
title: Einrichten einer Azure Migrate-Appliance für physische Server
description: Erfahren Sie, wie eine Azure Migrate-Appliance für die Bewertung physischer Server eingerichtet wird.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 04/15/2020
ms.openlocfilehash: 21d40b1962a3e4000876686659b76d019991785d
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751681"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>Einrichten einer Appliance für physische Server

In diesem Artikel wird beschrieben, wie Sie die Azure Migrate-Appliance einrichten, wenn Sie physische Server mithilfe der Azure Migrate-Serverbewertung bewerten.

Die Azure Migrate-Appliance ist eine einfache Appliance, die von der Azure Migrate-Serverbewertung für folgende Aufgaben verwendet wird:

- Ermitteln lokaler Server
- Senden von Meta- und Leistungsdaten für ermittelte Server an die Azure Migrate-Serverbewertung

[Erfahren Sie mehr](migrate-appliance.md) über die Azure Migrate-Appliance.


## <a name="appliance-deployment-steps"></a>Schritte für die Appliancebereitstellung

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

   ![Auswahloptionen für „Computer ermitteln“](./media/tutorial-assess-physical/servers-discover.png)


   ![Auswahloptionen für „Schlüssel generieren“](./media/tutorial-assess-physical/generate-key-physical.png)

### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor der Bereitstellung, dass die gezippte Datei sicher ist.

1. Öffnen Sie auf dem Computer, auf den Sie die Datei heruntergeladen haben, ein Administratorbefehlsfenster.
2. Führen Sie den folgenden Befehl aus, um den Hash für die gezippte Datei zu generieren:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Beispielverwendung für die öffentliche Cloud: ```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public.zip SHA256 ```
    - Beispielverwendung für die Government-Cloud: ```  C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-USGov.zip MD5 ```
3.  Überprüfen Sie die aktuelle Version der Appliance und die Einstellungen für [Hashwerte](tutorial-discover-physical.md#verify-security).
 

## <a name="run-the-azure-migrate-installer-script"></a>Ausführen des Azure Migrate-Installationsskripts
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
4. Wenn das für die Protokollierung verwendete Azure-Benutzerkonto über die richtigen [Berechtigungen](./tutorial-discover-physical.md) für die während der Schlüsselgenerierung erstellten Azure-Ressourcen verfügt, wird die Registrierung der Appliance initiiert.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie die Registrierungsdetails anzeigen, indem Sie auf **Details anzeigen** klicken.


## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Stellen Sie nun eine Verbindung zwischen der Appliance und den zu ermittelnden physischen Servern her, und starten Sie die Ermittlung.

1. Wählen Sie in **Schritt 1: Geben Sie Anmeldeinformationen für die Ermittlung von physischen oder virtuellen Windows- und Linux-Servern** ein, klicken Sie auf **Anmeldeinformationen hinzufügen**, um einen Anzeigenamen für die Anmeldeinformationen anzugeben, fügen Sie **Benutzername** und **Kennwort** für einen Windows- oder Linux-Server hinzu. Klicken Sie auf **Speichern**.
1. Wenn Sie mehrere Anmeldeinformationen gleichzeitig hinzufügen möchten, klicken Sie auf **Weitere hinzufügen**, um die Angeben zu speichern und weitere Anmeldeinformationen hinzuzufügen. Es werden mehrere Anmeldeinformationen für die Ermittlung physischer Server unterstützt.
1. Klicken Sie in **Schritt 2: Bereitstellen von Details zu physischen oder virtuellen Servern** auf **Ermittlungsquelle hinzufügen**, um den **IP-Adresse/FQDN**-Wert des Servers und den Anzeigenamen für Anmeldeinformationen zum Herstellen einer Verbindung mit dem Server anzugeben.
1. Sie können entweder jeweils **ein einzelnes Element** oder **mehrere Elemente** in einem Schritt hinzufügen. Es besteht auch die Möglichkeit, Serverdetails über **CSV importieren** bereitzustellen.

    ![Auswahloptionen zum Hinzufügen der Ermittlungsquelle](./media/tutorial-assess-physical/add-discovery-source-physical.png)

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

Testen Sie die [Bewertung physischer Server](tutorial-assess-physical.md) mit der Azure Migrate-Serverbewertung.