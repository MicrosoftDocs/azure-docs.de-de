---
title: Häufig gestellte Fragen zu Azure Dev Spaces
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Hier finden Sie Antworten auf einige der häufig gestellten Fragen zu Azure Dev Spaces.
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, Container, Helm, Service Mesh, Service Mesh-Routing, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548833"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Häufig gestellte Fragen zu Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Im Folgenden werden einige der häufig gestellten Fragen zu Azure Dev Spaces beantwortet.

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Welche Versionen von Kubernetes werden für Azure Dev Spaces unterstützt?

Von Azure Dev Spaces werden [derzeit unterstützte Versionen mit allgemeiner Verfügbarkeit (General Availability, GA) von Kubernetes in AKS bis 1.18][aks-supported-k8s] unterstützt. Ab 1.19 wird von Kubernetes in AKS ContainerD als Containerruntime verwendet. Dies funktioniert nicht mit Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>In welchen Azure-Regionen wird Azure Dev Spaces derzeit zur Verfügung gestellt?

Eine vollständige Liste der verfügbaren Regionen finden Sie unter [Unterstützte Regionen][supported-regions].

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Kann ich meinen AKS-Cluster mit Azure Dev Spaces zu einer anderen Region migrieren?

Ja. Wenn Sie Ihren AKS-Cluster mit Azure Dev Spaces in eine andere [unterstützte Region][supported-regions] verschieben möchten, empfiehlt es sich, in der anderen Region einen neuen Cluster zu erstellen und anschließend Azure Dev Spaces zu installieren und zu konfigurieren sowie Ihre Ressourcen und Anwendungen in Ihrem neuen Cluster bereitzustellen. Weitere Informationen zum Migrieren von AKS finden Sie unter [Migrieren zu Azure Kubernetes Service (AKS)][aks-migration].

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Kann ich Azure Dev Spaces mit vorhandenen Dockerfile-Dateien oder Helm-Diagrammen verwenden?

Ja, wenn Ihr Projekt bereits über eine Dockerfile-Datei oder ein Helm-Diagramm verfügt, können Sie diese Dateien mit Azure Dev Spaces verwenden. Wenn Sie `azds prep` ausführen, verwenden Sie den Parameter `--chart`, und geben Sie den Speicherort des Diagramms an. Azure Dev Spaces generiert weiterhin eine Datei *azds.yaml* und *Dockerfile.develop*, ersetzt oder ändert aber eine vorhandene Dockerfile-Datei oder ein Helm-Diagramm nicht. Möglicherweise müssen Sie die Dateien *azds.yaml* und *Dockerfile.develop* ändern, damit alles ordnungsgemäß mit Ihrer vorhandenen Anwendung funktioniert, wenn Sie `azds up` ausführen.

Wenn Sie eine eigene Dockerfile-Datei oder ein eigenes Helm-Diagramm verwenden, gelten die folgenden Einschränkungen:
* Wenn nur eine Dockerfile-Datei verwendet wird, muss sie alles enthalten, was Sie zum Aktivieren von Entwicklungsszenarien benötigen, z. B. das Sprach-SDK und nicht nur die Laufzeit. Wenn Sie eine separate Dockerfile-Datei für Azure Dev Spaces verwenden, z. B. eine Datei „Dockerfile.develop“, muss alles, was Sie zum Aktivieren von Entwicklungsszenarien benötigen, in dieser Dockerfile-Datei enthalten sein.
* In Ihrem Helm-Diagramm muss die Übergabe eines Teils des Imagetags oder des gesamten Imagetags als Wert aus *values.yaml* unterstützt werden.
* Wenn Sie Änderungen am Eingang vornehmen, können Sie auch Ihr Helm-Diagramm aktualisieren, um die von Azure Dev Spaces bereitgestellte Eingangslösung zu verwenden.
* Wenn Sie die [von Azure Dev Spaces bereitgestellten Routingfunktionen][dev-spaces-routing] verwenden möchten, müssen alle Dienste für ein einzelnes Projekt in einen einzelnen Kubernetes-Namespace passen und mit einem einfachen Namen bereitgestellt werden, z. B. mit *service-a*. In Helm-Standarddiagrammen kann dieses Benennungsupdate durch Angeben eines Werts für die *fullnameOverride*-Eigenschaft erfolgen.

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Kann ich die von Azure Dev Spaces generierten Dateien ändern?

Ja, Sie können die von Azure Dev Spaces generierte Datei *azds.yaml*, die Dockerfile-Datei und das Helm-Diagramm ändern, [wenn Sie Ihr Projekt vorbereiten][dev-spaces-prep]. Wenn Sie diese Dateien ändern, ändert sich, wie das Projekt erstellt und ausgeführt wird.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Kann ich Azure Dev Spaces auch ohne eine öffentliche IP-Adresse verwenden?

Nein, Azure Dev Spaces kann ohne öffentliche IP-Adresse nicht in einem AKS-Cluster bereitgestellt werden. Eine öffentliche IP-Adresse ist bei Azure Dev Spaces [für das Routing erforderlich][dev-spaces-routing].

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Kann ich mit Azure Dev Spaces meinen eigenen Dateneingangscontroller verwenden?

Ja, Sie können neben dem von Azure Dev Spaces erstellten Eingangscontroller auch einen eigenen Controller konfigurieren. Hierzu können Sie z. B. [traefik][ingress-traefik] oder [NGINX][ingress-nginx] verwenden.

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Kann ich HTTPS mit Azure Dev Spaces verwenden?

