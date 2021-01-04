---
title: Geräteverwaltung mit PowerShell für StorSimple
description: Erfahren Sie, wie Sie Windows PowerShell für StorSimple zum Verwalten von StorSimple-Geräten verwenden.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 65e9657c3948d8ce5883cd33ca8720f501352105
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995425"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Verwenden von Windows PowerShell für StorSimple zum Verwalten eines StorSimple-Geräts

## <a name="overview"></a>Übersicht

Windows PowerShell für StorSimple stellt eine Befehlszeilenschnittstelle zur Verfügung, die Sie zum Verwalten Ihres Microsoft Azure StorSimple-Geräts verwenden können. Wie der Name schon sagt, handelt es um eine auf Windows PowerShell basierende Befehlszeilenschnittstelle in einem eingeschränkten Runspace. Aus Sicht des Benutzers an der Befehlszeile wird ein eingeschränkter Runspace als eingeschränkte Version von Windows PowerShell angezeigt. Obwohl einige grundlegende Funktionen von Windows PowerShell erhalten bleiben, verfügt diese Schnittstelle über weitere dedizierte Cmdlets, die auf die Verwaltung des Microsoft Azure StorSimple-Geräts ausgelegt sind.

In diesem Artikel werden die Featues von Windows PowerShell für StorSimple beschrieben. Sie erfahren z. B., wie Sie eine Verbindung mit dieser Schnittstelle herstellen können. Ferner finden Sie Links zu schrittweisen Verfahren oder Workflows, die über diese Schnittstelle ausgeführt werden können. Die Workflows ermöglichen u. a. Folgendes: das Registrieren Ihres Geräts, das Konfigurieren der Netzwerkschnittstelle auf Ihrem Gerät, das Installieren Typen von Updates, für die es erforderlich ist, dass sich das Gerät im Wartungsmodus befindet, das Ändern des Gerätestatus und das Beheben etwaiger auftretender Probleme.

In diesem Artikel lernen Sie Folgendes:

* Herstellen einer Verbindung mit Ihrem StorSimple-Gerät über Windows PowerShell für StorSimple.
* Verwalten Ihres StorSimple-Geräts über Windows PowerShell für StorSimple.
* Abrufen von Hilfe in Windows PowerShell für StorSimple.

