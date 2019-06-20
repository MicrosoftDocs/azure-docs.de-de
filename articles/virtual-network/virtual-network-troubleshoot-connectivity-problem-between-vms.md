---
title: Problembehandlung bei Konnektivitätsproblemen zwischen Azure-VMs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Konnektivitätsprobleme zwischen Azure-VMs beheben.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036302"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Problembehandlung bei Konnektivitätsproblemen zwischen Azure-VMs

Es könnten Konnektivitätsprobleme zwischen virtuellen Azure-Computern (VMs) auftreten. Dieser Artikel enthält Schritte, mit denen Sie dieses Problem beheben können. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptom

Eine Azure-VM kann keine Verbindung mit einer anderen Azure-VM herstellen.

## <a name="troubleshooting-guidance"></a>Anleitungen zur Problembehandlung 

1. [Überprüfen Sie, ob die NIC falsch konfiguriert ist](#step-1-check-whether-nic-is-misconfigured)
2. [Überprüfen Sie, ob Netzwerkdatenverkehr von NSG oder UDR blockiert wird](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Überprüfen Sie, ob Netzwerkdatenverkehr von der VM-Firewall blockiert wird](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Überprüfen Sie, ob VM-App oder -Dienst auf dem Port lauscht](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Überprüfen Sie, ob das Problem durch SNAT verursacht wird](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Überprüfen Sie, ob Datenverkehr von ACLs für die klassische VM blockiert wird](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Überprüfen Sie, ob der Endpunkt für die klassische VM erstellt wird](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Versuchen Sie, eine Verbindung mit einer VM-Netzwerkfreigabe herzustellen](#step-8-try-to-connect-to-a-vm-network-share)
9. [Überprüfen Sie die VNET-übergreifende Konnektivität](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Führen Sie die folgenden Schritte aus, um das Problem zu beheben. Überprüfen Sie jeweils nach Abschluss eines Schritts, ob das Problem behoben ist. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Schritt 1: Überprüfen Sie, ob die NIC falsch konfiguriert ist

Führen Sie die Schritte in [Die Netzwerkschnittstelle für den virtuellen Windows-Computer in Azure zurücksetzen](../virtual-machines/windows/reset-network-interface.md) aus. 

Wenn das Problem nach Änderung der NIC auftritt, führen Sie diese Schritte aus:

**Multi-NIC-VMs**

1. Fügen Sie eine NIC hinzu.
2. Beheben Sie die Probleme in der fehlerhaften NIC oder entfernen Sie die fehlerhafte NIC.  Fügen Sie die NIC dann erneut hinzu.

Weitere Informationen finden Sie unter [Virtuelle Computer: Hinzufügen oder Entfernen von Netzwerkschnittstellenkarten](virtual-network-network-interface-vm.md).

**Einzel-NIC-VM** 

- [Erneutes Bereitstellen von virtuellen Windows-Computern](../virtual-machines/windows/redeploy-to-new-node.md)
- [Erneutes Bereitstellen von virtuellen Linux-Computern](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Schritt 2: Überprüfen Sie, ob Netzwerkdatenverkehr von NSG oder UDR blockiert wird

Bestimmen Sie mithilfe von [Einführung in die IP-Datenflussüberprüfung in Azure Network Watcher](../network-watcher/network-watcher-ip-flow-verify-overview.md) und [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-overview.md), ob eine Netzwerksicherheitsgruppe (NSG) oder benutzerdefinierte Route (UDR) mit dem Fluss des Datenverkehrs in Konflikt steht.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Schritt 3: Überprüfen Sie, ob Netzwerkdatenverkehr von der VM-Firewall blockiert wird

Deaktivieren Sie die Firewall, und testen Sie dann das Ergebnis. Wenn das Problem behoben ist, überprüfen Sie die Firewalleinstellungen, und aktivieren Sie die Firewall dann erneut.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Schritt 4: Überprüfen Sie, ob die VM-App oder der VM-Dienst an dem Port lauscht

Sie können eine der folgenden Methoden verwenden, um zu überprüfen, ob die VM-App oder der VM-Dienst auf dem Port lauscht.

- Führen Sie die folgenden Befehle aus, um zu überprüfen, ob der Server auf diesem Port lauscht.

**Windows-VM**

    netstat –ano

**Linux-VM**

    netstat -l

- Führen Sie den **telnet**-Befehl auf dem virtuellen Computer selbst aus, um den Port zu testen. Wenn bei dem Test ein Fehler auftritt, ist die Anwendung oder der Dienst nicht zum Lauschen auf diesem Port konfiguriert.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Schritt 5: Überprüfen Sie, ob das Problem durch SNAT verursacht wird

In einigen Szenarien wird der virtuelle Computer hinter einer Lastenausgleichslösung eingefügt, die eine Abhängigkeit von Ressourcen aufweist, die außerhalb von Azure liegen. Wenn in diesen Szenarien zeitweilige Verbindungsprobleme auftreten, könnte [SNAT-Portauslastung](../load-balancer/load-balancer-outbound-connections.md) die Ursache des Problems sein. Um das Problem zu beheben, erstellen Sie eine VIP-Adresse (oder ILPIP-Adresse für klassische VMs) für jeden virtuellen Computer, der sich hinter dem Lastenausgleich befindet und mit NSG oder ACL gesichert ist. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Schritt 6: Überprüfen Sie, ob Datenverkehr von ACLs für die klassische VM blockiert wird

Mit einer Zugriffssteuerungsliste (Access Control List, ACL) können Sie selektiv Datenverkehr für einen Endpunkt eines virtuellen Computers zulassen oder verweigern. Weitere Informationen finden Sie unter [Verwaltung der ACL für einen Endpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Schritt 7: Überprüfen Sie, ob der Endpunkt für die klassische VM erstellt wird

Alle VMs, die Sie mit dem klassischen Bereitstellungsmodell in Azure erstellen, können automatisch über einen privaten Netzwerkkanal mit anderen VMs im gleichen Clouddienst oder virtuellen Netzwerk kommunizieren. Computer in anderen virtuellen Netzwerken erfordern jedoch von den Endpunkten, dass sie den eingehenden Netzwerkdatenverkehr auf einen virtuellen Computer leiten. Weitere Informationen finden Sie unter [Gewusst wie: Einrichten von Endpunkten auf einem klassischen virtuellen Windows-Computer in Azure](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Schritt 8: Versuchen Sie, eine Verbindung mit einer VM-Netzwerkfreigabe herzustellen

Wenn Sie keine Verbindung mit einer VM-Netzwerkfreigabe herstellen können, kann die Ursache des Problems sein, dass keine NICs auf dem virtuellen Computer verfügbar sind. Hinweise zum Löschen nicht verfügbarer NICs finden Sie unter [Löschen Sie die nicht verfügbaren NICs](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics).

### <a name="step-9-check-inter-vnet-connectivity"></a>Schritt 9: Überprüfen Sie die VNET-übergreifende Konnektivität

Bestimmen Sie mithilfe von [Einführung in die IP-Datenflussüberprüfung in Azure Network Watcher](../network-watcher/network-watcher-ip-flow-verify-overview.md) und [Einführung in die Datenflussprotokollierung für Netzwerksicherheitsgruppen](../network-watcher/network-watcher-nsg-flow-logging-overview.md), ob eine NSG oder UDR mit dem Fluss des Datenverkehrs in Konflikt steht. Sie können auch [hier](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections) Ihre VNET-übergreifende Konfiguration überprüfen.

### <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Wenden Sie sich an den Support.
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.