---
title: Übersicht über den Modus für gemeinsam genutzte Geräte
titleSuffix: Microsoft identity platform | Azure
description: Informationen über den Modus für gemeinsam genutzte Geräte, der Mitarbeitern in Service und Produktion die gemeinsame Nutzung von Geräten ermöglicht.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3cd7074467332f89d4d6c60830be34f4e2a638c1
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94562082"
---
# <a name="overview-of-shared-device-mode"></a>Übersicht über den Modus für gemeinsam genutzte Geräte

Der Modus für gemeinsam genutzte Geräte ist ein Feature von Azure Active Directory, mit dem Sie Anwendungen erstellen können, die Mitarbeiter in Service und Produktion unterstützen und ihnen die gemeinsame Nutzung der für sie bereitgestellten Geräte ermöglichen.

>[!IMPORTANT]
> Dieses Feature [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="what-are-firstline-workers"></a>Was sind Mitarbeiter in Service und Produktion?

Mitarbeiter in Service und Produktion sind Mitarbeiter im Einzelhandel, Wartungs- und Außendienstmitarbeiter, medizinisches Personal und andere Benutzer, die über keinen Computerarbeitsplatz verfügen oder die für die Zusammenarbeit keine Unternehmens-E-Mail verwenden. In den folgenden Abschnitten werden die Aspekte und Herausforderungen der Unterstützung von Mitarbeitern in Service und Produktion beschrieben. Anschließend werden die von Microsoft bereitgestellten Features beschrieben, die die Verwendung Ihrer Anwendung durch die Mitarbeiter in Service und Produktion einer Organisation ermöglichen.

### <a name="challenges-of-supporting-firstline-workers"></a>Herausforderungen bei der Unterstützung von Mitarbeitern in Service und Produktion

Für die Unterstützung von Mitarbeitern in Service und Produktion sind andere Herausforderungen als für typische Information-Worker zu bewältigen. Zu diesen Herausforderungen zählen möglicherweise eine hohe Fluktuation und geringere Kenntnisse über die wichtigsten Produktivitätstools der Organisation. Organisationen nutzen unterschiedliche Strategien, um ihre Mitarbeiter in Service und Produktion zu unterstützen. Einige verfolgen eine BYOD-Strategie (Bring Your Own Device), sodass die Mitarbeiter Geschäftsanwendungen auf ihrem persönlichen Smartphone verwenden, während andere ihren Mitarbeitern gemeinsam genutzte Geräte, z. B. iPads oder Android-Tablets, zur Verfügung stellen.

### <a name="supporting-multiple-users-on-devices-designed-for-one-user"></a>Unterstützen mehrerer Benutzer auf Geräten, die für einen Benutzer vorgesehen sind

Da mobile iOS- oder Android-Geräte für Einzelbenutzer entwickelt wurden, sind die meisten Anwendungen für die Verwendung durch einen einzelnen Benutzer optimiert. Hierzu zählen beispielsweise anwendungsübergreifendes einmaliges Anmelden und die Eigenschaft, dass Benutzer auf ihrem Gerät angemeldet bleiben. Wenn ein Benutzer sein Konto aus einer Anwendung entfernt, wird dies von der Anwendung in der Regel nicht als sicherheitsrelevant erachtet. In vielen Anwendungen bleiben sogar die Anmeldeinformationen des Benutzers nach dem Entfernen des Kontos gespeichert, um eine schnelle Anmeldung zu ermöglichen. Sie haben möglicherweise selbst erlebt, dass Sie noch angemeldet waren, nachdem Sie eine Anwendung von Ihrem mobilen Gerät gelöscht und dann neu installiert hatten.

### <a name="global-sign-in-and-sign-out-sso"></a>Globale An- und Abmeldung (Sign-in and Sign-Out, SSO)

Damit die Mitarbeiter einer Organisation ihre Apps in einem Pool gemeinsam genutzter Geräte verwenden können, müssen Entwickler die entgegengesetzte Erfahrung ermöglichen. Die Mitarbeiter sollten in der Lage sein, ein Gerät aus dem Pool auszuwählen und eine einzelne Geste auszuführen, um das Gerät für die Dauer ihrer Schicht in Besitz zu nehmen. Am Ende der Schicht sollten sie sich mit einer anderen Geste global bei dem Gerät abmelden können, wobei alle persönlichen und Unternehmensdaten entfernt werden, sodass sie das Gerät an den Gerätepool zurückgeben können. Wenn ein Mitarbeiter vergisst, sich abzumelden, sollte er am Ende der Schicht und/oder nach einem bestimmten Zeitraum der Inaktivität automatisch beim Gerät abgemeldet werden.

Azure Active Directory ermöglicht diese Szenarien mit einem Feature, das als **Modus für gemeinsam genutzte Geräte** bezeichnet wird.

## <a name="introducing-shared-device-mode"></a>Der Modus für gemeinsam genutzte Geräte

Wie bereits erwähnt, ist der Modus für gemeinsame Geräte ein Feature von Azure Active Directory, das Folgendes ermöglicht:

* Erstellen von Anwendungen, die Mitarbeiter in Service und Produktion unterstützen
* Bereitstellen von Geräten für Mitarbeiter in Service und Produktion und Aktivieren des Modus für gemeinsam genutzte Geräte

### <a name="build-applications-that-support-firstline-workers"></a>Erstellen von Anwendungen, die Mitarbeiter in Service und Produktion unterstützen

Sie können in Ihren Anwendungen Mitarbeiter in Service und Produktion unterstützen, indem Sie mithilfe der Microsoft Authentication Library (MSAL) und der [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) den Gerätestatus *Modus für gemeinsam genutzte Geräte* aktivieren. Wenn sich ein Gerät im Modus für gemeinsam genutzte Geräte befindet, stellt Microsoft Informationen für Ihre Anwendung bereit, die es ermöglichen, das Verhalten der Anwendung auf Grundlage des Status des Benutzers auf dem Gerät zu ändern und die Benutzerdaten zu schützen.

Folgende Features werden unterstützt:

* **Geräteweites Anmelden des Benutzers** über jede unterstützte Anwendung
* **Geräteweites Abmelden des Benutzers** über jede unterstützte Anwendung
* **Abfragen des Gerätestatus**, um zu ermitteln, ob sich die Anwendung auf einem Gerät befindet, das den Modus für gemeinsam genutzte Geräte aufweist
* **Abfragen des Gerätestatus des Benutzers** auf dem Gerät, um zu ermitteln, ob sich seit der letzten Verwendung der Anwendung etwas geändert hat

Die Unterstützung des Modus für gemeinsam genutzte Geräte sollte als Verbesserung der Sicherheit und als Featureupgrade für Ihre Anwendung angesehen werden. Sie kann die Akzeptanz der Anwendung in hochgradig regulierten Umgebungen wie Gesundheitswesen und Finanzwirtschaft erhöhen.

Sie müssen sicherstellen, dass die Daten eines Benutzers nicht an andere Benutzer gelangen. Der Modus für gemeinsam genutzte Geräte bietet hilfreiche Signale, anhand derer die Anwendung erkennt, dass eine Änderung vorgenommen wurde, die Sie verwalten sollten. Ihre Anwendung muss den Status des Benutzers bei jeder Verwendung der Anwendung auf dem Gerät überprüfen und die Daten des vorherigen Benutzers löschen. Dies gilt auch, wenn die Anwendung bei Multitasking erneut aus dem Hintergrund geladen wird. Bei einem Wechsel des Benutzers müssen Sie sicherstellen, dass die Daten des vorherigen Benutzers gelöscht und alle zwischengespeicherten Daten, die in der Anwendung angezeigt werden, entfernt werden. Es wird empfohlen, immer eine gründliche Sicherheitsüberprüfung durchzuführen, nachdem Sie Ihrer Anwendung den Modus für gemeinsam genutzte Geräte hinzugefügt haben.

Ausführliche Informationen zum Ändern der Anwendungen, damit sie den Modus für gemeinsam genutzte Geräte unterstützen, finden Sie im Abschnitt [Nächste Schritte](#next-steps) am Ende dieses Artikels.

### <a name="deploy-devices-to-firstline-workers-and-turn-on-shared-device-mode"></a>Bereitstellen von Geräten für Mitarbeiter in Service und Produktion und Aktivieren des Modus für gemeinsam genutzte Geräte

Sobald Ihre Anwendungen den Modus für gemeinsam genutzte Geräte unterstützen und die erforderlichen Daten- und Sicherheitsänderungen aufweisen, können Sie den Mitarbeitern in Service und Produktion ihre Verwendbarkeit mitteilen.

Die Geräteadministratoren einer Organisation können ihre Geräte und Ihre Anwendungen über eine Lösung für die Verwaltung mobiler Geräte (Mobile Device Management, MDM) wie Microsoft Intune für ihre Stores und Arbeitsplätze bereitstellen. Zum Bereitstellungsprozess gehört das Markieren des Geräts als *Freigegebenes Gerät*. Administratoren konfigurieren den Modus für gemeinsam genutzte Geräte durch Bereitstellen der [Microsoft Authenticator-App](../user-help/user-help-auth-app-overview.md) und Festlegen des Modus für gemeinsam genutzte Geräte durch Konfigurationsparameter. Nach Durchführung dieser Schritte verwenden alle Anwendungen, die den Modus für gemeinsam genutzte Geräte unterstützen, die Anwendung Microsoft Authenticator, um den Benutzerstatus zu verwalten und Sicherheitsfeatures für das Gerät und die Organisation bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Wir unterstützen den Modus für gemeinsam genutzte Geräte auf iOS- und Android-Plattformen. Lesen Sie die folgende Dokumentation für Ihre Plattform, um in Ihren Anwendungen Mitarbeiter in Service und Produktion zu unterstützen.

* [Unterstützen des Modus für gemeinsam genutzte Geräte für iOS](msal-ios-shared-devices.md)
* [Unterstützen des Modus für gemeinsam genutzte Geräte für Android](msal-android-shared-devices.md)
