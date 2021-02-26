---
title: Allgemeine Fragen zu Azure Service Fabric Mesh
description: Informationen zu häufig gestellten Fragen und Antworten zu Azure Service Fabric-Mesh.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626035"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Häufig gestellte Fragen zu Service Fabric Mesh

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen über die Service Fabric Mesh-API werden nicht mehr gestattet. Vorhandene Bereitstellungen werden noch bis zum 28. April 2021 unterstützt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Azure Service Fabric Mesh ist ein vollständig verwalteter Dienst, der es Entwicklern ermöglicht, Microservicesanwendungen zu implementieren, ohne virtuelle Computer, Speicher oder Netzwerke verwalten zu müssen. Dieser Artikel bietet Antworten auf häufig gestellte Fragen.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Wie melde ich ein Problem oder stelle ich eine Frage?

Im [GitHub-Repository service-fabric-mesh-preview](https://aka.ms/sfmeshissues) können Sie Fragen stellen, Antworten von Microsoft-Entwicklern erhalten und Probleme melden.

## <a name="quota-and-cost"></a>Kontingent und Kosten

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Wie hoch sind die Kosten für die Teilnahme an der Vorschauversion?

Es fallen derzeit keine Kosten für die Bereitstellung von Anwendungen oder Containern in der Vorschauversion von Mesh an. Halten Sie im Mai Ausschau nach aktuellen Informationen zu Abrechnungsaktivierung. Sie werden jedoch gebeten, die von Ihnen bereitgestellten Ressourcen zu löschen und nicht weiter auszuführen, es sei denn, Sie testen sie aktiv.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Gibt es eine Kontingentgrenze für die Anzahl der Kerne und den Arbeitsspeicher?

Ja. Die Kontingente für jedes Abonnement sind wie folgt:

- Anzahl von Anwendungen: 5
- Kerne pro Anwendung: 12
- RAM insgesamt pro Anwendung: 48 GB
- Netzwerk- und Eingangsendpunkte: 5
- Anfügbare Azure-Volumes: 10
- Anzahl der Dienstreplikate: 3
- Der größte Container, den Sie bereitstellen können, ist auf 4 Kerne und 16 GB RAM beschränkt.
- Sie können Ihren Containern Teilkerne in Schritten von 0,5 Kernen bis maximal 6 Kernen zuweisen.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Wie lange kann ich meine Anwendung bereitgestellt lassen?

Wir haben die Lebensdauer einer Anwendung zurzeit auf zwei Tage beschränkt. Dadurch soll die Verwendung der für die Vorschau reservierten freien Kerne maximiert werden. Daher dürfen Sie eine bestimmten Bereitstellung nur für 48 Stunden durchgehend ausführen. Danach wird sie heruntergefahren.

Wenn dies geschieht, können Sie bestätigen, dass das System sie heruntergefahren hat, indem Sie in der Azure CLI den Befehl `az mesh app show` ausführen. Überprüfen Sie, ob er `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` zurückgibt. 

Beispiel: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Um die Ressourcengruppe zu löschen, verwenden Sie den Befehl `az group delete <nameOfResourceGroup>`.

## <a name="deployments"></a>Bereitstellungen

### <a name="what-container-images-are-supported"></a>Welche Containerimages werden unterstützt?

Wenn Sie zur Entwicklung einen Computer verwenden, auf dem das Windows Fall Creators Update (Version 1709) installiert ist, können Sie nur Docker-Images für die Windows-Version 1709 verwenden.

Wenn Sie zur Entwicklung einen Computer verwenden, auf dem das Windows 10-Update vom April 2018 (Version 1803) installiert ist, können Sie entweder Docker-Images für Windows-Version 1709 oder 1803 verwenden.

Die folgenden Images von Containerbetriebssystemen können beim Bereitstellen von Diensten verwendet werden:
- Windows: windowsservercore und nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Keine bekannten Einschränkungen

> [!NOTE]
> Visual Studio-Tools für Mesh unterstützen derzeit die Bereitstellung in Windows Server 2019- und -1809-Containern noch nicht.

### <a name="what-types-of-applications-can-i-deploy"></a>Welche Anwendungstypen können werden bereitgestellt? 

Sie können alle Anwendungen bereitstellen, die in Containern ausgeführt werden, die den Einschränkungen für Anwendungsressourcen entsprechen. (Weitere Informationen zu Kontingenten finden Sie weiter oben.) Wenn wir feststellen, dass Sie Mesh zum Ausführen von unzulässigen Workloads verwenden oder das System missbrauchen (z. B. Mining), behalten wir uns das Recht vor, Ihre Bereitstellungen zu beenden und Ihr Abonnement für die Ausführung unter dem Dienst zu sperren. Wenden Sie sich an uns, wenn Sie Fragen zum Ausführen einer bestimmten Workload haben. 

## <a name="developer-experience-issues"></a>Probleme der Entwicklerumgebung

### <a name="dns-resolution-from-a-container-doesnt-work"></a>DNS-Auflösung von einem Container funktioniert nicht

Bei aus einem Container an den Service Fabric-DNS-Dienst ausgehenden DNS-Abfragen können unter bestimmten Umständen Fehler auftreten. Dies wird zurzeit untersucht. So minimieren Sie die Auswirkungen des Problems:

- Verwenden Sie Windows Fall Creators Update (Version 1709) oder höher als Basiscontainerimage.
- Wenn der Dienstname allein nicht funktioniert, versuchen Sie es mit dem vollqualifizierten Namen: ServiceName.ApplicationName.
- Fügen Sie in der Docker-Datei für Ihren Dienst `EXPOSE <port>` hinzu. Dabei ist „port“ der Port, über den Ihr Dienst bereitgestellt wird. Beispiel:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS funktioniert nicht so wie in meinen Service Fabric-Entwicklungsclustern und in Mesh

Möglicherweise müssen Sie auf Dienste in Ihrem lokalen Entwicklungscluster anders als in Azure Mesh verweisen.

Verwenden Sie in Ihrem lokalen Entwicklungscluster `{serviceName}.{applicationName}`. Verwenden Sie in Azure Service Fabric Mesh `{servicename}`. 

Azure Mesh unterstützt zurzeit keine anwendungsübergreifende DNS-Auflösung.

Informationen zu anderen bekannten DNS-Problemen bei der Ausführung eines Service Fabric-Entwicklungsclusters unter Windows 10 finden Sie hier: [Debuggen von Windows-Containern](../service-fabric/service-fabric-how-to-debug-windows-containers.md) und [Bekannte DNS-Probleme](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Netzwerk

Die ServiceFabric-Netzwerk-NAT wird möglicherweise nicht mehr angezeigt, während Sie Ihre Anwendung auf Ihrem lokalen Computer ausführen. Um zu festzustellen, ob dies der Fall ist, führen Sie Folgendes an einer Eingabeaufforderung aus:

`docker network ls`. Achten Sie darauf, ob `servicefabric_nat` aufgeführt wird.  Wenn dies nicht der Fall ist, führen Sie den folgenden Befehl aus: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Dadurch wird das Problem behoben, auch wenn die App bereits lokal bereitgestellt wurde und einen nicht integren Zustand aufweist.

### <a name="issues-running-multiple-apps"></a>Probleme beim Ausführen mehrerer Apps

Es kann vorkommen, dass die CPU-Verfügbarkeit und Einschränkungen für alle Anwendungen festgelegt sind. So minimieren Sie die Auswirkungen des Problems:
- Erstellen Sie einen Cluster mit fünf Knoten.
- Verringern Sie die CPU-Auslastung in Diensten der App, die bereitgestellt wird. Ändern Sie z.B. in der Datei „service.yaml“ Ihres Diensts `cpu: 1.0` in `cpu: 0.5`.

Mehrere Anwendungen können nicht in einem Cluster mit einem Knoten bereitgestellt werden. So minimieren Sie die Auswirkungen des Problems:
- Verwenden Sie einen Cluster mit fünf Knoten, wenn Sie mehrere Apps in einem lokalen Cluster bereitstellen.
- Entfernen Sie Apps, die Sie zurzeit nicht testen.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>VS-Tools bietet eingeschränkte Unterstützung für Windows-Container

Die Visual Studio-Tools unterstützen derzeit lediglich die Bereitstellung von Windows-Containern mit der Basisbetriebssystemversion Windows Server 1709 und 1803. 

## <a name="feature-gaps-and-other-known-issues"></a>Featurelücken und andere bekannte Probleme

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Nach der Bereitstellung meiner Anwendung weist die damit verbundene Netzwerkressource keine IP-Adresse auf

Es gibt ein bekanntes Problem, durch das die IP-Adresse nicht sofort zur Verfügung gestellt wird. Überprüfen Sie den Status der Netzwerkressource nach wenigen Minuten. Die zugehörige IP-Adresse wird dann angezeigt.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Meine Anwendung kann nicht auf die richtige Netzwerk-/Volumeressource zugreifen

Verwenden Sie in Ihrem Anwendungsmodell die vollständige Ressourcen-ID für Netzwerke und Volumes, um auf die zugehörige Ressource zugreifen zu können. Dieses Beispiel stammt aus dem Schnellstartbeispiel:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Wenn ich aufskaliere, sind alle meine Container betroffen, einschließlich derjenigen, die aktuell ausgeführt werden

Dies ist ein Fehler, und eine Lösung wird zurzeit implementiert.

## <a name="next-steps"></a>Nächste Schritte

In der [Übersicht](service-fabric-mesh-overview.md) erfahren Sie mehr über Service Fabric Mesh.
