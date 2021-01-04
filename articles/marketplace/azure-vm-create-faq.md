---
title: Häufige Fragen zu VMs im Azure Marketplace
description: Häufige Fragen, die sich beim Erstellen eines virtuellen Computers im Azure Marketplace ergeben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 88ec58ca9bcfa5c64036d3b65e77f3248e26357d
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124950"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Häufige Fragen zu VMs im Azure Marketplace

Diese häufig gestellten Fragen (FAQ) beziehen sich auf allgemeine Probleme, die beim Erstellen eines VM (Virtual Machine)-Angebots im Azure Marketplace auftreten können.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Wie kann ich ein virtuelles privates Netzwerk (VPN) für VMs konfigurieren?

Bei Verwendung des Azure Resource Manager-Bereitstellungsmodells haben Sie drei Optionen:

- [Erstellen eines routenbasierten VPN-Gateways mit dem Azure-Portal](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Erstellen eines routenbasierten VPN-Gateways mit Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Erstellen eines routenbasierten VPN-Gateways mithilfe von CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Was sind Microsoft-Supportrichtlinien für die Ausführung von Microsoft-Serversoftware auf Azure-basierten VMs?

Ausführliche Informationen finden Sie unter [Microsoft-Server-Software-Support für virtuelle Maschinen von Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Wie kann ich auf einem virtuellen Computer die benutzerdefinierte Skripterweiterung in der Startaufgabe verwalten?

Informationen dazu, wie Sie die benutzerdefinierte Skripterweiterung mit dem Azure PowerShell-Modul verwenden, zu Azure Resource Manager-Vorlagen und zu Problembehandlungsschritten für Windows-Systeme finden Sie unter [CustomScript-Erweiterung für Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Werden 32-Bit-Anwendungen oder -Dienste für Azure Marketplace unterstützt?

Nein. Bei allen unterstützten Betriebssystemen und Standarddiensten für Azure-VMs handelt es sich um 64-Bit-Versionen. Die meisten 64-Bit-Betriebssysteme unterstützen aus Gründen der Abwärtskompatibilität zwar 32-Bit-Versionen von Anwendungen, aber die Nutzung von 32-Bit-Anwendungen für Ihre VM-Lösung wird nicht unterstützt, und wir raten daher dringend von der Nutzung ab. Erstellen Sie Ihre Anwendung als 64-Bit-Projekt neu.

Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

- [Running 32-bit applications](/windows/desktop/WinProg64/running-32-bit-applications) (Ausführen von 32-Bit-Anwendungen)
- [Unterstützung für 32-Bit-Betriebssysteme auf virtuellen Azure-Computern](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft-Server-Software-Support für virtuelle Maschinen von Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Error: Die virtuelle Festplatte ist im Imagerepository bereits als Ressource registriert.

Bei jeder versuchten Erstellung eines Images aus virtuellen Festplatten erhalte ich in Azure PowerShell eine Fehlermeldung mit dem Hinweis, dass die virtuelle Festplatte im Imagerepository bereits als Ressource registriert ist. Ich habe vorher weder ein Image erstellt, noch habe ich ein Image mit diesem Namen in Azure gefunden. Wie löse ich dieses Problem?

Dieses Problem tritt normalerweise auf, wenn Sie eine VM aus einer VHD erstellt haben, die mit einer Sperre versehen ist. Stellen Sie sicher, dass über diese virtuelle Festplatte kein virtueller Computer zugeordnet wird, und wiederholen Sie anschließend den Vorgang. Öffnen Sie ein Supportticket, falls dieses Problem weiterhin besteht. Weitere Informationen finden Sie unter [Support für das Programm „Kommerzieller Marketplace“ im Partner Center](support.md).

## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von Problemen bei der VM-Zertifizierung](azure-vm-create-certification-faq.md)