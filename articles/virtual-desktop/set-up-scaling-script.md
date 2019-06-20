---
title: Automatisches Skalieren von Windows Virtual Desktop-Sitzungshosts (Vorschauversion) – Azure
description: Es wird beschrieben, wie Sie das Skript für die automatische Skalierung für Windows Virtual Desktop-Sitzungshosts (Vorschauversion) einrichten.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: e9f500e3ab965b9dbfc5e395a6572497c85f6f8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755133"
---
# <a name="automatically-scale-session-hosts"></a>Automatisches Skalieren von Sitzungshosts

Für viele Bereitstellungen von Windows Virtual Desktop (Vorschauversion) in Azure machen die Kosten für virtuelle Computer einen erheblichen Teil der Gesamtkosten einer Bereitstellung aus. Zur Kostenreduzierung ist es am besten, die virtuellen Computer (VMs) von Sitzungshosts außerhalb der Spitzenzeiten herunterzufahren und die Zuordnung dafür aufzuheben und sie für die Spitzenzeiten dann wieder neu zu starten.

In diesem Artikel wird ein einfaches Skalierungsskript zum automatischen Skalieren der virtuellen Computer von Sitzungshosts in Ihrer Windows Virtual Desktop-Umgebung verwendet. Weitere Informationen zur Funktionsweise des Skalierungsskripts finden Sie im Abschnitt [Funktionsweise des Skalierungsskripts](#how-the-scaling-script-works).

## <a name="prerequisites"></a>Voraussetzungen

Die Umgebung, in der Sie das Skript ausführen, muss über Folgendes verfügen:

- Einen Windows Virtual Desktop-Mandanten und ein entsprechendes Konto oder einen Dienstprinzipal mit Berechtigungen zum Abfragen dieses Mandanten (z. B. „RDS-Mitwirkender“).
- Sitzungshostpool-VMs, die konfiguriert und für den Windows Virtual Desktop-Dienst registriert sind.
- Eine zusätzliche VM, auf der die geplante Aufgabe basierend auf einem Taskplaner ausgeführt wird, und die über Netzwerkzugriff auf Sitzungshosts verfügt. Auf diese wird später in diesem Dokument als Skalierungs-VM Bezug genommen.
- Installation des [PowerShell-Moduls von Microsoft Azure Resource Manager](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) auf der VM, auf der die geplante Aufgabe ausgeführt wird.
- Installation des [PowerShell-Moduls von Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) auf der VM, auf der die geplante Aufgabe ausgeführt wird.

## <a name="recommendations-and-limitations"></a>Empfehlungen und Einschränkungen

Beachten Sie beim Ausführen des Skalierungsskripts Folgendes:

- Mit diesem Skalierungsskript kann nur ein Hostpool pro Instanz der geplanten Aufgabe verarbeitet werden, über die das Skalierungsskript ausgeführt wird.
- Die geplanten Aufgaben, über die Skalierungsskripts ausgeführt werden, müssen auf einer VM angeordnet sein, die immer in Betrieb ist.
- Erstellen Sie einen separaten Ordner für jede Instanz des Skalierungsskripts und die zugehörige Konfiguration.
- Dieses Skript unterstützt nicht die Anmeldung als Administrator bei Windows Virtual Desktop mit Azure AD-Benutzerkonten, die mehrstufige Authentifizierung erfordern. Es wird empfohlen, die Dienstprinzipale zum Zugreifen auf den Windows Virtual Desktop-Dienst und Azure zu verwenden. Befolgen Sie [dieses Tutorial](create-service-principal-role-powershell.md) zum Erstellen eines Dienstprinzipals und einer Rollenzuweisung mit PowerShell.
- Die SLA-Garantie von Azure gilt nur für VMs in einer Verfügbarkeitsgruppe. In der aktuellen Version des Dokuments wird eine Umgebung beschrieben, in der nur eine VM die Skalierung durchführt. Dies reicht unter Umständen nicht aus, um die Verfügbarkeitsanforderungen zu erfüllen.

## <a name="deploy-the-scaling-script"></a>Bereitstellen des Skalierungsskripts

In den folgenden Verfahren wird veranschaulicht, wie Sie das Skalierungsskript bereitstellen.

### <a name="prepare-your-environment-for-the-scaling-script"></a>Vorbereiten Ihrer Umgebung für das Skalierungsskript

Bereiten Sie zunächst Ihre Umgebung für das Skalierungsskript vor:

1. Melden Sie sich bei der VM (Skalierungs-VM), auf der die geplante Aufgabe ausgeführt wird, mit einem Konto an, das für die Domänenverwaltung geeignet ist.
2. Erstellen Sie auf der Skalierungs-VM einen Ordner für das Skalierungsskript und die zugehörige Konfiguration (z.B. **C:\\scaling-HostPool1**).
3. Laden Sie die Dateien **basicScale.ps1**, **Config.xml** und **Functions-PSStoredCredentials.ps1** sowie den Ordner **PowershellModules** aus dem [Skalierungsskript-Repository](https://github.com/Azure/RDS-Templates/tree/master/wvd-sh/WVD%20scaling%20script) herunter, und kopieren Sie diese Daten in den Ordner, den Sie in Schritt 2 erstellt haben. Es gibt zwei bevorzugte Möglichkeiten zum Abrufen der Dateien, bevor sie auf die Skalierungs-VM kopiert werden:
    - Klonen Sie das Git-Repository auf Ihren lokalen Computer.
    - Zeigen Sie die **unformatierte** Version jeder Datei an, kopieren Sie den Inhalt jeder Datei, fügen Sie ihn in einen Text-Editor ein, und speichern Sie die Dateien mit entsprechendem Dateinamen und -typ. 

### <a name="create-securely-stored-credentials"></a>Erstellen von sicher gespeicherten Anmeldeinformationen

Als Nächstes müssen Sie die sicher gespeicherten Anmeldeinformationen erstellen:

1. Öffnen Sie PowerShell ISE als Administrator.
2. Importieren Sie das RDS-PowerShell-Modul, indem Sie das folgende Cmdlet ausführen:

    ```powershell
    Install-Module Microsoft.RdInfra.RdPowershell
    ```
    
3. Öffnen Sie den Bearbeitungsbereich, und laden Sie die Datei **Function-PSStoredCredentials.ps1**.
4. Führen Sie das folgende Cmdlet aus:
    
    ```powershell
    Set-Variable -Name KeyPath -Scope Global -Value <LocalScalingScriptFolder>
    ```
    
    Beispiel: **Set-Variable -Name KeyPath -Scope Global -Value "c:\\scaling-HostPool1"**
5. Führen Sie das Cmdlet **New-StoredCredential -KeyPath \$KeyPath** aus. Geben Sie bei entsprechender Aufforderung Ihre Windows Virtual Desktop-Anmeldeinformationen mit Berechtigungen zum Abfragen des Hostpools ein (der Hostpool ist in der Datei **config.xml** angegeben).
    - Wenn Sie verschiedene Dienstprinzipale oder Standardkonten verwenden, müssen Sie das Cmdlet **New-StoredCredential -KeyPath \$KeyPath** einmal für jedes Konto ausführen, um lokal gespeicherte Anmeldeinformationen zu erstellen.
6. Führen Sie **Get-StoredCredentials -List** aus, um sich zu vergewissern, dass die Erstellung der Anmeldeinformationen erfolgreich war.

### <a name="configure-the-configxml-file"></a>Konfigurieren der Datei „config.xml“

Geben Sie die relevanten Werte in die folgenden Felder ein, um die Skalierungsskripteinstellungen in der Datei „config.xml“ zu aktualisieren:

| Feld                     | BESCHREIBUNG                    |
|-------------------------------|------------------------------------|
| AADTenantId                   | ID des Azure AD-Mandanten, über den das Abonnement zugeordnet wird, unter dem die Sitzungshost-VMs ausgeführt werden.     |
| AADApplicationId              | Dienstprinzipal-Anwendungs-ID                                                       |
| AADServicePrincipalSecret     | Kann während der Testphase eingegeben werden, aber muss leer bleiben, nachdem Sie mit **Functions-PSStoredCredentials.ps1** Anmeldeinformationen erstellt haben.    |
| currentAzureSubscriptionId    | Die ID des Azure-Abonnements, unter dem die Sitzungshost-VMs ausgeführt werden.                        |
| tenantName                    | Name des Windows Virtual Desktop-Mandanten                                                    |
| hostPoolName                  | Name des Windows Virtual Desktop-Hostpools                                                 |
| RDBroker                      | URL zum WVD-Dienst, Standardwert ist „https:\//rdbroker.wvd.microsoft.com“             |
| Username                      | Die Dienstprinzipal-Anwendungs-ID (es kann der gleiche Dienstprinzipal wie in AADApplicationId verwendet werden) oder der Standardbenutzer ohne mehrstufige Authentifizierung. |
| isServicePrincipal            | Zulässige Werte sind **true** oder **false**. Gibt an, ob es sich beim zweiten Satz von Anmeldeinformationen, die verwendet werden, um einen Dienstprinzipal oder ein Standardkonto handelt. |
| BeginPeakTime                 | Beginn des Zeitraums der Spitzenauslastung                                                            |
| EndPeakTime                   | Ende des Zeitraums der Spitzenauslastung                                                              |
| TimeDifferenceInHours         | Zeitunterschied zwischen Ortszeit und UTC in Stunden                                   |
| SessionThresholdPerCPU        | Maximale Anzahl von Sitzungen pro CPU-Schwellenwert, mit denen ermittelt wird, wann während der Spitzenzeiten eine neue Sitzungshost-VM gestartet werden muss.  |
| MinimumNumberOfRDSH           | Mindestzahl von Hostpool-VMs, die außerhalb der Spitzenzeiten weiter in Betrieb bleiben sollen.             |
| LimitSecondsToForceLogOffUser | Wartezeit in Sekunden, bevor für Benutzer das Abmelden erzwungen wird. Wenn „0“ angegeben ist, wird das Abmelden von Benutzern nicht erzwungen.  |
| LogOffMessageTitle            | Titel der Nachricht, die vor dem Erzwingen des Abmeldens an einen Benutzer gesendet wird.                  |
| LogOffMessageBody             | Text der Warnmeldung, die vor dem Abmelden an Benutzer gesendet wird. Beispiel: „Dieser Computer wird in X Minuten heruntergefahren. Speichern Sie Ihre Änderungen, und melden Sie sich ab.“ |

### <a name="configure-the-task-scheduler"></a>Konfigurieren des Taskplaners

Nach dem Konfigurieren der XML-Konfigurationsdatei müssen Sie den Taskplaner so konfigurieren, dass die Datei „basicScaler.ps1“ in regelmäßigen Abständen ausgeführt wird.

1. Starten Sie den **Taskplaner**.
2. Wählen Sie im Fenster **Taskplaner** die Option **Aufgabe erstellen…** .
3. Wählen Sie im Dialogfeld **Aufgabe erstellen** die Registerkarte **Allgemein**, geben Sie einen **Namen** (z. B. „Dynamischer RDSH“) ein, und wählen Sie **Unabhängig von der Benutzeranmeldung ausführen** und **Mit höchsten Berechtigungen ausführen** aus.
4. Navigieren Sie zur Registerkarte **Trigger**, und wählen Sie **Neu…** aus.
5. Aktivieren Sie im Dialogfeld **Neuer Trigger** unter **Erweiterte Einstellungen** die Option **Wiederholen jede**, und wählen Sie den entsprechenden Zeitraum und die Dauer aus (z. B. **15 Minuten** oder **Unbegrenzt**).
6. Wählen Sie die Registerkarte **Aktionen** und dann **Neu…** aus.
7. Geben Sie im Dialogfeld **Neue Aktion** den Dateinamen **powershell.exe** in das Feld **Programm/Skript** und dann **C:\\scaling\\RDSScaler.ps1** in das Feld **Argumente hinzufügen (optional)** ein.
8. Navigieren Sie zu den Registerkarten **Bedingungen** und **Einstellungen**, und wählen Sie **OK**, um jeweils die Standardeinstellungen zu übernehmen.
9. Geben Sie das Kennwort für das Administratorkonto ein, unter dem Sie das Skalierungsskript ausführen möchten.

## <a name="how-the-scaling-script-works"></a>Funktionsweise des Skalierungsskripts

Mit diesem Skalierungsskript werden die Einstellungen aus der Datei „config.xml“ gelesen, z. B. Beginn und Ende des Zeitraums mit Spitzenauslastung während des Tags.

Während der Spitzenauslastung überprüft das Skript die aktuelle Anzahl von Sitzungen und die derzeit ausgeführte RDSH-Kapazität für jeden Hostpool. Es wird berechnet, ob die ausgeführten Sitzungshost-VMs genügend Kapazität für die Unterstützung der vorhandenen Sitzungen basierend auf dem Parameter SessionThresholdPerCPU aufweisen, der in der Datei „config.xml“ definiert ist. Wenn dies nicht der Fall ist, startet das Skript zusätzliche Sitzungshost-VMs im Hostpool.

Außerhalb der Spitzenauslastungszeiten ermittelt das Skript, welche Sitzungshost-VMs heruntergefahren werden sollten. Dies wird basierend auf dem Parameter MinimumNumberOfRDSH in der Datei „config.xml“ durchgeführt. Mit dem Skript werden die Sitzungshost-VMs auf den Ausgleichsmodus festgelegt, um zu verhindern, dass neue Sitzungen eine Verbindung mit den Hosts herstellen. Wenn Sie den Parameter **LimitSecondsToForceLogOffUser** in der Datei „config.xml“ auf einen positiven Wert (nicht Null) festlegen, fordert das Skript alle derzeit angemeldeten Benutzer per Benachrichtigung auf, ihre Änderungen zu speichern. Anschließend wird so lange gewartet, wie dies in der Konfiguration angegeben ist, und anschließend wird für die Benutzer das Abmelden erzwungen. Nachdem alle Benutzersitzungen einer Sitzungshost-VM abgemeldet wurden, wird der Server über das Skript heruntergefahren.

Wenn Sie den Parameter **LimitSecondsToForceLogOffUser** in der Datei „config.xml“ auf Null festlegen, überlässt es das Skript der Einstellung für die Sitzungskonfiguration in den Hostpooleigenschaften, die Abmeldung von Benutzersitzungen zu verarbeiten. Wenn Sitzungen auf einer Sitzungshost-VM ausgeführt werden, wird die Sitzungshost-VM weiterhin ausgeführt. Falls keine Sitzungen vorhanden sind, wird die Sitzungshost-VM über das Skript heruntergefahren.

Das Skript ist so konzipiert, dass es per Taskplaner auf dem Skalierungs-VM-Server in regelmäßigen Abständen ausgeführt wird. Wählen Sie anhand der Größe Ihrer Remotedesktopdienste-Umgebung das geeignete Zeitintervall aus, und beachten Sie, dass das Starten und Herunterfahren von virtuellen Computern einige Zeit dauern kann. Es wird empfohlen, das Skalierungsskript alle 15 Minuten auszuführen.

## <a name="log-files"></a>Protokolldateien

Mit dem Skalierungsskript werden zwei Protokolldateien erstellt: **WVDTenantScale.log** und **WVDTenantUsage.log**. In der Datei **WVDTenantScale.log** werden die Ereignisse und Fehler (falls vorhanden) protokolliert, die bei jeder Ausführung des Skalierungsskripts auftreten.

In der Datei **WVDTenantUsage.log** wird die aktive Anzahl von Kernen und die aktive Anzahl von virtuellen Computern bei jeder Ausführung des Skalierungsskripts aufgezeichnet. Sie können diese Informationen verwenden, um die tatsächliche Nutzung von Microsoft Azure-VMs und die Kosten zu schätzen. Die Datei wird als „Durch Trennzeichen getrennte Werte“ formatiert, und jedes Element enthält die folgenden Informationen:

>Zeit, Hostpool, Kerne, VMs

Der Dateiname kann auch so geändert werden, dass die Erweiterung „.csv“ lautet und die Daten in Microsoft Excel geladen und analysiert werden können.
