---
title: 'ML Studio (Classic): Erstellen eines Arbeitsbereichs – Azure'
description: Um Azure Machine Learning Studio (klassisch) verwenden zu können, benötigen Sie einen (klassischen) Machine Learning Studio-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520543"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Erstellen und Freigeben eines Machine Learning Studio (Classic)-Arbeitsbereichs

**GILT FÜR:**  ![Dies ist ein Häkchen, d. h., dieser Artikel bezieht sich auf Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic)   ![Dies ist ein X, d. h., dieser Artikel bezieht sich auf Azure Machine Learning. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Um Azure Machine Learning Studio (klassisch) verwenden zu können, benötigen Sie einen (klassischen) Machine Learning Studio-Arbeitsbereich. Dieser Arbeitsbereich enthält die Tools, die zum Erstellen, Verwalten und Veröffentlichen von Experimenten erforderlich sind.

## <a name="create-a-studio-classic-workspace"></a>Erstellen eines (klassischen) Studio-Arbeitsbereichs

Um einen Arbeitsbereich in Machine Learning Studio (klassisch) zu öffnen, müssen Sie bei dem Microsoft-Konto angemeldet sein, das zum Erstellen des Arbeitsbereichs verwendet wurde, oder Sie müssen eine Einladung des Besitzers zur Teilnahme am Arbeitsbereich erhalten haben. Sie können im Azure-Portal den Arbeitsbereich verwalten und haben dort u.a. die Möglichkeit zum Ändern des Besitzers und der Zugriffskonfiguration.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)

    > [!NOTE]
    > Um sich anzumelden und einen (klassischen) Studio-Arbeitsbereich zu erstellen, müssen Sie ein Azure-Abonnementadministrator sein. 
    >
    > 

2. Klicken Sie auf **+Neu**.

3. Geben Sie im Suchfeld **Machine Learning Studio (classic) Workspace** ein, und wählen Sie das entsprechende Element aus. Klicken Sie dann am unteren Rand der Seite auf **Erstellen**.

4. Geben Sie die Daten für Ihren Arbeitsbereich ein:

   - Der *Arbeitsbereichsname* darf bis zu 260 Zeichen enthalten und darf nicht auf ein Leerzeichen enden. Der Name darf nicht die folgenden Zeichen enthalten: `< > * % & : \ ? + /`
   - Der von Ihnen gewählte (oder erstellte) *Webdiensttarif* wird zusammen mit dem zugehörigen von Ihnen ausgewählten *Tarif* verwendet, wenn Sie Webdienste aus diesem Arbeitsbereich bereitstellen.

     ![Erstellen eines neuen (klassischen) Studio-Arbeitsbereichs](./media/create-workspace/create-new-workspace.png)

5. Klicken Sie auf **Erstellen**.

   Machine Learning ist derzeit in einer begrenzten Anzahl an Regionen verfügbar. Falls Ihr Abonnement keine dieser Regionen beinhaltet, wird ggf. die Fehlermeldung „Sie verfügen über keine Abonnements für die zulässigen Regionen.“ angezeigt.  Um anzufordern, dass Ihrem Abonnement eine Region hinzugefügt wird, erstellen Sie eine neue Microsoft-Supportanfrage im Azure-Portal und wählen **Abrechnung** als Problemtyp aus. Folgen Sie dann den Anweisungen, um Ihre Anfrage zu übermitteln.


> [!NOTE]
> In Machine Learning Studio (klassisch) ist ein von Ihnen angegebenes Azure-Speicherkonto zum Speichern temporärer Daten beim Ausführen des Workflows erforderlich. Wenn das Speicherkonto nach dem Erstellen des Arbeitsbereichs gelöscht wird oder die Zugriffsschlüssel geändert werden, funktioniert der Arbeitsbereich nicht mehr, und alle darin enthaltenen Experimente schlagen fehl.
Wenn Sie das Speicherkonto versehentlich löschen, können Sie es mit identischem Namen und in derselben Region neu erstellen und die Zugriffsschlüssel erneut synchronisieren. Wenn Sie Zugriffsschlüssel für Speicherkonten geändert haben, müssen Sie die Zugriffsschlüssel im Arbeitsbereich über das Azure-Portal neu synchronisieren.

Nachdem der Arbeitsbereich bereitgestellt wurde, können Sie ihn in Machine Learning Studio (klassisch) öffnen.

