---
title: Systemanforderungen für Microsoft Azure Data Box Gateway | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie mehr über die Software- und Netzwerkanforderungen für Azure Data Box Gateway.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 05/08/2019
ms.author: alkohli
ms.openlocfilehash: aadaedfd2c9ecf544d142e42a0fbeb410324b7d8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581362"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Systemanforderungen für Azure Data Box Gateway

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box Gateway-Lösung und die Clients beschrieben, die mit Azure Data Box Gateway verbunden sind. Sie sollten die Informationen sorgfältig lesen, bevor Sie Data Box Gateway bereitstellen. Auch später sollten Sie während der Bereitstellung und beim nachfolgenden Betrieb bei Bedarf als Referenz darauf zurückgreifen. 

Für das virtuelle Data Box Gateway-Gerät gelten die folgenden Systemanforderungen:

- **Softwareanforderungen für Hosts**: Beschreibt die unterstützten Plattformen, Browser für die lokale Webbenutzeroberfläche, SMB-Clients und alle zusätzlichen Anforderungen an Hosts, die sich mit dem Gerät verbinden.
- **Netzwerkanforderungen für das Gerät**: Liefert Informationen zu den Netzwerkanforderungen für den Betrieb des Geräts.


## <a name="specifications-for-the-virtual-device"></a>Spezifikationen für das virtuelle Gerät

Das dem Data Box Gateway zugrunde liegende Hostsystem ist in der Lage, die folgenden Ressourcen für die Bereitstellung Ihres virtuellen Geräts zuzuordnen:

| Spezifikationen                                          | BESCHREIBUNG              |
|---------------------------------------------------------|--------------------------|
| Virtuelle Prozessoren (Kerne)   | Mindestens 4 |
| Arbeitsspeicher  | Mindestens 8 GB|
| Verfügbarkeit|Einzelner Knoten|
| Datenträger| Betriebssystemdatenträger: 250 GB <br> Datenträger für Daten: Mindestens 2 TB, für schlanke Speicherzuweisung geeignet, muss durch SSD-Datenträger unterstützt werden|
| Netzwerkschnittstellen|Mindestens eine virtuelle Netzwerkschnittstelle|


## <a name="supported-os-for-clients-connected-to-device"></a>Unterstütztes Betriebssystem für Clients, die mit dem Gerät verbunden sind

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Unterstützte Protokolle für Clients, die auf das Gerät zugreifen

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>Unterstützte Virtualisierungsplattformen für das Gerät

| **Betriebssystem/-plattform**  |**Versionen**   |**Hinweise**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6,5 <br> 6.7       |VMware-Tools werden nicht unterstützt.         |


## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>Unterstützte Speichertypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Unterstützte Browser für die lokale Webbenutzeroberfläche

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Anforderungen für den Netzwerkport

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das Data Box Gateway-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z.B. ausgehende Verbindungen mit dem Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>URL-Muster für Firewallregeln

Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr Data Box Gateway-Gerät und der Data Box Gateway-Dienst hängen von anderen Microsoft-Anwendungen wie Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Servern ab. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Das bedeutet, dass der Netzwerkadministrator die Firewallregeln für Ihr Data Box Gateway bei Bedarf überwachen und aktualisieren muss.

Es empfiehlt sich, die Firewallregeln für den ausgehenden Verkehr basierend auf den festen IP-Adressen für Data Box Gateway in den meisten Fällen recht locker festzulegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [!NOTE]
> - Die Geräte-IPs (Quell-IPs) sollten immer für alle cloudaktivierten Netzwerkschnittstellen eingerichtet sein.
> - Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/download/confirmation.aspx?id=41653) festgelegt werden.

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>URL-Muster für Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>Internetbandbreite

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>Nächster Schritt

* [Bereitstellen Ihres Azure Data Box-Gateways](data-box-gateway-deploy-prep.md)

