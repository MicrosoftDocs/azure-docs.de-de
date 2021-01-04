---
title: Git-Integration für Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Azure Machine Learning in ein lokales Git-Repository integriert wird, um Repository-, Branch- und aktuelle Commit-Informationen als Teil einer Trainingsausführung nachzuverfolgen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 11/16/2020
ms.openlocfilehash: 989fc7cb66cf5381d174a3aad12f84f5b055aab8
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701635"
---
# <a name="git-integration-for-azure-machine-learning"></a>Git-Integration für Azure Machine Learning

[Git](https://git-scm.com/) ist ein beliebtes Versionskontrollsystem, mit dem Sie Projekte freigeben und gemeinsam mit anderen an ihnen arbeiten können. 

Azure Machine Learning unterstützt Git-Repositorys zur Nachverfolgung der Arbeit – Sie können Repositorys direkt für Ihr freigegebenes Arbeitsbereichsdateisystem klonen, Git auf Ihrer lokalen Arbeitsstation verwenden oder Git über eine CI/CD-Pipeline verwenden.

Wenn beim Übermitteln eines Auftrags an Azure Machine Learning die Quelldateien in einem lokalen Git-Repository gespeichert sind, werden Informationen über das Repository als Teil des Trainingsprozesses nachverfolgt.

Da Azure Machine Learning Informationen aus einem lokalen Git-Repository nachverfolgt, ist es nicht an ein spezifisches zentrales Repository gebunden. Ihr Repository kann von GitHub, GitLab, Bitbucket, Azure DevOps oder einem beliebigen anderen, mit Git kompatiblen Dienst geklont sein.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Klonen von Git-Respositorys für das Arbeitsbereichsdateisystem
Azure Machine Learning bietet ein gemeinsames Dateisystem für alle Benutzer im Arbeitsbereich.
Um ein Git-Repository für diese Dateifreigabe zu klonen, empfehlen wir Ihnen, eine Compute-Instanz zu erstellen und ein Terminal zu öffnen.
Nachdem das Terminal geöffnet ist, haben Sie Zugriff auf einen vollständigen Git-Client und können über die Git-CLI-Oberfläche entsprechend Git klonen und mit Git arbeiten.

Es wird empfohlen, dass Sie das Repository in das Verzeichnis Ihrer Benutzer klonen, damit andere keine Konflikte direkt in Ihrem Arbeitsbranch verursachen.

Sie können ein beliebiges Git-Repository klonen, bei dem Sie sich authentifizieren können (GitHub, Azure Repos, BitBucket, usw.)

Weitere Informationen zum Klonen finden Sie im Leitfaden zum [Verwenden der Git CLI](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Authentifizieren Ihres Git-Kontos mit SSH
### <a name="generate-a-new-ssh-key"></a>Generieren eines neuen SSH-Schlüssels
1) [Öffnen Sie das Terminalfenster](./how-to-run-jupyter-notebooks.md#terminal) auf der Registerkarte Azure Machine Learning-Notebook.

2) Fügen Sie den Text darunter ein, und setzen Sie dabei Ihre E-Mail-Adresse ein.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Dadurch wird ein neuer SSH-Schlüssel erstellt, wobei die bereitgestellte E-Mail als Bezeichnung verwendet wird.

```
> Generating public/private rsa key pair.
```

3) Wenn Sie zur Eingabe einer Datei aufgefordert werden, in der der Schlüssel gespeichert werden soll, drücken Sie die EINGABETASTE. Damit wird der Standardspeicherort der Datei akzeptiert.

4) Vergewissern Sie sich, dass der Standardspeicherort „/home/azureuser/.ssh“ lautet, und drücken Sie die EINGABETASTE. Geben Sie andernfalls den Speicherort „/home/azureuser/.ssh“ an.

> [!TIP]
> Stellen Sie sicher, dass der SSH-Schlüssel in „/home/azureuser/.ssh“ gespeichert wird. Diese Datei wird auf der Compute-Instanz gespeichert und ist nur für den Besitzer der Compute-Instanz zugänglich.

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) Geben Sie an der Eingabeaufforderung eine sichere Passphrase ein. Es wird empfohlen, dem SSH-Schlüssel eine Passphrase hinzuzufügen, um die Sicherheit zu erhöhen.

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Hinzufügen des öffentlichen Schlüssels zum Git-Konto
1) Kopieren Sie in Ihrem Terminalfenster den Inhalt der Datei mit dem öffentlichen Schlüssel. Wenn Sie den Schlüssel umbenannt haben, ersetzen Sie „id_rsa.pub“ durch den Namen der Datei mit dem öffentlichen Schlüssel.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Kopieren und Einfügen in das Terminal**
> * Windows: Drücken Sie `Ctrl-Insert` zum Kopieren und `Ctrl-Shift-v` oder `Shift-Insert` zum Einfügen.
> * Mac OS: Drücken Sie `Cmd-c` zum Kopieren und `Cmd-v` zum Einfügen.
> * FireFox/IE unterstützen die Berechtigungen für die Zwischenablage möglicherweise nicht ordnungsgemäß.

2) Wählen Sie die Schlüsselausgabe aus, und kopieren Sie sie in die Zwischenablage.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [Azure DevOps](/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs): Beginnen Sie mit **Schritt 2**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Beginnen Sie mit **Schritt 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Klonen des Git-Repositorys mit SSH

