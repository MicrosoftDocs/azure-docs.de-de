---
title: 'Azure Service Fabric: Konfigurieren der Anmeldeinformationen des Containerrepositorys'
description: Konfigurieren der Anmeldeinformationen des Repositorys zum Herunterladen von Images aus der Containerregistrierung
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574818"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Konfigurieren der Anmeldeinformationen des Repositorys für Ihre Anwendung zum Herunterladen von Containerimages

Konfigurieren Sie die Containerregistrierungsauthentifizierung, indem Sie `RepositoryCredentials` dem Abschnitt `ContainerHostPolicies` Ihres Anwendungsmanifests hinzufügen. Fügen Sie das Konto und Kennwort für die Containerregistrierung (*myregistry.azurecr.io* im Beispiel unten) hinzu, damit der Dienst das Containerimage aus dem Repository herunterladen kann.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Es wird empfohlen, das Repositorykennwort mithilfe eines Verschlüsselungszertifikats zu verschlüsseln, das auf allen Knoten des Clusters bereitgestellt wird. Bei der Bereitstellung des Dienstpakets für den Cluster durch Service Fabric wird das Verschlüsselungszertifikat zum Entschlüsseln des Verschlüsselungstexts verwendet. Das Cmdlet „Invoke-ServiceFabricEncryptText“ wird zum Verschlüsseln des Texts für das Kennwort verwendet, das der Datei „ApplicationManifest.xml“ hinzugefügt wird.
Weitere Informationen zu Zertifikaten und zur Verschlüsselungssemantik finden Sie unter [Verwalten von Geheimnissen](service-fabric-application-secret-management.md).

## <a name="configure-cluster-wide-credentials"></a>Konfigurieren von Anmeldeinformationen für den gesamten Cluster

Mit Service Fabric können Sie Anmeldeinformationen konfigurieren, die für den gesamten Cluster gelten und von Anwendungen als Standardanmeldeinformationen für Repositorys verwendet werden können.

Dieses Feature kann aktiviert oder deaktiviert werden, indem Sie in der Datei „ApplicationManifest.xml“ dem `ContainerHostPolicies`-Element das Attribut `UseDefaultRepositoryCredentials` mit dem Wert `true` oder `false` hinzufügen.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

In Service Fabric werden dann die Standardanmeldeinformationen für Repositorys verwendet, die im ClusterManifest im Abschnitt `Hosting` angegeben werden können.  Wenn `UseDefaultRepositoryCredentials` auf `true` festgelegt ist, liest Service Fabric die folgenden Werte aus dem ClusterManifest:

* DefaultContainerRepositoryAccountName (Zeichenfolge)
* DefaultContainerRepositoryPassword (Zeichenfolge)
* IsDefaultContainerRepositoryPasswordEncrypted (Boolescher Wert)
* DefaultContainerRepositoryPasswordType (string)

Hier ist ein Beispiel dafür angegeben, was in der Datei „ClusterManifestTemplate.json“ im Abschnitt `Hosting` hinzugefügt werden kann. Der `Hosting`-Abschnitt kann bei der Clustererstellung oder später in einem Konfigurationsupgrade hinzugefügt werden. Weitere Informationen finden Sie unter [Ändern von Azure Service Fabric-Clustereinstellungen](service-fabric-cluster-fabric-settings.md) und [Verwalten von Azure Service Fabric-Anwendungsgeheimnissen](service-fabric-application-secret-management.md).

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Verwenden von Token als Registrierungsanmeldeinformationen

Service Fabric unterstützt die Verwendung von Token als Anmeldeinformationen zum Herunterladen von Images für Ihre Container.  Diese Funktion nutzt die *verwaltete Identität* der zugrunde liegenden VM-Skalierungsgruppe, um sich bei der Registrierung zu authentifizieren, sodass keine Benutzeranmeldeinformationen mehr verwaltet werden müssen.  Weitere Informationen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).  Zur Verwendung dieser Funktion sind die folgenden Schritte erforderlich:

1. Stellen Sie sicher, dass *Systemseitig zugewiesene verwaltete Identität* für den virtuellen Computer aktiviert ist.

    ![Azure-Portal: Option „Identität für die VM-Skalierungsgruppe erstellen“](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> Überspringen Sie diesen Schritt bei benutzerseitig zugewiesenen verwalteten Identitäten. Die weiteren unten aufgeführten Schritte sind die gleichen, sofern die Skalierungsgruppe nur mit einer einzelnen benutzerseitig zugewiesenen verwalteten Identität verknüpft ist.

2. Erteilen Sie der VM-Skalierungsgruppe Berechtigungen zum Abrufen und Lesen von Images aus der Registrierung. Fügen Sie auf dem Blatt „Access Control (IAM)“ Ihrer Azure Container Registry im Azure-Portal eine *Rollenzuweisung* für den virtuellen Computer hinzu:

    ![Hinzufügen des VM-Prinzipals zu ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Ändern Sie nun das Anwendungsmanifest. Fügen Sie im Abschnitt `ContainerHostPolicies` das `‘UseTokenAuthenticationCredentials=”true”`-Attribut hinzu.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Wenn die Flags `UseDefaultRepositoryCredentials` und `UseTokenAuthenticationCredentials` beide auf „true“ festgelegt sind, tritt ein Fehler während der Bereitstellung auf.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über die [Authentifizierung der Containerregistrierung](../container-registry/container-registry-authentication.md).
