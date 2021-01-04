---
title: Verarbeiten von AKS-Knotenupgrades mit GitHub Actions
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie AKS-Knoten mithilfe von GitHub Actions aktualisieren.
services: container-service
ms.topic: article
ms.date: 11/27/2020
ms.openlocfilehash: 504c99a7305a5010f8dbe56a30b53d6fc3a13c5b
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96607839"
---
# <a name="apply-security-updates-to-azure-kubernetes-service-aks-nodes-automatically-using-github-actions"></a>Automatisches Anwenden von Sicherheitsupdates auf Azure Kubernetes Service-Knoten (AKS) mithilfe von GitHub Actions

Sicherheitsupdates sind ein wesentlicher Bestandteil der Aufrechterhaltung der Sicherheit und Compliance Ihres AKS-Clusters mit den neuesten Korrekturen für das zugrunde liegende Betriebssystem. Diese Updates enthalten Sicherheitsfixes für das Betriebssystem oder Kernelupdates. Einige Updates erfordern einen Neustart des Knotens, um den Vorgang abzuschließen.

Durch Ausführen von `az aks upgrade` haben Sie die Möglichkeit, Updates ohne Ausfallzeiten anzuwenden. Mit dem Befehl werden das Anwenden der neuesten Updates auf alle Knoten des Clusters, das Sperren und Abgleichen von Datenverkehr auf den Knoten und das Neustarten der Knoten sowie das anschließende Zulassen von Datenverkehr auf den aktualisierten Knoten verarbeitet. Wenn Sie die Knoten mit einer anderen Methode aktualisieren, werden sie in AKS nicht automatisch neu gestartet.

> [!NOTE]
> Der Hauptunterschied bei der Verwendung von `az aks upgrade` mit dem Flag `--node-image-only` besteht darin, dass nur die Knotenimages aktualisiert werden. Wenn das Flag nicht verwendet wird, werden sowohl die Knotenimages als auch die Version der Kubernetes-Steuerungsebene aktualisiert. Weitere ausführliche Informationen finden Sie in der Dokumentation für [verwaltete Upgrades auf Knoten][managed-node-upgrades-article] und für [Upgrades von Clustern][cluster-upgrades-article].

Alle Kubernetes-Knoten werden in einer standardmäßigen Azure-VM ausgeführt. Dabei kann es sich um Windows- oder Linux-basierte VMs handeln. Bei Linux-basierten VMs wird ein Ubuntu-Image verwendet, wobei das Betriebssystem so konfiguriert ist, dass automatisch jede Nacht nach Updates gesucht wird.

Bei Verwendung des Befehls `az aks upgrade` wird mit der Azure-Befehlszeilenschnittstelle eine Reihe neuer Knoten mit den neuesten Sicherheits- und Kernelupdates erstellt. Diese Knoten werden zunächst gesperrt, um zu verhindern, dass Anwendungen auf ihnen geplant werden, bis das Update abgeschlossen ist. Nach dessen Abschluss werden die älteren Knoten in Azure gesperrt (Knoten sind für die Planung neuer Workloads nicht mehr verfügbar) und geleert (vorhandene Workloads werden auf andere Knoten verschoben). Danach werden die Sperren der neuen Knoten aufgehoben, sodass alle geplanten Anwendungen effektiv auf die neuen Knoten übertragen werden.

Dieser Prozess ist besser als ein manuelles Update von Linux-basierten Kernels, da unter Linux bei der Installation eines neuen Kernelupdates ein Neustart erforderlich ist. Wenn Sie das Betriebssystem manuell aktualisieren, müssen Sie auch die VM neu starten und alle Anwendungen manuell sperren und ausgleichen.

In diesem Artikel wird beschrieben, wie Sie den Updateprozess von AKS-Knoten automatisieren können. Sie verwenden GitHub Actions und die Azure-Befehlszeilenschnittstelle, um eine Updateaufgabe basierend auf `cron` zu erstellen, die automatisch ausgeführt wird.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Außerdem muss mindestens die Version 2.0.59 der Azure CLI installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

Außerdem wird davon ausgegangen, dass Sie über ein [GitHub][github]-Konto verfügen, in dem Sie Aktionen erstellen können.

## <a name="create-a-timed-github-action"></a>Erstellen einer zeitgesteuerten GitHub-Aktion

`cron` ist ein Hilfsprogramm, mit dem Sie eine Gruppe von Befehlen oder einen Auftrag nach einem automatisierten Zeitplan ausführen können. Um einen Auftrag zum Aktualisieren der AKS-Knoten nach einem automatisierten Zeitplan erstellen zu können, benötigen Sie ein Repository zum Hosten der Aktionen. Normalerweise werden GitHub-Aktionen im gleichen Repository wie die Anwendung konfiguriert, Sie können jedoch auch ein anderes Repository verwenden. In diesem Artikel wird Ihr [Profilrepository][profile-repository] verwendet. Wenn Sie noch nicht über ein Repository verfügen, erstellen Sie ein neues Repository mit dem gleichen Namen wie Ihr GitHub-Benutzername.

