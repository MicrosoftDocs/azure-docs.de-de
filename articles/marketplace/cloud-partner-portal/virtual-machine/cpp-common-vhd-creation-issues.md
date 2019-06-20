---
title: Allgemeine Probleme bei der VHD-Erstellung (FAQ) für den Azure Marketplace
description: Enthält häufig gestellte Fragen zur Erstellung virtueller Festplatten und zu den damit verbundenen Problemen.
services: Azure Marketplace
author: HannibalSII
ms.service: marketplace
ms.topic: article
ms.date: 10/02/2018
ms.author: hascipio; v-divte; v-miclar
ms.openlocfilehash: d8b532793282db18d6182237bb921bc118ea717b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938452"
---
# <a name="common-issues-during-vhd-creation-faq"></a>Allgemeine Probleme bei der VHD-Erstellung (FAQ)

In den folgenden häufig gestellten Fragen (FAQ) werden allgemeine Probleme behandelt, die bei der Erstellung von virtuellen Festplatten (VHDs) und virtuellen Computern (VMs) für VM-Angebote häufiger auftreten. 

## <a name="how-do-you-create-a-vm-from-the-azure-portal-using-the-vhd-that-is-uploaded-to-premium-storage"></a>Wie kann mithilfe der virtuellen Festplatte, die in Storage Premium hochgeladen wird, ein virtueller Computer im Azure-Portal erstellt werden?

Der Azure Marketplace verfügt derzeit nicht über die Unterstützung für die Erstellung von VM-Angeboten aus Images, die sich im verwalteten Speicher oder in Azure Storage Premium befinden.  Weitere Informationen zu diesen Speicheroptionen finden Sie in der [Übersicht über Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).


## <a name="can-you-use-generation-2-vms-for-offers"></a>Können VMs der Generation 2 für Angebote genutzt werden?

Nein. Nur VHDs der Generation 1 werden unterstützt.  Wir arbeiten aber derzeit mit dem Microsoft Azure Platform Team zusammen an der Unterstützung für VMs der Generation 2.  Weitere Informationen zu den Unterschieden finden Sie unter [Should I create a generation 1 or 2 virtual machine in Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) (Sollte ich einen virtuellen Computer der 1. oder 2. Generation in Hyper-V erstellen?).


## <a name="how-do-you-change-the-name-of-the-host"></a>Wie kann der Name des Hosts geändert werden?

Dies ist nicht möglich.  Nachdem die VM erstellt wurde, können Benutzer (auch Besitzer) den Namen des Hosts nicht mehr aktualisieren.


## <a name="how-do-you-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Wie wird der Remotedesktopdienst bzw. das zugehörige Kennwort für die Anmeldung zurückgesetzt?

In den folgenden Artikeln wird beschrieben, wie Sie das Zurücksetzen des Remotedesktopdiensts für Windows- und Linux-basierte VMs durchführen:   

