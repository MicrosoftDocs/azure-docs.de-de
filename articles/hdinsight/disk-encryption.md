---
title: Doppelte Verschlüsselung für ruhende Daten
titleSuffix: Azure HDInsight
description: In diesem Artikel werden die beiden Verschlüsselungsebenen beschrieben, die in Azure HDInsight-Clustern für ruhende Daten zur Verfügung stehen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: a9a90fbb2eedd6db2873d4ac2a5fea94c05c7eed
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005655"
---
# <a name="azure-hdinsight-double-encryption-for-data-at-rest"></a>Doppelte Verschlüsselung für ruhende Daten in Azure HDInsight

In diesem Artikel werden die Methoden für die Verschlüsselung ruhender Daten in Azure HDInsight-Clustern erläutert. Die Verschlüsselung ruhender Daten bezieht sich auf die Verschlüsselung auf verwalteten Datenträgern (Daten-, Betriebssystem- und temporären Datenträgern), die an virtuelle Computer im HDInsight-Cluster angefügt sind. 

In diesem Dokument wird nicht auf Daten eingegangen, die in Ihrem Azure Storage-Konto gespeichert sind. Ihre Cluster verfügen möglicherweise über mindestens ein angefügtes Azure Storage-Konto, bei dem die Verschlüsselungsschlüssel auch von Microsoft oder kundenseitig verwaltet werden können, der Verschlüsselungsdienst ist jedoch anders. Weitere Informationen zur Azure Storage-Verschlüsselung finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md).

## <a name="introduction"></a>Einführung

In Azure gibt drei Haupttypen verwalteter Datenträger: „normale“ Datenträger, Betriebssystem-Datenträger und temporäre Datenträger. Weitere Informationen zu den verschiedenen Typen verwalteter Datenträger finden Sie in der [Einführung in Azure Managed Disks](../virtual-machines/managed-disks-overview.md). 

HDInsight unterstützt auf zwei Ebenen mehrere Verschlüsselungstypen:

- Serverseitige Verschlüsselung (Server Side Encryption, SSE), die vom Speicherdienst durchgeführt wird. In HDInsight wird SSE verwendet, um Betriebssystem- und normale Datenträger zu verschlüsseln. Es ist standardmäßig aktiviert. SSE ist ein Verschlüsselungsdienst der Ebene 1.
- Verschlüsselung auf dem Host mit einem von der Plattform verwalteten Schlüssel: (Ähnlich wie bei SSE) wird diese Art der Verschlüsselung vom Speicherdienst übernommen. Sie gilt jedoch nur für temporäre Datenträger und ist nicht standardmäßig aktiviert. Die Verschlüsselung auf dem Host ist auch ein Verschlüsselungsdienst der Ebene 1.
- Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel: Dieser Verschlüsselungstyp eignet sich für normale und temporäre Datenträger. Er ist standardmäßig nicht aktiviert und erfordert, dass der Kunde seinen eigenen Schlüssel über Azure Key Vault bereitstellt. Verschlüsselung ruhender Daten ist ein Verschlüsselungsdienst der Ebene 2.

Eine Zusammenfassung dieser Typen finden Sie in der folgenden Tabelle.

