---
title: Anpassen der Benutzeroberfläche der Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen dazu, wie Sie auf den Quellcode für die Benutzeroberfläche des Solution Accelerators für die Remoteüberwachung zugreifen können.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.openlocfilehash: aed63e332375be4f8ed939cf162545c9f366f329
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66143489"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Anpassen des Solution Accelerators für die Remoteüberwachung

Dieser Artikel enthält Informationen dazu, wie Sie auf den Quellcode zugreifen und die Benutzeroberfläche des Solution Accelerators für die Remoteüberwachung anpassen können.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Vorbereiten einer lokalen Entwicklungsumgebung für die Benutzeroberfläche

Der Code der Benutzeroberfläche des Solution Accelerators für die Remoteüberwachung wird mithilfe des Frameworks React.js implementiert. Sie finden den Quellcode im [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui)-GitHub-Repository.

Um Änderungen an der Benutzeroberfläche vorzunehmen, können Sie eine Kopie davon lokal ausführen. Um Aktionen, wie das Abrufen der Telemetriedaten, abzuschließen, stellt die lokale Kopie eine Verbindung mit einer bereitgestellten Instanz der Lösung her.

Die folgenden Schritte beschreiben den Prozess zum Einrichten einer lokalen Umgebung für die Entwicklung von Benutzeroberflächen:

