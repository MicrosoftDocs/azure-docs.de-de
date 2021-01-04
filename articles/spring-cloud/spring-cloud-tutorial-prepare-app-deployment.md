---
title: 'Vorgehensweise: Vorbereiten einer Anwendung für die Bereitstellung in Azure Spring Cloud'
description: Erfahren Sie, wie Sie eine Anwendung für die Bereitstellung in Azure Spring Cloud vorbereiten.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 5d160c46b235c6890426cab9de52ec7b827efe4a
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750712"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Vorbereiten einer Anwendung für die Bereitstellung in Azure Spring Cloud

::: zone pivot="programming-language-csharp"
Azure Spring Cloud bietet robuste Dienste zum Hosten, Überwachen, Skalieren und Aktualisieren einer Steeltoe-App. In diesem Artikel wird gezeigt, wie Sie eine vorhandene Steeltoe-Anwendung für die Bereitstellung in Azure Spring Cloud vorbereiten. 

In diesem Artikel werden die Abhängigkeiten, die Konfiguration und der Code erläutert, die zum Ausführen einer .NET Core-Steeltoe-App in Azure Spring Cloud erforderlich sind. Informationen zum Bereitstellen einer Anwendung in Azure Spring Cloud finden Sie unter [Bereitstellen Ihrer ersten Azure Spring Cloud-Anwendung](spring-cloud-quickstart.md).

