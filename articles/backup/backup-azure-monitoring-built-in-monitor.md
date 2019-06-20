---
title: 'Azure Backup: Überwachen von geschützten Azure Backup-Workloads'
description: Überwachen von Azure Backup-Workloads mithilfe des Azure-Portals
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup, Warnungen
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: ab7d2c0af4bc71733a7995b7e781f0facbfbb29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65236437"
---
# <a name="monitoring-azure-backup-workloads"></a>Überwachen von Azure Backup-Workloads

Azure Backup enthält mehrere Sicherheitslösungen, deren Einsatz von den Anforderungen an die Sicherheit und der Infrastrukturtopologie (lokal oder Azure) abhängt. Alle Benutzer und Administratoren der Sicherung sollten die Vorgänge in allen Lösungen verfolgen können und in wichtigen Fällen benachrichtigt werden. In diesem Artikel werden die Überwachungs- und Benachrichtigungsfunktionen in Azure Backup näher erläutert.

## <a name="backup-jobs-in-recovery-services-vault"></a>Sicherungsaufträge im Recovery Services-Tresor

Azure Backup bietet integrierte Überwachungs- und Warnungsfunktionen für durch Azure Backup geschützte Workloads. In den Einstellungen für den Recovery Services-Tresor finden Sie im Abschnitt **Überwachung** die integrierten Aufträge und Warnungen.

