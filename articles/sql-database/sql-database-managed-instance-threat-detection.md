---
title: 'Konfigurieren der Bedrohungserkennung: Verwaltete Azure SQL-Datenbank-Instanz | Microsoft-Dokumentation'
description: Die Bedrohungserkennung identifiziert anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank in der verwalteten Instanz hinweisen.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: a8e9dfe70e300e6b1d0d50aae60660644f2ab31d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61313892"
---
# <a name="configure-threat-detection-preview-in-azure-sql-database-managed-instance"></a>Konfigurieren der Bedrohungserkennung (Vorschau) für eine verwaltete Azure SQL-Datenbank-Instanz

Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) für eine [verwaltete Instanz](sql-database-managed-instance-index.yml) identifiziert anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese missbräuchlich zu verwenden. Mithilfe der Bedrohungserkennung können folgende Aktivitäten identifiziert werden: **potenzielle SQL-Einschleusung**, **Zugriff von einem ungewöhnlichen Standort oder Rechenzentrum**, **Zugriff über einen unbekannten Prinzipal oder eine potenziell schädliche Anwendung** und **Brute-Force-SQL-Anmeldeinformationen**. Weitere Informationen finden Sie im Abschnitt zu den [Warnungen der Bedrohungserkennung](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Sie können sich über [E-Mail-Benachrichtigungen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) oder im [Azure-Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) über die erkannten Bedrohungen benachrichtigen lassen.

Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) ist Bestandteil des [Advanced Data Security](sql-database-advanced-data-security.md)-Angebots (ADS). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf die Bedrohungserkennung und ihre Verwaltung sind über das zentrale SQL ADS-Portal möglich. Für den Bedrohungserkennungsdienst werden pro verwalteter Instanz 15 USD/Monat berechnet. Dabei sind die ersten 30 Tage kostenlos.

## <a name="set-up-threat-detection-for-your-managed-instance-in-the-azure-portal"></a>Einrichten der Bedrohungserkennung für Ihre verwaltete Instanz im Azure-Portal

1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com).
2. Navigieren Sie zur Konfigurationsseite der verwalteten Instanz, die geschützt werden soll. Wählen Sie auf der Seite **Einstellungen** die Option **Bedrohungserkennung** aus.
3. Auf der Konfigurationsseite für die Bedrohungserkennung
   - **Aktivieren Sie** die Bedrohungserkennung.
   - Konfigurieren Sie die **Liste der E-Mail-Empfänger**, die bei Erkennung anomaler Datenbankaktivitäten Sicherheitswarnungen erhalten sollen.
   - Wählen Sie das **Azure Storage-Konto** aus, in dem Überwachungsdatensätze zu anomalen Bedrohungen gespeichert werden.
4. Klicken Sie auf **Speichern**, um die neue oder aktualisierte Richtlinie für die Bedrohungserkennung zu speichern.

   ![Bedrohungserkennung](./media/sql-database-managed-instance-threat-detection/threat-detection.png)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Bedrohungserkennung](sql-database-threat-detection-overview.md).
- Informationen zu verwalteten Instanzen finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md)
- Weitere Informationen zur [Bedrohungserkennung für eine Einzeldatenbank](sql-database-threat-detection.md).
- Weitere Informationen zur [Überwachung einer verwalteten Instanz](https://go.microsoft.com/fwlink/?linkid=869430).
- Weitere Informationen zu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
