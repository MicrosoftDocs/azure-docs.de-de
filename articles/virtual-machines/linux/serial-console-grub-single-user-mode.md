---
title: Serielle Azure-Konsole für den GRUB- und Einzelbenutzermodus | Microsoft-Dokumentation
description: Verwenden der seriellen Konsole für GRUB in Azure Virtual Machines.
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: a249cf96981957de2c445079c0172b9c0c2ce543
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799472"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Verwenden der seriellen Konsole zum Zugreifen auf den GRUB- und Einzelbenutzermodus
GRUB steht für GRand Unified Bootloader. Mit GRUB können Sie unter anderem Ihre Startkonfiguration so ändern, dass das System im Einzelbenutzermodus gestartet wird.

Der Einzelbenutzermodus ist eine minimale Umgebung mit minimaler Funktionalität. Dieser Modus kann beim Untersuchen von Start-, Dateisystem- oder Netzwerkproblemen hilfreich sein. Es können weniger Dienste im Hintergrund ausgeführt werden, und je nach Ausführungsebene kann auch ein Dateisystem nicht automatisch eingebunden werden.

Der Einzelbenutzermodus ist auch in Situationen hilfreich, in denen Ihr virtueller Computer nur für die Anmeldung mit SSH-Schlüsseln konfiguriert werden kann. In diesem Fall können Sie den Einzelbenutzermodus verwenden, um ein Konto mit Kennwortauthentifizierung zu erstellen.

Damit Sie in den Einzelbenutzermodus wechseln können, müssen Sie GRUB beim Starten Ihres virtuellen Computers aktivieren und dann die Startkonfiguration in GRUB ändern. Hierzu kann die serielle Konsole des virtuellen Computers verwendet werden.

## <a name="general-grub-access"></a>Allgemeiner GRUB-Zugriff
Damit Sie auf GRUB zugreifen können, müssen Sie Ihren virtuellen Computer neu starten, während das Blatt für die serielle Konsole geöffnet ist. Einige Distributionen erfordern Tastatureingaben zum Anzeigen von GRUB, während andere Distributionen GRUB automatisch ein paar Sekunden lang anzeigen und Benutzereingaben über die Tastatur zulassen, um das Timeout abzubrechen. 

Damit Sie auf den Einzelbenutzermodus zugreifen können, müssen Sie sicherstellen, dass GRUB auf Ihrer VM aktiviert ist. Je nach Distribution müssen Sie möglicherweise einige Einrichtungsschritte durchführen, um sicherzustellen, dass GRUB aktiviert ist. Distributionsspezifische Informationen finden Sie weiter unten.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Neustarten Ihres virtuellen Computers zum Zugreifen auf GRUB in der seriellen Konsole
Sie können Ihren virtuellen Computer neu starten, während das Blatt für die serielle Konsole geöffnet ist, indem Sie einen Befehl vom Typ „SysRq+`'b'`“ ausführen, wenn [SysRq](./serial-console-nmi-sysrq.md) aktiviert ist, oder indem Sie auf dem Blatt „Übersicht“ auf die Schaltfläche „Neu starten“ klicken. (Öffnen Sie den virtuellen Computer auf einer neuen Browserregisterkarte, um den Neustart auszuführen, ohne das Blatt für die serielle Konsole zu schließen.) Lesen Sie die distributionsspezifischen Anweisungen weiter unten, um zu erfahren, was bei einem Neustart von GRUB zu erwarten ist.

## <a name="general-single-user-mode-access"></a>Allgemeiner Zugriff auf den Einzelbenutzermodus
Der manuelle Zugriff auf den Einzelbenutzermodus kann in Situationen erforderlich sein, in denen Sie kein Konto mit Kennwortauthentifizierung konfiguriert haben. Sie müssen die GRUB-Konfiguration ändern, um manuell in den Einzelbenutzermodus wechseln zu können. Lesen Sie anschließend den Abschnitt „Verwenden des Einzelbenutzermodus zum Zurücksetzen oder Hinzufügen eines Kennworts“, um weitere Informationen zu erhalten.

