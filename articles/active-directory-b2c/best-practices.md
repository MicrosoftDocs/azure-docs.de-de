---
title: Bewährte Methoden für Azure AD B2C
titleSuffix: Azure AD B2C
description: Empfehlungen und bewährte Methoden für das Arbeiten mit Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: 1c3c3d38ac0d8334f70f681d8ef86c0d6f86ecfa
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750219"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Empfehlungen und bewährte Methoden für Azure Active Directory B2C

Die folgenden bewährten Methoden und Empfehlungen decken einige der wichtigsten Aspekte der Integration von Azure Active Directory (Azure AD) B2C in vorhandene oder neue Anwendungsumgebungen ab.

## <a name="fundamentals"></a>Grundlagen

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| Auswählen von Benutzerflows für die meisten Szenarien | Identity Experience Framework von Azure AD B2C ist die Kernkompetenz des Diensts. Die Richtlinien beschreiben Benutzeroberflächen im Zusammenhang mit der Identität, z.B. Registrierung, Anmeldung oder Profilbearbeitung. Um Ihnen die Einrichtung der gängigsten Identitätsaufgaben zu erleichtern, enthält das Azure AD B2C-Portal vordefinierte und konfigurierbare Richtlinien, die als „Benutzerflows“ bezeichnet werden. Mit Benutzerflows können Sie mit nur wenigen Klicks innerhalb von Minuten tolle Benutzeroberflächen erstellen. [Erfahren Sie, wann Benutzerflows und wann benutzerdefinierte Richtlinien verwendet werden](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| App-Registrierungen | Jede Anwendung (Web- oder native Anwendung) und API, die gesichert ist, muss in Azure AD B2C registriert werden. Wenn eine App sowohl eine Web- als auch eine native Version von iOS und Android umfasst, können Sie sie als eine Anwendung in Azure AD B2C mit derselben Client-ID registrieren. Erfahren Sie, wie Sie [OIDC-, SAML-, Web- und native Apps registrieren](./tutorial-register-applications.md?tabs=applications). Weitere Informationen zu [in Azure Active Directory B2C verwendbaren Anwendungstypen](./application-types.md). |
| Wechsel zum MAU-Abrechnungsmodell | Azure AD B2C ist beim Abrechnungsmodell von monatlich aktiven Authentifizierungen auf monatlich aktive Benutzer (Monthly Active User, MAU) umgestiegen. Die meisten Kunden finden dieses Modell kostengünstig. [Weitere Informationen zum MAU-Abrechnungsmodell](https://azure.microsoft.com/updates/mau-billing/). [Diesen Link speichern](b2clogin.md) |

## <a name="planning-and-design"></a>Planung und Entwurf

Definieren Sie Ihre Anwendungs- und Dienstarchitektur, inventarisieren Sie die aktuellen Systeme, und planen Sie die Migration zu Azure AD B2C.

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| Entwerfen einer End-to-End-Lösung | Berücksichtigen Sie bei der Planung einer Azure AD B2C-Integration alle Abhängigkeiten Ihrer Anwendungen. Beachten Sie alle Dienste und Produkte, die sich derzeit in Ihrer Umgebung befinden oder der Lösung hinzugefügt werden müssen, z. B. Azure Functions, CRM-Systeme (Customer Relationship Management), Azure API Management-Gateway und Speicherdienste. Berücksichtigen Sie die Sicherheit und Skalierbarkeit für alle Dienste. |
| Dokumentieren der Erfahrungen Ihrer Benutzer | Erstellen Sie ausführliche Informationen zu allen User Journeys, die Ihre Kunden in Ihrer Anwendung haben können. Schließen Sie jeden Bildschirm und alle Branchflows ein, die bei der Interaktion mit den Identitäts- und Profilaspekten der Anwendung auftreten können. Berücksichtigen Sie Nutzbarkeit, Barrierefreiheit und Lokalisierung in Ihrer Planung. |
| Auswählen des richtigen Authentifizierungsprotokolls |  Eine Aufschlüsselung der verschiedenen Anwendungsszenarien und ihrer empfohlenen Authentifizierungsabläufe finden Sie unter [Szenarien und unterstützte Authentifizierungsabläufe](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilot eines Proof of Concept (POC) für die End-to-End-Benutzererfahrung | Beginnen Sie mit unseren [Microsoft-Codebeispielen](code-samples.md) und [Communitybeispielen](https://github.com/azure-ad-b2c/samples). |
| Erstellen eines Migrationsplans |Durch die Vorausplanung kann die Migration reibungsloser verlaufen. Weitere Informationen zur [Benutzermigration](user-migration.md).|
| Nutzbarkeit und Sicherheit | Ihre Lösung muss das richtige Gleichgewicht zwischen der Verwendbarkeit der Anwendung und einer akzeptablen Risikostufe für Ihre Organisation finden. |
| Verschieben lokaler Abhängigkeiten in die Cloud | Um eine robuste Lösung zu gewährleisten, sollten Sie vorhandene Anwendungsabhängigkeiten in die Cloud verschieben. |
| Migrieren vorhandener Apps zu b2clogin.com | Die Außerbetriebnahme von login.microsoftonline.com erfolgt für alle Azure AD B2C-Mandanten am 4. Dezember 2020. [Weitere Informationen](b2clogin.md) |
| Verwenden von Identity Protection und bedingtem Zugriff | Verwenden Sie diese Funktionen, um eine deutlich bessere Kontrolle über risikobehaftete Authentifizierungen und Zugriffsrichtlinien zu erhalten. Azure AD B2C Premium P2 ist erforderlich. [Weitere Informationen](conditional-access-identity-protection-overview.md) |

## <a name="implementation"></a>Implementierung

Beachten Sie während der Implementierungsphase die folgenden Empfehlungen.

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| Bearbeiten benutzerdefinierter Richtlinien mit der Azure AD B2C-Erweiterung für Visual Studio Code | Laden Sie Visual Studio Code und diese von der Community erstellte [Erweiterung aus Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) herunter. Obwohl es sich nicht um ein offizielles Microsoft-Produkt handelt, enthält die Azure AD B2C-Erweiterung für Visual Studio Code mehrere Features, die das Arbeiten mit benutzerdefinierten Richtlinien vereinfachen. |
| Informationen zur Problembehandlung von Azure AD B2C | Erfahren Sie, wie Sie [Problembehandlung für benutzerdefinierte Richtlinien](./troubleshoot-custom-policies.md?tabs=applications) während der Entwicklung ausführen. Erfahren Sie, wie ein normaler Authentifizierungsflow aussieht, und verwenden Sie Tools zum Ermitteln von Anomalien und Fehlern. Verwenden Sie beispielsweise [Application Insights](troubleshoot-with-application-insights.md), um Ausgabeprotokolle von User Journeys zu überprüfen. |
| Nutzen der Bibliothek bewährter benutzerdefinierter Richtlinienmuster | Sehen Sie sich [Beispiele](https://github.com/azure-ad-b2c/samples) für mehrere erweiterte Azure AD B2C-User Journeys zu CIAM (Customer Identity and Access Management, Kundenidentitäts- und Zugriffsverwaltung) an. |

## <a name="testing"></a>Testen

Testen und automatisieren Sie Ihre Azure AD B2C-Implementierung.

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| Berücksichtigen globalen Datenverkehrs | Verwenden Sie Datenverkehrsquellen aus unterschiedlichen globalen Adressen, um die Leistungs- und Lokalisierungsanforderungen zu testen. Stellen Sie sicher, dass alle HTMLs, CSS und Abhängigkeiten die Leistungsanforderungen erfüllen können. |
| Funktionale und UI-Tests | Testen Sie die Benutzerflows in einem End-to-End-Szenario. Fügen Sie im Minutentakt synthetische Tests mithilfe von Selenium, VS Web Test usw. hinzu. |
| Penetrationstests | Bevor Sie Ihre Lösung live schalten, führen Sie Penetrationstests durch, um zu überprüfen, ob alle Komponenten sicher sind, einschließlich aller Abhängigkeiten von Drittanbietern. Vergewissern Sie sich, dass Sie Ihre APIs mit Zugriffstoken gesichert und das richtige Authentifizierungsprotokoll für Ihr Anwendungsszenario verwendet haben. Erfahren Sie mehr über [Penetrationstests](../security/fundamentals/pen-testing.md) und die [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B-Tests | Testen Sie Ihre neuen Features mit einer kleinen, zufälligen Gruppe von Benutzern, bevor Sie das allgemeine Rollout ausführen. Mit aktiviertem JavaScript in Azure AD B2C können Sie A/B-Testtools wie Optimizely, Clarity und andere integrieren. |
| Auslastungstests | Azure AD B2C kann skaliert werden, aber Ihre Anwendung kann nur skaliert werden, wenn alle ihre Abhängigkeiten skalierbar sind. Führen Sie Auslastungstests für Ihre APIs und das CDN aus. |
| Drosselung |  Azure AD B2C drosselt Datenverkehr, wenn in kurzer Zeit zu viele Anforderungen von derselben Quelle gesendet werden. Verwenden Sie bei Auslastungstests mehrere Datenverkehrsquellen, und behandeln Sie den `AADB2C90229`-Fehlercode in Ihren Anwendungen ordnungsgemäß. |
| Automation | Verwenden Sie CI/CD-Pipelines (Continuous Integration, Continuous Delivery), um Tests und Bereitstellungen zu automatisieren, z. B. [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Operationen (Operations)

Verwalten Sie Ihre Azure AD B2C-Umgebung.

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| Erstellen mehrerer Umgebungen | Um den Betrieb und die Einführung der Bereitstellung zu erleichtern, sollten Sie separate Umgebungen für Entwicklung, Tests, Vorproduktion und Produktion erstellen. Erstellen Sie Azure AD B2C-Mandanten für jede dieser Umgebungen. |
| Verwenden von Versionskontrolle für benutzerdefinierte Richtlinien | Verwenden Sie ggf. GitHub, Azure Repos oder ein anderes cloudbasiertes Versionskontrollsystem für Ihre benutzerdefinierten Azure AD B2C-Richtlinien. |
| Verwenden der Microsoft Graph-API zur Automatisierung der Verwaltung von B2C-Mandanten | Microsoft Graph-APIs:<br/>Verwalten von [Identity Experience Framework](/graph/api/resources/trustframeworkpolicy?preserve-view=true&view=graph-rest-beta) (benutzerdefinierte Richtlinien)<br/>[Schlüssel](/graph/api/resources/trustframeworkkeyset?preserve-view=true&view=graph-rest-beta)<br/>[Benutzerabläufe](/graph/api/resources/identityuserflow?preserve-view=true&view=graph-rest-beta) |
| Integration in Azure DevOps | Eine [CI/CD-Pipeline](deploy-custom-policies-devops.md) vereinfacht das Verschieben von Code zwischen verschiedenen Umgebungen und stellt Produktionsbereitschaft jederzeit sicher.   |
| Integration in Azure Monitor | [Überwachungsprotokollereignisse](view-audit-logs.md) werden nur sieben Tage lang aufbewahrt. Durch [Integration in Azure Monitor](azure-monitor.md) können Sie die Protokolle entweder zur langfristigen Verwendung speichern oder in SIEM-Drittanbietertools (Security Information & Event Management) integrieren, um Einblicke in Ihre Umgebung zu gewinnen. |
| Einrichten von aktiven Warnungen und Überwachung | [Verfolgen Sie das Benutzerverhalten](./analytics-with-application-insights.md) in Azure AD B2C mithilfe von Application Insights nach. |

## <a name="support-and-status-updates"></a>Support und Statusaktualisierungen
## <a name="todays-support-and-status-updates"></a>Heutiger Support und Statusaktualisierungen
Bleiben Sie auf dem neuesten Stand bezüglich des Zustands des Diensts, und informieren Sie sich über Supportoptionen.

| Bewährte Methode | BESCHREIBUNG |
|--|--|
| [Dienstupdates](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Bleiben Sie mit Azure AD B2C-Produktupdates und -Ankündigungen auf dem neuesten Stand. |
| [Microsoft-Support](support-options.md) | Stellen Sie eine Supportanfrage für technische Azure AD B2C-Probleme. Der Support für die Abrechnungs- und Abonnementverwaltung wird kostenlos bereitgestellt. |
| [Azure-Status](https://status.azure.com/status) | Zeigen Sie den aktuellen Integritätsstatus aller Azure-Dienste an. |