|Clustertyp |Betriebssystemdatenträger (verwalteter Datenträger) |Datenträger für Daten (verwalteter Datenträger) |Temporärer Datenträger (lokale SSD) |
|---|---|---|---|
|Kafka, HBase mit beschleunigten Schreibvorgängen|Ebene 1: Standardmäßige [SSE-Verschlüsselung](../virtual-machines/managed-disks-overview.md#encryption)|Ebene 1: Standardmäßige [SSE-Verschlüsselung](../virtual-machines/managed-disks-overview.md#encryption), Ebene 2: Optionale Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel|Ebene 1: Optionale Verschlüsselung auf dem Host mithilfe von plattformseitig verwaltetem Schlüssel, Ebene 2: Optionale Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel|
|Alle anderen Cluster (Spark, Interactive, Hadoop, HBase ohne beschleunigte Schreibvorgänge)|Ebene 1: Standardmäßige [SSE-Verschlüsselung](../virtual-machines/managed-disks-overview.md#encryption)|–|Ebene 1: Optionale Verschlüsselung auf dem Host mithilfe von plattformseitig verwaltetem Schlüssel, Ebene 2: Optionale Verschlüsselung ruhender Daten mit kundenseitig verwaltetem Schlüssel|

## <a name="encryption-at-rest-using-customer-managed-keys"></a>Verschlüsselung ruhender Daten mithilfe von kundenseitig verwalteten Schlüsseln

Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln ist ein Prozess mit nur einem Schritt, der bei der Clustererstellung ohne zusätzliche Kosten ausgeführt wird. Sie müssen bei der Erstellung Ihres Clusters lediglich eine verwaltete Identität in Azure Key Vault autorisieren und den Verschlüsselungsschlüssel hinzufügen.

Sowohl normale als auch temporäre Datenträger auf den einzelnen Knoten des Clusters werden mit einem symmetrischen Datenverschlüsselungsschlüssel (Data Encryption Key, DEK) verschlüsselt. Der DEK wird mit dem Schlüssel für die Schlüsselverschlüsselung (Key Encryption Key, KEK) aus Ihrem Schlüsseltresor geschützt. Die Ver- und Entschlüsselung wird vollständig von Azure HDInsight übernommen.

Für Betriebssystem-Datenträger, die an die Cluster-VMs angefügt sind, ist nur eine Verschlüsselungsebene (plattformseitig verwalteter Schlüssel) verfügbar. Es wird empfohlen, dass Kunden das Kopieren sensibler Daten auf Betriebssystem-Datenträger vermeiden, wenn für ihre Szenarien eine Verschlüsselung mit kundenseitig verwaltetem Schlüssel erforderlich ist.

Wenn die Schlüsseltresor-Firewall für den Schlüsseltresor aktiviert ist, in dem der Schlüssel für die Datenträgerverschlüsselung gespeichert ist, müssen die IP-Adressen der regionalen HDInsight-Ressourcenanbieter für die Region, in der der Cluster bereitgestellt wird, der Konfiguration für die Schlüsseltresor-Firewall hinzugefügt werden. Dies ist erforderlich, weil HDInsight kein vertrauenswürdiger Azure Key Vault-Dienst ist.

Sie können für das sichere Rotieren der Schlüssel im Schlüsseltresor das Azure-Portal oder die Azure CLI verwenden. Beim Rotieren eines Schlüssels beginnt der HDInsight-Cluster innerhalb weniger Minuten mit der Verwendung des neuen Schlüssels. Aktivieren Sie die Schlüsselschutzfunktion [Vorläufiges Löschen](../key-vault/general/soft-delete-overview.md) zum Schutz vor Ransomware und versehentlichem Löschen. Schlüsseltresore ohne diese Schutzfunktion werden nicht unterstützt.

### <a name="get-started-with-customer-managed-keys"></a>Erste Schritte mit von Kunden verwalteten Schlüsseln

Die Erstellung eines HDInsight-Clusters mit aktiviertem kundenseitig verwaltetem Schlüssel umfasst folgende Schritte:

1. Erstellen verwalteter Identitäten für Azure-Ressourcen
1. Azure Key Vault erstellen
1. Erstellen eines Schlüssels
1. Erstellen einer Zugriffsrichtlinie
1. Erstellen eines HDInsight-Clusters mit aktiviertem kundenseitig verwaltetem Schlüssel
1. Rotieren des Verschlüsselungsschlüssels

Jeder Schritt wird in einem der folgenden Abschnitte ausführlich erläutert.

### <a name="create-managed-identities-for-azure-resources"></a>Erstellen verwalteter Identitäten für Azure-Ressourcen

Erstellen Sie eine benutzerseitig zugewiesene verwaltete Identität, um sich bei Key Vault zu authentifizieren.

Die einzelnen Schritte finden Sie unter [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Weitere Informationen zur Funktionsweise verwalteter Identitäten in Azure HDInsight finden Sie unter [Verwaltete Identitäten in Azure HDInsight](hdinsight-managed-identities.md). Achten Sie darauf, die Ressourcen-ID der verwalteten Identität zu speichern, da Sie sie später zur Key Vault-Zugriffsrichtlinie hinzufügen.

### <a name="create-azure-key-vault"></a>Azure Key Vault erstellen

Erstellen eines Schlüsseltresors Die einzelnen Schritte finden Sie unter [Erstellen eines Tresors](../key-vault/general/quick-create-portal.md).

HDInsight unterstützt nur Azure Key Vault. Falls Sie einen eigenen Schlüsseltresor besitzen, können Sie Ihre Schlüssel in Azure Key Vault importieren. Denken Sie daran, dass für den Schlüsseltresor das **vorläufige Löschen** aktiviert sein muss. Weitere Informationen zum Importieren vorhandener Schlüsseln finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](../key-vault/general/about-keys-secrets-certificates.md).

### <a name="create-key"></a>Erstellen eines Schlüssels

1. Navigieren Sie in Ihrem neuen Schlüsseltresor zu **Einstellungen** > **Schlüssel** >  **+ Generieren/Importieren**.

    ![Generieren eines neuen Schlüssels in Azure Key Vault](./media/disk-encryption/create-new-key.png "Generieren eines neuen Schlüssels in Azure Key Vault")

1. Geben Sie einen Namen an, und wählen Sie dann **Erstellen** aus. Behalten Sie den standardmäßigen **Schlüsseltyp** (Option **RSA**) bei.

    ![Generieren des Schlüsselnamens](./media/disk-encryption/create-key.png "Generieren des Schlüsselnamens")

1. Wenn Sie zur Seite **Schlüssel** zurückkehren, wählen Sie den erstellten Schlüssel aus.

    ![Key Vault-Schlüsselliste](./media/disk-encryption/key-vault-key-list.png)

1. Wählen Sie die Version aus, um die Seite **Schlüsselversion** zu öffnen. Wenn Sie Ihren eigenen Schlüssel für die Verschlüsselung des HDInsight-Clusters verwenden, müssen Sie die Schlüssel-URI angeben. Kopieren Sie den **Schlüsselbezeichner**, und speichern sie ihn, bis Sie zum Erstellen des Clusters bereit sind.

    ![Abrufen des Schlüsselbezeichners](./media/disk-encryption/get-key-identifier.png)

### <a name="create-access-policy"></a>Erstellen einer Zugriffsrichtlinie

1. Navigieren Sie in Ihrem neuen Schlüsseltresor zu **Einstellungen** > **Zugriffsrichtlinien** >  **+ Zugriffsrichtlinie hinzufügen**.

    ![Erstellen einer neuen Azure Key Vault-Zugriffsrichtlinie](./media/disk-encryption/key-vault-access-policy.png)

1. Geben Sie auf der Seite **Zugriffsrichtlinie hinzufügen** die folgenden Informationen an:

    |Eigenschaft |BESCHREIBUNG|
    |---|---|
    |Schlüsselberechtigungen|Wählen Sie **Abrufen**, **Schlüssel entpacken** und **Schlüssel packen** aus.|
    |Berechtigungen für Geheimnis|Wählen Sie **Abrufen**, **Festlegen** und **Löschen** aus.|
    |Prinzipal auswählen|Wählen Sie die benutzerseitig zugewiesene verwaltete Identität aus, die Sie zuvor erstellt haben.|

    ![Festlegen der Option „Prinzipal auswählen“ für die Azure Key Vault-Zugriffsrichtlinie](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie **Speichern** aus.

    ![Speichern einer Azure Key Vault-Zugriffsrichtlinie](./media/disk-encryption/add-key-vault-access-policy-save.png)

### <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Erstellen eines Clusters mit Datenträgerverschlüsselung mit kundenseitig verwalteten Schlüsseln

Sie können nun einen neuen HDInsight-Cluster erstellen. Kundenseitig verwaltete Schlüssel können ausschließlich im Rahmen der Clustererstellung auf neue Cluster angewendet werden. Die Verschlüsselung kann nicht aus Clustern mit kundenseitig verwalteten Schlüsseln entfernt werden, und der kundenseitig verwaltete Schlüssel kann keinen vorhandenen Clustern hinzugefügt werden.

#### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Geben Sie während der Clustererstellung den vollständigen **Schlüsselbezeichner** (einschließlich Schlüsselversion) an. Beispiel: `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Darüber hinaus müssen Sie die verwaltete Identität dem Cluster zuweisen und den Schlüssel-URI angeben.

![Neuen Cluster erstellen](./media/disk-encryption/create-cluster-portal.png)

#### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel wird mithilfe der Azure-Befehlszeilenschnittstelle ein neuer Apache Spark-Cluster mit aktivierter Datenträgerverschlüsselung erstellt. Weitere Informationen finden Sie in der Dokumentation der Azure-Befehlszeilenschnittstelle unter [az hdinsight create](/cli/azure/hdinsight#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

#### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Im folgenden Beispiel wird mithilfe einer Azure Resource Manager-Vorlage ein neuer Apache Spark-Cluster mit aktivierter Datenträgerverschlüsselung erstellt. Weitere Informationen finden Sie unter [Was sind ARM-Vorlagen?](../azure-resource-manager/templates/overview.md).

In diesem Beispiel wird PowerShell verwendet, um die Vorlage aufzurufen.

```powershell
$templateFile = "azuredeploy.json"
$ResourceGroupName = "MyResourceGroup"
$clusterName = "MyCluster"
$password = ConvertTo-SecureString 'HttpPassword1234!' -AsPlainText -Force
$diskEncryptionVaultUri = "https://MyKeyVault.vault.azure.net"
$diskEncryptionKeyName = "SparkClusterKey"
$diskEncryptionKeyVersion = "00000000000000000000000000000000"
$managedIdentityName = "MyMSI"

New-AzResourceGroupDeployment `
  -Name mySpark `
  -TemplateFile $templateFile `
  -ResourceGroupName $ResourceGroupName `
  -clusterName $clusterName `
  -clusterLoginPassword $password `
` -sshPassword $password `
  -diskEncryptionVaultUri $diskEncryptionVaultUri `
  -diskEncryptionKeyName $diskEncryptionKeyName `
  -diskEncryptionKeyVersion $diskEncryptionKeyVersion `
  -managedIdentityName $managedIdentityName
```

Der Inhalt der Resource Manager-Vorlage `azuredeploy.json`:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "0.9.0.0",
  "parameters": {
    "clusterName": {
      "type": "string",
      "metadata": {
        "description": "The name of the HDInsight cluster to create."
      }
    },
    "clusterLoginUserName": {
      "type": "string",
      "defaultValue": "admin",
      "metadata": {
        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
      }
    },
    "clusterLoginPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where all azure resources will be deployed."
      }
    },
    "sshUserName": {
      "type": "string",
      "defaultValue": "sshuser",
      "metadata": {
        "description": "These credentials can be used to remotely access the cluster."
      }
    },
    "sshPassword": {
      "type": "securestring",
      "metadata": {
        "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
      }
    },
    "headNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D12_v2",
      "metadata": {
        "description": "The VM size of the head nodes."
      }
    },
    "workerNodeSize": {
      "type": "string",
      "defaultValue": "Standard_D13_v2",
      "metadata": {
        "description": "The VM size of the worker nodes."
      }
    },
    "diskEncryptionVaultUri": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault DNSname."
      }
    },
    "diskEncryptionKeyName": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key name."
      }
    },
    "diskEncryptionKeyVersion": {
      "type": "string",
      "metadata": {
        "description": "The Key Vault key version for the selected key."
      }
    },
    "managedIdentityName": {
      "type": "string",
      "metadata": {
        "description": "The user-assigned managed identity."
      }
    }
  },
  "variables": {
    "defaultStorageAccount": {
      "name": "[uniqueString(resourceGroup().id)]",
      "type": "Standard_LRS"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('defaultStorageAccount').name]",
      "location": "[parameters('location')]",
      "apiVersion": "2019-06-01",
      "sku": {
        "name": "[variables('defaultStorageAccount').type]"
      },
      "kind": "Storage",
      "properties": {}
    },
    {
      "apiVersion": "2018-06-01-preview",
      "name": "[parameters('clusterName')]",
      "type": "Microsoft.HDInsight/clusters",
      "location": "[parameters('location')]",
      "properties": {
        "clusterVersion": "3.6",
        "osType": "Linux",
        "tier": "standard",
        "clusterDefinition": {
          "kind": "spark",
          "componentVersion": {
            "Spark": "2.3"
          },
          "configurations": {
            "gateway": {
              "restAuthCredential.isEnabled": true,
              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
            }
          }
        },
        "storageProfile": {
          "storageaccounts": [
            {
              "name": "[replace(replace(reference(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').primaryEndpoints.blob,'https://',''),'/','')]",
              "isDefault": true,
              "container": "[parameters('clusterName')]",
              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccount').name), '2019-06-01').keys[0].value]"
            }
          ]
        },
        "computeProfile": {
          "roles": [
            {
              "name": "headnode",
              "minInstanceCount": 1,
              "targetInstanceCount": 2,
              "hardwareProfile": {
                "vmSize": "[parameters('headNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            },
            {
              "name": "workernode",
              "targetInstanceCount": 1,
              "hardwareProfile": {
                "vmSize": "[parameters('workerNodeSize')]"
              },
              "osProfile": {
                "linuxOperatingSystemProfile": {
                  "username": "[parameters('sshUserName')]",
                  "password": "[parameters('sshPassword')]"
                },
              },
            }
          ]
        },
        "minSupportedTlsVersion": "1.2",
        "diskEncryptionProperties": {
          "vaultUri": "[parameters('diskEncryptionVaultUri')]",
          "keyName": "[parameters('diskEncryptionKeyName')]",
          "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
          "msiResourceId": "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]"
        }
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('managedIdentityName'))]": {}
        }
      }
    }
  ]
}
```

### <a name="rotating-the-encryption-key"></a>Rotieren des Verschlüsselungsschlüssels

In manchen Szenarien müssen unter Umständen die vom HDInsight-Cluster verwendeten Verschlüsselungsschlüssel geändert werden, nachdem der Cluster bereits erstellt wurde. Hierzu können Sie das Portal verwenden. Für diesen Vorgang muss der Cluster sowohl auf den aktuellen Schlüssel als auch auf den vorgesehenen neuen Schlüssel zugreifen können, andernfalls schlägt der Vorgang zum Rotieren des Schlüssels fehl.

#### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Zum Rotieren des Schlüssels benötigen Sie die Basis-URI des Schlüsseltresors. Wechseln Sie anschließend im Portal zum Abschnitt mit den HDInsight-Clustereigenschaften, und klicken Sie unter **Schlüssel-URL für Datenträgerverschlüsselung** auf **Schlüssel ändern**. Geben Sie die neue Schlüssel-URL ein, und senden Sie sie, um den Schlüssel zu rotieren.

![Rotieren des Schlüssels für die Datenträgerverschlüsselung](./media/disk-encryption/change-key.png)

#### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Im folgenden Beispiel wird der Datenträgerverschlüsselungsschlüssel für einen vorhandenen HDInsight-Cluster rotiert. Weitere Informationen finden Sie in der Dokumentation der Azure-Befehlszeilenschnittstelle unter [az hdinsight rotate-disk-encryption-key](/cli/azure/hdinsight#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Häufig gestellte Fragen zur Verschlüsselung mit kundenseitig verwalteten Schlüsseln

**Wie greift der HDInsight-Cluster auf meinen Schlüsseltresor zu?**

HDInsight verwendet die verwaltete Identität, die Sie dem HDInsight-Cluster zuordnen, um auf Ihre Azure Key Vault-Instanz zuzugreifen. Diese verwaltete Identität kann vor oder während der Clustererstellung erstellt werden. Außerdem müssen Sie der verwalteten Identität Zugriff auf den Schlüsseltresor mit dem gespeicherten Schlüssel gewähren.

**Ist dieses Feature für alle Cluster in HDInsight verfügbar?**

Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln steht für alle Clustertypen zur Verfügung (mit Ausnahme von Spark 2.1 und 2.2).

**Kann ich mehrere Schlüssel verwenden, um verschiedene Datenträger oder Ordner zu verschlüsseln?**

Nein. Alle verwalteten Datenträger und Ressourcendatenträger werden mit dem gleichen Schlüssel verschlüsselt.

**Was geschieht, wenn der Cluster den Zugriff auf den Schlüsseltresor oder den Schlüssel verliert?**

Wenn der Cluster den Zugriff auf den Schlüssel verliert, werden im Apache Ambari-Portal Warnungen angezeigt. In diesem Zustand schlägt der Vorgang **Schlüssel ändern** fehl. Sobald der Schlüsselzugriff wieder hergestellt wurde, verschwinden die Ambari-Warnungen, und Vorgänge wie z. B. die Schlüsselrotation können erfolgreich ausgeführt werden.

![Ambari-Warnung zum Schlüsselzugriff](./media/disk-encryption/ambari-alert.png)

**Wie kann ich den Cluster wiederherstellen, wenn die Schlüssel gelöscht werden?**

Da nur Schlüssel unterstützt werden, für die „vorläufiges Löschen“ aktiviert ist, sollte der Cluster wieder Zugriff auf die Schlüssel erlangen, wenn die Schlüssel im Schlüsseltresor wiederhergestellt werden. Informationen zum Wiederherstellen eines Azure Key Vault-Schlüssels finden Sie unter [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) oder [az-keyvault-key-recover](/cli/azure/keyvault/key#az-keyvault-key-recover).


**Werden kundenseitig verwaltete Schlüssel nahtlos von neuen Knoten unterstützt, wenn ein Cluster zentral hochskaliert wird?**

Ja. Der Cluster benötigt während der Hochskalierung Zugriff auf den Schlüssel im Schlüsseltresor. Sowohl verwaltete Datenträger als auch Ressourcendatenträger im Cluster werden mit dem gleichen Schlüssel verschlüsselt.

**Stehen kundenseitig verwaltete Schlüssel an meinem Standort zur Verfügung?**

Kundenseitig verwaltete Schlüssel von HDInsight stehen in allen öffentlichen und nationalen Clouds zur Verfügung.

## <a name="encryption-at-host-using-platform-managed-keys"></a>Verschlüsselung auf dem Host mithilfe plattformseitig verwalteter Schlüssel

### <a name="enable-in-the-azure-portal"></a>Aktivieren im Azure-Portal

Die Verschlüsselung auf dem Host kann während der Erstellung des Clusters im Azure-Portal aktiviert werden.

> [!Note]
> Wenn Verschlüsselung auf dem Host aktiviert ist, können Sie Ihrem HDInsight-Cluster keine Anwendungen aus Azure Marketplace hinzufügen.

:::image type="content" source="media/disk-encryption/encryption-at-host.png" alt-text="Verschlüsselung auf dem Host aktivieren.":::

Diese Option ermöglicht die [Verschlüsselung auf dem Host](../virtual-machines/disks-enable-host-based-encryption-portal.md) für temporäre Datenträger von HDInsight-VMs mit plattformseitig verwaltetem Schlüssel. Verschlüsselung auf dem Host wird nur [für bestimmte VM-SKUs in bestimmten Regionen](../virtual-machines/disks-enable-host-based-encryption-portal.md) unterstützt. HDInsight unterstützt die [folgende Knotenkonfiguration und die folgenden SKUs](./hdinsight-supported-node-configuration.md).

Informationen zur richtigen VM-Größe für Ihren HDInsight-Cluster finden Sie unter [Auswählen der richtigen VM-Größe für Ihren Azure HDInsight-Cluster](hdinsight-selecting-vm-size.md). Die Standard-VM-SKU für den Zookeeper-Knoten bei Aktivierung der Verschlüsselung auf dem Host ist DS2V2.

### <a name="enable-using-powershell"></a>Aktivieren mithilfe von PowerShell

Der folgende Codeausschnitt zeigt, wie Sie einen neuen Azure HDInsight-Cluster erstellen können, bei dem die Verschlüsselung auf dem Host mit PowerShell aktiviert wird. Der Parameter `-EncryptionAtHost $true` wird zum Aktivieren des Features verwendet.

```powershell
$storageAccountResourceGroupName = "Group"
$storageAccountName = "yourstorageacct001"
$storageAccountKey = Get-AzStorageAccountKey `
    -ResourceGroupName $storageAccountResourceGroupName `
    -Name $storageAccountName | %{ $_.Key1 }
$storageContainer = "container002"
# Cluster configuration info
$location = "East US 2"
$clusterResourceGroupName = "Group"
$clusterName = "your-hadoop-002"
$clusterCreds = Get-Credential
# If the cluster's resource group doesn't exist yet, run:
# New-AzResourceGroup -Name $clusterResourceGroupName -Location $location
# Create the cluster
New-AzHDInsightCluster `
    -ClusterType Hadoop `
    -ClusterSizeInNodes 4 `
    -ResourceGroupName $clusterResourceGroupName `
    -ClusterName $clusterName `
    -HttpCredential $clusterCreds `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.contoso.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $storageContainer `
    -SshCredential $clusterCreds `
    -EncryptionAtHost $true `
```

### <a name="enable-using-azure-cli"></a>Aktivieren mithilfe der Azure-Befehlszeilenschnittstelle

Der folgende Codeausschnitt zeigt, wie Sie einen neuen Azure HDInsight-Cluster erstellen können, bei dem die Verschlüsselung auf dem Host mit der Azure CLI aktiviert wird. Der Parameter `--encryption-at-host true` wird zum Aktivieren des Features verwendet.

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \\
-p "yourpass" \\
--storage-account MyStorageAccount --encryption-at-host true
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu Azure Key Vault finden Sie unter [Was ist Azure Key Vault](../key-vault/general/overview.md).
* [Übersicht über die Unternehmenssicherheit in Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