1) Kopieren Sie die URL des SSH-Git-Klons aus dem Git-Repository.

2) Fügen Sie die URL in den `git clone`-Befehl unten ein, um die URL Ihres SSH-Git-Repositorys zu verwenden. Das sieht ungefähr wie folgt aus:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Es wird eine Antwort wie die folgende angezeigt:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

SSH zeigt möglicherweise den SSH-Fingerabdruck des Servers an und fordert Sie auf, ihn zu überprüfen. Sie sollten überprüfen, ob der angezeigte Fingerabdruck mit einem der Fingerabdrücke auf der Seite mit den öffentlichen SSH-Schlüsseln übereinstimmt.

SSH zeigt diesen Fingerabdruck an, wenn es eine Verbindung mit einem unbekannten Host herstellt, um Sie vor [Man-in-the-Middle-Angriffen](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)) zu schützen. Sobald Sie den Fingerabdruck des Hosts akzeptiert haben, werden Sie von SSH nicht mehr aufgefordert, es sei denn, der Fingerabdruck ändert sich.

3) Wenn Sie gefragt werden, ob Sie das Herstellen der Verbindung fortsetzen möchten, geben Sie `yes` ein. Git wird das Repository klonen und den Remoteursprung einrichten, um für zukünftige Git-Befehle eine Verbindung mit SSH einzurichten.

## <a name="track-code-that-comes-from-git-repositories"></a>Nachverfolgen von Code, der aus Git-Repositorys stammt

Wenn Sie einen Trainingslauf vom Python SDK oder der Machine Learning-CLI übermitteln, werden die zum Trainieren des Modells erforderlichen Dateien in Ihren Arbeitsbereich hochgeladen. Wenn der `git`-Befehl in Ihrer Entwicklungsumgebung verfügbar ist, wird er vom Uploadvorgang verwendet, um zu überprüfen, ob die Dateien in einem Git-Repository gespeichert sind. Ist dies der Fall, werden die Informationen aus Ihrem Git-Repository ebenfalls als Bestandteil des Trainingslaufs hochgeladen. Diese Informationen sind in den folgenden Eigenschaften für den Trainingslauf gespeichert:

| Eigenschaft | Git-Befehl zum Abrufen des Werts | BESCHREIBUNG |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Der URI, von dem Ihr Repository geklont wurde. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Der URI, von dem Ihr Repository geklont wurde. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Der aktive Branch bei der Übermittlung des Laufs. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Der aktive Branch bei der Übermittlung des Laufs. |
| `azureml.git.commit` | `git rev-parse HEAD` | Der Commithash des Codes, der für den Lauf übermittelt wurde. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Der Commithash des Codes, der für den Lauf übermittelt wurde. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, wenn der Branch/Commit geändert wurde („dirty“), andernfalls `false`. |

Diese Informationen werden für Läufe gesendet, die einen Estimator, eine Machine Learning-Pipeline oder eine Skriptausführung verwenden.

Wenn Ihre Trainingsdateien nicht in einem Git-Repository in Ihrer Entwicklungsumgebung gespeichert sind oder der `git`-Befehl nicht verfügbar ist, werden keine Git-bezogenen Informationen nachverfolgt.

> [!TIP]
> Um zu überprüfen, ob der Git-Befehl in Ihrer Entwicklungsumgebung verfügbar ist, öffnen Sie eine Shellsitzung, eine Eingabeaufforderung, eine PowerShell oder eine andere Befehlszeilenschnittstelle, und geben Sie den folgenden Befehl ein:
>
> ```
> git --version
> ```
>
> Sofern installiert und im Pfad enthalten, erhalten Sie eine Antwort wie `git version 2.4.1`. Weitere Informationen zur Installation von Git in der Entwicklungsumgebung finden Sie auf der [Git-Website](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Anzeigen der protokollierten Informationen

Die Git-Informationen sind in den Eigenschaften für einen Trainingslauf gespeichert. Sie können diese Informationen mithilfe von Azure-Portal, Python SDK und CLI anzeigen. 

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie im [Studioportal](https://ml.azure.com) Ihren Arbeitsbereich aus.
1. Wählen Sie __Experimente__ und dann eins Ihrer Experimente aus.
1. Wählen Sie einen der Läufe in der Spalte __Ausführungsanzahl__ aus.
1. Wählen Sie __Ausgaben und Protokolle__ aus, und erweitern Sie dann die Einträge __logs__ und __azureml__. Wählen Sie den Link aus, der mit __###\_azure__ beginnt.

Die protokollierten Informationen enthalten Text, der so ähnlich aussieht wie der folgende JSON-Code:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Python SDK

Nach dem Übermitteln eines Trainingslaufs wird ein [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)-Objekt zurückgegeben. Das `properties`-Attribut dieses Objekts enthält die protokollierten Git-Informationen. Beispielsweise ruft der folgende Code den Commithash ab:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Der CLI-Befehl `az ml run` kann verwendet werden, um die Eigenschaften eines Laufs abzurufen. Beispielsweise gibt der folgende Befehl die Eigenschaften für den letzten Lauf im Experiment mit dem Namen `train-on-amlcompute` zurück:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Weitere Informationen finden Sie in der Referenzdokumentation zu [az ml run](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Computeziele für das Modelltraining](how-to-set-up-training-targets.md)