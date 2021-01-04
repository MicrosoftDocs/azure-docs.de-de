---
title: Schützen und Isolieren von Azure HDInsight-Clustern mit Private Link (Vorschauversion)
description: Es wird beschrieben, wie Sie Azure HDInsight-Cluster in einem virtuellen Netzwerk mit Azure Private Link isolieren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: fac26c616c977eedc466f004a9455297ec995fb8
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352540"
---
# <a name="secure-and-isolate-azure-hdinsight-clusters-with-private-link-preview"></a>Schützen und Isolieren von Azure HDInsight-Clustern mit Private Link (Vorschauversion)

In der [Standardarchitektur für virtuelle Netzwerke](./hdinsight-virtual-network-architecture.md) von Azure HDInsight kommuniziert der HDInsight-Ressourcenanbieter über öffentliche IP-Adressen mit dem Cluster. In einigen Szenarien ist eine vollständige Netzwerkisolation ohne Verwendung von öffentlichen IP-Adressen erforderlich. In diesem Artikel werden die erweiterten Steuerelemente beschrieben, die Sie zum Erstellen eines privaten HDInsight-Clusters verwenden können. Informationen zum Einschränken des ein- und ausgehenden Datenverkehrs für Ihren Cluster ohne vollständige Netzwerkisolation finden Sie unter [Steuern des Netzwerkdatenverkehrs in Azure HDInsight](./control-network-traffic.md).

Sie können private HDInsight-Cluster erstellen, indem Sie bestimmte Netzwerkeigenschaften in einer ARM-Vorlage (Azure Resource Manager) konfigurieren. Es gibt zwei Eigenschaften, die Sie für die Erstellung von privaten HDInsight-Clustern verwenden können:

* Entfernen Sie öffentliche IP-Adressen, indem Sie `resourceProviderConnection` auf „Outbound“ (Ausgehend) festlegen.
* Aktivieren Sie Azure Private Link, und verwenden Sie [private Endpunkte](../private-link/private-endpoint-overview.md), indem Sie `privateLink` auf „Enabled“ (Aktiviert) festlegen.

## <a name="remove-public-ip-addresses"></a>Entfernen von öffentlichen IP-Adressen

Standardmäßig nutzt der HDInsight-Ressourcenanbieter eine *eingehende* Verbindung mit dem Cluster über öffentliche IP-Adressen. Wenn die Netzwerkeigenschaft `resourceProviderConnection` auf *Outbound* (Ausgehend) festgelegt ist, werden die Verbindungen mit dem HDInsight-Ressourcenanbieter umgekehrt, damit sie immer aus dem Cluster zum Ressourcenanbieter initiiert werden. Ohne eingehende Verbindung sind Diensttags in Eingangsrichtung oder öffentliche IP-Adressen nicht erforderlich.

Bei den grundlegenden Lastenausgleichsmodulen der Standardarchitektur für virtuelle Netzwerke wird automatisch eine öffentliche Netzwerkadressenübersetzung (NAT) für den Zugriff auf die erforderlichen ausgehenden Abhängigkeiten, z. B. den HDInsight-Ressourcenanbieter, bereitgestellt. Wenn Sie die ausgehende Konnektivität für das öffentliche Internet einschränken möchten, können Sie eine [Firewall konfigurieren](./hdinsight-restrict-outbound-traffic.md), aber dies ist nicht zwingend erforderlich.

Die Festlegung von `resourceProviderConnection` auf „Outbound“ (Ausgehend) ermöglicht Ihnen auch den Zugriff auf clusterspezifische Ressourcen, z. B. Azure Data Lake Storage Gen2 oder externe Metastores, über private Endpunkte. Die Verwendung privater Endpunkte ist für diese Ressourcen nicht vorgeschrieben. Wenn Sie jedoch beabsichtigen, private Endpunkte für diese Ressourcen zu verwenden, müssen Sie die privaten Endpunkte und DNS-Einträge konfigurieren, `before` Sie den HDInsight-Cluster erstellen. Es wird empfohlen, während der Clustererstellung alle benötigten externen SQL-Datenbanken zu erstellen und bereitzustellen, z. B. Apache Ranger, Ambari, Oozie und Hive-Metastores. Es wird vorausgesetzt, dass auf alle diese Ressourcen von innerhalb des Clustersubnetzes zugegriffen werden kann – entweder über einen eigenen privaten Endpunkt oder auf andere Weise.

