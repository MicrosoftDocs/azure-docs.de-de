---
title: Probleme mit Konnektivität und Netzwerken in Microsoft Azure Cloud Services – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Dieser Artikel behandelt häufig gestellte Fragen zu Konnektivität und Netzwerkproblemen in Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a46879a6882e6d45e4a7ccce59e4a02feea9005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61432958"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Probleme mit Konnektivität und Netzwerken in Microsoft Azure Cloud Services: Häufig gestellte Fragen (FAQs)

Dieser Artikel enthält häufig gestellte Fragen zu Konnektivität und Netzwerkproblemen in [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informationen zur Größe finden sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Ich kann keine IP in einem Clouddienst mit mehreren VIPs reservieren.
Stellen Sie zunächst sicher, dass die Instanz des virtuellen Computers, für den Sie die IP reservieren möchten, eingeschaltet ist. Vergewissern Sie sich anschließend, dass Sie reservierte IPs für Staging- und Produktionsbereitstellungen verwenden. *nicht* , während ein Upgrade der Bereitstellung durchgeführt wird.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Wie verwende ich Remotedesktop bei einer NSG?
Fügen Sie der NSG Regeln hinzu, die Datenverkehr an den Ports **3389** und **20000** zulassen. Remotedesktop verwendet Port **3389**. Für Clouddienstinstanzen erfolgt ein Lastenausgleich, daher können Sie nicht direkt steuern, mit welcher Instanz eine Verbindung hergestellt wird. Die Agents *RemoteForwarder* und *RemoteAccess* verwalten den RDP-Datenverkehr (Remote Desktop Protocol) und ermöglichen dem Client, ein RDP-Cookie zu senden und eine bestimmte Instanz anzugeben, mit der eine Verbindung hergestellt werden soll. Die Agents *RemoteForwarder* und *RemoteAccess* fordern, dass Port **20000** geöffnet wird, der bei Vorhandensein einer NSG möglicherweise blockiert ist.

## <a name="can-i-ping-a-cloud-service"></a>Kann ich meinen Clouddienst mit „Ping“ abfragen?

Nein, nicht mithilfe des normalen „ping“/ICMP-Protokolls. Das ICMP-Protokoll ist durch Azure Load Balancer nicht zulässig.

Wir empfehlen, dass Sie die Konnektivität mithilfe eines Ping-Signals an einen Port testen. Während „ping.exe“ ICMP verwendet, ermöglichen andere Tools, z.B. PSPing, Nmap und telnet, die Konnektivität zu einem bestimmten TCP-Port zu testen.

Weitere Informationen finden Sie unter [Use port pings instead of ICMP to test Azure VM connectivity (Verwenden eines Ping-Signals an einen Port statt ICMP zum Testen von Azure-VM-Konnektivität)](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Wie verhindere ich, Tausende von Treffern von unbekannten IP-Adressen zu erhalten, die einen böswilligen Angriff auf den Clouddienst andeuten könnten?
Azure implementiert mehrstufige Netzwerksicherheit zum Schutz von Platform-Diensten gegen verteilte Denial-of-Services-Angriffe (DDoS). Das Azure DDoS-Verteidigungssystem ist Teil des ständigen Überwachungsprozesses von Azure, der ständig per Penetrationstests verbessert wird. Dieses DDoS-Verteidigungssystem ist darauf ausgelegt, nicht nur Angriffen von außerhalb, sondern auch von anderen Azure-Mandanten standzuhalten. Weitere Informationen finden Sie unter [Azure-Netzwerksicherheit](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Sie können auch Starttasks erstellen, um einige bestimmte IP-Adressen selektiv zu blockieren. Weitere Informationen finden Sie unter [Blockieren bestimmter IP-Adressen](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>Wenn ich versuche, eine RDP-Verbindung mit meiner Cloud Service-Instanz herzustellen, erhalte ich die Meldung „Dieses Benutzerkonto ist abgelaufen.“
Sie erhalten die Fehlermeldung „Dieses Benutzerkonto ist abgelaufen“ möglicherweise, wenn Sie das Ablaufdatum umgehen, das in Ihren RDP-Einstellungen festgelegt ist. Sie können das Ablaufdatum mithilfe der folgenden Schritte im Portal ändern:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, navigieren Sie zu Ihrem Clouddienst, und wählen Sie die Registerkarte **Remotedesktop** aus.

2. Wählen Sie den Bereitstellungsslot **Produktion** oder **Staging** aus.

3. Ändern Sie das Datum bei **Gültig bis**, und speichern Sie die Konfiguration anschließend.

Jetzt sollten Sie eine RDP-Verbindung zu Ihrem Computer herstellen können.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Warum gleicht Azure Load Balancer Datenverkehr nicht gleichmäßig aus?
Weitere Informationen zur Funktionsweise von internen Lastenausgleichen finden Sie unter [Azure Load Balancer-Neue Verteilung](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Standardmäßig wird ein 5-Tupel-Hash-Verteilungsalgorithmus (Quell-IP, Quellport, IP-Zieladresse, Zielport, Protokolltyp) zum Zuordnen des Datenverkehrs an verfügbare Server verwendet. Dabei wird Bindung nur in einer Transportsitzung angeboten. Pakete in derselben TCP- oder UDP-Sitzung werden an die gleiche Instanz der Rechenzentrums-IP (DIP) hinter dem Lastenausgleichs-Endpunkt geleitet. Wenn der Client die Verbindung schließt und erneut öffnet oder eine neue Sitzung über die gleiche Quell-IP startet, wird der Quellport geändert, sodass der Datenverkehr an einen anderen DIP-Endpunkt geleitet wird.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Wie kann ich den bei meiner Standard-URL des Clouddiensts eingehenden Datenverkehr zu einer benutzerdefinierten URL umleiten?

Das URL-Rewrite-Modul von IIS kann zum Umleiten von Datenverkehr, der bei der Standard-URL für den Clouddienst (z.B. \*. cloudapp.net) eingeht, zu einem benutzerdefinierten Namen/einer benutzerdefinierten URL verwendet werden. Da das URL-Rewrite-Modul standardmäßig auf Webrollen aktiviert ist und seine Regeln in der „web.config“ der Anwendung konfiguriert sind, ist es auf dem virtuellen Computer unabhängig von Neustarts/Durchführungen von Reimages immer verfügbar. Weitere Informationen finden Sie unter:

- [Erstellen von Neuschreibungsregeln für das URL-Rewrite-Modul](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Remove a default link (Entfernen eines Standardlinks)](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Wie kann ich den bei der Standard-URL des Clouddiensts eingehenden Datenverkehr blockieren/deaktivieren?

Sie können verhindern, dass Datenverkehr bei der Standard-URL/Name des Clouddiensts (z.B. \*. cloudapp.net) eingeht. Legen Sie für den Hostheader in der Clouddienst-Definitionsdatei (*.csdef) unter der Websitebindungskonfiguration wie folgt einen benutzerdefinierten DNS-Namen (z.B. www.MyCloudService.com) fest:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Da diese Hostheaderbindung über die CSDEF-Datei erzwungen wird, ist kann nur über den benutzerdefinierten Namen „www.MyCloudService.com“ auf den Dienst zugegriffen werden. Bei allen bei der Domäne „*.cloudapp.net“ eingehenden Anforderungen tritt ein Fehler auf. Wenn Sie einen benutzerdefinierten SLB-Test oder einen internen Lastenausgleich im Dienst verwenden, kann ein Konflikt zwischen dem Blockieren der Standard-URL/des Standardnamens des Diensts und dem Testverhalten auftreten.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Wie kann ich sicherstellen, dass die öffentliche IP-Adresse eines Clouddiensts sich nie ändert?

Damit die öffentliche IP-Adresse des Clouddiensts (auch bekannt als VIP-Adresse) sich nie ändert, sodass sie üblicherweise in der Zulassungsliste bestimmter Clients enthalten sein kann, sollten Sie ihr eine reservierte IP-Adresse zuordnen. Andernfalls wird die Zuordnung der von Azure bereitgestellten virtuellen IP-Adresse zu Ihrem Abonnement aufgehoben, wenn Sie die Bereitstellung löschen. Für einen erfolgreichen VIP-Swapvorgang benötigen Sie einzelne reservierte IP-Adressen für Produktions- und Stagingslots. Ohne sie tritt beim Swapvorgang ein Fehler auf. Wie Sie eine IP-Adresse reservieren und Ihren Clouddiensten zuordnen, erfahren Sie in diesen Artikeln:

- [Reservieren der IP-Adresse eines vorhandenen Clouddiensts](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [Zuordnen einer reservierten IP zu einem Clouddienst mit einer Dienstkonfigurationsdatei](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Wenn Sie über mehrere Instanzen für Ihre Rollen verfügen, sollte die Zuordnung von RIP zu Ihrem Clouddienst keine Downtime verursachen. Alternativ können Sie den IP-Adressbereich Ihres Azure-Rechenzentrums der Whitelist hinzufügen. Sie finden alle Azure-IP-Adressbereiche im [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Diese Datei enthält die IP-Adressbereiche (einschließlich Compute-, SQL- und Speicherbereiche), die in Azure-Rechenzentren verwendet werden. Eine aktualisierte Datei mit den derzeit bereitgestellten Bereichen und allen anstehenden Änderungen an den IP-Adressbereichen wird wöchentlich veröffentlicht. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet. Laden Sie die neue XML-Datei jede Woche herunter, und nehmen Sie die erforderlichen Änderungen an Ihrer Website vor, um in Azure ausgeführte Dienste ordnungsgemäß zu ermitteln. Diese Datei zum Aktualisieren der BGP-Ankündigung von Azure-Bereichen wird Azure ExpressRoute-Benutzern jeweils in der ersten Woche des Monats angezeigt.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Wie kann ich virtuelle Azure Resource Manager-Netzwerke mit Clouddiensten verwenden?

Clouddienste können nicht in virtuelle Azure Resource Manager-Netzwerke platziert werden. Virtuelle Resource Manager-Netzwerke und virtuelle Netzwerke in klassischen Bereitstellungen können über Peering verbunden werden. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Wie erhalte ich die Liste der öffentlichen IP-Adressen, die von meinen Cloud Services genutzt werden?

Sie können das folgende PS-Skript verwenden, um die Liste der öffentlichen IP-Adressen für Cloud Services unter Ihrem Konto abzurufen.

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
