---
title: 'Azure AD Connect: Aufgaben nach der Konfiguration für die Hybrid-Azure AD-Einbindung | Microsoft-Dokumentation'
description: Dieses Dokument enthält ausführliche Informationen zu den Aufgaben nach der Konfiguration, die für die Erstellung der Hybrid-Azure AD-Einbindung erforderlich sind.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9af969700f4f2dfbedc4833badd7e7349696302
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244590"
---
# <a name="post-configuration-tasks-for-hybrid-azure-ad-join"></a>Aufgaben nach der Konfiguration für die Hybrid-Azure AD-Einbindung

Nachdem Sie Azure AD Connect ausgeführt haben, um Ihre Organisation für die Hybrid-Azure AD-Einbindung zu konfigurieren, müssen Sie noch einige zusätzliche Schritte ausführen, um dieses Setup abzuschließen.  Führen Sie nur die Schritte aus, die für Ihre Geräte gelten.

## <a name="1-configure-controlled-rollout-optional"></a>1. Konfigurieren eines kontrollierten Rollouts (optional)
Alle in die Domäne eingebundenen Geräte, auf denen Windows 10 und Windows Server 2016 ausgeführt wird, werden automatisch bei Azure AD registriert, nachdem alle Konfigurationsschritte abgeschlossen sind. Falls Sie anstelle dieser automatischen Registrierung einen kontrollierten Rollout vorziehen, können Sie die Gruppenrichtlinie verwenden, um den automatischen Rollout selektiv zu aktivieren oder zu deaktivieren.  Diese Gruppenrichtlinie sollte festgelegt werden, bevor mit den weiteren Konfigurationsschritten begonnen wird: Azure AD
* Erstellen Sie ein Gruppenrichtlinienobjekt in Ihrer Active Directory-Instanz.
* Geben Sie ihm einen Namen (z.B. „Hybrid-Azure AD-Einbindung“).
* Bearbeiten Sie und navigieren Sie zu:  „Computerkonfiguration“ > „Richtlinien“ > „Administrative Vorlagen“ > „Windows-Komponenten“ > „Geräteregistrierung“.

>[!NOTE]
>Für 2012R2 befinden sich die Richtlinieneinstellungen unter **Computerkonfiguration > Richtlinien > Administrative Vorlagen > Windows-Komponenten > Arbeitsbereichverknüpfung > Clientcomputer automatisch in Arbeitsbereich einbinden**

* Deaktivieren Sie diese Einstellung:  „In die Domäne eingebundene Computer als Geräte registrieren“.
* Übernehmen Sie die Änderung, und klicken Sie auf „OK“.
* Verknüpfen Sie das GPO mit dem Speicherort Ihrer Wahl (Organisationseinheit, Sicherheitsgruppe oder Domäne für alle Geräte).

## <a name="2-configure-network-with-device-registration-endpoints"></a>2. Konfigurieren des Netzwerks mit Endpunkten für die Geräteregistrierung
Achten Sie darauf, dass von Computern innerhalb Ihres Unternehmensnetzwerks zur Registrierung bei Azure AD auf die folgenden URLs zugegriffen werden kann:

* https://enterpriseregistration.windows.net
* https://login.microsoftonline.com
* https://device.login.microsoftonline.com 

## <a name="3-implement-wpad-for-windows-10-devices"></a>3. Implementieren von WPAD für Windows 10-Geräte
Wenn in Ihrer Organisation in ausgehender Richtung über einen Proxy auf das Internet zugegriffen wird, sollten Sie WPAD (Web Proxy Auto-Discovery) installieren, damit Windows 10-Computer bei Azure AD registriert werden können.

## <a name="4-configure-the-scp-in-any-forests-that-were-not-configured-by-azure-ad-connect"></a>4. Konfigurieren des Dienstverbindungspunkts in allen Gesamtstrukturen, die nicht per Azure AD Connect konfiguriert wurden 

Der Dienstverbindungspunkt enthält Ihre Azure AD-Mandanteninformationen, die von Ihren Geräten für die automatische Registrierung verwendet werden.  Führen Sie das PowerShell-Skript „ConfigureSCP.ps1“ aus, das Sie von Azure AD Connect heruntergeladen haben.

## <a name="5-configure-any-federation-service-that-was-not-configured-by-azure-ad-connect"></a>5. Konfigurieren aller Verbunddienste, die nicht per Azure AD Connect konfiguriert wurden

