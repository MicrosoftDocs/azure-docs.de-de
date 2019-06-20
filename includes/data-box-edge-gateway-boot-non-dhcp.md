---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 880b630ae48eda086f6454f0d7108d27d3403b77
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178759"
---
Wenn Sie in einer Nicht-DHCP-Umgebung starten, führen Sie diese Schritte aus, um den virtuellen Computer für Ihr Data Box Gateway bereitzustellen.

1. [Stellen Sie eine Verbindung mit der Windows PowerShell-Schnittstelle des Geräts her](#connect-to-the-powershell-interface).
2. Verwenden Sie das `Get-HcsIpAddress`-Cmdlet, um die Netzwerkschnittstellen aufzulisten, die auf Ihrem virtuellen Gerät aktiviert sind. Wenn für das Gerät eine einzelne Netzwerkschnittstelle aktiviert ist, wird dieser Schnittstelle der Standardname `Ethernet`zugewiesen.

    Nachfolgend sehen Sie ein Beispiel für die Verwendung dieses Cmdlets:

    ```
    [10.100.10.10]: PS>Get-HcsIpAddress

    OperationalStatus : Up
    Name              : Ethernet
    UseDhcp           : True
    IpAddress         : 10.100.10.10
    Gateway           : 10.100.10.1
    ```

3. Verwenden Sie das `Set-HcsIpAddress` -Cmdlet, um das Netzwerk zu konfigurieren. Siehe folgendes Beispiel:

    ```
    Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1
    ```

