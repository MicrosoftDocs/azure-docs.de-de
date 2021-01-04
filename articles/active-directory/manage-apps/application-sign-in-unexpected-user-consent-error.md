---
title: Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung | Microsoft-Dokumentation
description: Erläuterungen von Fehlern, die während des Vorgangs des Genehmigens einer Anwendung auftreten können, und Abhilfemöglichkeiten
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 558c6dc24f6d0d17c9a82bbc79f39649f63dc7f4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658484"
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Unerwarteter Fehler beim Vorgang des Genehmigens einer Anwendung

In diesem Artikel werden Fehler erläutert, die während des Vorgangs des Genehmigens einer Anwendung auftreten können. Wenn Sie Probleme mit unerwarteten Zustimmungsaufforderungen behandeln, die keine Fehlermeldungen enthalten, sehen Sie sich die [Authentifizierungsszenarien für Azure AD](../develop/authentication-vs-authorization.md) an.

Viele Anwendungen, die in Azure Active Directory integriert sind, erfordern Berechtigungen für den Zugriff auf andere Ressourcen, um zu funktionieren. Wenn diese Ressourcen auch in Azure Active Directory integriert sind, werden Berechtigungen für den Zugriff darauf häufig mithilfe des allgemeinen Genehmigungsframeworks angefordert. Eine Zustimmungsaufforderung wird in der Regel angezeigt, wenn eine Anwendung erstmals verwendet wird. Sie kann jedoch auch zu einem späteren Zeitpunkt erscheinen.

Bestimmte Bedingungen müssen sein erfüllt, damit ein Benutzer die von einer Anwendung benötigten Berechtigungen genehmigt. Wenn diese Bedingungen nicht erfüllt sind, können die folgenden Fehler auftreten.

## <a name="requesting-not-authorized-permissions-error"></a>Anfordern nicht autorisierter Berechtigungen
* **AADSTS90093:** &lt;Anzeigename_der_Client-App&gt; fordert mindestens eine Berechtigung an, für deren Erteilung Sie nicht autorisiert sind. Wenden Sie sich an einen Administrator, der diese Anwendung in Ihrem Auftrag genehmigen kann.
* **AADSTS90094:** &lt;Anzeigename der Client-App&gt; benötigt eine Berechtigung zum Zugriff auf Ressourcen in Ihrer Organisation, die nur ein Administrator erteilen kann. Bitten Sie einen Administrator, dieser App Berechtigungen zu erteilen, bevor Sie sie verwenden können.

Dieser Fehler tritt auf, wenn ein Benutzer, der kein Unternehmensadministrator ist, versucht, eine Anwendung zu verwenden, die Berechtigungen anfordert, die nur ein Administrator erteilen kann. Dieser Fehler kann von einem Administrator behoben werden, indem der Zugriff auf die Anwendung im Auftrag der Organisation gewährt wird.

Dieser Fehler kann auch auftreten, wenn ein Benutzer daran gehindert wird, einer Anwendung zuzustimmen, weil Microsoft erkennt, dass die Berechtigungsanforderung riskant ist. In diesem Fall wird auch ein Überwachungsereignis der Kategorie „ApplicationManagement“ mit dem Aktivitätstyp „Zustimmung zu Anwendung“ und dem Statusgrund „Riskante Anwendung erkannt“ protokolliert.

Ein weiteres Szenario, in dem dieser Fehler auftreten kann, ist, wenn die Benutzerzuweisung für die Anwendung erforderlich ist, aber keine Administratoreinwilligung gegeben wurde. In diesem Fall muss der Administrator zuerst seine Einwilligung geben.   

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
* **AADSTS65005:** &lt;Anzeigename_der_Client-App&gt; fordert den Zugriff auf &lt;Anzeigename_der_Ressourcenanwendung&gt; an, die in &lt;Anzeigename_des_Mandanten&gt; Ihrer Organisation nicht verfügbar ist. 

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

## <a name="risky-app-error-and-warning"></a>Fehler und Warnungen bei riskanter App
* **AADSTS900941:** Administratoreinwilligung ist erforderlich. Die App wird als riskant eingestuft. (AdminConsentRequiredDueToRiskyApp)
* Diese App ist möglicherweise riskant. Wenn Sie dieser App vertrauen, bitten Sie Ihren Administrator, Ihnen Zugriff zu erteilen.
* **AADSTS900981:** Eine Anforderung zur Administratoreinwilligung wurde für eine riskante App empfangen. (AdminConsentRequestRiskyAppWarning)
* Diese App ist möglicherweise riskant. Fahren Sie nur fort, wenn Sie dieser App vertrauen.

Diese beiden Meldungen werden angezeigt, wenn Microsoft festgestellt hat, dass die Einwilligungsanforderung riskant sein kann. Neben einer Reihe anderer Faktoren kann dies vorkommen, wenn der App-Registrierung kein [verifizierter Herausgeber](../develop/publisher-verification-overview.md) hinzugefügt wurde. Wenn der [Workflow für Administratoreinwilligung](configure-admin-consent-workflow.md) deaktiviert ist, wird Endbenutzern der erste Fehlercode mit zugehöriger Meldung angezeigt. Der zweite Fehlercode mit zugehöriger Meldung wird ihnen angezeigt, wenn der Workflow für Administratoreinwilligung aktiviert ist und eine Weiterleitung an die Administratoren erfolgt. 

Der Endbenutzer kann für Apps, die als riskant erkannt wurden, keine Einwilligung erteilen. Administratoren können dies zwar tun, sollten die App aber sehr sorgfältig evaluieren und Vorsicht walten lassen. Wenn die App bei weiterer Überprüfung immer noch verdächtig scheint, kann sie über den Zustimmungsbildschirm an Microsoft gemeldet werden. 

## <a name="next-steps"></a>Nächste Schritte 

[Apps, Berechtigungen und Zustimmung in Azure Active Directory (Endpunkt, Version 1)](../develop/quickstart-register-app.md)<br>

[Bereiche, Berechtigungen und Zustimmung im Azure Active Directory (Endpunkt, Version 2.0)](../develop/v2-permissions-and-consent.md)