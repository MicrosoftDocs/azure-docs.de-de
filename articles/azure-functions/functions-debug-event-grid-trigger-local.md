---
title: Lokales Debuggen von Azure-Funktionen mit Event Grid
description: Informationen zum lokalen Debuggen von Azure-Funktionen, die durch ein Event Grid-Ereignis ausgelöst werden
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94833060"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokales Debuggen von Azure-Funktionen mit Event Grid-Trigger

In diesem Artikel wird das Debuggen einer lokalen Funktion veranschaulicht, die ein Azure Event Grid-Ereignis verarbeitet, das von einem Speicherkonto ausgelöst wurde. 

## <a name="prerequisites"></a>Voraussetzungen

- Erstellen oder Verwenden einer vorhandenen Funktions-App
- Erstellen oder Verwenden eines vorhandenen Speicherkontos
- Herunterladen von [ngrok](https://ngrok.com/) zum Ermöglichen des Aufrufs Ihrer lokalen Funktion in Azure

## <a name="create-a-new-function"></a>Erstellen einer neuen Funktion

Öffnen Sie Ihre Funktions-App in Visual Studio, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen, und klicken Sie auf **Hinzufügen > Neue Azure-Funktion**.

Wählen Sie im Fenster *Neue Azure-Funktion* die Option **Event Grid-Trigger** aus, und klicken Sie auf **OK**.

![Erstellen einer neuen Funktion](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Nachdem die Funktion erstellt wurde, öffnen Sie die Codedatei, und kopieren Sie die URL, die am Anfang der Datei auskommentiert ist. Dieser Speicherort wird beim Konfigurieren des Event Grid-Triggers verwendet.

![Speicherort kopieren](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Legen Sie dann einen Haltepunkt in der Zeile fest, die mit `log.LogInformation` beginnt.

![Haltepunkt setzen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Drücken Sie anschließend **F5**, um eine Debugsitzung zu starten.

## <a name="allow-azure-to-call-your-local-function"></a>Erlauben des Aufrufs Ihrer lokalen Funktion in Azure

Um eine Funktion zu unterbrechen, die auf dem Computer gedebuggt wird, müssen Sie Azure die Kommunikation mit Ihrer lokalen Funktion von der Cloud aus ermöglichen.

Das Hilfsprogramm [ngrok](https://ngrok.com/) bietet Azure eine Möglichkeit zum Aufrufen der Funktion, die auf dem Computer ausgeführt wird. Starten Sie *ngrok* über den folgenden Befehl:

```bash
ngrok http -host-header=localhost 7071
```
Während das Hilfsprogramm eingerichtet wird, sollte das Befehlsfenster in etwa wie im folgenden Screenshot aussehen:

![Screenshot: Eingabeaufforderung nach dem Start des Hilfsprogramms „ngrok“](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopieren Sie die **HTTPS**-URL, die beim Ausführen von *ngrok* generiert wird. Dieser Wert wird beim Konfigurieren des Event Grid-Ereignisendpunkts verwendet.

## <a name="add-a-storage-event"></a>Hinzufügen eines Speicherereignisses

Öffnen Sie das Azure-Portal, navigieren Sie zu einem Speicherkonto, und klicken Sie auf die Option **Ereignisse**.

![Speicherkontoereignis hinzufügen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Klicken Sie im Fenster *Ereignisse* auf die Schaltfläche **Ereignisabonnement**. Klicken Sie im Fenster *Ereignisabonnement* auf die Dropdownliste *Endpunkttyp*, und wählen Sie **Webhook** aus.

![Abonnementtyp auswählen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Nachdem der Endpunkttyp konfiguriert wurde, klicken Sie auf **Endpunkt auswählen**, um den Endpunktwert zu konfigurieren.

![Endpunkttyp auswählen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Der Wert *Abonnentenendpunkt* besteht aus drei verschiedenen Werten. Das Präfix ist die HTTPS-URL, die von *ngrok* generiert wird. Der Rest der URL entspricht der URL aus der Funktionscodedatei mit am Ende hinzugefügtem Funktionsnamen. Ausgehend von der URL aus der Funktionscodedatei ersetzt die *ngrok*-URL `http://localhost:7071`, und der Funktionsname ersetzt `{functionname}`.

Der folgende Screenshot zeigt die endgültige URL:

![Endpunktauswahl](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Nachdem Sie den entsprechenden Wert eingegeben haben, klicken Sie auf **Auswahl bestätigen**.

> [!IMPORTANT]
> Bei jedem Start von *ngrok* wird die HTTPS-URL neu generiert, und der Wert ändert sich. Aus diesem Grund müssen Sie jedes Mal ein neues Ereignisabonnement erstellen, wenn Sie Ihre Funktion über *ngrok* für Azure verfügbar machen.

## <a name="upload-a-file"></a>Hochladen einer Datei

Jetzt können Sie eine Datei in Ihr Speicherkonto hochladen, um ein Event Grid-Ereignis zur Verarbeitung durch Ihre lokale Funktion auszulösen. 

Öffnen Sie den [Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/), und stellen Sie eine Verbindung mit Ihrem Speicherkonto her. 

- Erweitern Sie **Blobcontainer**. 
- Klicken Sie mit der rechten Maustaste, und wählen Sie **Blobcontainer erstellen**.
- Geben Sie dem Container den Namen **test**.
- Wählen Sie den Container *test* aus.
- Klicken Sie auf die Schaltfläche **Hochladen**.
- Klicken Sie auf **Dateien hochladen**.
- Wählen Sie eine Datei aus, und laden Sie sie in den Blobcontainer hoch.

## <a name="debug-the-function"></a>Debuggen der Funktion

Sobald Event Grid erkennt, dass eine neue Datei in den Speichercontainer hochgeladen wurde, wird der Haltepunkt in der lokalen Funktion erreicht.

![ngrok starten](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die in diesem Artikel erstellten Ressourcen zu bereinigen, löschen Sie den Container **test** in Ihrem Speicherkonto.

## <a name="next-steps"></a>Nächste Schritte

- [Automatisieren der Größenänderung von hochgeladenen Images per Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid-Trigger für Azure Functions](./functions-bindings-event-grid.md)
