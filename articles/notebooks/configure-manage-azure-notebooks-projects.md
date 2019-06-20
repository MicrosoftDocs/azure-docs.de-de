---
title: Konfigurieren und Verwalten von Azure Notebook-Projekten
description: Verwalten von Projektmetadaten, Projektdateien, der Projektumgebung und von Setupschritten sowohl über die Azure Notebooks-Benutzeroberfläche als auch mit direktem Terminalzugriff.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/13/2019
ms.author: kraigb
ms.openlocfilehash: 0440e498451ee141fa03851b78418caf911d0e32
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65596742"
---
# <a name="manage-and-configure-projects"></a>Verwalten und Konfigurieren von Projekten

Ein Projekt in Azure Notebooks stellt im Wesentlichen eine Konfiguration des zugrunde liegenden virtuellen Linux-Computers, auf dem Jupyter-Notebooks ausgeführt werden, in Kombination mit einem Dateiordner und beschreibenden Metadaten dar. Das Projektdashboard in Azure Notebooks ermöglicht Ihnen das Verwalten von Dateien und das Konfigurieren der sonstigen Projektmerkmale:

- Die Computeebene, auf der das Projekt ausgeführt wird – dies kann die freie Ebene oder ein virtueller Azure-Computer sein.
- Zu den Projektmetadaten gehören der Name, die Beschreibung, ein Bezeichner, der beim Freigeben des Projekts verwendet wird, und die Angabe, ob das Projekt öffentlich oder privat ist.
- Die Verwaltung des Notebooks, der Daten und der sonstigen Dateien eines Projekts erfolgt genau so wie bei jedem anderen Dateisystem.
- Die Projektumgebung kann wahlweise durch Startupskripts oder direkt über das Terminal erfolgen.
- Protokolle, auf die Sie über das Terminal zugreifen.

> [!Note]
> Die hier beschriebenen Verwaltungs- und Konfigurationsfeatures stehen nur dem Projektbesitzer zur Verfügung, der das Projekt ursprünglich angelegt hat. Sie können das Projekt jedoch in Ihr eigenes Konto klonen. In diesem Fall werden Sie Besitzer und können das Projekt wie gewünscht konfigurieren.

Azure Notebooks startet den zugrunde liegenden virtuellen Computer, wann immer Sie ein Notebook oder eine andere Datei ausführen. Der Server speichert Dateien automatisch und wird nach 60 Minuten der Inaktivität heruntergefahren. Darüber hinaus können Sie den Server jederzeit mithilfe des Befehls **Herunterfahren** (Tastenkombination: h) beenden.

## <a name="compute-tier"></a>Computeebene

