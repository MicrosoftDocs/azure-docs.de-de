---
title: Unterstützung von Azure IoT Device Provisioning Service (DPS) für virtuelle Netzwerke
description: Verwenden des Verbindungsmusters für virtuelle Netzwerke mit Azure IoT Device Provisioning Service (DPS)
services: iot-hub
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: wesmc
ms.openlocfilehash: f1409a931195d236b2729e629e4603c606137593
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959780"
---
# <a name="azure-iot-hub-device-provisioning-service-dps-support-for-virtual-networks"></a>Unterstützung von Azure IoT Hub Device Provisioning Service (DPS) für virtuelle Netzwerke

In diesem Artikel wird das Verbindungsmuster für virtuelle Netzwerke (VNET) für die Bereitstellung von IoT-Geräten mit IoT-Hubs mithilfe von DPS vorgestellt. Dieses Muster bietet private Konnektivität zwischen den Geräten, DPS und dem IoT-Hub in einem kundeneigenen Azure-VNET. 

In den meisten Szenarien, in denen DPS mit einem VNET konfiguriert ist, wird Ihr IoT-Hub auch im selben VNET konfiguriert. Genauere Informationen zur VNET-Unterstützung und -Konfiguration für IoT-Hubs finden Sie unter [Unterstützung von virtuellen IoT-Hub-Netzwerken](../iot-hub/virtual-network-support.md).



## <a name="introduction"></a>Einführung

Standardmäßig werden DPS-Hostnamen einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche Endpunkt ist für alle Kunden sichtbar. Der Zugriff auf den öffentlichen Endpunkt kann von IoT-Geräten über WANs (Wide Area Networks) sowie lokale Netzwerke versucht werden.

Aus verschiedenen Gründen möchten Kunden möglicherweise die Verbindung mit Azure-Ressourcen wie DPS einschränken. Zu den Gründen gehören die folgenden:

* Verhindern, dass die Verbindung über das öffentliche Internet offengelegt wird. Die Offenlegung kann reduziert werden, indem durch Isolation auf Netzwerkebene zusätzliche Sicherheitsschichten für Ihren IoT-Hub und Ihre DPS-Ressourcen eingeführt werden.

* Aktivieren einer privaten Konnektivitätsumgebung von Ihren lokalen Netzwerkressourcen, um sicherzustellen, dass Ihre Daten und der Datenverkehr direkt an das Azure-Backbone-Netzwerk übertragen wird

* Verhindern der Exfiltration von Angriffen von vertraulichen, lokalen Netzwerken 

* Befolgen bewährter Azure-Konnektivitätsmuster mithilfe [privater Endpunkte](../private-link/private-endpoint-overview.md)

Zu den gängigen Ansätzen zum Einschränken der Konnektivität gehören [DPS-IP-Filterregeln](./iot-dps-ip-filtering.md) und virtuelle Netzwerke (VNET) mit [privaten Endpunkten](../private-link/private-endpoint-overview.md). In diesem Artikel wird der VNET-Ansatz für DPS mit privaten Endpunkten beschrieben. 

