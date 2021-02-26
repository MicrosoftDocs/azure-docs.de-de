---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/28/2020
ms.author: glenga
ms.openlocfilehash: 2517f132578b5de6b062b38ce94581f118327a13
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493588"
---
## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

In diesem Abschnitt erstellen Sie eine Funktions-App sowie zugehörige Ressourcen in Ihrem Azure-Abonnement und stellen anschließend Ihren Code bereit.

> [!IMPORTANT]
> Beim Veröffentlichen in einer vorhandenen Funktions-App wird der Inhalt dieser App in Azure überschrieben.


1. Wählen Sie auf der Aktivitätsleiste das Azure-Symbol und anschließend im Bereich **Azure: Funktionen** die Schaltfläche **Deploy to function app...** (In Funktions-App bereitstellen...) aus.

    ![Veröffentlichen Ihres Projekts in Azure](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geben Sie nach entsprechender Aufforderung Folgendes ein:

    - **Wählen Sie einen Ordner aus:** Wählen Sie einen Ordner in Ihrem Arbeitsbereich aus, oder navigieren Sie zu einem Ordner, der Ihre Funktions-App enthält. Diese Option wird nicht angezeigt, wenn Sie bereits eine gültige Funktions-App geöffnet haben.

    - **Wählen Sie das Abonnement aus:** Wählen Sie das zu verwendende Abonnement aus. Wenn Sie nur über ein Abonnement verfügen, wird diese Option nicht angezeigt.

    - **Select Function App in Azure:** (Wählen Sie die Funktions-App in Azure aus:) Wählen Sie die Option `- Create new Function App`. (Wählen Sie nicht die Option `Advanced` aus, diese wird im vorliegenden Artikel nicht behandelt.)
      
    - **Enter a globally unique name for the function app:** (Geben Sie einen global eindeutigen Namen für die Funktions-App ein:) Geben Sie einen Namen ein, der in einem URL-Pfad gültig ist. Der eingegebene Name wird überprüft, um sicherzustellen, dass er in Azure Functions eindeutig ist.
    
    - **Wählen Sie einen Speicherort für neue Ressourcen aus:**  Wählen Sie eine [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe aus, um eine bessere Leistung zu erzielen. 
    
    Die Erweiterung zeigt den Status einzelner Ressourcen an, während diese in Azure im Benachrichtigungsbereich erstellt werden.

    :::image type="content" source="media/functions-publish-project-vscode/resource-notification.png" alt-text="Benachrichtigung über die Erstellung von Azure-Ressourcen":::
    
1.  Nach Abschluss des Vorgangs werden in Ihrem Abonnement die folgenden Azure-Ressourcen erstellt, deren Namen auf dem Namen Ihrer Funktions-App basieren:
    
    [!INCLUDE [functions-vs-code-created-resources](functions-vs-code-created-resources.md)]

    Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet. 

    [!INCLUDE [functions-vs-code-create-tip](functions-vs-code-create-tip.md)]

4. Wählen Sie in dieser Benachrichtigungen **Ausgabe anzeigen** aus, um die Erstellungs- und Bereitstellungsergebnisse (auch für die von Ihnen erstellten Azure-Ressourcen) anzuzeigen. Wenn Sie die Benachrichtigung übersehen haben, wählen Sie das Glockensymbol in der unteren rechten Ecke aus, um sie erneut anzuzeigen.

    ![Benachrichtigung nach Abschluss der Erstellung](media/functions-publish-project-vscode/function-create-notifications.png)
