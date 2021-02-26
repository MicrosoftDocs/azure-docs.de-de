---
title: Verwalten von Azure Service Fabric Mesh-Anwendungsgeheimnissen
description: Verwalten Sie Anwendungsgeheimnisse, damit Sie auf sichere Weise eine Service Fabric Mesh-Anwendung erstellen und bereitstellen können.
ms.date: 4/2/2019
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3be0c2b21c3405f4f42b2ff4d02ca95c78956de
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626959"
---
# <a name="manage-service-fabric-mesh-application-secrets"></a>Verwalten von Service Fabric Mesh-Anwendungsgeheimnissen

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Service Fabric Mesh unterstützt Geheimnisse als Azure-Ressourcen. Bei einem Service Fabric Mesh-Geheimnis kann es sich um beliebige sensible Informationen in Textform handeln, z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die sicher gespeichert und übertragen werden sollten. In diesem Artikel wird beschrieben, wie Sie den Secure Store Service von Service Fabric verwenden, um Geheimnisse bereitzustellen und zu verwalten.

Ein Mesh-Anwendungsgeheimnis umfasst Folgendes:
* Eine Ressource **Geheimnisse**, bei der es sich um einen Container zum Speichern von Textgeheimnissen handelt. In der Ressource **Geheimnisse** enthaltene Geheimnisse werden auf sichere Weise gespeichert und übertragen.
* Mindestens eine Ressource vom Typ **Geheimnisse/Werte**, die im Ressourcencontainer **Geheimnisse** gespeichert ist. Jede Ressource **Geheimnisse/Werte** verfügt über eine Versionsnummer. Sie können die Version einer Ressource **Geheimnisse/Werte** nicht ändern, sondern nur eine neue Version anfügen.

Die Verwaltung von Geheimnissen umfasst die folgenden Schritte:
1. Deklarieren Sie die Mesh-Ressource **Geheimnisse** in einer YAML- oder JSON-Datei des Azure-Ressourcenmodells, indem Sie den Typ „inlinedValue“ und SecretsStoreRef-contentType-Definitionen verwenden.
2. Deklarieren Sie Mesh-Ressourcen vom Typ **Geheimnisse/Werte** in der YAML- oder JSON-Datei eines Azure-Ressourcenmodells, das unter der Ressource **Geheimnisse** (aus Schritt 1) gespeichert wird.
3. Ändern Sie die Mesh-Anwendung so, dass auf die Mesh-Geheimniswerte verwiesen wird.
4. Stellen Sie die Mesh-Anwendung bereit, oder führen Sie ein paralleles Upgrade durch, um Geheimniswerte zu nutzen.
5. Verwenden Sie die „az“-Befehle der Azure CLI für die Secure Store Service-Lebenszyklusverwaltung.