Das Verwenden privater Endpunkte für Azure Key Vault wird nicht unterstützt. Bei Verwendung von Azure Key Vault für die Verschlüsselung ruhender Daten mit kundenseitig verwalteten Schlüsseln (CMK) muss der Azure Key Vault-Endpunkt aus dem HDInsight-Subnetz ohne privaten Endpunkt zugänglich sein.

Im folgenden Diagramm ist dargestellt, wie eine potenzielle HDInsight-Architektur für virtuelle Netzwerke aussehen kann, wenn `resourceProviderConnection` auf „Outbound“ (Ausgehend) festgelegt ist:

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramm: HDInsight-Architektur mit einer ausgehenden Ressourcenanbieterverbindung":::

Nachdem Sie Ihren Cluster erstellt haben, sollten Sie die richtige DNS-Auflösung einrichten. Der folgende DNS-Eintrag vom Typ CNAME (kanonischer Name) wird in der von Azure verwalteten öffentlichen DNS-Zone erstellt: `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Für den Zugriff auf den Cluster mit Cluster-FQDNs können Sie entweder die privaten IP-Adressen des internen Lastenausgleichsmoduls direkt nutzen, oder Ihre eigene private DNS-Zone verwenden, um die Clusterendpunkte je nach Ihren Anforderungen außer Kraft zu setzen. Beispielsweise können Sie die private DNS-Zone `azurehdinsight.net` verwenden. Sie können Ihre privaten IP-Adressen dann je nach Bedarf hinzufügen:

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

## <a name="enable-private-link"></a>Aktivieren von Private Link

Für Private Link (standardmäßig deaktiviert) benötigen Sie umfassende Netzwerkkenntnisse, um vor dem Erstellen eines Clusters benutzerdefinierte Routen (User Defined Routes, UDR) und Firewallregeln einrichten zu können. Die Verwendung dieser Einstellung ist optional, aber sie ist nur verfügbar, wenn die Netzwerkeigenschaft `resourceProviderConnection` wie im vorherigen Abschnitt beschrieben auf *outbound* (ausgehend) festgelegt ist.

Wenn `privateLink` auf *Enabled* (Aktiviert) festgelegt ist, werden interne [Standard-Lastenausgleichsmodule](../load-balancer/load-balancer-overview.md) erstellt, und für jedes dieser Module wird ein Azure Private Link-Dienst bereitgestellt. Der Private Link-Dienst ermöglicht Ihnen den Zugriff auf den HDInsight-Cluster von privaten Endpunkten.

Von Standard-Lastenausgleichsmodulen wird die [öffentliche Netzwerkadressenübersetzung in ausgehender Richtung](../load-balancer/load-balancer-outbound-connections.md) nicht wie bei Basic-Lastenausgleichsmodulen automatisch bereitgestellt. Sie müssen Ihre eigene NAT-Lösung, z. B. [Virtual Network NAT](../virtual-network/nat-overview.md) oder eine [Firewall](./hdinsight-restrict-outbound-traffic.md), für ausgehende Abhängigkeiten bereitstellen. Ihr HDInsight-Cluster benötigt weiterhin Zugriff auf seine ausgehenden Abhängigkeiten. Falls diese ausgehenden Abhängigkeiten nicht zulässig sind, tritt bei der Clustererstellung unter Umständen ein Fehler auf.

### <a name="prepare-your-environment"></a>Vorbereiten der Umgebung

Für die erfolgreiche Erstellung von Private Link-Diensten müssen Sie explizit die [Richtlinien für Private Link-Dienste deaktivieren](../private-link/disable-private-link-service-network-policy.md).

Das folgende Diagramm enthält ein Beispiel für die Netzwerkkonfiguration, die vor dem Erstellen eines Clusters erforderlich ist. In diesem Beispiel wird für den gesamten ausgehenden Datenverkehr per UDR der Weg über Azure Firewall [erzwungen](../firewall/forced-tunneling.md), und die erforderlichen ausgehenden Abhängigkeiten sollten in der Firewall „zugelassen“ werden, bevor ein Cluster erstellt wird. Bei Clustern mit Enterprise-Sicherheitspaket kann die Netzwerkkonnektivität mit Azure Active Directory Domain Services per VNET-Peering bereitgestellt werden.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramm: Private Link-Umgebung vor der Clustererstellung":::

Nachdem Sie das Netzwerk eingerichtet haben, können Sie einen Cluster mit einer ausgehenden Ressourcenanbieterverbindung und Private Link-Aktivierung erstellen. Dies ist in der folgenden Abbildung dargestellt. Bei dieser Konfiguration sind keine öffentlichen IP-Adressen vorhanden, und der Private Link-Dienst wird für jedes Standard-Lastenausgleichsmodul bereitgestellt.

:::image type="content" source="media/hdinsight-private-link/after-cluster-creation.png" alt-text="Diagramm: Private Link-Umgebung nach der Clustererstellung":::

### <a name="access-a-private-cluster"></a>Zugreifen auf einen privaten Cluster

Für den Zugriff auf private Cluster können Sie die privaten IP-Adressen des internen Lastenausgleichsmoduls direkt nutzen, oder Sie können Private Link-DNS-Erweiterungen und private Endpunkte verwenden. Wenn die Einstellung `privateLink` auf „Enabled“ (Aktiviert) festgelegt ist, können Sie Ihre eigenen privaten Endpunkte erstellen und die DNS-Auflösung über private DNS-Zonen konfigurieren.

In der von Azure verwalteten öffentlichen DNS-Zone `azurehdinsight.net` werden die folgenden Private Link-Einträge erstellt:

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```

