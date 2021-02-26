---
title: Unterstützte Sprachen
titleSuffix: Azure Data Science Virtual Machine
description: Die unterstützten Programmsprachen und zugehörigen Tools, die auf der Data Science Virtual Machine vorinstalliert sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 42110eb896a751080044247932770f37617174c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516429"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Unterstützte Sprachen der Data Science-VM 

Die Data Science Virtual Machine (DSVM) verfügt über mehrere vorgefertigte Sprachen und Entwicklungstools für die Erstellung von Anwendungen, die künstliche Intelligenz (KI) nutzen. In Folgenden werden einige der wichtigsten aufgeführt.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

| Category | Wert |
| ------------- | ------------- |
| Unterstützte Sprachversionen | Python 2.7 und 3.7 |
| Unterstützte DSVM-Editionen      | Windows Server 2016     |
| Konfiguration/Installation auf der DSVM  | Es werden zwei globale `conda`-Umgebungen erstellt: <br /> * Die `root`-Umgebung unter `/anaconda/` ist Python 3.7. <br/> * Die `python2`-Umgebung unter `/anaconda/envs/python2` ist Python 2.7.       |
| Links zu Beispielen      | Jupyter-Beispielnotebooks für Python sind enthalten.     |
| Verwandte Tools auf der DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Builds von Windows Server 2016, die vor März 2018 erstellt wurden, enthalten Python 3.5 und Python 2.7. Python 2.7 ist die Conda-Umgebung (**root**), und **py37** ist die Python 3.7-Umgebung.

### <a name="how-to-use-and-run-it"></a>Verwenden und Ausführen    

* Geben Sie an der Eingabeaufforderung Folgendes ein:

  Öffnen Sie eine Eingabeaufforderung, und verwenden Sie abhängig von Python-Version, die ausgeführt werden soll, eine der folgenden Methoden:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Verwenden in einer IDE:

  Verwenden Sie Python Tools für Visual Studio (PTVS) aus Visual Studio Community Edition. Python 3.6 ist standardmäßig die einzige Umgebung, die automatisch in PTVS eingerichtet wird. 

    > [!NOTE]
    > Soll PTVS mit Python 2.7 arbeiten, müssen Sie eine benutzerdefinierte Umgebung in PTVS erstellen. Um diesen Umgebungspfad in Visual Studio Community Edition festzulegen, navigieren Sie zu **Extras** -> **Python Tools** -> **Python-Umgebungen**, und wählen Sie **+ Benutzerdefiniert** aus. Legen Sie dann den Speicherort auf **c:\anaconda\envs\python2** fest, und wählen Sie **Automatische Erkennung** aus.

* Verwenden in Jupyter:

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als _Python [Conda Root]_ für Python 3.7 und als _Python [Conda env:python2]_ für Python 2.7 festlegen.

* Installieren von Python-Paketen:

  Die Python-Standardumgebungen auf der DSVM sind globale Umgebungen, die für alle Benutzer lesbar sind. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Wenn Sie ein Paket in der globalen Umgebung installieren möchten, müssen Sie den Befehl `activate` als Administrator ausführen, um die root- oder die python2-Umgebung zu aktivieren. Anschließend können Sie Pakete mithilfe eines Paket-Managers wie `conda` oder `pip` installieren oder aktualisieren.

## <a name="python-linux-edition"></a>Python (Linux-Edition)

| Category | Wert |
| ------------- | ------------- |
| Unterstützte Sprachversionen | Python 2.7 und 3.5 |
| Unterstützte DSVM-Editionen      | Linux   |
| Konfiguration/Installation auf der DSVM  | Es werden zwei globale `conda`-Umgebungen erstellt: <br /> Die * `root`-Umgebung unter `/anaconda/` liegt in Python 2.7 vor. <br/> Die * `py35`-Umgebung unter `/anaconda/envs/py35` liegt in Python 3.5 vor.       |
| Links zu Beispielen      | Jupyter-Beispielnotebooks für Python sind enthalten.     |
| Verwandte Tools auf der DSVM      | PySpark, R, Julia      |

### <a name="how-to-use-and-run-it"></a>Verwenden und Ausführen    

* Ausführen in einem Terminal:

  Öffnen Sie das Terminal, und verwenden Sie abhängig von der gewünschten Python-Version eine der folgenden Vorgehensweisen:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Verwenden in einer IDE:

  Verwenden Sie PyCharm aus Visual Studio Community Edition. 

* Verwenden in Jupyter:

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als **Python [Conda Root]** für Python 2.7 und als **Python [Conda env:py35]** für die Python 3.5-Umgebung festlegen. 

* Installieren von Python-Paketen:

  Die Python-Standardumgebungen auf der DSVM sind globale, für alle Benutzer lesbare Umgebungen. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Wenn Sie Pakete in der globalen Umgebung installieren möchten, müssen Sie den Befehl `source activate` als Administrator oder als Benutzer mit sudo-Berechtigung ausführen, um die root- oder die py35-Umgebung zu aktivieren. Anschließend können Sie Pakete mithilfe eines Paket-Managers wie `conda` oder `pip` installieren oder aktualisieren.


## <a name="r"></a>R

