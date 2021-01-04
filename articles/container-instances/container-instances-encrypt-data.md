---
title: Verschlüsseln von Bereitstellungsdaten
description: Hier finden Sie Informationen zur Verschlüsselung gespeicherter Daten für Ihre Containerinstanzressourcen, und Sie erfahren, wie Sie die Daten mit einem kundenseitig verwalteten Schlüssel verschlüsseln.
ms.topic: article
ms.date: 01/17/2020
author: macolso
ms.author: macolso
ms.openlocfilehash: 1b73ce5c994231a1c7b2f26ad702f2ad5880ba44
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686275"
---
# <a name="encrypt-deployment-data"></a>Verschlüsseln von Bereitstellungsdaten

Wenn Sie ACI-Ressourcen (Azure Container Instances) in der Cloud ausführen, sammelt und speichert der ACI-Dienst Daten im Zusammenhang mit Ihren Containern. Diese Daten werden von ACI automatisch verschlüsselt, wenn sie in der Cloud gespeichert werden. Diese Verschlüsselung schützt Ihre Daten und unterstützt Sie bei der Einhaltung der Sicherheits- und Complianceanforderungen Ihrer Organisation. ACI ermöglicht zudem die Verschlüsselung dieser Daten mit Ihrem eigenen Schlüssel. Dadurch haben Sie mehr Kontrolle über die Daten im Zusammenhang mit Ihren ACI-Bereitstellungen.

## <a name="about-aci-data-encryption"></a>Informationen zur ACI-Datenverschlüsselung 

Daten in ACI werden mittels 256-Bit-AES-Verschlüsselung ver- und entschlüsselt. Die Verschlüsselung ist für alle ACI-Bereitstellungen aktiviert, und Sie müssen Ihre Bereitstellung oder Container nicht ändern, um die Verschlüsselung zu nutzen. Dies schließt Metadaten zur Bereitstellung, Umgebungsvariablen, die Übergabe von Schlüsseln an Ihre Container sowie Protokolle ein, die auch nach Beendigung Ihrer Container beibehalten werden, um sie weiterhin anzeigen zu können. Die Verschlüsselung hat keine Auswirkungen auf die Leistung Ihrer Containergruppe, und es fallen keine zusätzlichen Kosten für die Verschlüsselung an.

## <a name="encryption-key-management"></a>Verwaltung von Verschlüsselungsschlüsseln

Sie können entweder von Microsoft verwaltete Schlüssel für die Verschlüsselung Ihrer Containerdaten nutzen oder die Verschlüsselung mit Ihren eigenen Schlüsseln verwalten. In der folgenden Tabelle werden diese beiden Optionen miteinander verglichen: 

|    |    Von Microsoft verwaltete Schlüssel     |     Vom Kunden verwaltete Schlüssel     |
|----|----|----|
|    **Verschlüsselungs-/Entschlüsselungsvorgänge**    |    Azure    |    Azure    |
|    **Schlüsselspeicher**    |    Microsoft-Schlüsselspeicher    |    Azure-Schlüsseltresor    |
|    **Verantwortlich für die Schlüsselrotation**    |    Microsoft    |    Kunde    |
|    **Schlüsselzugriff**    |    Nur Microsoft    |    Microsoft, Kunde    |

Im restlichen Dokument werden die Schritte beschrieben, die ausgeführt werden müssen, um Ihre ACI-Bereitstellungsdaten mit Ihrem Schlüssel (kundenseitig verwalteter Schlüssel) zu verschlüsseln. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Verschlüsseln von Daten mit einem kundenseitig verwalteten Schlüssel

### <a name="create-service-principal-for-aci"></a>Erstellen eines Dienstprinzipals für ACI

Als Erstes muss dafür gesorgt werden, dass Ihr [Azure-Mandant](../active-directory/develop/quickstart-create-new-tenant.md) über einen zugewiesenen Dienstprinzipal verfügt, um dem Azure Container Instances-Dienst Berechtigungen zu gewähren. 

