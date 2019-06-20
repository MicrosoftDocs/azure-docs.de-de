---
title: Azure Container Registry-Authentifizierung mit einem Dienstprinzipal
description: Gewähren Sie Zugriff auf Images in Ihrer privaten Containerregistrierung, indem Sie einen Azure Active Directory-Dienstprinzipal verwenden.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61347064"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry-Authentifizierung mit Dienstprinzipalen

Sie können einen Dienstprinzipal von Azure Active Directory (Azure AD) verwenden, um den Containerimages `docker push` und `pull` den Zugriff auf Ihre Containerregistrierung zu gewähren. Mithilfe eines Dienstprinzipals können Sie den Zugriff für „monitorlose“ Dienste und Anwendungen ermöglichen.

## <a name="what-is-a-service-principal"></a>Was ist ein Dienstprinzipal?

*Dienstprinzipale* von Azure AD ermöglichen den Zugriff auf Azure-Ressourcen innerhalb Ihres Abonnements. Sie können sich einen Dienstprinzipal als Benutzeridentität für einen Dienst vorstellen, wobei „Dienst“ jede Anwendung, jeder Dienst oder jede Plattform sein kann, die Zugriff auf die Ressourcen benötigt. Sie haben die Möglichkeit, einen Dienstprinzipal mit Zugriffsrechten so zu konfigurieren, dass diese nur für die von Ihnen angegebenen Ressourcen gelten. Anschließend legen Sie für Ihre Anwendung bzw. Ihren Dienst fest, dass diese bzw. dieser die Anmeldeinformationen des Dienstprinzipals verwendet, um auf diese Ressourcen zuzugreifen.

Im Rahmen der Azure Container Registry können Sie einen Azure AD-Dienstprinzipal mit Pull-, Push- und Pull- oder anderen Berechtigungen für Ihre private Registrierung in Azure erstellen. Eine vollständige Liste finden Sie unter [Azure Container Registry – Rollen und Berechtigungen](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Gründe für die Verwendung eines Dienstprinzipals

Mithilfe eines Azure AD-Dienstprinzipals können Sie bereichsbezogenen Zugriff auf Ihre private Containerregistrierung gewähren. Für Ihre einzelnen Anwendungen oder Dienste können Sie verschiedene Dienstprinzipale erstellen, die jeweils über abgestimmte Zugriffsrechte für die Registrierung verfügen. Und da Sie die Anmeldeinformationen nicht an Dienste und Anwendungen weitergeben müssen, haben Sie die Möglichkeit, ausschließlich für den gewählten Dienstprinzipal (und somit für die Anwendung) Anmeldeinformationen weiterzugeben oder Zugriffsrechte zu widerrufen.

Beispielsweise kann Ihre Webanwendung einen Dienstprinzipal verwenden, der ihr nur Zugriff auf Image `pull` ermöglicht, während Ihr Buildsystem einen Dienstprinzipal verwenden kann, der ihm sowohl Zugriff auf `push` als auch `pull` bietet. Wenn die Entwicklung Ihrer Anwendung von einer anderen Person übernommen wird, können Sie die Anmeldeinformationen für den Dienstprinzipal weitergeben, ohne das Buildsystem zu beeinflussen.

## <a name="when-to-use-a-service-principal"></a>Einsatzbereiche eines Dienstprinzipals

Sie sollten einen Dienstprinzipal verwenden, um in **monitorlosen Szenarien** Zugriff auf die Registrierung bieten. Das heißt, dies betrifft jede Anwendung, jeden Dienst oder jedes Skript, das Containerimages in automatisierter oder anderweitig unbeaufsichtigter Weise pushen oder pullen muss.

Für den individuellen Zugriff auf eine Registrierung, z.B. wenn Sie manuell ein Containerimage auf Ihre Entwicklungsarbeitsstation pullen, sollten Sie stattdessen Ihre eigene [Azure AD-Identität](container-registry-authentication.md#individual-login-with-azure-ad) für den Registrierungszugriff verwenden (z.B. mit [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>Beispielskripts

Die obigen Beispielskripts für die Azure CLI auf GitHub sowie Versionen für Azure PowerShell finden Sie hier:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>Nächste Schritte

Sobald Sie einem Dienstprinzipal Zugriff auf Ihre Containerregistrierung gewährt haben, können Sie seine Anmeldeinformationen in Ihren Anwendungen und Diensten für die monitorlose Interaktion mit der Registrierung verwenden. Sie können die Anmeldeinformationen des Dienstprinzipals in jedem Azure-Dienst verwenden, der mit einer Azure-Containerregistrierung authentifiziert werden kann. Beispiele:

* [Authentifizieren per Azure Container Registry über Azure Kubernetes Service (AKS)](container-registry-auth-aks.md)
* [Authentifizieren per Azure Container Registry über Azure Container Instances (ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
