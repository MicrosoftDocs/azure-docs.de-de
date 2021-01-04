---
title: Leitfaden zur Problembehandlung für Azure Spring Cloud | Microsoft-Dokumentation
description: Leitfaden zur Problembehandlung für Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 09415c47432f71310b10c86390c10e55f1ccc4b2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498607"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Behandlung von allgemeinen Problemen mit Azure Spring Cloud

Dieser Artikel enthält Anweisungen zur Behandlung von Problemen bei der Entwicklung von Azure Spring Cloud. Weitere Informationen hierzu finden Sie unter [Häufig gestellte Fragen zu Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Verfügbarkeits-, Leistungs- und Anwendungsprobleme

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Meine Anwendung kann nicht gestartet werden (z. B. kann für den Endpunkt keine Verbindung hergestellt werden, oder nach einigen Wiederholungsversuchen wird 502 zurückgegeben)

Exportieren Sie die Protokolle nach Azure Log Analytics. Die Tabelle für Spring-Anwendungsprotokolle heißt *AppPlatformLogsforSpring*. Erfahren Sie mehr unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md).

In Ihren Protokollen wird möglicherweise die folgende Fehlermeldung angezeigt:

> „org.springframework.context.ApplicationContextException: Unable to start web server.“ (Der Webserver konnte nicht gestartet werden.)

Die Meldung weist auf eines der beiden wahrscheinlichen Probleme hin: 
* Eins der Beans oder eine seiner Abhängigkeiten fehlt.
* Eine der Bean-Eigenschaften fehlt oder ist ungültig. In diesem Fall wird wahrscheinlich „java.lang.IllegalArgumentException“ angezeigt.

Dienstbindungen können auch dazu führen, dass beim Starten von Anwendungen Fehler auftreten. Verwenden Sie Schlüsselwörter, die im Zusammenhang mit den gebundenen Diensten stehen, um die Protokolle abzufragen. Angenommen, Ihre Anwendung verfügt beispielsweise über eine Bindung an eine MySQL-Instanz, für die die lokale Systemzeit festgelegt ist. Wenn die Anwendung nicht gestartet werden kann, wird im Protokoll ggf. die folgende Fehlermeldung angezeigt:

> „java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.“ (Der Zeitzonenwert des Servers „Koordinierte Weltzeit“ wird nicht erkannt oder repräsentiert mehr als eine Zeitzone.)

Navigieren Sie zum Beheben dieses Fehlers zum `server parameters`-Element Ihrer MySQL-Instanz, und ändern Sie den Wert `time_zone` von *SYSTEM* zu *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Meine Anwendung stürzt ab oder löst einen unerwarteten Fehler aus

Falls beim Debuggen die Anwendung abstürzt, sollten Sie zunächst den Ausführungsstatus und den Ermittlungsstatus der Anwendung überprüfen. Gehen Sie folgendermaßen vor: Navigieren Sie im Azure-Portal zu _App-Verwaltung_, um sicherzustellen, dass die Status für alle Anwendungen _Wird ausgeführt_ und _UP_ (AKTIV) angegeben ist.

