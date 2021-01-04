---
title: Einrichten von Warnungen und Benachrichtigungen für eine verwaltete Instanz (Azure-Portal)
description: Verwenden Sie das Azure-Portal, um Warnungen für eine verwaltete SQL-Datenbank-Instanz zu erstellen, die Benachrichtigungen oder eine Automatisierung auslösen, wenn die angegebenen Bedingungen erfüllt sind.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 05/04/2020
ms.openlocfilehash: a85bf6510163c47fa39bdb28debff806e1812f58
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499270"
---
# <a name="create-alerts-for-azure-sql-managed-instance-using-the-azure-portal"></a>Erstellen von Warnungen für eine verwaltete Azure SQL-Datenbank-Instanz im Azure-Portal
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In diesem Artikel erfahren Sie, wie Sie im Azure-Portal Warnungen für Datenbank in einer verwaltete Azure SQL-Datenbank-Instanz einrichten können. Warnungen können Ihnen eine E-Mail senden, einen Webhook aufrufen, eine Azure-Funktion oder ein Runbook ausführen, ein externes ITSM-kompatibles Ticketsystem aufrufen, Sie auf dem Telefon anrufen oder eine SMS senden, wenn eine Metrik (z. B. die Instanzspeichergröße oder die CPU-Auslastung) einen vordefinierten Schwellenwert erreicht. Dieser Artikel nennt auch bewährte Methoden für das Festlegen von Warnungszeiträumen.


## <a name="overview"></a>Übersicht

Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services oder von Ereignissen, die bei diesen auftreten, eine Warnung empfangen.

* **Metrikwerte** : Die Warnung wird ausgelöst, wenn der Wert einer angegebenen Metrik einen von Ihnen festgelegten Schwellenwert in beliebiger Richtung überschreitet. Das Auslösen erfolgt sowohl, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird.

Sie können konfigurieren, dass bei einer Warnung Folgendes erfolgt, wenn sie ausgelöst wird:

* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mails an weitere von Ihnen angegebene Adressen
* Anrufen einer Telefonnummer mit Sprachanweisung
* Senden einer SMS an eine Telefonnummer
* Aufrufen eines Webhooks
* Aufrufen einer Azure-Funktion
* Aufrufen eines Azure-Runbooks
* Aufrufen eines externem ITSM-kompatiblen Ticketsystems

Sie können Informationen zu Warnungsregeln mithilfe des [Azure-Portals, PowerShell oder der Azure CLI](../../azure-monitor/platform/alerts-classic-portal.md) oder der [Azure Monitor REST-API](/rest/api/monitor/alertrules) konfigurieren und abrufen. 

## <a name="alerting-metrics-available-for-managed-instance"></a>Für verwaltete Instanz verfügbare Warnungsmetriken