> [!IMPORTANT]
> Um den folgenden Befehl auszuführen und erfolgreich einen Dienstprinzipal zu erstellen, vergewissern Sie sich, dass Sie über die Berechtigungen zum Erstellen von Dienstprinzipalen in Ihrem Mandanten verfügen.
>

Mit dem folgenden CLI-Befehl wird der ACI-Dienstprinzipal in Ihrer Azure-Umgebung eingerichtet:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Die Ausgabe des Befehls sollte ein Dienstprinzipal sein, der mit Folgendem eingerichtet wurde: "displayName": "Azure Container Instance Service".

Falls Sie den Dienstprinzipal nicht erfolgreich erstellen können:
* Vergewissern Sie sich, dass Sie über die dafür erforderlichen Berechtigungen in Ihrem Mandanten verfügen.
* Überprüfen Sie, ob bereits ein Dienstprinzipal in Ihrem Mandanten für die Bereitstellung in ACI vorhanden ist. Dies können Sie tun, indem Sie `az ad sp show --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9` ausführen und stattdessen diesen Dienstprinzipal verwenden.

### <a name="create-a-key-vault-resource"></a>Erstellen einer Key Vault-Ressource

Sie können eine Azure Key Vault-Instanz im [Azure-Portal](../key-vault/general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../key-vault/general/quick-create-cli.md) oder mit [Azure PowerShell](../key-vault/general/quick-create-powershell.md) erstellen.

Berücksichtigen Sie bei den Eigenschaften Ihres Schlüsseltresors die folgenden Richtlinien: 
* Name: Es ist ein eindeutiger Name erforderlich. 
* Abonnement: Wählen Sie ein Abonnement aus.
* Wählen Sie unter „Ressourcengruppe“ entweder eine bereits vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe, und geben Sie einen Ressourcengruppennamen ein.
* Wählen Sie im Pulldownmenü „Standort“ einen Standort aus.
* Bei den anderen Optionen können Sie entweder die Standardeinstellungen übernehmen oder sie gemäß Ihren individuellen Anforderungen festlegen.

> [!IMPORTANT]
> Wenn Sie kundenseitig verwaltete Schlüssel zum Verschlüsseln einer ACI-Bereitstellungsvorlage verwenden, empfiehlt es sich, für den Schlüsseltresor die Eigenschaften „Soft Delete“ (Vorläufig löschen) und „Do Not Purge“ (Nicht bereinigen) festzulegen. Diese Eigenschaften sind standardmäßig nicht aktiviert, können aber entweder mithilfe von PowerShell oder per Azure CLI für einen neuen oder vorhandenen Schlüsseltresor aktiviert werden.

### <a name="generate-a-new-key"></a>Generieren eines neuen Schlüssels 

Navigieren Sie nach der Erstellung Ihres Schlüsseltresors zur entsprechenden Ressource im Azure-Portal. Klicken Sie im linken Navigationsmenü des Ressourcenblatts unter „Einstellungen“ auf **Schlüssel**. Klicken Sie in der Ansicht für „Schlüssel“ auf „Generieren/importieren“, um einen neuen Schlüssel zu generieren. Verwenden Sie einen beliebigen eindeutigen Namen für diesen Schlüssel, und legen Sie die gewünschten Einstellungen fest. 

