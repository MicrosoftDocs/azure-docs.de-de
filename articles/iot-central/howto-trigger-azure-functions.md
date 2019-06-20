---
title: Auslösen von Azure Functions mithilfe von Webhooks in Azure IoT Central
description: Erstellen Sie eine Funktions-App, die immer dann ausgeführt wird, wenn eine Regel in Azure IoT Central ausgelöst wird.
author: viv-liu
ms.author: viviali
ms.date: 03/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0d92e9bdf8ec207e5ef0e3f891c162182b5a4fff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518345"
---
# <a name="trigger-azure-functions-using-webhooks-in-azure-iot-central"></a>Auslösen von Azure Functions mithilfe von Webhooks in Azure IoT Central

*Dieses Thema gilt für Generatoren und Administratoren.*

Verwenden Sie Azure Functions, um serverlosen Code bei der Webhookausgabe aus IoT Central-Regeln auszuführen. Sie müssen keinen virtuellen Computer bereitstellen oder eine Web-App veröffentlichen, um Azure Functions verwenden zu können. Stattdessen können Sie diesen Code serverlos ausführen. Verwenden Sie Azure Functions, um die Webhook-Nutzlast zu transformieren, bevor Sie sie an ihr endgültiges Ziel senden, z. B. eine SQL-Datenbank oder ein Event Grid.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="how-to-connect-azure-functions"></a>Gewusst wie: Verbinden von Azure Functions

1. [Erstellen einer neuen Funktions-App im Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.FunctionApp).

    ![Erstellen einer neuen Funktions-App im Azure-Portal](media/howto-trigger-azure-functions/createfunction.png)

2. Erweitern Sie die Funktions-App, und wählen Sie die Schaltfläche **+** neben „Funktionen“ aus. Wenn dies die erste Funktion in Ihrer Funktions-App ist, wählen Sie **im Portal** als Entwicklungsumgebung und dann **Weiter** aus.

    ![Auswählen einer benutzerdefinierten Funktion in der Funktions-App](media/howto-trigger-azure-functions/customfunction.png)

3. Wählen Sie die Vorlage **Webhook + API** und anschließend **Erstellen** aus. In diesem Thema wird eine .NET-basierte Azure-Funktion verwendet.

    ![Auswählen eines generischen Webhook-Triggers](media/howto-trigger-azure-functions/genericwebhooktrigger.png)

4. Wählen Sie in der neuen Funktion **</> Funktions-URL abrufen** aus, kopieren Sie den Wert, und speichern Sie ihn. Verwenden Sie diesen Wert zum Konfigurieren des Webhooks.

    ![Abrufen der URL für die Funktion](media/howto-trigger-azure-functions/getfunctionurl.png)

4. Navigieren Sie in IoT Central zu der Regel, die Sie mit Ihrer Funktions-App verbinden möchten.

5. Fügen Sie eine Webhookaktion hinzu. Geben Sie einen **Anzeigenamen** ein, und fügen Sie die zuvor kopierte Funktions-URL in **Rückruf-URL** ein.

    ![Die Funktions-URL im Feld „Rückruf-URL“ eingeben](media/howto-trigger-azure-functions/configurewebhook.PNG)

6. Speichern Sie die Regel. Beim Auslösen der Regel ruft der Webhook jetzt die Funktions-App auf, und sie wird ausgeführt. In Ihrer Funktions-App können Sie **Überwachen** auswählen, um den Aufrufverlauf der Funktion anzuzeigen. Zum Anzeigen des Verlaufs können Sie App Insights oder die klassische Ansicht verwenden.

    ![Überwachen des Aufrufverlaufs der Funktion](media/howto-trigger-azure-functions/monitorfunction.PNG)

Weitere Informationen finden Sie im Azure Functions-Artikel zum [Erstellen einer Funktion, die durch einen generischen Webhook ausgelöst wird](https://docs.microsoft.com/azure/azure-functions/functions-create-generic-webhook-triggered-function).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun gelernt haben, wie man Webhooks einrichtet und verwendet, erkunden Sie idealerweise als nächsten Schritt die [Erstellung von Workflows in Microsoft Flow](howto-add-microsoft-flow.md).
