---
title: Hinzufügen eines Rasters zur Benutzeroberfläche der Remoteüberwachungslösung – Azure | Microsoft-Dokumentation
description: Dieser Artikel zeigt Ihnen, wie Sie ein Raster auf einer Seite in die Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung hinzufügen können.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.openlocfilehash: a24cb7f39ccb8ea07d4dde2869dc7c924b91983a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447096"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Hinzufügen eines benutzerdefinierten Rasters zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung

Dieser Artikel zeigt Ihnen, wie Sie ein Raster auf einer Seite in die Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung hinzufügen können. Der Artikel beschreibt Folgendes:

- Vorbereiten einer lokalen Entwicklungsumgebung.
- Hinzufügen eines neuen Rasters zu einer Seite in der Webbenutzeroberfläche.

Das Beispielraster in diesem Artikel zeigt die Daten vom Dienst an, den Sie anhand des Artikels [Hinzufügen eines benutzerdefinierten Dienstes zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-service.md) hinzugefügt haben.

## <a name="prerequisites"></a>Voraussetzungen

Um die Schritte in diesem Leitfaden abzuschließen, muss die folgende Software auf dem lokalen Entwicklungscomputer installiert sein:

- [Git-Client](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Vorbereitung

Sie sollten die Schritte in den folgenden Artikeln ausführen, bevor Sie fortfahren:

- [Hinzufügen einer benutzerdefinierten Seite zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-page.md).
- [Hinzufügen eines benutzerdefinierten Dienstes zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-service.md).

## <a name="add-a-grid"></a>Hinzufügen eines Rasters

Um ein Raster zur Webbenutzeroberfläche hinzuzufügen, müssen Sie die Quelldateien hinzufügen, die das Raster definieren, und einige vorhandene Dateien ändern, um die Webbenutzeroberfläche über die neue Komponente zu informieren.

### <a name="add-the-new-files-that-define-the-grid"></a>Hinzufügen der neuen Dateien, die das Raster definieren

Für die ersten Schritte finden Sie im Ordner **src/walkthrough/components/pages/pageWithGrid/exampleGrid** Dateien, die ein Raster definieren:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Kopieren Sie den Ordner **src/walkthrough/components/pages/pageWithGrid/exampleGrid** in den Ordner **src/components/pages/example**.

### <a name="add-the-grid-to-the-page"></a>Hinzufügen des Rasters zur Seite

Ändern Sie die Datei **src/components/pages/example/basicPage.container.js** wie folgt, um die Dienstdefinitionen zu importieren:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Ändern Sie die Datei **src/components/pages/example/basicPage.js** wie folgt, um das Raster hinzuzufügen:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Ändern Sie die Datei **src/components/pages/example/basicPage.test.js** wie folgt, um die Tests zu aktualisieren:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testen des Rasters

Wenn die Webbenutzeroberfläche nicht lokal ausgeführt wird, führen Sie den folgenden Befehl im Stammverzeichnis Ihrer lokalen Kopie im Repository durch:

```cmd/sh
npm start
```

Der vorherige Befehl führt die Benutzeroberfläche lokal unter [http://localhost:3000/dashboard](http://localhost:3000/dashboard) aus. Navigieren Sie zur Seite **Beispiel**. Dort finden Sie die Rasteranzeigedaten aus dem Dienst.

## <a name="select-rows"></a>Zeilen auswählen

Es gibt zwei Möglichkeiten, wie ein Benutzer Zeilen im Raster auswählen kann:

### <a name="hard-select-rows"></a>Feste Auswahl von Zeilen

Wenn ein Benutzer gleichzeitig auf mehrere Zeilen reagieren muss, verwenden Sie Kontrollkästchen in den Zeilen:

1. Aktivieren Sie die feste Auswahl von Zeilen durch Hinzufügen von **checkboxColumn** zur **ColumnDefs** im Raster. **checkboxColumn** ist definiert in **/src/components/shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Um auf die ausgewählten Elemente zuzugreifen, erhalten Sie einen Verweis auf die internen Raster-API:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Weisen Sie der Seite Kontextschaltflächen zu, wenn eine Zeile im Raster fest ausgewählt ist:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Wenn Sie auf eine Kontextschaltfläche klicken, erhalten Sie die fest ausgewählten Elemente, an denen Sie arbeiten können:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Nicht feste Auswahl von Zeilen

Wenn der Benutzer nur auf eine einzelne Zeile reagieren muss, konfigurieren Sie einen Link für die nicht feste Auswahl für eine oder mehrere Spalten in der **columnDefs**.

1. Fügen Sie in **exampleGridConfig.js** **SoftSelectLinkRenderer** als **cellRendererFramework** für eine **columnDef** hinzu.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Wenn ein Link für die nicht feste Auswahl angeklickt wird, wird das Ereignis **OnSoftSelectChange** ausgelöst. Führen Sie die gewünschte Aktion für diese Zeile aus, z.B. das Öffnen eines Detail-Flyouts. Dieses Beispiel schreibt einfach in die Konsole:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zu den verfügbaren Ressourcen erhalten, mit denen Sie die Seiten Webbenutzeroberfläche im Solution Accelerator für die Remoteüberwachung hinzufügen oder anpassen können.

Nachdem Sie ein Raster definiert haben, ist der nächste Schritt das [Hinzufügen eines benutzerdefinierten Flyout zur Webbenutzeroberfläche des Solution Accelerators für die Remoteüberwachung](iot-accelerators-remote-monitoring-customize-flyout.md), das auf der Beispielseite angezeigt werden sollen.

Weitere konzeptuelle Informationen zum Solution Accelerator für die Remoteüberwachung finden Sie unter [Remoteüberwachungsarchitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).
