---
title: Einrichten einer Stagingumgebung in Azure Spring Cloud | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Blaugrün-Bereitstellung mit Azure Spring Cloud verwenden.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 82a8da9d2663b03d89ad0819ec6d918bebaf5f5e
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99574728"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Einrichten einer Stagingumgebung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

In diesem Artikel wird erläutert, wie Sie unter Verwendung des Blau-Grün-Bereitstellungsmusters in Azure Spring Cloud eine Stagingbereitstellung einrichten. Die Blau-Grün-Bereitstellung ist ein Azure DevOps-Muster für Continuous Delivery, bei dem eine vorhandene Version (blau) live bleibt, während eine neue Version (grün) bereitgestellt wird. In diesem Artikel wird außerdem gezeigt, wie Sie diese Stagingbereitstellung in die Produktion überführen, ohne die Produktionsbereitstellung zu ändern.

## <a name="prerequisites"></a>Voraussetzungen

* Azure Spring Cloud-Instanz im *Standard*-**Tarif**
* Azure-Befehlszeilenschnittstelle mit [Azure Spring Cloud-Erweiterung](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

In diesem Artikel wird eine Anwendung verwendet, die mit dem Spring Initializr erstellt wurde. Wenn Sie für dieses Beispiel eine andere Anwendung verwenden möchten, müssen Sie am öffentlichen Teil der Anwendung eine kleine Änderung vornehmen, um die Stagingbereitstellung von der Produktion zu unterscheiden.

>[!TIP]
> Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Anweisungen in diesem Artikel ausführen können.  Sie verfügt über allgemeine vorinstallierte Azure-Tools, u. a. die aktuellen Versionen von Git, JDK, Maven und der Azure-Befehlszeilenschnittstelle. Wenn Sie bei Ihrem Azure-Abonnement angemeldet sind, starten Sie die [Azure Cloud Shell](https://shell.azure.com).  Weitere Informationen finden Sie in der [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md).

Befolgen Sie die Anweisungen in den nächsten Abschnitten, um Blau-Grün-Bereitstellungen in Azure Spring Cloud einzurichten.

## <a name="install-the-azure-cli-extension"></a>Installieren der Erweiterung für die Azure-Befehlszeilenschnittstelle

Führen Sie den folgenden Befehl aus, um die Azure Spring Cloud-Erweiterung für die Azure CLI zu installieren:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Vorbereiten von App und Bereitstellungen
Führen Sie die folgenden Schritte aus, um diese Anwendung zu erstellen:
1. Generieren Sie den Code für die Beispiel-App mit dem Spring Initializr und [dieser Konfiguration](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Laden Sie den Code herunter.
3. Fügen Sie die folgende Quelldatei „HelloController.java“ im Ordner `\src\main\java\com\example\hellospring\` hinzu.
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. Erstellen Sie die JAR-Datei:
```azurecli
mvn clean packge -DskipTests
```
5. Erstellen Sie die App in Ihrer Azure Spring Cloud-Instanz:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
```
6. Stellen Sie die App in Azure Spring Cloud bereit:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. Ändern Sie den Code für die Stagingbereitstellung:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Erstellen Sie die JAR-Datei neu:
```azurecli
mvn clean packge -DskipTests
```
9. Erstellen Sie die Grün-Bereitstellung: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Anzeigen von Apps und Bereitstellungen

Bereitgestellte Apps können wie folgt angezeigt werden:

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Spring Cloud-Instanz.

1. Öffnen Sie über den Navigationsbereich auf der linken Seite das Blatt „Apps“, um Apps für Ihre Dienstinstanz anzuzeigen.

    [ ![Apps-dashboard](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Sie können auf eine App klicken und Details anzeigen.

    [ ![Apps-overview](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Öffnen Sie **Bereitstellungen**, um alle Bereitstellungen der App anzuzeigen. Das Raster zeigt die Produktions- und Stagingbereitstellungen.

    [ ![Dashboard mit Apps/Bereitstellungen](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Klicken Sie auf die URL, um die aktuell bereitgestellte Anwendung zu öffnen.
    ![URL der Bereitstellung](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Klicken Sie in der Spalte **State** (Status) auf **Production**, um die Standard-App anzuzeigen.
    ![Standard ausgeführt](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Klicken Sie in der Spalte **State** (Status) auf **Staging**, um die Staging-App anzuzeigen.
    ![Staging ausgeführt](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Vergewissern Sie sich, dass der Testendpunkt mit einem Schrägstrich (/) endet, um sicherzustellen, dass die CSS-Datei richtig geladen wird.  
> * Wenn Sie zum Anzeigen der Seite Anmeldeinformationen im Browser eingeben müssen, verwenden Sie [URL-Decodierung](https://www.urldecoder.org/), um den Testendpunkt zu decodieren. Nach der URL-Decodierung wird eine URL im Format „https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green“ zurückgegeben.  Verwenden Sie dieses Format für den Zugriff auf Ihren Endpunkt.

>[!NOTE]    
> Die Konfigurationsservereinstellungen gelten sowohl für die Stagingumgebung als auch für die Produktion. Beispiel: Wenn Sie als Kontextpfad (`server.servlet.context-path`) für App Gateway auf dem Konfigurationsserver *somepath* festgelegt haben, ändert sich der Pfad zu Ihrer grünen Bereitstellung in „https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...“.
 
 Wenn Sie jetzt die öffentliche App Gateway-Instanz aufrufen, wird die alte Seite ohne die Änderung angezeigt.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Festlegen der grünen Bereitstellung als Produktionsumgebung

1. Nachdem Sie die Änderung in Ihrer Stagingumgebung überprüft haben, können Sie sie in die Produktion überführen. Wählen Sie auf der Seite **Apps**/**Bereitstellungen** die Anwendung aus, die derzeit den Status `Production` aufweist.

1. Klicken Sie auf die Ellipse hinter dem **Registrierungsstatus** der Grün-Bereitstellung, und ändern Sie ihren Status mit der Option „Als Produktion festlegen“. 

   [ ![„Als Produktion festlegen“ für Stagingbereitstellung](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Die URL der App sollte Ihre Änderungen widerspiegeln.

   ![Staging jetzt in der Bereitstellung](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Sobald die grüne Bereitstellung als Produktionsumgebung festgelegt wurde, wird die vorherige Bereitstellung zur Stagingbereitstellung.

## <a name="modify-the-staging-deployment"></a>Ändern der Stagingbereitstellung

Sind Sie mit Ihrer Änderung nicht zufrieden, können Sie über die Azure CLI den Anwendungscode ändern, ein neues JAR-Paket erstellen und dieses in die grüne Bereitstellung hochladen.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Löschen der Stagingbereitstellung

Löschen Sie die Stagingbereitstellung aus dem Azure-Portal, indem Sie zur Seite der Stagingbereitstellung wechseln und dort die Schaltfläche **Löschen** auswählen.

Alternativ können Sie die Stagingbereitstellung über die Azure CLI löschen, indem Sie den folgenden Befehl ausführen:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Nächste Schritte

* [CI/CD für Azure Spring Cloud](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)
