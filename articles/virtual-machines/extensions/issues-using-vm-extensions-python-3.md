---
title: Probleme bei der Verwendung von VM-Erweiterungen in Python 3-fähigen Linux-Systemen mit Azure Virtual Machines
description: Erfahren Sie etwas über die Verwendung von VM-Erweiterungen in Python 3-fähigen Linux-Systemen.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: c5593257fa17944eebce6346a1eb9e88e7af2c06
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965951"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Probleme bei der Verwendung von VM-Erweiterungen in Python 3-fähigen Linux-Systemen mit Azure Virtual Machines

> [!NOTE]
> Microsoft ermutigt Benutzer, in ihren Systemen **Python 3.x** einzuführen, sofern die Workloads keine Unterstützung von **Python 2.x** erfordern. Beispiele für diese Anforderung sind veraltete Verwaltungsskripts oder Erweiterungen wie **Azure Disk Encryption** und **Azure Monitor**.
>
> Vor der Installation von **Python 2.x** in Produktionsumgebungen sollten Sie die Frage der langfristigen Unterstützung von Python 2.x untersuchen. Dies gilt insbesondere für die Möglichkeit, Sicherheitsupdates zu erhalten. Da Updates einiger Produkte, einschließlich einiger der erwähnten Erweiterungen, die Unterstützung für **Python 3.8** beinhalten, sollten Sie Ihre Verwendung von Python 2.x einstellen.

Einige Linux-Distributionen wurden auf Python 3.8 umgestellt, und der Legacy-Einstiegspunkt `/usr/bin/python` für Python wurde vollständig entfernt. Diese Umstellung wirkt sich auf die standardmäßige automatisierte Bereitstellung bestimmter VM-Erweiterungen mit diesen beiden Bedingungen aus:

- Erweiterungen, bei denen die Umstellung auf Python 3.x noch durchgeführt wird
- Erweiterungen, die den Legacy-Einstiegspunkt `/usr/bin/python` verwenden

Benutzer von Linux-Distributionen, die auf **Python 3.x** umgestellt haben, müssen sicherstellen, dass der Legacy-Einstiegspunkt `/usr/bin/python` vorhanden ist, bevor sie diese Erweiterungen auf ihren VMs bereitstellen. Andernfalls tritt bei der Bereitstellung der Erweiterung möglicherweise ein Fehler auf. 

- Zu den betroffenen unterstützten Linux-Distributionen gehören **Ubuntu Server 20.04 LTS** und **Ubuntu Pro 20.04 LTS**.

- Zu den betroffenen VM-Erweiterungen gehören **Azure Disk Encryption**, **Log Analytics**, **VM Access** (für die Kennwortzurücksetzung) und **Guest Diagnostics** (für zusätzliche Leistungsindikatoren).

Direkte Upgrades, z. B. ein Upgrade von **Ubuntu 18.04 LTS** auf **Ubuntu 20.04 LTS**, sollten den Symlink `/usr/bin/python` beibehalten und sind daher nicht weiter betroffen.

## <a name="resolution"></a>Lösung

Beachten Sie diese allgemeinen Empfehlungen, bevor Sie Erweiterungen in den zuvor in der Zusammenfassung beschriebenen betroffenen Szenarien bereitstellen:

1. Stellen Sie vor dem Bereitstellen der Erweiterung den Symlink `/usr/bin/python` mithilfe der vom Anbieter der Linux-Distribution bereitgestellten Methode wieder her.

   - Beispiel für **Python 2.7**: `sudo apt update && sudo apt install python-is-python2`

1. Diese Empfehlung gilt für Azure-Kunden und wird in Azure Stack nicht unterstützt:

   - Wenn Sie bereits eine Instanz bereitgestellt haben, in der dieses Problem auftritt, verwenden Sie die Funktion Befehl ausführen auf dem VM-Blatt, um die oben genannten Befehle auszuführen. Die Befehlserweiterung zum Ausführen von Befehlen ist von der Umstellung auf Python 3.8 nicht betroffen.

1. Wenn Sie eine neue Instanz bereitstellen und während der Bereitstellung eine Erweiterung festlegen müssen, verwenden Sie **cloud-init**-Benutzerdaten, um die oben genannten Pakete zu installieren.

   Beispiel für Python 2.7:

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. Wenn die Richtlinienadministratoren Ihrer Organisation festlegen, dass Erweiterungen nicht auf VMs bereitgestellt werden sollen, können Sie die Erweiterungsunterstützung während der Bereitstellung deaktivieren:

   - REST-API

     So deaktivieren und aktivieren Sie Erweiterungen, wenn Sie eine VM mit dieser Eigenschaft bereitstellen können

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Other base system changes since 18.04 LTS – Python 3 by default](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default) (Andere grundlegende Systemänderungen seit 18.04 LTS – Python 3 als Standard).