* Falls der Status _Wird ausgeführt_ lautet, aber als Ermittlungsstatus nicht _UP_ (AKTIV) angezeigt wird, navigieren Sie zum Abschnitt [„Meine Anwendung kann nicht registriert werden“](#my-application-cant-be-registered).

* Wenn der Ermittlungsstatus _UP_ (AKTIV) lautet, können Sie zu „Metriken“ navigieren, um die Integrität der Anwendung zu überprüfen. Untersuchen Sie die folgenden Metriken:


  - `TomcatErrorCount` (_tomcat.global.error_): Alle Spring-Anwendungsausnahmen werden hier gezählt. Wenn dieser Wert hoch ist, wechseln Sie zu Azure Log Analytics, um Ihre Anwendungsprotokolle zu überprüfen.

  - `AppMemoryMax` (_jvm.memory.max_): Für die Anwendung steht die maximale Menge an Arbeitsspeicher zur Verfügung. Die Menge kann undefiniert sein oder kann sich mit der Zeit ändern, wenn diese definiert ist. Wenn diese definiert ist, ist die Menge des verwendeten und zugesicherten Speichers immer kleiner als die maximale Menge oder gleich der maximalen Menge. Eine Speicherzuteilung kann jedoch mit einer `OutOfMemoryError`-Meldung fehlschlagen, wenn die Zuteilung versucht, den verwendeten Speicher so zu erhöhen, dass *verwendet > zugesichert* ist, auch wenn *verwendet <= max* immer noch wahr ist. Versuchen Sie in einer solchen Situation, die maximale Heapgröße mithilfe des Parameters `-Xmx` zu erhöhen.

  - `AppMemoryUsed` (_jvm.memory.used_): Die derzeit verwendete Arbeitsspeichermenge in Byte, die von der Anwendung verwendet wird. Bei einer normalen Load-Java-Anwendung ergibt diese Metrikreihe ein *Sägezahnmuster*, bei dem die Speicherauslastung in kleinen Schritten beständig zunimmt und abnimmt und plötzlich massiv abfällt. Dieses Muster wiederholt sich dann. Diese Metrikreihe erscheint aufgrund der Garbage Collection innerhalb eines virtuellen Java-Computers, bei der Sammlungsaktionen bei den „Sägezahnmustern“ einen Abfall darstellen.
    
    Diese Metrik ist wichtig, um Speicherprobleme zu identifizieren, wie z. B.:
    * eine Arbeitsspeicherexplosion direkt am Anfang
    * die Surge-Arbeitsspeicherzuordnung für einen bestimmten Logikpfad
    * allmähliche Speicherverluste
  Weitere Informationen finden Sie unter [Metriken](spring-cloud-concept-metrics.md).
  
* Wenn die Anwendung nicht gestartet wird, stellen Sie sicher, dass die Anwendung über gültige JVM-Parameter verfügt. Wenn der JVM-Arbeitsspeicher zu hoch festgelegt wird, wird in Ihren Protokollen möglicherweise die folgende Fehlermeldung angezeigt:

  >„required memory 2728741K is greater than 2000M available for allocation“ (erforderlicher Arbeitsspeicher 2728741K ist größer als 2000M für die Zuordnung verfügbar)



Weitere Informationen zu Azure Log Analytics finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Meine Anwendung hat eine hohe CPU- oder Speicherauslastung.

Wenn Ihre Anwendung über eine hohe CPU- oder Arbeitsspeicherauslastung verfügt, trifft einer der beiden folgenden Fälle zu:
* Alle App-Instanzen haben eine hohe CPU- oder Arbeitsspeicherauslastung.
* Einige der App-Instanzen haben eine hohe CPU- oder Arbeitsspeicherauslastung.

Gehen Sie wie folgt vor, um festzustellen, welche Situation zutrifft:

1. Navigieren Sie zu **Metriken**, und wählen Sie dann entweder **Service CPU Usage Percentage** (CPU-Auslastung des Diensts in Prozent) oder **Service Memory Used** (Arbeitsspeicherauslastung des Diensts) aus.
2. Fügen Sie einen **App=** -Filter hinzu, um anzugeben, welche Anwendung Sie überwachen möchten.
3. Teilen Sie die Metriken nach **Instanz** auf.

Falls *alle Instanzen* eine hohe CPU- oder Arbeitsspeicherauslastung aufweisen, müssen Sie entweder die Anwendung aufskalieren oder die CPU- oder Arbeitsspeicherleistung hochskalieren. Weitere Informationen finden Sie im [Tutorial: Skalieren einer Anwendung in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Wenn nur *einige Instanzen* eine hohe CPU- oder Arbeitsspeicherauslastung aufweisen, sollten Sie den Instanzstatus und den zugehörigen Ermittlungsstatus überprüfen.

Weitere Informationen finden Sie unter [Metriken für Azure Spring Cloud](spring-cloud-concept-metrics.md).

Wenn alle Instanzen aktiv sind und ausgeführt werden, wechseln Sie zu Azure Log Analytics, um Ihre Anwendungsprotokolle abzufragen und Ihre Codelogik zu überprüfen. Hiermit können Sie feststellen, ob diese sich ggf. auf die Skalierungspartitionierung auswirken. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md).

Weitere Informationen zu Azure Log Analytics finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md). Fragen Sie die Protokolle mit der [Kusto-Abfragesprache](/azure/kusto/query/) ab.

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Prüfliste für die Bereitstellung Ihrer Spring-Anwendung in Azure Spring Cloud

