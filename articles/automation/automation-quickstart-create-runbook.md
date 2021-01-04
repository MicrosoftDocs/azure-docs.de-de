---
title: Azure-Schnellstart – Erstellen eines Azure Automation-Runbooks | Microsoft-Dokumentation
description: Dieser Artikel hilft Ihnen bei der Erstellung eines Azure Automation-Runbooks.
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 948ca820347c7cdcd560ade46e850f66b25bc88e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "90987290"
---
# <a name="create-an-azure-automation-runbook"></a>Erstellen eines Azure Automation-Runbooks

Azure Automation-Runbooks können über Azure erstellt werden. Mit dieser Methode wird eine browserbasierte Benutzeroberfläche zum Erstellen von Automation-Runbooks bereitgestellt. In dieser Schnellstartanleitung wird das Erstellen, Bearbeiten, Testen und Veröffentlichen eines Automation PowerShell-Runbooks Schritt für Schritt beschrieben.

Wenn Sie über kein Azure-Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com bei Azure an.

## <a name="create-the-runbook"></a>Erstellen des Runbooks

Erstellen Sie zuerst ein Runbook. Die Ausgabe des Beispielrunbooks, das in dieser Schnellstartanleitung erstellt wird, lautet standardmäßig `Hello World`.

1. Öffnen Sie Ihr Automation-Konto.

1. Klicken Sie unter **Prozessautomatisierung** auf **Runbooks**. Die Liste mit den Runbooks wird angezeigt.

1. Klicken Sie oben in der Liste auf **Runbook erstellen**.

1. Geben Sie im Feld **Name** den Runbooknamen `Hello-World` ein, und wählen Sie im Feld **Runbooktyp** die Option **PowerShell** aus. 

   ![Eingeben von Informationen zu Ihrem Automation-Runbook auf der Seite](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klicken Sie auf **Erstellen**. Das Runbook wird erstellt, und die Seite „PowerShell-Runbook bearbeiten“ wird geöffnet.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png" alt-text="Screenshot: Seite „PowerShell-Runbook bearbeiten“":::

1. Geben Sie den folgenden Code im Bearbeitungsbereich ein (oder fügen Sie ihn ein). Dadurch wird der optionale Eingabeparameter `Name` mit dem Standardwert `World` erstellt, und es wird eine Zeichenfolge ausgegeben, für die dieser Eingabewert verwendet wird:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klicken Sie auf **Speichern**, um eine Entwurfskopie des Runbooks zu speichern.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-edit-runbook.png" alt-text="Screenshot: Seite „PowerShell-Runbook bearbeiten“ mit einem Codebeispiel im rechten Fenster":::

## <a name="test-the-runbook"></a>Testen des Runbooks

Testen Sie das Runbook nach der Erstellung, um sich zu vergewissern, dass es funktioniert.

1. Klicken Sie auf **Testbereich** , um den Testbereich zu öffnen.

1. Geben Sie einen Wert für **Name** ein, und klicken Sie auf **Starten**. Der Testauftrag wird gestartet, und der Auftragsstatus und die Ausgabe werden angezeigt.

    :::image type="content" source="./media/automation-quickstart-create-runbook/automation-test-runbook.png" alt-text="Screenshot: Testbereich mit einem Beispielwert im Feld „Name“":::

1. Schließen Sie den Testbereich, indem Sie rechts oben auf das **X** klicken. Klicken Sie im angezeigten Popupmenü auf **OK**.

1. Klicken Sie auf der Seite „PowerShell-Runbook bearbeiten“ auf **Veröffentlichen**, um das Runbook als offizielle Version des Runbooks im Konto zu veröffentlichen.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png" alt-text="Screenshot: Seite „PowerShell-Runbook bearbeiten“ mit ausgewählter Schaltfläche „Veröffentlichen“":::

## <a name="run-the-runbook"></a>Ausführen des Runbooks

Nachdem das Runbook veröffentlicht wurde, wird die Übersichtsseite angezeigt.

1. Klicken Sie auf der Übersichtsseite des Runbooks auf **Starten**, um die Konfigurationsseite „Runbook starten“ für das Runbook zu öffnen.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png" alt-text="Screenshot: Konfigurationsseite „Runbook starten“":::

1. Lassen Sie das Feld **Name** leer, damit der Standardwert verwendet wird, und klicken Sie auf **OK**. Der Runbookauftrag wird übermittelt, und die Auftragsseite wird angezeigt.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-job-page.png" alt-text="Screenshot: Auftragsseite mit ausgewählter Schaltfläche „Ausgabe“":::

1. Wenn der Auftragsstatus `Running` oder `Completed` lautet, können Sie auf **Ausgabe** klicken, um den Ausgabebereich zu öffnen und die Runbookausgabe anzuzeigen.

   :::image type="content" source="./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png" alt-text="Screenshot: Ausgabebereich mit der Runbookausgabe":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie das Runbook, falls es nicht mehr benötigt wird. Wählen Sie das Runbook hierzu in der Runbookliste aus, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Runbook erstellt, bearbeitet, getestet und veröffentlicht und einen Runbookauftrag gestartet. Weitere Informationen zu Automation-Runbooks finden Sie im Artikel zu den unterschiedlichen Runbooktypen, die Sie in Automation erstellen und nutzen können.

> [!div class="nextstepaction"]
> [Azure Automation-Runbooktypen](./automation-runbook-types.md)
