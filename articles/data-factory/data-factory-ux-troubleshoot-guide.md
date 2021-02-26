---
title: Problembehandlung bei der Azure Data Factory-Benutzeroberfläche
description: Hier erfahren Sie, wie Probleme bei der Azure Data Factory (ADF)-Benutzeroberfläche behandelt werden können.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382836"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problembehandlung bei der Azure Data Factory-Benutzeroberfläche

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden die gängigen Problembehandlungsmethoden für die Azure Data Factory (ADF)-Benutzeroberfläche beschrieben.

## <a name="adf-ux-not-loading"></a>ADF-Benutzeroberfläche wird nicht geladen

> [!NOTE]
> Die Azure Data Factory-Benutzeroberfläche unterstützt offiziell Microsoft Edge und Google Chrome. Die Verwendung anderer Webbrowser kann zu unerwartetem oder nicht dokumentiertem Verhalten führen.

### <a name="third-party-cookies-blocked"></a>Drittanbietercookies blockiert

In der ADF-Benutzeroberfläche werden Browsercookies verwendet, um die Benutzersitzung dauerhaft zu speichern sowie interaktive Entwicklungs- und Überwachungsumgebungen zu aktivieren. Es ist möglich, dass Ihr Browser Drittanbietercookies blockiert, weil Sie eine Inkognitositzung verwenden oder einen Werbeblocker aktiviert haben. Das Blockieren von Drittanbietercookies kann beim Laden des Portals zu Problemen führen. So werden Sie beispielsweise zu einer leeren Seite umgeleitet, ‚ https://adf.azure.com/accesstoken.html ‘, oder Sie erhalten eine Warnmeldung mit der Information, dass Drittanbietercookies blockiert werden. Aktivieren Sie zur Behebung dieses Problems die Optionen für Drittanbietercookies in Ihrem Browser mit den folgenden Schritten:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Alle Cookies zulassen