>[!Note]
> Steeltoe-Unterstützung für Azure Spring Cloud wird zurzeit als öffentliche Vorschau angeboten. Angebote der Public Preview ermöglichen Kunden das Experimentieren mit neuen Funktionen vor der offiziellen Veröffentlichung.  Funktionen und Dienste in der Public Preview sind nicht zur Verwendung in der Produktion bestimmt.  Weitere Informationen zum Support während der Vorschauphase finden Sie in den [häufig gestellten Fragen](https://azure.microsoft.com/support/faq/). Sie können auch eine [Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md) einreichen.

##  <a name="supported-versions"></a>Unterstützte Versionen

Azure Spring Cloud unterstützt Folgendes:

* .NET Core 3.1
* Steeltoe 2.4 und 3.0

## <a name="dependencies"></a>Abhängigkeiten

Für Steeltoe 2.4 fügen Sie der Projektdatei das neueste Paket [Microsoft.Azure.SpringCloud.Client 1.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) hinzu:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Für Steeltoe 3.0 fügen Sie der Projektdatei das neueste Paket [Microsoft.Azure.SpringCloud.Client 2.x.x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) hinzu:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Aktualisieren der Datei "Program.cs"

Rufen Sie in der `Program.Main`-Methode die `UseAzureSpringCloudService`-Methode auf.

Rufen Sie für Steeltoe 2.4.4 `UseAzureSpringCloudService` nach `ConfigureWebHostDefaults` und nach `AddConfigServer` auf, sofern ein Aufruf erfolgt:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Rufen Sie für Steeltoe 3.0.0 `UseAzureSpringCloudService` vor `ConfigureWebHostDefaults` und vor sonstigem Steeltoe-Konfigurationscode auf:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Aktivieren der Eureka Server-Dienstermittlung

Legen Sie in der Konfigurationsquelle, die bei der Ausführung der App in Azure Spring Cloud verwendet wird, `spring.application.name` auf denselben Namen fest wie die Azure Spring Cloud-App, für die das Projekt bereitgestellt wird.

Wenn Sie z. B. ein .NET-Projekt mit dem Namen `EurekaDataProvider` in einer Azure Spring Cloud-App mit dem Namen `planet-weather-provider` bereitstellen, sollte die Datei *appSettings.json* den folgenden JSON-Code enthalten:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Verwenden der Dienstermittlung

Um einen Dienst mithilfe der Eureka Server-Dienstermittlung aufzurufen, nehmen Sie HTTP-Anforderungen für `http://<app_name>` vor, wobei `app_name` der Wert `spring.application.name` der Ziel-App ist. Mit dem folgenden Code wird beispielsweise der `planet-weather-provider`-Dienst aufgerufen:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
In diesem Thema wird gezeigt, wie Sie eine vorhandene Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud vorbereiten. Bei ordnungsgemäßer Konfiguration bietet Azure Spring Cloud stabile Dienste zur Überwachung, Skalierung und Aktualisierung Ihrer Java Spring Cloud-Anwendung.

Bevor Sie dieses Beispiel ausführen, können Sie den [grundlegenden Schnellstart](spring-cloud-quickstart.md) ausprobieren.

In anderen Beispielen wird erläutert, wie eine Anwendung in Azure Spring Cloud bereitgestellt wird, wenn die POM-Datei konfiguriert ist. 
* [Starten Ihrer ersten App](spring-cloud-quickstart.md)
* [Erstellen und Ausführen von Microservices](spring-cloud-quickstart-sample-app-introduction.md)

In diesem Artikel werden die erforderlichen Abhängigkeiten erläutert und wie diese zur POM-Datei hinzugefügt werden.

## <a name="java-runtime-version"></a>Java Runtime-Version

In Azure Spring Cloud können nur Spring-/Java-Anwendungen ausgeführt werden.

Azure Spring Cloud unterstützt sowohl Java 8 als auch Java 11. Die Hostingumgebung enthält die aktuelle Version von Azul Zulu OpenJDK für Azure. Weitere Informationen zu Azul Zulu OpenJDK für Azure finden Sie unter [Installieren des JDK für Azure und Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Spring Boot- and Spring Cloud-Versionen

Wenn Sie eine vorhandene Spring Boot-Anwendung für die Bereitstellung in Azure Spring Cloud vorbereiten möchten, nehmen Sie die Abhängigkeiten von Spring Boot und Spring Cloud in die POM-Datei der Anwendung auf, wie in den folgenden Abschnitten gezeigt wird.

Azure Spring Cloud unterstützt ausschließlich Spring Boot-Anwendungen der Version 2.1 oder Version 2.2. Die folgende Tabelle enthält die unterstützten Spring Boot- und Spring Cloud-Kombinationen:

Spring Boot-Version | Spring Cloud-Version
---|---
2.1 | Greenwich.RELEASE
2.2 | Hoxton.SR8
2.3 | Hoxton.SR8

> [!NOTE]
> Wir haben ein Problem mit Spring Boot 2.4 bei der TLS-Authentifizierung zwischen Ihren Apps und Eureka identifiziert und arbeiten zurzeit gemeinsam mit der Spring-Community an dessen Behebung. Eine Problemumgehung finden Sie in unseren [Häufig gestellten Fragen (FAQ)](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-faq?pivots=programming-language-java#development).

### <a name="dependencies-for-spring-boot-version-21"></a>Abhängigkeiten für die Spring Boot-Version 2.1

Für Spring Boot-Version 2.1 fügen Sie die folgenden Abhängigkeiten zur POM-Datei der Anwendung hinzu.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.12.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Greenwich.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-22"></a>Abhängigkeiten für die Spring Boot-Version 2.2

Für Spring Boot-Version 2.2 fügen Sie die folgenden Abhängigkeiten zur POM-Datei der Anwendung hinzu.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
### <a name="dependencies-for-spring-boot-version-23"></a>Abhängigkeiten für die Spring Boot-Version 2.3

Für Spring Boot-Version 2.3 fügen Sie der POM-Datei der Anwendung die folgenden Abhängigkeiten hinzu.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.0.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
## <a name="azure-spring-cloud-client-dependency"></a>Azure Spring Cloud-Clientabhängigkeit

Azure Spring Cloud hostet und verwaltet Spring Cloud-Komponenten. Die Komponenten beinhalten Spring Cloud-Dienstregistrierung und Spring Cloud-Konfigurationsserver. Es wird empfohlen, Spring Boot 2.2 oder 2.3 zu verwenden. Bei Spring Boot 2.1 müssen Sie die Azure Spring Cloud-Clientbibliothek in Ihre Abhängigkeiten einschließen, um die Kommunikation mit der Azure Spring Cloud-Dienstinstanz zu ermöglichen.

Die folgende Tabelle enthält die korrekten Azure Spring Cloud-Versionen für Ihre App mit Spring Boot und Spring Cloud:

Spring Boot-Version | Spring Cloud-Version | Azure Spring Cloud-Clientstarterversion
---|---|---
2.1.x | Greenwich.RELEASE | 2.1.2
2.2.x | Hoxton.SR8 | Nicht erforderlich
2.3.x | Hoxton.SR8 | Nicht erforderlich

Schließen Sie bei Verwendung von Spring Boot 2.1. die folgende Abhängigkeit in die Datei „pom.xml“ ein:

```xml
<dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.2</version>
</dependency>
```
> [!WARNING]
> Geben Sie `server.port` nicht in Ihrer Konfiguration an. In Azure Spring Cloud wird diese Einstellung auf eine festgelegte Portnummer überschrieben. Beachten Sie auch diese Einstellung, und geben Sie im Code keinen Serverport an.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Weitere empfohlene Abhängigkeiten zum Aktivieren von Azure Spring Cloud-Funktionen

Wenn Sie die integrierten Funktionen von Azure Spring Cloud (von Dienstregistrierung bis hin zu verteilter Ablaufverfolgung) aktivieren möchten, müssen Sie außerdem die folgenden Abhängigkeiten in Ihre Anwendung aufnehmen. Sie können einige dieser Abhängigkeiten löschen, wenn Sie keine entsprechenden Funktionen für die spezifischen Apps benötigen.

### <a name="service-registry"></a>Dienstregistrierung

Schließen Sie die Abhängigkeit `spring-cloud-starter-netflix-eureka-client` in die Datei „pom.xml“ ein, um den verwalteten Dienst für die Azure-Dienstregistrierung zu verwenden:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Der Endpunkt des Dienstregistrierungsservers wird automatisch in Form von Umgebungsvariablen mit Ihrer App eingefügt. Anwendungen können sich selbst beim Dienstregistrierungsserver registrieren und andere abhängige Microservices ermitteln.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient-Anmerkung

Fügen Sie dem Quellcode der Anwendung die folgende Anmerkung hinzu.
```java
@EnableDiscoveryClient
```
Informationen hierzu finden Sie beispielsweise in der piggymetrics-Anwendung aus früheren Beispielen:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Verteilte Konfiguration

Schließen Sie zum Aktivieren der verteilten Konfiguration die folgende Abhängigkeit vom Typ `spring-cloud-config-client` in den Abhängigkeitenabschnitt der Datei „pom.xml“ ein:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Geben Sie nicht `spring.cloud.config.enabled=false` in Ihrer Bootstrapkonfiguration an. Andernfalls kann die Anwendung nicht mehr mit dem Konfigurationsserver verwendet werden.

### <a name="metrics"></a>Metriken

Schließen Sie die Abhängigkeit `spring-boot-starter-actuator` in den Abhängigkeitenabschnitt der Datei „pom.xml“ ein:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metriken werden in regelmäßigen Abständen von den JMX-Endpunkten gepullt. Die Metriken können über das Azure-Portal visualisiert werden.

 > [!WARNING]
 > Geben Sie `spring.jmx.enabled=true` in der Konfigurationseigenschaft an. Andernfalls können Metriken nicht im Azure-Portal visualisiert werden.

### <a name="distributed-tracing"></a>Verteilte Ablaufverfolgung

Schließen Sie die folgenden Abhängigkeiten vom Typ `spring-cloud-starter-sleuth` und `spring-cloud-starter-zipkin` in den Abhängigkeitenabschnitt der Datei „pom.xml“ ein:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

 Darüber hinaus müssen Sie eine Azure Application Insights-Instanz für die Verwendung mit der Azure Spring Cloud-Dienstinstanz aktivieren. Informationen zur Verwendung von Application Insights mit Azure Spring Cloud finden Sie in der [Dokumentation zur verteilten Ablaufverfolgung](spring-cloud-tutorial-distributed-tracing.md).

## <a name="see-also"></a>Weitere Informationen
* [Analysieren von Anwendungsprotokollen und -metriken](./diagnostic-services.md)
* [Einrichten Ihres Konfigurationsservers](./spring-cloud-tutorial-config-server.md)
* [Verwenden der verteilten Ablaufverfolgung mit Azure Spring Cloud](./spring-cloud-tutorial-distributed-tracing.md)
* [Schnellstartanleitung zu Spring](https://spring.io/quickstart)
* [Spring Boot-Dokumentation](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Nächste Schritte

In diesem Thema haben Sie erfahren, wie Sie eine Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud konfigurieren. Informationen zum Einrichten einer Konfigurationsserverinstanz finden Sie unter [Einrichten einer Spring Cloud-Konfigurationsserverinstanz für Ihren Dienst](spring-cloud-tutorial-config-server.md).

Weitere Beispiele finden Sie auf GitHub: [Azure Spring Cloud-Beispiele](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end
