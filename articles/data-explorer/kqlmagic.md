---
title: Verwenden einer Jupyter Notebook-Instanz zum Analysieren von Daten in Azure Data Explorer
description: In diesem Thema erfahren Sie, wie Sie mithilfe einer Jupyter Notebook-Instanz und der Kqlmagic-Erweiterung Daten in Azure Data Explorer analysieren.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 896a5d13279c15f0035f214da3d5a7d7e6f1861f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758414"
---
# <a name="use-a-jupyter-notebook-and-kqlmagic-extension-to-analyze-data-in-azure-data-explorer"></a>Verwenden einer Jupyter Notebook-Instanz und der Kqlmagic-Erweiterung zum Analysieren von Daten in Azure Data Explorer

Jupyter Notebook ist eine Open Source-Webanwendung, mit der Sie Dokumente erstellen und freigeben können, die Livecode, Gleichungen, Visualisierungen und beschreibenden Text enthalten. Die Nutzung umfasst Folgendes: Bereinigung und Transformation von Daten, numerische Simulation, statistische Modellierung, Datenvisualisierung und Machine Learning.
[Jupyter Notebook](https://jupyter.org/) unterstützt Magic-Befehle, die die Funktionen des Kernels mit zusätzlichen Befehlen erweitern. Kqlmagic ist ein Befehl, der die Funktionen des Python-Kernels in Jupyter Notebook erweitert, damit Sie Abfragen in der Kusto-Sprache nativ ausführen können. Sie können Python und die Kusto-Abfragesprache mühelos zum Abfragen und Visualisieren von Daten mithilfe der umfassenden Bibliothek „Plot.ly“ kombinieren, die mit `render`-Befehlen integriert ist. Zum Ausführen von Abfragen werden Datenquellen unterstützt. Zu diesen Datenquellen gehören Azure Data Explorer, ein schneller und hochgradig skalierbarer Dienst zur Untersuchung von Protokoll- und Telemetriedaten, sowie Azure Monitor-Protokolle und Application Insights. Kqlmagic funktioniert auch mit Azure Notebooks, Jupyter Lab und der Jupyter-Erweiterung von Visual Studio Code.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Organisations-E-Mail-Konto, das Mitglied von Azure Active Directory (Azure AD) ist
- Jupyter Notebook, installiert auf ihrem lokalen Computer, oder Azure Notebooks und das geklonte Beispielprojekt [Azure Notebook](https://kustomagicsamples-manojraheja.notebooks.azure.com/j/notebooks/Getting%20Started%20with%20kqlmagic%20on%20Azure%20Data%20Explorer.ipynb)

## <a name="install-kql-magic-library"></a>Installieren der Kqlmagic-Bibliothek

1. Installieren von Kqlmagic:

    ```python
    !pip install Kqlmagic --no-cache-dir  --upgrade
    ```
    > [!NOTE]
    > Wenn Sie Azure Notebooks verwenden, ist dieser Schritt nicht erforderlich.

1. Laden von Kqlmagic:

    ```python
    reload_ext Kqlmagic
    ```

## <a name="connect-to-the-azure-data-explorer-help-cluster"></a>Herstellen einer Verbindung mit dem Azure Data Explorer-Hilfecluster

Verwenden Sie den folgenden Befehl, um eine Verbindung mit der Datenbank *Samples* herzustellen, die auf dem *Hilfecluster* gehostet wird. Wenn Sie nicht Microsoft Azure AD verwenden, ersetzen Sie den Mandantenname `Microsoft.com` durch den Namen Ihres Azure AD-Mandanten.

```python
%kql AzureDataExplorer://tenant="Microsoft.com";code;cluster='help';database='Samples'
```

## <a name="query-and-visualize"></a>Abfragen und Visualisieren

Fragen Sie Daten mit dem [Render-Operator](/azure/kusto/query/renderoperator) ab, und visualisieren Sie Daten mithilfe der Bibliothek „ploy.ly“ Durch die Abfrage und Visualisierung wird eine integrierte Benutzeroberfläche bereitgestellt, die die native KQL verwendet. Kqlmagic unterstützt abgesehen von `timepivot`, `pivotchart` und `ladderchart` die meisten Diagramme. Der Render-Operator wird mit Ausnahme von `kind`, `ysplit` und `accumulate` mit allen Attributen unterstützt. 

### <a name="query-and-render-piechart"></a>Abfragen und Rendern eines Kreisdiagramms

```python
%%kql
StormEvents
| summarize statecount=count() by State
| sort by statecount 
| limit 10
| render piechart title="My Pie Chart by State"
```

### <a name="query-and-render-timechart"></a>Abfragen und Rendern eines Zeitdiagramms

```python
%%kql
StormEvents
| summarize count() by bin(StartTime,7d)
| render timechart
```

> [!NOTE]
> Diese Diagramme sind interaktiv. Wählen Sie einen Zeitbereich aus, um einen bestimmten Zeitraum zu vergrößern.

### <a name="customize-the-chart-colors"></a>Anpassen der Diagrammfarben

Wenn Ihnen die Standardfarbpalette nicht gefällt, können Sie die Diagramme mit Palettenoptionen anpassen. Die verfügbaren Paletten finden Sie auf der folgenden Website: [Choose colors palette for your Kqlmagic query chart result](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb) (Auswählen der Farbpalette für das Kqlmagic-Abfragediagrammergebnis)

1. Mit folgendem Befehl rufen Sie eine Liste der Paletten auf:

    ```python
    %kql --palettes -popup_window
    ```

1. Wählen Sie die Farbpalette `cool` aus, und rendern Sie die Abfrage erneut:

    ```python
    %%kql -palette_name "cool"
    StormEvents
    | summarize statecount=count() by State
    | sort by statecount
    | limit 10
    | render piechart title="My Pie Chart by State"
    ```

## <a name="parameterize-a-query-with-python"></a>Parametrisieren einer Abfrage mit Python

Kqlmagic ermöglicht einen einfachen Austausch zwischen der Kusto-Abfragesprache und Python. Weitere Informationen: [Parametrize your Kqlmagic query with Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) (Parametrisieren von Kqlmagic-Abfragen mit Python)

### <a name="use-a-python-variable-in-your-kql-query"></a>Verwenden einer Python-Variablen in KQL-Abfragen

Sie können den Wert einer Python-Variablen in Ihrer Abfrage verwenden, um die Daten zu filtern:

```python
statefilter = ["TEXAS", "KANSAS"]
```

```python
%%kql
let _state = statefilter;
StormEvents 
| where State in (_state) 
| summarize statecount=count() by bin(StartTime,1d), State
| render timechart title = "Trend"
```

### <a name="convert-query-results-to-pandas-dataframe"></a>Konvertieren von Abfrageergebnissen in Pandas Dataframe

Sie können in Pandas DataFrame auf die Ergebnisse einer KQL-Abfrage zugreifen. Wie im folgenden Beispiel gezeigt können Sie mit der Variablen `_kql_raw_result_` auf die Ergebnisse der zuletzt ausgeführten Abfrage zugreifen und diese unkompliziert in Pandas DataFrame konvertieren:

```python
df = _kql_raw_result_.to_dataframe()
df.head(10)
```

### <a name="example"></a>Beispiel

In vielen Analyseszenarios sollten Sie, wiederverwendbare Notebooks erstellen, die viele Abfragen enthalten und die Ergebnisse einer Abfrage an nachfolgende Abfragen übergeben. Im folgenden Beispiel wird die Python-Variable `statefilter` verwendet, um die Daten zu filtern.

1. Führen Sie eine Abfrage aus, um die zehn Zustände mit dem Höchstwert für die `DamageProperty`-Eigenschaft aufzuführen:

    ```python
    %%kql
    StormEvents
    | summarize max(DamageProperty) by State
    | order by max_DamageProperty desc
    | limit 10
    ```

1. Führen Sie eine Abfrage aus, um den Zustand mit dem Höchstwert zu extrahieren, und legen Sie diesen in einer Python-Variablen fest:

    ```python
    df = _kql_raw_result_.to_dataframe()
    statefilter =df.loc[0].State
    statefilter
    ```

1. Führen Sie eine Abfrage mit der `let`-Anweisung und der Python-Variablen aus:

    ```python
    %%kql
    let _state = statefilter;
    StormEvents 
    | where State in (_state)
    | summarize statecount=count() by bin(StartTime,1d), State
    | render timechart title = "Trend"
    ```

1. Führen Sie den help-Befehl aus:

    ```python
    %kql --help "help"
    ```

## <a name="next-steps"></a>Nächste Schritte

Führen Sie den help-Befehl aus, um die folgenden Beispiel-Notebooks kennenzulernen, die alle unterstützten Features enthalten:
- [Get started with Kqlmagic for Azure Data Explorer](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStart.ipynb) (Erste Schritte mit Kqlmagic für Azure Data Explorer) 
- [Get started with Kqlmagic for Application Insights](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartAI.ipynb) (Erste Schritte mit Kqlmagic für Application Insights) 
- [Get started with Kqlmagic for Azure Monitor logs](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FQuickStartLA.ipynb) (Erste Schritte mit Kqlmagic für Azure Monitor-Protokolle) 
- [Parametrize your Kqlmagic query with Python](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FParametrizeYourQuery.ipynb) (Parametrisieren von Kqlmagic-Abfragen mit Python) 
- [Choose colors palette for your Kqlmagic query chart result](https://mybinder.org/v2/gh/Microsoft/jupyter-Kqlmagic/master?filepath=notebooks%2FColorYourCharts.ipynb) (Auswählen der Farbpalette für das Kqlmagic-Abfragediagrammergebnis)
