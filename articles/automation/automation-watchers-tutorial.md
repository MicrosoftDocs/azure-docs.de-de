---
title: Erstellen eines Watcher-Tasks im Azure Automation-Konto
description: Erfahren Sie, wie Sie einen Watchertask im Azure Automation-Konto erstellen, um die Erstellung neuer Dateien in einem Ordner zu überwachen.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304611"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Erstellen von Azure Automation-Watchertasks zum Nachverfolgen von Dateiänderungen auf einem lokalen Computer

Azure Automation verwendet Watchertasks, um das System auf Ereignisse und Triggeraktionen in PowerShell-Runbooks zu überwachen. Dieses Tutorial führt Sie durch die Schritte zum Erstellen eines Watchertasks, mit dem Sie überwachen können, ob einem Verzeichnis eine neue Datei hinzugefügt wird.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Importieren eines Watcherrunbooks
> * Erstellen einer Automation-Variable
> * Erstellen eines Aktionsrunbooks
> * Erstellen eines Watchertasks
> * Auslösen eines Watchers
> * Untersuchen der Ausgabe

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorials müssen folgende Voraussetzungen erfüllt sein:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](automation-offering-get-started.md), um die Watcher- und Aktionsrunbooks und den Watchertask aufzunehmen.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), in dem der Watchertask ausgeführt wird.

> [!NOTE]
> Waterchertasks werden in Azure China nicht unterstützt.

## <a name="import-a-watcher-runbook"></a>Importieren eines Watcherrunbooks

Dieses Tutorial verwendet ein Watcherrunbook namens **Watch-NewFile**, um nach neuen Dateien in einem Verzeichnis zu suchen. Das Watcherrunbook ruft die Uhrzeit des letzten bekannten Schreibzugriffs auf die Dateien in einem Ordner ab und sucht nach Dateien, die jünger sind als dieser Zeitpunkt. Im folgenden Schritt importieren Sie dieses Runbook in Ihr Automation-Konto.

