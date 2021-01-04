---
title: Verwenden von R mit Machine Learning Studio (Classic) – Azure
description: Absolvieren Sie dieses Tutorial zur Programmierung in R, um einen Einstieg in Azure Machine Learning Studio (Classic) zum Erstellen einer Vorhersagelösung in R zu erhalten.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: bca2f0229a15f44ff8f3589a9c1e80032036b97c
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507216"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Erste Schritte mit Azure Machine Learning Studio (Classic) in R

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

<!-- Stephen F Elston, Ph.D. -->
In diesem Tutorial erfahren Sie, wie Sie mit Azure Machine Learning Studio (Classic) R-Code schreiben, testen und ausführen. Ziel ist es, eine komplette Vorhersagelösung zu erstellen.

> [!div class="checklist"]
> * Erstellen von Code für die Datenbereinigung und -transformation
> * Analysieren der Korrelationen zwischen mehreren Variablen in Ihrem Dataset
> * Erstellen eines saisonalen Zeitreihen-Vorhersagemodells für die Produktion von Milcherzeugnissen


Machine Learning Studio (Classic) enthält viele leistungsfähige Module für maschinelles Lernen und Datenbearbeitung. In Kombination mit der Programmiersprache R profitieren Benutzer von der Skalierbarkeit und einfachen Bereitstellung von Machine Learning Studio (Classic) und der Flexibilität und den tiefgreifenden Analysefunktionen von R.

Vorhersagen stellen eine verbreitet angewendete und nützliche Analysemethode dar. Allgemeine Anwendungen reichen von der Vorhersage der Umsätze für Saisonartikel, über die Bestimmung der optimalen Lagerbestände bis hin zur Vorhersage makroökonomischer Variablen. Vorhersagen werden normalerweise mit Zeitreihenmodellen erstellt. Zeitreihendaten sind Daten, deren Werte einen Zeitindex besitzen. Der Zeitindex kann regelmäßig sein, z. B. monatlich oder jede Minute. Der Zeitindex kann auch unregelmäßig sein. Ein Zeitreihenmodell basiert auf Zeitreihendaten. Die Programmiersprache R enthält ein flexibles Framework sowie umfassende Analysefunktionen für Zeitreihendaten.

## <a name="get-the-data"></a>Abrufen von Daten

In diesem Tutorial verwenden Sie die Daten zur Herstellung und den Preisen von Milchprodukten in Kalifornien. Diese enthalten monatliche Informationen zur Produktion mehrerer Milcherzeugnisse sowie zum Preis von Milchfett, einem Vergleichsrohstoff.

