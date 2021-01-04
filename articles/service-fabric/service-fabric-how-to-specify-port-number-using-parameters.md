---
title: Angeben der Portnummer eines Diensts mit Parametern
description: Erfahren Sie, wie Sie Parameter verwenden, um den Port für eine Anwendung in Service Fabric anzugeben.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: ba2fb459dc9c981ad168aca4d0edf969650ccf48
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576705"
---
# <a name="how-to-specify-the-port-number-of-a-service-using-parameters-in-service-fabric"></a>Angeben der Portnummer eines Diensts mithilfe von Parametern in Service Fabric

In diesem Artikel erfahren Sie, wie Sie mit Visual Studio die Portnummer eines Diensts mithilfe von Parametern in Service Fabric angeben.

## <a name="procedure-for-specifying-the-port-number-of-a-service-using-parameters"></a>Verfahren für die Angabe der Portnummer eines Diensts mit Parametern

In diesem Beispiel legen Sie die Portnummer für Ihre ASP.NET Core-Web-API mithilfe eines Parameters fest.

1. Öffnen Sie Visual Studio, und erstellen Sie eine neue Service Fabric-Anwendung.
1. Wählen Sie die Vorlage „Zustandsloses ASP.NET Core“ aus.
1. Wählen Sie die Web-API aus.
1. Öffnen Sie die Datei „ServiceManifest.xml“.
1. Notieren Sie den Namen des Endpunkts, der für Ihren Dienst angegeben wird. Der Standardwert ist `ServiceEndpoint`.
1. Öffnen Sie die Datei „ApplicationManifest.xml“.
1. Fügen Sie im `ServiceManifestImport`-Element ein neues `RessourceOverrides`-Element mit einem Verweis auf den Endpunkt in der Datei „ServiceManifest.xml“ hinzu.

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Sie können nun im `Endpoint`-Element alle Attribute mit einem Parameter überschreiben. Geben Sie in diesem Beispiel `Port` an, und legen Sie als Wert einen Parameternamen mit eckigen Klammern fest – z.B.`[MyWebAPI_PortNumber]`

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="Web1Pkg" ServiceManifestVersion="1.0.0" />
        <ResourceOverrides>
          <Endpoints>
            <Endpoint Name="ServiceEndpoint" Port="[MyWebAPI_PortNumber]"/>
          </Endpoints>
        </ResourceOverrides>
        <ConfigOverrides />
      </ServiceManifestImport>
    ```

1. Geben Sie dann, ebenfalls in der Datei „ApplicationManifest.xml“, den Parameter im `Parameters`-Element an.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" />
      </Parameters>
    ```

1. Definieren Sie nun einen Standardwert (`DefaultValue`).

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" DefaultValue="8080" />
      </Parameters>
    ```

1. Öffnen Sie den Ordner „ApplicationParameters“ und die Datei `Cloud.xml`.
1. Um einen anderen Port anzugeben, der beim Veröffentlichen in einem Remotecluster verwendet werden soll, fügen Sie in dieser Datei den Parameter mit der Portnummer hinzu.

    ```xml
      <Parameters>
        <Parameter Name="MyWebAPI_PortNumber" Value="80" />
      </Parameters>
    ```

Wenn Sie Ihre Anwendung aus Visual Studio über das Veröffentlichungsprofil „Cloud.xml“ veröffentlichen, wird Ihr Dienst für die Verwendung von Port 80 konfiguriert. Wenn Sie die Anwendung bereitstellen, ohne den MyWebAPI_PortNumber-Parameter anzugeben, verwendet der Dienst den Port 8080.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu einigen der grundlegenden Konzepte, die in diesem Artikel behandelt werden, finden Sie im Artikel [Verwalten von Anwendungen für mehrere Umgebungen](service-fabric-manage-multiple-environment-app-configuration.md).

Informationen zu anderen App-Verwaltungsfunktionen in Visual Studio finden Sie unter [Verwalten Ihrer Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md).
