---
title: Überwachen und Betreiben von Sicherungen mit Backup Center
description: In diesem Artikel wird erläutert, wie Sie mit Backup Center Sicherungen im gewünschten Umfang überwachen und betreiben.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 9ce3b0f00c51e9b466a9ed040b677df811448b1d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98893777"
---
# <a name="monitor-and-operate-backups-using-backup-center-preview"></a>Überwachen und Betreiben von Sicherungen mit Backup Center (Vorschau)

Als Sicherungsadministrator können Sie Backup Center als zentrale Benutzeroberfläche zum Überwachen Ihrer Aufträge und täglichen Sichern des Bestands verwenden. Mit Backup Center können Sie auch reguläre Vorgänge ausführen, z. B. auf Anforderungen der bedarfsgesteuerten Sicherung reagieren, Sicherungen wiederherstellen, Sicherungsrichtlinien erstellen usw.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Backup Center wird derzeit für Sicherungen von virtuellen Azure-Computern, für SQL in Azure-VM-Sicherungen, für SAP HANA in Azure-VM-Sicherungen, für Azure Files-Sicherungen und für Azure Database for PostgreSQL-Serversicherungen unterstützt.
* Eine ausführliche Liste der unterstützten und nicht unterstützten Szenarien finden Sie in der [Supportmatrix](backup-center-support-matrix.md).

## <a name="backup-instances"></a>Sicherungsinstanzen

Backup Center ermöglicht das problemlose Suchen und Auffinden von Sicherungsinstanzen in Ihrem Sicherungsbestand.

Wenn Sie in Backup Center die Registerkarte **Sicherungsinstanzen** auswählen, können Sie Details zu allen Sicherungsinstanzen anzeigen, auf die Sie Zugriff haben.

 Sie können die folgenden Informationen zu den einzelnen Sicherungsinstanzen anzeigen:

* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Letzter Wiederherstellungspunkt
* Der Sicherungsinstanz zugeordneter Tresor
* Schutzstatus

 Sie können die Liste der Sicherungsinstanzen auch nach den folgenden Parametern filtern:

* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Standort der Datenquelle
* Datenquellentyp
* Tresor
* Schutzstatus
* Datenquellentags

Wenn Sie mit der rechten Maustaste auf eines der Elemente im Raster klicken, können Sie Aktionen für die angegebene Sicherungsinstanz durchführen, z. B. zur Ressource navigieren, bedarfsgesteuerte Sicherungen und Wiederherstellungen auslösen oder die Sicherung beenden.

![Backup Center – Instanzen](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Sicherungsaufträge

Mithilfe von Backup Center können Sie detaillierte Informationen zu allen Aufträgen anzeigen, die in Ihrem Sicherungsbestand erstellt wurden, und geeignete Maßnahmen für fehlerhafte Aufträge ausführen.

Wenn Sie in Backup Center das Menüelement **Sicherungsaufträge** auswählen, erhalten Sie eine Übersicht über alle Aufträge. Jeder Auftrag umfasst die folgenden Informationen:

* Dem Auftrag zugeordnete Sicherungsinstanz
* Abonnement der Datenquelle
* Ressourcengruppe der Datenquelle
* Standort der Datenquelle
* Auftragsvorgang
* Auftragsstatus
* Startzeit des Auftrags.
* Auftragsdauer

Wenn Sie ein Element im Raster auswählen, können Sie weitere Details zum angegebenen Auftrag anzeigen. Durch Klicken mit der rechten Maustaste auf ein Element können Sie zur Ressource navigieren, um erforderliche Maßnahmen zu ergreifen.

![Backup Center – Aufträge](./media/backup-center-monitor-operate/backup-center-jobs.png)

Mit der Registerkarte **Sicherungsaufträge** können Sie Aufträge maximal für die letzten sieben Tage anzeigen. Zum Anzeigen älterer Aufträge verwenden Sie [Sicherungsberichte](backup-center-obtain-insights.md).

## <a name="vaults"></a>Tresore

Wenn Sie in Backup Center das Menüelement **Tresore** auswählen, können Sie eine Liste aller [Recovery Services-Tresore](backup-azure-recovery-services-vault-overview.md) und [Sicherungstresore](backup-vault-overview.md) anzeigen, auf die Sie Zugriff haben. Sie können die Liste anhand der folgenden Parameter filtern:

* Tresorabonnement
* Tresorressourcengruppe
* Name des Tresors
* Der Richtlinie zugeordneter Datenquellentyp

Durch Auswahl eines Elements in der Liste können Sie zu einem angegebenen Tresor navigieren.

![Backup Center – Tresore](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Sicherungsrichtlinien

Mit Backup Center können Sie wichtige Informationen für alle Ihre Sicherungsrichtlinien anzeigen und bearbeiten.

Wenn Sie das Menüelement **Sicherungsrichtlinien** auswählen, können Sie alle Richtlinien anzeigen, die Sie in Ihrem Sicherungsbestand erstellt haben. Sie können die Liste nach Tresorabonnement, Ressourcengruppe, Datenquellentyp und Tresor filtern. Wenn Sie mit der rechten Maustaste auf ein Element im Raster klicken, können Sie zugehörige Elemente für diese Richtlinie anzeigen, die Richtlinie bearbeiten oder sie bei Bedarf sogar löschen.

![Backup Center – Richtlinien](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Nächste Schritte

* [Steuern Ihres Sicherungsbestands](backup-center-govern-environment.md)
* [Ausführen von Aktionen mithilfe des Backup Centers](backup-center-actions.md)
* [Gewinnen von Einblicken in Ihre Sicherungen](backup-center-obtain-insights.md)