1. Navigieren Sie zu Machine Learning Studio (klassisch) unter [https://studio.azureml.net/](https://studio.azureml.net/).

2. Wählen Sie Ihren Arbeitsbereich in der oberen rechten Ecke aus.

    ![Arbeitsbereich auswählen](./media/create-workspace/open-workspace.png)

3. Klicken Sie auf **Meine Experimente**.

    ![Experimente öffnen](./media/create-workspace/my-experiments.png)

Informationen zum Verwalten des (klassischen) Studio-Arbeitsbereichs finden Sie unter [Verwalten eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](manage-workspace.md).
Wenn ein Problem beim Erstellen des Arbeitsbereichs auftritt, finden Sie weitere Informationen unter [Leitfaden zur Problembehandlung: Erstellen eines (klassischen) Machine Learning Studio-Arbeitsbereichs und Herstellen einer Verbindung](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Freigeben eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs
Sobald ein (klassischer) Machine Learning Studio-Arbeitsbereich erstellt wurde, können Sie Benutzer zu Ihrem Arbeitsbereich einladen, um den Zugriff auf Ihren Arbeitsbereich und alle zugehörigen Experimente, Datasets usw. freizugeben. Sie können Benutzer in einer der beiden Rollen hinzufügen:

* **Benutzer**: Ein Arbeitsbereichsbenutzer kann Experimente, Datasets etc. im Arbeitsbereich erstellen, öffnen, ändern und löschen.
* **Besitzer**: Zusätzlich zu den Möglichkeiten eines Benutzers kann ein Besitzer Benutzer im Arbeitsbereich einladen und entfernen.

> [!NOTE]
> Das Administratorkonto, das den Arbeitsbereich erstellt, wird dem Arbeitsbereich automatisch als Arbeitsbereichsbesitzer hinzugefügt. Allerdings erhalten andere Administratoren oder Benutzer in diesem Abonnement nicht automatisch Zugriff auf den Arbeitsbereich – Sie müssen sie explizit einladen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>So geben Sie einen (klassischen) Studio-Arbeitsbereich frei

1. Melden Sie sich bei Machine Learning Studio (klassisch) unter [https://studio.azureml.net/Home](https://studio.azureml.net/Home) an.

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

## <a name="troubleshoot-storage-accounts"></a>Beheben von Problemen mit Speicherkonten


Der Machine Learning-Dienst benötigt ein Speicherkonto zum Speichern von Daten. Sie können ein bestehendes Speicherkonto verwenden oder ein neues Speicherkonto erstellen, wenn Sie den neuen (klassischen) Machine Learning Studio-Arbeitsbereich einrichten (falls Ihr Kontingent ausreicht, um ein neues Speicherkonto zu erstellen).

Nachdem Sie den neuen (klassischen) Machine Learning Studio-Arbeitsbereich erstellt haben, können Sie sich mit dem Microsoft-Konto bei Machine Learning Studio (klassisch) anmelden, das Sie zum Erstellen des Arbeitsbereichs verwendet haben. Wenn die Fehlermeldung „Arbeitsbereich wurde nicht gefunden.“ (ähnlich wie im folgenden Screenshot) angezeigt wird, führen Sie die folgenden Schritte aus, um Ihre Browsercookies zu löschen:

![Arbeitsbereich nicht gefunden](media/troubleshooting-creating-ml-workspace/screen3.png)

**So löschen Sie Ihre Browsercookies**

1. Falls Sie Internet Explorer verwenden, klicken Sie oben rechts auf **Extras** und anschließend auf **Internetoptionen**.  

   ![Internetoptionen](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klicken Sie in der Registerkarte **Allgemein** auf **Löschen...**

   ![Registerkarte "Allgemein"](media/troubleshooting-creating-ml-workspace/screen5.png)

3. Wählen Sie im Dialogfeld **Browserverlauf löschen** den Eintrag **Cookies und Websitedaten** aus und klicken Sie auf **Löschen**.

   ![Cookies löschen](media/troubleshooting-creating-ml-workspace/screen6.png)

Starten Sie nach dem Löschen der Cookies den Browser neu, und wechseln Sie anschließend zur Seite [Microsoft Azure Machine Learning Studio (klassisch)](https://studio.azureml.net). Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, geben Sie das Microsoft-Konto ein, das Sie zum Erstellen des Arbeitsbereichs verwendet haben.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten eines Arbeitsbereichs finden Sie unter [Verwalten eines (klassischen) Azure Machine Learning Studio-Arbeitsbereichs](manage-workspace.md).