Die folgende Abbildung enthält ein Beispiel für die privaten DNS-Einträge, die für den Zugriff auf den Cluster aus einem virtuellen Netzwerk benötigt werden, das nicht über ein Peering oder eine direkte Sichtverbindung mit den Lastenausgleichsmodulen des Clusters verfügt. Sie können eine private Azure-Zone verwenden, um FQDNs vom Typ `*.privatelink.azurehdinsight.net` außer Kraft zu setzen und in Ihre eigenen IP-Adressen für private Endpunkte aufzulösen.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramm: Private Link-Architektur":::

## <a name="how-to-create-clusters"></a>Wie werden Cluster erstellt?
### <a name="use-arm-template-properties"></a>Verwenden der Eigenschaften von ARM-Vorlagen

Der folgende JSON-Codeausschnitt enthält die beiden Netzwerkeigenschaften, die Sie in Ihrer ARM-Vorlage konfigurieren müssen, um einen privaten HDInsight-Cluster zu erstellen.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled" | "Disabled"
}
```

Eine vollständige Vorlage mit vielen dieser HDInsight-Sicherheitsfunktionen für Unternehmen, z. B. Private Link, finden Sie im Artikel zur [Enterprise-Sicherheitsvorlage für HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Verwenden von Azure PowerShell

Informationen zur Verwendung von PowerShell finden Sie [hier](/powershell/module/az.hdinsight/new-azhdinsightcluster?view=azps-5.1.0#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature) in dem Beispiel.

### <a name="use-azure-cli"></a>Mithilfe der Azure-Befehlszeilenschnittstelle
Informationen zur Verwendung der Azure CLI finden Sie [hier](/cli/azure/hdinsight?view=azure-cli-latest#az_hdinsight_create-examples) in dem Beispiel.

## <a name="next-steps"></a>Nächste Schritte

* [Enterprise-Sicherheitspaket für Azure HDInsight](enterprise-security-package.md)
* [Allgemeine Informationen und Richtlinien für die Unternehmenssicherheit in Azure HDInsight](./domain-joined/general-guidelines.md)