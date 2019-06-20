---
title: Aufrufen einer Funktion aus PowerApps | Microsoft-Dokumentation
description: Erstellen Sie einen benutzerdefinierten Connector, und rufen Sie anschließend mithilfe dieses Connectors eine Funktion auf.
services: functions
keywords: Cloud-Apps, Clouddienste, PowerApps, Geschäftsprozesse, Geschäftsanwendung
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: 26f6502f63b39d3f1ecf8dfeb09c8df4daa63b68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65786107"
---
# <a name="call-a-function-from-powerapps"></a>Aufrufen einer Funktion aus PowerApps
Die Plattform [PowerApps](https://powerapps.microsoft.com) ermöglicht Fachleuten in Unternehmen die Erstellung von Apps ohne herkömmlichen Anwendungscode. Professionelle Entwickler können die Funktionen von PowerApps mithilfe von Azure Functions erweitern und gleichzeitig sicherstellen, dass sich PowerApps-App-Ersteller nicht mit technischen Details beschäftigen müssen.

In diesem Thema erstellen Sie eine App auf der Grundlage eines Wartungsszenarios für Windturbinen. Sie erfahren, wie Sie die Funktion aufrufen, die Sie in [Erstellen einer OpenAPI-Definition für eine Funktion](functions-openapi-definition.md) definiert haben. Die Funktion bestimmt, ob die Notfallreparatur einer Windturbine kosteneffizient ist.

![Fertige App in PowerApps](media/functions-powerapps-scenario/finished-app.png)

Informationen zum Aufrufen derselben Funktion aus Microsoft Flow finden Sie unter [Call a function from Microsoft Flow](functions-flow-scenario.md) (Aufrufen einer Funktion aus Microsoft Flow).

In diesem Thema lernen Sie Folgendes:

> [!div class="checklist"]
> * Vorbereiten von Beispieldaten in Excel
> * Exportieren einer API-Definition
> * Hinzufügen einer Verbindung mit der API
> * Erstellen einer App und Hinzufügen von Datenquellen
> * Hinzufügen von Steuerelementen zum Anzeigen von Daten in der App
> * Hinzufügen von Steuerelementen zum Aufrufen der Funktion und zum Anzeigen von Daten
> * Ausführen der App, um zu bestimmen, ob eine Reparatur kosteneffizient ist

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

+ Ein aktives [PowerApps-Konto](https://docs.microsoft.com/powerapps/maker/signup-for-powerapps) mit den gleichen Anmeldeinformationen wie Ihr Azure-Konto 
+ Excel und die [Excel-Beispieldatei](https://procsi.blob.core.windows.net/docs/turbine-data.xlsx) (als Datenquelle für Ihre App)
+ Absolvierung des Tutorials [Erstellen einer OpenAPI-Definition für eine Funktion](functions-openapi-definition.md)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Hinzufügen einer Verbindung mit der API
Die benutzerdefinierte API (auch benutzerdefinierter Connector genannt) ist zwar in PowerApps verfügbar, Sie müssen jedoch eine Verbindung mit der API herstellen, um sie in einer App verwenden zu können.

1. Klicken Sie in [web.powerapps.com](https://web.powerapps.com) auf **Verbindungen**.

    ![PowerApps-Verbindungen](media/functions-powerapps-scenario/powerapps-connections.png)

1. Klicken Sie auf **Neue Verbindung**, scrollen Sie nach unten zum Connector **Turbine Repair**, und klicken Sie darauf.

    ![Neue Verbindung](media/functions-powerapps-scenario/new-connection.png)

1. Geben Sie den API-Schlüssel ein, und klicken Sie auf **Erstellen**.

    ![Erstellen der Verbindung](media/functions-powerapps-scenario/create-connection.png)

> [!NOTE]
> Bei einer gemeinsam genutzten App muss jeder Benutzer den API-Schlüssel eingeben, um eine Verbindung mit der API herzustellen. Dieses Verhalten wird unter Umständen noch geändert und das Thema ggf. entsprechend angepasst.

## <a name="create-an-app-and-add-data-sources"></a>Erstellen einer App und Hinzufügen von Datenquellen
Nun können Sie die App in PowerApps erstellen und die Excel-Daten sowie die benutzerdefinierte API als Datenquellen für die App hinzufügen.

1. Klicken Sie auf [web.powerapps.com](https://web.powerapps.com) auf **Mit leerer App starten** > ![Symbol für Telefon-App ](media/functions-powerapps-scenario/icon-phone-app.png) (Telefon) > **Diese App erstellen**.

    ![Mit leerer App starten: Telefon-App](media/functions-powerapps-scenario/create-phone-app.png)

    Die App wird in PowerApps Studio für Web geöffnet. Die folgende Abbildung zeigt die verschiedenen Bereiche von PowerApps Studio.

    ![PowerApps Studio](media/functions-powerapps-scenario/powerapps-studio.png)

    **(A) Linke Navigationsleiste:** Enthält eine hierarchische Ansicht aller Steuerelemente der einzelnen Bildschirme.

    **(B) Mittlerer Bereich:** Zeigt den Bildschirm, an dem Sie gerade arbeiten.

    **(C) Rechter Bereich:** Dient zum Festlegen von Optionen wie Layout und Datenquellen.

    **(D) Dropdownliste für Eigenschaften:** Dient zum Auswählen der Eigenschaften, für die die Formeln gelten.

    **(E) Bearbeitungsleiste:** Dient zum Hinzufügen von Formeln (wie in Excel), die das Verhalten der App definieren.
    
    **(F) Menüband:** Ermöglicht das Hinzufügen von Steuerelementen und das Anpassen von Designelementen.

1. Fügen Sie die Excel-Datei als Datenquelle hinzu.

    Die zu importierenden Daten sehen wie folgt aus:

    ![Zu importierende Excel-Daten](media/functions-powerapps-scenario/excel-table.png)

    1. Klicken Sie auf der App-Canvas auf **Mit Daten verbinden**.

    1. Klicken Sie im Bereich **Daten** auf **Der App statische Daten hinzufügen**.

        ![Hinzufügen einer Datenquelle](media/functions-powerapps-scenario/add-static-data.png)

        Normalerweise werden Daten aus einer externen Quelle gelesen und in selbige geschrieben, in diesem Beispiel fügen Sie jedoch Excel-Daten als statische Daten hinzu.

    1. Navigieren Sie zu der Excel-Datei, die Sie gespeichert haben, wählen Sie die Tabelle **Turbines** aus, und klicken Sie auf **Verbinden**.

        ![Hinzufügen einer Datenquelle](media/functions-powerapps-scenario/choose-table.png)


1. Fügen Sie die benutzerdefinierte API als Datenquelle hinzu.

    1. Klicken Sie im Bereich **Daten** auf **Datenquelle hinzufügen**.

    1. Klicken Sie auf **Turbine Repair**.

        ![Connector „Turbine Repair“](media/functions-powerapps-scenario/turbine-connector.png)

## <a name="add-controls-to-view-data-in-the-app"></a>Hinzufügen von Steuerelementen zum Anzeigen von Daten in der App
Nachdem die Datenquellen nun in der App verfügbar sind, fügen Sie ihr einen Bildschirm hinzu, auf dem Sie die Turbinendaten anzeigen können.

1. Klicken Sie auf der Registerkarte **Start** auf **Neuer Bildschirm** > **Listenbildschirm**.

    ![Listenbildschirm](media/functions-powerapps-scenario/list-screen.png)

    PowerApps fügt einen Bildschirm mit einem *Katalog* zum Anzeigen von Elementen sowie mit weiteren Steuerelementen zum Suchen, Sortieren und Filtern hinzu.

1. Ändern Sie die Titelleiste in `Turbine Repair`, und passen Sie die Größe des Katalogs so an, dass darunter noch Platz für weitere Steuerelemente verfügbar ist.

    ![Ändern des Titels und Anpassen der Kataloggröße](media/functions-powerapps-scenario/gallery-title.png)

1. Vergewissern Sie sich, dass der Katalog ausgewählt ist, und klicken Sie im rechten Bereich unter **Eigenschaften** auf **CustomGallerySample**.

    ![Ändern der Datenquelle](media/functions-powerapps-scenario/change-data-source.png)

1. Wählen Sie im Bereich **Daten** in der Liste die Option **Turbines** aus.

    ![Auswählen einer Datenquelle](media/functions-powerapps-scenario/select-data-source.png)

    Da das Dataset kein Bild enthält, passen wir als Nächstes das Layout an, damit es besser zu den Daten passt. 

1. Ändern Sie im Bereich **Daten** die Option **Layout** in **Titel, Untertitel und Text**.

    ![Ändern des Kataloglayouts](media/functions-powerapps-scenario/change-layout.png)

1. Ändern Sie als Letztes im Bereich **Daten** die im Katalog angezeigten Felder.

    ![Ändern der Katalogfelder](media/functions-powerapps-scenario/change-fields.png)
    
    + **Body1** = LastServiceDate
    + **Subtitle2** = ServiceRequired
    + **Title2** = Title 

1. Wählen Sie den Katalog aus, und legen Sie die Eigenschaft **TemplateFill** auf die folgende Formel fest: `If(ThisItem.IsSelected, Orange, White)`.

    ![Formel für die Vorlagenfüllung](media/functions-powerapps-scenario/formula-fill.png)

    Nun können Sie besser erkennen, welches Katalogelement ausgewählt ist.

    ![Ausgewähltes Element](media/functions-powerapps-scenario/selected-item.png)

1. Der ursprüngliche Bildschirm wird in der App nicht benötigt. Zeigen Sie im linken Bereich auf **Screen1**, klicken Sie auf **. . .** , und klicken Sie anschließend auf **Löschen**.

    ![Löschen des Bildschirms](media/functions-powerapps-scenario/delete-screen.png)

1. Klicken Sie auf **Datei**, und benennen Sie die App. Klicken Sie im linken Menü auf **Speichern** und anschließend rechts unten auf **Speichern**.

In einer Produktions-App werden üblicherweise noch weitere Formatierungsschritte ausgeführt, wir widmen uns an dieser Stelle jedoch dem wichtigen Aspekt dieses Szenarios: dem Aufrufen der Funktion.

## <a name="add-controls-to-call-the-function-and-display-data"></a>Hinzufügen von Steuerelementen zum Aufrufen der Funktion und zum Anzeigen von Daten
Sie verfügen nun über eine App, die Zusammenfassungsdaten für die einzelnen Turbinen anzeigt. Als Nächstes fügen wir Steuerelemente hinzu, die die von Ihnen erstellte Funktion aufrufen und die zurückgegebenen Daten anzeigen. Der Zugriff auf die Funktion erfolgt auf der Grundlage des Namens, den Sie ihr in der OpenAPI-Definition gegeben haben (in unserem Beispiel: `TurbineRepair.CalculateCosts()`).

1. Klicken Sie auf dem Menüband auf der Registerkarte **Einfügen** auf **Schaltfläche**. Klicken Sie auf der gleichen Registerkarte auf **Bezeichnung**.

    ![Einfügen einer Schaltfläche und einer Bezeichnung](media/functions-powerapps-scenario/insert-controls.png)

1. Ziehen Sie die Schaltfläche und die Bezeichnung unter den Katalog, und passen Sie die Größe der Bezeichnung an. 

1. Wählen Sie den Text der Schaltfläche aus, und ändern Sie ihn in `Calculate costs`. Die App sollte nun wie in der folgenden Abbildung aussehen:

    ![App mit Schaltfläche](media/functions-powerapps-scenario/move-button-label.png)

1. Wählen Sie die Schaltfläche aus, und geben Sie für die Eigenschaft **OnSelect** die folgende Formel ein:

    ```
    If (BrowseGallery1.Selected.ServiceRequired="Yes", ClearCollect(DetermineRepair, TurbineRepair.CalculateCosts({hours: BrowseGallery1.Selected.EstimatedEffort, capacity: BrowseGallery1.Selected.MaxOutput})))
    ```
    Diese Formel wird beim Klicken auf die Schaltfläche ausgeführt und bewirkt Folgendes, wenn das ausgewählte Katalogelement den **ServiceRequired**-Wert `Yes` besitzt:

    + Sie löscht die *Auflistung* `DetermineRepair`, um Daten aus früheren Aufrufen zu entfernen. Eine Auflistung ist eine tabellarische Variable.

    + Sie weist der Auflistung die durch Aufrufen der Funktion `TurbineRepair.CalculateCosts()` zurückgegebenen Daten zu. 
    
        Die an die Funktion übergebenen Werte stammen aus den Feldern **EstimatedEffort** und **MaxOutput** für das ausgewählte Katalogelement. Diese Felder werden zwar im Katalog nicht angezeigt, stehen aber trotzdem für Formeln zur Verfügung.

1. Wählen Sie die Bezeichnung aus, und geben Sie für die Eigenschaft **Text** die folgende Formel ein:

    ```
    "Repair decision: " & First(DetermineRepair).message & " | Cost: " & First(DetermineRepair).costToFix & " | Revenue: " & First(DetermineRepair).revenueOpportunity
    ```
    Diese Formel greift unter Verwendung der Funktion `First()` auf die erste (und einzige) Zeile der Auflistung `DetermineRepair` zu. Anschließend zeigt sie die drei von der Funktion zurückgegebenen Werte (`message`, `costToFix` und `revenueOpportunity`) an. Diese Werte sind bis zur ersten Ausführung der App leer.

    Die fertige App sollte nun wie in der folgenden Abbildung aussehen:

    ![Fertig gestellte App vor der Ausführung](media/functions-powerapps-scenario/finished-app-before-run.png)


## <a name="run-the-app"></a>Ausführen der App
Sie verfügen nun über eine vollständige App! Führen Sie als Nächstes die App aus, um die Funktionsaufrufe in Aktion zu erleben.

1. Klicken Sie rechts oben in PowerApps Studio auf die Schaltfläche zum Ausführen: ![Schaltfläche zum Ausführen der App](media/functions-powerapps-scenario/f5-arrow-sm.png).

1. Wählen Sie eine Turbine aus, die für **ServiceRequired** den Wert `Yes` besitzt, und klicken Sie anschließend auf die Schaltfläche für die Kostenberechnung (**Calculate costs**). Das Ergebnis sollte in etwa wie folgt aussehen:

    ![Fertige App in PowerApps](media/functions-powerapps-scenario/finished-app.png)

1. Experimentieren Sie mit den anderen Turbinen, um die von der Funktion zurückgegebenen Ergebnisse anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Thema wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Vorbereiten von Beispieldaten in Excel
> * Exportieren einer API-Definition
> * Hinzufügen einer Verbindung mit der API
> * Erstellen einer App und Hinzufügen von Datenquellen
> * Hinzufügen von Steuerelementen zum Anzeigen von Daten in der App
> * Hinzufügen von Steuerelementen zum Aufrufen der Funktion und zum Anzeigen von Daten
> * Ausführen der App, um zu bestimmen, ob eine Reparatur kosteneffizient ist

Weitere Informationen zu PowerApps finden Sie in der [Einführung in PowerApps](https://powerapps.microsoft.com/tutorials/getting-started/).

Ein weiteres interessantes Szenario mit Azure Functions finden Sie unter [Call a function from Microsoft Flow](functions-flow-scenario.md) (Aufrufen einer Funktion aus Microsoft Flow) und [Erstellen einer Funktion, die in Azure Logic Apps integriert ist](functions-twitter-email.md).
