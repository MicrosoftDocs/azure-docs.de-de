---
title: 'Azure HDInsight: Erstellen eines Wörterbuchs für Clusterfehler'
description: Erfahren Sie, wie Sie Fehler beheben können, die beim Erstellen von Azure HDInsight-Clustern auftreten.
author: karkrish
ms.author: v-todmc
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 08/24/2020
ms.openlocfilehash: 97cad12ad1854df37b54ff663385fe79ca15b7c2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92543099"
---
# <a name="azure-hdinsight-cluster-creation-errors"></a>Azure HDInsight: Fehler bei der Clustererstellung

In diesem Artikel werden Lösungen für Fehler beschrieben, die beim Erstellen von Clustern auftreten können.

> [!NOTE]
> Bei den ersten drei Fehlern, die in diesem Artikel beschrieben werden, handelt es sich um Überprüfungsfehler. Sie können auftreten, wenn ein Azure HDInsight-Produkt die **CsmDocument_2_0** -Klasse verwendet.

## <a name="error-codedeploymentdocument-csmdocument_2_0-failed-the-validation"></a>Fehlercode: Fehler beim Überprüfen von DeploymentDocument „CsmDocument_2_0“

**Fehler:** „Script Action location cannot be accessed URI: \<SCRIPT ACTION URL\>“ (Zugriff auf Speicherort der Skriptaktion nicht möglich. URI:)

### <a name="error-message-1"></a>Fehlermeldung 1

Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden.“

#### <a name="cause"></a>Ursache

Der HDInsight-Dienst kann nicht auf die URL der Skriptaktion zugreifen, die Sie in der Anforderung zum Erstellen eines Clusters angegeben haben. Der Dienst empfängt die oben angegebene Fehlermeldung beim Versuch, auf die Skriptaktion zuzugreifen.

#### <a name="resolution"></a>Lösung

- Bei einer HTTP- oder HTTPS-URL überprüfen Sie die URL, indem Sie sie im Inkognitomodus eines Browsers öffnen.
- Stellen Sie bei einer WASB-URL sicher, dass das Skript in dem in der Anforderung angegebenen Speicherkonto vorhanden ist. Vergewissern Sie sich ebenfalls, dass der Speicherschlüssel für das Speicherkonto korrekt ist.
- Stellen Sie bei einer ADLS-URL sicher, dass das Skript im Speicherkonto vorhanden ist.

---

### <a name="error-message-2"></a>Fehlermeldung 2

„The given script URI \<SCRIPT_URI\> is in ADLS, but this cluster has no data lake storage principal“ (Das angegebene Skript-URI <SKRIPT_URI> befindet sich in ADLS, dieser Cluster hat jedoch keinen Data Lake Storage-Prinzipal)

#### <a name="cause"></a>Ursache

Der HDInsight-Dienst kann nicht auf die URL der Skriptaktion zugreifen, die Sie in der Anforderung zum Erstellen eines Clusters angegeben haben. Der Dienst empfängt die oben angegebene Fehlermeldung beim Versuch, auf die Skriptaktion zuzugreifen.

#### <a name="resolution"></a>Lösung

Fügen Sie dem Cluster das entsprechende Azure Data Lake Store Gen1-Konto hinzu. Fügen Sie dem Cluster auch den Dienstprinzipal hinzu, der auf das Data Lake Storage Gen1-Konto zugreift.

---

### <a name="error-message-3"></a>Fehlermeldung 3

„Die in der Anforderung angegebene VM-Größe ‚\<CUSTOMER_SPECIFIED_VM_SIZE\>‘ ist ungültig oder wird für die Rolle ‚\<ROLE\>‘ nicht unterstützt. Gültige Werte sind: \<VALID_VM_SIZE_FOR_ROLE\>“.

#### <a name="cause"></a>Ursache

Die von Ihnen angegebene VM-Größe ist für die Rolle nicht zulässig. Dieser Fehler kann auftreten, wenn der Wert für die VM-Größe nicht erwartungsgemäß funktioniert oder für die Computerrolle nicht geeignet ist.

#### <a name="resolution"></a>Lösung

In der Fehlermeldung sind die gültigen Werte für die VM-Größe aufgeführt. Wählen Sie einen dieser Werte aus, und wiederholen Sie die Anforderung zum Erstellen eines Clusters.

---

## <a name="error-codeinvalidvirtualnetworkid"></a>Fehlercode: InvalidVirtualNetworkId  

### <a name="error"></a>Fehler

„Die VirtualNetworkId ist ungültig. VirtualNetworkId ‚\<USER_VIRTUALNETWORKID\>‘*“

### <a name="cause"></a>Ursache

