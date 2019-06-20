---
title: Containergruppen von Azure Container Instances
description: Funktionsweise von Gruppen mit mehreren Containern in Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: cba57875daf9b570d274ec8c4e9c4146af0dc045
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072829"
---
# <a name="container-groups-in-azure-container-instances"></a>Containergruppen in Azure Container Instances

Die oberste Ressource in Azure Container Instances ist die *Containergruppe*. In diesem Artikel wird beschrieben, was Containergruppen sind und welche Arten von Szenarien damit möglich sind.

## <a name="how-a-container-group-works"></a>Funktionsweise einer Containergruppe

Eine Containergruppe ist eine Sammlung mit Containern, die auf demselben Hostcomputer geplant werden. Für die Container einer Containergruppe werden der Lebenszyklus, die Ressourcen, das lokale Netzwerk und die Speichervolumes gemeinsam genutzt. Dies ähnelt dem *Pod*-Konzept in [Kubernetes][kubernetes-pod].

Das folgende Diagramm zeigt ein Beispiel für eine Containergruppe, die mehrere Container enthält:

![Diagramm zu Containergruppen][container-groups-example]

Für diese Beispielcontainergruppe gilt Folgendes:

* Sie wird auf einem einzelnen Hostcomputer geplant.
* Ihr ist eine DNS-Namensbezeichnung zugewiesen.
* Sie macht eine einzelne öffentliche IP-Adresse mit einem Port verfügbar.
* Sie besteht aus zwei Containern. Ein Container lauscht über Port 80 und der andere über Port 5000.
* Sie enthält zwei Azure-Dateifreigaben als Volumebereitstellungen, und jeder Container stellt eine der Freigaben lokal bereit.

