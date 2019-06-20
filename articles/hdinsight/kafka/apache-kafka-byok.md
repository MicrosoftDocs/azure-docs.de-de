---
title: Bring Your Own Key für Apache Kafka in Azure HDInsight
description: In diesem Artikel wird beschrieben, wie Sie mit Ihrem eigenen Schlüssel aus Azure Key Vault Daten verschlüsseln, die in Apache Kafka in Azure HDInsight gespeichert sind.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 6108bfd9e39b37507ec7e113bf2c489e890f0ca0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233561"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Bring Your Own Key für Apache Kafka in Azure HDInsight

Azure HDInsight beinhaltet BYOK-Unterstützung (Bring Your Own Key) für Apache Kafka. Dank dieser Funktion können Sie die Verantwortung und die Verwaltung für die Schlüssel übernehmen, die zum Verschlüsseln ruhender Daten verwendet werden.

Alle verwalteten Datenträger in HDInsight werden mit der Speicherdienstverschlüsselung (Storage Service Encryption, SSE) von Azure geschützt. Die Daten auf diesen Datenträgern werden standardmäßig mit von Microsoft verwalteten Schlüsseln verschlüsselt. Bei der Aktivierung von BYOK geben Sie den Verschlüsselungsschlüssel für HDInsight für die Verwendung und Verwaltung mit Azure Key Vault an.

Die BYOK-Verschlüsselung besteht aus einem Schritt, der bei der Clustererstellung ohne zusätzliche Kosten ausgeführt wird. Sie müssen bei der Erstellung Ihres Clusters lediglich HDInsight als verwaltete Identität bei Azure Key Vault registrieren und den Verschlüsselungsschlüssel hinzufügen.

Alle Nachrichten an den Kafka-Cluster (einschließlich von Kafka verwaltete Replikate) werden mit einem symmetrischen Datenverschlüsselungsschlüssel (Data Encryption Key, DEK) verschlüsselt. Der DEK wird mit dem Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK) aus Ihrem Schlüsseltresor geschützt. Die Ver- und Entschlüsselung wird vollständig von Azure HDInsight übernommen. 

Sie können für das sichere Rotieren der Schlüssel im Schlüsseltresor das Azure-Portal oder die Azure CLI verwenden. Beim Rotieren eines Schlüssels beginnt der HDInsight Kafka-Cluster innerhalb weniger Minuten mit der Verwendung des neuen Schlüssels. Aktivieren Sie die Schlüsselschutzfunktion „Soft Delete“ (Vorläufig löschen) zum Schutz vor Ransomware und versehentlichem Löschen. Schlüsseltresore ohne diese Schutzfunktion werden nicht unterstützt.

## <a name="get-started-with-byok"></a>Erste Schritte mit BYOK
Zum Erstellen eines Kafka-Clusters mit BYOK führen Sie die folgenden Schritte aus:
1. Erstellen verwalteter Identitäten für Azure-Ressourcen
2. Einrichten von Azure Key Vault mit den Schlüsseln
3. Erstellen von HDInsight-Kafka-Clustern mit aktiviertem BYOK
4. Rotieren des Verschlüsselungsschlüssels

## <a name="create-managed-identities-for-azure-resources"></a>Erstellen verwalteter Identitäten für Azure-Ressourcen

   Erstellen Sie für die Authentifizierung bei Key Vault mit dem [Azure-Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) oder der [Azure-Befehlszeilenschnittstelle](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) eine benutzerseitig zugewiesene verwaltete Identität. Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Verwaltete Identitäten in Azure HDInsight](../hdinsight-managed-identities.md). Azure Active Directory ist für verwaltete Identitäten und BYOK in Kafka erforderlich, das Enterprise-Sicherheitspaket (Enterprise Security Package, ESP) jedoch nicht. Achten Sie darauf, die Ressourcen-ID der verwalteten Identität zu speichern, da Sie sie später zur Key Vault-Zugriffsrichtlinie hinzufügen.

   ![Erstellen einer benutzerseitig zugewiesenen verwalteten Identität im Azure-Portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Einrichten des Schlüsseltresors mit den Schlüsseln

   HDInsight unterstützt nur Azure Key Vault. Falls Sie einen eigenen Schlüsseltresor besitzen, können Sie Ihre Schlüssel in Azure Key Vault importieren. Denken Sie daran, dass für die Schlüssel „Vorläufig löschen“ aktiviert sein muss. Die Funktion für vorläufiges Löschen ist über REST, .NET/C#, PowerShell und die Azure-Befehlszeilenschnittstelle verfügbar.

   1. Befolgen Sie zum Erstellen eines neuen Schlüsseltresors die Schnellstartanleitung für [Azure Key Vault](../../key-vault/key-vault-overview.md). Weitere Informationen zum Importieren vorhandener Schlüsseln finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Aktivieren Sie „Vorläufig löschen“ für den Schlüsseltresor mithilfe des CLI-Befehls [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update).
        ```Azure CLI az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Apache Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, Ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access Ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