Der bei der Clustererstellung angegebene Wert für **VirtualNetworkId** weist nicht das richtige Format auf.

### <a name="resolution"></a>Lösung

Stellen Sie sicher, dass **VirtualNetworkId** und die Subnetzwerte im richtigen Format angegeben sind. So erhalten Sie den Wert für **VirtualNetworkId** :

1. Öffnen Sie das Azure-Portal.
1. Wählen Sie Ihr virtuelles Netzwerk aus.
1. Wählen Sie das Menüelement **Eigenschaften** aus. Der **ResourceID** -Eigenschaftswert entspricht dem Wert für **VirtualNetworkId** .

Hier ein Beispiel für die ID eines virtuellen Netzwerks:

„/subscriptions/c15fd9b8-e2b8-1d4e-aa85-2e668040233b/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/myvnet“

---

## <a name="error-code-customizationfailederrorcode"></a>Fehlercode: CustomizationFailedErrorCode

### <a name="error"></a>Fehler

„Cluster deployment failed due to an error in the custom script action. (Fehler bei Clusterbereitstellung aufgrund eines Fehlers in der benutzerdefinierten Skriptaktion.) Failed Actions: \<SCRIPT_NAME\>, Please go to Ambari UI to further debug the failure.“ (Fehlgeschlagene Aktionen: <SKRIPTNAME>. Debuggen Sie den Fehler auf der Ambari-Benutzeroberfläche)

### <a name="cause"></a>Ursache

Das benutzerdefinierte Skript, das Sie bei der Anforderung zum Erstellen eines Clusters angegeben haben, wird nach der erfolgreichen Bereitstellung des Clusters ausgeführt. Dieser Fehlercode gibt an, dass während der Ausführung des benutzerdefinierten Skripts mit dem Namen \<SCRIPT_NAME\> ein Fehler aufgetreten ist.

### <a name="resolution"></a>Lösung

