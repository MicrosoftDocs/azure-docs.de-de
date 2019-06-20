---
title: 'Azure-Portal: Dynamische Datenmaskierung für SQL-Datenbank | Microsoft-Dokumentation'
description: Einstieg in die dynamische Datenmaskierung für SQL-Datenbank im Azure-Portal
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61077709"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Erste Schritte mit der dynamischen Datenmaskierung für SQL-Datenbank im Azure-Portal

In diesem Artikel wird erläutert, wie die [dynamische Datenmaskierung](sql-database-dynamic-data-masking-get-started.md) im Azure-Portal implementiert wird. Die dynamische Datenmaskierung können Sie auch mithilfe von [Azure SQL-Datenbank-Cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) oder der [REST-API](https://docs.microsoft.com/rest/api/sql/) implementieren.

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Einrichten der dynamischen Datenmaskierung für Ihre Datenbank im Azure-Portal

1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com).
2. Navigieren Sie zur Seite mit den Einstellungen der Datenbank mit den sensiblen Daten, die Sie maskieren möchten.
3. Klicken Sie auf die Kachel **Dynamische Datenmaskierung**, woraufhin die Konfigurationsseite **Dynamische Datenmaskierung** geöffnet wird.

   * Alternativ können Sie nach unten zum Abschnitt **Vorgänge** scrollen und auf **Dynamische Datenmaskierung** klicken.

     ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. Auf der Konfigurationsseite **Dynamische Datenmaskierung** werden ggf. einige Datenbankspalten angezeigt, die von der Empfehlungs-Engine für die Maskierung gekennzeichnet wurden. Klicken Sie zum Akzeptieren der Empfehlungen einfach für eine oder mehrere Spalten auf **Maske hinzufügen**. Basierend auf dem Standardtyp der jeweiligen Spalte wird eine Maskierung erstellt. Sie können die Maskierungsfunktion ändern, indem Sie auf die Maskierungsregel klicken und das Maskierungsfeldformat in ein anderes Format Ihrer Wahl ändern. Klicken Sie auf **Speichern**, um Ihre Einstellungen zu speichern.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. Klicken Sie zum Hinzufügen einer Maskierung für eine beliebige Spalte der Datenbank oben auf der Konfigurationsseite **Dynamische Datenmaskierung** auf **Maske hinzufügen**, um die Konfigurationsseite **Maskierungsregel hinzufügen** zu öffnen.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. Wählen Sie **Schema**, **Tabelle** und **Spalte** aus, um das Feld für die Maskierung zu bestimmen.
7. Wählen Sie in der Liste der Kategorien für das Maskieren empfindlicher Daten ein **Maskierungsfeldformat** .

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. Klicken Sie auf der Datenmaskierungsregel-Seite auf **Speichern**, um die Gruppe von Maskierungsregeln in der Richtlinie für die dynamische Datenmaskierung zu aktualisieren.
9. Geben Sie die SQL-Benutzer oder AAD-Identitäten ein, die von der Maskierung ausgeschlossen werden sollen und Zugriff auf die vertraulichen Daten ohne Maskierung haben. Hierbei sollte es sich um eine durch Semikolons getrennte Liste mit Benutzern handeln. Benutzer mit Administratorrechten haben immer Zugriff auf die Originaldaten ohne Maskierung.

    ![Navigationsbereich](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > Damit die Anwendungsschicht sensible Daten für die privilegierten Anwendungsbenutzer anzeigen kann, fügen Sie den SQL-Benutzer oder die AAD-Identität hinzu, die von der Anwendung zum Abfragen der Datenbank verwendet wird. Es wird ausdrücklich empfohlen, dass diese Liste nur eine minimale Anzahl von privilegierten Benutzern enthält, um die Anzeige sensibler Daten zu minimieren.

10. Klicken Sie auf der Konfigurationsseite für die Datenmaskierung auf **Speichern**, um die neue oder aktualisierte Maskierungsrichtlinie zu speichern.

## <a name="next-steps"></a>Nächste Schritte

* Eine Übersicht über die dynamische Datenmaskierung finden Sie unter [Dynamische Datenmaskierung](sql-database-dynamic-data-masking-get-started.md).
* Die dynamische Datenmaskierung können Sie auch mithilfe von [Azure SQL-Datenbank-Cmdlets](https://docs.microsoft.com/powershell/module/az.sql/) oder der [REST-API](https://docs.microsoft.com/rest/api/sql/) implementieren.
