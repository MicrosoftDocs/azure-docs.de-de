---
title: Erstellen von Workflows mit dem Azure IoT Central-Connector in Microsoft Flow | Microsoft-Dokumentation
description: Verwenden Sie den IoT Central-Connector in Microsoft Flow zum Auslösen von Workflows und Erstellen, Abrufen, Aktualisieren und Löschen von Geräten sowie Ausführen von Befehlen in Workflows.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: c50271e28db104522c0e8811508fa882dc316eab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235807"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Erstellen von Workflows mit dem IoT Central-Connector in Microsoft Flow

*Dieses Thema gilt für Generatoren und Administratoren.*

Verwenden Sie Microsoft Flow, um Workflows viele für Geschäftskunden unverzichtbare Anwendungen und Dienste übergreifend zu automatisieren. Mit dem IoT Central-Connector in Microsoft Flow können Sie Workflows auslösen, wenn eine Regel in IoT Central ausgelöst wird. In einem von IoT Central ausgelösten Workflow oder einer anderen Anwendung können Sie die Aktionen im IoT Central-Connector für Folgendes einsetzen:
- Erstellen eines Geräts
- Bearbeiten von Geräteinformationen
- Aktualisieren von Eigenschaften und Einstellungen eines Geräts
- Ausführen eines Befehls auf einem Gerät
- Gerät löschen

