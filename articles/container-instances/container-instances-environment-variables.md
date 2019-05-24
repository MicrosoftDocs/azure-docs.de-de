---
title: Festlegen von Umgebungsvariablen in Azure Container Instances
description: Es wird beschrieben, wie Sie Umgebungsvariablen in den Containern festgelegen, die Sie in Azure Container Instances ausführen.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 4a4b19338d96094f28b4f4bedd8042723f67f10a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66149141"
---
# <a name="set-environment-variables-in-container-instances"></a>Festlegen von Umgebungsvariablen in Container Instances

Das Festlegen von Umgebungsvariablen in Ihren Containerinstanzen ermöglicht es Ihnen, eine dynamische Konfiguration der Anwendung oder des Skripts bereitzustellen, die bzw. das vom Container ausgeführt wird. Dies ist vergleichbar mit dem Befehlszeilenargument `--env` für `docker run`. 

Um Umgebungsvariablen in einem Container festzulegen, geben Sie diese bei der Erstellung einer Containerinstanz an. Dieser Artikel enthält Beispiele zum Festlegen von Umgebungsvariablen beim Starten eines Containers über die [Azure CLI](#azure-cli-example), mit [Azure PowerShell](#azure-powershell-example) und im [Azure-Portal](#azure-portal-example). 

Wenn Sie z. B. das Microsoft-Containerimage [aci-wordcount][aci-wordcount] ausführen, können Sie dessen Verhalten ändern, indem Sie die folgenden Umgebungsvariablen angeben:

*NumWords*: Die Anzahl der Wörter, die an STDOUT gesendet wird.

*MinLength*: Die Mindestanzahl von Zeichen in einem Wort, damit es gezählt wird. Bei einem höheren Wert werden häufige Wörter wie „von“ und „das“ ignoriert.

Wenn Sie Geheimnisse als Umgebungsvariablen weitergeben müssen, unterstützt Azure Container Instances [sichere Werte](#secure-values) sowohl für Windows- als auch Linux-Container.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Azure CLI-Beispiel

Um die Standardausgabe des [aci-wordcount][aci-wordcount]-Containers anzuzeigen, führen Sie ihn zunächst mit diesem [az container create][az-container-create]-Befehl aus (ohne Umgebungsvariablen):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Um die Ausgabe zu ändern, starten Sie einen zweiten Container, wobei Sie das Argument `--environment-variables` hinzufügen und Werte für die Variablen *NumWords* und *MinLength* angeben. (In diesem Beispiel wird davon ausgegangen, dass Sie die Befehlszeilenschnittstelle in einer Bash-Shell oder in Azure Cloud Shell ausführen. Wenn Sie die Windows-Eingabeaufforderung verwenden, geben Sie die Variablen in doppelten Anführungszeichen an, z.B. `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Sobald für beide Container der Status *Beendet* angezeigt wird, (verwenden Sie [az container show][az-container-show] zum Überprüfen des Status), zeigen Sie deren Protokolle mit [az container logs][az-container-logs] an, um die Ausgabe zu sehen.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

Die Ausgabe der Container zeigt, wie Sie die das Skriptverhalten des zweiten Containers durch Festlegen von Umgebungsvariablen geändert haben.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Azure PowerShell-Beispiel

Das Festlegen von Umgebungsvariablen in PowerShell ähnelt dem Vorgang über die Befehlszeilenschnittstelle, es wird jedoch das Befehlszeilenargument `-EnvironmentVariable` verwendet.

Starten Sie zuerst den Container [aci-wordcount][aci-wordcount] in seiner Standardkonfiguration mit diesem [New-AzContainerGroup][new-Azcontainergroup]-Befehl:

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Führen Sie nun den folgenden [New-AzContainerGroup][new-Azcontainergroup]-Befehl aus. Dieser gibt die Umgebungsvariablen *NumWords* und *MinLength* nach dem Auffüllen einer Arrayvariablen (`envVars`) an:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Sobald für beide Container der Status *Beendet* angezeigt wird (verwenden Sie [Get-AzContainerInstanceLog][azure-instance-log] zum Überprüfen des Status), rufen Sie mit dem [Get-AzContainerInstanceLog][azure-instance-log]-Befehl deren Protokolle ab.

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

Die Ausgabe für jeden Container zeigt, wie Sie das vom Container ausgeführte Skript durch Festlegen von Umgebungsvariablen geändert haben.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Azure-Portal-Beispiel

Um Umgebungsvariablen beim Starten eines Containers im Azure-Portal festzulegen, geben Sie diese auf der Seite **Erweitert** an, wenn Sie den Container erstellen.

1. Legen Sie auf der Seite **Erweitert** die **Neustartrichtlinie** auf *Bei einem Fehler* fest.
2. Geben Sie unter **Umgebungsvariablen** die Variable `NumWords` mit dem Wert `5` als erste Variable und `MinLength` mit dem Wert `8` als zweite Variable ein. 
1. Wählen Sie **Überprüfen + erstellen** aus, um den Container zu überprüfen, und stellen Sie ihn dann bereit.

![Portalseite mit Umgebungsvariable, Aktivieren-Schaltfläche und Textfeldern][portal-env-vars-01]

Wählen Sie zum Anzeigen der Protokolle des Containers unter **Einstellungen** die Option **Container** und dann **Protokolle** aus. Ähnlich wie in der in den vorherigen Abschnitten zur Befehlszeilenschnittstelle und zu PowerShell angezeigten Ausgabe sehen Sie, wie das Skriptverhalten durch die Umgebungsvariablen geändert wurde. Nur fünf Wörter werden angezeigt, jeweils mit einer minimalen Länge von acht Zeichen.

![Portal mit der Containerprotokollausgabe][portal-env-vars-02]

## <a name="secure-values"></a>Sichere Werte

Objekte mit sicheren Werten sind zur Aufbewahrung sensibler Informationen wie Passwörter oder Schlüssel für Ihre Anwendung gedacht. Die Verwendung von sicheren Werten für Umgebungsvariablen ist sowohl sicherer als auch flexibler als die Einbindung in Ihr Containerimage. Eine andere Möglichkeit ist die Verwendung von geheimen Volumes, wie in [Einbinden eines geheimen Volumes in Azure Container Instances](container-instances-volume-secret.md) beschrieben.

Umgebungsvariablen mit sicheren Werten sind in den Eigenschaften Ihres Containers nicht sichtbar – der Zugriff auf ihre Werte ist nur von innerhalb des Containers möglich. Beispielsweise wird in den Containereigenschaften, die im Azure-Portal oder in der Azure CLI angezeigt werden, nur der Name einer sicheren Variablen angezeigt, nicht ihr Wert.

Legen Sie eine sichere Umgebungsvariable fest, indem Sie für den Variablentypen den `secureValue`-Wert anstelle des normalen `value` angeben. Die in der folgenden YAML definierten zwei Variablen veranschaulichen die zwei Variablentypen.

### <a name="yaml-deployment"></a>YAML-Bereitstellung

Erstellen Sie eine `secure-env.yaml`-Datei mit dem folgenden Ausschnitt.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Führen Sie den folgenden Befehl zum Bereitstellen der Containergruppe mit YAML aus (passen Sie den Namen der Ressourcengruppe nach Bedarf an):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Überprüfen von Umgebungsvariablen

Führen Sie den Befehl [az container show][az-container-show] aus, um die Umgebungsvariablen für Ihren Container abzufragen:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

Die JSON-Antwort zeigt Schlüssel und Wert der unsicheren Umgebungsvariablen an, aber nur den Namen der sicheren Umgebungsvariablen:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Mit dem Befehl [az container exec][az-container-exec], der die Ausführung eines Befehls aus einem laufenden Container heraus ermöglicht, können Sie überprüfen, ob die sichere Umgebungsvariable festgelegt wurde. Führen Sie den folgenden Befehl aus, um eine interaktive Bash-Sitzung im Container zu starten:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Wenn Sie eine interaktive Shell innerhalb des Containers geöffnet haben, können Sie auf den Wert der `SECRET`-Variablen zugreifen:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Nächste Schritte

Aufgabenbasierte Szenarien, z. B. Stapelverarbeitung eines großen Datasets mit mehreren Containern, können von benutzerdefinierten Umgebungsvariablen zur Laufzeit profitieren. Weitere Informationen zum Ausführen aufgabenbasierter Container finden Sie unter [Ausführen von Aufgaben in Containern mit Neustartrichtlinien](container-instances-restart-policy.md).

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
