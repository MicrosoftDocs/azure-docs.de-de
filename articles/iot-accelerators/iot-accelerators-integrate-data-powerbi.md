---
title: Visualisieren von Remoteüberwachungsdaten mit Power BI – Azure | Microsoft-Dokumentation
description: In diesem Tutorial werden Daten aus einer Remoteüberwachungslösung unter Verwendung von Power BI Desktop und Cosmos DB in eine benutzerdefinierte Visualisierung integriert. So können Benutzer ihre eigenen benutzerdefinierten Dashboards erstellen und sie mit Benutzern teilen, die die Lösung nicht verwenden.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61448130"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Visualisieren von Remoteüberwachungsdaten mit Power BI

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie die Daten Ihrer Remoteüberwachungslösung aus Cosmos DB in Power BI einbinden. Nachdem Sie diese Verbindung hergestellt haben, können Sie eigene benutzerdefinierte Dashboards erstellen und in das Dashboard Ihrer Remoteüberwachungslösung integrieren. Dieser Arbeitsstream ermöglicht die Erstellung spezialisierterer Diagramme (zusätzlich zu den vorkonfigurierten Diagrammen). Auf der Grundlage dieses Tutorials können Sie auch eine Integration für andere Datenströme einrichten oder benutzerdefinierte Dashboards zur Verwendung außerhalb Ihrer Remoteüberwachungslösung erstellen. Die Erstellung von Dashboards in Power BI bedeutet, dass die einzelnen Bereiche auch miteinander interagieren können, wenn Sie bestimmte Komponenten auswählen. Wenn Sie also beispielsweise einen Filter einrichten, der nur Informationen zu Ihren simulierten LKWs anzeigt, werden dank der Interaktion in den einzelnen Komponenten Ihres Dashboards nur Informationen zu simulierten LKWs angezeigt. Falls Sie anstelle von Power BI ein anderes Tool verwenden möchten, können Sie diese Schritte auch erweitern, um Ihr bevorzugtes Visualisierungstool zu verwenden und die Cosmos-Datenbank (oder eine benutzerdefinierte Datenbank, sofern vorhanden) einzubinden. 

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen eine aktive Remoteüberwachungslösung.
- Sie benötigen Zugriff auf das [Azure-Portal](https://portal.azure.com) und auf Ihr Abonnement, unter dem die IoT Hub-Instanz und die Lösung ausgeführt werden.
- Bei Ihnen muss eine beliebige Version von [Power BI Desktop](https://powerbi.microsoft.com) installiert sein.


## <a name="information-needed-from-azure-portal"></a>Erforderliche Informationen aus dem Azure-Portal

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich an (falls erforderlich).

2. Klicken Sie im linken Bereich auf „Ressourcengruppen“.

    ![Seitlicher Navigationsbereich](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Navigieren Sie zu der Ressourcengruppe, die Ihrer ausgeführten IoT-Lösung zugrunde liegt, und klicken Sie darauf, um zur Übersichtsseite der Ressourcengruppe zu gelangen. 

4. Klicken Sie auf der Übersichtsseite auf das Element vom Typ „Azure Cosmos DB-Konto“, um zur Übersichtsseite des Cosmos DB-Datenstroms für die betreffende IoT-Lösung zu gelangen.

    ![Ressourcengruppe](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Klicken Sie im linken Bereich auf den Abschnitt „Schlüssel“, und notieren Sie sich die folgenden Werte zur späteren Verwendung in Power BI:

   - URI
   - Primärschlüssel

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Einrichten des Datenstroms in Power BI
  
1. Öffnen Sie die Power BI-Desktop-App, und klicken Sie in der linken oberen Ecke auf „Daten abrufen“. 

    ![Abrufen von Daten](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Wenn Sie zur Eingabe von Daten aufgefordert werden, suchen Sie nach „Azure Cosmos DB“, und wählen Sie den entsprechenden Connector aus. Dieser Connector pullt im Wesentlichen Daten direkt aus der Cosmos-Datenbank Ihrer Azure IoT-Lösung.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Geben Sie die weiter oben notierten Informationen ein:

    * URI
    * Primärschlüssel

4. Wählen Sie alle Tabellen aus, die in Power BI importiert werden sollen. Dadurch wird das Laden der Daten initiiert. Das Laden der Daten kann abhängig davon, wie lange Ihre Lösung bereits aktiv ist, einige Stunden dauern. 

    ![Importieren von Tabellen](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Klicken Sie nach Abschluss des Ladevorgangs in der obersten Zeile von Power BI auf „Abfragen bearbeiten“, und erweitern Sie alle Tabellen, indem Sie auf der gelben Leiste der einzelnen Tabellen jeweils auf den Pfeil klicken. So zeigen Sie im Wesentlichen alle Spalten an. Wie Sie sehen, haben Daten für Aspekte wie Luftfeuchtigkeit, Geschwindigkeit, Zeit usw. nicht den richtigen Datentyp.

    ![Neue Spalte](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Beispielsweise wurden die Daten, die über den Connector bei Power BI eingehen, in UNIX-Zeit konvertiert. Auf diese Konvertierung können Sie reagieren, indem Sie eine neue Spalte erstellen und die Daten mithilfe der folgenden Formel in das Datums-/Uhrzeitformat umwandeln: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Aktualisierte Tabelle](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    „Document.device.msg.received“ ist nur eine der Spalten mit UNIX-Formatierung und kann durch andere ersetzt werden, bei denen eine Konvertierung erforderlich ist. 
  
    Andere Datenpunkte wurden in den Datentyp „String“ konvertiert und können mithilfe der oben erläuterten Schritte ggf. in Werte vom Typ „Double“ oder „Int“ geändert werden.

## <a name="creating-a-dashboard"></a>Erstellen eines Dashboards

Nach der Verbindungsherstellung mit dem Datenstrom können Sie Ihre personalisierten Dashboards erstellen. Im weiter unten dargestellten Beispieldashboard werden auf der Grundlage der Telemetriedaten unserer simulierten Geräte unter anderem folgende Pivots angezeigt: 

* Gerätestandort auf einer Karte (rechts)
* Geräte mit Status und Schweregrad (links oben)
* Geräte mit Regeln und Warnungsstatus (links unten)

![Power BI-Visualisierung](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Veröffentlichen des Dashboards und Aktualisieren der Daten

Erfolgreich erstellte Dashboards sollten [veröffentlicht](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) werden, um sie mit anderen zu teilen.

Darüber hinaus sollten Sie die [Daten für das veröffentlichte Dashboard aktualisieren](https://docs.microsoft.com/power-bi/refresh-data), um sicherzustellen, dass Sie über das neueste Dataset verfügen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Remoteüberwachungsdaten mithilfe von Power BI visualisieren.

Weitere Informationen zum Anpassen der Remoteüberwachungslösung finden Sie hier:

* [Anpassen der vorkonfigurierten Remoteüberwachungslösung](iot-accelerators-remote-monitoring-customize.md)
* [Referenzhandbuch für Entwickler](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Handbuch zur Problembehandlung für Entwickler](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

