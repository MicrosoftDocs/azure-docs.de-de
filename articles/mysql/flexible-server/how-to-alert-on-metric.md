---
title: Konfigurieren von Warnungen zu Metriken – Azure-Portal – Azure Database for MySQL – Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal die Warnungen zu Metriken für Azure Database for MySQL Flexible Server konfigurieren und auf diese zugreifen.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 4a099a9850289a046435b4e1763d7f54a702c0d0
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545088"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Verwenden des Azure-Portals zum Einrichten von Warnungen zu Metriken für Azure Database for MySQL – Flexible Server 

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In diesem Artikel erfahren Sie, wie Sie mit dem Azure-Portal Azure Database for MySQL-Warnungen einrichten können. Sie können auf der Grundlage von Überwachungsmetriken für Ihre Azure-Services eine Warnung empfangen.

Die Warnung wird ausgelöst, wenn der Wert für eine bestimmte Metrik einen von Ihnen definierten Schwellenwert überschreitet. Die Warnung wird sowohl ausgelöst, wenn die Bedingung erstmals erfüllt wird, als auch danach, wenn diese Bedingung nicht mehr erfüllt wird. Metrikwarnungen sind zustandsbehaftet. Sie senden Benachrichtigungen nur dann, wenn sich der Zustand ändert.

Sie können konfigurieren, dass bei einer Warnung die folgenden Aktionen ausgeführt werden, wenn sie ausgelöst wird:
* Senden von E-Mail-Benachrichtigungen an den Dienstadministrator und Co-Administratoren
* Senden von E-Mails an weitere von Ihnen angegebene Adressen
* Aufrufen eines Webhooks

Sie haben folgende Möglichkeiten zum Konfigurieren von Warnungsregeln und Abrufen zugehöriger Informationen:
* [Azure portal](../../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [Azure-Befehlszeilenschnittstelle](../../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [Azure Monitor-REST-API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Erstellen einer Warnungsregel anhand einer Metrik aus dem Azure-Portal
1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) den zu überwachenden Azure Database for MySQL Flexible Server aus.
2. Wählen Sie auf der Seitenleiste im Abschnitt **Überwachung** die Option **Warnungen** aus.
3. Wählen Sie **+ Neue Warnungsregel** aus.
4. Die Seite **Regel erstellen** wird geöffnet. Füllen Sie die erforderlichen Informationen aus:
5. Wählen Sie im Abschnitt **Bedingung** die Option **Bedingung auswählen** aus.
6. Es wird eine Liste der unterstützten Signale angezeigt. Wählen Sie die Metrik aus, für die Sie eine Warnung erstellen möchten. Wählen Sie z. B. „Speicher in Prozent“ aus.
7. Es wird ein Diagramm für die Metrik für die letzten sechs Stunden angezeigt. Verwenden Sie die Dropdownliste **Diagrammzeitraum** , um einen längeren Verlauf für die Metrik anzuzeigen.
8. Wählen Sie den Typ **Schwellenwert** (z. B. „Statisch“ oder „Dynamisch“), **Operator** (z. B. „Größer als“) und **Aggregationstyp** (z. B. Durchschnitt) aus. Dadurch wird die Logik bestimmt, die die Metrikwarnungsregel auswerten soll.
    - Wenn Sie einen Schwellenwert vom Typ **Statisch** verwenden, legen Sie auch einen **Schwellenwert** fest (z. B. 85 Prozent). Anhand des Metrikdiagramms können Sie einen angemessenen Schwellenwert ermitteln.
    - Wenn Sie einen Schwellenwert vom Typ **Dynamisch** verwenden, legen Sie auch die **Schwellenwertempfindlichkeit** fest. Das Metrikdiagramm zeigt die berechneten Schwellenwerte basierend auf aktuellen Daten an. [Erfahren Sie mehr über den Bedingungstyp „Dynamische Schwellenwerte“ und Empfindlichkeitsoptionen.](../../azure-monitor/platform/alerts-dynamic-thresholds.md)
9. Optimieren Sie die Bedingung durch Anpassung des Intervalls **Aggregationsgranularität (Periode)** über das Datenpunkte mithilfe der Aggregationstypfunktion gruppiert werden (z. B. „30 Minuten“) und **Frequenz** (z. B. „Alle 15 Minuten“).
10. Klicken Sie auf **Fertig** .
11. Fügen Sie eine Aktionsgruppe hinzu. Eine Aktionsgruppe ist eine Sammlung von Benachrichtigungseinstellungen, die vom Besitzer eines Azure-Abonnements definiert wurden. Wählen Sie innerhalb des Abschnitts **Aktionsgruppen** die Option **Aktionsgruppe auswählen** aus, um eine bereits bestehende Aktionsgruppe auszuwählen, die der Warnungsregel zugeordnet werden soll.
12. Sie können auch eine neue Aktionsgruppe erstellen, um Benachrichtigungen über die Warnung zu erhalten. Weitere Informationen finden Sie unter [Erstellen und Verwalten von Aktionsgruppen](../../azure-monitor/platform/action-groups.md).
13. Um eine neue Aktionsgruppe zu erstellen, wählen Sie **+ Aktionsgruppe erstellen** aus. Füllen Sie das Formular „Aktionsgruppe erstellen“ mit **Abonnement** , **Ressourcengruppe** , **Aktionsgruppenname** und **Anzeigename** aus.
14. Konfigurieren Sie **Benachrichtigungen** für die Aktionsgruppe.
    
    Wählen Sie in **Benachrichtigungstyp** die Option „E-Mail an Azure Resource Manager-Rolle“ aus, um Besitzer, Mitwirkende und Leser des Abonnements auszuwählen, die Benachrichtigungen erhalten sollen. Wählen Sie die **Azure Resource Manager-Rolle** zum Senden der E-Mail aus.
    Sie können auch **E-Mail/SMS-Nachricht/Push/Voice** auswählen, um Benachrichtigungen an bestimmte Empfänger zu senden.

    Geben Sie **Name** für den Benachrichtigungstyp an, und wählen Sie nach Abschluss **Überprüfen + erstellen** aus.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Füllen Sie **Warnungsregeldetails** wie **Warnungsregelname** , **Beschreibung** , **Warnungsregel in Ressourcengruppe speichern** und **Schweregrad** aus.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Wählen Sie **Benachrichtigungsregel erstellen** aus, um die Benachrichtigung zu erstellen.
    Innerhalb weniger Minuten wird die Warnung aktiv und wie oben beschrieben ausgelöst.
## <a name="manage-your-alerts"></a>Verwalten Ihrer Warnungen
Nachdem Sie eine Warnung erstellt haben, können Sie sie auswählen und folgende Aktionen ausführen:

* Ein Diagramm anzeigen, das den Schwellenwert der Metrik und die tatsächlichen Werte vom Vortag zeigt, die für diese Warnung relevant sind.
* Die Warnungsregel **bearbeiten** oder **löschen** .
* Die Warnung **deaktivieren** oder **aktivieren** , wenn Sie den Empfang von Benachrichtigungen vorübergehend beenden oder fortsetzen möchten.


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Festlegen von Warnungen für Metriken](../../azure-monitor/platform/alerts-metric.md).
- Erfahren Sie mehr über verfügbare [Metriken in Azure Database for MySQL Flexible Server](./concepts-monitoring.md).
- [Informationen zur Funktionsweise von Metrikwarnungen in Azure Monitor](../../azure-monitor/platform/alerts-metric-overview.md)