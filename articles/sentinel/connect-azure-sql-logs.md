---
title: Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank mit Azure Sentinel
description: Erfahren Sie, wie Sie Diagnose- und Sicherheitsüberwachungsprotokolle von Azure SQL-Datenbank mit Azure Sentinel verbinden.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807732"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Verbinden von Diagnose- und Überwachungsprotokollen von Azure SQL-Datenbank

Azure SQL ist eine vollständig verwaltete PaaS-Datenbank-Engine (Platform-as-a-Service), bei der die meisten Funktionen für die Datenbankverwaltung ohne Benutzereingriff erfolgen, z. B. Upgrades, Patches, Sicherungen und Überwachung. 

Mit dem Azure SQL-Datenbank-Connector können Sie die Überwachungs- und Diagnoseprotokolle Ihrer Datenbanken in Azure Sentinel streamen, sodass Sie die Aktivitäten in allen Instanzen kontinuierlich überwachen können.

- Das Verbinden von Diagnoseprotokollen ermöglicht Ihnen das Senden von Datenbankdiagnoseprotokollen mit unterschiedlichen Datentypen an Ihren Azure Sentinel-Arbeitsbereich.

- Durch das Verbinden von Überwachungsprotokollen können Sie Sicherheitsüberwachungsprotokolle aus allen Azure SQL-Datenbanken auf Serverebene streamen.

Weitere Informationen finden Sie unter [Überwachung in Azure SQL-Datenbanken](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Zum Verbinden von Überwachungsprotokollen müssen Sie über Lese- und Schreibberechtigungen für die Azure SQL Server-Überwachungs Einstellungen verfügen.

## <a name="connect-to-azure-sql-database"></a>Herstellen einer Verbindung mit Azure SQL-Datenbank
    
1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie im Katalog für Datenconnectors **Azure SQL-Datenbank** und dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Beachten Sie im Abschnitt **Konfiguration** der Connectorseite die beiden Kategorien von Protokollen, die Sie verbinden können.

### <a name="connect-diagnostics-logs"></a>Verbinden von Diagnoseprotokollen

1. Erweitern Sie unter **Diagnoseprotokolle** die Option **Diagnoseprotokolle für jede Ihrer Azure SQL-Datenbanken manuell aktivieren**.

1. Wählen Sie den Link **Azure SQL öffnen >** aus, um das Ressourcenblatt **Azure SQL** zu öffnen.

1. **(Optional)** Um die Datenbankressourcen auf einfache Weise zu finden, wählen Sie **Filter hinzufügen** oben auf der Filterleiste aus.
    1. Wählen Sie in der Dropdown Liste **Filter** die Option **Ressourcentyp** aus.
    1. Deaktivieren Sie in der Dropdownliste **Wert** die Option **Alle auswählen**, und wählen Sie dann **SQL-Datenbank** aus.
    1. Klicken Sie auf **Anwenden**.
    
1. Wählen Sie die Datenbankressource aus, deren Diagnoseprotokolle an Azure Sentinel gesendet werden sollen.

    > [!NOTE]
    > Sie müssen diesen Vorgang ab diesem Schritt für jede Datenbankressource wiederholen, deren Protokolle Sie erfassen möchten.

1. Wählen Sie auf der Ressourcenseite der Datenbank, die Sie ausgewählt haben, unter **Überwachung** im Navigationsmenü **Diagnoseeinstellungen** aus.

    1. Wählen Sie am Ende der Tabelle **+ Diagnoseeinstellung hinzufügen** aus.

    1. Geben Sie auf dem Bildschirm **Diagnoseeinstellung** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.
    
    1. Aktivieren Sie in der Spalte **Zieldetails** das Kontrollkästchen **An Log Analytics-Arbeitsbereich senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Azure Sentinel befindet) aus.

    1. Aktivieren Sie in der Spalte **Kategoriedetails** die Kontrollkästchen für die Protokoll- und Metriktypen, die Sie erfassen möchten. Es wird empfohlen, alle verfügbaren Typen unter **Protokoll** und **Metrik** auszuwählen.

    1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

- Alternativ können Sie das bereitgestellten **PowerShell-Skript** verwenden, um die Diagnoseprotokolle zu verbinden.
    1. Erweitern Sie unter **Diagnoseprotokolle** die Option **Durch PowerShell-Skript aktivieren**.

    1. Kopieren Sie den Codeblock, und fügen Sie ihn in PowerShell ein.

### <a name="connect-audit-logs"></a>Verbinden von Überwachungsprotokollen

1. Erweitern Sie unter **Überwachungsprotokolle (Vorschau)** die Option **Überwachungsprotokolle für alle Azure SQL-Datenbanken aktivieren (auf Serverebene)** .

1. Wählen Sie den Link **Azure SQL öffnen >** aus, um das Ressourcenblatt **SQL-Server** zu öffnen.

1. Wählen Sie den SQL-Server aus, dessen Überwachungsprotokolle an Azure Sentinel gesendet werden sollen.

    > [!NOTE]
    > Sie müssen diesen Vorgang ab diesem Schritt für jede Serverressource wiederholen, deren Protokolle Sie erfassen möchten.

1. Wählen Sie auf der Ressourcenseite des Servers, den Sie ausgewählt haben, unter **Sicherheit** im Navigationsmenü **Überwachung** aus.

    1. Verschieben Sie den Umschalter **Azure SQL-Überwachung aktivieren** auf **EIN**.

    1. Wählen Sie unter **Ziel des Überwachungsprotokolls** die Option **Log Analytics (Vorschau)** aus.
    
    1. Wählen Sie in der Liste der Arbeitsbereiche, die angezeigt wird, Ihren Arbeitsbereich aus (in dem sich Azure Sentinel befindet).

    1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

- Alternativ können Sie das bereitgestellten **PowerShell-Skript** verwenden, um die Diagnoseprotokolle zu verbinden.
    1. Erweitern Sie unter **Überwachungsprotokolle** die Option **Durch PowerShell-Skript aktivieren**.

    1. Kopieren Sie den Codeblock, und fügen Sie ihn in PowerShell ein.


> [!NOTE]
>
> Mit diesem speziellen Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten zwei Wochen erfasst wurden. Wenn zwei Wochen ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Diagnose- und Überwachungsprotokolle von Azure SQL-Datenbank mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).