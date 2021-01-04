---
title: 'Azure HDInsight: Verschlüsselung während der Übertragung'
description: Hier erfahren Sie mehr über Sicherheitsfeatures, die die Verschlüsselung während der Übertragung für Ihren Azure HDInsight-Cluster ermöglichen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: ac210adbc29075f27739b08dcf83eeedbeea3ed5
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630361"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>IPSec-Verschlüsselung während der Übertragung für Azure HDInsight

In diesem Artikel wird die Implementierung der Verschlüsselung während der Übertragung für die Kommunikation zwischen Azure HDInsight-Clusterknoten erläutert.

## <a name="background"></a>Hintergrund

Azure HDInsight bietet eine Vielzahl von Sicherheitsfeatures zum Schützen von Unternehmensdaten. Diese Lösungen sind unter den Säulen der Umgebungssicherheit, Authentifizierung, Autorisierung, Überwachung, Verschlüsselung und Compliance gruppiert. Die Verschlüsselung kann sowohl auf ruhende Daten als auch auf Daten während der Übertragung angewendet werden.

Die Verschlüsselung ruhender Daten wird durch die serverseitige Verschlüsselung in Azure-Speicherkonten sowie durch die Datenträgerverschlüsselung auf virtuellen Azure-Computern abgedeckt, die Teil Ihres HDInsight-Clusters sind.

Die Verschlüsselung von Daten während der Übertragung in HDInsight wird mit [Transport Layer Security (TLS)](../transport-layer-security.md) für Zugreifen auf die Clustergateways und [Internetprotokollsicherheit (Internet Protocol Security, IPSec)](https://wikipedia.org/wiki/IPsec) zwischen Clusterknoten erreicht. IPSec kann zwischen allen Hauptknoten, Workerknoten, Edgeknoten und ZooKeeper-Knoten aktiviert werden. IPSec wird nicht für Datenverkehr zwischen Gateway- oder [Identitätsbroker](./identity-broker.md)knoten aktiviert, die Windows-VMs und weitere Linux-Knoten im Cluster sind.

## <a name="enable-encryption-in-transit"></a>Aktivieren der Verschlüsselung während der Übertragung

### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um über das Azure-Portal einen neuen Cluster zu erstellen, für den die Verschlüsselung während der Übertragung aktiviert ist:

1. Starten Sie den normalen Clustererstellungsprozess. Informationen zu den ersten Schritten bei der Clustererstellung finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).
1. Vervollständigen Sie die Angaben auf den Registerkarten **Grundlagen** und **Speicher**. Fahren Sie mit der Registerkarte **Sicherheit + Netzwerkbetrieb** fort.

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Clustererstellung: Registerkarte „Sicherheit + Netzwerkbetrieb":::

1. Aktivieren Sie auf der Registerkarte **Sicherheit + Netzwerkbetrieb** das Kontrollkästchen **Verschlüsselung während der Übertragung aktivieren**.

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Clustererstellung: Verschlüsselung während der Übertragung aktivieren":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Erstellen eines Clusters, für den die Verschlüsselung während der Übertragung, aktiviert ist, über die Azure CLI

Die Verschlüsselung während der Übertragung wird mithilfe der Eigenschaft `isEncryptionInTransitEnabled` aktiviert.

Sie können [eine Beispielvorlage und eine Parameterdatei](https://github.com/Azure-Samples/hdinsight-enterprise-security) herunterladen. Ersetzen Sie vor der Verwendung der Vorlage und des Azure CLI-Codeausschnitts unten die folgenden Platzhalter durch die korrekten Werte:

| Platzhalter | BESCHREIBUNG |
|---|---|
| `<SUBSCRIPTION_ID>` | Die ID Ihres Azure-Abonnements |
| `<RESOURCE_GROUP>` | Die Ressourcengruppe, in der der neue Cluster und das Speicherkonto erstellt werden sollen |
| `<STORAGEACCOUNTNAME>` | Das vorhandene Speicherkonto, das mit dem Cluster verwendet werden soll. Der Name sollte im Format `ACCOUNTNAME.blob.core.windows.net` angegeben werden. |
| `<CLUSTERNAME>` | Der Name des HDInsight-Clusters. |
| `<PASSWORD>` | Das gewählte Kennwort für die Anmeldung im Cluster über SSH und das Ambari-Dashboard |
| `<VNET_NAME>` | Das virtuelle Netzwerk, in dem der Cluster bereitgestellt wird |

Der Codeausschnitt führt die folgenden ersten Schritte durch:

1. Anmeldung bei Ihrem Azure-Konto
1. Festlegen des aktiven Abonnements, in dem die Erstellung durchgeführt wird
1. Erstellen einer neuen Ressourcengruppe für die neuen Bereitstellungsaktivitäten
1. Bereitstellen der Vorlage, um einen neuen Cluster zu erstellen

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über die Unternehmenssicherheit in Azure HDInsight](hdinsight-security-overview.md)
* [Synchronisieren von Azure Active Directory-Benutzern in einen HDInsight-Cluster](../disk-encryption.md)
