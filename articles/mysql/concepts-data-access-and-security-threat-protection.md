---
title: 'Advanced Threat Protection: Azure Database for MySQL'
description: Informationen zu den Konzepten von Advanced Threat Protection zum Erkennen anomaler Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hinweisen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: c9e884d153e85e7b68dee38494ac5d6f4271978a
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542574"
---
# <a name="azure-database-for-mysql-advanced-threat-protection"></a>Azure Database for MySQL: Advanced Threat Protection

Advanced Threat Protection für Azure Database for MySQL erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese zu nutzen.

> [!NOTE]
> Advanced Threat Protection befindet sich in der Public Preview.

Advanced Threat Protection ist Teil des Angebots „Advanced Data Security“ (Erweiterte Datensicherheit). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte Sicherheitsfunktionen. Sie können über das [Azure-Portal](https://portal.azure.com) oder mithilfe der [REST-API](/rest/api/mysql) auf den Advanced Threat Protection-Dienst zugreifen und diesen verwalten. Das Feature ist für universelle und arbeitsspeicheroptimierte Server verfügbar.

> [!NOTE]
> Das Advanced Threat Protection-Feature ist in den folgenden Azure Government- und Sovereign Cloud-Regionen **nicht** verfügbar: „US Gov Texas“, „US Gov Arizona“, „US Gov Iowa“, „US Gov Virginia“, „US DoD, Osten“, „US DoD, Mitte“, „Deutschland, Mitte“, „Deutschland, Norden“, „China, Osten“, „China, Osten 2“. Informationen zur allgemeinen Produktverfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="what-is-advanced-threat-protection"></a>Was ist Advanced Threat Protection?

Advanced Threat Protection für Azure Database for MySQL bietet eine neue Sicherheitsebene, die es den Kunden ermöglicht, auf erkannte potenzielle Bedrohungen zu reagieren. Zu diesem Zweck werden Sicherheitswarnungen zu anomalen Aktivitäten bereitgestellt. Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten und potenziellen Sicherheitsrisiken sowie zu anomalen Datenbankzugriffs- und -abfragemustern. Advanced Threat Protection für Azure Database for MySQL integriert Warnungen in [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dabei werden Detailinformationen zu verdächtigen Aktivitäten und empfohlene Maßnahmen zur Untersuchung und Abwendung der Bedrohung bereitgestellt. Advanced Threat Protection für Azure Database for MySQL vereinfacht den Umgang mit potenziellen Bedrohungen für die Datenbank, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen. 

:::image type="content" source="media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png" alt-text="Advanced Threat Protection-Konzept":::

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection-Warnungen 
Advanced Threat Protection für Azure Database for MySQL erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese missbraucht werden sollen. Hierbei können die folgenden Warnungen ausgelöst werden:
- **Zugriff von einem ungewöhnlichen Ort**: Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters beim Azure Database for MySQL-Server erfolgt, bei der sich eine Person von einem ungewöhnlichen geografischen Standort aus beim Azure Database for MySQL-Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (eine neue Anwendung oder Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff aus einem ungewöhnlichen Azure-Rechenzentrum**: Diese Warnung wird ausgelöst, wenn sich das Zugriffsmuster für den Azure Database for MySQL-Server geändert hat, weil sich eine Person über ein ungewöhnliches Azure-Rechenzentrum beim Server angemeldet hat, und dies auf diesem Server während des letzten Zeitraums aufgefallen ist ist. In einigen Fällen erkennt die Warnung eine legitime Aktion (Ihre neue Anwendung in Azure, Power BI, Azure Database for MySQL-Abfrage-Editor usw.). In anderen Fällen erkennt die Warnung ggf. eine schädliche Aktion einer Azure-Ressource bzw. eines -Diensts (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über einen ungewöhnlichen Prinzipal**: Diese Warnung wird ausgelöst, wenn eine Änderung des Zugriffsmusters des Azure Database for MySQL-Servers erfolgt, weil sich eine Person über einen ungewöhnlichen Prinzipal beim Server angemeldet hat (Azure Database for MySQL-Benutzer). In einigen Fällen erkennt die Warnung eine legitime Aktion (neue Anwendung, Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über eine potenziell schädliche Anwendung:** Diese Warnung wird ausgelöst, wenn zum Zugreifen auf die Datenbank eine potenziell schädliche Anwendung verwendet wird. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Angriff mit allgemeinen Angriffstools.
- **Brute-Force-Angriff auf Azure Database for MySQL-Anmeldeinformationen**: Diese Warnung wird ausgelöst, wenn eine ungewöhnlich hohe Zahl von fehlgeschlagenen Anmeldungen mit unterschiedlichen Anmeldeinformationen vorliegt. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Brute-Force-Angriff.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Security Center](../security-center/security-center-introduction.md)
* Weitere Informationen zu Tarifen finden Sie unter [Azure-Datenbank for MySQL – Preise](https://azure.microsoft.com/pricing/details/mysql/). 
* Konfigurieren von [Azure Database for MySQL Advanced Threat Protection](howto-database-threat-protection-portal.md) mit dem Azure-Portal