---
title: Die serielle Azure-Konsole für Linux | Microsoft-Dokumentation
description: Bidirektionale serielle Konsole für Azure Virtual Machines und Virtual Machine Scale Sets
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 989f737add20e72d7952e7b322c11bea6277c982
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238890"
---
# <a name="azure-serial-console-for-linux"></a>Die serielle Azure-Konsole für Linux

Die serielle Konsole im Azure-Portal ermöglicht den Zugriff auf eine textbasierte Konsole für virtuelle Linux-Computer (VMs) und Instanzen von VM-Skalierungsgruppen. Diese serielle Verbindung erfolgt über den seriellen COM1-Port der VM oder der VM-Skalierungsgruppe und ermöglicht Zugriff auf diesen, unabhängig vom Zustand des Netzwerks oder Betriebssystems. Auf die serielle Konsole kann nur über das Azure-Portal und von Benutzern zugegriffen werden, die mindestens über die Zugriffsrolle „Mitwirkender“ für die VM oder VM-Skalierungsgruppeninstanzen verfügen.

Die serielle Konsole funktioniert auf die gleiche Weise für VMs und VM-Skalierungsgruppeninstanzen. Deshalb beziehen sich alle Äußerungen bezüglich VMs in dieser Dokumentation, sofern nicht anders angegeben, implizit auch auf VM-Skalierungsgruppeninstanzen.

Die Dokumentation zur seriellen Konsole für Windows finden Sie unter [Serielle Konsole für Windows](../windows/serial-console.md).

> [!NOTE]
> Die serielle Konsole ist in den globalen Azure-Regionen allgemein verfügbar. Sie ist noch nicht in den Clouds „Azure Government“ und „Azure China“ verfügbar.


## <a name="prerequisites"></a>Voraussetzungen

- Ihre VM oder VM-Skalierungsgruppeninstanz muss das Bereitstellungsmodell für die Ressourcenverwaltung verwenden. Klassische Bereitstellungen werden nicht unterstützt.

- Ihr Konto, das eine serielle Konsole verwendet, muss die Rolle [Mitwirkender für virtuelle Computer](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) für die VM und das Speicherkonto [Startdiagnose](boot-diagnostics.md) aufweisen.

- Ihre VM oder VM-Skalierungsgruppeninstanz muss über einen kennwortbasierten Benutzer verfügen. Mit der Funktion [Kennwort zurücksetzen](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) der Erweiterungen für den Zugriff auf virtuelle Computer können Sie eines erstellen. Wählen Sie im Abschnitt **Support + Problembehandlung** **Kennwort zurücksetzen** aus.