> [!NOTE]
> * Windows PowerShell für StorSimple-Cmdlets ermöglicht Ihnen die Verwaltung Ihres StorSimple-Geräts über eine serielle Konsole oder die Remoteverwaltung über Windows PowerShell-Remoting. Weitere Informationen zu den einzelnen Cmdlets, die an dieser Schnittstelle verwendet werden können, finden Sie in der [Cmdlet-Referenz für Windows PowerShell für StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).
> * Die Azure PowerShell-Cmdlets für StorSimple sind eine andere Sammlung von Cmdlets, die Ihnen die Automatisierung von Aufgaben auf StorSimple-Dienstebene und Migrationsaufgaben über die Befehlszeile ermöglichen. Weitere Informationen zu den Azure PowerShell-Cmdlets für StorSimple finden Sie in der [Referenz zu den Azure StorSimple-Cmdlets](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Es gibt zwei Möglichkeiten des Zugriffs auf Windows PowerShell für StorSimple:

* [Herstellen einer Verbindung mit StorSimple mithilfe der seriellen Konsole des Geräts](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Herstellen einer Remoteverbindung mit StorSimple mithilfe von Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Herstellen einer Verbindung mit StorSimple mithilfe der seriellen Konsole des Geräts

Sie können [PuTTY herunterladen](https://www.putty.org/) oder auch eine ähnliche Terminalemulationssoftware zum Herstellen einer Verbindung mit Windows PowerShell für StorSimple verwenden. Sie müssen PuTTY entsprechend konfigurieren, um auf das Microsoft Azure StorSimple-Gerät zuzugreifen. In den folgenden Themen finden Sie ausführliche Schritte zum Konfigurieren von PuTTY sowie zum Herstellen einer Verbindung mit dem Gerät. Verschiedene Menüoptionen in der seriellen Konsole werden ebenfalls erläutert.

### <a name="putty-settings"></a>PuTTY-Einstellungen

Stellen Sie sicher, dass die folgenden PuTTY-Einstellungen zum Herstellen einer Verbindung mit der Windows PowerShell-Benutzeroberfläche über die serielle Konsole verwendet werden.

#### <a name="to-configure-putty"></a>So konfigurieren Sie PuTTY

1. Wählen Sie im Dialogfeld **Neukonfiguration** von PuTTY im Bereich **Kategorie** die Option **Tastatur** aus.
2. Stellen Sie sicher, dass die folgenden Optionen aktiviert sind (dies sind die Standardeinstellungen, wenn Sie eine neue Sitzung starten).
   
   | Tastaturelement | Select |
   | --- | --- |
   | RÜCKTASTE |Strg+? (127) |
   | POS1- und Ende-Tasten |Standard |
   | Funktionstasten und Zehnertastatur |ESC[n~ |
   | Ursprünglicher Status der Pfeiltasten |Normal |
   | Ursprünglicher Status der Zehnertastatur |Normal |
   | Zusätzliche Tastaturfunktionen aktivieren |Strg+Alt unterscheidet sich von AltGr |
   
    ![Unterstützte PuTTY-Einstellungen](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Klicken Sie auf **Anwenden**.
4. Wählen Sie im Bereich **Kategorie** die Option **Übersetzung** aus.
5. Wählen Sie im Listenfeld **Remotezeichensatz** die Option **UTF-8** aus.
6. Klicken Sie unter **Handhabung von Strichzeichnungszeichen** auf **Verwenden von Unicode-Strichzeichnungscodepunkten**. Der folgende Screenshot zeigt die ordnungsgemäße PuTTY-Auswahl.
   
    ![PuTTY-Einstellungen für UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Klicken Sie auf **Anwenden**.

Sie können nun mit PuTTY eine Verbindung mit der seriellen Konsole des Geräts herstellen, indem Sie die folgenden Schritte ausführen.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Informationen zur seriellen Konsole

Wenn Sie auf die Windows PowerShell-Benutzeroberfläche Ihres StorSimple-Geräts über die serielle Konsole zugreifen, wird eine Bannermeldung angezeigt, auf die Menüoptionen folgen.

Die Bannermeldung enthält grundlegende StorSimple-Geräteinformationen, z. B. Modell, Name, installierte Softwareversion und Status des Controllers, auf den Sie zugreifen. Die folgende Abbildung zeigt ein Beispiel einer Bannermeldung.

![Meldungsbanner in der seriellen Konsole](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Sie können anhand der Bannermeldung feststellen, ob der Controller, mit dem Sie verbunden sind, _aktiv_ oder _passiv_ ist.

Die folgende Abbildung zeigt die Runspaceoptionen, die im Menü der seriellen Konsole verfügbar sind.

![Registrieren des Geräts 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Sie können zwischen folgenden Einstellungen wählen:

1. **Anmelden mit Vollzugriff:** Diese Option erlaubt Ihnen das Herstellen einer Verbindung (mit den richtigen Anmeldeinformationen) mit dem **SSAdminConsole**-Runspace auf dem lokalen Controller. (Der lokale Controller ist der Controller, auf den Sie aktuell über die serielle Konsole Ihres StorSimple-Geräts zugreifen.) Diese Option kann auch verwendet werden, um dem Microsoft-Support den Zugriff auf den uneingeschränkten Runspace (eine Supportsitzung) zum Zweck der Behandlung möglicher Geräteprobleme zu erlauben. Nachdem Sie Option 1 für die Anmeldung verwendet haben, können Sie dem Microsoft-Supporttechniker durch Ausführen eines bestimmten Cmdlets den Zugriff auf den uneingeschränkten Runspace erlauben. Ausführliche Informationen finden Sie unter [Starten einer Supportsitzung](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Anmelden am Peercontroller mit Vollzugriff:** Diese Option ist im Wesentlichen mit Option 1 identisch. Sie können jedoch eine Verbindung (mit den richtigen Anmeldeinformationen) mit dem **SSAdminConsole**-Runspace auf dem Peercontroller herstellen. Da ein StorSimple-Gerät für Hochverfügbarkeit mit zwei Controllern in einer Aktiv/Passiv-Konfiguration betrieben wird, bezieht sich Peer auf den anderen Controller im Gerät, auf das Sie über die serielle Konsole zugreifen.
   Ähnlich wie Option 1 kann diese Option auch verwendet werden, um dem Microsoft-Support den Zugriff auf den uneingeschränkten Runspace auf einem Peercontroller zu erlauben.

3. **Herstellen einer Verbindung mit beschränktem Zugriff** Diese Option wird für den Zugriff auf die Windows PowerShell-Benutzeroberfläche im eingeschränkten Modus verwendet. Sie werden nicht zur Eingabe von Anmeldeinformationen für den Zugriff aufgefordert. Diese Option stellt eine Verbindung mit einem eingeschränkteren Runspace im Vergleich zu den Optionen 1 und 2 her.  Einige der Aufgaben, die über Option 1 verfügbar sind, die in diesem Runspace **nicht* ausgeführt werden können:
   
   * Zurücksetzen auf die Werkseinstellungen
   * Ändern des Kennworts
   * Aktivieren oder Deaktivieren des Supportzugriffs
   * Anwenden von Updates
   * Installieren von Hotfixes

     > [!NOTE]
     > Dies ist die bevorzugte Option, wenn Sie das Administratorkennwort für das Gerät vergessen haben und keine Verbindung über Option 1 oder 2 herstellen können.

4. **Ändern der Sprache** Diese Option ermöglicht das Ändern der Anzeigesprache auf der Windows PowerShell-Benutzeroberfläche. Die unterstützten Sprachen sind Chinesisch, Deutsch, Englisch, Französisch, Italienisch, Japanisch, Portugiesisch, Russisch, Spanisch und Südkoreanisch.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Herstellen einer Remoteverbindung mit StorSimple mithilfe von Windows PowerShell für StorSimple

Sie können Windows PowerShell-Remoting zum Herstellen einer Verbindung mit dem StorSimple-Gerät verwenden. Wenn Sie auf diese Weise eine Verbindung herstellen, wird kein Menü angezeigt. (Sie sehen nur ein Menü, wenn Sie zum Verbinden die serielle Konsole auf dem Gerät verwenden. Beim Herstellen einer Remoteverbindung werden Sie direkt zur Entsprechung von „Option 1 – Vollzugriff“ auf der seriellen Konsole weitergeleitet.) Mit Windows PowerShell-Remoting stellen Sie eine Verbindung mit einem bestimmten Runspace her. Sie können zudem die Anzeigesprache angeben.

Die Anzeigesprache ist unabhängig von der Sprache, die Sie mithilfe der Option **Sprache ändern** im Menü der seriellen Konsole festlegen. Remote PowerShell wählt automatisch das Gebietsschema des Geräts aus, mit dem Sie eine Verbindung herstellen, wenn keines angegeben ist.

> [!NOTE]
> Wenn Sie virtuelle Microsoft Azure-Hosts und virtuelle StorSimple Cloud Appliances einsetzen, können Sie Windows PowerShell-Remoting und den virtuellen Host verwenden, um eine Verbindung mit der Cloud Appliance herzustellen. Falls Sie einen freigegebenen Speicherort auf dem Host eingerichtet haben, auf dem Informationen aus der Windows PowerShell-Sitzung gespeichert werden, sollten Sie daran denken, dass der Prinzipal _Jeder_ nur authentifizierte Benutzer enthält. Wenn Sie also die Freigabe so eingerichtet haben, dass _Jeder_ Zugriff erhält, und Sie die Verbindung ohne Angabe von Anmeldeinformationen herstellen, wird der nicht authentifizierte Prinzipal „Anonym“ verwendet, weshalb ein Fehler auftritt. Zum Beheben dieses Problems müssen Sie auf dem Freigabehost das Gastkonto aktivieren und diesem dann Vollzugriff auf die Freigabe gewähren, oder Sie müssen gültige Anmeldeinformationen mit einem Windows PowerShell-Cmdlet angeben.


Sie können HTTP oder HTTPS verwenden, um über Windows PowerShell-Remoting eine Verbindung herzustellen. In den folgenden Tutorials werden die einzelnen Methoden beschrieben:

* [Herstellen einer Remoteverbindung über HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Herstellen einer Remoteverbindung über HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Überlegungen zur Verbindungssicherheit

Bei der Wahl der Verbindungsmethode für Windows PowerShell für StorSimple sollten Sie Folgendes beachten:

* Das direkte Verbinden mit der seriellen Konsole des Geräts ist sicher, was für das Verbinden mit der seriellen Konsole über Netzwerkswitches nicht gilt. Beachten Sie das Sicherheitsrisiko bei Verbindungen mit dem seriellen Gerät über Netzwerkswitches.
* Eine Verbindung über eine HTTP-Sitzung ist u. U. sicherer als eine Verbindung über die serielle Konsole über das Netzwerk. Obwohl dies nicht die sicherste Methode ist, ist eine solche Verbindung bei vertrauenswürdigen Netzwerken akzeptabel.
* Eine Verbindung über eine HTTPS-Sitzung ist die sicherste und empfohlene Option.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Verwalten Ihres StorSimple-Geräts über Windows PowerShell für StorSimple

Die folgende Tabelle zeigt eine Zusammenfassung aller allgemeinen Verwaltungsaufgaben und komplexen Workflows, die über die Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts ausgeführt werden können. Klicken Sie auf den entsprechenden Eintrag in der Tabelle, um weitere Informationen zu den einzelnen Workflows zu erhalten.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell für StorSimple-Workflows

| Gewünschte Aktion | Verfahren |
| --- | --- |
| Registrieren Ihres Geräts |[Konfigurieren und Registrieren des Geräts über Windows PowerShell für StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurieren des Webproxys</br>Anzeigen von Webproxyeinstellungen |[Konfigurieren des Webproxys für Ihr StorSimple-Gerät](storsimple-8000-configure-web-proxy.md) |
| Ändern der Einstellungen der DATA 0-Netzwerkschnittstelle auf dem Gerät |[Ändern der Einstellungen der DATA 0-Netzwerkschnittstelle auf dem StorSimple-Gerät](storsimple-8000-modify-data-0.md) |
| Beenden eines Controllers </br> Neustarten oder Herunterfahren eines Controllers </br> Herunterfahren eines Geräts</br>Zurücksetzen des Geräts auf die werkseitigen Standardeinstellungen |[Verwalten von Gerätecontrollern](storsimple-8000-manage-device-controller.md) |
| Installieren von Updates und Hotfixes im Wartungsmodus |[Aktualisieren Ihres Geräts](storsimple-update-device.md) |
| Wechseln in den Wartungsmodus </br>Beenden des Wartungsmodus |[StorSimple-Gerätemodi](storsimple-8000-device-modes.md) |
| Erstellen eines Unterstützungspakets</br>Entschlüsseln und Bearbeiten eines Unterstützungspakets |[Erstellen und Verwalten eines Unterstützungspakets](storsimple-8000-create-manage-support-package.md) |
| Starten einer Supportsitzung</br> |[Starten einer Supportsitzung in Windows PowerShell für StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Abrufen von Hilfe in Windows PowerShell für StorSimple

Windows PowerShell für StorSimple bietet Hilfe zu Cmdlets. Diese Hilfe ist auch als aktuelle Onlineversion verfügbar, die Sie zum Aktualisieren der auf Ihrem System installierten Hilfe-Inhalte verwenden können.

Hilfe-Informationen werden an dieser Schnittstelle ähnlich wie in Windows PowerShell abgerufen, und die meisten hilfebezogenen Cmdlets funktionieren. Hilfe zu Windows PowerShell können Sie online hier finden: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Im Folgenden finden Sie eine kurze Beschreibung der Hilfetypen für diese Windows PowerShell-Schnittstelle und Anweisungen zum Aktualisieren der Hilfe.

### <a name="to-get-help-for-a-cmdlet"></a>So erhalten Sie Hilfe zu einem Cmdlet

* Um Hilfe zu einem Cmdlet oder eine Funktion zu erhalten, verwenden Sie den folgenden Befehl: `Get-Help <cmdlet-name>`
* Um Onlinehilfe zu einem beliebigen Cmdlet zu erhalten, verwenden Sie das vorherige Cmdlet mit dem Parameter `-Online`: `Get-Help <cmdlet-name> -Online`
* Mit dem `–Full`-Parameter können Sie die vollständige Hilfe anzeigen. Der `–Examples`-Parameter dient zum Anzeigen von Beispielen.

### <a name="to-update-help"></a>So aktualisieren Sie die Hilfe

Sie können die Hilfe in der Windows PowerShell-Schnittstelle problemlos aktualisieren. Führen Sie die folgenden Schritte aus, um die auf Ihrem System installierte Hilfe zu aktualisieren:

#### <a name="to-update-cmdlet-help"></a>So aktualisieren Sie die Cmdlet-Hilfe
1. Starten Sie Windows PowerShell mit der Option **Als Administrator ausführen** .
2. Geben Sie an der Eingabeaufforderung Folgendes ein: `Update-Help`.
3. Die aktualisierten Hilfedateien werden installiert.
4. Geben Sie nach der Installation der Hilfedateien Folgendes ein: `Get-Help Get-Command`. Dadurch wird eine Liste mit Cmdlets angezeigt, für die Hilfe verfügbar ist.

> [!NOTE]
> Wenn Sie eine Liste aller verfügbaren Cmdlets in einem der Runspaces abrufen möchten, melden Sie sich bei der entsprechenden Menüoption an, und führen Sie dann das Cmdlet `Get-Command` aus.


## <a name="next-steps"></a>Nächste Schritte

Wenn Probleme auf Ihrem StorSimple-Gerät bei einem der zuvor genannten Workflows auftreten, konsultieren Sie die [Tools zur Problembehandlung für StorSimple-Bereitstellungen](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).