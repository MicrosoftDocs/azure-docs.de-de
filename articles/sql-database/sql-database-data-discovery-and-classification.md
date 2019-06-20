---
title: 'Azure SQL-Datenbank und SQL Data Warehouse: Datenermittlung und -klassifizierung | Microsoft-Dokumentation'
description: Azure SQL-Datenbank und Datenermittlung sowie -klassifizierung
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: e451b7837a1cff4bbeaecd1573dc860524caf4d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142655"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Azure SQL-Datenbank und SQL Data Warehouse: Datenermittlung und -klassifizierung

Datenermittlung und -klassifizierung (zurzeit in der Vorschau) bietet erweiterte Funktionen für Azure SQL-Datenbank zum **Ermitteln**, **Klassifizieren**, **Bezeichnen** & **Schützen** sensibler Daten in Ihren Datenbanken.
Das Ermitteln und Klassifizieren Ihrer besonders sensiblen Daten (Geschäfts-/Finanz-/Gesundheitsdaten, persönlich identifizierbare Daten (PII) usw.) kann eine entscheidende Rolle in der Strategie Ihrer Organisation zum Datenschutz spielen. Das Feature kann als Infrastruktur für Folgendes dienen:

- Unterstützen der Einhaltung von Datenschutzstandards und gesetzlicher Bestimmungen
- Verschiedene Sicherheitsszenarien, z.B. Überwachung und Warnungen bei abweichendem Zugriff auf sensible Daten
- Steuern des Zugriffs auf und Härten der Sicherheit von Datenbanken, die sensible Daten enthalten

Datenermittlung und -klassifizierung ist Teil des Angebots [Advanced Data Security (ADS)](sql-database-advanced-data-security.md). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf und die Verwaltung von Datenermittlung und -klassifizierung ist über das zentrale SQL ADS-Portal möglich.

