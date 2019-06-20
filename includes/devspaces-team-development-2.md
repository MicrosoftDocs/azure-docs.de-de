---
title: Includedatei
description: Includedatei
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157872"
---
### <a name="run-the-service"></a>Ausführen des Diensts

Drücken Sie F5 (oder geben Sie im Terminalfenster `azds up` ein), um den Dienst auszuführen. Der Dienst wird automatisch im neu ausgewählten Bereich _dev/scott_ ausgeführt. Führen Sie `azds list-up` aus, um sich zu vergewissern, dass Ihr Dienst in einem eigenen Bereich ausgeführt wird:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Wie Sie sehen, wird nun eine Instanz von *mywebapi* im Bereich _dev/scott_ ausgeführt. Die in _dev_ ausgeführte Version wird zwar weiterhin ausgeführt, ist aber nicht in der Liste enthalten.

Führen Sie `azds list-uris` aus, um die URLs für den aktuellen Bereich aufzulisten.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Beachten Sie, dass die URL des öffentlichen Zugriffspunkts für *webfrontend* das Präfix *scott.s* besitzt. Diese URL ist eindeutig für den Bereich _dev/scott_. Durch das URL-Präfix wird der Eingangscontroller angewiesen, Anforderungen an die Version _dev/scott_ eines Diensts weiterzuleiten. Wenn von Dev Spaces eine Anforderung mit dieser URL verarbeitet wird, versucht der Eingangscontroller zunächst, die Anforderung an den Dienst *webfrontend* im Bereich _dev/scott_ weiterzuleiten. Ist dies nicht möglich, wird die Anforderung als Fallback an den Dienst *webfrontend* im Bereich _dev_ weitergeleitet. Beachten Sie, dass auch eine Localhost-URL für den Dienstzugriff über Localhost (unter Verwendung der *Portweiterleitung* von Kubernetes) verfügbar ist. Weitere Informationen zu URLs und zum Routing in Azure Dev Spaces finden Sie unter [Funktionsweise und Konfiguration von Azure Dev Spaces](../articles/dev-spaces/how-dev-spaces-works.md).



![Bereichsrouting](../articles/dev-spaces/media/common/Space-Routing.png)

Mit dieser integrierten Funktion von Azure Dev Spaces können Sie Code in einer freigegebenen Umgebung testen, ohne dass die einzelnen Entwickler den kompletten Dienststapel in ihrem Bereich neu erstellen müssen. Für dieses Routing muss Ihr App-Code Weitergabeheader weiterleiten, wie im vorherigen Schritt dieses Leitfadens erläutert.

### <a name="test-code-in-a-space"></a>Testen von Code in einem Bereich
Wenn Sie Ihre neue Version von *mywebapi* mit *webfrontend* testen möchten, öffnen Sie in Ihrem Browser die URL des öffentlichen Zugriffspunkts für ein *webfrontend*, und navigieren Sie zur Seite „Info“. Ihre neue Meldung sollte angezeigt werden.

Entfernen Sie jetzt den Teil „scott.s.“ aus der URL, und aktualisieren Sie den Browser. Jetzt sollten Sie das frühere Verhalten sehen (mit der in _dev_ ausgeführten *mywebapi*-Version).

Sobald Sie einen _dev_-Bereich haben, der immer die aktuellen Änderungen enthält, und vorausgesetzt, Ihre Anwendung ist zur Nutzung des in diesem Abschnitt des Tutorials beschriebenen bereichsbasierten Routings von Dev Spaces konzipiert, sollte es leicht nachvollziehbar sein, von welch großem Nutzen Dev Spaces beim Testen neuer Features im Kontext der größeren Anwendung sein kann. Anstatt _alle_ Dienste in Ihrem privaten Bereich bereitstellen zu müssen, können Sie einen von _dev_ abgeleiteten privaten Bereich erstellen und darin nur die Dienste bereitstellen, an denen Sie tatsächlich arbeiten. Die Routinginfrastruktur von Dev Spaces übernimmt dann den Rest: Sie nutzt alle Dienste, die Sie in Ihrem privaten Bereich findet, und verwendet standardmäßig wieder die aktuelle Version im Bereich _dev_. Und besser noch: _Mehrere_ Entwickler können zur gleichen Zeit aktiv unterschiedliche Dienste in ihrem eigenen Bereich entwickeln, ohne einander zu stören.

### <a name="well-done"></a>Gut gemacht!
Sie haben den Leitfaden zu den ersten Schritten abgeschlossen. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Einrichten von Azure Dev Spaces mit einem verwalteten Kubernetes-Cluster in Azure
> * Iteratives Entwickeln von Code in Containern
> * Unabhängiges Entwickeln von zwei separaten Diensten und Verwenden der DNS-Dienstermittlung von Kubernetes, um einen anderen Dienst aufzurufen
> * Produktives Entwickeln und Testen Ihres Codes in einer Teamumgebung
> * Einrichten einer Baseline der Funktionen mithilfe von Dev Spaces, um isolierte Änderungen im Kontext einer größeren Microserviceanwendung mühelos zu testen

Nachdem Sie sich mit Azure Dev Spaces vertraut gemacht haben, können Sie [Ihren Entwicklungsbereich für Teammitglieder freigeben](../articles/dev-spaces/how-to/share-dev-spaces.md) und mit der Zusammenarbeit beginnen.

## <a name="clean-up"></a>Bereinigen
Um eine Azure Dev Spaces-Instanz in einem Cluster vollständig zu löschen, einschließlich aller Entwicklungsbereiche und der darin ausgeführten Dienste, können Sie den Befehl `az aks remove-dev-spaces` verwenden. Beachten Sie, dass diese Aktion nicht rückgängig gemacht werden kann. Sie können dem Cluster die Unterstützung für Azure Dev Spaces wieder hinzufügen, aber dies entspricht einem kompletten Neubeginn. Ihre alten Dienste und Bereiche werden nicht wiederhergestellt.

Im folgenden Beispiel werden die Azure Dev Spaces-Controller Ihres aktiven Abonnements aufgelistet, und anschließend wird der Azure Dev Spaces-Controller gelöscht, der dem AKS-Cluster „myaks“ in der Ressourcengruppe „myaks-rg“ zugeordnet ist.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```