Bevor Sie ein Onboarding Ihrer Anwendung durchführen, vergewissern Sie sich, dass sie die folgenden Kriterien erfüllt:

* Die Anwendung kann lokal mit der angegebenen Java-Laufzeitversion ausgeführt werden.
* Die Umgebungskonfiguration (CPU/RAM/Instanzen) erfüllt die vom Anwendungsanbieter festgelegte Mindestanforderung.
* Die Konfigurationselemente haben ihre erwarteten Werte. Weitere Informationen finden Sie in [diesem Artikel zum Konfigurationsserver](spring-cloud-tutorial-config-server.md).
* Die Umgebungsvariablen verfügen über ihre erwarteten Werte.
* Die JVM-Parameter verfügen über ihre erwarteten Werte.
* Es wird empfohlen, den eingebetteten _Konfigurationsserver_ und die _Spring-Dienstregistrierung_ zu deaktivieren oder aus dem Anwendungspaket zu entfernen.
* Wenn Azure-Ressourcen mittels _Dienstbindung_ gebunden werden sollen, stellen Sie sicher, dass die Zielressourcen in Betrieb sind und ausgeführt werden.

## <a name="configuration-and-management"></a>Konfiguration und Verwaltung

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Bei mir ist ein Problem beim Erstellen einer Azure Spring Cloud-Dienstinstanz aufgetreten

Wenn Sie über das Azure-Portal eine Instanz eines Azure Spring Cloud-Diensts bereitstellen, wird die Überprüfung von Azure Spring Cloud für Sie durchgeführt.

Wenn Sie jedoch versuchen, die Dienstinstanz von Azure Spring Cloud mithilfe der Vorlage [Azure CLI](/cli/azure/get-started-with-azure-cli) oder [Azure Resource Manager](../azure-resource-manager/index.yml) einzurichten, überprüfen Sie, dass:

* Das Abonnement ist aktiv.
* der Standort von Azure Spring Cloud [unterstützt](spring-cloud-faq.md) wird.
* Die Ressourcengruppe für die Instanz wurde bereits erstellt.
* Der Ressourcenname entspricht der Benennungsregel. die Instanz nur Kleinbuchstaben, Zahlen und Bindestriche enthält. Das erste Zeichen muss ein Buchstabe sein. Das letzte Zeichen muss ein Buchstabe oder eine Zahl sein. Der Wert muss zwischen 2 und 32 Zeichen lang sein.

Wenn Sie die Azure Spring Cloud-Dienstinstanz mithilfe der Vorlage „Resource Manager“ einrichten möchten, lesen Sie zunächst den Artikel [Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/template-syntax.md).

Der Name der Azure Spring Cloud-Dienstinstanz wird verwendet, um den Namen der Unterdomäne unter `azureapps.io` anzufordern. Die Bereitstellung ist nicht erfolgreich, wenn für den Namen ein Konflikt mit einem bereits vorhandenen Namen besteht. Sie werden möglicherweise weitere Informationen in den Aktivitätsprotokollen finden.

### <a name="i-cant-deploy-a-net-core-app"></a>Ich kann keine .NET Core-App bereitstellen

Sie können keine *ZIP*-Datei für eine .NET Core-Steeltoe-App über das Azure-Portal oder die Resource Manager-Vorlage hochladen.