In Fällen, in denen der virtuelle Computer nicht gestartet werden kann, wird bei Distributionen häufig automatisch der Einzelbenutzer- oder Notfallmodus aktiviert. Andere erfordern jedoch eine zusätzliche Einrichtung, damit der Einzelbenutzer- oder Notfallmodus automatisch aktiviert werden kann (etwa das Einrichten eines root-Kennworts).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Verwenden des Einzelbenutzermodus zum Zurücksetzen oder Hinzufügen eines Kennworts
Wenn Sie sich im Einzelbenutzermodus befinden, gehen Sie wie folgt vor, um einen neuen Benutzer mit sudo-Berechtigungen hinzuzufügen:
1. Führen Sie `useradd <username>` aus, um einen Benutzer hinzuzufügen.
1. Führen Sie `sudo usermod -a -G sudo <username>` aus, um dem neuen Benutzer root-Berechtigungen zu erteilen.
1. Verwenden Sie `passwd <username>`, um das Kennwort für den neuen Benutzer festzulegen. Anschließend können Sie sich als der neue Benutzer anmelden.

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Zugriff für Red Hat Enterprise Linux (RHEL)
RHEL aktiviert automatisch den Einzelbenutzermodus, wenn ein normaler Start nicht möglich ist. Wenn Sie keinen Stammzugriff für den Einzelbenutzermodus eingerichtet haben, haben Sie jedoch kein Stammkennwort und können sich nicht anmelden. Es ist eine Problemumgehung verfügbar (siehe „Manuelles Aktivieren des Einzelbenutzermodus“ unten), es wird jedoch empfohlen, zuerst den Stammzugriff einzurichten.

### <a name="grub-access-in-rhel"></a>GRUB-Zugriff in RHEL
In RHEL ist GRUB standardmäßig aktiviert. Starten Sie zum Aktivieren des GRUB-Modus Ihre VM mit `sudo reboot` neu, und drücken Sie eine beliebige Taste. Daraufhin wird der GRUB-Bildschirm angezeigt.

