---
title: 'Best Practices für Operator: Clustersicherheit in Azure Kubernetes Services (AKS)'
description: Lernen Sie die Best Practices für den Clusteroperator zum Verwalten der Clustersicherheit und von Upgrades in Azure Kubernetes Service (AKS) kennen.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 54f1455467295e786d9e634b64dfab0933d948db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475590"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Best Practices für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)

Beim Verwalten von Clustern in Azure Kubernetes Service (AKS) spielt die Sicherheit Ihrer Workloads und Daten eine Schlüsselrolle. Besonders wenn Sie Cluster mit mehreren Mandanten mithilfe von logischer Isolierung ausführen, müssen Sie den Zugriff auf Ressourcen und Workloads sichern. Sorgen Sie außerdem dafür, dass Sie die neuesten Sicherheitsupdates für Kubernetes und dem Betriebssystem für Knoten durchgeführt haben, um das Risiko einer Attacke zu minimieren.

In diesem Artikel wird erläutert, wie AKS-Cluster gesichert werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Verwenden von Azure Active Directory und rollenbasierter Zugriffssteuerung, um den Zugriff auf API-Server zu sichern
> * Sicherer Containerzugriff auf Knotenressourcen
> * Durchführen eines Upgrades auf AKS-Cluster auf die neueste Kubernetes-Version
> * Aktualisieren von Knoten-Updates und automatisches Anwenden von Sicherheitspatches

