---
title: Endbenutzerumgebungen für Anwendungen – Azure Active Directory
description: Azure Active Directory (Azure AD) bietet mehrere anpassbare Möglichkeiten, um Anwendungen für Endbenutzer in Ihrer Organisation bereitzustellen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.reviewer: arvindh
ms.openlocfilehash: 9bcf45cdfbadbf16f48f8f2ac3bd0c6320a2803f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649227"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Endbenutzerumgebungen für Anwendungen in Azure Active Directory

Azure Active Directory (Azure AD) bietet mehrere anpassbare Möglichkeiten, um Anwendungen für Endbenutzer in Ihrer Organisation bereitzustellen:

* „Meine Apps“ in Azure AD
* Microsoft 365-Anwendungsstartprogramm
* Direkte Anmeldung bei Verbund-Apps
* Deep-Links zu verbundenen, kennwortbasierten oder vorhandene Apps

Welche Methode(n) Sie zur Bereitstellung in Ihrer Organisation wählen, liegt in Ihrem eigenen Ermessen.

## <a name="azure-ad-my-apps"></a>„Meine Apps“ in Azure AD

„Meine Apps“ unter https://myapps.microsoft.com ist ein webbasiertes Portal, über das ein Endbenutzer mit einem Organisationskonto in Azure Active Directory Anwendungen anzeigen und starten kann, für die ihm der Azure AD-Administrator Zugriff gewährt hat. Als Endbenutzer mit [Azure Active Directory Premium](https://azure.microsoft.com/pricing/details/active-directory/) können Sie auch die Funktionen zur Self-Service-Gruppenverwaltung über „Meine Apps“ nutzen.

Standardmäßig werden alle Anwendungen auf einer einzelnen Seite aufgelistet. Sie können jedoch mithilfe von Auflistungen verwandte Anwendungen gruppieren und sie auf einer separaten Registerkarte präsentieren, damit sie leichter zu finden sind. So können Sie mit Auflistungen beispielsweise logische Gruppierungen von Anwendungen für bestimmte Positionen, Aufgaben, Projekte usw. erstellen. Informationen dazu finden Sie unter [Erstellen von Sammlungen im Portal „Meine Apps“](access-panel-collections.md). 

„Meine Apps“ ist unabhängig vom Azure-Portal und setzt nicht voraus, dass Benutzer über ein Azure- oder Microsoft 365-Abonnement verfügen.

