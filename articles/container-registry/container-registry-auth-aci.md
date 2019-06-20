---
title: Authentifizieren per Azure Container Registry über Azure Container Instances
description: Es wird beschrieben, wie Sie Zugriff auf Images in Ihrer privaten Containerregistrierung über Azure Container Instances gewähren, indem Sie einen Azure Active Directory-Dienstprinzipal verwenden.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: 8a2d19a09233e510055e147fa1cf95dd4471768b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61333576"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Authentifizieren per Azure Container Registry über Azure Container Instances

Sie können einen Dienstprinzipal von Azure Active Directory (Azure AD) verwenden, um den Zugriff auf Ihre privaten Containerregistrierungen in Azure Container Registry zu gewähren.

In diesem Artikel erfahren Sie, wie Sie einen Azure AD-Dienstprinzipal mit *Pull*-Berechtigungen für Ihre Registrierung erstellen und konfigurieren. Dann starten Sie einen Container in Azure Container Instances (ACI), der sein Image mit einem Pullvorgang aus Ihrer privaten Registrierung überträgt, indem er den Dienstprinzipal für die Authentifizierung verwendet.

## <a name="when-to-use-a-service-principal"></a>Einsatzbereiche eines Dienstprinzipals

Ein Dienstprinzipal eignet sich für die Authentifizierung von ACI in **monitorlosen Szenarien**, z.B. in Anwendungen oder Diensten, die Containerinstanzen in automatisierter oder anderweitig unbeaufsichtigter Weise erstellen.

Wenn Sie beispielsweise über ein automatisiertes Skript verfügen, das jede Nacht ausgeführt wird und eine [aufgabenbasierte Containerinstanz](../container-instances/container-instances-restart-policy.md) zur Verarbeitung einiger Daten erstellt, kann hierfür zum Authentifizieren bei der Registrierung ein Dienstprinzipal mit Pull-Only-Berechtigungen verwendet werden. Sie können dann die Anmeldeinformationen des Dienstprinzipals weitergeben oder den Zugriff vollständig widerrufen, ohne andere Dienste und Anwendungen zu beeinträchtigen.

Dienstprinzipale sollten auch dann verwendet werden, wenn der [Administratorbenutzer](container-registry-authentication.md#admin-account) der Registrierung deaktiviert ist.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>Authentifizieren mithilfe des Dienstprinzipals

Um einen Container in Azure Container Instances mithilfe eines Dienstprinzipals zu starten, geben Sie die ID für `--registry-username` und das zugehörige Kennwort für `--registry-password` ein.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>Beispielskripts

Die obigen Beispielskripts für die Azure CLI auf GitHub sowie Versionen für Azure PowerShell finden Sie hier:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel enthalten weitere Informationen zum Arbeiten mit Dienstprinzipalen und ACR:

* [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md)
* [Authentifizieren per Azure Container Registry über Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
