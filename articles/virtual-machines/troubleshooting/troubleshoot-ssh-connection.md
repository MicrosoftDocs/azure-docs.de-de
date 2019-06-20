---
title: Behandeln von Problemen bei der SSH-Verbindung mit einem virtuellen Azure-Computer | Microsoft Docs
description: Erfahren Sie, wie Sie SSH-Fehler wie etwa fehlgeschlagene oder abgelehnte SSH-Verbindungen auf virtuellen Azure-Computern unter Linux beheben.
keywords: SSH-Verbindung abgelehnt, SSH-Fehler, Azure SSH, SSH-Verbindungsfehler
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: dcb82e19-29b2-47bb-99f2-900d4cfb5bbb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 81e00c4a3b9490a05667d58952f7bdf8945bacdb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61405253"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Behandeln von Problemen, Fehlern oder Ablehnungen im Zusammenhang mit der SSH-Verbindung mit einem virtuellen Azure Linux-Computer
Dieser Artikel hilft Ihnen dabei, Probleme zu suchen und zu beheben, die aufgrund von SSH-Fehlern (Secure Shell), SSH-Verbindungsfehlern oder der Ablehnung einer SSH-Verbindung, wenn Sie versuchen, eine Verbindung mit einem virtuellen Linux-Computer herzustellen, auftreten. Sie können das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder die VM-Zugriffserweiterung für Linux verwenden, um Verbindungsproblemen zu ermitteln und zu beheben.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Azure-Experten im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/)Fragen stellen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie **Support erhalten**aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Schritte zur schnellen Problembehandlung
Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

