---
title: Verwenden einer Startbefehlszeile in Azure Container Instances
description: Setzen Sie den in einem Containerimage konfigurierten Einstiegspunkt außer Kraft, wenn Sie eine Azure-Containerinstanz bereitstellen.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/15/2019
ms.author: danlep
ms.openlocfilehash: da94a4c79694f511d41e5c8dda8c786fc7049726
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569636"
---
# <a name="set-the-command-line-in-a-container-instance-to-override-the-default-command-line-operation"></a>Legen Sie die Befehlszeile in einer Containerinstanz so fest, dass sie die standardmäßige Befehlszeilenoperation außer Kraft setzt.

Wenn Sie eine Containerinstanz erstellen, geben Sie optional einen Befehl an, um die standardmäßige Befehlszeilenanweisung im Containerimage außer Kraft zu setzen. Dieses Verhalten ist vergleichbar mit dem Befehlszeilenargument `--entrypoint` für `docker run`.

Wie das Festlegen von [Umgebungsvariablen](container-instances-environment-variables.md) für Containerinstanzen ist die Angabe einer Startbefehlszeile nützlich für Batchaufträge, bei denen Sie jeden Container dynamisch mit einer aufgabenspezifischen Konfiguration vorbereiten müssen.

## <a name="command-line-guidelines"></a>Befehlszeilenrichtlinien

* Standardmäßig gibt die Befehlszeile einen *einzelnen Prozess an, der ohne Shell* im Container startet. Über die Befehlszeile kann z. B. ein Python-Skript oder eine ausführbare Datei ausgeführt werden. 

* Um mehrere Befehle auszuführen, beginnen Sie Ihre Befehlszeile mit dem Einrichten einer Shellumgebung, die im Containerbetriebssystem unterstützt wird. Beispiele:

  |Betriebssystem  |Standardshell  |
  |---------|---------|
  |Ubuntu     |   `/bin/bash`      |
  |Alpine     |   `/bin/sh`      |
  |Windows     |    `cmd`     |

  Folgen Sie den Konventionen der Shell, um mehrere Befehle zu kombinieren und in Folge auszuführen.

* Abhängig von der Containerkonfiguration müssen Sie möglicherweise einen vollständigen Pfad zur ausführbaren Befehlszeilendatei oder zu Argumenten festlegen.

* Legen Sie eine geeignete [Neustartrichtlinie](container-instances-restart-policy.md) für die Containerinstanz fest, je nachdem, ob die Befehlszeile eine zeitintensive Aufgabe oder eine einmal ausgeführte Aufgabe angibt. Für eine einmal ausgeführte Aufgabe wird z. B. eine Neustartrichtlinie von `Never` oder `OnFailure` empfohlen. 

* Wenn Sie Informationen zum standardmäßig in einem Containerimage festgelegten Einstiegspunkt benötigen, verwenden Sie den Befehl [docker image inspect](https://docs.docker.com/engine/reference/commandline/image_inspect/).

## <a name="command-line-syntax"></a>Befehlszeilensyntax

Die Befehlszeilensyntax variiert je nach Azure-API oder dem Tool, die bzw. das zum Erstellen der Instanzen verwendet wird. Wenn Sie eine Shellumgebung angeben, beachten Sie auch die Befehlssyntaxkonventionen der Shell.

* Befehl [az container create][az-container-create]: Übergeben Sie eine Zeichenfolge mit dem Parameter `--command-line`. Beispiel: `--command-line "python myscript.py arg1 arg2"`).

* Azure PowerShell-Cmdlet [New-AzureRmContainerGroup][new-azurermcontainergroup]: Übergeben Sie eine Zeichenfolge mit dem Parameter `-Command`. Beispiel: `-Command "echo hello"`.

* Azure-Portal: Geben Sie in der Eigenschaft **Command override** der Containerkonfiguration eine kommagetrennte Liste von Zeichenfolgen ohne Anführungszeichen an. Beispiel: `python, myscript.py, arg1, arg2`). 

