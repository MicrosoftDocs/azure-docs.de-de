---
title: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung in Azure Active Directory (Azure AD) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Active Directory eingebundene Hybridgeräte konfigurieren.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64dd8067654246f7c9a077d027c068df820f439d
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688700"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Anleitung: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung

Ähnlich wie ein Benutzer ist ein Gerät eine weitere zentrale Identität, die Sie schützen sowie jederzeit und überall zum Schutz Ihrer Ressourcen verwenden können. Dafür können Sie die Geräteidentitäten mit einer der folgenden Methoden in Azure AD bereitstellen und verwalten:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

Durch das Bereitstellen Ihrer Geräte in Azure AD maximieren Sie die Produktivität Ihrer Benutzer durch einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch den [bedingten Zugriff](../active-directory-conditional-access-azure-portal.md) sichern.

Wenn Sie in einer lokalen Active Directory (AD)-Umgebung Ihre in die AD-Domäne eingebundenen Compute in Azure AD einbinden möchten, kann dies durch Vornehmen einer Einbindung in Azure AD Hybrid erfolgen. Dieser Artikel enthält eine Anleitung zum Implementieren einer Azure AD-Hybrideinbindung in Ihre Umgebung. 

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie die [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](../device-management-introduction.md) gelesen haben.

> [!NOTE]
> Die mindestens erforderliche Domänen- und Gesamtstruktur-Funktionsebene für Hybrid-Azure AD-Einbindung in Windows 10 ist Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planen Ihrer Implementierung

Um Ihre Azure AD-Hybridimplementierung zu planen, sollten Sie sich mit folgenden Themen vertraut machen:

|   |   |
| --- | --- |
| ![Prüfen][1] | Überprüfung unterstützter Geräte |
| ![Prüfen][1] | Überprüfung wichtiger Informationen |
| ![Prüfen][1] | Überprüfung der kontrollierten Überprüfung der Azure AD-Hybrideinbindung |
| ![Prüfen][1] | Auswählen Ihres Szenarios, basierend auf Ihrer Identitätsinfrastruktur |
| ![Prüfen][1] | Überprüfung der lokalen AD UPN-Unterstützung (Benutzerprinzipalname) für Azure AD-Hybrideinbindung |

## <a name="review-supported-devices"></a>Überprüfung unterstützter Geräte

Azure AD Hybrid Join unterstützt zahlreiche Windows-Geräte. Da die Konfiguration für Geräte mit älteren Versionen von Windows zusätzliche oder unterschiedliche Schritte erfordert, werden die unterstützten Geräte in zwei Kategorien eingeteilt:

### <a name="windows-current-devices"></a>Aktuelle Windows-Geräte

- Windows 10
- Windows Server 2016
- Windows Server 2019

