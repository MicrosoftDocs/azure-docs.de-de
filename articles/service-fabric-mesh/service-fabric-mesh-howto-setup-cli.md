---
title: Einrichten der Azure Service Fabric Mesh-CLI
description: Die Service Fabric Mesh-Befehlszeilenschnittstelle (Command Line Interface, CLI) ist zum Bereitstellen und Verwalten von Ressourcen lokal und in Azure Service Fabric Mesh erforderlich. Die Einrichtung erfolgt folgendermaßen.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: ea4a7764cf1ede1cfaf53b1097034c5894660376
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660677"
---
# <a name="set-up-service-fabric-mesh-cli"></a>Einrichten der Service Fabric Mesh-CLI
Die Service Fabric Mesh-Befehlszeilenschnittstelle (Command Line Interface, CLI) ist zum Bereitstellen und Verwalten von Ressourcen lokal und in Azure Service Fabric Mesh erforderlich. Die Einrichtung erfolgt folgendermaßen.

Es gibt drei Arten der CLI, die Sie verwenden können. Sie sind unten in der Tabelle zusammengefasst.

| CLI-Modul | Zielumgebung |  BESCHREIBUNG | 
|---|---|---|
| az mesh | Azure Service Fabric Mesh | Die primäre CLI, mit der Sie Ihre Anwendungen bereitstellen und Ressourcen für die Azure Service Fabric Mesh-Umgebung verwalten können. 
| sfctl | Lokale Cluster | Die Service Fabric-CLI, die die Bereitstellung und das Testen von Service Fabric-Ressourcen für lokale Cluster ermöglicht.  
| Maven-CLI | Lokale Cluster und Azure Service Fabric Mesh | Ein Wrapper für `az mesh` und `sfctl`, mit dem Java-Entwickler für die Entwicklung lokal und in Azure eine vertraute Befehlszeilenoberfläche nutzen können.  

Für die Vorschauversion wird die Azure Service Fabric Mesh CLI als eine Erweiterung der Azure CLI bereitgestellt. Sie können sie in der Azure Cloud Shell oder in einer lokalen Installation der Azure CLI installieren. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens die Version 2.0.67 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="install-the-azure-service-fabric-mesh-cli"></a>Installieren der Azure Service Fabric Mesh-CLI

Installieren Sie mithilfe des folgenden Befehls das Azure Service Fabric Mesh-CLI-Erweiterungsmodul (sofern noch nicht geschehen): 
 
```azurecli-interactive
az extension add --name mesh
```

Ist das Modul bereits installiert, verwenden Sie den folgenden Befehl, um Ihr bereits vorhandenes Azure Service Fabric Mesh-CLI-Modul zu aktualisieren:

```azurecli-interactive
az extension update --name mesh
```

## <a name="install-the-service-fabric-cli-sfctl"></a>Installieren der Service Fabric-CLI (sfctl) 

Befolgen Sie die Anleitung unter [Azure Service Fabric CLI](../service-fabric/service-fabric-cli.md). Das Modul **sfctl** kann für die Bereitstellung von Anwendungen basierend auf dem Ressourcenmodell für Service Fabric-Cluster auf Ihrem lokalen Computer verwendet werden. 

## <a name="install-the-maven-cli"></a>Installieren der Maven-CLI 

Auf Ihrem Computer muss Folgendes installiert sein, damit Sie die Maven-CLI verwenden können: 

* [Java](https://www.azul.com/downloads/zulu/)
* [Maven](https://maven.apache.org/download.cgi)
* [Git-Client](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* Azure Mesh-CLI (az mesh): Für Azure Service Fabric Mesh 
* SFCTL (sfctl): Für lokale Cluster 

Die Maven-CLI für Service Fabric befindet sich noch in der Vorschauphase. 

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
          <configuration>
            ...
          </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

Lesen Sie den Abschnitt mit der [Referenz zur Maven-CLI](service-fabric-mesh-reference-maven.md), um ausführliche Informationen zur Nutzung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

Sie können auch Ihre [Windows-Entwicklungsumgebung](service-fabric-mesh-howto-setup-developer-environment-sdk.md) einrichten.

[Hier](service-fabric-mesh-faq.md) finden Sie Antworten auf häufig gestellte Fragen sowie Informationen zu Problemen.

[azure-cli-install]: /cli/azure/install-azure-cli
