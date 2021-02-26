---
title: Abrufen von Erkenntnissen mithilfe von Backup Center
description: Erfahren Sie, wie Sie mit Backup Center historische Trends analysieren und tiefere Erkenntnisse aus Ihren Sicherungen gewinnen.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c5f707ce3bc8d1172805ac14958a50bf59d1412d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893692"
---
# <a name="obtain-insights-using-backup-center-preview"></a>Abrufen von Erkenntnissen mithilfe von Backup Center (Vorschau)

Zum Analysieren von historischen Trends und Gewinnen von tieferen Erkenntnissen aus Ihren Sicherungen bietet Backup Center (Vorschau) eine Schnittstelle zu [Sicherungsberichten](configure-reports.md), die [Azure Monitor-Protokolle](../azure-monitor/platform/data-platform-logs.md) und [Azure-Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md) verwendet. Sicherungsberichte bieten die folgenden Funktionen:

- Zuordnung und Vorhersagen des verbrauchten Cloudspeichers.

- Überwachung von Sicherungen und Wiederherstellungen.

- Identifizierung wichtiger Trends auf verschiedenen Granularitätsebenen.

- Erhalten von Einblicken in Kostenoptimierungsmöglichkeiten für Ihre Sicherungen und Gewinnen von Erkenntnissen dazu

## <a name="supported-scenarios"></a>Unterstützte Szenarien

- Sicherungsberichte (Vorschau) sind derzeit für Sicherungen von Azure Database for PostgreSQL-Servern nicht verfügbar.

- Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="get-started"></a>Erste Schritte

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Konfigurieren Ihrer Tresore zum Senden von Daten an einen Log Analytics-Arbeitsbereich

[Informationen zum Konfigurieren von Diagnoseeinstellungen für Ihre Tresore im großen Stil](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Anzeigen von Sicherungsberichten im Backup Center-Portal

Durch Auswählen der Menüoption **Sicherungsberichte** in Backup Center werden die Berichte geöffnet. Wählen Sie mindestens einen Log Analytics Arbeitsbereich aus, um zentrale Informationen zu Ihren Sicherungen anzuzeigen und zu analysieren.

![Sicherungsberichte in Backup Center](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Im Folgenden finden Sie die verfügbaren Ansichten:

1. **Zusammenfassung:** Verwenden Sie diese Registerkarte, um eine allgemeine Übersicht über Ihr Sicherungsumfeld zu erhalten. [Weitere Informationen](./configure-reports.md#summary)

1. **Sicherungselemente:** Auf dieser Registerkarte können Sie Informationen und Trends für Cloudspeicher anzeigen, der auf einer Sicherungselementebene verarbeitet wird. [Weitere Informationen](./configure-reports.md#backup-items)

1. **Nutzung:** Verwenden Sie diese Registerkarte zum Anzeigen von wichtigen Abrechnungsparametern für Ihre Sicherungen. [Weitere Informationen](./configure-reports.md#usage)

1. **Aufträge:** Verwenden Sie diese Registerkarte zum Anzeigen von langfristigen Trends für Aufträge, z. B. der Anzahl fehlerhafter Aufträge pro Tag und der Hauptgründe für Auftragsfehler. [Weitere Informationen](./configure-reports.md#jobs)

1. **Richtlinien:** Verwenden Sie diese Registerkarte zum Anzeigen von Informationen zu allen aktiven Richtlinien, z. B. der Anzahl zugeordneter Elemente und des gesamten Cloudspeicherplatzes, der von Elementen verbraucht wird, die im Rahmen einer bestimmten Richtlinie gesichert wurden. [Weitere Informationen](./configure-reports.md#policies)

1. **Optimieren:** Verwenden Sie diese Registerkarte, um potenzielle Möglichkeiten zur Kostenoptimierung Ihrer Sicherungen anzuzeigen. [Weitere Informationen](./configure-reports.md#optimize)

## <a name="next-steps"></a>Nächste Schritte

- [Überwachen und Betreiben von Sicherungen](backup-center-monitor-operate.md)
- [Steuern Ihres Sicherungsbestands](backup-center-govern-environment.md)
- [Ausführen von Aktionen mithilfe des Backup Centers](backup-center-actions.md)