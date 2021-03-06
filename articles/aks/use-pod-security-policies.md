---
title: Verwenden von Podsicherheitsrichtlinien in Azure Kubernetes Service (AKS)
description: Hier erfahren Sie, wie Sie den Podzugang mittels Podsicherheitsrichtlinie in Azure Kubernetes Service (AKS) steuern.
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: 260b7a0811db4e3c33894d1b43d7fbd38c26214f
ms.sourcegitcommit: 2f322df43fb3854d07a69bcdf56c6b1f7e6f3333
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/27/2021
ms.locfileid: "108015330"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Vorschauversion: Schützen Ihres Clusters mithilfe von Podsicherheitsrichtlinien in Azure Kubernetes Service (AKS)

> [!WARNING]
> **Die [Einstellung](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) des in diesem Dokument beschriebenen Features „Podsicherheitsrichtlinie (Vorschau)“ beginnt mit Kubernetes-Version 1.21, und es wird in Version 1.25 entfernt.** Da sich Kubernetes Upstream diesen Meilenstein nähert, arbeitet die Kubernetes-Community an der Dokumentation funktionsfähiger Alternativen. Die vorherige Einstellungsankündigung wurde zu dem Zeitpunkt vorgenommen, als es noch keine funktionsfähige Option für Kunden gab. Nun, da die Kubernetes-Community an einer Alternative arbeitet, ist es nicht mehr zwingend erforderlich, vor Kubernetes einzustellen. 
>
> Wenn die Podsicherheitsrichtlinie (Vorschauversion) veraltet ist, müssen Sie das Feature für alle vorhandenen Cluster deaktivieren, die das veraltete Feature verwenden, um zukünftige Clusterupgrades ausführen und weiterhin Azure-Support erhalten zu können.

Zur Verbesserung der Sicherheit Ihres AKS-Clusters können Sie einschränken, welche Pods geplant werden können. Pods, die von Ihnen nicht zugelassene Ressourcen anfordern, können im AKS-Cluster nicht ausgeführt werden. Dieser Zugriff wird mithilfe von Podsicherheitsrichtlinien definiert. In diesem Artikel erfahren Sie, wie Sie Podsicherheitsrichtlinien verwenden, um die Bereitstellung von Pods in AKS einzuschränken.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Um Podsicherheitsrichtlinien verwenden zu können, benötigen Sie die *aks-preview* CLI-Erweiterung Version 0.4.1 oder höher. Installieren Sie die Azure CLI-Erweiterung *aks-preview* mit dem Befehl [az extension add][az-extension-add], und suchen Sie dann mit dem Befehl [az extension update][az-extension-update] nach verfügbaren Updates:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Registrieren des Anbieters des Podsicherheitsrichtlinien-Features

**Dieses Dokument und dieses Feature sollen am 15. Oktober 2020 eingestellt werden.**

Um einen AKS-Cluster für die Verwendung von Podsicherheitsrichtlinien zu erstellen oder zu aktualisieren, müssen Sie zunächst ein Featureflag für Ihr Abonnement aktivieren. Verwenden Sie den Befehl [az feature register][az-feature-register] wie im folgenden Beispiel gezeigt, um das Featureflag *PodSecurityPolicyPreview* zu registrieren:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie mithilfe des Befehls [az provider register][az-provider-register] die Registrierung des Ressourcenanbieters *Microsoft.ContainerService*:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Übersicht über Podsicherheitsrichtlinien

In einem Kubernetes-Cluster wird eine Zugangssteuerung verwendet, um an den API-Server gerichtete Anforderungen abzufangen, wenn eine Ressource erstellt werden soll. Die Zugangssteuerung kann dann die Ressourcenanforderung anhand einer Reihe von Regeln *überprüfen* oder die Ressource *mutieren*, um die Bereitstellungsparameter zu ändern.