1. [Setzen Sie die SSH-Konfiguration zurück.](#reset-config)
2. [Setzen Sie die Anmeldeinformationen](#reset-credentials) für den Benutzer zurück.
3. Überprüfen Sie, ob die Regeln der [Netzwerksicherheitsgruppe](../../virtual-network/security-overview.md) SSH-Datenverkehr zulassen.
   * Stellen Sie sicher, dass eine [Netzwerksicherheitsgruppen-Regel](#security-rules) vorhanden ist, die SSH-Datenverkehr zulässt (standardmäßig über TCP-Port 22).
   * Sie können die Portweiterleitung/-zuordnung nicht ohne Azure-Lastenausgleich verwenden.
4. Überprüfen Sie die [Ressourcenintegrität des virtuellen Computers](../../resource-health/resource-health-overview.md).
   * Stellen Sie sicher, dass der virtuelle Computer als fehlerfrei gemeldet wird.
   * Wenn Sie die [Startdiagnose aktiviert](boot-diagnostics.md) haben, stellen Sie sicher, dass der virtuelle Computer in den Protokollen keine Startfehler meldet.
5. [Starten Sie den virtuellen Computer neu.](#restart-vm)
6. [Stellen Sie den virtuellen Computer erneut bereit.](#redeploy-vm)

Lesen Sie weiter, falls Sie ausführlichere Schritte und Erläuterungen zur Problembehandlung benötigen.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Verfügbare Methoden zum Beheben von SSH-Verbindungsproblemen
Sie können die Anmeldeinformationen oder die SSH-Konfiguration mithilfe einer der folgenden Methoden zurücksetzen:

* [Azure-Portal](#use-the-azure-portal): Ermöglicht das schnelle Zurücksetzen der SSH-Konfiguration oder des SSH-Schlüssels, falls die Azure-Tools nicht installiert sind.
* [Serielle Konsole für virtuelle Azure-Computer](https://aka.ms/serialconsolelinux): Die serielle Konsole für virtuelle Computer funktioniert unabhängig von der SSH-Konfiguration. Sie erhalten hierbei eine interaktive Konsole für Ihre VM. Die serielle Konsole wurde speziell für Fälle entwickelt, in denen keine SSH-Verbindung hergestellt werden kann. Unten sind weitere Details hierzu angegeben.
* [Azure CLI](#use-the-azure-cli): Wenn Sie bereits eine Befehlszeile geöffnet haben, können Sie die SSH-Konfiguration oder Anmeldeinformationen schnell zurücksetzen. Wenn Sie mit einem klassischen virtuellen Computer arbeiten, können Sie die [klassische Azure-Befehlszeilenschnittstelle](#use-the-azure-classic-cli) verwenden.
* [VMAccessForLinux-Erweiterung in Azure](#use-the-vmaccess-extension): Erstellen und verwenden Sie JSON-Definitionsdateien wieder, um die SSH-Konfiguration oder Benutzeranmeldeinformationen zurückzusetzen.

Versuchen Sie nach jedem Problembehandlungsschritt, die Verbindung mit dem virtuellen Computer erneut herzustellen. Sollte sich immer noch keine Verbindung herstellen lassen, versuchen Sie es mit dem nächsten Schritt.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals
Das Azure-Portal bietet eine schnelle Möglichkeit, die SSH-Konfiguration oder Benutzeranmeldeinformationen zurücksetzen, ohne dafür Tools auf dem lokalen Computer installieren zu müssen.

Wählen Sie als Erstes im Azure-Portal Ihren virtuellen Computer aus. Scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**, und wählen Sie **Kennwort zurücksetzen**, wie im folgenden Beispiel gezeigt:

![Zurücksetzen der SSH-Konfiguration oder Anmeldeinformationen im Azure-Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="a-idreset-config-reset-the-ssh-configuration"></a><a id="reset-config" />Zurücksetzen der SSH-Konfiguration
Um die SSH-Konfiguration zurückzusetzen, wählen Sie `Reset configuration only` im Abschnitt **Modus** aus, wie im obigen Screenshot dargestellt, und wählen Sie dann **Aktualisieren** aus. Nachdem die Aktion abgeschlossen ist, versuchen Sie erneut, auf Ihren virtuellen Computer zuzugreifen.

### <a name="a-idreset-credentials-reset-ssh-credentials-for-a-user"></a><a id="reset-credentials" />Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Um die Anmeldeinformationen eines vorhandenen Benutzers zurückzusetzen, wählen Sie `Reset SSH public key` oder `Reset password` im Bereich **Modus** aus, wie im vorherigen Screenshot dargestellt. Geben Sie den Benutzernamen und einen SSH-Schlüssel oder ein neues Kennwort an, und wählen Sie dann **Aktualisieren** aus.

Über dieses Menü können Sie auch einen Benutzer mit sudo-Berechtigungen auf dem virtuellen Computer erstellen. Geben Sie einen neuen Benutzernamen und ein zugehöriges Kennwort oder einen SSH-Schlüssel ein, und wählen Sie dann **Aktualisieren** aus.

### <a name="a-idsecurity-rules-check-security-rules"></a><a id="security-rules" />Überprüfen von Sicherheitsregeln

Verwenden Sie den Ansatz [Überprüfen des IP-Flusses](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md), um zu bestätigen, dass eine Regel in einer Netzwerksicherheitsgruppe den Datenverkehr an einen oder von einem virtuellen Computer blockiert. Sie können auch die aktiven Sicherheitsgruppenregeln überprüfen, um sicherzustellen, dass die NSG-Regel „Zulassen“ für eingehende Verbindungen vorhanden ist und für den SSH-Port (standardmäßig 22) Priorität hat. Weitere Informationen finden Sie unter [Problembehandlung bei Netzwerksicherheitsgruppen über das Azure-Portal](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Überprüfen des Routings

Vergewissern Sie sich mit der Funktion [Nächster Hop](../../network-watcher/network-watcher-check-next-hop-portal.md) von Network Watcher, dass eine Route nicht das Weiterleiten des Datenverkehrs an einen bzw. von einem virtuellen Computer verhindert. Sie können auch die effektiven Routen überprüfen, um alle effektiven Routen für eine Netzwerkschnittstelle anzuzeigen. Weitere Informationen finden Sie unter [Problembehandlung bei Routen über das Azure-Portal](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Verwenden der seriellen Konsole für virtuelle Azure-Computer
Die [serielle Konsole für virtuelle Azure-Computer](./serial-console-linux.md) ermöglicht den Zugriff auf eine textbasierte Konsole für virtuelle Linux-Computer. Sie können die Konsole zum Beheben von Problemen mit Ihrer SSH-Verbindung in einer interaktiven Shell verwenden. Stellen Sie sicher, dass Sie die [Voraussetzungen](./serial-console-linux.md#prerequisites) zur Verwendung der seriellen Konsole erfüllt haben, und probieren Sie die unten angegebenen Befehle aus, um die Problembehandlung für Ihre SSH-Verbindung durchzuführen.

### <a name="check-that-ssh-is-running"></a>Überprüfen, ob SSH ausgeführt wird
Sie können den folgenden Befehl verwenden, um zu überprüfen, ob SSH auf Ihrer VM ausgeführt wird:
```
$ ps -aux | grep ssh
```
Wenn keine Ausgabe erfolgt, ist SSH aktiv und wird ausgeführt.

### <a name="check-which-port-ssh-is-running-on"></a>Überprüfen, auf welchem Port SSH ausgeführt wird
Sie können den folgenden Befehl verwenden, um zu überprüfen, auf welchem Port SSH ausgeführt wird:
```
$ sudo grep Port /etc/ssh/sshd_config
```
Die Ausgabe sieht in etwa wie folgt aus:
```
Port 22
```

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI
Wenn nicht bereits geschehen, installieren Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-az-cli2), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

Wenn Sie ein benutzerdefiniertes Linux-Datenträgerimage erstellt und hochgeladen haben, stellen Sie sicher, dass [Microsoft Azure Linux Agent](../extensions/agent-windows.md) Version 2.0.5 oder höher installiert ist. Bei virtuellen Computern, die über Images aus dem Katalog erstellt wurde, ist diese Zugriffserweiterung bereits installiert und konfiguriert.

### <a name="reset-ssh-configuration"></a>Zurücksetzen der SSH-Konfiguration
Sie können zunächst versuchen, die SSH-Konfiguration auf die Standardwerte zurückzusetzen und den SSH-Server auf der VM neu zu starten. Hierdurch werden der Name, das Kennwort und die SSH-Schlüssel des Benutzerkontos nicht geändert.
Im folgenden Beispiel wird mithilfe von [az vm user reset-ssh](/cli/azure/vm/user) die SSH-Konfiguration auf der VM mit dem Namen `myVM` in `myResourceGroup` zurückgesetzt. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Im folgenden Beispiel werden mithilfe von [az vm user update](/cli/azure/vm/user) die Anmeldeinformationen für `myUsername` auf den in `myPassword` angegebenen Wert zurückgesetzt, der auf der VM `myVM` in `myResourceGroup` angegeben ist. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Wenn Sie die SSH-Schlüsselauthentifizierung verwenden, können Sie den SSH-Schlüssel für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel aktualisiert mithilfe von **az vm access set-linux-user** auf dem virtuellen Computer `myVM` in `myResourceGroup` den in `~/.ssh/id_rsa.pub` gespeicherten SSH-Schlüssel für den Benutzer `myUsername`. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Verwenden der Erweiterung VMAccess
Die VM-Zugriffserweiterung für Linux liest eine JSON-Datei, die die auszuführenden Aktionen definiert. Zu diesen Aktionen gehört das Zurücksetzen von SSHD, das Zurücksetzen eines SSH-Schlüssels und das Hinzufügen eines Benutzers. Sie verwenden weiterhin die Azure-Befehlszeilenschnittstelle, um die VM-Zugriffserweiterung aufzurufen, aber Sie können die JSON-Dateien bei Bedarf VM-übergreifend wiederverwenden. Auf diese Weise können Sie ein Repository mit JSON-Dateien erstellen, die für verschiedene Szenarien aufgerufen werden können.

### <a name="reset-sshd"></a>Zurücksetzen von SSHD
Erstellen Sie eine Datei namens `settings.json` mit folgendem Inhalt:

```json
{
    "reset_ssh":"True"
}
```

Rufen Sie dann über die Azure-Befehlszeilenschnittstelle die `VMAccessForLinux`-Erweiterung auf, und geben Sie die entsprechende JSON-Datei an, um Ihre SSHD-Verbindung zurückzusetzen. Im folgenden Beispiel wird mithilfe von [az vm extension set](/cli/azure/vm/extension) die SSHD auf der VM mit dem Namen `myVM` in `myResourceGroup` zurückgesetzt. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Wenn SSHD ordnungsgemäß funktioniert, können Sie die Anmeldeinformationen für einen bestimmten Benutzer zurücksetzen. Um das Kennwort für einen Benutzer zurückzusetzen, erstellen Sie eine Datei namens `settings.json`. Das folgende Beispiel setzt die Anmeldeinformationen für `myUsername` auf den in `myPassword` angegebenen Wert zurück. Geben Sie folgende Zeilen in Ihre `settings.json`-Datei ein, und verwenden Sie dabei Ihre eigenen Werte:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Um den SSH-Schlüssel für einen Benutzer zurückzusetzen, erstellen Sie zuerst eine Datei namens `settings.json`. Das folgende Beispiel setzt auf dem virtuellen Computer `myVM` in `myResourceGroup` die Anmeldeinformationen für den Benutzer `myUsername` auf den in `myPassword` angegebenen Wert zurück. Geben Sie folgende Zeilen in Ihre `settings.json`-Datei ein, und verwenden Sie dabei Ihre eigenen Werte:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

Nachdem Sie die JSON-Datei erstellt haben, verwenden Sie die Azure-Befehlszeilenschnittstelle, um die `VMAccessForLinux`-Erweiterung aufzurufen, mit der Sie unter Angabe der JSON-Datei die SSH-Benutzeranmeldeinformationen zurücksetzen können. Das folgende Beispiel setzt Anmeldeinformationen auf dem virtuellen Computer mit dem Namen `myVM` in `myResourceGroup` zurück. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Verwenden der klassischen Azure-Befehlszeilenschnittstelle
[Installieren Sie die klassische Azure-Befehlszeilenschnittstelle, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her](../../cli-install-nodejs.md) (falls noch nicht geschehen). Vergewissern Sie sich wie folgt, dass Sie den Resource Manager-Modus verwenden:

```azurecli
azure config mode arm
```

Wenn Sie ein benutzerdefiniertes Linux-Datenträgerimage erstellt und hochgeladen haben, stellen Sie sicher, dass [Microsoft Azure Linux Agent](../extensions/agent-windows.md) Version 2.0.5 oder höher installiert ist. Bei virtuellen Computern, die über Images aus dem Katalog erstellt wurde, ist diese Zugriffserweiterung bereits installiert und konfiguriert.

### <a name="reset-ssh-configuration"></a>Zurücksetzen der SSH-Konfiguration
Die SSHD-Konfiguration selbst kann fehlerhaft sein, oder beim Dienst ist ein Fehler aufgetreten. Sie können SSHD zurücksetzen, um sicherzustellen, dass die SSH-Konfiguration gültig ist. Das Zurücksetzen von SSHD sollte der erste Schritt bei der Problembehandlung sein.

Das folgende Beispiel setzt SSHD auf einem virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` zurück. Verwenden Sie folgendermaßen Ihre eigenen Namen für den virtuellen Computer und die Ressourcengruppe:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Zurücksetzen von SSH-Anmeldeinformationen für einen Benutzer
Wenn SSHD ordnungsgemäß funktioniert, können Sie das Kennwort für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel setzt auf dem virtuellen Computer `myVM` in `myResourceGroup` die Anmeldeinformationen für den Benutzer `myUsername` auf den in `myPassword` angegebenen Wert zurück. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Wenn Sie die SSH-Schlüsselauthentifizierung verwenden, können Sie den SSH-Schlüssel für einen bestimmten Benutzer zurücksetzen. Das folgende Beispiel aktualisiert den in `~/.ssh/id_rsa.pub` gespeicherten SSH-Schlüssel für den Benutzer namens `myUsername` auf dem virtuellen Computer `myVM` in `myResourceGroup`. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="a-idrestart-vm-restart-a-vm"></a><a id="restart-vm" />Neustarten eines virtuellen Computers
Wenn Sie die SSH-Konfiguration und Benutzeranmeldeinformationen zurückgesetzt haben oder dabei ein Fehler aufgetreten ist, können Sie den virtuellen Computer neu starten, um zugrunde liegende Computeprobleme zu beheben.

### <a name="azure-portal"></a>Azure-Portal
Um einen virtuellen Computer über das Azure-Portal neu zu starten, wählen Sie den virtuellen Computer aus. Wählen Sie dann **Neu starten** aus, wie im folgenden Beispiel gezeigt:

![Neustarten eines virtuellen Computers im Azure-Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Im folgenden Beispiel wird mit [az vm restart](/cli/azure/vm) der virtuelle Computer `myVM` in der Ressourcengruppe `myResourceGroup` neu gestartet. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle
Das folgende Beispiel startet den virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` neu. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="a-idredeploy-vm-redeploy-a-vm"></a><a id="redeploy-vm" />Erneutes Bereitstellen eines virtuellen Computers
Sie können einen virtuellen Computer in Azure auf einem anderen Knoten erneut bereitstellen und dadurch möglicherweise zugrunde liegende Netzwerkprobleme beheben. Informationen zum erneuten Bereitstellen eines virtuellen Computers finden Sie unter [Einen virtuellen Computer in einem neuen Azure-Knoten erneut bereitstellen](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!NOTE]
> Nach Beendigung dieses Vorgangs gehen kurzlebige Datenträgerdaten verloren, und dynamische IP-Adressen, die dem virtuellen Computer zugeordnet sind, werden aktualisiert.
>
>

### <a name="azure-portal"></a>Azure-Portal
Um einen virtuellen Computer über das Azure-Portal erneut bereitzustellen, wählen Sie den Computer aus, und scrollen Sie nach unten zum Abschnitt **Support + Problembehandlung**. Wählen Sie **Erneut bereitstellen** aus, wie im folgenden Beispiel gezeigt:

![Erneutes Bereitstellen eines virtuellen Computers im Azure-Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Im folgenden Beispiel wird mit [az vm redeploy](/cli/azure/vm) die Bereitstellung des virtuellen Computers `myVM` in der Ressourcengruppe `myResourceGroup` erneuert. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle
Das folgende Beispiel stellt den virtuellen Computer mit dem Namen `myVM` in der Ressourcengruppe `myResourceGroup` erneut bereit. Verwenden Sie Ihre eigenen Werte wie folgt:

```azurecli
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer
Führen Sie die folgenden Schritte aus, um die häufigsten SSH-Verbindungsfehler für virtuelle Computer zu beheben, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Versuchen Sie nach jedem Schritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

* Setzen Sie den Remotezugriff über das [Azure-Portal](https://portal.azure.com)zurück. Wählen Sie im Azure-Portal Ihren virtuellen Computer und dann **Remote zurücksetzen** aus.
* Starten Sie den virtuellen Computer neu. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren virtuellen Computer und dann **Neu starten** aus.

* Stellen Sie den virtuellen Computer auf einem neuen Azure-Knoten erneut bereit. Informationen zum erneuten Bereitstellen eines virtuellen Computers finden Sie unter [Einen virtuellen Computer in einem neuen Azure-Knoten erneut bereitstellen](../windows/redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

    Nach Beendigung dieses Vorgangs gehen kurzlebige Datenträgerdaten verloren, und dynamische IP-Adressen, die dem virtuellen Computer zugeordnet sind, werden aktualisiert.
* Führen Sie die unter [Zurücksetzen des Kennworts oder des SSH-Schlüssels für Linux-basierte virtuelle Computer](../linux/classic/reset-access-classic.md) beschriebenen Anweisungen aus, um Folgendes zu erreichen:

  * Zurücksetzen des Kennworts oder des SSH-Schlüssels
  * Erstellen eines *sudo*-Benutzerkontos
  * Zurücksetzen der SSH-Konfiguration
* Überprüfen Sie die Ressourcenintegrität des virtuellen Computers auf etwaige Plattformprobleme.<br>
     Wählen Sie Ihre VM aus, und scrollen Sie nach unten zu **Einstellungen** > **Integrität überprüfen**.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* Wenn Sie nach Ausführung der Schritte immer noch nicht keine SSH-Verbindung mit Ihrem virtuellen Computer herstellen können, finden Sie unter [Ausführliche Schritte zur Problembehandlung bei SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) weitere Hinweise, damit Sie das Problem lösen können.
* Weitere Informationen zur Problembehandlung beim Anwendungszugriff finden Sie unter [Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Weitere Informationen zur Problembehandlung bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden, finden Sie unter [Zurücksetzen eines Kennworts oder eines SSH-Schlüssels für Linux-basierte virtuelle Computer](../linux/classic/reset-access-classic.md).
