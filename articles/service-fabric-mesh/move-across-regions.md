---
title: Verschieben einer Service Fabric Mesh-Anwendung in eine andere Region
description: Sie können Service Fabric Mesh-Ressourcen verschieben, indem Sie eine Kopie Ihrer aktuellen Vorlage in einer neuen Azure-Region bereitstellen.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626086"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Verschieben einer Service Fabric Mesh-Anwendung in eine andere Azure-Region

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

In diesem Artikel wird beschrieben, wie Sie Ihre Service Fabric Mesh-Anwendung und ihre Ressourcen in eine andere Azure-Region verschieben. Sie können Ihre Ressourcen aus verschiedenen Gründen in eine andere Region verschieben. Beispielsweise als Reaktion auf Ausfälle, um Features oder Dienste zu nutzen, die nur in bestimmten Regionen verfügbar sind, um die internen Richtlinien- und Governanceanforderungen zu erfüllen, oder als Reaktion auf Anforderungen an die Kapazitätsplanung.

 Service Fabric Mesh unterstützt [nicht](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) die Möglichkeit, Ressourcen direkt in Azure-Regionen zu verschieben. Sie können Ressourcen jedoch indirekt verschieben, indem Sie eine Kopie der aktuellen Azure Resource Manager-Vorlage in der neuen Zielregion bereitstellen und dann den eingehenden Datenverkehr und die Abhängigkeiten an die neu erstellte Service Fabric Mesh-Anwendung umleiten.

## <a name="prerequisites"></a>Voraussetzungen

* Eingangscontroller (z. B. [Application Gateway](../application-gateway/index.yml)), der als Vermittler für das Routing von Datenverkehr zwischen Clients und Ihrer Service Fabric Mesh-Anwendung dient.
* Verfügbarkeit von Service Fabric Mesh (Vorschau) in der Azure-Zielregion (`westus`, `eastus` oder `westeurope`).

## <a name="prepare"></a>Vorbereiten

1. Erstellen Sie eine „Momentaufnahme“ des aktuellen Status Ihrer Service Fabric Mesh-Anwendung, indem Sie die Azure Resource Manager-Vorlage und die Parameter aus der aktuellen Bereitstellung exportieren. Befolgen Sie hierzu die Schritte unter [Exportieren einer Vorlage nach der Bereitstellung](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) mithilfe des Azure-Portals. Sie können auch die [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates) oder die [REST-API](/rest/api/resources/resourcegroups/exporttemplate) verwenden.

2. [Exportieren Sie ggf. andere Ressourcen in derselben Ressourcengruppe](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) für eine erneute Bereitstellung in der Zielregion.

3. Überprüfen Sie die exportierte Vorlage (und bearbeiten Sie sie bei Bedarf), um sicherzustellen, dass die vorhandenen Eigenschaftswerte den Werten entsprechen, die Sie in der Zielregion verwenden möchten. Das neue `location`-Element (Azure-Region) ist ein Parameter, den Sie während der erneuten Bereitstellung angeben.

## <a name="move"></a>Move

1. Erstellen Sie eine neue Ressourcengruppe in der Zielregion (oder verwenden Sie eine bereits vorhandene Ressourcengruppe).

2. Führen Sie mit der exportierten Vorlage die Schritte in [Bereitstellen von Ressourcen aus einer benutzerdefinierten Vorlage](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) mithilfe des Azure-Portals aus. Sie können auch die [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) oder die [REST-API](../azure-resource-manager/templates/deploy-rest.md) verwenden.

3. Anleitungen zum Verschieben verwandter Ressourcen (z. B. [Azure Storage Konten](../storage/common/storage-account-move.md)) finden Sie in den Anweisungen für einzelne Dienste, die unter dem Thema [Regionsübergreifendes Verschieben von Azure-Ressourcen](../azure-resource-manager/management/move-region.md) aufgeführt werden.

## <a name="verify"></a>Überprüfen

1. Testen Sie nach Abschluss der Bereitstellung die Anwendungsendpunkte, um die Funktionalität der Anwendung zu überprüfen.

2. Sie können den Status der Anwendung auch überprüfen, indem Sie den Anwendungsstatus überprüfen ([az mesh app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) und die Anwendungsprotokolle und -Befehle mithilfe der [Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli) überprüfen ([az mesh code-package-log](/cli/azure/ext/mesh/mesh/code-package-log)).

## <a name="commit"></a>Commit

Nachdem Sie die äquivalente Funktionalität Ihrer Service Fabric Mesh-Anwendung in der Zielregion bestätigt haben, konfigurieren Sie den Eingangscontroller (z. B. [Application Gateway](../application-gateway/redirect-overview.md)), um den Datenverkehr an die neue Anwendung umzuleiten.

## <a name="clean-up-source-resources"></a>Bereinigen der Quellressourcen

Um das Verschieben der Service Fabric Mesh-Anwendung abzuschließen, [löschen Sie die Quellanwendung und/oder die übergeordnete Ressourcengruppe](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verschieben von Azure-Ressourcen zwischen Regionen](../azure-resource-manager/management/move-region.md)
* [Unterstützung für das regionsübergreifende Verschieben von Azure-Ressourcen](../azure-resource-manager/management/region-move-support.md)
* [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Unterstützung des Verschiebevorgangs für Ressourcen](../azure-resource-manager/management/move-support-resources.md
)
