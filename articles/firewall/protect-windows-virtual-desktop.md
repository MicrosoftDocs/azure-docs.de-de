---
title: Verwenden von Azure Firewall zum Schutz von Windows Virtual Desktop
description: Informationen zur Verwendung von Azure Firewall zum Schutz von Windows Virtual Desktop-Bereitstellungen
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ad4f42d0e33f6d70c75abfcd1daab4f5aa9a515f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654982"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Verwenden von Azure Firewall zum Schutz von Windows Virtual Desktop-Bereitstellungen

Bei Windows Virtual Desktop handelt es sich um einen Dienst für die Desktop- und App-Virtualisierung, der unter Azure ausgeführt wird. Wenn ein Endbenutzer eine Verbindung mit einer WVD-Umgebung herstellt, wird die Sitzung von einem Hostpool ausgeführt. Ein Hostpool ist eine Sammlung von virtuellen Azure-Computern, die unter WVP als Sitzungshosts registriert werden. Diese virtuellen Computer werden in Ihrem virtuellen Netzwerk ausgeführt und unterliegen dessen Sicherheitskontrollen. Sie benötigen ausgehenden Zugriff auf das Internet, damit der Windows Virtual Desktop-Dienst ordnungsgemäß arbeiten kann, und benötigen möglicherweise zudem ausgehenden Internetzugriff für Endbenutzer. Mithilfe von Azure Firewall können Sie Ihre Umgebung sperren und ausgehenden Datenverkehr filtern.

[ ![Windows Virtual Desktop-Architektur](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Beachten Sie die Richtlinien in diesem Artikel, um mithilfe von Azure Firewall zusätzlichen Schutz für Ihren Windows Virtual Desktop-Hostpool bereitzustellen.

## <a name="prerequisites"></a>Voraussetzungen


 - Eine bereitgestellte Windows Virtual Desktop-Umgebung und ein Hostpool.

   Weitere Informationen finden Sie im [Tutorial: Erstellen eines Hostpools mit dem Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) und [Erstellen eines Hostpools mit einer Azure Resource Manager-Vorlage](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Weitere Informationen zu Windows Virtual Desktop-Umgebungen finden Sie unter [Windows Virtual Desktop-Umgebung](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Ausgehender Zugriff für den Hostpool auf Windows Virtual Desktop

Die virtuellen Azure-Computer, die Sie für Windows Virtual Desktop erstellen, müssen Zugriff auf mehrere vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) haben, um ordnungsgemäß funktionieren zu können. Azure Firewall bietet ein FQDN-Tag für WVD, um diese Konfiguration zu vereinfachen. Führen Sie die folgenden Schritte aus, um ausgehenden Plattformdatenverkehr für Windows Virtual Desktop zuzulassen:

- Stellen Sie Azure Firewall bereit, und konfigurieren Sie die benutzerdefinierte Route (User Defined Route, UDR) für das Subnetz des Windows Virtual Desktop-Hostpools, um den gesamten Datenverkehr über Azure Firewall zu leiten. Die Standardroute zeigt nun auf die Firewall.
- Erstellen Sie eine Sammlung für Anwendungsregeln, und fügen Sie eine Regel hinzu, um das FQDN-Tag *WindowsVirtualDesktop* zu aktivieren. Der Quell-IP-Adressbereich ist das virtuelle Netzwerk des Hostpools, das Protokoll ist **https**, und das Ziel ist **WindowsVirtualDesktop**.

- Aufgrund des erforderlichen Speichers und der erforderlichen Service Bus-Instanzen muss Ihr Windows Virtual Desktop-Hostpool bereitstellungsspezifisch sein. Deshalb ist er noch nicht im FQDN-Tag „WindowsVirtualDesktop“ enthalten. Sie haben die folgenden Möglichkeiten, um dies zu ändern:

   - Lassen Sie HTTPS-Zugriff aus Ihrem Hostpoolsubnetz auf *xt.blob.core.windows.net, *eh.servicebus.windows.net und *xt.table.core.windows.net zu. Diese Platzhalter-FQDNs ermöglichen den erforderlichen Zugriff, sind aber weniger restriktiv.
   - Verwenden Sie die folgende Log Analytics-Abfrage, um die erforderlichen FQDNs aufzulisten, und lassen Sie sie dann explizit in den Firewallanwendungsregeln zu:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Erstellen Sie eine Netzwerkregelsammlung mit den folgenden Regeln:

   - DNS zulassen: Lassen Sie Datenverkehr von Ihrer privaten ADDS-IP-Adresse auf * für den TCP- und den UDP-Port 53 zu.
   - KMS zulassen: Lassen Sie Datenverkehr von Ihren virtuellen Windows Virtual Desktop-Computern an den TCP-Port 1688 für Windows Activation Service zu. Weitere Informationen zu den Ziel-IP-Adressen finden Sie unter [Fehler bei der Windows-Aktivierung in einem Szenario mit Tunnelerzwingung](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Für einige Bereitstellungen sind möglicherweise keine DNS-Regeln erforderlich, z. B. leiten Azure Active Directory-Domänencontroller DNS-Abfragen unter 168.63.129.16 an Azure DNS weiter.

## <a name="host-pool-outbound-access-to-the-internet"></a>Ausgehender Zugriff für Hostpools auf das Internet

Je nach Anforderungen Ihrer Organisation sollten Sie sicheren ausgehenden Zugriff auf das Internet für Ihre Endbenutzer aktivieren. In Fällen, in denen die Liste der zulässigen Ziele klar definiert ist (z. B. beim [Zugriff auf Microsoft 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)), können Sie die Anwendungs- und Netzwerkregeln für Azure Firewall verwenden, um den erforderlichen Zugriff zu konfigurieren. Dadurch wird der Endbenutzerdatenverkehr direkt an das Internet geleitet, um eine optimale Leistung zu erzielen.

Wenn Sie den ausgehenden Internetdatenverkehr von Benutzern mithilfe eines vorhandenen lokalen, sicheren Webgateways filtern möchten, können Sie Webbrowser oder andere Anwendungen, die auf dem Windows Virtual Desktop-Hostpool ausgeführt werden, mithilfe einer expliziten Proxykonfiguration konfigurieren. Weitere Informationen hierzu finden Sie beispielsweise unter [So verwenden Sie Microsoft Edge-Befehlszeilenoptionen zum Konfigurieren von Proxyeinstellungen](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Diese Proxyeinstellungen beeinflussen nur den Internetzugriff für Endbenutzer, wodurch der ausgehende Datenverkehr der Windows Virtual Desktop-Plattform direkt über Azure Firewall ermöglicht wird.

## <a name="additional-considerations"></a>Weitere Überlegungen

Je nach Anforderungen müssen Sie möglicherweise zusätzliche Firewallregeln konfigurieren:

- NTP-Serverzugriff

   Standardmäßig wird für virtuelle Computer unter Windows über den UDP-Port 123 zur Zeitsynchronisierung eine Verbindung mit time.windows.com hergestellt. Erstellen Sie eine Netzwerkregel, um diesen Zugriff zuzulassen, oder erstellen Sie eine solche Regel für einen Zeitserver, den Sie in Ihrer Umgebung verwenden.


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Windows Virtual Desktop: [Was ist Windows Virtual Desktop?](../virtual-desktop/overview.md)