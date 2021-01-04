---
title: Herstellen von Verbindungen mit der Azure Media Services v3-API – Java
description: In diesem Artikel wird beschrieben, wie Sie mit Java eine Verbindung mit der Azure Media Services v3-API herstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 769b4bc431040ee4d872fa60270196db96978ed2
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94748181"
---
# <a name="connect-to-media-services-v3-api---java"></a>Verbinden mit der Media Services v3-API – Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel zeigt Ihnen, wie Sie mit der Methode der Dienstprinzipalanmeldung eine Verbindung zum Azure Media Services v3 Java SDK herstellen.

In diesem Artikel wird Visual Studio Code zum Entwickeln der Beispiel-App verwendet.

## <a name="prerequisites"></a>Voraussetzungen

- Folgen Sie den Anweisungen in [Writing Java with Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) (Schreiben von Java mit Visual Studio Code), um Folgendes zu installieren:

   - JDK
   - Apache Maven
   - Java-Erweiterungspaket
- Achten Sie darauf, die Umgebungsvariablen `JAVA_HOME` und `PATH` festzulegen.
- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich unbedingt den Namen der Ressourcengruppe und den Namen des Media Services-Kontos.
- Führen Sie die Schritte im Thema [Zugriff auf Azure Media Services API mit Azure CLI](./access-api-howto.md) aus. Halten Sie die Abonnement-ID, die Anwendungs-ID (Client-ID), den Authentifizierungsschlüssel und die Mandanten-ID fest. Sie benötigen diese Angaben in einem späteren Schritt.

Lesen Sie außerdem:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Java-Projektmanagement in VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Informieren Sie sich über die [Namenskonventionen](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Erstellen eines Maven-Projekts

Öffnen Sie ein Befehlszeilentool, und wechseln Sie per `cd` in ein Verzeichnis, in dem Sie das Projekt erstellen möchten.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Wenn Sie den Befehl ausführen, werden `pom.xml`, `App.java` und weitere Dateien erstellt. 

## <a name="add-dependencies"></a>Hinzufügen von Abhängigkeiten

1. Öffnen Sie in Visual Studio Code den Ordner, in dem sich Ihr Projekt befindet
1. Suchen und öffnen Sie `pom.xml`
1. Fügen Sie die erforderlichen Abhängigkeiten hinzu.

   Sehen Sie sich `pom.xml` im Beispiel zur [Videocodierung](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml) an.

## <a name="connect-to-the-java-client"></a>Verbinden mit dem Java-Client

1. Öffnen Sie die Datei `App.java` in `src\main\java\com\azure\ams`, und achten Sie darauf, dass sich die Includeanweisung für Ihr Paket ganz oben befindet:

    ```java
    package com.azure.ams;
    ```
1. Fügen Sie unterhalb der Paketanweisung folgende Importanweisungen hinzu:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Um die Active Directory-Anmeldeinformationen zu erstellen, die Sie zum Senden von Anforderungen benötigen, fügen Sie der main-Methode der App-Klasse folgenden Code hinzu, und legen Sie die Werte fest, die Sie aus [Access APIs](./access-api-howto.md) entnommen haben:
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Führen Sie die App aus.

## <a name="see-also"></a>Weitere Informationen

- [Media Services-Konzepte](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Java-Referenz](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Nächste Schritte

Sie können jetzt `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` einschließen und mit dem Ändern von Entitäten beginnen.

Weitere Codebeispiele finden Sie im Repository für [Java SDK-Beispiele](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/).
