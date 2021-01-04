---
title: Verwaltete Containerregistrierungen
description: Enthält eine Einführung in den Dienst „Azure-Containerregistrierung“ und die Bereitstellung von cloudbasierten, verwalteten, privaten Docker-Registrierungen.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b5c81d8b2e6d7eac2dcd9070bf1f448340ea1a18
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341264"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Einführung in private Docker-Containerregistrierungen in Azure

Azure Container Registry ist ein verwalteter, privater Docker-Registrierungsdienst, der auf Version 2.0 der Open-Source-Docker-Registrierung basiert. Erstellen und verwalten Sie Azure-Containerregistrierungen, um Ihre privaten Docker-Containerimages und zugehörigen Artefakte zu speichern und zu verwalten.

Verwenden Sie Azure-Containerregistrierungen mit Ihren vorhandenen Pipelines für die Containerentwicklung und -bereitstellung, oder nutzen Sie Azure Container Registry Tasks, um Containerimages in Azure zu erstellen. Erstellen Sie bedarfsgesteuerte oder voll automatisierte Builds mit Triggern wie etwa Quellcode-Commits und Basisimage-Aktualisierungen.

Weitere Informationen zu Docker und Registrierungskonzepten finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/) sowie unter [About registries, repositories, and images](container-registry-concepts.md) (Informationen zu Registrierungen, Repositorys und Images).

## <a name="use-cases"></a>Anwendungsfälle

Rufen Sie Images aus einer Azure-Containerregistrierung für verschiedene Bereitstellungsziele ab:

* **Skalierbare Orchestrierungssysteme** zum Verwalten von Anwendungen in Containern über Cluster mit Hosts hinweg, z.B. [DC/OS](https://kubernetes.io/docs/), [Docker Swarm](https://docs.mesosphere.com/) und [Kubernetes](https://docs.docker.com/get-started/swarm-deploy/).
* **Azure-Dienste**, die die bedarfsorientierte Erstellung und Ausführung von Anwendungen unterstützen, z. B. [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) und andere.

Entwickler können im Rahmen eines Workflows der Containerentwicklung auch eine Pushübertragung in eine Containerregistrierung durchführen. Sie können Daten beispielsweise mit einem Tool für Continuous Integration und Continuous Delivery an eine Containerregistrierung wie etwa [Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) oder [Jenkins](https://jenkins.io/) übertragen.

Konfigurieren Sie ACR Tasks für das automatische erneute Erstellen von Anwendungsimages, wenn die Basisimages aktualisiert werden, oder automatisieren Sie Imagebuilds, wenn Ihr Team Code in einem Git-Repository committet. Erstellen Sie Tasks mit mehreren Schritten, um das parallele Erstellen, Testen und Patchen mehrerer Containerimages in der Cloud zu automatisieren.

Azure bietet für die Verwaltung Ihrer Azure-Containerregistrierungen verschiedene Tools, etwa die Azure-Befehlszeilenschnittstelle, das Azure-Portal und API-Unterstützung. Installieren Sie optional die [Docker-Erweiterung für Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) und die [Azure-Kontoerweiterung](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) für die Verwendung mit Ihren Azure-Containerregistrierungen. In Visual Studio Code können Sie Pull- und Pushvorgänge für Images in einer Azure-Containerregistrierung oder auch ACR Tasks ausführen.

## <a name="key-features"></a>Wichtige Features

* **Registrierungsdienstebenen**: Erstellen Sie in Ihrem Azure-Abonnement eine oder mehrere Containerregistrierungen. Registrierungen sind in drei Ebenen verfügbar: [Basic, Standard und Premium](container-registry-skus.md). Diese unterstützen jeweils die Webhook-Integration, die Registrierungsauthentifizierung mit Azure Active Directory sowie Löschfunktionen. Nutzen Sie den lokalen Speicher in räumlicher Nähe zu Ihren Containerimages, indem Sie eine Registrierung an demselben Azure-Standort wie Ihre Bereitstellungen erstellen. Verwenden der [Georeplikation](container-registry-geo-replication.md) von Premium-Registrierungen für erweiterte Replikations- und Containerimageverteilung-Szenarien. 

* **Sicherheit und Zugriff**: Melden Sie sich mithilfe der Azure-Befehlszeilenschnittstelle oder mit dem Standardbefehl `docker login` bei der Registrierung an. Azure Container Registry überträgt Containerimages über HTTPS und unterstützt TLS zum Sichern von Clientverbindungen. 

  > [!IMPORTANT]
  > Ab dem 13. Januar 2020 setzt Azure Container Registry voraus, dass alle sicheren Verbindungen von Servern und Anwendungen TLS 1.2 verwenden. Aktivieren Sie TLS 1.2 mithilfe eines beliebigen aktuellen Docker-Clients (Version 18.03.0 und höher). Die Unterstützung für TLS 1.0 und 1.1 wird eingestellt. 

  Sie [steuern den Zugriff](container-registry-authentication.md) auf eine Containerregistrierung mit einer Azure-Identität, einem auf Azure Active Directory basierenden [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) oder einem bereitgestellten Administratorkonto. Verwenden Sie die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC), um Benutzern oder Systemen differenzierte Berechtigungen für eine Registrierung zuzuweisen.

  Zu den Sicherheitsfeatures der Premium-Dienstebene zählen [Inhaltsvertrauen](container-registry-content-trust.md) für das Signieren von Imagetags und [Firewalls und virtuelle Netzwerke (Vorschau)](container-registry-vnet.md), um den Zugriff auf die Registrierung einzuschränken. Azure Security Center wird optional in Azure Container Registry integriert, um [Images zu überprüfen](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json), wenn ein Image in eine Registrierung gepusht wird.

* **Unterstützte Images und Artefakte**: Jedes Image wird in einem Repository gruppiert und ist eine schreibgeschützte Momentaufnahme eines mit Docker kompatiblen Containers. Azure-Containerregistrierungen können sowohl Windows- als auch Linux-Images enthalten. Sie steuern Imagenamen für alle Containerbereitstellungen. Verwenden Sie [Docker-Standardbefehle](https://docs.docker.com/engine/reference/commandline/), um Images in ein Repository zu übertragen (Push) oder ein Image aus einem Repository abzurufen (Pull). Zusätzlich zu den Dockercontainerimages speichert die Azure Container Registry [zugehörige Inhaltsformate](container-registry-image-formats.md) wie [Helm-Diagramme](container-registry-helm-repos.md) und Images, die nach der [Bildformatspezifikation Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) erstellt wurden.

* **Automatisierte Imagebuilds**: Verwenden Sie [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks), um das Erstellen, Testen, Pushen und Bereitstellen von Images in Azure zu optimieren. So können Sie mit ACR Tasks beispielsweise Ihre Inner Loop-Entwicklungsumgebung auf die Cloud ausdehnen, indem Sie Vorgänge vom Typ `docker build` in Azure auslagern. Konfigurieren Sie Buildaufgaben, um die Pipeline für das Containerbetriebssystem- und Frameworkpatching zu automatisieren und automatisch Images zu erstellen, wenn das Team ein Commit für Code an die Quellcodeverwaltung ausführt.

  [Mehrstufige Aufgaben](container-registry-tasks-overview.md#multi-step-tasks) bieten schrittbasierte Aufgabendefinition und -ausführung für Erstellen, Testen und Patchen von Containerimages in der Cloud. Aufgabenschritte definieren einzelne Containerimagebuild- und Pushvorgänge. Sie können auch die Ausführung eines oder mehrerer Container mit jedem Schritt definieren, wobei der Container als Ausführungsumgebung verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Containerregistrierung mit dem Azure-Portal](container-registry-get-started-portal.md)
* [Erstellen einer Containerregistrierung mit der Azure-Befehlszeilenschnittstelle](container-registry-get-started-azure-cli.md)
* [Automatisieren von Betriebssystem- und Frameworkpatches mit ACR Tasks](container-registry-tasks-overview.md)