- Die [Startdiagnose](boot-diagnostics.md) muss für Ihre VM oder VM-Skalierungsgruppeninstanz aktiviert sein.

    ![Einstellungen der Startdiagnose](./media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

- Spezifische Einstellungen für Linux-Distributionen finden Sie unter [Verfügbarkeit der seriellen Konsole in Linux-Distributionen](#serial-console-linux-distribution-availability).



## <a name="get-started-with-the-serial-console"></a>Erste Schritte mit der seriellen Konsole
Auf die serielle Konsole für VMs und VM-Skalierungsgruppen kann nur über das Azure-Portal zugegriffen werden:

### <a name="serial-console-for-virtual-machines"></a>Serielle Konsole für virtuelle Computer
Für den Zugriff auf die serielle Konsole für VMs müssen Sie lediglich auf **Serielle Konsole** im Abschnitt **Support + Problembehandlung** des Azure-Portals klicken.
  1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

  1. Navigieren Sie zu **Alle Ressourcen**, und wählen Sie eine VM aus. Die Übersichtsseite für die VM wird geöffnet.

  1. Scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**, und wählen Sie **Serielle Konsole** aus. Ein neuer Bereich mit der seriellen Konsole wird geöffnet, und die Verbindung wird hergestellt.

     ![Fenster der seriellen Linux-Konsole](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Serielle Konsole für Virtual Machine Scale Sets
Die serielle Konsole steht pro Instanz für VM-Skalierungsgruppen zur Verfügung. Sie müssen zur individuellen Instanz einer VM-Skalierungsgruppe navigieren, um die Schaltfläche **Serielle Konsole** zu finden. Wenn die Startdiagnose nicht für Ihre VM-Skalierungsgruppe aktiviert ist, stellen Sie sicher, dass Sie Ihr VM-Skalierungsgruppenmodell aktualisieren, um die Startdiagnose zu aktivieren, und führen Sie das Upgrades für alle Instanzen auf das neue Modell durch, um auf die serielle Konsole zugreifen zu können.
  1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

  1. Navigieren Sie zu **Alle Ressourcen**, und wählen Sie eine VM-Skalierungsgruppe aus. Die Übersichtsseite für die VM-Skalierungsgruppe wird geöffnet.

  1. Navigieren Sie zu **Instanzen**.

  1. Wählen Sie eine VM-Skalierungsgruppeninstanz aus.

  1. Klicken Sie im Abschnitt **Support + Problembehandlung** auf **Serielle Konsole**. Ein neuer Bereich mit der seriellen Konsole wird geöffnet, und die Verbindung wird hergestellt.

     ![Serielle Konsole für Linux-VM-Skalierungsgruppe](./media/virtual-machines-serial-console/vmss-start-console.gif)


> [!NOTE]
> Die serielle Konsole erfordert einen lokalen Benutzer mit konfiguriertem Kennwort. VMs oder VM-Skalierungsgruppen, die nur mit einem öffentlichen SSH-Schlüssel konfiguriert wurden, können sich nicht bei der seriellen Konsole anmelden. Wenn Sie einen lokalen Benutzer mit Kennwort erstellen möchten, verwenden Sie die [Erweiterung für VM-Zugriff](https://docs.microsoft.com/azure/virtual-machines/linux/using-vmaccess-extension), die im Azure-Portal durch Auswählen von **Kennwort zurücksetzen** verfügbar ist, und erstellen Sie einen lokalen Benutzer mit einem Kennwort.
> Sie können auch das Administratorkennwort in Ihrem Konto zurücksetzen, indem Sie [GRUB verwenden, um im Einzelbenutzermodus zu starten](./serial-console-grub-single-user-mode.md).

## <a name="serial-console-linux-distribution-availability"></a>Verfügbarkeit der seriellen Konsole in Linux-Distributionen
Damit die serielle Konsole ordnungsgemäß ausgeführt wird, muss das Gastbetriebssystem so konfiguriert werden, dass Konsolenmeldungen über den seriellen Anschluss gelesen und geschrieben werden. In den meisten [von Azure unterstützten Linux-Distributionen](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) ist die serielle Konsole standardmäßig konfiguriert. Zugriff auf die serielle Konsole erhalten Sie, indem Sie im Azure-Portal im Bereich **Support + Problembehandlung** **Serielle Konsole** auswählen.

Distribution      | Zugriff auf die serielle Konsole
:-----------|:---------------------
Red Hat Enterprise Linux    | Der Zugriff auf die serielle Konsole ist standardmäßig aktiviert.
CentOS      | Der Zugriff auf die serielle Konsole ist standardmäßig aktiviert.
Ubuntu      | Der Zugriff auf die serielle Konsole ist standardmäßig aktiviert.
CoreOS      | Der Zugriff auf die serielle Konsole ist standardmäßig aktiviert.
SUSE        | Für die neueren SLES-Images, die auf Azure verfügbar sind, ist der Zugriff auf die serielle Konsole standardmäßig aktiviert. Wenn Sie in Azure ältere Versionen von SLES (10 oder niedriger) verwenden, lesen Sie die Anweisungen in [diesem KB-Artikel](https://www.novell.com/support/kb/doc.php?id=3456486), um die serielle Konsole zu aktivieren.
Oracle Linux        | Der Zugriff auf die serielle Konsole ist standardmäßig aktiviert.
Benutzerdefinierte Linux-Images     | Um die serielle Konsole für Ihr benutzerdefiniertes Linux-VM-Image zu aktivieren, aktivieren Sie den Konsolenzugriff in der Datei */etc/inittab*, um ein Terminal auf `ttyS0` auszuführen. Beispiel: `S0:12345:respawn:/sbin/agetty -L 115200 console vt102`. Weitere Informationen zur ordnungsgemäßen Erstellung von benutzerdefinierten Images finden Sie unter [Erstellen und Hochladen einer Linux-VHD in Azure](https://aka.ms/createuploadvhd). Wenn Sie einen benutzerdefinierten Kernel erstellen, sollten Sie die Aktivierung dieser Kernelflags in Erwägung ziehen: `CONFIG_SERIAL_8250=y` und `CONFIG_MAGIC_SYSRQ_SERIAL=y`. Die Konfigurationsdatei befindet sich normalerweise im Pfad */boot/* .

> [!NOTE]
> Wenn in der seriellen Konsole nichts angezeigt wird, überprüfen Sie, ob die Startdiagnose auf Ihrer VM aktiviert ist. Das Drücken der **EINGABETASTE** kann häufig Probleme beheben, bei denen nichts in der seriellen Konsole angezeigt wird.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Gängige Szenarios für den Zugriff auf die serielle Konsole

Szenario          | Aktionen in der seriellen Konsole
:------------------|:-----------------------------------------
Fehlerhafte *FSTAB*-Datei | Drücken Sie die **EINGABETASTE**, um fortzufahren, und verwenden Sie einen Text-Editor, um die Datei *FSTAB* zu korrigieren. Dazu müssen Sie sich möglicherweise im Einzelbenutzermodus befinden. Weitere Informationen finden Sie im Abschnitt zur seriellen Konsole unter [Beheben von FSTAB-Fehlern](https://support.microsoft.com/help/3206699/azure-linux-vm-cannot-start-because-of-fstab-errors) und [Verwenden der seriellen Konsole zum Zugreifen auf GRUB und den Einzelbenutzermodus](serial-console-grub-single-user-mode.md).
Falsche Firewallregeln |  Wenn Sie iptables zum Blockieren der SSH-Konnektivität konfiguriert haben, können Sie die serielle Konsole für die Interaktion mit Ihrer VM verwenden, ohne dass Sie SSH benötigen. Weitere Details finden Sie auf der Seite zu [iptables man](https://linux.die.net/man/8/iptables).<br>Falls der SSH-Zugriff durch firewalld blockiert wird, können Sie auch über die serielle Konsole auf die VM zugreifen und firewalld neu konfigurieren. Weitere Informationen hierzu finden Sie in der [firewalld-Dokumentation](https://firewalld.org/documentation/).
Dateisystembeschädigung/-überprüfung | Weitere Informationen zur Problembehandlung für beschädigte Dateisysteme per serieller Konsole finden Sie im entsprechenden Abschnitt unter [Azure Linux VM kann nicht wegen Dateisystemfehler gestartet werden](https://support.microsoft.com/en-us/help/3213321/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck).
SSH-Konfigurationsprobleme | Greifen Sie auf die serielle Konsole zu, und ändern Sie die Einstellungen. Die serielle Konsole kann unabhängig von der SSH-Konfiguration einer VM verwendet werden, da für die VM hierfür keine Netzwerkverbindung erforderlich ist. Einen Leitfaden zur Problembehandlung finden Sie unter [Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-ssh-connection). Weitere Informationen finden Sie unter [Ausführliche Schritte zum Beheben von SSH-Verbindungsproblemen mit einer Azure-VM unter Linux](./detailed-troubleshoot-ssh-connection.md).
Interaktion mit Bootloader | Starten Sie auf dem Blatt der seriellen Konsole Ihren virtuellen Computer neu, um auf Ihrem virtuellen Linux-Computer auf GRUB zuzugreifen. Weitere Informationen und distributionsspezifische Informationen finden Sie unter [Verwenden der seriellen Konsole zum Zugreifen auf den GRUB- und Einzelbenutzermodus](serial-console-grub-single-user-mode.md).

## <a name="disable-the-serial-console"></a>Deaktivieren der seriellen Konsole
Die serielle Konsole ist standardmäßig für alle Abonnements aktiviert. Sie können die serielle Konsole entweder für das Abonnement oder für die VMs bzw. VM-Skalierungsgruppen deaktivieren. Beachten Sie, dass die Startdiagnose für eine VM aktiviert sein muss, damit die serielle Konsole funktioniert.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Deaktivieren der seriellen Konsole für eine VM/VM-Skalierungsgruppe
Die serielle Konsole kann für bestimmte VMs oder VM-Skalierungsgruppen deaktiviert werden, indem die Startdiagnose deaktiviert wird. Deaktivieren Sie die Startdiagnose im Azure-Portal, um die serielle Konsole für eine VM oder VM-Skalierungsgruppe zu deaktivieren. Stellen Sie sicher, dass Sie Ihre VM-Skalierungsgruppeninstanzen auf die neueste Version aktualisieren, wenn Sie die serielle Konsole für eine VM-Skalierungsgruppe verwenden.

> [!NOTE]
> Um die serielle Konsole für ein Abonnement zu aktivieren oder zu deaktivieren, müssen Sie über Schreibberechtigungen für das Abonnement verfügen. Diese Berechtigungen schließen Administrator- oder Besitzerrollen ein. Benutzerdefinierte Rollen können ebenfalls über Schreibberechtigungen verfügen.

### <a name="subscription-level-disable"></a>Deaktivieren auf Abonnementebene
Die serielle Konsole kann über den [REST-API-Aufruf zum Deaktivieren der Konsole](/rest/api/serialconsole/console/disableconsole) für ein gesamtes Abonnement deaktiviert werden. Für diese Aktion ist die Zugriffsebene „Mitwirkender“ oder höher für das Abonnement erforderlich. Sie können die auf der Seite der API-Dokumentation verfügbare Funktion **Jetzt testen** verwenden, um die serielle Konsole für ein Abonnement zu deaktivieren und zu aktivieren. Geben Sie unter **subscriptionId** Ihre Abonnement-ID ein, geben Sie unter **default** **default** ein, und wählen Sie dann **Ausführen** aus. Azure CLI-Befehle sind noch nicht verfügbar.

Um die serielle Konsole für ein Abonnement erneut zu aktivieren, verwenden Sie den [REST-API-Aufruf „Konsole aktivieren“](/rest/api/serialconsole/console/enableconsole).

![REST-API testen](./media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

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
> Dies bedeutet, dass ein Benutzer, der getrennt wird, nicht abgemeldet wird. Die Möglichkeit, bei der Trennung der Verbindung eine Abmeldung zu erzwingen (mithilfe von SIGHUP oder einem ähnlichen Mechanismus), ist noch nicht implementiert. Für Windows ist in SAC (Special Administrative Console, spezielle Verwaltungskonsole) ein automatisches Timeout aktiviert. Für Linux können Sie die Timeouteinstellung für das Terminal konfigurieren. Fügen Sie zu diesem Zweck Ihrer *.bash_profile*- oder *.profile*-Datei für den Benutzer, den Sie für die Anmeldung bei der Konsole verwenden, `export TMOUT=600` hinzu. Durch diese Einstellung erfolgt nach 10 Minuten ein Timeout der Sitzung.

## <a name="accessibility"></a>Barrierefreiheit
Die Barrierefreiheit ist ein wichtiger Aspekt bei der seriellen Konsole in Azure. Daher haben wir sichergestellt, dass die serielle Konsole vollständig barrierefrei ist.

### <a name="keyboard-navigation"></a>Tastaturnavigation
Verwenden Sie die **TAB**-Taste auf der Tastatur, um im Azure-Portal in der seriellen Konsolenschnittstelle zu navigieren. Auf dem Bildschirm wird hervorgehoben, wo Sie sich gerade befinden. Um den Fokus vom seriellen Konsolenbereich zu entfernen, drücken Sie **STRG**+**F6** auf der Tastatur.

### <a name="use-serial-console-with-a-screen-reader"></a>Verwenden der seriellen Konsole mit einer Sprachausgabe
Die serielle Konsole bietet integrierte Unterstützung für die Sprachausgabe. Beim Navigieren mit aktivierter Sprachausgabe kann der Alternativtext für die aktuell ausgewählte Schaltfläche von der Sprachausgabe vorgelesen werden.

## <a name="errors"></a>Errors
Da die meisten Fehler vorübergehend sind, können sie oftmals durch Wiederherstellen der Verbindung behoben werden. Die folgende Tabelle zeigt eine Liste von Fehlern und deren Behebung. Diese Fehler und Gegenmaßnahmen gelten sowohl für VMs als auch für VM-Skalierungsgruppeninstanzen.

Error                            |   Lösung
:---------------------------------|:--------------------------------------------|
Die Startdiagnoseeinstellungen für *&lt;VMNAME&gt;* können nicht abgerufen werden. Damit Sie die serielle Konsole verwenden können, stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist. | Stellen Sie sicher, dass für den virtuellen Computer [Startdiagnose](boot-diagnostics.md) aktiviert ist.
Der virtuelle Computer befindet sich in einem beendeten Zustand mit aufgehobener Zuordnung. Starten Sie den virtuellen Computer neu, und wiederholen Sie die Verbindung mit der seriellen Konsole. | Die VM muss sich im gestarteten Zustand befinden, um auf die serielle Konsole zugreifen zu können.
Sie verfügen nicht über die erforderlichen Berechtigungen, um diese VM mit der seriellen Konsole zu verwenden. Achten Sie darauf, dass Sie mindestens über die Berechtigungen der Rolle „Mitwirkender für virtuelle Computer“ verfügen.| Für den Zugriff auf die serielle Konsole sind bestimmte Berechtigungen erforderlich. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).
Die Ressourcengruppe für das Startdiagnose-Speicherkonto *&lt;STORAGEACCOUNTNAME&gt;* kann nicht ermittelt werden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist und Sie über Zugriff auf dieses Speicherkonto verfügen. | Für den Zugriff auf die serielle Konsole sind bestimmte Berechtigungen erforderlich. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).
Das Websocket ist geschlossen oder konnte nicht geöffnet werden. | Möglicherweise müssen Sie der Whitelist den Eintrag `*.console.azure.com` hinzufügen. Ein detaillierterer, aber längerer Ansatz besteht darin, der Whitelist die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) hinzuzufügen, die sich jedoch relativ regelmäßig ändern.
Beim Zugriff auf das Speicherkonto für die Startdiagnose dieses virtuellen Computers wurde eine „Forbidden“-Antwort empfangen. | Stellen Sie sicher, dass die Startdiagnose nicht über eine Kontofirewall verfügt. Damit die serielle Konsole funktioniert, ist Zugriff auf ein Startdiagnose-Speicherkonto erforderlich.

## <a name="known-issues"></a>Bekannte Probleme
Uns sind einige Probleme mit der seriellen Konsole bekannt. Hier finden Sie eine Liste dieser Probleme und Schritte zur Lösung. Diese Probleme und Gegenmaßnahmen gelten sowohl für VMs als auch für VM-Skalierungsgruppeninstanzen.

Problem                           |   Lösung
:---------------------------------|:--------------------------------------------|
Das Drücken der **EINGABETASTE** nach dem Verbindungsbanner führt nicht zur Anzeige einer Anmeldeaufforderung. | Weitere Informationen finden Sie unter [Hitting enter does nothing](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) (Das Drücken der Eingabetaste bewirkt nichts). Dieses Problem kann auftreten, wenn Sie eine benutzerdefinierte VM, eine Appliance mit verstärkter Sicherheit oder eine GRUB-Konfiguration ausführen, und Linux aufgrund dessen keine Verbindung mit dem seriellen Port herstellen kann.
Der Text in der seriellen Konsole nimmt nur einen Teil der Größe des Bildschirms in Anspruch (häufig nach Verwendung eines Text-Editors). | Serielle Konsolen unterstützen keine Aushandlung der Fenstergröße ([RFC 1073](https://www.ietf.org/rfc/rfc1073.txt)). Daher wird kein SIGWINCH-Signal gesendet, um die Bildschirmgröße zu aktualisieren, und der virtuelle Computer erhält keine Informationen über die Größe Ihres Terminals. Installieren Sie xterm oder ein ähnliches Hilfsprogramm, das über einen `resize`-Befehl verfügt, und führen Sie dann `resize` aus.
Das Einfügen von langen Zeichenfolgen funktioniert nicht. | Die serielle Konsole begrenzt die Länge der Zeichenfolgen, die in das Terminal eingefügt werden können, auf 2048 Zeichen, um die Bandbreite am seriellen Port nicht zu überlasten.
Die serielle Konsole funktioniert nicht mit einer Speicherkontofirewall. | Die serielle Konsole kann programmbedingt nicht verwendet werden, wenn für das Speicherkonto mit Startdiagnose Speicherkontofirewalls aktiviert sind.


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**F: Wie kann ich Feedback senden?**

A. Geben Sie Feedback, indem Sie ein GitHub-Problem unter https://aka.ms/serialconsolefeedback erstellen. Alternativ dazu können Sie Feedback über azserialhelp@microsoft.com oder in der Kategorie „Virtuelle Computer“ von https://feedback.azure.com senden (dies sind die weniger bevorzugten Methoden).

**F: Unterstützt die serielle Konsole das Kopieren und Einfügen?**

A. Ja. Verwenden Sie **STRG**+**UMSCHALT**+**C** und **STRG**+**UMSCHALT**+**V** zum Kopieren und Einfügen in das Terminal.

**F: Kann ich die serielle Konsole anstelle einer SSH-Verbindung verwenden?**

A. Diese Nutzung mag technisch möglich sein, doch ist die serielle Konsole in erster Linie als ein Tool zur Problembehandlung in Situationen gedacht, in denen keine Verbindung über SSH möglich ist. Aus den folgenden Gründen raten wir davon ab, die serielle Konsole als SSH-Ersatz zu verwenden:

- Die serielle Konsole hat nicht so viel Bandbreite wie SSH. Da es sich um eine reine Textverbindung handelt, sind eher GUI-lastige Interaktionen schwierig.
- Der Zugriff auf die serielle Konsole ist derzeit nur über Benutzername und Kennwort möglich. SSH-Schlüssel sind weitaus sicherer als Benutzername/Kennwort-Kombinationen. Daher ist aus Gründen der Anmeldesicherheit SSH der seriellen Konsole vorzuziehen.

**F: Wer kann die serielle Konsole für mein Abonnement aktivieren oder deaktivieren?**

A. Um die serielle Konsole auf Abonnementebene aktivieren oder deaktivieren zu können, müssen Sie über Schreibberechtigungen für das Abonnement verfügen. Die Rollen „Administrator“ und „Besitzer“ verfügen beispielsweise über Schreibberechtigungen. Benutzerdefinierte Rollen können ebenfalls über Schreibberechtigungen verfügen.

**F: Wer kann auf die serielle Konsole für meine VM/VM-Skalierungsgruppe zugreifen?**

A. Sie müssen mindestens der Rolle „Mitwirkender für virtuelle Computer“ einer VM oder VM-Skalierungsgruppe angehören, um auf die serielle Konsole zuzugreifen.

**F: Meine serielle Konsole zeigt nichts an, was muss ich tun?**

A. Ihr Image ist wahrscheinlich nicht richtig für den Zugriff auf die serielle Konsole konfiguriert. Informationen zum Konfigurieren Ihres Images für die Aktivierung der seriellen Konsole finden Sie unter [Verfügbarkeit der seriellen Konsole in Linux-Distributionen](#serial-console-linux-distribution-availability).

**F: Ist die serielle Konsole für VM-Skalierungsgruppen verfügbar?**

A. Ja. Siehe [Serielle Konsole für Virtual Machine Scale Sets](#serial-console-for-virtual-machine-scale-sets).

**F: Ich habe meine VM oder VM-Skalierungsgruppe nur mit SSH-Schlüsselauthentifizierung eingerichtet. Kann ich trotzdem die serielle Konsole verwenden, um eine Verbindung mit meiner VM/VM-Skalierungsgruppeninstanz herzustellen?**

A. Ja. Da die serielle Konsole keine SSH-Schlüssel erfordert, brauchen Sie nur eine Kombination aus Benutzername und Kennwort einzurichten. Dazu können Sie im Azure-Portal **Kennwort zurücksetzen** auswählen und diese Anmeldeinformationen für die Anmeldung bei der seriellen Konsole verwenden.

## <a name="next-steps"></a>Nächste Schritte
* Verwenden der seriellen Konsole zum [Zugreifen auf GRUB und den Einzelbenutzermodus](serial-console-grub-single-user-mode.md).
* Verwenden der seriellen Konsole für [NMI- und SysRq-Aufrufe](serial-console-nmi-sysrq.md).
* Erfahren Sie, wie Sie die serielle Konsole zum [Aktivieren von GRUB in verschiedenen Distributionen](https://blogs.msdn.microsoft.com/linuxonazure/2018/10/23/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/) verwenden.
* Die serielle Konsole ist auch für [virtuelle Windows-Computer](../windows/serial-console.md) verfügbar.
* Erfahren Sie mehr zur [Startdiagnose](boot-diagnostics.md).

