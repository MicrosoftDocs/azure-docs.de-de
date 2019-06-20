---
title: Einbinden eines geheimen Volumes in Azure Container Instances
description: Hier erfahren Sie, wie Sie ein geheimes Volume zum Speichern vertraulicher Informationen einbinden, auf das von Ihren Containerinstanzen zugegriffen werden kann.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 07/19/2018
ms.author: danlep
ms.openlocfilehash: 3c1c83bb0c3e46a7eaab519050d9c556e2cc1a7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60563085"
---
# <a name="mount-a-secret-volume-in-azure-container-instances"></a>Einbinden eines geheimen Volumes in Azure Container Instances

Verwenden Sie ein *geheimes* Volume, um für die Container in einer Containergruppe vertrauliche Informationen bereitzustellen. Das *geheime* Volume speichert Ihre Geheimnisse in Dateien im Volume, auf die die Container in der Containergruppe zugreifen können. Mit dem Speichern von Geheimnissen in einem *geheimen* Volume können Sie vermeiden, vertrauliche Daten wie SSH-Schlüssel oder Datenbankanmeldeinformationen dem Anwendungscode hinzuzufügen.

Alle *geheimen* Volumes werden durch [tmpfs][tmpfs], ein auf RAM basierendes Dateisystem, gesichert. Ihre Inhalte werden nie in permanenten Speicher geschrieben.

> [!NOTE]
> *Geheime* Volumes sind momentan auf Linux-Container beschränkt. Unter [Festlegen von Umgebungsvariablen](container-instances-environment-variables.md) erfahren Sie, wie Sie sichere Umgebungsvariablen für Windows- und Linux-Container übergeben. Bis alle Features auch für Windows-Container verfügbar sind, finden Sie die aktuellen Plattformunterschiede unter [Kontingente und Regionsverfügbarkeit für Azure Container Instances](container-instances-quotas.md).

## <a name="mount-secret-volume---azure-cli"></a>Einbinden eines geheimen Volumes – Azure CLI

Um einen Container mit einem oder mehreren Geheimnissen mit Azure CLI bereitzustellen, beziehen Sie die Parameter `--secrets` und `--secrets-mount-path` in den Befehl [az container create][az-container-create] ein. Dieses Beispiel bindet ein aus zwei Geheimnissen, „mysecret1“ und „mysecret2“, bestehendes *geheimes* Volume unter `/mnt/secrets` ein:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name secret-volume-demo \
    --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --secrets mysecret1="My first secret FOO" mysecret2="My second secret BAR" \
    --secrets-mount-path /mnt/secrets
```

Die folgende Ausgabe von [az container exec][az-container-exec] zeigt das Öffnen einer Shell im ausgeführten Container, das Auflisten der Dateien im geheimen Volume und dann das Anzeigen ihrer Inhalte:

```console
$ az container exec --resource-group myResourceGroup --name secret-volume-demo --exec-command "/bin/sh"
/usr/src/app # ls -1 /mnt/secrets
mysecret1
mysecret2
/usr/src/app # cat /mnt/secrets/mysecret1
My first secret FOO
/usr/src/app # cat /mnt/secrets/mysecret2
My second secret BAR
/usr/src/app # exit
Bye.
```

## <a name="mount-secret-volume---yaml"></a>Einbinden eines geheimen Volumes – YAML

Sie können auch Gruppen von Containern mit der Azure CLI und einer [YAML-Vorlage](container-instances-multi-container-yaml.md) bereitstellen. Die Bereitstellung der YAML-Vorlage ist die bevorzugte Methode bei der Bereitstellung von Containergruppen, die aus mehreren Containern bestehen.

Wenn Sie mit einer YAML-Vorlage bereitstellen, müssen die geheimen Werte in der Vorlage **Base64-codiert** sein. Allerdings werden die geheimen Werte in den Dateien im Container in Nur-Text-Form angezeigt.

Die folgende YAML-Vorlage definiert eine Containergruppe mit einem Container, der ein *geheimes* Volume unter `/mnt/secrets` einbindet. Das geheime Volume verfügt über zwei Geheimnisse, „mysecret1“ und „mysecret2“.

```yaml
apiVersion: '2018-06-01'
location: eastus
name: secret-volume-demo
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - mountPath: /mnt/secrets
        name: secretvolume1
  osType: Linux
  restartPolicy: Always
  volumes:
  - name: secretvolume1
    secret:
      mysecret1: TXkgZmlyc3Qgc2VjcmV0IEZPTwo=
      mysecret2: TXkgc2Vjb25kIHNlY3JldCBCQVIK
tags: {}
type: Microsoft.ContainerInstance/containerGroups
```

Um mit der YAML-Vorlage bereitzustellen, speichern Sie den vorherigen YAML-Code in einer Datei namens `deploy-aci.yaml` und führen dann den Befehl [az container create][az-container-create] mit dem `--file`-Parameter aus:

```azurecli-interactive
# Deploy with YAML template
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

## <a name="mount-secret-volume---resource-manager"></a>Einbinden eines geheimen Volumes – Resource Manager

Zusätzlich zur CLI- und YAML-Bereitstellung können Sie eine Containergruppe mit einer [Azure Resource Manager-Vorlage](/azure/templates/microsoft.containerinstance/containergroups) bereitstellen.

Füllen Sie in der Vorlage zunächst das `volumes`-Array im Abschnitt `properties` für die Containergruppe auf. Wenn Sie mit einer Resource Manager-Vorlage bereitstellen, müssen die geheimen Werte in der Vorlage **Base64-codiert** sein. Allerdings werden die geheimen Werte in den Dateien im Container in Nur-Text-Form angezeigt.

Füllen Sie als Nächstes für jeden Container in der Containergruppe, in die Sie das *geheime* Volume einbinden möchten, das Array `volumeMounts` im Abschnitt `properties` der Containerdefinition auf.

Die folgende Resource Manager-Vorlage definiert eine Containergruppe mit einem Container, der ein *geheimes* Volume unter `/mnt/secrets` einbindet. Das geheime Volume verfügt über zwei Geheimnisse, „mysecret1“ und „mysecret2“.

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-secret.json -->
[!code-json[volume-secret](~/azure-docs-json-samples/container-instances/aci-deploy-volume-secret.json)]

Um mit der Resource Manager-Vorlage bereitzustellen, speichern Sie den vorherigen JSON-Code in einer Datei namens `deploy-aci.json` und führen dann den Befehl [az group deployment create][az-group-deployment-create] mit dem `--template-file`-Parameter aus:

```azurecli-interactive
# Deploy with Resource Manager template
az group deployment create --resource-group myResourceGroup --template-file deploy-aci.json
```

## <a name="next-steps"></a>Nächste Schritte

### <a name="volumes"></a>Volumes

Erfahren Sie, wie andere Volumetypen in Azure Container Instances bereitgestellt werden:

* [Einbinden einer Azure-Dateifreigabe in Azure Container Instances](container-instances-volume-azure-files.md)
* [Einbinden eines emptyDir-Volumes in Azure Container Instances](container-instances-volume-emptydir.md)
* [Einbinden eines gitRepo-Volumes in Azure Container Instances](container-instances-volume-gitrepo.md)

### <a name="secure-environment-variables"></a>Sichere Umgebungsvariablen

Eine andere Methode zum Bereitstellen von vertraulichen Informationen für Container (einschließlich Windows-Containern) ist die Verwendung von [sicheren Umgebungsvariablen](container-instances-environment-variables.md#secure-values).

<!-- LINKS - External -->
[tmpfs]: https://wikipedia.org/wiki/Tmpfs

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
