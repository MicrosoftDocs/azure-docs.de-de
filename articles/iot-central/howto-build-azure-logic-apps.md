---
title: Erstellen von Workflows mit dem IoT Central-Connector in Azure Logic Apps | Microsoft-Dokumentation
description: Verwenden Sie den IoT Central-Connector in Azure Logic Apps zum Auslösen von Workflows sowie zum Erstellen, Aktualisieren und Löschen von Geräten in Workflows.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 2269ede83ad64172e316a208871ab65053b2d10e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053072"
---
# <a name="build-workflows-with-the-iot-central-connector-in-azure-logic-apps"></a>Erstellen von Workflows mit dem IoT Central-Connector in Azure Logic Apps

*Dieses Thema gilt für Generatoren und Administratoren.*

Verwenden Sie Azure Logic Apps, um automatisierte skalierbare Workflows zu erstellen, die Apps und Daten übergreifend in Clouddienste und lokale Systeme integrieren. Azure Logic Apps bietet viele Connectors für zahlreiche Azure-Dienste, Microsoft-Dienste und andere Software-as-a-Service-Produkte (SaaS). Mit dem IoT Central-Connector in Azure Logic Apps können Sie Workflows auslösen, wenn eine Regel in IoT Central ausgelöst wird. Sie können auch die Aktionen im IoT Central-Connector zum Erstellen eines Geräts, Aktualisieren der Eigenschaften und Einstellungen eines Geräts oder Löschen eines Geräts nutzen.

