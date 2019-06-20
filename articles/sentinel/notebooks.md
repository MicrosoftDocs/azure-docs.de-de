---
title: Ermittlungsfunktionen mit Notebooks in Azure Sentinel Preview | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Notebooks mit den Ermittlungsfunktionen von Azure Sentinel verwendet werden.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228640"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks

> [!IMPORTANT]
> Azure Sentinel ist derzeit als öffentliche Vorschauversion (Public Preview) verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grundlage für Azure Sentinel ist der Datenspeicher; dieser zeichnet sich durch Hochleistungsabfragen, ein dynamisches Schema und die Skalierung auf große Datenvolumen aus. Das Azure Sentinel-Portal und alle Azure Sentinel-Tools greifen über eine gemeinsame API auf diesen Datenspeicher zu. Die gleiche API ist auch für externe Tools wie [Jupyter Notebooks](https://jupyter.org/) und Python verfügbar. Viele gängige Aufgaben können im Portal durchgeführt werden, Jupyter erweitert jedoch den Rahmen der Verarbeitung dieser Daten. Jupyter bietet sowohl umfassende Programmierbarkeit als auch eine große Sammlung von Bibliotheken für Machine Learning, Visualisierung und Datenanalyse. Dies macht Jupyter zu einem überzeugenden Tool für Sicherheitsuntersuchungen und Aufspüren von Sicherheitsrisiken.

![Beispielnotebook](./media/notebooks/sentinel-nb-mapandtimeline.png)

Wir haben die Jupyter-Benutzeroberfläche in das Azure Sentinel-Portal integriert, sodass Sie Notebooks auf einfache Weise erstellen und mit diesen Notebooks Daten analysieren können. Mit der *Kqlmagic*-Bibliothek als verbindende Komponente können Sie Abfragen von Azure Sentinel annehmen und diese direkt in einem Notebook ausführen. Für Abfragen wird die Abfragesprache [Kusto](https://kusto.azurewebsites.net/docs/query/index.html) verwendet. Verschiedene von Microsoft-Sicherheitsanalysten entwickelte Notebooks werden mit Azure Sentinel verpackt. Einige diese Notebooks wurden auf ein bestimmtes Szenario ausgelegt, und sie sind ohne weitere Änderungen sofort verwendungsfähig. Andere sollen beispielhaft Techniken und Funktionen veranschaulichen, die Sie kopieren oder für die Verwendung in Ihren eigenen Notebooks anpassen können. Andere Notebooks wiederum können auch aus der Azure Sentinel-Community GitHub importiert werden.

Diese integrierte Jupyter-Benutzererfahrung verwendet [Azure Notebooks](https://notebooks.azure.com/) zum Speichern, Freigeben und Ausführen von Notebooks. Sie können diese Notebooks auch lokal (wenn Sie über eine Python-Umgebung verfügen und Jupyter auf Ihrem Computer installiert ist) oder in anderen JupyterHub-Umgebungen wie Azure Databricks ausführen.

Notebooks verfügen über zwei Komponenten:

- die browserbasierte Benutzeroberfläche, in der Sie Abfragen und Code eingeben und ausführen und in der die Ergebnisse der Ausführung angezeigt werden.
- einen *Kernel*, der für das Analysieren und Ausführen des Codes selbst zuständig ist. 

In Azure Notebooks wird dieser Kernel standardmäßig unter Azure *Free Cloud Compute and Storage* ausgeführt. Wenn Ihre Notebooks komplexe Machine Learning-Modelle oder Visualisierungen einschließen, empfiehlt es sich, leistungsfähigere, dedizierte Computeressourcen wie [Data Science Virtual Machines](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM) zu verwenden. Die Vertraulichkeit der Notebooks in Ihrem Konto bleibt gewahrt, sofern Sie sie nicht freigeben.

Die Azure Sentinel-Notebooks nutzen viele beliebte Python-Bibliotheken wie pandas, matplotlib, bokeh und andere. Zahlreiche weitere Python-Pakete stehen zur Auswahl, die u.a. die folgenden Bereiche abdecken:

- Visualisierungen und Grafiken
- Datenverarbeitung und -analyse
- Statistiken und numerisches Computing
- Machine Learning und Deep Learning

Im Paket [msticpy](https://github.com/Microsoft/msticpy/) haben wir zudem einige Open Source-Jupyter-Sicherheitstools veröffentlicht. Dieses Paket wird in vielen der enthaltenen Notebooks verwendet. Msticpy-Tools sollen Sie insbesondere beim Erstellen von Notebooks zum Aufspüren und Untersuchen von Sicherheitsrisiken unterstützen, und wir arbeiten aktiv an neuen Features und Verbesserungen.

Derzeit sind u. a. folgende Notebooks enthalten:

- **Guided investigation - Process Alerts** (Geführte Untersuchung – Warnungen verarbeiten): Hiermit können Sie Warnungen durch Analysieren der Aktivität auf den betroffenen Hosts schnell selektieren.
- **Guided hunting - Windows host explorer** (Geführte Suche – Windows-Host-Explorer): Hiermit können Sie Kontoaktivitäten, Prozessausführungen, Netzwerkaktivitäten und andere Ereignisse auf einem Host untersuchen.  
- **Guided hunting - Office365-Exploring** (Geführte Suche – Office365-Untersuchung): Hiermit können Sie nach verdächtigen Aktivitäten in Office 365 in mehreren Office 365-Datasets suchen.

Im [GitHub-Repository der Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) finden sich alle künftig von Microsoft erstellten oder von der Community beigesteuerten Azure Sentinel-Notebooks.

## <a name="run-a-notebook"></a>Ausführen eines Notebooks

Im folgenden Beispiel wird ein Azure Notebooks-Projekt im Azure Sentinel-Portal erstellt, und das Projekt wird mit Notebooks gefüllt. Vor dem Verwenden dieser Notebooks empfiehlt es sich, eine Kopie des Notebooks zu erstellen und mit dieser Kopie zu arbeiten. Durch das Arbeiten mit Kopien können Sie problemlos auf künftige Versionen von Notebooks aktualisieren, ohne dass Ihre Daten überschrieben werden.

1. Klicken Sie im Azure Sentinel-Portal im Navigationsmenü auf **Notebooks**. Klicken Sie zum Erstellen eines neuen Azure Notebooks-Projekts auf **Clone Azure Sentinel Notebooks** (Azure Sentinel Notebooks klonen); wenn Sie ein vorhandenes Notebooks-Projekt öffnen möchten, klicken Sie auf **Go to your Notebooks** (Zu eigenen Notebooks wechseln).
  
   ![Notebooks auswählen](./media/notebooks/sentinel-az-notebooks-home.png)

2. Wenn Sie im vorherigen Schritt **Clone Azure Sentinel Notebooks** (Azure Sentinel Notebooks klonen) ausgewählt haben, wird das folgende Dialogfeld angezeigt. Klicken Sie auf **Importieren**, um das GitHub-Repository in Ihr Azure Notebooks-Projekt zu klonen. Wenn Sie über kein vorhandenes Azure Notebooks-Konto verfügen, werden Sie aufgefordert, eines zu erstellen und sich anzumelden.

   ![Notebook importieren](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Beim Erstellen eines neuen Projekts müssen Sie das Projekt benennen. Übernehmen Sie den Standardnamen, oder geben Sie einen neuen Namen ein. Aktivieren Sie nicht die Option **Clone Recursively** (Rekursiv klonen), denn diese Option bezieht sich auf verknüpfte GitHub-Repositorys. Wenn Sie auf **Importieren** klicken, werden die GitHub-Inhalte geklont; dieser Vorgang kann einige Minuten dauern.

   ![Notebook importieren](./media/notebooks/sentinel-create-nb-project.png)

4. Öffnen Sie den Ordner **Notebooks**, um die Notebooks anzuzeigen. In jedem Notebook werden Sie durch die einzelnen Schritte zum Durchführen einer Suche oder Untersuchung geführt. Bibliotheken und andere benötigte Abhängigkeiten des Notebooks können über das Notebook selbst oder über eine einfache Konfigurationsprozedur installiert werden. Die Konfiguration, die Ihr Notebook-Projekt mir Ihrem Azure Sentinel-Abonnement verknüpft, wurde automatisch in den vorherigen Schritten festgelegt. Ihre Notebooks können nun im Azure Sentinel-Log Analytics-Arbeitsbereich ausgeführt werden.

   ![Repository importieren](./media/notebooks/sentinel-open-notebook1.png)

5. Öffnen Sie ein Notebook. Für die Ausführung der Notebooks ist standardmäßig „Free Compute“ ausgewählt (hervorgehoben). Wenn Sie eine zu verwendende DSVM konfiguriert haben (siehe oben), wählen Sie die DSVM aus, und führen Sie vor dem Öffnen des ersten Notebooks die Authentifizierung aus. Klicken Sie auf ein Notebook, um es zu öffnen.

   ![Notebook auswählen](./media/notebooks/sentinel-open-notebook2.png)

6. Wählen Sie die Python-Version aus. Beim erstmaligen Öffnen eines Notebooks werden Sie u.U. aufgefordert, eine Kernelversion auszuwählen. Wählen Sie andernfalls den zu verwendenden Kernel wie beschrieben aus. Als Kernel sollte Python 3.6 oder höher ausgewählt sein (obere rechte Ecke des Notebook-Fensters).

   ![Notebook auswählen](./media/notebooks/sentinel-select-kernel.png)

Eine kurze Einführung zum Abfragen von Daten in Azure Sentinel, finden Sie im Notebook [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) im Hauptordner „Notebooks“. Weitere Beispiel-Notebooks finden sich im Unterordner **Sample-Notebooks**. Die Beispiel-Notebooks wurden mit Daten gespeichert, sodass die gewünschte Ausgabe leichter überprüft werden kann (wir empfehlen für die Anzeige den [nbviewer](https://nbviewer.jupyter.org/)). Der Ordner **HowTos** enthält Beschreibungen zu Notebooks, u.a. zum Festlegen der Python-Standardversion, zum Konfigurieren einer DSVM, zum Erstellen von Azure Sentinel-Lesezeichen in einem Notebook sowie zu anderen Themen.

Diese Notebooks fungieren einerseits als hilfreiche Tools, Sie dienen andererseits aber auch zur Veranschaulichung mit Codebeispielen, die Sie in der Umgebung Ihrer eigenen Notebooks verwenden können.

Wir freuen uns über Feedback, egal ob es sich um Vorschläge, Empfehlungen für Funktionen, eigene Beiträge von Notebooks, Fehlerberichte oder Verbesserungen und Ergänzungen zur vorhandenen Notebooks handelt. Besuchen Sie die [Azure Sentinel-Community GitHub](https://github.com/Azure/Azure-Sentinel), um ein Problem zu melden oder eine Verzweigung anzulegen oder um einen Beitrag hochzuladen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden die ersten Schritte beim Verwenden von Jupyter-Notebooks in Azure Sentinel erläutert. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Use bookmarks to save interesting information while hunting (Verwenden von Lesezeichen zum Speichern interessanter Informationen bei der Ermittlung)](bookmarks.md)