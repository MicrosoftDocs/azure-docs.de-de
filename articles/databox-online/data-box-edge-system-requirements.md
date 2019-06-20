---
title: Systemanforderungen für Microsoft Azure Data Box Edge | Microsoft-Dokumentation
description: Erfahren Sie etwas über die Software- und Netzwerkanforderungen für Ihr Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: 90c60d586d505ca0c9bd787c37e137f7a38ee1f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60756544"
---
# <a name="azure-data-box-edge-system-requirements"></a>Systemanforderungen für Azure Data Box Edge

In diesem Artikel werden wichtige Systemanforderungen für Ihre Microsoft Azure Data Box Edge-Lösung und die Clients beschrieben, die mit Azure Data Box Edge verbunden sind. Es wird empfohlen, dass Sie die Informationen sorgfältig überprüfen, bevor Sie Data Box Edge bereitstellen. Sie können diese Informationen ggf. während der Bereitstellung und des nachfolgenden Betriebs als Referenz nutzen.

Die Systemanforderungen von Data Box Edge:

- **Softwareanforderungen für Hosts:** beschreibt die unterstützten Plattformen, Browser für die lokale Konfigurationsbenutzeroberfläche, SMB-Clients und alle weiteren Anforderungen an Clients, die auf das Gerät zugreifen.
- **Netzwerkanforderungen für das Gerät:** enthält Informationen zu den Netzwerkanforderungen für den Betrieb des physischen Geräts.

## <a name="supported-os-for-clients-connected-to-device"></a>Unterstütztes Betriebssystem für Clients, die mit dem Gerät verbunden sind

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Unterstützte Protokolle für Clients, die auf das Gerät zugreifen

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Unterstützte Speichertypen

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Unterstützte Browser für die lokale Webbenutzeroberfläche

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Anforderungen für den Netzwerkport

### <a name="port-requirements-for-data-box-edge"></a>Portanforderungen für Data Box Edge

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der das Data Box Edge-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet: z.B. ausgehende Verbindungen mit dem Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Portanforderungen für IoT Edge

Azure IoT Edge ermöglicht die ausgehende Kommunikation von einem lokalen Edgegerät mit der Azure-Cloud über unterstützte IoT Hub-Protokolle. Die eingehende Kommunikation ist nur für bestimmte Szenarien erforderlich, in denen Azure IoT Hub Nachrichten an das Azure IoT Edge-Gerät (z.B. C2D-Nachrichten) pushen muss.

Die folgende Tabelle enthält die Portkonfiguration für Server, die die Azure IoT Edge-Runtime hosten:

| Port-Nr. | ein oder aus | Portbereich | Erforderlich | Anleitungen |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| aus       | WAN        | Ja      | Ausgehend offen für die IoT Edge-Bereitstellung. Diese Konfiguration ist bei Verwendung manueller Skripts oder des Azure IoT Device Provisioning-Diensts (Device Provisioning Service, DPS) erforderlich.|

Ausführliche Informationen finden Sie unter [Firewall- und Portkonfigurationsregeln für die IoT Edge-Bereitstellung](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>URL-Muster für Firewallregeln

Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr Data Box Edge-Gerät und -Dienst hängen von anderen Microsoft-Anwendungen wie Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Servern ab. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Diese Änderungen erfordern, dass der Netzwerkadministrator die Firewallregeln für Ihre Data Box Edge-Instanz ggf. überwachen und aktualisieren muss.

Es empfiehlt sich, die Firewallregeln für den ausgehenden Verkehr basierend auf den festen IP-Adressen für Data Box Edge in den meisten Fällen recht locker festzulegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [!NOTE]
> - Die Geräte-IPs (Quell-IPs) sollten immer für alle cloudaktivierten Netzwerkschnittstellen eingerichtet sein.
> - Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/download/confirmation.aspx?id=41653) festgelegt werden.

### <a name="url-patterns-for-gateway-feature"></a>URL-Muster für Gateway-Feature

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-Muster für Compute-Feature