## <a name="declare-a-mesh-secrets-resource"></a>Deklarieren einer Mesh-Geheimnisressource
Eine Ressource vom Typ „Mesh-Geheimnis“ wird in einer JSON- oder YAML-Datei des Azure-Ressourcenmodells mit einer Definition der Art „inlinedValue“ deklariert. Die Ressource vom Typ „Mesh-Geheimnis“ unterstützt per Secure Store Service erstellte Geheimnisse. 
>
Hier ist ein Beispiel dafür angegeben, wie Sie Ressourcen vom Typ „Mesh-Geheimnis“ in einer JSON-Datei deklarieren:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    }
  ]
}
```
Hier ist ein Beispiel dafür angegeben, wie Sie Ressourcen vom Typ „Mesh-Geheimnis“ in einer YAML-Datei deklarieren:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="declare-mesh-secretsvalues-resources"></a>Deklarieren von Ressourcen vom Typ „Mesh-Geheimnisse/-Werte“
Ressourcen vom Typ „Mesh-Geheimnisse/-Werte“ verfügen über eine Abhängigkeit von den Ressourcen vom Typ „Mesh-Geheimnisse“, die Sie im vorherigen Schritt definiert haben.

In Bezug auf die Beziehung zwischen den Feldern „value:“ und „name:“ des Abschnitts „resources“ ist der zweite Teil der Zeichenfolge „name:“, der durch einen Doppelpunkt abgetrennt ist, die Versionsnummer, die für ein Geheimnis verwendet wird. Der Name vor dem Doppelpunkt muss mit dem Mesh-Geheimniswert übereinstimmen, mit dem eine Abhängigkeit besteht. Für das Element ```name: mysecret:1.0``` lautet die Versionsnummer beispielsweise 1.0, und der Name ```mysecret``` muss mit der zuvor definierten Angabe ```"value": "mysecret"``` übereinstimmen.

>
Hier ist ein Beispiel dafür angegeben, wie Sie Ressourcen vom Typ „Mesh-Geheimnisse/-Werte“ in einer JSON-Datei deklarieren:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "WestUS",
      "metadata": {
        "description": "Location of the resources (e.g. westus, eastus, westeurope)."
      }
    }
  },
  "sfbpHttpsCertificate": {
      "type": "string",
      "metadata": {
        "description": "Plain Text Secret Value that your container ingest"
      }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx",
      "type": "Microsoft.ServiceFabricMesh/secrets",
      "location": "[parameters('location')]", 
      "dependsOn": [],
      "properties": {
        "kind": "inlinedValue",
        "description": "SFBP Application Secret",
        "contentType": "text/plain",
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "sfbpHttpsCertificate.pfx/2019.02.28",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/sfbpHttpsCertificate.pfx"
      ],
      "properties": {
        "value": "[parameters('sfbpHttpsCertificate')]"
      }
    }
  ],
}
```
Hier ist ein Beispiel dafür angegeben, wie Sie Ressourcen vom Typ „Mesh-Geheimnisse/-Werte“ in einer YAML-Datei deklarieren:
```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
          Secrets:
            - name: MySecret.txt
            description: My Mesh Application Secret
            secret_type: inlinedValue
            content_type: SecretStoreRef
            value: mysecret
            - name: mysecret:1.0
            description: My Mesh Application Secret Value
            secret_type: value
            content_type: text/plain
            value: "P@ssw0rd#1234"
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

## <a name="modify-mesh-application-to-reference-mesh-secret-values"></a>Ändern Sie die Mesh-Anwendung so, dass auf die Mesh-Geheimniswerte verwiesen wird.
Service Fabric Mesh-Anwendungen müssen über die folgenden beiden Zeichenfolgen verfügen, um Secure Store Service-Geheimniswerte nutzen zu können:
1. „Microsoft.ServiceFabricMesh/Secrets.name“ enthält den Namen der Datei sowie den Geheimniswert in Klartext.
2. Die Windows- oder Linux-Umgebungsvariable „Fabric_SettingPath“ enthält den Verzeichnispfad, unter dem die Dateien mit den Secure Store Service-Geheimniswerten verfügbar sind. Dies ist „C:\Settings“ für unter Windows gehostete Mesh-Anwendungen und „/var/settings“ für unter Linux gehostete Mesh-Anwendungen.

## <a name="deploy-or-use-a-rolling-upgrade-for-mesh-application-to-consume-secret-values"></a>Stellen Sie die Mesh-Anwendung bereit, oder verwenden Sie dafür ein paralleles Upgrade, um Geheimniswerte zu nutzen.
Die Erstellung von Geheimnissen bzw. Geheimnissen/Werten mit Versionsangabe ist auf Bereitstellungen beschränkt, die über das Ressourcenmodell deklariert werden. Die einzige Möglichkeit zum Erstellen dieser Ressourcen ist das Übergeben einer JSON- oder YAML-Datei eines Ressourcenmodells mit dem Befehl **az mesh deployment**:

```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```

## <a name="azure-cli-commands-for-secure-store-service-lifecycle-management"></a>Azure CLI-Befehle für Secure Store Service-Lebenszyklusverwaltung

### <a name="create-a-new-secrets-resource"></a>Erstellen einer neuen Ressource „Geheimnisse“
```azurecli-interactive
az mesh deployment create –-<template-file> or --<template-uri>
```
Übergeben Sie entweder **template-file** oder **template-uri** (aber nicht beides).

Beispiel:
- az mesh deployment create --c:\MyMeshTemplates\SecretTemplate1.txt
- az mesh deployment create --https:\//www.fabrikam.com/MyMeshTemplates/SecretTemplate1.txt

### <a name="show-a-secret"></a>Anzeigen eines Geheimnisses
Gibt die Beschreibung des Geheimnisses zurück (aber nicht den Wert).
```azurecli-interactive
az mesh secret show --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="delete-a-secret"></a>Löschen eines Geheimnisses

- Ein Geheimnis kann nicht gelöscht werden, während von einer Mesh-Anwendung darauf verwiesen wird.
- Beim Löschen einer Ressource „Geheimnisse“ werden alle Geheimnis- bzw. Ressourcenversionen gelöscht.
  ```azurecli-interactive
  az mesh secret delete --Resource-group <myResourceGroup> --secret-name <mySecret>
  ```

### <a name="list-secrets-in-subscription"></a>Auflisten von Geheimnissen im Abonnement
```azurecli-interactive
az mesh secret list
```
### <a name="list-secrets-in-resource-group"></a>Auflisten von Geheimnissen der Ressourcengruppe
```azurecli-interactive
az mesh secret list -g <myResourceGroup>
```
### <a name="list-all-versions-of-a-secret"></a>Auflisten aller Versionen eines Geheimnisses
```azurecli-interactive
az mesh secretvalue list --Resource-group <myResourceGroup> --secret-name <mySecret>
```

### <a name="show-secret-version-value"></a>Anzeigen des Versionswerts für das Geheimnis
```azurecli-interactive
az mesh secretvalue show --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

### <a name="delete-secret-version-value"></a>Löschen des Versionswerts für das Geheimnis
```azurecli-interactive
az mesh secretvalue delete --Resource-group <myResourceGroup> --secret-name <mySecret> --version <N>
```

## <a name="next-steps"></a>Nächste Schritte 
In der Übersicht erfahren Sie mehr über Service Fabric Mesh:
- [Übersicht über Service Fabric Mesh](service-fabric-mesh-overview.md)