![Generieren eines neuen Schlüssels](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Festlegen der Zugriffsrichtlinie

Erstellen Sie eine neue Zugriffsrichtlinie, um dem ACI-Dienst den Zugriff auf Ihren Schlüssel zu ermöglichen.

* Klicken Sie nach Abschluss der Schlüsselgenerierung auf dem Ressourcenblatt Ihres Schlüsseltresors unter „Einstellungen“ auf **Zugriffsrichtlinien**.
* Klicken Sie auf der Seite „Zugriffsrichtlinien“ für Ihren Schlüsseltresor auf **Zugriffsrichtlinie hinzufügen**.
* Schließen Sie unter *Schlüsselberechtigungen* die Berechtigungen **Abrufen** und **Schlüssel entpacken** ein. ![Festlegen der Schlüsselberechtigungen](./media/container-instances-encrypt-data/set-key-permissions.png)
* Wählen Sie unter *Prinzipal auswählen* die Option **Azure Container Instance Service** aus.
* Klicken Sie unten auf **Hinzufügen**. 

Die Zugriffsrichtlinie sollte nun in den Zugriffsrichtlinien Ihres Schlüsseltresors angezeigt werden.

![Neue Zugriffsrichtlinie](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Ändern Ihrer JSON-Bereitstellungsvorlage

> [!IMPORTANT]
> Das Verschlüsseln von Bereitstellungsdaten mit einem kundenseitig verwalteten Schlüssel ist in der neuesten API-Version (2019-12-01) verfügbar, deren Rollout momentan durchgeführt wird. Geben Sie diese API-Version in Ihrer Bereitstellungsvorlage an. Sollten dabei Probleme auftreten, wenden Sie sich an den Azure-Support.

Fügen Sie Ihrer ACI-Bereitstellungsvorlage nach dem Einrichten des Schlüsseltresorschlüssels und der Zugriffsrichtlinie die folgenden Eigenschaften hinzu. Weitere Informationen zum Bereitstellen von ACI-Ressourcen mit einer Vorlage finden Sie im [Tutorial: Bereitstellen einer Gruppe mit mehreren Containern über eine Resource Manager-Vorlage](./container-instances-multi-container-group.md). 
* Legen Sie unter `resources` `apiVersion` auf `2019-12-01` fest.
* Fügen Sie im Abschnitt mit den Containergruppeneigenschaften der Bereitstellungsvorlage eine `encryptionProperties` mit folgenden Werten hinzu:
  * `vaultBaseUrl`: Der DNS-Name Ihres Schlüsseltresors. Diesen finden Sie auf dem Übersichtsblatt der Schlüsseltresorressource im Portal.
  * `keyName`: Der Name des zuvor generierten Schlüssels.
  * `keyVersion`: Die aktuelle Version des Schlüssels. Diese Angabe finden Sie, indem Sie direkt auf den Schlüssel klicken (im Abschnitt „Einstellungen“ Ihrer Schlüsseltresorressource unter „Schlüssel“).
* Fügen Sie unter den Eigenschaften der Containergruppe eine `sku`-Eigenschaft mit dem Wert `Standard` hinzu. Die `sku`-Eigenschaft ist in API-Version 2019-12-01 erforderlich.

Der folgende Vorlagenausschnitt zeigt diese zusätzlichen Eigenschaften zum Verschlüsseln von Bereitstellungsdaten:

```json
[...]
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "sku": "Standard",
            "containers": {
                [...]
            }
        }
    }
]
```

Im Folgenden finden Sie eine vollständige Vorlage, angepasst auf Grundlage der Vorlage im [Tutorial: Bereitstellen einer Gruppe mit mehreren Containern über eine Resource Manager-Vorlage](./container-instances-multi-container-group.md). 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "encryptionProperties": {
            "vaultBaseUrl": "https://example.vault.azure.net",
            "keyName": "acikey",
            "keyVersion": "xxxxxxxxxxxxxxxx"
        },
        "sku": "Standard",  
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

### <a name="deploy-your-resources"></a>Bereitstellen Ihrer Ressourcen

Wenn Sie die Vorlagendatei auf Ihrem Desktop erstellt und bearbeitet haben, können Sie sie in Ihr Cloud Shell-Verzeichnis hochladen, indem Sie sie dorthin ziehen. 

Erstellen Sie mithilfe des Befehls [az group create][az-group-create] eine Ressourcengruppe.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Stellen Sie die Vorlage mit dem Befehl [az deployment group create][az-deployment-group-create] bereit.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file deployment-template.json
```

Innerhalb weniger Sekunden sollten Sie eine erste Antwort von Azure erhalten. Nach Abschluss der Bereitstellung werden alle bereitstellungsbezogenen Daten, die vom ACI-Dienst gespeichert werden, mit dem bereitgestellten Schlüssel verschlüsselt.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group/#az-deployment-group-create
