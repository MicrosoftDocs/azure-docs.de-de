---
title: Problembehandlung bei allgemeinen Problemen mit Azure Kubernetes Service
description: Erfahren Sie, wie Sie allgemeine Probleme bei der Verwendung von Azure Kubernetes Service (AKS) beheben und lösen können
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: f0b0ff3ff4ac742a7e850798c736eb31098f66e8
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65966378"
---
# <a name="aks-troubleshooting"></a>AKS-Problembehandlung

Beim Erstellen oder Verwalten von Clustern mit Azure Kubernetes Service (AKS) können gelegentlich Probleme auftreten. In diesem Artikel werden einige allgemeine Probleme und Schritte zur Problembehandlung ausführlich erläutert.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Wo finde ich grundsätzlich Informationen zum Debuggen von Problemen mit Kubernetes?

Lesen Sie den [offiziellen Leitfaden zur Problembehandlung von Kubernetes-Clustern](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Es gibt auch einen [Leitfaden zur Problembehandlung](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md), der von einem Microsoft-Techniker veröffentlicht wurde und sich mit der Problembehandlung von Pods, Knoten, Clustern usw. befasst.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Während der Erstellung oder Aktualisierung erhalte ich die Fehlermeldung, dass das Kontingent überschritten wurde. Wie sollte ich vorgehen? 

Sie müssen [Kerne anfordern](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Was ist die maximale Einstellung für die Anzahl von Pods pro Knoten für AKS?

Die maximale Anzahl von Pods pro Knoten ist standardmäßig auf 30 festgelegt, wenn Sie einen AKS-Cluster im Azure-Portal bereitstellen.
Die maximale Anzahl von Pods pro Knoten ist standardmäßig auf 110 festgelegt, wenn Sie einen AKS-Cluster über die Azure-Befehlszeilenschnittstelle bereitstellen. (Stellen Sie sicher, dass Sie die aktuelle Version der Azure-Befehlszeilenschnittstelle verwenden.) Diese Standardeinstellung kann mithilfe des Flags `–-max-pods` im Befehl `az aks create` geändert werden.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Bei der Bereitstellung eines AKS-Clusters mit erweitertem Netzwerk erhalte ich einen insufficientSubnetSize-Fehler. Wie sollte ich vorgehen?

Wenn Azure CNI (erweiterte Netzwerke) verwendet wird, weist AKS auf Basis des unter „Max. Pods pro Knoten“ konfigurierten Werts vorab IP-Adressen zu. Die Anzahl der Knoten in einem AKS-Cluster können zwischen 1 und 110 liegen. Basierend auf dem unter „Max. Pods pro Knoten“ konfigurierten Wert sollte die Subnetzgröße die Summe aus der Anzahl der Knoten mal der Höchstzahl der Pods pro Knoten überschreiten. Die folgende einfache Gleichung zeigt dies:

Subnetzgröße > Anzahl der Knoten im Cluster (unter Berücksichtigung der zukünftigen Skalierungsanforderungen) * Max. Pods pro Knoten.

Weitere Informationen finden Sie unter [Planen der IP-Adressierung für Ihren Cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mein Pod ist im Modus CrashLoopBackOff hängen geblieben. Wie sollte ich vorgehen?

Es kann verschiedene Gründe dafür geben, dass der Pod in diesem Modus hängen bleibt. Untersuchen Sie Folgendes:

* Den Pod selbst mithilfe von `kubectl describe pod <pod-name>`
* Die Protokolle mit `kubectl log <pod-name>`

Weitere Informationen zur Behandlung von Podproblemen finden Sie unter [Debuggen von Anwendungen](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ich versuche, RBAC in einem vorhandenen Cluster zu aktivieren. Wie gehe ich dazu vor?

Eine Aktivierung der rollenbasierten Zugriffssteuerung (RBAC) in vorhandenen Clustern wird derzeit nicht unterstützt. Sie müssen explizit neue Cluster erstellen. Wenn Sie die Befehlszeilenschnittstelle verwenden, ist RBAC standardmäßig aktiviert. Wenn Sie das AKS-Portal verwenden, ist eine Umschaltfläche zum Aktivieren von RBAC im Erstellungsworkflow verfügbar.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Ich habe über die Azure-Befehlszeilenschnittstelle mit Standardwerten oder über das Azure-Portal einen Cluster mit aktivierter RBAC erstellt, und nun werden im Kubernetes-Dashboard zahlreiche Warnungen angezeigt. Das Dashboard hat vorher ohne Warnungen funktioniert. Wie sollte ich vorgehen?

Der Grund für die Warnungen auf dem Dashboard ist, dass der Cluster nun mit RBAC aktiviert wurde und der Zugriff darauf standardmäßig deaktiviert wurde. Dieser Ansatz gilt allgemein als bewährte Methode, da die standardmäßige Offenlegung des Dashboards für alle Benutzer des Clusters zu Sicherheitsrisiken führen kann. Wenn Sie das Dashboard weiterhin aktivieren möchten, befolgen Sie die Schritte in [diesem Blogbeitrag](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Ich kann keine Verbindung mit dem Dashboard herstellen. Wie sollte ich vorgehen?

Der einfachste Weg, außerhalb des Clusters auf Ihren Dienst zuzugreifen, besteht im Ausführen von `kubectl proxy`, der Proxyanforderungen für Ihren localhost-Port 8001 an den Kubernetes-API-Server sendet. Von dort kann der API-Server eine Proxyverbindung mit Ihrem Dienst herstellen: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Wenn das Kubernetes-Dashboard nicht angezeigt wird, überprüfen Sie, ob der Pod `kube-proxy` im Namespace `kube-system` ausgeführt wird. Wenn der Pod keinen Ausführungsstatus aufweist, müssen Sie ihn löschen. Er wird anschließend neu gestartet.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ich kann Protokolle nicht mithilfe von kubectl-Protokollen abrufen, oder ich kann keine Verbindung mit dem API-Server herstellen. Ich erhalte „Fehler vom Server: Fehler beim Anwählen des Back-Ends: Wählen Sie tcp…“. Wie sollte ich vorgehen?

Stellen Sie sicher, dass die Netzwerksicherheits-Standardgruppe nicht geändert wurde und dass Port 22 für die Verbindung mit dem API-Server geöffnet ist. Überprüfen Sie, ob der `tunnelfront`-Pod im *kube-system*-Namespace ausgeführt wird, indem Sie den Befehl `kubectl get pods --namespace kube-system` verwenden. Falls nicht, erzwingen Sie das Löschen des Pods. Er wird anschließend neu gestartet.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ich möchte ein Upgrade oder eine Skalierung ausführen und erhalte die folgende Fehlermeldung: „Das Ändern der Eigenschaft ‚imageReference‘ ist nicht zulässig.“ Wie kann ich dieses Problem beheben?

Möglicherweise erhalten Sie diese Fehlermeldung, da Sie die Tags in den Agent-Knoten innerhalb des AKS-Clusters geändert haben. Das Ändern und Löschen von Tags und anderen Eigenschaften von Ressourcen in der Ressourcengruppe MC_* kann zu unerwarteten Ergebnissen führen. Durch das Ändern der Ressourcen unter der Gruppe „MC_*“ im AKS-Cluster wird das Servicelevelziel (SLO) unterbrochen.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Ich erhalte Fehler, dass sich mein Cluster in einem fehlerhaften Zustand befindet und ein Upgrade oder eine Skalierung nicht funktioniert, bis der Fehler behoben wurde.

*Diese Unterstützung bei der Problembehandlung wurde von https://aka.ms/aks-cluster-failed weitergeleitet.*

Dieser Fehler tritt auf, wenn Cluster aus mehreren Gründen in einen fehlerhaften Zustand übergehen. Führen Sie die folgenden Schritte aus, um den Fehlerzustand Ihres Clusters zu beheben, bevor Sie den zuvor fehlgeschlagenen Vorgang erneut versuchen:

1. Bis der Cluster den `failed`-Zustand verlassen hat, schlagen `upgrade`- und `scale`-Vorgänge fehl. Häufige Probleme und Lösungen sind unter anderem:
    * Skalieren mit einem **nicht ausreichenden Computekontingent (CRP)** . Um das Problem zu beheben, skalieren Sie zuerst Ihren Cluster wieder auf einen stabilen Zielzustand innerhalb des Kontingents. Befolgen Sie dann diese [Schritte, um eine Erhöhung des Computekontingents anzufordern](../azure-supportability/resource-manager-core-quotas-request.md), bevor Sie versuchen, den Wert erneut über die anfänglichen Kontingentgrenzen hinaus zentral hochzuskalieren.
    * Skalieren eines Clusters mit erweiterter Netzwerkunterstützung und **nicht ausreichenden Subnetzressourcen (Netzwerk)** . Um das Problem zu beheben, skalieren Sie zuerst Ihren Cluster wieder auf einen stabilen Zielzustand innerhalb des Kontingents. Befolgen Sie dann diese [Schritte, um eine Erhöhung des Ressourcenkontingents anzufordern](../azure-resource-manager/resource-manager-quota-errors.md#solution), bevor Sie versuchen, den Wert erneut über die anfänglichen Kontingentgrenzen hinaus zentral hochzuskalieren.
2. Sobald die zugrunde liegende Ursache für den Upgradefehler behoben wurde, sollte sich Ihr Cluster in einem erfolgreichen Zustand befinden. Nachdem ein erfolgreicher Zustand bestätigt wurde, wiederholen Sie den ursprünglichen Vorgang.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Ich erhalte Fehler, wenn ich versuche, den Zustand meines Clusters zu aktualisieren oder zu skalieren, der gerade aktualisiert wird oder bei dem ein Upgrade fehlgeschlagen ist.

*Diese Unterstützung bei der Problembehandlung wurde von https://aka.ms/aks-pending-upgrade weitergeleitet.*

Clustervorgänge sind eingeschränkt, wenn aktive Upgradevorgänge stattfinden oder ein Upgrade versucht wurde, aber anschließend fehlgeschlagen ist. Um das Problem zu diagnostizieren, führen Sie `az aks show -g myResourceGroup -n myAKSCluster -o table` aus, um den detaillierten Status Ihres Clusters abzurufen. Basierend auf dem Ergebnis:

* Wenn ein aktives Upgrade des Clusters durchgeführt wird, warten Sie, bis der Vorgang beendet wurde. Wenn dies erfolgreich war, versuchen Sie den zuvor fehlgeschlagenen Vorgang erneut.
* Wenn das Upgrade des Clusters fehlgeschlagen ist, führen Sie die oben beschriebenen Schritte aus.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kann ich meinen Cluster in ein anderes Abonnement verschieben oder mein Abonnement mit meinem Cluster in einen anderen Mandanten?

Wenn Sie Ihren AKS-Cluster in ein anderes Abonnement verschoben haben oder das Abonnement, das den Cluster besitzt, in einen anderen Mandanten, verliert der Cluster seine Funktionalität aufgrund des Verlusts von Rollenzuweisungen und Dienstprinzipalrechten. Aufgrund dieser Einschränkung **unterstützt AKS das Verschieben von Clustern über Abonnements oder Mandanten nicht**.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ich erhalte Fehler, wenn ich versuche, Funktionen zu verwenden, die VM-Skalierungsgruppen erfordern

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-vmss-enablement“ weitergeleitet.*

Möglicherweise erhalten Sie Fehlermeldungen, die angeben, dass sich Ihr AKS-Cluster nicht in einer VM-Skalierungsgruppe befindet. Beispiel:

**Der AgentPool „Agent-Pool“ hat die Autoskalierung aktiviert, befindet sich aber nicht in einer VM-Skalierungsgruppe.**

Zur Verwendung von Funktionen wie der automatischen Clusterskalierung oder mehrerer Knotenpools müssen AKS-Cluster erstellt werden, die VM-Skalierungsgruppen verwenden. Es werden Fehler zurückgegeben, wenn Sie versuchen Funktionen zu verwenden, die von VM-Skalierungsgruppen abhängig sind, und Sie einen normalen AKS-Cluster ohne VM-Skalierungsgruppen als Ziel verwenden. Die Unterstützung von VM-Skalierungsgruppen befindet sich in AKS derzeit in der Vorschauphase.

Führen Sie die Schritte unter *Voraussetzungen* im entsprechenden Dokument aus, um sich ordnungsgemäß für die Funktionsvorschau für VM-Skalierungsgruppen zu registrieren und einen AKS-Cluster zu erstellen:

* [Verwenden der automatischen Clusterskalierung](cluster-autoscaler.md)
* [Erstellen und Verwenden mehrerer Knotenpools](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welche Benennungseinschränkungen gelten für AKS-Ressourcen und -Parameter?

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-naming-rules“ weitergeleitet.*

Benennungseinschränkungen werden sowohl von der Azure-Plattform als auch AKS implementiert. Verletzt ein Ressourcenname oder Parameter eine dieser Einschränkungen, wird ein Fehler zurückgegeben, der Sie auffordert, eine andere Eingabe zu machen. Es gelten die folgenden allgemeinen Benennungsrichtlinien:

* Der Name der *MC_* -Ressourcengruppe in AKS ist eine Kombination aus Ressourcengruppenname und Ressourcenname. Die automatisch generierte Syntax von `MC_resourceGroupName_resourceName_AzureRegion` darf nicht mehr als 80 Zeichen umfassen. Kürzen Sie bei Bedarf Ihren Ressourcengruppennamen oder AKS-Clusternamen.
* Das *dnsPrefix* muss mit alphanumerischen Werten beginnen und enden. Gültige Zeichen sind alphanumerische Werte und Bindestriche (-). Das *dnsPrefix* darf keine Sonderzeichen wie z.B. einen Punkt (.) enthalten.

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Wenn ich versuche, einen Cluster zu erstellen, zu aktualisieren, zu skalieren, zu löschen oder zu aktualisieren, erhalte ich die Fehlermeldung, dass dieser Vorgang nicht erlaubt sei, da ein anderer Vorgang ausgeführt werde.

*Diese Unterstützung bei der Problembehandlung wurde von „aka.ms/aks-pending-operations“ weitergeleitet*

Clustervorgänge sind nur eingeschränkt möglich, wenn ein vorheriger Vorgang noch im Gange ist. Um den detaillierten Status Ihres Clusters abzurufen, verwenden Sie den Befehl `az aks show -g myResourceGroup -n myAKSCluster -o table`. Verwenden Sie bei Bedarf Ihre eigene Ressourcengruppe und Ihren Namen für den AKS-Cluster.

Basierend auf der Ausgabe des Status des Clusters:

* Wenn sich der Cluster einem anderen Bereitstellungsstatus als *Erfolgreich* oder *Fehler* befindet, warten Sie, bis der Vorgang (*Upgrade/Aktualisieren/Erstellen/Skalieren/Löschen/Migrieren*) abgeschlossen ist. Sobald der vorherige Vorgang abgeschlossen ist, wiederholen Sie Ihren letzten Clustervorgang.

* Wenn im Cluster ein fehlerhaftes Upgrade erfolgt, befolgen Sie die Anweisungen unter [Ich erhalte Fehler, dass sich mein Cluster in einem fehlerhaften Zustand befindet und ein Upgrade oder eine Skalierung nicht funktioniert, bis der Fehler behoben wurde](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).