Da es sich bei dem Skript um Ihr benutzerdefiniertes Skript handelt, empfiehlt es sich, das Problem zu beheben und das Skript bei Bedarf erneut auszuführen. Überprüfen Sie die Protokolle im Ordner „/var/lib/ambari-agent/*“, um den Skriptfehler zu beheben. Alternativ können Sie auch die Seite **Operations** (Vorgänge) auf der Ambari-Benutzeroberfläche öffnen und dann den Vorgang **run_customscriptaction** auswählen, um die Fehlerdetails anzuzeigen.

---

## <a name="error-codeinvaliddocumenterrorcode"></a>Fehlercode: InvalidDocumentErrorCode

### <a name="error"></a>Fehler

„Die \<META_STORE_TYPE\>-Metastore-Schemaversion \<METASTORE_MAJOR_VERSION\> in der Datenbank \<DATABASE_NAME\> ist mit der Clusterversion \<CLUSTER_VERSION\> nicht kompatibel“.

### <a name="cause"></a>Ursache

Der benutzerdefinierte Metastore ist nicht mit der ausgewählten HDInsight-Clusterversion kompatibel. HDInsight 4.0-Cluster unterstützen derzeit nur Metastore, Version 3.0 und höher, während HDInsight 3.6-Cluster keine Unterstützung für Metastore, Version 3.0 und höher, bieten.

### <a name="resolution"></a>Lösung

Verwenden Sie nur Metastore-Versionen, die Ihre HDInsight-Clusterversion unterstützt. Wenn Sie keinen benutzerdefinierten Metastore angeben, erstellt HDInsight intern einen Metastore und löscht diesen beim Löschen des Clusters.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Fehlercode: FailedToConnectWithClusterErrorCode 

### <a name="error"></a>Fehler

„Unable to connect to cluster management endpoint to perform scaling operation. Verify that network security rules are not blocking external access to the cluster, and that the cluster manager (Ambari) UI can be successfully accessed. (Zum Ausführen des Skalierungsvorgangs kann keine Verbindung mit dem Clusterverwaltungsendpunkt hergestellt werden. Vergewissern Sie sich, dass der externe Zugriff auf den Cluster nicht durch Netzwerksicherheitsregeln blockiert ist und dass der Zugriff auf die Cluster Manager-Benutzeroberfläche [Ambari] möglich ist.)“

### <a name="cause"></a>Ursache

Eine Firewallregel in der Netzwerksicherheitsgruppe (NSG) blockiert die Kommunikation des Clusters mit wichtigen Integritäts- und Verwaltungsdiensten in Azure.

### <a name="resolution"></a>Lösung

Wenn Sie Netzwerksicherheitsgruppen zum Steuern des Netzwerkdatenverkehrs verwenden möchten, führen Sie vor der Installation von HDInsight folgende Aktionen aus:

- Identifizieren Sie die Azure-Region, die Sie für HDInsight verwenden möchten.
- Identifizieren Sie die IP-Adressen, die für HDInsight erforderlich sind. Weitere Informationen finden Sie unter [HDInsight management IP addresses (IP-Adressen für die HDInsight-Verwaltung)](./hdinsight-management-ip-addresses.md).
  - Erstellen oder ändern Sie die Netzwerksicherheitsgruppen für das Subnetz, in dem Sie HDInsight installieren möchten.
  - Lassen Sie für Netzwerksicherheitsgruppen eingehenden Datenverkehr über Port 443 für die IP-Adressen zu. Durch diese Konfiguration wird sichergestellt, dass HDInsight-Verwaltungsdienste den Cluster außerhalb des virtuellen Netzwerks erreichen können.

---

## <a name="error-code-storagepermissionsblockedformsi"></a>Fehlercode: StoragePermissionsBlockedForMsi

### <a name="error"></a>Fehler

„The Managed Identity does not have permissions on the storage account. (Die verwaltete Identität weist keine Berechtigungen für das Speicherkonto auf.) Please verify that 'Storage Blob Data Owner' role is assigned to the Managed Identity for the storage account. Storage: /subscriptions/ \<Subscription ID\> /resourceGroups/\< Resource Group Name\> /providers/Microsoft.Storage/storageAccounts/ \<Storage Account Name\>, Managed Identity: /subscriptions/ \<Subscription ID\> /resourceGroups/ /\< Resource Group Name\> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/ \<User Managed Identity Name\>"

### <a name="cause"></a>Ursache

Sie haben nicht die erforderlichen Berechtigungen zum Verwalten der Identität bereitgestellt. Die benutzerseitig zugewiesene verwaltete Identität weist nicht die Rolle „Mitwirkender an Storage-Blobdaten“ für das Azure Data Lake Storage Gen2-Speicherkonto auf.

### <a name="resolution"></a>Lösung

1. Öffnen Sie das Azure-Portal.
1. Wechseln Sie zum Speicherkonto.
1. Prüfen Sie die Einstellung unter **Zugriffssteuerung (IAM)** .
1. Stellen Sie sicher, dass dem Benutzer die Rolle „Mitwirkender an Storage-Blobdaten“ oder „Besitzer von Speicherblobdaten“ zugewiesen wurde.

Weitere Informationen finden Sie unter [Festlegen von Berechtigungen für verwaltete Identitäten im Data Lake Storage Gen2-Konto](hdinsight-hadoop-use-data-lake-storage-gen2.md).

---

## <a name="error-code-invalidnetworksecuritygroupsecurityrules"></a>Fehlercode: InvalidNetworkSecurityGroupSecurityRules

### <a name="error"></a>Fehler

"The security rules in the Network Security Group /subscriptions/\<SubscriptionID\>/resourceGroups/<Resource Group name\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\> configured with subnet /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> does not allow required inbound and/or outbound connectivity. („Die Sicherheitsregeln in der Netzwerksicherheitsgruppe /subscriptions/\<SubscriptionID\>/resourceGroups/<Resource Group name\> default/providers/Microsoft.Network/networkSecurityGroups/\<Network Security Group Name\>, die mit dem Subnetz /subscriptions/\<SubscriptionID\>/resourceGroups/\<Resource Group name\> RG-westeurope-vnet-tomtom-default/providers/Microsoft.Network/virtualNetworks/\<Virtual Network Name\>/subnets/\<Subnet Name\> konfiguriert ist, lässt keine eingehenden und/oder ausgehenden Verbindungen zu) For more information, please visit [Plan a virtual network for Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md), or contact support. (Weitere Informationen finden Sie unter „Planen eines virtuellen Netzwerks für Azure HDInsight“, oder wenden Sie sich an den Support.)“

### <a name="cause"></a>Ursache

Wenn der beim HDInsight-Cluster eingehende Datenverkehr über Netzwerksicherheitsgruppen oder benutzerdefinierte Routen (UDRs) gesteuert wird, müssen Sie sicherstellen, dass Ihr Cluster mit wichtigen Integritäts- und Verwaltungsdiensten in Azure kommunizieren kann.

### <a name="resolution"></a>Lösung

Wenn Sie Netzwerksicherheitsgruppen zum Steuern des Netzwerkdatenverkehrs verwenden möchten, führen Sie vor der Installation von HDInsight folgende Aktionen aus:

- Identifizieren Sie die Azure-Region, die Sie für HDInsight verwenden möchten, und erstellen Sie eine Liste mit sicheren IP-Adressen für die Region. Weitere Informationen finden Sie unter [Integritäts- und Verwaltungsdienste: Bestimmte Regionen](./hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions).
- Identifizieren Sie die IP-Adressen, die für HDInsight erforderlich sind. Weitere Informationen finden Sie unter  [HDInsight-Verwaltungs-IP-Adressen](./hdinsight-management-ip-addresses.md).
- Erstellen oder ändern Sie die Netzwerksicherheitsgruppen für das Subnetz, in dem Sie HDInsight installieren möchten. Lassen Sie für Netzwerksicherheitsgruppen eingehenden Datenverkehr über Port 443 für die IP-Adressen zu. Durch diese Konfiguration wird sichergestellt, dass HDInsight-Verwaltungsdienste den Cluster außerhalb des virtuellen Netzwerks erreichen können.

---

## <a name="error-code-cluster-setup-failed-to-install-components-on-one-or-more-hosts"></a>Fehlercode: Cluster setup failed to install components on one or more hosts (Bei der Clustereinrichtung konnten Komponenten auf mindestens einem Host nicht erstellt werden.)

### <a name="error"></a>Fehler

„Cluster setup failed to install components on one or more hosts. (Bei der Clustereinrichtung konnten auf mindestens einem Host keine Komponenten installiert werden.) Please retry your request. (Wiederholen Sie die Anforderung.)“

### <a name="cause"></a>Ursache 

Dieser Fehler wird in der Regel generiert, wenn ein vorübergehendes Problem vorliegt oder Azure ausfällt.

### <a name="resolution"></a>Lösung

Überprüfen Sie die Seite [Azure-Status](https://status.azure.com) auf Azure-Ausfälle, die sich auf die Clusterbereitstellung auswirken könnten. Wenn keine Ausfälle angegeben sind, führen Sie die Clusterbereitstellung erneut durch.

---

## <a name="error-code-failedtoconnectwithclustererrorcode"></a>Fehlercode: FailedToConnectWithClusterErrorCode

### <a name="error"></a>Fehler

Unable to connect to cluster management endpoint. (Es konnte keine Verbindung mit dem Verwaltungsendpunkt des Clusters hergestellt werden.) Versuchen Sie es später noch mal.

### <a name="cause"></a>Ursache

Der HDInsight-Dienst kann beim Erstellen des Clusters keine Verbindung mit dem Cluster herstellen.

### <a name="resolution"></a>Lösung

Wenn Sie benutzerdefinierte VNet-Netzwerksicherheitsgruppen (NSGs) und benutzerdefinierte Routen (UDRs) verwenden, stellen Sie sicher, dass Ihr Cluster mit den HDInsight-Verwaltungsdiensten kommunizieren kann. Weitere Informationen finden Sie unter [HDInsight-Verwaltungs-IP-Adressen](./hdinsight-management-ip-addresses.md).

---

## <a name="error-code-deployments-failed-due-to-policy-violation-resource-resource-uri-was-disallowed-by-policy-policy-identifiers-policyassignmentnamepolicy-name-idprovidersmicrosoftmanagementmanagementgroupsmanagement-group-name-providersmicrosoftauthorizationpolicyassignmentspolicy-namepolicydefinition-policy-definition"></a>Fehlercode: Deployments failed due to policy violation: 'Resource '<Resource URI>' was disallowed by policy. (Fehler bei Bereitstellungen aufgrund einer Richtlinienverletzung: Die Ressource wurde durch eine Richtlinie abgelehnt.) Richtlinienbezeichner: '[{"policyAssignment":{"name":"<Policy Name> ","id":"/providers/Microsoft.Management/managementGroups/<Management Group Name> providers/Microsoft.Authorization/policyAssignments/<Policy Name>"},"policyDefinition": <Policy Definition>

### <a name="cause"></a>Ursache

Abonnementbasierte Azure-Richtlinien können die Erstellung öffentlicher IP-Adressen verhindern. Zum Erstellen von HDInsight-Clustern sind zwei öffentliche IP-Adressen erforderlich.

Die folgenden Richtlinien wirken sich in der Regel auf die Clustererstellung aus:

* Richtlinien, die die Erstellung von IP-Adressen und Lastenausgleichsmodulen innerhalb des Abonnements verhindern
* Richtlinie, die das Erstellen von Speicherkonten verhindert
* Richtlinie, die das Löschen von Netzwerkressourcen wie IP-Adressen oder Lastenausgleichsmodule verhindert

### <a name="resolution"></a>Lösung

Löschen oder deaktivieren Sie die auf Abonnements basierende Azure-Richtlinienzuweisung beim Erstellen eines HDInsight-Clusters.

---

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung bei der Clustererstellung finden Sie unter [Behandeln von Fehlern bei der Clustererstellung mit Azure HDInsight](./hadoop/hdinsight-troubleshoot-cluster-creation-fails.md).