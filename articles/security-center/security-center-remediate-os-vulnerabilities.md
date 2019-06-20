---
title: Korrigieren von Sicherheitskonfigurationen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument wird erläutert, wie Sie die Azure Security Center-Empfehlung „Sicherheitskonfigurationen korrigieren“ umsetzen.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f4558c6fdb1e5e4f0ffb7a4b4fdb1ab62eb4cfa9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60332946"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Korrigieren von Sicherheitskonfigurationen in Azure Security Center
Azure Security Center analysiert täglich das Betriebssystem Ihrer virtuellen Computer (VMs) und physischen Computer auf Konfigurationen, die sie möglicherweise anfälliger für Angriffe machen. Security Center empfiehlt die Behandlung von Sicherheitsrisiken, wenn die Betriebssystemkonfiguration nicht den empfohlenen Sicherheitskonfigurationsregeln entspricht, und empfiehlt Konfigurationsänderungen, um diese Sicherheitsrisiken zu beseitigen.

Weitere Informationen zu den spezifischen Konfigurationen, die überwacht werden, finden Sie in der [Liste mit den empfohlenen Konfigurationsregeln](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Wie Sie Sicherheitskonfigurationsbewertungen anpassen, erfahren Sie unter [Anpassen von Sicherheitskonfigurationen von Betriebssystemen in Azure Security Center [Vorschau]](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementieren der Empfehlung
„Sicherheitskonfigurationen korrigieren“ wird in Security Center als Empfehlung angezeigt. Die Empfehlung finden Sie unter **Empfehlungen** > **Compute und Apps**.

In diesem Beispiel betrachten wir unter **Compute und Apps** die Empfehlung „Sicherheitskonfigurationen korrigieren“.
1. Wählen Sie im linken Bereich in Security Center die Option **Compute und Apps** aus.  
   Das Fenster **Compute und Apps** wird geöffnet.

   ![Optimieren von Sicherheitskonfigurationen][1]

2. Wählen Sie **Sicherheitskonfigurationen korrigieren** aus.  
   Das Fenster **Sicherheitskonfigurationen** wird geöffnet.

   ![Das Fenster „Sicherheitskonfigurationen“][2]

   Der obere Abschnitt des Dashboards zeigt Folgendes:

   - **Regeln mit Fehlern nach Schweregrad**: Die Gesamtanzahl von Regeln (nach Schweregrad), denen die Betriebssystemkonfiguration Ihrer virtuellen und physischen Computer nicht entspricht.
   - **Regeln mit Fehlern nach Typ**: Die Gesamtanzahl von Regeln (nach Typ), denen die Betriebssystemkonfiguration Ihrer virtuellen und physischen Computer nicht entspricht.
   - **Fehlerhafte Windows-Regeln**: Die Gesamtanzahl von Regelfehlern, aufgeschlüsselt nach Windows-Betriebssystemkonfigurationen.
   - **Fehlerhafte Linux-Regeln**: Die Gesamtanzahl von Regelfehlern, aufgeschlüsselt nach Linux-Betriebssystemkonfigurationen.

   Im unteren Bereich des Dashboards werden alle Regelfehler für Ihre virtuellen und physischen Computer sowie der Schweregrad des fehlenden Updates aufgeführt. Die Liste enthält die folgenden Elemente:

   - **CCEID**: Der eindeutige CCE-Bezeichner für die Regel. Security Center verwendet die Common Configuration Enumeration (CCE), um Konfigurationsregeln eindeutige Bezeichner zuzuweisen.
   - **Name**: Der Name des Regelfehlers.
   - **Regeltyp**: Der Regeltyp *Registrierungsschlüssel*, *Sicherheitsrichtlinie*, *Überwachungsrichtlinie* oder *IIS*.
   - **Anzahl von VMs und Computern**: Die Gesamtzahl virtueller und physischer Computer mit diesem Regelfehler.
   - **Schweregrad der Regel**: Der CCE-Schweregrad *Kritisch*, *Wichtig* oder *Warnung*.
   - **Status**: Der aktuelle Status der Empfehlung:

     - **Offen**: Die Empfehlung wurde noch nicht umgesetzt.
     - **In Bearbeitung**: Die Empfehlung wird derzeit auf die Ressourcen angewendet, und es ist keine Aktion Ihrerseits erforderlich.
     - **Aufgelöst**: Die Empfehlung wurde angewendet. Wenn das Problem behoben wurde, wird der Eintrag abgeblendet dargestellt.

3. Um die Details eines Regelfehlers anzuzeigen, wählen Sie diesen in der Liste aus.

   ![Detailansicht einer fehlerhaften Konfigurationsregel][3]

   In der Detailansicht werden die folgenden Informationen angezeigt:

   - **Name**: Der Name der Regel.
   - **CCIED**: Der eindeutige CCE-Bezeichner für die Regel.
   - **Betriebssystemversion**: Die Betriebssystemversion des virtuellen oder physischen Computers.
   - **Schweregrad der Regel**: Der CCE-Schweregrad *Kritisch*, *Wichtig* oder *Warnung*.
   - **Vollständige Beschreibung**: Die Beschreibung der Regel.
   - **Sicherheitsrisiko**: Die Erläuterung des Sicherheitsrisikos, wenn die Regel nicht angewendet wird.
   - **Potenzielle Auswirkung**: Geschäftliche Auswirkung, wenn die Regel angewendet wird.
   - **Gegenmaßnahme**: Die Schritte zur Bereinigung.
   - **Erwarteter Wert**: Der erwartete Wert, wenn Security Center die Betriebssystemkonfiguration Ihres virtuellen Computers mit der Regel abgleicht.
   - **Tatsächlicher Wert**: Der zurückgegebene Wert, nachdem Security Center die Betriebssystemkonfiguration Ihres virtuellen Computers mit der Regel abgeglichen hat.
   - **Regelvorgang**: Von Security Center verwendeter Regelvorgang, wenn Security Center die Betriebssystemkonfiguration Ihres virtuellen Computers mit der Regel abgleicht.

4. Klicken Sie oben in der Detailansicht auf die Option **Suche**.  
   Die Suche wird mit einer Liste der Arbeitsbereiche geöffnet, die virtuelle und physische Computer mit den ausgewählten nicht übereinstimmenden Sicherheitskonfigurationen enthalten. Das Blatt für die Arbeitsbereichauswahl wird nur angezeigt, wenn die ausgewählte Regel für mehrere virtuelle Computer gilt, die mit unterschiedlichen Arbeitsbereichen verbunden sind.

   ![Aufgeführte Arbeitsbereiche][4]

5. Wählen Sie einen Arbeitsbereich aus.  
   Eine für den Arbeitsbereich mit den nicht übereinstimmenden Sicherheitskonfigurationen gefilterte Suchabfrage aus Azure Monitor-Protokolle wird geöffnet.

   ![Arbeitsbereich mit Betriebssystem-Sicherheitsrisiko][5]

6. Wählen Sie in der Liste einen Computer aus.  
   Ein neues Suchergebnis wird geöffnet, das gefilterte Informationen für diesen speziellen Computer enthält.

   ![Detaillierte Informationen zum ausgewählten Computer][6]

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde gezeigt, wie Sie die Security Center-Empfehlung „Sicherheitskonfigurationen korrigieren“ umsetzen. Wie Sie Sicherheitskonfigurationsbewertungen anpassen, erfahren Sie unter [Anpassen von Sicherheitskonfigurationen von Betriebssystemen in Azure Security Center [Vorschau]](security-center-customize-os-security-config.md).

Weitere Informationen zu den spezifischen Konfigurationen, die überwacht werden, finden Sie in der [Liste mit den empfohlenen Konfigurationsregeln](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Security Center verwendet die Common Configuration Enumeration (CCE), um Konfigurationsregeln eindeutige Bezeichner zuzuweisen. Weitere Informationen finden Sie auf der Website zu [CCE](https://nvd.nist.gov/cce/index.cfm).

Weitere Informationen zu Security Center finden Sie in den folgenden Ressourcen:

* Eine Liste mit unterstützten Windows- und Linux-VMs finden Sie unter [Unterstützte Plattformen in Azure Security Center](security-center-os-coverage.md).
* Anweisungen zum Konfigurieren von Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen finden Sie unter [Festlegen von Sicherheitsrichtlinien in Azure Security Center](tutorial-security-policy.md).
* Hier erfahren Sie, wie Empfehlungen Ihnen beim Schutz der Azure-Ressourcen helfen: [Verwalten von Sicherheitsempfehlungen in Azure Security Center](security-center-recommendations.md).
* Anweisungen zum Überwachen der Integrität Ihrer Azure-Ressourcen finden Sie unter [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md).
* Erfahren Sie im Artikel [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md), wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* Informationen zum Überwachen des Integritätsstatus Ihrer Partnerlösungen finden Sie unter [Überwachen von Partnerlösungen mit Azure Security Center](security-center-partner-solutions.md).
* Antworten auf häufig gestellte Fragen zur Verwendung des Diensts finden Sie unter [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md).
* Blogbeiträge zur Azure-Sicherheit und -Compliance finden Sie im [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) (Blog zur Azure-Sicherheit).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
