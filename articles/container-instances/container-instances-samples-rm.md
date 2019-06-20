---
title: Beispiele für Azure Resource Manager-Vorlagen – Azure Container Instances
description: Beispiele für Azure Resource Manager-Vorlagen für Azure Container Instances
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 03/07/2019
ms.author: danlep
ms.openlocfilehash: 3d73d05c64f4b4867c69a15089c19ab8c320b9a8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60537954"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>Azure Resource Manager-Vorlagen für Azure Container Instances

Die folgenden Beispielvorlagen stellen Containerinstanzen in verschiedenen Konfigurationen bereit.

Informationen zu Bereitstellungsoptionen finden Sie im Abschnitt [Bereitstellung](#deployment). Wenn Sie eigene Vorlagen erstellen möchten, nutzen Sie die ausführlichen Informationen zu Vorlagenformat und verfügbaren Eigenschaften in der [Resource Manager-Vorlagenreferenz][ref] von Azure Container Instances.

## <a name="sample-templates"></a>Beispielvorlagen

| | |
|-|-|
| **Anwendungen** ||
| [WordPress][app-wp] | Erstellt eine WordPress-Website und ihre MySQL-Datenbank in einer Containergruppe. Der Inhalt der WordPress-Website und MySQL-Datenbank werden persistent in einer Azure Files-Freigabe gespeichert. Erstellt auch ein Anwendungsgateway, um öffentlichen Netzwerkzugriff auf WordPress zu ermöglichen. |
| [MS-NAV mit SQLServer und IIS][app-nav] | Stellt einen einzelnen Windows-Container mit einer vollständig ausgestatteten eigenständigen Dynamics NAV / Dynamics 365 Business Central-Umgebung bereit. |
| **Volumes** ||
| [emptyDir][vol-emptydir] | Stellt zwei Linux-Container bereit, die ein emptyDir-Volume gemeinsam nutzen. |
| [gitRepo][vol-gitrepo] | Stellt einen Linux-Container bereit, der ein GitHub-Repository klont und als Volume einbindet. |
| [secret][vol-secret] | Stellt einen Linux-Container mit einem PFX-Zertifikat als geheimes Volume eingebunden bereit. |
| **Netzwerk** ||
| [Für UDP verfügbar gemachter Container][net-udp] | Stellt einen Windows- oder Linux-Container bereit, der einen UDP-Port verfügbar macht. |
| [Linux-Container mit öffentlicher IP-Adresse][net-publicip] | Stellt einen einzelnen Linux-Container bereit, auf den über eine öffentliche IP-Adresse zugegriffen werden kann. |
| [Bereitstellen einer Containergruppe in einem virtuellen Netzwerk (Vorschau)][net-vnet] | Stellt ein neues virtuelles Netzwerk, Subnetz, Netzwerkprofil und eine neue Containergruppe bereit. |
| **Azure-Ressourcen** ||
| [Azure Storage-Konto und Files-Freigabe erstellen][az-files] | Verwendet die Azure CLI in einer Containerinstanz zum Erstellen eines Speicherkontos und einer Azure Files-Freigabe.

## <a name="deployment"></a>Bereitstellung

Sie können zwischen verschiedenen Optionen für das Bereitstellen von Ressourcen mit Resource Manager-Vorlagen wählen:

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure-Portal][deploy-portal]

[REST-API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[net-vnet]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups
