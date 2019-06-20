---
title: include file
description: include file
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 04/01/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: 0f3303e7bc87ca0bd29f367405372568ed6da7a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178246"
---
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

1. Wählen Sie **+ Ressource erstellen** auf der linken Seite aus, und wählen Sie dann **Funktions-App** aus.

1. Wählen Sie als **Hostingplan** den **App Service-Plan** aus und dann **App Service-Plan/Standort**.

    ![Erstellen einer Funktionen-App](./media/functions-premium-create/create-function-app-resource.png)

1. Wählen Sie **Neu erstellen** aus, geben Sie einen Namen für **App Service-Plan** ein, wählen Sie einen **Standort** in einer [Region](https://azure.microsoft.com/regions/) in Ihrer Nähe oder in der Nähe anderer Dienste aus, auf die Ihre Funktionen zugreifen, und wählen Sie dann **Tarif** aus.

    ![App Service-Plan erstellen](./media/functions-premium-create/new-app-service-plan.png)

1. Wählen Sie den Plan **EP1** (elastisches Premium) aus, und wählen Sie dann **Anwenden** aus.

    ![Premium-Plan auswählen](./media/functions-premium-create/hosting-plan.png) 

1. Wählen Sie **OK** aus, um den Plan zu erstellen, und verwenden Sie dann die in der Tabelle unter der Abbildung angegebenen Einstellungen für die Funktions-App. 

    ![Fertig gestellter App Service-Plan](./media/functions-premium-create/create-function-app.png)  

    | Einstellung      | Empfohlener Wert  | BESCHREIBUNG                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **App-Name** | Global eindeutiger Name | Der Name, der Ihre neue Funktionen-App bezeichnet Gültige Zeichen sind `a-z`, `0-9` und `-`.  | 
    | **Abonnement** | Ihr Abonnement | Das Abonnement, unter dem diese neue Funktions-App erstellt wird. |
    | **[Ressourcengruppe](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Der Name der neuen Ressourcengruppe, in der die Funktionen-App erstellt wird Sie können auch den vorgeschlagenen Wert verwenden. |
    | **Betriebssystem** | Windows | Linux wird im Premium-Plan zurzeit nicht unterstützt. |
    | **Laufzeitstapel** | Bevorzugte Sprache | Wählen Sie eine Runtime aus, die Ihre bevorzugte Programmiersprache für Funktionen unterstützt. Wählen Sie **.NET** für C#- und F#-Funktionen aus. Nur die Sprachen, die von Ihrem ausgewählten **Betriebssystem** unterstützt werden, werden angezeigt. |
    | **[Storage](../articles/storage/common/storage-quickstart-create-account.md)** |  Global eindeutiger Name |  Erstellen Sie ein Speicherkonto, das von Ihrer Funktions-App verwendet wird. Speicherkontonamen müssen zwischen 3 und 24 Zeichen lang sein und dürfen nur Zahlen und Kleinbuchstaben enthalten. Sie können auch ein vorhandenes Konto verwenden, das die [Anforderungen an das Speicherkonto](../articles/azure-functions/functions-scale.md#storage-account-requirements) erfüllen muss. |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standard | Erstellt eine Application Insights-Ressource mit dem gleichen *App-Namen* in der nächstgelegenen unterstützten Region. Durch Erweitern dieser Einstellung können Sie den **neuen Ressourcennamen** ändern oder einen anderen **Standort** in einer [Azure-Region](https://azure.microsoft.com/global-infrastructure/geographies/) wählen, in der Sie Ihre Daten speichern möchten. |

1. Wählen Sie nach der Überprüfung Ihrer Einstellungen **Erstellen** aus.

1. Wählen Sie oben rechts im Portal das Benachrichtigungssymbol aus, und achten Sie auf die Meldung **Bereitstellung erfolgreich**.

    ![Definieren neuer Funktions-App-Einstellungen](./media/functions-premium-create/function-app-create-notification.png)

1. Wählen Sie **Zu Ressource wechseln**, um Ihre neue Funktionen-App anzuzeigen. Sie können auch die Option **An Dashboard anheften** auswählen. Wenn Sie die Funktions-App anheften, können Sie einfacher über das Dashboard auf sie zugreifen.