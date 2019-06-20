---
title: Integrieren der Remoteüberwachungslösung in Data Lake Store – Azure | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Remoteüberwachungslösung in Azure Data Lake Store mit einem Azure Stream Analytics-Auftrag integrieren können.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 021f18f588613817110539d408f9260fb9247895
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61449343"
---
# <a name="integrate-the-remote-monitoring-solution-with-azure-data-lake-store"></a>Integrieren der Remoteüberwachungslösung in Azure Data Lake Store

Sie verfügen möglicherweise über erweiterte Analyseanforderungen, die über das hinausgehen, was in der Remoteüberwachungslösung angeboten wird. Azure Data Lake Store eignet sich ideal für diese Anwendung, da Daten aus massiven und verschiedenen Datasets gespeichert werden können und eine Integration in Azure Data Lake Analytics erfolgen kann, um bei Bedarf Analysen bereitzustellen.

In dieser schrittweisen Vorgehensweise verwenden Sie einen Azure Stream Analytics-Auftrag zum Streamen von Daten vom IoT Hub in Ihrer Remoteüberwachungslösung in einen Azure Data Lake Store.

## <a name="prerequisites"></a>Voraussetzungen

Für diese Vorgehensweise ist Folgendes erforderlich:

* [Bereitstellen des Solution Accelerators für die Remoteüberwachung](quickstart-remote-monitoring-deploy.md)
  * Die Remoteüberwachungslösung stellt den IoT Hub und den Azure Stream Analytics-Auftrag in Ihrem Azure-Abonnement bereit, der in diesem Artikel verwendet wird.