Weitere Informationen zu „Meine Apps“ in Azure AD finden Sie in der [Einführung in „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="microsoft-365-application-launcher"></a>Microsoft 365-Anwendungsstartprogramm

Für Organisationen, die Microsoft 365 bereitgestellt haben, werden Anwendungen, die Benutzern über Azure AD zugewiesen werden, auch im Office 365-Portal unter [https://portal.office.com/myapps](https://portal.office.com/myapps) angezeigt. So können Benutzer in einer Organisation ihre Apps einfach und komfortabel starten, ohne ein zweites Portal verwenden zu müssen. Diese Lösung für den App-Start wird für Unternehmen empfohlen, die Microsoft 365 nutzen.

Weitere Informationen zum Office 365-Anwendungsstartprogramm finden Sie unter [Anzeige Ihrer App im Office 365-Anwendungsstartprogramm](/previous-versions/office/office-365-api/).

## <a name="direct-sign-on-to-federated-apps"></a>Direkte Anmeldung bei Verbund-Apps

Die meisten Verbundanwendungen, die SAML 2.0, WS-Verbund oder OpenID Connect unterstützen, bieten die Möglichkeit, dass Benutzer die Anwendung aufrufen, und dann entweder durch automatische Umleitung oder Klicken auf einen Link zur Anmeldung über Azure AD angemeldet werden. Dies bezeichnet man als „vom Dienstanbieter initiierte Anmeldung“, und die meisten Verbundanwendungen im Azure AD-Anwendungskatalog unterstützen diese Form der Anmeldung (weitere Informationen finden Sie in der Dokumentation, die Sie im Azure-Portal über einen Link im Assistenten zum Konfigurieren der einmaligen Anmeldung für die App aufrufen können).

## <a name="direct-sign-on-links"></a>Links für die direkte Anmeldung

Azure AD unterstützt auch Direktlinks für die einmalige Anmeldung bei einzelnen Anwendungen, die eine kennwortbasierte einmalige Anmeldung, einmaliges Anmelden über Link und eine beliebige Art der einmaligen Verbundanmeldung unterstützen.

Diese Links sind speziell gestaltete URLs, die einen Benutzer durch den Azure AD-Anmeldevorgang für eine bestimmte Anwendung leiten, ohne dass der Benutzer sie über „Meine Apps“ in Azure AD oder in Microsoft 365 starten muss. Diese **Benutzer-Zugriffs-URLs** befinden sich unter den Eigenschaften der verfügbaren Unternehmensanwendungen. Wählen Sie im Azure-Portal **Azure Active Directory** > **Unternehmens-Apps** aus. Wählen Sie die Anwendung und dann **Eigenschaften** aus.

![Beispiel für die Benutzerzugriffs-URL in den Twitter-Eigenschaften](media/end-user-experiences/direct-sign-on-link.png)

Diese Links können Sie kopieren und überall dort einfügen, wo Sie einen Anmeldelink zur ausgewählten Anwendung bereitstellen möchten. Dies kann eine E-Mail oder ein beliebiges benutzerdefiniertes webbasiertes Portal sein, das Sie für den Benutzerzugriff auf eine Anwendung eingerichtet haben. Hier sehen Sie ein Beispiel für eine Azure AD-Direkt-URL zur einmaligen Anmeldung für Twitter:

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Ähnlich wie organisationsspezifische URLs für „Meine Apps“ können Sie diese URL durch Hinzufügen einer der aktiven oder überprüften Domänen für Ihr Verzeichnis nach der Domäne *myapps.microsoft.com* weiter anpassen. So wird sichergestellt, dass jegliches Organisationsbranding sofort auf der Anmeldeseite geladen wird, ohne dass Benutzer vorher ihre Benutzer-ID eingeben müssen:

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

Wenn autorisierte Benutzer auf einen dieser anwendungsspezifischen Links klicken, wird ihnen zuerst die Anmeldeseite ihrer Organisation angezeigt (vorausgesetzt, dass sie noch nicht angemeldet sind), und nach dem Anmelden werden sie ohne ein Anhalten in „My Apps“ direkt zu ihrer App umgeleitet. Wenn Voraussetzungen für den Zugriff auf die Anwendung nicht erfüllt sind, wenn z. B. die kennwortbasierte Browsererweiterung zur einmaligen Anmeldung fehlt, wird der Benutzer über den Link zur Installation der fehlenden Erweiterung aufgefordert. Die Link-URL bleibt auch dann unverändert, wenn die Konfiguration für die einmalige Anmeldung geändert wird.

Bei diesen Links werden die gleichen Mechanismen der Zugriffssteuerung verwendet wie von „Meine Apps“ und Microsoft 365, und nur die Benutzer oder Gruppen, die der Anwendung im Azure-Portal zugewiesen wurden, können sich erfolgreich authentifizieren. Alle nicht autorisierten Benutzer werden in einer Meldung darüber informiert, dass ihnen kein Zugriff gewährt wurde. Anschließend wird ihnen ein Link zum Laden von „Meine Apps“ bereitgestellt, über die verfügbare Anwendungen angezeigt werden, auf die sie zugreifen können.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
* [Worum handelt es sich beim einmaligen Anmelden?](what-is-single-sign-on.md)
* [Erste Schritte zur Integration von Anwendungen in Azure Active Directory](plan-an-application-integration.md)