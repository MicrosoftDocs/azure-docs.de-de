---
title: Beheben von Problemen mit der Azure AD Connect-Cloudsynchronisierung
description: In diesem Artikel wird beschrieben, wie Sie Probleme beheben, die beim Cloudbereitstellungs-Agent auftreten können.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: fad065795345314c58e83c8d2614ed01c9e78e49
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805528"
---
# <a name="cloud-sync-troubleshooting"></a>Problembehandlung bei der Cloudsynchronisierung

Die Cloudsynchronisierung berührt viele verschiedene Bereiche und weist viele unterschiedliche Abhängigkeiten auf. Diese große Bandbreite kann zu verschiedenen Fehlern führen. Dieser Artikel hilft Ihnen, diese Fehler zu beheben. Sie lernen in ihm die typischen Fehlerbereiche kennen, auf die Sie sich konzentrieren sollten, und Sie erfahren, wie Sie zusätzliche Informationen abrufen können. Außerdem werden die verschiedenen Verfahren vorgestellt, mit denen die Fehler behoben werden können.


## <a name="common-troubleshooting-areas"></a>Allgemeine Problembehandlungsbereiche

|Name|BESCHREIBUNG|
|-----|-----|
|[Agent-Probleme](#agent-problems)|Hier überprüfen Sie, ob der Agent korrekt installiert wurde und vergewissern sich, dass er mit Azure Active Directory (Azure AD) kommuniziert.|
|[Objektsynchronisierungsprobleme](#object-synchronization-problems)|Hier verwenden Sie Bereitstellungsprotokolle, um Probleme bei der Objektsynchronisierung zu beheben.|
|[Bereitstellungsprobleme im Status „Quarantäne“](#provisioning-quarantined-problems)|Hier lernen Sie Bereitstellungsprobleme im Status „Quarantäne“ und deren Behebung kennen.|


## <a name="agent-problems"></a>Agent-Probleme
Zu den ersten Punkten, die Sie beim Agent überprüfen sollten, zählen folgende:

-  Ist der Agent installiert?
-  Wird der Agent lokal ausgeführt?
-  Befindet sich der Agent im Portal?
-  Ist der Agent als fehlerfrei gekennzeichnet?

Diese Punkte können Sie im Azure-Portal und auf dem lokalen Server überprüfen, auf dem der Agent ausgeführt wird.

### <a name="azure-portal-agent-verification"></a>Agent-Überprüfung im Azure-Portal

Führen Sie die folgenden Schritte aus, um zu überprüfen, ob der Agent von Azure erkannt wird und fehlerfrei funktioniert:

1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie auf der linken Seite **Azure Active Directory** > **Azure AD Connect** aus. Wählen Sie in der Mitte **Manage sync** (Synchronisierung verwalten) aus.
1. Wählen Sie auf dem Bildschirm **Azure AD Connect cloud sync** (Azure AD Connect-Cloudsynchronisierung) die Option **Alle Agents überprüfen** aus.

   ![Option „Alle Agents überprüfen“](media/how-to-install/install-7.png)</br>
 
1. Auf dem Bildschirm **On-premises provisioning agents** (Lokale Bereitstellungs-Agents) werden die von Ihnen installierten Agents angezeigt. Vergewissern Sie sich, dass der betreffende Agent aufgeführt wird und als *Fehlerfrei* markiert ist.

   ![Bildschirm „On-premises provisioning agents“ (Lokale Bereitstellungs-Agents)](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Überprüfen des Ports

Stellen Sie sicher, dass Azure an Port 443 lauscht und dass der Agent mit diesem kommunizieren kann. 

Mithilfe dieses Tests wird überprüft, ob Ihre Agents über Port 443 mit Azure kommunizieren können. Öffnen Sie einen Browser, und navigieren Sie von dem Server, auf dem der Agent installiert ist, zur obigen URL.

![Überprüfung der Porterreichbarkeit](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Auf dem lokalen Server

Führen Sie die folgenden Schritte aus, um sicherzustellen, dass der Agent ausgeführt wird:

1. Öffnen Sie auf dem Server, auf dem der Agent installiert ist, die Seite **Dienste**, indem Sie zu dieser Seite oder zu **Start** > **Ausführen** > **Services.msc** navigieren.
1. Vergewissern Sie sich, dass unter **Dienste** die Dienste **Microsoft Azure AD Connect Agent Updater** und **Microsoft Azure AD Connect Provisioning Agent** mit dem Status *Wird ausgeführt* angezeigt werden.

   ![Bildschirm „Dienste“](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Allgemeine Probleme bei der Agent-Installation

In den folgenden Abschnitten werden einige häufiger auftretende Probleme bei der Agent-Installation sowie gängige Lösungsansätze erläutert.

#### <a name="agent-failed-to-start"></a>Fehler beim Starten des Agents

Möglicherweise wird eine Fehlermeldung angezeigt, die folgendermaßen lautet:

**Der Dienst „Microsoft Azure AD Connect Provisioning Agent“ wurde nicht gestartet. Überprüfen Sie, ob Sie ausreichende Berechtigungen zum Starten von Systemdiensten besitzen.** 

Dieses Problem wird in der Regel von einer Gruppenrichtlinie verursacht, die verhindert, dass Berechtigungen auf das vom Installationsprogramm erstellte Anmeldekonto für den lokalen NT-Dienst (NT SERVICE\AADConnectProvisioningAgent) angewendet werden. Diese Berechtigungen sind zum Starten des Diensts erforderlich.

Gehen Sie folgendermaßen vor, um dieses Problem zu beheben:

1. Melden Sie sich beim Server mit einem Administratorkonto an.
1. Öffnen Sie die Seite **Dienste**, indem Sie zu dieser Seite navigieren oder **Start** > **Ausführen** > **Services.msc** ausführen.
1. Doppelklicken Sie unter **Dienste** auf **Microsoft Azure AD Connect Provisioning Agent**.
1. Ändern Sie auf der Registerkarte **Anmelden** den Eintrag für **Dieses Konto** in das Konto eines Domänenadministrators. Starten Sie den Dienst dann neu. 

   ![Registerkarte „Anmelden“](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Bei der Ausführung des Agents tritt ein Timeout auf, oder das Zertifikat ist ungültig.

Beim Versuch, den Agent zu registrieren, kann die folgende Fehlermeldung angezeigt werden:

![Fehlermeldung über einen Timeout](media/how-to-troubleshoot/troubleshoot-4.png)

Dieses Problem wird in der Regel dadurch verursacht, dass der Agent keine Verbindung mit dem Hybrididentitätsdienst herstellen kann und die Konfiguration eines HTTP-Proxys erforderlich ist. Konfigurieren Sie zur Problembehebung einen Proxy für den ausgehenden Datenverkehr. 

Der Bereitstellungs-Agent unterstützt die Verwendung von Proxys für ausgehenden Datenverkehr. Dies können Sie durch das Konfigurieren der Konfigurationsdatei des Agent *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config* konfigurieren. Fügen Sie in der Datei gegen Ende unmittelbar vor dem `</configuration>`-Tag die folgenden Zeilen ein.
Ersetzen Sie die Variablen `[proxy-server]` und `[proxy-port]` durch den Namen Ihres Proxyservers und die Portwerte.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Fehler bei der Agent-Registrierung mit Sicherheitsfehler

Möglicherweise erhalten Sie während der Installation des Cloudbereitstellungs-Agent eine Fehlermeldung.

Dieses Problem wird in der Regel dadurch verursacht, dass der Agent die PowerShell-Registrierungsskripts aufgrund von lokalen PowerShell-Ausführungsrichtlinien nicht ausführen kann.

Ändern Sie zur Lösung dieses Problems die PowerShell-Ausführungsrichtlinien auf dem Server. Sie müssen die Computer- und Benutzerrichtlinien auf *Undefined* oder *RemoteSigned* festlegen. Wenn sie auf *Unrestricted* festgelegt sind, wird diese Fehlermeldung angezeigt. Weitere Informationen finden Sie unter [PowerShell-Ausführungsrichtlinien](/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6). 

### <a name="log-files"></a>Protokolldateien

Standardmäßig gibt der Agent minimalistische Fehlermeldungen und Überwachungsinformationen aus. Diese Überwachungsprotokolle finden Sie im Ordner **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**.

Führen Sie die folgenden Schritte aus, um weitere Details zur Behandlung von Problemen mit dem Agent zu sammeln.

1.  Installieren Sie das PowerShell-Modul „AADCloudSyncTools“ wie [hier](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) beschrieben.
2. Verwenden Sie das PowerShell-Cmdlet `Export-AADCloudSyncToolsLogs`, um die Informationen zu erfassen.  Mit den folgenden Switches können Sie Ihre Datensammlung optimieren.
      - Verwenden Sie „SkipVerboseTrace“, um nur aktuelle Protokolle zu exportieren, ohne ausführliche Protokolle zu erfassen (Standard: false).
      - Verwenden Sie „TracingDurationMins“, um eine andere Erfassungsdauer anzugeben (Standard: 3 Minuten).
      - Verwenden Sie „OutputPath“, um einen anderen Ausgabepfad anzugeben (Standard: Ordner „Dokumente“ des Benutzers)


## <a name="object-synchronization-problems"></a>Objektsynchronisierungsprobleme

Der folgende Abschnitt enthält Informationen zur Behandlung von Problemen bei der Objektsynchronisierung.

### <a name="provisioning-logs"></a>Bereitstellungsprotokolle

Im Azure-Portal können Sie mit Bereitstellungsprotokollen Probleme bei der Objektsynchronisierung ermitteln und beheben. Wählen Sie **Protokolle** aus, um die Protokolle anzuzeigen.

![Schaltfläche „Protokolle“](media/how-to-troubleshoot/log-1.png)

In den Bereitstellungsprotokollen finden Sie sehr viele Informationen zum Status der Objekte, die zwischen der lokalen Active Directory-Umgebung und Azure synchronisiert werden.

![Bildschirm „Bereitstellungsprotokolle“](media/how-to-troubleshoot/log-2.png)

Mithilfe der Dropdownfelder oben auf der Seite können Sie die Ansicht filtern, um sich auf bestimmte Probleme wie Datumsangaben zu konzentrieren. Doppelklicken Sie auf ein einzelnes Ereignis, damit nähere Informationen angezeigt werden.

![Dropdownfeld mit Informationen aus Bereitstellungsprotokollen](media/how-to-troubleshoot/log-3.png)

Diese Informationen enthalten ausführliche Schritte und geben an, wo das Synchronisierungsproblem auftritt. So können Sie herausfinden, wo genau ein Problem aufgetreten ist.


## <a name="provisioning-quarantined-problems"></a>Bereitstellungsprobleme im Status „Quarantäne“

Die Cloudsynchronisierung überwacht die Integrität Ihrer Konfiguration und versetzt fehlerhafte Objekte in den Status „Quarantäne“. Wenn die meisten oder alle Aufrufe an das Zielsystem aufgrund eines Fehlers (z. B. bei ungültigen Administratoranmeldeinformationen) dauerhaft nicht erfolgreich sind, wird der Synchronisierungsauftrag in den Quarantänestatus versetzt.

![Quarantänestatus](media/how-to-troubleshoot/quarantine-1.png)

Durch Klicken auf den Status können Sie zusätzliche Informationen zur Quarantäne anzeigen. Sie können auch den Fehlercode und die Fehlermeldung abrufen.

![Screenshot mit zusätzlichen Informationen zur Quarantäne](media/how-to-troubleshoot/quarantine-2.png)

Wenn Sie mit der rechten Maustaste auf den Status klicken, werden zusätzliche Optionen angezeigt:
    
   - Bereitstellungsprotokolle anzeigen
   - Agent anzeigen
   - Quarantäne löschen

![Screenshot der Optionen im Kontextmenü](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Aufheben einer Quarantäne
Es gibt zwei Möglichkeiten, eine Quarantäne aufzuheben.  Sie lauten wie folgt:

  - Aufheben der Quarantäne: Löschen Sie das Wasserzeichen, und führen Sie eine Deltasynchronisierung aus.
  - Erneutes Starten des Bereitstellungsauftrags: Löschen Sie das Wasserzeichen, und führen Sie eine Erstsynchronisierung aus.

#### <a name="clear-quarantine"></a>Aufheben der Quarantäne
Wenn Sie das Wasserzeichen löschen und eine Deltasynchronisierung für den Bereitstellungsauftrag ausführen möchten, nachdem Sie diesen überprüft haben, klicken Sie einfach mit der rechten Maustaste auf den Status, und wählen Sie **Clear quarantine** (Quarantäne löschen) aus.

Es sollte ein Hinweis angezeigt werden, dass die Quarantäne aufgehoben wird.

![Screenshot des Hinweises, dass die Quarantäne aufgehoben wird](media/how-to-troubleshoot/quarantine-5.png)

Daraufhin sollte der Status des Agents als fehlerfrei angezeigt werden.

![Informationen zum Quarantänestatus](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Erneutes Starten des Bereitstellungsauftrags
Verwenden Sie das Azure-Portal, um den Bereitstellungsauftrag neu zu starten. Wählen Sie auf der Seite mit der Agent-Konfiguration die Option **Bereitstellung erneut starten** aus.

  ![Erneutes Starten der Bereitstellung](media/how-to-troubleshoot/quarantine-3.png)

- Verwenden Sie Microsoft Graph, um [den Bereitstellungsauftrag neu zu starten](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta). Sie haben vollständige Kontrolle über die Elemente, die Sie neu starten. Sie können auswählen, dass Folgendes geschieht:
  - Der Zählerwert wird auf 0 zurückgesetzt und steigt wieder an, sobald neue Elemente in Quarantäne verschoben werden.
  - Eine Anwendung wird aus der Quarantäne entfernt.
  - Grenzwerte werden geändert. 
  
  Verwenden Sie die folgende Anforderung:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Reparieren des Cloud Sync-Dienstkontos
Wenn Sie das Cloud Sync-Dienstkonto reparieren müssen, können Sie das Cmdlet `Repair-AADCloudSyncToolsAccount` verwenden.  


   1.  Führen Sie zunächst die [hier](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) beschriebenen Installationsschritte aus, und fahren Sie dann mit den verbleibenden Schritten fort.
   2.  Geben Sie in einer Windows PowerShell-Sitzung mit Administratorrechten den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Geben Sie die Anmeldeinformationen des globalen Azure AD-Administrators ein.
   4. Geben Sie den folgenden Befehl ein (Sie können ihn auch kopieren und einfügen): 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Nach Abschluss dieses Vorgangs sollten Sie die Meldung erhalten, dass das Konto erfolgreich repariert wurde.

## <a name="next-steps"></a>Nächste Schritte 

- [Bekannte Einschränkungen](how-to-prerequisites.md#known-limitations)
- [Fehlercodes](reference-error-codes.md)
