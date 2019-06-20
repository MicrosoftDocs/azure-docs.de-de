---
title: Anzeigen von Containerprotokollen in Azure Service Fabric | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Containerprotokolle für einen ausgeführten Service Fabric-Containerdienst mithilfe von Service Fabric Explorer angezeigt werden.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 0408010a49b8ec83aa02c74887139f663788ad80
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60881473"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Anzeigen von Protokollen für einen Service Fabric-Containerdienst
Azure Service Fabric ist ein Containerorchestrator und unterstützt [Linux- und Windows-Container](service-fabric-containers-overview.md).  In diesem Artikel wird beschrieben, wie Containerprotokolle eines ausgeführten Containerdiensts oder eines inaktiven Containers angezeigt werden, sodass Sie Probleme diagnostizieren und beheben können.

## <a name="access-the-logs-of-a-running-container"></a>Zugreifen auf die Protokolle eines ausgeführten Containers
Der Zugriff auf Containerprotokolle ist über [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) möglich.  Öffnen Sie in einem Webbrowser Service Fabric Explorer über den Verwaltungsendpunkt des Clusters. Navigieren Sie dazu zu [http://mycluster.region.cloudapp.azure.com:19080/Explorer](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Die Containerprotokolle befinden sich auf dem Clusterknoten, auf dem die Containerdienstinstanz ausgeführt wird. Rufen Sie beispielsweise die Protokolle für den Front-End-Webcontainer der [Linux-Voting-Beispielanwendung](service-fabric-quickstart-containers-linux.md) ab. Erweitern Sie in der Strukturansicht **Cluster**>**Anwendungen**>**VotingType**>**fabric:/Voting/azurevotefront**.  Erweitern Sie anschließend die Partition (in diesem Beispiel d1aa737e-f22a-e347-be16-eec90be24bc1), und überprüfen Sie, ob der Container auf dem Clusterknoten *_lnxvm_0* ausgeführt wird.

Suchen Sie in der Strukturansicht das Codepaket auf dem Knoten *_lnxvm_0*, indem Sie **Knoten**> **_lnxvm_0**>**fabric:/Voting**>**azurevotfrontPkg**>**Codepakete**>**Code** erweitern.  Wählen Sie dann die Option **Containerprotokolle** aus, um die Containerprotokolle anzuzeigen.

![Service Fabric-Plattform][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Zugreifen auf die Protokolle inaktiver oder abgestürzter Container
Ab Version 6.2 können Sie auch die Protokolle für inaktive oder abgestürzte Container mithilfe von Befehlen der [REST-APIs](/rest/api/servicefabric/sfclient-index) oder der [Service Fabric-CLI (SFCTL)](service-fabric-cli.md) abrufen.

### <a name="set-container-retention-policy"></a>Festlegen der Aufbewahrungsrichtlinie für Container
Zur Diagnose von Startfehlern unterstützt Service Fabric (ab Version 6.1) die Aufbewahrung von Containern, die beendet wurden oder nicht gestartet werden konnten. Diese Richtlinie kann in der Datei **ApplicationManifest.xml** festgelegt werden, wie im folgenden Codeausschnitt gezeigt:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

Mit der Einstellung **ContainersRetentionCount** wird die Anzahl von Containern angegeben, die bei Fehlern beibehalten werden sollen. Wenn ein negativer Wert angegeben wird, werden alle fehlerhaften Container beibehalten. Wenn das Attribut **ContainersRetentionCount** nicht angegeben wird, werden keine Container beibehalten. Das Attribut **ContainersRetentionCount** unterstützt auch Anwendungsparameter, sodass Benutzer unterschiedliche Werte für Test- und Produktionscluster angeben können. Nutzen Sie bei Verwendung dieses Features Platzierungseinschränkungen, um den Containerdienst einem bestimmten Knoten zuzuordnen und zu verhindern, dass er auf andere Knoten verschoben wird. Alle Container, die mit diesem Feature beibehalten werden, müssen manuell entfernt werden.

Die Einstellung **RunInteractive** entspricht den [Docker-Flags](https://docs.docker.com/engine/reference/commandline/run/#options) `--interactive` und `tty`. Wenn diese Einstellung in der Manifestdatei auf TRUE festgelegt wurde, werden diese Flags verwendet, um den Container zu starten.  

### <a name="rest"></a>REST
Verwenden Sie den Vorgang [Abrufen von auf Containerknoten bereitgestellten Containerprotokollen](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode), um die Protokolle für einen abgestürzten Container abzurufen. Geben Sie den Namen des Knotens, auf dem der Container ausgeführt wurde, den Anwendungsnamen, den Dienstmanifestnamen und den Codepaketnamen an.  Geben Sie `&Previous=true` an. Die Antwort enthält die Containerprotokolle für den inaktiven Container der Codepaketinstanz.

Der Anforderungs-URI weist folgende Form auf:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Beispiel für eine Anforderung:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200-Antworttext:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Verwenden Sie den Befehl [sfctl service get-container-logs](service-fabric-sfctl-service.md), um die Protokolle für einen abgestürzten Container abzurufen.  Geben Sie den Namen des Knotens, auf dem der Container ausgeführt wurde, den Anwendungsnamen, den Dienstmanifestnamen und den Codepaketnamen an. Geben Sie das `--previous`-Flag an.  Die Antwort enthält die Containerprotokolle für den inaktiven Container der Codepaketinstanz.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Antwort:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Nächste Schritte
- Durcharbeiten des Tutorials [Erstellen einer Linux-Containeranwendung](service-fabric-tutorial-create-container-images.md).
- Weitere Informationen zu [Service Fabric und Containern](service-fabric-containers-overview.md).

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