Geräte, die in lokalen Netzwerken betrieben werden, können privates Peering mit [virtuellen privaten Netzwerken (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](https://azure.microsoft.com/services/expressroute/) verwenden, um eine Verbindung mit einem VNET in Azure herzustellen und über private Endpunkte auf DPS-Ressourcen zuzugreifen. 

Ein privater Endpunkt ist eine private IP-Adresse, die in einem kundeneigenen VNET zugeordnet ist, über das eine Azure-Ressource zugänglich ist. Wenn Sie über einen privaten Endpunkt für Ihre DPS-Ressource verfügen, können Sie zulassen, dass in Ihrem VNET ausgeführte Geräte die Bereitstellung durch Ihre DPS-Ressource anfordern, ohne Datenverkehr an den öffentlichen Endpunkt zu erlauben.


## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie fortfahren:

* Ihre DPS-Ressource wurde bereits erstellt und mit Ihren IoT-Hubs verknüpft. Anleitungen zum Einrichten einer neuen DPS-Ressource finden Sie unter [Einrichten von IoT Hub Device Provisioning Service über das Azure-Portal](./quick-setup-auto-provision.md).

* Sie haben ein Azure-VNET mit einem Subnetz bereitgestellt, in dem der private Endpunkt erstellt wird. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks mithilfe der Azure-Befehlszeilenschnittstelle](../virtual-network/quick-create-cli.md).

* Richten Sie in Ihrem Azure-VNET für Geräte, die in lokalen Netzwerken ausgeführt werden, ein [virtuelles privates Netzwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder das private [ExpressRoute-Peering](https://azure.microsoft.com/services/expressroute/) ein.

## <a name="private-endpoint-limitations"></a>Einschränkungen privater Endpunkte

Beachten Sie die folgenden aktuellen Einschränkungen für DPS bei der Verwendung privater Endpunkte:

* Private Endpunkte funktionieren nicht mit DPS, wenn sich die DPS-Ressource und der verknüpfte Hub in unterschiedlichen Clouds befinden, z. B. [Azure Government und globales Azure](../azure-government/documentation-government-welcome.md).

* Derzeit funktionieren [benutzerdefinierte Zuordnungsrichtlinien mit Azure Functions](how-to-use-custom-allocation-policies.md) für DPS nicht, wenn die Azure-Funktion an ein VNET und private Endpunkte gebunden ist. 

* Die aktuelle VNET-Unterstützung von DPS betrifft nur eingehenden Datenverkehr in DPS. Ausgehender Datenverkehr, also von DPS zu IoT Hub, verwendet einen internen Dienst-zu-Dienst-Mechanismus anstelle eines dedizierten VNET. Eine Unterstützung für die vollständige VNET-basierte Sperrung von ausgehendem Datenverkehr zwischen DPS und IoT Hub ist zurzeit nicht verfügbar.

* Über die Richtlinie mit der niedrigsten Latenzzuordnung wird dem IoT-Hub mit der niedrigsten Latenz ein Gerät zugewiesen. Diese Zuordnungsrichtlinie ist in einer virtuellen Netzwerkumgebung nicht zuverlässig. 

## <a name="set-up-a-private-endpoint"></a>Einrichten eines privaten Endpunkts

Führen Sie die folgenden Schritte aus, um einen privaten Endpunkt einzurichten:

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com/) Ihre DPS-Ressource, und klicken Sie auf die Registerkarte **Netzwerk**. Klicken Sie auf **Private Endpunktverbindungen** und **+ Privater Endpunkt**.

    ![Hinzufügen eines neuen privaten Endpunkts für DPS](./media/virtual-network-support/networking-tab-add-private-endpoint.png)

2. Geben Sie auf der Seite „Grundlagen“ von _Privaten Endpunkt erstellen_ die in der nachstehenden Tabelle genannten Informationen ein.

    ![Erstellen des privaten Endpunkts: Grundlagen](./media/virtual-network-support/create-private-endpoint-basics.png)

    | Feld | Wert |
    | :---- | :-----|
    | **Abonnement** | Wählen Sie das gewünschte Azure-Abonnement aus, das den privaten Endpunkt enthalten soll.  |
    | **Ressourcengruppe** | Wählen Sie eine Ressourcengruppe für den privaten Endpunkt aus, oder erstellen Sie eine. |
    | **Name**       | Geben Sie einen Namen für den privaten Endpunkt ein. |
    | **Region**     | Es muss die Region ausgewählt werden, die das VNET enthält, aber es muss sich nicht um dieselbe DPS-Ressource handeln. |

    Klicken Sie unten auf der Seite auf **Weiter: Ressource**, um die Ressource zu konfigurieren, auf die der private Endpunkt verweisen soll.

3. Geben Sie auf der Seite „Ressource“ von _Privaten Endpunkt erstellen_ die in der nachstehenden Tabelle genannten Informationen ein.

    ![Erstellen des privaten Endpunkts: Ressource](./media/virtual-network-support/create-private-endpoint-resource.png)

    | Feld | Wert |
    | :---- | :-----|
    | **Abonnement**        | Wählen Sie das Azure-Abonnement mit der DPS-Ressource aus, auf die der private Endpunkt verweisen soll.  |
    | **Ressourcentyp**       | Wählen Sie **Microsoft.Devices/ProvisioningServices** aus. |
    | **Ressource**            | Wählen Sie die DPS-Ressource aus, der der private Endpunkt zugeordnet werden soll. |
    | **Zielunterressource** | Wählen Sie **iotDps** aus. |

    > [!TIP]
    > Informationen zur Einstellung **Stellen Sie über eine Ressourcen-ID oder einen Alias eine Verbindung mit einer Azure-Ressource her** finden Sie im Abschnitt [Anfordern eines privaten Endpunkts](#request-a-private-endpoint) in diesem Artikel.


    Klicken Sie unten auf der Seite auf **Weiter: Konfiguration**, um das VNET für den privaten Endpunkt zu konfigurieren.

4. Wählen Sie auf der Seite „Konfiguration“ von _Privaten Endpunkt erstellen_ Ihr virtuelles Netzwerk und das Subnetz aus, in dem der private Endpunkt erstellt werden soll.
 
    Klicken Sie unten auf der Seite auf **Weiter: Tags**, und stellen Sie optional Tags für Ihre Ressource bereit.

    ![Konfigurieren des privaten Endpunkts](./media/virtual-network-support/create-private-endpoint-configuration.png)

6. Klicken Sie auf **Überprüfen und erstellen** und dann auf **Erstellen**, um die Ressource für den privaten Endpunkt zu erstellen.


## <a name="request-a-private-endpoint"></a>Anfordern eines privaten Endpunkts

Sie können einen privaten Endpunkt für eine DPS-Ressource anhand der Ressourcen-ID anfordern. Um diese Anforderung zu erstellen, müssen Sie die Ressourcen-ID beim Ressourcenbesitzer anfordern. 

1. Die Ressourcen-ID wird auf der Registerkarte „Eigenschaften“ für die DPS-Ressource bereitgestellt, wie unten gezeigt.

    ![Registerkarte „Eigenschaften“ von DPS](./media/virtual-network-support/dps-properties.png)

    > [!CAUTION]
    > Beachten Sie, dass die Ressourcen-ID die Abonnement-ID enthält. 

2. Wenn Sie über die Ressourcen-ID verfügen, führen Sie die oben genannten Schritte unter [Einrichten eines privaten Endpunkts](#set-up-a-private-endpoint) bis Schritt 3 auf der Seite „Ressourcen“ von _Privaten Endpunkt erstellen_ aus. Klicken Sie auf **Stellen Sie über eine Ressourcen-ID oder einen Alias eine Verbindung mit einer Azure-Ressource her**, und geben Sie die Informationen in der nachstehenden Tabelle ein. 

    | Feld | Wert |
    | :---- | :-----|
    | **Ressourcen-ID oder Alias** | Geben Sie die Ressourcen-ID für die DPS-Ressource ein. |
    | **Zielunterressource** | Enter **iotDps** |
    | **Anforderungsnachricht** | Geben Sie eine Anforderungsnachricht für den Besitzer der DPS-Ressource ein.<br>Beispiel: <br>`Please approve this new private endpoint`<br>`for IoT devices in site 23 to access this DPS instance`  |

    Klicken Sie unten auf der Seite auf **Weiter: Konfiguration**, um das VNET für den privaten Endpunkt zu konfigurieren.

3. Wählen Sie auf der Seite „Konfiguration“ von _Privaten Endpunkt erstellen_ das virtuelle Netzwerk und das Subnetz aus, in dem der private Endpunkt erstellt werden soll.
 
    Klicken Sie unten auf der Seite auf **Weiter: Tags**, und stellen Sie optional Tags für Ihre Ressource bereit.

4. Klicken Sie auf **Überprüfen und erstellen** und dann auf **Erstellen**, um die Anforderung für den privaten Endpunkt zu erstellen.

5. Der DPS-Besitzer kann die Anforderung des privaten Endpunkts in der Liste **Verbindungen mit privatem Endpunkt** auf der Registerkarte „Netzwerk“ von DPS einsehen. Auf dieser Seite kann der Besitzer die Anforderung des privaten Endpunkts **Genehmigen** oder **Ablehnen**, wie unten gezeigt.

    ![DPS-Genehmigung](./media/virtual-network-support/approve-dps-private-endpoint.png)


## <a name="pricing-private-endpoints"></a>Preise für private Endpunkte

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).



## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die unten angegebenen Links, um weitere Informationen zu den Sicherheitsfeatures von DPS zu erhalten:

* [Security](./concepts-service.md#attestation-mechanism)
* [Unterstützung für TLS 1.2](tls-support.md)