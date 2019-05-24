---
title: Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung | Microsoft-Dokumentation
description: Erläuterungen von Fehlern, die während des Vorgangs des Genehmigens einer Anwendung auftreten können, und Abhilfemöglichkeiten
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dff3be9a9bc7fd897f340e5fe6a4775a4914810
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65824941"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung

In diesem Artikel werden Fehler erläutert, die während des Vorgangs des Genehmigens einer Anwendung auftreten können. Wenn Sie Probleme mit unerwarteten Zustimmungsaufforderungen behandeln, die keine Fehlermeldungen enthalten, sehen Sie sich die [Authentifizierungsszenarien für Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios) an.

Viele Anwendungen, die in Azure Active Directory integriert sind, erfordern Berechtigungen für den Zugriff auf andere Ressourcen, um zu funktionieren. Wenn diese Ressourcen auch in Azure Active Directory integriert sind, werden Berechtigungen für den Zugriff darauf häufig mithilfe des allgemeinen Genehmigungsframeworks angefordert. Eine Zustimmungsaufforderung wird in der Regel angezeigt, wenn eine Anwendung erstmals verwendet wird. Sie kann jedoch auch zu einem späteren Zeitpunkt erscheinen.

Bestimmte Bedingungen müssen sein erfüllt, damit ein Benutzer die von einer Anwendung benötigten Berechtigungen genehmigt. Wenn diese Bedingungen nicht erfüllt sind, können die folgenden Fehler auftreten.

## <a name="requesting-not-authorized-permissions-error"></a>Anfordern nicht autorisierter Berechtigungen
* **AADSTS90093:**&lt;Anzeigename_der_Client-App&gt; fordert mindestens eine Berechtigung an, für deren Erteilung Sie nicht autorisiert sind. Wenden Sie sich an einen Administrator, der diese Anwendung in Ihrem Auftrag genehmigen kann.

Dieser Fehler tritt auf, wenn ein Benutzer, der kein Unternehmensadministrator ist, versucht, eine Anwendung zu verwenden, die Berechtigungen anfordert, die nur ein Administrator erteilen kann. Dieser Fehler kann von einem Administrator behoben werden, indem der Zugriff auf die Anwendung im Auftrag der Organisation gewährt wird.

## <a name="policy-prevents-granting-permissions-error"></a>Richtlinien verhindern das Erteilen von Berechtigungen
* **AADSTS90093:** Ein Administrator von &lt;Anzeigename_des_Mandanten&gt; hat eine Richtlinie festgelegt, die Sie daran hindert, &lt;Name der App&gt; die angeforderten Berechtigungen zu erteilen. Wenden Sie sich an einen Administrator von &lt;Anzeigename_des_Mandanten&gt;, der Berechtigungen für diese App in Ihrem Auftrag erteilen kann.

Dieser Fehler tritt auf, wenn ein Unternehmensadministrator für Benutzer die Fähigkeit deaktiviert, Anwendungen zu genehmigen, und ein Nichtadministrator anschließend versucht, eine Anwendung zu nutzen, die eine Genehmigung erfordert. Dieser Fehler kann von einem Administrator behoben werden, indem der Zugriff auf die Anwendung im Auftrag der Organisation gewährt wird.

## <a name="intermittent-problem-error"></a>Vorübergehendes Problem
* **AADSTS90090:** Beim Anmeldevorgang ist ein vorübergehendes Problem beim Aufzeichnen der Berechtigungen aufgetreten, die Sie &lt;Anzeigename_der_Client-App&gt; erteilen wollten. Versuchen Sie es später erneut.

Dieser Fehler zeigt an, dass auf Serverseite ein vorübergehendes Dienstproblem aufgetreten ist. Es kann behoben werden, indem versucht wird, die Anwendung erneut zu genehmigen.

## <a name="resource-not-available-error"></a>Ressource nicht verfügbar
* **AADSTS65005:** Die App &lt;Anzeigename_der_Client-App&gt; hat Berechtigungen für den Zugriff auf die Ressource &lt;Anzeigename_der_Ressourcenanwendung&gt; angefordert, die nicht verfügbar ist. 

Wenden Sie sich an den Anwendungsentwickler.

##  <a name="resource-not-available-in-tenant-error"></a>Ressource im Mandanten nicht verfügbar
* **AADSTS65005:**&lt;Anzeigename_der_Client-App&gt; fordert den Zugriff auf &lt;Anzeigename_der_Ressourcenanwendung&gt; an, die in &lt;Anzeigename_des_Mandanten&gt; Ihrer Organisation nicht verfügbar ist. 

Stellen Sie sicher, dass diese Ressource verfügbar ist, oder wenden Sie sich an einen Administrator von &lt;Anzeigename_des_Mandanten&gt;.

## <a name="permissions-mismatch-error"></a>Berechtigungskonflikt
* **AADSTS65005:** Die App hat die Genehmigung für den Zugriff auf &lt;Anzeigename_der_Ressourcenanwendung&gt; angefordert. Diese Anforderung ist fehlgeschlagen, da sie nicht damit übereinstimmt, wie die App während ihrer Registrierung vorkonfiguriert wurde. Wenden Sie sich an den App-Hersteller.**

Diese Art von Fehler tritt auf, wenn die Anwendung, die ein Benutzer versucht zu genehmigen, Berechtigungen für den Zugriff auf eine Ressourcenanwendung anfordert, die im Verzeichnis (Mandanten) der Organisation nicht vorhanden ist. Dies kann aus unterschiedlichen Gründen passieren:

-   Der Entwickler der Clientanwendung hat seine Anwendung falsch konfiguriert, was bewirkt, dass ein Zugriff auf eine ungültige Ressource angefordert wird. In diesem Fall muss der Anwendungsentwickler die Konfiguration der Clientanwendung zum Beheben dieses Problems aktualisieren.

-   Ein Dienstprinzipal, der die Zielressourcenanwendung darstellt, ist in der Organisation nicht vorhanden oder war in der Vergangenheit vorhanden, wurde jedoch entfernt. Um dieses Problem zu beheben, muss ein Dienstprinzipal für die Ressourcenanwendung in der Organisation so bereitgestellt werden, dass die Clientanwendung Berechtigungen dafür anfordern kann. Je nach Anwendungstyp gibt es unterschiedliche Bereitstellungsmöglichkeiten für den Dienstprinzipal, einschließlich:

    -   Erwerben eines Abonnements für die Ressourcenanwendung (von Microsoft veröffentlichte Anwendungen)

    -   Genehmigen der Ressourcenanwendung

    -   Erteilen der Anwendungsberechtigungen über das Azure-Portal

    -   Hinzufügen der Anwendung aus dem Azure AD-Anwendungskatalog

## <a name="next-steps"></a>Nächste Schritte 

[Apps, Berechtigungen und Zustimmung in Azure Active Directory (Endpunkt, Version 1)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Bereiche, Berechtigungen und Zustimmung im Azure Active Directory (Endpunkt, Version 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


