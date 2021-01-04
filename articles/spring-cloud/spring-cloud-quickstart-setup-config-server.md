---
title: 'Schnellstart: Einrichten eines Azure Spring Cloud-Konfigurationsservers'
description: Hier wird die Einrichtung eines Azure Spring Cloud-Konfigurationsservers für die App-Bereitstellung erläutert.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 7400aeeba80ce168a9dea0d81e1ad0f2fbe24c95
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750842"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Schnellstart: Einrichten des Azure Spring Cloud-Konfigurationsservers

Der Azure Spring Cloud-Konfigurationsserver ist ein zentralisierter Konfigurationsdienst für verteilte Systeme. Er nutzt eine austauschbare Repositoryebene, die derzeit lokalen Speicher, Git und Subversion unterstützt. In dieser Schnellstartanleitung richten Sie den Konfigurationsserver zum Abrufen von Daten aus einem Git-Repository ein.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie den vorherigen Schnellstart in dieser Serie: [Bereitstellen des Azure Spring Cloud-Diensts](spring-cloud-quickstart-provision-service-instance.md)

## <a name="azure-spring-cloud-config-server-procedures"></a>Verfahren für den Azure Spring Cloud-Konfigurationsserver

Richten Sie den Konfigurationsserver mit dem Speicherort des Git-Repositorys für das Projekt ein, indem Sie den folgenden Befehl ausführen. Ersetzen Sie `<service instance name>` durch den Namen des Diensts, den Sie zuvor erstellt haben. Der Standardwert für den Dienstinstanznamen, den Sie in der vorherigen Schnellstartanleitung festgelegt haben, funktioniert nicht mit diesem Befehl.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Dieser Befehl weist den Konfigurationsserver an, die Konfigurationsdaten im Ordner [steeltoe-sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) des Beispiel-App-Repositorys zu suchen. Da der Name der App, die die Konfigurationsdaten abruft, `planet-weather-provider` lautet, heißt die verwendete Datei [planet-weather-provider.yml](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml).

::: zone-end

::: zone pivot="programming-language-java"
Der Azure Spring Cloud-Konfigurationsserver ist ein zentralisierter Konfigurationsdienst für verteilte Systeme. Er nutzt eine austauschbare Repositoryebene, die derzeit lokalen Speicher, Git und Subversion unterstützt.  Richten Sie den Konfigurationsserver für die Bereitstellung von Microservice-Apps in Azure Spring Cloud ein.

## <a name="prerequisites"></a>Voraussetzungen

* [Installation von JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Registrierung für ein Azure-Abonnement](https://azure.microsoft.com/free/)
* (Optional) [Installation der Azure CLI, Version 2.0.67 oder höher](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) und Installation der Azure Spring Cloud-Erweiterung mit dem Befehl `az extension add --name spring-cloud`
* (Optional) [Installation des Azure-Toolkits für IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) und [Anmeldung](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="azure-spring-cloud-config-server-procedures"></a>Verfahren für den Azure Spring Cloud-Konfigurationsserver

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Im folgenden Verfahren wird der Konfigurationsserver mithilfe des Azure-Portals zum Bereitstellen des [Piggy Metrics-Beispiels](spring-cloud-quickstart-sample-app-introduction.md) eingerichtet.

1. Navigieren Sie zur Seite **Übersicht**, und wählen Sie **Konfigurationsserver** aus.

2. Geben Sie im Abschnitt **Standardrepository** für **URI** den Wert „https://github.com/Azure-Samples/piggymetrics-config“ ein.

3. Klicken Sie auf **Überprüfen**.

    ![Navigieren zum Konfigurationsserver](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

4. Klicken Sie nach Abschluss der Überprüfung auf **Anwenden**, um die Änderungen zu speichern.

    ![Überprüfen des Konfigurationsservers](media/spring-cloud-quickstart-launch-app-portal/validate-complete.png)

5. Die Aktualisierung der Konfiguration kann einige Minuten dauern.
 
    ![Aktualisieren des Konfigurationsservers](media/spring-cloud-quickstart-launch-app-portal/updating-config.png) 

6. Sie sollten eine Benachrichtigung erhalten, wenn die Konfiguration abgeschlossen ist.

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/Azure-CLI)

Im folgenden Verfahren wird die Azure CLI zum Einrichten des Konfigurationsservers und zum Bereitstellen des [Piggy Metrics-Beispiels](spring-cloud-quickstart-sample-app-introduction.md) eingerichtet.

Richten Sie den Konfigurationsserver mit dem Speicherort des Git-Repositorys für das Projekt ein:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```
---
::: zone-end

> [!TIP]
> Bei Verwendung eines privaten Repositorys für den Konfigurationsserver finden Sie weitere Informationen im [Tutorial zum Einrichten der Authentifizierung](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-config-server).

## <a name="troubleshooting-of-azure-spring-cloud-config-server"></a>Problembehandlung beim Azure Spring Cloud-Konfigurationsserver

Im folgenden Verfahren wird erläutert, wie die Konfigurationsservereinstellungen behoben werden.

1. Wechseln Sie im Azure-Portal auf die Dienstseite **Übersicht**, und wählen Sie **Protokolle** aus. 
1. Wählen Sie **Abfragen** aus, und **zeigen Sie die Anwendungsprotokolle an, die die Begriffe „Fehler“ oder „Ausnahme“ enthalten**. 
1. Klicken Sie auf **Ausführen**. 
1. Wenn Sie in den Protokollen den Fehler **java.lang.illegalStateException** finden, deutet dies darauf hin, dass der Spring Cloud-Dienst keine Eigenschaften vom Konfigurationsserver finden kann.

    [ ![Über das ASC-Portal ausgeführte Abfrage](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-query.png)

1. Wechseln Sie zur Dienstseite **Übersicht**.
1. Wählen Sie **Probleme diagnostizieren und beheben** aus. 
1. Wählen Sie die Erkennung **Konfigurationsserver** aus.

    [ ![Problemdiagnose über das ASC-Portal](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-diagnose.png)

3. Klicken Sie auf **Integritätsüberprüfung des Konfigurationsservers**.

    [ ![ASC-Portal-Genie](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-genie.png)

4. Klicken Sie auf **Konfigurationsserverstatus**, um weitere Details der Erkennung anzuzeigen.

    [ ![Integritätsstatus des ASC-Portals](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png) ](media/spring-cloud-quickstart-setup-config-server/setup-config-server-health-status.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Azure-Ressourcen erstellt, für die weiterhin Gebühren anfallen, falls sie in Ihrem Abonnement verbleiben. Wenn Sie nicht mit der nächsten Schnellstartanleitung fortfahren möchten, lesen Sie die Informationen unter [Bereinigen von Ressourcen](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). Fahren Sie andernfalls mit der nächsten Schnellstartanleitung fort:

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen von Apps](spring-cloud-quickstart-deploy-apps.md)