Weitere Informationen zu Best Practices bei Containerimages und Podsicherheit erhalten Sie in den Artikeln [Best practices for container image management and security in Azure Kubernetes Service (AKS) ( Best Practices beim Verwalten von Containerimages und bei der Sicherheit in Azure Kubernetes Service (AKS))][best-practices-container-image-management] und [Best practices for pod security in Azure Kubernetes Service (AKS) (Best Practices für Podsicherheit in Azure Kubernetes Service (AKS))][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Sicherer Zugriff auf API-Server und Clusterknoten

**Best Practice-Anleitung:** Eines der wichtigsten Dinge, die Sie tun können, um den Zugriff auf den Kubernetes API-Server zu sichern, ist die Sicherung Ihres Clusters. Integrieren Sie die rollenbasierte Zugriffssteuerung von Kubernetes (RBAC) in Azure Active Directory, um den Zugriff auf den API-Server steuern zu können. Mit dieser Steuerung können Sie AKS in der gleichen Weise sichern, wie Sie auch den Zugriff auf Ihre Azure-Abonnements sichern können.

Der Kubernetes API-Server stellt einen einzelnen Verbindungspunkt zur Verfügung, der für Aktionsanforderungen innerhalb eines Clusters zuständig ist. Indem Sie den Zugriff beschränken und nur die minimal erforderlichen Zugriffsberechtigungen auswählen, können Sie den Zugriff auf den API-Server sichern und überwachen. Dieses Vorgehensweise richtet sich nicht speziell an Kubernetes, sondern hat eine besondere Bedeutung, wenn der AKS-Cluster bei einer Verwendung mit mehreren Mandanten logisch isoliert ist.

Azure Active Directory (AD) bietet eine Lösung zur Identitätsverwaltung für Unternehmen, die in AKS-Cluster integriert werden kann. Da Kubernetes keine Identitätsverwaltungslösung bietet, ist es schwierig, auf andere Weise eine genau abgestimmte Möglichkeit zur Zugriffsbeschränkung auf API-Server zur Verfügung zu stellen. Wenn Sie in AKS Cluster verwenden, die in Azure AD integriert sind, authentifizieren Sie Benutzer im API-Server anhand von bestehenden Benutzer- und Gruppenkonten.

![Azure Active Directory-Integration mit AKS-Clustern](media/operator-best-practices-cluster-security/aad-integration.png)

Verwenden Sie Kubernetes RBAC und die Integration in Azure AD, um den API-Server zu sichern und die minimal erforderliche Anzahl an Berechtigungen für eine bereichsbezogene Menge an Ressourcen zur Verfügung zu stellen, beispielsweise für einen einzelnen Namespace. In Azure AD können verschiedenen Benutzern und Gruppen verschiedene RBAC-Rollen zugewiesen werden. Diese granularen Berechtigungen ermöglichen es Ihnen, den Zugriff auf den API-Server zu beschränken und stellen ein eindeutiges Überwachungsprotokoll durchgeführter Aktionen zur Verfügung.

Es empfiehlt sich, ganze Gruppen und nicht nur einzelne Identitäten zu verwenden, um Zugriff auf Dateien und Ordner zu gewähren und Azure AD-*Gruppenmitgliedschaften* anstatt einzelne *Benutzer* zu verwenden, um Benutzern RBAC-Rollen zuzuweisen. So ändern sich die Zugriffsberechtigungen eines Benutzers im AKS-Cluster automatisch, wenn die Gruppenmitgliedschaft eines Benutzers geändert wird. Wenn Sie einem Benutzer direkt eine Rolle zuweisen, ändert sich möglicherweise dessen Stellenfunktion. Wenn die Gruppenmitgliedschaften eines Benutzers in Azure AD dann aktualisiert werden, wird dies nicht für dessen Berechtigungen im AKS-Cluster übernommen. In diesem Szenario würde ein Benutzer letzten Endes dann über mehr Berechtigungen verfügen, als für einen Benutzer erforderlich ist.

Weitere Informationen zur Integration in Azure AD und RBAC finden Sie unter [Zugriffs- und Identitätsoptionen für Azure Kubernetes Service (AKS)][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Sicherer Containerzugriff auf Ressourcen

**Best Practice-Anleitung:** Begrenzen Sie den Zugriff auf Aktionen, die Container ausführen können. Geben Sie die niedrigste Anzahl an Berechtigungen an, und vermeiden Sie die Verwendung von Stamm- oder Rechteausweitung.

Genauso wie Sie Benutzern und Gruppen nur die minimal notwendigen Berechtigungen gewähren sollten, sollten Sie auch Container auf die Aktionen und Prozesse beschränken, die sie benötigen. Konfigurieren Sie keine Anwendungen und Container, die ausgeweitete Berechtigungen oder Stammzugriff benötigen, um das Risiko einer Attacke so gering wie möglich zu halten. Legen Sie im Podmanifest beispielsweise `allowPrivilegeEscalation: false` fest. Diese *Podsicherheitskontexte* sind in Kubernetes integriert und ermöglichen es Ihnen, zusätzliche Berechtigungen festzulegen, z. B. welche Ausführungsberechtigungen Benutzer und Gruppen haben oder welche Linux-Funktionen zur Verfügung gestellt werden. Weitere Best Practices finden Sie unter [Secure pod access to ressources (Sichern des Podzugriffs auf Ressourcen)][pod-security-contexts].

Wenn Sie die Steuerungsmöglichkeiten für Containeraktionen noch feiner abstimmen möchten, können Sie dazu auch integrierte Linux-Sicherheitsfeatures wie *AppArmor* und *seccomp* verwenden. Diese Features sind auf der Knotenebene definiert und werden dann über ein Podmanifest implementiert. In Linux integrierte Sicherheitsfunktionen sind nur auf Linux-Knoten und -Pods verfügbar.

> [!NOTE]
> Kubernetes-Umgebungen, ob in AKS oder an anderer Stelle, sind nicht völlig sicher vor feindlicher Verwendung mit mehreren Mandanten. Zusätzliche Sicherheitsfunktionen wie *AppArmor*, *seccomp*, *Pod Security Policies* oder differenziertere rollenbasierte Zugriffssteuerung (RBAC) für Knoten erschweren Angriffe. Für echte Sicherheit bei der Ausführung feindlicher Workloads mit mehreren Mandanten ist jedoch ein Hypervisor die einzige Sicherheitsstufe, der Sie vertrauen sollten. Die Sicherheitsdomäne für Kubernetes wird zum gesamten Cluster und nicht zu einem einzelnen Knoten. Für diese Art von feindlichen Workloads mit mehreren Mandanten sollten Sie physisch isolierte Cluster verwenden.

### <a name="app-armor"></a>AppArmor

Verwenden Sie das Kernelsicherheitsmodul [AppArmor][k8s-apparmor] von Linux, um Aktionen, die von Containern ausgeführt werden können, zu beschränken. AppArmor ist als Teil des zugrunde liegenden AKS Knotens des Betriebssystems verfügbar und standardmäßig aktiviert. Sie erstellen AppArmor-Profile, die Aktionen beschränken wie „Lesen“, „Schreiben“ oder „Ausführen“, oder Systemfunktionen wie das Einlegen von Dateisystemen. Standardprofile von AppArmor beschränken den Zugriff auf verschiedene `/proc`- und `/sys`-Speicherorte und ermöglichen es, Container logisch vom zugrunde liegenden Knoten zu isolieren. AppArmor funktioniert nicht nur mit Kubernetes-Pods, sondern zusammen mit jeder Anwendung, die auf Linux ausgeführt werden kann.

![Verwendete AppArmor-Profile in einem AKS-Cluster, um Containeraktionen zu beschränken](media/operator-best-practices-container-security/apparmor.png)

In der folgenden Beispielverwendung von AppArmor wird ein Profil erstellt, das den Schreibzugriff auf Dateien verhindert. Stellen Sie eine [SSH][aks-ssh]-Verbindung zu einem AKS-Knoten her, und erstellen Sie dann eine Datei mit dem Namen *deny-write.profile*, und fügen Sie folgenden Inhalt ein:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor-Profile werden mithilfe des `apparmor_parser`-Befehls hinzugefügt. Fügen Sie das Profil zu AppArmor hinzu, und geben Sie den Namen des Profils an, das Sie im vorherigen Schritt erstellt haben:

```console
sudo apparmor_parser deny-write.profile
```

Wenn das Profil richtig analysiert und in AppArmor angewendet wurde, erhalten Sie keine Ausgabe. Sie werden zur Eingabeaufforderung zurückgeleitet.

Erstellen Sie auf Ihrem lokalen Computer nun ein Podmanifest names *aks-apparmor.yaml*, und fügen Sie den folgenden Inhalt ein. Dieses Manifest bestimmt eine Anmerkung für `container.apparmor.security.beta.kubernetes` und verweist auf das *deny-write*-Profil, das im vorherigen Schritt erstellt wurde:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Stellen Sie den Beispielpod mithilfe des Befehls [kubectl apply][kubectl-apply] bereit:

```console
kubectl apply -f aks-apparmor.yaml
```

Wurde der Pod bereitgestellt, verwenden Sie den Befehl [kubectl exec][kubectl-exec], um einen Schreibvorgang in einer Datei durchzuführen. Wie in folgender Beispielausgabe ersichtlich ist, kann der Befehl nicht ausgeführt werden:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Weitere Informationen erhalten Sie im Kubernetes-Artikel [AppArmor][k8s-apparmor].

### <a name="secure-computing"></a>Sicheres Computing

Während AppArmor für Linux-Anwendungen funktioniert, arbeitet [seccomp (*sec*ure *comp*uting)][seccomp] auf Prozessebene. Seccomp ist ebenfalls ein Kernelsicherheitsmodul von Linux und wird nativ von der Docker-Runtime unterstützt, die für AKS-Knoten verwendet wird. Seccomp sorgt dafür, dass die Prozessaufrufe, die ein Container durchführen kann, begrenzt sind. Erstellen Sie Filter, die festlegen, welche Aktionen erlaubt oder verweigert werden, und verwenden Sie dann zur Zuordnung zum jeweiligen Seccomp-Filter Anmerkungen innerhalb eines Pod-YAML-Manifests. Dies entspricht der bewährten Methode, Containern wirklich nur die minimal zur Ausführung benötigten Berechtigungen zu erteilen.

In der folgenden Beispielverwendung von Seccomp erstellen Sie einen Filter, der verhindert, dass Berechtigungen für eine Datei geändert werden können. Stellen Sie eine [SSH][aks-ssh]-Verbindung zu einem AKS-Knoten her, und erstellen Sie dann einen Seccomp-Filter namens */var/lib/kubelet/seccomp/prevent-chmod*, und fügen Sie folgenden Inhalt ein:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Erstellen Sie auf Ihrem lokalen Computer nun ein Podmanifest names *aks-seccomp.yaml*, und fügen Sie den folgenden Inhalt ein. Dieses Manifest bestimmt eine Anmerkung für `seccomp.security.alpha.kubernetes.io` und verweist auf den *prevent-chmod*-Filter, der im vorherigen Schritt erstellt wurde:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Stellen Sie den Beispielpod mithilfe des Befehls [kubectl apply][kubectl-apply] bereit:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Sehen Sie sich den Status des Pods mithilfe des Befehls [kubectl get pods][kubectl-get] an. Der Pod gibt eine Fehlermeldung zurück. Wie in folgender Beispielausgabe ersichtlich wird, wird das Ausführen des `chmod`-Befehls vom Seccomp-Filter verhindert:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Weitere Informationen zu verfügbaren Filtern erhalten Sie im Artikel [Seccomp security profiles for Docker (Seccomp-Sicherheitsprofile für Docker)][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Regelmäßiges Update auf die neueste Version von Kubernetes

**Best Practice-Anleitung:** Führen Sie regelmäßig Upgrades auf die Kubernetes-Version in Ihrem AKS-Cluster durch, um alle neuen Features und Fehlerbehebungen zu erhalten.

Kubernetes veröffentlicht neue Features viel schneller als herkömmliche Infrastrukturplattformen. Kubernetes-Updates beinhalten neue Features und Behebungen von Fehlern oder Sicherheitsproblemen. Neue Features durchlaufen normalerweise eine *Alphaversion* und dann eine *Betaversion* bevor sie *stabil* und allgemein verfügbar sind und zur Verwendung in der Produktion empfohlen werden. Dieser Veröffentlichungszyklus sollte es Ihnen ermöglichen, Kubernetes zu aktualisieren, ohne ständig auf Breaking Changes zu treffen oder Ihre Bereitstellungen und Vorlagen anpassen zu müssen.

AKS unterstützt vier Nebenversionen von Kubernetes. Wenn also eine neue Neben-/Patchversion veröffentlicht wird, laufen die älteste Nebenversion und unterstützte Patchreleases aus. Kleinere Kubernetes-Updates werden regelmäßig durchgeführt. Sorgen Sie für einen Governance-Prozess, der regelmäßig notwendige Überprüfungen und Upgrades durchführt, damit der Support ohne Probleme funktioniert. Weitere Informationen finden Sie unter [Unterstützte Kubernetes-Versionen in Azure Kubernetes Service (AKS)][aks-supported-versions].

Wenn Sie überprüfen möchten, welche Versionen für Ihr Cluster verfügbar sind, verwenden Sie den Befehl [az aks get-upgrades][az-aks-get-upgrades] wie in folgendem Beispiel beschrieben:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Sie können dann mithilfe des Befehls [az aks upgrade][az-aks-upgrade] ein Upgrade auf Ihren AKS-Cluster durchführen. Der Upgradeprozess sperrt Knoten sicher einen nach dem anderen ab und gleicht sie aus, legt einen Zeitplan für die verbleibenden Knoten fest und stellt dann einen neuen Knoten bereit, der die aktuellsten Versionen des Betriebssystems und von Kubernetes ausführt.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.8
```

Weitere Informationen zu Upgrades in AKS erhalten Sie in den Artikeln [Unterstützte Kubernetes-Versionen in Azure Kubernetes Service (AKS)][aks-supported-versions] und [Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Verarbeiten von Updates und Neustarts von Linux-Knoten mithilfe von kured

**Best Practice-Anleitung:** AKS lädt automatisch Behebungen von Sicherheitsproblemen für jeden einzelnen Linux-Knoten herunter und installiert diese. Allerdings werden notwendige Neustarts nicht automatisch durchgeführt. Verwenden Sie `kured`, um auf ausstehende Neustarts zu achten, und sperren Sie den Knoten dann sicher ab, und gleichen Sie ihn aus, um dem Knoten einen Neustart zu ermöglichen. Wenden Sie anschließend die Updates an, und gehen Sie so umsichtig wie möglich mit Ihrem Betriebssystem um. Führen Sie für Windows Server-Knoten (derzeit in der Vorschau in AKS) regelmäßig ein AKS-Upgrade durch, um die Pods sicher abzusperren und zu leeren und aktualisierte Knoten bereitzustellen.

Jeden Abend werden über die Updateverteilungskanäle Sicherheitspatches für Linux-Knoten in AKS zur Verfügung gestellt. Dieses Verhalten ist im Rahmen der Bereitstellung von Knoten in einem AKS-Cluster automatisch konfiguriert. Neustarts werden für Knoten nicht automatisch ausgeführt, wenn ein Sicherheitspatch oder Kernelupdate es erfordern würde, um Störungen und eventuelle negative Einflüsse auf ausgeführte Workloads zu minimieren.

Das Open-Source-Projekt [kured (KUbernetes REboot Daemon) (kured (Kubernetes Neustart-Daemon))][kured] von Weaveworks überwacht ausstehende Neustarts für Knoten. Wenn ein Linux-Knoten Updates implementiert, die einen Neustart erfordern, wird der Knoten sicher abgesperrt und geleert, um Pods in andere Knoten des Clusters zu verschieben und einen Zeitplan für sie festzulegen. Sobald der Knoten neu gestartet wurde, wird er dem Cluster wieder hinzugefügt, und Kubernetes fährt damit fort, Zeitpläne für Pods in ihm festzulegen. Immer nur ein Knoten auf einmal kann durch `kured` neu gestartet werden, um Störungen so gering wie möglich zu halten.

![Der Neustartvorgang eines AKS-Knotens mithilfe von kured.](media/operator-best-practices-cluster-security/node-reboot-process.png)

Wenn Sie detailgenau steuern möchten, wann Neustarts durchgeführt werden, kann `kured` in Prometheus integriert werden, sodass Neustarts verhindert werden, wenn gleichzeitig andere Wartungsereignisse stattfinden oder der Cluster in irgendeiner anderen Weise involviert ist. Diese Integration minimiert zusätzliche durch Neustarts von Knoten hervorgerufene Komplikationen, wenn Sie gerade aktiv andere Probleme behandeln möchten.

Weitere Informationen zu Neustarts von Knoten finden Sie im Artikel [Anwenden von Sicherheits- und Kernelupdates auf Knoten in Azure Kubernetes Service (AKS)][aks-kured].

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie AKS-Cluster gesichert werden. Wenn Sie einige dieser Best Practices implementieren möchten, lesen Sie folgende Artikel:

* [Integrieren von Azure Active Directory in AKS][aks-aad]
* [Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)][aks-upgrade]
* [Anwenden von Sicherheits- und Kernelupdates auf Knoten in Azure Kubernetes Service (AKS)][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
