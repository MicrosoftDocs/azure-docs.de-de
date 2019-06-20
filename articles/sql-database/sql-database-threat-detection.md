---
title: 'Bedrohungserkennung: Azure SQL-Datenbank | Microsoft-Dokumentation'
description: Die Bedrohungserkennung erkennt anormale Datenbankaktivitäten, die in einer einzelnen Datenbank oder in einem Pool für elastische Datenbanken auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rmatchoro
ms.author: ronmat
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 60839fa06d8436d3d2202d1d97735c42c5768e9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60331474"
---
# <a name="azure-sql-database-threat-detection-for-single-or-pooled-databases"></a>Warnungen der Bedrohungserkennung von Azure SQL-Datenbank für Einzel- oder Pooldatenbanken

Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) für Einzel- und Pooldatenbanken identifiziert anomale Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Datenbanken zuzugreifen oder diese missbräuchlich zu verwenden. Mithilfe der Bedrohungserkennung können folgende Aktivitäten identifiziert werden: **potenzielle SQL-Einschleusung**, **Zugriff von einem ungewöhnlichen Standort oder Rechenzentrum**, **Zugriff über einen unbekannten Prinzipal oder eine potenziell schädliche Anwendung** und **Brute-Force-SQL-Anmeldeinformationen**. Weitere Informationen finden Sie im Abschnitt zu den [Warnungen der Bedrohungserkennung](sql-database-threat-detection-overview.md#advanced-threat-protection-alerts).

Sie können sich über [E-Mail-Benachrichtigungen](sql-database-threat-detection-overview.md#explore-anomalous-database-activities-upon-detection-of-a-suspicious-event) oder im [Azure-Portal](sql-database-threat-detection-overview.md#explore-advanced-threat-protection-alerts-for-your-database-in-the-azure-portal) über die erkannten Bedrohungen benachrichtigen lassen.

Die [Bedrohungserkennung](sql-database-threat-detection-overview.md) ist Bestandteil des [Advanced Data Security](sql-database-advanced-data-security.md)-Angebots (ADS). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte SQL-Sicherheitsfunktionen. Der Zugriff auf die Bedrohungserkennung und ihre Verwaltung sind über das zentrale SQL ADS-Portal möglich. Für das Advanced Data Security-Paket werden pro logischem Server 15 USD/Monat berechnet. Dabei sind die ersten 30 Tage kostenlos.

## <a name="set-up-threat-detection-for-your-database-in-the-azure-portal"></a>Einrichten der Bedrohungserkennung für Ihre Datenbank im Azure-Portal

1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com).
2. Navigieren Sie zur Konfigurationsseite des Azure SQL-Datenbank-Servers, den Sie schützen möchten. Wählen Sie in den Sicherheitseinstellungen **Advanced Data Security** aus.
3. Wählen Sie auf der Seite für die **Advanced Data Security**-Konfiguration

   - Aktivieren Sie Advanced Data Security auf dem Server.
   - Geben Sie unter **Einstellungen für Bedrohungserkennung** im Textfeld **Send alerts to** (Warnungen senden an) eine Liste von E-Mail-Adressen an, die Sicherheitswarnungen bei der Erkennung von anomalen Datenbankaktivitäten empfangen sollen.
  
   ![Einrichten der Bedrohungserkennung](./media/sql-database-threat-detection/set_up_threat_detection.png)

## <a name="set-up-threat-detection-using-powershell"></a>Einrichten der Bedrohungserkennung über PowerShell

Ein Skriptbeispiel finden Sie unter [Konfigurieren von Überwachung von SQL-Datenbank und Bedrohungserkennung mit PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Bedrohungserkennung](sql-database-threat-detection-overview.md).
- Erfahren Sie mehr über die [Bedrohungserkennung für die verwaltete Instanz](sql-database-managed-instance-threat-detection.md).  
- Erfahren Sie mehr über [Advanced Data Security](sql-database-advanced-data-security.md).
- Weitere Informationen zur [Überwachung](sql-database-auditing.md).
- Weitere Informationen zu [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro).
- Weitere Informationen zu den Preisen finden Sie unter [SQL-Datenbank: Preise](https://azure.microsoft.com/pricing/details/sql-database/).  