Machen Sie sich mit [diesen Microsoft Flow-Vorlagen](https://aka.ms/iotcentralflowtemplates) vertraut, die eine Verbindung von IoT Central mit anderen Diensten wie mobilen Benachrichtigungen und Microsoft Teams herstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Anwendung mit nutzungsbasierter Bezahlung
- Ein persönliches oder Geschäfts-, Schul- oder Unikonto für die Anmeldung bei Microsoft Flow ([weitere Informationen zu Microsoft Flow-Tarifen](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Auslösen eines Workflows

In diesem Abschnitt erfahren Sie, wie Sie eine mobile Benachrichtigung in der mobilen Flow-App auslösen, wenn eine Regel in IoT Central ausgelöst wird. Mit dem integrierten Microsoft Flow Designer können Sie diesen gesamten Workflow in Ihrer IoT Central-App erstellen.

1. Beginnen Sie mit dem [Erstellen einer Regel in IoT Central](howto-create-telemetry-rules.md). Wählen Sie **Microsoft Flow-Aktion** als neue Aktion aus, nachdem Sie die Regelbedingungen gespeichert haben. Es wird ein Dialogfeld geöffnet, in dem Sie Ihren Workflow konfigurieren können. Das IoT Central-Benutzerkonto, bei dem Sie angemeldet sind, wird zur Anmeldung bei Microsoft Flow verwendet.

    ![Erstellen einer neuen Microsoft Flow-Aktion](media/howto-add-microsoft-flow/createflowaction.png)

1. Es wird eine Liste von Workflows angezeigt, auf die Sie Zugriff haben und die dieser IoT Central-Regel angefügt sind. Klicken Sie auf **Vorlagen erkunden** oder **Neu > Aus Vorlage erstellen**, um aus einer der verfügbaren Vorlagen zu wählen. 

    ![Verfügbare Microsoft Flow-Vorlagen](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Sie werden aufgefordert, sich bei den Connectors in der von Ihnen gewählten Vorlage anzumelden. Sobald die Connectors angemeldet sind, gelangen Sie zum Designer, mit dem Sie Ihren Workflow erstellen können. Der Workflow verfügt über einen IoT Central-Auslöser, in den Ihre Anwendung und Regel bereits eingetragen sind.

1. Sie können den Workflow anpassen, indem Sie die an die Aktion übergebenen Informationen bearbeiten und neue Aktionen hinzufügen. In diesem Beispiel ist die Aktion **Notifications - Send me a mobile notification** (Benachrichtigungen – Eine Benachrichtigung auf mein Mobilgerät senden). Sie können *Dynamischen Inhalt* aus Ihrer IoT Central-Regel einschließen, um Ihrer Benachrichtigung wichtige Informationen wie den Namen des Geräts und den Zeitstempel zu übergeben.

    > [!NOTE]
    > Wählen Sie im Fenster für dynamischen Inhalt den Text **Weitere Informationen** aus, um Mess- und Eigenschaftswerte abzurufen, welche die Regel ausgelöst haben.

    ![Flow-Bearbeitungsaktion mit geöffnetem dynamischem Bereich](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Wenn Sie die Bearbeitung Ihrer Aktion abgeschlossen haben, wählen Sie **Speichern** aus. Sie werden zur Übersichtsseite des Workflows weitergeleitet. Hier können Sie den Ausführungsverlauf anzeigen und für Kollegen freigeben.

    > [!NOTE]
    > Wenn Sie möchten, dass andere Benutzer Ihrer IoT Central-App diese Regel bearbeiten, müssen Sie sie für sie in Microsoft Flow freigeben. Fügen Sie ihre Microsoft Flow-Konten als Besitzer Ihrem Workflow hinzu.

1. Wenn Sie zu Ihrer IoT Central-App zurückkehren, sehen Sie, dass dieser Regel im Bereich „Aktionen“ eine Microsoft Flow-Aktion zugeordnet ist.

Mit dem IoT Central-Connector können Sie Workflows auch direkt über Microsoft Flow erstellen. Anschließend können Sie auswählen, mit welcher IoT Central-App Sie eine Verbindung herstellen möchten.

## <a name="create-a-device-in-a-workflow"></a>Erstellen eines Geräts in einem Workflow

In diesem Abschnitt erfahren Sie, wie Sie auf einem mobilen Gerät mithilfe der mobilen Microsoft Flow-App auf Schaltflächendruck ein neues Gerät in IoT Central erstellen. Sie können mit dieser Aktion in Flow ERP-Systeme wie Dynamics in IoT Central integrieren, indem Sie ein neues Gerät erstellen, wenn an anderer Stelle ein Gerät hinzugefügt wird.

1. Erstellen Sie zunächst einen leeren Workflow in Microsoft Flow.

1. Suchen Sie nach **Flow-Schaltfläche für mobile Verwendung** als Auslöser.

1. Fügen Sie diesem Trigger die Texteingabe **Gerätename** hinzu. Ändern Sie den Beschreibungstext in **Geben Sie den Gerätenamen Ihres neuen Geräts ein**.

1. Fügen Sie eine neue Aktion hinzu. Suchen Sie nach der Aktion **Azure IoT Central – Erstellen eines Geräts**.

1. Wählen Sie in den Dropdownmenüs Ihre Anwendung und anschließend eine Gerätevorlage aus, um ein Gerät zu erstellen. Sie sehen, dass die Aktion erweitert wird, um alle Eigenschaften und Einstellungen des Geräts anzuzeigen.

1. Wählen Sie das Feld „Gerätename“ aus. Wählen Sie im dynamischen Inhaltsbereich **Gerätename** aus. Dieser Wert wird aus der Eingabe des Benutzers über die mobile App übergeben und entspricht dem Namen Ihres neuen Geräts in IoT Central. In diesem Beispiel ist das einzige erforderliche Feld der Gerätenamen, gekennzeichnet mit einem roten Sternchen. Eine andere Gerätevorlage weist möglicherweise mehrere erforderliche Felder auf, die zum Erstellen eines neuen Geräts ausgefüllt werden müssen.

    ![Dynamischer Bereich der Flow-Aktion zum Erstellen eines Geräts](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Optional) Füllen Sie andere Felder aus, die Sie zum Erstellen neuer Geräte für geeignet halten.

1. Speichern Sie abschließend Ihren Workflow.

1. Probieren Sie den Workflow in der mobilen Microsoft Flow-App aus. Wechseln Sie in der App zur Registerkarte **Schaltflächen**. Ihr Workflow „Schaltfläche -> Neues Gerät erstellen“ sollte angezeigt werden. Geben Sie den Namen des neuen Geräts ein, und beobachten Sie, wie es in IoT Central angezeigt wird!

    ![Screenshot des Erstellens eines mobilen Geräts in Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aktualisieren eines Geräts in einem Workflow

In diesem Abschnitt erfahren Sie, wie Sie auf einem mobilen Gerät mithilfe der mobilen Microsoft Flow-App auf Schaltflächendruck in IoT Central Geräteeinstellungen und -eigenschaften aktualisieren.

1. Erstellen Sie zunächst einen leeren Workflow in Microsoft Flow.

1. Suchen Sie nach **Flow-Schaltfläche für mobile Verwendung** als Auslöser.

1. Fügen Sie diesem Auslöser eine Eingabe wie die Texteingabe **Speicherort** hinzu, die einer Einstellung oder Eigenschaft entspricht, die Sie ändern möchten. Ändern Sie den Beschreibungstext.

1. Fügen Sie eine neue Aktion hinzu. Suchen Sie nach der Aktion **Azure IoT Central – Aktualisieren eines Geräts**.

1. Wählen Sie aus der Dropdownliste Ihre Anwendung aus. Jetzt benötigen Sie eine ID des vorhandenen Geräts, das Sie aktualisieren möchten. 

    > [!NOTE] 
    > **Sie müssen die ID aus der URL verwenden**, die auf der Seite „Gerätedetails“ des zu aktualisierenden Geräts zu finden ist. Die Geräte-ID in der Geräteliste von Device Explorer ist für die Verwendung in Microsoft Flow nicht geeignet.

    ![IoT Central-ID aus der URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. Sie können den Gerätenamen aktualisieren. Um beliebige Geräteeigenschaften und -einstellungen zu aktualisieren, müssen Sie in der Dropdownliste **Gerätevorlage** die Gerätevorlage des Geräts auswählen, das Sie aktualisieren möchten. Die Aktionskachel wird erweitert, um alle Eigenschaften und Einstellungen anzuzeigen, die Sie aktualisieren können.

    ![Flow-Workflow zum Aktualisieren eines Geräts](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Wählen Sie die Eigenschaften und Einstellungen aus, die Sie aktualisieren möchten. Wählen Sie im dynamischen Inhaltsbereich die entsprechende Eingabe vom Auslöser aus. In diesem Beispiel wird der Wert „Speicherort“ nach unten weitergegeben, um die Eigenschaft „Speicherort“ des Geräts zu aktualisieren.

1. Speichern Sie abschließend Ihren Workflow.

1. Probieren Sie den Workflow in der mobilen Microsoft Flow-App aus. Wechseln Sie in der App zur Registerkarte **Schaltflächen**. Ihr Workflow „Schaltfläche -> Aktualisieren eines Geräts“ sollte angezeigt werden. Nehmen Sie die Eingaben vor, und beobachten Sie, wie das Gerät in IoT Central aktualisiert wird!

## <a name="get-device-information-in-a-workflow"></a>Abrufen von Geräteinformationen in einem Workflow

Sie können Geräteinformationen mit der Aktion **Azure IoT Central – Get a device** (Azure IoT Central – Abrufen eines Geräts) anhand der Geräte-ID abrufen. 
> [!NOTE] 
> **Sie müssen die ID aus der URL verwenden**, die auf der Seite „Gerätedetails“ des zu aktualisierenden Geräts zu finden ist. Die Geräte-ID in der Geräteliste von Device Explorer ist für die Verwendung in Microsoft Flow nicht geeignet.

Dabei erhalten Sie Informationen wie Gerätename, Gerätevorlagenname, Eigenschaftswerte und Einstellungswerte, um diese an spätere Aktionen im Workflow zu übergeben. Hier sehen Sie einen Beispielsworkflow, bei dem der Eigenschaftswert „Kundenname“ von einem Gerät an Microsoft Teams übergeben wird.

   ![Flow-Workflow zum Abrufen eines Geräts](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Ausführen eines Befehls auf einem Gerät in einem Workflow
Sie können einen Befehl auf einem Gerät, das anhand seiner ID angegeben wurde, mit der Aktion **Azure IoT Central – Run a command** (Azure IoT Central – Ausführen eines Befehls) ausführen. 

> [!NOTE] 
> **Sie müssen die ID aus der URL verwenden**, die auf der Seite „Gerätedetails“ des zu aktualisierenden Geräts zu finden ist. Die Geräte-ID in der Geräteliste von Device Explorer ist für die Verwendung in Microsoft Flow nicht geeignet.
    
Wählen Sie den auszuführenden Befehl aus, und übergeben Sie mit dieser Aktion die Parameter des Befehls. Hier ist ein Beispielworkflow, der ein Gerät über eine Schaltfläche für einen Neustartbefehl in der mobilen Microsoft Flow-App ausführt.

   ![Flow-Workflow zum Abrufen eines Geräts](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Löschen eines Geräts in einem Workflow

Sie können ein Gerät mit der Aktion **Azure IoT Central – Delete a device** (Azure IoT Central – Löschen eines Geräts) anhand seiner ID löschen. 
> [!NOTE] 
> **Sie müssen die ID aus der URL verwenden**, die auf der Seite „Gerätedetails“ des zu aktualisierenden Geräts zu finden ist. Die Geräte-ID in der Geräteliste von Device Explorer ist für die Verwendung in Microsoft Flow nicht geeignet.

Hier ist ein Beispielworkflow, der ein Gerät auf Schaltflächendruck in der mobilen Microsoft Flow-App löscht.

   ![Flow-Workflow zum Löschen eines Geräts](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>Problembehandlung

Die folgenden Tipps sollen Ihnen helfen, wenn beim Erstellen einer Verbindung mit dem Azure IoT Central-Connector Probleme auftreten.

1. Persönliche Microsoft-Konten (z.B. die Domänen @hotmail.com, @live.com, @outlook.com) werden derzeit nicht unterstützt. Sie müssen ein Azure Active Directory (AD)-Geschäfts-, Schul- oder Unikonto verwenden.

2. Sie müssen sich mindestens einmal an der IoT Central-Anwendung angemeldet haben, um den IoT Central-Connector in Microsoft Flow verwenden zu können. Andernfalls wird die Anwendung in der Dropdownliste der Anwendungen nicht angezeigt.

3. Wenn Sie bei der Verwendung eines Azure AD-Kontos eine Fehlermeldung erhalten, versuchen Sie, Windows PowerShell zu öffnen, und führen Sie die folgenden Cmdlets als Administrator aus.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun gelernt haben, Microsoft Flow zum Erstellen von Workflows zu verwenden, sollten Sie im nächsten Schritt [Geräte verwalten](howto-manage-devices.md).

