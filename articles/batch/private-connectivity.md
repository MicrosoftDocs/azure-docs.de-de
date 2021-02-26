---
title: Verwenden privater Endpunkte mit Azure Batch-Konten
description: Erfahren Sie, wie Sie eine private Verbindung mit einem Azure Batch-Konto mithilfe privater Endpunkte herstellen.
ms.topic: how-to
ms.date: 09/28/2020
ms.custom: references_regions
ms.openlocfilehash: d2e9d36e9e964f2e9f9a5a986fbf55d19b3069d8
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920002"
---
# <a name="use-private-endpoints-with-azure-batch-accounts"></a>Verwenden privater Endpunkte mit Azure Batch-Konten

Standardmäßig verfügen [Azure Batch-Konten](accounts.md) über einen öffentlichen Endpunkt und sind öffentlich zugänglich. Der Batch-Dienst bietet die Möglichkeit, private Batch-Konten zu erstellen und den öffentlichen Netzwerkzugriff zu deaktivieren.

Durch Verwendung von [Azure Private Link](../private-link/private-link-overview.md) können Sie eine Verbindung mit einem Azure Batch-Konto über einen [privaten Endpunkt](../private-link/private-endpoint-overview.md) herstellen. Bei einem privaten Endpunkt handelt es sich um eine Gruppe privater IP-Adressen in einem Subnetz innerhalb Ihres virtuellen Netzwerks. Sie können dann den Zugriff auf ein Azure Batch-Konto über private IP-Adressen einschränken.

Private Link ermöglicht Benutzern den Zugriff auf ein Azure Batch-Konto innerhalb des virtuellen Netzwerks oder über ein mittels Peering verbundenes Netzwerk. Auf Ressourcen, die Private Link zugeordnet sind, kann auch lokal über privates Peering über VPN oder [Azure ExpressRoute](../expressroute/expressroute-introduction.md) zugegriffen werden. Mithilfe der [automatischen oder manuellen Genehmigungsmethode](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) können Sie eine Verbindung mit einem Azure Batch-Konto herstellen, das über Private Link konfiguriert wurde.

> [!IMPORTANT]
> Private Verbindungen in Azure Batch sind derzeit mit Ausnahme von „Deutschland, Mitte“, „Deutschland, Nordosten“, „China, Osten“, „China, Osten 2“, „China, Norden“ und „China, Norden 2“ für alle öffentlichen Regionen verfügbar.

In diesem Artikel werden die Schritte zum Erstellen eines privaten Batch-Kontos und für den Zugriff darauf mithilfe eines privaten Endpunkts beschrieben.

## <a name="azure-portal"></a>Azure-Portal

Gehen Sie wie folgt vor, um ein privates Batch-Konto über das Azure-Portal zu erstellen:

1. Wählen Sie im Bereich **Ressource erstellen** die Option **Batch-Dienst** und dann **Erstellen** aus.
2. Geben Sie auf der Registerkarte **Grundlagen** das Abonnement, die Ressourcengruppe, die Region und den Batch-Kontonamen ein, und wählen Sie dann **Weiter: Erweitert** aus.
3. Legen Sie auf der Registerkarte **Erweitert** die Option **Öffentlicher Netzwerkzugriff** auf **Deaktiviert** fest.
4. Wählen Sie die Option **Private Endpunktverbindungen** unter **Einstellungen** und dann **+ Privater Endpunkt** aus.
   :::image type="content" source="media/private-connectivity/private-endpoint-connections.png" alt-text="Private Endpunktverbindungen":::
5. Geben Sie im Bereich **Grundlagen** das Abonnement, die Ressourcengruppe, den Ressourcennamen des privaten Endpunkts und die Regionsdetails ein, oder wählen Sie diese aus, und wählen Sie dann **Weiter: Ressource** aus.
6. Legen Sie im Bereich **Ressource** den **Ressourcentyp** auf **Microsoft.Batch/batchAccounts** fest. Wählen Sie das private Batch-Konto aus, auf das Sie zugreifen möchten, und wählen Sie dann **Weiter: Konfiguration** aus.
   :::image type="content" source="media/private-connectivity/create-private-endpoint.png" alt-text="Erstellen eines privaten Endpunkts: Bereich „Ressourcen“":::
7. Geben Sie diese Informationen im Bereich **Konfiguration** ein, oder wählen Sie sie aus:
   - **Virtuelles Netzwerk:** Wählen Sie Ihr virtuelles Netzwerk aus.
   - **Subnetz**: Wählen Sie das Subnetz aus.
   - **Integration in eine private DNS-Zone**:   Wählen Sie **Ja** aus. Für die Herstellung einer privaten Verbindung mit Ihrem privaten Endpunkt benötigen Sie einen DNS-Eintrag. Es wird empfohlen, den privaten Endpunkt in eine private DNS-Zone zu integrieren. Sie können auch Ihre eigenen DNS-Server verwenden oder DNS-Einträge mithilfe der Hostdateien auf Ihren virtuellen Computern erstellen.
   - **Private DNS-Zone**:  Wählen Sie „privatelink.\<region\>.batch.azure.com“ aus. Die private DNS-Zone wird automatisch bestimmt. Sie können sie nicht über das Azure-Portal ändern.