> [!NOTE]
> Gruppen mit mehreren Containern unterstützen aktuell nur Linux-Container. Für Windows-Container unterstützt Azure Container Instances nur die Bereitstellung einer einzelnen Instanz. Bis alle Features auch für Windows-Container verfügbar sind, finden Sie die aktuellen Plattformunterschiede in der [Dienstübersicht](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Bereitstellung

Zwei gebräuchliche Wege zum Bereitstellen einer Gruppe mit mehreren Containern sind eine [Resource Manager-Vorlage][resource-manager template] und eine [YAML-Datei][yaml-file]. Verwenden Sie eine Resource Manager-Vorlage, wenn Sie bei der Bereitstellung von Containerinstanzen zusätzliche Azure-Dienstressourcen (z.B. eine [Azure Files-Freigabe][azure-files]) bereitstellen müssen. Das YAML-Format ist präziser, daher wird eine YAML-Datei empfohlen, wenn Ihre Bereitstellung nur Containerinstanzen enthält.

Mit dem Azure CLI-Befehl [az container export][az-container-export] können Sie die Konfiguration einer Containergruppe in eine YAML-Datei exportieren, um diese beizubehalten. Durch den Export können Sie die Konfiguration einer Containergruppe in der Versionskontrolle für „Konfiguration als Code“ speichern. Oder Sie können die exportierte Datei als Ausgangspunkt bei der Entwicklung einer neuen Konfiguration in YAML verwenden.

## <a name="resource-allocation"></a>Ressourcenzuteilung

Azure Container Instances weist einer Containergruppe Ressourcen wie CPUs, Arbeitsspeicher und optional [GPUs][gpus] (Vorschau) zu, indem die [Ressourcenanforderungen][resource-requests] der Instanzen in der Gruppe hinzugefügt werden. Wenn Sie zum Beispiel bei CPU-Ressourcen eine Containergruppe mit zwei Instanzen erstellen, von denen jede 1 CPU erfordert, werden der Containergruppe 2 CPUs zugeordnet.

Die maximal für eine Containergruppe verfügbaren Ressourcen hängen von der [Azure-Region][region-availability] ab, die für die Bereitstellung verwendet wird.

### <a name="container-resource-requests-and-limits"></a>Containerressourcenanforderungen und -grenzwerte

* Standardmäßig teilen sich die Containerinstanzen in einer Gruppe die angeforderten Ressourcen der Gruppe. In einer Gruppe mit zwei Instanzen, von denen jede 1 CPU erfordert, hat die Gruppe als Ganzes Zugriff auf 2 CPUs. Jede Instanz kann bis zu 2 CPUs verwenden, und die Instanzen konkurrieren während der Ausführung möglicherweise um eine CPU-Ressource.

* Sie können optional einen [Ressourcengrenzwert][resource-limits] für eine Instanz festlegen, um die Ressourcennutzung der Instanz in der Gruppe zu beschränken. In einer Gruppe mit zwei Instanzen, die 1 CPU erfordern, können für einen der Container mehr CPUs für die Ausführung erforderlich sein als für den anderen.

  In diesem Szenario könnten Sie einen Ressourcengrenzwert von 0,5 CPU für die eine Instanz und einen Grenzwert von 2 CPUs für die zweite festlegen. Diese Konfiguration beschränkt die Ressourcennutzung des ersten Containers auf 0,5 CPU und lässt den zweiten Container gegebenenfalls bis zu 2 CPUs verwenden.

Weitere Informationen finden Sie unter der Eigenschaft [ResourceRequirements][resource-requirements] in der REST-API für Containergruppen.

### <a name="minimum-and-maximum-allocation"></a>Minimale und maximale Zuordnung

* Ordnen Sie einer Containergruppe **mindestens** 1 CPU und 1 GB Arbeitsspeicher zu. Einzelne Containerinstanzen in einer Gruppe können mit weniger als 1 CPU und 1 GB Arbeitsspeicher bereitgestellt werden. 

* Informationen zur **maximalen** Zuordnung von Ressourcen in einer Containergruppe finden Sie unter [Ressourcenverfügbarkeit][region-availability] für Azure Container Instances in der Bereitstellungsregion.

## <a name="networking"></a>Netzwerk

Containergruppen nutzen eine IP-Adresse und einen Portnamespace dieser IP-Adresse gemeinsam. Sie müssen den Port unter der IP-Adresse und für den Container verfügbar machen, um es externen Clients zu ermöglichen, einen Container in der Gruppe zu erreichen. Da Container in der Gruppe einen Portnamespace gemeinsam nutzen, wird die Portzuordnung nicht unterstützt. Container in einer Gruppe können einander per Localhost auf den Ports erreichen, die sie verfügbar gemacht haben. Dies gilt auch, wenn diese Ports für die IP-Adresse der Gruppe nicht extern verfügbar gemacht werden.

Durch die optionale Bereitstellung von Containergruppen in einem [virtuellen Azure-Netzwerk][virtual-network] (Vorschau) können Container sicher mit anderen Ressourcen im virtuellen Netzwerk kommunizieren.

## <a name="storage"></a>Storage

Sie können externe Volumes für die Bereitstellung in einer Containergruppe angeben. Sie können diese Volumes bestimmten Pfaden in den einzelnen Containern einer Gruppe zuordnen.

## <a name="common-scenarios"></a>Häufige Szenarios

Gruppen mit mehreren Containern sind nützlich, wenn Sie eine einzelne funktionale Aufgabe auf eine kleine Anzahl von Containerimages aufteilen wollen. Diese Images können dann von verschiedenen Teams bereitgestellt werden und separate Ressourcenanforderungen aufweisen.

Beispiel für Verwendung:

* Ein Container, über den eine Webanwendung bereitgestellt wird, und ein Container, der den aktuellen Inhalt aus der Quellcodeverwaltung abruft.
* Ein Anwendungscontainer und ein Protokollierungscontainer. Mit dem Protokollierungscontainer werden die Protokolle und Metriken gesammelt, die von der Hauptanwendung ausgegeben werden, und in den Langzeitspeicher geschrieben.
* Ein Anwendungscontainer und ein Überwachungscontainer. Der Überwachungscontainer sendet regelmäßig eine Anforderung an die Anwendung, um sicherzustellen, dass sie ausgeführt wird und richtig reagiert. Andernfalls wird eine Warnung ausgelöst.
* Ein Front-End-Container und ein Back-End-Container. Das Front-End kann als Webanwendung dienen und das Back-End einen Dienst zum Abrufen von Daten ausführen. 

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das Bereitstellen einer Gruppe mit mehreren Containern per Azure Resource Manager-Vorlage.

> [!div class="nextstepaction"]
> [Bereitstellen einer Containergruppe][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
