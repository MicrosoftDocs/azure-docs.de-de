---
title: Beheben von Bereitstellungsproblemen bei der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt die Diagnose und Problembehandlung von Fehlern, die beim Bereitstellen von StorSimple auftreten.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 600934e2d46c1a84a83fa1290db13b3d0d1508f4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995402"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Beheben von Problemen mit der Bereitstellung von StorSimple-Geräten
## <a name="overview"></a>Übersicht
Dieser Artikel bietet hilfreiche Anleitungen zur Behandlung von Problemen bei der Microsoft Azure StorSimple-Bereitstellung . Er beschreibt allgemeine Probleme, mögliche Ursachen und empfohlene Schritte zum Beheben von Problemen bei der StorSimple-Konfiguration. 

Diese Informationen gelten sowohl für physische Geräte der StorSimple 8000-Serie als auch für StorSimple Cloud Appliances.

> [!NOTE]
> Gerätekonfigurationsbezogene Probleme können bei der ersten Bereitstellung oder im späteren Betrieb auftreten. In diesem Artikel liegt der Fokus auf der Problembehandlung bei der erstmaligen Bereitstellung. Um Probleme mit betriebsbereiten Geräten zu beheben, lesen Sie [Beheben von Problemen bei betriebsbereiten Geräten mit dem Diagnosetool](storsimple-8000-diagnostics.md).

Dieser Artikel beschreibt weiterhin die Tools für die Problembehandlung bei StorSimple-Bereitstellungen und bietet eine schrittweise Anleitung zur Problembehandlung.

## <a name="first-time-deployment-issues"></a>Probleme bei der erstmaligen Bereitstellung
Wenn bei der ersten Bereitstellung des Geräts ein Problem auftritt, berücksichtigen Sie Folgendes:

* Wenn Sie Probleme mit einem physischen Gerät beheben, stellen Sie sicher, dass die Hardware so installiert und konfiguriert ist, wie unter [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md) oder [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md) beschrieben.
* Überprüfen Sie die Voraussetzungen für die Bereitstellung. Stellen Sie sicher, dass alle erforderlichen Informationen aus der [Konfigurationsprüfliste für die Bereitstellung](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist)vorliegen.
* Überprüfen Sie die StorSimple-Versionshinweise, um festzustellen, ob das Problem dort beschrieben wird. Die Versionshinweise enthalten Problemumgehungen für bekannte Installationsprobleme. 

Während der Bereitstellung der Geräte treten die meisten Probleme im Zusammenhang mit der Ausführung des Setup-Assistenten sowie der Registrierung des Geräts über Windows PowerShell für StorSimple auf. (Windows PowerShell für StorSimple wird verwendet, um das StorSimple-Gerät zu registrieren und zu konfigurieren. Weitere Informationen zur Geräteregistrierung finden Sie unter [Schritt 3: Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).)

In den folgenden Abschnitten erhalten Sie Hilfestellung bei Problemen, die bei der erstmaligen Konfiguration des StorSimple-Geräts auftreten können.

## <a name="first-time-setup-wizard-process"></a>Schritte bei der erstmaligen Ausführung des Setup-Assistenten
In den folgenden Schritten werden die Schritte des Setup-Assistenten zusammengefasst. Ausführliche Informationen zum Setup finden Sie unter [Bereitstellen lokaler StorSimple-Geräte](storsimple-8000-deployment-walkthrough-u2.md).

1. Führen Sie das Cmdlet [Invoke-HcsSetupWizard](/previous-versions/windows/powershell-scripting/dn688135(v=wps.630)) aus, um den Setup-Assistenten zu starten, der Sie durch die verbleibenden Schritte führt. 
2. Konfigurieren des Netzwerks: im Setup-Assistenten können Sie die Netzwerkeinstellungen für die DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät konfigurieren. Dazu gehören folgende Einstellungen:
   * Virtuelle IP-Adresse (VIP), Subnetzmaske und Gateway – Das Cmdlet [Set HcsNetInterface](/previous-versions/windows/powershell-scripting/dn688161(v=wps.630)) wird im Hintergrund ausgeführt. Dieses Cmdlet konfiguriert IP-Adresse, Subnetzmaske und Gateway für die DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät.
   * Primärer DNS-Server – das Cmdlet [Set HcsDnsClientServerAddress](/previous-versions/windows/powershell-scripting/dn688172(v=wps.630)) wird im Hintergrund ausgeführt. Konfiguriert die DNS-Einstellungen für Ihre StorSimple-Lösung.
   * NTP-Server – Das Cmdlet [Set HcsNtpClientServerAddress](/previous-versions/windows/powershell-scripting/dn688138(v=wps.630)) wird im Hintergrund ausgeführt. Konfiguriert die NTP-Servereinstellungen für Ihre StorSimple-Lösung.
   * Optionaler Webproxy – Das Cmdlet [Set HcsWebProxy](/previous-versions/windows/powershell-scripting/dn688154(v=wps.630)) wird im Hintergrund ausgeführt. Bestimmt und aktiviert die Webproxy-Konfiguration für Ihre StorSimple-Lösung.
3. Einrichten des Kennworts: Der nächste Schritt ist das Einrichten des Geräteadministratorkennworts.
   Das Geräteadministratorkennwort wird zur Anmeldung am Gerät verwendet. Das Standardkennwort für das Gerät lautet **Password1**.
        
     > [!IMPORTANT]
     > Kennwörter werden vor der Registrierung erfasst, aber erst nach erfolgreicher Registrierung des Geräts angewendet. Wenn beim Zuweisen des Kennworts ein Fehler auftritt, werden Sie aufgefordert, das Kennwort erneut anzugeben, bis die erforderlichen Kennwörter (gemäß den Komplexitätsanforderungen) erfasst wurden.
     