- [Zurücksetzen des Remotedesktopdiensts oder seines Anmeldekennworts in einer Windows-VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
- [Zurücksetzen eines Kennworts oder eines SSH-Schlüssels für einen virtuellen Linux-Computer, Beheben von SSH-Konfigurationsproblemen und Überprüfen der Datenträgerkonsistenz mithilfe der VMAccess-Erweiterung](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)


## <a name="how-do-you-generate-new-ssh-certificates"></a>Wie werden neue SSH-Zertifikate generiert?

Die Generierung von Zertifikaten wird im Artikel [Abrufen des Shared Access Signature-URI für Ihr VM-Image](./cpp-get-sas-uri.md) im Abschnitt [Create technical assets for a virtual machine offer](./cpp-create-technical-assets.md) (Erstellen von technischen Ressourcen für ein VM-Angebot) beschrieben.


## <a name="how-do-you-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Wie wird ein virtuelles privates Netzwerk (VPN) für VMs konfiguriert?

Wenn Sie das Azure Resource Manager-Bereitstellungsmodell verwenden, verfügen Sie über drei gängige Optionen zum Einrichten eines VPN:
- [Erstellen eines routenbasierten VPN-Gateways mit dem Azure-Portal](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
- [Erstellen eines routenbasierten VPN-Gateways mit PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
- [Erstellen eines routenbasierten VPN-Gateways mithilfe von CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)


## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Was sind Microsoft-Supportrichtlinien für die Ausführung von Microsoft-Serversoftware auf Azure-basierten VMs?

Diese Supportrichtlinien werden im Artikel [Microsoft-Server-Software-Support für virtuelle Maschinen von Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) ausführlich beschrieben.


## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>Werden virtuellen Computern eindeutige Bezeichner zugeordnet?

Ja. Dies ist der Fall, wenn sie unter Azure gehostet werden.  Azure weist einen eindeutigen Bezeichner, der als eindeutige ID des virtuellen Azure-Computers bezeichnet wird, für jede neu erstellte VM-Ressource zu.  Weitere Informationen finden Sie im Blogbeitrag [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) (Eindeutige ID für virtuelle Azure-Computer).  Sie können diesen Bezeichner auch programmgesteuert über die [List-API](https://docs.microsoft.com/rest/api/compute/virtualmachines/list) abrufen.


## <a name="in-a-vm-how-do-you-manage-the-custom-script-extension-in-the-startup-task"></a>Wie wird auf einem virtuellen Computer die benutzerdefinierte Skripterweiterung in der Startaufgabe verwaltet?

Im folgenden Artikel erfahren Sie, wie Sie die benutzerdefinierte Skripterweiterung über das Azure PowerShell-Modul ausführen. Sie erfahren, wie Sie eine Azure Resource Manager-Vorlage verwenden, und erhalten Informationen zu Problembehandlungsschritten für Windows-Systeme: [Benutzerdefinierte Skripterweiterung für Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)


## <a name="are-32-bit-applications-or-services-supported-in-the-azure-marketplace"></a>Werden 32-Bit-Anwendungen oder -Dienste für den Azure Marketplace unterstützt?

Im Allgemeinen nicht.  Bei allen unterstützten Betriebssystemen und Standarddiensten für Azure-VMs handelt es sich um 64-Bit-Versionen.  Aus technischer Sicht unterstützen die meisten 64-Bit-Betriebssysteme aufgrund der Abwärtskompatibilität aber 32-Bit-Versionen von Anwendungen.  Die Nutzung von 32-Bit-Anwendungen im Rahmen Ihrer VM-Lösung wird aber nicht unterstützt, und aus diesem Grund *raten wir hiervon dringend ab*.  Kompilieren Sie Ihre Anwendung stattdessen als 64-Bit-Projekt neu.

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Running 32-bit applications](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications) (Ausführen von 32-Bit-Anwendungen)
- [Unterstützung für 32-Bit-Betriebssysteme auf virtuellen Azure-Computern](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft-Server-Software-Support für virtuelle Maschinen von Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)


## <a name="every-time-i-try-to-create-an-image-from-my-vhds-i-get-the-error-vhd-is-already-registered-with-image-repository-as-the-resource-in-powershell-i-did-not-create-any-image-before-nor-did-i-find-any-image-with-this-name-in-azure-how-do-i-resolve-this-issue"></a>Wenn ich versuche, ein Image aus meinen VHDs zu erstellen, erhalte ich in PowerShell den Fehler `.VHD is already registered with image repository as the resource`. Ich habe vorher weder ein Image erstellt noch habe ich ein Image mit diesem Namen in Azure gefunden. Wie kann ich dieses Problem lösen?

Dieses Problem tritt normalerweise auf, wenn der Benutzer einen virtuellen Computer von einer virtuellen Festplatte bereitgestellt hat, die über eine Sperre verfügt.  Stellen Sie sicher, dass über diese virtuelle Festplatte kein virtueller Computer zugeordnet wird, und wiederholen Sie anschließend den Vorgang.  Wenn das Problem weiterhin besteht, können Sie ein Supportticket erstellen, wie unter [Erhalten von Support für das Cloud-Partnerportal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-support-for-cloud-partner-portal) beschrieben. 