Für Geräte, auf denen das Windows-Desktopbetriebssystem ausgeführt wird, sind die unterstützten Versionen in diesem Artikel aufgeführt: [Windows 10 Releaseinformationen](https://docs.microsoft.com/windows/release-information/). Als bewährte Methode empfiehlt Microsoft, ein Upgrade auf die aktuelle Version von Windows 10 durchzuführen.

### <a name="windows-down-level-devices"></a>Kompatible Windows-Geräte

- Windows 8.1
- Windows 7. Informationen zum Support für Windows 7 finden Sie in diesem Artikel: [Unterstützung für Windows 7 wird beendet](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Als ersten Planungsschritt sollten Sie Ihre Umgebung überprüfen und ermitteln, ob Sie Unterstützung für kompatible Windows-Geräte benötigen.

## <a name="review-things-you-should-know"></a>Überprüfung wichtiger Informationen

Azure AD-Hybrideinbindung wird zurzeit nicht unterstützt, wenn Ihre Umgebung aus einer einzelnen AD-Gesamtstruktur besteht, die Identitätsdaten mit mehr als einem Azure AD-Mandanten synchronisiert.

Azure AD Hybrideinbindung wird zurzeit nicht unterstützt, wenn eine virtuelle Desktopinfrastruktur (VDI) verwendet wird.

Azure AD Hybrid wird für FIPS-kompatible TPMs nicht unterstützt. Wenn Ihre Geräte über FIPS-kompatible TPMs verfügen, müssen Sie sie vor dem Fortsetzen der Hybrid-Azure AD-Einbindung deaktivieren. Microsoft stellt keine Tools zum Deaktivieren des FIPS-Modus für TPMs bereit, da dieser vom TPM-Hersteller abhängig ist. Wenden Sie sich an Ihren Hardware-OEM, um Unterstützung zu erhalten.

Azure AD Hybrideinbindung wird für Windows Server, die die Domänencontrollerrolle ausführen, nicht unterstützt.

Azure AD Hybrideinbindung wird für kompatible Windows-Geräte nicht unterstützt, wenn sie das Roaming von Anmeldeinformationen oder Benutzerprofilen verwenden.

Wenn Sie das Systemvorbereitungstool (Sysprep) verwenden und ein Image einer niedrigeren Version als **Windows 10 1809** für die Installation verwenden, stellen Sie sicher, dass dieses Image nicht von einem Gerät stammt, das bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.

Wenn Sie zusätzliche VMs mit einer Momentaufnahme des virtuellen Computers erstellen, stellen Sie sicher, dass diese Momentaufnahme nicht von einem Computer stammt, der bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.

Wenn Ihre in die Windows 10-Domäne eingebundenen Geräte bereits für Ihren Mandanten bei [Azure AD registriert](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) sind, wird dringend empfohlen, diesen Status vor dem Aktivieren von Azure AD Hybrid Join zu entfernen. In Windows 10 Release 1809 wurden die folgenden Änderungen vorgenommen, um diesen Doppelstatus zu vermeiden:

- Eine etwaige Registrierung bei Azure AD würde nach der Azure AD-Hybrideinbindung des Geräts automatisch entfernt werden.
- Sie können verhindern, dass Ihr in die Domäne eingebundenes Gerät bei Azure AD registriert wird, indem Sie diesen Registrierungsschlüssel hinzufügen: HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- Diese Änderung ist jetzt für Windows 10, Release 1803 mit angewendetem KB4489894 verfügbar. Wenn Sie jedoch Windows Hello for Business konfiguriert haben, muss der Benutzer Windows Hello for Business nach der zweistufigen Bereinigung erneut einrichten.


## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Überprüfung der kontrollierten Überprüfung der Azure AD-Hybrideinbindung

Wenn alle Voraussetzungen erfüllt sind, werden Windows-Geräte automatisch als Geräte bei Ihrem Azure AD-Mandanten registriert. Der Zustand dieser Geräteidentitäten in Azure AD wird als Azure AD-Hybrideinbindung bezeichnet. Weitere Informationen zu den in diesem Artikel beschriebenen Konzepten finden Sie in den Artikeln [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](overview.md) und [Planen der Implementierung Ihrer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md).

Organisationen sollten eine kontrollierte Überprüfung der Azure AD-Hybrideinbindung vornehmen, bevor sie diese in ihrer gesamten Organisation gleichzeitig aktivieren. Das Verständnis, wie Sie dies erreichen können, erhalten Sie in dem Artikel [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md).


## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Auswählen Ihres Szenarios, basierend auf Ihrer Identitätsinfrastruktur

Azure AD-Hybrideinbindung funktioniert sowohl mit verwalteten Umgebungen als auch mit Verbundumgebungen.  

### <a name="managed-environment"></a>Verwaltete Umgebung

Eine verwaltete Umgebung kann entweder durch [Kennworthashsynchronisierung](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) (Password Hash Sync, PHS) oder [Passthrough-Authentifizierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) (Pass Through Authentication, PTA) mit [nahtlosem einmaligem Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) (Seamless Single Sign-On, Seamless SSO) bereitgestellt werden.

In diesen Szenarien müssen Sie keinen Verbundserver für die Authentifizierung konfigurieren.

### <a name="federated-environment"></a>Verbundumgebung

Bei Verbundumgebungen sollte ein Identitätsanbieter verwendet werden, der die folgenden Anforderungen erfüllt:

- **WS-Trust-Protokoll:** Dieses Protokoll ist erforderlich, um aktuelle Azure AD-hybrideingebundene Windows-Geräte mit Azure AD zu authentifizieren.
- **WIAORMULTIAUTHN-Anspruch:** Dieser Anspruch ist erforderlich, um eine Azure AD-Hybrideinbindung für kompatible Windows-Geräte durchzuführen.

Wenn Sie eine Verbundumgebung besitzen, die Active Directory-Verbunddienste (AD FS) verwendet, werden die oben genannten Anforderungen bereits unterstützt.

> [!NOTE]
> Azure AD unterstützt keine Smartcards oder Zertifikate in verwalteten Domänen.

Ab Version 1.1.819.0 bietet Azure AD Connect einen Assistenten für die Konfiguration der Azure AD-Hybrideinbindung. Mit dem Assistenten können Sie den Konfigurationsprozess erheblich vereinfachen. Wenn das Installieren der erforderlichen Version von Azure AD Connect keine Option für Sie ist, informieren Sie sich über die [manuelle Konfiguration der Geräteregistrierung](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Lesen Sie auf Grundlage des Szenarios, das Ihrer Identitätsinfrastruktur entspricht:

- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-managed-domains.md)



## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Überprüfung der lokalen AD UPN-Unterstützung (Benutzerprinzipalname) für Azure AD-Hybrideinbindung

In einigen Fällen können Ihre lokalen AD-UPNs von den Azure AD-UPNs abweichen. In diesen Fällen bietet Azure AD Hybrid Join unter Windows 10 auf Grundlage der [Authentifizierungsmethode](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), dem Domänentyp und der Windows 10-Version eingeschränkte Unterstützung für lokale AD-UPNs. Es gibt zwei Arten lokaler AD-UPNs, die in Ihrer Umgebung vorhanden sein können:

- Routingfähige Benutzerprinzipalnamen (UPNs): Ein routingfähiger UPN verfügt über eine gültige überprüfte Domäne, die bei einer Domänenregistrierungsstelle registriert ist. Ist beispielsweise „contoso.com“ die primäre Domäne in Azure AD, ist „contoso.org“ die primäre Domäne im lokalen Active Directory, die Contoso gehört und [in Azure AD überprüft wird](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).
- Nicht routingfähige Benutzerprinzipalnamen (UPNs): Ein nicht routingfähiger UPN verfügt nicht über eine überprüfte Domäne. Sie ist nur in einem privaten Netzwerk Ihrer Organisation gültig. Ist beispielsweise „contoso.com“ die primäre Domäne in Azure AD, ist „contoso.local“ die primäre Domäne im lokalen Active Directory, aber keine überprüfbare Domäne im Internet und wird nur innerhalb des Contoso-Netzwerks verwendet.

Die folgende Tabelle enthält Details zur Unterstützung dieser lokalen AD UPNs in Azure AD Hybrid Join unter Windows 10

| Art der lokalen AD UPNs | Domänentyp | Windows 10-Version | Beschreibung |
| ----- | ----- | ----- | ----- |
| Routingfähig | Im Verbund | Version 1703 | Allgemein verfügbar |
| Nicht routingfähig | Im Verbund | Version 1803 | Allgemein verfügbar |
| Routingfähig | Verwaltet | Nicht unterstützt | |
| Nicht routingfähig | Verwaltet | Nicht unterstützt | |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-federated-domains.md)
> [Konfigurieren von Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
