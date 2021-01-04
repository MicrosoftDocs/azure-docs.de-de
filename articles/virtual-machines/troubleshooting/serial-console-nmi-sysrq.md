---
title: Serielle Azure-Konsole für SysRq- und NMI-Aufrufe | Microsoft-Dokumentation
description: Verwenden der seriellen Konsole für SysRq- und NMI-Aufrufe auf virtuellen Azure-Computern.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022852"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>Verwenden der seriellen Azure-Konsole für SysRq- und NMI-Aufrufe

## <a name="system-request-sysrq"></a>SysRq (System Request, Systemabfrage)
Eine SysRq ist eine Sequenz von Schlüsseln, die vom Linux-Betriebssystemkernel verstanden wird und eine Reihe von vordefinierten Aktionen auslösen kann. Diese Befehle werden häufig verwendet, wenn die Problembehandlung des virtuellen Computers oder dessen Wiederherstellung nicht über die herkömmliche Verwaltung ausgeführt werden kann (etwa, wenn der virtuelle Computer nicht reagiert). Mithilfe des SysRq-Features der seriellen Azure-Konsole wird das Drücken der SysRq-Taste (je nach Tastatur auch S-Abf-Taste genannt) imitiert, und auf einer physischen Tastatur werden Zeichen eingegeben.

