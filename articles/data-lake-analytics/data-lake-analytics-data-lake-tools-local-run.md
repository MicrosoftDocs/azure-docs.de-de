---
title: Ausführen von Azure Data Lake-U-SQL-Skripts auf dem lokalen Computer
description: Erfahren Sie, wie Sie U-SQL-Aufträge mithilfe von Azure Data Lake Tools für Visual Studio auf dem lokalen Computer ausführen.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018986"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Ausführen von U-SQL-Skripts auf dem lokalen Computer

Durch die lokale Ausführung der Skripts können Sie bei der Entwicklung von U-SQL-Skripts Zeit und Kosten sparen. In Azure Data Lake Tools für Visual Studio ist es möglich, U-SQL-Skripts auf dem lokalen Computer auszuführen. 

## <a name="basic-concepts-for-local-runs"></a>Grundlegende Konzepte für lokale Ausführungen

In der folgenden Aufstellung sind die Komponenten für die lokale Ausführung und ihre Entsprechung bei der Ausführung in der Cloud aufgeführt.

|Komponente|Lokaler Testlauf|Ausführung in der Cloud|
|---------|---------|---------|
|Storage|Lokaler Datenstammordner|Azure Data Lake Store-Standardkonto|
|Compute|Engine für lokale U-SQL-Ausführung|Azure Data Lake Analytics-Dienst|
|Ausführungsumgebung|Arbeitsverzeichnis auf dem lokalen Computer|Azure Data Lake Analytics-Cluster|

Die folgenden Abschnitte enthalten weitere Informationen zu lokalen Ausführungskomponenten.

### <a name="local-data-root-folders"></a>Lokale Datenstammordner

Ein lokaler Datenstammordner ist ein **lokaler Speicher** für das lokale Computekonto. Jeder Ordner im lokalen Dateisystem auf dem lokalen Computer kann als lokaler Datenstammordner dienen. Er entspricht dem Azure Data Lake Store-Standardkonto eines Data Lake Analytics-Kontos. Der Wechsel zu einem anderen Datenstammordner entspricht dem Wechsel zu einem anderen Standardspeicherkonto. 

Der Datenstammordner wird für Folgendes verwendet:
- Speichern von Metadaten. Beispiele: Datenbanken, Tabellen, Tabellenwertfunktionen und Assemblys.
- Suchen nach Eingabe- und Ausgabepfaden, die als relative Pfade in U-SQL-Skripts definiert sind. Durch die Verwendung von relativen Pfaden wird das Bereitstellen von U-SQL-Skripts in Azure erleichtert.

### <a name="u-sql-local-run-engines"></a>Engines für lokale U-SQL-Ausführung