1. Bereitstellen einer **grundlegenden** Instanz des Solution Accelerators mit der **pcs**-CLI. Notieren Sie den Namen der Bereitstellung und die Anmeldeinformationen, die Sie für die VM angegeben haben. Weitere Informationen finden Sie unter [Bereitstellen der vorkonfigurierten Remoteüberwachungslösung über die Befehlszeilenschnittstelle](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Um den SSH-Zugriff auf den virtuelle Computer zu ermöglichen, der die Microservices in Ihrer Lösung hostet, verwenden Sie das Azure-Portal oder die Azure Cloud Shell. Beispiel: 

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Aktivieren Sie SSH-Zugriff nur während der Test- und Entwicklungsphase. Wenn Sie SSH aktivieren, [sollten Sie wieder deaktivieren, sobald Sie fertig sind](../security/azure-security-network-security-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Suchen Sie über das Azure-Portal oder Azure Cloud Shell den Namen und die öffentliche IP-Adresse des virtuellen Computers. Beispiel: 

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer her. Verwenden Sie die IP-Adresse aus dem vorherigen Schritt und die Anmeldeinformationen, die Sie beim Ausführen von **pcs** angegeben haben, um die Lösung bereitzustellen. Der Befehl `ssh` ist in Azure Cloud Shell verfügbar.

1. Damit der lokale Computer eine Verbindung herstellen kann, führen Sie die folgenden Befehle in der Bash-Shell des virtuellen Computers aus:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Sobald der Befehl abgeschlossen ist und die Website gestartet wird, können Sie den virtuellen Computer trennen.

1. Bearbeiten Sie in Ihrer lokalen Kopie des [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui)-Repositorys die **ENV**-Datei, um die URL für die bereitgestellte Lösung hinzuzufügen:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. Navigieren Sie in der Eingabeaufforderung zu Ihrer lokalen Kopie des Ordners `azure-iot-pcs-remote-monitoring-webui`.

1. Um die erforderlichen Bibliotheken zu installieren und die Benutzeroberfläche lokal auszuführen, führen Sie die folgenden Befehle aus:

    ```cmd/sh
    npm install
    npm start
    ```

1. Der vorherige Befehl führt die Benutzeroberfläche lokal unter „http:\//localhost:3000/dashboard“ aus. Sie können den Code bearbeiten, während die Website ausgeführt wird, und ihre dynamische Aktualisierung beobachten.

## <a name="customize-the-layout"></a>Anpassen des Layouts

Jede Seite in der Remoteüberwachungslösung besteht aus einem Satz von Steuerelementen, den sogenannten *Bereichen* im Quellcode. Die **Dashboard**-Seite enthält fünf Bereiche: Übersicht, Zuordnung, Warnungen, Telemetrie und Analysen. Sie finden den Quellcode, der jede Seite und ihre Bereiche definiert, im [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui)-GitHub-Repository. Beispielsweise befindet sich der Code, der die **Dashboard**-Seite, ihr Layout und die Bereiche auf der Seite definiert, im [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard)-Ordner.

Da die Bereiche ihr Layout und ihre Größe selbst verwalten, können Sie einfach das Layout einer Seite ändern. Nehmen Sie die folgenden Änderungen am Element **PageContent** Element in der `src/components/pages/dashboard/dashboard.js`-Datei vor:

* Tauschen Sie die Position des Zuordnungs- und Telemetriebereichs.
* Ändern Sie die relative Breite der Zuordnungs- und Analysebereiche.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Bereichslayout ändern](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Sie können auch mehrere Instanzen des gleichen Bereichs oder mehrere Versionen hinzufügen, wenn Sie [einen Bereich duplizieren und anpassen](#duplicate-and-customize-an-existing-control). Im folgenden Beispiel wird das Hinzufügen von zwei Instanzen des Telemetriebereichs gezeigt: Um diese Änderungen vorzunehmen, bearbeiten Sie die `src/components/pages/dashboard/dashboard.js`-Datei:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Sie können dann in den einzelnen Bereichen verschiedene Telemetriedaten anzeigen:

![Mehrere Telemetriedatenbereiche](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplizieren und Anpassen eines vorhandenen Steuerelements

Die folgenden Schritte beschreiben das Duplizieren eines vorhandenen Bereichs, seine Änderung und die Verwendung der geänderten Version. Bei diesen Schritten wird als Beispiel der Bereich **Warnungen** verwendet:

1. Erstellen Sie in der lokalen Kopie des Repositorys eine Kopie des **alerts**-Ordners im `src/components/pages/dashboard/panels`-Ordner. Benennen Sie die neue Kopie **cust_alerts**.

1. Nennen Sie in der Datei **alertsPanel.js** im **cust_alerts**-Ordner die Klasse in **CustAlertsPanel** um:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Fügen Sie der Datei `src/components/pages/dashboard/panels/index.js` die folgende Zeile hinzu:

    ```javascript
    export * from './cust_alerts';
    ```

1. Ersetzen Sie in der `src/components/pages/dashboard/dashboard.js`-Datei `alertsPanel` durch `CustAlertsPanel`:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Sie haben jetzt den ursprünglichen Bereich **Warnungen** durch eine Kopie namens **CustAlerts** ersetzt. Diese Kopie ist mit dem Original identisch. Sie können jetzt die Kopie ändern. Ändern Sie z.B. die Spaltenreihenfolge im Bereich **Warnungen**:

1. Öffnen Sie die Datei `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js` .

1. Ändern Sie die Spaltendefinitionen, wie im folgenden Codeausschnitt gezeigt:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Der folgende Screenshot zeigt die neue Version des Bereichs **Warnungen**:

![Aktualisierter Bereich „Warnungen“](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Anpassen des Telemetriediagramms

Das Telemetriediagramm auf der `src/components/pages/dashboard/panels/telemtry`Dashboard **-Seite wird durch die Dateien im** -Ordner definiert. Die Benutzeroberfläche ruft die Telemetriedaten aus dem Lösungs-Back-End in der `src/services/telemetryService.js`-Datei ab. Die folgenden Schritte zeigen Ihnen, wie Sie den im Telemetriediagramm angezeigten Zeitraum von 15 auf 5 Minuten ändern:

1. In der `src/services/telemetryService.js`-Datei finden Sie die aufgerufene Funktion **getTelemetryByDeviceIdP15M**. Erstellen Sie eine Kopie dieser Funktion, und ändern Sie die Kopie wie folgt:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Um mit dieser neuen Funktion das Telemetriediagramm aufzufüllen, öffnen Sie die `src/components/pages/dashboard/dashboard.js`-Datei. Ändern Sie dort die Zeile, die den Telemetriedatenstrom initialisiert, wie folgt:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Das Telemetriediagramm zeigt jetzt die Telemetriedaten von fünf Minuten an:

![Telemetriediagramm für einen Tag](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Hinzufügen eines neuen KPI

Auf der **Dashboard**-Seite werden KPIs im Bereich **Analysen** angezeigt. Diese KPIs werden in der `src/components/pages/dashboard/dashboard.js`-Datei berechnet. Die KPIs werden durch die `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`-Datei gerendert. Die folgenden Schritte beschreiben das Berechnen und Rendern eines neuen KPI-Werts auf der **Dashboard**-Seite. Im Beispiel wird eine neue prozentuale Änderung im KPI für Warnungen hinzugefügt:

1. Öffnen Sie die Datei `src/components/pages/dashboard/dashboard.js` . Ändern Sie das **initialState**-Objekt, sodass eine **warningAlertsChange**-Eigenschaft wie folgt einbezogen wird:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Ändern Sie das **currentAlertsStats**-Objekt, sodass **totalWarningCount** als Eigenschaft einbezogen wird:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Berechnen Sie den neuen KPI. Finden Sie die Berechnung für die kritische Anzahl von Warnungen. Kopieren Sie den Code, und ändern Sie die Kopie wie folgt:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Beziehen Sie den neuen KPI **warningAlertsChange** in den KPI-Datenstrom ein:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Beziehen Sie den neuen KPI **warningAlertsChange** in die Zustandsdaten ein, die zum Rendern der Benutzeroberfläche verwendet werden:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Aktualisieren Sie die Daten, die dem Bereich „KPIs“ übergeben werden:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Sie haben jetzt die Änderungen in der `src/components/pages/dashboard/dashboard.js`-Datei fertig gestellt. Die folgenden Schritte beschreiben, welche Änderungen in der `src/components/pages/dashboard/panels/analytics/analyticsPanel.js`-Datei zum Anzeigen der neuen KPI erforderlich sind:

1. Ändern Sie die folgende Codezeile wie folgt zum Abrufen des neuen KPI-Werts:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Ändern Sie das Markup zum Anzeigen des neuen KPI-Werts wie folgt:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Auf der **Dashboard**-Seite wird jetzt der neue KPI-Wert angezeigt:

![Warnungs-KPI](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Anpassen der Karte

Ausführliche Informationen zu den Kartenkomponenten in der Lösung finden Sie auf der Seite [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Anpassungsübersicht) in GitHub.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Andere Optionen zur Anpassung

Um die Darstellungs- und Visualisierungsschicht in der Remoteüberwachungslösung weiter zu ändern, können Sie den Code bearbeiten. Relevante GitHub-Repositorys:

* [The configuration microservice for Azure IoT Solutions (.NET) (Der Konfigurations-Microservice für Azure IoT-Lösungen [.NET])](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [The configuration microservice for Azure IoT Solutions (Java) (Der Konfigurations-Microservice für Azure IoT-Lösungen [Java])](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Azure IoT PCS Remote Monitoring Web UI (Azure IoT PCS-Remoteüberwachungs-Webbenutzeroberfläche)](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zu den verfügbaren Ressourcen erhalten, mit denen Sie die Webbenutzeroberfläche im Solution Accelerator für die Remoteüberwachung anpassen können. Weitere Informationen zum Anpassen der Benutzeroberfläche finden Sie in den folgenden Artikeln:

* [Hinzufügen einer benutzerdefinierten Seite zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-page.md)
* [Hinzufügen eines benutzerdefinierten Dienstes zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-service.md).
* [Hinzufügen eines benutzerdefinierten Rasters zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-grid.md)
* [Hinzufügen eines benutzerdefinierten Flyouts zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Hinzufügen eines benutzerdefinierten Panels zum Dashboard zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-panel.md)

Weitere konzeptuelle Informationen zum Solution Accelerator für die Remoteüberwachung finden Sie unter [Remoteüberwachungsarchitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Weitere Informationen zum Anpassen der Microservices der Remoteüberwachungslösung finden Sie unter [Anpassen und erneutes Bereitstellen eines Microservice](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