Wenn Sie Ihr Anwendungspaket über die [Azure CLI](/cli/azure/get-started-with-azure-cli) bereitstellen, wird in regelmäßigen Abständen der Bereitstellungsfortschritt durch die Azure CLI abgerufen und am Ende das Bereitstellungsergebnis angezeigt.

Stellen Sie sicher, dass Ihre Anwendung im richtigen *ZIP*-Dateiformat gepackt ist. Wenn die Anwendung nicht ordnungsgemäß gepackt ist, reagiert der Prozess nicht mehr, oder Sie erhalten eine Fehlermeldung.

### <a name="i-cant-deploy-a-jar-package"></a>Ich kann ein JAR-Paket nicht bereitstellen

Sie können keine JAR-Datei (Java Archive) oder kein Quellpaket über das Azure-Portal oder die Resource Manager-Vorlage hochladen.

Wenn Sie Ihr Anwendungspaket über die [Azure CLI](/cli/azure/get-started-with-azure-cli) bereitstellen, wird in regelmäßigen Abständen der Bereitstellungsfortschritt durch die Azure CLI abgerufen und am Ende das Bereitstellungsergebnis angezeigt.

Sollte der Abruf unterbrochen werden, können Sie mithilfe des folgenden Befehls die Bereitstellungsprotokolle abrufen:

`az spring-cloud app show-deploy-log -n <app-name>`

Stellen Sie sicher, dass Ihre Anwendung im richtigen [ausführbaren JAR-Format](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html) gepackt ist. Wenn die Anwendung nicht korrekt gepackt ist, erhalten Sie eine Fehlermeldung, die wie folgt aussehen kann:

> „Fehler: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714“ (ungültige oder beschädigte JAR-Datei /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714)

### <a name="i-cant-deploy-a-source-package"></a>Ich kann ein Quellpaket nicht bereitstellen

Sie können keine JAR-Datei oder kein Quellpaket über das Azure-Portal oder die Resource Manager-Vorlage hochladen.

Wenn Sie Ihr Anwendungspaket über die [Azure CLI](/cli/azure/get-started-with-azure-cli) bereitstellen, wird in regelmäßigen Abständen der Bereitstellungsfortschritt durch die Azure CLI abgerufen und am Ende das Bereitstellungsergebnis angezeigt.

Sollte der Abruf unterbrochen werden, können Sie mithilfe des folgenden Befehls die Build- und Bereitstellungsprotokolle abrufen:

`az spring-cloud app show-deploy-log -n <app-name>`

Beachten Sie aber, dass die Azure Spring Cloud-Dienstinstanz nur jeweils einen Buildauftrag pro Quellpaket auslösen kann. Weitere Informationen finden Sie unter [Schnellstart: Starten einer Azure Spring Cloud-Anwendung über das Azure-Portal](spring-cloud-quickstart.md) und unter [Einrichten einer Stagingumgebung](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Meine Anwendung kann nicht registriert werden

In den meisten Fällen tritt diese Situation auf, wenn *Erforderliche Abhängigkeiten* und *Suchdienst* in Ihrer POM-Datei (Project Object Model) nicht ordnungsgemäß konfiguriert sind. Nach der Konfiguration wird der integrierte Serverendpunkt der Dienstregistrierung als Umgebungsvariable für Ihre Anwendung eingefügt. Die Anwendungen führen dann die Registrierung für den Dienstregistrierungsserver durch und können andere abhängige Microservices ermitteln.

Die Wartezeit, bis eine neu registrierte Instanz mit dem Empfang von Datenverkehr beginnen kann, beträgt mindestens zwei Minuten.

Stellen Sie beim Migrieren einer vorhandenen Spring Cloud-basierten Lösung zu Azure sicher, dass Ihre Ad-hoc-Instanzen der _Dienstregistrierung_ und des _Konfigurationsservers_ entfernt (oder deaktiviert) werden. So können Sie einen Konflikt mit den von Azure Spring Cloud bereitgestellten verwalteten Instanzen vermeiden.

Sie können die Clientprotokolle der _Dienstregistrierung_ auch in Azure Log Analytics überprüfen. Weitere Informationen finden Sie unter [Analysieren von Protokollen und Metriken mit Diagnoseeinstellungen](diagnostic-services.md).

Weitere Informationen zu Azure Log Analytics finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md). Fragen Sie die Protokolle mit der [Kusto-Abfragesprache](/azure/kusto/query/) ab.

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Ich möchte die Umgebungsvariablen meiner Anwendung überprüfen.