1. Navigieren Sie zu Ihrem Repository in GitHub.
1. Klicken Sie oben auf der Seite auf die Registerkarte **Actions** (Aktionen).
1. Wenn Sie bereits einen Workflow in diesem Repository eingerichtet haben, werden Sie zur Liste der abgeschlossenen Ausführungen weitergeleitet. Klicken Sie in diesem Fall auf die Schaltfläche **New Workflow** (Neuer Workflow). Wenn dies der erste Workflow im Repository ist, werden in GitHub einige Projektvorlagen angezeigt. Klicken Sie unter dem Beschreibungstext auf den Link **Set up a workflow yourself** (Workflow selbst einrichten).
1. Ändern Sie die Tags `name` und `on` des Workflows ähnlich wie unten gezeigt. In GitHub Actions wird dieselbe [POSIX-cron-Syntax][cron-syntax] wie in allen Linux-basierten Systemen verwendet. In diesem Zeitplan wird festgelegt, dass der Workflow alle 15 Tage um 3.00 Uhr ausgeführt wird.

    ```yml
    name: Upgrade cluster node images
    on:
      schedule:
        - cron: '0 3 */15 * *'
    ```

1. Erstellen Sie anhand der folgenden Angaben einen neuen Auftrag. Dieser Auftrag hat den Namen `upgrade-node`, wird auf einem Ubuntu-Agent ausgeführt und stellt eine Verbindung mit Ihrem Azure CLI-Konto her, um die erforderlichen Schritte zum Aktualisieren der Knoten auszuführen.

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest
    ```

## <a name="set-up-the-azure-cli-in-the-workflow"></a>Einrichten der Azure-Befehlszeilenschnittstelle im Workflow

Im Schlüssel `steps` definieren Sie alle Aufgaben, die im Workflow zum Aktualisieren der Knoten ausgeführt werden.

Laden Sie die Azure-Befehlszeilenschnittstelle herunter, und melden Sie sich bei der Azure-Befehlszeilenschnittstelle an.

1. Suchen Sie rechts auf dem GitHub Actions-Bildschirm nach der *Marketplace-Suchleiste*, und geben Sie **Azure Login** (Azure-Anmeldung) ein.
1. Als Ergebnis wird die Aktion **Azure Login** (Azure-Anmeldung), veröffentlicht von Azure (**By Azure**), angezeigt:

      :::image type="content" source="media/node-upgrade-github-actions/azure-login-search.png" alt-text="Suchergebnisse mit zwei Zeilen: erste Aktion: „Azure Login“ (Azure-Anmeldung), zweite Aktion: „Azure Container Registry Login“ (Azure Container Registry-Anmeldung)":::

1. Klicken Sie auf **Azure Login** (Azure-Anmeldung). Klicken Sie auf dem nächsten Bildschirm rechts oben im Codebeispiel auf das **Kopiersymbol**.

    :::image type="content" source="media/node-upgrade-github-actions/azure-login.png" alt-text="Ergebnisbereich der Aktion „Azure Login“ (Azure-Anmeldung) mit Codebeispiel im unteren Bereich. Ein rotes Quadrat um das Kopiersymbol hebt den Klickpunkt hervor.":::

1. Fügen Sie unter dem Schlüssel `steps` Folgendes ein:

      ```yml
      name: Upgrade cluster node images

      on:
        schedule:
          - cron: '0 3 */15 * *'

      jobs:
        upgrade-node:
          runs-on: ubuntu-latest

          steps:
            - name: Azure Login
              uses: Azure/login@v1.1
              with:
                creds: ${{ secrets.AZURE_CREDENTIALS }}
      ```

1. Führen Sie in der Azure-Befehlszeilenschnittstelle den folgenden Befehl aus, um einen neuen Benutzernamen und ein neues Kennwort zu generieren.

    ```azurecli-interactive
    az ad sp create-for-rbac -o json
    ```

    Die Ausgabe sollte dem folgenden JSON-Code ähneln:

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-xxxx-xx-xx-xx-xx-xx",
      "name": "http://azure-cli-xxxx-xx-xx-xx-xx-xx",
      "password": "xXxXxXxXx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. Navigieren Sie **in einem neuen Browserfenster** zu Ihrem GitHub-Repository, und öffnen Sie die Registerkarte **Settings** (Einstellungen) des Repositorys. Klicken Sie auf **Secrets** (Geheimnisse) und dann auf **New Repository Secret** (Neues Repositorygeheimnis).
1. Verwenden Sie für *Name* den Wert `AZURE_CREDENTIALS`.
1.  Fügen Sie für *Value* (Wert) den gesamten Inhalt der Ausgabe des vorherigen Schritts ein, in dem Sie einen neuen Benutzernamen und ein neues Kennwort erstellt haben.

    :::image type="content" source="media/node-upgrade-github-actions/azure-credential-secret.png" alt-text="Formular mit AZURE_CREDENTIALS als Geheimnistitel und der Ausgabe des ausgeführten Befehls als eingefügter JSON-Code":::

1. Klicken Sie auf **Add Secret** (Geheimnis hinzufügen).

Die für die Aktion verwendete Befehlszeilenschnittstelle wird in Ihrem Azure-Konto protokolliert und kann zum Ausführen von Befehlen verwendet werden.

So erstellen Sie die Schritte zum Ausführen von Azure CLI-Befehlen

1. Navigieren Sie rechts auf dem Bildschirm zur **Suchleiste** für den *GitHub-Marketplace*, und suchen Sie nach *Azure CLI Action* (Azure CLI-Aktion). Wählen Sie *Azure CLI Action by Azure* (Azure CLI-Aktion von Azure) aus.

    :::image type="content" source="media/node-upgrade-github-actions/azure-cli-action.png" alt-text="Suchergebnis für „Azure CLI Action“ (Azure CLI-Aktion). Das erste Ergebnis wird als von Azure veröffentlicht angezeigt.":::

1. Klicken Sie im *Ergebnisbereich des GitHub-Marketplace* auf die Kopierschaltfläche, und fügen Sie den Inhalt der Aktion ähnlich wie im folgenden Beispiel im Hauptbereich des Editors unter dem Schritt *Azure Login* (Azure-Anmeldung) ein:

    ```yml
    name: Upgrade cluster node images

    on:
      schedule:
        - cron: '0 3 */15 * *'

    jobs:
      upgrade-node:
        runs-on: ubuntu-latest

        steps:
          - name: Azure Login
            uses: Azure/login@v1.1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}
          - name: Upgrade node images
            uses: Azure/cli@v1.0.0
            with:
              inlineScript: az aks upgrade -g {resourceGroupName} -n {aksClusterName} --node-image-only
    ```

    > [!TIP]
    > Sie können die Parameter `-g` und `-n` vom Befehl entkoppeln, indem Sie sie ähnlich wie bei den vorherigen Schritten Geheimnissen hinzufügen. Ersetzen Sie die Platzhalter `{resourceGroupName}` und `{aksClusterName}` durch ihre entsprechenden Geheimnisse, z. B. `${{secrets.RESOURCE_GROUP_NAME}}` und `${{secrets.AKS_CLUSTER_NAME}}`.

1. Benennen Sie die Datei in `upgrade-node-images` um.
1. Klicken Sie auf **Start Commit** (Commit starten), fügen Sie einen Meldungstitel hinzu, und speichern Sie den Workflow.

Nachdem Sie den Commit erstellt haben, wird der Workflow gespeichert und kann ausgeführt werden.

> [!NOTE]
> Um einen einzelnen Knotenpool anstelle aller Knotenpools im Cluster zu aktualisieren, fügen Sie dem Befehl `az aks nodepool upgrade` den Parameter `--name` hinzu, um den Namen des Knotenpools anzugeben. Beispiel:
> ```azurecli-interactive
> az aks nodepool upgrade -g {resourceGroupName} --cluster-name {aksClusterName} --name {{nodePoolName}} --node-image-only
> ```

## <a name="run-the-github-action-manually"></a>Manuelles Ausführen der GitHub-Aktion

Sie können den Workflow zusätzlich zur geplanten Ausführung manuell ausführen, indem Sie einen neuen `on`-Trigger mit der Bezeichnung `workflow_dispatch` hinzufügen. Die fertige Datei sollte wie die folgende YAML-Datei aussehen:

```yml
name: Upgrade cluster node images

