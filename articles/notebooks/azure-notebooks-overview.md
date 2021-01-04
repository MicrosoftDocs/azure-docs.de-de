---
title: Übersicht über Azure Notebooks (Vorschauversion)
description: Führen Sie Jupyter-Notebooks in der Cloud aus, indem Sie den kostenlosen Dienst „Azure Notebooks (Vorschauversion)“ verwenden, der weder Setup noch Konfiguration erfordert.
ms.topic: overview
ms.date: 04/05/2019
ms.openlocfilehash: d59faaf85a1fdbad776e954e974bafa300f93d85
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845134"
---
# <a name="overview-of-azure-notebooks-preview"></a>Übersicht über Azure Notebooks (Vorschauversion)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks ist ein kostenloser gehosteter Dienst zum Entwickeln und Ausführen von Jupyter-Notebooks in der Cloud ohne Installation. [Jupyter](https://jupyter.org/) (vormals IPython) ist ein Open Source-Projekt, mit dem Sie Markdowntext, ausführbaren Code, beständige Daten, Grafiken und Visualisierungen einfach in einer einzelnen, gemeinsam nutzbaren Canvas – einem so genannten *Notebook* – zusammenführen können (Bild von „jupyter.org“):

[![Beispiele für Jupyter Notebooks](https://jupyter.org/assets/jupyterpreview.png)](https://jupyter.org/assets/jupyterpreview.png#lightbox)

Aufgrund dieser leistungsstarken Kombination aus Code, Grafiken und beschreibendem Text ist Jupyter zu einer beliebten Lösung für viele Anwendungsfälle geworden, z.B. Data Science-Anleitung, Datenbereinigung und -transformation, numerische Simulation, statistische Modellierung und Entwicklung von Machine Learning-Modellen.

## <a name="hassle-free-experience"></a>Benutzerfreundliche Umgebung

Azure Notebooks ermöglicht den schnellen Einstieg in die Prototyperstellung, Data Science, akademische Forschung oder Programmierung von Python:

- Ein Data Scientist hat ohne Installationsaufwand sofortigen Zugriff auf die gesamte Anaconda-Umgebung.
- Ein Lehrer kann für Kursteilnehmer eine benutzerfreundliche Python-Umgebung bereitstellen.
- Ein Referent kann live eine Rede oder ein Webinar halten, ohne dass die Teilnehmer erst einmal 45 Minuten mit der Installation der Software verbringen müssen.
- Ein Entwickler oder Hobbyentwickler kann Notebooks als Notizblock für schnelle Codeskizzierungen verwenden.

Notebooks sind sogar noch leistungsfähiger, wenn Benutzer damit über einen per Browser zugänglichen Clouddienst, z.B. Azure Notebooks (Vorschauversion), zusammenarbeiten können. In der Cloud müssen Benutzer Jupyter nicht lokal installieren und sich nicht um die Wartung einer Umgebung kümmern. Darüber hinaus ist es mit der Cloud auch einfach, Notebooks (und die zugehörigen Dateien) mit anderen autorisierten Benutzern gemeinsam zu nutzen, sodass Notebooks nicht umständlich über externe Wege, z.B. Repositorys für die Quellcodeverwaltung, freigegeben werden müssen. Mit Azure Notebooks können Benutzer Notebooks zu Änderungs- oder Experimentierzwecken auch in ihr eigenes Konto kopieren (bzw. „klonen“). Dies ist besonders für Anleitungen hilfreich.

Da es sich bei Azure Notebooks um eine allgemeine Plattform für die Codeerstellung, -ausführung und -freigabe handelt, können Sie den Dienst für viele verschiedene Szenarien nutzen:

- Erlernen einer neuen Programmiersprache: Probieren Sie ein [Frontpage-Tutorial](https://notebooks.azure.com/Microsoft/projects/samples/html/Introduction%20to%20Python.ipynb) aus.
- Erlernen von Data Science: Lesen Sie das [Buch von Jake VanderPlas](https://notebooks.azure.com/jakevdp/projects/PythonDataScienceHandbook).
- [Halten eines Kurses](https://notebooks.azure.com/garth-wells/projects/CUED-IA-Computing-Michaelmas) für Hunderte von Kursteilnehmern
- Halten eines Webinars (online oder bei einer Konferenz) ohne Installationsaufwand 
- Ermöglichen des direkten Ladens und Ausführens von Notebooks durch die [Erstellung eines GitHub-Startbadge](https://notebooks.azure.com/help/projects/sharing/create-a-github-badge) für GitHub-Benutzer
- Vorführen von [PowerPoint-ähnlichen Diashows](https://notebooks.azure.com/help/jupyter-notebooks/slides), bei denen der Code auf den Folien ausführbar ist!

Kurz zusammengefasst: Mit Azure Notebooks können Sie Ihre Arbeit effizienter erledigen und mehr schaffen.

> [!Note]
> Weitere Informationen zu Jupyter selbst finden Sie auf [jupyter.org](https://jupyter.org/) und in der [Jupyter-Dokumentation](https://jupyter-notebook.readthedocs.io/en/latest/).

## <a name="pricing-and-quotas"></a>Preise und Kontingente

Azure Notebooks ist ein kostenloser Dienst, aber jedes Projekt ist auf 4 GB Speicher und 1 GB Daten beschränkt, um Missbrauch zu vermeiden. Für berechtigte Benutzer, die diese Grenzwerte überschreiten, wird eine Captcha-Eingabe abgefragt, bevor die Notebooks weiter ausgeführt werden können.

Melden Sie sich mit einem Konto, das Azure Active Directory nutzt (etwa einem Unternehmenskonto), bei Azure Notebooks an, um alle Beschränkungen aufzuheben. Ist dieses Konto mit einem Azure-Abonnement verknüpft, können Sie eine Verbindung mit beliebigen Data Science Virtual Machine-Instanzen in diesem Abonnement herstellen. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten](configure-manage-azure-notebooks-projects.md#compute-tier) im Abschnitt zum Compute-Tarif.

Notebook-Server sind maximal acht Stunden lang vorhanden. In den meisten Fällen ist Ihr Container von diesem Limit nicht betroffen und wird weiter ausgeführt. Langlebige Sitzungen werden jedoch bisweilen aus Stabilitätsgründen herunterfahren.

## <a name="available-kernels-and-environments"></a>Verfügbare Kernel und Umgebungen

Für jedes Notebook wählen Sie den Kernel aus (also die Laufzeitumgebung), der zum Ausführen von Codezellen verwendet wird. Azure Notebooks unterstützt die folgenden Kernel:

- Python 2.7 + Anaconda2-5.3.0
- Python 3.6 + Anaconda3-5.3.0
- Python 3.5 + Anaconda3-4.2.0 (wird eingestellt)
- R 3.4.1 + Microsoft R Open 3.4.1
- F# 4.1.9

Azure Notebooks enthält über die Basisdistributionen hinaus noch weitere Pakete. Die Python-Kernel umfassen beispielsweise die Bibliotheken numpy, pandas, scikit-learn, matplotlib und bokeh.

Sie können ein Projekt auch anpassen, um eine Umgebung für alle Notebooks dieses Projekts zu erstellen. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Projekts mit benutzerdefinierter Umgebung](quickstart-create-jupyter-notebook-project-environment.md).

Zusätzlich zu den Basisdistributionen sind für Azure Notebooks viele weitere Pakete vorinstalliert, die für Data Scientists nützlich sind. Sie können auch Ihre eigenen Pakete installieren, indem Sie für jede Sprache jeweils den üblichen Prozess verwenden.

## <a name="pre-configured-jupyter-extensions"></a>Vorkonfigurierte Jupyter-Erweiterungen

Für Azure Notebooks sind die folgenden Jupyter-Erweiterungen vorkonfiguriert:

- [RISE](https://github.com/damianavila/RISE): Eine Jupyter-Diashowerweiterung (auch als „live_reveal“ bezeichnet). Weitere Informationen finden Sie unter [Run a notebook slideshow](present-jupyter-notebooks-slideshow.md) (Ausführen einer Notebook-Diashow).
- [JupyterLab](https://github.com/jupyterlab/jupyterlab): Eine vollständige Computeumgebung für die Arbeit mit Jupyter-Notebooks.
- [Altair](https://github.com/ellisonbg/altair): Eine deklarative statistische Visualisierungsbibliothek für Python.
- [BQPlot](https://github.com/bloomberg/bqplot): Ein interaktives Ausgabeframework für Jupyter-Notebooks.
- [IpyWidgets](https://github.com/jupyter-widgets/ipywidgets): Interaktive HTML-Widgets für Jupyter-Notebooks.

## <a name="issues-and-getting-help"></a>Probleme und Hilfe bei Problemen

Da sich Azure Notebooks noch in der Vorschauphase befindet, kann es für den Dienst zu vorübergehenden Ausfällen kommen, die häufiger als bei anderen Azure-Diensten auftreten bzw. länger dauern. Es kann sein, dass einige Features unvollständig sind oder Fehler enthalten.

Derzeit raten wir davon ab, die Vorschauversion von Azure Notebooks für unternehmenskritische Anwendungen oder sensible Notebooks und Daten zu nutzen.

Sie können im [GitHub-Repository](https://github.com/Microsoft/AzureNotebooks/issues) ein Problem melden, falls Sie Fragen zu Azure Notebooks haben.

## <a name="next-steps"></a>Nächste Schritte  

- [Erkunden von Beispielnotebooks](azure-notebooks-samples.md)

- Schnellstartanleitungen:

  - [Erstellen und Freigeben eines Notebooks](quickstart-create-share-jupyter-notebook.md)
  - [Klonen eines Notebooks](quickstart-clone-jupyter-notebook.md)
  - [Migrieren eines lokalen Jupyter Notebooks](quickstart-migrate-local-jupyter-notebook.md)
  - [Verwenden einer benutzerdefinierten Umgebung](quickstart-create-jupyter-notebook-project-environment.md)
  - [Anmelden und Festlegen einer Benutzer-ID](quickstart-sign-in-azure-notebooks.md)

- Tutorials:

  - [Erstellen und Ausführen eines Notebooks](tutorial-create-run-jupyter-notebook.md  )

- Gewusst-wie-Artikel:
  
  - [Erstellen und Klonen von Projekten](create-clone-jupyter-notebooks.md)
  - [Konfigurieren und Verwalten von Projekten](configure-manage-azure-notebooks-projects.md)
  - [Installieren von Paketen aus einem Notebook](install-packages-jupyter-notebook.md)
  - [Zeigen einer Bildschirmpräsentation](present-jupyter-notebooks-slideshow.md)
  - [Arbeiten mit Datendateien](work-with-project-data-files.md)
  - [Zugreifen auf Datenressourcen](access-data-resources-jupyter-notebooks.md)
  - [Verwenden von Azure Machine Learning](../machine-learning/samples-notebooks.md)