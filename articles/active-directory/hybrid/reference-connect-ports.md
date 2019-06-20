---
title: Erforderliche Ports und Protokolle für die Hybrid-Identität – Azure | Microsoft-Dokumentation
description: Diese Seite ist eine Seite technische Referenz für Ports, die für Azure AD Connect geöffnet sein müssen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/02/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48d2ef0de9ae59e63cd9957200c46c788e2d785f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60387304"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Erforderliche Ports und Protokolle für die Hybrid-Identität
Das folgende Dokument ist eine technische Referenz zu den erforderlichen Ports und Protokollen für die Implementierung einer Hybrid-Identitätslösung. Sehen Sie sich die folgende Abbildung und die entsprechende Tabelle an.

![Was ist Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabelle 1: Azure AD Connect und lokales AD
In dieser Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen dem Azure AD Connect-Server und der lokalen AD-Instanz erforderlich sind.

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-Suchen in der Zielgesamtstruktur |
| Kerberos |88 (TCP/UDP) |Kerberos-Authentifizierung für die AD-Gesamtstruktur |
| MS-RPC |135 (TCP/UDP) |Wird bei der anfänglichen Konfiguration des Azure AD Connect-Assistenten beim Binden an die AD-Gesamtstruktur sowie bei der Kennwortsynchronisierung verwendet |
| LDAP |389 (TCP/UDP) |Wird zum Importieren von Daten aus AD verwendet. Daten werden per Kerberos Sign & Seal verschlüsselt. |
| SMB | 445 (TCP/UDP) |Wird bei nahtlosem SSO zum Erstellen eines Computerkontos in der Active Directory-Gesamtstruktur verwendet. |
| LDAP/SSL |636 (TCP/UDP) |Wird zum Importieren von Daten aus AD verwendet. Die Datenübertragung ist signiert und verschlüsselt. Gilt nur bei Verwendung von SSL. |
| RPC |49152- 65535 (Random High RPC-Port) (TCP/UDP) |Wird bei der anfänglichen Konfiguration von Azure AD Connect beim Binden an die AD-Gesamtstrukturen und bei der Kennwortsynchronisierung verwendet. Weitere Informationen finden Sie unter [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017) und [KB224196](https://support.microsoft.com/kb/224196). |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabelle 2: Azure AD Connect und Azure AD
In dieser Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen dem Azure AD Connect-Server und Azure AD erforderlich sind.

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Wird zum Herunterladen von Zertifikatsperrlisten zur Überprüfung von SSL-Zertifikaten verwendet |
| HTTPS |443 (TCP/UDP) |Wird zum Synchronisieren mit Azure AD verwendet |

Eine Liste der URLs und IP-Adressen, die in der Firewall geöffnet sein müssen, finden Sie unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabelle 3: Azure AD Connect und AD FS-Verbund-/WAP-Server
In dieser Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen dem Azure AD Connect-Server und AD FS-Verbund-/WAP-Servern erforderlich sind.  

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Wird zum Herunterladen von Zertifikatsperrlisten zur Überprüfung von SSL-Zertifikaten verwendet |
| HTTPS |443 (TCP/UDP) |Wird zum Synchronisieren mit Azure AD verwendet |
| WinRM |5985 |WinRM-Listener |

## <a name="table-4---wap-and-federation-servers"></a>Tabelle 4: WAP- und Verbundserver
In dieser Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen den Verbundservern und WAP-Servern erforderlich sind.

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Wird für die Authentifizierung verwendet |

## <a name="table-5---wap-and-users"></a>Tabelle 5: WAP und Benutzer
In dieser Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen Benutzern und den WAP-Servern erforderlich sind.

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Wird für die Geräteauthentifizierung verwendet |
| TCP |49443 (TCP) |Wird für die Zertifikatauthentifizierung verwendet |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabellen 6a und 6b: Passthrough-Authentifizierung mit einmaligem Anmelden (SSO) und Kennworthashsynchronisierung mit einmaligem Anmelden (SSO)
In dieser folgenden Tabelle werden die Ports und Protokolle beschrieben, die für die Kommunikation zwischen Azure AD Connect und Azure AD erforderlich sind.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabelle 6a: Passthrough-Authentifizierung mit SSO
|Protocol|Portnummer|BESCHREIBUNG
| --- | --- | ---
|HTTP|80|Ermöglicht ausgehenden HTTP-Verkehr für die Sicherheitsüberprüfung, z.B. SSL. Wird auch für die ordnungsgemäße Funktion automatischer Updates für den Connector benötigt.
|HTTPS|443| Ermöglicht ausgehenden HTTPS-Datenverkehr für Vorgänge wie das Aktivieren und Deaktivieren des Features, das Registrieren von Connectors, das Herunterladen von Connectorupdates und das Verarbeiten aller Benutzeranmeldeanforderungen.

Darüber hinaus muss Azure AD Connect in der Lage sein, direkte IP-Verbindungen mit den [IP-Adressbereichen des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) herzustellen.

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabelle 6b: Kennworthashsynchronisierung mit SSO

|Protocol|Portnummer|BESCHREIBUNG
| --- | --- | ---
|HTTPS|443| Ermöglicht die SSO-Registrierung (nur für den SSO-Registrierungsprozess erforderlich)

Darüber hinaus muss Azure AD Connect in der Lage sein, direkte IP-Verbindungen mit den [IP-Adressbereichen des Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) herzustellen. Auch in diesem Fall ist dies nur für den SSO-Registrierungsprozess erforderlich.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabelle 7a und 7b: Azure AD Connect Health-Agent für (AD FS/Sync) und Azure AD
In den folgenden Tabellen werden die Endpunkte, Ports und Protokolle beschrieben, die für die Kommunikation zwischen Azure AD Connect Health-Agents und Azure AD erforderlich sind.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabelle 7a: Ports und Protokolle für den Azure AD Connect Health-Agent für (AD FS/Sync) und Azure AD
In dieser Tabelle werden die folgenden ausgehenden Ports und Protokolle beschrieben, die für die Kommunikation zwischen Azure AD Connect Health-Agents und Azure AD erforderlich sind.  

| Protocol | Ports | BESCHREIBUNG |
| --- | --- | --- |
| HTTPS |443 (TCP/UDP) |Ausgehend |
| Azure-Servicebus |5671 (TCP/UDP) |Ausgehend |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabelle 7b: Endpunkte für Azure AD Connect Health-Agent für (AD FS/Sync) und Azure AD
Eine Liste mit Endpunkten finden Sie im [Abschnitt mit den Anforderungen für den Azure AD Connect Health-Agent](how-to-connect-health-agent-install.md#requirements).