> [!IMPORTANT]
> Warnungs Metriken sind nur für eine verwaltete Instanz verfügbar. Warnungsmetriken für einzelne Datenbanken in einer verwalteten Instanz sind nicht verfügbar. Datenbankdiagnosetelemetrie steht jedoch in Form von [Diagnoseprotokollen](../database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export) zur Verfügung. Warnungen zu Diagnoseprotokollen können in [SQL Analytics](../../azure-monitor/insights/azure-sql.md) mit [Protokollwarnungsskripts](../../azure-monitor/insights/azure-sql.md#creating-alerts-for-sql-managed-instance) für eine verwaltete Instanz eingerichtet werden.

Die folgenden Metriken für verwaltete Instanzen sind für eine Warnungskonfiguration verfügbar:

| Metrik | BESCHREIBUNG | Maßeinheit/mögliche Werte |
| :--------- | --------------------- | ----------- |
| Durchschnittlicher CPU-Prozentsatz | Durchschnittlicher Prozentsatz der CPU-Auslastung im ausgewählten Zeitraum. | 0 bis 100 (Prozent) |
| Gelesene E/A-Bytes | E/A-Bytes, die im ausgewählten Zeitraum gelesen wurden. | Byte |
| Geschriebene E/A-Bytes | E/A-Bytes, die im ausgewählten Zeitraum geschrieben wurden. | Byte |
| Anzahl von E/A-Anforderungen | Anzahl der E/A-Anforderungen im ausgewählten Zeitraum. | Numerisch |
| Reservierter Speicherplatz | Aktueller maximaler Speicherplatz, der für die verwaltete Instanz reserviert ist. Änderungen beim Ressourcenskalierungsvorgang. | MB (Megabyte) |
| Belegter Speicherplatz | Im ausgewählten Zeitraum verwendeter Speicherplatz. Änderungen mit Speicherverbrauch durch Datenbanken und die Instanz. | MB (Megabyte) |
| Anzahl virtueller Kerne | Virtuelle Kerne, die für die verwaltete Instanz bereitgestellt werden. Änderungen beim Ressourcenskalierungsvorgang. | 4 bis 80 (virtuelle Kerne) |

## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Erstellen einer Warnungsregel anhand einer Metrik mit dem Azure-Portal

1. Suchen Sie im Azure-[Portal](https://portal.azure.com/) die verwaltete Instanz, die Sie überwachen möchten, und wählen Sie sie aus.

2. Wählen Sie das Menüelement **Metriken** im Abschnitt „Überwachung“ aus.

   ![Überwachung](./media/alerts-create/mi-alerting-menu-annotated.png)
  
3. Wählen Sie im Dropdownmenü eine der Metriken aus, für die Sie Ihre Warnung einrichten möchten (im Beispiel wird der verwendete Speicherplatz gezeigt).

4. Wählen Sie den Aggregationszeitraum (Durchschnitt, Minimum oder erreichtes Maximum) im angegebenen Zeitraum aus (Avg, Min oder Max). 

5. Wählen Sie **Neue Warnungsregel** aus.

6. Klicken Sie im Bereich „Warnungsregel erstellen“ auf **Bedingungsname** (im Beispiel wird der verwendete Speicherplatz gezeigt).

   ![Definieren der Bedingung](./media/alerts-create/mi-create-metrics-alert-smaller-annotated.png)

7. Definieren Sie im Bereich „Signallogik konfigurieren“ den Operator, den Aggregationstyp und den Schwellenwert.

   * Die Optionen für den Operatortyp sind größer als, gleich und kleiner als (der Schwellenwert).
   * Optionen für den Aggregationstyp sind Minimum, Maximum oder Durchschnitt (im Aggregationsgranularitätszeitraum).
   * Der Schwellenwert ist der Warnungswert, der basierend auf dem Operator und den Aggregationskriterien ausgewertet wird.
   
   ![Configure_signal_logic](./media/alerts-create/mi-configure-signal-logic-annotated.png)
   
   Das im Screenshot gezeigte Beispiel verwendet den Wert von 1.840.876 MB, der einen Schwellenwert von 1,8 TB darstellt. Da der Operator im Beispiel auf „größer als“ festgelegt ist, wird die Warnung erstellt, wenn die Speicherplatzbelegung für die verwaltete Instanz 1,8 TB überschreitet. Beachten Sie, dass der Schwellenwert für die Speicherplatzmetriken in MB ausgedrückt werden muss.

8. Legen Sie den Auswertungszeitraum fest: Aggregationsgranularität in Minuten und Häufigkeit der Auswertung. Die Häufigkeit der Auswertung gibt an, wie lange das Warnungssystem regelmäßig prüft, ob die Schwellenwertbedingung erfüllt ist.

9. Wählen Sie die Aktionsgruppe aus. Der Bereich „Aktionsgruppe“ wird angezeigt, in dem Sie eine vorhandene Aktion auswählen oder eine neue Aktion erstellen können. Diese Aktion definiert, was beim Auslösen einer Warnung geschieht (z. B. Senden einer E-Mail, Anruf bei einer Telefonnummer, Ausführen eines Webhooks, einer Azure-Funktion oder eines Runbooks).

   ![Select_action_group](./media/alerts-create/mi-select-action-group-smaller-annotated.png)

   * Wählen Sie zum Erstellen einer neuen Aktionsgruppe **+ Aktionsgruppe erstellen** aus.

      ![Create_action_group_alerts](./media/alerts-create/mi-create-alert-action-group-smaller-annotated.png)
   
   * Legen Sie fest, wie Sie benachrichtigt werden möchten: Geben Sie den Aktionsgruppennamen, Kurznamen und Aktionsnamen ein, und wählen Sie den Aktionstyp aus. Der Aktionstyp definiert, ob Sie über E-Mail, SMS oder Sprachanruf benachrichtigt werden oder ob möglicherweise ein Webhook, eine Azure-Funktion oder ein Runbook ausgeführt oder ein ITSM-Ticket in Ihrem kompatiblen System erstellt wird.

      ![Define_how_to_be_alerted](./media/alerts-create/mi-add-alerts-action-group-annotated.png)

10. Geben Sie die Warnungsregeldetails für Ihre Datensätze ein, und wählen Sie den Schweregrad aus.

      ![Rule_description](./media/alerts-create/mi-rule-details-complete-smaller-annotated.png)

   * Schließen Sie das Erstellen der Warnungsregel ab, indem Sie auf die Schaltfläche **Warnungsregel erstellen** klicken.

Die neue Warnungsregeln werden innerhalb weniger Minuten aktiv und basierend auf Ihren Einstellungen ausgelöst.

## <a name="verifying-alerts"></a>Überprüfen von Warnungen

> [!NOTE]
> Informationen zum Unterdrücken von störenden Warnungen finden Sie unter [Unterdrücken von Warnungen mit Aktionsregeln](../../azure-monitor/platform/alerts-action-rules.md#suppression-of-alerts).

Vergewissern Sie sich beim Einrichten einer Warnungsregel, dass Sie mit dem Warnungsauslöser und der -häufigkeit zufrieden sind. Für das auf dieser Seite gezeigte Beispiel zum Einrichten einer Warnung zum verwendeten Speicherplatz können Sie eine E-Mail wie die unten gezeigte erhalten, wenn die Warnungsoption E-Mail lautet.

   ![alert_example](./media/alerts-create/mi-email-alert-example-smaller-annotated.png)

Die E-Mail zeigt den Namen der Warnung, Details des Schwellenwerts und den Grund für die Auslösung der Warnung und unterstützt Sie so beim Überprüfen und Beheben der Warnung. Mithilfe der Schaltfläche **Im Azure-Portal anzeigen** können Sie die über E-Mail empfangene Warnung im Azure-Portal anzeigen. 

## <a name="view-suspend-activate-modify-and-delete-existing-alert-rules"></a>Anzeigen, Anhalten, Aktivieren, Ändern und Löschen vorhandener Warnungsregeln

> [!NOTE]
> Vorhandene Warnungen müssen über das Dashboard im Azure-Portal über das Menü „Warnungen“ verwaltet werden. Vorhandene Warnungen können nicht über das Ressourcenblatt „Verwaltete Instanz“ geändert werden.

Anzeigen, Anhalten, Aktivieren, Ändern und Löschen vorhandener Warnungen:

1. Suchen Sie mit der Suchfunktion im Azure-Portal nach Warnungen. Klicken Sie auf „Warnungen“.

   ![find_alerts](./media/alerts-create/mi-manage-alerts-browse-smaller-annotated.png)

   Alternativ dazu können Sie auch auf der Navigationsleiste von Azure auf „Warnungen“ klicken, wenn Sie dies konfiguriert haben.

2. Wählen Sie im Bereich „Warnungen“ die Option „Warnungsregeln verwalten“ aus.

   ![modify_alerts](./media/alerts-create/mi-manage-alert-rules-smaller-annotated.png)

   Die Liste vorhandener Warnungen wird angezeigt. Wählen Sie eine einzelne vorhandene Warnungsregel aus, um sie zu verwalten. Vorhandene aktive Regeln können geändert und auf Ihre bevorzugte Einstellung abgestimmt werden. Aktive Regeln können auch angehalten werden, ohne gelöscht zu werden. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Azure Monitor-Warnungssystem finden Sie unter [Übersicht über Warnungen in Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).
* Weitere Informationen zu Metrikwarnungen finden Sie unter [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md).
* Weitere Informationen zum Konfigurieren eines Webhooks in Warnungen finden Sie unter [Aufrufen eines Webhooks mit einer klassischen Metrikwarnung](../../azure-monitor/platform/alerts-webhooks.md).
* Weitere Informationen zum Konfigurieren und Verwalten von Warnungen mithilfe von PowerShell finden Sie unter [Aktionsregeln](/powershell/module/az.monitor/add-azmetricalertrulev2).
* Weitere Informationen zum Konfigurieren und Verwalten von Warnungen mithilfe der API finden Sie unter [Azure Monitor-REST-API-Referenz](/rest/api/monitor/).