---
title: Azure Active Directory und transaktionsfähige SaaS-Angebote im kommerziellen Marketplace
description: Erfahren Sie, wie transaktionsfähige SaaS-Angebote von Azure Active Directory im kommerziellen Microsoft-Marketplace unterstützt werden.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 674f267d3d99dd22c1ae06b6d32587761d5983ce
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124916"
---
# <a name="azure-ad-and-transactable-saas-offers-in-the-commercial-marketplace"></a>Azure AD und transaktionsfähige SaaS-Angebote im kommerziellen Marketplace

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft, mit dem sich Benutzer anmelden und auf interne und externe Ressourcen zugreifen können. Azure AD hilft, transaktionsfähige SaaS-Angebote im kommerziellen Microsoft-Marketplace für jeden – vom Herausgeber über den Käufer bis hin zum Benutzer – zu vereinfachen und sicherer zu gestalten. Mit Azure AD können Herausgeber die Bereitstellung von Benutzern für Ihre SaaS-Apps (Software-as-a-Service) automatisieren. Gleichzeitig können Käufer diese bereitgestellten Benutzer selbst verwalten. 

Außerdem bietet das [einmalige Anmelden in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md) (Single Sign-On, SSO) Sicherheit und Benutzerfreundlichkeit, wenn sich Benutzer in Azure AD bei Apps anmelden. Die schnellere Einbindung und optimierte Benutzererfahrung stärkt zusätzlich das Vertrauen von Käufern und Benutzern – und zwar direkt bei der ersten Interaktion mit der SaaS-App eines Herausgebers. So entsteht ein positiver Eindruck, der für Transparenz sorgt und Folgegeschäfte fördert.

