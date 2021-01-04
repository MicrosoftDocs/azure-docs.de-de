---
title: Azure IoT Hub-Unterstützung für virtuelle Netzwerke
description: Hier erfahren Sie mehr über die Verwendung von Konnektivitätsmustern für virtuelle Netzwerke mit IoT Hub.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: jlian
ms.openlocfilehash: f79b03884109ffbd856ff4f60909565daeb0e792
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549111"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>IoT Hub-Unterstützung für virtuelle Netzwerke mit Private Link und verwalteter Identität

Standardmäßig werden IoT Hub-Hostnamen einem öffentlichen Endpunkt mit einer öffentlich routingfähigen IP-Adresse über das Internet zugeordnet. Dieser öffentliche IoT Hub-Endpunkt wird von mehreren Kunden gemeinsam genutzt, und sämtliche IoT-Geräte in WANs und lokalen Netzwerken können darauf zugreifen.

![Öffentlicher IoT Hub-Endpunkt](./media/virtual-network-support/public-endpoint.png)

IoT Hub-Features wie das [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md), der [Dateiupload](./iot-hub-devguide-file-upload.md) und das [Importieren und Exportieren von Geräten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md) erfordern ebenfalls Konnektivität zwischen IoT Hub und einer kundeneigenen Azure-Ressource über den öffentlichen Endpunkt. Diese Konnektivitätspfade bilden gemeinsam den ausgehenden Datenverkehr von IoT Hub zu Kundenressourcen.

Möglicherweise möchten Sie die Konnektivität mit Ihren Azure-Ressourcen (einschließlich IoT Hub) über ein VNET einschränken, das Sie besitzen und betreiben. Zu den Gründen gehören die folgenden:

* Netzwerkisolation für Ihren IoT-Hub durch Unterbinden der Konnektivität mit dem öffentlichen Internet.

* Aktivieren einer privaten Konnektivitätsumgebung von Ihren lokalen Netzwerkressourcen, um sicherzustellen, dass Ihre Daten und der Datenverkehr direkt an das Azure-Backbone-Netzwerk übertragen wird

* Verhindern der Exfiltration von Angriffen von vertraulichen, lokalen Netzwerken 

* Befolgen bewährter Azure-Konnektivitätsmuster mithilfe [privater Endpunkte](../private-link/private-endpoint-overview.md)

In diesem Artikel wird beschrieben, wie Sie diese Ziele erreichen, indem Sie [Azure Private Link](../private-link/private-link-overview.md) für eingehende Verbindungen mit IoT Hub verwenden und für ausgehende Verbindungen von IoT Hub mit anderen Azure-Ressourcen Ausnahmen für vertrauenswürdige Microsoft-Dienste definieren.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Eingehende Konnektivität mit IoT Hub mithilfe von Azure Private Link

