---
title: Kopier-Assistent in Azure Data Factory
description: Erfahren Sie, wie Sie den Kopier-Assistenten von Azure Data Factory verwenden, um Daten aus unterstützten Datenquellen in Senken zu kopieren.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0f95b0d62bc81a8dddc72239491a05ca78945490
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393376"
---
# <a name="azure-data-factory-copy-wizard"></a>Kopier-Assistent in Azure Data Factory

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. 

Der Kopier-Assistent in Azure Data Factory vereinfacht den Datenerfassungsvorgang (üblicherweise der erste Schritt in einem Szenario mit End-to-End-Datenintegration). Zur Arbeit mit dem Kopier-Assistenten in Azure Data Factory sind Kenntnisse hinsichtlich der JSON-Definitionen für verknüpfte Dienste, Datasets und Pipelines nicht erforderlich. Der Assistent erstellt automatisch eine Pipeline, um Daten aus der ausgewählten Datenquelle in das gewünschte Ziel zu kopieren. Darüber hinaus können Sie mit dem Kopier-Assistenten die erfassten Daten zum Zeitpunkt der Erstellung überprüfen. Dies spart insbesondere dann Zeit, wenn Sie Daten zum ersten Mal aus der Datenquelle erfassen. Klicken Sie zum Starten des Kopier-Assistenten auf der Startseite Ihrer Data Factory auf die Kachel **Daten kopieren** .

![Kopier-Assistent](./media/data-factory-copy-wizard/copy-data-wizard.png)

## <a name="designed-for-big-data"></a>Für Big Data konzipiert
Mit diesem Assistenten können Sie problemlos Daten in wenigen Minuten aus einer Vielzahl von Quellen an verschiedene Ziele verschieben. Nachdem Sie den Assistenten ausgeführt haben, werden eine Pipeline mit einer Kopieraktivität sowie einige davon abhängige Data Factory-Entitäten (verknüpfte Dienste und Datasets) automatisch erstellt. Keine weiteren Schritte sind erforderlich, um die Pipeline zu erstellen.   

![Auswählen einer Datenquelle](./media/data-factory-copy-wizard/select-data-source-page.png)

