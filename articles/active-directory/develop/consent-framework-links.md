---
title: Funktionsweise der Anwendungszustimmung | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Funktionsweise des Azure AD-Zustimmungsframeworks und dessen Verwendung beim Entwickeln von Anwendungen in Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766b7572ed54cc194dc28fce1ad7e4979f1af5a5
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540135"
---
# <a name="how-application-consent-works"></a>Funktionsweise der Anwendungszustimmung

In diesem Artikel erfahren Sie, wie das Azure AD-Zustimmungsframework funktioniert, das Sie für eine effizientere Entwicklung von Anwendungen verwenden können.

## <a name="recommended-documents"></a>Empfohlene Dokumente

- Eine allgemeine Übersicht über die [Steuerung des Anwendungszugriffs auf Ressourcen durch einen Ressourcenbesitzer mittels Zustimmung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#consent)
- Eine ausführliche Übersicht über die [Zustimmungsimplementierung mit dem Azure AD-Zustimmungsframework](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- Ausführlichere Informationen zur [Verwendung des Zustimmungsframeworks durch mehrinstanzenfähige Anwendungen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) zur Implementierung von Benutzer- und Administratorzustimmung mit Unterstützung für erweiterte Anwendungsmuster mit mehreren Ebenen
- Ausführlichere Informationen zur [OAuth 2.0-Protokollunterstützung für Zustimmung während des Flows zum Gewähren des Autorisierungscodes](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code)

## <a name="next-steps"></a>Nächste Schritte
[Azure AD bei Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