Die in diesem Artikel verwendeten Daten und die R-Skripts können unter [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples) heruntergeladen werden. Die Daten in der Datei `cadairydata.csv` wurden ursprünglich aus Informationen zusammengestellt, die von der University of Wisconsin auf der [Website der Milchmärkte](https://dairymarkets.com) zur Verfügung gestellt wurden.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Interagieren mit der Sprache R in Machine Learning Studio (klassisch)

Dieser Abschnitt führt Sie durch einige Grundlagen der Interaktion mit der Programmiersprache R in der Umgebung der klassischen Version von Machine Learning Studio. Die Sprache R stellt ein leistungsfähiges Tool zum Erstellen benutzerdefinierter Analyse- und Datenbearbeitungsmodule innerhalb der Umgebung von Machine Learning Studio (Classic) bereit.

Wir verwenden RStudio, um R-Code in geringem Umfang zu entwickeln, zu testen und zu debuggen. Dieser Code wird dann ausgeschnitten und in ein ausführbereites Modul [R-Skript ausführen][execute-r-script] in Machine Learning Studio (Classic) eingefügt.

### <a name="the-execute-r-script-module"></a>Das "Execute R Script"-Modul

In Machine Learning Studio (klassisch) werden R-Skripts innerhalb des Moduls [R-Script ausführen][execute-r-script] ausgeführt. Ein Beispiel für das Modul [R-Script ausführen][execute-r-script] in Machine Learning Studio (Classic) ist hier dargestellt.

 ![Screenshot: Programmiersprache R: In Machine Learning Studio (Classic) wurde das Modul „R-Skript ausführen“ ausgewählt.](./media/r-quickstart/fig1.png)

In der obigen Abbildung sind einige der Hauptteile der Umgebung von Machine Learning Studio (Classic) für das Arbeiten mit dem Modul [R-Skript ausführen][execute-r-script] dargestellt.

* Die Module in dem Experiment werden im mittleren Bereich angezeigt.
* Der obere Teil des rechten Bereichs enthält ein Fenster zum Anzeigen und Bearbeiten Ihrer R-Skripts.
* Im unteren Teil des rechten Bereichs werden einige Eigenschaften des Moduls [R-Skript ausführen][execute-r-script] gezeigt. Sie können die Fehler- und Ausgabeprotokolle anzeigen, indem Sie auf die entsprechenden Stellen in diesem Bereich klicken.

Das Modul [R-Skript ausführen][execute-r-script] wird im Verlauf dieses Artikels noch ausführlicher behandelt.

Bei der Arbeit mit komplexen R-Funktionen sollten Sie das Bearbeiten, Testen und Debuggen in RStudio durchführen. Wie bei jeder Softwareentwicklung erweitern Sie Ihren Code schrittweise und testen ihn mit kleinen, einfachen Testfällen. Danach schneiden Sie Ihre Funktionen aus und fügen sie in das Fenster des Moduls [R-Skript ausführen][execute-r-script] ein. Dieser Ansatz erlaubt es Ihnen, sowohl die RStudio-IDE (Integrated Development Environment, integrierte Entwicklungsumgebung) als auch die Leistungsfähigkeit von Machine Learning Studio (Classic) zu nutzen.

#### <a name="execute-r-code"></a>Ausführen von R-Code

Der R-Code im Modul [R-Skript ausführen][execute-r-script] wird ausgeführt, wenn Sie das Experiment über die Schaltfläche **Ausführen** ausführen. Nach Abschluss der Ausführung wird auf dem Symbol [R-Skript ausführen][execute-r-script] ein Häkchen angezeigt.

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Defensive R-Codierung für Machine Learning Studio (Classic)

Wenn Sie R-Code beispielsweise für einen Webdienst mithilfe von Machine Learning Studio (Classic) entwickeln, sollten Sie auf jeden Fall planen, wie Ihr Code eine unerwartete Dateneingabe und Ausnahmen behandeln soll. Der Übersichtlichkeit wegen wurden in die meisten der dargestellten Codebeispiele kaum Testverfahren oder Verfahren zur Behandlung von Ausnahmen aufgenommen. Im weiteren Verlauf erhalten Sie jedoch einige Beispiele für Funktionen, die die Ausnahmebehandlungsfunktionen von R verwenden.

Wenn Sie eine umfassendere Erläuterung zur Ausnahmebehandlung in R benötigen, lesen Sie die entsprechenden Abschnitte des Buchs von Wickham, das unter [Weitere Informationen](#appendixb) aufgeführt ist.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Debuggen und Testen von R in Machine Learning Studio (klassisch)

Testen und Debuggen Sie Ihren R-Code in geringem Umfang in RStudio. Es gibt Fälle, in denen Sie Probleme im R-Code im Modul [R-Skript ausführen][execute-r-script] selbst ermitteln müssen. Darüber hinaus hat es sich bewährt, die eigenen Ergebnisse in Machine Learning Studio (Classic) zu überprüfen.

Ausgaben der Ausführung Ihres R-Codes und von der Machine Learning Studio-Plattform (Classic) finden sich hauptsächlich in der Datei „output.log“. Einige zusätzliche Informationen finden Sie auch in der Datei „error.log“.

Tritt in Machine Learning Studio (Classic) ein Fehler während der Ausführung Ihres R-Codes auf, sollten Sie als Erstes in der Datei „error.log“ nachsehen. Die Datei "error.log" kann nützliche Fehlermeldungen enthalten, die Ihnen dabei helfen, den Fehler zu verstehen und zu korrigieren. Klicken Sie im Bereich „Eigenschaften“ für das Modul [R-Skript ausführen][execute-r-script] auf **Fehlerprotokoll anzeigen**, um die Datei „error.log“ anzuzeigen.

Wir haben beispielsweise im Modul [R-Skript ausführen][execute-r-script] den folgenden R-Code mit der nicht definierten Variable „y“ ausgeführt.

```r
x <- 1.0
z <- x + y
```

Dieser Code kann nicht ausgeführt werden, was zu einem Fehlerzustand führt. Wenn Sie im Bereich „Eigenschaften“ auf **Fehlerprotokoll anzeigen** klicken, wird Folgendes angezeigt.

  ![Screenshot: Popupelement mit Fehlermeldung](./media/r-quickstart/fig2.png)

Sie müssen sich anscheinend in der Datei "output.log" die R-Fehlermeldung ansehen. Klicken Sie auf das Modul [R-Skript ausführen][execute-r-script], und wählen Sie rechts im Bereich „Eigenschaften“ das Element **View output.log** (output.log anzeigen) aus. Ein neues Browserfenster wird geöffnet, und die folgende Fehlermeldung wird angezeigt.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Diese Fehlermeldung enthält keine Überraschungen, und das Problem wird eindeutig identifiziert.

Um den Wert eines beliebigen Objekts in R zu überprüfen, können Sie diese Werte in die Datei "output.log" ausgeben. Die Regeln zum Untersuchen von Objektwerten sind im Wesentlichen mit denen in einer interaktiven R-Sitzung identisch. Wenn Sie beispielsweise einen Variablennamen in eine Zeile eingeben, wird der Wert des Objekts in die Datei „output.log“ ausgegeben.

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakete in Machine Learning Studio (klassisch)

Machine Learning Studio (Classic) verfügt über mehr als 350 vorinstallierte R-Sprachpakete. Sie können im Modul [R-Skript ausführen][execute-r-script] den folgenden Code verwenden, um eine Liste der vorinstallierten Pakete abzurufen.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Wenn Sie die letzte Zeile des Codes im Moment noch nicht verstehen, lesen Sie trotzdem weiter. Die Verwendung von R in der Umgebung von Machine Learning Studio (Classic) wird in diesem Artikel noch ausführlicher erläutert.

### <a name="introduction-to-rstudio"></a>Einführung in RStudio

RStudio ist eine weit verbreitete IDE für R. Wir verwenden RStudio, um R-Code in dieser Anleitung zu bearbeiten, zu testen und zu debuggen. Nachdem der R-Code getestet wurde und bereit ist, können Sie ihn im RStudio-Editor ausschneiden und in ein [R-Skript ausführen][execute-r-script]-Modul in Machine Learning Studio (Classic) einfügen.

Wenn die Programmiersprache R nicht auf Ihrem Desktopcomputer installiert ist, holen Sie das jetzt nach. Kostenlose Downloads der Open Source-Sprache R finden Sie in [CRAN (Comprehensive R Archive Network)](https://www.r-project.org/). Downloads sind für Windows, macOS und Linux/UNIX verfügbar. Wählen Sie einen Spiegelserver in Ihrer Nähe aus, und befolgen Sie die Downloadanweisungen. Zusätzlich finden Sie im CRAN zahlreiche nützliche Analyse- und Datenbearbeitungspakete.

Wenn Sie noch keine Erfahrungen mit RStudio haben, sollten Sie die Desktopversion herunterladen und installieren. Die RStudio-Downloads für Windows, macOS und Linux/UNIX finden Sie unter [RStudio](http://www.rstudio.com/products/RStudio/). Befolgen Sie die bereitgestellten Anweisungen zum Installieren von RStudio auf Ihrem Desktopcomputer.

Eine Tutorialeinführung in RStudio finden Sie unter [Using the RStudio IDE (Verwenden der RStudio-IDE)](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Weitere Informationen zur Verwendung von RStudio finden Sie in der [Anleitung zur RStudio-Dokumentation](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Durchführen von Dateneingaben und -ausgaben im "Execute R Script"-Modul

In diesem Abschnitt wird beschrieben, wie Sie Daten im Modul [R-Skript ausführen][execute-r-script] ein- und ausgeben können. Zudem erfahren Sie, wie verschiedene Datentypen, die vom Modul [R-Skript ausführen][execute-r-script] ein- und ausgelesen werden, zu handhaben sind.

Der vollständige Code für diesen Abschnitt befindet sich unter [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Laden und Überprüfen der Daten

#### <a name="load-the-dataset"></a><a id="loading"></a>Laden des DataSets

Wir beginnen damit, dass wir die Datei **csdairydata.csv** in Machine Learning Studio (Classic) laden.

1. Starten Sie Ihre Umgebung von Machine Learning Studio (Classic).
1. Klicken Sie unten links auf dem Bildschirm auf **+ Neu**, und wählen Sie **Dataset** aus.
1. Klicken Sie auf **Aus lokaler Datei** und anschließend auf **Durchsuchen**, um die Daten auszuwählen.
1. Wählen Sie **Generische CSV-Datei mit Header (.csv)** als Dataset-Typ aus.
1. Aktivieren Sie das Kontrollkästchen.
1. Nachdem das Dataset hochgeladen wurde, können Sie es anzeigen, indem Sie auf die Registerkarte **Datasets** klicken.

#### <a name="create-an-experiment"></a>Erstellen eines Experiments

Nachdem wir nun einige Daten in Machine Learning Studio (Classic) geladen haben, erstellen wir ein Experiment, um die Analyse durchführen zu können.  

1. Klicken Sie unten links auf dem Bildschirm auf **+ Neu**, und wählen Sie **Experiment** > **Leeres Experiment** aus.
1. Geben Sie dem Experiment einen Namen, indem Sie oben auf der Seite den Titel **Experiment erstellt am** auswählen und bearbeiten. Ändern Sie den Titel beispielsweise in **CA Dairy Analysis**.
1. Wählen Sie links auf der Experimentseite **Gespeicherte Datasets** > **Meine Datasets** aus. Hier wird die Datei **cadairydata.csv** angezeigt, die Sie zuvor hochgeladen haben.
1. Ziehen Sie das Dataset **csdairydata.csv** in das Experiment.
1. Geben Sie links oben in das Feld **Search experiment items** (Experimentelemente suchen) als Suchbegriff [R-Skript ausführen][execute-r-script] ein. Daraufhin wird das Modul in der Suchliste angezeigt.
1. Ziehen Sie das Modul [R-Skript ausführen][execute-r-script] auf Ihre Palette.
1. Verbinden Sie die Ausgabe von **csdairydata.csv** mit der Eingabe links außen (**Dataset1**) von [R-Skript ausführen][execute-r-script].
1. Wählen Sie **Speichern**.

Jetzt sollte das Experiment ungefähr wie in diesem Beispiel aussehen.

![Abbildung: Das Experiment „CA Dairy Analysis“ mit Dataset und dem Modul „R-Skript ausführen“](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Überprüfen der Daten

Lassen Sie uns einen Blick auf die Daten werfen, die wir in unser Experiment geladen haben. Klicken Sie im Experiment auf die Ausgabe des Datasets **cadairydata.csv**, und wählen Sie **Visualisieren** aus. Die Ausgabe sollte in etwa wie diese Zusammenfassung aussehen.

![Abbildung: Zusammenfassung des Datasets „cadairydata.csv“](./media/r-quickstart/fig4.png)

In dieser Ansicht werden zahlreiche nützliche Informationen angezeigt. Wir sehen die ersten Zeilen dieses Datasets. Wenn wir eine Spalte auswählen, werden im Abschnitt **Statistik** weitere Informationen zu der Spalte angezeigt. In der Zeile **Funktionstyp** wird beispielsweise angezeigt, welche Datentypen Machine Learning Studio (Classic) den Spalten zuweist. Sehen Sie sich diese Anzeige an, bevor Sie mit der eigentlichen Arbeit beginnen.

### <a name="first-r-script"></a>Erstes R-Skript

Lassen Sie uns zuerst ein einfaches erstes R-Skript erstellen, um damit in Machine Learning Studio (Classic) zu experimentieren. Wir haben folgendes Skript in RStudio erstellt und getestet.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Jetzt müssen wir dieses Skript in Machine Learning Studio (Classic) übertragen. Sie können das R-Skript ausschneiden und einfügen. In diesem Fall übertragen Sie es jedoch in Form einer ZIP-Datei.

### <a name="data-input-to-the-execute-r-script-module"></a>Dateneingabe in das "Execute R Script"-Modul

Sehen Sie sich die Eingaben für das Modul [R-Skript ausführen][execute-r-script] an. In diesem Beispiel lesen wir die kalifornischen Milchproduktdaten in das Modul [R-Skript ausführen][execute-r-script] ein.

Es gibt drei mögliche Eingaben für das Modul [R-Skript ausführen][execute-r-script]. Sie können je nach Anwendung eine oder alle Eingaben verwenden. Sie können auch ein R-Skript verwenden, das überhaupt keine Eingaben unterstützt.

Betrachten wir nun jede dieser Eingaben von links nach rechts. Sie können den Namen jeder einzelnen Eingabe anzeigen, indem Sie mit dem Mauszeiger auf die Eingabe zeigen und die QuickInfo lesen.

#### <a name="script-bundle"></a>Script Bundle

Die Eingabe „Script Bundle“ erlaubt es Ihnen, den Inhalt einer ZIP-Datei an das Modul [R-Skript ausführen][execute-r-script] zu übergeben. Sie können einen der folgenden Befehle verwenden, um den Inhalt der ZIP-Datei in Ihren R-Code einzulesen:

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (Classic) behandelt Dateien im ZIP-Archiv, als befänden sie sich im Verzeichnis „src/“, weshalb Sie Ihren Dateinamen diesen Ordnernamen als Präfix voranstellen müssen. Wenn der Stamm der ZIP-Datei beispielsweise die Dateien `yourfile.R` und `yourData.rdata` enthält, würden Sie diese bei Verwendung von `source` und `load` als `src/yourfile.R` und `src/yourData.rdata` adressieren.

Das Laden von Datasets wurde bereits unter [Laden des Datasets](#loading) erläutert. Nachdem Sie das im vorherigen Abschnitt gezeigte R-Skript erstellt und getestet haben, führen Sie folgende Schritte aus.

1. Speichern Sie das R-Skript in einer ".R"-Datei. Wir geben der Skriptdatei den Namen **simpleplot.R**. Die Datei enthält Folgendes:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Erstellen Sie eine ZIP-Datei, und kopieren Sie Ihr Skript in diese ZIP-Datei. Unter Windows können Sie mit der rechten Maustaste auf die Datei klicken, und **Senden an** > **Komprimierter Ordner** auswählen. Dadurch wird eine neue ZIP-Datei erstellt, die die Datei **simpleplot.R** enthält.

1. Fügen Sie Ihre Datei den **Datasets** in Machine Learning Studio (Classic) hinzu, und geben Sie dabei den Typ **zip** an. Die ZIP-Datei sollte jetzt in Ihren DataSets angezeigt werden.

1. Ziehen Sie die ZIP-Datei aus **Datasets** in den **Bereich von ML Studio (Classic)** .

1. Verbinden Sie die Ausgabe des **ZIP-Daten**-Symbols mit der Eingabe **Script Bundle** des Moduls [R-Skript ausführen][execute-r-script].

1. Geben Sie die `source()`-Funktion mit dem Namen Ihrer ZIP-Datei in das Codefenster des Moduls [R-Skript ausführen][execute-r-script] ein. In diesem Fall geben Sie `source("src/simpleplot.R")` ein.

1. Wählen Sie **Speichern**.

Nachdem Sie diese Schritte durchgeführt haben, wird das R-Skript aus der ZIP-Datei beim Ausführen des Experiments vom Modul [R-Skript ausführen][execute-r-script] ausgeführt. Jetzt sollte das Experiment ungefähr wie in diesem Beispiel aussehen.

![Abbildung: Experiment, bei dem ein gezipptes R-Skript verwendet wird](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Dataset1

Sie können mithilfe der Eingabe "Dataset1" eine rechteckige Datentabelle an Ihren R-Code übergeben. In unserem einfachen Skript liest die `maml.mapInputPort(1)`-Funktion die Daten von Port 1. Diese Daten werden dann einem Dataframe-Variablennamen in Ihrem Code zugewiesen. In unserem einfachen Skript führt die erste Zeile des Codes die Zuweisung durch.

```r
cadairydata <- maml.mapInputPort(1)
```

Führen Sie das Experiment aus, indem Sie auf die Schaltfläche **Ausführen** klicken. Wenn die Ausführung abgeschlossen ist, klicken Sie auf [R-Skript ausführen][execute-r-script] und dann im Bereich „Eigenschaften“ auf **View output log** (Ausgabeprotokoll anzeigen). In Ihrem Browser sollte eine neue Seite mit dem Inhalt der Datei "Output.log" angezeigt werden. Wenn Sie nach unten scrollen, sollte etwa die folgende Ausgabe angezeigt werden.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Weiter unten auf der Seite finden Sie ausführlichere Informationen zu den Spalten, die in etwa wie folgende Ausgabe aussehen.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Diese Ergebnisse sind größtenteils wie erwartet, mit 228 Beobachtungen und 9 Spalten im Dataframe. Wir sehen die Spaltennamen, den R-Datentyp und ein Beispiel für jede Spalte.

> [!NOTE]
> Dieselbe Druckausgabe steht bequem über die Ausgabe „R Device“ des Moduls [R-Skript ausführen][execute-r-script] zur Verfügung. Die Ausgaben des Moduls [R-Skript ausführen][execute-r-script] werden im nächsten Abschnitt erklärt.  

#### <a name="dataset2"></a>Dataset2

Das Verhalten der Eingabe "Dataset2" ist mit der von Dataset1 identisch. Mit dieser Eingabe können Sie eine zweite rechteckige Datentabelle an Ihren R-Code übergeben. Die `maml.mapInputPort(2)`-Funktion wird mit dem Argument 2 verwendet, um diese Daten zu übergeben.  

### <a name="execute-r-script-outputs"></a>"Execute R Script"-Ausgaben

#### <a name="output-a-dataframe"></a>Ausgeben eines Dataframes

Sie können den Inhalt eines R-Dataframes mithilfe der `maml.mapOutputPort()` -Funktion als rechteckige Tabelle über den Ergebnisport "Dataset1" ausgeben. In unserem einfachen R-Skript wird diese Aktion durch die folgende Zeile ausgeführt.

```r
maml.mapOutputPort('cadairydata')
```

Klicken Sie nach dem Ausführen des Experiments auf den Ausgabeport „Result Dataset1“ und dann auf **Visualisieren**. Die Ausgabe sollte in etwa wie das folgende Beispiel aussehen.

![Abbildung: Visualisierung der Ausgabe der kalifornischen Milchproduktdaten](./media/r-quickstart/fig7.png)

Diese Ausgabe ist identisch mit der Eingabe, genau wie erwartet.

### <a name="r-device-output"></a>"R Device"-Ausgabe

Die Geräteausgabe des Moduls [R-Skript ausführen][execute-r-script] enthält Meldungen und Grafikausgaben. Sowohl die Standardausgabe als auch die Standardfehlermeldungen von R werden an den "R Device"-Ausgabeport gesendet.

Klicken Sie auf den Port und dann auf **Visualisieren**, um die „R Device“-Ausgabe anzuzeigen. Die Standardausgabe und Standardfehlermeldung des R-Skripts werden wie hier angezeigt.

![Screenshot: Standardausgabe und Standardfehlermeldung des „R Device“-Ports](./media/r-quickstart/fig8.png)

Wenn wir nach unten scrollen, sehen wir die Grafikausgabe unseres R-Skripts.

![Screenshot: Grafikausgabe des „R Device“-Ports](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtern und Transformieren von Daten

In diesem Abschnitt werden wir einige grundlegende Vorgänge zum Filtern und Transformieren von Daten mit den kalifornischen Milchproduktdaten ausführen. Am Ende dieses Abschnitts erhalten wir dann Daten in einem Format, das für die Erstellung eines Analysemodells geeignet ist.

Wir werden in diesem Abschnitt insbesondere mehrere allgemeine Aufgaben zur Datenbereinigung und -transformation durchführen: Typtransformationen, Filtern von Dataframes, Hinzufügen neuer, berechneter Spalten und Werttransformationen. Dieser Hintergrund sollte Ihnen beim Umgang mit den vielen Variationen, mit denen Sie in Szenarios der Realität konfrontiert werden, helfen.

Der vollständige R-Code für diesen Abschnitt befindet sich unter [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Typtransformationen

Nachdem wir nun die kalifornischen Milchproduktdaten in den R-Code im Modul [R-Skript ausführen][execute-r-script] einlesen können, müssen wir sicherstellen, dass die Daten in den Spalten den gewünschten Typ und das gewünschte Format aufweisen.

R ist eine dynamisch typisierte Sprache, d. h., dass die Datentypen ganz nach Bedarf vom einen in den anderen umgewandelt werden. Zu den atomischen Datentypen in R gehören die Typen numerisch, logisch und Zeichen. Der Faktortyp wird verwendet, um Kategoriedaten kompakt zu speichern. Weitere Informationen zu Datentypen finden Sie in den Verweisen unter [Weitere Informationen](#appendixb).

Wenn die Tabellendaten aus einer externen Quelle in R eingelesen werden, empfiehlt es sich, die resultierenden Typen in den Spalten zu überprüfen. Möglicherweise benötigen Sie eine Spalte vom Typ „Zeichen“, die Spalte wird jedoch als „Faktor“ angezeigt oder umgekehrt. Oder die Daten in einer Spalte, die eigentlich numerisch sein sollte, werden als Zeichendaten dargestellt, z. B. „1,23“ anstelle der Gleitkommazahl 1,23.

Glücklicherweise lassen sich die Typen einfach untereinander konvertieren, solange eine Zuordnung möglich ist. Zum Beispiel können Sie „Nevada“ nicht in einen numerischen Wert konvertieren, aber Sie können es in einen Faktor (Kategorievariable) konvertieren. Als weiteres Beispiel können Sie eine numerische 1 in ein Zeichen „1“ oder einen Faktor konvertieren.

Die Syntax für jede dieser Konvertierungen ist einfach: `as.datatype()`. Diese Typkonvertierungsfunktionen umfassen folgende Funktionen:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Wenn wir die Datentypen der Spalten betrachten, die wir im vorherigen Abschnitt eingegeben haben, stellen wir fest, dass alle Spalten vom Typ „numerisch“ sind. Eine Ausnahme stellt die Spalte mit der Bezeichnung „Month“ dar, die vom Typ „Zeichen“ ist. Lassen Sie uns nun diesen Typ in den Typ „Faktor“ konvertieren und die Ergebnisse testen.

Wir haben die Zeile gelöscht, die die Punktdiagrammmatrix erstellt, und eine Zeile hinzugefügt, die die Spalte „Month“ in den Typ „Faktor“ konvertiert. In diesem Experiment schneiden wir den R-Code aus und fügen ihn in das Codefenster des Moduls [R-Skript ausführen][execute-r-script] ein. Sie könnten auch die ZIP-Datei aktualisieren und in Machine Learning Studio (Classic) hochladen. Diese Option erfordert jedoch mehrere Schritte.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Führen wir nun diesen Code aus, und sehen wir uns die Ausgabe des R-Skripts an. Die relevanten Daten aus dem Protokoll werden hier gezeigt.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Der Typ von „Month“ sollte nun „**Factor w/ 14 levels**“ (Faktor mit 14 Ebenen) lauten. Dieser Typ stellt ein Problem dar, weil das Jahr nur 12 Monate hat. Sie können auch überprüfen, ob der Typ in **Visualize** des „Result Dataset“-Ports **Categorical** ist.

Das Problem ist, dass die Spalte „Month“ nicht systematisch codiert wurde. In einigen Fällen wird ein Monat als „April“ bezeichnet, während er in anderen Fällen als „Apr“ abgekürzt wird. Wir können dieses Problem beheben, indem wir die Zeichenfolge auf drei Zeichen kürzen. Die Codezeile sieht nun wie im folgenden Beispiel aus.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Führen Sie das Experiment erneut aus, und zeigen Sie die Ausgabe an. Die erwarteten Ergebnisse sind hier dargestellt.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Unsere Faktor-Variable verfügt jetzt über die gewünschten 12 Ebenen.

### <a name="basic-dataframe-filtering"></a>Grundlegendes Filtern von Dataframes

R-Dataframes unterstützen leistungsstarke Filterfunktionen. Datasets können mithilfe von auf Zeilen oder Spalten angewendeten logischen Filtern in Teilmengen unterteilt werden. In vielen Fällen sind komplexe Filterkriterien erforderlich. Ausführliche Beispiele zum Filtern von Dataframes finden Sie in den Verweisen unter [Weitere Informationen](#appendixb).

Wir sollten unser Dataset ein wenig filtern. Wenn Sie die Spalten im Dataframe „cadariydata“ betrachten, sehen Sie zwei nicht benötigte Spalten. Die erste Spalte enthält eine Zeilennummer, die nicht sehr nützlich ist. Die zweite Spalte, "Year.Month", enthält redundante Informationen. Wir können diese Spalten ganz einfach mithilfe des folgenden R-Codes ausschließen.

> [!NOTE]
> Von nun an zeigen wir Ihnen in diesem Abschnitt nur noch den zusätzlichen Code, den wir im Modul [R-Skript ausführen][execute-r-script] hinzufügen. Alle neuen Zeilen werden *vor* der `str()`-Funktion eingefügt. Wir verwenden diese Funktion zum Überprüfen unserer Ergebnisse in Machine Learning Studio (Classic).

Wir fügen dem R-Code im Modul [R-Skript ausführen][execute-r-script] die folgende Zeile hinzu.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Führen Sie diesen Code in Ihrem Experiment aus, und überprüfen Sie das Ergebnis im Ausgabeprotokoll. Die Ergebnisse sind hier dargestellt.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Wir haben die erwarteten Ergebnisse erhalten.

### <a name="add-a-new-column"></a>Hinzufügen einer neuen Spalte

Beim Erstellen von Zeitreihenmodellen ist es praktisch, wenn eine Spalte vorhanden ist, die die Monate seit dem Start der Zeitreihe enthält. Wir erstellen eine neue „Month.Count“-Spalte.

Zur besseren Strukturierung des Codes erstellen wir unsere erste einfache Funktion: `num.month()`. Anschließend wenden diese Funktion an, um im Dataframe eine neue Spalte zu erstellen. Der neue Code lautet wie folgt:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Führen Sie nun das aktualisierte Experiment aus, und überprüfen Sie die Ergebnisse im Ausgabeprotokoll. Die Ergebnisse sind hier dargestellt.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Es scheint alles zu funktionieren. Wir haben jetzt die neue Spalte mit den erwarteten Werten in unserem Dataframe.

### <a name="value-transformations"></a>Werttransformationen

In diesem Abschnitt führen wir einige einfache Transformationen der Werte in einigen Spalten des Dataframes durch. Die R-Sprache unterstützt nahezu beliebige Werttransformationen. Weitere Beispiele finden Sie in den Verweisen unter [Weitere Informationen](#appendixb).

Wenn Sie sich die Werte in den Zusammenfassungen des Dataframes ansehen, sollte Ihnen etwas merkwürdig vorkommen. Wird in Kalifornien mehr Speiseeis erzeugt, als Milch produziert wird? Nein, natürlich nicht. Das Problem ist, dass sich die Einheiten unterscheiden. Der Preis wird in Einheiten von US-Pfund angegeben, Milch in Einheiten von 1 Million US-Pfund und Speiseeis in Einheiten von 1.000 US-Gallonen sowie Hüttenkäse in Einheiten von 1.000 US-Pfund. Wenn Speiseeis beispielsweise etwa 6,5 Pfund pro Gallone wiegt, können wir ganz einfach eine Multiplikation durchführen, um diese Werte so zu konvertieren, dass sie alle in gleichen Einheiten von 1.000 Pfund angegeben werden.

Für unser Vorhersagemodell verwenden wir ein multiplikatives Modell für die Trend- und saisonale Anpassung dieser Daten. Eine logarithmische Transformation ermöglicht uns, ein lineares Modell zu verwenden, was diesen Prozess vereinfacht. Wir können die logarithmische Transformation in derselben Funktion anwenden, in der der Multiplikator angewendet wird.

Im folgenden Code definieren wir eine neue `log.transform()`-Funktion und wenden sie auf die Zeilen an, die die numerischen Werte enthalten. Die R-Funktion `Map()` wird verwendet, um die `log.transform()`-Funktion auf die ausgewählten Spalten des Dataframes anzuwenden. Die `Map()`-Funktion ähnelt `apply()`, gestattet der Funktion aber mehr als eine Liste von Argumenten. Beachten Sie, dass eine Liste von Multiplikatoren das zweite Argument für die `log.transform()`-Funktion bereitstellt. Die `na.omit()`-Funktion wird praktisch zur Bereinigung verwendet, um sicherzustellen, dass im Dataframe keine Werte fehlen bzw. keine nicht definierten Werte enthalten sind.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

In der `log.transform()`-Funktion geschieht einiges. Der größte Teil dieses Codes sucht nach potenziellen Problemen mit den Argumenten oder behandelt Ausnahmen, die bei den Berechnungen immer noch auftreten können. Es sind nur einige Zeilen dieses Codes, in denen die Berechnungen durchgeführt werden.

Ziel der defensiven Programmierung ist es, den Ausfall einer einzelnen Funktion zu verhindern, durch den die Fortsetzung der Verarbeitung verhindert werden könnte. Ein abruptes Fehlschlagen einer lang andauernden Analyse kann für die Benutzer frustrierend sein. Um diese Situation zu vermeiden, müssen Standardrückgabewerte ausgewählt werden, die Schäden an der Downstreamverarbeitung begrenzen. Es wird auch eine Nachricht zur Benachrichtigung der Benutzer erzeugt, dass ein Fehler aufgetreten ist.

Wenn Sie mit der defensiven Programmierung in R nicht vertraut sind, kann dieser Code ein wenig verwirrend sein. Die wichtigsten Schritte:

1. Ein Vektor aus vier Meldungen wird definiert. Diese dienen der Kommunikation von Informationen über einige der möglichen Fehler und Ausnahmen, die bei diesem Code auftreten können.
1. Wir geben für jeden Fall den Wert „NA“ zurück. Es gibt viele weitere Möglichkeiten, die eventuell weniger Nebeneffekte haben. Wir könnten beispielsweise einen Vektor aus Nullen zurückgeben oder den ursprünglichen Eingabevektor.
1. Es werden Tests mit den Argumenten der Funktion ausgeführt. In jedem Fall wird beim Erkennen eines Fehlers ein Standardwert zurückgegeben und von der `warning()`-Funktion eine Meldung erzeugt. Wir verwenden `warning()` anstelle von `stop()`, da die letztere Funktion die Ausführung beendet – und genau das versuchen wir zu vermeiden. Dieser Code wurde in einem prozeduralen Stil geschrieben, da in diesem Fall ein funktionaler Ansatz zu komplex und undurchsichtig erschien.
1. Die logarithmischen Berechnungen sind in `tryCatch()` eingeschlossen, damit Ausnahmen keine abrupte Unterbrechung der Verarbeitung verursachen können. Ohne `tryCatch()` erzeugen die meisten von R-Funktionen ausgelösten Fehler ein Stoppsignal (Beenden), was genau dies bewirkt.

Führen Sie diesen R-Code in Ihrem Experiment aus, und sehen Sie sich die Ausgabe in der Datei "output.log" an. Die transformierten Werte der vier Spalten werden im Protokoll wie hier dargestellt angezeigt.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Wir sehen, dass die Werte transformiert wurden. Die Milchproduktion überschreitet nun erheblich die Produktion aller anderen Milchprodukte, wobei Sie sich in Erinnerung rufen müssen, dass wir jetzt eine logarithmische Skalierung verwenden.

Jetzt sind unsere Daten bereinigt, und wir können mit der Modellierung beginnen. Wenn Sie die Visualisierungszusammenfassung für die Ausgabe „Result Dataset“ unseres Moduls [R-Skript ausführen][execute-r-script] betrachten, stellen Sie fest, dass die Spalte „Month“ genau wie beabsichtigt „Categorical“ mit 12 eindeutigen Werten ist.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Zeitreihenobjekte und Korrelationsanalyse

In diesem Abschnitt behandeln wir ein paar grundlegende R-Zeitreihenobjekte und analysieren die Korrelationen zwischen einigen der Variablen. Unser Ziel ist es, ein Dataframe auszugeben, das die paarweisen Korrelationsinformationen an mehreren Verzögerungen enthält.

Der vollständige R-Code für diesen Abschnitt befindet sich unter [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Zeitreihenobjekte in R

Wie bereits erwähnt, sind Zeitreihen Reihen von Datenwerten, die einen Zeitindex besitzen. R-Zeitreihenobjekte dienen zum Erstellen und Verwalten des Zeitindex. Es gibt mehrere Vorteile bei der Verwendung von Zeitreihenobjekten. Zeitreihenobjekte übernehmen für Sie zahlreiche Details der Verwaltung der Zeitreihen-Indexwerte, die im Objekt gekapselt sind. Darüber hinaus ermöglichen Zeitreihenobjekte die Verwendung der zahlreichen Zeitreihenmethoden zum Plotten, Drucken, Modellieren usw.

Die Zeitreihenklasse "POSIXct" wird häufig verwendet und ist relativ einfach. Diese Zeitreihenklasse misst die Zeit seit Beginn der Epoche, 1. Januar 1970. Wir verwenden in diesem Beispiel POSIXct-Zeitreihenobjekte. Es gibt andere weit verbreitete R-Zeitreihen-Objektklassen wie etwa „zoo“ und „xts“, (eXtensible Time Series, erweiterbare Zeitreihe).

### <a name="time-series-object-example"></a>Beispiel für Zeitreihenobjekte

Beginnen wir mit unserem Beispiel. Ziehen Sie ein neues [R-Skript ausführen][execute-r-script]-Modul in Ihr Experiment. Verbinden Sie den Ausgabeport „Result Dataset1“des vorhandenen Moduls [R-Skript ausführen][execute-r-script] mit dem Eingabeport „Dataset1“des neuen Moduls [R-Skript ausführen][execute-r-script].

Wie bei den ersten Beispielen werden wir das Beispiel Schritt für Schritt durchgehen. An einigen Stellen werden wir nur die inkrementellen zusätzlichen Zeilen des R-Codes im jeweiligen Schritt angeben.

#### <a name="read-the-dataframe"></a>Lesen des Dataframes

Als ersten Schritt lesen wir einen Dataframe ein und stellen sicher, dass wir die erwarteten Ergebnisse erhalten. Der folgende Code sollte diese Aufgabe erledigen:

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Führen Sie jetzt das Experiment aus. Das Protokoll für die neue Form von „R-Skript ausführen“ sollte wie hier aussehen.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Diese Daten haben die erwarteten Typen und Formate. Die Spalte „Month“ ist vom Typ „Faktor“ und besitzt die erwartete Anzahl von Ebenen.

#### <a name="create-a-time-series-object"></a>Erstellen eines Zeitreihenobjekts

Wir müssen unserem Dataframe ein Zeitreihenobjekt hinzufügen. Ersetzen Sie den aktuellen Code durch den folgenden Code, der eine neue Spalte der „POSIXct“-Klasse hinzufügt.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Prüfen Sie jetzt das Protokoll. Es sollte wie im folgenden Beispiel aussehen.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

In der Zusammenfassung können wir sehen, dass die neue Spalte tatsächlich zur POSIXct-Klasse gehört.

### <a name="explore-and-transform-the-data"></a>Untersuchen und Transformieren der Daten

Lassen Sie uns einige der Variablen in diesem DataSet untersuchen. Eine Punktdiagrammmatrix ist eine gute Möglichkeit, um einen schnellen Einblick zu gewinnen. Wir ersetzen die `str()`-Funktion im vorherigen R-Code durch die folgende Zeile.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Führen Sie diesen Code aus, und sehen Sie, was passiert. Die am „R Device“-Port erzeugte Grafik sollte wie in diesem Beispiel aussehen.

![Screenshot: Punktdiagrammmatrix der ausgewählten Variablen](./media/r-quickstart/fig17.png)

Die Beziehungen zwischen diesen Variablen weisen eine seltsam aussehende Struktur auf. Möglicherweise wird diese Struktur von Trends in den Daten und durch die Tatsache erzeugt, dass wir die Variablen nicht standardisiert haben.

### <a name="correlation-analysis"></a>Korrelationsanalyse

Um die Korrelationsanalyse durchzuführen, müssen wir Trends aus den Variablen entfernen und sie standardisieren. Wir könnten einfach die R-Funktion `scale()` verwenden, die Variablen sowohl zentriert als auch skaliert. Diese Funktion dürfte schneller ausgeführt werden. Sehen wir uns ein Beispiel für defensive Programmierung in R an.

Die folgende `ts.detrend()`-Funktion führt diesen beiden Vorgänge aus. Die folgenden zwei Codezeilen entfernen Trends aus den Daten und standardisieren dann die Werte.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

In der `ts.detrend()`-Funktion geschieht einiges. Der größte Teil dieses Codes sucht nach potenziellen Problemen mit den Argumenten oder behandelt Ausnahmen, die bei den Berechnungen immer noch auftreten können. Es sind nur einige Zeilen dieses Codes, in denen die tatsächlichen Berechnungen durchgeführt werden.

Wir haben unter „Werttransformationen“ bereits ein Beispiel für die defensive Programmierung besprochen. Beide Berechnungsblöcke werden in `tryCatch()` eingeschlossen. Bei einigen Fehlern ist es sinnvoll, den ursprünglichen Eingabevektor zurückzugeben. In anderen Fällen wird dagegen ein Vektor aus Nullen zurückgegeben.

Beachten Sie, dass die für die Beseitigung von Trends verwendete lineare Regression eine Zeitreihenregression ist. Die Vorhersagevariable ist ein Zeitreihenobjekt.

Nachdem `ts.detrend()` definiert wurde, wenden wir die Funktion auf die für uns interessanten Variablen in unserem Dataframe an. Wir müssen die resultierende Liste, die von `lapply()` erstellt wurde, mithilfe von `as.data.frame()` in das Dataframe umwandeln. Aufgrund der defensiven Aspekte von `ts.detrend()`verhindert ein Fehler bei der Verarbeitung einer der Variablen nicht die ordnungsgemäße Verarbeitung der anderen.

Die letzte Zeile des Codes erstellt ein paarweises Punktdiagramm. Nach der Ausführung des R-Codes werden die Ergebnisse des Punktdiagramms hier dargestellt.

![Screenshot: paarweises Punktdiagramm der von Trends befreiten und standardisierten Zeitreihe](./media/r-quickstart/fig18.png)

Sie können diese Ergebnisse mit den im vorherigen Beispiel dargestellten vergleichen. Nachdem Trends aus den Variablen entfernt und sie standardisiert wurden, sehen wir viel weniger Strukturen in den Beziehungen zwischen diesen Variablen.

Der Code zum Berechnen der Korrelationen als ccf-Objekte in R lautet wie folgt:

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Durch Ausführen des Codes wird das hier dargestellte Protokoll erzeugt.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Es gibt einen Korrelationswert für jede Verzögerung. Keiner dieser Korrelationswerte ist groß genug, um signifikant zu sein. Wir können daraus schließen, dass wir jede Variable unabhängig modellieren können.

### <a name="output-a-dataframe"></a>Ausgeben eines Dataframes
Wir haben die paarweisen Korrelationen als Liste von ccf-Objekten in R berechnet. Dies stellt ein gewisses Problem dar, da der "Result Dataset"-Ausgabeport tatsächlich ein Dataframe erfordert. Darüber hinaus ist das ccf-Objekt selbst eine Liste, und wir benötigen nur die Werte im ersten Element dieser Liste, also die Korrelationen an den verschiedenen Verzögerungen.

Der folgende Code extrahiert die Verzögerungswerte aus der Liste der ccf-Objekte, die ihrerseits aufgelistet werden:

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Die erste Codezeile ist etwas schwieriger, doch einige Erläuterungen sollten Ihnen helfen, sie zu verstehen. Gehen Sie von innen nach außen vor:

1. Der „ **[[** “-Operator wählt mit dem „**1**“-Argument den Vektor der Korrelationen an den Verzögerungen aus dem ersten Element der ccf-Objektliste aus.
1. Die `do.call()`-Funktion wendet die `rbind()`-Funktion auf die Elemente der Liste an, die von `lapply()` zurückgegeben wird.
1. Die `data.frame()`-Funktion wandelt das von `do.call()` erzeugte Ergebnis in ein Dataframe um.

Beachten Sie, dass sich die Zeilennamen in einer Spalte des Dataframes befinden. Auf diese Weise bleiben die Zeilennamen erhalten, wenn sie vom Modul [R-Skript ausführen][execute-r-script] ausgegeben werden.

Durch Ausführen des Codes wird die hier dargestellte Ausgabe erzeugt, wenn wir zum Anzeigen der Ausgabe am „Result Dataset“-Port **Visualisieren** auswählen. Die Zeilennamen sind wie vorgesehen in der ersten Spalte.

![Screenshot: Ergebnisausgabe der Korrelationsanalyse](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Beispiel für eine Zeitreihe: saisonale Vorhersagen

Unsere Daten liegen nun in einer Form vor, die für die Analyse geeignet ist, und wir haben festgestellt, dass keine nennenswerten Korrelationen zwischen den Variablen bestehen. Lassen Sie uns nun fortfahren und ein Zeitreihen-Vorhersagemodell erstellen. Mit diesem Modell werden wir die kalifornische Milchproduktion für die 12 Monate von 2013 vorhersagen.

Unser Vorhersagemodell wird aus zwei Komponenten bestehen: einer Trendkomponente und einer saisonalen Komponente. Die vollständige Vorhersage ist das Produkt aus diesen beiden Komponenten. Dieser Typ von Modell ist auch als multiplikatives Modell bekannt. Die Alternative ist ein additives Modell. Wir haben bereits eine logarithmische Transformation auf die betreffenden Variablen angewendet, wodurch diese Analyse kontrollierbar wird.

Der vollständige R-Code für diesen Abschnitt befindet sich unter [MachineLearningSamples-Notebooks/studio-samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Erstellen des Dataframes für die Analyse

Beginnen Sie damit, dass Sie Ihrem Experiment ein neues [R-Skript ausführen][execute-r-script]-Modul hinzufügen. Verbinden Sie den Ausgabeport Result Dataset des vorhandenen Moduls [R-Skript ausführen][execute-r-script] mit dem Eingabeport **Dataset1** des neuen Moduls. Das Ergebnis sollte in etwa wie dieses Beispiel aussehen.

![Abbildung: Experiment mit dem neu hinzugefügten „R-Skript ausführen“-Modul](./media/r-quickstart/fig21.png)

Wie bei der gerade abgeschlossenen Korrelationsanalyse, müssen wir eine Spalte mit einem POSIXct-Zeitreihenobjekt hinzufügen. Die Spalte wird mit dem folgenden Code hinzugefügt.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Führen Sie diesen Code aus, und überprüfen Sie das Protokoll. Das Ergebnis sollte wie dieses Beispiel aussehen.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Mit diesem Ergebnis können wir nun unsere Analyse starten.

### <a name="create-a-training-dataset"></a>Erstellen eines Trainings-DataSets

Mit dem erzeugten Dataframe müssen wir ein Trainingsdataset erstellen. Diese Daten enthalten dann alle Beobachtungen im Jahr 2013, außer den letzten 12, bei denen es sich um unser Testdataset handelt. Der folgende Code unterteilt das Dataframe in Teilmengen und erstellt grafische Darstellungen der Milchproduktions- und -preisvariablen. Anschließend erstellen wir grafische Darstellungen der vier Produktions- und Preisvariablen. Es wird eine anonyme Funktion verwendet, um einige Argumente zur grafischen Darstellung zu definieren und dann eine Iteration durch die Liste der anderen beiden Argumente mit `Map()`durchzuführen. Wenn Sie der Meinung sind, dass an dieser Stelle auch eine For-Schleife gut funktioniert hätte, liegen Sie richtig. Aber da R eine funktionale Sprache ist, zeigen wir Ihnen hier einen funktionalen Ansatz.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Durch Ausführen des Codes wird die Serie von Zeitreihengrafiken aus der „R Device“-Ausgabe erzeugt, die hier dargestellt sind. Die Zeitachse wird in Dateneinheiten angegeben – ein praktischer Vorteil der Zeitreihen-Plotmethode.

![Die erste Zeitreihengrafik von kalifornischen Milchproduktions- und Milchpreisdaten](./media/r-quickstart/unnamed-chunk-161.png)

![Die zweite Zeitreihengrafik von kalifornischen Milchproduktions- und Milchpreisdaten](./media/r-quickstart/unnamed-chunk-162.png)

![Die dritte Zeitreihengrafik von kalifornischen Milchproduktions- und Milchpreisdaten](./media/r-quickstart/unnamed-chunk-163.png).

![Die vierte Zeitreihengrafik von kalifornischen Milchproduktions- und Milchpreisdaten](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Ein Trendmodell

Nachdem wir nun ein Zeitreihenobjekt erstellt und uns die Daten angesehen haben, beginnen wir nun damit, ein Trendmodell für die kalifornischen Milchproduktionsdaten zu erstellen. Wir können eine Zeitreihenregression verwenden. Aus der Grafik wird deutlich, dass wir mehr als eine Steigung (slope) und ein konstantes Glied (intercept) benötigen, um den in den Trainingsdaten beobachteten Trend präzise zu modellieren.

Angesichts des geringen Umfangs der Daten erstellen wir das Modell für den Trend in RStudio, schneiden das resultierende Modell dann aus und fügen es in Machine Learning Studio (Classic) ein. RStudio bietet eine interaktive Umgebung für diese Art von interaktiver Analyse.

Als einen ersten Versuch probieren wir es mit einer polynomialen Regression mit Potenzen bis drei. Es besteht die realistische Gefahr, diese Arten von Modellen zu stark anzupassen. Daher sollten Terme höherer Ordnung vermieden werden. Die `I()`-Funktion unterdrückt die Interpretation des Inhalts (der Inhalt wird ohne Anpassungen interpretiert) und ermöglicht es Ihnen, eine literal interpretierte Funktion in einer Regressionsgleichung zu schreiben.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Mit dieser Funktion wird die folgende Ausgabe generiert.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 **_
## Time              1.63e-09   1.72e-10    9.47   <2e-16 _*_
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 _  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

An den p-Werten (`Pr(>|t|)`) in dieser Ausgabe können Sie erkennen, dass der quadrierte Term möglicherweise nicht signifikant ist. Wir verwenden die `update()`-Funktion zum Ändern dieses Modells, indem wir den quadrierten Term löschen.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Mit dieser Funktion wird die folgende Ausgabe generiert.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 **_
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Das sieht schon besser aus. Alle Terme sind signifikant. Der „2e-16“-Wert ist ein Standardwert und sollte nicht zu ernst genommen werden.  

Als Funktionstest erstellen wir eine Zeitreihengrafik der kalifornischen Milchproduktionsdaten mit der angezeigten Trendkurve. Wir haben den folgenden Code im Modul [R-Script ausführen][execute-r-script] von Machine Learning Studio (Classic) (nicht in RStudio) hinzugefügt, um das Modell sowie eine Grafik davon zu erstellen. Das Ergebnis wird im folgenden Beispiel dargestellt.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornische Milchproduktionsdaten mit angezeigtem Trendmodell](./media/r-quickstart/unnamed-chunk-18.png)

Es sieht so aus, als ob das Trendmodell ziemlich gut den Daten angepasst ist. Darüber hinaus scheint es keine Anzeichen für Überanpassung zu geben, wie z. B. merkwürdige Ausreißer in der Modellkurve.

### <a name="seasonal-model"></a>Saisonales Modell

Mit dem uns jetzt zur Verfügung stehenden Trendmodell fahren wir fort und berücksichtigen die saisonalen Auswirkungen. Wir verwenden den Monat des Jahres als Platzhaltervariable im linearen Modell, um die Auswirkungen von Monat zu Monat zu erfassen. Bei Einführung von Faktorvariablen in einem Modell darf das konstante Glied nicht berechnet werden. Wenn Sie dies versäumen, ist die Gleichung überspezifiziert, und R lässt einen der gewünschten Faktoren fallen, behält aber den konstanten Gliedterm bei.

Da wir über ein zufriedenstellendes Trendmodell verfügen, können wir die `update()`-Funktion verwenden, um dem vorhandenen Modell die neuen Terme hinzuzufügen. Durch die "-1" in der "update"-Gleichung wird der konstante Gliedterm fallen gelassen. Für den Moment fahren wir in RStudio fort:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Mit dieser Funktion wird die folgende Ausgabe generiert.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 **_
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 _*_
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 _*_
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 _*_
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 _*_
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 _*_
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 _*_
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 _*_
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 _*_
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 _*_
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 _*_
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 _*_
## ---
## Signif. codes:  0 '_*_' 0.001 '_*' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Wir sehen, dass das Modell keinen konstanten Gliedterm mehr und 12 signifikante Monatsfaktoren hat. Dieses Ergebnis ist genau das, was wir sehen wollten.

Lassen Sie uns eine weitere Zeitreihengrafik der kalifornischen Milchproduktionsdaten erstellen, um zu sehen, wie gut das saisonale Modell funktioniert. Wir haben den folgenden Code im Modul [R-Script ausführen][execute-r-script] von Machine Learning Studio (Classic) hinzugefügt, um das Modell sowie eine Grafik davon zu erstellen.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Durch Ausführen des Codes in Machine Learning Studio (Classic) wird die hier dargestellte Grafik erzeugt.

![Kalifornische Milchproduktion mit Modell, einschließlich saisonaler Auswirkungen](./media/r-quickstart/unnamed-chunk-20.png)

Die in diesem Beispiel dargestellte Anpassung an die Daten ist ziemlich vielversprechend. Sowohl der Trend als auch die saisonalen Auswirkungen (monatliche Variation) sehen vernünftig aus.

Als weiteren Test unseres Modells sehen wir uns die Residuen an. Der folgende Code berechnet die vorhergesagten Werte aus unseren beiden Modellen und die Restdaten (Residuen) für das saisonale Modell und erstellt dann eine Grafik dieser Restdaten für die Trainingsdaten.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Die Restdatengrafik sehen Sie hier.

![Restdaten des saisonalen Modells für die Trainingsdaten](./media/r-quickstart/unnamed-chunk-21.png)

Die Restwerte sehen vernünftig aus. Es gibt keine bestimmte Struktur, mit Ausnahme der Auswirkungen der Rezession 2008 bis 2009, die unser Modell nicht besonders gut abbildet.

Die in diesem Beispiel dargestellte Grafik eignet sich zum Erkennen von zeitabhängigen Mustern in den Restdaten. Durch den verwendeten expliziten Ansatz der Berechnung und Darstellung der Restdaten werden diese in zeitlicher Reihenfolge in der Grafik platziert. Wenn wir die Grafik mit `milk.lm$residuals` erstellt hätten, würde die Grafik nicht die zeitliche Reihenfolge widerspiegeln.

Sie können auch `plot.lm()` verwenden, um eine Reihe von Diagnosegrafiken zu erzeugen:

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Dieser Code erzeugt eine Reihe von Diagnosegrafiken, die in den folgenden Beispielen dargestellt sind.

![Erste Diagnosegrafik für das saisonale Modell](./media/r-quickstart/unnamed-chunk-221.png)

![Zweite Diagnosegrafik für das saisonale Modell](./media/r-quickstart/unnamed-chunk-222.png)

![Dritte Diagnosegrafik für das saisonale Modell](./media/r-quickstart/unnamed-chunk-223.png)

![Vierte Diagnosegrafik für das saisonale Modell](./media/r-quickstart/unnamed-chunk-224.png)

In diesen Grafiken werden einige hoch einflussreiche Größen identifiziert, aber nichts, was uns Sorgen bereiten sollte. Außerdem können wir in der „Normal Q-Q“-Grafik ablesen, dass die Residuale fast normal verteilt sind – eine wichtige Voraussetzung für lineare Modelle.

### <a name="forecasting-and-model-evaluation"></a>Vorhersage und Modellevaluierung

Es gibt nur noch eine Sache zu tun, um unser Beispiel abzuschließen. Wir müssen Vorhersagen berechnen und den Fehler gegenüber den tatsächlichen Daten berechnen. Unsere Vorhersage erstellen wir für 12 Monate in 2013. Wir können ein Fehlermaß für diese Vorhersage gegenüber den tatsächlichen Daten berechnen, die nicht Bestandteil unseres Trainingsdatasets sind. Darüber hinaus können vergleichen wir die Leistung bei den Trainingsdaten aus 18 Jahren gegenüber den Testdaten aus 12 Monaten.

Eine Reihe von Metriken wird zum Messen der Leistung von Zeitreihenmodellen verwendet. In unserem Fall verwenden wir die mittlere quadratische Abweichung (RMS-Fehler). Die folgende Funktion berechnet den RMS-Fehler zwischen zwei Reihen:

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Wie bei der `log.transform()`-Funktion, die wir im Abschnitt „Werttransformationen“ besprochen haben, enthält diese Funktion ziemlich viel Code zur Fehlerprüfung und Behandlung von Ausnahmen. Die angewendeten Prinzipien sind identisch. Die Arbeit erfolgt an zwei Stellen innerhalb von `tryCatch()`. Zunächst werden die Zeitreihen potenziert, da wir mit logarithmierten Werten gearbeitet haben. Als Nächstes wird der tatsächliche RMS-Fehler berechnet.

Ausgestattet mit einer Funktion zum Messen des RMS-Fehlers, erstellen wir nun ein den RMS-Fehler enthaltendes Dataframe und geben es aus. Wir nehmen Terme für das Trendmodell alleine sowie das gesamte Modell mit saisonalen Faktoren auf. Der folgende Code erledigt die Aufgabe unter Verwendung zweier von uns erstellter linearer Modelle.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Durch Ausführen des Codes wird die hier dargestellte Ausgabe am „Result Dataset“-Ausgabeport erzeugt.

![Screenshot: Vergleich von RMS-Fehlern bei den Modellen](./media/r-quickstart/fig26.png)

Anhand dieser Ergebnisse können wir erkennen, dass der RMS-Fehler durch das Hinzufügen der saisonalen Faktoren zu dem Modell signifikant abnimmt. Es überrascht uns nicht allzu sehr, dass der RMS-Fehler für die Trainingsdaten ein wenig niedriger ist für die Vorhersage.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Leitfaden zur RStudio-Dokumentation

RStudio ist ausführlich dokumentiert. Hier finden Sie einige Links zu den wichtigsten Abschnitten der RStudio-Dokumentation, die Ihnen den Einstieg erleichtern.

* **Erstellen von Projekten**: Sie können Ihren R-Code mit RStudio in Projekten organisieren. Weitere Informationen finden Sie unter [Using Projects (Verwenden von Projekten)](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Befolgen Sie diese Anweisungen, und erstellen Sie für die R-Codebeispiele in diesem Artikel ein Projekt.
* **Bearbeiten und Ausführen von R-Code**: RStudio bietet eine integrierte Umgebung zum Bearbeiten und Ausführen von R-Code. Weitere Informationen finden Sie unter [Editing and Executing Code (Bearbeiten und Ausführen von Code)](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Debuggen:** RStudio enthält leistungsfähige Debugfunktionen. Weitere Informationen zu diesen Features finden Sie unter [Debugging with RStudio (Debuggen mit RStudio)](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Weitere Informationen zu den Features zur Behandlung von Problemen mit Breakpoints finden Sie unter [Breakpoint Troubleshooting (Behandlung von Problemen mit Breakpoints)](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting).

## <a name="further-reading"></a><a id="appendixb"></a>Weitere Informationen

Dieses Tutorial für die Programmierung in R behandelt die Grundlagen der Sprache R mit Machine Learning Studio (Classic). Wenn Sie nicht mit R vertraut sind, finden Sie zwei Einführungen im CRAN:

* Die Informationen in [R for Beginners](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) von Emmanuel Paradis sind ein guter Einstieg.
* [An Introduction to R](https://cran.r-project.org/doc/manuals/R-intro.html) von W. N. Venables et al. bietet ausführlichere Informationen.

Es gibt viele Bücher zu R, die Ihnen beim Einstieg helfen können:

* **Art of R Programming: A Tour of Statistical Software Design** von Norman Matloff ist eine ausgezeichnete Einführung in die Programmierung in R.
* Das Werk **R Cookbook** von Paul Teetor enthält Problemstellungen und Lösungen für die Verwendung von R.
* **R in Action** von Robert Kabacoff ist eine weitere nützliche Einführung. Die begleitende Website zu [Quick R](https://www.statmethods.net/) ist eine nützliche Ressource.
* **R Inferno** von Patrick Burns ist ein erstaunlich humorvolles Buch, das sich mit einer Reihe von verzwickten und komplizierten Themen beschäftigt, die bei der Programmierung in R vorkommen können. Das Buch ist unter [The R Inferno](https://www.burns-stat.com/documents/books/the-r-inferno/) kostenlos erhältlich.
* **Advanced R** von Hadley Wickham enthält ausführliche Informationen zu komplexeren Themen in R. Die Onlineversion dieses Buchs ist unter [Advanced R](http://adv-r.had.co.nz/) kostenlos erhältlich.
* **Introductory Time Series with R** von Paul Cowpertwait und Andrew Metcalfe bietet eine Einführung in die Verwendung von R für Zeitreihenanalysen. Außerdem gibt es noch jede Menge theoretische Texte, in denen Sie R-Beispiele finden können.

Hier finden Sie weitere nützliche Internetressourcen:

* [CRAN Task View: Time Series Analysis](https://cran.r-project.org/web/views/TimeSeries.html) enthält einen Katalog mit R-Zeitreihenpaketen. Informationen zu speziellen Zeitreihen-Objektpaketen finden Sie in der Dokumentation zum jeweiligen Paket.
* [Introduction to R](https://www.datacamp.com/courses/introduction-to-r) ist ein kostenloser interaktiver Kurs von DataCamp. Damit erlernen Sie R bequem über den Browser mit Videolektionen und Codierungsübungen. Es gibt interaktive Lernprogramme zu den neuesten R-Techniken und -Paketen.
* [Learn R Programming, The Definitive Guide](https://www.datamentor.io/r-programming/) von DataMentor.
* [R CODER](https://r-coder.com/) enthält ausführliche R-Tutorials und einen kostenlosen R-Kurs für Einsteiger.
* [R Tutorial](https://www.cyclismo.org/tutorial/R/) von Kelly Black von der Clarkson University ist ein kompaktes Tutorial.
* Unter [Top R language resources to improve your data skills](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) sind mehr als 60 Ressourcen zu R aufgelistet.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