1. Öffnen Sie Ihr Automation-Konto, und klicken Sie auf die Seite **Runbooks**.
2. Klicken Sie auf die Schaltfläche **Katalog durchsuchen**.
3. Suchen Sie nach „Watcherrunbook“, wählen Sie **Watcherrunbook, das nach neuen Dateien in einem Verzeichnis sucht** aus, und klicken Sie auf **Importieren**.
  ![Importieren eines Automation-Runbooks über die Benutzeroberfläche](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Geben Sie einen Namen und eine Beschreibung für das Runbook ein, und wählen Sie **OK**, um das Runbook in Ihr Automation-Konto zu importieren.
1. Wählen Sie **Bearbeiten** aus, und klicken Sie dann auf **Veröffentlichen**. Wählen Sie bei der Aufforderung **Ja** aus, um das Runbook zu veröffentlichen.

## <a name="create-an-automation-variable"></a>Erstellen einer Automation-Variable

Eine [Automation-Variable](automation-variables.md) wird verwendet, um die Zeitstempel zu speichern, die das vorhergehende Runbook aus jeder Datei liest und speichert.

1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Variablen** aus, und klicken Sie dann auf **+Variable hinzufügen**.
1. Geben Sie als Namen „Watch-NewFileTimestamp“ ein.
1. Wählen Sie den Typ „DateTime“ aus.
1. Klicken Sie auf die Schaltfläche **Erstellen**. Damit wird die Automation-Variable erstellt.

## <a name="create-an-action-runbook"></a>Erstellen eines Aktionsrunbooks

Ein Aktionsrunbook wird in einem Watchertask verwendet, um Aktionen für die von einem Watcherrunbook übergebenen Daten auszuführen. PowerShell-Workflow-Runbooks werden von Watchertasks nicht unterstützt, Sie müssen PowerShell-Runbooks verwenden. In diesem Schritt importieren Sie ein vordefiniertes Aktionsrunbook namens „Process-NewFile“.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie in der Kategorie **Prozessautomatisierung** die Option **Runbooks** aus.
1. Klicken Sie auf die Schaltfläche **Katalog durchsuchen**.
1. Suchen Sie nach „Watcheraktion“, wählen Sie **Watcheraktion, die von einem Watcherrunbook ausgelöste Ereignisse verarbeitet** aus, und klicken Sie auf **Importieren**.
  ![Importieren eines Aktionsrunbooks über die Benutzeroberfläche](media/automation-watchers-tutorial/importsourceaction.png)
1. Geben Sie einen Namen und eine Beschreibung für das Runbook ein, und wählen Sie **OK**, um das Runbook in Ihr Automation-Konto zu importieren.
1. Wählen Sie **Bearbeiten** aus, und klicken Sie dann auf **Veröffentlichen**. Wählen Sie bei der Aufforderung **Ja** aus, um das Runbook zu veröffentlichen.

## <a name="create-a-watcher-task"></a>Erstellen eines Watchertasks

Der Watchertask besteht aus zwei Teilen, dem Watcher und der Aktion. Der Watcher wird in einem Intervall ausgeführt, das im Watchertask definiert ist. Daten aus dem Watcherrunbook werden an das Aktionsrunbook übergeben. In diesem Schritt konfigurieren Sie den Watchertask und verweisen dabei auf die Watcher- und Aktionsrunbooks, die in den vorherigen Schritten definiert wurden.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie in der Kategorie **Prozessautomatisierung** die Option **Watchertasks** aus.
1. Wählen Sie die Seite „Watchertasks“ aus, und klicken Sie auf die Schaltfläche **+Watchertask hinzufügen**.
1. Geben Sie „WatchMyFolder“ als Namen ein.

1. Wählen Sie **Watcher konfigurieren** und dann das Runbook **Watch-NewFile** aus.

1. Geben Sie die folgende Werte für die Parameter ein:

   * **Ordnerpfad**: Ein Ordner in dem Hybrid Worker, in dem neue Dateien erstellt werden. d:\examplefiles
   * **Erweiterung**: Lassen Sie dieses Feld leer, um alle Dateierweiterungen zu verarbeiten.
   * **Rekursiv durchlaufen**: Behalten Sie hier den Standardwert bei.
   * **Ausführungseinstellungen**: Wählen Sie den Hybrid Worker aus.

1. Klicken Sie auf „OK“ und dann auf „Auswählen“, um zur Watcherseite zurückzukehren.
1. Wählen Sie **Aktion konfigurieren** und dann das Runbook „Process-NewFile“ aus.
1. Geben Sie die folgende Werte für die Parameter ein:

   * **Ereignisdaten**: Lassen Sie dieses Feld leer. Die Daten werden vom Watcherrunbook übergeben.  
   * **Ausführungseinstellungen**: Übernehmen Sie hier „Azure“, da dieses Runbook im Automation-Dienst ausgeführt wird.

1. Klicken Sie auf **OK** und dann auf „Auswählen“, um zur Watcherseite zurückzukehren.
1. Klicken Sie auf **OK**, um den Watchertask zu erstellen.

![Konfigurieren der Watcheraktion über die Benutzeroberfläche](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Auslösen eines Watchers

Um zu testen, ob der Watcher erwartungsgemäß funktioniert, müssen Sie eine Testdatei erstellen.

Stellen Sie eine Remoteverbindung mit dem Hybrid Worker her. Öffnen Sie **PowerShell**, und erstellen Sie im Ordner eine Testdatei.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Das folgende Beispiel zeigt die erwartete Ausgabe.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Untersuchen der Ausgabe

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie in der Kategorie **Prozessautomatisierung** die Option **Watchertasks** aus.
1. Wählen Sie den Watchertask „WatchMyFolder“ aus.
1. Klicken Sie unter **Datenströme** auf **Watcherdatenströme anzeigen**, um zu überprüfen, ob der Watcher die neue Datei gefunden und das Aktionsrunbook gestartet hat.
1. Klicken Sie auf **Watcheraktionsaufträge anzeigen**, um den Aktionsrunbookauftrag anzuzeigen. Sie können jeden einzelnen Auftrag auswählen, um die Details anzuzeigen.

   ![Watcheraktionsaufträge über die Benutzeroberfläche](media/automation-watchers-tutorial/WatcherActionJobs.png)

Im folgenden Beispiel sehen Sie die erwartete Ausgabe, wenn die neue Datei gefunden wurde:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Importieren eines Watcherrunbooks
> * Erstellen einer Automation-Variable
> * Erstellen eines Aktionsrunbooks
> * Erstellen eines Watchertasks
> * Auslösen eines Watchers
> * Untersuchen der Ausgabe

Klicken Sie auf folgenden Link, um weitere Informationen zum Erstellen von Runbooks zu erhalten:

> [!div class="nextstepaction"]
> [Mein erstes PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

