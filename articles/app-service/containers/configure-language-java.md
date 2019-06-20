---
title: Konfigurieren von Linux-Java-Apps – Azure App Service | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Java-Apps konfigurieren, die in Azure App Service unter Linux ausgeführt werden.
keywords: Azure App Service, Web-App, Linux, OSS, Java, Java EE, JEE, Javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: cf9356c2792781558c4403608ff5de0e3aaddb6a
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254464"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurieren einer Linux-Java-App für Azure App Service

Mit Azure App Service unter Linux können Java-Entwickler schnell ihre mit Tomcat oder Java Standard Edition (SE) paketierten Webanwendungen in einem vollständig verwalteten Linux-basierten Dienst erstellen, bereitstellen und skalieren. Stellen Sie Anwendungen mit Maven-Plug-Ins über die Befehlszeile oder in Editoren wie IntelliJ, Eclipse oder Visual Studio Code bereit.

Dieser Leitfaden enthält wichtige Konzepte und Anleitungen für Java-Entwickler, die in App Service einen integrierten Linux-Container verwenden. Falls Sie Azure App Service noch nicht verwendet haben, arbeiten Sie zunächst die [Java-Schnellstartanleitung](quickstart-java.md) und das [Tutorial „Java mit PostgreSQL“](tutorial-java-enterprise-postgresql-app.md) durch.

## <a name="deploying-your-app"></a>Bereitstellen Ihrer App

Sie können das [Maven-Plug-In für Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) zum Bereitstellen von JAR- und WAR-Dateien verwenden. Die Bereitstellung mit beliebten IDEs wird außerdem mit [Azure Toolkit für IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) oder [Azure Toolkit für Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse) unterstützt.

In allen anderen Fällen hängt Ihre Bereitstellungsmethode von Ihrem Archivtyp ab:

- Um WAR-Dateien in Tomcat bereitzustellen, verwenden Sie den `/api/wardeploy/`-Endpunkt, um Ihre Archivdatei mit POST zu veröffentlichen. Weitere Informationen zu dieser API finden Sie in [dieser Dokumentation](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Um JAR-Dateien in den Java SE-Images bereitzustellen, verwenden Sie den `/api/zipdeploy/`-Endpunkt der Kudu-Website. Weitere Informationen zu dieser API finden Sie in [dieser Dokumentation](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Stellen Sie Ihre WAR- oder JAR-Dateien nicht mithilfe von FTP bereit. Der FTP-Tool dient zum Hochladen von Startskripts, Abhängigkeiten oder anderen Laufzeitdateien. Es ist nicht die optimale Wahl für die Bereitstellung von Web-Apps.

## <a name="logging-and-debugging-apps"></a>Protokollieren und Debuggen von Apps

Leistungsberichte, Datenverkehrsvisualisierungen und Integritätsprüfungen für die einzelnen Apps stehen über das Azure-Portal zur Verfügung. Weitere Informationen hierzu finden Sie unter [Azure App Service-Pläne – Diagnoseübersicht](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-Konsolenzugriff

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Streamen von Diagnoseprotokollen

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Weitere Informationen finden Sie unter [Streaming mit der Azure-Befehlszeilenschnittstelle](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>App-Protokollierung

Aktivieren Sie die [Anwendungsprotokollierung](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) über das Azure-Portal oder die [Azure-Befehlszeilenschnittstelle](/cli/azure/webapp/log#az-webapp-log-config), um App Service so zu konfigurieren, dass die Streams mit der Standardkonsolenausgabe und den Standardkonsolenfehlern Ihrer Anwendung in das lokale Dateisystem oder Azure Blob Storage geschrieben werden. Die Protokollierung in der lokalen App Service-Dateisysteminstanz wird 12 Stunden nach der Konfiguration deaktiviert. Wenn Sie eine längere Beibehaltung benötigen, konfigurieren Sie die Anwendung für die Ausgabe in einen Blob Storage-Container. Ihre Java- und Tomcat-App-Protokolle befinden sich im Verzeichnis `/home/LogFiles/Application/`.

Wenn Ihre Anwendung [Logback](https://logback.qos.ch/) oder [Log4j](https://logging.apache.org/log4j) für die Ablaufverfolgung verwendet, können Sie diese Ablaufverfolgungen mithilfe der Konfigurationsanweisungen für das Protokollierungsframework unter [Untersuchen von Java-Ablaufverfolgungsprotokollen in Application Insights](/azure/application-insights/app-insights-java-trace-logs) zur Überprüfung an Azure Application Insights weiterleiten.

### <a name="troubleshooting-tools"></a>Problembehandlungstools

Die integrierten Java-Images basieren auf dem [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html)-Betriebssystem. Verwenden Sie den `apk`-Paket-Manager zur Installation von Tools oder Befehlen zur Problembehandlung.

## <a name="customization-and-tuning"></a>Anpassung und Optimierung

Azure App Service für Linux unterstützt eine sofort verfügbare Optimierung und Anpassung über das Azure-Portal und die CLI. Lesen Sie die folgenden Artikel zur nicht Java-spezifischen Web-App-Konfiguration:

- [Konfigurieren von App-Einstellungen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Einrichten einer benutzerdefinierten Domäne](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivieren von SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Hinzufügen eines CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurieren der Kudu-Website](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Festlegen von Java-Runtimeoptionen

Um reservierten Arbeitsspeicher oder andere JVM-Runtimeoptionen in den Tomcat- und Java SE-Umgebungen festzulegen, erstellen Sie mit den Optionen eine [App-Einstellung](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) mit dem Namen `JAVA_OPTS`. App Service für Linux übergibt diese Einstellung beim Start als Umgebungsvariable an die Java-Runtime.

Erstellen Sie im Azure-Portal unter **Anwendungseinstellungen** für die Web-App eine neue App-Einstellung namens `JAVA_OPTS`, welche die zusätzlichen Einstellungen enthält, z.B. `-Xms512m -Xmx1204m`.

Um die App-Einstellung über das Maven-Plug-In zu konfigurieren, fügen Sie Einstellungs-/Werttags im Azure-Plug-In-Abschnitt hinzu. Im folgenden Beispiel werden bestimmte Mindest- und Höchstwerte für die Java-Heapgröße festgelegt:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Entwickler, die eine einzige Anwendung mit einem Bereitstellungsslot in ihrem App Service-Plan ausführen, können die folgenden Optionen verwenden:

- B1- und S1-Instanzen: `-Xms1024m -Xmx1024m`
- B2- und S2-Instanzen: `-Xms3072m -Xmx3072m`
- B3- und S3-Instanzen: `-Xms6144m -Xmx6144m`

Überprüfen Sie beim Optimieren Ihrer Anwendungsheapeinstellungen Ihre App Service-Plandetails, und berücksichtigen Sie mehrere Anwendungs- und Bereitstellungsslotanforderungen, um die optimale Zuordnung von Arbeitsspeicher zu ermitteln.

Wenn Sie eine JAR-Anwendung bereitstellen, sollte sie den Namen `app.jar` tragen, damit das integrierte Image Ihre App ordnungsgemäß identifizieren kann. (Das Maven-Plug-In nimmt diese Umbenennung automatisch vor.) Wenn Sie Ihre JAR-Datei nicht in `app.jar` umbenennen möchten, können Sie ein Shellskript mit dem Befehl zum Ausführen Ihrer JAR-Datei hochladen. Fügen Sie anschließend den vollständigen Pfad zu diesem Skript in das Textfeld [Startdatei](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) im Abschnitt „Konfiguration“ des Portals ein.

### <a name="turn-on-web-sockets"></a>Aktivieren von Websockets

Aktivieren Sie die Unterstützung für Websockets im Azure-Portal in den **Anwendungseinstellungen** für die Anwendung. Sie müssen die Anwendung neu starten, damit die Einstellung in Kraft tritt.

Aktivieren Sie die Websocketunterstützung über die Azure CLI mithilfe des folgenden Befehls:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starten Sie Ihre Anwendung anschließend neu:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Festlegen der Standardzeichencodierung

Erstellen Sie im Azure-Portal unter **Anwendungseinstellungen** für die Web-App eine neue App-Einstellung namens `JAVA_OPTS` mit dem Wert `-Dfile.encoding=UTF-8`.

Alternativ können Sie die App-Einstellung mit dem App Service-Maven-Plug-In konfigurieren. Fügen Sie die name- und value-Tags der Einstellung in der Plug-In-Konfiguration hinzu:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Anpassen des Timeoutwerts für den Start

Wenn Ihre Java-Anwendung besonders groß ist, sollten Sie das Zeitlimit für den Start heraufsetzen. Erstellen Sie zu diesem Zweck eine Anwendungseinstellung `WEBSITES_CONTAINER_START_TIME_LIMIT`, und legen Sie sie auf die Anzahl Sekunden fest, die App Service vor dem Timeout warten soll. Der Maximalwert beträgt `1800` Sekunden.

## <a name="secure-applications"></a>Sichere Anwendungen

Für App Service für Linux ausgeführte Java-Anwendungen gelten dieselben [bewährten Sicherheitsmethoden](/azure/security/security-paas-applications-using-app-services) wie für andere Anwendungen. 

### <a name="authenticate-users"></a>Authentifizieren von Benutzern

Richten Sie die App-Authentifizierung im Azure-Portal über die Option **Authentifizierung und Autorisierung** ein. Von dort aus können Sie die Authentifizierung über Azure Active Directory oder soziale Netzwerke wie Facebook, Google oder GitHub aktivieren. Die Konfiguration des Azure-Portals funktioniert nur, wenn Sie einen einzelnen Authentifizierungsanbieter konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren Ihrer App Service-App zur Verwendung der Azure Active Directory-Anmeldung](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) und in den entsprechenden Artikeln für andere Identitätsanbieter. Wenn Sie mehrere Anmeldungsanbieter aktivieren müssen, befolgen Sie die Anweisungen im Artikel [Anpassen der Authentifizierung und Autorisierung in Azure App Service](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

#### <a name="tomcat"></a>Tomcat

Ihre Tomcat-Anwendung kann direkt aus dem Tomcat-Servlet auf die Ansprüche des Benutzers zugreifen, indem sie das Principal-Objekt in ein Map-Objekt umwandelt. Das Map-Objekt ordnet jeden Anspruchstyp einer Sammlung der Ansprüche für diesen Typ zu. Im folgenden Code ist `request` eine Instanz von `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nun können Sie das `Map`-Objekt auf bestimmte Ansprüche überprüfen. Der folgende Codeausschnitt durchläuft beispielsweise alle Anspruchstypen und gibt den Inhalt der einzelnen Sammlungen aus.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Um Benutzer abzumelden und andere Aktionen auszuführen, lesen Sie die Dokumentation unter [Nutzung von App Service-Authentifizierung und -Autorisierung](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Es gibt auch offizielle Dokumentation zur Tomcat [HttpServletRequest-Schnittstelle](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) und ihren Methoden. Die folgenden Servletmethoden werden auch basierend auf Ihrer App Service-Konfiguration aktualisiert:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Um dieses Feature zu deaktivieren, erstellen Sie eine Anwendungseinstellung mit dem Namen `WEBSITE_AUTH_SKIP_PRINCIPAL` und dem Wert `1`. Um alle Servletfilter zu deaktivieren, die von App Service hinzugefügt wurden, erstellen Sie eine Einstellung namens `WEBSITE_SKIP_FILTERS` mit dem Wert `1`.

#### <a name="spring-boot"></a>Spring Boot

Spring Boot-Entwickler können die [Azure Active Directory-Startoption für Spring Boot](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) verwenden, um Anwendungen mithilfe von vertrauten Spring Security-Anmerkungen und -APIs absichern. Achten Sie darauf, dass Sie die maximale Headergröße in Ihrer `application.properties`-Datei erhöhen. Wir empfehlen den Wert `16384`.

### <a name="configure-tlsssl"></a>Konfigurieren von TLS/SSL

Befolgen Sie die Anweisungen unter [Tutorial: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json), um ein vorhandenes SSL-Zertifikat hochzuladen und an den Domänennamen Ihrer Anwendung zu binden. Standardmäßig lässt Ihre Anwendung dennoch HTTP-Verbindungen zu. Befolgen Sie die spezifischen Schritte im Tutorial, um SSL und TLS zu erzwingen.

### <a name="use-keyvault-references"></a>Verwenden von KeyVault-Verweisen

[Azure KeyVault](../../key-vault/key-vault-overview.md) bietet eine zentrale Verwaltung von Geheimnissen mit Zugriffsrichtlinien und Überprüfungsverlauf. Sie können in KeyVault Geheimnisse (wie Kennwörter oder Verbindungszeichenfolgen) speichern und über Umgebungsvariablen auf diese Geheimnisse in Ihrer Anwendung zugreifen.

Befolgen Sie zunächst die Anweisungen zum [Gewähren des Zugriffs auf KeyVault für Ihre App](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) und zum [Erstellen eines KeyVault-Verweises auf Ihr Geheimnis in einer Anwendungseinstellung](../app-service-key-vault-references.md#reference-syntax). Sie können überprüfen, ob der Verweis auf das Geheimnis aufgelöst wird, indem Sie die Umgebungsvariable ausgeben, während Sie remote auf das App Service-Terminal zugreifen.

Um diese Geheimnisse in Ihre Spring- oder Tomcat-Konfigurationsdatei einzufügen, verwenden Sie die Syntax der Umgebungsvariablen (`${MY_ENV_VAR}`). Informationen zu Spring-Konfigurationsdateien finden Sie in dieser Dokumentation zu [externalisierten Konfigurationen](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Konfigurieren von APM-Plattformen

In diesem Abschnitt wird veranschaulicht, wie Sie Java-Anwendungen, die in Azure App Service für Linux bereitgestellt werden, mit den Plattformen NewRelic und AppDynamics für die Überwachung der Anwendungsleistung (Application Performance Monitoring, APM) verbinden.

[Konfigurieren von New Relic](#configure-new-relic)
[Konfigurieren von AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Konfigurieren von NewRelic

1. Erstellen Sie ein NewRelic-Konto unter [NewRelic.com](https://newrelic.com/signup).
2. Laden Sie den Java-Agent von NewRelic herunter. Der Dateiname hat etwa das Format `newrelic-java-x.x.x.zip`.
3. Kopieren Sie Ihren Lizenzschlüssel. Sie benötigen ihn später zum Konfigurieren des Agents.
4. [Stellen Sie eine SSH-Verbindung mit Ihrer App Service-Instanz her](app-service-linux-ssh-support.md), und erstellen Sie das neue Verzeichnis `/home/site/wwwroot/apm`.
5. Laden Sie die entpackten Dateien für den NewRelic-Java-Agent in ein Verzeichnis unter `/home/site/wwwroot/apm` herunter. Die Dateien für Ihren Agent sollten sich in `/home/site/wwwroot/apm/newrelic` befinden.
6. Ändern Sie die YAML-Datei unter `/home/site/wwwroot/apm/newrelic/newrelic.yml`, und ersetzen Sie den Platzhalter-Lizenzwert durch Ihren eigenen Lizenzschlüssel.
7. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.
    - Wenn für Ihre App **Java SE** verwendet wird, sollten Sie die Umgebungsvariable `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` erstellen.
    - Wenn **Tomcat** verwendet wird, sollten Sie die Umgebungsvariable `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` erstellen.
    - Wenn Sie **WildFly** verwenden, finden Sie in der Dokumentation zu New Relic [hier](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) eine Anleitung zur Installation des Java-Agents und der JBoss-Konfiguration.
    - Falls Sie bereits über eine Umgebungsvariable für `JAVA_OPTS` oder `CATALINA_OPTS` verfügen, können Sie die Option `javaagent` am Ende des aktuellen Werts anhängen.

### <a name="configure-appdynamics"></a>Konfigurieren von AppDynamics

1. Erstellen Sie unter [AppDynamics.com](https://www.appdynamics.com/community/register/) ein AppDynamics-Konto.
1. Laden Sie den Java-Agent von der AppDynamics-Website herunter. Der Dateiname hat etwa das Format `AppServerAgent-x.x.x.xxxxx.zip`.
1. [Stellen Sie eine SSH-Verbindung mit Ihrer App Service-Instanz her](app-service-linux-ssh-support.md), und erstellen Sie das neue Verzeichnis `/home/site/wwwroot/apm`.
1. Laden Sie die Dateien des Java-Agents in ein Verzeichnis unter `/home/site/wwwroot/apm` hoch. Die Dateien für Ihren Agent sollten sich in `/home/site/wwwroot/apm/appdynamics` befinden.
1. Navigieren Sie im Azure-Portal zu Ihrer Anwendung in App Service, und erstellen Sie eine neue Anwendungseinstellung.
    - Erstellen Sie bei Verwendung von **Java SE** die Umgebungsvariable `JAVA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.
    - Erstellen Sie bei Verwendung von **Tomcat** die Umgebungsvariable `CATALINA_OPTS` mit dem Wert `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`, wobei `<app-name>` für Ihren App Service-Namen steht.
    - Wenn Sie **WildFly** verwenden, finden Sie in der Dokumentation zu AppDynamics [hier](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) eine Anleitung zur Installation des Java-Agents und der JBoss-Konfiguration.
    
## <a name="configure-jar-applications"></a>Konfigurieren von JAR-Anwendungen

### <a name="starting-jar-apps"></a>Starten von JAR-Apps

Standardmäßig erwartet App Service, dass Ihre JAR-Anwendung den Namen `app.jar` hat. Wenn sie diesen Namen aufweist, wird sie automatisch ausgeführt. Für Maven-Benutzer können Sie den JAR-Namen festlegen, indem Sie in den Abschnitt `<build>` der Datei `pom.xml` die Zeichenfolge `<finalName>app</finalName>` einfügen. [Sie können dies auch in Gradle durchführen](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName), indem Sie die `archiveFileName`-Eigenschaft festlegen.

Falls Sie einen anderen Namen für Ihre JAR-Anwendung wählen, müssen Sie auch den [Startbefehl](app-service-linux-faq.md#built-in-images) angeben, mit dem Ihre JAR-Datei ausgeführt wird. Beispiel: `java -jar my-jar-app.jar`. Sie können den Wert für Ihren Startbefehl im Portal festlegen, indem Sie zu „Konfiguration“ > „Allgemeine Einstellungen“ navigieren oder die Anwendungseinstellung `STARTUP_COMMAND` verwenden.

### <a name="server-port"></a>Serverport

Da App Service für Linux eingehende Anforderungen an Port 80 leitet, sollte Ihre Anwendung auch an Port 80 lauschen. Sie können dies in der Konfiguration Ihrer Anwendung (z. B. Datei `application.properties` von Spring) oder über Ihren Startbefehl (z. B. `java -jar spring-app.jar --server.port=80`) durchführen. Die folgende Dokumentation enthält Informationen zu gängigen Java-Frameworks:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Datenquellen

### <a name="tomcat"></a>Tomcat

Diese Anweisungen gelten für alle Datenbankverbindungen. Für die Platzhalter müssen der Treiberklassenname und die JAR-Datei der gewählten Datenbank angegeben werden. In der folgenden Tabelle finden Sie Klassennamen und Treiberdownloads für gängige Datenbanken:

| Datenbank   | Treiberklassenname                             | JDBC-Treiber                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Download](https://dev.mysql.com/downloads/connector/j/) (Wählen Sie „Platform Independent“ (Plattformunabhängig) aus.) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Wenn Sie Tomcat für die Verwendung von Java Database Connectivity (JDBC) oder der Java-Persistenz-API (JPA) konfigurieren möchten, passen Sie zuerst die Umgebungsvariable `CATALINA_OPTS` an, die von Tomcat beim Start eingelesen wird. Diese Werte können mithilfe einer App-Einstellung im [App Service-Maven-Plug-In](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) festgelegt werden:

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Alternativ können Sie die Umgebungsvariablen im Azure-Portal auf der Seite **Konfiguration** > **Anwendungseinstellungen** festlegen.

Legen Sie als Nächstes fest, ob die Datenquelle nur für eine einzelne Anwendung oder für alle im Tomcat-Servlet ausgeführten Anwendungen verfügbar sein soll.

#### <a name="application-level-data-sources"></a>Datenquellen auf Anwendungsebene

1. Erstellen Sie im Verzeichnis `META-INF/` Ihres Projekts eine Datei vom Typ `context.xml`. Erstellen Sie das Verzeichnis `META-INF/`, falls es noch nicht vorhanden ist.

2. Fügen Sie in `context.xml` ein Element vom Typ `Context` hinzu, um die Datenquelle mit einer JNDI-Adresse zu verknüpfen. Ersetzen Sie den Platzhalter `driverClassName` durch den Klassennamen Ihres Treibers aus der obigen Tabelle.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. Aktualisieren Sie die Datei `web.xml` Ihrer Anwendung, sodass die Datenquelle in Ihrer Anwendung verwendet wird.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Gemeinsam verwendete Ressourcen auf Serverebene

1. Kopieren Sie den Inhalt von `/usr/local/tomcat/conf` über SSH in `/home/tomcat/conf` in Ihrer Linux-Instanz von App Service, wenn dort noch keine Konfiguration vorliegt.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Fügen Sie in `server.xml` innerhalb des Elements `<Server>` ein Kontextelement hinzu.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Aktualisieren Sie die Datei `web.xml` Ihrer Anwendung, sodass die Datenquelle in Ihrer Anwendung verwendet wird.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Abschließen der Konfiguration

Platzieren Sie abschließend die JAR-Dateien des Treibers im Tomcat-Klassenpfad, und starten Sie Ihre App Service-Instanz neu.

1. Stellen Sie sicher, dass die JDBC-Treiberdateien für den Tomcat-Klassenladeprogramm verfügbar sind, indem Sie sie im Verzeichnis `/home/tomcat/lib` ablegen. (Erstellen Sie dieses Verzeichnis, falls es noch nicht vorhanden ist.) Um diese Dateien in Ihre App Service-Instanz hochzuladen, gehen Sie folgendermaßen vor:
    1. Installieren Sie in der [Cloud Shell](https://shell.azure.com) die Web-App-Erweiterung:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Führen Sie den folgenden CLI-Befehl aus, um einen SSH-Tunnel von Ihrem lokalen System zu App Service zu erstellen:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Stellen Sie mit Ihrem SFTP-Client eine Verbindung mit dem lokalen Tunnelport her, und laden Sie die Dateien in den Ordner `/home/tomcat/lib` hoch.

    Alternativ können Sie zum Hochladen des JDBC-Treibers auch einen FTP-Client verwenden. Führen Sie dazu die [Schritte zum Abrufen Ihrer FTP-Anmeldeinformationen](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) aus.

2. Wenn Sie eine Datenquelle auf Serverebene erstellt haben, starten Sie die App Service-Linux-Anwendung neu. Tomcat setzt `CATALINA_HOME` auf `/home/tomcat/conf` zurück und verwendet die aktualisierte Konfiguration.

### <a name="spring-boot"></a>Spring Boot

Für das Verbinden mit Datenquellen in Spring Boot-Anwendungen wird empfohlen, Verbindungszeichenfolgen zu erstellen und in Ihre Datei `application.properties` einzufügen.

1. Legen Sie auf der App Service-Seite im Abschnitt „Konfiguration“ einen Namen für die Zeichenfolge fest, fügen Sie Ihre JDBC-Verbindungszeichenfolge in das Feld für den Wert ein, und legen Sie den Typ auf „Benutzerdefiniert“ fest. Sie können diese Verbindungszeichenfolge optional als Sloteinstellung festlegen.

    Diese Verbindungszeichenfolge ist für die Anwendung als die Umgebungsvariable `CUSTOMCONNSTR_<your-string-name>` verfügbar. Die Verbindungszeichenfolge, die Sie oben erstellt haben, erhält z. B. den Namen `CUSTOMCONNSTR_exampledb`.

2. Verweisen Sie in Ihrer Datei `application.properties` über den Namen der Umgebungsvariable auf diese Verbindungszeichenfolge. In unserem Beispiel würden wir Folgendes verwenden.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Weitere Informationen zu diesem Thema finden Sie in der Spring Boot-Dokumentation unter [Data Access](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) (Datenzugriff) und [Externalized Configuration](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) (Externalisierte Konfiguration).

## <a name="configure-java-ee-wildfly"></a>Konfigurieren von Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition für App Service für Linux befindet sich derzeit in der Vorschau. Dieser Stapel wird für die produktionsrelevante Aufgaben **nicht** empfohlen. Enthält Informationen zu unseren Java SE- und Tomcat-Stapeln.

Mit Azure App Service für Linux können Java-Entwickler Java Enterprise-Anwendungen (Java EE) in einem vollständig verwalteten Linux-basierten Dienst erstellen, bereitstellen und skalieren.  Der Java Enterprise Runtime-Umgebung liegt der Open-Source-Anwendungsserver [WildFly](https://wildfly.org/) zugrunde.

[Skalierung mit App Service](#scale-with-app-service)
[Anpassen der Anwendungsserverkonfiguration](#customize-application-server-configuration)
[Module und Abhängigkeiten](#modules-and-dependencies)
[Datenquellen](#data-sources)
[Aktivieren von Messaginganbietern](#enable-messaging-providers)
[Konfigurieren der Zwischenspeichern der Sitzungsverwaltung](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Skalierung mit App Service

Der in App Service unter Linux ausgeführte WildFly-Anwendungsserver wird im eigenständigen Modus und nicht in einer Domänenkonfiguration ausgeführt. Wenn Sie den App Service-Plan erweitern, wird jede WildFly-Instanz als eigenständiger Server konfiguriert.

 Sie können Ihre Anwendung mit [Skalierungsregeln](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) und durch [Erhöhen der Anzahl der Instanzen](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) vertikal oder horizontal skalieren.

### <a name="customize-application-server-configuration"></a>Anpassen der Anwendungsserverkonfiguration

Web-App-Instanzen sind zustandslos. Daher muss jede neue Instanz beim Start konfiguriert werden, um die von der Anwendung benötigte WildFly-Konfiguration zu unterstützen.
Sie können ein Bash-Startskript schreiben, um über die WildFly-Befehlszeilenschnittstelle folgende Aktionen auszuführen:

- Einrichten von Datenquellen
- Konfigurieren von Messaginganbietern
- Hinzufügen weiterer Module und Abhängigkeiten zur WildFly-Serverkonfiguration

 Das Skript wird ausgeführt, wenn WildFly betriebsbereit ist, jedoch vor dem Start der Anwendung. Im Skript sollte die von `/opt/jboss/wildfly/bin/jboss-cli.sh` aufgerufene [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) verwendet werden, um den Anwendungsserver mit allen Konfigurationen oder Änderungen zu konfigurieren, die nach dem Start des Servers erforderlich sind.

Konfigurieren Sie WildFly nicht mit dem interaktiven Modus der CLI. Stattdessen können Sie mit dem Befehl `--file` ein Skript von Befehlen für die JBoss CLI bereitstellen, z.B.:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Laden Sie das Startskript in `/home/site/deployments/tools` in Ihrer App Service-Instanz hoch. Anweisungen zum Abrufen der FTP-Anmeldeinformationen finden Sie in [diesem Dokument](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope).

Legen Sie das Feld **Startskript** im Azure-Portal auf den Speicherort Ihres Startshellskripts fest, z.B. `/home/site/deployments/tools/your-startup-script.sh`.

Geben Sie [App-Einstellungen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) in der Anwendungskonfiguration an, um Umgebungsvariablen für die Verwendung im Skript zu übergeben. Anwendungseinstellungen enthalten Verbindungszeichenfolgen und andere Geheimnisse, die zum Konfigurieren Ihrer Anwendung außerhalb der Versionskontrolle erforderlich sind.

### <a name="modules-and-dependencies"></a>Module und Abhängigkeiten

Um Module und die zugehörigen Abhängigkeiten über die JBoss CLI im WildFly-Klassenpfad zu installieren, müssen Sie die folgenden Dateien in Ihrem Verzeichnis erstellen. Für einige Module und Abhängigkeiten sind möglicherweise zusätzliche Konfigurationsschritte erforderlich, z.B. JNDI-Benennung oder andere API-spezifische Konfigurationen. In der folgenden Liste sind daher die Komponenten aufgeführt, die Sie in den meisten Fällen zum Konfigurieren einer Abhängigkeit mindestens benötigen.

- Ein [XML-Moduldeskriptor](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Diese XML-Datei definiert den Namen, die Attribute und die Abhängigkeiten Ihres Moduls. Diese [module.xml-Beispieldatei](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definiert ein Postgres-Modul, die zugehörige JAR-Datei für die JDBC-Abhängigkeit und andere erforderliche Modulabhängigkeiten.
- Alle erforderlichen JAR-Dateiabhängigkeiten für Ihr Modul.
- Ein Skript mit den JBoss CLI-Befehlen zum Konfigurieren des neuen Moduls. Diese Datei enthält die über die JBoss CLI auszuführenden Befehle, um den Server zur Verwendung der Abhängigkeit zu konfigurieren. Informationen zu den Befehlen zum Hinzufügen von Modulen, Datenquellen und Messaginganbietern finden Sie in [diesem Dokument](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Ein Bash-Startskript zum Aufrufen der JBoss CLI und zum Ausführen des Skripts im vorherigen Schritt. Diese Datei wird ausgeführt, wenn Ihre App Service-Instanz neu gestartet wird oder wenn neue Instanzen bei einer horizontalen Skalierung bereitgestellt werden. In diesem Startskript können Sie andere Konfigurationen für Ihre Anwendung ausführen, während die JBoss-Befehle an die JBoss CLI übergeben werden. Diese Datei kann mindestens ein einzelner Befehl zum Übergeben des JBoss CLI-Befehlsskripts an die JBoss CLI sein:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

Nachdem Sie die Dateien und Inhalte für Ihr Modul definiert haben, führen Sie die folgenden Schritte aus, um das Modul dem WildFly-Anwendungsserver hinzuzufügen.

1. Übertragen Sie Ihre Dateien per FTP nach `/home/site/deployments/tools` in Ihrer App Service-Instanz. Anweisungen zum Abrufen der FTP-Anmeldeinformationen finden Sie in diesem Dokument.
2. Legen Sie im Azure-Portal auf der Seite **Konfiguration** > **Allgemeine Einstellungen** das Feld „Startskript“ auf den Speicherort Ihres Startshellskripts fest, z. B. `/home/site/deployments/tools/your-startup-script.sh`.
3. Starten Sie Ihre App Service-Instanz neu, indem Sie im Abschnitt **Übersicht** des Portals auf die Schaltfläche **Neu starten** klicken oder die Azure-Befehlszeilenschnittstelle verwenden.

### <a name="configure-data-source-connections"></a>Konfigurieren von Datenquellenverbindungen

Um WildFly für eine Datenquellenverbindung zu konfigurieren, führen Sie die gleichen Schritte aus, die oben im Abschnitt „Module und Abhängigkeiten“ beschrieben sind. Sie können die gleichen Schritte für alle Azure-Datenbankdienste ausführen.

1. Laden Sie den JDBC-Treiber für Ihren Datenbanktyp herunter. Der Einfachheit halber finden Sie hier die Treiber für [Postgres](https://jdbc.postgresql.org/download.html) und [MySQL](https://dev.mysql.com/downloads/connector/j/). Entpacken Sie den Download, um die JAR-Datei zu erhalten.
2. Führen Sie die unter „Module und Abhängigkeiten“ beschriebenen Schritte aus, um den XML-Moduldeskriptor, das JBoss CLI-Skript, das Startskript und die JDBC-JAR-Abhängigkeit zu erstellen und hochzuladen.

Weitere Informationen zum Konfigurieren von WildFly mit [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource) und [SQL-Datenbank](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) stehen zur Verfügung. Anhand dieser angepassten Anweisungen zusammen mit den generalisierten Anweisungen oben können Sie auf dem Server Definitionen von Datenquellen hinzufügen.

### <a name="enable-messaging-providers"></a>Aktivieren von Messaginganbietern

So aktivieren Sie nachrichtengesteuerte Beans mit Service Bus als Messagingmechanismus

1. Verwenden Sie die [Apache QPId JMS-Messagingbibliothek](https://qpid.apache.org/proton/index.html). Fügen Sie diese Abhängigkeit in der Datei „pom.xml“ (oder einer anderen Builddatei) für die Anwendung ein.

2. Erstellen Sie [Service Bus-Ressourcen](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Erstellen Sie einen Azure Service Bus-Namespace und eine Warteschlange in diesem Namespace und eine SAS-Richtlinie mit Funktionen zum Senden und Empfangen.

3. Übergeben Sie den Schlüssel für die SAS-Richtlinie an den Code, entweder durch URL-Codierung des Primärschlüssels der Richtlinie oder durch [Verwenden des Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Führen Sie die im Abschnitt „Module und Abhängigkeiten“ beschriebenen Schritte mit dem XML-Moduldeskriptor, den JAR-Abhängigkeiten, den JBoss CLI-Befehlen und dem Startskript für den JMS-Anbieter aus. Zusätzlich zu diesen vier Dateien müssen Sie auch eine XML-Datei erstellen, die den JNDI-Namen für die JMS-Warteschlange und das JMS-Thema definiert. Referenzkonfigurationsdateien finden Sie in [diesem Repository](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig).

### <a name="configure-session-management-caching"></a>Konfigurieren der Zwischenspeicherung der Sitzungsverwaltung

Standardmäßig werden in App Service unter Linux Sitzungsaffinitätscookies verwendet, um sicherzustellen, dass Clientanforderungen mit vorhandenen Sitzungen an die gleiche Instanz der Anwendung weitergeleitet werden. Dieses Standardverhalten erfordert keine Konfiguration, es gelten jedoch einige Einschränkungen:

- Wenn eine Anwendungsinstanz neu gestartet oder zentral herunterskaliert wird, geht der Zustand der Benutzersitzung im Anwendungsserver verloren.
- Wenn für Anwendungen Einstellungen für lange Sitzungstimeouts oder eine festgelegte Anzahl von Benutzern festgelegt sind, kann es einige Zeit dauern, bis automatisch skalierte neue Instanzen Lasten empfangen, da nur neue Sitzungen an die neu gestarteten Instanzen weitergeleitet werden.

Sie können WildFly zur Verwendung eines externen Sitzungsspeichers konfigurieren, z. B. [Aure Cache für Redis](/azure/azure-cache-for-redis/). Sie müssen die Konfiguration [der vorhandenen ARR-Instanzaffinität deaktivieren](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/), um die cookiebasierte Weiterleitung der Sitzung zu deaktivieren und zu ermöglichen, dass der konfigurierte WildFly-Sitzungsspeicher ohne Beeinträchtigung ausgeführt wird.

## <a name="docker-containers"></a>Docker-Container

Wenn Sie das von Azure unterstützte Zulu JDK in Ihren Containern verwenden möchten, rufen Sie die auf der [Downloadseite für Azul Zulu Enterprise for Azure](https://www.azul.com/downloads/azure-only/zulu/) aufgeführten vorgefertigten Images per Pull ab, oder nutzen Sie die `Dockerfile`-Beispiele aus dem [Microsoft Java-GitHub-Repository](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Unterstützungserklärung

### <a name="runtime-availability"></a>Runtimeverfügbarkeit

App Service für Linux unterstützt zwei Runtimes zum verwalteten Hosten von Java-Webanwendungen:

- [Tomcat-Servlet-Container](https://tomcat.apache.org/) zum Ausführen von Anwendungen, die als Webarchivdateien (WAR) paketiert wurden. Unterstützt werden die Versionen 8.5 und 9.0.
- Java SE Runtime Environment zum Ausführen von Anwendungen, die als Java-Archivdateien (JAR) paketiert wurden. Unterstützt werden die Versionen Java 8 und 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-Versionen und -Wartung

Als Java Development Kit (JDK) wird das von [Azul Systems](https://www.azul.com/) bereitgestellte [Zulu](https://www.azul.com/downloads/azure-only/zulu/) von Azure unterstützt.

Größere Versionsupdates werden durch neue Runtimeoptionen in Azure App Service für Linux bereitgestellt. Kunden führen das Update auf diese neueren Versionen von Java durch die Konfiguration ihrer App Service-Bereitstellung durch und müssen durch Tests sicherstellen, dass das größere Update ihren Anforderungen entspricht.

Unterstützte JDKs werden jedes Vierteljahr im Januar, April, Juli und Oktober automatisch gepatcht.

### <a name="security-updates"></a>Sicherheitsupdates

Patches und Fixes für größere Sicherheitsrisiken werden veröffentlicht, sobald sie von Azul Systems zur Verfügung gestellt werden. Ein „größeres“ Sicherheitsrisiko wird durch eine Gesamtbewertung von 9.0 oder höher im [NIST Common Vulnerability Scoring System, Version 2](https://nvd.nist.gov/cvss.cfm), definiert.

### <a name="deprecation-and-retirement"></a>Einstellung und Außerbetriebnahme

Wenn eine unterstützte Java-Runtime eingestellt wird, erhalten Azure-Entwickler, die die betreffende Runtime verwenden, mindestens sechs Monate vor Außerbetriebnahme der Runtime eine entsprechende Benachrichtigung.

### <a name="local-development"></a>Lokale Entwicklung

Entwickler können die Production Edition des Azul Zulu Enterprise JDK zur lokalen Entwicklung von der [Azul-Downloadsite](https://www.azul.com/downloads/azure-only/zulu/) herunterladen.

### <a name="development-support"></a>Entwicklungsunterstützung

Produktsupport für das [von Azure unterstützte Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) ist bei der Entwicklung für Azure oder [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) mit einem [qualifizierten Azure-Supportplan](https://azure.microsoft.com/support/plans/) erhältlich.

### <a name="runtime-support"></a>Laufzeitunterstützung

Entwickler können ein [Problem](/azure/azure-supportability/how-to-create-azure-support-request) mit den Azul Zulu JDKs über den Azure-Support melden, wenn sie einen [qualifizierten Supportplan](https://azure.microsoft.com/support/plans/) besitzen.

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie das Center [Azure für Java-Entwickler](/java/azure/), um Azure-Schnellstarts, Tutorials und Java-Referenzdokumentation zu finden.

Allgemeine Fragen zur Verwendung von App Service für Linux, die sich nicht speziell auf die Java-Entwicklung beziehen, werden unter [Häufig gestellte Fragen (FAQ) zu Azure App Service unter Linux](app-service-linux-faq.md) beantwortet.

