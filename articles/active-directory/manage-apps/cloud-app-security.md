---
title: App-Transparenz und -Steuerung mit Microsoft Cloud App Security
description: Hier erfahren Sie, wie Sie App-Risikostufen identifizieren, Sicherheitsverletzungen und Datenlecks in Echtzeit verhindern und App-Connectors verwenden, um Anbieter-APIs für Transparenz und Governance zu nutzen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e3e0c10a7ec6b42420db30955ae4911bca27cc0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651225"
---
# <a name="cloud-app-visibility-and-control"></a>Sichtbarkeit und Steuerung von Cloud-Apps

Zur optimalen Nutzung von Cloud-Apps und Clouddiensten müssen IT-Teams das richtige Verhältnis zwischen Zugriffsunterstützung und Kontrolle zum Schutz kritischer Daten finden. Microsoft Cloud App Security bietet umfassende Transparenz und Kontrolle über den Datenverkehr sowie ausgeklügelte Analysefunktionen zur Erkennung und Abwehr von Cyberbedrohungen für alle Ihre Clouddienste (sowohl von Microsoft als auch von Drittanbietern).

## <a name="discover-and-manage-shadow-it-in-your-network"></a>Entdecken und Verwalten von Schatten-IT in Ihrem Netzwerk

IT-Administratoren schätzen die Anzahl von Cloud-Apps, die von den Mitarbeitern in ihrer Organisation genutzt werden, im Schnitt auf 30 oder 40. Tatsächlich werden von den Mitarbeitern in Ihrer Organisation jedoch im Schnitt über 1.000 separate Apps verwendet. Schatten-IT hilft Ihnen bei der Ermittlung und Identifizierung der verwendeten Apps und Ihrer Risikostufe. 80 Prozent der Mitarbeiter verwenden nicht sanktionierte Apps, die von niemandem überprüft wurden und möglicherweise nicht Ihren Sicherheits- und Compliancerichtlinien entsprechen. Und da Ihre Mitarbeiter von außerhalb Ihres Unternehmensnetzwerks auf Ihre Ressourcen und Apps zugreifen können, reichen Firewallregeln und -richtlinien nicht mehr aus.

Mit Microsoft Cloud App Discovery (einem Azure Active Directory Premium P1-Feature) können Sie die verwendeten Apps ermitteln, die Risiken dieser App untersuchen, Richtlinien zur Identifizierung neuer riskanter Apps konfigurieren und die Sanktionierung dieser Apps aufheben, um sie nativ mithilfe Ihrer Proxy- oder Firewallappliance zu blockieren.

- Erkennen und Identifizieren von Schatten-IT
- Auswerten und Analysieren
- Verwalten Ihrer Apps
- Erweiterte Berichte für die Schatten-IT-Ermittlung
- Steuern sanktionierter Apps
 
### <a name="learn-more"></a>Weitere Informationen

- [Tutorial: Erkennen und Verwalten von Schatten-IT in Ihrem Netzwerk](/cloud-app-security/tutorial-shadow-it)
- [Arbeiten mit ermittelten Apps](/cloud-app-security/discovered-apps)
 
## <a name="user-session-visibility-and-control"></a>Transparenz und Steuerung von Benutzersitzungen 

Heutzutage reicht es häufig nicht aus, wenn Sie erst im Nachhinein von Vorgängen in Ihrer Cloudumgebung erfahren. Sicherheitsverletzungen und Datenlecks sollten nach Möglichkeit verhindert werden, bevor Mitarbeiter absichtlich oder versehentlich Ihre Daten und Ihre Organisation gefährden. Die dazu nötigen Funktionen werden von Microsoft Cloud App Security in Kombination mit Azure Active Directory (Azure AD) in Form einer ganzheitlichen und integrierten Lösung mit App-Steuerung für bedingten Zugriff bereitgestellt. 

Die Sitzungssteuerung verwendet eine Reverseproxyarchitektur und ist auf einzigartige Weise in den bedingten Azure AD-Zugriff integriert. Der bedingte Azure AD-Zugriff ermöglicht das Erzwingen von Zugriffssteuerungen für die Apps Ihrer Organisation auf der Grundlage bestimmter Bedingungen. Die Bedingungen definieren, auf wen (Benutzer oder Benutzergruppe), auf was (welche Cloud-Apps) und wo (welche Orte und Netzwerke) eine Richtlinie für bedingten Zugriff angewendet wird. Nach Bestimmung der Bedingungen können Sie Benutzer an Cloud App Security weiterleiten, wo Ihre Daten in Echtzeit geschützt werden können.  

Diese Steuerung ermöglicht Folgendes:  
- Steuern von Dateidownloads
- Überwachen von B2B-Szenarien  
- Steuern des Dateizugriffs  
- Schützen von Dokumenten beim Herunterladen  
 
### <a name="learn-more"></a>Weitere Informationen

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
 
## <a name="advanced-app-visibility-and-controls"></a>Erweiterte App-Transparenz und -Steuerung 

App-Connectors nutzen die APIs von App-Anbietern, um für die Apps, mit denen Sie eine Verbindung herstellen, die Transparenz und Steuerung durch Microsoft Cloud App Security zu verbessern. Cloud App Security nutzt die vom Cloudanbieter bereitgestellten APIs. Jeder Dienst hat seine eigenen Framework- und API-Einschränkungen wie etwa Drosselung, API-Grenzwerte und dynamische API-Fenster mit Zeitversatz. Das Cloud App Security-Produktteam hat mit diesen Diensten gearbeitet, um die API-Verwendung zu optimieren und die bestmögliche Leistung zu erzielen. Von den Cloud App Security-Engines wird die maximal zulässige Kapazität genutzt (unter Berücksichtigung verschiedener API-Einschränkungen, die von den Diensten erzwungen werden). Einige Vorgänge wie etwa das Scannen aller Dateien im Mandanten erfordern zahlreiche API-Aufrufe und erstrecken sich daher über einen längeren Zeitraum. Bei einigen Richtlinien ist mit einer Ausführungsdauer von mehreren Stunden oder Tagen zu rechnen. 
 
### <a name="learn-more"></a>Weitere Informationen  

- [Verbinden von Apps](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)

## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Erkennen und Verwalten von Schatten-IT in Ihrem Netzwerk](/cloud-app-security/tutorial-shadow-it)
- [Arbeiten mit ermittelten Apps](/cloud-app-security/discovered-apps)
- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
- [Verbinden von Apps](/cloud-app-security/enable-instant-visibility-protection-and-governance-actions-for-your-apps)