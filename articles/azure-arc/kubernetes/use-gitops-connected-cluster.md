---
title: Bereitstellen von Konfigurationen mithilfe von GitOps in Arc-fähigen Kubernetes-Clustern (Vorschauversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Verwenden von GitOps für Azure Arc-fähiger Clusterkonfiguration (Vorschauversion)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, Container
ms.openlocfilehash: ce6c754c308d2979db9b1b8eb36e7858e8a91c3c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659793"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Bereitstellen von Konfigurationen mithilfe von GitOps in Arc-fähigen Kubernetes-Clustern (Vorschauversion)

GitOps ist die Praxis des Deklarierens des gewünschten Zustands einer Kubernetes-Konfiguration (Bereitstellungen, Namespaces usw.) in einem Git-Repository gefolgt von einer abruf- und pullbasierten Bereitstellung dieser Konfigurationen im Cluster mithilfe eines Operators. In diesem Dokument wird das Einrichten solcher Workflows in Azure Arc-fähigen Kubernetes-Clustern beschrieben.

Die Verbindung zwischen Ihrem Cluster und mindestens einem Git-Repository wird in Azure Resource Manager als `sourceControlConfiguration`-Erweiterungsressource nachverfolgt. Die `sourceControlConfiguration`-Ressourceneigenschaften stellen dar, wohin und wie Kubernetes-Ressourcen von Git an Ihren Cluster weitergeleitet werden sollen. Die `sourceControlConfiguration`-Daten werden im Ruhezustand verschlüsselt in einer Azure Cosmos DB-Datenbank gespeichert, um ihre Vertraulichkeit zu gewährleisten.

Der in Ihrem Cluster ausgeführte `config-agent` ist für die Überwachung neuer oder aktualisierter `sourceControlConfiguration`-Erweiterungsressourcen in der Azure Arc-fähigen Kubernetes-Ressource, das Bereitstellen eines Flux-Operators zum Überwachen des Git-Repositorys und das Weitergeben von Updates von `sourceControlConfiguration` verantwortlich. Es ist sogar möglich, mehrere `sourceControlConfiguration`-Ressourcen mit `namespace`-Bereich im gleichen Azure Arc-fähigen Kubernetes-Cluster zu erstellen, um die Mehrinstanzenfähigkeit zu erzielen. In einem solchen Fall kann jeder Operator nur Konfigurationen für den jeweiligen Namespace bereitstellen.

Das Git-Repository kann gültige Kubernetes-Ressourcen einschließlich Namespaces, ConfigMaps, Bereitstellungen, DaemonSets usw. enthalten.  Es kann auch Helm-Charts für die Bereitstellung von Anwendungen enthalten. Mehrere allgemeine Szenarios umfassen das Definieren einer Baselinekonfiguration für Ihre Organisation, die allgemeine Azure-Rollen und -Bindungen, Überwachungs- oder Protokollierungs-Agents oder clusterweite Dienste beinhalten kann.

Das gleiche Muster kann verwendet werden, um eine größere Sammlung von Clustern zu verwalten, die in heterogenen Umgebungen bereitgestellt werden können. Sie können beispielsweise mit einem Repository die Baselinekonfiguration für Ihre Organisation definieren und diese auf zehn Kubernetes-Cluster gleichzeitig anwenden. [Azure Policy kann die Erstellung](use-azure-policy.md) einer `sourceControlConfiguration`-Ressource mit bestimmten Parametern in allen Azure Arc-fähigen Kubernetes-Ressourcen in einem Bereich (Abonnement oder Ressourcengruppe) automatisieren.

Dieser Leitfaden für die ersten Schritte erläutert das Anwenden mehrerer Konfigurationen für Cluster auf Administratorebene.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über einen verbundenen, Azure Arc-fähigen Kubernetes-Cluster verfügen. Wenn Sie einen verbundenen Cluster benötigen, finden Sie weitere Informationen im [Schnellstart zum Verbinden eines Clusters](./connect-cluster.md).

## <a name="create-a-configuration"></a>Erstellen einer Konfiguration

Das in diesem Dokument verwendete [Beispielrepository](https://github.com/Azure/arc-k8s-demo) ist um die Persona eines Clusteroperators strukturiert, der einige Namespaces, eine gemeinsame Workload und teamspezifische Konfigurationen bereitstellen möchte. Durch Verwendung dieses Repositorys werden die folgenden Ressourcen in Ihrem Cluster erstellt:

**Namespaces:** `cluster-config`, `team-a`, `team-b`
**Bereitstellung:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

Der `config-agent` fragt Azure alle 30 Sekunden nach neuen oder aktualisierten `sourceControlConfiguration`-Ressourcen. Dies ist der maximale Zeitraum, den der `config-agent` zum Abrufen einer neuen oder aktualisierten Konfiguration benötigt.
Stellen Sie sicher, dass Sie auch die Schritte unter [Anwenden der Konfiguration über ein privates Git-Repository](#apply-configuration-from-a-private-git-repository) ausführen, wenn Sie ein privates Repository mit `sourceControlConfiguration` zuordnen.

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

Mit der Azure CLI-Erweiterung für `k8sconfiguration` verknüpfen wir den verbundenen Cluster mit einem [Git-Beispielrepository](https://github.com/Azure/arc-k8s-demo). Wir geben dieser Konfiguration den Namen `cluster-config`, weisen den Agent an, den Operator im `cluster-config`-Namespace bereitzustellen, und erteilen ihm `cluster-admin`-Berechtigungen.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>Parameter „repository-url“

Im Folgenden finden Sie die unterstützten Szenarios für den Wert des Parameters „--repository-url“.

| Szenario | Format | BESCHREIBUNG |
| ------------- | ------------- | ------------- |
| Öffentliches Git-Repository | http[s]://server/repo.git or git://server/repo.git   | Öffentliches Git-Repository  |
| Privates Git-Repository – SSH – mit Flux erstellte Schlüssel | ssh://[user@]server/repo.git or [user@]server:repo.git | Der von Flux generierte öffentliche Schlüssel muss dem Benutzerkonto in Ihrem Git-Dienstanbieter hinzugefügt werden. Wenn der Bereitstellungsschlüssel dem Repository statt dem Benutzerkonto hinzugefügt wird, verwenden Sie `git@` anstelle von `user@`. Weitere Informationen finden Sie [hier](#apply-configuration-from-a-private-git-repository) |

Diese Szenarios werden von Flux, jedoch noch nicht von der „sourceControlConfiguration“-Ressource unterstützt.

| Szenario | Format | Beschreibung |
| ------------- | ------------- | ------------- |
| Privates Git-Repository – HTTPS | https://server/repo.git | In Kürze verfügbar (Unterstützung für Benutzername/Kennwort, Benutzername/Token, Zertifikat) |
| Privates Git-Repository – SSH – benutzerseitig bereitgestellte Schlüssel | ssh://[user@]server/repo.git or [user@]server:repo.git | In Kürze verfügbar |
| Privater Git-Host – SSH – benutzerdefinierte known_hosts | ssh://[user@]server/repo.git or [user@]server:repo.git | In Kürze verfügbar |

#### <a name="additional-parameters"></a>Zusätzliche Parameter

Hier finden Sie einige zusätzliche Parameter, um die Erstellung der Konfiguration anzupassen.

`--enable-helm-operator` : *Optionaler* Switch zum Aktivieren der Unterstützung für Helm-Chartbereitstellungen.

`--helm-operator-chart-values` : *Optionale* Chartwerte für den Helm-Operator (falls aktiviert).  Beispiel: „--set helm.versions=v3“

`--helm-operator-chart-version` : *Optionale* Chartversion für den Helm-Operator (falls aktiviert). Standardwert: „0.6.0“

`--operator-namespace` : *Optionaler* Name für den Operatornamespace. Standardwert: „default“

`--operator-params` : *Optionale* Parameter für den Operator. Diese müssen in einfachen Anführungszeichen angegeben werden. Zum Beispiel, ```--operator-params='--git-readonly --git-path=releases' ```

Unterstützte Optionen in „--operator-params“

| Option | BESCHREIBUNG |
| ------------- | ------------- |
| --git-branch  | Dies ist die Verzweigung des Git-Repositorys für Kubernetes-Manifeste. Der Standardwert ist „master“. |
| --git-path  | Dies ist der relative Pfad im Git-Repository für Flux zum Suchen von Kubernetes-Manifesten. |
| --git-readonly | Das Git-Repository wird als schreibgeschützt betrachtet. Flux versucht nicht, in die Datei zu schreiben. |
| --manifest-generation  | Wenn diese Option aktiviert ist, sucht Flux nach „.flux.yaml“ und führt Kustomize oder andere Manifest-Generatoren aus. |
| --git-poll-interval  | Dies ist die Zeitspanne, in der das Git-Repository für neue Commits abgerufen wird. Der Standardwert ist „5m“ (fünf Minuten). |
| --sync-garbage-collection  | Wenn diese Option aktiviert ist, werden die von Flux erstellten Ressourcen gelöscht und sind nicht mehr in Git verfügbar. |
| --git-label  | Dies ist die Bezeichnung zum Nachverfolgen des Synchronisierungsprozesses, der verwendet wird, um die Git-Verzweigung zu markieren.  Der Standardwert ist „flux-sync“. |
| --git-user  | Dies ist der Benutzername für Git-Commits. |
| --git-email  | Dies ist die für Git-Commits zu verwendende E-Mail-Adresse. |

* Wenn „--git-user“ oder „--git-email“ nicht festgelegt ist, da Sie nicht möchten, dass Flux in das Repository schreibt, wird „--git-readonly“ (falls nicht bereits festgelegt) automatisch festgelegt.

* Wenn „enableHelmOperator“ den Wert „True“ aufweist, können die Zeichenfolgen „operatorInstanceName“ und „operatorNamespace“ zusammen nicht mehr als 47 Zeichen enthalten.  Wenn Sie diesen Grenzwert nicht einhalten, erhalten Sie den folgenden Fehler:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Weitere Informationen finden Sie in der [Flux-Dokumentation](https://aka.ms/FluxcdReadme).

> [!TIP]
> Es ist ebenfalls möglich, im Azure-Portal auf der Registerkarte **Konfigurationen** im Bereich der Azure Arc-fähigen Kubernetes-Ressource eine sourceControlConfiguration-Ressource zu erstellen.

## <a name="validate-the-sourcecontrolconfiguration"></a>Überprüfen der „sourceControlConfiguration“-Ressource

Überprüfen Sie mithilfe der Azure CLI, ob die `sourceControlConfiguration`-Ressource erfolgreich erstellt wurde.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Beachten Sie, dass die `sourceControlConfiguration`-Ressource mit einem Compliancestatus, Nachrichten und Debuginformationen aktualisiert wird.

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Wenn die `sourceControlConfiguration`-Ressource erstellt wird, geschieht im Hintergrund Folgendes:

1. Der `config-agent` von Azure Arc überwacht den Azure Resource Manager auf neue oder aktualisierte Konfigurationen (`Microsoft.KubernetesConfiguration/sourceControlConfiguration`).
1. Der `config-agent` bemerkt die neue `Pending`-Konfiguration.
1. Der `config-agent` liest die Konfigurationseigenschaften und bereitet die Bereitstellung einer verwalteten Instanz von `flux` vor.
    * Der `config-agent` erstellt den Zielnamespace.
    * Der `config-agent` bereitet ein Kubernetes-Dienstkonto mit der entsprechenden Berechtigung vor (`cluster`- oder `namespace`-Bereich).
    * Der `config-agent` stellt eine Instanz von `flux` bereit.
    * `flux` generiert einen SSH-Schlüssel und protokolliert den öffentlichen Schlüssel.
1. Der `config-agent` meldet den Status zurück an die `sourceControlConfiguration`-Ressource.

Während des Bereitstellungsprozesses wird der Status der `sourceControlConfiguration`-Ressource mehrfach geändert. Überwachen Sie den Fortschritt mit dem obigen `az k8sconfiguration show ...`-Befehl:

1. `complianceStatus` -> `Pending`: Hiermit werden der anfängliche Status und der Status während der Bearbeitung dargestellt.
1. `complianceStatus` -> `Installed`: Der `config-agent` konnte den Cluster erfolgreich konfigurieren und `flux` ohne Fehler bereitstellen.
1. `complianceStatus` -> `Failed`: Der `config-agent` hat einen Fehler beim Bereitstellen von `flux` gefunden, die Details sind im Antworttext `complianceStatus.message` verfügbar.

## <a name="apply-configuration-from-a-private-git-repository"></a>Anwenden der Konfiguration über ein privates Git-Repository

Wenn Sie ein privates Git-Repository verwenden, müssen Sie eine weitere Aufgabe ausführen: Fügen Sie den von `flux` generierten öffentlichen Schlüssel als **Bereitstellungsschlüssel** im Repository hinzu.

**Abrufen des öffentlichen Schlüssels mithilfe der Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Abrufen des öffentlichen Schlüssels über das Azure-Portal**

1. Navigieren Sie im Azure-Portal zur verbundenen Clusterressource.
2. Klicken Sie auf der Ressourcenseite auf „Konfigurationen“, um die Liste der Konfigurationen für diesen Cluster anzuzeigen.
3. Klicken Sie auf die Konfiguration, die das private Git-Repository verwendet.
4. Kopieren Sie den **öffentlichen Repositoryschlüssel** im geöffneten Kontextfenster unten auf der Seite.

Wenn Sie GitHub verwenden, verwenden Sie eine der folgenden beiden Optionen:

**Option 1: Hinzufügen des öffentlichen Schlüssels zu Ihrem Benutzerkonto**

1. Öffnen Sie GitHub, und klicken Sie in der oberen rechten Ecke der Seite auf Ihr Profilsymbol.
2. Klicken Sie auf **Einstellungen**.
3. Klicken Sie auf **SSH- und GPG-Schlüssel**.
4. Klicken Sie auf **Neuer SSH-Schlüssel**.
5. Geben Sie einen Titel an.
6. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
7. Klicken Sie auf **SSH-Schlüssel hinzufügen**.

**Option 2: Hinzufügen des öffentlichen Schlüssels als Bereitstellungsschlüssel zum Git-Repository**

1. Öffnen Sie GitHub, navigieren Sie zunächst zu Ihrem Repository, dann zu **Einstellungen** und anschließend zu **Deploy keys** (Bereitstellungsschlüssel).
2. Klicken Sie auf **Bereitstellungsschlüssel hinzufügen**.
3. Geben Sie einen Titel an.
4. Aktivieren Sie die Option **Allow write access** (Schreibzugriff gewähren).
5. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
6. Klicken Sie auf **Schlüssel hinzufügen**.

**Hinzufügen des Schlüssels zu den SSH-Schlüsseln bei Verwendung eines Azure DevOps-Repositorys**

1. Klicken Sie unter **Benutzereinstellungen** oben rechts (neben dem Profilbild) auf **Öffentliche SSH-Schlüssel**.
1. Klicken Sie auf **Neuer Schlüssel**.
1. Geben Sie einen Namen an.
1. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
1. Klicken Sie auf **Hinzufügen**.

## <a name="validate-the-kubernetes-configuration"></a>Überprüfen der Kubernetes-Konfiguration

Nachdem der `config-agent` die `flux`-Instanz installiert hat, sollten die im Git-Repository gespeicherten Ressourcen an den Cluster weitergeleitet werden. Überprüfen Sie, ob die Namcespaces, Bereitstellungen und Ressourcen erstellt wurden:

```console
kubectl get ns --show-labels
```

**Ausgabe:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Wir sehen, dass die Namespaces `team-a`, `team-b`, `itops` und `cluster-config` erstellt wurden.

Der `flux`-Operator wurde wie von unserer `sourceControlConfig`-Ressource im `cluster-config`-Namespace bereitgestellt:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Ausgabe:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Weitere Untersuchungen

Sie können die anderen Ressourcen untersuchen, die als Teil des Konfigurationsrepositorys bereitgestellt werden:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Löschen einer Konfiguration

Löschen Sie eine `sourceControlConfiguration`-Ressource mithilfe der Azure CLI oder über das Azure-Portal.  Nachdem Sie den Löschbefehl initiiert haben, wird die `sourceControlConfiguration`-Ressource sofort in Azure gelöscht. Es kann jedoch bis zu einer Stunde dauern, bis die zugeordneten Objekte aus dem Cluster gelöscht werden (Zeitspanne kann mit Backlog Item verkürzt werden).

> [!NOTE]
> Nachdem eine sourceControlConfiguration-Ressource mit Namespacebereich erstellt wurde, können Benutzer mit der `edit`-Rollenbindung an den Namespace Workloads für diesen Namespace bereitstellen. Wenn diese `sourceControlConfiguration`-Ressource mit Namespacebereich gelöscht wird, bleibt der Namespace intakt und wird nicht gelöscht, um zu vermeiden, dass diese anderen Workloads unterbrochen werden.
> Alle Änderungen am Cluster, die das Ergebnis von Bereitstellungen aus dem nachverfolgten Git-Repository sind, werden beim Löschen der `sourceControlConfiguration`-Ressource nicht gelöscht.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von GitOps mit Helm für Azure Arc-fähige Clusterkonfiguration (Vorschauversion)](./use-gitops-with-helm.md)
- [Verwenden von Azure Policy zum Anwenden von Clusterkonfigurationen im großen Stil (Vorschau)](./use-azure-policy.md)
