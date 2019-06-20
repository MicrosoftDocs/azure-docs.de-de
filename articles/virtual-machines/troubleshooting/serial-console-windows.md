---
title: Die serielle Azure-Konsole für Windows | Microsoft-Dokumentation
description: Bidirektionale serielle Konsole für Azure Virtual Machines und Virtual Machine Scale Sets
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 8fea35a0fc5469d030092e5440ff1f19aa08b2a4
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238868"
---
# <a name="azure-serial-console-for-windows"></a>Die serielle Azure-Konsole für Windows

Die serielle Konsole im Azure-Portal ermöglicht den Zugriff auf eine textbasierte Konsole für virtuelle Windows-Computer (VMs) und Instanzen von VM-Skalierungsgruppen. Diese serielle Verbindung erfolgt über den seriellen COM1-Port der VM oder der VM-Skalierungsgruppe und ermöglicht Zugriff auf diesen, unabhängig vom Zustand des Netzwerks oder Betriebssystems. Auf die serielle Konsole kann nur über das Azure-Portal und von Benutzern zugegriffen werden, die mindestens über die Zugriffsrolle „Mitwirkender“ für die VM oder VM-Skalierungsgruppeninstanzen verfügen.

Die serielle Konsole funktioniert auf die gleiche Weise für VMs und VM-Skalierungsgruppeninstanzen. Deshalb beziehen sich alle Äußerungen bezüglich VMs in dieser Dokumentation, sofern nicht anders angegeben, implizit auch auf VM-Skalierungsgruppeninstanzen.

Die Dokumentation zur seriellen Konsole für Linux-VMs und VM-Skalierungsgruppen finden Sie unter [Serielle Azure-Konsole für Linux](serial-console-linux.md).

> [!NOTE]
> Die serielle Konsole ist in den globalen Azure-Regionen allgemein verfügbar. Sie ist noch nicht in den Clouds „Azure Government“ und „Azure China“ verfügbar.


## <a name="prerequisites"></a>Voraussetzungen

* Ihre VM oder VM-Skalierungsgruppeninstanz muss das Bereitstellungsmodell für die Ressourcenverwaltung verwenden. Klassische Bereitstellungen werden nicht unterstützt.

- Ihr Konto, das eine serielle Konsole verwendet, muss die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) für die VM und das Speicherkonto [Startdiagnose](boot-diagnostics.md) aufweisen.

- Ihre VM oder VM-Skalierungsgruppeninstanz muss über einen kennwortbasierten Benutzer verfügen. Mit der Funktion [Kennwort zurücksetzen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) der Erweiterungen für den Zugriff auf virtuelle Computer können Sie eines erstellen. Wählen Sie im Abschnitt **Support + Problembehandlung** **Kennwort zurücksetzen** aus.

