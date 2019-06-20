---
title: Testen eines Azure Stream Analytics-Auftrags mit Beispieldaten
description: In diesem Artikel wird beschrieben, wie Sie mithilfe des Azure-Portals einen Azure Stream Analytics-Auftrag mit Beispieleingabedaten testen und Beispieldaten hochladen.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: fca76b632e9bcc27ed762886eaea696a5696ad3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761912"
---
# <a name="test-a-stream-analytics-query-with-sample-data"></a>Testen einer Stream Analytics-Abfrage mit Beispieldaten

Mit Azure Stream Analytics können Sie Beispieldaten und Testabfragen in das Azure-Portal hochladen, ohne einen Auftrag starten oder beenden zu müssen.

## <a name="upload-sample-data-and-test-the-query"></a>Hochladen von Beispieldaten und Testen der Abfrage

1. Melden Sie sich beim Azure-Portal an. 

2. Suchen Sie Ihren vorhandenen Stream Analytics-Auftrag, und wählen Sie ihn aus.

3. Wählen Sie auf der Stream Analytics-Auftragsseite unter der Überschrift **Auftragstopologie** die Option **Abfrage** aus, um das Fenster des Abfrage-Editors zu öffnen. 

4. Um Ihre Abfrage mit Beispieleingabedaten zu testen, klicken Sie mit der rechten Maustaste auf eine Ihrer Eingaben.  Wählen Sie dann **Beispieldaten aus Datei hochladen** aus. Die Daten müssen in JSON, CSV oder AVRO serialisiert werden. Die Beispieleingabe muss UTF-8-codiert und darf nicht komprimiert werden. Als Trennzeichen wird für Tests von CSV-Eingaben im Portal nur das Komma (,) unterstützt.

    ![Testen von Abfragen im Stream Analytics-Abfrage-Editor](media/stream-analytics-test-query/stream-analytics-test-query-editor-upload.png)

5. Wählen Sie nach Abschluss des Uploadvorgangs die Option **Testen** aus, um diese Abfrage mit den Beispieldaten zu testen, die Sie bereitgestellt haben.

    ![Testen von Beispieldaten im Stream Analytics-Abfrage-Editor](media/stream-analytics-test-query/stream-analytics-test-query-editor-test.png)

6. Wenn Sie die Testausgabe zur späteren Verwendung benötigen, wird die Ausgabe der Abfrage im Browser mit einem Link zu den Downloadergebnissen angezeigt. 

7. Ändern Sie Ihre Abfrage iterativ, und testen Sie sie erneut, um festzustellen, wie sich die Ausgabe ändert.

   ![Beispielausgabe im Stream Analytics-Abfrage-Editor](media/stream-analytics-test-query/stream-analytics-test-query-editor-samples-output.png)

   Wenn Sie in einer Abfrage mehrere Ausgaben verwenden, werden die Ergebnisse auf separaten Registerkarten angezeigt, und Sie können problemlos zwischen ihnen wechseln.

8. **Speichern** Sie Ihre Abfrage, nachdem Sie die im Browser angezeigten Ergebnisse überprüft haben. **Starten** Sie dann den Auftrag, und lassen Sie ihn die eingehenden Ereignisse verarbeiten.

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
