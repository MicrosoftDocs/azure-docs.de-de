---
title: 'Tutorial: Verwenden des Trennschalterdashboards mit Azure Spring Cloud'
description: Erfahren Sie, wie Sie das Trennschalterdashboard mit Azure Spring Cloud verwenden.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 53884c2f6d9f2e8cbb5676e9ac10e8fb15ed919e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024278"
---
# <a name="use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Verwenden des Trennschalterdashboards mit Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

[Spring Cloud Netflix Turbine](https://github.com/Netflix/Turbine) wird häufig verwendet, um mehrere [Hystrix](https://github.com/Netflix/Hystrix)-Metrikdatenströme zu aggregieren, sodass die Datenströme mit dem Hystrix-Dashboard in einer einzelnen Ansicht überwacht werden können. In diesem Tutorial wird veranschaulicht, wie sie in Azure Spring Cloud verwendet werden.
> [!NOTE]
> Netflix Hystrix wird in vielen bestehenden Spring Cloud-Apps verwendet, befindet sich aber nicht mehr in aktiver Entwicklung. Wenn Sie ein neues Projekt entwickeln, verwenden Sie stattdessen Spring Cloud-Trennschalterimplementierungen wie [resilience4j](https://github.com/resilience4j/resilience4j). Anders als die in diesem Tutorial gezeigte Turbine vereinigt das neue Spring Cloud-Trennschalterframework alle Implementierungen seiner Metrikdatenpipeline in Micrometer. Wir arbeiten weiterhin an der Micrometer-Unterstützung in Azure Spring Cloud, daher wird sie in diesem Tutorial nicht behandelt.

## <a name="prepare-your-sample-applications"></a>Vorbereiten der Beispielanwendungen
Das Beispiel wird aus diesem [Repository](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine) geforkt.

Klonen Sie das Beispielrepository in Ihre Entwicklungsumgebung:
```
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Erstellen Sie die drei Anwendungen, die in diesem Tutorial verwendet werden sollen:
* user-service: Ein einfacher REST-Dienst mit einem einzelnen Endpunkt von „/personalized/{id}“
* recommendation-service: Ein einfacher REST-Dienst mit einem einzelnen Endpunkt von „/recommendations“, der von „user-service“ aufgerufen wird.
* hystrix-turbine: Ein Hystrix-Dashboarddienst zur Anzeige von Hystrix-Datenströmen und ein Turbine-Dienst, der Hystrix-Metrikdatenströme aus anderen Diensten zusammenfasst.
```
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```
## <a name="provision-your-azure-spring-cloud-instance"></a>Bereitstellen Ihrer Azure Spring Cloud-Instanz
Befolgen Sie das Verfahren: [Bereitstellen einer Dienstinstanz über die Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Bereitstellen von Anwendungen in Azure Spring Cloud
Diese Apps verwenden nicht den **Konfigurationsserver**, daher ist es nicht notwendig, den **Konfigurationsserver** für Azure Spring Cloud einzurichten.  Führen Sie die Erstellung und Bereitstellung wie folgt durch:
```azurecli
az spring-cloud app create -n user-service --is-public
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --is-public

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```
## <a name="verify-your-apps"></a>Überprüfen Sie Ihre Apps.
Nachdem alle Apps ausgeführt werden und erkennbar sind, greifen Sie mit Ihrem Browser über den Pfad „https://<username>-user-service.azuremicroservices.io/personalized/1“ auf `user-service` zu. Wenn der Benutzerdienst auf `recommendation-service` zugreifen kann, sollten Sie die folgende Ausgabe erhalten. Aktualisieren Sie die Webseite mehrmals, wenn sie nicht funktioniert.
```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```
## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Zugreifen auf Ihr Hystrix-Dashboard und den Metrikdatenstrom
Führen Sie die Überprüfung mit öffentlichen Endpunkten oder privaten Testendpunkten durch.

### <a name="using-public-endpoints"></a>Verwenden öffentlicher Endpunkte
Greifen Sie mit Ihrem Browser über den Pfad `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` auf „hystrix-turbine“ zu.  In der folgenden Abbildung ist das Hystrix-Dashboard in dieser App dargestellt.

![Hystrix-Dashboard](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Kopieren Sie die URL des Turbine-Datenstroms `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` in das Textfeld, und klicken Sie auf **Datenstrom überwachen**.  Dadurch wird das Dashboard angezeigt. Wenn im Viewer nichts angezeigt wird, erfassen Sie die `user-service`-Endpunkte, um Datenströme zu generieren.

![Hystrix-Datenstrom](media/spring-cloud-circuit-breaker/hystrix-stream.png) Jetzt können Sie mit dem Trennschalterdashboard experimentieren.
> [!NOTE] 
> In der Produktionsumgebung sollten das Hystrix-Dashboard und der Metrikdatenstrom nicht über das Internet zugänglich sein.

### <a name="using-private-test-endpoints"></a>Verwenden privater Testendpunkte
Hystrix-Metrikdatenströme sind auch von `test-endpoint` aus zugänglich. Als Back-End-Dienst haben wir keinen öffentlichen Endpunkt für `recommendation-service` zugewiesen, aber wir können seine Metriken mit einem Testendpunkt bei `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream` anzeigen.

![Datenstrom des Hystrix-Testendpunkts](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Als Web-App sollte das Hystrix-Dashboard mit `test-endpoint` arbeiten. Wenn es nicht ordnungsgemäß funktioniert, kann dies zwei Gründe haben: Die Verwendung von `test-endpoint` hat die Basis-URL von `/ to /<APP-NAME>/<DEPLOYMENT-NAME>` geändert oder die Web-App verwendet absolute Pfade für statische Ressourcen. Damit es mit `test-endpoint` funktioniert, müssen Sie möglicherweise die <base> in den Front-End-Dateien manuell bearbeiten.

## <a name="next-steps"></a>Nächste Schritte
* [Bereitstellen einer Dienstinstanz über die Azure CLI](./spring-cloud-quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](./spring-cloud-tutorial-prepare-app-deployment.md)

