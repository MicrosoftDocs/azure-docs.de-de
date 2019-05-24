---
title: Self-Service-Registrierungsportal für B2B-Zusammenarbeit – Azure Active Directory | Microsoft-Dokumentation
description: Die Azure Active Directory B2B-Zusammenarbeit ermöglicht Geschäftspartnern den gezielten Zugriff auf Ihre Unternehmensanwendungen und unterstützt so Ihre unternehmensübergreifenden Beziehungen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28db43a345fb29a7529136ddfb929f5a48ccb1f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785429"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Self-Service-Anmeldungsportal für Azure AD B2B-Zusammenarbeit

Kunden können mit den integrierten Features, die über das [Azure-Portal](https://portal.azure.com) und den [Anwendungszugriffsbereich](https://myapps.microsoft.com) für Endbenutzer verfügbar gemacht werden, viele Aufgaben erledigen. Unter Umständen müssen Sie jedoch den Onboarding-Workflow für B2B-Benutzer an die Bedürfnisse ihrer Organisation anpassen. Hierzu können Sie die [Einladungs-API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) verwenden.

Als einladende Organisation wissen Sie möglicherweise im Vorfeld nicht, welche externen Mitarbeiter Zugriff auf ihre Ressourcen benötigen. Benutzer von Partnerunternehmen müssen sich selbstständig registrieren können – mit einer Reihe von Richtlinien, die von der einladenden Organisation (sprich: von Ihnen) gesteuert werden. Dieses Szenario lässt sich über die APIs realisieren. Auf GitHub steht ein [entsprechendes Beispielprojekt](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) zur Verfügung.

Dieses GitHub-Projekt zeigt, wie Organisationen mithilfe der APIs eine richtlinienbasierte Self-Service-Registrierungsfunktion für ihre vertrauenswürdigen Partner bereitstellen und Regeln definieren, die bestimmen, auf welche Apps die Partner Zugriff haben. Partnerbenutzer können auf Ressourcen zugreifen, wenn Sie sie benötigen. Der Zugriff ist sicher, und es ist kein manuelles Onboarding durch die einladende Organisation erforderlich. Sie können das Projekt ganz einfach in einem Azure-Abonnement Ihrer Wahl bereitstellen.

## <a name="as-is-code"></a>Unveränderter Code

Dieser Code wird als Beispiel zur Veranschaulichung der Verwendung der Einladungs-API von Azure Active Directory B2B zur Verfügung gestellt. Er muss von Ihrem Entwicklungsteam oder von einem Partner angepasst und überprüft werden, bevor Sie ihn in einem Produktionsszenario bereitstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Lizenzierung der Azure AD B2B-Zusammenarbeit](licensing-guidance.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](faq.md)
