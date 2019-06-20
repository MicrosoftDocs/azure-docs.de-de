---
title: Anzeigen von Dateiinhaltsänderungen mit Azure Automation
description: Zeigen Sie geänderte Dateiinhalte mithilfe des Features „Änderungsnachverfolgung“ an.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dae0ea3e564a3dfafcd7f072f9b0d8854df17ed7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61304223"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Anzeigen des Inhalts einer Datei, die mit der Änderungsnachverfolgung überwacht wird

Die Nachverfolgung von Dateiinhalten ermöglicht Ihnen, den Inhalt einer Datei vor und nach einer mit Änderungsnachverfolgung verfolgten Änderung einzusehen. Dazu wird der Dateiinhalt nach jeder Änderung in einem Speicherkonto gespeichert.

## <a name="requirements"></a>Requirements (Anforderungen)

* Für die Speicherung von Dateiinhalten ist ein Standardspeicherkonto unter Verwendung des Ressourcen-Manager-Bereitstellungsmodells erforderlich. Storage Premium-Konten und Konten mit klassischem Bereitstellungsmodell sollten nicht verwendet werden. Weitere Informationen zu Speicherkonten finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/common/storage-create-storage-account.md).

* Mit dem verwendeten Speicherkonto darf nur ein einziges Automation-Konto verbunden sein.

* [Änderungsnachverfolgung](automation-change-tracking.md) ist in Ihrem Automation-Konto aktiviert.

## <a name="enable-file-content-tracking"></a>Aktivieren der Nachverfolgung von Dateiinhalten

1. Öffnen Sie im Azure-Portal Ihr Automation-Konto, und wählen Sie dann **Änderungsnachverfolgung** aus.
2. Wählen Sie im oberen Menü **Einstellungen bearbeiten** aus.
3. Wählen Sie **Dateiinhalt** aus, und klicken Sie auf **Link**. Der Bereich **Inhaltsspeicherort für Änderungsnachverfolgung hinzufügen** wird geöffnet.

   ![enable](./media/change-tracking-file-contents/enable.png)

4. Wählen Sie das Abonnement und das Speicherkonto aus, in dem der Dateiinhalt gespeichert werden soll. Wenn Sie die Nachverfolgung von Dateiinhalten für alle vorhandenen nachverfolgten Dateien aktivieren möchten, wählen Sie **Ein** für **Dateiinhalte für alle Einstellungen hochladen**. Sie können dies für jeden Dateipfad nachträglich ändern.

   ![Speicherkonto festlegen](./media/change-tracking-file-contents/storage-account.png)

5. Nach der Aktivierung werden das Speicherkonto und die SAS-URIs angezeigt. Die SAS-URIs laufen nach 365 Tagen ab und können durch Klicken auf die Schaltfläche **Erneut generieren** erneut erstellt werden.

   ![Auflisten von Kontoschlüsseln](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Hinzufügen einer Datei

In den folgenden Schritten wird exemplarisch das Aktivieren der Nachverfolgung für eine Datei erläutert:

1. Wählen Sie auf der Seite **Einstellungen bearbeiten** von **Änderungsnachverfolgung** entweder **Windows-Dateien** oder **Linux-Dateien** aus, und klicken Sie auf **Hinzufügen**.

1. Füllen Sie die Informationen für den Dateipfad aus, und wählen Sie **Wahr** unter **Dateiinhalte für alle Einstellungen hochladen** aus. Diese Einstellung ermöglicht die Nachverfolgung von Dateiinhalten nur für diesen Dateipfad.

   ![Hinzufügen einer Linux-Datei](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Anzeigen des Inhalts einer nachverfolgten Datei

1. Sobald eine Änderung für die Datei oder eine Datei im Pfad erkannt wurde, wird sie im Portal angezeigt. Wählen Sie die Dateiänderung in der Liste der Änderungen aus. Der Bereich **Änderungsdetails** wird angezeigt.

   ![Änderungen auflisten](./media/change-tracking-file-contents/change-list.png)

1. Auf der Seite **Änderungsdetails** sehen Sie den Standard vor und nach der Dateiinformation. Klicken Sie links oben auf **Änderungen am Dateiinhalt anzeigen**, um den Inhalt der Datei anzuzeigen.

   ![Änderungsdetails](./media/change-tracking-file-contents/change-details.png)

1. Die neue Seite zeigt Ihnen den Dateiinhalt in einer nebeneinander angeordneten Ansicht. Sie können auch **Inline** auswählen, um eine Inlineansicht der Änderungen anzuzeigen.

   ![Anzeigen von Dateiänderungen](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Nächste Schritte

Besuchen Sie das Tutorial zur Änderungsnachverfolgung, um mehr über die Verwendung der Lösung zu erfahren:

> [!div class="nextstepaction"]
> [Problembehandlung für Änderungen in Ihrer Umgebung](automation-tutorial-troubleshoot-changes.md)

* Verwenden Sie [Protokollsuchen in Azure Monitor-Protokolle](../log-analytics/log-analytics-log-searches.md), um ausführliche Nachverfolgungsdaten anzuzeigen.