> Hinweis: Red Hat bietet auch eine Dokumentation zum Start im Wiederherstellungsmodus, Notfallmodus, Debugmodus und Zurücksetzen des Stammkennworts. [Klicken Sie hier, um auf die Dokumentation zuzugreifen](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Einrichten des Stammzugriffs für den Einzelbenutzermodus in RHEL
Für den Einzelbenutzermodus in RHEL muss der Root-Benutzer aktiviert werden. Dieser ist standardmäßig deaktiviert. Wenn Sie den Einzelbenutzermodus aktivieren möchten, gehen Sie wie folgt vor:

1. Melden Sie sich über SSH beim Red Hat-System an.
1. Wechseln Sie zum Stamm.
1. Aktivieren Sie das Kennwort für den Root-Benutzer. 
    * `passwd root` (Legen Sie ein sicheres Root-Kennwort fest.)
1. Stellen Sie sicher, dass sich der Root-Benutzer nur über ttyS0 anmelden kann.
    * `edit /etc/ssh/sshd_config`, und stellen Sie sicher, dass „PermitRootLogIn“ auf „no“ festgelegt ist.
    * `edit /etc/securetty file`, um die Anmeldung nur über ttyS0 zuzulassen. 

Wenn das System nun im Einzelbenutzermodus gestartet wird, können Sie sich über das Root-Kennwort anmelden.

Alternativ können Sie für RHEL 7.4+ oder 6.9+ den Einzelbenutzermodus in den GRUB-Eingabeaufforderungen aktivieren. Anweisungen dazu finden Sie [hier](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Manuelles Aktivieren des Einzelbenutzermodus in RHEL
Wenn Sie GRUB und den Stammzugriff wie oben beschrieben eingerichtet haben, können Sie den Einzelbenutzermodus wie folgt aktivieren:

1. Drücken Sie während des Neustarts der VM die ESC-TASTE, um den GRUB-Modus zu aktivieren.
1. Drücken Sie auf dem GRUB-Bildschirm „e“, um das ausgewählte Betriebssystem zu bearbeiten, in das Sie starten möchten (in der Regel ist dies die erste Zeile).
1. Suchen Sie nach der Kernelzeile. In Azure beginnt diese mit `linux16`.
1. Drücken Sie STRG+E, um zum Ende der Zeile zu wechseln.
1. Fügen Sie am Ende der Zeile Folgendes hinzu: `systemd.unit=rescue.target`
    * Dadurch starten Sie im Einzelbenutzermodus. Wenn Sie den Notfallmodus verwenden möchten, fügen Sie anstelle von `systemd.unit=rescue.target` am Ende der Zeile `systemd.unit=emergency.target` hinzu.
1. Drücken Sie STRG+X, um den Modus zu beenden und einen Neustart mit den angewendeten Einstellungen durchzuführen.
1. Sie werden zur Eingabe des Administratorkennworts aufgefordert, bevor Sie in den Einzelbenutzermodus wechseln können. Dies ist das Kennwort, das Sie in den obigen Anweisungen erstellt haben.    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Aktivieren des Einzelbenutzermodus ohne aktiviertes Stammkonto in RHEL
Wenn Sie die obigen Schritte zum Aktivieren des Stammbenutzers nicht ausgeführt haben, können Sie dennoch das Stammkennwort zurücksetzen. Gehen Sie folgendermaßen vor:

> Hinweis: Wenn Sie SELinux verwenden, stellen Sie bitte sicher, dass Sie beim Zurücksetzen des Stammkennworts die in der [Red Hat-Dokumentation](https://aka.ms/rhel7grubterminal) beschriebenen zusätzlichen Schritte ausgeführt haben.

1. Drücken Sie während des Neustarts der VM die ESC-TASTE, um den GRUB-Modus zu aktivieren.
1. Drücken Sie auf dem GRUB-Bildschirm „e“, um das ausgewählte Betriebssystem zu bearbeiten, in das Sie starten möchten (in der Regel ist dies die erste Zeile).
1. Suchen Sie nach der Kernelzeile. In Azure beginnt diese mit `linux16`.
1. Fügen Sie `rd.break` am Ende der Zeile hinzu, und achten Sie darauf, dass ein Leerzeichen vor `rd.break` vorhanden ist (siehe folgendes Beispiel).
    - Dadurch wird der Startprozess unterbrochen, bevor die Steuerung von `initramfs` an `systemd` übergeben wird (Informationen dazu finden Sie in der [Red Hat-Dokumentation](https://aka.ms/rhel7rootpassword)).
1. Drücken Sie STRG+X, um den Modus zu beenden und einen Neustart mit den angewendeten Einstellungen durchzuführen.
1. Nach dem Start wird der Notfallmodus mit einem schreibgeschützten Dateisystem aktiviert. Geben Sie in der Shell `mount -o remount,rw /sysroot` ein, um das Stammdateisystem mit Lese-/Schreibberechtigungen erneut einzubinden.
1. Geben Sie nach dem Start im Einzelbenutzermodus `chroot /sysroot` ein, um in das `sysroot`-Jail zu wechseln.
1. Sie sind jetzt der Root-Benutzer. Sie können Ihr Stammkennwort mit `passwd` zurücksetzen und anschließend wie oben beschrieben in den Einzelbenutzermodus wechseln. Geben Sie anschließend `reboot -f` ein, um neu zu starten.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Hinweis: Wenn Sie den obigen Anweisungen folgen, gelangen Sie in die Notfall-Shell, sodass Sie auch Aufgaben wie das Bearbeiten von `fstab` ausführen können. Im Allgemeinen wird jedoch empfohlen, das Stammkennwort zurückzusetzen und damit den Einzelbenutzermodus zu aktivieren. 


## <a name="access-for-centos"></a>Zugriff für CentOS
Ähnlich wie in Red Hat Enterprise Linux erfordert der Einzelbenutzermodus in CentOS, dass GRUB und der Stammbenutzer aktiviert sind. 

### <a name="grub-access-in-centos"></a>GRUB-Zugriff in CentOS
In CentOS ist GRUB standardmäßig aktiviert. Starten Sie zum Aktivieren des GRUB-Modus Ihre VM mit `sudo reboot` neu, und drücken Sie eine beliebige Taste. Daraufhin wird der GRUB-Bildschirm angezeigt.

### <a name="single-user-mode-in-centos"></a>Einzelbenutzermodus in CentOS
Befolgen Sie die obigen Anweisungen für RHEL, um den Einzelbenutzermodus in CentOS zu aktivieren.

## <a name="access-for-ubuntu"></a>Zugriff für Ubuntu 
Ubuntu-Images erfordern kein Stammkennwort. Wenn das System im Einzelbenutzermodus gestartet wird, können Sie es ohne zusätzliche Anmeldeinformationen verwenden. 

### <a name="grub-access-in-ubuntu"></a>GRUB-Zugriff in Ubuntu
Halten Sie zum Zugreifen auf GRUB während des Starts der VM die ESC-TASTE gedrückt. 

Standardmäßig zeigen Ubuntu-Images den GRUB-Bildschirm nicht automatisch an. Dies können Sie durch Ausführen der folgenden Anweisungen ändern:
1. Öffnen Sie `/etc/default/grub.d/50-cloudimg-settings.cfg` in einem Text-Editor Ihrer Wahl.
1. Ändern Sie den Wert von `GRUB_TIMEOUT` in einen Wert ungleich null.
1. Öffnen Sie `/etc/default/grub` in einem Text-Editor Ihrer Wahl.
1. Kommentieren Sie die Zeile `GRUB_HIDDEN_TIMEOUT=1` aus.
1. Führen Sie `sudo update-grub` aus.

### <a name="single-user-mode-in-ubuntu"></a>Einzelbenutzermodus in Ubuntu
Ubuntu aktiviert automatisch den Einzelbenutzermodus, wenn ein normaler Start nicht möglich ist. Um den Einzelbenutzermodus manuell zu aktivieren, gehen Sie wie folgt vor:

1. Drücken Sie auf dem GRUB-Bildschirm „e“, um Ihren Starteintrag (Ubuntu-Eintrag) zu bearbeiten.
1. Suchen Sie nach der Zeile, die mit `linux` beginnt, und dann nach `ro`.
1. Fügen Sie `single` nach `ro` hinzu, und achten Sie darauf, dass vor und nach `single` ein Leerzeichen vorhanden ist.
1. Drücken Sie STRG+X, um einen Neustart mit diesen Einstellungen durchzuführen und den Einzelbenutzermodus zu aktivieren.

## <a name="access-for-coreos"></a>Zugriff für CoreOS
Der Einzelbenutzermodus in CoreOS erfordert, dass GRUB aktiviert ist. 

### <a name="grub-access-in-coreos"></a>GRUB-Zugriff in CoreOS
Drücken Sie zum Zugreifen auf GRUB während des Starts der VM eine beliebige Taste.

### <a name="single-user-mode-in-coreos"></a>Einzelbenutzermodus in CoreOS
CoreOS aktiviert automatisch den Einzelbenutzermodus, wenn ein normaler Start nicht möglich ist. Um den Einzelbenutzermodus manuell zu aktivieren, gehen Sie wie folgt vor:
1. Drücken Sie auf dem GRUB-Bildschirm „e“, um Ihren Starteintrag zu bearbeiten.
1. Suchen Sie nach der Zeile, die mit `linux$` beginnt. Es sollte 2, eingekapselt in verschiedene If-/Else-Klauseln, angezeigt werden.
1. Fügen Sie `coreos.autologin=ttyS0` an das Ende beider `linux$`-Zeilen an.
1. Drücken Sie STRG+X, um einen Neustart mit diesen Einstellungen durchzuführen und den Einzelbenutzermodus zu aktivieren.

## <a name="access-for-suse-sles"></a>Zugriff für SUSE SLES
Neuere Images von SLES 12 SP3 und höher ermöglichen den Zugriff über die serielle Konsole, wenn das System im Notfallmodus gestartet wird. 

### <a name="grub-access-in-suse-sles"></a>GRUB-Zugriff in SUSE SLES
Der GRUB-Zugriff in SLES erfordert eine Bootloaderkonfiguration über YaST. Gehen Sie dazu wie folgt vor:

1. Stellen Sie eine SSH-Verbindung mit Ihrer SLES-VM her, und führen Sie `sudo yast bootloader` aus. Navigieren Sie mit den Tasten `tab` und `enter` sowie den Pfeiltasten durch das Menü. 
1. Navigieren Sie zu `Kernel Parameters`, und überprüfen Sie `Use serial console`. 
1. Fügen Sie den Konsolenargumenten `serial --unit=0 --speed=9600 --parity=no` hinzu.

1. Drücken Sie zum Speichern Ihrer Einstellungen und Beenden F10.
1. Starten Sie zum Aktivieren des GRUB-Modus Ihre VM neu, und drücken Sie während der Startsequenz eine beliebige Taste, damit der GRUB-Bildschirm nicht geschlossen wird.
    - Das Standardzeitlimit für GRUB ist 1 Sekunde. Sie können diese Einstellung ändern, indem Sie die Variable `GRUB_TIMEOUT` in `/etc/default/grub` bearbeiten.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Einzelbenutzermodus in SUSE SLES
Die Notfall-Shell wird automatisch geöffnet, wenn SLES nicht normal gestartet werden kann. Gehen Sie wie folgt vor, um die Notfall-Shell manuell zu öffnen:

1. Drücken Sie auf dem GRUB-Bildschirm „e“, um Ihren Starteintrag (SLES-Eintrag) zu bearbeiten.
1. Suchen Sie nach der Kernelzeile. Diese beginnt mit `linux`.
1. Fügen Sie `systemd.unit=emergency.target` an das Ende der Zeile an.
1. Drücken Sie STRG+X, um einen Neustart mit diesen Einstellungen durchzuführen und die Notfall-Shell zu öffnen.
   > Beachten Sie, dass die Notfall-Shell mit einem _schreibgeschützten_ Dateisystem aktiviert wird. Wenn Sie Dateien bearbeiten möchten, müssen Sie das Dateisystem mit Lese-/Schreibberechtigungen erneut einbinden. Geben Sie dazu in der Shell `mount -o remount,rw /` ein.

## <a name="access-for-oracle-linux"></a>Zugriff für Oracle Linux
Ähnlich wie in Red Hat Enterprise Linux erfordert der Einzelbenutzermodus in Oracle Linux, dass GRUB und der Stammbenutzer aktiviert sind. 

### <a name="grub-access-in-oracle-linux"></a>GRUB-Zugriff in Oracle Linux
In Oracle Linux ist GRUB standardmäßig aktiviert. Starten Sie zum Aktivieren des GRUB-Modus Ihre VM mit `sudo reboot` neu, und drücken Sie die ESC-TASTE. Daraufhin wird der GRUB-Bildschirm angezeigt.

### <a name="single-user-mode-in-oracle-linux"></a>Einzelbenutzermodus in Oracle Linux
Befolgen Sie die obigen Anweisungen für RHEL, um den Einzelbenutzermodus in Oracle Linux zu aktivieren.

## <a name="next-steps"></a>Nächste Schritte
* Die Hauptseite der Linux-Dokumentation für die serielle Konsole finden Sie [hier](serial-console.md).
* Verwenden der seriellen Konsole für [NMI- und SysRq-Aufrufe](serial-console-nmi-sysrq.md)
* Die serielle Konsole ist auch für [Windows](../windows/serial-console.md)-VMs verfügbar.
* Erfahren Sie mehr über die [Startdiagnose](boot-diagnostics.md).
