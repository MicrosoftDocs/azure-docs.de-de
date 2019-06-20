---
title: Zusammenfassen mehrerer Azure AD-Instanzen zu einem Verbund mit einer einzelnen AD FS-Instanz – Azure
description: In diesem Dokument erfahren Sie, wie mehrere Azure AD-Instanzen mit einer einzelnen AD FS-Instanz zu einem Verbund zusammenfassen.
keywords: Verbund erstellen, ADFS, AD FS, mehrere Mandanten, einzelne AD FS-Instanz, eine ADFS-Instanz, Verbund mit mehreren Mandanten, ADFS mit mehreren Gesamtstrukturen, AAD Connect, Verbund, mandantenübergreifender Verbund
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9122e3a7af2230dc0f68e72b28891d488b01a80a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65137831"
---
# <a name="federate-multiple-instances-of-azure-ad-with-single-instance-of-ad-fs"></a>Erstellen eines Verbunds mit mehreren Instanzen von Azure AD und einer Einzelinstanz von AD FS

Mehrere Gesamtstrukturen können in einer einzelnen hochverfügbaren AD FS-Farm zu einem Verbund zusammengefasst werden, sofern zwischen ihnen eine bidirektionale Vertrauensstellung eingerichtet ist. Die Gesamtstrukturen können der gleichen Azure Active Directory-Instanz entsprechen, müssen aber nicht. In diesem Artikel erfahren Sie, wie Sie einen Verbund zwischen einer einzelnen AD FS-Bereitstellung und mehreren Gesamtstrukturen konfigurieren, die mit unterschiedlichen Azure AD-Instanzen synchronisiert werden.

![Verbund mit mehreren Mandanten und einer einzelnen AD FS-Instanz](./media/how-to-connect-fed-single-adfs-multitenant-federation/concept.png)
 
> [!NOTE]
> Geräterückschreiben und automatische Geräteeinbindung werden in diesem Szenario nicht unterstützt.

> [!NOTE]
> In diesem Szenario kann der Verbund nicht mithilfe von Azure AD Connect konfiguriert werden, da Azure AD Connect zum Konfigurieren des Verbunds von Domänen in einer einzelnen Azure AD-Instanz verwendet wird.

## <a name="steps-for-federating-ad-fs-with-multiple-azure-ad"></a>Schritte zum Erstellen eines Verbunds mit AD FS und mehreren Azure AD-Instanzen

Angenommen, eine Domäne namens „contoso.com“ ist in „contoso.onmicrosoft.com“ (Azure Active Directory) bereits Teil eines Verbunds mit der lokalen AD FS-Instanz, die in der lokalen Active Directory-Umgebung „contoso.com“ installiert ist. „fabrikam.com“ ist eine Domäne in „fabrikam.onmicrosoft.com“ (Azure Active Directory).

## <a name="step-1-establish-a-two-way-trust"></a>Schritt 1: Einrichten einer bidirektionalen Vertrauensstellung
 
Damit AD FS in „contoso.com“ Benutzer in „fabrikam.com“ authentifizieren kann, ist zwischen „contoso.com“ und „fabrikam.com“ eine bidirektionale Vertrauensstellung erforderlich. Eine Anleitung zum Erstellen der bidirektionalen Vertrauensstellung finden Sie in [diesem Artikel](https://technet.microsoft.com/library/cc816590.aspx).
 
## <a name="step-2-modify-contosocom-federation-settings"></a>Schritt 2: Ändern der Verbundeinstellungen für „contoso.com“ 
 
Für den Verbund zwischen einer einzelnen Domäne und AD FS ist der Standardaussteller „http\://ADFSServiceFQDN/adfs/services/trust“ festgelegt (Beispiel: `http://fs.contoso.com/adfs/services/trust`). Azure Active Directory benötigt für jede Verbunddomäne einen eindeutigen Aussteller. Da die gleiche AD FS-Instanz zwei Domänen zu einem Verbund zusammenfasst, muss der Ausstellerwert so angepasst werden, dass er für jede Domäne eindeutig ist, die AD FS mit Azure Active Directory zu einem Verbund zusammenfasst. 
 
Öffnen Sie auf dem AD FS-Server Azure AD PowerShell. (Stellen Sie sicher, dass das MSOnline-Modul installiert ist.) Führen Sie die folgenden Schritte aus:
 
Stellen Sie mit „Connect-MsolService“ eine Verbindung mit der Azure Active Directory-Instanz her, die die Domäne „contoso.com“ enthält. Aktualisieren Sie mit „Update-MsolFederatedDomain -DomainName contoso.com –SupportMultipleDomain“ die Verbundeinstellungen für „contoso.com“.
 
Der Aussteller in der Domänenverbundeinstellung wird in „http\://contoso.com/adfs/services/trust“ geändert, und für die Vertrauensstellung der vertrauenden Seite von Azure AD wird eine Ausstellungsanspruchsregel hinzugefügt, um auf der Grundlage des UPN-Suffixes den korrekten issuerId-Wert auszugeben.
 
## <a name="step-3-federate-fabrikamcom-with-ad-fs"></a>Schritt 3: Erstellen eines Verbunds mit „fabrikam.com“ und AD FS
 
Führen Sie in einer PowerShell-Sitzung für Azure AD folgende Schritte aus: Stellen Sie eine Verbindung mit dem Azure Active Directory-Verzeichnis her, das die Domäne „fabrikam.com“ enthält:

    Connect-MsolService
Konvertieren Sie die verwaltete Domäne „fabrikam.com“ in eine Verbunddomäne:

    Convert-MsolDomainToFederated -DomainName fabrikam.com -Verbose -SupportMultipleDomain
 
Durch den obigen Vorgang werden die Domäne „fabrikam.com“ und die gleiche AD FS-Instanz zu einem Verbund zusammengefasst. Die Domäneneinstellungen können für beide Domänen mithilfe von „Get-MsolDomainFederationSettings“ überprüft werden.

## <a name="next-steps"></a>Nächste Schritte
[Herstellen einer Verbindung zwischen Active Directory und Azure Active Directory](whatis-hybrid-identity.md)
