---
title: Integrieren von Time Series Insights mit Remoteüberwachung – Azure | Microsoft-Dokumentation
description: In diesem Gewusst wie-Artikel wird beschrieben, wie Sie Time Series Insights für eine vorhandene Remoteüberwachungslösung konfigurieren, die nicht bereits über Time Series Insights verfügt.
author: aditidugar
manager: timlt
ms.author: adugar
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 4cc9b0051eaa12eee07f067352126ad159107a83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442928"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrieren von Azure Time Series Insights mit Remoteüberwachung

Azure Time Series Insights ist ein vollständig verwalteter Analyse-, Speicher- und Visualisierungsdienst für die Verwaltung von IoT-Zeitreihendaten in der Cloud. Sie können Time Series Insights verwenden, um Zeitreihendaten zu speichern und zu verwalten, Ereignisse gleichzeitig zu analysieren und zu visualisieren, Fehlerursachenanalysen durchzuführen und mehrere Sites und Ressourcen zu vergleichen.

Der Solution Accelerator für die Remoteüberwachung ermöglicht jetzt die automatische Bereitstellung und Integration mit Time Series Insights. In diesem Gewusst wie-Artikel wird beschrieben, wie Sie Time Series Insights für eine vorhandene Remoteüberwachungslösung konfigurieren, die nicht bereits über Time Series Insights verfügt.

> [!NOTE]
> Time Series Insights ist für die Azure-Cloud in China derzeit nicht verfügbar. Für neue Solution Accelerator-Bereitstellungen für die Remoteüberwachung in der Azure-Cloud in China kann Cosmos DB für alle Speicherzwecke genutzt werden.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Gewusst wie-Artikels müssen Sie bereits eine Remoteüberwachungslösung bereitgestellt haben:

* [Bereitstellen des Solution Accelerators für die Remoteüberwachung](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Erstellen einer Consumergruppe

Erstellen Sie eine dedizierte Consumergruppe in Ihrer IoT Hub-Instanz, die zum Streamen von Daten an Time Series Insights verwendet wird.

> [!NOTE]
> Consumergruppen werden von Anwendungen verwendet, um Daten aus Azure IoT Hub abzurufen. Jede Consumergruppe kann bis zu fünf Ausgabeconsumer enthalten. Sie sollten eine neue Consumergruppe für jeweils fünf Ausgabesenken erstellen. Es können bis zu 32 Consumergruppen erstellt werden.

1. Klicken Sie im Azure-Portal auf die Schaltfläche „Cloud Shell“.

1. Führen Sie den folgenden Befehl aus, um eine neue Consumergruppe zu erstellen. Verwenden Sie den Namen des IoT-Hubs in Ihrer Bereitstellung für die Remoteüberwachung und den Namen der Bereitstellung für die Remoteüberwachung als Ressourcengruppennamen:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Bereitstellen von Time Series Insights

Stellen Sie als Nächstes Time Series Insights als zusätzliche Ressource in Ihrer Remoteüberwachungslösung bereit, und stellen Sie dafür eine Verbindung mit dem IoT-Hub her.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **Ressource erstellen** > **Internet der Dinge** > **Time Series Insights**.

    ![Neu: Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Verwenden Sie zur Erstellung Ihrer Time Series Insights-Umgebung die Werte in der folgenden Tabelle:

    | Einstellung | Wert |
    | ------- | ----- |
    | Umgebungsname | Im folgenden Screenshot wird der Name **contorosrmtsi** verwendet. Wählen Sie Ihren eigenen eindeutigen Namen, wenn Sie diesen Schritt durchführen. |
    | Abonnement | Wählen Sie Ihr Azure-Abonnement in der Dropdownliste aus. |
    | Ressourcengruppe | **Vorhandene verwenden**: Wählen Sie den Namen Ihrer vorhandenen Ressourcengruppe für die Remoteüberwachung aus. |
    | Location | Hier verwenden wir **USA, Osten**. Erstellen Sie Ihre Umgebung nach Möglichkeit in derselben Region wie Ihre Remoteüberwachungslösung. |
    | Sku |**S1** |
    | Capacity | **1** |

    ![Time Series Insights erstellen](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Klicken Sie auf **Create**. Die Erstellung der Umgebung kann einen Moment dauern.

## <a name="create-event-source"></a>Erstellen der Ereignisquelle

Erstellen Sie eine neue Ereignisquelle für die Verbindung mit Ihrem IoT Hub. Stellen Sie sicher, dass Sie die Consumergruppe verwenden, die in den vorherigen Schritten erstellt wurde. Für Time Series Insights muss jeder Dienst über eine dedizierte Consumergruppe verfügen, die von keinem anderen Dienst verwendet wird.

1. Navigieren Sie zu Ihrer neuen Time Series Insights-Umgebung.

1. Wählen Sie auf der linken Seite **Ereignisquellen**.

    ![Anzeigen von Ereignisquellen](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen einer Ereignisquelle](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Verwenden Sie die Werte in der folgenden Tabelle, um Ihren IoT Hub als eine neue Ereignisquelle zu konfigurieren:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name der Ereignisquelle | Im folgenden Screenshot wird der Name **contosorm-iot-hub** verwendet. Verwenden Sie Ihren eigenen eindeutigen Namen, wenn Sie diesen Schritt durchführen. |
    | `Source` | **IoT Hub** |
    | Importoption | **Verwenden eines IoT Hub aus verfügbaren Abonnements** |
    | Abonnement-ID | Wählen Sie Ihr Azure-Abonnement in der Dropdownliste aus. |
    | IoT Hub-Name | **contosorma57a6**. Verwenden Sie den Namen Ihres IoT Hub aus Ihrer Remoteüberwachungslösung. |
    | IoT Hub-Richtlinienname | **iothubowner** Stellen Sie sicher, dass die verwendete Richtlinie eine Besitzerrichtlinie ist. |
    | IoT Hub-Richtlinienschlüssel | Dieses Feld wird automatisch ausgefüllt. |
    | IoT Hub-Consumergruppe | **timeseriesinsights** |
    | Ereignisserialisierungsformat | **JSON**     | 
    | Name der Timestamp-Eigenschaft | Nicht ausfüllen |

    ![Erstellen der Ereignisquelle](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Klicken Sie auf **Create**.

## <a name="configure-the-data-access-policy"></a>Konfigurieren der Datenzugriffsrichtlinie

Fügen Sie Ihre Anwendung und die Benutzer unter den Datenzugriffsrichtlinien im Azure-Portal hinzu, um sicherzustellen, dass alle Benutzer, die über Zugriff auf Ihre Remoteüberwachungslösung verfügen, Daten im Time Series Insights-Explorer durchsuchen können. 

1. Wählen Sie in der Navigationsliste die Option **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe **ContosoRM** aus.

1. Wählen Sie in der Liste mit den Azure-Ressourcen den Eintrag **contosormtsi** aus.

1. Wählen Sie **Datenzugriffsrichtlinien**, um die aktuelle Liste mit den Rollenzuweisungen anzuzeigen.

1. Wählen Sie **Hinzufügen**, um den Bereich **Select User Rule** (Benutzerregel auswählen) zu öffnen.

   Falls Sie nicht über Berechtigungen zum Zuweisen von Rollen verfügen, wird die Option **Hinzufügen** nicht angezeigt.

1. Wählen Sie in der Dropdownliste **Rolle** eine Rolle aus, z.B. **Leser** und **Mitwirkender**.

1. Wählen Sie in der Liste **Auswählen** einen Benutzer, eine Gruppe oder eine Anwendung aus. Wird der Sicherheitsprinzipal in der Liste nicht angezeigt, können Sie im Feld **Auswählen** einen Begriff eingeben, um das Verzeichnis nach Anzeigenamen, E-Mail-Adressen und Objektbezeichner zu durchsuchen.

1. Wählen Sie **Speichern** aus, um die Rollenzuweisung zu erstellen. Nach einigen Augenblicken wird der Sicherheitsprinzipal der Rolle in den Datenzugriffsrichtlinien zugewiesen.

> [!NOTE]
> Wenn Sie weiteren Benutzern Zugriff auf den Time Series Insights-Explorer gewähren möchten, können Sie folgendermaßen vorgehen, um [Datenzugriff zu erteilen](../time-series-insights/time-series-insights-data-access.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurieren von Azure Stream Analytics 

Im nächsten Schritt wird der Azure Stream Analytics-Manager-Microservice konfiguriert, damit Nachrichten nicht mehr an Cosmos DB gesendet und nur unter Time Series Insights gespeichert werden. Überspringen Sie diesen Schritt, wenn Sie Ihre Nachrichten in Cosmos DB duplizieren möchten.

1. Wählen Sie in der Navigationsliste die Option **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe **ContosoRM** aus.

1. Suchen Sie in der Liste mit den Ressourcen nach dem ASA-Streamingauftrag (Azure Stream Analytics). Der Ressourcenname beginnt mit **streamingjobs-** .

1. Klicken Sie oben auf die Schaltfläche zum Beenden der ASA-Streamingaufträge.

1. Bearbeiten Sie die ASA-Abfrage, und entfernen Sie die **SELECT**-, **INTO**- und **FROM**-Klauseln, in denen auf den Nachrichtendatenstrom in Cosmos DB verwiesen wird. Diese Klauseln sollten in der Abfrage unten angeordnet sein und wie im folgenden Beispiel aussehen:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Starten Sie die Azure Stream Analytics-Streamingaufträge neu.

7. Führen Sie für die aktuellen Änderungen einen Pull zum Azure Stream Analytics-Manager-Microservice aus, indem Sie an der Eingabeaufforderung den folgenden Befehl eingeben:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurieren des Telemetriedaten-Microservice

Führen Sie einen Pull für den aktuellen Telemetriedaten-Microservice aus, indem Sie an der Eingabeaufforderung den folgenden Befehl eingeben:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Optional]* Konfigurieren eines Links zum Time Series Insights-Explorer für die Webbenutzeroberfläche

Wir empfehlen Ihnen, die Benutzeroberfläche so anzupassen, dass sie einen einfachen Link zur Umgebung enthält, damit Sie Ihre Daten leicht im Time Series Insights-Explorer anzeigen können. Führen Sie hierzu einen Pull für die aktuellen Änderungen zur Webbenutzeroberfläche aus, indem Sie den folgenden Befehl verwenden:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurieren der Umgebungsvariablen

Zum Abschließen der Time Series Insights-Integration müssen Sie die Umgebung Ihrer Bereitstellung für die aktualisierten Microservices konfigurieren.

### <a name="basic-deployments"></a>Basic-Bereitstellungen

Konfigurieren Sie die Umgebung für die Bereitstellung vom Typ `basic` für die aktualisierten Microservices.

1. Klicken Sie im Azure-Portal im linken Bereich auf die Registerkarte **Azure Active Directory**.

1. Klicken Sie auf **App-Registrierungen**.

1. Suchen Sie nach der Anwendung **ContosoRM**, und klicken Sie darauf.

1. Navigieren Sie zu **Einstellungen** > **Schlüssel**, und erstellen Sie dann einen neuen Schlüssel für Ihre Anwendung. Kopieren Sie den Schlüsselwert an einen sicheren Ort.

1. Rufen Sie die [aktuelle Datei „docker-compose.yml“](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) per Pullvorgang aus dem GitHub-Repository ab, indem Sie das aktuelle Tag verwenden. 

1. Stellen Sie eine SSH-Verbindung mit der VM her, indem Sie die Schritte unter [Verwenden von SSH-Schlüsseln mit Windows in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) ausführen.

1. Geben Sie `cd /app` ein, nachdem die Verbindung hergestellt wurde.

1. Fügen Sie jedem Microservice in der Docker Compose-YAML-Datei und dem Skript `env-setup` auf dem virtuellen Computer die folgenden Umgebungsvariablen hinzu:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Navigieren Sie zum **Telemetriedienst**, und bearbeiten Sie auch die Docker Compose-Datei, indem Sie die oben angegebenen Umgebungsvariablen hinzufügen.

1. Navigieren Sie zum **ASA-Manager-Dienst**, und bearbeiten Sie die Docker Compose-Datei, indem Sie `PCS_TELEMETRY_STORAGE_TYPE` hinzufügen.

1. Starten Sie die Docker-Container mit `sudo ./start.sh` auf dem virtuellen Computer neu.

> [!NOTE]
> Die obige Konfiguration von Umgebungsvariablen gilt für Versionen der Remoteüberwachung vor Version 1.0.2.

### <a name="standard-deployments"></a>Standard-Bereitstellungen

Konfigurieren Sie die Umgebung für die Bereitstellung vom Typ `standard` für die obigen aktualisierten Microservices.

1. Führen Sie in der Befehlszeile `kubectl proxy` aus. Weitere Informationen finden Sie unter [Accessing the Kubernetes API](https://kubernetes.io/docs/tasks/access-kubernetes-api/http-proxy-access-api/#using-kubectl-to-start-a-proxy-server) (Zugreifen auf die Kubernetes-API).

1. Öffnen Sie die Verwaltungskonsole für Kubernetes.

1. Suchen Sie nach der Konfigurationszuordnung, um die folgenden neuen Umgebungsvariablen für TSI hinzuzufügen:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Bearbeiten Sie die YAML-Vorlagendatei für den Telemetriedienst-Pod:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Bearbeiten Sie die YAML-Vorlagendatei für den ASA-Manager-Dienst-Pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie das Tutorial zum [Durchführen einer Analyse der Grundursache](iot-accelerators-remote-monitoring-root-cause-analysis.md), um zu erfahren, wie Sie im Time Series Insights-Explorer Ihre Daten durchsuchen und eine Warnung diagnostizieren können.

* Informationen zum Durchsuchen und Abfragen von Daten im Time Series Insights-Explorer finden Sie in der Dokumentation zum [Azure Time Series Insights-Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
