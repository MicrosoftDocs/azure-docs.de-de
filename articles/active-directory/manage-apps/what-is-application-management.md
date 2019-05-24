---
title: Verwalten von Anwendungen mit Azure Active Directory | Microsoft Docs
description: Dieser Artikel erläutert die Vorteile der Integration von Azure Active Directory in lokale, Cloud- und SaaS-Anwendungen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 657527272ccf8b5f69764052a2385ceec57ddc03
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826035"
---
# <a name="application-management-with-azure-active-directory"></a>Anwendungsverwaltung mit Azure Active Directory

Azure Active Directory (Azure AD) bietet einen sicheren und nahtlosen Zugriff auf die Cloud und lokale Anwendungen. Benutzer müssen sich nur einmal anmelden, um auf Office 365 und andere Geschäftsanwendungen von Microsoft, Software-as-a-Service-Anwendungen (SaaS), lokale Anwendungen und branchenspezifische Apps zuzugreifen. Senken Sie Verwaltungskosten durch die Automatisierung der Benutzerbereitstellung. Verwenden Sie die mehrstufige Authentifizierung und Richtlinien für bedingten Zugriff, um sicheren Zugriff auf Anwendungen zu ermöglichen.

![Apps, die per Azure AD-Verbund verknüpft sind](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Gründe für die Verwaltung von Anwendungen mit einer Cloudlösung

Organisationen besitzen häufig Hunderte von Anwendungen, die für die Arbeit ihrer Benutzer unerlässlich sind. Der Benutzerzugriff auf diese Anwendungen erfolgt über eine Vielzahl von Geräten und Standorten. Tagtäglich werden neue Anwendungen hinzugefügt und entwickelt. Angesichts dieser Vielzahl an Anwendungen und Zugriffspunkte gewinnt die Verwaltung des Benutzerzugriffs auf alle Anwendungen über eine cloudbasierte Lösung zunehmend an Bedeutung.

## <a name="manage-risk-with-conditional-access-policies"></a>Risikomanagement durch Richtlinien für bedingten Zugriff
In Kombination mit der einmaligen Anmeldung (Single Sign-On, SSO) mit Azure AD bieten Richtlinien für bedingten Zugriff ein hohes Maß an Sicherheit für den Zugriff auf Anwendungen. Zu den Sicherheitsfunktionen zählen der cloudgestützte Identitätsschutz, die risikobasierte Zugriffssteuerung, die native mehrstufige Authentifizierung und Richtlinien für bedingten Zugriff. Durch diese Funktionen können präzise Steuerungsrichtlinien basierend auf Anwendungen oder Gruppen geschaffen werden, die ein höheres Maß an Sicherheit erfordern.

## <a name="improve-productivity-with-single-sign-on"></a>Produktivitätssteigerung durch einmaliges Anmelden
Die Aktivierung des einmaligen Anmeldens (Single Sign-On, SSO) für verschiedenste Anwendungen und in Office 365 bietet eine optimierte Anmeldung für bestehende Benutzer, da hierdurch die Anzahl von Anmeldeeingabeaufforderungen reduziert wird oder diese gänzlich beseitigt werden. Ohne die Vielzahl an Eingabeaufforderungen oder die Verwaltung mehrerer Kennwörter wirkt die Umgebung des Benutzers ganzheitlicher und weniger verwirrend. Die Unternehmensgruppe kann Zugriffe über Self-Service-Funktionen und dynamische Mitgliedschaften verwalten und genehmigen. Indem Sie den richtigen Personen im Unternehmen die Verwaltung des Zugriffs auf eine Anwendung ermöglichen, erhöhen Sie die Sicherheit des Identitätssystems.

SSO erhöht die Sicherheit. *Ohne einmaliges Anmelden* müssen Administratoren Benutzerkonten für jede einzelne Anwendung erstellen und aktualisieren, was viel Zeit in Anspruch nimmt. Darüber hinaus müssen Benutzer mehrere Sätze von Anmeldeinformationen für den Zugriff auf ihre Anwendungen nachverfolgen. Die Folge: Benutzer neigen dazu, ihre Kennwörter aufzuschreiben oder andere Lösungen zur Kennwortverwaltung zu verwenden, was zu Risiken für die Datensicherheit führt. 

## <a name="address-governance-and-compliance"></a>Beheben von Governance- und Complianceproblemen
Mit Azure AD können Sie Anmeldungen bei Anwendungen durch Berichte überwachen, die SIEM-Tools (Security Information & Event Monitoring) nutzen. Sie können über das Portal oder über APIs auf die Berichte zugreifen. Lassen Sie programmgesteuert überwachen, wer auf Ihre Anwendungen zugreifen darf, und entfernen Sie den Zugriff für inaktive Benutzer durch Zugriffsüberprüfungen.

## <a name="manage-costs"></a>Verwalten von Kosten
Durch eine Migration zu Azure AD können Sie Ihre Kosten senken und sich den Aufwand der Verwaltung Ihrer lokalen Infrastruktur ersparen. Azure AD bietet außerdem Self-Service-Zugriff auf Anwendungen, was sowohl Administratoren als auch Benutzern Zeit erspart. Einmaliges Anmelden macht anwendungsspezifische Kennwörter überflüssig. Diese Option für die einmalige Anmeldung spart Kosten für das Zurücksetzen von Kennwörtern für Anwendungen und vermeidet Produktivitätsverluste durch das Abrufen von Kennwörtern.

