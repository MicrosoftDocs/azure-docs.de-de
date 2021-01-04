---
title: Beheben von Problemen mit Legacygeräten mit Hybrid-Azure Active Directory-Einbindung
description: Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a4e8ec75d6610e19f241d2047518c3a43132a6e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079018"
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
- Das gleiche physische Gerät erscheint mehrmals in Azure AD, wenn sich mehrere Domänenbenutzer auf Geräten mit einer Vorgängerversion anmelden, die in Azure AD Hybrid eingebunden sind.  Beispiel: Wenn *jdoe* und *jharnett* sich auf einem Gerät anmelden, wird für jeden dieser Benutzer eine separate Registrierung (DeviceID) auf der Registerkarte **USER** erstellt. 
- Aufgrund einer Neuinstallation des Betriebssystems oder einer manuellen Neuregistrierung können Sie mehrere Einträge für ein Gerät auf der Registerkarte „Benutzerinformationen“ abrufen.
- Bei der anfänglichen Gerätekonfiguration für die Registrierung bzw. den Beitritt von Geräten wird zunächst eine Anmeldung oder Sperren/Entsperren versucht. Es kann eine Verzögerung von bis zu 5 Minuten auftreten, die durch eine Aufgabe der Aufgabenplanung ausgelöst wird. 
- Stellen Sie sicher, dass [KB4284842](https://support.microsoft.com/help/4284842) installiert, falls Sie Windows 7 SP1 oder Windows Server 2008 R2 SP1 verwenden. Dieses Update verhindert zukünftige Authentifizierungsfehler, wenn Kunden den Zugriff auf geschützte Schlüssel nach dem Ändern des Kennworts verlieren.

## <a name="step-1-retrieve-the-registration-status"></a>Schritt 1: Abrufen des Registrierungsstatus 

**So überprüfen Sie den Registrierungsstatus:**  

1. Melden Sie sich mit dem Benutzerkonto an, das eine Hybrideinbindung in Azure AD ausgeführt hat.
1. Öffnen Sie die Eingabeaufforderung. 
1. Geben Sie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i` ein

Dieser Befehl zeigt ein Dialogfeld an, das Ihnen ausführliche Informationen zum Einbindungsstatus bietet.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Screenshot: Dialogfeld „Workplace Join for Windows“. Im Text, der eine E-Mail-Adresse enthält, ist angegeben, dass ein bestimmtes Gerät einem Arbeitsplatz hinzugefügt wurde." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Schritt 2: Bewerten des Status des Azure AD-Hybridbeitritts 

Wenn das Gerät nicht in Azure AD Hybrid eingebunden war, können Sie versuchen, es in den Dienst einzubinden, indem Sie auf die Schaltfläche „Verknüpfen“ klicken. Falls der Versuch fehlschlägt, werden die Fehlerdetails angezeigt.

**Folgende Probleme treten am häufigsten auf:**

- AD FS oder Azure AD ist falsch konfiguriert

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Screenshot: Dialogfeld „Workplace Join for Windows“. Im Text ist angegeben, dass während der Kontoauthentifizierung ein Fehler aufgetreten ist." border="false":::
    
   - „Autoworkplace.exe“ kann sich nicht unbeaufsichtigt bei Azure AD oder AD FS authentifizieren. Die Fehlerursache können eine fehlende bzw. falsche Konfiguration von AD FS (für Verbunddomänen) oder des nahtlosen einmaligen Azure AD-Anmeldens (für verwaltete Domänen) oder aber Netzwerkprobleme sein. 
   - Möglicherweise ist die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) für den Benutzer aktiviert/konfiguriert, und „WIAORMULTIAUTHN“ nicht auf dem AD FS-Server konfiguriert. 
   - Eine weitere Möglichkeit ist, dass die Seite der Startbereichsermittlung (Home Realm Discovery, HRD) auf eine Benutzerinteraktion wartet, wodurch **autoworkplace.exe** daran gehindert wird, unbeaufsichtigt ein Token abzurufen.
   - Möglicherweise fehlen AD FS- und Azure AD-URLs in der Intranetzone des IE.
   - Probleme mit der Netzwerkkonnektiviät verhindern möglicherweise, dass **autoworkplace.exe** eine Verbindung mit AD FS- oder Azure AD-URLs herstellen kann. 
   - **Autoworkplace.exe** erfordert, dass der Client über eine direkte Sichtverbindung vom Client zum lokalen AD-Domänencontroller der Organisation verfügt. Das bedeutet, Azure AD Hybrid Join wird nur dann erfolgreich ausgeführt, wenn der Client mit dem Intranet der Organisation verbunden ist.
   - Ihre Organisation verwendet nahtloses einmaliges Azure AD-Anmelden, `https://autologon.microsoftazuread-sso.com` oder `https://aadg.windows.net.nsatc.net` ist nicht in den IE-Intraneteinstellungen des Geräts vorhanden, und **Updates der Statusleiste über ein Skript zulassen** ist nicht für die Intranetzone aktiviert.
- Sie sind nicht als Domänenbenutzer angemeldet

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Screenshot: Dialogfeld „Workplace Join for Windows“. Im Text ist angegeben, dass während der Kontoüberprüfung ein Fehler aufgetreten ist." border="false":::

   Dieses Problem kann aus verschiedenen Gründen auftreten:

   - Der angemeldete Benutzer ist kein Domänenbenutzer (sondern beispielsweise ein lokaler Benutzer). Eine Azure AD-Hybrideinbindung auf Geräten niedriger Ebene wird nur für Domänenbenutzer unterstützt.
   - Der Client kann keine Verbindung mit einem Domänencontroller herstellen.    
- Ein Kontingent wurde erreicht

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Screenshot: Dialogfeld „Workplace Join for Windows“. Im Text ist ein Fehler angegeben, da der Benutzer die maximale Anzahl von eingebundenen Geräten erreicht hat." border="false":::

- Der Dienst antwortet nicht 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Screenshot: Dialogfeld „Workplace Join for Windows“. Im Text ist angegeben, dass ein Fehler aufgetreten ist, weil der Server nicht reagiert hat." border="false":::

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
