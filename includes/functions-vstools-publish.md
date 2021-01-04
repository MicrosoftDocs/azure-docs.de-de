---
title: include file
description: include file
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ef617b9ed6c35d33350b173efe6189b5b79b6ae6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008180"
---
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie unter **Ziel** die Option **Azure** :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-1.png" alt-text="Auswählen von „Azure“ als Ziel"::: aus.

1. Wählen Sie unter **Spezifisches Ziel** die Option **Azure-Funktions-App (Windows)** aus.

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-2.png" alt-text="Auswählen von „Azure-Funktions-App“":::

1. Wählen Sie unter **Funktionsinstanz** die Option **Neue Azure-Funktion erstellen...** aus, und verwenden Sie dann die in der folgenden Tabelle angegebenen Werte:

    | Einstellung      | Wert  | Beschreibung                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Global eindeutiger Name | Name, der Ihre neue Funktions-App eindeutig identifiziert. Übernehmen Sie diesen Namen, oder geben Sie einen neuen Namen ein. Gültige Zeichen: `a-z`, `0-9` und `-`. |
    | **Abonnement** | Ihr Abonnement | Das zu verwendende Azure-Abonnement. Übernehmen Sie dieses Abonnement, oder wählen Sie in der Dropdownliste ein neues Abonnement aus. |
    | **[Ressourcengruppe](../articles/azure-resource-manager/management/overview.md)** | Name Ihrer Ressourcengruppe |  Die Ressourcengruppe, in der Ihre Funktions-App erstellt werden soll. Wählen Sie in der Dropdownliste eine vorhandene Ressourcengruppe aus, oder wählen Sie **Neu** aus, um eine neue Ressourcengruppe zu erstellen.|
    | **[Plantyp](../articles/azure-functions/functions-scale.md)** | Nutzung | Wenn Sie Ihr Projekt in einer Funktions-App veröffentlichen, die in einem [Verbrauchsplan](../articles/azure-functions/functions-scale.md#consumption-plan) ausgeführt wird, bezahlen Sie nur für die Ausführungen Ihrer Funktions-App. Für andere Hostingpläne fallen höhere Kosten an. |
    | **Location** | Standort des App-Diensts | Wählen Sie einen **Standort** in einer [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe anderer Dienste aus, auf die Ihre Funktionen zugreifen. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Universelles Speicherkonto | Für die Functions-Runtime wird ein Azure Storage-Konto benötigt. Wählen Sie **Neu** aus, um ein universelles Speicherkonto zu konfigurieren. Sie können auch ein vorhandenes Konto auswählen, das die [Anforderungen an das Speicherkonto](../articles/azure-functions/functions-scale.md#storage-account-requirements) erfüllt.  |

    ![Dialogfeld „App Service erstellen“](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Wählen Sie **Erstellen** aus, um eine Funktions-App und die zugehörigen Ressourcen in Azure zu erstellen. 
1. Vergewissern Sie sich unter **Funktionsinstanz**, dass die Option **Aus Paketdatei ausführen** aktiviert ist. Ihre Funktions-App wird unter Verwendung der [ZIP-Bereitstellung](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) mit aktiviertem Modus [Run-From-Package](../articles/azure-functions/run-functions-from-deployment-package.md) bereitgestellt. Dies ist die empfohlene Bereitstellungsmethode für Ihr Funktionsprojekt, da damit eine bessere Leistung erzielt wird. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Abschließen der Profilerstellung":::

1. Wählen Sie **Fertig stellen** und anschließend auf der Seite „Veröffentlichen“ die Option **Veröffentlichen** aus, um das Paket mit Ihren Projektdateien in der neuen Funktions-App in Azure bereitzustellen. 

    Nach Abschluss der Bereitstellung wird die Stamm-URL der Funktions-App in Azure auf der Registerkarte **Veröffentlichen** angezeigt. 
    
1.  Wählen Sie auf der Registerkarte „Veröffentlichen“ die Option **Im Cloud-Explorer verwalten** aus. Dadurch wird die Azure-Ressource für die neue Funktions-App im Cloud-Explorer geöffnet. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Veröffentlichen einer Erfolgsmeldung":::
    
    Im Cloud-Explorer können Sie Visual Studio verwenden, um die Inhalte der Website anzuzeigen, die Funktions-App zu starten und anzuhalten und direkt zu Funktions-App-Ressourcen in Azure und im Azure-Portal zu navigieren. 
