---
title: 'Azure Service Fabric Mesh: Maven-Referenz'
description: Enthält die Referenz für die Verwendung des Maven-Plug-Ins für Service Fabric Mesh.
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625582"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven-Plug-In für Service Fabric Mesh

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

## <a name="prerequisites"></a>Voraussetzungen

- Java-SDK
- Maven
- Azure CLI mit Mesh-Erweiterung
- Service Fabric-Befehlszeilenschnittstelle

## <a name="goals"></a>Ziele

### `azure-sfmesh:init`
- Erstellt einen Ordner `servicefabric`, der einen Ordner `appresources` enthält, in dem die Datei `application.yaml` gespeichert ist. 

### `azure-sfmesh:addservice`
- Erstellt einen Ordner im Ordner `servicefabric` mit dem Dienstnamen und die YAML-Datei des Diensts. 

### `azure-sfmesh:addnetwork`
- Generiert eine `network`-YAML-Datei mit dem angegebenen Netzwerknamen im Ordner `appresources`. 

### `azure-sfmesh:addgateway`
- Generiert eine `gateway`-YAML-Datei mit dem angegebenen Gatewaynamen im Ordner `appresources`. 

#### `azure-sfmesh:addvolume`
- Generiert eine `volume`-YAML-Datei mit dem angegebenen Volumenamen im Ordner `appresources`.

### `azure-sfmesh:addsecret`
- Generiert eine `secret`-YAML-Datei mit dem angegebenen Geheimnisnamen im Ordner `appresources`. 

### `azure-sfmesh:addsecretvalue`
- Generiert eine `secretvalue`-YAML-Datei mit dem angegebenen Geheimnis- und Geheimniswertnamen im Ordner `appresources`. 

### `azure-sfmesh:deploy`
- Mergt die YAML-Dateien aus dem Ordner `servicefabric` und erstellt JSON-Code für eine Azure Resource Manager-Vorlage im aktuellen Ordner.
- Stellt alle Ressourcen in der Azure Service Fabric Mesh-Umgebung bereit. 

### `azure-sfmesh:deploytocluster`
- Erstellt einen Ordner (`meshDeploy`), der den JSON-Bereitstellungscode enthält, der aus YAML-Dateien generiert wurde, die für für Service Fabric-Cluster gelten.
- Stellt alle Ressourcen im Service Fabric-Cluster bereit.
 

## <a name="usage"></a>Verwendung

Fügen Sie Ihrer Datei „pom.xml“ den folgenden Codeausschnitt hinzu, um das Maven-Plug-In in Ihrer Maven-Java-App zu nutzen:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Allgemeine Konfiguration

Das Maven-Plug-In unterstützt zurzeit keine allgemeinen Konfigurationen des Maven-Plug-Ins für Azure.

## <a name="how-to"></a>Vorgehensweise

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Initialisieren des Maven-Projekts für Azure Service Fabric Mesh
Führen Sie den folgenden Befehl aus, um die YAML-Anwendungsressourcendatei zu erstellen.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Erstellt einen Ordner namens `servicefabric->appresources` in Ihrem Stammordner, der eine YAML-Anwendungsdatei mit dem Namen `app_helloworldserver` enthält.

### <a name="add-resource-to-your-application"></a>Hinzufügen einer Ressource zu einer Anwendung

#### <a name="add-a-new-network-to-your-application"></a>Hinzufügen eines neuen Netzwerks zu Ihrer Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-Netzwerkressourcendatei zu erstellen. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Erstellt eine YAML-Netzwerkdatei im Ordner `servicefabric->appresources` mit dem Namen `network_helloworldservicenetwork`.

#### <a name="add-a-new-service-to-your-application"></a>Hinzufügen eines neuen Diensts zur Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-Dienstdatei zu erstellen. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Erstellt eine YAML-Dienstdatei im Ordner `servicefabric->helloworldservice` mit dem Namen `service_helloworldservice`, die auf `helloworldservicenetwork` und die `helloworldserver`-App verweist.
- Der Dienst lauscht an Port 8080.
- Der Dienst würde ***helloworldserver:latest*** als Containerimage verwenden.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Hinzufügen einer neuen Gatewayressource zur Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-Gatewayressourcendatei zu erstellen. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Erstellt eine neue YAML-Gatewaydatei im Ordner `servicefabric->appresources` mit dem Namen `gateway_helloworldgateway`.
- Verweist auf `helloworldservicelistener` als Dienstlistener, der auf Aufrufe von diesem Gateway lauscht. Verweist außerdem auf `helloworldservice` als Dienst, `helloworldservicenetwork` als Netzwerk und `helloworldserver` als Anwendung. 
- Lauscht auf Anforderungen an Port 80.

#### <a name="add-a-new-volume-to-your-application"></a>Hinzufügen eines neuen Volumes zur Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-Volumeressourcendatei zu erstellen. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Erstellt eine YAML-Volumedatei im Ordner `servicefabric->appresources` mit dem Namen `volume_vol1`.
- Legt Eigenschaften für erforderliche Parameter (`volumeAccountKey` und `volumeShareName`) wie oben beschrieben fest.
- Weitere Informationen dazu, wie Sie auf dieses erstellte Volume verweisen können, finden Sie unter [Bereitstellen der App mit dem Azure Files-Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

#### <a name="add-a-new-secret-resource-to-your-application"></a>Hinzufügen einer neuen Geheimnisressource zur Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-Geheimnisressourcendatei zu erstellen. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Erstellt eine YAML-Geheimnisdatei im Ordner `servicefabric->appresources` mit dem Namen `secret_secret1`.
- Weitere Informationen dazu, wie Sie auf dieses erstellte Geheimnis verweisen können, finden Sie unter [Verwalten von Geheimnissen](service-fabric-mesh-howto-manage-secrets.md).

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Hinzufügen einer neuen secretvalue-Ressource zur Anwendung
Führen Sie den folgenden Befehl aus, um eine YAML-secretvalue-Ressourcendatei zu erstellen. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Erstellt eine YAML-secretvalue-Datei im Ordner `servicefabric->appresources` mit dem Namen `secretvalue_secret1_v1`.

### <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

Mithilfe des Ziels `azure-sfmesh:deploytocluster` können Sie die Anwendung lokal mit dem folgenden Befehl ausführen:

```cmd
mvn azure-sfmesh:deploytocluster
```

Standardmäßig stellt dieses Ziel Ressourcen für einen lokalen Cluster bereit. Wenn Sie die Bereitstellung auf einem lokalen Cluster durchführen, wird davon ausgegangen, dass ein lokaler Service Fabric-Cluster installiert ist und ausgeführt wird. Ein lokaler Service Fabric-Cluster für Ressourcen wird zurzeit nur unter [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md) unterstützt.

- Erstellt JSON-Code aus YAML-Dateien, die für Service Fabric-Cluster gelten.
- Führt die Bereitstellung dann für den Clusterendpunkt aus.

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Bereitstellen einer Anwendung für Azure Service Fabric Mesh

Mithilfe des Ziels `azure-sfmesh:deploy` können Sie die Bereitstellung in der Service Fabric Mesh-Umgebung ausführen, indem Sie den folgenden Befehl ausführen:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Erstellt eine Ressourcengruppe namens `todoapprg`, wenn diese noch nicht vorhanden ist.
- Erstellt eine JSON-Azure Resource Manager-Vorlage durch Mergen der YAML-Dateien. 
- Stellt den JSON-Code in der Azure Service Fabric Mesh-Umgebung bereit.