*PodSecurityPolicy* ist eine Zugangssteuerung, die überprüft, ob eine Podspezifikation die von Ihnen definierten Anforderungen erfüllt. Diese Anforderungen schränken möglicherweise die Verwendung privilegierter Container, den Zugriff auf bestimmte Arten von Speicher oder die Benutzer/Gruppen ein, als die der Container ausgeführt werden kann. Wenn Sie versuchen, eine Ressource bereitzustellen, bei der die Podspezifikationen nicht die in der Podsicherheitsrichtlinie beschriebenen Anforderungen erfüllen, wird die Anforderung abgelehnt. Die Möglichkeit zu steuern, welche Pods im AKS-Cluster geplant werden können, beugt einigen möglichen Sicherheitsrisiken und Rechteausweitungen vor.

Wenn Sie Podsicherheitsrichtlinien in einem AKS-Cluster aktivieren, werden einige Standardrichtlinien angewendet. Diese Standardrichtlinien sind sofort einsatzbereit und definieren, welche Pods geplant werden können. Clusterbenutzer haben jedoch möglicherweise Probleme bei der Podbereitstellung, bis Sie Ihre eigenen Richtlinien definieren. Empfohlene Vorgehensweise:

* Erstellen eines AKS-Clusters
* Definieren Ihrer eigenen Podsicherheitsrichtlinien
* Aktivieren des Podsicherheitsrichtlinien-Features

Um zu zeigen, wie Podbereitstellungen durch die Standardrichtlinien eingeschränkt werden, aktivieren wir in diesem Artikel zunächst das Podsicherheitsrichtlinien-Feature und erstellen dann eine benutzerdefinierte Richtlinie.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Verhaltensänderungen zwischen der Podsicherheitsrichtlinie und Azure Policy

Nachfolgend finden Sie eine Zusammenfassung der Verhaltensänderungen zwischen der Podsicherheitsrichtlinie und Azure Policy.

