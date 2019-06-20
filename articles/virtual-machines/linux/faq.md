---
title: Häufig gestellte Fragen zu Linux-VMs in Azure | Microsoft-Dokumentation
description: Hier finden Sie Antworten auf die häufigsten Fragen zu virtuellen Linux-Computern, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 0623a7aff15184822ee8abde0b3c751f8a105b5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65463581"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Häufig gestellte Fragen zu virtuellen Linux-Computern
Dieser Artikel enthält einige häufig gestellte Fragen zu virtuellen Linux-Computern, die in Azure mit dem Resource Manager-Bereitstellungsmodell erstellt wurden. Die Windows-Version dieses Themas finden Sie unter [Häufig gestellte Fragen zu virtuellen Windows-Computern](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Was kann ich auf einem virtuellen Azure-Computer ausführen?
Alle Abonnenten können Serversoftware auf einem virtuellen Azure-Computer ausführen. Weitere Informationen finden Sie unter [Linux auf von Azure unterstützten Distributionen](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Wie viel Speicher kann mit einem virtuellen Computer verwendet werden?
Jeder Datenträger kann bis zu 4 TB (4.095 GB) groß sein. Die Anzahl der Datenträger, die Sie verwenden können, hängt von der Größe des virtuellen Computers ab. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks ist die empfohlene Lösung für die dauerhafte Speicherung von Daten auf Datenträgern, die mit Azure Virtual Machines verwendet werden. Sie können für jeden virtuellen Computer mehrere Datenträger (Managed Disks) verwenden. Managed Disks bietet zwei Arten von permanentem Speicher: verwaltete Datenträger der Tarife Premium und Standard. Informationen zu den Preisen finden Sie unter [Verwaltete Datenträger – Preise](https://azure.microsoft.com/pricing/details/managed-disks).

Mit Azure-Speicherkonten kann auch Speicher für Betriebssystemdatenträger und Datenträger für Daten bereitgestellt werden. Bei jedem Datenträger handelt es sich um eine VHD-Datei, die als Seiten-Blob gespeichert wird. Ausführliche Informationen zu Preisen finden Sie unter [Speicherpreisübersicht](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Wie kann ich auf meinen virtuellen Computer zugreifen?
Stellen Sie eine Remoteverbindung mit Secure Shell (SSH) her, um sich beim virtuellen Computer anzumelden. Informationen zum Herstellen einer Verbindung finden Sie in den Anweisungen [für Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) oder [für Linux und Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). SSH erlaubt standardmäßig maximal 10 gleichzeitige Verbindungen. Sie können diese Anzahl erhöhen, indem Sie die Konfigurationsdatei bearbeiten.

Wenn Probleme auftreten, finden Sie weitere Informationen unter [Problembehandlung für SSH-Verbindungen (Secure Shell)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kann ich den temporären Datenträger (/dev/sdb1) zum Speichern von Daten verwenden?
Verwenden Sie den temporären Datenträger (/dev/sdb1) nicht zum Speichern von Daten. Er dient nur zur temporären Speicherung. Sie riskieren den Verlust von Daten, die nicht wiederhergestellt werden können.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kann ich einen vorhandenen virtuellen Azure-Computer kopieren oder klonen?
Ja. Anweisungen hierzu finden Sie unter [Erstellen einer Kopie eines virtuellen Linux-Computers im Resource Manager-Bereitstellungsmodell](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Warum werden die Regionen „Kanada, Mitte“ und „Kanada, Osten“ nicht in Azure Resource Manager angezeigt?
Die beiden neuen Regionen „Kanada, Mitte“ und „Kanada, Osten“ werden nicht automatisch für das Erstellen von virtuellen Computern im Rahmen von vorhandenen Azure-Abonnements registriert. Diese Registrierung erfolgt automatisch, wenn ein virtueller Computer mit Azure Resource Manager über das Azure-Portal für eine andere Region bereitgestellt wird. Nach der Bereitstellung eines virtuellen Computers in einer anderen Azure-Region sollten die neuen Regionen für nachfolgende virtuelle Computer verfügbar sein.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kann ich meinem virtuellen Computer nach der Erstellung eine NIC hinzufügen?
Ja, dies ist jetzt möglich. Der virtuelle Computer muss zuerst beendet/freigegeben werden. Anschließend können Sie eine NIC hinzufügen oder entfernen (sofern es sich nicht um die letzte NIC auf dem virtuellen Computer handelt). 

## <a name="are-there-any-computer-name-requirements"></a>Gibt es Anforderungen an den Computernamen?
Ja. Der Computername kann maximal 64 Zeichen lang sein. Weitere Informationen zum Benennen von Ressourcen finden Sie unter [Regeln und Einschränkungen der Namenskonventionen](/azure/architecture/best-practices/naming-conventions).

## <a name="are-there-any-resource-group-name-requirements"></a>Gelten für Namen von Ressourcengruppen bestimmte Anforderungen?
Ja. Der Name der Ressourcengruppe darf maximal 90 Zeichen lang sein. Weitere Informationen zu Ressourcengruppen finden Sie unter [Regeln und Einschränkungen der Namenskonventionen](/azure/architecture/best-practices/naming-conventions).

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Welche Anforderungen an den Benutzernamen gelten beim Erstellen eines virtuellen Computers?

Benutzernamen müssen 1 bis 32 Zeichen lang sein.

Die folgenden Benutzernamen sind nicht zulässig:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Welche Anforderungen an das Kennwort gelten beim Erstellen eines virtuellen Computers?

Je nach verwendetem Tool gelten unterschiedliche Anforderungen an die Kennwortlänge:
 - Portal: zwischen 12 und 72 Zeichen
 - PowerShell: zwischen 8 und 123 Zeichen
 - Befehlszeilenschnittstelle: zwischen 12 und 123 Zeichen
 

Kennwörter müssen außerdem 3 der folgenden 4 Komplexitätsanforderungen erfüllen:

* Kleinbuchstaben
* Großbuchstaben
* Eine Ziffer
* Ein Sonderzeichen (Übereinstimmung mit regulärem Ausdruck [\W_])

Die folgenden Kennwörter sind nicht zulässig:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Password!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>
