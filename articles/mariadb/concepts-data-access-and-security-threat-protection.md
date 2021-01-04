---
title: Advanced Threat Protection – Azure Database for MariaDB
description: Advanced Threat Protection erkennt anomale Datenbankaktivitäten, die auf potenzielle Sicherheitsrisiken für die Datenbank hindeuten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 018ba33271f8a3a81c3b7109607334b076d8609c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95977025"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB: Advanced Threat Protection

Advanced Threat Protection für Azure Database for MariaDB erkennt Anomalien bei Aktivitäten, die auf ungewöhnliche und potenziell schädliche Versuche hinweisen, auf Ihre Datenbanken zuzugreifen oder diese missbräuchlich zu nutzen.

> [!IMPORTANT]
> Advanced Threat Protection befindet sich in der Public Preview.

Advanced Threat Protection ist Teil des Angebots „Advanced Data Security“ (Erweiterte Datensicherheit). Dabei handelt es sich um ein vereinheitlichtes Paket für erweiterte Sicherheitsfunktionen. Sie können über das [Azure-Portal](https://portal.azure.com) auf den Advanced Threat Protection-Dienst zugreifen und diesen verwalten. Das Feature ist für universelle und arbeitsspeicheroptimierte Server verfügbar.

> [!NOTE]
> Das Advanced Threat Protection-Feature ist in den folgenden Azure Government- und Sovereign Cloud-Regionen **nicht** verfügbar: „US Gov Texas“, „US Gov Arizona“, „US Gov Iowa“, „US Gov Virginia“, „US DoD, Osten“, „US DoD, Mitte“, „Deutschland, Mitte“, „Deutschland, Norden“, „China, Osten“, „China, Osten 2“. Informationen zur allgemeinen Produktverfügbarkeit finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="what-is-advanced-threat-protection"></a>Was ist Advanced Threat Protection?

Advanced Threat Protection für Azure Database for MariaDB bietet eine neue Sicherheitsebene, die es den Kunden ermöglicht, auf erkannte potenzielle Bedrohungen zu reagieren. Zu diesem Zweck werden Sicherheitswarnungen zu anomalen Aktivitäten bereitgestellt. Benutzer erhalten Warnungen zu verdächtigen Datenbankaktivitäten und potenziellen Sicherheitsrisiken sowie zu anomalen Datenbankzugriffs- und -abfragemustern. Advanced Threat Protection für Azure Database for MariaDB integriert Warnungen in [Azure Security Center](https://azure.microsoft.com/services/security-center/). Dabei werden Detailinformationen zu verdächtigen Aktivitäten und empfohlene Maßnahmen zur Untersuchung und Entschärfung der Bedrohung bereitgestellt. Advanced Threat Protection für Azure Database for MariaDB vereinfacht den Umgang mit potenziellen Bedrohungen für die Datenbank, ohne das Fachwissen eines Sicherheitsexperten besitzen oder komplexe Sicherheitsüberwachungssysteme verwalten zu müssen. 

![Advanced Threat Protection-Konzept](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Advanced Threat Protection-Warnungen 
Advanced Threat Protection für Azure Database for MariaDB erkennt anomale Aktivitäten, die auf ungewöhnliche und möglicherweise schädliche Versuche hinweisen, bei denen auf Datenbanken zugegriffen oder diese missbräuchlich verwendet werden sollen. Hierbei können die folgenden Warnungen ausgelöst werden:
- **Zugriff von einem ungewöhnlichen Ort:** Diese Warnung wird ausgelöst, wenn sich das Zugriffsmuster für Azure Database for MariaDB-Server geändert hat, weil sich eine Person von einem ungewöhnlichen geografischen Standort aus beim Azure Database for MariaDB-Server angemeldet hat. In einigen Fällen erkennt die Warnung eine legitime Aktion (eine neue Anwendung oder Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über ein ungewöhnliches Azure-Rechenzentrum:** Diese Warnung wird ausgelöst, wenn sich das Zugriffsmuster für den Azure Database for MariaDB-Server geändert hat, weil  im letzten Überwachungszeitraum eine Serveranmeldung über ein ungewöhnliches Azure-Rechenzentrum festgestellt wurde. In einigen Fällen erkennt die Warnung eine legitime Aktion (eine neue Anwendung in Azure, Power BI). In anderen Fällen erkennt die Warnung ggf. eine schädliche Aktion einer Azure-Ressource bzw. eines -Diensts (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über einen unbekannten Prinzipal:** Diese Warnung wird ausgelöst, wenn sich das Zugriffsmuster des Azure Database for MariaDB-Servers ändert, weil sich eine Person über einen ungewöhnlichen Prinzipal beim Server angemeldet hat (Azure Database for MariaDB-Benutzer). In einigen Fällen erkennt die Warnung eine legitime Aktion (neue Anwendung, Wartungsarbeiten von Entwicklern). In anderen Fällen erkennt die Warnung eine schädliche Aktion (ehemaliger Mitarbeiter, externer Angreifer).
- **Zugriff über eine potenziell schädliche Anwendung:** Diese Warnung wird ausgelöst, wenn zum Zugreifen auf die Datenbank eine potenziell schädliche Anwendung verwendet wird. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Angriff mit allgemeinen Angriffstools.
- **Brute-Force-Angriffe auf Azure Database for MariaDB-Anmeldeinformationen**: Diese Warnung wird ausgelöst, wenn eine ungewöhnlich hohe Anzahl von fehlerhaften Anmeldungen mit unterschiedlichen Anmeldeinformationen vorliegt. In einigen Fällen erkennt die Warnung aktive Eindringversuche. In anderen Fällen erkennt die Warnung einen Brute-Force-Angriff.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Security Center](../security-center/security-center-introduction.md)
* Weitere Informationen zu Tarifen finden Sie unter [Azure Database for MariaDB – Preise](https://azure.microsoft.com/pricing/details/mariadb/). 
* Konfigurieren von [Azure Database for MariaDB: Advanced Threat Protection](howto-database-threat-protection-portal.md) mit dem Azure-Portal