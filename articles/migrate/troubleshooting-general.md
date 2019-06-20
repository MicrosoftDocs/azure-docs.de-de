---
title: Behandeln von Problemen bei Azure Migrate | Microsoft-Dokumentation
description: Bietet eine Übersicht über bekannte Probleme im Azure Migrate-Dienst und Problembehandlungstipps für häufige Fehler.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: dff3c96cf3ac8eea7c1160ee1834cc70390c0333
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60533249"
---
# <a name="troubleshoot-azure-migrate"></a>Problembehandlung für Azure Migrate

## <a name="troubleshoot-common-errors"></a>Problembehandlung für häufige Fehler

[Azure Migrate](migrate-overview.md) bewertet lokale Workloads für die Migration zu Azure. Verwenden Sie diesen Artikel zur Problembehandlung bei der Bereitstellung und Verwendung von Azure Migrate.

### <a name="i-am-using-the-ova-that-continuously-discovers-my-on-premises-environment-but-the-vms-that-are-deleted-in-my-on-premises-environment-are-still-being-shown-in-the-portal"></a>Ich verwende die OVA, die kontinuierlich meine lokale Umgebung ermittelt, aber die VMs, die in meiner lokalen Umgebung gelöscht wurden, werden immer noch im Portal angezeigt.

Die Appliance für die kontinuierliche Ermittlung sammelt nur kontinuierlich Leistungsdaten. Sie erkennt keine Konfigurationsänderungen in der lokalen Umgebung (etwa das Hinzufügen von VMs, Löschvorgänge, hinzugefügte Datenträger und Ähnliches). Wenn sich die Konfiguration in der lokalen Umgebung ändert, können Sie wie folgt vorgehen, damit die Änderungen im Portal berücksichtigt werden:

- Hinzugefügte Elemente (virtuelle Computer, Datenträger, Kerne usw.): Damit diese Änderungen im Azure-Portal berücksichtigt werden, können Sie die Ermittlung über die Appliance beenden und anschließend wieder starten. Dadurch wird sichergestellt, dass die Änderungen im Azure Migrate-Projekt aktualisiert werden.

   ![Beenden der Ermittlung](./media/troubleshooting-general/stop-discovery.png)

- Gelöschte virtuelle Computer: Das Löschen von virtuellen Computern wird aufgrund des Entwurfs der Appliance auch dann nicht berücksichtigt, wenn Sie die Ermittlung beenden und wieder starten. Das liegt daran, dass Daten nachfolgender Ermittlungen älteren Ermittlungen angefügt und nicht überschrieben werden. In diesem Fall können Sie die VM im Portal einfach ignorieren, indem Sie sie aus Ihrer Gruppe entfernen und die Bewertung neu berechnen.

### <a name="deletion-of-azure-migrate-projects-and-associated-log-analytics-workspace"></a>Löschen von Azure Migrate-Projekten und zugehörigen Log Analytics-Arbeitsbereichen

Wenn Sie ein Azure Migrate-Projekt löschen, wird das Migrationsprojekt zusammen mit allen Gruppen und Bewertungen gelöscht. Wenn Sie dem Projekt jedoch einen Log Analytics-Arbeitsbereich angefügt hatten, wird dieser nicht automatisch gelöscht. Dies liegt daran, dass derselbe Log Analytics-Arbeitsbereich für mehrere Anwendungsfälle verwendet werden kann. Wenn Sie den Log Analytics-Arbeitsbereich ebenfalls löschen möchten, müssen Sie dies manuell tun.

1. Navigieren Sie zu dem Log Analytics-Arbeitsbereich, der dem Projekt angefügt ist.
   a. Wenn Sie das Migrationsprojekt noch nicht gelöscht haben, finden den Link zum Arbeitsbereich auf der Übersichtsseite des Projekts im Abschnitt "Essentials".

   ![LA-Arbeitsbereich](./media/troubleshooting-general/LA-workspace.png)

   b. Wenn Sie das Migrationsprojekt bereits gelöscht haben, klicken Sie im Azure-Portal im linken Bereich auf **Ressourcengruppen**, und wechseln Sie zu der Ressourcengruppe, in der der Arbeitsbereich erstellt wurde, und navigieren Sie dann zu diesem.
2. Befolgen Sie die Anweisungen [in diesem Artikel](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace), um den Arbeitsbereich zu löschen.

### <a name="migration-project-creation-failed-with-error-requests-must-contain-user-identity-headers"></a>Erstellen des Migrationsprojekts mit dem Fehler *Anforderungen müssen Benutzeridentitätsheader enthalten*