Die Anleitungen in diesem Artikel sollen Sie bei der Zertifizierung Ihres SaaS-Angebots im kommerziellen Marketplace unterstützen. Weitere Informationen zur Zertifizierung finden Sie in den detaillierten [Zertifizierungsrichtlinien für den kommerziellen Marketplace](/legal/marketplace/certification-policies#100-general), insbesondere in den [spezifischen Richtlinien für SaaS](/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

## <a name="before-you-begin"></a>Voraussetzungen

Wenn Sie Ihr [SaaS-Angebot](./create-new-saas-offer.md) im Partner Center erstellen, wählen Sie aus einer Reihe spezifischer Auflistungsoptionen aus, die in der Angebotsliste angezeigt werden. Ihre Auswahl bestimmt, wie die Transaktion Ihres Angebots im kommerziellen Marketplace abläuft. Über Microsoft verkaufte Angebote werden als transaktionsfähige Angebote bezeichnet. Kunden werden alle transaktionsfähigen Angebote in Ihrem Namen von uns in Rechnung gestellt. Wenn Sie Ihr Angebot über Microsoft vertreiben und Transaktionen in Ihrem Namen von uns hosten lassen möchten (Option **Ja** ), dann haben Sie sich dafür entschieden, ein transaktionsfähiges Angebot zu erstellen. In diesem Fall ist dieser Artikel genau richtig für Sie, und Sie sollten ihn vollständig lesen.

Wenn Sie Ihr Angebot nur im kommerziellen Marketplace auflisten und Transaktionen unabhängig verarbeiten möchten (die Option **Nein** ), können Sie potenziellen Kunden drei Möglichkeiten bieten, auf Ihr Angebot zuzugreifen: „Jetzt abrufen (kostenlos)“, „Kostenlose Testversion“ und „Kontakt mit mir aufnehmen“. Bei Auswahl von **Jetzt abrufen (kostenlos)** oder **Kostenlose Testversion** ist dieser Artikel nicht für Sie geeignet. Informieren Sie sich stattdessen unter [Erstellen der Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot im kommerziellen Marketplace](./azure-ad-free-or-trial-landing-page.md). Bei Auswahl von **Kontakt mit mir aufnehmen** ist der Herausgeber nicht direkt verantwortlich. Fahren Sie mit der Erstellung Ihres Angebots im Partner Center fort.

## <a name="how-azure-ad-works-with-the-commercial-marketplace-for-saas-offers"></a>So unterstützt Azure AD SaaS-Angebote im kommerziellen Marketplace

Azure AD bietet eine nahtlose Erfahrung beim Kauf, der Ausführung und Verwaltung von Lösungen im kommerziellen Marketplace. Abbildung 1 veranschaulicht, wie der Herausgeber, Käufer und Benutzer interagieren, um ein Abonnement zu erwerben und zu aktivieren. Außerdem wird gezeigt, wie Kunden SaaS-Anwendungen nutzen und verwalten, die sie im kommerziellen Marketplace erwerben. In dieser Abbildung entspricht der Käufer dem Benutzer der SaaS-Anwendung, der einen Kauf im kommerziellen Marketplace tätigt.

In Abbildung 1 ist dargestellt, wie die Auswahl Ihres Angebots durch einen Käufer eine Reihe von Workflows für die Einkaufs-, Abonnement- und Benutzerverwaltung auslöst. Innerhalb dieser Abfolge sind Sie als Herausgeber für bestimmte Anforderungen verantwortlich, wobei Microsoft an wichtigen Punkten Unterstützung leistet.

**_Abbildung 1: Verwenden von Azure AD für SaaS-Angebote im kommerziellen Marketplace_*

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow.png" alt-text="Veranschaulicht die Schritte für die Einkaufs-, Abonnement- und (optionale) Benutzerverwaltung.":::

Die folgenden Abschnitte enthalten Details zu den Anforderungen für die einzelnen Schritte.

## <a name="process-steps-for-purchase-management"></a>Schritte für die Einkaufsverwaltung

In dieser Abbildung werden die vier Schritte für die Einkaufsverwaltung veranschaulicht.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-1-4.png" alt-text="Veranschaulicht die vier Schritte für die Einkaufsverwaltung.":::

Diese Tabelle enthält Details zu den Schritten für die Einkaufsverwaltung.

| Schritt | Aktion des Herausgebers | Für Herausgeber empfohlen oder erforderlich |
| ------------ | ------------- | ------------- |
| 1. Der Käufer meldet sich mit seiner Azure-ID-Identität beim kommerziellen Marketplace an und wählt ein SaaS-Angebot aus. | Es ist keine Aktion seitens des Herausgebers erforderlich. | Nicht verfügbar |
| 2. Nach dem Kauf wählt der Käufer die Option *Konto konfigurieren* * im Azure Marketplace oder **Jetzt konfigurieren** in AppSource aus. So wird er zur Landing Page des Herausgebers dieses Angebots weitergeleitet. Der Käufer muss in der Lage sein, sich per einmaligem Anmelden in Azure AD bei der SaaS-Anwendung des Herausgebers anzumelden. Außerdem muss er nur eine minimale Zustimmung geben, ohne dass eine Genehmigung des Azure AD-Administrators erforderlich ist. | Gestalten Sie die [Landing Page](azure-ad-transactable-saas-landing-page.md) für das Angebot so, dass Benutzer mit ihrer Azure-AD- oder MSA-Identität (Microsoft-Konto) empfangen werden und dass zusätzliche Voraussetzungen oder Konfigurationen, die erforderlich sein können, erfüllt werden. | Erforderlich |
| 3. Der Herausgeber fordert Details zum Kauf von der SaaS-Fulfillment-API an. | Rufen Sie mit einem [Zugriffstoken](./partner-center-portal/pc-saas-registration.md), das unter Verwendung der Anwendungs-ID der Landing Page generiert wurde, den [Endpunkt für die Auflösung](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription) auf, um Einzelheiten zum Kauf abzurufen. | Erforderlich |
| 4. Der Herausgeber erfasst über Azure AD und die Microsoft Graph-API Details zum Unternehmen und zum Benutzer, die für die Bereitstellung des Käufers in der SaaS-Anwendung des Herausgebers erforderlich sind.  | Gliedern Sie das Azure AD-Benutzertoken auf, um den Namen und die E-Mail-Adresse zu ermitteln, oder rufen Sie die [Microsoft Graph-API](/graph/use-the-api) auf, um unter Verwendung delegierter Berechtigungen [Informationen zum angemeldeten Benutzer](/graph/api/user-get) abzurufen. | Erforderlich |
||||

## <a name="process-steps-for-subscription-management"></a>Schritte für die Abonnementverwaltung

In dieser Abbildung werden die zwei Schritte für die Abonnementverwaltung veranschaulicht.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-5-6.png" alt-text="Veranschaulicht die zwei Schritte für die Abonnementverwaltung.":::

In dieser Tabelle werden die Schritte für die Abonnementverwaltung ausführlich beschrieben.

| Schritt | Aktion des Herausgebers | Für Herausgeber empfohlen oder erforderlich |
| ------------ | ------------- | ------------- |
| 5. Der Herausgeber verwaltet das Abonnement für die SaaS-Anwendung über die SaaS-Fulfillment-API. | Führen Sie Abonnementänderungen und andere Verwaltungsaufgaben über die [SaaS-Fulfillment-APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md) durch.<br><br>Dieser Schritt erfordert ein Zugriffstoken, wie in Schritt 3 beschrieben. | Erforderlich |
| 6. Bei Verwendung eines getakteten Preismodells gibt der Herausgeber Nutzungsereignisse für die Messungsdienst-API aus. | Wenn für Ihre SaaS-App die nutzungsbasierte Abrechnung verwendet wird, generieren Sie Nutzungsbenachrichtigungen über die [Marketplace-Messungsdienst-APIs](./partner-center-portal/marketplace-metering-service-apis.md).<br><br>Dieser Schritt erfordert ein Zugriffstoken, wie in Schritt 3 beschrieben. | Für Messung erforderlich |
||||

## <a name="process-steps-for-user-management"></a>Schritte für die Benutzerverwaltung

In dieser Abbildung werden die drei Schritte für die Benutzerverwaltung veranschaulicht.

:::image type="content" source="./media/azure-ad-saas/azure-ad-saas-flow-7-9.png" alt-text="Veranschaulicht die drei optionalen Schritte für die Benutzerverwaltung.":::

Die Schritte 7 bis 9 sind optional für die Benutzerverwaltung. Sie bieten Herausgebern, die das einmalige Anmelden in Azure AD (SSO) unterstützen, zusätzliche Vorteile. In dieser Tabelle werden die Schritte für die Benutzerverwaltung ausführlich beschrieben.

| Schritt | Aktion des Herausgebers | Für Herausgeber empfohlen oder erforderlich |
| ------------ | ------------- | ------------- |
| 7. Azure AD-Administratoren im Unternehmen des Käufers können optional den Benutzer- und Gruppenzugriff über Azure AD verwalten. | Wenn das einmalige Anmelden in Azure AD für Benutzer eingerichtet ist, ist zur Aktivierung kein Eingreifen des Herausgebers erforderlich (Schritt 9). | Nicht verfügbar |
| 8. Der Azure AD-Bereitstellungsdienst kommuniziert Änderungen zwischen Azure AD und der SaaS-Anwendung des Verlegers. | [Implementieren Sie einen SCIM-Endpunkt](../active-directory/app-provisioning/use-scim-to-provision-users-and-groups.md), um sich von Azure AD informieren zu lassen, wenn Benutzer hinzugefügt und entfernt werden. | Empfohlen |
| 9. Nachdem die App genehmigt und bereitgestellt wurde, können Benutzer aus dem Unternehmen des Käufers das einmalige Anmelden in Azure AD nutzen, um sich bei der SaaS-Anwendung des Herausgebers anzumelden. | [Verwenden Sie das einmalige Anmelden in Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md), um Benutzern die einmalige Anmeldung mit einem einzigen Konto bei der SaaS-Anwendung des Herausgebers zu ermöglichen. | Empfohlen |
||||

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen der Landing Page für Ihr transaktionsfähiges SaaS-Angebot im kommerziellen Marketplace](azure-ad-transactable-saas-landing-page.md)
- [Erstellen der Landing Page für Ihr kostenloses SaaS-Angebot oder Ihr SaaS-Testangebot im kommerziellen Marketplace](azure-ad-free-or-trial-landing-page.md)
- [Erstellen eines SaaS-Angebots im kommerziellen Marketplace](create-new-saas-offer.md)