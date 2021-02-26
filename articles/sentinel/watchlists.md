---
title: Verwenden von Azure Sentinel-Watchlists
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Azure Sentinel-Watchlists Bedrohungen untersuchen, Geschäftsdaten importieren, Zulassungslisten erstellen und Ereignisdaten erweitern können.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/06/2020
ms.openlocfilehash: 97509b878fb5e0cb28bddc5d1b58c21b32c34675
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99555645"
---
# <a name="use-azure-sentinel-watchlists"></a>Verwenden von Azure Sentinel-Watchlists

> [!IMPORTANT]
> Das Feature „Watchlists“ befindet sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Azure Sentinel-Watchlists ermöglichen die Erfassung von Daten aus externen Datenquellen, um sie mit Ereignissen in Ihrer Azure Sentinel-Umgebung zu korrelieren. Nach der Erstellung können Sie Watchlists in Ihrer Suche, für Erkennungsregeln, bei der Bedrohungssuche sowie in Playbooks für die Reaktion auf Bedrohungen verwenden. Watchlists werden in Ihrem Azure Sentinel-Arbeitsbereich als Name-Wert-Paare gespeichert und für eine optimale Abfrageleistung und geringe Latenz zwischengespeichert.

Häufige Szenarien für die Verwendung von Watchlists umfassen Folgendes:

- **Untersuchen von Bedrohungen** und schnelles Reagieren auf Incidents, indem IP-Adressen, Dateihashes und anderen Daten schnell aus CSV-Dateien importiert werden. Nach dem Importieren können Sie Name-Wert-Paare der Watchlist zum Verknüpfen und Filtern in Warnungsregeln, bei der Bedrohungssuche, in Arbeitsmappen, in Notebooks und für allgemeine Abfragen verwenden.

- **Importieren von Geschäftsdaten** als Watchliste. Importieren Sie z. B. Benutzerlisten mit privilegiertem Systemzugriff oder ehemaligen Mitarbeitern, und verwenden Sie dann die Watchlist, um Zulassungs- und Verweigerungslisten zu erstellen, mit denen eine Anmeldung dieser Benutzer beim Netzwerk ermittelt oder verhindert wird.

- **Reduzieren von Warnungsmüdigkeit**. Erstellen Sie Zulassungslisten, um Warnungen von einer Gruppe von Benutzern zu unterdrücken (z. B. Benutzer von autorisierten IP-Adressen, die Aufgaben ausführen, durch die normalerweise die Warnung ausgelöst wird) und zu verhindern, dass legitime Ereignisse zu Warnungen führen.

- **Erweitern von Ereignisdaten**. Verwenden Sie Watchlists, um Ihre Ereignisdaten mit Name-Wert-Kombinationen zu erweitern, die aus externen Datenquellen stammen.

## <a name="create-a-new-watchlist"></a>Erstellen einer neuen Watchlist

1. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Konfiguration** > **Watchlist**, und wählen Sie dann **Neu hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Neue Watchlist](./media/watchlists/sentinel-watchlist-new.png)

1. Geben Sie auf der Seite **Allgemein** den Namen, die Beschreibung und den Alias für die Watchlist an, und wählen Sie dann **Weiter** aus.

    > [!div class="mx-imgBorder"]
    > ![Seite „Allgemein“ für die Watchlist](./media/watchlists/sentinel-watchlist-general.png)

1. Wählen Sie auf der Seite **Quelle** den Datasettyp aus, laden Sie eine Datei hoch, und wählen Sie dann **Weiter** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="Seite „Quelle“ für die Watchlist" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Dateiuploads sind aktuell auf Dateien mit einer Größe von bis zu 3,8 MB beschränkt.

1. Sehen Sie sich die Informationen an, überprüfen Sie, ob sie korrekt sind, und wählen Sie dann **Erstellen** aus.

    > [!div class="mx-imgBorder"]
    > ![Überprüfungsseite für die Watchlist](./media/watchlists/sentinel-watchlist-review.png)

    Sobald die Watchlist erstellt wurde, wird eine Benachrichtigung angezeigt.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="Benachrichtigung über erfolgreiche Erstellung der Watchlist" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::

## <a name="use-watchlists-in-queries"></a>Verwenden von Watchlists in Abfragen

1. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Konfiguration** > **Watchlist**, wählen Sie die gewünschte Watchlist aus, und wählen Sie dann **In Log Analytics anzeigen** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="Verwenden von Watchlists in Abfragen" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png":::

1. Die Elemente in Ihrer Watchlist werden automatisch für Ihre Abfrage extrahiert und auf der Registerkarte **Ergebnisse** angezeigt. Das folgende Beispiel zeigt die Ergebnisse der Extraktion für die Felder **ServerName** und **IpAddress**.

    > [!NOTE]
    > Der Zeitstempel Ihrer Abfragen wird sowohl auf der Benutzeroberfläche der Abfrage als auch in geplanten Benachrichtigungen ignoriert.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="Abfragen mit Feldern der Watchlist" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::
    
1. Sie können für die Daten einer Tabelle eine Abfrage für die Daten aus einer Watchlist durchführen, indem Sie die Watchlist für Verknüpfungs- und Suchvorgänge wie eine Tabelle behandeln.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('IPlist') 
     on $left.ComputerIP == $right.IPAddress
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="Abfragen für Watchlist zu Suchzwecken":::

## <a name="use-watchlists-in-analytics-rules"></a>Verwenden von Watchlists in Analyseregeln

Um Watchlists in Analyseregeln zu verwenden, navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Konfiguration** > **Analysen**, und erstellen Sie eine Regel mithilfe der Funktion `_GetWatchlist('<watchlist>')` in der Abfrage.

1. In diesem Beispiel erstellen Sie eine Watchlist mit dem Namen „ipwatchlist“, die die folgenden Werte enthält:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="Liste mit vier Elementen für die Watchlist":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-2.png" alt-text="Erstellen einer Watchlist mit vier Elementen":::

1. Als Nächstes erstellen Sie die Analyseregel.  In diesem Beispiel fügen wir nur Ereignisse von IP-Adressen in die Watchlist ein:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project IPAddress)
    )
    ```

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule-2.png" alt-text="Verwenden von Watchlists in Analyseregeln":::

## <a name="view-list-of-watchlists-aliases"></a>Anzeigen der Liste von Watchlist-Aliasen

Um eine Liste von Watchlist-Aliasen abzurufen, navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Allgemein** > **Protokolle**, und führen Sie die Abfrage `_GetWatchlistAlias` aus. Die Liste der Aliase wird auf der Registerkarte **Ergebnisse** angezeigt.

> [!div class="mx-imgBorder"]
> ![Auflisten von Watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Watchlists in Azure Sentinel verwenden, um Daten zu erweitern und Untersuchungen zu verbessern. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.
