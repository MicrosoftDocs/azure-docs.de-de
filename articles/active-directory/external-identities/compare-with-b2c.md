---
title: Vergleichen von Externe Identitäten – Azure Active Directory | Microsoft-Dokumentation
description: Mit Externe Azure AD-Identitäten können Personen außerhalb Ihrer Organisation auf Ihre Apps und Ressourcen unter Verwendung ihrer eigenen Identität zugreifen. Vergleichen Sie Lösungen für Externe Identitäten, einschließlich Azure Active Directory B2B-Zusammenarbeit und Azure AD B2C.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 10/23/2020
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6be0720b7eafb251b262d5cd182bdf06e201402d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173072"
---
# <a name="what-are-external-identities-in-azure-active-directory"></a>Worum handelt es sich bei „Externe Identitäten“ in Azure Active Directory?

Mit „Externe Identitäten“ in Azure AD können Sie Personen außerhalb Ihrer Organisation den Zugriff auf Ihre Apps und Ressourcen erlauben. Gleichzeitig können sie sich mit der von ihnen bevorzugten Identität anmelden. Ihre Partner, Vertriebspartner, Lieferanten, Anbieter und andere Gastbenutzer können „ihre eigenen Identitäten einbringen“. Sie können ihre eigenen Anmeldeinformationen für die Anmeldung verwenden – unabhängig davon, ob sie über eine digitale Unternehmensidentität, über eine behördlich ausgegebene digitale Identität oder über eine nicht verwaltete Social Media-Identität wie Google oder Facebook verfügen. Der Identitätsanbieter des externen Benutzers verwaltet dessen Identität, und Sie verwalten den Zugriff auf Ihre Apps mit Azure AD, um Ihre Ressourcen zu schützen.

## <a name="external-identities-scenarios"></a>Szenarien für Externe Identitäten

Bei externen Azure AD-Identitäten steht weniger die Beziehung eines Benutzers zu Ihrer Organisation im Mittelpunkt, sondern eher, wie sich der Benutzer bei Ihren Apps und Ressourcen anmelden möchte. Innerhalb dieses Frameworks unterstützt Azure AD eine Vielzahl von Szenarien – von der B2B-Zusammenarbeit (Business-to-Business) bis hin zur Zugriffsverwaltung für consumer-, kunden- oder bürgerorientierte Anwendungen (Business-to-Consumer oder B2C).

