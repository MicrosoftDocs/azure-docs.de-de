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
ms.openlocfilehash: fd3c8a08e5512d15be4dfb26ca3eff151d08386f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651361"
---
# <a name="use-azure-sentinel-watchlists"></a>Verwenden von Azure Sentinel-Watchlists

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
    
## <a name="use-watchlists-in-analytics-rules"></a>Verwenden von Watchlists in Analyseregeln

Um Watchlists in Analyseregeln zu verwenden, navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Konfiguration** > **Analysen**, und erstellen Sie eine Regel mithilfe der Funktion `_GetWatchlist('<watchlist>')` in der Abfrage.

:::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="Verwenden von Watchlists in Analyseregeln" lightbox="./media/watchlists/sentinel-watchlist-analytics-rule.png":::

## <a name="view-list-of-watchlists-aliases"></a>Anzeigen der Liste von Watchlist-Aliasen

Um eine Liste von Watchlist-Aliasen abzurufen, navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Allgemein** > **Protokolle**, und führen Sie die Abfrage `_GetWatchlistAlias` aus. Die Liste der Aliase wird auf der Registerkarte **Ergebnisse** angezeigt.

> [!div class="mx-imgBorder"]
> ![Auflisten von Watchlists](./media/watchlists/sentinel-watchlist-alias.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Watchlists in Azure Sentinel verwenden, um Daten zu erweitern und Untersuchungen zu verbessern. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.