---
title: Aktivieren von Synapse-Arbeitsbereichsfunktionen in einem dedizierten SQL-Pool (vormals SQL DW)
description: In diesem Dokument wird beschrieben, wie ein Kunde auf seine vorhandene eigenständige SQL DW-Instanz im Arbeitsbereich zugreifen und diese verwenden kann.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 5efb1df378df323585bc0ca1094451cdb095fe4e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499780"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Aktivieren von Synapse-Arbeitsbereichsfunktionen in einem vorhandenen dedizierten SQL-Pool (vormals SQL DW)

Alle SQL Data Warehouse-Kunden können jetzt auf die Instanz eines vorhandenen dedizierten SQL-Pools (vormals SQL DW) über Synapse Studio und den zugehörigen Arbeitsbereich zugreifen, ohne dass die Automatisierung, die Verbindungen oder die Tools beeinträchtigt werden. In diesem Artikel wird beschrieben, wie ein vorhandener Azure Synapse Analytics-Kunde seine vorhandene Analytics-Lösung erstellen und erweitern kann, indem er die neuen umfangreichen Funktionen nutzt, die jetzt über den Synapse-Arbeitsbereich und Synapse Studio verfügbar sind.   

## <a name="experience"></a>Erfahrung
 
Nachdem der Synapse-Arbeitsbereich nun die Phase der allgemeinen Verfügbarkeit erreicht hat, ist im Übersichtsbereich des DW-Portals eine neue Funktion vorhanden. Sie ermöglicht Ihnen das Erstellen eines Synapse-Arbeitsbereichs für Ihre vorhandenen Instanzen des dedizierten SQL-Pools (vormals SQL DW). Mit dieser neuen Funktion können Sie für den logischen Server, der Ihre vorhandenen Data Warehouse-Instanzen hostet, eine Verbindung mit einem neuen Synapse-Arbeitsbereich herstellen. Mit der Verbindung wird sichergestellt, dass alle auf diesem Server gehosteten Data Warehouses über den Arbeitsbereich und Studio verfügbar gemacht werden und zusammen mit den Synapse-Partnerdiensten (serverloser SQL-Pool, Apache Spark-Pool und ADF) genutzt werden können. Sie können auf Ihre Ressourcen zugreifen und diese verwenden, sobald die Bereitstellungsschritte abgeschlossen wurden und die Verbindung mit dem neu erstellten Arbeitsbereich hergestellt wurde.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Verbundener Synapse-Arbeitsbereich":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Verwenden der Funktionen des Synapse-Arbeitsbereichs und von Studio zum Zugreifen auf und Verwenden eines dedizierten SQL-Pools (vormals SQL DW)
 
