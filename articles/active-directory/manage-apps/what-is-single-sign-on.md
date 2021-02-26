---
title: Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO) von Azure?
description: Enthält eine Beschreibung der Funktionsweise des einmaligen Anmeldens (Single Sign-On, SSO) mit Azure Active Directory. Nutzen Sie SSO, damit sich die Benutzer nicht für jede Anwendung ein eigenes Kennwort merken müssen. Verwenden Sie SSO auch, um die Verwaltung Ihrer Konten zu vereinfachen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: overview
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.custom: contperf-fy21q1
ms.openlocfilehash: 36025a5a68e4c3d0de1d6b11a13df656c72745d4
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260196"
---
# <a name="what-is-single-sign-on-sso"></a>Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?

Beim einmaligen Anmelden (Single Sign-On, SSO) müssen sich Benutzer nicht bei jeder Anwendung anmelden, die sie verwenden. Ein Benutzer meldet sich einmal an, und die Anmeldeinformationen werden anschließend auch für andere Apps verwendet.

Als Endbenutzer interessieren Sie die SSO-Details wahrscheinlich nicht sonderlich. Sie möchten nur die Apps nutzen, mit denen Sie produktiv arbeiten können, ohne häufig Ihr Kennwort eingeben zu müssen. Sie finden Ihre Apps unter https://myapps.microsoft.com.
 
Lesen Sie als Administrator oder IT-Experte weiter, um mehr zu SSO und zur Implementierung in Azure zu erfahren.

## <a name="single-sign-on-basics"></a>Grundlagen des einmaligen Anmeldens
Einmaliges Anmelden bedeutet einen gewaltigen Fortschritt in Bezug darauf, wie sich Benutzer bei Anwendungen anmelden und diese nutzen. Auf einmaligem Anmelden basierende Authentifizierungssysteme werden häufig als „moderne Authentifizierung“ bezeichnet. Die moderne Authentifizierung und einmaliges Anmelden fallen in die Computingkategorie „Identitäts- und Zugriffsverwaltung“ (Identity & Access Management, IAM). Sehen Sie sich das hier angegebene Video an, um sich damit vertraut zu machen, wie einmaliges Anmelden ermöglicht wird.

Grundlagen der Authentifizierung: Grundlagen | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/fbSVgC8nGz4]

## <a name="single-sign-on-with-web-applications"></a>Einmaliges Anmelden mit Webanwendungen
Webanwendungen sind sehr beliebt. Web-Apps werden von verschiedenen Unternehmen gehostet und als Dienst zur Verfügung gestellt. Beispiele für beliebte Web-Apps sind Microsoft 365, GitHub und Salesforce, und es gibt noch einige Tausend mehr. Benutzer verwenden auf ihren Computern einen Webbrowser, um auf Web-Apps zuzugreifen. Einmaliges Anmelden ermöglicht Benutzern die Navigation zwischen verschiedenen Web-Apps, ohne dass sie sich mehrfach anmelden müssen.

Informationen zur Funktionsweise des einmaligen Anmeldens mit Web-Apps finden Sie in den beiden unten angegebenen Videos.

Grundlagen der Authentifizierung: Webanwendungen | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/tCNcG1lcCHY]

Grundlagen der Authentifizierung: Einmalige Webanmeldung | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/51B-jSOBF8U]

## <a name="cloud-versus-on-premises-hosted-apps"></a>Vergleich von in der Cloud und lokal gehosteten Apps
Die Art der Implementierung des einmaligen Anmeldens richtet sich danach, wo die App gehostet wird. Das Hosting ist aufgrund der Art und Weise wichtig, wie Netzwerkdatenverkehr für den Zugriff auf die App weitergeleitet wird. Wenn eine App über Ihr lokales Netzwerk gehostet wird und darüber zugänglich ist (als lokale App bezeichnet), müssen Benutzer für die Nutzung der App nicht über das Internet darauf zugreifen. Wenn die App an einem anderen Ort gehostet wird (als in der Cloud gehostete App bezeichnet), müssen Benutzer über das Internet zugreifen, um die App zu nutzen.

> [!TIP]
> In der Cloud gehostete Apps werden auch als SaaS-Apps (Software-as-a-Service) bezeichnet. 

Das einmalige Anmelden für in der Cloud gehostete Apps ist einfach. Sie informieren den Identitätsanbieter darüber, dass diese Vorgehensweise für die App genutzt wird. Anschließend konfigurieren Sie die App so, dass der Identitätsanbieter als vertrauenswürdig angesehen wird. Informationen dazu, wie Sie Azure AD als Identitätsanbieter für eine App verwenden, finden Sie unter [Schnellstartserie zur Anwendungsverwaltung](add-application-portal.md).

> [!TIP]
> Die Ausdrücke „Cloud“ und „Internet“ werden häufig synonym verwendet. Der Grund hierfür sind die Netzwerkdiagramme. Es ist üblich, große Computernetzwerke in einem Diagramm in Form einer Wolke darzustellen, weil nicht alle Komponenten eingezeichnet werden können. Das Internet ist das bekannteste Netzwerk, und daher werden die Ausdrücke häufig als Synonyme gebraucht. Es können aber alle Computernetzwerke als Cloud bezeichnet werden.

Sie können einmaliges Anmelden auch für lokale Apps nutzen. Die Technologie für lokales einmaliges Anmelden (Single Sign-On) wird als „Anwendungsproxy“ bezeichnet. Weitere Informationen hierzu finden Sie unter [Optionen für einmaliges Anmelden](sso-options.md).

## <a name="multiple-identity-providers"></a>Mehrere Identitätsanbieter
Wenn Sie einmaliges Anmelden für mehrere Identitätsanbieter einrichten, wird dies als Verbund bezeichnet. Informationen zur Funktionsweise des Verbunds finden Sie im unten angegebenen Video.

Grundlagen der Authentifizierung: Verbund | Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/CjarTgjKcX8]


## <a name="next-steps"></a>Nächste Schritte
* [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
* [Optionen für einmaliges Anmelden](sso-options.md)