* Resource Manager-Vorlage oder YAML-Datei oder eines der Azure-SDKs: Geben Sie die Befehlszeileneigenschaft als Array von Zeichenfolgen an. Beispiel: Das JSON-Array `["python", "myscript.py", "arg1", "arg2"]` in einer Resource Manager-Vorlage. 

  Wenn Sie mit der [Dockerfile](https://docs.docker.com/engine/reference/builder/)-Syntax vertraut sind, ist dieses Format ähnlich der *exec*-Form der CMD-Anweisung.

### <a name="examples"></a>Beispiele

|    |  Azure-Befehlszeilenschnittstelle   | Portal | Vorlage | 
| ---- | ---- | --- | --- |
| Einzelner Befehl | `--command-line "python myscript.py arg1 arg2"` | **Außerkraftsetzung von Befehl**: `python, myscript.py, arg1, arg2` | `"command": ["python", "myscript.py", "arg1", "arg2"]` |
| Mehrere Befehle | `--command-line "/bin/bash -c 'mkdir test; touch test/myfile; tail -f /dev/null'"` |**Außerkraftsetzung von Befehl**: `/bin/bash, -c, mkdir test; touch test/myfile; tail -f /dev/null` | `"command": ["/bin/bash", "-c", "mkdir test; touch test/myfile; tail -f /dev/null"]` |

## <a name="azure-cli-example"></a>Azure CLI-Beispiel

Ändern Sie z. B. das Verhalten des Containerimages [microsoft/aci-wordcount][aci-wordcount], das Text in Shakespeares *Hamlet* analysiert, um die am häufigsten vorkommenden Wörter zu finden. Anstatt *Hamlet* zu analysieren, können Sie eine Befehlszeile festlegen, die auf eine andere Textquelle verweist.

Um die Ausgabe des Containers [microsoft/aci-wordcount][aci-wordcount] anzuzeigen, wenn er den Standardtext analysiert, führen Sie ihn mit dem folgenden Befehl [az container create][az-container-create] aus. Es wird keine Startbefehlszeile angegeben, sodass der Standardcontainerbefehl ausgeführt wird. Zur Veranschaulichung legt dieses Beispiel [Umgebungsvariablen](container-instances-environment-variables.md) fest, um die drei wichtigsten Wörter zu finden, die mindestens fünf Buchstaben lang sind:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --environment-variables NumWords=3 MinLength=5 \
    --restart-policy OnFailure
```

Sobald für den Container der Status *Beendet* angezeigt wird, (verwenden Sie [az container show][az-container-show] zum Überprüfen des Status), zeigen Sie deren Protokolle mit [az container logs][az-container-logs] an, um die Ausgabe anzuzeigen.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
```

Ausgabe:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

Richten Sie jetzt einen zweiten Beispielcontainer ein, um einen anderen Text zu analysieren, indem Sie eine andere Befehlszeile angeben. Das vom Container ausgeführte Python-Skript (*wordcount.py*) akzeptiert eine URL als Argument und verarbeitet den Inhalt dieser Seite anstelle des Standardwerts.

Wenn Sie z. B. die häufigsten drei Wörter in *Romeo und Julia* ermitteln möchten, die mindestens fünf Zeichen lang sind:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

Zeigen Sie auch hier, wenn der Container den Status *Beendet* aufweist, die Ausgabe durch Aufrufen der Containerprotokolle an:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

Ausgabe:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>Nächste Schritte

Aufgabenbasierte Szenarien, z. B. Stapelverarbeitung eines großen Datasets mit mehreren Containern, können von benutzerdefinierten Befehlszeilen zur Laufzeit profitieren. Weitere Informationen zum Ausführen aufgabenbasierter Containern finden Sie unter [Ausführen von Aufgaben in Containern mit Neustartrichtlinien](container-instances-restart-policy.md).

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[new-azurermcontainergroup]: /powershell/module/azurerm.containerinstance/new-azurermcontainergroup
[portal]: https://portal.azure.com