Eine Engine für die lokale U-SQL-Ausführung ist ein **lokales Computekonto** für U-SQL-Aufträge. Benutzer können U-SQL-Aufträge über Azure Data Lake Tools für Visual Studio lokal ausführen. Lokale Ausführungen werden auch über die Befehlszeilen- und Programmierschnittstellen des Azure Data Lake U-SQL SDK unterstützt. Erfahren Sie mehr über das [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Arbeitsverzeichnisse

Wenn Sie ein U-SQL-Skript ausführen, ist ein Arbeitsverzeichnisordner erforderlich, um Kompilierungsergebnisse zwischenzuspeichern, Protokolle auszuführen und andere Funktionen auszuführen. In Azure Data Lake Tools für Visual Studio ist das Arbeitsverzeichnis das Arbeitsverzeichnis des U-SQL-Projekts. Es befindet sich unter `<U-SQL project root path>/bin/debug>`. Das Arbeitsverzeichnis wird jedes Mal bereinigt, wenn eine neue Ausführung ausgelöst wird.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokale Ausführungen in Microsoft Visual Studio

Azure Data Lake Tools für Visual Studio umfasst eine integrierte Engine für die lokale Ausführung. In den Tools wird die Engine als lokales Computerkonto angezeigt. Um ein U-SQL-Skript lokal auszuführen, wählen Sie im Skript-Editor im Dropdownmenü das Konto **Local-machine** oder **Local-project** aus. Klicken Sie dann auf **Senden**.

![Übermitteln eines U-SQL-Skripts an ein lokales Konto](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokale Ausführungen mit einem „Local-machine“-Konto

Ein **Local-machine**-Konto ist ein freigegebenes lokales Computekonto mit einem einzelnen lokalen Datenstammordner als lokales Speicherkonto. Der Datenstammordner befindet sich standardmäßig unter **C:\Users\<username>\AppData\Local\USQLDataRoot**. Es ist auch über **Tools** > **Data Lake** > **Optionen und Einstellungen** konfigurierbar.

![Konfigurieren eines lokalen Datenstammordners](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Für die lokale Ausführung ist ein U-SQL-Projekt erforderlich. Das Arbeitsverzeichnis des U-SQL-Projekts wird als Arbeitsverzeichnis für die lokale U-SQL-Ausführung verwendet. Kompilierungsergebnisse, Ausführungsprotokolle und andere auf die Ausführung von Aufträgen bezogene Dateien werden während der lokalen Ausführung im Ordner des Arbeitsverzeichnisses generiert und gespeichert. Jedes Mal, wenn Sie das Skript erneut ausführen, werden alle Dateien im Arbeitsverzeichnis bereinigt und erneut generiert.

## <a name="local-runs-with-a-local-project-account"></a>Lokale Ausführungen mit einem „Local-project“-Konto

Ein **Local-project**-Konto ist ein projektisoliertes lokales Computekonto für die einzelnen Projekte mit einem isolierten lokalen Datenstammordner. Jedes aktive U-SQL-Projekt, das im Projektmappen-Explorer in Visual Studio geöffnet wird, verfügt über ein entsprechendes `(Local-project: <project name>)`-Konto. Die Konten werden im Server-Explorer in Visual Studio und am Rand des U-SQL-Skript-Editors aufgeführt.  

Das Konto **Local-project** bietet eine bereinigte und isolierte Entwicklungsumgebung. Ein **Local-machine**-Konto verfügt über einen gemeinsamen lokalen Datenstammordner, in dem Metadaten sowie Eingabe- und Ausgabedaten für alle lokalen Aufträge gespeichert werden. Ein **Local-project**-Konto hingegen erstellt immer einen temporären lokalen Datenstammordner in einem Arbeitsverzeichnis für U-SQL-Projekte, wenn ein U-SQL-Skript ausgeführt wird. Dieser temporäre Datenstammordner wird bereinigt, wenn eine Neuerstellung oder erneute Ausführung erfolgt. 

In einem U-SQL-Projekt lässt sich die isolierte Umgebung zur lokalen Ausführung über Projektverweise und -eigenschaften verwalten. Sie können die Eingabedatenquellen für U-SQL-Skripts im Projekt und die referenzierten Datenbankumgebungen konfigurieren.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Verwalten der Eingabedatenquelle für ein „Local-project“-Konto 

Ein U-SQL-Projekt erstellt einen lokalen Datenstammordner und richtet Daten für ein **Local-project**-Konto ein. Bei jeder Neuerstellung und jeder lokalen Ausführung wird der temporäre Datenstammordner bereinigt und unter dem Arbeitsverzeichnis des U-SQL-Projekts neu erstellt. Alle durch das U-SQL-Projekt konfigurierten Datenquellen werden vor der Ausführung eines lokalen Auftrags in diesen temporären lokalen Datenstammordner kopiert. 

Sie können den Stammordner Ihrer Datenquellen konfigurieren. Klicken Sie mit der rechten Maustaste auf **U-SQL-Projekt** > **Eigenschaft** > **Testdatenquelle**. Beim Ausführen eines U-SQL-Skripts in einem **Local-project**-Konto werden alle Dateien und Unterordner im Ordner **Testdatenquelle** in den temporären lokalen Datenstammordner kopiert. Dateien in Unterordnern werden einbezogen. Nach der Ausführung eines lokalen Auftrags befinden sich auch die Ausgabeergebnisse unter dem temporären lokalen Datenstammordner im Arbeitsverzeichnis des Projekts. Alle diese Ausgaben werden gelöscht und bereinigt, wenn das Projekt neu erstellt und bereinigt wird. 

![Konfigurieren der Testdatenquelle eines Projekts](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Verwalten einer referenzierten Datenbankumgebung für ein **Local-project**-Konto 

Wenn bei einer U-SQL-Abfrage U-SQL-Datenbankobjekte verwendet oder abgefragt werden, müssen Sie vor dem lokalen Ausführen des U-SQL-Skripts die Datenbankumgebungen lokal einrichten. Für das Konto **Local-project** können U-SQL-Datenbankabhängigkeiten durch U-SQL-Projektverweise verwaltet werden. Sie können dem U-SQL-Projekt U-SQL-Datenbankprojektverweise hinzufügen. Vor dem Ausführen von U-SQL-Skripts im Konto **Local-project** werden alle referenzierten Datenbanken im temporären lokalen Datenstammordner bereitgestellt. Bei jeder Ausführung wird der temporäre Datenstammordner außerdem als neue isolierte Umgebung bereinigt.

Einzelheiten finden Sie im entsprechenden Artikel:
* Erfahren Sie, wie U-SQL-Datenbankdefinitionen und Verweise in [U-SQL-Datenbankprojekten](data-lake-analytics-data-lake-tools-develop-usql-database.md) verwaltet werden.

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Unterschied zwischen dem Konto **Local-machine** und dem Konto **Local-project**

Ein **Local-machine**-Konto simuliert ein Azure Data Lake Analytics-Konto auf den lokalen Computern von Benutzern. Es weist die gleiche Oberfläche wie bei einem Azure Data Lake Analytics-Konto auf. Ein **Local-project**-Konto bietet eine benutzerfreundliche lokale Entwicklungsumgebung. Diese Umgebung unterstützt Benutzer bei der Bereitstellung von Verweisen auf eine Datenbank und Eingabedaten, bevor sie Skripts lokal ausführen. Ein **Local-machine**-Konto bietet eine freigegebene permanente Umgebung, auf die über alle Projekte zugegriffen werden kann. Ein **Local-project**-Konto bietet eine isolierte Entwicklungsumgebung für jedes Projekt. Es wird bei jeder Ausführung aktualisiert. Im Vergleich bietet ein **Local-project**-Konto eine schnellere Entwicklungsumgebung, da neue Änderungen rasch angewendet werden.

Weitere Unterschiede zwischen **Local-machine**- und **Local-project**-Konten werden in der folgenden Tabelle angezeigt:

|Unterschied|Local-machine|Local-project|
|----------------|---------------|---------------|
|Lokaler Zugriff|Der Zugriff kann über alle Projekte erfolgen.|Nur das entsprechende Projekt kann auf dieses Konto zugreifen.|
|Lokaler Datenstammordner|Ein permanenter lokaler Ordner. Wird über **Extras** > **Data Lake** > **Optionen und Einstellungen** konfiguriert.|Ein temporärer Ordner, der für jede lokale Ausführung unter dem Arbeitsverzeichnis des U-SQL-Projekts erstellt wird. Der Ordner wird bereinigt, wenn eine Neuerstellung oder erneute Ausführung erfolgt.|
|Eingabedaten für ein U-SQL-Skript|Der relative Pfad unter dem permanenten lokalen Datenstammordner.|Wird über **U-SQL-Projekteigenschaft** > **Testdatenquelle** festgelegt. Alle Dateien und Unterordner werden vor einer lokalen Ausführung in den temporären Datenstammordner kopiert.|
|Ausgabedaten für ein U-SQL-Skript|Relativer Pfad unter dem permanenten lokalen Datenstammordner.|Die Ausgabe erfolgt im temporären Datenstammordner. Die Ergebnisse werden bereinigt, wenn eine Neuerstellung oder erneute Ausführung erfolgt.|
|Bereitstellung von referenzierten Datenbanken|Referenzierte Datenbanken werden beim Ausführen in einem **Local-machine**-Konto nicht automatisch bereitgestellt. Dies gilt auch für die Übermittlung von Daten an ein Azure Data Lake Analytics-Konto.|Referenzierte Datenbanken werden vor einer lokalen Ausführung automatisch im Konto **Local-project** bereitgestellt. Alle Datenbankumgebungen werden bereinigt und neu bereitgestellt, wenn eine Neuerstellung oder erneute Ausführung erfolgt.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Lokale Ausführung mit dem U-SQL SDK

Sie können U-SQL-Skripts in Visual Studio lokal ausführen und auch das Azure Data Lake U-SQL SDK verwenden, um U-SQL-Skripts lokal über die Befehlszeilen- und Programmierschnittstellen auszuführen. Über diese Schnittstellen können Sie lokale U-SQL-Ausführungen und -Tests automatisieren.

Erfahren Sie mehr über das [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Einrichten einer CI/CD-Pipeline für Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Testen des Azure Data Lake Analytics-Codes](data-lake-analytics-cicd-test.md)