Dieses Problem kann für Benutzer auftreten, die nicht über Zugriff auf den Azure Active Directory (Azure AD)-Mandanten der Organisation verfügen. Wenn ein Benutzer zum ersten Mal zu einem Azure AD-Mandanten hinzugefügt wird, erhält er eine E-Mail-Einladung, dem Mandanten beizutreten. Benutzer müssen die E-Mail aufrufen und die Einladung annehmen, um erfolgreich zum Mandanten hinzugefügt zu werden. Wenn Sie die E-Mail nicht sehen können, wenden Sie sich an einen Benutzer, der bereits auf den Mandanten zugreifen kann, und bitten Sie ihn, die Einladung anhand der [hier](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator#resend-invitations-to-guest-users) angegebenen Schritte erneut zu senden.

Wenn Sie die Einladung per E-Mail erhalten haben, öffnen Sie die diese und klicken Sie auf den darin angegebenen Link, um die Einladung anzunehmen. Sobald dies geschehen ist, müssen Sie sich beim Azure-Portal ab- und erneut anmelden. Das Aktualisieren des Browsers allein funktioniert nicht. Sie können dann versuchen, das Migrationsprojekt zu erstellen.

### <a name="i-am-unable-to-export-the-assessment-report"></a>Ich kann den Bewertungsbericht nicht exportieren

Wenn Sie den Bewertungsbericht nicht aus dem Portal exportieren können, versuchen Sie es mit der folgenden REST-API, um eine Download-URL für den Bewertungsbericht abzurufen.

1. Installieren Sie *armclient* auf Ihrem Computer (sofern nicht bereits geschehen):

   a. Führen Sie in einem Eingabeaufforderungsfenster mit Administratorrechten den folgenden Befehl aus: ```@powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"```

   b. Führen Sie in einem Windows PowerShell-Fenster mit Administratorrechten den folgenden Befehl aus: ```choco install armclient```

2. Abrufen der Download-URL für den Bewertungsbericht mithilfe der Azure Migrate REST-API

   a.    Führen Sie in einem Windows PowerShell-Fenster mit Administratorrechten den folgenden Befehl aus: ```armclient login```

        This opens the Azure login pop-up where you need to sign in to Azure.

   b.    Führen Sie im selben PowerShell-Fenster den folgenden Befehl aus, um die Download-URL für den Bewertungsbericht abzurufen (ersetzen Sie die URI-Parameter durch die entsprechenden Werten; API-Beispielanforderung weiter unter).

       ```armclient POST https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Migrate/projects/{projectName}/groups/{groupName}/assessments/{assessmentName}/downloadUrl?api-version=2018-02-02```

      Beispielanforderung und -ausgabe:

      ```PS C:\WINDOWS\system32> armclient POST https://management.azure.com/subscriptions/8c3c936a-c09b-4de3-830b-3f5f244d72e9/r
   esourceGroups/ContosoDemo/providers/Microsoft.Migrate/projects/Demo/groups/contosopayroll/assessments/assessment_11_16_2
   018_12_16_21/downloadUrl?api-version=2018-02-02
   {
   "assessmentReportUrl": "https://migsvcstoragewcus.blob.core.windows.net/4f7dddac-f33b-4368-8e6a-45afcbd9d4df/contosopayrollassessment_11_16_2018_12_16_21?sv=2016-05-31&sr=b&sig=litQmHuwi88WV%2FR%2BDZX0%2BIttlmPMzfVMS7r7dULK7Oc%3D&st=2018-11-20T16%3A09%3A30Z&se=2018-11-20T16%3A19%3A30Z&sp=r",
   "expirationTime": "2018-11-20T22:09:30.5681954+05:30"```

3. Kopieren Sie die URL aus der Antwort, und öffnen Sie sie in einem Browser um den Bewertungsbericht herunterzuladen.

4. Sobald der Bericht heruntergeladen wurde, verwenden Sie Excel, um zu dem heruntergeladenen Ordner zu wechseln, und öffnen Sie die Datei in Excel, um sie anzuzeigen.

### <a name="performance-data-for-cpu-memory-and-disks-is-showing-up-as-zeroes"></a>Leistungsdaten für CPU, Arbeitsspeicher und Datenträger werden als Nullen angezeigt.

Azure Migrate erstellt fortlaufend Profile der lokalen Umgebung, um Leistungsdaten der lokalen virtuellen Computer zu sammeln. Wenn Sie die Ermittlung in Ihrer Umgebung gerade erst gestartet haben, müssen Sie mindestens einen Tag warten, damit zunächst Leistungsdaten gesammelt werden können. Wenn eine Bewertung erstellt wird, ohne einen Tag zu warten, werden die Leistungsmetriken mit Nullwerten angezeigt. Nachdem Sie einen Tag gewartet haben, können Sie eine neue Bewertung erstellen oder die vorhandene Bewertung über die Option „Neue Bewertung“ im Bewertungsbericht aktualisieren.

### <a name="i-specified-an-azure-geography-while-creating-a-migration-project-how-do-i-find-out-the-exact-azure-region-where-the-discovered-metadata-would-be-stored"></a>Ich habe beim Erstellen eines Migrationsprojekts eine Azure-Geografie angegeben. Wie ermittle ich die exakte Azure-Region, in der die erkannten Metadaten gespeichert werden?

Sie können zum Abschnitt **Grundlagen** auf der Seite **Übersicht** des Projekts navigieren, um den exakten Speicherort der Metadaten zu ermitteln. Der Speicherort wird von Azure Migrate zufällig innerhalb der Geografie ausgewählt und kann nicht geändert werden. Wenn Sie ein Projekt nur in einer bestimmten Region erstellen möchten, können Sie das Migrationsprojekt mit den REST-APIs erstellen und die gewünschte Region übergeben.

   ![Projektspeicherort](./media/troubleshooting-general/geography-location.png)

## <a name="collector-issues"></a>Collector-Probleme

### <a name="deployment-of-azure-migrate-collector-failed-with-the-error-the-provided-manifest-file-is-invalid-invalid-ovf-manifest-entry"></a>Die Bereitstellung von Azure Migrate-Collector schlägt mit folgendem Fehler fehl: Die angegebene Manifestdatei ist ungültig: Ungültiger OVF-Manifesteintrag.

1. Überprüfen Sie, ob die OVA-Datei für Azure Migrate-Collector ordnungsgemäß heruntergeladen wird, indem Sie deren Hashwert überprüfen. Informationen zum Überprüfen des Hashwerts finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/migrate/tutorial-assessment-vmware#verify-the-collector-appliance). Wenn der Hashwert nicht übereinstimmt, laden Sie die OVA-Datei erneut herunter, und wiederholen Sie die Bereitstellung.
2. Wenn der Fehler weiterhin auftritt und Sie zum Bereitstellen der OVF-Datei VMware vSphere-Client verwenden, führen Sie die Bereitstellung über den vSphere-Webclient aus. Wenn der Fehler weiterhin auftritt, versuchen Sie es mit einem anderen Webbrowser.
3. Wenn Sie den vSphere-Webclient und die Bereitstellung auf vCenter Server 6.5 oder 6.7 ausführen möchten, stellen Sie die OVA-Datei mit den folgenden Schritten direkt auf dem ESXi-Host bereit:
   - Stellen Sie mithilfe des Webclients (https://<*Host-IP-Adresse*>/ui) eine direkte Verbindung mit dem ESXi-Host her (anstelle von vCenter Server).
   - Wechseln Sie zu „Startseite > Inventar“.
   - Klicken Sie auf „Datei“ > „Deploy OVF template“ (OVF-Vorlage bereitstellen). Navigieren Sie zur OVA-Datei, und stellen Sie die Bereitstellung fertig.
4. Wenn weiterhin ein Fehler bei der Bereitstellung auftritt, wenden Sie sich an den Azure Migrate-Support.

### <a name="unable-to-select-the-azure-cloud-in-the-appliance-fails-with-error-azure-cloud-selection-failed"></a>Die Azure Cloud kann in der Appliance nicht ausgewählt werden, der Fehler „Fehler beim Auswählen der Azure-Cloud“ tritt auf.

Dies ist ein bekanntes Problem und eine Lösung für das Problem ist verfügbar. Laden Sie die [neuesten Upgrade-Teile](https://docs.microsoft.com/azure/migrate/concepts-collector-upgrade#continuous-discovery-upgrade-versions) für die Appliance herunter, und aktualisieren Sie die Appliance, um die Korrektur anzuwenden.

### <a name="collector-is-not-able-to-connect-to-the-internet"></a>Collector kann keine Verbindung mit dem Internet herstellen

Dies kann passieren, wenn sich der Computer, den Sie verwenden, hinter einem Proxy befindet. Stellen Sie sicher, dass Sie die Anmeldeinformationen für die Autorisierung angeben, wenn der Proxy diese benötigt.
Wenn Sie URL-basierte Firewallproxys zur Steuerung ausgehender Verbindungen verwenden, stellen Sie sicher, dass die folgenden erforderlichen Dienst-URLs zur Whitelist hinzugefügt werden:

**URL** | **Zweck**  
--- | ---
*.portal.azure.com | Erforderlich, um die Konnektivität mit dem Azure-Dienst zu überprüfen und Probleme mit der Zeitsynchronisation zu überprüfen.
*.oneget.org | Erforderlich, um das auf PowerShell basierende vCenter PowerCLI-Modul herunterzuladen.

**Der Collector kann aufgrund von Fehlern bei der Zertifikatüberprüfung keine Internetverbindung herstellen.**

Dieses Problem kann auftreten, wenn Sie einen abfangenden Proxy verwenden, um eine Internetverbindung herzustellen, und auf dem virtuellen Collectorcomputer das Proxyzertifikat nicht importiert haben. Eine Anleitung zum Importieren des Proxyzertifikats finden Sie [hier](https://docs.microsoft.com/azure/migrate/concepts-collector).

**Der Collector kann mit der Projekt-ID und dem Schlüssel, die ich aus dem Portal kopiert habe, keine Verbindung mit dem Projekt herstellen.**

Stellen Sie sicher, dass Sie die richtigen Informationen kopiert und eingefügt haben. Installieren Sie zur Problembehandlung Microsoft Monitoring Agent (MMA), und vergewissern Sie sich, dass MMA eine Verbindung mit dem Projekt herstellen kann. Gehen Sie dazu wie folgt vor:

1. Laden Sie [MMA](https://go.microsoft.com/fwlink/?LinkId=828603) auf den virtuellen Collector-Computer herunter.
2. Doppelklicken Sie auf die heruntergeladenen Datei, um die Installation zu starten.
3. Klicken Sie im Setup auf der Seite **Willkommen** auf **Weiter**. Klicken Sie auf der Seite **Lizenzbedingungen** auf **Ich stimme zu**, um die Lizenzbedingungen zu akzeptieren.
4. Behalten Sie unter **Zielordner** den Standardinstallationsordner bei, oder ändern Sie ihn, und klicken Sie anschließend auf **Weiter**.
5. Wählen Sie unter **Agent-Setupoptionen** die Optionen **Azure Log Analytics** > **Weiter**.
6. Klicken Sie auf **Hinzufügen**, um einen neuen Log Analytics-Arbeitsbereich hinzuzufügen. Fügen Sie die Projekt-ID und den Schlüssel ein, die Sie kopiert haben. Klicken Sie auf **Weiter**.
7. Stellen Sie sicher, dass der Agent eine Verbindung mit dem Projekt herstellen kann. Ist dies nicht der Fall, überprüfen Sie die Einstellungen. Wenn der Agent eine Verbindung herstellen kann, der Collector jedoch nicht, wenden Sie sich an den Support.


### <a name="error-802-date-and-time-synchronization-error"></a>Fehler 802: Fehler bei der Datums- und Zeitsynchronisierung

Möglicherweise weicht die Serveruhr um mehr als fünf Minuten von der aktuellen Uhrzeit ab. Passen Sie die Uhrzeit auf dem virtuellen Collector-Computer folgendermaßen an die aktuelle Uhrzeit an:

1. Öffnen Sie auf dem virtuellen Computer eine Administratoreingabeaufforderung.
2. Führen Sie „w32tm /tz“ aus, um die Zeitzone zu überprüfen.
3. Führen Sie „w32tm /resync“ aus, um die Zeit zu synchronisieren.

### <a name="vmware-powercli-installation-failed"></a>VMware PowerCLI-Installation fehlgeschlagen

Azure Migrate-Collector lädt PowerCLI herunter und installiert sie auf der Appliance. Ein Fehler bei der PowerCLI-Installation kann durch nicht erreichbare Endpunkte für das PowerCLI-Repository verursacht werden. Um das Problem zu beheben, versuchen Sie, PowerCLI anhand der folgenden Schritte manuell auf der Collector-VM zu installieren:

1. Öffnen Sie Windows PowerShell im Administratormodus.
2. Wechseln Sie zum Verzeichnis „C:\Programme\ProfilerService\VMWare\Scripts\“.
3. Führen Sie das Skript „InstallPowerCLI.ps1“ aus.

### <a name="error-unhandledexception-internal-error-occurred-systemiofilenotfoundexception"></a>Fehler: interner UnhandledException-Fehler: System.IO.FileNotFoundException

Dieses Problem kann aufgrund eines Problems mit der VMware PowerCLI-Installation auftreten. Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Falls Sie nicht die aktuelle Version der Collectorappliance verwenden, aktualisieren Sie Ihren Collector auf die [aktuelle Version](https://aka.ms/migrate/col/checkforupdates), und überprüfen Sie, ob das Problem dadurch behoben wurde.
2. Wenn Sie bereits über die aktuelle Collectorversion verfügen, führen Sie die folgenden Schritte aus, um eine Neuinstallation von PowerCLI vorzunehmen:

   a. Schließen Sie den Webbrowser in der Appliance.

   b. Beenden Sie den Dienst „Azure Migrate-Collector“ in Windows Service Manager (öffnen Sie „Ausführen“, und geben Sie „services.msc“ ein, um Windows Service Manager zu öffnen). Klicken Sie mit der rechten Maustaste auf den Dienst „Azure Migrate-Collector“, und klicken Sie auf „Beenden“.

   c. Löschen Sie an den folgenden Speicherorten alle Ordner, die mit „VMware“ beginnen: C:\Programme\WindowsPowerShell\Modules  
        C:\Programme (x86)\WindowsPowerShell\Modules

   d. Starten Sie den Dienst „Azure Migrate-Collector“ in Windows Service Manager neu (öffnen Sie „Ausführen“, und geben Sie „services.msc“ ein, um Windows Service Manager zu öffnen). Klicken Sie mit der rechten Maustaste auf den Dienst „Azure Migrate-Collector“, und klicken Sie auf „Starten“.

   e. Doppelklicken Sie auf die Desktopverknüpfung „Collector ausführen“, um die Collectoranwendung zu starten. Die Collectoranwendung sollte die erforderliche Version von PowerCLI automatisch herunterladen und installieren.

3. Wenn das Problem durch die oben genannten Schritte nicht behoben wird, führen Sie die Schritte a bis c oben aus, und installieren Sie PowerCLI anschließend manuell auf der Appliance, indem Sie die folgenden Schritte ausführen:

   a. Bereinigen Sie alle unvollständigen Installationsdateien von PowerCLI, indem Sie die Schritte a bis c oben in Schritt 2 ausführen.

   b. Navigieren Sie zu „Start“ > „Ausführen“ > „Windows PowerShell als Administrator öffnen“.

   c. Führen Sie den folgenden Befehl aus:  Install-Module "VMWare.VimAutomation.Core" -RequiredVersion "6.5.2.6234650" (Geben Sie „A“ ein, wenn Sie zur Bestätigung aufgefordert werden.)

   d. Starten Sie den Dienst „Azure Migrate-Collector“ in Windows Service Manager neu (öffnen Sie „Ausführen“, und geben Sie „services.msc“ ein, um Windows Service Manager zu öffnen). Klicken Sie mit der rechten Maustaste auf den Dienst „Azure Migrate-Collector“, und klicken Sie auf „Starten“.

   e. Doppelklicken Sie auf die Desktopverknüpfung „Collector ausführen“, um die Collectoranwendung zu starten. Die Collectoranwendung sollte die erforderliche Version von PowerCLI automatisch herunterladen und installieren.

4. Wenn Sie dieses Modul aufgrund von Firewallproblemen nicht auf die Appliance herunterladen können, führen Sie Download und Installation auf einem Computer mit Internetzugriff aus, und befolgen Sie dabei die folgenden Schritte:

    a. Bereinigen Sie alle unvollständigen Installationsdateien von PowerCLI, indem Sie die Schritte a bis c oben in Schritt 2 ausführen.

    b. Navigieren Sie zu „Start“ > „Ausführen“ > „Windows PowerShell als Administrator öffnen“.

    c. Führen Sie den folgenden Befehl aus:  Install-Module "VMWare.VimAutomation.Core" -RequiredVersion "6.5.2.6234650" (Geben Sie „A“ ein, wenn Sie zur Bestätigung aufgefordert werden.)

    d. Kopieren Sie alle Module, die mit „VMware“ beginnen, aus „C:\Programme (x86) \WindowsPowerShell\Modules“ an den gleichen Speicherort auf der Collector-VM.

    e. Starten Sie den Dienst „Azure Migrate-Collector“ in Windows Service Manager neu (öffnen Sie „Ausführen“, und geben Sie „services.msc“ ein, um Windows Service Manager zu öffnen). Klicken Sie mit der rechten Maustaste auf den Dienst „Azure Migrate-Collector“, und klicken Sie auf „Starten“.

    f. Doppelklicken Sie auf die Desktopverknüpfung „Collector ausführen“, um die Collectoranwendung zu starten. Die Collectoranwendung sollte die erforderliche Version von PowerCLI automatisch herunterladen und installieren.

### <a name="error-unabletoconnecttoserver"></a>Fehler: UnableToConnectToServer

Wegen folgendem Fehler kann keine Verbindung mit vCenter Server „Servername.com:9443“ hergestellt werden: Unter https://Servername.com:9443/sdk hat kein Endpunkt gelauscht, der die Nachricht akzeptieren konnte.

Überprüfen Sie, ob die neueste Version der Collectorappliance ausgeführt wird. Wenn das nicht der Fall ist, aktualisieren Sie auf die [neueste Version](https://docs.microsoft.com/azure/migrate/concepts-collector).

Wenn das Problem auch bei der neuesten Version auftritt, kann der Collectorcomputer den angegebenen vCenter Server-Namen möglicherweise nicht auflösen oder der angegebene Port ist falsch. Wenn der Port nicht angegeben ist, versucht Collector standardmäßig, eine Verbindung mit Port 443 herzustellen.

1. Versuchen Sie, „Servername.com“ vom Collectorcomputer aus per Ping zu erreichen.
2. Wenn Schritt 1 nicht erfolgreich ist, können Sie versuchen, über die IP-Adresse eine Verbindung mit vCenter Server herzustellen.
3. Ermitteln Sie die richtige Portnummer für die Verbindungsherstellung mit vCenter.
4. Überprüfen Sie abschließend, ob vCenter Server ausgeführt wird und betriebsbereit ist.

### <a name="antivirus-exclusions"></a>Antivirenausschlüsse

Um die Azure Migrate Appliance zu härten, müssen Sie die folgenden Ordner in der Appliance von Antivirenscans ausschließen:

- Ordner mit den Binärdateien für den Azure Migrate-Dienst. Schließen Sie alle Unterordner aus.
  %ProgramFiles%\ProfilerService  
- Azure Migrate-Webanwendung. Schließen Sie alle Unterordner aus.
  %SystemDrive%\inetpub\wwwroot
- Lokaler Cache für Datenbank- und Protokolldateien. Der Azure Migrate-Dienst benötigt Lese-/Schreibzugriff auf diesen Ordner.
  %SystemDrive%\Profiler

## <a name="dependency-visualization-issues"></a>Probleme bei der Visualisierung von Abhängigkeiten

### <a name="i-am-unable-to-find-the-dependency-visualization-functionality-for-azure-government-projects"></a>Ich kann die Funktion zur Visualisierung von Abhängigkeiten für Azure Government-Projekte nicht finden.

Azure Migrate hängt hinsichtlich der Visualisierungsfunktion von der Dienstzuordnung ab, und da die Dienstzuordnung in Azure Government zurzeit nicht verfügbar ist, ist diese Funktionalität auch nicht in Azure Government verfügbar.

### <a name="i-installed-the-microsoft-monitoring-agent-mma-and-the-dependency-agent-on-my-on-premises-vms-but-the-dependencies-are-now-showing-up-in-the-azure-migrate-portal"></a>Ich habe Microsoft Monitoring Agent (MMA) und den Dependency-Agent auf meinen lokalen virtuellen Computern installiert. Jetzt werden die Abhängigkeiten jedoch im Azure Migrate-Portal angezeigt.

Nachdem Sie die Agents installiert haben, benötigt Azure Migrate üblicherweise 15 bis 30 Minuten, um die Abhängigkeiten im Portal anzuzeigen. Wenn Sie bereits länger als 30 Minuten warten, stellen Sie sicher, dass MMA mit dem OMS-Arbeitsbereich kommunizieren kann. Gehen Sie hierzu wie folgt vor:

Bei einer virtuellen Windows-VM:
1. Wechseln Sie zur **Systemsteuerung**, und starten Sie **Microsoft Monitoring Agent**
2. Rufen Sie im Popup zu den MMA-Eigenschaften die Registerkarte **Azure Log Analytics (OMS)** auf.
3. Stellen Sie sicher, dass der **Status** für den Arbeitsbereich grün angezeigt wird.
4. Wenn der Status nicht grün angezeigt wird, entfernen Sie den Arbeitsbereich, und fügen Sie ihn zu MMA neu hinzu.
        ![MMA-Status](./media/troubleshooting-general/mma-status.png)

Überprüfen Sie bei einer Linux-VM, ob die Installationsbefehle für MMA und den Dependency-Agent erfolgreich ausgeführt wurden.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Welche Betriebssysteme werden von MMA unterstützt?

Die Liste der von MMA unterstützten Windows-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Die Liste der von MMA unterstützten Linux-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Welche Betriebssysteme werden vom Dependency-Agent unterstützt?

Die Liste der vom Dependency-Agent unterstützten Windows-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Die Liste der vom Dependency-Agent unterstützten Linux-Betriebssysteme finden Sie [hier](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="i-am-unable-to-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Ich kann Abhängigkeiten in Azure Migrate nicht länger als eine Stunde visualisieren.
Azure Migrate ermöglicht Ihnen das Visualisieren von Abhängigkeiten für einen Zeitraum von bis zu einer Stunde. Mit Azure Migrate können Sie zwar um bis zu einen Monat zu einem bestimmten Datum im Verlauf zurückgehen. Die Abhängigkeiten können Sie jedoch nur für einen Zeitraum von maximal einer Stunde visualisieren. So können Sie beispielsweise mithilfe der Zeitraumfunktionalität im Abhängigkeitsdiagramm Abhängigkeiten für gestern, jedoch nur für ein Zeitfenster von einer Stunde anzeigen. Sie können jedoch zum [Abfragen der Abhängigkeitsdaten](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) über einen längeren Zeitraum Azure Monitor-Protokolle verwenden.

### <a name="i-am-unable-to-visualize-dependencies-for-groups-with-more-than-10-vms"></a>Für Gruppen mit mehr als 10 virtuellen Computern kann ich keine Abhängigkeiten visualisieren.
Sie können [Abhängigkeiten für Gruppen visualisieren](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies), die aus bis zu zehn VMs bestehen. Falls eine Gruppe aus mehr als zehn VMs besteht, sollten Sie die Gruppe in kleinere Gruppen aufteilen und die Abhängigkeiten anschließend visualisieren.

### <a name="i-installed-agents-and-used-the-dependency-visualization-to-create-groups-now-post-failover-the-machines-show-install-agent-action-instead-of-view-dependencies"></a>Ich habe Agents installiert und die Visualisierung von Abhängigkeiten verwendet, um Gruppen zu erstellen. Jetzt zeigen die Computer nach dem Failover die Aktion „Agent installieren“ anstelle von „Abhängigkeiten anzeigen“ an.
* Nach einem geplanten oder ungeplanten Failover werden lokale Computer abgeschaltet, und entsprechende Computer in Azure werden hochgefahren. Diese Computer beziehen eine andere MAC-Adresse. Je nachdem, ob der Benutzer die lokale IP-Adresse beibehalten möchte oder nicht, beziehen sie möglicherweise auch eine andere IP-Adresse. Wenn sich sowohl MAC- als auch IP-Adressen unterscheiden, ordnet Azure Migrate die lokalen Computern keinen Dienstzuordnungs-Abhängigkeitsdaten zu und fordert den Benutzer auf, Agents zu installieren, anstatt Abhängigkeiten anzuzeigen.
* Nach dem Testfailover bleiben die lokalen Computer erwartungsgemäß eingeschaltet. Entsprechende Computer, die in Azure hochgefahren wurden, erhalten andere MAC-Adressen und u.U. auch andere IP-Adressen. Sofern der Benutzer ausgehenden Datenverkehr von Azure Monitor-Protokolle von diesen Computern nicht sperrt, ordnet Azure Migrate die lokalen Computer keinen Dienstzuordnungs-Abhängigkeitsdaten zu und fordert den Benutzer auf, Agents zu installieren, anstatt Abhängigkeiten anzuzeigen.

## <a name="troubleshoot-azure-readiness-issues"></a>Behandeln von Problemen bei der Azure-Bereitschaft

**Problem** | **Behebung**
--- | ---
Nicht unterstützter Starttyp | Azure unterstützt keine virtuellen Computer mit dem Starttyp „EFI“. Vor einer Migration muss der Starttyp in „BIOS“ konvertiert werden. <br/><br/>Die entsprechenden virtuellen Computer können mit [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/tutorial-migrate-on-premises-to-azure) migriert werden, da Azure Site Recovery den Starttyp des virtuellen Computers im Rahmen der Migration in „BIOS“ konvertiert.
Bedingt unterstütztes Windows-Betriebssystem | Der Unterstützungszeitraum für das Betriebssystem ist abgelaufen. Für die [Unterstützung in Azure](https://aka.ms/WSosstatement) wird eine benutzerdefinierte Supportvereinbarung (Custom Support Agreement, CSA) benötigt. Führen Sie vor der Migration ggf. ein Upgrade des Betriebssystems durch.
Nicht unterstütztes Windows-Betriebssystem | Azure unterstützt nur [bestimmte Windows-Betriebssystemversionen](https://aka.ms/WSosstatement). Führen Sie vor der Migration zu Azure ggf. ein Upgrade des Betriebssystems durch.
Bedingt unterstütztes Linux-Betriebssystem | Azure unterstützt nur [bestimmte Linux-Betriebssystemversionen](../virtual-machines/linux/endorsed-distros.md). Führen Sie vor der Migration zu Azure ggf. ein Upgrade des Betriebssystems durch.
Nicht unterstütztes Linux-Betriebssystem | Der Computer startet zwar unter Umständen in Azure, das Betriebssystem wird von Azure jedoch nicht unterstützt. Führen Sie vor der Migration zu Azure ggf. ein Upgrade auf eine [unterstützte Linux-Version](../virtual-machines/linux/endorsed-distros.md) durch.
Unbekanntes Betriebssystem | Das Betriebssystem des virtuellen Computers wurde in vCenter Server als „Anderes“ angegeben, und Azure Migrate kann deshalb nicht beurteilen, ob der virtuelle Computer für Azure bereit ist. Vergewissern Sie sich, dass das Betriebssystem auf dem Computer von Azure [unterstützt](https://aka.ms/azureoslist) wird, bevor Sie den Computer migrieren.
Nicht unterstützte Bitanzahl für das Betriebssystem | Virtuelle Computer mit 32-Bit-Betriebssystem starten zwar unter Umständen in Azure, es empfiehlt sich jedoch, vor der Migration zu Azure ein Betriebssystemupgrade auf die 64-Bit-Version durchzuführen.
Erfordert Visual Studio-Abonnement. | Auf dem Computer wird ein Windows-Clientbetriebssystem ausgeführt. Dies wird nur in Visual Studio-Abonnements unterstützt.
Kein virtueller Computer für erforderliche Speicherleistung gefunden. | Die erforderliche Speicherleistung (IOPS/Durchsatz) für den Computer überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie vor der Migration die Speicheranforderungen für den Computer.
Kein virtueller Computer für erforderliche Netzwerkleistung gefunden. | Die erforderliche Netzwerkleistung (ein-/ausgehend) für den Computer überschreitet die von virtuellen Azure-Computern unterstützte Leistung. Reduzieren Sie die Netzwerkanforderungen für den Computer.
Kein virtueller Computer am angegebenen Speicherort gefunden. | Geben Sie vor der Migration einen anderen Zielort an.
Mindestens ein Datenträger ist ungeeignet. | Mindestens ein mit der VM verbundener Datenträger erfüllt nicht die Azure-Anforderungen. Stellen Sie für jeden mit der VM verbundenen Datenträger sicher, dass die Größe des Datenträgers kleiner als 4 TB ist. Anderenfalls verkleinern Sie die Datenträgergröße, bevor Sie zu Azure migrieren. Stellen Sie sicher, dass die von jedem Datenträger benötigte Leistung (IOPS/Durchsatz) von [Azure-Datenträgern verwalteter virtueller Computer](https://docs.microsoft.com/azure/azure-subscription-service-limits#storage-limits) unterstützt wird.   
Mindestens ein Netzwerkadapter ist ungeeignet. | Entfernen Sie nicht verwendete Netzwerkadapter vor der Migration vom Computer.
Anzahl der Datenträger überschreitet den Grenzwert | Entfernen Sie nicht verwendete Datenträger vor der Migration vom Computer.
Datenträgergröße überschreitet den Grenzwert | Azure unterstützt Datenträger mit einer Größe von bis zu 4 TB. Verkleinern Sie Datenträger vor der Migration auf weniger als 4 TB.
Datenträger am angegebenen Speicherort nicht verfügbar | Stellen Sie vor der Migration sicher, dass sich der Datenträger am Zielspeicherort befindet.
Datenträger für die angegebene Redundanz nicht verfügbar | Der Datenträger muss den in den Bewertungseinstellungen definierten Redundanzspeichertyp verwenden (standardmäßig LRS).
Datenträgereignung konnte aufgrund eines internen Fehlers nicht ermittelt werden | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Kein virtueller Computer gefunden, der die Kern- und Arbeitsspeicheranforderungen erfüllt | Azure konnte keinen geeigneten VM-Typ finden. Reduzieren Sie vor der Migration den Arbeitsspeicher und die Anzahl der Kerne auf dem lokalen Computer.
Eignung des virtuellen Computers konnte aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Datenträger aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.
Eignung konnte für mindestens einen Netzwerkadapter aufgrund eines internen Fehlers nicht ermittelt werden. | Versuchen Sie, eine neue Bewertung für die Gruppe zu erstellen.


## <a name="collect-logs"></a>Erfassen von Protokollen

**Wie erfasse ich Protokolle auf dem virtuellen Collector-Computer?**

Die Protokollierung ist standardmäßig aktiviert. Die Protokolle befinden sich an folgenden Speicherorten:

- C:\Profiler\ProfilerEngineDB.sqlite
- C:\Profiler\Service.log
- C:\Profiler\WebApp.log

Führen Sie folgende Schritte aus, um die Ereignisablaufverfolgung für Windows zu erfassen:

1. Öffnen Sie auf dem virtuellen Collector-Computer ein PowerShell-Befehlsfenster.
2. Führen Sie **Get-EventLog -LogName Application | export-csv eventlog.csv** aus.

**Wie erfasse ich im Portal Protokolle des Netzwerkdatenverkehrs?**

1. Öffnen Sie den Browser, navigieren Sie zum [Portal](https://portal.azure.com), und melden Sie sich an.
2. Drücken Sie F12, um die Entwicklertools zu starten. Deaktivieren Sie ggf. die Einstellung **Einträge beim Navigieren löschen**.
3. Klicken Sie auf die Registerkarte **Netzwerk**, und starten Sie die Erfassung von Netzwerkdatenverkehr:
   - Wählen Sie in Chrome die Option **Protokoll speichern**. Die Aufzeichnung sollte automatisch gestartet werden. Ein roter Kreis gibt an, dass der Datenverkehr erfasst wird. Wenn er nicht angezeigt wird, klicken Sie zum Starten auf den schwarzen Kreis.
   - In Microsoft Edge/IE sollte die Aufzeichnung automatisch gestartet werden. Ist dies nicht der Fall, klicken Sie auf die grüne Wiedergabeschaltfläche.
4. Versuchen Sie, den Fehler zu reproduzieren.
5. Nachdem der Fehler während der Aufzeichnung aufgetreten ist, beenden Sie die Aufzeichnung, und speichern Sie eine Kopie der aufgezeichneten Aktivität:
   - Klicken Sie in Chrome mit der rechten Maustaste, und klicken Sie dann auf **Als HAR mit Inhalt speichern**. Daraufhin werden die Protokolle komprimiert und als HAR-Datei exportiert.
   - Klicken Sie in Microsoft Edge/IE auf das Symbol **Aufgezeichneten Datenverkehr exportieren**. Daraufhin werden die Protokolle komprimiert und exportiert.
6. Navigieren Sie zur Registerkarte **Konsole**, um sie auf Warnungen oder Fehler zu prüfen. So speichern Sie das Konsolenprotokoll:
   - Klicken Sie in Chrome mit der rechten Maustaste auf eine beliebige Stelle im Konsolenprotokoll. Wählen Sie **Speichern als**, um das Protokoll zu exportieren und zu komprimieren.
   - Klicken Sie in Microsoft Edge/IE mit der rechten Maustaste auf die Fehler, und wählen Sie **Alle kopieren** aus.
7. Schließen Sie die Entwicklertools.

## <a name="collector-error-codes-and-recommended-actions"></a>Fehlercodes des Collectors und empfohlene Aktionen

| Fehlercode | Fehlerbezeichnung   | `Message`   | Mögliche Ursachen | Empfohlene Maßnahme  |
| --- | --- | --- | --- | --- |
| 601       | CollectorExpired               | Der Collector ist abgelaufen.                                                        | Collector abgelaufen.                                                                                    | Laden Sie eine neue Version des Collectors herunter, und wiederholen Sie den Vorgang.                                                                                      |
| 751       | UnableToConnectToServer        | Fehler bei der Verbindungsherstellung mit vCenter Server-Instanz „%Name;“. Fehler: %ErrorMessage;     | Weitere Informationen finden Sie in der Fehlermeldung.                                                             | Beheben Sie das Problem, und wiederholen Sie den Vorgang.                                                                                                           |
| 752       | InvalidvCenterEndpoint         | Der Server „%Name;“ ist keine vCenter Server-Instanz.                                  | Geben Sie vCenter Server-Details an.                                                                       | Wiederholen Sie den Vorgang mit den richtigen vCenter Server-Details.                                                                                   |
| 753       | InvalidLoginCredentials        | Fehler bei der Verbindungsherstellung mit vCenter Server-Instanz „%Name;“. Fehler: %ErrorMessage; | Fehler bei der Verbindung mit der vCenter Server-Instanz aufgrund von ungültigen Anmeldeinformationen.                             | Stellen Sie sicher, dass die angegebenen Anmeldeinformationen richtig sind.                                                                                    |
| 754       | NoPerfDataAvailable           | Keine Leistungsdaten verfügbar.                                               | Überprüfen Sie die Statistikstufe in vCenter Server. Sie muss auf „3“ gesetzt sein, damit Leistungsdaten verfügbar sind. | Ändern Sie die Statistikstufe auf „3“ (für eine Dauer von 5 Minuten, 30 Minuten und 2 Stunden), und versuchen Sie es erneut, nachdem Sie mindestens einen Tag gewartet haben.                   |
| 756       | NullInstanceUUID               | Einen Computer mit InstanceUUID „NULL“ gefunden.                                  | Ein Objekt in der vCenter Server-Instanz ist möglicherweise nicht geeignet.                                                      | Beheben Sie das Problem, und wiederholen Sie den Vorgang.                                                                                                           |
| 757       | VMNotFound                     | Virtueller Computer wurde nicht gefunden.                                                  | Virtueller Computer wurde möglicherweise gelöscht: %VMID;                                                                | Stellen Sie sicher, dass die virtuellen Computer, die beim Scannen des vCenter-Bestands ausgewählt wurden, während der Ermittlung vorhanden sind.                                      |
| 758       | GetPerfDataTimeout             | Timeout bei vCenter-Anforderung. Meldung: %Message;                                  | Die vCenter Server-Anmeldeinformationen sind falsch.                                                              | Überprüfen Sie die vCenter Server-Anmeldeinformationen, und stellen Sie sicher, dass vCenter Server erreichbar ist. Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht. |
| 759       | VmwareDllNotFound              | VMWare.Vim-DLL wurde nicht gefunden.                                                     | PowerCLI wurde nicht ordnungsgemäß installiert.                                                                   | Überprüfen Sie, ob PowerCLI korrekt installiert ist. Wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.                               |
| 800       | ServiceError                   | Der Dienst „Azure Migrate-Collector“ wird nicht ausgeführt.                               | Der Dienst „Azure Migrate-Collector“ wird nicht ausgeführt.                                                       | Verwenden Sie „Services.msc“, um den Dienst zu starten, und wiederholen den Vorgang.                                                                             |
| 801       | PowerCLIError                  | Fehler bei der VMware PowerCLI-Installation.                                          | Fehler bei der VMware PowerCLI-Installation.                                                                  | Wiederholen Sie den Vorgang. Wenn das Problem weiterhin besteht, führen Sie die Installation manuell aus und wiederholen den Vorgang.                                                   |
| 802       | TimeSyncError                  | Die Uhrzeit ist nicht mit dem Internetzeitserver synchronisiert.                            | Die Uhrzeit ist nicht mit dem Internetzeitserver synchronisiert.                                                    | Stellen Sie sicher, dass die Uhrzeit auf dem Computer genau auf die Zeitzone des Computers eingestellt ist, und wiederholen Sie den Vorgang.                                 |
| 702       | OMSInvalidProjectKey           | Ungültiger Projektschlüssel angegeben.                                                | Ungültiger Projektschlüssel angegeben.                                                                        | Wiederholen Sie den Vorgang mit dem richtigen Projektschlüssel.                                                                                              |
| 703       | OMSHttpRequestException        | Fehler beim Senden der Anforderung. Meldung: %Message;                                | Überprüfen Sie die Projekt-ID und den Projektschlüssel, und stellen Sie sicher, dass der Endpunkt erreichbar ist.                                       | Wiederholen Sie den Vorgang. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.                                                                     |
| 704       | OMSHttpRequestTimeoutException | Timeout der HTTP-Anforderung. Meldung: %Message;                                     | Überprüfen Sie die Projekt-ID und den Projektschlüssel, und stellen Sie sicher, dass der Endpunkt erreichbar ist.                                       | Wiederholen Sie den Vorgang. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.                                                                     |