8. Wählen Sie **Überprüfen + erstellen** aus, und warten Sie, bis Azure Ihre Konfiguration überprüft hat.
9. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus.

Nachdem der private Endpunkt bereitgestellt wurde, können Sie über den privaten Endpunkt von VMs im selben virtuellen Netzwerk auf das Batch-Konto zugreifen.

> [!IMPORTANT]
> Die Durchführung von Operationen außerhalb des virtuellen Netzwerks, in dem der private Endpunkt bereitgestellt wird, führt zu einer „AuthorizationFailure“-Meldung im Azure-Portal.

Zum Anzeigen der IP-Adresse im Azure-Portal führen Sie die folgenden Schritte aus:

1. Wählen Sie **Alle Ressourcen**.
2. Suchen Sie nach dem privaten Endpunkt, den Sie zuvor erstellt haben.
3. Wählen Sie die Registerkarte **Übersicht** aus, um die DNS-Einstellungen und IP-Adressen anzuzeigen.

:::image type="content" source="media/private-connectivity/access-private.png" alt-text="DNS-Einstellungen und IP-Adressen des privaten Endpunkts":::

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage

Wenn Sie [ein Batch-Konto mithilfe einer Azure Resource Manager-Vorlage erstellen](quick-create-template.md), ändern Sie die Vorlage so, dass **publicNetworkAccess** auf **Deaktiviert** festgelegt ist, wie unten gezeigt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "batchAccountName": {
      "type": "string",
      },
    "location": {
      "type": "string",
    }
  },
  "resources": [
    {
      "name": "[parameters('batchAccountName')]",
      "type": "Microsoft.Batch/batchAccounts",
      "apiVersion": "2020-03-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": []
      "properties": {
        "poolAllocationMode": "BatchService"
        "publicNetworkAccess": "Disabled"
      }
    }
  ]
}
```

## <a name="configure-dns-zones"></a>Konfigurieren von DNS-Zonen

Verwenden Sie eine [private DNS-Zone](../dns/private-dns-privatednszone.md) innerhalb des Subnetzes, in dem Sie den privaten Endpunkt erstellt haben. Konfigurieren Sie die Endpunkte so, dass jede private IP-Adresse einem DNS-Eintrag zugeordnet ist.

Wenn Sie den privaten Endpunkt erstellen, können Sie ihn in eine [private DNS-Zone](../dns/private-dns-privatednszone.md) in Azure integrieren. Wenn Sie stattdessen eine [benutzerdefinierte Domäne](../dns/dns-custom-domain.md) verwenden möchten, müssen Sie diese so konfigurieren, dass DNS-Einträge für alle privaten IP-Adressen hinzugefügt werden, die für den privaten Endpunkt reserviert sind.

## <a name="pricing"></a>Preise

Einzelheiten zu Kosten im Zusammenhang mit privaten Endpunkten finden Sie unter [Azure Private Link_ Preise](https://azure.microsoft.com/pricing/details/private-link/).

## <a name="current-limitations-and-best-practices"></a>Aktuelle Einschränkungen und bewährte Methoden

Berücksichtigen Sie bei der Erstellung des privaten Batch-Kontos Folgendes:

- Private Endpunktressourcen müssen im gleichen Abonnement wie das Batch-Konto erstellt werden.
- Um die private Verbindung zu löschen, müssen Sie die private Endpunktressource löschen.
- Nachdem ein Batch-Konto mit öffentlichem Netzwerkzugriff erstellt wurde, können Sie es nicht nur in ein Konto mit privatem Zugriff ändern.
- DNS-Einträge in der privaten DNS-Zone werden nicht automatisch entfernt, wenn Sie einen privaten Endpunkt löschen oder eine Region aus dem Batch-Konto entfernen. Sie müssen die DNS-Einträge manuell entfernen, bevor Sie einen neuen privaten Endpunkt hinzufügen, der mit dieser privaten DNS-Zone verknüpft ist. Wenn Sie die DNS-Einträge nicht bereinigen, können unerwartete Probleme mit der Datenebene auftreten. Hierzu zählen beispielsweise Datenausfälle für Regionen, die nach dem Entfernen eines privaten Endpunkts oder einer Region hinzugefügt wurden.

## <a name="next-steps"></a>Nächste Schritte

- Lernen Sie, wie Sie [Batch-Pools in virtuellen Netzwerken erstellen](batch-virtual-network.md).
- Informationen zum [Erstellen von Batchpools ohne öffentliche IP-Adresse](batch-pool-no-public-ip-address.md)
- Erfahren Sie, wie Sie [Batch-Pools mit angegebenen öffentlichen IP-Adressen erstellen](create-pool-public-ip.md).
- Erfahren Sie mehr über [Azure Private Link](../private-link/private-link-overview.md).