* Auf der VM, auf der Sie auf eine serielle Konsole zugreifen, muss [Startdiagnose](boot-diagnostics.md) aktiviert sein.

    ![Einstellungen der Startdiagnose](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Erste Schritte mit der seriellen Konsole
Auf die serielle Konsole für VMs und VM-Skalierungsgruppen kann nur über das Azure-Portal zugegriffen werden:

### <a name="serial-console-for-virtual-machines"></a>Serielle Konsole für virtuelle Computer
Für den Zugriff auf die serielle Konsole für VMs müssen Sie lediglich auf **Serielle Konsole** im Abschnitt **Support + Problembehandlung** des Azure-Portals klicken.
  1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

  1. Navigieren Sie zu **Alle Ressourcen**, und wählen Sie eine VM aus. Die Übersichtsseite für die VM wird geöffnet.

  1. Scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**, und wählen Sie **Serielle Konsole** aus. Ein neuer Bereich mit der seriellen Konsole wird geöffnet, und die Verbindung wird hergestellt.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Serielle Konsole für Virtual Machine Scale Sets
Die serielle Konsole steht pro Instanz für VM-Skalierungsgruppen zur Verfügung. Sie müssen zur individuellen Instanz einer VM-Skalierungsgruppe navigieren, um die Schaltfläche **Serielle Konsole** zu finden. Wenn die Startdiagnose nicht für Ihre VM-Skalierungsgruppe aktiviert ist, stellen Sie sicher, dass Sie Ihr VM-Skalierungsgruppenmodell aktualisieren, um die Startdiagnose zu aktivieren, und führen Sie das Upgrades für alle Instanzen auf das neue Modell durch, um auf die serielle Konsole zugreifen zu können.
  1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

  1. Navigieren Sie zu **Alle Ressourcen**, und wählen Sie eine VM-Skalierungsgruppe aus. Die Übersichtsseite für die VM-Skalierungsgruppe wird geöffnet.

  1. Navigieren Sie zu **Instanzen**.

  1. Wählen Sie eine VM-Skalierungsgruppeninstanz aus.

  1. Klicken Sie im Abschnitt **Support + Problembehandlung** auf **Serielle Konsole**. Ein neuer Bereich mit der seriellen Konsole wird geöffnet, und die Verbindung wird hergestellt.

## <a name="enable-serial-console-functionality"></a>Aktivieren der Funktionalität der seriellen Konsole

> [!NOTE]
> Wenn in der seriellen Konsole nichts angezeigt wird, vergewissern Sie sich, dass die Startdiagnose auf Ihrer VM oder Ihrer VM-Skalierungsgruppeninstanz aktiviert ist.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Aktivieren der seriellen Konsole in benutzerdefinierten oder älteren Images
Für neuere Windows Server-Images in Azure ist [Spezielle Verwaltungskonsole](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (Special Administrative Console, SAC) standardmäßig aktiviert. Die SAC wird unter Serverversionen von Windows unterstützt, ist aber unter Clientversionen (wie Windows 10, Windows 8 oder Windows 7) nicht verfügbar.

Für ältere Windows Server-Images (erstellt vor Februar 2018) können Sie die serielle Konsole automatisch über das Befehlsausführungsfeature im Azure-Portal aktivieren. Wählen Sie im Azure-Portal **Befehl ausführen** aus, und wählen Sie dann den Befehl **EnableEMS** in der Liste aus.

![Ausführen-Befehlsliste](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Gehen Sie alternativ zum manuellen Aktivieren der seriellen Konsole für Windows-VMs/VM-Skalierungsgruppen, die vor Februar 2018 erstellt wurden, wie folgt vor:

1. Stellen Sie über den Remotedesktop eine Verbindung mit Ihrem virtuellen Windows-Computer her.
1. Führen Sie an einer Administratoreingabeaufforderung die folgenden Befehle aus:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Starten Sie das System neu, damit die SAC-Konsole aktiviert wird.

    ![SAC-Konsole](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Bei Bedarf kann die SAC auch offline aktiviert werden:

1. Fügen Sie den gewünschten Windows-Datenträger an, für den die SAC als Datenträger für die vorhandene VM konfiguriert werden soll.

1. Führen Sie an einer Administratoreingabeaufforderung die folgenden Befehle aus:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Wie kann ich feststellen, ob SAC aktiviert ist?

Wenn [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nicht aktiviert ist, zeigt die serielle Konsole die SAC-Eingabeaufforderung nicht an. In einigen Fällen werden Integritätsinformationen zur VM angezeigt, in anderen Fällen ist sie leer. Wenn Sie ein Windows Server-Image verwenden, das vor Februar 2018 erstellt wurde, wird die SAC wahrscheinlich nicht aktiviert.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Aktivieren des Windows-Startmenüs in der seriellen Konsole

Wenn Sie Windows-Startlade-Eingabeaufforderungen in der seriellen Konsole aktivieren müssen, können Sie den Startkonfigurationsdaten die folgenden zusätzlichen Optionen hinzufügen. Weitere Informationen finden Sie unter [BCDEdit/set](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Stellen Sie über den Remotedesktop eine Verbindung mit Ihrer Windows-VM bzw. Ihrer VM-Skalierungsgruppeninstanz her.

1. Führen Sie an einer Administratoreingabeaufforderung die folgenden Befehle aus:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Starten Sie das System neu, damit das Startmenü aktiviert wird.

> [!NOTE]
> Das Timeout, das Sie für das Start-Manager-Menü festlegen, wirkt sich auf den Startzeitpunkt Ihres Betriebssystems aus. Wenn der Timeoutwert von 10 Sekunden Ihrer Einschätzung nach zu kurz oder zu lang ist, legen Sie einen anderen Wert fest.

## <a name="use-serial-console"></a>Verwenden der seriellen Konsole

### <a name="use-cmd-or-powershell-in-serial-console"></a>Verwenden von CMD oder PowerShell in der seriellen Konsole

1. Stellen Sie eine Verbindung mit der seriellen Konsole her. Wenn Sie eine Verbindung hergestellt haben, lautet die Eingabeaufforderung **SAC>** :

    ![Herstellen einer Verbindung mit SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Geben Sie `cmd` ein, um einen Kanal zu erstellen, der über eine CMD-Instanz verfügt.

1.  Geben Sie `ch -si 1` ein, um zu dem Kanal zu wechseln, in dem die CMD-Instanz ausgeführt wird.

1.  Drücken Sie die **EINGABETASTE**, und geben Sie dann die Anmeldeinformationen mit Administratorrechten ein.

1.  Nachdem Sie gültige Anmeldeinformationen eingegeben haben, wird die CMD-Instanz geöffnet.

1.  Geben Sie zum Starten einer PowerShell-Instanz `PowerShell` in der CMD-Instanz ein, und drücken Sie die **EINGABETASTE**.

    ![Öffnen einer PowerShell-Instanz](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Verwenden der seriellen Konsole für NMI-Aufrufe
Ein nicht maskierbarer Interrupt (NMI) dient dazu, ein Signal zu erstellen, das die Software auf einem virtuellen Computer nicht ignoriert. In der Vergangenheit wurden NMIs verwendet, um Hardwareprobleme auf Systemen zu überwachen, die bestimmte Antwortzeiten erforderten. Heute verwenden Programmierer und Systemadministratoren NMIs häufig als Mechanismus zum Debuggen oder Beheben von Problemen in Systemen, die nicht mehr reagieren.

Sie können mit der seriellen Konsole einen NMI an einen virtuellen Azure-Computer senden. Verwenden Sie dazu das Tastatursymbol in der Befehlsleiste. Nach der Übermittlung des NMI bestimmt die Konfiguration des virtuellen Computers, wie das System reagiert. Windows kann so konfiguriert werden, dass beim Empfang eines NMI ein Absturz erfolgt und eine Speicherabbilddatei erstellt wird.

![Senden eines NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Weitere Informationen zum Konfigurieren von Windows zum Erstellen eines Absturzabbilds beim Empfang eines NMI finden Sie unter [Gewusst wie: Erstellen einer vollständigen Absturzabbilddatei oder einer Kernel-Absturzabbilddatei mit einem NMI auf einem Windows-basierten System](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Verwenden von Funktionstasten in der seriellen Konsole
Funktionstasten sind für die Verwendung in der seriellen Konsole auf virtuellen Windows-Computern aktiviert. F8 in der Dropdownliste der seriellen Konsole bietet die Möglichkeit, einfach in das Menü mit den erweiterten Starteinstellungen zu gelangen. Die serielle Konsole ist jedoch mit allen anderen Funktionstasten kompatibel. Möglicherweise müssen Sie **Fn** + **F1** (oder F2, F3 usw.) auf Ihrer Tastatur drücken, je nachdem, auf welchem Computer Sie die serielle Konsole verwenden.

### <a name="use-wsl-in-serial-console"></a>Verwenden von WSL in der seriellen Konsole
Das Windows-Subsystem für Linux (WSL) wurde für Windows Server 2019 oder höher aktiviert. Daher ist es auch möglich, WSL für die Verwendung in der seriellen Konsole zu aktivieren, wenn Sie Windows Server 2019 oder höher ausführen. Dies kann für Benutzer vorteilhaft sein, die auch mit Linux-Befehlen vertraut sind. Anweisungen zur Aktivierung von WSL für Windows Server finden Sie in der [Installationsanleitung](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Neustarten Ihrer Windows-VM/VM-Skalierungsgruppeninstanz in der seriellen Konsole
Sie können einen Neustart Ihrer VM in der seriellen Konsole auslösen, indem Sie zum Netzschalter navigieren und auf „VM neu starten“ klicken. Hierdurch wird ein Neustart der VM initiiert, und es wird eine Benachrichtigung hinsichtlich des Neustarts im Azure-Portal angezeigt.

Dies ist hilfreich in Situationen, in denen Sie auf das Startmenü zugreifen möchten, ohne die Benutzeroberfläche der seriellen Konsole zu verlassen.

![Neustart der seriellen Konsole für Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Deaktivieren der seriellen Konsole
Standardmäßig haben alle Abonnements Zugriff auf die serielle Konsole, die für alle virtuellen Computer aktiviert ist. Sie können die serielle Konsole auf Abonnement- oder VM-Ebene deaktivieren.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Deaktivieren der seriellen Konsole für eine VM/VM-Skalierungsgruppe
Die serielle Konsole kann für bestimmte VMs oder VM-Skalierungsgruppen deaktiviert werden, indem die Startdiagnose deaktiviert wird. Deaktivieren Sie die Startdiagnose im Azure-Portal, um die serielle Konsole für eine VM oder VM-Skalierungsgruppe zu deaktivieren. Stellen Sie sicher, dass Sie Ihre VM-Skalierungsgruppeninstanzen auf die neueste Version aktualisieren, wenn Sie die serielle Konsole für eine VM-Skalierungsgruppe verwenden.

> [!NOTE]
> Um die serielle Konsole für ein Abonnement zu aktivieren oder zu deaktivieren, müssen Sie über Schreibberechtigungen für das Abonnement verfügen. Diese Berechtigungen umfassen beispielsweise die Rollen „Administrator“ oder „Besitzer“. Benutzerdefinierte Rollen können ebenfalls über Schreibberechtigungen verfügen.

### <a name="subscription-level-disable"></a>Deaktivieren auf Abonnementebene
Die serielle Konsole kann über den [REST-API-Aufruf zum Deaktivieren der Konsole](/rest/api/serialconsole/console/disableconsole) für ein gesamtes Abonnement deaktiviert werden. Für diese Aktion ist die Zugriffsebene „Mitwirkender“ oder höher für das Abonnement erforderlich. Sie können die auf der Seite der API-Dokumentation verfügbare Funktion **Jetzt testen** verwenden, um die serielle Konsole für ein Abonnement zu deaktivieren und zu aktivieren. Geben Sie unter **subscriptionId** Ihre Abonnement-ID ein, geben Sie unter „default“ **default** ein, und wählen Sie dann **Ausführen** aus. Azure CLI-Befehle sind noch nicht verfügbar.

Um die serielle Konsole für ein Abonnement erneut zu aktivieren, verwenden Sie den [REST-API-Aufruf „Konsole aktivieren“](/rest/api/serialconsole/console/enableconsole).

![REST-API testen](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Alternativ können Sie den folgenden Satz von Bash-Befehlen in Cloud Shell verwenden, um die serielle Konsole für ein Abonnement zu deaktivieren, zu aktivieren oder den Deaktivierungsstatus der seriellen Konsole anzuzeigen:

* So rufen Sie den Deaktivierungsstatus der seriellen Konsole für ein Abonnement ab:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* So deaktivieren Sie die serielle Konsole für ein Abonnement:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* So aktivieren Sie die serielle Konsole für ein Abonnement:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Sicherheit der seriellen Konsole

### <a name="access-security"></a>Zugriffssicherheit
Der Zugriff auf die serielle Konsole ist auf Benutzer eingeschränkt, die über eine Zugriffsrolle als [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) oder höher für den virtuellen Computer verfügen. Wenn für Ihren Azure Active Directory-Mandanten mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) erforderlich ist, ist MFA auch für den Zugriff auf die serielle Konsole erforderlich, weil der Zugriff auf die serielle Konsole über das [Azure-Portal](https://portal.azure.com) erfolgt.

### <a name="channel-security"></a>Kanalsicherheit
Alle gesendeten Daten werden bei der Übertragung verschlüsselt.

### <a name="audit-logs"></a>Überwachungsprotokolle
Alle Zugriffe auf die serielle Konsole werden zurzeit in den Protokollen [Startdiagnose](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) des virtuellen Computers protokolliert. Der Zugriff auf diese Protokolle wird durch den Administrator des virtuellen Azure-Computers (der auch Besitzer dieser Protokolle ist) gesteuert.

> [!CAUTION]
> Zugriffskennwörter für die Konsole werden nicht protokolliert. Wenn jedoch innerhalb der Konsole Befehle ausgeführt werden, die Kennwörter, Geheimnisse, Benutzernamen oder personenbezogene Informationen anderer Art enthalten oder ausgeben, werden diese in den Protokollen der VM-Startdiagnose erfasst. Diese werden gemeinsam mit dem gesamten anderen sichtbaren Text als Teil der Implementierung der Scrollbackfunktion der seriellen Konsole geschrieben. Diese Protokolle sind zirkulär, und nur Personen mit Leseberechtigungen für das Speicherkonto der Diagnose haben auf sie Zugriff. Allerdings empfehlen wir die bewährte Methode, für alles, das Geheimnisse und oder personenbezogene Informationen beinhalten kann, den Remotedesktop zu verwenden.

### <a name="concurrent-usage"></a>Parallele Verwendung
Wenn ein Benutzer mit der seriellen Konsole verbunden ist und ein anderer Benutzer erfolgreich den Zugriff auf denselben virtuellen Computer anfordert, wird der erste Benutzer getrennt und der zweite Benutzer mit der gleichen Sitzung verbunden.

> [!CAUTION]
> Dies bedeutet, dass ein Benutzer, der getrennt wird, nicht abgemeldet wird. Die Möglichkeit, bei der Trennung eine Abmeldung zu erzwingen (mithilfe von SIGHUP oder einem ähnlichen Mechanismus), ist noch nicht implementiert. Für Windows ist in der SAC ein automatisches Timeout aktiviert. Für Linux können Sie die Timeouteinstellung für das Terminal konfigurieren.

## <a name="accessibility"></a>Barrierefreiheit
Die Barrierefreiheit ist ein wichtiger Aspekt bei der seriellen Konsole in Azure. Zu diesem Zweck haben wir sichergestellt, dass auch Personen mit Seh- und Hörbehinderung sowie Personen, die möglicherweise keine Maus nutzen können, auf die serielle Konsole zugreifen können.

### <a name="keyboard-navigation"></a>Tastaturnavigation
Verwenden Sie die **TAB**-Taste auf der Tastatur, um im Azure-Portal in der seriellen Konsolenschnittstelle zu navigieren. Auf dem Bildschirm wird hervorgehoben, wo Sie sich gerade befinden. Um den Fokus vom seriellen Konsolenbereich zu entfernen, drücken Sie **STRG**+**F6** auf der Tastatur.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Verwenden der seriellen Konsole mit einer Sprachausgabe
Die serielle Konsole bietet integrierte Unterstützung für die Sprachausgabe. Beim Navigieren mit aktivierter Sprachausgabe kann der Alternativtext für die aktuell ausgewählte Schaltfläche von der Sprachausgabe vorgelesen werden.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Häufige Szenarien für den Zugriff auf die serielle Konsole

Szenario          | Aktionen in der seriellen Konsole
:------------------|:-----------------------------------------
Falsche Firewallregeln | Greifen Sie auf die serielle Konsole zu, und korrigieren Sie die Windows-Firewallregeln.
Dateisystembeschädigung/-überprüfung | Greifen Sie auf die serielle Konsole zu, und stellen Sie das Dateisystem wieder her.
RDP-Konfigurationsprobleme | Greifen Sie auf die serielle Konsole zu, und ändern Sie die Einstellungen. Weitere Informationen finden Sie in der [Dokumentation zum RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Netzwerksperrsystem | Greifen Sie aus dem Azure-Portal auf die serielle Konsole zu, um das System zu verwalten. Einige Netzwerkbefehle finden Sie unter [Windows-Befehle: CMD und PowerShell](serial-console-cmd-ps-commands.md).
Interaktion mit Bootloader | Greifen Sie über die serielle Konsole auf das BCD zu. Weitere Informationen finden Sie unter [Aktivieren des Windows-Startmenüs in der seriellen Konsole](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Errors
Da die meisten Fehler vorübergehend sind, können sie oftmals durch Wiederherstellen der Verbindung behoben werden. In der folgenden Tabelle wird eine Liste von Fehlern und Gegenmaßnahmen für VMs und VM-Skalierungsgruppeninstanzen aufgeführt.

Error                            |   Lösung
:---------------------------------|:--------------------------------------------|
Die Startdiagnoseeinstellungen für *&lt;VMNAME&gt;* können nicht abgerufen werden. Damit Sie die serielle Konsole verwenden können, stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist. | Stellen Sie sicher, dass für den virtuellen Computer [Startdiagnose](boot-diagnostics.md) aktiviert ist.
Der virtuelle Computer befindet sich in einem beendeten Zustand mit aufgehobener Zuordnung. Starten Sie den virtuellen Computer neu, und wiederholen Sie die Verbindung mit der seriellen Konsole. | Der virtuelle Computer muss den Zustand „Gestartet“ aufweisen, um auf die serielle Konsole zugreifen zu können.
Sie verfügen nicht über die erforderlichen Berechtigungen zum Verwenden der seriellen Konsole dieses virtuellen Computers. Achten Sie darauf, dass Sie mindestens über die Berechtigungen der Rolle „Mitwirkender für virtuelle Computer“ verfügen.| Für den Zugriff auf die serielle Konsole sind bestimmte Berechtigungen erforderlich. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).
Die Ressourcengruppe für das Startdiagnose-Speicherkonto *&lt;STORAGEACCOUNTNAME&gt;* kann nicht ermittelt werden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist und Sie über Zugriff auf dieses Speicherkonto verfügen. | Für den Zugriff auf die serielle Konsole sind bestimmte Berechtigungen erforderlich. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).
Beim Zugriff auf das Speicherkonto für die Startdiagnose dieses virtuellen Computers wurde eine „Forbidden“-Antwort empfangen. | Stellen Sie sicher, dass die Startdiagnose nicht über eine Kontofirewall verfügt. Damit die serielle Konsole funktioniert, ist Zugriff auf ein Startdiagnose-Speicherkonto erforderlich.
Das Websocket ist geschlossen oder konnte nicht geöffnet werden. | Möglicherweise müssen Sie der Whitelist den Eintrag `*.console.azure.com` hinzufügen. Ein detaillierterer, aber längerer Ansatz besteht darin, der Whitelist die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) hinzuzufügen, die sich jedoch relativ regelmäßig ändern.
Es werden nur Integritätsinformationen angezeigt, wenn eine Verbindung mit einem virtuellen Windows-Computer hergestellt wird.| Dieser Fehler tritt auf, wenn die spezielle Verwaltungskonsole (Special Administrative Console, SAC) für Ihr Windows-Image nicht aktiviert wurde. Unter [Aktivieren der seriellen Konsole in benutzerdefinierten oder älteren Images](#enable-the-serial-console-in-custom-or-older-images) finden Sie Anweisungen für das manuelle Aktivieren der SAC auf Ihrer Windows-VM. Weitere Informationen finden Sie in der Dokumentation zu [Windows-Integritätssignalen](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Bekannte Probleme
Uns sind einige Probleme mit der seriellen Konsole bekannt. Hier finden Sie eine Liste dieser Probleme und Schritte zur Lösung. Diese Probleme und Gegenmaßnahmen gelten sowohl für VMs als auch für VM-Skalierungsgruppeninstanzen.

Problem                             |   Lösung
:---------------------------------|:--------------------------------------------|
Das Drücken der **EINGABETASTE** nach dem Verbindungsbanner führt nicht zur Anzeige einer Anmeldeaufforderung. | Weitere Informationen finden Sie unter [Hitting enter does nothing](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) (Das Drücken der Eingabetaste bewirkt nichts). Dieser Fehler kann auftreten, wenn Sie eine benutzerdefinierte VM, eine Appliance mit verstärkter Sicherheit oder eine Startkonfiguration ausführen, und Windows aufgrund dessen keine ordnungsgemäße Verbindung mit dem seriellen Port herstellen kann. Dieser Fehler geschieht auch dann, wenn Sie eine Windows 10-Client-VM ausführen, da nur Windows Server-VMs für EMS konfiguriert sind.
Bei aktiviertem Kerneldebugging ist an der SAC-Eingabeaufforderung keine Eingabe möglich. | Stellen Sie eine RDP-Verbindung mit dem virtuellen Computer her, und führen Sie `bcdedit /debug {current} off` an einer Eingabeaufforderung mit erhöhten Rechten aus. Falls Sie keine RDP-Verbindung herstellen können, fügen Sie stattdessen den Betriebssystemdatenträger an eine andere Azure-VM an, bearbeiten Sie ihn durch Ausführung von `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, während er als Datenträger angefügt ist, und fügen Sie ihn anschließend wieder an die ursprüngliche VM an.
Das Einfügen in PowerShell in der SAC führt zu einem dritten Zeichen, wenn der ursprüngliche Inhalt ein sich wiederholendes Zeichen enthielt. | Eine Problemumgehung besteht darin, `Remove-Module PSReadLine` zum Entladen des PSReadLine-Moduls aus der aktuellen Sitzung auszuführen. Durch diese Aktion wird das Modul nicht gelöscht oder deinstalliert.
Einige Tastatureingaben erzeugen seltsame SAC-Ausgaben (z.B. **[A**, **[3~** ). | [VT100](https://aka.ms/vtsequences)-Escapesequenzen werden von der SAC-Eingabeaufforderung nicht unterstützt.
Das Einfügen von langen Zeichenfolgen funktioniert nicht. | Die serielle Konsole begrenzt die Länge der Zeichenfolgen, die in das Terminal eingefügt werden können, auf 2048 Zeichen, um die Bandbreite am seriellen Port nicht zu überlasten.
Die serielle Konsole funktioniert nicht mit einer Speicherkontofirewall. | Die serielle Konsole kann programmbedingt nicht verwendet werden, wenn für das Speicherkonto mit Startdiagnose Speicherkontofirewalls aktiviert sind.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie kann ich Feedback senden?**

A. Geben Sie Feedback, indem Sie ein GitHub-Problem unter https://aka.ms/serialconsolefeedback erstellen. Alternativ dazu können Sie Feedback über azserialhelp@microsoft.com oder in der Kategorie „Virtuelle Computer“ von https://feedback.azure.com senden (dies sind die weniger bevorzugten Methoden).

**F: Unterstützt die serielle Konsole das Kopieren und Einfügen?**

A. Ja. Verwenden Sie **STRG**+**UMSCHALT**+**C** und **STRG**+**UMSCHALT**+**V** zum Kopieren und Einfügen in das Terminal.

**F: Wer kann die serielle Konsole für mein Abonnement aktivieren oder deaktivieren?**

A. Um die serielle Konsole auf Abonnementebene aktivieren oder deaktivieren zu können, müssen Sie über Schreibberechtigungen für das Abonnement verfügen. Die Rollen „Administrator“ und „Besitzer“ verfügen beispielsweise über Schreibberechtigungen. Benutzerdefinierte Rollen können ebenfalls über Schreibberechtigungen verfügen.

**F: Wer kann auf die serielle Konsole für meinen virtuellen Computer zugreifen?**

A. Sie müssen mindestens der Rolle „Mitwirkender für virtuelle Computer“ einer VM angehören, um auf die serielle Konsole der VM zuzugreifen.

**F: Meine serielle Konsole zeigt nichts an, was muss ich tun?**

A. Ihr Image ist wahrscheinlich nicht richtig für den Zugriff auf die serielle Konsole konfiguriert. Informationen zum Konfigurieren Ihres Images für die Aktivierung der seriellen Konsole finden Sie unter [Aktivieren der seriellen Konsole in benutzerdefinierten oder älteren Images](#enable-the-serial-console-in-custom-or-older-images).

**F: Ist die serielle Konsole für VM-Skalierungsgruppen verfügbar?**

A. Derzeit wird der Zugriff auf die serielle Konsole für Instanzen von VM-Skalierungsgruppen nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
* Eine ausführliche Anleitung zu CMD- und PowerShell-Befehlen, die Sie im Windows SAC verwenden können, finden Sie unter [Windows-Befehle: CMD und PowerShell](serial-console-cmd-ps-commands.md).
* Die serielle Konsole ist auch für [virtuelle Linux-Computer](serial-console-linux.md) verfügbar.
* Erfahren Sie mehr zur [Startdiagnose](boot-diagnostics.md).