Sobald die SysRq-Sequenz übermittelt wurde, bestimmt die Kernelkonfiguration die Antwort des Systems. Informationen zum Aktivieren und Deaktivieren von SysRq finden Sie im *SysRq-Administratorhandbuch* ([Text](https://aka.ms/kernelorgsysreqdoc) | [Markdown](https://aka.ms/linuxsysrq)).

Mit der seriellen Konsole in Azure können Sie einen SysRq-Befehl an einen virtuellen Azure-Computer senden. Verwenden Sie dazu das Tastatursymbol in der unten gezeigten Befehlsleiste.

![Screenshot der seriellen Azure-Konsole. Das Tastatursymbol ist hervorgehoben, und das Menü ist sichtbar. Dieses Menü enthält das Element „SysRq-Befehl senden“.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Durch die Auswahl von „SysRq-Befehl senden“ wird ein Dialogfeld geöffnet. Hier werden entweder allgemeine SysRq-Optionen bereitgestellt, oder Sie können eine Sequenz von im Dialogfeld eingegebenen SysRq-Befehlen übernehmen.  Dadurch werden eine Reihe von SysRq-Befehlen zum Ausführen einer Operation auf höchster Stufe (z. B. ein sicherer Neustart mit `REISUB`) ermöglicht.

![Screenshot des Dialogfelds „SysRq-Befehl an Gast senden“. Die Option zum Eingeben von Befehlen ist ausgewählt, und das Befehlsfeld enthält „REISUB“.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Der SysRq-Befehl kann nicht auf virtuellen Computern verwendet werden, die angehalten wurden oder deren Kernel sich in einem nicht reaktionsfähigen Status befindet (z. B. Kernel panic).

### <a name="enable-sysrq"></a>Aktivieren von SysRq
Wie im vorgenannten *SysRq-Administratorhandbuch* beschrieben, kann SysRq so konfiguriert werden, dass alle, keine oder nur bestimmte Befehle zur Verfügung stehen. Mit dem nachfolgenden Schritt können Sie alle SysRq-Befehle aktivieren, doch wird diese Auswahl einen Neustart nicht überstehen:
```
echo "1" >/proc/sys/kernel/sysrq
```
Um die SysReq-Konfiguration dauerhaft zu speichern, gehen Sie zum Aktivieren aller SysRq-Befehle wie folgt vor:
1. Hinzufügen der folgenden Zeile zu */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Neustarten oder Aktualisieren von sysctl durch Ausführen von <br>
    `sysctl -p`

### <a name="command-keys"></a>Befehlstasten
Aus dem oben genannten SysRq-Administratorhandbuch entnommen:

|Get-Help| Funktion
| ------| ----------- |
|``b``  |   Führt sofort einen Systemneustart ohne Synchronisieren oder Aufheben der Bereitstellung der Datenträger durch.
|``c``  |   Führt einen Systemabsturz durch eine NULL-Zeigerdereferenzierung herbei. Wenn konfiguriert, wird ein Absturzabbild (Crash Dump) erstellt.
|``d``  |   Zeigt alle Sperren, die gehalten werden.
|``e``  |   Sendet ein SIGTERM-Signal an alle Prozesse, mit Ausnahme von „init“.
|``f``  |   Ruft den OOM-Killer auf, um einen Memory Hog-Prozess (Speicherkiller) zu beenden, allerdings ohne „panic“, wenn nichts beendet werden kann.
|``g``  |   Wird von kgdb (Kernel-Debugger) verwendet.
|``h``  |   Zeigt die Hilfe an (mit jeder anderen außer den hier genannten Tasten können Sie ebenfalls die Hilfe anzeigen, jedoch ist ``h`` einfacher zu merken :-)
|``i``  |    Sendet ein SIGKILL-Signal an alle Prozesse, mit Ausnahme von „init“.
|``j``  |    Zwangsweise „einfach reaktivieren“ – von FIFREEZE ioctl fixierte Dateisysteme.
|``k``  |    Secure Access Key (SAK) beendet alle Programme auf der aktuellen virtuellen Konsole. HINWEIS:  Beachten Sie die wichtigen Kommentare im SAK-Abschnitt weiter unten.
|``l``  |    Zeigt einen Stack-Backtrace für alle aktiven CPUs.
|``m``  |    Sichert die aktuellen Speicherinformationen in der Konsole.
|``n``  |    Wird verwendet, um RT-Tasks ansprechend zu machen.
|``o``  |    Fährt Ihr System herunter (sofern konfiguriert und unterstützt).
|``p``  |    Sichert die aktuellen Register und Flags in der Konsole.
|``q``  |    Sichert pro CPU Listen aller zurückgesetzten HR-Timer (aber NICHT der regulären Timer aus der timer_list-Struktur) sowie detaillierte Informationen zu allen Clock-Event-Geräten.
|``r``  |    Deaktiviert den Raw-Modus der Tastatur und legt den XLATE-Modus fest.
|``s``  |    Versucht, alle bereitgestellten Dateisysteme zu synchronisieren.
|``t``  |    Sichert eine Liste der aktuellen Tasks und der zugehörigen Informationen in der Konsole.
|``u``  |    Versucht, alle bereitgestellten Dateisysteme schreibgeschützt erneut bereitzustellen.
|``v``  |    Erzwingt die Wiederherstellung der Framebuffer-Konsole.
|``v``  |    Verursacht ein ETM-Pufferspeicherabbild [ARM-spezifisch].
|``w``  |    Sichert Tasks, die sich im unterbrechungsfreien (gesperrten) Zustand befinden.
|``x``  |    Wird von der Xmon-Schnittstelle auf PPC/PowerPC-Plattformen verwendet. Zeigt globale PMU-Register auf SPARC64 an. Sichert alle TLB-Einträge auf MIPS.
|``y``  |    Zeigt die globalen CPU-Register an [SPARC64-spezifisch].
|``z``  |    Sichert den fTRACE-Puffer.
|``0``-``9`` | Legt die Protokollebene für die Konsole fest, die bestimmt, welche Kernelnachrichten an die Konsole ausgegeben werden. (``0`` zum Beispiel würde dafür sorgen, dass nur Notfallmeldungen wie PANIC oder OOPS auf Ihre Konsole gelangen.)

### <a name="distribution-specific-documentation"></a>Distributionsspezifische Dokumentation ###
Eine distributionsspezifische Dokumentation zu SysRq und eine Beschreibung der Schritte, mit denen Sie Linux so konfigurieren, dass beim Empfangen eines SysRq-Befehls des Typs „Crash“ ein Absturzabbild erstellt wird, finden Sie unter folgenden Links:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Kernel-Absturzabbild](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Was ist die SysRq-Funktion, und wie wird sie verwendet?](https://access.redhat.com/articles/231663)
- [Verwenden der SysRq-Funktion zum Sammeln von Informationen von einem RHEL-Server](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurieren der Erfassung des Kernel-Kernspeicherabbilds](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Sammeln von Absturzprotokollen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>NMI (Nicht maskierbarer Interrupt)
Ein nicht maskierbarer Interrupt (NMI) dient dazu, ein Signal zu erstellen, das die Software auf einem virtuellen Computer nicht ignoriert. In der Vergangenheit wurden NMIs verwendet, um Hardwareprobleme auf Systemen zu überwachen, die bestimmte Antwortzeiten erforderten.  Heute verwenden Programmierer und Systemadministratoren NMIs häufig als Mechanismus zum Debuggen oder Beheben von Problemen in Systemen, die nicht mehr reagieren.

Sie können mit der seriellen Konsole einen NMI an einen virtuellen Azure-Computer senden. Verwenden Sie dazu das Tastatursymbol in der unten gezeigten Befehlsleiste. Nach der Übermittlung des NMI bestimmt die Konfiguration des virtuellen Computers, wie das System reagiert.  Linux-Betriebssysteme können so konfiguriert werden, dass beim Empfang eines NMI ein Absturz erfolgt und ein Speicherabbild erstellt wird.

![Screenshot der seriellen Konsole. Das Tastatursymbol ist hervorgehoben, und das Menü ist sichtbar. Dieses Menü enthält das Element „Nicht maskierbaren Interrupt senden“.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Aktivieren von NMI
Bei Linux-Systemen, die sysctl zum Konfigurieren von Kernelparametern unterstützen, können Sie beim Empfang dieses NMI einen „Panic“-Status aktivieren. Gehen Sie dazu wie folgt vor:
1. Hinzufügen der folgenden Zeile zu */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Neustarten oder Aktualisieren von sysctl durch Ausführen von <br>
    `sysctl -p`

Weitere Informationen zu Linux-Kernelkonfigurationen (einschließlich `unknown_nmi_panic`, `panic_on_io_nmi` und `panic_on_unrecovered_nmi`) finden Sie hier: [Documentation for /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt) (Dokumentation zu /proc/sys/kernel/) Eine distributionsspezifische Dokumentation zu NMI und eine Beschreibung der Schritte, mit denen Sie Linux so konfigurieren, dass beim Empfangen eines NMI ein Absturzabbild erstellt wird, finden Sie unter folgenden Links:

### <a name="ubuntu"></a>Ubuntu
 - [Kernel-Absturzabbild](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Was ist ein NMI und wofür kann ich ihn verwenden?](https://access.redhat.com/solutions/4127)
 - [Wie kann ich mein System so konfigurieren, dass es beim Drücken des NMI-Switches abstürzt?](https://access.redhat.com/solutions/125103)
 - [Administratorhandbuch zu Absturzabbildern](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Konfigurieren der Erfassung des Kernel-Kernspeicherabbilds](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Sammeln von Absturzprotokollen](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Nächste Schritte
* Die Hauptseite der Linux-Dokumentation für die serielle Konsole finden Sie [hier](serial-console-linux.md).
* Verwenden Sie die seriellen Konsole zum [Starten in GRUB und zum Wechseln in den Einzelbenutzermodus](serial-console-grub-single-user-mode.md)
* Die serielle Konsole ist auch für [virtuelle Windows-Computer](serial-console-windows.md) verfügbar.
* Erfahren Sie mehr über die [Startdiagnose](boot-diagnostics.md).