4. Registrieren des Geräts: Im letzten Schritt wird das Gerät im StorSimple-Geräte-Manager-Dienst in Microsoft Azure registriert. Die Registrierung erfordert ein [Abrufen des Dienstregistrierungsschlüssels](storsimple-8000-manage-service.md#get-the-service-registration-key) aus dem Azure-Portal sowie dessen Angabe im Setup-Assistenten. **Nachdem das Gerät erfolgreich registriert wurde, wird ein Dienstdatenverschlüsselungs-Schlüssel bereitgestellt. Achten Sie darauf, dass Sie diesen Verschlüsselungsschlüssel an einem sicheren Ort speichern, da alle nachfolgenden Geräte mit dem Dienst registriert werden müssen.**

## <a name="common-errors-during-device-deployment"></a>Häufige Fehler bei der Gerätebereitstellung
Die folgenden Tabellen enthalten häufige Fehler, die auftreten können, wenn Sie:

* die erforderlichen Netzwerkeinstellungen konfigurieren .
* die optionalen Webproxy-Einstellungen konfigurieren.
* das Geräteadministratorkennwort einrichten.
* das Gerät registrieren.

## <a name="errors-during-the-required-network-settings"></a>Fehler während der erforderlichen Netzwerkeinstellungen
| Nein. | Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Dieser Befehl kann nur auf dem aktiven Controller ausgeführt werden. |Die Konfiguration wurde für den passiven Controller ausgeführt. |Führen Sie diesen Befehl auf dem aktiven Controller aus. Weitere Informationen finden Sie unter [Identifizieren eines aktiven Controllers auf Ihrem Gerät](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Das Gerät ist nicht bereit. |Es gibt Probleme mit der DATA 0-Netzwerkverbindung. |Überprüfen Sie die physische DATA 0-Netzwerkkonnektivität. |
| 3 |Invoke-HcsSetupWizard: Es liegt ein IP-Adressenkonflikt mit einem anderen System im Netzwerk vor (Ausnahme von HRESULT: 0x80070263). |Die für DATA 0 angegebene IP-Adresse wird bereits von einem anderen System verwendet. |Geben Sie eine neue IP-Adresse an, die nicht belegt ist. |
| 4 |Invoke-HcsSetupWizard: Fehler in einer Clusterressource. (Ausnahme von HRESULT: 0x800713AE). |Doppelte VIP. Die angegebene IP-Adresse wird bereits verwendet. |Geben Sie eine neue IP-Adresse an, die nicht belegt ist. |
| 5 |Invoke-HcsSetupWizard: Ungültige IPv4-Adresse. |Die IP-Adresse hat das falsche Format. |Überprüfen Sie das Format, und geben Sie die IP-Adresse erneut an. Weitere Informationen finden Sie unter [IPv4-Adressierung][1]. |
| 6 |Invoke-HcsSetupWizard: Ungültige IPv6-Adresse. |Die IP-Adresse hat das falsche Format. |Überprüfen Sie das Format, und geben Sie die IP-Adresse erneut an. Weitere Informationen finden Sie unter [IPv6-Adressierung][2]. |
| 7 |Invoke-HcsSetupWizard: Es sind keine Endpunkte mehr von der Endpunktzuordnung verfügbar. (Ausnahme von HRESULT: 0x800706D9) |Die Clusterfunktionalität ist nicht funktionsfähig. |[Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) für weitere Schritte. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fehler während der optionalen Webproxyeinstellungen
| Nein. | Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ungültiger Parameter (Ausnahme von HRESULT: 0x80070057) |Einer der Parameter für die Proxyeinstellungen ist ungültig. |Der URI wird nicht im richtigen Format bereitgestellt. Verwenden Sie das folgende Format: http:// *\<IP address or FQDN of the web proxy server>* : *\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC-Server nicht verfügbar (Ausnahme von HRESULT: 0x800706ba) |Im Folgenden sind die Hauptursachen aufgeführt:<ol><li>Der Cluster wird nicht ausgeführt.</li><li>Der passive Controller kann nicht mit passiven Controller kommunizieren, und der Befehl wird über den passiven Controller ausgeführt.</li></ol> |Je nach Hauptursache:<ol><li>[Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um sicherzustellen, dass der Cluster aktiv ist.</li><li>Führen Sie den Befehl auf dem aktiven Controller aus. Wenn Sie den Befehl auf dem passiven Controller ausführen möchten, müssen Sie sicherstellen, dass der passive Controller mit dem aktiven Controller kommunizieren kann. Sie müssen sich an den [Microsoft Support](storsimple-8000-contact-microsoft-support.md) wenden, wenn diese Verbindung beeinträchtigt ist.</li></ol> |
| 3 |Invoke-HcsSetupWizard: Fehler des RPC-Aufrufs (Ausnahme von HRESULT: 0x800706be) |Cluster ist nicht verfügbar. |[Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um sicherzustellen, dass der Cluster aktiv ist. |
| 4 |Invoke-HcsSetupWizard: Clusterressource wurde nicht gefunden (Ausnahme von HRESULT: 0x8007138f) |Die Clusterressource wurde nicht gefunden. Dies kann auftreten, wenn die Installation fehlerhaft verlaufen ist. |Möglicherweise müssen Sie das Gerät auf die werksseitigen Standardeinstellungen zurücksetzen. [Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um eine Clusterressource zu erstellen. |
| 5 |Invoke-HcsSetupWizard: Clusterressource ist nicht online (Ausnahme von HRESULT: 0x8007138c) |Die Clusterressourcen sind nicht online. |[Wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) für weitere Schritte. |

## <a name="errors-related-to-device-administrator-password"></a>Fehler im Zusammenhang mit dem Geräteadministratorkennwort
Das Standardkennwort für den Geräteadministrator lautet **Password1**. Dieses Kennwort läuft nach der ersten Anmeldung ab. Aus diesem Grund müssen Sie den Setup-Assistenten verwenden, um es zu ändern. Sie müssen ein neues Kennwort für den Geräteadministrator angeben, wenn Sie das Gerät zum ersten Mal registrieren. 

Stellen Sie sicher, dass Ihre Kennwörter die folgenden Anforderungen erfüllen:

* Ihr Kennwort muss zwischen 8 und 15 Zeichen lang sein.
* Kennwörter sollten 3 von 4 folgenden Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. 
* Ihr Kennwort muss sich von den letzten 24 Kennwörtern unterscheiden.

Bedenken Sie außerdem, dass die Kennwörter jedes Jahr ablaufen und erst geändert werden können, nachdem das Gerät erfolgreich registriert wurde. Wenn die Registrierung aus irgendeinem Grund fehlschlägt, werden die Kennwörter nicht geändert.

Weitere Informationen zum Geräteadministratorkennwort finden Sie unter [Verwenden des StorSimple-Geräte-Manager-Diensts zum Ändern von StorSimple-Kennwörtern](storsimple-8000-change-passwords.md).

Beim Einrichten der Geräteadministrator- und StorSimple-Momentaufnahme-Manager-Kennwörter können einer oder mehrere der folgenden Fehler auftreten.

| Nein. | Fehlermeldung | Empfohlene Maßnahme |
| --- | --- | --- |
| 1 |Das Kennwort überschreitet die maximale Länge. |Das Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein. |
| 2 |Das Kennwort erfüllt nicht die erforderliche Länge. |Das Administratorkennwort für das Gerät muss zwischen 8 und 15 Zeichen lang sein.|
| 3 |Das Kennwort muss Kleinbuchstaben enthalten. |Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 4 |Das Kennwort muss Zahlen enthalten. |Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 5 |Das Kennwort muss Sonderzeichen enthalten. |Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Kleinbuchstaben, Großbuchstaben, Zahlen und Sonderzeichen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 6 |Kennwörter sollten drei der folgenden vier Zeichentypen enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und Sonderzeichen. |Ihr Kennwort enthält nicht die erforderlichen Zeichentypen. Stellen Sie sicher, dass Ihr Kennwort diese Anforderungen erfüllt. |
| 7 |Parameter passt nicht zur Bestätigung. |Stellen Sie sicher, dass Ihr Kennwort alle Anforderungen erfüllt und dass Sie es richtig eingegeben haben. |
| 8 |Ihr Kennwort darf nicht mit dem Standardwert übereinstimmen. |Das Standardkennwort lautet *Password1*. Sie müssen dieses Kennwort ändern, nachdem Sie sich zum ersten Mal angemeldet haben. |
| 9 |Das eingegebene Kennwort entspricht nicht dem Gerätekennwort. Geben Sie das Kennwort erneut ein. |Überprüfen Sie das Kennwort, und geben Sie es erneut ein. |

Kennwörter werden vor der Registrierung erfasst, aber erst nach erfolgreicher Registrierung des Geräts angewendet. Der Workflow zur Kennwortwiederherstellung erfordert, dass das Gerät registriert wurde.

> [!IMPORTANT]
> Nach einem Fehler beim Anwenden eines Kennworts versucht die Software im Allgemeinen wiederholt, das Kennwort zu erfassen, bis dies erfolgreich ist. In seltenen Fällen kann das Kennwort nicht angewendet werden. In diesem Fall können Sie das Gerät registrieren und den Vorgang fortsetzen, jedoch kann das Kennwort nicht geändert werden. Sie können das Geräteadministratorkennwort nach der Registrierung im Azure-Portal ändern.


Sie können das Kennwort im Azure-Portal über den StorSimple-Geräte-Manager-Dienst zurücksetzen. Weitere Informationen finden Sie unter [Ändern des Geräteadministratorkennworts](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Fehler bei der Geräteregistrierung
Der StorSimple-Geräte-Manager-Dienst in Microsoft Azure wird zur Registrierung des Geräts verwendet. Während der Geräteregistrierung können eines oder mehrere der folgenden Probleme auftreten.

| Nein. | Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
| --- | --- | --- | --- |
| 1 |Fehler 350027: Fehler bei der Registrierung des Geräts im StorSimple-Geräte-Manager. | |Warten Sie einige Minuten, und versuchen Sie es dann erneut. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Fehler 350013: Fehler bei der Registrierung des Geräts. Dies kann aufgrund falscher Dienstregistrierungsschlüssel auftreten. | |Registrieren Sie das Gerät erneut mit dem korrekten Dienstregistrierungsschlüssel. Weitere Informationen finden Sie unter [Abrufen des Dienstregistrierungsschlüssels](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Fehler 350063: Authentifizierung des StorSimple-Geräte-Manager-Diensts erfolgreich, aber Fehler bei der Registrierung. Wiederholen Sie den Vorgang nach einiger Zeit. |Dieser Fehler zeigt an, dass die Authentifizierung mit ACS erfolgreich war, der Registrierungsaufruf an den Dienst jedoch fehlgeschlagen ist. Dies kann durch eine sporadische Netzwerkstörung verursacht werden. |Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fehler 350049: Der Dienst war während der Registrierung nicht erreichbar. |Wenn der Aufruf an den Dienst erfolgt, wird eine Webausnahme empfangen. In einigen Fällen wird dies möglicherweise behoben, indem der Vorgang später wiederholt wird. |Überprüfen Sie die IP-Adresse und DNS-Namen, und wiederholen Sie den Vorgang. Wenn das Problem weiterhin besteht, [wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) |
| 5 |Fehler 350031: Das Gerät wurde bereits registriert. | |Keine Aktion erforderlich. |
| 6 |Fehler 350016: Fehler bei der Geräteregistrierung. | |Stellen Sie sicher, dass der Registrierungsschlüssel korrekt ist. |
| 7 |Invoke-HcsSetupWizard: Fehler beim Registrieren des Geräts. Dies kann durch falsche IP-Adressen oder DNS-Namen auftreten. Überprüfen Sie Ihre Netzwerkeinstellungen, und versuchen Sie es erneut. Wenn das Problem weiterhin besteht, [contact Microsoft Support](storsimple-8000-contact-microsoft-support.md)(in englischer Sprache). (Fehler 350050) |Stellen Sie sicher, dass Ihr Gerät außerhalb des Netzwerks erfolgreich Ping-Befehle ausführen kann. Wenn keine Verbindung mit dem externen Netzwerk besteht, kann die Registrierung mit diesem Fehler fehlschlagen. Dieser Fehler kann eine Kombination aus einem oder mehreren der folgenden Ursachen sein:<ul><li>Falsche IP</li><li>Falsches Subnetz</li><li>Falsches Gateway</li><li>Falsche DNS-Einstellungen</li></ul> |Lesen Sie die Schritte in der [schrittweisen Anleitung zur Problembehandlung](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Beim aktuellen Vorgang ist aufgrund eines internen Dienstfehlers [0x1FBE2] ein Fehler aufgetreten. Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Microsoft Support. |Dies ist ein allgemeiner Fehler, der für alle für den Benutzer nicht sichtbaren Fehler vom Dienst oder Agent ausgegeben wird. Der häufigste Grund ist möglicherweise, dass die ACS-Authentifizierung fehlgeschlagen ist. Eine mögliche Ursache für diesen Fehler ist, dass Probleme mit der NTP-Serverkonfiguration vorliegen und die Uhrzeit auf dem Gerät nicht richtig festgelegt ist. |Passen Sie die Zeiteinstellung (im Fall von Problemen) an, und wiederholen Sie den Registrierungsvorgang. Wenn Sie den Befehl Set-HcsSystem -Timezone verwenden, um die Zeitzone anzupassen, schreiben Sie jedes Wort der Zeitzone mit großem Anfangsbuchstaben (z.B. „Pacific Standard Time“).  Wenn das Problem weiterhin auftritt, [wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md), um Informationen zu den nächsten Schritten zu erhalten. |
| 9 |Warnung: Das Gerät konnte nicht aktiviert werden. Die Kennwörter für den Geräteadministrator und für den StorSimple-Momentaufnahme-Manager wurden nicht geändert. |Wenn die Registrierung fehlschlägt, werden die Geräteadministrator- und StorSimple-Momentaufnahme-Manager-Kennwörter nicht geändert. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Tools zur Problembehandlung für StorSimple-Bereitstellungen
StorSimple umfasst mehrere Tools, mit denen sich Probleme mit der StorSimple-Lösung beheben lassen. Dazu gehören:

* Supportpakete und Geräteprotokolle
* Cmdlets, die speziell für die Problembehandlung entwickelt wurden

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Für die Problembehandlung verfügbare Supportpakete und Geräteprotokolle
Ein Supportpaket enthält alle relevanten Protokolle, die das Microsoft Support-Team bei der Behandlung von Geräteproblemen unterstützen können. Sie können Windows PowerShell für StorSimple verwenden, um ein verschlüsseltes Supportpaket zu generieren, das Sie für den Support freigeben können.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>So zeigen Sie die Protokolle oder den Inhalt des Supportpakets an
1. Generieren Sie mithilfe von Windows PowerShell für StorSimple ein Unterstützungspaket, wie unter [Erstellen und Verwalten eines Unterstützungspakets](storsimple-8000-create-manage-support-package.md)beschrieben.
2. Laden Sie das [Script to decrypt a support package for Windows PowerShell for StorSimple](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) (Skript zum Entschlüsseln eines Supportpakets für Windows PowerShell für StorSimple, in englischer Sprache) lokal auf den Clientcomputer herunter.
3. Verwenden Sie diese schrittweise Anleitung zum [Bearbeiten eines Supportpakets](storsimple-8000-create-manage-support-package.md#edit-a-support-package) , um das Supportpaket zu öffnen und zu entschlüsseln.
4. Die entschlüsselten Protokolle des Supportpakets haben das ETW/ETVX-Format. Sie können die folgenden Schritte ausführen, um diese Dateien in der Windows-Ereignisanzeige anzuzeigen:
   
   1. Führen Sie den Befehl **eventvwr** auf dem Windows-Client aus. Dadurch wird die Ereignisanzeige gestartet.
   2. Klicken Sie im Bereich **Aktionen** auf **Gespeichertes Protokoll öffnen**, und zeigen Sie auf die Protokolldateien im ETVX/ETW-Format (das Supportpaket). Die Datei kann jetzt angezeigt werden. Nach dem Öffnen der Datei können Sie die Datei als Text speichern, indem Sie mit der rechten Maustaste auf die Datei klicken.
      
      > [!IMPORTANT]
      > Sie können auch das Cmdlet **Get-WinEvent** verwenden, um diese Dateien in Windows PowerShell zu öffnen. Weitere Informationen finden Sie in der Windows PowerShell-Cmdlet-Referenzdokumentation unter [Get-WinEvent](/powershell/module/microsoft.powershell.diagnostics/get-winevent) .
     
5. Wenn die Protokolle in der Ereignisanzeige geöffnet sind, suchen Sie nach den folgenden Protokollen, die Probleme im Zusammenhang mit der Gerätekonfiguration enthalten:
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. Suchen Sie in den Protokolldateien nach Zeichenfolgen im Zusammenhang mit den Cmdlets, die vom Setup-Assistenten aufgerufen wurden. Eine Liste der Cmdlets finden Sie unter [Schritte bei der erstmaligen Ausführung des Setup-Assistenten](#first-time-setup-wizard-process) .
7. Wenn Sie die Ursache des Problems nicht ermitteln können, wenden Sie sich für die nächsten Schritte [an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) . Führen Sie die Schritte unter [Erstellen einer Supportanfrage](storsimple-8000-contact-microsoft-support.md#create-a-support-request) aus, wenn Sie den Microsoft Support kontaktieren.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets für die Problembehandlung
Verwenden Sie die folgenden Windows PowerShell-Cmdlets, um Verbindungsfehler zu erkennen.

* `Get-NetAdapter`: Verwenden Sie dieses Cmdlet, um die Integrität der Netzwerkschnittstellen zu ermitteln.
* `Test-Connection`: Verwenden Sie dieses Cmdlet, um die Netzwerkkonnektivität innerhalb und außerhalb des Netzwerks zu überprüfen.
* `Test-HcsmConnection`: Verwenden Sie dieses Cmdlet zum Überprüfen der Verbindung eines erfolgreich registrierten Geräts.
* `Sync-HcsTime`: Verwenden Sie dieses Cmdlet, um Gerätezeit anzuzeigen und die Zeitsynchronisierung mit dem NTP-Server zu erzwingen.
* `Enable-HcsPing` und `Disable-HcsPing`: Verwenden Sie diese Cmdlets, um den Hosts zu ermöglichen, die Netzwerkschnittstellen auf dem StorSimple-Gerät zu pingen. Standardmäßig antworten StorSimple-Netzwerkschnittstellen nicht auf Ping-Anforderungen.
* `Trace-HcsRoute`: Verwenden Sie dieses Cmdlet als ein Werkzeug zum Verfolgen von Routen. Es sendet über einen bestimmten Zeitraum Pakete an alle Router auf dem Weg zu einem Ziel und berechnet dann die Ergebnisse auf Grundlage der von den einzelnen Hops zurückgegebenen Pakete. Da `Trace-HcsRoute` das Maß an Paketverlust bei jedem Router oder jeder Verbindung anzeigt, können Sie feststellen, durch welche Router oder welche Verknüpfungen Netzwerkprobleme verursacht werden können.
* `Get-HcsRoutingTable`: Verwenden Sie dieses Cmdlet zum Anzeigen der lokalen IP-Routingtabelle.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Problembehandlung mit dem Cmdlet "Get-NetAdapter"
Beim Konfigurieren von Netzwerkschnittstellen für eine erste Gerätebereitstellung ist der Hardwarestatus nicht in der Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts verfügbar, da das Gerät noch nicht im Dienst registriert ist. Darüber hinaus wird der Status des Geräts auf dem Blatt **Hardwareintegrität** nicht immer richtig dargestellt, insbesondere dann, wenn Probleme mit der Dienstsynchronisierung vorliegen. In diesen Fällen können Sie das Cmdlet `Get-NetAdapter` verwenden, um den Zustand und Status der Netzwerkschnittstellen zu bestimmen.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>So zeigen Sie eine Liste aller Netzwerkadapter auf Ihrem Gerät an
1. Starten Sie Windows PowerShell für StorSimple, und geben Sie `Get-NetAdapter`ein. 
2. Verwenden Sie die Ausgabe des Cmdlets `Get-NetAdapter` und die folgenden Richtlinien, um den Status der Netzwerkschnittstelle festzustellen.
   
   * Wenn die Schnittstelle aktiviert ist und fehlerfrei funktioniert, wird der Status **ifIndex** als **Up** angezeigt.
   * Wenn die Schnittstelle fehlerfrei funktioniert, aber nicht physisch (durch ein Netzwerkkabel) verbunden ist, wird **ifIndex** als **Disabled** angezeigt.
   * Wenn die Schnittstelle fehlerfrei funktioniert, jedoch nicht aktiviert ist, wird der Status **ifIndex** als **NotPresent** angezeigt.
   * Wenn die Schnittstelle nicht vorhanden ist, wird sie nicht in dieser Liste angezeigt. Der Benutzeroberfläche des StorSimple-Geräte-Manager-Diensts zeigt diese Schnittstelle in einem fehlerhaften Zustand an.

Weitere Informationen zur Verwendung dieses Cmdlets finden Sie unter [Get-NetAdapter](/powershell/module/netadapter/get-netadapter?view=win10-ps) in der Windows PowerShell-Cmdlet-Referenz.

In den folgenden Abschnitten sind Beispiele für die Ausgabe des Cmdlets `Get-NetAdapter` aufgeführt.

 In diesen Beispielen ist "Controller 0" der passive Controller und wurde wie folgt konfiguriert:

* Auf dem Gerät sind die Netzwerkschnittstellen DATA 0, DATA 1, DATA 2 und DATA 3 vorhanden.
* Die Netzwerkschnittstellenkarten DATA 4 und DATA 5 sind nicht vorhanden. Daher sind sie nicht in der Ausgabe aufgeführt.
* DATA 0 ist aktiviert.

Controller 1 ist der aktive Controller und wurde wie folgt konfiguriert:

* Die Netzwerkschnittstellen DATA 0, DATA 1, DATA 2, DATA 3, DATA 4 und DATA 5 sind auf dem Gerät vorhanden.
* DATA 0 ist aktiviert.

**Beispielausgabe – Controller 0**

Im folgenden finden Sie die Ausgabe von Controller 0 (passiver Controller). DATA 1, DATA 2 und DATA 3 sind nicht verbunden. DATA 4 und DATA 5 sind nicht aufgeführt, da sie nicht auf dem Gerät vorhanden sind.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Beispielausgabe – Controller 1**

Im Folgenden finden Sie die Ausgabe von Controller 1 (aktiver Controller). Nur die DATA 0-Netzwerkschnittstelle ist auf dem Gerät konfiguriert und funktioniert.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Problembehandlung mit dem Cmdlet "Test-Connection"
Mit dem Cmdlet `Test-Connection` können Sie bestimmen, ob Ihr StorSimple-Gerät mit dem externen Netzwerk eine Verbindung herstellen kann. Wenn alle Netzwerkparameter, einschließlich DNS, im Setup-Assistenten korrekt konfiguriert sind, können Sie das Cmdlet `Test-Connection` verwenden, um einen Ping-Befehl an eine bekannte Adresse außerhalb des Netzwerks, z. B. outlook.com, zu senden.

Aktivieren Sie Ping zum Beheben von Verbindungsproblemen mit diesem Cmdlet, wenn Ping deaktiviert ist.

Im Folgenden sind Beispiele für die Ausgabe des Cmdlets `Test-Connection` aufgeführt.

> [!NOTE]
> Im ersten Beispiel wird das Gerät mit einem falschen DNS konfiguriert. Im zweiten Beispiel ist der DNS richtig.

**Beispielausgabe – falsche DNS**

Im folgenden Beispiel erfolgt keine Ausgabe für die IPv4- und IPv6-Adressen. Dies weist darauf hin, dass der DNS nicht aufgelöst wird. Dies bedeutet, dass keine Verbindung mit dem externen Netzwerk besteht und eine korrekter DNS bereitgestellt werden muss.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Beispielausgabe – richtiger DNS**

Im folgenden Beispiel gibt der DNS die IPv4-Adresse zurück. Dies bedeutet, dass der DNS ordnungsgemäß konfiguriert ist. Dadurch wird bestätigt, dass eine Verbindung mit dem externen Netzwerk besteht.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Problembehandlung mit dem Cmdlet "Test-HcsmConnection"
Verwenden Sie das Cmdlet `Test-HcsmConnection` für ein Gerät, das bereits verbunden und im StorSimple-Geräte-Manager-Dienst registriert ist. Mit diesem Cmdlet können Sie die Verbindung zwischen einem registrierten Gerät und dem entsprechenden StorSimple-Geräte-Manager-Dienst überprüfen. Sie können diesen Befehl in Windows PowerShell für StorSimple ausführen.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>So führen Sie das Cmdlet "Test-HcsmConnection" aus
1. Stellen Sie sicher, dass das Gerät registriert ist.
2. Überprüfen Sie den Gerätestatus. Wenn das Gerät deaktiviert ist, sich im Wartungsmodus befindet oder offline ist, wird möglicherweise einer der folgenden Fehler angezeigt:
   
   * ErrorCode.CiSDeviceDecommissioned – gibt an, dass das Gerät deaktiviert wurde.
   * ErrorCode.DeviceNotReady – gibt an, dass sich das Gerät im Wartungsmodus befindet.
   * ErrorCode.DeviceNotReady – gibt an, dass das Gerät nicht online ist.
3. Stellen Sie sicher, dass der StorSimple-Geräte-Manager-Dienst ausgeführt wird (mit dem Cmdlet [Get-ClusterResource](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee461004(v=technet.10))). Wenn der Dienst nicht ausgeführt wird, werden möglicherweise die folgenden Fehler angezeigt:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – gibt an, dass bei der Ausführung von "Get-ClusterResource" eine Ausnahme aufgetreten ist.
4. Überprüfen Sie das ACS-Token (Access Control Service). Wenn eine Webausnahme ausgelöst wird, könnte dies auf ein Gatewayproblem, fehlende Proxyauthentifizierung, einen falschen DNS oder einen Authentifizierungsfehler zurückzuführen sein. Es werden möglicherweise die folgenden Fehler angezeigt:
   
   * ErrorCode.CiSApplianceGateway – weist auf eine HttpStatusCode.BadGateway-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen.
   * ErrorCode.CiSApplianceProxy – weist auf eine HttpStatusCode.ProxyAuthenticationRequired-Ausnahme (HTTP-Statuscode 407) hin: Der Client konnte nicht beim Proxyserver authentifiziert werden.
   * ErrorCode.CiSApplianceDNSError – weist auf eine WebExceptionStatus.NameResolutionFailure-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen.
   * ErrorCode.CiSApplianceACSError – gibt an, dass der Dienst einen Authentifizierungsfehler zurückgegeben hat, während eine Verbindung vorliegt.
     
     Wenn keine Webausnahme ausgelöst wird, überprüfen Sie auf ErrorCode.CiSApplianceFailure. Dies gibt an, dass das Gerät fehlgeschlagen ist.
5. Überprüfen Sie die Verbindung des Cloud-Diensts. Wenn der Dienst eine Webausnahme auslöst, werden möglicherweise die folgenden Fehler angezeigt:
   
   * ErrorCode.CiSApplianceGateway – weist auf eine HttpStatusCode.BadGateway-Ausnahme hin: Ein zwischengeschalteter Proxyserver hat von einem anderen Proxy oder dem ursprünglichen Server eine ungültige Anforderung erhalten.
   * ErrorCode.CiSApplianceProxy – weist auf eine HttpStatusCode.ProxyAuthenticationRequired-Ausnahme (HTTP-Statuscode 407) hin: Der Client konnte nicht beim Proxyserver authentifiziert werden.
   * ErrorCode.CiSApplianceDNSError – weist auf eine WebExceptionStatus.NameResolutionFailure-Ausnahme hin: Der Namensauflösungsdienst konnte den Hostnamen nicht auflösen.
   * ErrorCode.CiSApplianceACSError – gibt an, dass der Dienst einen Authentifizierungsfehler zurückgegeben hat, während eine Verbindung vorliegt.
     
     Wenn keine Webausnahme ausgelöst wird, überprüfen Sie auf ErrorCode.CiSApplianceSaasServiceError. Dies gibt an, dass ein Problem mit dem StorSimple-Geräte-Manager-Dienst vorliegt.
6. Überprüfen Sie die Azure Service Bus-Verbindung. ErrorCode.CiSApplianceServiceBusError – gibt an, dass das Gerät nicht mit dem Service Bus verbunden werden kann.

Die Protokolldateien "CiSCommandletLog0Curr.errlog" und "CiSAgentsvc0Curr.errlog" enthalten weitere Informationen, wie z. B. Details der Ausnahme.

Weitere Informationen zur Verwendung des Cmdlets finden Sie unter [Test-HcsmConnection](/previous-versions/windows/powershell-scripting/dn715782(v=wps.630)) in der Windows PowerShell-Referenzdokumentation.

> [!IMPORTANT]
> Sie können dieses Cmdlet für den aktiven und den passiven Controller ausführen.

Im Folgenden sind Beispiele für die Ausgabe des Cmdlets `Test-HcsmConnection` aufgeführt.

**Beispielausgabe – erfolgreich registriertes Gerät mit StorSimple Update 3**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Beispielausgabe – Offlinegerät** 

Dieses Beispiel stammt von einem Gerät mit dem Status **Offline** im Azure-Portal.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Das Gerät konnte unter Verwendung der aktuellen Webproxy-Konfiguration keine Verbindung herstellen. Dies kann durch ein Problem mit der Webproxy-Konfiguration oder mit der Netzwerkverbindung verursacht sein. In diesem Fall sollten Sie sicherstellen, dass Ihre Webproxy-Einstellungen korrekt sind und der Webproxyserver online und erreichbar ist.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Problembehandlung mit dem Cmdlet Sync-HcsTime
Verwenden Sie dieses Cmdlet, um die Gerätezeit anzuzeigen. Wenn die Gerätezeit eine Abweichung mit dem NTP-Server aufweist, können Sie dieses Cmdlet verwenden, um eine Synchronisierung mit dem NTP-Server zu erzwingen.
- Wenn die Abweichung zwischen dem Gerät und dem NTP-Server mehr als 5 Minuten beträgt, wird eine Warnung angezeigt. 
- Wenn die Abweichung 15 Minuten überschreitet, wird das Gerät offline geschaltet. Sie können dieses Cmdlet immer noch verwenden, um eine Zeitsynchronisierung zu erzwingen. 
- Wenn die Abweichung jedoch 15 Stunden überschreitet, können Sie keine Synchronisierung erzwingen und es wird eine Warnung angezeigt.

**Beispielausgabe – erzwungene Zeitsynchronisierung mit Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Problembehandlung mit den Cmdlets Enable-HcsPing und Disable-HcsPing
Verwenden Sie diese Cmdlets, um sicherzustellen, dass die Netzwerkschnittstellen auf Ihrem Gerät auf ICMP-Ping-Anforderungen reagieren. Standardmäßig antworten StorSimple-Netzwerkschnittstellen nicht auf Ping-Anforderungen. Die Verwendung dieses Cmdlet ist die einfachste Möglichkeit, festzustellen, ob das Gerät online und erreichbar ist.

**Beispielausgabe - Enable-HcsPing und Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Problembehandlung mit dem Cmdlet Trace-HcsRoute
Verwenden Sie dieses Cmdlet als ein Werkzeug zum Verfolgen von Routen. Es sendet über einen bestimmten Zeitraum Pakete an alle Router auf dem Weg zu einem Ziel und berechnet dann die Ergebnisse auf Grundlage der von den einzelnen Hops zurückgegebenen Pakete. Da das Cmdlet das Maß an Paketverlust bei jedem Router oder jeder Verbindung anzeigt, können Sie feststellen, durch welche Router oder welche Verknüpfungen Netzwerkprobleme verursacht werden können.

**Beispielausgabe die zeigt, wie man die Route eines Pakets mit Trace-HcsRoute verfolgt**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Problembehandlung mit dem Cmdlet Get-HcsRoutingTable
Verwenden Sie dieses Cmdlet, um die Routingtabelle für Ihr StorSimple-Gerät anzuzeigen. Eine Routing-Tabelle ist ein Satz von Regeln, die helfen zu bestimmen, wohin ein Datenpaket, das über ein Internetprotokoll (IP) läuft, gesendet wird.

Die Routingtabelle zeigt die Schnittstellen und das Gateway, das die Daten an die angegebenen Netzwerke weiterleitet. Darüber hinaus wird die Routingmetrik angegeben, die der Entscheidungsträger für den Pfad zu ein bestimmtes Ziel ist. Je niedriger die Routingmetrik, desto höher ist die Voreinstellung.

Wenn Sie z. B. über zwei mit dem Internet verbundene Netzwerkschnittstellen verfügen, DATA 2 und DATA 3 für Daten. Wenn die Routingmetrik für DATA 2 und DATA 3 15 bzw. 261 sind, ist DATA 2 mit der niedrigeren Routingmetrik die bevorzugte Schnittstelle für die Verbindungsherstellung mit dem Internet.

Wenn Sie Update 1 auf dem StorSimple-Gerät ausführen, hat die Netzwerkschnittstelle von DATA 0 die höchste Einstellung für den Cloud-Datenverkehr. Das bedeutet, dass der Cloud-Datenverkehr durch DATA 0 weitergeleitet wird, selbst wenn es andere cloudfähige Schnittstellen gibt.

Wenn Sie das Cmdlet `Get-HcsRoutingTable` ausführen, ohne Parameter anzugeben (wie im folgenden Beispiel gezeigt), gibt das Cmdlet sowohl IPv4- als auch IPv6-Routingtabellen aus. Sie können zum Abrufen einer entsprechenden Routingtabelle auch `Get-HcsRoutingTable -IPv4` oder `Get-HcsRoutingTable -IPv6` angeben.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Schrittweise Beispielanleitung zur StorSimple-Problembehandlung
Im folgenden Beispiel finden Sie schrittweise Anleitungen zur Problembehandlung bei einer StorSimple-Bereitstellung. In diesem Beispielszenario schlägt die Geräteregistrierung mit einer Fehlermeldung fehl, dass die Netzwerkeinstellungen oder der DNS-Name falsch ist.

Die zurückgegebene Fehlermeldung lautet:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

Der Fehler kann eine der folgenden Ursachen haben:

* Falsche Hardwareinstallation
* Fehlerhafte Netzwerkschnittstellen
* Falsche IP-Adresse, Subnetzmaske, Gateway, falscher primärer DNS-Server oder Webproxy
* Falscher Registrierungsschlüssel
* Falsche Firewalleinstellungen

### <a name="to-locate-and-fix-the-device-registration-problem"></a>So ermitteln und beheben Sie das Problem mit der Geräteregistrierung
1. Überprüfen Sie Ihre Gerätekonfiguration: Führen Sie auf dem aktiven Controller `Invoke-HcsSetupWizard`aus.
   
   > [!NOTE]
   > Der Setup-Assistent muss auf dem aktiven Controller ausgeführt werden. Um sicherzustellen, dass Sie mit dem aktiven Controller verbunden sind, überprüfen Sie das Banner in der seriellen Konsole. Das Banner zeigt an, ob eine Verbindung mit Controller 0 oder 1 besteht und ob der Controller aktiv oder passiv ist. Weitere Informationen finden Sie unter [Identifizieren eines aktiven Controllers auf Ihrem Gerät](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Stellen Sie sicher, dass das Gerät richtig verkabelt ist: Überprüfen Sie das Netzwerkkabel an der Rückseite des Geräts. Die Verkabelung ist von Modell zu Modell unterschiedlich. Weitere Informationen finden Sie unter [Installieren des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md) oder [Installieren des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Bei Verwendung von 10-GbE-Netzwerkanschlüsssen, müssen Sie die bereitgestellten QSFP-SFP-Adapter und SFP-Kabel verwenden. Weitere Informationen finden Sie unter der [Liste der für 10-GbE-Ports empfohlenen Kabel, Switches und Transceiver](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Überprüfen Sie die Integrität der Netzwerkschnittstelle:
   
   * Verwenden Sie das Cmdlet "Get-NetAdapter", um den Status der Netzwerkschnittstellen für DATA 0 zu ermitteln. 
   * Wenn der Link nicht funktioniert, zeigt der Status **IfIndex** an, dass die Schnittstelle ausgefallen ist. Sie müssen dann die Netzwerkverbindung zwischen Anschluss des Geräts und Switch überprüfen. Sie müssen außerdem fehlerhafte Kabel ausfindig machen. 
   * Wenn Sie vermuten, dass der DATA 0-Anschluss am aktiven Controller einen Fehler verursacht hat, können Sie dies nachprüfen, indem Sie eine Verbindung mit dem DATA 0-Anschluss an Controller 1 herstellen. Um dies zu bestätigen, trennen Sie das Netzwerkkabel von der Rückseite des Geräts von Controller 0, schließen das Kabel an Controller 1 an und führen dann das Cmdlet "Get-NetAdapter" erneut aus.
     Wenn der DATA 0-Anschluss an einem Controller einen Fehler verursacht, [wenden Sie sich für die nächsten Schritte an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md) . Möglicherweise müssen Sie den Controller in Ihrem System ersetzen.
4. Überprüfen Sie die Verbindung mit dem Switch:
   
   * Stellen Sie sicher, dass die DATA 0-Netzwerkschnittstellen an Controller 0 und Controller 1 im primären Gehäuse sich im gleichen Subnetz befinden. 
   * Überprüfen Sie den Hub oder Router. In der Regel sollten Sie beide Controller mit dem gleichen Hub oder Router verbinden. 
   * Stellen Sie sicher, dass die für die Verbindung verwendeten Switches über DATA 0 für beide Controller im gleichen vLAN verfügen.
5. Ausschließen etwaiger Benutzerfehler:
   
   * Führen Sie den Setup-Assistenten erneut aus (führen Sie **Invoke-HcsSetupWizard**) aus, und geben Sie die Werte erneut ein, um sicherzustellen, dass keine Fehler vorliegen. 
   * Überprüfen Sie den verwendeten Registrierungsschlüssel. Der gleiche Registrierungsschlüssel kann für die Verbindung mehrerer Geräte mit dem StorSimple-Geräte-Manager-Dienst verwendet werden. Verwenden Sie das Verfahren in [Abrufen des Dienstregistrierungsschlüssels](storsimple-8000-manage-service.md#get-the-service-registration-key) , um sicherzustellen, dass Sie den richtigen Registrierungsschlüssel nutzen.
     
     > [!IMPORTANT]
     > Wenn Sie mehrere Dienste ausführen, müssen Sie sicherstellen, dass der Registrierungsschlüssel für den entsprechenden Dienst verwendet wird, um das Gerät zu registrieren. Wenn Sie ein Gerät beim falschen StorSimple-Geräte-Manager-Dienst registriert haben, wenden Sie sich für die nächsten Schritte [an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md). Sie müssen das Gerät möglicherweise auf die Werkseinstellungen zurücksetzen (kann zu Datenverlusten führen), um anschließend eine Verbindung mit dem gewünschten Dienst herzustellen.
     > 
     > 
6. Verwenden Sie das Cmdlet "Test-Connection", um sicherzustellen, dass eine Netzwerkverbindung mit dem externen Netzwerk besteht. Weitere Informationen finden Sie unter [Problembehandlung mit dem Cmdlet "Test-Connection"](#troubleshoot-with-the-test-connection-cmdlet).
7. Überprüfen Sie auf Firewallstörungen. Wenn Sie überprüft haben, dass virtuelle IP-Adresse (VIP), Subnetzmaske, Gateway und DNS-Einstellungen richtig sind, und es liegen weiterhin Verbindungsprobleme vor, blockiert Ihre Firewall möglicherweise die Kommunikation zwischen dem Gerät und dem externen Netzwerk. Stellen Sie sicher, dass die Ports 80 und 443 auf Ihrem StorSimple-Gerät für die ausgehende Kommunikation verfügbar sind. Weitere Informationen finden Sie unter [Netzwerkanforderungen für das StorSimple-Gerät](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Überprüfen Sie die Protokolle. Gehen Sie zu [Für die Problembehandlung verfügbare Supportpakete und Geräteprotokolle](#support-packages-and-device-logs-available-for-troubleshooting).
9. Wenn das Problem durch die vorhergehenden Schritte nicht behoben wird, [wenden Sie sich an den Microsoft Support](storsimple-8000-contact-microsoft-support.md) , um Hilfe zu erhalten.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [das StorSimple-Diagnosetool zum Beheben von Problemen mit StorSimple-Geräten verwenden](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd379547(v=ws.10)
[2]: /previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd392266(v=ws.10)