|Szenario| Podsicherheitsrichtlinie | Azure Policy |
|---|---|---|
|Installation|Aktivieren des Podsicherheitsrichtlinien-Features |Aktivieren des Azure Policy-Add-Ons
|Bereitstellen von Richtlinien| Bereitstellen der Podsicherheitsrichtlinien-Ressource| Weisen Sie Azure-Richtlinien dem Abonnement- oder Ressourcengruppenbereich zu. Das Azure Policy-Add-On ist für Kubernetes-Ressourcenanwendungen erforderlich.
| Standardrichtlinien | Wenn die Podsicherheitsrichtlinie in AKS aktiviert ist, werden standardmäßig die Richtlinien „Privilegiert“ (Privileged) und „Uneingeschränkt“ (Unrestricted) angewendet. | Durch die Aktivierung des Azure Policy-Add-Ons werden keine Standardrichtlinien angewendet. Sie müssen Richtlinien in Azure Policy explizit aktivieren.
| Wer kann Richtlinien erstellen und zuweisen? | Der Clusteradministrator erstellt eine Ressource für Podsicherheitsrichtlinien. | Benutzer müssen in der AKS-Clusterressourcengruppe mindestens die Berechtigungen „Besitzer“ oder „Ressourcenrichtlinienmitwirkender“ aufweisen. – Über die API können Benutzer Richtlinien im Bereich der AKS-Clusterressourcen zuweisen. Der Benutzer sollte mindestens die Berechtigungen „Besitzer“ oder „Ressourcenrichtlinienmitwirkender" für die AKS-Clusterressource aufweisen. – Im Azure-Portal können Richtlinien auf Verwaltungsgruppen-, Abonnement- oder Ressourcengruppenebene angewendet werden.
| Autorisierungsrichtlinien| Benutzer und Dienstkonten benötigen explizite Berechtigungen zur Verwendung von Podsicherheitsrichtlinien. | Für die Autorisierung von Richtlinien ist keine zusätzliche Zuweisung erforderlich. Nachdem Richtlinien in Azure zugewiesen wurden, können alle Clusterbenutzer diese Richtlinien verwenden.
| Anwendbarkeit von Richtlinien | Der Administratorbenutzer umgeht die Erzwingung von Podsicherheitsrichtlinien. | Alle Benutzer (Administrator und Nicht-Administrator) sehen dieselben Richtlinien. Es gibt keine spezielle Schreibweise auf der Basis von Benutzern. Die Anwendung von Richtlinien kann auf der Namespaceebene ausgeschlossen werden.
| Geltungsbereich der Richtlinie | Podsicherheitsrichtlinien sind keinem Namespace zugeordnet | Die von Azure Policy verwendeten Einschränkungsvorlagen sind keinem Namespace zugeordnet.
| Deny/Audit/Mutation (Aktion) | Podsicherheitsrichtlinien unterstützen nur Aktionen vom Typ „deny“. Die Mutation kann mit Standardwerten bei Erstellungsanforderungen erfolgen. Die Validierung kann während der Aktualisierungsanforderungen durchgeführt werden.| Azure Policy unterstützt sowohl Aktionen vom Typ „audit“ als auch vom Typ „deny“. Die Mutation wird noch nicht unterstützt, ist aber geplant.
| Compliance von Sicherheitsrichtlinien | Es gibt keine Transparenz hinsichtlich der Compliance von Pods, die vor der Aktivierung der Podsicherheitsrichtlinie existierten. Nicht konforme Pods, die nach der Aktivierung von Podsicherheitsrichtlinien erstellt wurden, werden verweigert. | Nicht konforme Pods, die vor der Anwendung von Azure-Richtlinien existierten, würden in Richtlinienverletzungen angezeigt werden. Nicht konforme Pods, die nach der Aktivierung von Azure-Richtlinien erstellt wurden, werden verweigert, wenn Richtlinien mit einer Auswirkung vom Typ „deny“ festgelegt werden.
| Anzeigen von Richtlinien für den Cluster | `kubectl get psp` | `kubectl get constrainttemplate` – Alle Richtlinien werden zurückgegeben.
| Podsicherheitsrichtlinie (Standard): Privilegiert | Beim Aktivieren des Features wird standardmäßig eine Sicherheitsrichtlinienressource für privilegierte Pods erstellt. | Der privilegierte Modus impliziert keine Einschränkung, daher ist er gleichbedeutend mit keiner Zuweisung von Azure-Richtlinien.
| [Podsicherheitsrichtlinie (Standard): Baseline/Standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Der Benutzer installiert eine Baseline-Ressource der Podsicherheitsrichtlinie. | Azure Policy bietet eine [integrierte Baseline-Initiative](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d), die sich an der Baseline-Richtlinie für Pods orientiert.
| [Podsicherheitsrichtlinie (Standard): Eingeschränkt](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Der Benutzer installiert eine eingeschränkte Ressource der Podsicherheitsrichtlinie. | Azure Policy bietet eine [integrierte eingeschränkte Initiative](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00), die der eingeschränkten Podsicherheitsrichtlinie zugeordnet ist.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Aktivieren von Podsicherheitsrichtlinien für einen AKS-Cluster

Podsicherheitsrichtlinien können mithilfe des Befehls [az aks update][az-aks-update] aktiviert oder deaktiviert werden. Im folgenden Beispiel werden Podsicherheitsrichtlinien für den Clusternamen *myAKSCluster* in der Ressourcengruppe *myResourceGroup* aktiviert.

> [!NOTE]
> In der Praxis sollten Podsicherheitsrichtlinien erst aktiviert werden, nachdem Sie Ihre eigenen benutzerdefinierten Richtlinien definiert haben. In diesem Artikel werden Podsicherheitsrichtlinien im ersten Schritt aktiviert, um zu zeigen, wie Podbereitstellungen durch die Standardrichtlinien eingeschränkt werden.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>AKS-Standardrichtlinien

Wenn Sie Podsicherheitsrichtlinien aktivieren, erstellt AKS eine Standardrichtlinie mit dem Namen *privileged* (Privilegiert). Die Standardrichtlinie darf nicht bearbeitet oder entfernt werden. Erstellen Sie stattdessen Ihre eigenen Richtlinien, um die Einstellungen zu definieren, die Sie steuern möchten. Befassen wir uns zunächst mit den Standardrichtlinien und ihren Auswirkungen auf Podbereitstellungen.

Verwenden Sie zum Anzeigen der verfügbaren Richtlinien den Befehl [kubectl get psp][kubectl-get], wie im folgenden Beispiel gezeigt.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Die Podsicherheitsrichtlinie *privileged* gilt für alle authentifizierten Benutzer im AKS-Cluster. Diese Zuweisung wird durch Clusterrollen und Clusterrollenbindungen gesteuert. Verwenden Sie den Befehl [kubectl get clusterrolebindings][kubectl-get], und suchen Sie im Namespace *kube-system* nach der Bindung *default:privileged:* :

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Wie in der folgenden gekürzten Ausgabe zu sehen, ist die Clusterrolle *psp:privileged* allen Benutzern vom Typ *system:authenticated* zugewiesen. Diese Funktionalität bietet eine einfache Berechtigungsebene, ohne dass Sie eigene Richtlinien definiert haben.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Es ist wichtig zu verstehen, wie diese Standardrichtlinien mit den Benutzeranforderungen für die Podplanung interagieren, bevor Sie mit der Erstellung Ihrer eigenen Podsicherheitsrichtlinien beginnen. In den nächsten Abschnitten werden einige Pods geplant, um die Auswirkungen der Standardrichtlinien zu veranschaulichen.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Erstellen eines Testbenutzers in einem AKS-Cluster

Bei Verwendung des Befehls [az aks get-credentials][az-aks-get-credentials] werden Ihrer `kubectl`-Konfiguration standardmäßig die Administratoranmeldeinformationen (*admin*) für den AKS-Cluster hinzugefügt. Der Administratorbenutzer umgeht die Erzwingung von Podsicherheitsrichtlinien. Wenn Sie für Ihre AKS-Cluster die Azure Active Directory-Integration verwenden, können Sie sich mit den Anmeldeinformationen eines Benutzers ohne Administratorrechte anmelden, um die Erzwingung von Richtlinien in Aktion zu sehen. In diesem Artikel erstellen wir ein Testbenutzerkonto im AKS-Cluster, das Sie verwenden können.

Erstellen Sie mithilfe des Befehls [kubectl create namespace][kubectl-create] einen Beispielnamespace namens *psp-aks* für Testressourcen. Erstellen Sie anschließend mithilfe des Befehls [kubectl create serviceaccount][kubectl-create] ein Dienstkonto namens *nonadmin-user*:

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Erstellen Sie als Nächstes mithilfe des Befehls [kubectl create rolebinding][kubectl-create] eine Rollenbindung für *nonadmin-user*, um grundlegende Aktionen im Namespace ausführen zu können:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Erstellen von Aliasbefehlen für Administratorbenutzer und Benutzer ohne Administratorrechte

Zur Verdeutlichung des Unterschieds bei der Verwendung von `kubectl` zwischen dem regulären Administratorbenutzer und dem Benutzer ohne Administratorrechte, der in den vorherigen Schritten erstellt wurde, erstellen wir zwei Befehlszeilenaliase:

* Der Alias **kubectl-admin** ist für den regulären Administratorbenutzer vorgesehen und auf den Namespace *psp-aks* ausgerichtet.
* Der Alias **kubectl-nonadminuser** ist für den im vorherigen Schritt erstellten Benutzer ohne Administratorrechte (*nonadmin-user*) vorgesehen und auf den Namespace *psp-aks* ausgerichtet.

Erstellen Sie diese beiden Aliase wie in den folgenden Befehlen gezeigt:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testen der Erstellung eines privilegierten Pods

Testen Sie als Erstes, was passiert, wenn Sie einen Pod mit dem Sicherheitskontext `privileged: true` planen. Dieser Sicherheitskontext weitet die Podberechtigungen aus. Im vorherigen Abschnitt mit den standardmäßigen AKS-Podsicherheitsrichtlinien sollte diese Anforderung durch die Richtlinie *privilege* abgelehnt werden.

Erstellen Sie eine Datei mit dem Namen `nginx-privileged.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Erstellen Sie den Pod mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Der Pod kann nicht geplant werden, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Da der Pod die Planungsphase nicht erreicht, müssen keine Ressourcen gelöscht werden, und Sie können direkt mit dem nächsten Schritt fortfahren.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testen der Erstellung eines nicht privilegierten Pods

Im vorherigen Beispiel wurde durch die Podspezifikation eine Rechteausweitung angefordert. Diese Anforderung wird durch die standardmäßige Podsicherheitsrichtlinie *privilege* abgelehnt, sodass der Pod nicht geplant werden kann. Versuchen Sie als Nächstes, den gleichen NGINX-Pod ohne Anforderung der Rechteausweitung auszuführen.

Erstellen Sie eine Datei mit dem Namen `nginx-unprivileged.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Erstellen Sie den Pod mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Der Pod kann nicht geplant werden, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Da der Pod die Planungsphase nicht erreicht, müssen keine Ressourcen gelöscht werden, und Sie können direkt mit dem nächsten Schritt fortfahren.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testen der Erstellung eines Pods mit einem spezifischen Benutzerkontext

Im vorherigen Beispiel hat das Containerimage automatisch versucht, Stammberechtigungen zu verwenden, um NGINX an den Port 80 zu binden. Diese Anforderung wurde durch die standardmäßige Podsicherheitsrichtlinie *privilege* abgelehnt, sodass der Pod nicht gestartet werden kann. Als Nächstes versuchen wir, den gleichen NGINX-Pod mit einem spezifischen Benutzerkontext (etwa `runAsUser: 2000`) auszuführen.

Erstellen Sie eine Datei mit dem Namen `nginx-unprivileged-nonroot.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Erstellen Sie den Pod mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Der Pod kann nicht geplant werden, wie in der folgenden Beispielausgabe zu sehen:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Da der Pod die Planungsphase nicht erreicht, müssen keine Ressourcen gelöscht werden, und Sie können direkt mit dem nächsten Schritt fortfahren.

## <a name="create-a-custom-pod-security-policy"></a>Erstellen einer benutzerdefinierten Podsicherheitsrichtlinie

Nachdem Sie nun mit dem Verhalten der Standard-Podsicherheitsrichtlinien vertraut sind, erfahren Sie als Nächstes, wie Sie dem Benutzer ohne Administratorrechte (*nonadmin-user*) die erfolgreiche Planung von Pods ermöglichen.

Hierzu erstellen wir eine Richtlinie zur Ablehnung von Pods, die privilegierten Zugriff anfordern. Andere Optionen wie *RunAsUser* oder zulässige *Volumes* werden explizit nicht eingeschränkt. Diese Art von Richtlinie lehnt die Anforderung von privilegiertem Zugriff ab, lässt aber ansonsten die Ausführung der angeforderten Pods durch den Cluster zu.

Erstellen Sie eine Datei mit dem Namen `psp-deny-privileged.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Erstellen Sie die Richtlinie mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Verwenden Sie zum Anzeigen der verfügbaren Richtlinien den Befehl [kubectl get psp][kubectl-get], wie im folgenden Beispiel gezeigt. Vergleichen Sie die Richtlinie *psp-deny-privileged* mit der Standardrichtlinie *privilege*, die in den vorherigen Beispielen für die Poderstellung erzwungen wurde. Durch Ihre Richtlinie wird lediglich die Verwendung von *PRIV* für die Rechteausweitung abgelehnt. Bei der Richtlinie *psp-deny-privileged* gibt es keinerlei Benutzer- oder Gruppeneinschränkungen.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Zulassen der Verwendung der benutzerdefinierten Podsicherheitsrichtlinie durch das Benutzerkonto

Im vorherigen Schritt haben Sie eine Podsicherheitsrichtlinie erstellt, um Pods abzulehnen, die privilegierten Zugriff anfordern. Damit die Richtlinie verwendet werden kann, müssen Sie eine Rolle (*Role*) oder Clusterrolle (*ClusterRole*) erstellen. Anschließend müssen Sie eine dieser Rollen mithilfe einer Rollenbindung (*RoleBinding*) oder Clusterrollenbindung (*ClusterRoleBinding*) zuordnen.

In diesem Beispiel wird eine Clusterrolle erstellt, die es Ihnen ermöglicht, die im vorherigen Schritt erstellte Richtlinie *psp-deny-privileged* zu verwenden (*use*). Erstellen Sie eine Datei mit dem Namen `psp-deny-privileged-clusterrole.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Erstellen Sie die Clusterrolle mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Erstellen Sie nun eine Clusterrollenbindung für die Verwendung der im vorherigen Schritt erstellten Clusterrolle. Erstellen Sie eine Datei mit dem Namen `psp-deny-privileged-clusterrolebinding.yaml`, und fügen Sie das folgende YAML-Manifest ein:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Erstellen Sie eine Clusterrollenbindung mithilfe des Befehls [kubectl apply][kubectl-apply], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Im ersten Schritt dieses Artikels wurde das Podsicherheitsrichtlinien-Feature für den AKS-Cluster aktiviert. Laut empfohlener Vorgehensweise sollte das Podsicherheitsrichtlinien-Feature allerdings erst aktiviert werden, nachdem Sie Ihre eigenen Richtlinien definiert haben. Das ist nun die Phase, in der Sie das Podsicherheitsrichtlinien-Feature normalerweise aktivieren würden. Es wurde mindestens eine benutzerdefinierte Richtlinie definiert, und den Richtlinien wurden Benutzerkonten zugeordnet. Nun können Sie problemlos das Podsicherheitsrichtlinien-Feature aktivieren und Probleme minimieren, die durch die Standardrichtlinien verursacht werden.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Erneutes Testen der Erstellung eines nicht privilegierten Pods

Versuchen Sie erneut, einen nicht privilegierten Pod zu erstellen, nachdem Sie Ihre benutzerdefinierte Podsicherheitsrichtlinie angewendet haben und über eine Bindung verfügen, damit das Benutzerkonto die Richtlinie verwenden kann. Verwenden Sie zum Erstellen des Pods das gleiche Manifest (`nginx-privileged.yaml`) und den Befehl [kubectl apply][kubectl-apply]:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Der Pod wird erfolgreich geplant. Wenn Sie mithilfe des Befehls [kubectl get pods][kubectl-get] den Status des Pods überprüfen, sehen Sie, dass der Pod ausgeführt wird (*Running*):

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Dieses Beispiel zeigt, wie Sie benutzerdefinierte Podsicherheitsrichtlinien erstellen, um den Zugriff auf den AKS-Cluster für verschiedene Benutzer oder Gruppen zu definieren. Mit den AKS-Standardrichtlinien wird sehr streng kontrolliert, welche Pods ausgeführt werden können. Erstellen Sie daher Ihre eigenen benutzerdefinierten Richtlinien, um die korrekten Einschränkungen für Ihre Anforderungen zu definieren.

Löschen Sie den nicht privilegierten NGINX-Pod mithilfe des Befehls [kubectl delete][kubectl-delete], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Podsicherheitsrichtlinien können durch erneutes Ausführen des Befehls [az aks update][az-aks-update] deaktiviert werden. Im folgenden Beispiel werden Podsicherheitsrichtlinien für den Clusternamen *myAKSCluster* in der Ressourcengruppe *myResourceGroup* deaktiviert:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Löschen Sie als Nächstes die Clusterrolle und die Clusterrollenbindung:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Löschen Sie die Sicherheitsrichtlinie mithilfe des Befehls [kubectl delete][kubectl-delete], und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Löschen Sie abschließend den Namespace *psp-aks*:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Podsicherheitsrichtlinie erstellen, um die Verwendung von privilegiertem Zugriff zu verhindern. Mit einer Richtlinie lassen sich noch viele andere Features erzwingen – etwa die Art des Volumes oder der RunAs-Benutzer. Weitere Informationen zu den verfügbaren Optionen finden Sie in der [Referenzdokumentation zu Kubernetes-Podsicherheitsrichtlinien][kubernetes-policy-reference].

Weitere Informationen zum Einschränken des Netzwerkdatenverkehrs von Pods finden Sie unter [Vorschauversion: Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
