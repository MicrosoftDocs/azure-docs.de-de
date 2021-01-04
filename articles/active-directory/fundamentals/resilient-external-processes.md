---
title: Robuste Schnittstellen mit externen Prozessen mithilfe von Azure AD B2C | Microsoft-Dokumentation
description: Methoden zum Erstellen robuster Schnittstellen mit externen Prozessen
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96856c988cae891e64ddf460d61851102e4666c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919032"
---
# <a name="resilient-interfaces-with-external-processes"></a>Robuste Schnittstellen mit externen Prozessen

In diesem Artikel erfahren Sie, wie Sie die RESTFul-APIs in der User Journey planen und implementieren und die Anwendung robuster gegen API-Ausfälle gestalten.

![Abbildung: Schnittstellen mit externen Prozesskomponenten](media/resilient-external-processes/external-processes-architecture.png)

## <a name="ensure-correct-placement-of-the-apis"></a>Sicherstellen der ordnungsgemäßen Platzierung der APIs

Mithilfe der IEF-Richtlinien (Identity Experience Framework) können Sie ein externes System mithilfe eines [technischen RESTful-API-Profils](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) aufrufen. Externe Systeme werden nicht von der IEF-Laufzeitumgebung gesteuert und stellen einen potenziellen Fehlerpunkt dar.

### <a name="how-to-manage-external-systems-using-apis"></a>Verwalten externer Systeme mithilfe von APIs

- Wenn Sie eine Schnittstelle für den Zugriff auf bestimmte Daten aufrufen, überprüfen Sie, ob die Daten die Authentifizierungsentscheidung steuern werden. Bewerten Sie, ob die Informationen für die Kernfunktionen der Anwendung von entscheidender Bedeutung sind. Beispielsweise eine E-Commerce- im Vergleich zu einer sekundären Funktion, etwa einer Verwaltungsfunktion. Wenn die Informationen für die Authentifizierung nicht benötigt werden und nur für sekundäre Szenarien erforderlich sind, sollten Sie erwägen, den Aufruf in die Anwendungslogik zu verschieben.

- Wenn die für die Authentifizierung erforderlichen Daten relativ statisch und klein sind und kein anderer geschäftlicher Grund für die Externalisierung aus dem Verzeichnis vorliegt, sollten Sie erwägen, sie im Verzeichnis zu speichern.

- Entfernen Sie nach Möglichkeit API-Aufrufe aus dem vorab authentifizierten Pfad. Wenn dies nicht möglich ist, müssen Sie strikte Schutzvorkehrungen für DoS-Angriffe (Denial-of-Service) und DDoS-Angriffe (Distributed Denial-of-Service) vor ihren APIs platzieren. Angreifer können die Anmeldeseite laden und versuchen, Ihre API mit DoS-Angriffen zu überfluten und Ihre Anwendung lahmzulegen. Beispielsweise kann die Verwendung von CAPTCHA beim Anmeldungs- bzw. Registrierungsvorgang hilfreich sein.

