---
title: Funktionsweise der Anwendungszustimmung
description: Erfahren Sie mehr über die Funktionsweise des Azure AD-Zustimmungsframeworks und dessen Verwendung beim Entwickeln von Anwendungen in Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: cd55375f2c5970ff0620e753923f369d40478cef
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052024"
---
# <a name="how-application-consent-works"></a>Funktionsweise der Anwendungszustimmung

In diesem Artikel erfahren Sie, wie das Azure AD-Zustimmungsframework funktioniert, das Sie für eine effizientere Entwicklung von Anwendungen verwenden können.

## <a name="recommended-documents"></a>Empfohlene Dokumente

- Eine allgemeine Übersicht über die [Steuerung des Anwendungszugriffs auf Ressourcen durch einen Ressourcenbesitzer mittels Zustimmung](./developer-glossary.md#consent)
- Eine ausführliche Übersicht über die [Zustimmungsimplementierung mit dem Azure AD-Zustimmungsframework](./quickstart-register-app.md)
- Ausführlichere Informationen zur [Verwendung des Zustimmungsframeworks durch mehrinstanzenfähige Anwendungen](./howto-convert-app-to-be-multi-tenant.md) zur Implementierung von Benutzer- und Administratorzustimmung mit Unterstützung für erweiterte Anwendungsmuster mit mehreren Ebenen
- Ausführlichere Informationen zur [OAuth 2.0-Protokollunterstützung für Zustimmung während des Flows zum Gewähren des Autorisierungscodes](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Nächste Schritte
[Azure AD bei Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)