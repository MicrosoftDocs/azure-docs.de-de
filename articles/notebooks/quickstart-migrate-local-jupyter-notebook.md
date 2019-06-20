---
title: Migrieren eines lokalen Jupyter Notebooks zu Azure Notebooks
description: Übertragen Sie schnell ein Jupyter-Notebook vom lokalen Computer oder von einer Web-URL in Azure Notebooks, und geben Sie es dann für die Zusammenarbeit frei.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 7df64c3fb70bdf3e7689787ec558bfe0e4942352
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754007"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Schnellstart: Migrieren eines lokalen Jupyter Notebooks

Auf Jupyter-Notebooks, die Sie lokal auf Ihrem Computer erstellen, haben nur Sie Zugriff. Sie können Ihre Dateien auf verschiedene Weise teilen, aber dann haben die Empfänger eine eigene, lokale Kopie des Notebooks, und es wird für Sie schwierig, die von ihnen vorgenommenen Änderungen zu integrieren. Sie können Notebooks auch in einem gemeinsam verwendeten Onlinerepository wie GitHub speichern, aber das setzt immer noch voraus, dass jeder Mitwirkende über eine eigene lokale Installation von Jupyter mit der gleichen Konfiguration wie Ihre verfügt.

Durch Migrieren Ihrer lokalen oder auf Repositorys gehosteten Notebooks zu Azure Notebooks erreichen Sie die Speicherung in der Cloud, von wo aus Sie sie sofort mit Ihren Mitwirkenden teilen können. Diese Mitwirkenden benötigen lediglich einen Browser zum Anzeigen und Ausführen Ihrer Notebooks, und wenn sie sich bei Azure Notebooks [anmelden](quickstart-sign-in-azure-notebooks.md), können sie auch Änderungen vornehmen.

Dieser Schnellstart veranschaulicht den Migrationsprozesss für ein Notebook von Ihrem lokalen Computer oder einer sonstigen Datei-URL, die zugänglich ist. Informationen zum Migrieren von Notebooks aus einem GitHub-Repository finden Sie unter [Schnellstart: Klonen eines Notebooks](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Erstellen eines Projekts auf Azure Notebooks

1. Navigieren Sie zu [Azure Notebooks](https://notebooks.azure.com), und melden Sie sich an. (Ausführliche Informationen hierzu finden Sie unter [Schnellstart: Anmelden bei Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Wählen Sie oben auf Ihrer öffentlichen Profilseite **Meine Projekte** aus:

    ![Link „Meine Projekte“ am oberen Rand des Browserfensters](media/quickstarts/my-projects-link.png)

1. Klicken Sie auf der Seite **My Projects** (Meine Projekte) auf **+ New Project** (+ Neues Projekt, Tastenkombination: n). Wenn Ihr Browserfenster schmal ist, wird möglicherweise nur **+** für die Schaltfläche angezeigt:

    ![Befehl „New Project“ (Neues Projekt) auf der Seite „My Projects“ (Meine Projekte)](media/quickstarts/new-project-command.png)

1. Geben Sie im Popupfenster **Neues Projekt erstellen**, das dann angezeigt wird, passende Werte für das zu migrierende Notebook in den Feldern **Projektname** und **Projekt-ID** ein, deaktivieren Sie die Optionen für **Öffentliches Projekt** und **README.md-Datei erstellen**, und wählen Sie dann **Erstellen** aus.

## <a name="upload-the-local-notebook"></a>Hochladen des lokalen Notebooks

1. Wählen Sie auf der Projektseite **Hochladen** (was möglicherweise nur als Pfeil nach oben angezeigt wird, wenn Ihr Browserfenster klein ist) und dann „1“ aus. Wählen Sie im angezeigten Popupfenster **Von Computer** aus, wenn sich Ihr Notebook in Ihrem lokalen Dateisystem befindet, oder **Von URL**, wenn Ihr Notebook online gespeichert ist:

    ![Befehl zum Hochladen eines Notebooks von einer URL oder dem lokalen Computer](media/quickstarts/upload-from-computer-url-command.png)

   (Nur zur Erinnerung, falls sich Ihr Notebook in einem GitHub-Repository befindet, führen Sie stattdessen die Schritte unter [Schnellstart: Klonen eines Notebooks](quickstart-clone-jupyter-notebook.md) aus.)

   - Wenn Sie **Von Computer** verwenden, ziehen Sie Ihre *.ipynb*-Dateien auf das Popupfenster, und legen Sie sie dort ab, oder wählen Sie **Dateien auswählen**, navigieren Sie dann zu den zu importierenden Dateien, und wählen Sie sie aus. Wählen Sie dann die Option **Hochladen**. Die hochgeladenen Dateien erhalten die gleichen Namen wie die lokalen Dateien. (Die Inhalte von *.ipynb_checkpoints*-Ordnern brauchen nicht hochgeladen zu werden.)

     ![Popupfenster „Vom Computer hochladen“](media/quickstarts/upload-from-computer-popup.png)

   - Wenn Sie **Von URL** verwenden, geben Sie die Quelladresse im Feld **Datei-URL** und den Dateinamen, der dem Notebook in Ihrem Projekt zugewiesen werden soll, im Feld **Dateiname** ein. Wählen Sie dann die Option **Hochladen**. Wenn Sie über mehrere Dateien mit separaten URLs verfügen, verwenden Sie den Befehl **+ Datei hinzufügen**, um die erste eingegebene URL zu überprüfen; anschließend stellt das Popup neue Felder für eine weitere Datei zur Verfügung.

     ![Popup „Von URL hochladen“](media/quickstarts/upload-from-url-popup.png)

1. Öffnen Sie Ihr neu hochgeladenes Notebook, und führen Sie es aus, um Inhalt und Betrieb zu prüfen. Wenn Sie fertig sind, wählen Sie **Datei** > **Beenden und schließen** aus, um das Notebook zu schließen.

1. Um einen Link zu Ihrem hochgeladenen Notebook zu teilen, klicken Sie mit der rechten Maustaste auf die Datei im Projekt, wählen Sie **Link kopieren** (Tastenkombination: y) aus, und fügen Sie diesen Link dann in die entsprechende Nachricht ein. Alternativ können Sie das Projekt mit dem Steuerelement **Freigabe** auf der Projektseite als Ganzes teilen.

1. Um andere Dateien als Notebooks zu bearbeiten, klicken Sie mit der rechten Maustaste auf die Datei im Projekt, und wählen Sie **Edit file** (Datei bearbeiten; Tastenkombination: i) aus. Mit der Standardaktion **Run** (Ausführen; Tastenkombination: r) wird nur der Dateiinhalt angezeigt, Bearbeiten ist nicht möglich.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Ausführen eines Jupyter-Notebooks für lineare Regression](tutorial-create-run-jupyter-notebook.md)
