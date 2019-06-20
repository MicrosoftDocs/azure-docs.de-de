---
title: Erstellen eines Azure Machine Learning Studio-Arbeitsbereichs
titleSuffix: Azure Machine Learning Studio
description: Um Azure Machine Learning Studio verwenden zu können, benötigen Sie einen Machine Learning Studio-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 7aeee4f24f6c7133ad978bc0c6c7fb8853bc4c35
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62109347"
---
# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Erstellen und Freigeben eines Azure Machine Learning Studio-Arbeitsbereichs

Um Azure Machine Learning Studio verwenden zu können, benötigen Sie einen Machine Learning Studio-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.

## <a name="create-a-studio-workspace"></a>Erstellen eines Studio-Arbeitsbereichs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

    > [!NOTE]
    > Um sich anzumelden und einen Studio-Arbeitsbereich zu erstellen, müssen Sie ein Azure-Abonnementadministrator sein. 
    >
    > 

2. Klicken Sie auf **+Neu**.

3. Geben Sie im Suchfeld **Machine Learning Studio Workspace** ein, und wählen Sie das entsprechende Element aus. Klicken Sie dann am unteren Rand der Seite auf **Erstellen**.

4. Geben Sie die Daten für Ihren Arbeitsbereich ein:

   - Der *Arbeitsbereichsname* darf bis zu 260 Zeichen enthalten und darf nicht auf ein Leerzeichen enden. Der Name darf nicht die folgenden Zeichen enthalten: `< > * % & : \ ? + /`
   - Der von Ihnen gewählte (oder erstellte) *Webdiensttarif* wird zusammen mit dem zugehörigen von Ihnen ausgewählten *Tarif* verwendet, wenn Sie Webdienste aus diesem Arbeitsbereich bereitstellen.

     ![Erstellen eines neuen Studio-Arbeitsbereichs](./media/create-workspace/create-new-workspace.png)

5. Klicken Sie auf **Create**.

> [!NOTE]
> In Machine Learning Studio ist ein von Ihnen angegebenes Azure-Speicherkonto zum Speichern temporärer Daten beim Ausführen des Workflows erforderlich. Wenn das Speicherkonto nach dem Erstellen des Arbeitsbereichs gelöscht wird oder die Zugriffsschlüssel geändert werden, funktioniert der Arbeitsbereich nicht mehr, und alle darin enthaltenen Experimente schlagen fehl.
Wenn Sie das Speicherkonto versehentlich löschen, können Sie es mit identischem Namen und in derselben Region neu erstellen und die Zugriffsschlüssel erneut synchronisieren. Wenn Sie Zugriffsschlüssel für Speicherkonten geändert haben, müssen Sie die Zugriffsschlüssel im Arbeitsbereich über das Azure-Portal neu synchronisieren.

Nachdem der Arbeitsbereich bereitgestellt wurde, können Sie ihn in Machine Learning Studio öffnen.

1. Navigieren Sie zu Machine Learning Studio unter [https://studio.azureml.net/](https://studio.azureml.net/).

2. Wählen Sie Ihren Arbeitsbereich in der oberen rechten Ecke aus.

    ![Arbeitsbereich auswählen](./media/create-workspace/open-workspace.png)

3. Klicken Sie auf **Meine Experimente**.

    ![Experimente öffnen](./media/create-workspace/my-experiments.png)

Informationen zum Verwalten des Studio-Arbeitsbereichs finden Sie unter [Verwalten eines Azure Machine Learning Studio-Arbeitsbereichs](manage-workspace.md).
Wenn ein Problem beim Erstellen des Arbeitsbereichs auftritt, finden Sie weitere Informationen unter [Leitfaden zur Problembehandlung: Erstellen eines Machine Learning Studio-Arbeitsbereichs und Verbindungsaufbau](troubleshooting-creating-ml-workspace.md).


## <a name="share-an-azure-machine-learning-studio-workspace"></a>Gemeinsames Nutzen eines Azure Machine Learning Studio-Arbeitsbereichs
Sobald ein Machine Learning Studio-Arbeitsbereich erstellt wurde, können Sie Benutzer zu Ihrem Arbeitsbereich einladen, um den Zugriff auf Ihren Arbeitsbereich und alle zugehörigen Experimente, Datasets, Notizbücher usw. freizugeben. Sie können Benutzer in einer der beiden Rollen hinzufügen:

* **Benutzer**: Ein Arbeitsbereichsbenutzer kann Experimente, Datasets etc. im Arbeitsbereich erstellen, öffnen, ändern und löschen.
* **Besitzer**: Zusätzlich zu den Möglichkeiten eines Benutzers kann ein Besitzer Benutzer im Arbeitsbereich einladen und entfernen.

> [!NOTE]
> Das Administratorkonto, das den Arbeitsbereich erstellt, wird dem Arbeitsbereich automatisch als Arbeitsbereichsbesitzer hinzugefügt. Allerdings erhalten andere Administratoren oder Benutzer in diesem Abonnement nicht automatisch Zugriff auf den Arbeitsbereich – Sie müssen sie explizit einladen.
> 
> 

### <a name="to-share-a-studio-workspace"></a>So geben Sie einen Studio-Arbeitsbereich frei

1. Melden Sie sich bei Azure Machine Learning Studio unter [https://studio.azureml.net/Home](https://studio.azureml.net/Home) an.

2. Klicken Sie im linken Bereich auf **EINSTELLUNGEN**.

3. Klicken Sie auf die Registerkarte **BENUTZER**.

4. Klicken Sie im unteren Seitenbereich auf **WEITERE BENUTZER EINLADEN**.

    ![Studio-Einstellungen](./media/create-workspace/settings.png)

5. Geben Sie mindestens eine E-Mail-Adresse ein. Die Benutzer benötigen ein gültiges Microsoft-Konto oder ein Organisationskonto (aus Azure Active Directory).

6. Wählen Sie, ob Benutzer als Besitzer oder Benutzer hinzugefügt werden sollen.

7. Klicken Sie auf die Häkchenschaltfläche **OK** .

Jeder hinzugefügte Benutzer erhält eine E-Mail mit Anweisungen zum Anmelden beim freigegebenen Arbeitsbereich.

> [!NOTE]
> Damit Benutzer Webdienste in diesem Arbeitsbereich bereitstellen oder verwalten können, müssen sie Mitwirkender oder Administrator im Azure-Abonnement sein. 