Der IoT Central-Connector kann in Microsoft Flow verwendet werden. Sowohl Azure Logic Apps als auch Microsoft Flow sind „Designer First“-Integrationsdienste, ihre Zielgruppen unterscheiden sich jedoch geringfügig. Flow ermöglicht allen Mitarbeitern im Büro das Erstellen geschäftlicher Workflows, die sie für ihre Arbeit benötigen. Mit Logic Apps können IT-Experten die Integrationen erstellen, die Sie zum Verknüpfen von Daten benötigen. Einen Vergleich von Flow und Logic Apps finden Sie [hier](https://docs.microsoft.com/azure/azure-functions/functions-compare-logic-apps-ms-flow-webjobs). Informationen zum Erstellen von Workflows mit dem IoT Central-Connector in Microsoft Flow finden Sie [hier](howto-add-microsoft-flow.md).

## <a name="prerequisites"></a>Voraussetzungen

- Eine Anwendung mit nutzungsbasierter Bezahlung
- Ein Azure-Konto und -Abonnement zum Erstellen und Verwalten von Logik-Apps

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Auslösen eines Workflows bei Auslösen einer Regel

In diesem Abschnitt erfahren Sie, wie Sie eine Nachricht an Microsoft Teams senden, wenn eine Regel ausgelöst wird. Sie können Ihren Workflow mit weiteren Connectors konfigurieren, um beispielsweise ein Ereignis an Ihren Event Hub zu senden, ein neues Azure DevOps-Arbeitselement zu erstellen oder eine neue Zeile in SQL Server einzufügen.

1. Beginnen Sie mit dem [Erstellen einer Regel in IoT Central](howto-create-telemetry-rules.md). Nachdem Sie die Regelbedingungen gespeichert haben, wählen Sie die Kachel **Azure Logic Apps** als neue Aktion aus. Wählen Sie **Im Azure-Portal erstellen** aus. Sie gelangen zum Azure-Portal, wo Sie eine neue Logik-App erstellen können. Möglicherweise müssen Sie sich bei Ihrem Azure-Konto anmelden.

1. Geben Sie die erforderlichen Informationen zum Erstellen einer neuen Logik-App ein. Sie können ein Azure-Abonnement auswählen, in dem die neue Logik-App bereitgestellt werden soll. Dabei muss es sich nicht um das Abonnement handeln, in dem Ihre IoT Central-App erstellt wurde. Klicken Sie auf **Erstellen**.

    ![Erstellen einer Logik-App im Azure-Portal](./media/howto-build-azure-logic-apps/createinazureportal.png)

1. Nachdem Ihre Logik-App erfolgreich erstellt wurde, werden Sie automatisch zum Designer für Logik-Apps weitergeleitet. Wählen Sie **Leere Logik-App** aus. 

    ![Erstellen einer leeren Logik-App](./media/howto-build-azure-logic-apps/blanklogicapp.png)

1. Suchen Sie im Designer nach „IoT Central“, und wählen Sie den Trigger **When a rule is fired** (Beim Auslösen einer Regel) aus. Melden Sie sich mit dem Konto, das Sie zur Anmeldung bei Ihrer IoT Central-App verwenden, beim Connector an.

    ![Anmelden beim IoT Central-Connector](./media/howto-build-azure-logic-apps/addtrigger.png)

1. Nach der erfolgreichen Anmeldung sollten Felder angezeigt werden. Wählen Sie in den Dropdownlisten die **Anwendung** und die **Regel** aus.

    ![Auswählen der Anwendung und Regel](./media/howto-build-azure-logic-apps/pickappandrule.png)

1. Fügen Sie eine neue Aktion hinzu. Suchen Sie nach **versenden Nachricht Teams**, und wählen Sie im Microsoft Teams-Connector die Option **Nachricht versenden** aus. Melden Sie sich mit dem Konto, das Sie in Microsoft Teams verwenden, beim Connector an.

1. Wählen Sie in der Aktion das **Team** und den **Kanal** aus. Geben Sie den gewünschten Wortlaut jeder Nachricht in das Feld **Nachricht** ein. Sie können *Dynamischen Inhalt* aus Ihrer IoT Central-Regel einschließen, um Ihrer Benachrichtigung wichtige Informationen wie den Namen des Geräts und den Zeitstempel zu übergeben.
    > [!NOTE]
    > Wählen Sie im Fenster für dynamischen Inhalt den Text **Weitere Informationen** aus, um Mess- und Eigenschaftswerte abzurufen, welche die Regel ausgelöst haben.

    ![Logik-App-Bearbeitungsaktion mit geöffnetem Bereich „Dynamischer Inhalt“](./media/howto-build-azure-logic-apps/buildworkflow.png)

1. Wenn Sie die Bearbeitung Ihrer Aktion abgeschlossen haben, wählen Sie **Speichern** aus.

1. Wenn Sie zu Ihrer IoT Central-App zurückkehren, sehen Sie, dass dieser Regel im Bereich „Aktionen“ eine Azure Logic Apps-Aktion zugeordnet ist.

Sie können die Erstellung eines Workflows immer mit dem IoT Central-Connector in Logic Apps im Azure-Portal beginnen. Anschließend können Sie auswählen, mit welcher IoT Central-App und welcher Regel Sie eine Verbindung herstellen möchten. Die Funktionsweise ändert sich dadurch nicht. Es ist nicht erforderlich, jedes Mal auf der Seite für IoT Central-Regeln zu beginnen.

## <a name="create-update-and-delete-a-device-in-a-workflow"></a>Erstellen, Aktualisieren und Löschen eines Geräts in einem Workflow

Wenn Sie einen Workflow in Ihrer Logik-App erstellen, können Sie eine Aktion mit dem IoT Central-Connector hinzufügen. Die verfügbaren Aktionen sind **Gerät erstellen**, **Gerät aktualisieren** und **Gerät löschen**.

> [!NOTE]
> Für **Update a device** (Gerät aktualisieren) und **Delete a device** (Gerät löschen) benötigen Sie die ID des vorhandenen Geräts, das Sie aktualisieren bzw. löschen möchten. Sie können die ID des IoT Central-Geräts im **Device Explorer** abrufen.

![Geräte-ID im Device Explorer von IoT Central](./media/howto-build-azure-logic-apps/iotcdeviceid.png)

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun gelernt haben, Microsoft Flow zum Erstellen von Workflows zu verwenden, sollten Sie im nächsten Schritt [Geräte verwalten](howto-manage-devices.md).