![Integrierte Überwachung im Recovery Services-Tresor](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Aufträge werden generiert, wenn Vorgänge wie das Konfigurieren der Sicherung, die Sicherung, Wiederherstellung oder das Löschen der Sicherung durchgeführt werden.

Im Folgenden werden die Aufträge der folgenden Azure Backup-Lösungen vorgestellt:

  - Azure VM Backup
  - Azure-Dateisicherung
  - Azure-Workloadsicherung wie SQL
  - Azure Backup-Agent (Microsoft Azure Backup, MAB)

Aufträge aus System Center Data Protection Manager (SC-DPM) und Microsoft Azure Backup Server (MABS) werden nicht aufgeführt.

> [!NOTE]
> Azure-Workloads wie SQL-Sicherungen in Azure-VMs umfassen viele Sicherungsaufträge. Protokollsicherungen können beispielsweise alle 15 Minuten ausgeführt werden. Für solche Datenbankworkloads werden darum nur vom Benutzer ausgelöste Vorgänge angezeigt. Geplante Sicherungsvorgänge werden nicht angezeigt.

## <a name="backup-alerts-in-recovery-services-vault"></a>Sicherungswarnungen im Recovery Services-Tresor

Bei Warnungen handelt es sich in erster Linie um Szenarios, in denen Benutzer benachrichtigt werden, damit Sie entsprechende Maßnahmen ergreifen können. Im Abschnitt **Sicherungswarnungen** werden die von Azure Backup generierten Warnungen angezeigt. Diese Warnungen werden vom Dienst definiert. Es können keine benutzerdefinierten Warnungen erstellt werden.

### <a name="alert-scenarios"></a>Warnungsszenarios
Die folgenden Szenarios werden vom Dienst als solche definiert, in denen Warnungen ausgelöst werden können.

  - Fehler bei der Sicherung/Wiederherstellung
  - Sicherung mit Warnungen für Azure Backup-Agent (MAB) erfolgreich abgeschlossen
  - Beenden des Schutzes unter Beibehaltung der Daten/Beenden des Schutzes inklusive Löschung der Daten

### <a name="exceptions-when-an-alert-is-not-raised"></a>Ausnahmen, bei denen keine Warnung ausgelöst wird
Es gibt folgende Ausnahmefälle, in denen keine Warnung ausgelöst wird:

  - Wenn der Benutzer den ausgeführten Auftrag explizit abgebrochen hat
  - Wenn der Auftrag fehlschlägt, weil ein anderer Sicherungsauftrag gerade ausgeführt wird (keine Handlung erforderlich, da gewartet werden muss, bis der aktuelle Auftrag abgeschlossen ist)
  - Wenn der Sicherungsauftrag der VM fehlschlägt, weil die gesicherte Azure-VM nicht mehr existiert

Diese Ausnahmen wurden eingeführt, weil das Ergebnis der oben aufgeführten Vorgänge, die in erster Linie vom Benutzer ausgelöst werden, direkt im Portal bzw. im PS- oder CLI-Client dargestellt wird. Sie sind dem Benutzer also bekannt, sodass keine Benachrichtigung erforderlich ist.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Im Folgenden werden die Warnungen der folgenden Azure Backup-Lösungen vorgestellt:

  - Azure-VM-Sicherungen
  - Azure-Dateisicherungen
  - Azure-Workloadsicherungen wie SQL
  - Azure Backup-Agent (Microsoft Azure Backup, MAB)

> [!NOTE]
> Warnungen aus System Center Data Protection Manager (SC-DPM) und Microsoft Azure Backup Server (MABS) werden hier nicht aufgeführt.

### <a name="alert-types"></a>Warnungstypen
Je nach Schweregrad der Warnung werden diese drei Arten zugeordnet:

  - **Kritisch**: Im Prinzip führt jeder Fehler bei der Sicherung oder Wiederherstellung (geplant oder vom Benutzer ausgelöst) zu einer Warnung, die als „Kritisch“ angezeigt wird. Das gilt auch für destruktive Vorgänge wie das Löschen der Sicherung.
  - **Warnung**: Wenn der Sicherungsvorgang erfolgreich war, aber einige Warnungen auslöst werden, werden diese als „Warnung“ aufgeführt.
  - **Informativ:** Derzeit werden keine informativen Warnungen von Azure Backup generiert.

## <a name="notification-for-backup-alerts"></a>Benachrichtigungen für Azure Backup-Warnungen

> [!NOTE]
> Sie können die Benachrichtigungen über nur über das Azure-Portal konfigurieren. PS-, CLI, REST-API- und Azure Resource Manager-Vorlagen werden nicht unterstützt.

Wenn eine Warnung ausgelöst wird, werden die Benutzer benachrichtigt. Azure Backup enthält einen Mechanismus, der Benachrichtigungen per E-Mail sendet. Sie können einzelne E-Mail-Adressen oder Verteilerlisten angeben, die benachrichtigt werden sollen, wenn eine Warnung generiert wurde. Sie können ebenfalls Benachrichtigungen für jede einzelne Warnung aktivieren oder diese in einer stündlichen Übersicht gruppieren.

![Im Recovery Services-Tresor integrierte E-Mail-Benachrichtigung](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Wenn Sie die Benachrichtigungen konfiguriert haben, erhalten Sie eine Begrüßungs-E-Mail bzw. eine einführende E-Mail. Dadurch wird bestätigt, dass Azure Backup E-Mails an diese Adressen senden kann, wenn eine Warnung ausgelöst wird.<br>

Wenn die Häufigkeit auf eine stündliche Übersicht festgelegt und eine Warnung innerhalb einer Stunde ausgelöst und behoben wurde, ist diese nicht in der stündlichen Übersicht enthalten.

> [!NOTE]
>
> * Wenn ein destruktiver Vorgang wie das **Beenden des Schutzes inklusive Löschung der Daten** durchgeführt wird, wird eine Warnung ausgelöst und eine E-Mail an den Besitzer des Abonnements sowie an die Administratoren und Co-Admins des Abonnements gesendet, auch wenn die Benachrichtigungen für den Recovery Services-Tresor nicht konfiguriert wurden.
> * Verwenden Sie [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace), um Benachrichtigungen für erfolgreiche Aufträge zu konfigurieren.

## <a name="next-steps"></a>Nächste Schritte

[Monitor Azure backup workloads using Azure Monitor (Überwachen von Azure Backup-Workloads mithilfe von Azure Monitor)](backup-azure-monitoring-use-azuremonitor.md)