| URL-Muster                      | Komponente oder Funktion                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft-Containerregistrierung (erforderlich)               |
| https://\*.azurecr.io                     | Persönliche Containerregistrierungen und Containerregistrierungen von Drittanbietern (optional) | 
| https://\*.azure-devices.net              | IoT Hub-Zugriff (erforderlich)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-Muster für das Gateway für Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-Muster für Compute für Azure Government

| URL-Muster                      | Komponente oder Funktion                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft-Containerregistrierung (erforderlich)               |
| https://\*.azure-devices.us              | IoT Hub-Zugriff (erforderlich)           |
| https://\*.azurecr.us                    | Persönliche Containerregistrierungen und Containerregistrierungen von Drittanbietern (optional) | 

## <a name="internet-bandwidth"></a>Internetbandbreite

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Berechnen von Überlegungen zur Größenanpassung

Nutzen Sie Ihre Erfahrung bei der Entwicklung und dem Testen Ihrer Lösung, um sicherzustellen, dass auf Ihrem Data Box Edge-Gerät ausreichend Kapazität vorhanden ist und Sie die optimale Leistung Ihres Geräts erhalten.

Zu den zu berücksichtigenden Faktoren gehören:

- **Containerdetails**: Bedenken Sie Folgendes.

    - Wie viele Container umfasst Ihre Workload? Sie könnten eine Vielzahl von leichten Containern anstelle von einigen ressourcenintensiven Containern verwenden.
    - Welche Ressourcen sind diesen Containern zugeordnet im Vergleich zu den von ihnen verbrauchten Ressourcen?
    - Wie viele Ebenen werden von den Containern gemeinsam genutzt?
    - Gibt es nicht verwendete Container? Ein angehaltener Container belegt weiterhin Speicherplatz.
    - In welcher Sprache sind Ihre Container geschrieben?
- **Umfang der verarbeiteten Daten**: Wie viele Daten werden Ihre Container verarbeiten? Werden diese Daten Speicherplatz verbrauchen oder werden die Daten im Arbeitsspeicher verarbeitet?
- **Erwartete Leistung**: Was sind die gewünschten Leistungsmerkmale Ihrer Lösung? 

Um die Leistung Ihrer Lösung zu verstehen und zu optimieren, können Sie Folgendes verwenden:

- Die im Azure-Portal verfügbaren Computemetriken. Wechseln Sie zu Ihrer Data Box Edge-Ressource und dann zu **Überwachung > Metriken**. Betrachten Sie **Edgecomputing – Arbeitsspeichernutzung** und **Edgecomputing – CPU in Prozent**, um die verfügbaren Ressourcen zu verstehen und wie die Ressourcen verbraucht werden.
- Die über die PowerShell-Schnittstelle des Geräts verfügbaren Überwachungsbefehle wie:

    - `dkr`: Statistiken zum Abrufen eines Livestreams der Ressourcennutzungsstatistiken für Container. Der Befehl unterstützt Metriken zu CPU, Arbeitsspeicherauslastung, Arbeitsspeicherlimit und Netzwerk-E/A.
    - `dkr system df`: zum Abrufen von Informationen bezüglich des belegten Speicherplatzes. 
    - `dkr image [prune]`: zum Löschen nicht verwendeter Images und Freigeben von Speicherplatz.
    - `dkr ps --size`: zum Anzeigen der ungefähren Größe eines aktiven Containers. 

    Weitere Informationen zu den verfügbaren Befehlen finden Sie unter [Überwachung und Problembehandlung von Computemodulen](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Stellen Sie abschließend sicher, dass Sie Ihre Lösung für Ihr Dataset validieren und die Leistung auf Data Box Edge quantifizieren, bevor Sie sie in der Produktionsumgebung bereitstellen.


## <a name="next-step"></a>Nächster Schritt

- [Bereitstellen von Azure Data Box Edge](data-box-edge-deploy-prep.md)