Ja, Sie können mithilfe von [traefik][ingress-https-traefik] oder [NGINX][ingress-https-nginx] Ihren eigenen Eingangscontroller mit HTTPS konfigurieren.

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Kann ich Azure Dev Spaces in einem Cluster verwenden, der CNI anstelle von Kubenet verwendet? 

Ja, Sie können Azure Dev Spaces in einem AKS-Cluster verwenden, der für Netzwerke CNI verwendet. Beispielsweise können Sie Azure Dev Spaces in einem AKS-Cluster mit [vorhandenen Windows-Containern][windows-containers] verwenden, der für Netzwerke CNI verwendet. Weitere Informationen zur Verwendung von CNI für Netzwerke mit Azure Dev Spaces finden Sie [hier](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Kann ich Azure Dev Spaces mit Windows-Containern verwenden?

Derzeit ist Azure Dev Spaces nur für die Ausführung auf Linux-Pods und -Knoten vorgesehen. Sie können Azure Dev Spaces aber in einem AKS-Cluster mit [vorhandenen Windows-Containern][windows-containers] ausführen.

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Kann ich Azure Dev Spaces in AKS-Clustern mit vom API-Server autorisierten IP-Adressbereichen verwenden?

Ja. Sie können Azure Dev Spaces in AKS-Clustern mit [vom API-Server autorisierten IP-Adressbereichen][aks-auth-range] verwenden. Weitere Informationen zur Verwendung eines AKS-Clusters mit vom API-Server autorisierten IP-Adressbereichen, die für Azure Dev Spaces aktiviert sind, finden Sie [hier](configure-networking.md#using-api-server-authorized-ip-ranges).

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Kann ich Azure Dev Spaces in AKS-Clustern mit eingeschränktem ausgehenden Datenverkehr für Clusterknoten verwenden?

Ja. Sie können Azure Dev Spaces in AKS-Clustern mit [eingeschränktem ausgehenden Datenverkehr für Clusterknoten][aks-restrict-egress-traffic] verwenden, wenn die korrekten FQDNs zugelassen wurden. Weitere Informationen zur Verwendung eines AKS-Clusters mit eingeschränktem ausgehenden Datenverkehr für Clusterknoten, die für Azure Dev Spaces aktiviert sind, finden Sie [hier](configure-networking.md#ingress-and-egress-network-traffic-requirements).

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>Kann ich Azure Dev Spaces für Kubernetes RBAC-fähige AKS-Cluster verwenden?

Ja. Sie können Azure Dev Spaces für AKS-Cluster mit oder ohne Aktivierung der rollenbasierten Zugriffssteuerung für Kubernetes (Kubernetes Role-Based Access Control, Kubernetes RBAC) verwenden.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Was geschieht, wenn ich eingehenden Datenverkehr für das Projekt in Visual Studio aktiviere?

Wenn Sie Ihr Projekt mithilfe von Visual Studio vorbereiten, haben Sie die Möglichkeit, eingehenden Datenverkehr für Ihren Dienst zu aktivieren. Durch das Aktivieren von eingehendem Datenverkehr wird ein öffentlicher Endpunkt für den Zugriff auf Ihren Dienst erstellt, wenn er auf Ihrem AKS-Cluster ausgeführt wird. Wenn Sie eingehenden Datenverkehr nicht aktivieren, können Sie nur aus Ihrem AKS-Cluster auf den Dienst zugreifen.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Können verwaltete Podidentitäten mit Azure Dev Spaces verwendet werden?

Ja, Sie können [verwaltete Podidentitäten][aks-pod-managed-id] in AKS-Clustern mit aktiviertem Azure Dev Spaces verwenden. Nach dem Aktivieren von Azure Dev Spaces in Ihrem Cluster mit verwalteten Podidentitäten sind jedoch [zusätzliche Konfigurationsschritte][dev-spaces-pod-managed-id-steps] erforderlich. Informationen zum Deinstallieren ggf. installierter verwalteter Podidentitäten finden Sie bei Bedarf in den [Deinstallationshinweisen][aks-pod-managed-id-uninstall].

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>Kann ich Azure Dev Spaces mit mehreren Microservices in einer Anwendung verwenden?

Ja, Sie können Azure Dev Spaces in einer Anwendung mit mehreren Microservices verwenden, Sie müssen die einzelnen Microservices jedoch in ihrem Stammverzeichnis vorbereiten und ausführen. Die Azure Dev Spaces-Befehlszeilenschnittstelle, die VS Code-Erweiterung für Azure Dev Spaces und die Azure-Entwicklungsworkload in Visual Studio erwarten, dass sich die Datei *azds.yaml* im Stammverzeichnis des Microservice befinden, um diesen auszuführen und zu debuggen. Ein Beispiel für mehrere Microservices in einer einzelnen Anwendung finden Sie in der [Beispielanwendung „Bike Sharing“][bike-sharing].

Es ist in Visual Studio Code möglich, [separate Projekte in einem einzelnen Arbeitsbereich zu öffnen][vs-code-multi-root-workspaces] und separat über Azure Dev Spaces zu debuggen. Jedes Projekt muss eigenständig und für Azure Dev Spaces vorbereitet sein.

In Visual Studio können .NET Core-Lösungen für das Debuggen über Azure Dev Spaces konfiguriert werden.

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>Kann ich Azure Dev Spaces mit einem Service Mesh verwenden?

Derzeit können Sie Azure Dev Spaces nicht mit Service Meshes wie [Istio][istio] oder [Linkerd][linkerd] verwenden. Sie können Azure Dev Spaces und ein Service Mesh im selben AKS-Cluster ausführen, aber Azure Dev Spaces und ein Service Mesh können nicht im selben Namespace aktiviert sein.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md