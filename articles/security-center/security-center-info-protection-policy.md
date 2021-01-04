---
title: SQL Information Protection-Richtlinie in Azure Security Center
description: Erfahren Sie, wie Sie Information Protection-Richtlinien im Azure Security Center anpassen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013784"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>SQL Information Protection-Richtlinie in Azure Security Center
 
Der [Mechanismus zur Datenermittlung und -klassifizierung](../azure-sql/database/data-discovery-and-classification-overview.md) von SQL Information Protection bietet erweiterte Funktionen für die Ermittlung, Klassifizierung, Bezeichnung und Berichterstellung vertraulicher Daten in Ihren Datenbanken. Dieser Mechanismus ist in [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md), [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) und [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) integriert.

Der Klassifizierungsmechanismus basiert auf den folgenden beiden Elementen:

- **Bezeichnungen**: Die wichtigsten Klassifizierungsattribute zum Definieren der *Vertraulichkeitsstufe* der in der Spalte gespeicherten Daten. 
- **Informationstypen**: Bieten zusätzliche Granularität für den *Typ* der in der Spalte gespeicherten Daten.

Die Information Protection-Richtlinienoptionen in Security Center bieten vordefinierte Bezeichnungen und Informationstypen, die als Standardwerte für die Klassifizierungs-Engine dienen. Sie können die Richtlinie wie unten beschrieben an die Anforderungen Ihrer Organisation anpassen.

> [!IMPORTANT]
> Um die Information Protection-Richtlinie für Ihren Azure-Mandanten anpassen zu können, benötigen Sie Administratorrechte für die Stammverwaltungsgruppe des Mandanten. Mehr dazu erfahren Sie unter [Erzielen der mandantenweiten Sichtbarkeit für Azure Security Center](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Seite mit Ihrer SQL Information Protection-Richtlinie":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Wie kann ich auf die SQL Information Protection-Richtlinie zugreifen?

Es gibt drei Möglichkeiten für den Zugriff auf die Information Protection-Richtlinie:

- **(Empfohlen)** : Über die Security Center-Seite mit Preisen und Einstellungen
- Über die Sicherheitsempfehlung „Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden“
- Über die Azure SQL-DB-Seite zur Datenermittlung

Jede dieser Möglichkeiten wird in der folgenden Abbildung auf der entsprechenden Registerkarte angezeigt.



### <a name="from-security-centers-settings"></a>[**Über die Security Center-Einstellungen**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Zugreifen auf die Richtlinie über die Security Center-Seite mit Preisen und Einstellungen <a name="sqlip-tenant"></a>

Wählen Sie auf der Security Center-Seite **Preise und Einstellungen** die Einstellung **SQL Information Protection** aus.

> [!NOTE]
> Diese Option wird nur Benutzern mit Berechtigungen auf Mandantenebene angezeigt. 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Zugreifen auf die SQL Information Protection-Richtlinie über die Azure Security Center-Seite mit Preisen und Einstellungen":::



### <a name="from-security-centers-recommendation"></a>[**Über eine Security Center-Empfehlung**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Zugreifen auf die Richtlinie über eine Security Center-Empfehlung <a name="sqlip-db"></a>

Verwenden Sie die Security Center-Empfehlung „Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden“, um die Seite zur Ermittlung und Klassifizierung von Daten für Ihre Datenbank anzuzeigen. Dort sehen Sie auch die Spalten, in denen Informationen entdeckt wurden, deren Klassifizierung empfohlen wird.

1. Suchen Sie auf der Security Center-Seite **Empfehlungen** nach der Empfehlung **Sensible Daten in Ihren SQL-Datenbanken müssen klassifiziert werden**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Suchen nach der Empfehlung, die Zugriff auf die SQL Information Protection-Richtlinien bietet":::

1. Wählen Sie auf der Seite mit den Empfehlungsdetails auf der Registerkarte **Fehlerfrei** oder **Fehlerhaft** eine Datenbank aus.

1. Die Seite **Datenermittlung und -klassifizierung** wird geöffnet. Wählen Sie **Konfigurieren** aus.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Öffnen der die SQL Information Protection-Richtlinie über die jeweilige Empfehlung in Azure Security Center":::



### <a name="from-azure-sql"></a>[**Über Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Zugreifen auf die Richtlinie über Azure SQL <a name="sqlip-azuresql"></a>

1. Öffnen Sie Azure SQL im Azure-Portal.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Öffnen von Azure SQL im Azure-Portal":::

1. Wählen Sie eine beliebige Datenbank aus.

1. Öffnen Sie im Menübereich **Sicherheit** die Seite **Datenermittlung und -klassifizierung** (1), und wählen Sie **Konfigurieren** (2) aus.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Öffnen der SQL Information Protection-Richtlinie in Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Anpassen der Informationstypen

Gehen Sie zum Verwalten und Anpassen von Informationstypen folgendermaßen vor:

1. Wählen Sie **Informationstypen verwalten** aus.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Verwalten von Informationstypen für Ihre Information Protection-Richtlinie":::

1. Um einen neuen Typ hinzuzufügen, wählen Sie **Informationstyp erstellen** aus. Sie können einen Namen, eine Beschreibung und Suchmusterzeichenfolgen für den Informationstyp konfigurieren. Suchzeichenfolgenmuster können optional Schlüsselwörter mit Platzhalterzeichen (mit dem Zeichen „%“) enthalten, anhand derer die automatisierte Ermittlungs-Engine sensible Daten in Ihren Datenbanken basierend auf den Metadaten der Spalten ermittelt.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Konfigurieren eines neuen Informationstyps für Ihre Information Protection-Richtlinie":::

1. Sie können auch die integrierten Informationstypen ändern, indem Sie zusätzliche Suchmusterzeichenfolgen hinzufügen, einige der vorhandenen Zeichenfolgen deaktivieren oder die Beschreibung ändern. 

    > [!TIP]
    > Sie können integrierte Typen jedoch weder löschen noch ihre Namen ändern. 

1. **Informationstypen** werden in aufsteigender Reihenfolge der Ermittlung aufgeführt, d.h., die Typen, die weiter oben in der Liste stehen, werden bei der Suche nach Übereinstimmungen bevorzugt. Um die Rangfolge zwischen den Informationstypen zu ändern, ziehen Sie die Typen an die entsprechende Position in der Tabelle oder verwenden die Schaltflächen **Nach oben verschieben** und **Nach unten verschieben**, um die Reihenfolge zu ändern. 

1. Wenn Sie fertig sind, klicken Sie auf **OK**.

1. Nachdem die Verwaltung Ihrer Informationstypen abgeschlossen ist, ordnen Sie den relevanten Bezeichnungen die entsprechenden Typen zu, indem Sie für eine bestimmte Bezeichnung auf **Konfigurieren** klicken und nach Bedarf Informationstypen hinzufügen oder löschen.

1. Um Ihre Änderungen anzuwenden, klicken Sie auf der Hauptseite **Bezeichnungen** auf **Speichern**.
 

## <a name="exporting-and-importing-a-policy"></a>Exportieren und Importieren einer Richtlinie

Sie können eine JSON-Datei mit Ihren definierten Bezeichnungen und Informationstypen herunterladen, in einem Editor Ihrer Wahl bearbeiten und die aktualisierte Datei dann importieren. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Exportieren und Importieren Ihrer Information Protection-Richtlinie":::

> [!NOTE]
> Sie benötigen Berechtigungen auf Mandantenebene, um eine Richtliniendatei zu importieren. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Verwalten von SQL Information Protection mit Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): Ruft die effektive SQL Information Protection-Richtlinie für den Mandanten ab.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): Legt die effektive SQL Information Protection-Richtlinie für den Mandanten fest.
 

## <a name="next-steps"></a>Nächste Schritte
 
In diesem Artikel haben Sie erfahren, wie Sie eine Information Protection-Richtlinie im Azure Security Center definieren. Weitere Informationen zum Klassifizieren und Schutz von sensiblen Daten in Ihren SQL-Datenbank-Instanzen mit SQL Information Protection finden Sie unter [Azure SQL-Datenbank – Datenermittlung und -klassifizierung](../azure-sql/database/data-discovery-and-classification-overview.md).

Weitere Informationen zu Sicherheitsrichtlinien und zur Datensicherheit in Security Center finden Sie in den folgenden Artikeln:
 
- [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md): Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Azure Security Center-Datensicherheit](security-center-data-security.md): Erfahren Sie, wie Security Center Daten verwaltet und schützt.
