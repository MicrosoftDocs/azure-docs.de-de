---
title: Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory | Microsoft-Dokumentation
description: Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce1d2aaed85f61e2e18f696d749219a1b951c862
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474262"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory 

Dieser Artikel gilt nur für die folgenden Geräte: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Weitere Informationen zu Windows 10 und Windows Server 2016 finden Sie unter [Beheben von Problemen mit Geräten unter Windows 10 und Windows Server 2016 mit Hybrideinbindung in Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

In diesem Artikel wird vorausgesetzt, dass Sie [Geräte mit Hybrideinbindung in Azure Active Directory konfiguriert haben](hybrid-azuread-join-plan.md), um die folgenden Szenarien zu unterstützen:

- Gerätebasierter bedingter Zugriff


Dieser Artikel enthält Anleitungen zur Problembehandlung zum Beheben potenzieller Probleme.  

**Wichtige Informationen:** 

- Azure AD Hybrid Join für Windows-Geräte mit einer Vorgängerversion funktioniert etwas anders als unter Windows 10. Viele Kunden wissen nicht, dass sie Active Directory-Verbunddienste (AD FS) (für Verbunddomänen) oder nahtloses einmaliges Anmelden (für verwaltete Domänen) benötigen.

- Wenn Kunden mit Verbunddomänen den Dienstverbindungspunkt so konfigurieren, dass er auf den verwalteten Domänennamen zeigt (z.B. „contoso.onmicrosoft.com“ statt „contoso.com“), funktioniert Azure AD Hybrid Join nicht für Windows-Geräte mit einer Vorgängerversion.

- Die maximale Anzahl von Geräten pro Benutzer gilt derzeit auch für in Azure AD Hybrid eingebundene Geräte mit einer Vorgängerversion. 

- Das gleiche physische Gerät erscheint mehrmals in Azure AD, wenn sich mehrere Domänenbenutzer auf Geräten mit einer Vorgängerversion anmelden, die in Azure AD Hybrid eingebunden sind.  Beispiel: Wenn *jdoe* und *jharnett* sich auf einem Gerät anmelden, wird für jeden dieser Benutzer eine separate Registrierung (DeviceID) auf der Registerkarte **USER** erstellt. 

- Aufgrund einer Neuinstallation des Betriebssystems oder einer manuellen Neuregistrierung können Sie mehrere Einträge für ein Gerät auf der Registerkarte „Benutzerinformationen“ abrufen.

- Bei der anfänglichen Gerätekonfiguration für die Registrierung bzw. den Beitritt von Geräten wird zunächst eine Anmeldung oder Sperren/Entsperren versucht. Es kann eine Verzögerung von bis zu 5 Minuten auftreten, die durch eine Aufgabe der Aufgabenplanung ausgelöst wird. 

- Stellen Sie sicher, dass [KB4284842](https://support.microsoft.com/help/4284842) installiert, falls Sie Windows 7 SP1 oder Windows Server 2008 R2 SP1 verwenden. Dieses Update verhindert zukünftige Authentifizierungsfehler, wenn Kunden den Zugriff auf geschützte Schlüssel nach dem Ändern des Kennworts verlieren.

## <a name="step-1-retrieve-the-registration-status"></a>Schritt 1: Abrufen des Registrierungsstatus 

**So überprüfen Sie den Registrierungsstatus:**  

1. Melden Sie sich mit dem Benutzerkonto an, das eine Hybrideinbindung in Azure AD ausgeführt hat.

2. Öffnen Sie die Eingabeaufforderung. 

3. Geben Sie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i` ein

Dieser Befehl zeigt ein Dialogfeld an, das Ihnen ausführliche Informationen zum Einbindungsstatus bietet.

![Workplace Join für Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Schritt 2: Bewerten des Status der Azure AD-Hybrideinbindung 

Wenn das Gerät nicht in Azure AD Hybrid eingebunden war, können Sie versuchen, es in den Dienst einzubinden, indem Sie auf die Schaltfläche „Verknüpfen“ klicken. Falls der Versuch fehlschlägt, werden die Fehlerdetails angezeigt.


**Folgende Probleme treten am häufigsten auf:**

- AD FS oder Azure AD ist falsch konfiguriert

    ![Workplace Join für Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
  - „Autoworkplace.exe“ kann sich nicht unbeaufsichtigt bei Azure AD oder AD FS authentifizieren. Die Fehlerursache können eine fehlende bzw. falsche Konfiguration von AD FS (für Verbunddomänen) oder des nahtlosen einmaligen Azure AD-Anmeldens (für verwaltete Domänen) oder aber Netzwerkprobleme sein. 
    
    - Möglicherweise ist die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für den Benutzer aktiviert/konfiguriert, und „WIAORMULTIAUTHN“ nicht auf dem AD FS-Server konfiguriert. 
     
    - Eine weitere Möglichkeit ist, dass die Seite der Startbereichsermittlung (Home Realm Discovery, HRD) auf eine Benutzerinteraktion wartet, wodurch **autoworkplace.exe** daran gehindert wird, unbeaufsichtigt ein Token abzurufen.
     
    - Möglicherweise fehlen AD FS- und Azure AD-URLs in der Intranetzone des IE.
     
    - Probleme mit der Netzwerkkonnektiviät verhindern möglicherweise, dass **autoworkplace.exe** eine Verbindung mit AD FS- oder Azure AD-URLs herstellen kann. 
     
    - **Autoworkplace.exe** erfordert, dass der Client über eine direkte Sichtverbindung vom Client zum lokalen AD-Domänencontroller der Organisation verfügt. Das bedeutet, Azure AD Hybrid Join wird nur dann erfolgreich ausgeführt, wenn der Client mit dem Intranet der Organisation verbunden ist.
     
    - Ihre Organisation verwendet nahtloses einmaliges Azure AD-Anmelden, `https://autologon.microsoftazuread-sso.com` oder `https://aadg.windows.net.nsatc.net` ist nicht in den IE-Intraneteinstellungen des Geräts vorhanden, und **Updates der Statusleiste über ein Skript zulassen** ist nicht für die Intranetzone aktiviert.

- Sie sind nicht als Domänenbenutzer angemeldet

    ![Workplace Join für Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Dieses Problem kann aus verschiedenen Gründen auftreten:
    
    - Der angemeldete Benutzer ist kein Domänenbenutzer (sondern beispielsweise ein lokaler Benutzer). Eine Azure AD-Hybrideinbindung auf Geräten niedriger Ebene wird nur für Domänenbenutzer unterstützt.
    
    - Der Client kann keine Verbindung mit einem Domänencontroller herstellen.    

- Ein Kontingent wurde erreicht

    ![Workplace Join für Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- Der Dienst antwortet nicht 

    ![Workplace Join für Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Die Statusinformationen finden Sie auch im Ereignisprotokoll unter **Anwendungs- und Dienstprotokolle\Microsoft-Workplace Join**.
  
**Die häufigsten Ursachen für Fehler bei der Azure AD-Hybrideinbindung sind:** 

- Der Computer ist weder mit dem internen Netzwerk Ihrer Organisation noch mit einem VPN mit Verbindung mit Ihrem lokalen AD-Domänencontroller verbunden.

- Sie sind über ein lokales Computerkonto bei ihrem Computer angemeldet. 

- Probleme bei der Dienstkonfiguration: 

  - Der AD FS-Server wurde für die Unterstützung von **WIAORMULTIAUTHN** konfiguriert. 

  - Die Gesamtstruktur Ihres Computers hat kein Dienstverbindungspunkt-Objekt, das auf Ihren verifizierten Domänennamen in Azure AD verweist. 
  
  - Falls Ihre Domäne verwaltet wird: Das nahtlose einmalige Anmelden wurde nicht konfiguriert bzw. funktioniert nicht.

  - Ein Benutzer hat den Grenzwert von Geräten erreicht. 

## <a name="next-steps"></a>Nächste Schritte

Bei Fragen lesen Sie unter [Häufig gestellte Fragen zur Geräteverwaltung](faq.md) nach.  
