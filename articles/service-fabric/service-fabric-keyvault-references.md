---
title: 'Azure Service Fabric: Verwenden der KeyVault-Verweise von Service Fabric-Anwendungen'
description: In diesem Artikel wird erläutert, wie Sie die KeyVaultReference-Unterstützung von Service Fabric für Anwendungsgeheimnisse verwenden.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898595"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>KeyVaultReference-Unterstützung für in Azure bereitgestellte Service Fabric-Anwendungen

Eine häufige Herausforderung bei der Erstellung von Cloudanwendungen ist die sichere Verteilung der Geheimnisse auf Ihre Anwendungen. Beispielsweise könnten Sie einen Datenbankschlüssel für Ihre Anwendung bereitstellen, ohne den Schlüssel während der Pipeline oder für den Operator verfügbar zu machen. Die KeyVaultReference-Unterstützung von Service Fabric vereinfacht das Bereitstellen von Geheimnissen für Ihre Anwendungen, indem auf die URL des Geheimnisses verwiesen wird, die in Key Vault gespeichert ist. Service Fabric behandelt das Abrufen des Geheimnisses für die verwaltete Identität Ihrer Anwendung und das Aktivieren der Anwendung mit dem Geheimnis.

> [!NOTE]
> Die KeyVaultReference-Unterstützung für Service Fabric-Anwendungen ist ab Service Fabric Version 7.2 CU5 allgemein verfügbar (nicht mehr in der Vorschau). Sie sollten ein Upgrade auf diese Version durchführen, bevor Sie dieses Feature verwenden.

> [!NOTE]
> Die KeyVaultReference-Unterstützung für Service Fabric-Anwendungen unterstützt nur Geheimnisse mit [Versionsangabe](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning). Versionslose Geheimnisse werden nicht unterstützt. Die Key Vault-Instanz muss sich in demselben Abonnement befinden wie Ihr Service Fabric-Cluster. 

## <a name="prerequisites"></a>Voraussetzungen

- Verwaltete Identität für Service Fabric-Anwendungen

    Die KeyVaultReference-Unterstützung für Service Fabric verwendet die verwaltete Identität einer Anwendung, um Geheimnisse für die Anwendung abzurufen, sodass Ihre Anwendung über eine verwaltete Identität bereitgestellt und zugewiesen werden muss. Befolgen Sie die Anweisungen in diesem [Dokument](concepts-managed-identity.md), um die verwaltete Identität für Ihre Anwendung zu aktivieren.

- Central Secrets Store (CSS)

    Central Secrets Store (CSS) ist der verschlüsselte lokale Cache für Geheimnisse von Service Fabric. Diese Funktion verwendet CSS, um Geheimnisse zu schützen und beizubehalten, nachdem sie aus Key Vault abgerufen wurden. Das Aktivieren dieses optionalen Systemdiensts ist auch erforderlich, um dieses Feature zu nutzen. Aktivieren und konfigurieren Sie CSS wie in diesem [Dokument](service-fabric-application-secret-store.md) beschrieben.

- Erteilen der Zugriffsberechtigung für verwaltete Identitäten der Anwendung auf den Schlüsseltresor

    Lesen Sie dieses [Dokument](how-to-grant-access-other-resources.md), um zu sehen, wie Sie dem Schlüsseltresor Zugriff auf verwaltete Identitäten erteilen können. Beachten Sie auch, dass bei Verwendung der systemseitig zugewiesenen verwalteten Identität die verwaltete Identität erst nach der Bereitstellung der Anwendung erstellt wird. Dadurch können Racebedingungen entstehen, unter denen die Anwendung versucht, auf das Geheimnis zuzugreifen, bevor der Identität Zugriff auf den Tresor erteilt werden kann. Der Name der systemseitig zugewiesenen Identität wird `{cluster name}/{application name}/{service name}` lauten.
    
## <a name="use-keyvaultreferences-in-your-application"></a>Verwenden von KeyVaultReferences in der Anwendung
KeyVaultReferences kann auf verschiedene Weise genutzt werden.
- [Als Umgebungsvariable](#as-an-environment-variable)
- [Als Datei in Ihren Container eingebunden](#mounted-as-a-file-into-your-container)
- [Als Verweis auf ein Containerrepository-Kennwort](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Als Umgebungsvariable

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Als Datei in Ihren Container eingebunden

- Hinzufügen eines Abschnitts zu „settings.xml“

    Definieren Sie den Parameter `MySecret` mit dem Typ `KeyVaultReference` und dem Wert `<KeyVaultURL>`.

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Verweisen Sie auf den neuen Abschnitt in „ApplicationManifest.xml“ in `<ConfigPackagePolicies>`.

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Nutzen von Geheimnissen vom Dienstcode aus

    Jeder unter `<Section  Name=MySecrets>` aufgeführte Parameter ist eine Datei unter dem Ordner, auf den „EnvironmentVariable SecretPath“ verweist. Der nachfolgende C#-Codeausschnitt zeigt, wie Sie MySecret aus Ihrer Anwendung lesen können.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint steuert den Ordner, in den die Dateien mit den geheimen Werten eingebunden werden.

### <a name="as-a-reference-to-a-container-repository-password"></a>Als Verweis auf ein Containerrepository-Kennwort

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Nächste Schritte

* [Azure KeyVault-Dokumentation](../key-vault/index.yml)
* [Weitere Informationen zum zentralen Geheimnisspeicher](service-fabric-application-secret-store.md)
* [Weitere Informationen zur verwalteten Identität für Service Fabric-Anwendungen](concepts-managed-identity.md)
