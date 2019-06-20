---
title: Bereitstellen von OpenShift in Azure Stack | Microsoft-Dokumentation
description: Hier finden Sie Informationen zum Bereitstellen von OpenShift in Azure Stack.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: haroldw
ms.openlocfilehash: 91b37753ae80596612eda9d3ccd34858691e35ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60771548"
---
# <a name="deploy-openshift-container-platform-or-okd-in-azure-stack"></a>Bereitstellen von OpenShift Container Platform oder OKD in Azure Stack

OpenShift kann in Azure Stack bereitgestellt werden. Es gibt einige wesentliche Unterschiede zwischen Azure und Azure Stack, daher weichen die Bereitstellung und die Funktionen etwas voneinander ab.

Zurzeit funktioniert der Azure-Cloudanbieter nicht in Azure Stack. Aus diesem Grund können Sie in Azure Stack keine Datenträger für dauerhafte Speicherung anfügen. Stattdessen können Sie andere Speicheroptionen konfigurieren, z.B. NFS, iSCSI, GlusterFS usw. Als Alternative können Sie CNS aktivieren und GlusterFS zur dauerhaften Speicherung verwenden. Wenn CNS aktiviert ist, werden drei zusätzliche Knoten mit zusätzlichem Speicher für die Verwendung durch GlusterFS bereitgestellt.

Zum Bereitstellen von OpenShift Container PD in Azure Stack können Sie verschiedene Methoden verwenden:

- Sie können die erforderlichen Azure-Infrastrukturkomponenten manuell bereitstellen und dann die Anweisungen in der [Dokumentation zu OpenShift Container Platform](https://docs.openshift.com/container-platform) oder der [Dokumentation zu OKD](https://docs.okd.io) befolgen.
- Sie können auch eine vorhandene [Ressourcen-Manager-Vorlage](https://github.com/Microsoft/openshift-container-platform/) verwenden, die die Bereitstellung des OpenShift Container Platform-Clusters vereinfacht.
- Sie können auch eine vorhandene [Resource Manager-Vorlage](https://github.com/Microsoft/openshift-origin) verwenden, die die Bereitstellung des OKD-Clusters vereinfacht.

Wenn Sie die Resource Manager-Vorlage verwenden, wählen Sie den richtigen Branch aus (azurestack-release-3.x). Die Vorlagen für Azure funktionieren nicht, weil sich die API-Versionen für Azure und Azure Stack unterscheiden. Der RHEL-Imageverweis ist zurzeit als Variable in der Datei „azuredeploy.json“ hartcodiert und muss Ihrem Image entsprechend geändert werden.

```json
"imageReference": {
    "publisher": "Redhat",
    "offer": "RHEL-OCP",
    "sku": "7-4",
    "version": "latest"
}
```

Für alle Optionen ist ein Red Hat-Abonnement erforderlich. Während der Bereitstellung wird die Red Hat Enterprise Linux-Instanz beim Red Hat-Abonnement registriert und an die Pool-ID, die die Berechtigungen für OpenShift Container Platform enthält, angefügt.
Stellen Sie sicher, dass Sie über einen gültigen Benutzernamen, ein gültiges Kennwort und eine gültige Pool-ID für Red Hat Subscription Manager (RHSM) verfügen. Alternativ dazu können Sie einen Aktivierungsschlüssel, eine Organisations-ID und eine Pool-ID verwenden.  Sie können diese Informationen überprüfen, indem Sie sich hier anmelden: https://access.redhat.com.

## <a name="azure-stack-prerequisites"></a>Voraussetzungen für Azure Stack

Ein RHEL-Image (OpenShift Container Platform) oder CentOS-Image (OKD) muss Ihrer Azure Stack-Umgebung hinzugefügt werden, damit ein OpenShift-Cluster bereitgestellt werden kann. Wenden Sie sich an ihren Azure Stack-Administrator, um diese Images hinzuzufügen. Anweisungen dazu finden Sie hier:

- https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image
- https://docs.microsoft.com/azure/azure-stack/azure-stack-marketplace-azure-items
- https://docs.microsoft.com/azure/azure-stack/azure-stack-redhat-create-upload-vhd

## <a name="deploy-by-using-the-openshift-container-platform-or-okd-resource-manager-template"></a>Bereitstellen mithilfe der Ressourcen-Manager-Vorlage für OpenShift Container Platform oder OKD

Zum Bereitstellen mithilfe der Ressourcen-Manager-Vorlage werden die Eingabeparameter über eine Parameterdatei angegeben. Forken Sie zur weiteren Anpassung der Bereitstellung das GitHub-Repository, und ändern Sie die entsprechenden Elemente.

Im Folgenden finden Sie eine Auswahl allgemeiner Optionen, die Sie anpassen können:

- Größe der geschützten VM (Variable in „azuredeploy.json“)
- Namenskonvention (Variable in „azuredeploy.json“)
- OpenShift-Clusterbesonderheiten, angepasst über „hosts“-Datei („deployOpenShift.sh“)
- Verweis auf das RHEL-Image (Variable in „azuredeploy.json“)

Die Schritte für die Bereitstellung mithilfe der Azure CLI finden Sie im entsprechenden Abschnitt für [OpenShift Container Platform](./openshift-container-platform.md) oder [OKD](./openshift-okd.md).

## <a name="next-steps"></a>Nächste Schritte

- [Aufgaben nach der Bereitstellung](./openshift-post-deployment.md)
- [Behandeln von Problemen beim Bereitstellen von OpenShift in Azure](./openshift-troubleshooting.md)