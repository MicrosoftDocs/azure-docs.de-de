---
title: Java-Client-APIs für Azure Service Fabric | Microsoft-Dokumentation
description: Generieren und verwenden Sie Java-Client-APIs für Service Fabric mithilfe der Spezifikation der Service Fabric-Client-REST-API.
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60720279"
---
# <a name="azure-service-fabric-java-client-apis"></a>Java-Client-APIs für Azure Service Fabric

Service Fabric-Client-APIs ermöglichen die Bereitstellung und Verwaltung von auf Microservices basierenden Anwendungen und Containern in einem Service Fabric-Cluster in Azure, lokal auf einem lokalen Entwicklungscomputer oder in einer anderen Cloud. In diesem Artikel wird beschrieben, wie Sie für Service Fabric-Java-Client-APIs basierend auf den Service Fabric-Client-REST-APIs generieren und verwenden.

## <a name="generate-the-client-code-using-autorest"></a>Generieren des Clientcodes mithilfe von AutoRest

[AutoRest](https://github.com/Azure/autorest) ist ein Tool, das Clientbibliotheken für den Zugriff auf RESTful-Webdienste generiert. Die Eingabe für AutoRest ist eine Spezifikation, die die REST-API anhand des OpenAPI-Spezifikationsformats beschreibt. [Service Fabric-Client-REST-APIs](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) folgen dieser Spezifikation.

Führen Sie die unten stehenden Schritte aus, um mithilfe des AutoRest-Tools Service Fabric-Java-Clientcode zu generieren.

1. Installieren von Node.js und npm auf dem Computer

    Wenn Sie Linux verwenden:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Wenn Sie Mac OS X verwenden:
    ```bash
    brew install node
    ```

2. Installieren Sie AutoRest über NPM.
    ```bash
    npm install -g autorest
    ```

3. Forken und klonen Sie das Repository [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) auf Ihrem lokalen Computer, und wechseln Sie vom Terminal Ihres Computers aus zum geklonten Speicherort.


4. Wechseln Sie in Ihrem geklonten Repository zu dem unten genannten Speicherort.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Wenn Sie eine andere Clusterversion als 6.0.* verwenden, wechseln Sie im Ordner „stable“ zum entsprechenden Verzeichnis.
    >   

5. Führen Sie den folgenden autorest-Befehl aus, um den Java-Clientcode zu generieren.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Im Folgenden sehen Sie ein Beispiel, das die Verwendung von „autorest“ veranschaulicht.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Der folgende Befehl akzeptiert die Spezifikationsdatei ``servicefabric.json`` als Eingabe, generiert Java-Clientcode im Ordner ``java-rest-api-     code`` und umschließt den Code im Namespace ``servicefabricrest``. Nach diesem Schritt sehen Sie, dass die beiden Ordner ``models`` und ``implementation`` sowie die beiden Dateien ``ServiceFabricClientAPIs.java`` und ``package-info.java`` im Ordner ``java-rest-api-code`` generiert wurden.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Einschließen und Verwenden des generierten Clients in Ihrem Projekt

1. Fügen Sie den generierten Code auf geeignete Weise in Ihr Projekt ein. Es empfiehlt sich, mithilfe des generierten Codes eine Clientbibliothek zu erstellen und diese in Ihr Projekt einzuschließen.
2. Wenn Sie eine Bibliothek erstellen, schließen Sie die folgende Abhängigkeit in das Projekt der Bibliothek ein. Sollten Sie einen anderen Ansatz verfolgen, schließen Sie die Abhängigkeit auf geeignete Weise ein.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Wenn Sie z.B. das Maven-Buildsystem verwenden, schließen Sie Folgendes in Ihre ``pom.xml``-Datei ein:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Erstellen Sie mithilfe des folgenden Codes einen RestClient:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Verwenden Sie das Clientobjekt, und führen Sie die entsprechenden Aufrufe aus. Hier finden Sie einige Beispiele, die die Verwendung des Clientobjekts veranschaulichen. Es wird angenommen, dass vor der Verwendung der folgenden APIs das Anwendungspaket erstellt und in den Imagespeicher hochgeladen wurde.
    * Bereitstellen einer Anwendung
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Erstellen einer Anwendung

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Grundlegendes zum generierten Code
Für jede API sehen Sie vier Überladungen der Implementierung. Wenn optionale Parameter vorhanden sind, finden Sie vier weitere Varianten, einschließlich dieser optionalen Parameter. Betrachten Sie z.B. die API ``removeReplica``.
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Dies ist die synchrone Variante des removeReplica-API-Aufrufs.
 2. **public ServiceFuture\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout, final ServiceCallback\<Void> serviceCallback)**
    * Diese Variante des API-Aufrufs kann verwendet werden, wenn Sie zukunftsbasierte asynchrone Programmierung und Rückrufe verwenden möchten.
 3. **public Observable\<Void> removeReplicaAsync(String nodeName, UUID partitionId, String replicaId)**
    * Diese Variante des API-Aufrufs kann verwendet werden, wenn Sie eine reaktive asynchrone Programmierung verwenden möchten.
 4. **public Observable\<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync(String nodeName, UUID partitionId, String replicaId)**
    * Diese Variante des API-Aufrufs kann verwendet werden, wenn Sie eine reaktive asynchrone Programmierung verwenden und RAW-REST-Antworten verarbeiten möchten.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zu [Service Fabric-REST-APIs](https://docs.microsoft.com/rest/api/servicefabric/)