- Verwenden Sie nach Möglichkeit [API-Connectors des integrierten Anmeldebenutzerflusses](https://docs.microsoft.com/azure/active-directory-b2c/api-connectors-overview) zur Integration in Web-APIs nach dem Anmelden mit einem Identitätsanbieter oder vor dem Erstellen des Benutzers. Da die Benutzerflows bereits ausgiebig getestet wurden, ist es wahrscheinlich, dass Sie keine Funktions-, Leistungs- oder Skalierungstests auf Benutzerflowebene durchführen müssen. Sie müssen Ihre Anwendungen weiterhin auf Funktionalität, Leistung und Skalierung testen.

- [Technische Profile](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) der RESTFul-API von Azure AD stellen kein Zwischenspeicherungsverhalten zur Verfügung. Stattdessen implementiert das RESTFul-API-Profil eine Wiederholungslogik und ein Timeout, das in die Richtlinie integriert ist.

- Für APIs, die Daten schreiben müssen, können Sie einen Task in eine Warteschlange stellen, damit solche Tasks von einem Hintergrundworker ausgeführt werden. Dienste wie [Azure-Warteschlangen](https://docs.microsoft.com/azure/storage/queues/storage-queues-introduction) können verwendet werden. Dies führt dazu, dass die Rückgabe der API effizient erfolgt und die Ausführungsleistung der Richtlinie erhöht wird.  

## <a name="api-error-handling"></a>API-Fehlerbehandlung

Da sich die APIs außerhalb des Azure AD B2C-Systems befinden, ist eine ordnungsgemäße Fehlerbehandlung innerhalb des technischen Profils erforderlich. Stellen Sie sicher, dass der Endbenutzer entsprechend informiert ist und die Anwendung Fehler ordnungsgemäß behandeln kann.

### <a name="how-to-gracefully-handle-api-errors"></a>Ordnungsgemäßes Behandeln von API-Fehlern

- Eine API kann aus verschiedenen Gründen fehlschlagen. Sorgen Sie dafür, dass Ihre Anwendung gegenüber solchen Fehlern resistent ist. [Geben Sie eine HTTP 4xx-Fehlermeldung zurück](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#returning-validation-error-message), wenn die API die Anforderung nicht abschließen kann. Versuchen Sie in der Azure AD B2C-Richtlinie, die Nichtverfügbarkeit der API ordnungsgemäß zu behandeln, und rendern Sie ggf. eine verringerte Benutzeroberfläche.

- [Behandeln Sie vorübergehende Fehler ordnungsgemäß](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile#error-handling). Das RESTFul-API-Profil ermöglicht es Ihnen, Fehlermeldungen für verschiedene [Störfälle](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) zu konfigurieren.

- Überwachen und verwenden Sie proaktiv Continuous Integration/Continuous Delivery (CI/CD), und rotieren Sie die Anmeldeinformationen für den API-Zugriff, z. B. Kennwörter und Zertifikate, die von der [Engine des technischen Profils](https://docs.microsoft.com/azure/active-directory-b2c/restful-technical-profile) verwendet werden.

## <a name="api-management---best-practices"></a>Bewährte Methoden für die API-Verwaltung

Wenn Sie die RESTful-APIs bereitstellen und das technische RESTful-Profil konfigurieren, können Sie die empfohlenen bewährten Methoden befolgen, um häufige Fehler und Probleme zu vermeiden, die übersehen werden können.

### <a name="how-to-manage-apis"></a>Verwalten von APIs

- API Management (APIM) veröffentlicht, verwaltet und analysiert Ihre APIs. APIM verarbeitet außerdem die Authentifizierung, um sicheren Zugriff auf Back-End-Dienste und Microservices zu ermöglichen. Verwenden Sie ein API-Gateway zum horizontalen Hochskalieren von API-Bereitstellungen, Zwischenspeicherung und Lastenausgleich.

- Es wird empfohlen, das richtige Token am Anfang der User Journey abrufen, anstatt für jede API mehrere Aufrufe auszuführen, und [eine Azure APIM-API zu sichern](https://docs.microsoft.com/azure/active-directory-b2c/secure-api-management?tabs=app-reg-ga).

## <a name="next-steps"></a>Nächste Schritte

- [Resilienzressourcen für Azure AD B2C-Entwickler](resilience-b2c.md)
  - [Resiliente Endbenutzerumgebung](resilient-end-user-experience.md)
  - [Resilienz durch bewährte Entwicklermethoden](resilience-b2c-developer-best-practices.md)
  - [Resilienz durch Überwachung und Analyse](resilience-with-monitoring-alerting.md)
- [Erzielen von Resilienz in der Authentifizierungsinfrastruktur](resilience-in-infrastructure.md)
- [Mehr Resilienz bei Authentifizierung und Autorisierung in Ihren Anwendungen](resilience-app-development-overview.md)