Wenn Ihre Organisation einen Verbunddienst verwendet, um sich an Azure AD anzumelden, müssen die Anspruchsregeln in Ihrer Vertrauensstellung der vertrauenden Azure AD-Seite die Geräteauthentifizierung zulassen. Wenn Sie den Verbund mit AD FS nutzen, helfen Ihnen die Informationen in der [Hilfe zu AD FS](https://aka.ms/aadrptclaimrules) beim Generieren der Anspruchsregeln weiter. Falls Sie eine Verbundlösung verwenden, die nicht von Microsoft stammt, können Sie sich an den Anbieter wenden, um Hilfe zu erhalten.  

>[!NOTE]
>Wenn Sie über kompatible Windows-Geräte verfügen, muss der Dienst das Ausstellen der Ansprüche authenticationmethod und wiaormultiauthn unterstützen, wenn Anforderungen für die Azure AD-Vertrauensstellung eingehen. In AD FS sollten Sie eine Ausstellungstransformationsregel hinzufügen, die die Authentifizierungsmethode weitergibt.

## <a name="6-enable-azure-ad-seamless-sso-for-windows-down-level-devices"></a>6. Aktivieren des nahtlosen einmaligen Anmeldens mit Azure AD für kompatible Windows-Geräte

Wenn Sie in Ihrer Organisation die Kennworthashsynchronisierung oder die Passthrough-Authentifizierung nutzen, um sich an Azure AD anzumelden, können Sie das nahtlose einmalige Anmelden mit Azure AD mit diesem Anmeldeverfahren aktivieren, um kompatible Windows-Geräte zu authentifizieren: https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso. 

## <a name="7-set-azure-ad-policy-for-windows-down-level-devices"></a>7. Festlegen der Azure AD-Richtlinie für kompatible Windows-Geräte

Zum Registrieren von kompatiblen Windows-Geräten müssen Sie sicherstellen, dass für die Azure AD-Richtlinie das Registrieren von Geräten durch Benutzer zulässig ist. 

* Melden Sie sich im Azure-Portal an Ihrem Konto an.
* Wechseln Sie zu:  „Azure Active Directory“ > „Geräte“ > „Geräteeinstellungen“.
* Legen Sie „Benutzer dürfen ihre Geräte für Azure AD registrieren“ auf „ALLE“ fest.
* Klicken Sie auf Speichern.

## <a name="8-add-azure-ad-endpoint-to-windows-down-level-devices"></a>8. Hinzufügen eines Azure AD-Endpunkts für Windows-kompatible Geräte

Fügen Sie den Endpunkt für die Azure AD-Geräteauthentifizierung den lokalen Intranetzonen auf Ihren kompatiblen Windows-Geräten hinzu, um beim Authentifizieren des Geräts Zertifikataufforderungen zu vermeiden: https://device.login.microsoftonline.com 

Aktivieren Sie bei Verwendung des [nahtlosen einmaligen Anmeldens](how-to-connect-sso.md) auch die Option „Statusleistenupdates über Skript zulassen“ für diese Zone, und fügen Sie den folgenden Endpunkt hinzu: https://autologon.microsoftazuread-sso.com. 

## <a name="9-install-microsoft-workplace-join-on-windows-down-level-devices"></a>9. Installieren von Microsoft Workplace Join auf kompatiblen Windows-Geräten

Dieses Installationsprogramm erstellt einen geplanten Task auf dem Gerätesystem, der im Kontext des Benutzers ausgeführt wird. Der Task wird ausgelöst, wenn sich der Benutzer bei Windows anmeldet. Nach der Authentifizierung per integrierter Windows-Authentifizierung bindet der Task das Gerät unter Verwendung der Anmeldeinformationen des Benutzers automatisch in Azure AD ein. Das Download Center befindet sich unter https://www.microsoft.com/download/details.aspx?id=53554. 

## <a name="10-configure-group-policy-to-allow-device-registration"></a>10. Konfigurieren der Gruppenrichtlinie, um die Geräteregistrierung zuzulassen

* Erstellen Sie ein Gruppenrichtlinienobjekt in Ihrer Active Directory-Instanz, falls Sie dies nicht bereits getan haben.
* Geben Sie ihm einen Namen (z.B. „Hybrid-Azure AD-Einbindung“).
* Bearbeiten Sie und navigieren Sie zu:  „Computerkonfiguration“ > „Richtlinien“ > „Administrative Vorlagen“ > „Windows-Komponenten“ > „Geräteregistrierung“.
* Aktivieren Sie:  „In die Domäne eingebundene Computer als Geräte registrieren“.
* Übernehmen Sie die Änderung, und klicken Sie auf „OK“.
* Verknüpfen Sie das GPO mit dem Speicherort Ihrer Wahl (Organisationseinheit, Sicherheitsgruppe oder Domäne für alle Geräte).

>[!NOTE]
>Für 2012R2 befinden sich die Richtlinieneinstellungen unter **Computerkonfiguration > Richtlinien > Administrative Vorlagen > Windows-Komponenten > Arbeitsbereichverknüpfung > Clientcomputer automatisch in Arbeitsbereich einbinden**

## <a name="next-steps"></a>Nächste Schritte
[Konfigurieren des Geräterückschreibens](how-to-connect-device-writeback.md)