Umgebungsvariablen informieren das Azure Spring Cloud-Framework und stellen sicher, dass Azure versteht, wo und wie die Dienste konfiguriert werden, aus denen Ihre Anwendung besteht. Bei der Behebung potenzieller Probleme muss zunächst sichergestellt werden, dass Ihre Umgebungsvariablen richtig sind.  Sie können die Umgebungsvariablen mithilfe des Endpunkts für den Spring Boot-Aktor überprüfen.  

> [!WARNING]
> Mit dieser Prozedur werden die Umgebungsvariablen unter Verwendung Ihres Testendpunkts verfügbar gemacht.  Setzen Sie den Vorgang nicht fort, wenn der Testendpunkt öffentlich zugänglich ist oder Sie Ihrer Anwendung einen Domänennamen zugewiesen haben.

1. Gehe zu `https://<your application test endpoint>/actuator/health`.  
    - Eine Antwort ähnlich wie `{"status":"UP"}` gibt an, dass der Endpunkt aktiviert wurde.
    - Ist die Antwort negativ, nehmen Sie die folgende Abhängigkeit in die Datei *POM.xml* auf:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Wenn Sie den Endpunkt für den Spring Boot-Aktor aktiviert haben, navigieren Sie im Azure-Portal zur Konfigurationsseite Ihrer Anwendung.  Fügen Sie eine Umgebungsvariable mit dem Namen `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` und dem Wert `*` hinzu. 

1. Starten Sie Ihre Anwendung neu.

1. Navigieren Sie zu `https://<your application test endpoint>/actuator/env`, und überprüfen Sie die Antwort.  Diese sollte wie folgt aussehen:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Suchen Sie den untergeordneten Knoten mit dem Namen `systemEnvironment`.  Dieser Knoten enthält die Umgebungsvariablen Ihrer Anwendung.

> [!IMPORTANT]
> Denken Sie daran, das Verfügbarmachen Ihrer Umgebungsvariablen rückgängig zu machen, bevor Sie die Anwendung öffentlich zugänglich machen.  Wechseln Sie zum Azure-Portal, navigieren Sie zur Konfigurationsseite Ihrer Anwendung, und löschen Sie die folgende Umgebungsvariable: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Ich kann keine Metriken oder Protokolle für meine Anwendung finden

Navigieren Sie zu **App-Verwaltung**, um sicherzustellen, dass für die Anwendung die Status _Wird ausgeführt_ und _UP_ (AKTIV) festgelegt ist.

Überprüfen Sie, ob _JMX_ in Ihrem Anwendungspaket aktiviert ist. Diese Funktion kann mit der-Konfigurationseigenschaft `spring.jmx.enabled=true` aktiviert werden.  

Überprüfen Sie, ob die Abhängigkeit `spring-boot-actuator` in Ihrem Anwendungspaket aktiviert ist und der Startvorgang erfolgreich ausgeführt werden kann.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Wenn Ihre Anwendungsprotokolle in einem Speicherkonto archiviert, aber nicht an Azure Log Analytics gesendet werden können, überprüfen Sie, ob Sie [Ihren Arbeitsbereich ordnungsgemäß eingerichtet haben](../azure-monitor/learn/quick-create-workspace.md). Falls Sie einen kostenlosen Tarif von Azure Log Analytics verwenden, sollten Sie beachten, dass [für den kostenlosen Tarif keine SLA gilt](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>Nächste Schritte

* [Selbstdiagnose und Lösung von Problemen in Azure Spring Cloud](spring-cloud-howto-self-diagnose-solve.md)