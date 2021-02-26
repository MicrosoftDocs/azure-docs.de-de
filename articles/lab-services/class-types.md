---
title: Beispielklassentypen für Azure Lab Services | Microsoft-Dokumentation
description: In diesem Artikel sind einige Typen von Klassen aufgeführt, für die Sie mithilfe von Azure Lab Services Labs einrichten können.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627432"
---
# <a name="class-types-overview---azure-lab-services"></a>Übersicht über Klassentypen: Azure Lab Services

Mithilfe von Azure Lab Services können Sie schnell eine Classroom-Lab-Umgebung in der Cloud einrichten. Die Artikel in diesem Abschnitt bieten Anleitungen zum Einrichten verschiedener Typen von Labs mithilfe von Azure Lab Services.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) ist ein geografisches Informationssystem (GIS).  Sie können ein Lab einrichten, das die verschiedenen Anwendungen von ArcGIS Desktop verwendet, z. B. [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) zum Erstellen, Bearbeiten und Analysieren von 2D-Karten.

Ausführliche Informationen zum Einrichten dieser Art von Lab finden Sie unter [Einrichten eines Labs für ArcMap\ArcGIS Desktop](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Big Data-Analyse
Sie können für einen Big Data-Analysekurs ein GPU-Lab einrichten. Bei dieser Art von Kurs lernen die Kursteilnehmer, mit großen Datenmengen umzugehen und maschinelle und statistische Lernalgorithmen anzuwenden, um Erkenntnisse aus Daten zu gewinnen. Ein wichtiges Ziel für Kursteilnehmer ist es, den Umgang mit Datenanalysetools zu erlernen, z. B. mit dem Open-Source-Softwarepaket von Apache Hadoop, das Tools für die Speicherung, Verwaltung und Verarbeitung von Big Data bereitstellt. 

Ausführliche Informationen zum Einrichten dieser Art von Lab finden Sie unter [Einrichten eines Labs für Big Data-Analysen mithilfe der Docker-Bereitstellung von Hortonworks Data Platform](class-type-big-data-analytics.md).

## <a name="database-management"></a>Datenbankverwaltung
Datenbankkonzepte werden in den Einführungskursen vermittelt, die in den meisten Informatikfachbereichen an Hochschulen angeboten werden. Sie können ein Lab für einen Grundkurs zur Datenbankverwaltung in Azure Lab Services einrichten. Sie können beispielsweise eine Vorlage für virtuelle Computer in einem Lab mit einem [MySQL](https://www.mysql.com/)-Datenbankserver oder einem [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)-Server einrichten.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zum Vermitteln von Kenntnissen zur Verwaltung von relationalen Datenbanken](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Deep Learning mit Verarbeitung natürlicher Sprache
Sie können ein Lab mit Schwerpunkt auf Deep Learning mit Verarbeitung natürlicher Sprache mithilfe von Azure Lab Services einrichten. NLP ist eine Form der künstlichen Intelligenz (KI), die Computern Übersetzungs-, Spracherkennungs- und andere Sprachverständnisfunktionen bietet. Kursteilnehmer, die einen Kurs zur Verarbeitung natürlicher Sprache belegen, erhalten einen virtuellen Linux-Computer, auf dem Sie lernen, wie neuronale Netzwerkalgorithmen angewendet werden, um Deep Learning-Modelle zum Analysieren geschriebener menschlicher Sprache zu entwickeln.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs mit Schwerpunkt auf Deep Learning mit Verarbeitung natürlicher Sprache mithilfe von Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>„Ethisches“ Hacken
Sie können ein Lab für eine Klasse einrichten, die sich auf die forensischen Aspekte des ethischen Hackens konzentriert. Bei Penetrationstests, eine von der Community für ethisches Hacken verwendete Vorgehensweise, versucht eine Person, auf das System oder Netzwerk zuzugreifen, um Schwachstellen zu demonstrieren, die ein böswilliger Angreifer ausnutzen könnte.

In einem Kurs für ethisches Hacken erlernen die Kursteilnehmer moderne Techniken zum Schützen vor Schwachstellen kennen. Jeder Kursteilnehmer erhält einen virtuellen Windows Server-Hostcomputer mit zwei geschachtelten virtuellen Computern: einem virtuellen Computer mit einem [Metaspoiltable3](https://github.com/rapid7/metasploitable3)-Image und einem anderen Computer mit einem [Kali Linux](https://www.kali.org/)-Image. Der virtuelle Metasploitable-Computer wird angegriffen.  Der virtuelle Kali-Computer stellt die Tools zur Verfügung, die zum Ausführen forensischer Aufgaben erforderlich sind.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zum Vermitteln von Kursen für ethisches Hacken](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
[MATLAB](https://www.mathworks.com/products/matlab.html) steht für „Matrix Laboratory“ und bezeichnet eine Programmierplattform von [MathWorks](https://www.mathworks.com/).  Sie kombiniert Rechenleistung und Visualisierung, was sie in den Bereichen Mathematik, Ingenieurwesen, Physik und Chemie zu einem beliebten Tool macht.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zum Kennenlernen von MATLAB](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Netzwerk mit GNS3
Sie können ein Lab für einen Kurs einrichten, in dem Kursteilnehmer unter Verwendung der Software [GNS3](https://www.gns3.com/) virtuelle und reale Netzwerke emulieren, konfigurieren und testen und auftretende Fehler beheben. 

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs für einen Netzwerkkurs](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Project Lead The Way (PLTW)
[Project Lead The Way (PLTW)](https://www.pltw.org/) ist eine gemeinnützige Organisation, die in den USA einen PreK-12-Lehrplan für Informatik, Engineering und biomedizinische Wissenschaft bereitstellt.  In jedem PLTW-Kurs verwenden die Kursteilnehmer eine Reihe von Softwareanwendungen als Teil ihrer praktischen Lernerfahrung.

Ausführliche Informationen zum Einrichten dieser Typen von Labs finden Sie unter [Einrichten von Labs für Project Lead The Way-Kurse](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Python und Jupyter Notebook-Instanzen
Sie können einen Vorlagencomputer in Azure Lab Services mit den Tools einrichten, die erforderlich sind, um Kursteilnehmern die Verwendung von [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io) zu vermitteln. Jupyter Notebooks ist ein Open-Source-Projekt, mit dem Sie problemlos Rich-Text und ausführbaren [Python](https://www.python.org/)-Quellcode in einem einzelnen Zeichenbereich kombinieren können, der als Notebook bezeichnet wird. Das Ausführen eines Notebooks führt zu einem linearen Datensatz von Eingaben und Ausgaben.  Diese Ausgaben können Text, Tabellen mit Informationen, Punktdiagramme und mehr enthalten.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zum Vermitteln von Data Science mit Python und Jupyter Notebooks](class-type-jupyter-notebook.md).

## <a name="shell-scripting-on-linux"></a>Shellskripts unter Linux
Sie können ein Lab zur Schulung in Shellskripts unter Linux einrichten. Die Skripterstellung empfiehlt sich bei der Systemverwaltung, um Administratoren sich wiederholende Aufgaben abzunehmen. In diesem beispielhaften Kurs geht es um klassische Bash-Skripts und erweiterte Skripts. Erweiterte Skripts sind Skripts, die Bash-Befehle und Ruby kombinieren. Dieser Ansatz ermöglicht Ruby das Weiterleiten von Daten, während Bash-Befehle mit der Shell interagieren können.

Kursteilnehmer bekommen einen virtuellen Linux-Computer zugewiesen, um die Grundlagen von Linux zu erlernen und sich mit der Erstellung von Bash-Skripts vertraut zu machen. Bei dem virtuellen Linux-Computer ist der Remotedesktopzugriff aktiviert und die Text-Editoren [gedit](https://help.gnome.org/users/gedit/stable/) und [Visual Studio Code](https://code.visualstudio.com/) sind installiert.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zur Schulung in Shellskripts unter Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>SolidWorks: CAD (Computer-Aided Design)
Sie können ein GPU-Lab einrichten, mit dem Studenten der Ingenieurswissenschaften Zugriff auf [SolidWorks](https://www.solidworks.com/) erhalten.  SolidWorks stellt eine 3D-CAD-Umgebung für die Modellierung von Festkörpern dar.  Mit SolidWorks können Engineers ihre Entwürfe problemlos erstellen, visualisieren, simulieren und dokumentieren.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs für Engineeringkurse mithilfe von SolidWorks](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>SQL-Datenbank und Verwaltung
SQL (Structured Query Language) ist die Standardsprache für die Verwaltung relationaler Datenbanken. Hierzu zählen das Hinzufügen, Aufrufen und Verwalten von Inhalten in einer Datenbank.  Sie können ein Lab einrichten, um Datenbankkonzepte sowohl mithilfe eines Datenbankservers mit [MySQL](https://www.mysql.com/) als auch eines Servers mit [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) zu vermitteln.

Ausführliche Informationen zum Einrichten derartiger Labs finden Sie unter [Einrichten eines Labs zum Vermitteln von Kenntnissen zur Verwaltung von relationalen Datenbanken](class-type-database-management.md).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Einrichten eines Labs mit Schwerpunkt auf Deep Learning mit Verarbeitung natürlicher Sprache mithilfe von Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Shellskripts unter Linux](class-type-shell-scripting-linux.md)
- [„Ethisches“ Hacken](class-type-ethical-hacking.md)