on:
  schedule:
    - cron: '0 3 */15 * *'
  workflow_dispatch:

jobs:
  upgrade-node:
    runs-on: ubuntu-latest

    steps:
      - name: Azure Login
        uses: Azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      # Code for upgrading one or more node pools
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den neuesten Knotenimages finden Sie in den [AKS-Versionshinweisen](https://github.com/Azure/AKS/releases).
- Erfahren Sie, wie Sie die Kubernetes-Version durch [Aktualisieren eines AKS-Clusters][cluster-upgrades-article] aktualisieren können.
- Weitere Informationen zu mehreren Knotenpools und zum Durchführen von Upgrades für Knotenpools erhalten Sie unter [Erstellen und Verwalten von mehreren Knotenpools][use-multiple-node-pools].
- Weitere Informationen zu [Systemknotenpools][system-pools].
- Informationen zum Einsparen von Kosten mithilfe von Spot-Instanzen finden Sie unter [Hinzufügen eines Spot-Knotenpools zu AKS][spot-pools].

<!-- LINKS - external -->
[github]: https://github.com
[profile-repository]: https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-github-profile/about-your-profile
[cron-syntax]: https://pubs.opengroup.org/onlinepubs/9699919799/utilities/crontab.html#tag_20_25_07

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[managed-node-upgrades-article]: node-image-upgrade.md
[cluster-upgrades-article]: upgrade-cluster.md
[system-pools]: use-system-pools.md
[spot-pools]: spot-node-pool.md
[use-multiple-node-pools]: use-multiple-node-pools.md