Ein privater Endpunkt ist eine private IP-Adresse, die in einem kundeneigenen VNET zugeordnet ist, über das eine Azure-Ressource erreichbar ist. Mit Azure Private Link können Sie einen privaten Endpunkt für Ihren IoT-Hub einrichten und damit den Diensten in Ihrem VNET erlauben, IoT Hub zu erreichen, ohne dass Datenverkehr an den öffentlichen IoT Hub-Endpunkt gesendet werden muss. Ebenso können Ihre lokalen Geräte ein [virtuelles privates Netzwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Peering verwenden, um eine Verbindung mit Ihrem VNET und Ihrer IoT Hub-Instanz (über den privaten Endpunkt) herzustellen. So können Sie die Konnektivität mit den öffentlichen Endpunkten Ihres IoT-Hubs einschränken oder vollständig blockieren, indem Sie [IoT Hub-IP-Filter](./iot-hub-ip-filtering.md) oder [die Funktion zum Umschalten des Zugriffs über öffentliche Netzwerke](iot-hub-public-network-access.md) verwenden. Auf diese Weise erfolgen Verbindungen mit Ihrem Hub ausschließlich über den privaten Endpunkt für Geräte. Der Schwerpunkt dieses Setups liegt auf Geräten, die sich in einem lokalen Netzwerk befinden. Dieses Setup wird nicht für Geräte empfohlen, die in einem WAN bereitgestellt werden.

![IoT Hub des virtuellen Netzwerks – Eingang](./media/virtual-network-support/virtual-network-ingress.png)

Vergewissern Sie sich, dass die folgenden Voraussetzungen erfüllt sind, bevor Sie fortfahren:

* Sie haben ein [Azure-VNET](../virtual-network/quick-create-portal.md) mit einem Subnetz erstellt, in dem der private Endpunkt erstellt wird.

* Richten Sie für Geräte, die in lokalen Netzwerken betrieben werden, Verbindungen mit Ihrem Azure-VNET über ein [virtuelles privates Netzwerk (VPN)](../vpn-gateway/vpn-gateway-about-vpngateways.md) oder per privatem [ExpressRoute](https://azure.microsoft.com/services/expressroute/)-Peering ein.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>Einrichten eines privaten Endpunkts für eingehenden IoT Hub-Datenverkehr

Ein privater Endpunkt kann für IoT Hub-Geräte-APIs (z. B. D2C-Nachrichten) sowie Dienst-APIs (z. B. Erstellen und Aktualisieren von Geräten) verwendet werden.

1. Wählen Sie im Azure-Portal **Netzwerk** und **Verbindungen mit privatem Endpunkt** aus, und klicken Sie auf **+ Privater Endpunkt**.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="Screenshot, der zeigt, wo ein privater Endpunkt für IoT Hub hinzugefügt wird":::

1. Geben Sie das Abonnement, die Ressourcengruppe, den Namen und die Region an, in der der neue private Endpunkt erstellt werden soll. Idealerweise sollte ein privater Endpunkt in der gleichen Region wie Ihr Hub erstellt werden.

1. Klicken Sie auf **Weiter: Ressource**, und geben Sie das Abonnement für die IoT Hub-Ressource an. Wählen Sie dann **"Microsoft.Devices/IotHubs"** als Ressourcentyp, den IoT Hub-Namen als **Ressource** und **iotHub** als Zielunterressource aus.

1. Klicken Sie auf **Weiter: Konfiguration**, und stellen Sie Ihr virtuelles Netzwerk und Subnetz bereit, in dem der private Endpunkt erstellt werden soll. Klicken Sie bei Bedarf auf die Option für die Integration in eine private Azure-DNS-Zone.

1. Klicken Sie auf **Weiter: Tags**, und stellen Sie optional Tags für Ihre Ressource bereit.

1. Klicken Sie auf **Überprüfen und erstellen**, um die Private Link-Ressource zu erstellen.

### <a name="built-in-event-hub-compatible-endpoint"></a>Integrierter, mit Event Hub kompatibler Endpunkt 

Auf den [integrierten, mit Event Hub kompatiblen Endpunkt](iot-hub-devguide-messages-read-builtin.md) kann auch über den privaten Endpunkt zugegriffen werden. Bei konfigurierter privater Verbindung sollte für den integrierten Endpunkt eine zusätzliche Verbindung für private Endpunkte angezeigt werden. Dabei handelt es sich um das Element mit `servicebus.windows.net` im vollqualifizierten Domänennamen.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="Abbildung: Zwei private Endpunkte mit jeweils angegebener privater IoT Hub-Verbindung":::


### <a name="pricing-for-private-link"></a>Preise für Private Link

Ausführliche Preisinformationen finden Sie unter [Azure Private Link – Preise](https://azure.microsoft.com/pricing/details/private-link).

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>Ausgehende Konnektivität von IoT Hub zu anderen Azure-Ressourcen

IoT Hub kann für das [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md), den [Dateiupload](./iot-hub-devguide-file-upload.md) und das [Importieren und Exportieren von Geräten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md) über den öffentlichen Endpunkt der Ressourcen eine Verbindung mit Azure Blob Storage-, Event Hub- und Service Bus-Ressourcen herstellen. Durch Binden einer Ressource an ein VNET wird standardmäßig die Konnektivität mit dieser Ressource blockiert. Dies führt dazu, dass diese Konfiguration IoT Hub daran hindert, Daten an Ihre Ressourcen zu senden. Um dieses Problem zu beheben, müssen Sie die Konnektivität von Ihrer IoT Hub-Ressource mit Ihren Speicherkonto-, Event Hub- oder Service Bus-Ressourcen über die Option **Vertrauenswürdige Microsoft-Dienste** aktivieren.

### <a name="turn-on-managed-identity-for-iot-hub"></a>Aktivieren einer verwalteten Identität für IoT Hub

Damit andere Dienste Ihre IoT Hub-Instanz als vertrauenswürdigen Microsoft-Dienst finden können, muss diese über eine systemseitig zugewiesene verwaltete Identität verfügen.

1. Navigieren Sie im IoT Hub-Portal zu **Identität**.

1. Klicken Sie unter **Status** auf **Ein** und dann auf **Speichern**.

    :::image type="content" source="media/virtual-network-support/managed-identity.png" alt-text="Screenshot, der zeigt, wie eine verwaltete Identität für IoT Hub aktiviert wird":::

### <a name="assign-managed-identity-to-your-iot-hub-at-creation-time-using-arm-template"></a>Zuweisen einer verwalteten Identität zu Ihrem IoT-Hub zum Zeitpunkt der Erstellung mithilfe einer ARM-Vorlage

Verwenden Sie die folgende ARM-Vorlage, um Ihrem IoT-Hub bei der Ressourcenbereitstellung eine verwaltete Identität zuzuweisen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Devices/IotHubs",
      "apiVersion": "2020-03-01",
      "name": "<provide-a-valid-resource-name>",
      "location": "<any-of-supported-regions>",
      "identity": {
        "type": "SystemAssigned"
      },
      "sku": {
        "name": "<your-hubs-SKU-name>",
        "tier": "<your-hubs-SKU-tier>",
        "capacity": 1
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-02-01",
      "name": "updateIotHubWithKeyEncryptionKey",
      "dependsOn": [
        "<provide-a-valid-resource-name>"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "0.9.0.0",
          "resources": [
            {
              "type": "Microsoft.Devices/IotHubs",
              "apiVersion": "2020-03-01",
              "name": "<provide-a-valid-resource-name>",
              "location": "<any-of-supported-regions>",
              "identity": {
                "type": "SystemAssigned"
              },
              "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
              }
            }
          ]
        }
      }
    }
  ]
}
```

Nachdem Sie die Werte für Ihre Ressourcen `name`, `location`, `SKU.name` und `SKU.tier` ersetzt haben, können Sie die Ressource mithilfe der Azure CLI in einer vorhandenen Ressourcengruppe bereitstellen, indem Sie Folgendes verwenden:

```azurecli-interactive
az deployment group create --name <deployment-name> --resource-group <resource-group-name> --template-file <template-file.json>
```

Nachdem die Ressource erstellt wurde, können Sie die verwaltete Dienstidentität, die Ihrem Hub zugewiesen ist, mithilfe der Azure CLI abrufen:

```azurecli-interactive
az resource show --resource-type Microsoft.Devices/IotHubs --name <iot-hub-resource-name> --resource-group <resource-group-name>
```

### <a name="pricing-for-managed-identity"></a>Preise für verwaltete Identitäten

Das Feature für die Ausnahme für vertrauenswürdige Microsoft-Erstanbieterdienste ist kostenlos. Die Gebühren für die bereitgestellten Speicherkonten, Event Hubs oder Service Bus-Ressourcen werden separat berechnet.

### <a name="egress-connectivity-to-storage-account-endpoints-for-routing"></a>Ausgehende Konnektivität zu Speicherkontoendpunkten für das Routing

IoT Hub kann Nachrichten an ein Speicherkonto im Kundenbesitz weiterleiten. Damit die Routingfunktion trotz Firewalleinschränkungen auf ein Speicherkonto zugreifen kann, muss Ihre IoT Hub-Instanz über eine [verwaltete Identität](#turn-on-managed-identity-for-iot-hub) verfügen. Nachdem eine verwaltete Identität bereitgestellt wurde, führen Sie die folgenden Schritte aus, um der Ressourcenidentität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihr Speicherkonto zu erteilen.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Speicherkontos, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

2. Wählen Sie **Mitwirkender an Storage-Blobdaten** ([*nicht* „Mitwirkender“ oder „Speicherkontomitwirkender“](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) als **Rolle** und **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** für **Zugriff zuweisen für** aus, und wählen Sie dann in der Dropdownliste den Ressourcennamen Ihrer IoT Hub-Instanz aus. Klicken Sie auf die Schaltfläche **Save** .

3. Navigieren Sie in Ihrem Speicherkonto zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. Klicken Sie auf die Schaltfläche **Save** .

4. Navigieren Sie auf der Seite Ihrer IoT Hub-Ressource zur Registerkarte **Nachrichtenrouting**.

5. Navigieren Sie zum Abschnitt **Benutzerdefinierte Endpunkte**, und klicken Sie auf **Hinzufügen**. Wählen Sie **Storage** als Endpunkttyp aus.

6. Geben Sie auf der angezeigten Seite einen Namen für den Endpunkt ein, wählen Sie den Container aus, den Sie in Blob Storage verwenden möchten, und geben Sie das Codierungs- und Dateinamenformat an. Wählen Sie **Identitätsbasiert** als **Authentifizierungstyp** für Ihren Speicherendpunkt aus. Klicken Sie auf die Schaltfläche **Erstellen** .

Nun ist Ihr benutzerdefinierter Speicherendpunkt für die Verwendung der vom System zugewiesenen Identität des Hubs eingerichtet und verfügt trotz der Firewalleinschränkungen über die Berechtigung, auf Ihre Speicherressource zuzugreifen. Sie können diesen Endpunkt jetzt zum Einrichten einer Routingregel verwenden.

### <a name="egress-connectivity-to-event-hubs-endpoints-for-routing"></a>Ausgehende Konnektivität zu Event Hub-Endpunkten für das Routing

IoT Hub kann so konfiguriert werden, dass Nachrichten an einen Event Hub-Namespace im Besitz von Kunden weitergeleitet werden. Damit die Routingfunktion trotz Firewalleinschränkungen auf eine Event Hub-Ressource zugreifen kann, muss Ihre IoT Hub-Instanz über eine verwaltete Identität verfügen. Nachdem eine verwaltete Identität erstellt wurde, führen Sie die folgenden Schritte aus, um der Ressourcenidentität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihre Event Hub-Instanzen zu erteilen.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Event Hubs, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

2. Wählen Sie **Azure Event Hubs-Datensender** als **Rolle** und **Azure AD user, group, or service principal** (Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal) für **Assigning access to** (Zugriff für) aus, und klicken Sie dann auf den Ressourcennamen Ihres IoT-Hubs in der Dropdownliste. Klicken Sie auf die Schaltfläche **Save** .

3. Navigieren Sie in Ihren Event Hubs zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Allow trusted Microsoft services to access event hubs** (Vertrauenswürdigen Microsoft-Diensten den Zugriff auf Event Hubs erlauben). Klicken Sie auf die Schaltfläche **Save** .

4. Navigieren Sie auf der Seite Ihrer IoT Hub-Ressource zur Registerkarte **Nachrichtenrouting**.

5. Navigieren Sie zum Abschnitt **Benutzerdefinierte Endpunkte**, und klicken Sie auf **Hinzufügen**. Wählen Sie **Event Hubs** als Endpunkttyp aus.

6. Geben Sie auf der angezeigten Seite einen Namen für den Endpunkt ein, und wählen Sie den Event Hubs-Namespace und die Event Hubs-Instanz aus. Wählen Sie **Identitätsbasiert** als **Authentifizierungstyp** aus, und klicken Sie auf die Schaltfläche **Erstellen**.

Nun ist Ihr benutzerdefinierter Event Hub-Endpunkt für die Verwendung der vom System zugewiesenen Identität des Hubs eingerichtet und verfügt trotz der Firewalleinschränkungen über die Berechtigung, auf Ihre Event Hubs zuzugreifen. Sie können diesen Endpunkt jetzt zum Einrichten einer Routingregel verwenden.

### <a name="egress-connectivity-to-service-bus-endpoints-for-routing"></a>Ausgehende Konnektivität zu Service Bus-Endpunkten für das Routing

IoT Hub kann so konfiguriert werden, dass Nachrichten an einen Service Bus-Namespace im Besitz von Kunden weitergeleitet werden. Damit die Routingfunktion trotz Firewalleinschränkungen auf eine Service Bus-Ressource zugreifen kann, muss Ihre IoT Hub-Instanz über eine verwaltete Identität verfügen. Nachdem eine verwaltete Identität bereitgestellt wurde, führen Sie die folgenden Schritte aus, um der Ressourcenidentität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihre Service Bus-Instanz zu erteilen.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Service Bus, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

2. Wählen Sie **Azure Service Bus-Datensender** als **Rolle** und **Azure AD user, group, or service principal** (Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal) für **Assigning access to** (Zugriff für) aus, und klicken Sie dann auf den Ressourcennamen Ihres IoT-Hubs in der Dropdownliste. Klicken Sie auf die Schaltfläche **Save** .

3. Navigieren Sie in Ihrem Service Bus zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Allow trusted Microsoft services to access this service bus** (Vertrauenswürdigen Microsoft-Diensten den Zugriff auf Service Bus erlauben). Klicken Sie auf die Schaltfläche **Save** .

4. Navigieren Sie auf der Seite Ihrer IoT Hub-Ressource zur Registerkarte **Nachrichtenrouting**.

5. Navigieren Sie zum Abschnitt **Benutzerdefinierte Endpunkte**, und klicken Sie auf **Hinzufügen**. Wählen Sie **Service Bus-Warteschlange** oder **Service Bus-Thema** (sofern zutreffend) als Endpunkttyp aus.

6. Geben Sie auf der angezeigten Seite einen Namen für den Endpunkt ein, und wählen Sie den Namespace, die Warteschlange oder das Thema (sofern zutreffend) Ihres Service Bus aus. Wählen Sie **Identitätsbasiert** als **Authentifizierungstyp** aus, und klicken Sie auf die Schaltfläche **Erstellen**.

Nun ist Ihr benutzerdefinierter Service Bus-Endpunkt für die Verwendung der vom System zugewiesenen Identität des Hubs eingerichtet und verfügt trotz der Firewalleinschränkungen über die Berechtigung, auf Ihre Service Bus-Ressource zuzugreifen. Sie können diesen Endpunkt jetzt zum Einrichten einer Routingregel verwenden.

### <a name="egress-connectivity-to-storage-accounts-for-file-upload"></a>Ausgehende Konnektivität zu Speicherkonten für den Dateiupload

Das IoT Hub-Feature für den Dateiupload ermöglicht Geräten das Hochladen von Dateien in ein Speicherkonto, das im Besitz von Kunden ist. Sowohl Geräte als auch IoT Hub müssen eine Verbindung mit dem Speicherkonto herstellen, damit der Dateiuploadvorgang funktioniert. Wenn Firewalleinschränkungen für das Speicherkonto vorhanden sind, müssen Ihre Geräte einen der unterstützten Mechanismen des Speicherkontos verwenden (z. B. [private Endpunkte](../private-link/tutorial-private-endpoint-storage-portal.md), [Dienstendpunkte](../virtual-network/virtual-network-service-endpoints-overview.md) oder eine [direkte Firewallkonfiguration](../storage/common/storage-network-security.md)), um die Verbindung herzustellen. Wenn Firewalleinschränkungen im Speicherkonto vorhanden sind, muss IoT Hub entsprechend konfiguriert werden, um über die Ausnahme für vertrauenswürdige Microsoft-Dienste auf die Speicherressource zuzugreifen. Zu diesem Zweck muss Ihre IoT Hub-Instanz über eine verwaltete Identität verfügen. Nachdem eine verwaltete Identität bereitgestellt wurde, führen Sie die folgenden Schritte aus, um der Ressourcenidentität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihr Speicherkonto zu erteilen.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Speicherkontos, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

2. Wählen Sie **Mitwirkender an Storage-Blobdaten** ([*nicht* „Mitwirkender“ oder „Speicherkontomitwirkender“](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) als **Rolle** und **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** für **Zugriff zuweisen für** aus, und wählen Sie dann in der Dropdownliste den Ressourcennamen Ihrer IoT Hub-Instanz aus. Klicken Sie auf die Schaltfläche **Save** .

3. Navigieren Sie in Ihrem Speicherkonto zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. Klicken Sie auf die Schaltfläche **Save** .

4. Navigieren Sie auf der Seite Ihrer IoT Hub-Ressource zur Registerkarte **Dateiupload**.

5. Wählen Sie auf der angezeigten Seite den Container aus, den Sie in Blob Storage verwenden möchten, und konfigurieren Sie die **Dateibenachrichtigungseinstellungen**, **SAS TTL**, **Standard-TTL** und die **Anzahl maximaler Zustellungen** nach Bedarf. Wählen Sie **Identitätsbasiert** als **Authentifizierungstyp** für Ihren Speicherendpunkt aus. Klicken Sie auf die Schaltfläche **Erstellen** . Wenn bei diesem Schritt eine Fehlermeldung angezeigt wird, legen Sie Ihr Speicherkonto vorübergehend so fest, dass der Zugriff aus **Allen Netzwerken** ermöglicht wird, und wiederholen Sie dann den Vorgang. Sie können die Firewall für das Speicherkonto konfigurieren, sobald die Konfiguration des Dateiuploads beendet ist.

Nun ist Ihr Speicherendpunkt für den Dateiupload für die Verwendung der vom System zugewiesenen Identität des Hubs eingerichtet und verfügt trotz der Firewalleinschränkungen über die Berechtigung, auf Ihre Speicherressource zuzugreifen.

### <a name="egress-connectivity-to-storage-accounts-for-bulk-device-importexport"></a>Ausgehende Konnektivität zu Speicherkonten für das Importieren und Exportieren von Geräten per Massenvorgang

IoT Hub unterstützt die Funktion zum [Importieren und Exportieren](./iot-hub-bulk-identity-mgmt.md) von Geräteinformationen per Massenvorgang von/in vom Kunden bereitgestellte Azure Storage Blob-Instanzen. Sowohl Geräte als auch IoT Hub müssen eine Verbindung mit dem Speicherkonto herstellen, damit das Feature für den Massenimport/-export funktioniert.

Diese Funktion erfordert Konnektivität von IoT Hub mit dem Speicherkonto. Damit der Zugriff auf eine Service Bus-Ressource trotz Firewalleinschränkungen funktioniert, muss Ihre IoT Hub-Instanz über eine verwaltete Identität verfügen. Nachdem eine verwaltete Identität bereitgestellt wurde, führen Sie die folgenden Schritte aus, um der Ressourcenidentität Ihres Hubs die Azure RBAC-Berechtigung für den Zugriff auf Ihre Service Bus-Instanz zu erteilen.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Zugriffssteuerung (IAM)** Ihres Speicherkontos, und klicken Sie im Abschnitt **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

2. Wählen Sie **Mitwirkender an Storage-Blobdaten** ([*nicht* „Mitwirkender“ oder „Speicherkontomitwirkender“](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues)) als **Rolle** und **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** für **Zugriff zuweisen für** aus, und wählen Sie dann in der Dropdownliste den Ressourcennamen Ihrer IoT Hub-Instanz aus. Klicken Sie auf die Schaltfläche **Save** .

3. Navigieren Sie in Ihrem Speicherkonto zur Registerkarte **Firewalls und virtuelle Netzwerke**, und aktivieren Sie die Option **Allow access from selected networks** (Zugriff von ausgewählten Netzwerken zulassen). Aktivieren Sie unter **Ausnahmen** das Kontrollkästchen für **Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben**. Klicken Sie auf die Schaltfläche **Save** .

Sie können jetzt die Azure IoT-REST-APIs zum [Erstellen von Import-/Exportaufträgen](/rest/api/iothub/service/jobs/getimportexportjobs) verwenden, um Informationen zur Verwendung der Funktion für Massenimporte/-exporte zu erhalten. Sie müssen `storageAuthenticationType="identityBased"` im Anforderungstext bereitstellen und `inputBlobContainerUri="https://..."` und `outputBlobContainerUri="https://..."` als Eingabe- bzw. Ausgabe-URLs des Speicherkontos verwenden.

Azure IoT Hub SDKs unterstützen diese Funktion im Registrierungs-Manager des Dienstclients. Der folgende Codeausschnitt zeigt, wie ein Importauftrag bzw. ein Exportauftrag mithilfe des C# SDK ausgelöst werden.

```csharp
// Call an import job on the IoT Hub
JobProperties importJob = 
await registryManager.ImportDevicesAsync(
  JobProperties.CreateForImportJob(inputBlobContainerUri, outputBlobContainerUri, null, StorageAuthenticationType.IdentityBased), 
  cancellationToken);

// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
await registryManager.ExportDevicesAsync(
    JobProperties.CreateForExportJob(outputBlobContainerUri, true, null, StorageAuthenticationType.IdentityBased),
    cancellationToken);
```

Gehen Sie folgendermaßen vor, um diese Version der Azure IoT SDKs mit Unterstützung virtueller Netzwerke für C#, Java und Node.js zu verwenden:

1. Erstellen Sie eine Umgebungsvariable namens `EnableStorageIdentity`, und legen Sie ihren Wert auf `1` fest.

2. Laden Sie das SDK herunter:  [Java](https://aka.ms/vnetjavasdk) | [C#](https://aka.ms/vnetcsharpsdk) | [Node.js](https://aka.ms/vnetnodesdk)
 
Laden Sie für Python die beschränkte Version von GitHub herunter.

1. Navigieren Sie zur [GitHub-Releaseseite](https://aka.ms/vnetpythonsdk).

2. Laden Sie die folgende Datei herunter, die sich unten auf der Releaseseite unter dem Header namens **Assets** befindet.
    > *azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl*

3. Öffnen Sie ein Terminal, und navigieren Sie zum Ordner mit der heruntergeladenen Datei.

4. Führen Sie den folgenden Befehl aus, um das Python-Dienst-SDK mit Unterstützung für virtuelle Netzwerke zu installieren:
    > pip install ./azure_iot_hub-2.2.0_limited-py2.py3-none-any.whl


## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie die unten angegebenen Links, um weitere Informationen zu IoT Hub-Features zu erhalten:

* [Nachrichtenrouting](./iot-hub-devguide-messages-d2c.md)
* [Hochladen von Dateien mit IoT Hub](./iot-hub-devguide-file-upload.md)
* [Importieren und Exportieren von IoT Hub-Geräteidentitäten per Massenvorgang](./iot-hub-bulk-identity-mgmt.md)
