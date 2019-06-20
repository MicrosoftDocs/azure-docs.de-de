---
title: Erstellen und Klonen von Jupyter Notebooks in Azure
description: Azure Notebooks-Projekte verwalten eine Sammlung von Notebooks und verwandten Dateien, die Sie neu erstellen oder aus einer anderen Quelle klonen können.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60237507"
---
# <a name="create-and-clone-projects"></a>Erstellen und Klonen von Projekten

Azure Notebooks organisiert Ihre Jupyter-Notebooks und verwandten Dateien in logischen Gruppen, die als *Projekte* bezeichnet werden. Sie erstellen ein Projekt zunächst als Container, anschließend erstellen oder klonen Sie ein oder mehrere Notebooks innerhalb eines Ordners zusammen mit anderen Projektdateien. (Dieser Vorgang wird im [Tutorial](tutorial-create-run-jupyter-notebook.md) vorgeführt.)

Ein Projekt verwaltet außerdem Metadaten und andere Konfigurationseinstellungen, die den Server betreffen, auf dem die Notizbücher ausgeführt werden, einschließlich benutzerdefinierter Einrichtungsschritte und Paketinstallation. Weitere Informationen finden Sie unter [Verwalten und Konfigurieren von Projekten](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Verwenden des Dashboards „Meine Projekte“

Ihr Dashboard **Meine Projekte** unter `https://notebooks.azure.com/<userID>/projects` ist der Ort, an dem Sie Projekte anzeigen, verwalten und erstellen:

[![Dashboard „Meine Projekte“ in Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Die für Sie auf dem Dashboard möglichen Aktionen hängen davon ab, ob Sie mit dem Konto angemeldet sind, das die Benutzer-ID besitzt:

| Get-Help | Verfügbar für | BESCHREIBUNG |
| --- | --- | --- |
| **Run** | Owner (Besitzer) | Startet den Projektserver und öffnet den Projektordner in Jupyter. (Sie werden wohl häufiger zuerst in den Projektordner navigieren und dann dort ein Notebook starten.) |
| **Download** | Beliebiger Benutzer | Lädt eine Kopie des ausgewählten Projekts als ZIP-Datei herunter. |
| **Teilen** | Beliebiger Benutzer | Zeigt das Freigabepopupfenster, über das Sie eine URL zu einem ausgewählten Projekt erhalten, in sozialen Medien teilen, eine E-Mail mit der URL senden und wahlweise HTML- oder Markdowncode zusammen mit einem Badge „Notebook starten“ (siehe dazu [Abrufen eines Notebook-Badge](#obtain-a-launch-badge)) zusammen mit der URL erhalten können. |
| **Löschen** | Owner (Besitzer) | Löscht das ausgewählte Projekt. Dieser Vorgang kann nicht rückgängig gemacht werden. |
| **Terminal** | Owner (Besitzer) | Startet den Projektserver und öffnet dann ein neues Fenster mit dem Bash-Terminal für den betreffenden Server. |
| **+ Neues Projekt** | Owner (Besitzer) | Erstellt ein neues Projekt. Siehe dazu [Erstellen eines neuen Projekts](#create-a-new-project). |
| **Upload GitHub Repo** (GitHub-Repository hochladen) | Owner (Besitzer) | Importiert ein Projekt von GitHub. [Importieren eines Projekts von GitHub](#import-a-project-from-github). |
| **Klonen** | Beliebiger Benutzer | Kopiert ein ausgewähltes Projekt in Ihr eigenes Konto. Fordert Sie zur Anmeldung auf, wenn das noch nicht erfolgt ist. Siehe dazu [Klonen eines Projekts](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Abrufen eines Start-Badge

Wenn Sie den Befehl **Freigabe** verwenden und die Registerkarte **Einbetten** auswählen, können Sie entweder HTML-Code oder Markdown kopieren, der ein Badge „Notebook starten“ erstellt:

![Badge „Notebook starten“](https://notebooks.azure.com/launch.png)

Wenn Sie nicht über ein Azure Notebooks-Projekt verfügen, können Sie einen Link erstellen, der Klone unter Verwendung der folgenden Vorlagen direkt von GitHub erstellt – setzen Sie die passenden Namen für Benutzername und Repository ein:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Erstellen eines neuen Projekts

Wenn Sie den Befehl **+ Neues Projekt** verwenden, zeigt Azure Notebooks ein Popupfenster **Neues Projekt erstellen** an. Geben Sie in diesem Popupfenster die folgenden Informationen ein, und wählen Sie dann **Erstellen** aus:

| Feld | BESCHREIBUNG |
| --- | --- |
| Projektname | Ein Anzeigename für Ihr Projekt, den Azure Notebooks zu Anzeigezwecken verwendet. Beispielsweise „Mein Notebook-Projekt“. |
| Projekt-ID | Ein benutzerdefinierter Bezeichner, der Teil der URL wird, die zum Freigeben eines Projekts verwendet wird (das Format lautet `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Diese ID darf nur aus Buchstaben, Ziffern und Bindestrichen bestehen, ist auf 30 Zeichen beschränkt und darf keine [reservierte Projekt-ID](#reserved-project-ids) sein. Falls Sie nicht wissen, was Sie verwenden sollen, können Sie sich nach einer gängigen Konvention richten. Dazu legen Sie den Projektnamen in Kleinbuchstaben zugrunde und wandeln Leerzeichen in Bindestriche um, wie etwa in „mein-notebook-projekt“ (ggf. gekürzt, um die Längenbeschränkung einzuhalten). |
| Öffentlich | Wenn diese Option festgelegt ist, kann jeder, der über den Link verfügt, auf das Projekt zugreifen. Deaktivieren Sie diese Option, wenn Sie ein privates Projekt erstellen. |
| Projekt mit einer Infodatei initialisieren | Wenn diese Option festgelegt ist, wird eine standardmäßige Datei *README.md* im Projekt erstellt. Die Datei *README.md* ist der Ort, an dem Sie ggf. Dokumentation zu Ihrem Projekt bereitstellen. |

### <a name="reserved-project-ids"></a>Reservierte Projekt-IDs

Die folgenden reservierten Wörter können nicht allein als Projekt-IDs verwendet werden. Sie können jedoch als Teil längerer Projekt-IDs verwendet werden.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| about | account | administration | api | blog | classroom |
| Inhalt | Dashboard | explore | faq | help | html |
| home | Import | Bibliothek | management | Neu | notebook |
| notebooks | pdf | preview | Preise | Profil | search |
| status | support | test | | | |

Wenn Sie versuchen, eines dieser Wörter als Projekt-ID zu verwenden, wird in den Popups **Neues Projekt erstellen** und **Projekteinstellungen** „Bibliotheks-ID ist ein reservierter Bezeichner“ angezeigt.

Da eine Projekt-ID auch Teil der URL eines Projekts ist, kann Werbeblockersoftware die Verwendung bestimmter Schlüsselwörter, wie beispielsweise „advert“ blockieren. Verwenden Sie in solchen Fällen ein anderes Wort in der Projekt-ID.

## <a name="import-a-project-from-github"></a>Importieren eines Projekts von GitHub

Sie können problemlos ein vollständiges öffentliches GitHub-Repository als Projekt importieren, einschließlich aller Daten und *README.md*-Dateien. Verwenden Sie den Befehl **GitHub-Repository hochladen**, geben Sie die folgenden Details im Popupfenster ein, und wählen Sie dann **Importieren** aus:

| Feld | BESCHREIBUNG |
| --- | --- |
| GitHub-Repository | Der Name des Quellrepositorys auf github.com. Um beispielsweise die Jupyter-Notebooks für Azure Cognitive Services unter [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) zu klonen, geben Sie „Microsoft/cognitive-services-notebooks“ ein.  |
| Clone recursively (Rekursiv klonen) | GitHub-Repositorys können mehrere untergeordnete Repositorys enthalten. Legen Sie diese Option fest, wenn Sie das übergeordnete Repository und alle seine untergeordneten Repositorys klonen möchten. Da ein Repository über viele untergeordnete Repositorys verfügen kann, lassen Sie diese Option im Normalfall deaktiviert, es sei denn, Sie wissen, dass Sie sie brauchen. |
| Projektname | Ein Anzeigename für Ihr Projekt, den Azure Notebooks zu Anzeigezwecken verwendet. |
| Projekt-ID | Ein benutzerdefinierter Bezeichner, der Teil der URL wird, die zum Freigeben eines Projekts verwendet wird (das Format lautet `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Diese ID darf nur aus Buchstaben, Ziffern und Bindestrichen bestehen, ist auf 30 Zeichen beschränkt und darf keine [reservierte Projekt-ID](#reserved-project-ids) sein. Falls Sie nicht wissen, was Sie verwenden sollen, können Sie sich nach einer gängigen Konvention richten. Dazu legen Sie den Projektnamen in Kleinbuchstaben zugrunde und wandeln Leerzeichen in Bindestriche um, wie etwa in „mein-notebook-projekt“ (ggf. gekürzt, um die Längenbeschränkung einzuhalten). |
| Öffentlich | Wenn diese Option festgelegt ist, kann jeder, der über den Link verfügt, auf das Projekt zugreifen. Deaktivieren Sie diese Option, wenn Sie ein privates Projekt erstellen. |

Beim Importieren eines Repositorys von GitHub wird auch dessen Verlauf importiert. Sie können Git-Standardbefehle im Terminal verwenden, um neue Änderungen zu committen, Änderungen per Pull von GitHub zu übertragen usw.

## <a name="clone-a-project"></a>Klonen eines Projekts

Beim Klonen wird eine Kopie eines vorhandenen Projekts in Ihrem eigenen Konto erstellt, wo Sie es dann ausführen und alle Notebook- oder sonstigen Dateien im Projekt ändern können. Ferner können Sie Klonen auch verwenden, um Kopien eigener Projekte zu erstellen, in denen Sie dann experimentieren oder andere Arbeiten verrichten können, ohne das Originalprojekt zu beeinträchtigen.

So klonen Sie ein Projekt:

1. Klicken Sie auf dem Dashboard **Meine Projekte** mit der rechten Maustaste auf das gewünschte Projekt, und wählen Sie dann **Klonen** (Tastenkombination: c) aus.

    ![Befehl „Klonen“ im Kontextmenü von Projekten](media/clone-command.png)

1. Geben Sie im Popupfenster **Projekt klonen** einen Namen und eine ID für den Klon ein, und geben Sie an, ob der Klon öffentlich ist. Dies sind die gleichen Einstellungen wie für ein [neues Projekt](#create-a-new-project).

    ![Popupfenster „Projekt klonen“](media/clone-project.png)

1. Nachdem Sie die Schaltfläche **Klonen** ausgewählt haben, navigiert Azure Notebooks direkt zur Kopie.

## <a name="next-steps"></a>Nächste Schritte

- [Erkunden von Beispielnotebooks](azure-notebooks-samples.md)
- [Gewusst wie: Konfigurieren und Verwalten von Projekten](configure-manage-azure-notebooks-projects.md)
- [Gewusst wie: Installieren von Paketen aus einem Notebook](install-packages-jupyter-notebook.md)
- [Gewusst wie: Zeigen einer Bildschirmpräsentation](present-jupyter-notebooks-slideshow.md)
- [Gewusst wie: Arbeiten mit Datendateien](work-with-project-data-files.md)
- [Gewusst wie: Zugreifen auf Datenressourcen](access-data-resources-jupyter-notebooks.md)
- [Gewusst wie: Verwenden von Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
