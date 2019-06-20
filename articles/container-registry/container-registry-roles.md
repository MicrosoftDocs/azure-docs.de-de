---
title: 'Azure Container Registry: Rollen und Berechtigungen'
description: Verwenden Sie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) und das Identity & Access Management (IAM) von Azure, um differenzierte Berechtigungen für Ressourcen in einer Azure-Containerregistrierung bereitzustellen.
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.openlocfilehash: d62dd6c65975d63a0127bb5dd1c62cd741b59ac6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068002"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry: Rollen und Berechtigungen

Der Azure Container Registry-Dienst unterstützt mehrere Azure-Rollen, die unterschiedliche Berechtigungsstufen für eine Azure-Containerregistrierung bereitstellen. Verwenden Sie die [rollenbasierte Zugriffssteuerung](../role-based-access-control/index.yml) (Role-Based Access Control, RBAC) von Azure, um Benutzern oder Dienstprinzipalen, die mit einer Registrierung interagieren müssen, bestimmte Berechtigungen zuzuweisen.

| Rolle/Berechtigung       | [Zugreifen auf Resource Manager](#access-resource-manager) | [Erstellen/löschen einer Registrierung](#create-and-delete-registry) | [Übertragen eines Image mithilfe von Push](#push-image) | [Übertragen eines Images mithilfe von Pull](#pull-image) | [Löschen von Imagedaten](#delete-image-data) | [Ändern von Richtlinien](#change-policies) |   [Signieren von Images](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Owner (Besitzer) | X | X | X | X | X | X |  |  
| Mitwirkender | X | X | X |  X | X | X |  |  
| Leser | X |  |  |  |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Unterscheiden von Benutzern und Diensten

Immer wenn Berechtigungen angewendet werden, hat es sich bewährt, einen möglichst begrenzte Anzahl von Berechtigungen an eine Person oder einen Dienst bereitzustellen, um eine Aufgabe zu erfüllen. Die folgenden Berechtigungen stehen für mehrere Funktionen, die von Menschen und entkoppelten Diensten verwendet werden können.

### <a name="cicd-solutions"></a>CI/CD-Lösungen

Bei der Automatisierung von `docker build`-Befehlen aus CI/CD-Lösungen benötigen Sie `docker push`-Funktionen. Es wird empfohlen, für diese Szenarios entkoppelter Dienste die Rolle **AcrPush** zuzuweisen. Im Gegensatz zur umfassenderen Rolle **Mitwirkender** verhindert sie, dass das Konto andere Registrierungsvorgänge durchführt oder auf den Azure Resource Manager zugreift.

### <a name="container-host-nodes"></a>Containerhostknoten

Knoten, auf denen Ihre Container ausgeführt werden, benötigen ebenfalls die Rolle **AcrPull**, sollten aber keine **Leserfunktionen** erfordern.

### <a name="visual-studio-code-docker-extension"></a>Docker-Erweiterung für Visual Studio Code

Für Tools wie die [Docker-Erweiterung](https://code.visualstudio.com/docs/azure/docker) für Visual Studio Code ist zusätzlicher Ressourcenanbieterzugriff erforderlich, um die verfügbaren Azure-Containerregistrierungen aufzulisten. Erteilen Sie Ihren Benutzern in diesem Fall Zugriff auf die Rollen **Leser** und **Mitwirkender**. Diese Rollen ermöglichen `docker pull`, `docker push`, `az acr list`, `az acr build` und andere Funktionen. 

## <a name="access-resource-manager"></a>Zugreifen auf Resource Manager

Der Zugriff auf den Azure Resource Manager ist für das Azure-Portal und die Registrierungsverwaltung mit der [Azure CLI](/cli/azure/) erforderlich. Diese Berechtigungen benötigen Sie zum Beispiel, um mit dem `az acr list`-Befehl eine Liste mit Registrierungen zu erhalten. 

## <a name="create-and-delete-registry"></a>Erstellen und Löschen der Registrierung

Die Fähigkeit zum Erstellen und Löschen von Azure-Containerregistrierungen.

## <a name="push-image"></a>Übertragen eines Image mithilfe von Push

Die Fähigkeit, ein Image mithilfe von `docker push` oder ein anderes [unterstütztes Artefakt](container-registry-image-formats.md), z.B. ein Helm-Chart, mithilfe von Push an eine Registrierung zu übertragen. Dafür ist eine [Authentifizierung](container-registry-authentication.md) bei der Registrierung mit der autorisierten Identität erforderlich. 

## <a name="pull-image"></a>Übertragen eines Images mithilfe von Pull

Die Fähigkeit, ein nicht unter Quarantäne stehendes Image mithilfe von `docker pull` oder ein anderes [unterstütztes Artefakt](container-registry-image-formats.md), z.B. ein Helm-Chart, mithilfe von Pull aus einer Registrierung zu übertragen. Dafür ist eine [Authentifizierung](container-registry-authentication.md) bei der Registrierung mit der autorisierten Identität erforderlich.

## <a name="delete-image-data"></a>Löschen von Imagedaten

Die Möglichkeit zum Löschen von [Containerimages](container-registry-delete.md) oder zum Löschen anderer [unterstützter Artefakte](container-registry-image-formats.md) wie Helm-Diagramme aus einer Registrierung.

## <a name="change-policies"></a>Ändern von Richtlinien

Die Fähigkeit, Richtlinien für eine Registrierung zu konfigurieren. Die Richtlinien umfassen das Löschen von Images, das Aktivieren der Quarantäne und das Signieren von Images.

## <a name="sign-images"></a>Signieren von Images

Die Fähigkeit, Images zu signieren, die in der Regel einem automatisierten Prozess zugewiesen wird, der einen Dienstprinzipal verwenden würde. Diese Berechtigung wird meist mit [push image](#push-image) kombiniert, damit ein vertrauenswürdiges Image mithilfe von Push an eine Registrierung übertragen werden kann. Weitere Informationen finden Sie unter [Inhaltsvertrauen in Azure Container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das Zuweisen von RBAC-Rollen an eine Azure-Identität mit dem [Azure-Portal](../role-based-access-control/role-assignments-portal.md), der [Azure CLI](../role-based-access-control/role-assignments-cli.md) oder anderen Azure-Tools.

* Weitere Informationen finden Sie unter den [Authentifizierungsoptionen](container-registry-authentication.md) für Azure Container Registry.
