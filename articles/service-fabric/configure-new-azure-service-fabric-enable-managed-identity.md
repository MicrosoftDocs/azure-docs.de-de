---
title: Konfigurieren von Unterstützung der verwalteten Identität für einen neuen Service Fabric-Cluster
description: 'Vorgehensweise: Aktivieren der Unterstützung der verwalteten Identität in einem neuen Azure Service Fabric-Cluster'
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574665"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurieren von Unterstützung der verwalteten Identität für einen neuen Service Fabric-Cluster

Um [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) in Ihren Service Fabric-Anwendungen zu verwenden, aktivieren Sie zunächst den *Tokendienst für verwaltete Identitäten* auf dem Cluster. Dieser Dienst ist für die Authentifizierung von Service Fabric-Anwendungen anhand ihrer verwalteten Identitäten und für den Abruf von Zugriffstoken in deren Auftrag zuständig. Nachdem der Dienst aktiviert wurde, wird er in Service Fabric Explorer im Abschnitt **System** im linken Bereich unter dem Namen **fabric:/System/ManagedIdentityTokenService** neben anderen Systemdiensten angezeigt.

> [!NOTE]
> Zum Aktivieren des **Tokendiensts für verwaltete Identitäten** ist mindestens Version 6.5.658.9590 der Service Fabric-Runtime erforderlich.  

## <a name="enable-the-managed-identity-token-service"></a>Aktivieren des Tokendiensts für verwaltete Identitäten

Fügen Sie den folgenden Codeausschnitt in einer Azure Resource Manager-Clustervorlage hinzu, um den Tokendienst für verwaltete Identitäten bei der Clustererstellung zu aktivieren:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Errors

Sollte bei der Bereitstellung der folgende Fehler auftreten, verfügt der Cluster nicht über die erforderliche Service Fabric-Version. (Die niedrigste unterstützte Runtimeversion ist 6.5 CU2.)


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Verwandte Artikel

* Machen Sie sich mit der [Unterstützung der verwalteten Identität](./concepts-managed-identity.md) in Azure Service Fabric vertraut.

* [Aktivieren Sie die Unterstützung der verwalteten Identität in einem bereits vorhandenen Azure Service Fabric-Cluster.](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer systemseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer benutzerseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Nutzen der verwalteten Identität einer Service Fabric-Anwendung aus dem Dienstcode](./how-to-managed-identity-service-fabric-app-code.md)
* [Gewähren des Zugriffs auf andere Azure-Ressourcen für eine Azure Service Fabric-Anwendung](./how-to-grant-access-other-resources.md)