* [Bereitstellen eines Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
  * Ihr Data Lake Store sollte in der gleichen Region wie Ihre Remoteüberwachungslösung bereitgestellt werden.
  * [Erstellen Sie einen Ordner](../data-lake-store/data-lake-store-get-started-portal.md#createfolder) mit dem Namen „streaming“ in Ihrem Konto.

## <a name="create-a-consumer-group"></a>Erstellen einer Consumergruppe

Erstellen Sie eine dedizierte Comsumergruppe im IoT Hub Ihrer Remoteüberwachungslösung. Diese wird vom Stream Analytics-Auftrags für das Streamen von Daten in Ihren Data Lake Store verwendet.

> [!NOTE]
> Consumergruppen werden von Anwendungen verwendet, um Daten aus Azure IoT Hub abzurufen. Sie sollten für jeweils fünf Ausgabeconsumer eine neue Consumergruppe erstellen. Sie können bis zu 32 Consumergruppen erstellen.

1. Melden Sie sich beim Azure-Portal an.

1. Klicken Sie im Azure-Portal auf die Schaltfläche **Cloud Shell**.

    ![Symbol für den Portalstart](./media/iot-accelerators-integrate-data-lake/portal-launch-icon.png)

1. Führen Sie diesen Befehl aus, um eine neue Consumergruppe zu erstellen:

```azurecli-interactive
az iot hub consumer-group create --hub-name contoso-rm30263 --name streamanalyticsjob --resource-group contoso-rm
```

> [!NOTE]
> Verwenden Sie die Ressourcengruppen- und IoT Hub-Namen aus der Remoteüberwachungslösung.

## <a name="create-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

Erstellen Sie einen Azure Stream Analytics-Auftrag zum Streamen der Daten aus Ihrem IoT Hub in Ihren Azure Data Lake Store.

1. Klicken Sie auf **Ressource erstellen**, wählen Sie Internet der Dinge (IoT) aus Marketplace aus, und klicken Sie dann auf **Stream Analytics-Auftrag**.

    ![Neuer Stream Analytics-Auftrag](./media/iot-accelerators-integrate-data-lake/new-stream-analytics-job.png)

1. Geben Sie einen Auftragsnamen ein, und wählen Sie das entsprechende Abonnement und die Ressourcengruppe aus.

1. Wählen Sie einen Speicherort in der Nähe oder in der gleichen Region aus, in der sich Ihr Data Lake Store befindet. Hier verwenden wir „USA, Osten“.

1. Achten Sie darauf, die Standardeinstellung **Cloud** für die Hostingumgebung beizubehalten.

1. Klicken Sie auf **Create**.

    ![Erstellen eines Stream Analytics-Auftrags](./media/iot-accelerators-integrate-data-lake/create-stream-analytics-job.png)

## <a name="configure-the-stream-analytics-job"></a>Konfigurieren des Stream Analytics-Auftrags

1. Wechseln Sie zu Ihrem **Stream Analytics-Auftrag** in der Ressourcengruppe der Remoteüberwachungslösung.

1. Klicken Sie auf der Seite „Übersicht“ auf **Eingaben**.

    ![Seite „Übersicht“](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview.png)

1. Klicken Sie auf **Datenstromeingabe hinzufügen**, und wählen Sie dann **IoT Hub** aus der Dropdownliste aus.

    ![Hinzufügen der Eingabe](./media/iot-accelerators-integrate-data-lake/stream-analytics-add-input.png)

1. Geben Sie auf der Registerkarte „Neue Eingabe“ den Eingabealias **IoTHub** ein.

1. Wählen Sie aus der Dropdownliste „Consumergruppe“ die Consumergruppe aus, die Sie zuvor erstellt haben. Hier verwenden wir **streamanalyticsjob**.

    ![Auswählen der Eingabe](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-input.png)

1. Klicken Sie auf **Speichern**.

1. Klicken Sie auf der Seite „Übersicht“ auf **Ausgaben**.

    ![Hinzufügen von Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-overview-2.png)

1. Klicken Sie auf **Hinzufügen**, und wählen Sie **Data Lake Store** aus der Dropdownliste aus.

    ![Hinzufügen der Ausgabe](./media/iot-accelerators-integrate-data-lake/stream-analytics-output.png)

1. Geben Sie auf der Registerkarte „Neue Ausgabe“ den Ausgabealias **DataLakeStore** ein.

1. Wählen Sie das Data Lake Store-Konto aus, das Sie in den vorherigen Schritten erstellt haben, und geben Sie die Ordnerstruktur zum Streamen von Daten in den Speicher an.

1. Geben Sie im Feld Datumsformat **/streaming/ {Datum}/{Uhrzeit}** ein. Behalten Sie das Standarddatumsformat JJJJ/MM/TT und das Uhrzeitformat HH bei.

    ![Bereitstellen der Ordnerstruktur](./media/iot-accelerators-integrate-data-lake/stream-analytics-new-output.png)

1. Klicken Sie auf **Autorisieren**.

    Sie müssen eine Autorisierung mit Data Lake Store ausführen, um dem Stream Analytics-Auftrag Schreibzugriff auf das Dateisystem zu gewähren.

    ![Autorisieren von Stream Analytics für Data Lake Store](./media/iot-accelerators-integrate-data-lake/stream-analytics-out-authorize.png)

    Es wird ein Popupfenster angezeigt. Sobald das Popupfenster geschlossen wird, wird die Schaltfläche „Autorisieren“ nach Abschluss der Autorisierung abgeblendet.

    > [!NOTE]
    > Wenn im Popupfenster ein Fehler angezeigt wird, öffnen Sie ein neues Browserfenster im Inkognitomodus, und versuchen Sie es erneut.

1. Klicken Sie auf **Speichern**.

## <a name="edit-the-stream-analytics-query"></a>Bearbeiten der Stream Analytics-Abfrage

Azure Stream Analytics verwendet eine SQL-ähnliche Abfragesprache zum Angeben einer Eingabequelle, die Daten streamt, diese Daten wie gewünscht transformiert und die Ausgabe an eine Vielzahl von Speicher- oder Verarbeitungsziele vornimmt.

1. Klicken Sie auf der Registerkarte „Übersicht“ auf **Abfrage bearbeiten**.

    ![Abfrage bearbeiten](./media/iot-accelerators-integrate-data-lake/stream-analytics-edit-query.png)

1. Ersetzen Sie im Abfrage-Editor die Platzhalter [YourOutputAlias] und [YourInputAlias] durch die zuvor definierten Werte.

    ```sql
    SELECT
        *, System.Timestamp as time
    INTO
        DataLakeStore
    FROM
        IoTHub
    ```

    ![Stream Analytics-Abfrage](./media/iot-accelerators-integrate-data-lake/stream-analytics-query.png)

1. Klicken Sie auf **Speichern**.
1. Klicken Sie auf **Ja** um die Änderungen zu übernehmen.

## <a name="start-the-stream-analytics-job"></a>Starten des Stream Analytics-Auftrags

1. Klicken Sie auf der Registerkarte „Übersicht“ auf **Start**.

    ![Dient zum Starten von Stream Analytics-Aufträgen.](./media/iot-accelerators-integrate-data-lake/stream-analytics-start.png)

1. Klicken Sie auf der Registerkarte „Auftrag starten“ auf **Benutzerdefiniert**.

1. Legen Sie die benutzerdefinierte Zeit auf einen Zeitpunkt vor einigen Stunden fest, um Daten abzurufen, die erfasst wurden, als Ihr Gerät mit dem Streaming begonnen hat.

1. Klicken Sie auf **Start**.

    ![Auswählen des benutzerdefinierten Datums](./media/iot-accelerators-integrate-data-lake/stream-analytics-start-custom.png)

    Warten Sie, bis der Auftrag ausgeführt wird. Wenn Fehler angezeigt werden, stammen dieses möglicherweise aus Ihrer Abfrage. Stellen Sie sicher, dass die Syntax richtig ist.

    ![Auftrag wird ausgeführt](./media/iot-accelerators-integrate-data-lake/stream-analytics-running.png)

    Die Streamingauftrag beginnt mit dem Lesen von Daten aus Ihrem IoT Hub und speichert die Daten in Ihrem Data Lake Store. Es dauert möglicherweise einige Minuten, bis die Daten im Data Lake Store angezeigt werden.

## <a name="explore-the-streaming-data"></a>Untersuchen der Streamingdaten

1. Wechseln Sie zu Ihrem Data Lake Store.

1. Klicken Sie auf der Registerkarte „Übersicht“ auf **Daten-Explorer**.

1. Führen Sie im Daten-Explorer einen Drilldown zum Ordner **/streaming** aus. Es wurden Ordner mit dem Format „JJJJ/MM/TT/HH“ erstellt.

    ![Untersuchen der Streamingdaten](./media/iot-accelerators-integrate-data-lake/data-lake-store-data-explorer.png)

    Es werden JSON-Dateien (eine Datei pro Stunde) erstellt.

    ![Untersuchen der Streamingdaten](./media/iot-accelerators-integrate-data-lake/data-lake-store-file-preview.png)

## <a name="next-steps"></a>Nächste Schritte

Azure Data Lake Analytics kann verwendet werden, um Big Data-Analysen für Ihre Data Lake Store-Datasets auszuführen. Weitere Informationen finden Sie in der [Dokumentation zu Data Lake Analytics](https://docs.microsoft.com/azure/data-lake-analytics).
