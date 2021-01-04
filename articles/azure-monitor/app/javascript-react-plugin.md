---
title: React-Plug-In für das Application Insights-JavaScript-SDK
description: Hier erfahren Sie, wie Sie das React-Plug-In für das Application Insights-JavaScript-SDK installieren und verwenden.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3348654a83b6d0930d10e1f58e07455623b5861d
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94981084"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>React-Plug-In für das Application Insights-JavaScript-SDK

Das React-Plug-In für das Application Insights-JavaScript-SDK ermöglicht Folgendes:

- Nachverfolgung von Routenänderungen
- Nutzungsstatistiken für React-Komponenten

## <a name="getting-started"></a>Erste Schritte

Installieren Sie das npm-Paket:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Grundlegende Verwendung

Initialisieren Sie eine Verbindung mit Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Umschließen Sie Ihre Komponente mit der Komponentenfunktion höherer Ordnung, um Application Insights für die Komponente zu aktivieren:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin, appInsights, MyComponent);
```

## <a name="configuration"></a>Konfiguration

| Name    | Standard | BESCHREIBUNG                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| history | NULL    | Hierbei handelt es sich um den React Router-Verlauf. Weitere Informationen finden Sie in der [Dokumentation für das react-router-Paket](https://reactrouter.com/web/api/history). Weitere Informationen zum Zugriff auf das Verlaufsobjekt außerhalb von Komponenten finden Sie in den [FAQ zu react-router](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components).    |

### <a name="react-components-usage-tracking"></a>Nachverfolgung der Nutzung von React-Komponenten

Wenden Sie zum Instrumentieren der Nachverfolgung der Nutzung verschiedener React-Komponenten die Komponentenfunktion höherer Ordnung `withAITracking` an.

Diese misst die Zeit zwischen dem `ComponentDidMount`-Ereignis und dem `ComponentWillUnmount`-Ereignis. Sie zieht hiervon jedoch die Zeit ab, in der der Benutzer inaktiv war, um eine genauere Angabe zu erhalten: `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time`.

Wenn Sie diese Metrik im Azure-Portal anzeigen möchten, müssen Sie zur Application Insights-Ressource navigieren, dort auf die Registerkarte „Metriken“ klicken und die leeren Diagramme so konfigurieren, dass der benutzerdefinierte Metrikname „React-Komponente Nutzungsdauer (Sekunden)“ angezeigt wird. Wählen Sie anschließend die Aggregation (Summe, Durchschnitt etc.) für Ihre Metrik aus und eine Aufteilung nach „Komponentenname“.

![Screenshot: Diagramm, das die benutzerdefinierte Metrik „React-Komponente Nutzungsdauer (Sekunden)“ aufgeteilt nach „Komponentenname“ zeigt](./media/javascript-react-plugin/chart.png)

Sie können auch benutzerdefinierte Abfragen ausführen, um Application Insights-Daten zu unterteilen, um Berichte und Visualisierungen gemäß Ihren Anforderungen zu generieren. Navigieren Sie im Azure-Portal zur Application Insights-Ressource, klicken Sie im oberen Menü der Registerkarte „Übersicht“ auf die Option „Analyse“, und führen Sie Ihre Abfrage aus.

![Screenshot: Ergebnisse der benutzerdefinierten Metrikabfrage](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Es kann bis zu 10 Minuten dauern, bis neue benutzerdefinierte Metriken im Azure-Portal angezeigt werden.

## <a name="using-react-hooks"></a>Verwenden von React-Hooks

[React-Hooks](https://reactjs.org/docs/hooks-reference.html) sind ein Ansatz für die Zustands- und Lebenszyklusverwaltung in einer React-Anwendung, der keine klassenbasierten React-Komponenten erfordert. Das React-Plug-In für Application Insights bietet eine Reihe von Hookintegrationen, die ähnlich funktionieren wie der Ansatz mit einer Komponente höherer Ordnung.

### <a name="using-react-context"></a>Verwenden des React-Kontexts

Die React-Hooks für Application Insights sind so konzipiert, dass sie den [React-Kontext](https://reactjs.org/docs/context.html) als enthaltenden Aspekt verwenden. Um den Kontext zu nutzen, initialisieren Sie Application Insights wie oben beschrieben, und importieren Sie dann das Context-Objekt:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Dieser Kontextanbieter macht Application Insights als `useContext`-Hook innerhalb aller zugehörigen untergeordneten Komponenten verfügbar.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

Der `useTrackMetric`-Hook repliziert die Funktionalität der `withAITracking`-Komponente höherer Ordnung, ohne der Komponentenstruktur eine zusätzliche Komponente hinzuzufügen. Der Hook erfordert zwei Argumente: die Application Insights-Instanz (die aus dem `useAppInsightsContext`-Hook abgerufen werden kann) und einen Bezeichner für die Komponente (z. B. der Name) zur Nachverfolgung.

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Er funktioniert wie die Komponente höherer Ordnung, reagiert aber auf Lebenszyklusereignisse von Hooks und nicht auf einen Komponentenlebenszyklus. Der Hook muss explizit für Benutzerereignisse bereitgestellt werden, wenn die Ausführung bei bestimmten Interaktionen erforderlich ist.

### `useTrackEvent`

Der `useTrackEvent`-Hook dient zur Nachverfolgung beliebiger benutzerdefinierter Ereignisse, die von einer Anwendung nachverfolgt werden müssen, z. B. das Klicken auf eine Schaltfläche oder andere API-Aufrufe. Er erfordert zwei Argumente: die Application Insights-Instanz (die aus dem `useAppInsightsContext`-Hook abgerufen werden kann) und einen Namen für das Ereignis.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Für den Hook kann bei seiner Verwendung eine Datennutzlast bereitgestellt werden, um dem Ereignis zusätzliche Daten hinzuzufügen, wenn es in Application Insights gespeichert wird.

## <a name="react-error-boundaries"></a>React-Fehlergrenzen

[Fehlergrenzen](https://reactjs.org/docs/error-boundaries.html) ermöglichen die ordnungsgemäße Behandlung von Ausnahmen, die in einer React-Anwendung auftreten. Wenn ein solcher Fehler auftritt, muss die Ausnahme wahrscheinlich protokolliert werden. Das React-Plug-In für Application Insights bietet eine ErrorBoundary-Komponente, die den Fehler bei seinem Auftreten automatisch protokolliert.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

Der `AppInsightsErrorBoundary`-Hook erfordert, dass zwei Eigenschaften an ihn übergeben werden: die für die Anwendung erstellte `ReactPlugin`-Instanz und eine Komponente, die beim Auftreten eines Fehlers gerendert werden soll. Wenn ein nicht behandelter Fehler auftritt, wird `trackException` mit den für die Fehlergrenze bereitgestellten Informationen aufgerufen, und die `onError`-Komponente wird angezeigt.

## <a name="sample-app"></a>Beispiel-App

Sehen Sie sich die [Application Insights-React-Demo](https://github.com/Azure-Samples/application-insights-react-demo) an.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum JavaScript SDK finden Sie in der [Dokumentation zum Application Insights JavaScript SDK](javascript.md).
- Informationen zur Kusto-Abfragesprache und zum Abfragen von Daten in Log Analytics finden Sie in der [Übersicht über Protokollabfragen](../../azure-monitor/log-query/log-query-overview.md).
