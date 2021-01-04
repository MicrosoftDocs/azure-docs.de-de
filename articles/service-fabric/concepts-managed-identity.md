---
title: Verwaltete Identitäten für Azure
description: Erfahren Sie mehr über die Verwendung von verwalteten Identitäten für Azure mit Service Fabric.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 28c992792d4572a43e12f5d32855f8411b0f4c6f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574750"
---
# <a name="using-managed-identities-for-azure-with-service-fabric"></a>Verwenden verwalteter Identitäten für Azure mit Service Fabric

Eine gängige Herausforderung beim Entwickeln von Cloudanwendungen ist die sichere Verwaltung der Anmeldeinformationen in Ihrem Code für die Authentifizierung bei verschiedenen Diensten, ohne diese lokal auf einer Entwicklerarbeitsstation oder in der Quellcodeverwaltung zu speichern. *Verwaltete Identitäten für Azure* lösen dieses Problem für alle Ihre Ressourcen in Azure Active Directory (Azure AD), indem sie automatisch verwaltete Identitäten in Azure AD bereitstellen. Sie können die Identität eines Diensts für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt, einschließlich Key Vault. Hierfür müssen keine Anmeldeinformationen im Code gespeichert werden.

*Verwaltete Identitäten für Azure-Ressourcen* sind in Azure AD für Azure-Abonnements kostenlos. Es fallen keine zusätzlichen Kosten an.

> [!NOTE]
> *Verwaltete Identitäten für Azure* ist der neue Name für den Dienst, der früher als „Verwaltete Dienstidentität“ (Managed Service Identity, MSI) bezeichnet wurde.

## <a name="concepts"></a>Konzepte

Verwaltete Identitäten für Azure basieren auf verschiedenen wichtigen Konzepten:

- **Client-ID:** ein eindeutiger, in Azure AD generierter Bezeichner, der während der ersten Bereitstellung an eine Anwendung und einen Dienstprinzipal gebunden wird (siehe auch [Anwendungs-ID](../active-directory/develop/developer-glossary.md#application-id-client-id))

- **Prinzipal-ID:** die Objekt-ID des Dienstprinzipalobjekts für die verwaltete Identität, mit der der rollenbasierte Zugriff auf eine Azure-Ressource gewährt wird

- **Dienstprinzipal:** ein Azure Active Directory-Objekt, das die Projektion einer AAD-Anwendung in einem bestimmten Mandanten darstellt (siehe auch [Dienstprinzipalobjekt](../active-directory/develop/developer-glossary.md#service-principal-object))

Es gibt zwei Arten von verwalteten Identitäten:

- Eine **vom System zugewiesene verwaltete Identität** wird direkt für eine Azure-Dienstinstanz aktiviert.  Der Lebenszyklus einer vom System zugewiesenen Identität ist für die Azure-Dienstinstanz eindeutig, für die sie aktiviert wurde.
- Eine **vom Benutzer zugewiesene verwaltete Identität** wird als eigenständige Azure-Ressource erstellt. Die Identität kann mindestens einer Azure-Dienstinstanz zugewiesen werden und wird separat von den Lebenszyklen dieser Instanzen verwaltet.

Weitere Informationen zu den Unterschieden zwischen den Typen verwalteter Identitäten finden Sie unter [Wie funktionieren verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

## <a name="supported-scenarios-for-service-fabric-applications"></a>Unterstützte Szenarien für Service Fabric-Anwendungen

Verwaltete Identitäten für Service Fabric werden nur in Service Fabric-Clustern unterstützt, die in Azure bereitgestellt werden, und auch nur für Anwendungen, die als Azure-Ressourcen bereitgestellt werden. Einer Anwendung, die nicht als Azure-Ressourcen bereitgestellt wird, kann keine Identität zugewiesen werden. Konzeptionell besteht die Unterstützung für verwaltete Identitäten in einem Azure Service Fabric-Cluster aus zwei Phasen:

1. Zuweisen mindestens einer verwalteten Identität zu der Anwendungsressource. Einer Anwendung können jeweils eine einzelne vom System zugewiesene Identität und/oder bis zu 32 vom Benutzer zugewiesene Identitäten zugewiesen werden.

2. Zuordnen einer der Identitäten, die der Anwendung zugewiesen sind, zu einem einzelnen Dienst, der die Anwendung enthält, in der Anwendungsdefinition

Die vom System zugewiesene Identität einer Anwendung ist eindeutig für diese Anwendung. Eine vom Benutzer zugewiesene Identität ist eine eigenständige Ressource, die mehreren Anwendungen zugewiesen werden kann. Innerhalb einer Anwendung kann eine einzelne (vom System oder vom Benutzer zugewiesene) Identität mehreren Diensten der Anwendung zugewiesen werden, jedem einzelnen Dienst kann jedoch nur eine einzige Identität zugewiesen werden. Schließlich muss einem Dienst explizit eine Identität zugewiesen werden, um auf diese Funktion zugreifen zu können. Tatsächlich ermöglicht die Zuordnung der Identitäten einer Anwendung zu den zugehörigen einzelnen Diensten eine Isolation in der Anwendung. In einem Dienst kann nur die ihm zugewiesene Identität verwendet werden.  

Zurzeit werden die folgenden Szenarien für dieses Feature unterstützt:

- Bereitstellen einer neuen Anwendung mit einem oder mehreren Diensten und einer oder mehreren zugewiesenen Identitäten

- Zuweisen mindestens einer verwalteten Identität zu einer vorhandenen (in Azure bereitgestellten) Anwendung, um auf Azure-Ressourcen zuzugreifen

Die folgenden Szenarien werden nicht unterstützt oder nicht empfohlen. Beachten Sie, dass diese Aktionen möglicherweise nicht blockiert werden, aber zu Ausfällen in Ihren Anwendungen führen können:

- Entfernen oder Ändern der Identitäten, die einer Anwendung zugewiesen sind. Wenn Sie Änderungen vornehmen möchten, übermitteln Sie separate Bereitstellungen, um zunächst die Zuweisung einer neuen Identität hinzuzufügen und dann eine zuvor zugewiesene Identität zu entfernen. Das Entfernen einer Identität aus einer vorhandenen Anwendung kann unerwünschte Auswirkungen haben und z. B. die Anwendung in einen nicht aktualisierbaren Zustand bringen. Die Anwendung kann vollständig gelöscht werden, wenn das Entfernen einer Identität erforderlich ist. Beachten Sie, dass dadurch die der Anwendung zugeordnete vom System zugewiesene Identität gelöscht wird (sofern definiert) und alle Zuordnungen mit den der Anwendung vom Benutzer zugewiesenen Identitäten entfernt werden.

- Die Unterstützung für verwaltete Identitäten durch Service Fabric ist zurzeit nicht in [AzureServiceTokenProvider](../key-vault/general/service-to-service-authentication.md) integriert.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen eines neuen Azure Service Fabric-Clusters mit Unterstützung für verwaltete Identitäten](./configure-new-azure-service-fabric-enable-managed-identity.md)
- [Aktivieren Sie die Unterstützung der verwalteten Identität in einem bereits vorhandenen Azure Service Fabric-Cluster.](./configure-existing-cluster-enable-managed-identity-token-service.md)
- [Bereitstellen einer Azure Service Fabric-Anwendung mit einer systemseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
- [Bereitstellen einer Azure Service Fabric-Anwendung mit einer benutzerseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
- [Nutzen der verwalteten Identität einer Service Fabric-Anwendung aus dem Dienstcode](./how-to-managed-identity-service-fabric-app-code.md)
- [Gewähren des Zugriffs auf andere Azure-Ressourcen für eine Azure Service Fabric-Anwendung](./how-to-grant-access-other-resources.md)
- [Deklarieren und Verwenden von Anwendungsgeheimnissen als KeyVaultReferences](./service-fabric-keyvault-references.md)
