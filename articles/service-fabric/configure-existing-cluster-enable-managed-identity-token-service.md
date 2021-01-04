---
title: Konfigurieren von Unterstützung der verwalteten Identität in einem vorhandenen Service Fabric-Cluster
description: 'Vorgehensweise: Aktivieren der Unterstützung der verwalteten Identität in einem vorhandenen Azure Service Fabric-Cluster'
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 7bcba34e941f6fee038aa475d7c0869eb65aca2e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574699"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Konfigurieren von Unterstützung der verwalteten Identität in einem vorhandenen Service Fabric-Cluster

Um [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) in Ihren Service Fabric-Anwendungen zu verwenden, aktivieren Sie zunächst den *Tokendienst für verwaltete Identitäten* auf dem Cluster. Dieser Dienst ist für die Authentifizierung von Service Fabric-Anwendungen anhand ihrer verwalteten Identitäten und für den Abruf von Zugriffstoken in deren Auftrag zuständig. Nachdem der Dienst aktiviert wurde, wird er im Service Fabric Explorer im Abschnitt **System** im linken Bereich unter dem Namen **fabric:/System/ManagedIdentityTokenService** angezeigt.

> [!NOTE]
> Zum Aktivieren des **Tokendiensts für verwaltete Identitäten** ist mindestens Version 6.5.658.9590 der Service Fabric-Runtime erforderlich.  
>
> Sie finden die Service Fabric-Version eines Clusters im Azure-Portal. Öffnen Sie dazu dort die Clusterressource, und überprüfen Sie die Eigenschaft **Service Fabric-Version** im Abschnitt **Zusammenfassung**.
>
> Wenn sich der Cluster im **manuellen** Upgrademodus befindet, müssen Sie ihn zunächst auf Version 6.5.658.9590 oder höher aktualisieren.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Aktivieren des *Tokendiensts für verwaltete Identitäten* in einem vorhandenen Cluster

Um den Tokendienst für verwaltete Identitäten in einem vorhandenen Cluster zu aktivieren, müssen Sie ein Clusterupgrade initiieren und dabei zwei Änderungen angeben: (1) Aktivieren Sie den Tokendienst für verwaltete Identitäten, und fordern Sie (2) einen Neustart jedes einzelnen Knotens an. Fügen Sie zunächst den folgenden Codeausschnitt Ihrer Azure Resource Manager-Clustervorlage hinzu:

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

Damit die Änderungen wirksam werden, müssen Sie auch die Upgraderichtlinie ändern, um auf jedem Knoten einen Neustart der Service Fabric-Runtime zu erzwingen, wenn das Upgrade den Cluster durchläuft. Mit diesem Neustart wird sichergestellt, dass der neu aktivierte Systemdienst auf jedem Knoten gestartet und ausgeführt wird. Im folgenden Codeausschnitt ist `forceRestart` die wesentliche Einstellung, um den Neustart zu aktivieren. Verwenden Sie für die restlichen Parameter die unten beschriebenen Werte, oder verwenden Sie vorhandene benutzerdefinierte Werte, die bereits für die Clusterressource angegeben wurden. Benutzerdefinierte Einstellungen für die Fabric-Upgraderichtlinie („upgradeDescription“) können im Azure-Portal angezeigt werden, indem Sie in der Service Fabric-Ressource oder in „resources.azure.com“ die Option „Fabric-Upgrades“ auswählen. Standardoptionen für die Upgraderichtlinie („upgradeDescription“) können nicht über PowerShell oder „resources.azure.com“ angezeigt werden. Weitere Informationen finden Sie unter [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy?view=azure-dotnet).  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> Vergessen Sie nach dem erfolgreichen Abschluss des Upgrades nicht, ein Rollback für die Einstellung `forceRestart` auszuführen, um die Auswirkungen nachfolgender Upgrades zu minimieren. 

## <a name="errors-and-troubleshooting"></a>Fehler und Troubleshooting

Wenn die Bereitstellung mit der folgenden Meldung zu einem Fehler führt, bedeutet dies, dass im Cluster eine zu niedrige Service Fabric-Version ausgeführt wird:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer systemseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Bereitstellen einer Azure Service Fabric-Anwendung mit einer benutzerseitig zugewiesenen verwalteten Identität](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Nutzen der verwalteten Identität einer Service Fabric-Anwendung aus dem Dienstcode](./how-to-managed-identity-service-fabric-app-code.md)
* [Gewähren des Zugriffs auf andere Azure-Ressourcen für eine Azure Service Fabric-Anwendung](./how-to-grant-access-other-resources.md)