- **Freigeben Ihrer Apps und Ressourcen für externe Benutzer (B2B-Zusammenarbeit):** Laden Sie externe Benutzer in Ihren eigenen Mandanten ein. Diesen Gastbenutzern können Sie dann Berechtigungen zuweisen (zur Autorisierung) und ihnen die Verwendung ihrer bereits vorhandenen Anmeldeinformationen ermöglichen (zur Authentifizierung). Benutzer melden sich bei den freigegebenen Ressourcen über einen einfachen Einladungs- und Einlösungsprozess mit ihrem Geschäfts-, Schul- oder Unikonto oder mit einem anderen E-Mail-Konto an. Über die [Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-overview.md) können Sie auch Richtlinien zum [Verwalten des Zugriffs für externer Benutzer](../governance/entitlement-management-external-users.md#how-access-works-for-external-users) konfigurieren. Und dank der verfügbaren [Benutzerflows für die Self-Service-Registrierung (Vorschau)](self-service-sign-up-overview.md) können sich externe Benutzer nun selbst für Anwendungen registrieren. Die Umgebung kann angepasst werden, um die Registrierung mit einer Geschäfts-, Schul- oder Uni-Identität oder mit einer Social Media-Identität (wie Google oder Facebook) zu ermöglichen. Im Rahmen des Registrierungsprozesses können auch zusätzliche Benutzerinformationen erfasst werden. Weitere Informationen finden Sie in der [Azure AD B2B-Dokumentation](index.yml).

- **Erstellen von User Journeys mit einer White-Label-Identitätsverwaltungslösung für consumer- und kundenorientierte Apps (Azure AD B2C):** Unternehmen oder Entwickler, die kundenorientierte Apps erstellen, können diese mithilfe von Azure AD B2C für Millionen von Consumern, Kunden oder Bürgern skalieren. Entwickler können Azure AD als umfassendes CIAM-System (Customer Identity and Access Management, Kundenidentitäts- und Zugriffsverwaltung) für ihre Anwendungen verwenden. Kunden können sich mit einer bereits eingerichteten Identität wie Facebook oder Gmail anmelden. Mit Azure AD B2C können Sie vollständig anpassen und steuern, wie sich Kunden bei Nutzung Ihrer Anwendungen registrieren, anmelden und ihre Profile verwalten. Weitere Informationen finden Sie in der [Azure AD B2C-Dokumentation](../../active-directory-b2c/index.yml).

## <a name="compare-external-identities-solutions"></a>Vergleichen von Lösungen für externe Identitäten

Die folgende Tabelle enthält einen detaillierten Vergleich der Szenarien, die mit externen Azure AD-Identitäten möglich sind.

|   | Zusammenarbeit mit externem Benutzer (B2B) | Zugriff auf consumer-/kundenorientierte Apps (B2C)  |
| ---- | --- | --- |
| **Primäres Szenario** | Zusammenarbeit über Microsoft-Anwendungen (Microsoft 365, Teams usw.) oder über Ihre eigenen Anwendungen (SaaS-Apps, speziell entwickelte Apps usw.).  | Identitäts- und Zugriffsverwaltung für moderne SaaS-Anwendungen oder speziell entwickelte Anwendungen (keine Erstanbieter-Apps von Microsoft).   |
| **Vorgesehen für**    | Zusammenarbeit mit Geschäftspartnern aus externen Organisationen wie Lieferanten, Partner und Zulieferer. Benutzer werden als Gastbenutzer in Ihrem Verzeichnis angezeigt. Diese Benutzer können über verwaltete IT verfügen, dies muss aber nicht zwingend der Fall sein.  | Kunden Ihres Produkts. Diese Benutzer werden in einem separaten Azure AD-Verzeichnis verwaltet.  |
| **Unterstützte Identitätsanbieter** | Externe Benutzer können für die Zusammenarbeit Geschäfts-, Schul- oder Unikonten, eine beliebige E-Mail-Adresse, SAML- und WS-Fed-basierte Identitätsanbieter, Gmail und Facebook verwenden.  | Consumerbenutzer mit lokalen Anwendungskonten (beliebige E-Mail-Adresse oder beliebiger Benutzername), verschiedene unterstützte Social Media-Identitäten sowie Benutzer mit Unternehmensidentität und behördlich ausgegebener Identität per Direktverbund.       |
| **Externe Benutzerverwaltung**   | Externe Benutzer werden im gleichen Verzeichnis verwaltet wie Mitarbeiter, sind aber üblicherweise als Gastbenutzer gekennzeichnet. Gastbenutzer können genau wie Mitarbeiter verwaltet und beispielsweise den gleichen Gruppen hinzugefügt werden.    | Externe Benutzer werden im Azure AD B2C-Verzeichnis verwaltet. Sie werden getrennt vom Verzeichnis für Mitarbeiter und Partner (sofern vorhanden) der Organisation verwaltet.  |
| **Einmaliges Anmelden (Single Sign-On, SSO)**      | Einmaliges Anmelden (Single Sign-On, SSO) bei allen mit Azure AD verbundenen Apps wird unterstützt. Beispielsweise können Sie den Zugriff auf Microsoft 365 oder lokale Apps und auf andere SaaS-Apps wie z. B. Salesforce oder Workday bereitstellen.    | Einmaliges Anmelden bei Kunden-Apps innerhalb der Azure AD B2C-Mandanten wird unterstützt. Einmaliges Anmelden bei Microsoft 365 oder anderen SaaS-Apps von Microsoft wird nicht unterstützt.    |
| **Sicherheitsrichtlinie und Compliance**        | Wird von der Hostorganisation/einladenden Organisation verwaltet (beispielsweise mit [Richtlinien für den bedingten Zugriff](conditional-access.md)). | Wird von der Organisation über bedingten Zugriff und Identity Protection verwaltet.        |
| **Branding**  | Das Branding der Hostorganisation/einladenden Organisation wird verwendet.    | Vollständig anpassbares Branding pro Anwendung oder Organisation.   |
| **Abrechnungsmodell** | [Preise für Azure Active Directory External Identities](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) auf der Grundlage monatlich aktiver Benutzer (Monthly Active Users, MAU). <br>(Siehe auch: [Abrechnungsmodell für externe Identitäten in Azure AD](external-identities-pricing.md)) | [Preise für Azure Active Directory External Identities](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/) auf der Grundlage monatlich aktiver Benutzer (Monthly Active Users, MAU). <br>(Siehe auch: [Abrechnungsmodell für Azure Active Directory B2C](../../active-directory-b2c/billing.md)) |
| **Weitere Informationen** | [Blogbeitrag](https://blogs.technet.microsoft.com/enterprisemobility/2017/02/01/azure-ad-b2b-new-updates-make-cross-business-collab-easy/), [Dokumentation](what-is-b2b.md)                   | [Produktseite](https://azure.microsoft.com/services/active-directory-b2c/), [Dokumentation](../../active-directory-b2c/index.yml)       |

Sichern und verwalten Sie Kunden und Partner über die Grenzen Ihrer Organisation hinaus mit Externe Azure AD-Identitäten.

## <a name="about-multitenant-applications"></a>Informationen zu mehrinstanzenfähigen Anwendungen

Wenn Sie eine App als Dienst bereitstellen und die Benutzerkonten Ihrer Kunden nicht verwalten möchten, ist eine mehrinstanzenfähige App wahrscheinlich die richtige Wahl für Sie. Wenn Sie für andere Azure AD-Mandanten vorgesehene Anwendungen entwickeln, können Sie sie auf Benutzer aus einer einzelnen Organisation (einzelner Mandant) oder auf Benutzer aus einer beliebigen Organisation ausrichten, die bereits über einen Azure AD-Mandanten verfügt (mehrinstanzenfähige Anwendungen). App-Registrierungen in Azure AD sind standardmäßig Registrierungen mit einem einzelnen Mandanten, können aber mehrinstanzenfähig gemacht werden. Diese mehrinstanzenfähige Anwendung wird von Ihnen einmalig bei Ihrer eigenen Azure AD-Instanz registriert. Danach kann die Anwendung von jedem Azure AD-Benutzer aus einer beliebigen Organisation verwendet werden, ohne dass Sie weitere Schritte ausführen müssen. Weitere Informationen finden Sie unter [Verwalten der Identität in mehrinstanzenfähigen Anwendungen](/azure/architecture/multitenant-identity/) sowie unter [Gewusst wie: Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](../develop/howto-convert-app-to-be-multi-tenant.md).

## <a name="next-steps"></a>Nächste Schritte

- [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
- [Informationen zu Azure AD B2C](../../active-directory-b2c/overview.md)