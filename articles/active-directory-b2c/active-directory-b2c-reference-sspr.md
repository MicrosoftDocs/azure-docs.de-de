---
title: Self-Service-Kennwortzurücksetzung in Azure Active Directory B2C | Microsoft-Dokumentation
description: Veranschaulicht das Einrichten der Self-Service-Kennwortzurücksetzung für Ihre Kunden in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e8137bc0e75595b5f548584bb2d1644de2e0fecd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508920"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Einrichten der Self-Service-Kennwortzurücksetzung für Ihre Kunden

Das Feature für die Self-Service-Kennwortzurücksetzung ermöglicht Ihren Kunden, die für lokale Konten registriert sind, das eigenständige Zurücksetzen ihrer Kennwörter. Dadurch wird die Belastung für Ihre Supportmitarbeiter erheblich reduziert, insbesondere, wenn Ihre Anwendung Millionen von Kunden besitzt, die sie regelmäßig verwenden. Derzeit wird nur die Verwendung einer verifizierten E-Mail-Adresse als Wiederherstellungsmethode unterstützt.

> [!NOTE]
> Dieser Artikel gilt für die Self-Service-Kennwortzurücksetzung im Kontext des V1-Benutzerflows **Anmeldung**, bei dem **Anmeldung mit lokalem Konto** als Identitätsanbieter verwendet wird. Informationen zu vollständig anpassbaren Benutzerflows zur Kennwortzurücksetzung, die in Ihrer App aufgerufen werden, finden Sie in [diesem Artikel](active-directory-b2c-reference-policies.md).
> 
> 

Standardmäßig ist für Ihr Verzeichnis die Self-Service-Kennwortzurücksetzung nicht aktiviert. Verwenden Sie die folgenden Schritte zum Aktivieren der Funktion:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Abonnementadministrator an. Dies ist dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie Ihr Verzeichnis erstellt haben.
2. Öffnen Sie **Azure Active Directory** (auf der Navigationsleiste auf der linken Seite).
4. Legen Sie **Self-Service-Kennwortzurücksetzung aktiviert** auf **Alle** fest. 
5. Klicken Sie oben auf der Seite auf **Speichern**. Sie haben es geschafft!

Zum Testen verwenden Sie das Feature „Jetzt ausführen“ für einen beliebigen Benutzerflow für die Anmeldung mit lokalen Konten als Identitätsanbieter. Klicken Sie auf der lokalen Kontoanmeldeseite (auf der Sie eine E-Mail-Adresse und ein Kennwort oder einen Benutzernamen und ein Kennwort eingeben) auf **Sie können nicht auf Ihr Konto zugreifen?** , um den Vorgang zu überprüfen.

> [!NOTE]
> Die Seiten der Self-Service-Kennwortrücksetzung können mithilfe des [Features für Unternehmensbranding](../active-directory/fundamentals/customize-branding.md)angepasst werden.
> 
> 

