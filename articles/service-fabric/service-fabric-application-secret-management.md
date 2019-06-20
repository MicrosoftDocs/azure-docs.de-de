---
title: Verwalten von Azure Service Fabric-Anwendungsgeheimnissen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie geheime Werte in einer Service Fabric-Anwendung (plattformunabhängig) schützen.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: d151dbf20e68a2152e9d886a74e51786bb8fbfa6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60614478"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Verwalten von verschlüsselten Geheimnissen in Service Fabric-Anwendungen
In diesem Leitfaden werden die Schritte zum Verwalten von Geheimnissen in einer Service Fabric-Anwendung beschrieben. Geheimnisse beinhalten jegliche Art von vertraulichen Informationen (z.B. Speicherverbindungszeichenfolgen, Kennwörter oder andere Werte, die nicht als Nur-Text verarbeitet werden sollen).

Die Verwendung von verschlüsselten Geheimnissen in einer Service Fabric-Anwendung umfasst drei Schritte:
* Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen
* Angeben von verschlüsselten Geheimnissen in einer Anwendung
* Entschlüsseln von verschlüsselten Geheimnissen im Dienstcode

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen
Das Einrichten eines Verschlüsselungszertifikats und das anschließende Verwenden zum Verschlüsseln von Geheimnissen unterscheidet sich unter Windows und Linux.
* [Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen in Windows-Clustern][secret-management-windows-specific-link]
* [Einrichten eines Verschlüsselungszertifikats und Verschlüsseln von Geheimnissen in Linux-Clustern][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Angeben von verschlüsselten Geheimnissen in einer Anwendung
Im vorherigen Schritt wird beschrieben, wie Sie ein Geheimnis mit einem Zertifikat verschlüsseln und eine Zeichenfolge mit Base64-Codierung erzeugen, die in einer Anwendung verwendet werden kann. Diese Base64-codierte Zeichenfolge kann als verschlüsselter [Parameter][parameters-link] in der Datei „Settings.xml“ eines Diensts oder als verschlüsselte [Umgebungsvariable][environment-variables-link] in der Datei „ServiceManifest.xml“ eines Diensts angegeben werden.

Geben Sie einen verschlüsselten [Parameter][parameters-link] in der Konfigurationsdatei „Settings.xml“ Ihres Diensts an, und legen Sie das Attribut `IsEncrypted` auf `true` fest:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Geben Sie eine verschlüsselte [Umgebungsvariable][environment-variables-link] in der Datei „ServiceManifest.xml“ Ihres Diensts an, und legen Sie das Attribut `Type` auf `Encrypted` fest:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

### <a name="inject-application-secrets-into-application-instances"></a>Einfügen von Geheimnissen aus Anwendungen in Anwendungsinstanzen
Die Bereitstellung in anderen Umgebungen sollte idealerweise so automatisiert wie möglich erfolgen. Zu diesem Zweck können Sie die Geheimnisse in einer Buildumgebung verschlüsseln und die verschlüsselten Geheimnisse beim Erstellen von Anwendungsinstanzen als Parameter angeben.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Verwenden von überschreibbaren Parametern in „Settings.xml“
Die Konfigurationsdatei „Settings.xml“ ermöglicht die Verwendung überschreibbarer Parameter, die bei der Anwendungserstellung angegeben werden können. Verwenden Sie das Attribut `MustOverride` , anstatt einen Wert für einen Parameter anzugeben:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Um Werte in „Settings.xml“ zu überschreiben, deklarieren Sie einen Außerkraftsetzungsparameter für den Dienst in „ApplicationManifest.xml“:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Der Wert kann nun beim Erstellen einer Instanz der Anwendung als *Anwendungsparameter* angegeben werden. Sie können mithilfe von PowerShell ein Skript zum Erstellen einer Anwendungsinstanz erstellen. Alternativ können Sie C# verwenden, um eine problemlose Integration in einen Erstellungsprozess zu ermöglichen.

Bei Verwendung von PowerShell wird der Parameter als [Hashtabelle](https://technet.microsoft.com/library/ee692803.aspx) an den Befehl `New-ServiceFabricApplication` übergeben:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Bei Verwendung von C# werden Anwendungsparameter in einer `ApplicationDescription` als `NameValueCollection` angegeben:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Entschlüsseln von verschlüsselten Geheimnissen im Dienstcode
Mit den APIs für den Zugriff auf [Parameter][parameters-link] und [Umgebungsvariablen][environment-variables-link] wird eine einfache Entschlüsselung von verschlüsselten Werten ermöglicht. Da die verschlüsselte Zeichenfolge Informationen zum Zertifikat enthält, das für die Verschlüsselung verwendet wurde, müssen Sie das Zertifikat nicht manuell angeben. Das Zertifikat muss lediglich auf dem Knoten installiert sein, auf dem der Dienst ausgeführt wird.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über [Anwendungs- und Dienstsicherheit](service-fabric-application-and-service-security.md).

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
