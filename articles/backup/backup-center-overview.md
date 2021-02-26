---
title: Übersicht über Backup Center
description: Dieser Artikel enthält eine Übersicht zu Backup Center für Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42fcc782eb50c061ab7617ea0ef1f3344fee4e9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893608"
---
# <a name="overview-of-backup-center-preview"></a>Übersicht über Backup Center (Vorschau)

Backup Center bietet eine **zentralisierte einheitliche Verwaltungsumgebung** in Azure, mit der Unternehmen Sicherungen in großem Maßstab steuern, überwachen, betreiben und analysieren können. Als solches ist Backup Center konsistent mit den nativen Verwaltungsumgebungen von Azure.

Zu den wichtigsten Vorteilen von Backup Center gehören:

* **Zentralisierte Benutzeroberfläche zum Verwalten von Sicherungen** – Backup Center ist für die Verwendung in einer großen und verteilten Azure-Umgebung ausgelegt. Mit Backup Center können Sie Sicherungen, die mehrere Workloadtypen, Tresore, Abonnements, Regionen und [Azure Lighthouse](../lighthouse/overview.md)-Mandanten umfassen, effizient verwalten.
* **Datenquellenzentrierte Verwaltung** – Backup Center bietet Ansichten und Filter, die auf die zu sichernden Datenquellen (z. B. virtuelle Computer und Datenbanken) zentriert sind. Dies ermöglicht es Ressourcenbesitzern oder Sicherungsadministratoren, Sicherungen von Elementen zu überwachen und betreiben, ohne sich damit befassen zu müssen, in welchem Tresor ein Element gesichert wird. Ein wesentliches Merkmal dieses Designs ist die Möglichkeit, Ansichten nach datenquellenspezifischen Eigenschaften zu filtern, wie z. B. nach Datenquellenabonnement, Datenquellen-Ressourcengruppe oder Datenquellentags. Wenn Ihre Organisation beispielsweise üblicherweise virtuellen Computern, die verschiedenen Abteilungen angehören, unterschiedliche Tags zuweist, können Sie Backup Center verwenden, um Sicherungsinformationen auf der Grundlage der Tags der zugrunde liegenden virtuellen Computer, die gesichert werden, zu filtern, ohne sich auf das Tag des Tresors konzentrieren müssen.
* **Verbundene Erfahrungen** – Backup Center bietet native Integrationen in bestehende Azure-Dienste, die eine Verwaltung in großem Maßstab ermöglichen. Beispielsweise nutzt Backup Center die [Azure Policy](../governance/policy/overview.md)-Umgebung, um Ihnen bei der Steuerung Ihrer Sicherungen zu helfen. Es nutzt auch [Azure-Arbeitsmappen](../azure-monitor/platform/workbooks-overview.md) und [Azure Monitor-Protokolle](../azure-monitor/platform/data-platform-logs.md), um Ihnen zu helfen, detaillierte Berichte über Sicherungen anzuzeigen. Sie müssen also keine neuen Prinzipien erlernen, um die vielfältigen Features, die Backup Center bietet, nutzen zu können.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Backup Center wird derzeit für Sicherungen von virtuellen Azure-Computern, für SQL in Azure-VM-Sicherungen, für SAP HANA in Azure-VM-Sicherungen, für Azure Files-Sicherungen und für Azure Database for PostgreSQL-Serversicherungen unterstützt.
* Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="get-started"></a>Erste Schritte

Um mit der Verwendung von Backup Center zu beginnen, suchen Sie im Azure-Portal nach **Backup Center**, und navigieren Sie zum Dashboard **Backup Center (Vorschau)** .

![Backup Center-Suche](./media/backup-center-overview/backup-center-search.png)

Der erste Bildschirm, der angezeigt wird, ist der Bildschirm **Übersicht**. Er enthält zwei Kacheln – **Aufträge** und **Sicherungsinstanzen**.

![Backup Center-Kacheln](./media/backup-center-overview/backup-center-overview-widgets.png)

Über die Kachel **Aufträge** erhalten Sie eine zusammenfassende Ansicht aller Sicherungs- und Wiederherstellungsaufträge, die in den letzten 24 Stunden in Ihrem gesamten Sicherungsbestand ausgelöst wurden. Sie können Informationen über die Anzahl der abgeschlossenen, fehlgeschlagenen und in Bearbeitung befindlichen Aufträge anzeigen. Wenn Sie eine der Zahlen auf dieser Kachel auswählen, können Sie weitere Informationen zu Aufträgen für einen bestimmten Datenquellentyp, Vorgangstyp und Status anzeigen.

Über die Kachel **Sicherungsinstanzen** erhalten Sie eine zusammenfassende Ansicht aller Sicherungsinstanzen in Ihrem Sicherungsbestand. Sie können z. B. die Anzahl der Sicherungsinstanzen sehen, die sich im Zustand „Vorläufig gelöscht“ befinden, und der Anzahl der Instanzen gegenüberstellen, die noch für den Schutz konfiguriert sind. Wenn Sie eine der Zahlen auf dieser Kachel auswählen, können Sie weitere Informationen zu Sicherungsinstanzen für einen bestimmten Datenquellentyp und Schutzzustand anzeigen.

Sehen Sie sich das folgende Video an, um die Funktionen von Backup Center zu verstehen:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Führen Sie die [nächsten Schritte](#next-steps) aus, um die verschiedenen Funktionen von Backup Center zu verstehen und zu erfahren, wie Sie diese Funktionen zur effizienten Verwaltung Ihres Sicherungsbestands nutzen können.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen und Betreiben von Sicherungen](backup-center-monitor-operate.md)
* [Steuern Ihres Sicherungsbestands mit Backup Center](backup-center-govern-environment.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
* [Ausführen von Aktionen mithilfe des Backup Centers](backup-center-actions.md)