> [!NOTE]
> Dieses Dokument bezieht sich auf Azure SQL-Datenbank und Azure SQL Data Warehouse. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind. Informationen zu SQL Server (lokal) finden Sie unter [SQL-Datenermittlung und -klassifizierung](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Was ist Datenermittlung und -klassifizierung?

Datenermittlung und -klassifizierung führt eine Reihe von erweiterten Diensten und neuen SQL-Funktionen ein und bildet damit ein neues SQL Information Protection-Paradigma für den Schutz der Daten (nicht nur der Datenbank):

- **Ermittlung und Empfehlungen**

  Das Klassifizierungsmodul scannt Ihre Datenbank und identifiziert Spalten, die unter Umständen sensible Daten enthalten. Es bietet dann eine einfache Möglichkeit, diese zu überprüfen und die entsprechenden Klassifizierungsempfehlungen über das Azure Portal anzuwenden.

- **Bezeichnung**

  Mithilfe der neu im SQL-Modul eingeführten Metadatenattribute können sensible Daten dauerhaft mit Klassifizierungsbezeichnungen gekennzeichnet werden. Diese Metadaten können dann für erweiterte Überwachungs- und Schutzszenarien für sensible Daten genutzt werden.

- **Vertraulichkeit von Abfrageergebnissen**

  Die Vertraulichkeit von Abfrageergebnissen wird zu Überwachungszwecken in Echtzeit berechnet.

- **Sichtbarkeit**

  Der Klassifizierungsstatus der Datenbank kann in einem detaillierten Dashboard im Portal angezeigt werden. Darüber hinaus können Sie einen Bericht (im Excel-Format) herunterladen, der für Compliance- und Überwachungszwecke und für weitere Zwecke verwendet werden kann.

## <a id="subheading-2"></a>Ermitteln, Klassifizieren und Bezeichnen von vertraulichen Spalten

Im folgenden Abschnitt werden die Schritte zum Ermitteln, Klassifizieren und Bezeichnen von Spalten mit sensiblen Daten in der Datenbank sowie das Anzeigen des aktuellen Klassifizierungsstatus der Datenbank und das Exportieren von Berichten beschrieben.

Die Klassifizierung umfasst zwei Metadatenattribute:

- Bezeichnungen – die wichtigsten Klassifizierungsattribute zum Definieren der Vertraulichkeitsstufe der in der Spalte gespeicherten Daten  
- Informationstypen – bieten zusätzliche Granularität für den Typ der in der Spalte gespeicherten Daten

## <a name="define-and-customize-your-classification-taxonomy"></a>Definieren und Anpassen der Klassifizierungstaxonomie

SQL-Datenermittlung und -klassifizierung verfügt über einen integrierten Satz von Vertraulichkeitsbezeichnungen und einen integrierten Satz von Informationstypen und Ermittlungslogik. Sie haben jetzt die Möglichkeit, diese Taxonomie anzupassen und eine spezielle Gruppe und Rangfolge von Klassifizierungskonstrukten für Ihre Umgebung zu definieren.

Die Definition und Anpassung Ihrer Klassifizierungstaxonomie erfolgt an zentraler Stelle für den ganzen Azure-Mandanten. Diese Stelle befindet sich im [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) und ist Bestandteil Ihrer Sicherheitsrichtlinie. Nur Benutzer mit Administratorrechten für die Stammverwaltungsgruppe des Mandanten können diese Aufgabe ausführen.

Im Rahmen der Richtlinienverwaltung von Azure Information Protection können Sie benutzerdefinierte Bezeichnungen definieren, deren Rangfolge festlegen und sie einer ausgewählten Gruppe von Informationstypen zuordnen. Sie können auch eigene benutzerdefinierte Informationstypen hinzufügen und diese mit Zeichenfolgemustern konfigurieren, die der Ermittlungslogik zum Identifizieren des Datentyps in Ihren Datenbanken hinzugefügt werden.
Weitere Informationen zum Anpassen und Verwalten Ihrer Richtlinie finden Sie in der [Anleitung zu Information Protection-Richtlinien](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

Sobald die mandantenweite Richtlinie definiert wurde, können Sie die Klassifizierung einzelner Datenbanken mithilfe Ihrer benutzerdefinierten Richtlinie fortsetzen.

## <a name="classify-your-sql-database"></a>Klassifizieren Ihrer SQL-Datenbank

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Navigieren Sie im Bereich von Azure SQL-Datenbank unter der Überschrift „Sicherheit“ zu **Advanced Data Security**. Klicken Sie, um Advanced Data Security zu aktivieren, und klicken Sie dann auf die Karte **Datenermittlung und -klassifizierung (Vorschau)** .

   ![Überprüfen einer Datenbank](./media/sql-data-discovery-and-classification/data_classification.png)

3. Die Registerkarte **Übersicht** enthält eine Zusammenfassung des aktuellen Klassifizierungsstatus der Datenbank. Dazu gehört auch eine ausführliche Liste aller klassifizierten Spalten, die Sie filtern können, um nur bestimmte Schemateile, Informationstypen und Bezeichnungen anzuzeigen. Wenn Sie noch keine Spalten klassifiziert haben, [fahren Sie mit Schritt 5 fort](#step-5).

   ![Zusammenfassung des aktuellen Klassifizierungsstatus](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Um einen Bericht im Excel-Format herunterzuladen, klicken Sie oben im Fenster im Menü auf die Option **Exportieren**.

   ![Exportieren in Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Um die Klassifizierung Ihrer Daten zu starten, klicken Sie oben im Fenster auf die Registerkarte **Klassifikation**.

    ![Klassifizieren von Daten](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Das Klassifizierungsmodul scannt Ihre Datenbank nach Spalten mit potenziell sensiblen Daten und stellt eine Liste der **empfohlenen Spaltenklassifizierungen** bereit. So zeigen Sie Klassifizierungsempfehlungen an und übernehmen sie

   - Um die Liste der empfohlenen Spaltenklassifizierungen anzuzeigen, klicken Sie am unteren Rand des Fensters auf den Empfehlungsbereich:

      ![Klassifizieren von Daten](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Überprüfen Sie die Liste der Empfehlungen. Um eine Empfehlung für eine bestimmte Spalte zu akzeptieren, aktivieren Sie das Kontrollkästchen in der linken Spalte der entsprechenden Zeile. Sie können auch *alle Empfehlungen* als akzeptiert markieren, indem Sie das Kontrollkästchen im Tabellenkopf der Empfehlungen aktivieren.

       ![Überprüfen der Empfehlungsliste](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Um die ausgewählten Empfehlungen anzuwenden, klicken Sie auf die blaue Schaltfläche **Accept selected recommendations** (Ausgewählte Empfehlungen akzeptieren).

      ![Anwenden von Empfehlungen](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Sie können Spalten auch **manuell klassifizieren** – entweder als Alternative zur empfehlungsbasierten Klassifizierung oder als Ergänzung:

   - Klicken Sie im oberen Menü des Fensters auf **Klassifizierung hinzufügen**.

      ![Manuelles Hinzufügen einer Klassifizierung](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - Wählen Sie im daraufhin geöffneten Kontextfenster das Schema, die Tabelle und dann die Spalte, die Sie klassifizieren möchten, sowie die den Informationstyp und die Vertraulichkeitsbezeichnung aus. Klicken Sie dann am unteren Rand des Kontextfensters auf die blaue Schaltfläche **Klassifizierung hinzufügen**.

      ![Auswählen der klassifizierenden Spalte](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Klicken Sie im Menü oben im Fenster auf **Speichern**, um die Klassifizierung abzuschließen und die Datenbankspalten dauerhaft mit den neuen Klassifizierungsmetadaten zu bezeichnen (mit Tags zu versehen).

   ![Speichern](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Überwachen des Zugriffs auf sensible Daten

Ein wichtiger Aspekt des Paradigmas für den Schutz von Informationen ist die Möglichkeit, den Zugriff auf sensible Daten zu überwachen. Die [Azure SQL-Datenbank-Überwachung](sql-database-auditing.md) wurde erweitert, um das Überwachungsprotokoll um das neue Feld *data_sensitivity_information* zu ergänzen. In diesem werden die Vertraulichkeitsklassifizierungen (Bezeichnungen) der eigentlichen Daten erfasst, die bei der Abfrage zurückgegeben wurden.

![Überwachungsprotokoll](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Verwalten der Datenklassifizierung mit T-SQL

Mit T-SQL können Sie Spaltenklassifizierungen hinzufügen/entfernen sowie alle Klassifizierungen für die gesamte Datenbank abrufen.

> [!NOTE]
> Bei der Verwendung von T-SQL zur Verwaltung von Bezeichnungen gibt es keine Validierung, dass zu einer Spalte hinzugefügte Bezeichnungen in der Richtlinie zum Schutz von Organisationsinformationen (die in den Portalempfehlungen erscheinen) vorhanden sind. Aus diesem Grund müssen Sie dies überprüfen.

- Hinzufügen/Aktualisieren der Klassifizierung einer oder mehrerer Spalten: [VERTRAULICHKEITSKLASSIFIZIERUNG HINZUFÜGEN](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Entfernen der Klassifizierung aus einer oder mehreren Spalten: [VERTRAULICHKEITSKLASSIFIZIERUNG LÖSCHEN](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Anzeigen aller Klassifizierungen in der Datenbank: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Sie können zudem REST-APIs verwenden, um Klassifizierungen programmgesteuert zu verwalten. Die veröffentlichten REST-APIs unterstützen die folgenden Vorgänge:

- [Erstellen oder aktualisieren:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) erstellt oder aktualisiert die Vertraulichkeitsbezeichnung einer bestimmten Spalte.
- [Löschen:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) löscht die Vertraulichkeitsbezeichnung einer bestimmten Spalte.
- [Abrufen:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) ruft die Vertraulichkeitsbezeichnung einer bestimmten Spalte ab.
- [Aktuelle nach Datenbank auflisten:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) listet die derzeitigen Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf.
- [Empfohlene nach Datenbank auflisten:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) listet die empfohlenen Vertraulichkeitsbezeichnungen einer bestimmten Datenbank auf.

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Verwalten der Datenermittlung und Klassifizierung mit Azure PowerShell

Sie können PowerShell verwenden, um alle empfohlenen Spalten in einer Azure SQL-Datenbank und einer verwalteten Instanz abzurufen.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-Cmdlets für eine Azure SQL Data-Datenbank

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell-Cmdlets für eine verwaltete Instanz

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Berechtigungen

Die folgenden integrierten Rollen können die Datenklassifizierung einer Azure SQL-Datenbank lesen: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` und `User Access Administrator`.

Die folgenden integrierten Rollen können die Datenklassifizierung einer Azure SQL-Datenbank ändern: `Owner`, `Contributor`, `SQL Security Manager`.

Weitere Informationen zu [RBAC für Azure Resources](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Nächste Schritte

- Erfahren Sie mehr über [Advanced Data Security](sql-database-advanced-data-security.md).
- Sie sollten in Betracht ziehen, die [Azure SQL-Datenbank-Überwachung](sql-database-auditing.md) für die Überwachung und Überprüfung des Zugriffs auf Ihre klassifizierten sensiblen Daten zu konfigurieren.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
