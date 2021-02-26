---
title: Erstellen von Integrationsdienstumgebungen (Integration Service Environments, ISEs) mit der Logic Apps-REST-API
description: Hier erfahren Sie, wie Sie mithilfe der Logic Apps-REST-API eine Integrationsdienstumgebung (Integration Service Environment, ISE) erstellen, um über Azure Logic Apps auf virtuelle Azure-Netzwerke (VNETs) zugreifen zu können.
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: d4500229800fa5d1743779b29927637777647e47
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550656"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Erstellen einer Integrationsdienstumgebung (Integration Service Environment, ISE) mithilfe der Logic Apps-REST-API

Für Szenarien, in denen Ihre Logik-Apps und Integrationskonten Zugriff auf ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) benötigen, können Sie mithilfe der Logic Apps-REST-API eine [*Integrationsdienstumgebung* (Integration Service Environment, ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erstellen. Weitere Informationen zu ISEs finden Sie unter [Zugreifen auf Ressourcen virtueller Azure-Netzwerke über Azure Logic Apps mit Integrationsdienstumgebungen (ISEs)](connect-virtual-network-vnet-isolated-environment-overview.md).

In diesem Artikel wird gezeigt, wie Sie mithilfe der Logic Apps-REST-API generell eine ISE erstellen. Optional können Sie auch eine [systemseitig zugewiesene oder benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) in Ihrer ISE aktivieren, aber zurzeit nur mithilfe der Logic Apps-REST-API. Mit dieser Identität kann Ihre ISE den auf geschützte Ressourcen wie virtuelle Computer und andere Systeme oder Dienste, die sich innerhalb eines virtuellen Azure-Netzwerks befinden oder damit verbunden sind, authentifizieren. Auf diese Weise müssen Sie sich nicht mit Ihren Anmeldeinformationen anmelden.

Weitere Informationen zu anderen Möglichkeiten zum Erstellen einer ISE finden Sie in den folgenden Artikeln:

* [Erstellen einer ISE mithilfe des Azure-Portals](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Erstellen einer ISE mithilfe der Azure Resource Manager-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Erstellen einer ISE, die die Verwendung von kundenseitig verwalteten Schlüsseln für die Verschlüsselung ruhender Daten unterstützt](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Voraussetzungen

* Dieselben [Voraussetzungen](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) und [Zugriffsanforderungen ](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) wie bei Erstellung der ISE im Azure-Portal

* Alle zusätzlichen Ressourcen, die Sie mit Ihrer ISE verwenden möchten, damit Sie deren Informationen in die ISE-Definition einschließen können, z. B.: 

  * Um die Unterstützung für selbstsignierte Zertifikate zu aktivieren, müssen Sie Informationen zu diesem Zertifikat in die ISE-Definition einschließen.

  * Um die benutzerseitig zugewiesene verwaltete Identität zu aktivieren, müssen Sie diese Identität im Voraus erstellen und die Eigenschaften `objectId`, `principalId` und `clientId` sowie deren Werte in die ISE-Definition einschließen. Weitere Informationen finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Ein Tool, mit dem Sie Ihre ISE durch Aufrufen der Logic Apps-REST-API mit einer HTTPS PUT-Anforderung erstellen können. Beispielsweise können Sie [Postman](https://www.getpostman.com/downloads/) verwenden oder eine Logik-App erstellen, die diese Aufgabe ausführt.

## <a name="create-the-ise"></a>Erstellen der ISE

Um Ihre ISE durch Aufrufen der Logic Apps-REST-API zu erstellen, führen Sie diese HTTPS PUT-Anforderung aus:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Version 2019-05-01 der Logic Apps-REST-API erfordert, dass Sie Ihre eigene HTTP PUT-Anforderung für ISE-Connectors ausführen.

Der Bereitstellungsvorgang dauert in der Regel maximal zwei Stunden. Gelegentlich kann die Bereitstellung bis zu vier Stunden dauern. Wählen Sie zum Überprüfen des Bereitstellungsstatus im [Azure-Portal](https://portal.azure.com) auf Ihrer Azure-Symbolleiste das Benachrichtigungssymbol aus, um den Benachrichtigungsbereich zu öffnen.

> [!NOTE]
> Wenn die Bereitstellung fehlschlägt oder Sie Ihre ISE löschen, kann es bis zu einer Stunde dauern, bis Azure Ihre Subnetze freigibt. Daher müssen Sie möglicherweise warten, bevor Sie diese Subnetze in einer anderen ISE wiederverwenden können.
>
> Wenn Sie Ihr virtuelles Netzwerk löschen, dauert es in der Regel bis zu zwei Stunden, bis Azure Ihre Subnetze freigibt, dieser Vorgang kann aber auch länger dauern. 
> Stellen Sie beim Löschen virtueller Netzwerke sicher, dass keine Ressourcen mehr verbunden sind. 
> Beachten Sie hierzu die Anleitung [Löschen eines virtuellen Netzwerks](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Anforderungsheader

Schließen Sie die folgenden Eigenschaften in den Anforderungsheader ein:

* `Content-type`: Legen Sie diesen Eigenschaftswert auf `application/json` fest.

* `Authorization`: Legen Sie diesen Eigenschaftswert auf das Bearertoken für den Kunden fest, der Zugriff auf das gewünschte Azure-Abonnement bzw. die Ressourcengruppe hat.

<a name="request-body"></a>

## <a name="request-body"></a>Anforderungstext

Geben Sie im Anforderungstext die für die Erstellung der ISE zu verwendende Ressourcendefinition an, einschließlich der Informationen zu zusätzlichen Funktionen, die Sie in Ihrer ISE aktivieren möchten, z. B.:

* Nehmen Sie das `certificates`-Objekt wie später in diesem Artikel beschrieben in den `properties`-Abschnitt der ISE-Definition auf, um eine ISE zu erstellen, die die Verwendung eines selbstsignierten Zertifikats und eines von der Zertifizierungsstelle ausgestellten Zertifikats zulässt, das am `TrustedRoot`-Speicherort gespeichert ist.

* Zum Erstellen einer ISE, die eine systemseitig zugewiesene oder benutzerseitig zugewiesene verwaltete Identität verwendet, schließen Sie das `identity`-Objekt mit dem verwalteten Identitätstyp sowie andere erforderliche Informationen in die ISE-Definition ein, wie später in diesem Artikel beschrieben.

* Zum Erstellen einer ISE, die kundenseitig verwaltete Schlüssel und Azure Key Vault zum Verschlüsseln ruhender Daten verwendet, schließen Sie die [Informationen ein, die die Unterstützung kundenseitig verwalteter Schlüssel ermöglichen](customer-managed-keys-integration-service-environment.md). Sie können kundenseitig verwaltete Schlüssel *nur bei der Erstellung* und nicht mehr später festlegen.

### <a name="request-body-syntax"></a>Syntax des Anforderungstexts

Hier ist die Syntax des Anforderungstexts, in der die Eigenschaften für das Erstellen der ISE beschrieben werden:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Beispiel für Anforderungstext

In diesem Beispielanforderungstext werden die Beispielwerte gezeigt:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Hinzufügen benutzerdefinierter Stammzertifikate

Häufig verwenden Sie eine ISE, um eine Verbindung mit benutzerdefinierten Diensten in Ihrem virtuellen Netzwerk oder Ihrem lokalen Netzwerk herzustellen. Diese benutzerdefinierten Dienste sind oft durch ein Zertifikat geschützt, das von einer benutzerdefinierten Stammzertifizierungsstelle ausgestellt wird (z. B. Unternehmenszertifizierungsstelle oder selbstsigniertes Zertifikat). Weitere Informationen zum Verwenden von selbstsignierten Zertifikaten finden Sie unter [Sicherer Zugriff und Daten: Zugriff für ausgehende Aufrufe anderer Dienste und Systeme](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Damit Ihre ISE erfolgreich eine Verbindung mit diesen Diensten über die TLS (Transport Layer Security) herstellen kann, benötigt Ihre ISE Zugriff auf diese Stammzertifikate.

#### <a name="considerations-for-adding-custom-root-certificates"></a>Überlegungen zum Hinzufügen von benutzerdefinierten Stammzertifikaten

Bevor Sie Ihre ISE mit einem benutzerdefinierten vertrauenswürdigen Stammzertifikat aktualisieren, überprüfen Sie diese Aspekte:

* Stellen Sie sicher, dass Sie das Stammzertifikat *und* alle Zwischenzertifikate hochladen. Es gilt eine maximale Anzahl von 20 Zertifikaten.

* Das Hochladen von Stammzertifikaten ist ein Ersetzungsvorgang, bei dem der aktuelle Upload vorherige Uploads überschreibt. Wenn Sie beispielsweise eine Anforderung senden, die ein Zertifikat hochlädt, und dann eine weitere Anforderung senden, um ein anderes Zertifikat hochzuladen, verwendet Ihre ISE nur das zweite Zertifikat. Wenn Sie beide Zertifikate verwenden müssen, fügen Sie diese derselben Anforderung hinzu.  

* Das Hochladen von Stammzertifikaten ist ein asynchroner Vorgang, der einige Zeit in Anspruch nehmen kann. Sie können mit demselben URI eine `GET`-Anforderung senden, um den Status oder das Ergebnis zu überprüfen. Die Antwortnachricht umfasst ein `provisioningState`-Feld, das den `InProgress`-Wert zurückgibt, wenn der Uploadvorgang noch ausgeführt wird. Wenn der `provisioningState`-Wert `Succeeded` ist, ist der Uploadvorgang abgeschlossen.

#### <a name="request-syntax"></a>Anforderungssyntax

Um Ihre ISE mit einem benutzerdefinierten vertrauenswürdigen Stammzertifikat zu aktualisieren, senden Sie die folgende HTTPS PATCH-Anforderung an die [Azure Resource Manager-URL, die sich je nach ihrer Azure-Umgebung unterscheidet](../azure-resource-manager/management/control-plane-and-data-plane.md#control-plane), z. B.:

| Umgebung | Azure Resource Manager-URL |
|-------------|----------------------------|
| Azure Global (mehrere Mandanten) | `PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Azure Government | `PATCH https://management.usgovcloudapi.net/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
| Microsoft Azure China 21Vianet | `PATCH https://management.chinacloudapi.cn/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01` |
|||

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Syntax des Anforderungstexts zum Hinzufügen von benutzerdefinierten Stammzertifikaten

Dies ist die Syntax des Anforderungstexts, in der die Eigenschaften für das Hinzufügen von Stammzertifikaten beschrieben werden:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Ressourcen zu Ihrer Integrationsdienstumgebung (Integration Service Environment, ISE) in Azure Logic Apps](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Verwalten von Integrationsdienstumgebungen](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