1. Navigieren Sie in Ihrem Browser zu **chrome://settings/cookies**.
1. Wählen Sie die Option **Alle Cookies zulassen** aus. 

    ![Alle Cookies in Chrome zulassen](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Die Verwendung von Cookies nur für die ADF-Benutzeroberfläche zulassen
Wenn Sie nicht alle Cookies zulassen möchten, können Sie sie optional nur für die ADF-Benutzeroberfläche zulassen:
1. Navigieren Sie zu **chrome://settings/cookies**.
1. Wählen Sie unter der Option **Sites that can always use cookies** (Websites, die immer Cookies verwenden können) **Hinzufügen** aus. 

    ![Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Fügen Sie die Website **adf.azure.com** hinzu, aktivieren Sie die Option **Alle Cookies**, und speichern Sie. 

    ![Alle Cookies von der Website der ADF-Benutzeroberfläche zulassen](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Navigieren Sie in Ihrem Browser zu **edge://settings/content/cookies**.
1. Stellen Sie sicher, dass **Allow sites to save and read cookie data** (Für Websites das Speichern und Lesen von Cookiedaten zulassen) aktiviert und die Option **Cookies von Drittanbietern blockieren** deaktiviert ist. 

    ![Alle Cookies in Edge zulassen](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Die Verwendung von Cookies nur für die ADF-Benutzeroberfläche zulassen

Wenn Sie nicht alle Cookies zulassen möchten, können Sie sie optional nur für die ADF-Benutzeroberfläche zulassen:

1. Navigieren Sie zu **edge://settings/content/cookies**.
1. Wählen Sie im Abschnitt **Zulassen** die Option **Hinzufügen** aus, und fügen Sie die Website **adf.azure.com** hinzu. 

    ![Hinzufügen der ADF-Benutzeroberfläche zu zugelassenen Websites in Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Aktualisieren Sie die ADF-Benutzeroberfläche, und versuchen Sie es erneut.

## <a name="connection-failed-on-adf-ux"></a>Fehler bei der Verbindung mit der ADF-Benutzeroberfläche

In manchen Fällen wird auf der ADF-Benutzeroberfläche „Fehler bei Verbindung“ angezeigt (siehe Screenshot unten), nachdem Sie auf **Verbindung testen**, **Vorschau** usw. geklickt haben.

![Fehler bei der Verbindung](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

In diesem Fall können Sie den gleichen Vorgang mit dem InPrivate-Browsermodus in Ihrem Browser ausprobieren.

Wenn es dann weiterhin nicht funktioniert, drücken Sie die Taste F12, um **Entwicklertools** zu öffnen. Wechseln Sie zur Registerkarte **Netzwerk**, aktivieren Sie **Cache deaktivieren**, wiederholen Sie den Vorgang, bei dem der Fehler aufgetreten ist, und suchen Sie nach der fehlerhaften Anforderung (rot).

![Fehlerhafte Anforderung](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Suchen Sie anschließend den **Hostnamen** (in diesem Fall **dpnortheurope.svc.datafactory.azure.com**) in der **Anforderungs-URL** der fehlerhaften Anforderung.

Geben Sie den **Hostnamen** direkt in der Adressleiste Ihres Browsers ein. Wenn im Browser 404 angezeigt wird, bedeutet dies in der Regel, dass Ihre Clientseite in Ordnung ist und das Problem auf der ADF-Dienstseite vorliegt. Übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung der ADF-Benutzeroberfläche.

![Ressource nicht gefunden](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Wenn dies nicht der Fall ist oder unten im Browser ein ähnlicher Fehler angezeigt wird, bedeutet dies in der Regel, dass ein clientseitiges Problem vorliegt. Befolgen Sie die weiteren Schritte zur Problembehandlung.

![Clientseitiger Fehler](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Öffnen Sie die **Eingabeaufforderung**, und geben Sie **nslookup dpnortheurope.svc.datafactory.azure.com** ein. Das Ergebnis sieht normalerweise in etwa wie folgt aus:

![Befehlsantwort 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Wenn eine normale DNS-Antwort angezeigt wird, wenden Sie sich an den lokalen IT-Support, um die Firewalleinstellungen in Bezug darauf zu überprüfen, ob die HTTPS-Verbindung mit diesem Hostnamen blockiert ist. Wenn das Problem nicht behoben werden kann, übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung der ADF-Benutzeroberfläche.

Wenn etwas anderes angezeigt wird, bedeutet dies in der Regel, dass beim DNS-Server ein Fehler mit der Auflösung des DNS-Namens vorliegt. Eine Problemumgehung besteht beispielsweise im Ändern des Internetdienstanbieters (Internet Service Provider, ISP) oder des DNS (z. B. in den Google-DNS 8.8.8.8). Wenn das Problem weiterhin besteht, prüfen Sie **nslookup datafactory.azure.com** und **nslookup azure.com**, um zu sehen, auf welcher Ebene die DNS-Auflösung fehlgeschlagen ist, und übermitteln Sie alle Informationen an Ihren lokalen IT-Support oder Ihren ISP zur Problembehandlung. Wenn der Support der Meinung ist, dass das Problem weiterhin aufseiten von Microsoft vorliegt, übermitteln Sie ein Supportticket mit der **Aktivitäts-ID** aus der Fehlermeldung ADF-Benutzeroberfläche.

![Befehlsantwort 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Ändern des verknüpften Diensttyps in Datasets

Datasets im Dateiformat können mit allen dateibasierten Connectors verwendet werden. Sie können z. B. ein Parquet-Dataset für Azure Blob Storage oder Azure Data Lake Storage Gen2 konfigurieren. Beachten Sie, dass die einzelnen Connectors unterschiedliche datenspeicherbezogene Einstellungen für die Aktivität sowie unterschiedliche App-Modelle unterstützen. 

Wenn Sie auf der ADF-Benutzeroberfläche zur Erstellung Datasets im Dateiformat für eine Aktivität verwenden (einschließlich „Kopieren“, „Suchen“, „Metadaten abrufen“ und „Löschen“) und im Dataset auf einen verknüpften Dienst zeigen möchten, dessen Typ nicht dem aktuellen Typ entspricht (z. B. ein Wechsel von Dateisystem zu ADLS Gen2), wird die folgende Warnmeldung angezeigt. Für einen reibungslosen Wechsel werden die Pipelines und Aktivitäten, die auf dieses Dataset verweisen, nach Ihrer Zustimmung so geändert, dass sie ebenfalls den neuen Typ verwenden. Da vorhandene Datenspeichereinstellungen, die nicht mit dem neuen Typ kompatibel sind, nicht mehr gültig sind, werden diese Einstellungen gelöscht.

Weitere Informationen zu den unterstützten Datenspeichereinstellungen für die verschiedenen Connectors finden Sie im entsprechenden Artikel zum jeweiligen Connector. Kopieren Sie die Aktivitätseigenschaften, um eine detaillierte Liste der Eigenschaften anzuzeigen. Weitere Informationen zu [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Dateisystem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md) und [SFTP](connector-sftp.md).

![Warnmeldung](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

* [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory-Funktionsanfragen](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-Videos](https://azure.microsoft.com/resources/videos/index/)
* [Frageseite von Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-data-factory.html)