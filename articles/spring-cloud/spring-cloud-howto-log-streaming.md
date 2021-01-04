---
title: Streamen von Azure Spring Cloud-App-Protokollen in Echtzeit
description: Verwenden des Protokollstreamings zum sofortigen Anzeigen von Anwendungsprotokollen
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fcfddce568be6c641a5bf5be70c2cd0ad368095f
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843603"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Streamen von Azure Spring Cloud-App-Protokollen in Echtzeit

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Azure Spring Cloud ermöglicht das Protokollstreaming in der Azure-Befehlszeilenschnittstelle, um Anwendungskonsolenprotokolle in Echtzeit für die Problembehandlung zu erhalten. Sie können auch [Protokolle und Metriken mit Diagnoseeinstellungen analysieren](./diagnostic-services.md).

## <a name="prerequisites"></a>Voraussetzungen

* Installieren Sie die [Azure CLI-Erweiterung](/cli/azure/install-azure-cli) für Spring Cloud (mindestens Version 0.2.0).
* Eine Instanz von **Azure Spring Cloud** mit einer laufenden Anwendung, z. B. der [Spring Cloud-App](./spring-cloud-quickstart.md).

> [!NOTE]
>  Die ASC CLI-Erweiterung wird von Version 0.2.0 auf 0.2.1 aktualisiert. Diese Änderung wirkt sich auf die Syntax des Befehls für das Protokollstreaming aus: `az spring-cloud app log tail` wird ersetzt durch: `az spring-cloud app logs`. Der Befehl `az spring-cloud app log tail` wird in einer zukünftigen Version als veraltet markiert. Wenn Sie Version 0.2.0 verwendet haben, können Sie ein Upgrade auf 0.2.1 durchführen. Entfernen Sie zunächst mithilfe des Befehls `az extension remove -n spring-cloud` die alte Version.  Installieren Sie dann Version 0.2.1 mit dem folgenden Befehl: `az extension add -n spring-cloud`.

## <a name="use-cli-to-tail-logs"></a>Verwenden der Befehlszeilenschnittstelle zum Verfolgen von Protokollen

Legen Sie den Namen der Standardressourcengruppe und den Clusternamen fest, um die wiederholte Angabe von Ressourcengruppen- und Dienstinstanznamen zu vermeiden.
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
In den folgenden Beispielen werden die Ressourcengruppen- und Dienstnamen in den Befehlen ausgelassen.

### <a name="tail-log-for-app-with-single-instance"></a>Protokollfragment für eine App mit einer einzelnen Instanz
Wenn eine App mit dem Namen „auth-service“ nur über eine Instanz verfügt, können Sie das Protokoll der App-Instanz mit dem folgenden Befehl anzeigen:
```
az spring-cloud app logs -n auth-service
```
Damit werden die Protokolle zurückgegeben:
```
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Protokollfragment für eine App mit mehreren Instanzen
Wenn mehrere Instanzen für eine App mit dem Namen `auth-service` vorhanden sind, können Sie das Instanzprotokoll mithilfe der Option `-i/--instance` anzeigen. 

Sie können zunächst die Namen der App-Instanzen mit folgendem Befehl abrufen.

```
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Ergebnisse:

```
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Anschließend können Sie Protokolle einer App-Instanz mit der Option `-i/--instance` streamen:

```
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

Sie können auch Details zu App-Instanzen im Azure-Portal anzeigen.  Nachdem Sie im linken Navigationsbereich Ihres Azure Spring Cloud-Diensts **Apps** ausgewählt haben, wählen Sie **App-Instanzen** aus.

### <a name="continuously-stream-new-logs"></a>Fortlaufendes Streamen neuer Protokolle
Standardmäßig gibt `az spring-cloud ap log tail` nur vorhandene Protokolle aus, die an die App-Konsole gestreamt werden, und wird dann beendet. Wenn Sie neue Protokolle streamen möchten, fügen Sie „-f“ („--follow“) an:  

```
az spring-cloud app logs -n auth-service -f
``` 
So überprüfen Sie alle unterstützten Protokollierungsoptionen
``` 
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Nächste Schritte
* [Schnellstart: Überwachen von Azure Spring Cloud-Apps mit Protokollen, Metriken und Ablaufverfolgung](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](./diagnostic-services.md)

