---
title: Verwenden des SAL-Protokolls durch Microsoft Identity Platform
description: Dieser Artikel enthält eine Übersicht über die SAML-Profile für das einmalige Anmelden und Abmelden in Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/05/2018
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 06f80f94be25e42c9e8f0270e6cb15aca086ae18
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994399"
---
# <a name="how-microsoft-identity-platform-uses-the-saml-protocol"></a>Verwenden des SAL-Protokolls durch Microsoft Identity Platform

Microsoft Identity Platform verwendet das SAML 2.0-Protokoll, um Anwendungen das Bereitstellen einer Umgebung für einmaliges Anmelden für die Benutzer zu ermöglichen. In den SAML-Profilen zum [einmaligen Anmelden](single-sign-on-saml-protocol.md) und [einmaligen Abmelden](single-sign-out-saml-protocol.md) von Azure AD wird beschrieben, wie SAML-Assertionen, -Protokolle und -Bindungen im Identitätsanbieterdienst verwendet werden.

Für das SAML-Protokoll ist es erforderlich, dass der Identitätsanbieter (Microsoft Identity Platform) und der Dienstanbieter (die Anwendung) Informationen übereinander austauschen.

Wenn eine Anwendung unter Azure AD registriert ist, registriert der App-Entwickler verbundbezogene Informationen unter Azure AD. Dies gilt auch für den **Umleitungs-URI** und den **Metadaten-URI** der Anwendung.

Microsoft Identity Platform nutzt den **Metadaten-URI** des Clouddiensts, um den Signaturschlüssel und den Abmelde-URI abzurufen. Kunden können die App unter **Azure AD > App-Registrierung** öffnen und dann über **Einstellungen > Eigenschaften** die Abmelde-URL aktualisieren. Dadurch kann Microsoft Identity Platform die Antwort an die richtige URL senden. 

Azure Active Directory macht mandantenspezifische und allgemeine (mandantenunabhängige) Endpunkte für das einmalige Anmelden und das einmalige Abmelden verfügbar. Bei diesen URLs handelt es sich um adressierbare Speicherorte (nicht nur um Bezeichner), damit Sie auf den Endpunkt zugreifen können, um die Metadaten zu lesen.

* Der mandantenspezifische Endpunkt befindet sich unter `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`. Der Platzhalter *\<TenantDomainName>* steht für einen registrierten Domänennamen oder die TenantID-GUID eines Azure AD-Mandanten. Die Verbundmetadaten des Mandanten „contoso.com“ befinden sich beispielsweise hier: https://login.microsoftonline.com/contoso.com/FederationMetadata/2007-06/FederationMetadata.xml.

* Der mandantenunabhängige Endpunkt befindet sich unter `https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml`. Bei dieser Endpunktadresse wird anstelle eines Mandantendomänennamens oder einer ID das Wort **common** verwendet.

Informationen zu den Verbundmetadatendokumenten, die von Azure AD veröffentlicht werden, finden Sie unter [Verbundmetadaten](../azuread-dev/azure-ad-federation-metadata.md).