| Category | Wert |
| ------------- | ------------- |
| Unterstützte Sprachversionen | Microsoft R Open 3.x (vollständig mit CRAN-R kompatibel)<br /> Microsoft R Server 9.x Developer Edition (eine skalierbare R-Plattform für Unternehmen)|
| Unterstützte DSVM-Editionen      | Linux, Windows     |
| Konfiguration/Installation auf der DSVM  | Windows: `C:\Program Files\Microsoft\ML Server\R_SERVER` <br />Linux: `/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Links zu Beispielen      | Jupyter-Beispielnotebooks für R sind enthalten.     |
| Verwandte Tools auf der DSVM      | SparkR, Python, Julia      |

### <a name="how-to-use-and-run-it"></a>Verwenden und Ausführen    

**Windows**:

* Geben Sie an der Eingabeaufforderung Folgendes ein:

  Öffnen Sie eine Eingabeaufforderung, und geben Sie `R` ein.

* Verwenden in einer IDE:

  Verwenden Sie RTools für Visual Studio (RTVS) aus Visual Studio Community Edition oder RStudio. Diese stehen im Startmenü oder als Symbol auf dem Desktop zur Verfügung. 

* Verwenden in Jupyter

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als **R** festlegen, um den Jupyter R-Kernel (IRKernel) zu verwenden.

* Installieren von R-Paketen:

  R wird auf der DSVM in einer globalen Umgebung installiert, die für alle Benutzer lesbar ist. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Führen Sie R mit einer der vorhergehenden Methoden aus, um Pakete in der globalen Umgebung zu installieren. Anschließend können Sie Pakete durch Ausführen des R-Paket-Managers `install.packages()` installieren oder aktualisieren.

**Linux:**

* Ausführen in einem Terminal:

  Öffnen Sie ein Terminal, und führen Sie `R` aus.  

* Verwenden in einer IDE:

  Verwenden Sie RStudio (auf der Linux-DSVM installiert).  

* Verwenden in Jupyter:

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als **R** festlegen, um den Jupyter R-Kernel (IRKernel) zu verwenden. 

* Installieren von R-Paketen:

  R wird auf der DSVM in einer globalen Umgebung installiert, die für alle Benutzer lesbar ist. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Führen Sie R mit einer der vorhergehenden Methoden aus, um Pakete in der globalen Umgebung zu installieren. Anschließend können Sie Pakete durch Ausführen des R-Paket-Managers `install.packages()` installieren oder aktualisieren.


## <a name="julia"></a>Julia

| Category | Wert |
| ------------- | ------------- |
| Unterstützte Sprachversionen | 0,6 |
| Unterstützte DSVM-Editionen      | Linux, Windows     |
| Konfiguration/Installation auf der DSVM  | Windows: Installiert unter `C:\JuliaPro-VERSION`<br /> Linux: Installiert unter `/opt/JuliaPro-VERSION`    |
| Links zu Beispielen      | Jupyter-Beispielnotebooks für Julia sind enthalten.     |
| Verwandte Tools auf der DSVM      | Python, R      |

### <a name="how-to-use-and-run-it"></a>Verwenden und Ausführen    

**Windows**:

* Ausführen an einer Eingabeaufforderung

  Öffnen Sie eine Eingabeaufforderung, und führen Sie `julia` aus.
* Verwenden in einer IDE:

  Verwenden Sie `Juno` mit der Julia-IDE, die auf der DSVM installiert und als Desktopverknüpfung verfügbar ist.

* Verwenden in Jupyter:

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als **Julia VERSION** festlegen.

* Installieren von Julia-Paketen:

  Julia befindet sich standardmäßig in einer globalen Umgebung, die für alle Benutzer lesbar ist. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Führen Sie Julia mit einer der vorhergehenden Methoden aus, um Pakete in der globalen Umgebung zu installieren. Anschließend können den Julia-Paket-Manager mit Befehlen wie `Pkg.add()` ausführen, um Pakete zu installieren oder zu aktualisieren.


**Linux:**
* Ausführen in einem Terminal:

  Öffnen Sie ein Terminal, und führen Sie `julia` aus.
* Verwenden in einer IDE:

  Verwenden Sie `Juno` mit der Julia-IDE, die auf der DSVM installiert und als Verknüpfung im Menü **Anwendung** verfügbar ist.

* Verwenden in Jupyter:

  Öffnen Sie Jupyter, und wählen Sie **Neu** aus, um ein neues Notebook zu erstellen. Sie können den Kerneltyp als **Julia VERSION** festlegen.

* Installieren von Julia-Paketen:

  Julia befindet sich standardmäßig in einer globalen Umgebung, die für alle Benutzer lesbar ist. Globale Pakete können aber nur von Administratoren geschrieben und installiert werden. Führen Sie Julia mit einer der vorhergehenden Methoden aus, um Pakete in der globalen Umgebung zu installieren. Anschließend können den Julia-Paket-Manager mit Befehlen wie `Pkg.add()` ausführen, um Pakete zu installieren oder zu aktualisieren.

## <a name="other-languages"></a>Andere Sprachen

**C#** : Verfügbar unter Windows, wobei Zugriff darauf über die Visual Studio Community Edition oder an der `Developer Command Prompt for Visual Studio` möglich ist, wo Sie den Befehl `csc` ausführen können.

**Java**: OpenJDK ist sowohl in der Linux- als auch in der Windows-Edition der DSVM verfügbar und ist im Pfad festgelegt. Wenn Sie Java verwenden möchten, geben Sie den Befehl `javac` oder `java` an einer Eingabeaufforderung in Windows oder in der bash-Shell in Linux ein.

**Node.js**: Node.js ist sowohl in der Linux- als auch in der Windows-Edition der DSVM verfügbar und ist im Pfad festgelegt. Wenn Sie auf Node.js zugreifen möchten, geben Sie den Befehl `node` oder `npm` an einer Eingabeaufforderung in Windows oder in der bash-Shell in Linux ein. Unter Windows ist die Visual Studio-Erweiterung für die Node.js-Tools installiert, um eine grafische IDE bereitzustellen, mit der Sie Ihre Node.js-Anwendung entwickeln können.

**F#** : Verfügbar unter Windows, wobei Zugriff darauf über die Visual Studio Community Edition oder an einer `Developer Command Prompt for Visual Studio` möglich ist, wo Sie den Befehl `fsc` ausführen können.