> [!NOTE]
> Im [Tutorial zum Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie schrittweise Anweisungen zum Erstellen einer Beispielpipeline, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbanktabelle zu kopieren.

Der Assistent wurde von Beginn an für Big Data konzipiert und bietet Unterstützung für verschiedene Daten- und Objekttypen. Sie können Data Factory-Pipelines erstellen, die Hunderte von Ordnern, Dateien oder Tabellen verschieben können. Der Assistent unterstützt die automatische Datenvorschau, Schemaerfassung und -zuordnung sowie das Filtern von Daten.

## <a name="automatic-data-preview"></a>Automatische Datenvorschau
Sie können eine Vorschau für einen Teil der Daten aus der ausgewählten Datenquelle anzeigen, um zu überprüfen, ob es tatsächlich die Daten sind, die Sie kopieren möchten. Wenn die Datenquelle eine Textdatei ist, analysiert der Kopier-Assistent darüber hinaus diese Datei und erfasst Trennzeichen für Zeilen und Spalten sowie das Schema automatisch.

![Dateiformateinstellungen](./media/data-factory-copy-wizard/file-format-settings.png)

## <a name="schema-capture-and-mapping"></a>Schemaerfassung und -zuordnung
Das Schema der Eingabedaten stimmt nicht in jedem Fall mit dem Schema der Ausgabedaten überein. In diesem Szenario müssen die Spalten des Quellschemas den Spalten des Zielschemas zugeordnet werden.

> [!TIP]
> Beim Kopieren von Daten aus SQL Server oder Azure SQL-Datenbank in Azure Synapse Analytics unterstützt Data Factory die automatische Tabellenerstellung anhand des Quellschemas, wenn die Tabelle im Zielspeicher nicht vorhanden ist. Weitere Informationen finden Sie unter [Verschieben von Daten in und aus Azure Synapse Analytics mithilfe von Azure Data Factory](./data-factory-azure-sql-data-warehouse-connector.md).

Verwenden Sie eine Dropdownliste, um eine Spalte aus dem Quellschema auszuwählen, die einer Spalte im Zielschema zugeordnet werden soll. Der Kopier-Assistent versucht, das Muster für die Spaltenzuordnung zu verstehen. Er wendet das gleiche Muster auf die restlichen Spalten an, damit Sie nicht jede Spalte einzeln auswählen müssen, um die Schemazuordnung abzuschließen. Falls gewünscht, können Sie diese Zuordnungen überschreiben, indem Sie mithilfe der Dropdownlisten die Spalten einzeln zuordnen. Das Muster wird genauer, wenn Sie mehr Spalten zugeordnet haben. Der Kopier-Assistent aktualisiert das Muster ständig und erreicht letztlich das richtige Muster für die Spaltenzuordnung, die Sie erreichen möchten.     

![Schemazuordnung](./media/data-factory-copy-wizard/schema-mapping.png)

## <a name="filtering-data"></a>Filtern von Daten
Sie können Quelldaten filtern, wenn nur bestimmte Daten in den Senkendatenspeicher kopiert werden sollen. Durch Filtern wird das Datenvolumen der in den Senkendatenspeicher kopierten Daten verringert. Der Durchsatz des Kopiervorgangs steigt. Der Assistent bietet flexible Möglichkeiten, die Daten einer relationalen Datenbank mittels der SQL-Abfragesprache oder Dateien in einem Azure-Blobordner mittels [Data Factory-Funktionen und -Variablen](data-factory-functions-variables.md) zu filtern.   

### <a name="filtering-of-data-in-a-database"></a>Filtern von Datenbankdaten
Der folgende Screenshot zeigt eine SQL-Abfrage mit der Funktion `Text.Format` und der Variablen `WindowStart`.

![Überprüfen von Ausdrücken](./media/data-factory-copy-wizard/validate-expressions.png)

### <a name="filtering-of-data-in-an-azure-blob-folder"></a>Filtern von Daten in einem Azure-Blobordner
Sie können im Ordnerpfad Variablen verwenden, um Daten aus einem Ordner zu kopieren, der zur Laufzeit auf der Grundlage von [Systemvariablen](data-factory-functions-variables.md#data-factory-system-variables) bestimmt wird. Folgende Variablen werden unterstützt: **{year}** , **{month}** , **{day}** , **{hour}** , **{minute}** und **{custom}** . Beispiel: Eingabeordner/{year}/{month}/{day}.

Angenommen, Sie haben Eingabeordner im folgenden Format:

```text
2016/03/01/01
2016/03/01/02
2016/03/01/03
...
```

Klicken Sie auf die Schaltfläche **Durchsuchen** für **Datei oder Ordner**, navigieren Sie zu einem der Ordner (z.B. 2016->03->01->02), und klicken Sie auf **Auswählen**. Das Textfeld sollte jetzt `2016/03/01/02` enthalten. Ersetzen Sie nun **2016** durch **{year}** , **03** durch **{month}** , **01** durch **{day}** , **02** durch **{hour}** , und drücken Sie die **TAB**-TASTE. Daraufhin werden Dropdownlisten zum Auswählen des Formats für diese vier Variablen angezeigt:

![Verwenden von Systemvariablen](./media/data-factory-copy-wizard/blob-standard-variables-in-folder-path.png)   

Sie können auch eine **benutzerdefinierte** Variable und beliebige [unterstützte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings)verwenden (siehe Screenshot unten). Um einen Ordner mit dieser Struktur auszuwählen, verwenden Sie zunächst die Schaltfläche **Durchsuchen** . Ersetzen Sie dann einen Wert mit **{custom}** , und drücken Sie die **TAB**-TASTE, um das Textfeld anzuzeigen, in dem Sie die Formatzeichenfolge eingeben können.     

![Verwenden von benutzerdefinierten Variablen](./media/data-factory-copy-wizard/blob-custom-variables-in-folder-path.png)

## <a name="scheduling-options"></a>Planungsoptionen
Sie können den Kopiervorgang einmal oder nach einem Zeitplan (stündlich, täglich usw.) ausführen. Beide Optionen können für die verschiedensten Connectors in unterschiedlichen Umgebungen verwendet werden, einschließlich lokaler Kopien, Cloudkopien und Kopien auf dem lokalen Desktop.

Ein einmaliger Kopiervorgang ermöglicht nur einmal das Verschieben von Daten aus einer Quelle in ein Ziel. Er gilt für Daten jeder Größe in jedem unterstützten Format. Das Kopieren nach einem Zeitplan ermöglicht Ihnen das Kopieren von Daten nach einem vorgeschriebenen Wiederholungszyklus. Sie können umfangreiche Einstellungen (wie Wiederholen, Timeout, Warnungen usw.) nutzen, um das Kopieren nach einem Zeitplan zu konfigurieren.

![Planen von Eigenschaften](./media/data-factory-copy-wizard/scheduling-properties.png)

## <a name="troubleshooting"></a>Problembehandlung

In diesem Abschnitt werden die gängigen Problembehandlungsmethoden für den Kopier-Assistenten in Azure Data Factory beschrieben.

> [!NOTE] 
> Diese Tipps zur Problembehandlung beziehen sich auf den Kopier-Assistenten in Version 1 von Data Factory. Informationen für Data Factory-Version 2 finden Sie im Leitfaden zur Problembehandlung unter [Problembehandlung für Azure Data Factory](../data-factory-ux-troubleshoot-guide.md).

### <a name="error-code-unable-to-validate-in-copy-wizard"></a>Fehlercode: Unable to validate in Copy Wizard (Kann nicht im Kopier-Assistenten überprüft werden)

- **Symptome:** Im ersten Schritt des Kopier-Assistenten tritt die Warnung „Unable to validate“ (Kann nicht überprüft werden) auf.
- **Ursachen**: Dies könnte auftreten, wenn alle Drittanbietercookies deaktiviert sind.
- **Lösung:** 
    - Verwenden der Browser „Internet Explorer“ und „Microsoft Edge“.
    - Wenn Sie den Chrome-Browser verwenden, befolgen Sie die Anleitung unten, um unten Ausnahmen für Cookies für *microsoftonline.com* und *windows.net* hinzuzufügen.
        1.  Öffnen Sie den Chrome-Browser.
        2.  Klicken Sie auf das Schraubendrehersymbol bzw. auf die drei Linien auf der rechten Seite (Customize and control Google Chrome (Anpassen und Steuern von Google Chrome)).
        3.  Klicken Sie auf **Einstellungen**.
        4.  Suchen Sie nach **Cookies**, oder navigieren Sie unter „Advanced Settings“ (Erweiterte Einstellungen) zu **Privacy** (Datenschutz).
        5.  Klicken Sie auf **Content Settings** (Inhaltseinstellungen).    
        6.  Cookies sollten auf **allow local data to be set (recommended)** (lokale Daten dürfen festgelegt werden (empfohlen)) festgelegt werden.
        7.  Klicken Sie auf **Manage exceptions** (Ausnahmen verwalten). Geben Sie unter **hostname pattern** (Hostnamemuster) Folgendes ein, und sorgen Sie dafür, dass für das Verhalten **Allow** (Erlauben) festgelegt ist.
            - login.microsoftonline.com
            - login.windows.net
        8.  Schließen Sie den Browser, und starten Sie ihn neu.
    - Wenn Sie den Firefox-Browser verwenden, befolgen Sie die Anleitung unten, um Cookieausnahmen hinzuzufügen.
        1. Navigieren Sie im Firefox-Menü zu **Tools** > **Options** (Tools > Optionen).
        2. Unter **Privacy** > **History** (Datenschutz > Verlauf) wird möglicherweise angezeigt, dass die aktuelle Einstellung **Use Custom settings for history** (Benutzerdefinierte Einstellungen für den Verlauf verwenden) lautet.
        3. Unter **Accept third-party cookies** (Cookies von Drittanbietern akzeptieren) könnte aktuell die Einstellung **Never** (Nie) festgelegt sein. In diesem Fall sollten Sie rechts auf **Exceptions** (Ausnahmen) klicken, um folgende Websites hinzuzufügen.
            - https://login.microsoftonline.com
            - https://login.windows.net
        4.  Schließen Sie den Browser, und starten Sie ihn neu. 


### <a name="error-code-unable-to-open-login-page-and-enter-password"></a>Fehlercode: Unable to open login page and enter password (Das Öffnen der Anmeldeseite und das Eingeben eines Kennworts ist nicht möglich)

- **Symptome:** Der Kopier-Assistent leitet Sie zur Anmeldeseite weiter. Diese wird jedoch nicht erfolgreich angezeigt.
- **Ursachen**: Dieses Problem kann auftreten, wenn Sie die Netzwerkumgebung von einem Büronetzwerk in ein privates Netzwerk geändert haben. In Browsern gibt es Zwischenspeicher. 
- **Lösung:** 
    1.  Schließen Sie den Browser, und versuchen Sie es noch mal. Fahren Sie mit dem nächsten Schritt fort, wenn das Problem weiterhin besteht.   
    2.  Wenn Sie den Internet Explorer verwenden, versuchen Sie, ihn im privaten Modus zu öffnen. Drücken Sie dazu STRG + UMSCHALT + P. Wenn Sie Chrome verwenden, versuchen Sie, ihn im Inkognitomodus zu öffnen. Drücken Sie dazu STRG + UMSCHALT + N. Fahren Sie mit dem nächsten Schritt fort, wenn das Problem weiterhin besteht. 
    3.  Verwenden Sie einen anderen Browser. 


## <a name="next-steps"></a>Nächste Schritte
Eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mit einer Kopieraktivität unter Verwendung des Data Factory-Kopier-Assistenten finden Sie im [Tutorial: Erstellen einer Pipeline mithilfe des Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md).