Die folgenden Informationen gelten, wenn Sie eine Instanz eines dedizierten SQL DW (vormals SQL DW) mit aktivierten Synapse-Arbeitsbereichsfunktionen verwenden: 
- **SQL-Funktionen** Alle SQL-Funktionen verbleiben auf dem logischen SQL-Server, nachdem die Synapse-Arbeitsbereichsfunktion aktiviert wurde. Der Zugriff auf den Server über den SQL-Ressourcenanbieter ist weiterhin möglich, nachdem der Arbeitsbereich aktiviert wurde. Alle Verwaltungsfunktionen können über den Arbeitsbereich initiiert werden, und der Vorgang wird auf der logischen SQL Server-Instanz durchgeführt, auf der Ihre SQL-Pools gehostet werden. Vorhandene Automatisierungen, Tools oder Verbindungen werden nicht beschädigt oder unterbrochen, wenn ein Arbeitsbereich aktiviert wird.  
- **Ressourcenverschiebung** Wenn auf einem Server mit aktivierter Synapse-Arbeitsbereichsfunktion eine Ressourcenverschiebung initiiert wird, wird die Verknüpfung des Servers mit dem Arbeitsbereich getrennt. Dies führt dazu, dass Sie aus dem Arbeitsbereich nicht mehr auf Ihre Instanzen des vorhandenen dedizierten SQL-Pools (vormals SQL DW) zugreifen können. Wir empfehlen Ihnen, beide Ressourcen unter demselben Abonnement und in derselben Ressourcengruppe anzuordnen, um sicherzustellen, dass die Verbindung erhalten bleibt. 
- **Überwachung** SQL-Anforderungen, die über Synapse Studio in einem für den Arbeitsbereich aktivierten dedizierten SQL-Pool (vormals SQL DW) übermittelt werden, können im Überwachungshub angezeigt werden. Für alle anderen Überwachungsaktivitäten können Sie die Überwachung von dedizierten SQL-Pools (vormals SQL DW) im Azure-Portal verwenden. 
- **Sicherheit** und **Zugriffssteuerungen** Wie oben erwähnt, sind alle Verwaltungsfunktionen für Ihre SQL Server-Instanz und die Instanzen der dedizierten SQL-Pools (vormals SQL DW) weiterhin auf dem logischen SQL-Server vorhanden. Zu diesen Funktionen gehören die Verwaltung von Firewallregeln, die Einrichtung des Azure AD-Administrators des Servers und alle Zugriffssteuerungen für die Daten in Ihrem dedizierten SQL-Pool (vormals SQL DW). Die folgenden Schritte müssen ausgeführt werden, um sicherzustellen, dass Ihr dedizierter SQL-Pool (vormals SQL DW) verfügbar ist und über den Synapse-Arbeitsbereich verwendet werden kann. Mit der Zuweisung von Arbeitsbereichsrollen erhalten Benutzer keine Berechtigungen für die Daten der Instanzen des dedizierten SQL-Pools (vormals SQL DW). Halten Sie sich an Ihre üblichen Richtlinien für die [SQL-Authentifizierung](sql-data-warehouse-authentication.md), um sicherzustellen, dass Benutzer auf die Instanzen des dedizierten SQL-Pools (vormals SQL DW) auf dem logischen Server zugreifen können. 

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > Im verbundenen Arbeitsbereich von Synapse Studio werden die Namen von dedizierten Pools basierend auf den Berechtigungen angezeigt, über die der Benutzer in Azure verfügt. Auf die Objekte der Pools kann nicht zugegriffen werden, wenn der Benutzer nicht über Berechtigungen für die SQL-Pools verfügt. 

- **Netzwerksicherheit** Gehen Sie wie folgt vor, wenn für den Synapse-Arbeitsbereich, den Sie in Ihrem vorhandenen dedizierten SQL-Pool (vormals SQL DW) aktiviert haben, der Schutz vor Dateninfiltration aktiviert ist: Erstellen Sie über den verwalteten privaten Endpunkt eine Verbindung vom Arbeitsbereich zum logischen SQL-Server. Genehmigen Sie die Anforderung einer Verbindung über den privaten Endpunkt, um die Kommunikation zwischen dem Server und dem Arbeitsbereich zuzulassen.
- **Studio** SQL-Pools im **Datenhub** Ein für Arbeitsbereiche aktivierter dedizierter SQL-Pool (vormals SQL DW) kann über die QuickInfo im Datenhub identifiziert werden. 
- **Erstellen eines neuen dedizierten SQL-Pools (vormals SQL DW)** Neue dedizierte SQL-Pools können über den Synapse-Arbeitsbereich und Studio erstellt werden, nachdem die Arbeitsbereichsfunktion aktiviert wurde. Die Bereitstellung eines neuen Pools erfolgt auf dem logischen SQL-Server. Die neuen Ressourcen werden im Portal und in Studio angezeigt, nachdem die Bereitstellung abgeschlossen ist.      

## <a name="next-steps"></a>Nächste Schritte
Aktivieren Sie die [Synapse-Arbeitsbereichsfunktionen](workspace-connected-create.md) für Ihren vorhandenen dedizierten SQL-Pool (vormals SQL DW).
