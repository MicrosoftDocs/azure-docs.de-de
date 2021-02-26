---
title: Bereitstellen von Konfigurationen mithilfe von GitOps in Arc-fähigen Kubernetes-Clustern (Vorschauversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Verwenden von GitOps zum Konfigurieren eines Azure Arc-fähigen Kubernetes-Clusters (Vorschau)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, Container
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988702"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Bereitstellen von Konfigurationen mithilfe von GitOps in Arc-fähigen Kubernetes-Clustern (Vorschauversion)

GitOps ist, das es auf Kubernetes basiert, die Praxis des Deklarierens des gewünschten Zustands einer Kubernetes-Konfiguration (Bereitstellungen, Namespaces usw.) in einem Git-Repository gefolgt von einer abruf- und pullbasierten Bereitstellung dieser Konfigurationen im Cluster mithilfe eines Operators. In diesem Dokument wird das Einrichten solcher Workflows in Azure Arc-fähigen Kubernetes-Clustern beschrieben.

Die Verbindung zwischen Ihrem Cluster und einem Git-Repository wird in Azure Resource Manager als `Microsoft.KubernetesConfiguration/sourceControlConfigurations`-Erweiterungsressource erstellt. Die `sourceControlConfiguration`-Ressourceneigenschaften stellen dar, wohin und wie Kubernetes-Ressourcen von Git an Ihren Cluster weitergeleitet werden sollen. Die `sourceControlConfiguration`-Daten werden im Ruhezustand verschlüsselt in einer Azure Cosmos DB-Datenbank gespeichert, um ihre Vertraulichkeit zu gewährleisten.

Der in Ihrem Cluster ausgeführte `config-agent` ist für die Überwachung neuer oder aktualisierter `sourceControlConfiguration`-Erweiterungsressourcen in der Azure Arc-fähigen Kubernetes-Ressource, für das Bereitstellen eines Flux-Operators zum Überwachen des Git-Repositorys für jede `sourceControlConfiguration` und für das Anwenden aller Updates auf jede `sourceControlConfiguration` verantwortlich. Es ist möglich, mehrere `sourceControlConfiguration`-Ressourcen im selben Azure Arc-fähigen Kubernetes-Cluster zu erstellen, um die Mehrinstanzenfähigkeit zu erzielen. Sie können jede `sourceControlConfiguration` mit einem anderen `namespace`-Bereich erstellen, um Bereitstellungen auf innerhalb der jeweiligen Namespaces zu beschränken.

Das Git-Repository kann Manifeste im YAML-Fomat enthalten, die gültige Kubernetes-Ressourcen einschließlich Namespaces, ConfigMaps, Bereitstellungen, DaemonSets usw. beschreiben.  Es kann auch Helm-Charts für die Bereitstellung von Anwendungen enthalten. Mehrere allgemeine Szenarios umfassen das Definieren einer Baselinekonfiguration für Ihre Organisation, die allgemeine Azure-Rollen und -Bindungen, Überwachungs- oder Protokollierungs-Agents oder clusterweite Dienste beinhalten kann.

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

Verwenden Sie die Azure CLI-Erweiterung für `k8sconfiguration`, um einen verbundenen Cluster mit dem [Git-Beispielrepository](https://github.com/Azure/arc-k8s-demo) zu verknüpfen. Wir geben dieser Konfiguration den Namen `cluster-config`, weisen den Agent an, den Operator im `cluster-config`-Namespace bereitzustellen, und erteilen ihm `cluster-admin`-Berechtigungen.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>Verwenden eines öffentlichen Git-Repositorys

| Parameter | Format |
| ------------- | ------------- |
| --repository-url | http[s]://server/repo[.git] oder git://server/repo[.git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Verwenden eines privaten Git-Repositorys mit SSH und mit Flux erstellten Schlüsseln

| Parameter | Format | Notizen
| ------------- | ------------- | ------------- |
| --repository-url | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen

> [!NOTE]
> Der mit Flux generierte öffentliche Schlüssel muss dem Benutzerkonto in Ihrem Git-Dienstanbieter hinzugefügt werden. Wenn der Schlüssel dem Repository statt dem Benutzerkonto hinzugefügt wird, verwenden Sie in der URL `git@` anstelle von `user@`. [Anzeigen weiterer Details](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Verwenden eines privaten Git-Repositorys mit SSH und benutzerseitig bereitgestellten Schlüsseln

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen |
| --ssh-private-key | base64-codierter Schlüssel im [PEM-Format](https://aka.ms/PEMformat) | Direktes Bereitstellen des Schlüssels |
| --ssh-private-key-file | Vollständiger Pfad zur lokalen Datei | Bereitstellen des vollständigen Pfads zur lokalen Datei, die den Schlüssel im PEM-Format enthält

> [!NOTE]
> Stellen Sie Ihren eigenen privaten Schlüssel direkt oder in einer Datei bereit. Der Schlüssel muss im [PEM-Format](https://aka.ms/PEMformat) sein und mit einem Zeilenumbruch (\n) enden.  Der zugeordnete öffentliche Schlüssel muss dem Benutzerkonto in Ihrem Git-Dienstanbieter hinzugefügt werden. Wenn der Schlüssel dem Repository statt dem Benutzerkonto hinzugefügt wird, verwenden Sie `git@` anstelle von `user@`. [Anzeigen weiterer Details](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Verwenden eines privaten Git-Hosts mit SSH und benutzerseitig bereitgestellten bekannten Hosts

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] oder user@server:repo[.git] | `git@` kann `user@` ersetzen |
| --ssh-known-hosts | base64-codiert | Direkt bereitgestellter Inhalt von bekannten Hosts |
| --ssh-known-hosts-file | Vollständiger Pfad zur lokalen Datei | In einer lokalen Datei bereitgestellter Inhalt von bekannten Hosts

> [!NOTE]
> Der Flux-Operator verwaltet eine Liste gängiger Git-Hosts in seiner Datei bekannter Hosts, um das Git-Repository vor dem Einrichten der SSH-Verbindung zu authentifizieren. Wenn Sie ein nicht gängiges Git-Repository oder Ihren eigenen Git-Host verwenden, müssen Sie möglicherweise den Hostschlüssel bereitstellen, um sicherzustellen, dass Flux Ihr Repository identifizieren kann. Sie können Ihren Inhalt von bekannten Hosts direkt oder in einer Datei bereitstellen. [Anzeigen der Formatspezifikation für Inhalt von bekannten Hosts](https://aka.ms/KnownHostsFormat).
> Sie können diese in Verbindung mit einem der oben beschriebenen SSH-Schlüsselszenarien verwenden.

#### <a name="use-a-private-git-repo-with-https"></a>Verwenden eines privaten Git-Repository mit HTTPS

| Parameter | Format | Notizen |
| ------------- | ------------- | ------------- |
| --repository-url | https://server/repo [.git] | HTTPS mit Standardauthentifizierung |
| --https-user | Unformatiert (raw) base64-codiert | HTTPS-Benutzername |
| --https-key | Unformatiert (raw) base64-codiert | Persönliches HTTPS-Zugriffstoken oder -Kennwort

> [!NOTE]
> Die private Authentifizierung von HTTPS Helm Release wird nur mit der Chartversion >= 1.2.0 des Helm-Operators unterstützt.  Version 1.2.0 wird standardmäßig verwendet.
> Die private Authentifizierung von HTTPS Helm Release wird für verwaltete Azure Kubernetes Services-Cluster derzeit nicht unterstützt.
> Wenn Sie Flux benötigen, um über Ihren Proxy auf das Git-Repository zuzugreifen, müssen Sie die Azure Arc-Agents mit den Proxyeinstellungen aktualisieren. [Weitere Informationen](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Zusätzliche Parameter

Hier finden Sie einige weitere Parameter, um die Konfiguration anzupassen:

`--enable-helm-operator` : *Optionaler* Switch zum Aktivieren der Unterstützung für Helm-Chartbereitstellungen.

`--helm-operator-params` : *Optionale* Chartwerte für den Helm-Operator (falls aktiviert).  Beispiel: „--set helm.versions=v3“

`--helm-operator-version` : *Optionale* Chartversion für den Helm-Operator (falls aktiviert). Verwenden Sie „1.2.0“ oder höher. Standardwert: ‚1.2.0‘.

`--operator-namespace` : *Optionaler* Name für den Operatornamespace. Standardwert: „default“. Maximal 23 Zeichen

`--operator-params` : *Optionale* Parameter für den Operator. Diese müssen in einfachen Anführungszeichen angegeben werden. Zum Beispiel, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ```

Unterstützte Optionen in „--operator-params“

| Option | BESCHREIBUNG |
| ------------- | ------------- |
| --git-branch  | Die Verzweigung des Git-Repositorys für Kubernetes-Manifeste. Der Standardwert ist „master“. Neuere Repositorys haben einen Stammzweig namens „main“, in diesem Fall müssen Sie --git-branch=main festlegen. |
| --git-path  | Dies ist der relative Pfad im Git-Repository für Flux zum Suchen von Kubernetes-Manifesten. |
| --git-readonly | Das Git-Repository wird als schreibgeschützt betrachtet. Flux versucht nicht, in die Datei zu schreiben. |
| --manifest-generation  | Wenn diese Option aktiviert ist, sucht Flux nach „.flux.yaml“ und führt Kustomize oder andere Manifest-Generatoren aus. |
| --git-poll-interval  | Dies ist die Zeitspanne, in der das Git-Repository für neue Commits abgerufen wird. Der Standardwert ist „5m“ (fünf Minuten). |
| --sync-garbage-collection  | Wenn diese Option aktiviert ist, werden die von Flux erstellten Ressourcen gelöscht und sind nicht mehr in Git verfügbar. |
| --git-label  | Dies ist die Bezeichnung zum Nachverfolgen des Synchronisierungsprozesses, der verwendet wird, um die Git-Verzweigung zu markieren.  Der Standardwert ist „flux-sync“. |
| --git-user  | Benutzername für Git-Commits. |
| --git-email  | Für Git-Commits zu verwendende E-Mail-Adresse. |

* Wenn „--git-user“ oder „--git-email“ nicht festgelegt ist, da Sie nicht möchten, dass Flux in das Repository schreibt, wird „--git-readonly“ (falls nicht bereits festgelegt) automatisch festgelegt.

Weitere Informationen finden Sie in der [Flux-Dokumentation](https://aka.ms/FluxcdReadme).

> [!TIP]
> Es ist möglich, im Azure-Portal auf der Registerkarte **GitOps** der Azure Arc-fähigen Kubernetes-Ressource eine sourceControlConfiguration zu erstellen.

## <a name="validate-the-sourcecontrolconfiguration"></a>Überprüfen der „sourceControlConfiguration“-Ressource

Überprüfen Sie mithilfe der Azure CLI, ob die `sourceControlConfiguration`-Ressource erfolgreich erstellt wurde.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Beachten Sie, dass die `sourceControlConfiguration`-Ressource mit einem Compliancestatus, Nachrichten und Debuginformationen aktualisiert wird.

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Wenn die Ressource `sourceControlConfiguration` erstellt oder aktualisiert wird, geschieht im Hintergrund Folgendes:

1. Der `config-agent` von Azure Arc überwacht Azure Resource Manager auf neue oder aktualisierte Konfigurationen (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) und erkennt die neue Konfiguration `Pending`.
1. Der `config-agent` liest die Konfigurationseigenschaften und erstellt den Zielnamespace.
1. Der `controller-manager` von Azure Arc bereitet ein Kubernetes-Dienstkonto mit der entsprechenden Berechtigung vor (Bereich `cluster` oder `namespace`) und stellt dann eine Instanz von `flux` bereit.
1. Bei Verwendung der Option von SSH mit von Flux generierten Schlüsseln generiert `flux` einen SSH-Schlüssel und protokolliert den öffentlichen Schlüssel.
1. Der `config-agent` meldet den Status zurück an die Ressource `sourceControlConfiguration` in Azure.

Während des Bereitstellungsprozesses wird der Status der `sourceControlConfiguration`-Ressource mehrfach geändert. Überwachen Sie den Fortschritt mit dem obigen `az k8sconfiguration show ...`-Befehl:

1. `complianceStatus` -> `Pending`: Hiermit werden der anfängliche Status und der Status während der Bearbeitung dargestellt.
1. `complianceStatus` -> `Installed`: Der `config-agent` konnte den Cluster erfolgreich konfigurieren und `flux` ohne Fehler bereitstellen.
1. `complianceStatus` -> `Failed`: Der `config-agent` hat einen Fehler beim Bereitstellen von `flux` gefunden, die Details sind im Antworttext `complianceStatus.message` verfügbar.

## <a name="apply-configuration-from-a-private-git-repository"></a>Anwenden der Konfiguration aus einem privaten Git-Repository

Wenn Sie ein privates Git-Repository verwenden, müssen Sie den öffentlichen SSH-Schlüssel in Ihrem Repository konfigurieren. Sie können den öffentlichen Schlüssel entweder für das spezifische Git-Repository oder den Git-Benutzer konfigurieren, der Zugriff auf das Repository hat. Der öffentliche SSH-Schlüssel ist entweder der von Ihnen bereitgestellte oder der von Flux generierte.

**Abrufen Ihres eigenen öffentlichen Schlüssels**

Wenn Sie Ihre eigenen SSH-Schlüssel generiert haben, verfügen Sie bereits über die privaten und öffentlichen Schlüssel.

**Abrufen des öffentlichen Schlüssels mithilfe der Azure CLI (nützlich, wenn Flux die Schlüssel generiert)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Abrufen des öffentlichen Schlüssels aus dem Azure-Portal (nützlich, wenn Flux die Schlüssel generiert)**

1. Navigieren Sie im Azure-Portal zur verbundenen Clusterressource.
2. Wählen Sie auf der Ressourcenseite „GitOps“ aus, um die Liste der Konfigurationen für diesen Cluster anzuzeigen.
3. Klicken Sie auf die Konfiguration, die das private Git-Repository verwendet.
4. Kopieren Sie den **öffentlichen Repositoryschlüssel** im geöffneten Kontextfenster unten auf der Seite.

Wenn Sie GitHub verwenden, verwenden Sie eine der folgenden beiden Optionen:

**Option 1: Hinzufügen des öffentlichen Schlüssels zu Ihrem Benutzerkonto (gilt für alle Repositorys in Ihrem Konto)**

1. Öffnen Sie GitHub, und klicken Sie in der oberen rechten Ecke der Seite auf Ihr Profilsymbol.
2. Klicken Sie auf **Einstellungen**.
3. Klicken Sie auf **SSH- und GPG-Schlüssel**.
4. Klicken Sie auf **Neuer SSH-Schlüssel**.
5. Geben Sie einen Titel an.
6. Fügen Sie den öffentlichen Schlüssel ohne die diesen umgebenden Anführungszeichen ein.
7. Klicken Sie auf **SSH-Schlüssel hinzufügen**.

**Option 2: Hinzufügen des öffentlichen Schlüssels als Bereitstellungsschlüssel zum Git-Repository (gilt nur für dieses Repository)**

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

Löschen Sie eine `sourceControlConfiguration`-Ressource mithilfe der Azure CLI oder über das Azure-Portal.  Nachdem Sie den Löschbefehl initiiert haben, wird die `sourceControlConfiguration`-Ressource sofort in Azure gelöscht, und die vollständige Löschung der zugeordneten Objekte aus dem Cluster sollte innerhalb von 10 Minuten erfolgen.  Wenn sich die `sourceControlConfiguration` beim Löschen in einem fehlerhaften Zustand befindet, kann das vollständige Löschen der zugeordneten Objekte bis zu einer Stunde dauern.

> [!NOTE]
> Nachdem eine sourceControlConfiguration mit Namespacebereich erstellt wurde, können Benutzer mit der `edit`-Rollenbindung an den Namespace Workloads für diesen Namespace bereitstellen. Wenn diese `sourceControlConfiguration`-Ressource mit Namespacebereich gelöscht wird, bleibt der Namespace intakt und wird nicht gelöscht, um zu vermeiden, dass diese anderen Workloads unterbrochen werden.  Bei Bedarf können Sie diesen Namespace manuell mit kubectl löschen.
> Alle Änderungen am Cluster, die das Ergebnis von Bereitstellungen aus dem nachverfolgten Git-Repository sind, werden beim Löschen der `sourceControlConfiguration`-Ressource nicht gelöscht.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Ausgabe:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von GitOps mit Helm für Azure Arc-fähige Clusterkonfiguration (Vorschauversion)](./use-gitops-with-helm.md)
- [Verwenden von Azure Policy zum Anwenden von Clusterkonfigurationen im großen Stil (Vorschau)](./use-azure-policy.md)