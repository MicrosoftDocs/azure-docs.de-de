---
title: include file
description: include file
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: b6cafcfe6c892cd43f056458fe3586da834c2fd1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177854"
---
Mit Functions ist es einfach, die Application Insights-Integration über das [Azure-Portal] einer Funktionen-App hinzuzufügen.

1. Wählen Sie im [Portal][Azure-Portal] die Option **Alle Dienste > Funktionen-Apps** und dann Ihre Funktionen-App aus. Wählen Sie anschließend oben im Fenster das Banner **Application Insights** aus.

    ![Aktivieren von Application Insights über das Portal](media/functions-connect-new-app-insights/enable-application-insights.png)

1. Erstellen Sie eine Application Insights-Ressource, indem Sie die Einstellungen verwenden, die in der Tabelle unterhalb des Bilds angegeben sind:

   ![Erstellen einer Application Insights-Ressource](media/functions-connect-new-app-insights/ai-general.png)

    | Einstellung      | Empfohlener Wert  | Beschreibung                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name** | Eindeutiger App-Name | Es ist am einfachsten, den gleichen Namen wie für Ihre Funktionen-App zu verwenden, der in Ihrem Abonnement eindeutig sein muss. | 
    | **Location** | Europa, Westen | Verwenden Sie nach Möglichkeit dieselbe [Region](https://azure.microsoft.com/regions/) wie für Ihre Funktionen-App (oder eine Region in der Nähe). |

1. Klicken Sie auf **OK**. Die Application Insights-Ressource wird in derselben Ressourcengruppe und unter demselben Abonnement wie Ihre Funktionen-App erstellt. Schließen Sie das Application Insights-Fenster, nachdem die Erstellung abgeschlossen ist.

1. Wählen Sie in Ihrer Funktionen-App die Option **Anwendungseinstellungen**, und scrollen Sie nach unten zu **Anwendungseinstellungen**. Wenn die Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` angezeigt wird, bedeutet dies, dass die Application Insights-Integration für Ihre unter Azure ausgeführte Funktionen-App aktiviert ist.

[Azure-Portal]: https://portal.azure.com