Standardmäßig werden Projekte auf der Ebene **Freies Compute** ausgeführt, die auf 4 GB Speicher und 1 GB Daten beschränkt ist, um Missbrauch zu verhindern. Sie können diese Einschränkungen umgehen und die Computeleistung erhöhen, indem Sie eine anderen virtuellen Computer verwenden, den Sie in einem Azure-Abonnement bereitgestellt haben. Weitere Informationen finden Sie unter [Verwenden von Data Science Virtual Machines](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Bearbeiten von Projektmetadaten

Wählen Sie im Projektdashboard **Projekteinstellungen** und dann die Registerkarte **Informationen** aus, die die in der folgenden Tabelle beschriebenen Projektmetadaten enthält. Sie können die Projektmetadaten jederzeit ändern.

| Einstellung | BESCHREIBUNG |
| --- | --- |
| Projektname | Ein Anzeigename für Ihr Projekt, das Azure Notebooks zu Anzeigezwecken verwendet. Beispielsweise "Hallo Welt in Python". |
| Projekt-ID | Ein benutzerdefinierter Bezeichner, der Teil der URL wird, die zum Freigeben eines Projekts verwendet wird. Diese ID darf nur aus Buchstaben, Ziffern und Bindestrichen bestehen, ist auf 30 Zeichen beschränkt und darf keine [reservierte Projekt-ID](create-clone-jupyter-notebooks.md#reserved-project-ids) sein. Falls Sie nicht wissen, was Sie verwenden sollen, können Sie sich nach einer gängigen Konvention richten. Dazu legen Sie den Projektnamen in Kleinbuchstaben zugrunde und wandeln Leerzeichen in Bindestriche um, wie etwa in „mein-notebook-projekt“ (ggf. gekürzt, um die Längenbeschränkung einzuhalten). |
| Öffentliches Projekt | Wenn diese Option festgelegt ist, kann jeder, der über den Link verfügt, auf das Projekt zugreifen. Deaktivieren Sie diese Option, wenn Sie ein privates Projekt erstellen. |
| Klone ausblenden | Wenn diese Option festgelegt ist, können andere Benutzer die Liste der Klone, die für dieses Projekt erstellt wurden, nicht sehen. Das Ausblenden der Klone empfiehlt sich bei Projekten, die mit einer großen Anzahl Personen geteilt werden, die nicht Teil der gleichen Organisation sind, etwa wenn ein Notebook zum Unterrichten eines Kurses verwendet wird. |

> [!Important]
>
> Durch Ändern der Projekt-ID werden alle Links, die Sie zuvor geteilt haben, ungültig.

## <a name="manage-project-files"></a>Verwalten von Projektdateien

Das Projektdashboard zeigt den Inhalt des Ordnersystems des Projekts. Sie können verschiedene Befehle verwenden, um diese Dateien zu verwalten.

### <a name="create-new-files-and-folders"></a>Erstellen neuer Dateien und Ordner

Mit dem Befehl **+ Neu** (Tastenkombination: n) werden neue Dateien oder Ordner erstellt. Wählen Sie bei der Verwendung des Befehls zunächst den Typ des zu erstellenden Elements aus:

| Elementtyp | BESCHREIBUNG | Verhalten des Befehls |
| --- | --- | --- |
| **Notebook** | Ein Jupyter-Notebook | Zeigt ein Popup an, in dem Sie den Dateinamen und die Sprache des Notebooks angeben. |
| **Ordner** | Ein Unterordner | Erstellt ein Bearbeitungsfeld in der Dateiliste des Projekts, in dem Sie den Ordnernamen eingeben. |
| **Leere Datei** | Eine Datei, in der Sie beliebige Inhalte speichern können, also Text, Daten usw. | Erstellt ein Bearbeitungsfeld in der Dateiliste des Projekts, in dem Sie den Dateinamen eingeben. |
| **Markdown** | Eine Markdowndatei. | Erstellt ein Bearbeitungsfeld in der Dateiliste des Projekts, in dem Sie den Dateinamen eingeben. |

### <a name="upload-files"></a>Hochladen von Dateien

Der Befehl **Hochladen** bietet zwei Optionen zum Importieren von Daten aus anderen Speicherorten: **Aus URL** und **Von Computer**. Weitere Informationen finden Sie unter [Arbeiten mit Datendateien in Azure Notebooks-Projekten](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Auswählen dateispezifischer Befehle

Über ein mit der rechten Maustaste zugängliches Kontextmenü stellt jedes Element in der Dateiliste des Projekts Befehle zur Verfügung:

![Befehle im Kontextmenü einer Datei](media/project-file-commands.png)

| Get-Help | Tastenkombinationen | Aktion |
| --- | --- | --- |
| Ausführen | r (oder Klicken) | Führt eine Notebook-Datei aus. Andere Dateitypen werden zur Ansicht geöffnet.  |
| Link kopieren | y | Kopiert einen Link zur Datei in die Zwischenablage. |
| In Jupyter Lab ausführen | j | Führt ein Notebook in JupyterLab aus, einer stärker als die normale Benutzeroberfläche von Jupyter für Entwickler ausgelegten Oberfläche. |
| Vorschau | p | Öffnet eine HTML-Vorschau der Datei; für Notebooks stellt die Vorschau eine schreibgeschützte Darstellung des Notebooks dar. Weitere Informationen finden Sie im Abschnitt [Vorschau](#preview). |
| Datei bearbeiten | i | Öffnet die Datei zur Bearbeitung. |
| Download | d | Lädt eine Zip-Datei herunter, die die Datei oder den Inhalt eines Ordners enthält. |
| Umbenennen | a | Fordert zur Eingabe eines neuen Namens für die Datei oder den Ordner auf. |
| Löschen | x | Fordert eine Bestätigung an und entfernt die Datei dann endgültig aus dem Projekt. Löschungen können nicht rückgängig gemacht werden. |
| Move | m | Verschiebt eine Datei in einen anderen Ordner im selben Projekt. |

#### <a name="preview"></a>Vorschau

Eine Vorschau einer Datei oder eines Notebooks ist eine schreibgeschützte Darstellung der Inhalte; das Ausführen der Notebookzellen ist deaktiviert. Eine Vorschau wird allen Personen angezeigt, die über einen Link zur Datei oder zum Notebook verfügen, sich aber nicht bei Azure Notebooks angemeldet haben. Nach der Anmeldung können Benutzer ein Notebook in ihr eigenes Konto klonen oder es auf ihren lokalen Computer herunterladen.

Die Seite „Vorschau“ unterstützt eine Reihe von Symbolleisten mit Tastenkombinationen:

| Get-Help | Tastenkombinationen | Aktion |
| --- | --- | --- |
| Freigabe | s | Zeigt das Freigabepopup an, aus dem Sie einen Link abrufen, in sozialen Medien teilen, eine HTML zur Einbettung abrufen oder eine E-Mail senden können. |
| Klonen | c  | Klont das Notebook in Ihr Konto. |
| Ausführen | r | Führt das Notebook aus, falls Sie dazu berechtigt sind. |
| Download | d | Lädt eine Kopie des Notebooks herunter. |

## <a name="configure-the-project-environment"></a>Konfigurieren der Projektumgebung

Es gibt drei Möglichkeiten, die Umgebung des virtuellen Computers zu konfigurieren, auf dem Ihre Notebooks ausgeführt werden:

- Einschließen eines einmaligen Initialisierungsskripts
- Verwenden der Umgebungseinstellungen des Projekts zur Angabe von Setupschritten
- Zugriff auf den virtuellen Computer über ein Terminal

Alle Formen der Projektkonfiguration werden bei jedem Starten des virtuellen Computers angewendet und wirken sich daher auf alle Notebooks im Projekt aus.

### <a name="one-time-initialization-script"></a>Einmaliges Initialisierungsskript

Bei der erstmaligen Erstellung eines Servers für das Projekt sucht Azure Notebooks im Projekt nach einer Datei mit dem Namen *aznbsetup.sh*. Wenn diese Datei vorhanden ist, führt Azure Notebooks sie aus. Die Ausgabe des Skripts wird in Ihrem Projektordner als *.aznbsetup.log* gespeichert.

### <a name="environment-setup-steps"></a>Schritte zum Einrichten der Umgebung

Sie können die Umgebungseinstellungen des Projekts verwenden, um einzelne Schritte zum Konfigurieren der Umgebung zu erstellen.

Wählen Sie auf dem Projektdashboard **Projekteinstellungen** und dann die Registerkarte **Umgebung** aus, auf der Sie Setupschritte für das Projekt hinzufügen, entfernen und ändern können:

![Popup „Projekteinstellungen“ mit ausgewählter Registerkarte „Umgebung“](media/project-settings-environment-steps.png)

Zum Hinzufügen eines Schritts wählen Sie zuerst **+ Hinzufügen** und dann in der Dropdownliste **Vorgang** einen Schritttyp aus:

![Vorgangsauswahl für einen neuen Schritt zur Umgebungseinrichtung](media/project-settings-environment-details.png)

Die Informationen, die Sie anschließend vorgeben, hängen vom ausgewählten Vorgangstyp ab:

- **Requirements.txt**: Wählen Sie in der zweiten Dropdownliste eine Datei *requirements.txt* aus, die bereits im Projekt vorhanden ist. Wählen Sie anschließend in der dritten Dropdownliste, die daraufhin angezeigt wird, eine Python-Version aus. Mithilfe der *requirements.txt*-Datei führt Azure Notebooks beim Starten eines Notebookservers `pip install -r` mit der *requirements.txt*-Datei aus. Innerhalb des eigentlichen Notebooks brauchen Sie nicht explizit Pakete zu installieren.

- **Shellskript**: Wählen Sie in der zweiten Dropdownliste ein Bash-Shellskript im Projekt aus (normalerweise eine Datei mit der *.sh*-Erweiterung), die alle Befehle enthält, die Sie zur Initialisierung der Umgebung ausführen möchten.

- **Environment.yml**: Wählen Sie in der zweiten Dropdownliste eine *environments.yml*-Datei für Python-Projekte aus, die eine Conda-Umgebung verwenden.

Wenn Sie mit dem Hinzufügen von Schritten fertig sind, wählen Sie **Speichern** aus.

### <a name="use-the-terminal"></a>Verwenden des Terminals

Auf dem Projektdashboard öffnet der Befehl **Terminal** ein Linux-Terminal, das Ihnen Direktzugriff auf den Server gibt. Im Terminal können Sie Daten herunterladen, Dateien bearbeiten oder verwalten, Prozesse untersuchen und sogar Tools wie vi und nano verwenden.

> [!Note]
> Wenn Sie in der Umgebung Ihres Projekts Startupskripts verwenden, wird beim Öffnen des Terminals möglicherweise eine Meldung angezeigt, dass die Einrichtung noch ausgeführt wird.

Im Terminal können alle Linux-Standardbefehle ausgegeben werden. Sie können im Stammordner ferner `ls` verwenden, um die verschiedenen Umgebungen auf dem virtuellen Computer, wie etwa *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* und *R* zusammen mit einem *Projektordner* anzuzeigen, der das Projekt enthält:

![Projektterminal in Azure Notebooks](media/project-terminal.png)

Um eine bestimmte Umgebung zu bearbeiten, wechseln Sie zuerst in den Ordner der entsprechenden Umgebung.

Für die Python-Umgebungen finden Sie `pip` und `conda` im Ordner *bin* der einzelnen Umgebungen. Darüber hinaus können Sie die integrierten Aliase für die Umgebungen verwenden:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Am Server vorgenommene Änderungen gelten nur für die aktuelle Sitzung, mit Ausnahme von Dateien und Ordnern, die im *Projektordner* selbst erstellt werden. Beispielsweise bleiben die Änderungen an einer Datei im Projektordner zwischen Sitzungen erhalten, mit `pip install` installierte Pakete aber nicht.

> [!Note]
> Wenn Sie `python` oder `python3` verwenden, rufen Sie die auf dem System installieren Versionen von Python auf, die für Notebooks nicht verwendet werden. Sie haben dann auch keine Berechtigung zum Ausführen von Vorgängen wie `pip install`, also achten Sie darauf, die versionsspezifischen Aliase zu verwenden.

## <a name="access-notebook-logs"></a>Zugriff auf Notebookprotokolle

Wenn beim Ausführen eines Notebooks Probleme auftreten, werden Ausgaben von Jupyter in einem Ordner mit dem Namen *.nb.log* gespeichert. Sie können auf diese Protokolle mit dem Befehl **Terminal** oder über das Projektdashboard zugreifen.

Bei der lokalen Ausführung haben Sie Jupyter normalerweise wohl aus einem Terminalfenster gestartet. Im Terminalfenster werden Ausgaben wie der Kernelstatus angezeigt.

Verwenden Sie im Terminal den folgenden Befehl, um Protokolle anzuzeigen:

```bash
cat .nb.log
```

Sie können den Befehl auch aus einer Codezelle in einem Python-Notebook ausführen:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Nächste Schritte

- [Gewusst wie: Arbeiten mit Projektdatendateien](work-with-project-data-files.md)
- [Zugreifen auf Clouddaten in einem Notebook](access-data-resources-jupyter-notebooks.md)
