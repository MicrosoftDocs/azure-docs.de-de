---
title: Problembehandlung beim Azure Linux-Gast-Agent
description: Problembehandlung bei Fehlern des Typs „Azure Linux-Gast-Agent funktioniert nicht“
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: fc609b60c9d5d4d4734c3d73cbda87935b533caf
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500256"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Problembehandlung beim Azure Linux-Gast-Agent

Der [Azure Linux-Gast-Agent](../extensions/agent-linux.md) ist ein VM-Agent (virtuelle Computer). Er ermöglicht dem virtuellen Computer die Kommunikation mit dem Fabric Controller (dem zugrunde liegenden physischen Server, auf dem der virtuelle Computer gehostet wird) über die IP-Adresse 168.63.129.16. Dies ist eine virtuelle öffentliche IP-Adresse für die Kommunikation. Unter [Was ist die IP-Adresse 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md) finden Sie weitere Informationen dazu.

## <a name="checking-agent-status-and-version"></a>Überprüfen des Agent-Status und der -Version

Siehe https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Problembehandlung beim VM-Agent, der den Status „Nicht bereit“ aufweist

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Schritt 1: Überprüfen, ob der Azure Linux-Gast-Agent-Dienst ausgeführt wird

Abhängig von der Distribution kann der Dienstname „walinuxagent“ oder „waagent“ lauten:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Wenn die Dienste angezeigt und ausgeführt werden, starten Sie den Dienst neu, um zu überprüfen, ob das Problem behoben ist. Wenn die Dienste beendet wurden, starten Sie die Dienste, und warten Sie einige Minuten. Überprüfen Sie dann, ob der **Agent-Status** als **Bereit** angezeigt wird. Wenden Sie sich zur weiteren Problembehandlung an den [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Schritt 2: Überprüfen, ob automatische Updates aktiviert sind

Sie überprüfen diese Einstellung in „/etc/waagent.conf“:

```
AutoUpdate.Enabled=y
```

Weitere Informationen zum Aktualisieren des Azure Linux-Agents finden Sie unter https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent. 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Schritt 3: Überprüfen, ob die VM eine Verbindung mit dem Fabric Controller herstellen kann

Verwenden Sie ein Tool wie cURL, um zu testen, ob die VM über die Ports 80, 32526 und 443 eine Verbindung mit 168.63.129.16 herstellen kann. Wenn die VM nicht wie erwartet eine Verbindung herstellt, überprüfen Sie, ob die ausgehende Kommunikation über die Ports 80, 443 und 32526 in Ihrer lokalen Firewall auf der VM offen ist. Wenn diese IP-Adresse blockiert wurde, zeigt der VM-Agent in verschiedenen Szenarien möglicherweise unerwartetes Verhalten.

## <a name="advanced-troubleshooting"></a>Erweiterte Problembehandlung

Ereignisse für die Problembehandlung beim Azure Linux-Gast-Agent werden in den folgenden Protokolldateien aufgezeichnet:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Es kann keine Verbindung mit der WireServer-IP (Host-IP) hergestellt werden 

Sie bemerken die folgenden Fehlereinträge in der Datei „/var/log/waagent.log“:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Analyse**

Die VM kann die WireServer-IP-Adresse auf dem Hostserver nicht erreichen.

**Lösung**

1. Da die WireServer-IP-Adresse nicht erreichbar ist, stellen Sie per SSH eine Verbindung mit der VM her, und versuchen Sie dann, über cURL auf die folgende URL zuzugreifen: http://168.63.129.16/?comp=versions. 
1. Suchen Sie nach Problemen, die möglicherweise von einer Firewall, einem Proxy oder einer anderen Quelle verursacht werden, die den Zugriff auf die IP-Adresse 168.63.129.16 blockieren könnten.
1. Überprüfen Sie, ob IPTables von Linux oder eine Firewall eines Drittanbieters den Zugriff auf die Ports 80, 443 und 32526 blockiert. Unter [Was ist die IP-Adresse 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md) finden Sie weitere Informationen dazu, warum diese Adresse nicht blockiert werden sollte.


## <a name="next-steps"></a>Nächste Schritte

Zur weiteren Problembehandlung des Fehlers „Windows Azure-Gast-Agent funktioniert nicht“ [wenden Sie sich an den Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).