---
title: Zertifikaterneuerung für Office 365- und Azure AD-Benutzer | Microsoft-Dokumentation
description: In diesem Artikel wird für Office 365-Benutzer erläutert, wie Probleme mit E-Mails behoben werden, die sie zum Erneuern eines Zertifikats auffordern.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d98a1aabef2de505e66b2127226b9e89cd791e20
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60244827"
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Erneuern von Verbundzertifikaten für Office 365 und Azure Active Directory
## <a name="overview"></a>Übersicht
Damit ein erfolgreicher Verbund zwischen Azure Active Directory (Azure AD) und Active Directory-Verbunddienste (AD FS) eingerichtet werden kann, müssen die Zertifikate, die AD FS zum Signieren der Sicherheitstoken für Azure AD verwendet, mit den in Azure AD konfigurierten Werten übereinstimmen. Jede fehlende Übereinstimmung kann zu einem Bruch der Vertrauensstellung führen. Azure AD stellt sicher, dass die Informationen bei der Bereitstellung von AD FS und einem Webanwendungsproxy (für Extranetzugriff) synchron gehalten werden.

Dieser Artikel enthält weitere Informationen zum Verwalten der Tokensignaturzertifikate und Synchronisieren der Zertifikate mit Azure AD in den folgenden Fällen:

* Sie stellen den Webanwendungsproxy nicht bereit, und daher sind die Verbundmetadaten nicht im Extranet verfügbar.
* Sie verwenden nicht die Standardkonfiguration von AD FS für Tokensignaturzertifikate.
* Sie verwenden einen externen Identitätsanbieter.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Standardkonfiguration von AD FS für Tokensignaturzertifikate
Bei Tokensignatur- und Tokenentschlüsselungszertifikaten handelt es sich in der Regel um selbstsignierte Zertifikate, die ein Jahr lang gültig sind. AD FS enthält standardmäßig einen automatischen Erneuerungsprozess mit der Bezeichnung **AutoCertificateRollover**. Wenn Sie AD FS 2.0 oder höher verwenden, aktualisieren Office 365 und Azure AD das Zertifikat automatisch, bevor es abläuft.

### <a name="renewal-notification-from-the-microsoft-365-admin-center-or-an-email"></a>Verlängerungsbenachrichtigung aus dem Microsoft 365 Admin-Center oder per E-Mail
> [!NOTE]
> Wenn Sie eine E-Mail oder Portalbenachrichtigung mit der Aufforderung erhalten haben, Ihr Zertifikat für Office zu erneuern, können Sie unter [Verwalten von Änderungen an Tokensignaturzertifikaten](#managecerts) überprüfen, ob Sie Maßnahmen ergreifen müssen. Microsoft ist sich des möglichen Problems bewusst, dass Benachrichtigungen zur Zertifikaterneuerung auch dann gesendet werden, wenn keine Maßnahmen erforderlich sind.
>
>

Azure AD versucht, die Verbundmetadaten zu überwachen und die Tokensignaturzertifikate wie von den Metadaten angegeben zu aktualisieren. 30 Tage vor Ablauf der Tokensignaturzertifikate überprüft Azure AD durch Abfragen der Verbundmetadaten, ob neue Zertifikate verfügbar sind.

* Wenn die Verbundmetadaten und die neuen Zertifikate abgerufen werden können, erhält der Benutzer keine E-Mail-Benachrichtigung oder Warnung im Microsoft 365 Admin Center.
* Falls die neuen Tokensignaturzertifikate nicht abgerufen werden können, weil die Verbundmetadaten nicht erreichbar sind oder das automatische Zertifikatrollover nicht aktiviert ist, gibt Azure AD eine E-Mail-Benachrichtigung und eine Warnung im Microsoft 365 Admin Center aus.

![Office 365-Portalbenachrichtigung](./media/how-to-connect-fed-o365-certs/notification.png)

> [!IMPORTANT]
> Wenn Sie AD FS verwenden, überprüfen Sie zur Gewährleistung der Geschäftskontinuität, ob auf Ihren Servern die folgenden Updates installiert sind, damit keine Authentifizierungsfehler aufgrund von bereits bekannten Problemen auftreten. Dadurch werden bekannte AD FS-Proxyserverprobleme bei dieser Erneuerung und künftigen Erneuerungsperioden vermieden:
>
> Server 2012 R2 – [Windows Server May 2014 rollup](https://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 und 2012 – [Authentication through proxy fails in Windows Server 2012 or Windows 2008 R2 SP1](https://support.microsoft.com/kb/3094446)
>
>

## Überprüfen, ob die Zertifikate aktualisiert werden müssen <a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Schritt 1: Überprüfen des AutoCertificateRollover-Status
Öffnen Sie auf dem AD FS-Server die PowerShell. Stellen Sie sicher, dass der AutoCertificateRollover-Wert auf „True“ festgelegt ist.

    Get-Adfsproperties

![AutoCertificateRollover](./media/how-to-connect-fed-o365-certs/autocertrollover.png)

>[!NOTE] 
>Führen Sie bei Verwendung von AD FS 2.0 zuerst „Add-Pssnapin Microsoft.Adfs.Powershell“ aus.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Schritt 2: Überprüfen, ob AD FS und Azure AD synchronisiert sind
Öffnen Sie auf dem AD FS-Server die MSOnline PowerShell-Eingabeaufforderung, und stellen Sie eine Verbindung mit Azure AD her.

> [!NOTE]
> MSOL-Cmdlets sind Teil des MSOnline PowerShell-Moduls.
> Sie können das MSOnline PowerShell-Modul direkt aus dem PowerShell-Katalog herunterladen.
> 
>

    Install-Module MSOnline

Stellen Sie über das MSOnline PowerShell-Modul eine Verbindung mit Azure AD her.

    Import-Module MSOnline
    Connect-MsolService

Überprüfen Sie, ob die in AD FS und Azure AD konfigurierten Zertifikate den Eigenschaften für die angegebene Domäne vertrauen.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/how-to-connect-fed-o365-certs/certsync.png)

Wenn die Fingerabdrücke in beiden Ausgaben übereinstimmen, sind Ihre Zertifikate mit Azure AD synchronisiert.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Schritt 3: Überprüfen, ob das Zertifikat bald abläuft
Überprüfen Sie in der Ausgabe von „Get-MsolFederationProperty“ oder „Get-AdfsCertificate“ das Datum unter „Not after“ (Nicht nach). Falls das Datum weniger als 30 Tage entfernt ist, sollten Sie Maßnahmen ergreifen.

| AutoCertificateRollover | Mit Azure AD synchronisierte Zertifikate | Verbundmetadaten sind öffentlich zugänglich | Gültigkeitsdauer | Aktion |
|:---:|:---:|:---:|:---:|:---:|
| Ja |Ja |Ja |- |Keine Aktion erforderlich. Siehe [Automatisches Erneuern von Tokensignaturzertifikaten](#autorenew). |
| Ja |Nein |- |Weniger als 15 Tage |Sofort erneuern. Siehe [Manuelles Erneuern von Tokensignaturzertifikaten](#manualrenew). |
| Nein |- |- |Weniger als 30 Tage |Sofort erneuern. Siehe [Manuelles Erneuern von Tokensignaturzertifikaten](#manualrenew). |

\[-] Unwichtig

## Automatisches Erneuern von Tokensignaturzertifikaten (empfohlen) <a name="autorenew"></a>
Sie müssen keine manuellen Schritte ausführen, wenn die beiden folgenden Bedingungen erfüllt sind:

* Sie haben einen Webanwendungsproxy bereitgestellt, mit dem der Zugriff auf die Verbundmetadaten aus dem Extranet ermöglicht werden kann.
* Sie verwenden die AD FS-Standardkonfiguration (AutoCertificateRollover ist aktiviert).

Überprüfen Sie Folgendes, um sicherzustellen, dass das Zertifikat automatisch aktualisiert werden kann.

**1. Die AD FS-Eigenschaft „AutoCertificateRollover“ muss auf „True“ festgelegt sein.** Damit wird angegeben, dass AD FS automatisch neue Tokensignatur- und Tokenentschlüsselungszertifikate generiert, bevor die alten ablaufen.

**2. Die AD FS-Verbundmetadaten sind öffentlich zugänglich.** Überprüfen Sie, ob die Verbundmetadaten öffentlich zugänglich sind, indem Sie von einem Computer im öffentlichen Internet (außerhalb des Unternehmensnetzwerks) zur folgenden URL navigieren:

https://(Ihr_FS_Name)/federationmetadata/2007-06/federationmetadata.xml

Dabei wird `(your_FS_name)` durch den Verbunddiensthostnamen ersetzt, den Ihre Organisation verwendet, z. B. „fs.contoso.com“.  Wenn Sie diese beiden Einstellungen erfolgreich überprüfen können, müssen Sie nichts weiter tun.  

Beispiel: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml
## Manuelles Erneuern des Tokensignaturzertifikats <a name="manualrenew"></a>
Sie können die Tokensignaturzertifikate auch manuell signieren. Beispielsweise funktionieren die folgenden Szenarien unter Umständen besser für die manuelle Erneuerung:

* Bei den Tokensignaturzertifikaten handelt es sich nicht um selbstsignierte Zertifikate. Die häufigste Ursache hierfür ist, dass Ihre Organisation AD FS-Zertifikate verwaltet, die von einer Organisationszertifizierungsstelle registriert werden.
* Die Netzwerksicherheit lässt nicht zu, dass die Verbundmetadaten öffentlich zugänglich sind.

In diesen Szenarien müssen Sie bei jeder Aktualisierung der Tokensignaturzertifikate auch die Office 365-Domäne mithilfe des PowerShell-Befehls „Update-MsolFederatedDomain“ aktualisieren.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Schritt 1: Sicherstellen, dass AD FS über neue Tokensignaturzertifikate verfügt
**Nicht standardmäßige Konfiguration**

Wenn Sie eine nicht dem Standard entsprechende Konfiguration von AD FS nutzen, bei der **AutoCertificateRollover** auf **False** festgelegt ist, verwenden Sie wahrscheinlich benutzerdefinierte Zertifikate (nicht selbstsigniert). Weitere Informationen zur Erneuerung der AD FS-Tokensignaturzertifikate finden Sie unter [Anleitung für Kunden, die keine selbstsignierten AD FS-Zertifikate verwenden](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Die Verbundmetadaten sind nicht öffentlich verfügbar.**

Wenn **AutoCertificateRollover** auf **True** festgelegt ist, aber Ihre Verbundmetadaten nicht öffentlich zugänglich sind, sollten Sie sich zunächst vergewissern, ob von AD FS neue Tokensignaturzertifikate generiert wurden. Überprüfen Sie, ob Sie über neue Tokensignaturzertifikate verfügen, indem Sie die folgenden Schritte ausführen:

1. Stellen Sie sicher, dass Sie am primären AD FS-Server angemeldet sind.
2. Überprüfen Sie die aktuellen Signaturzertifikate in AD FS, indem Sie ein PowerShell-Befehlsfenster öffnen und den folgenden Befehl ausführen:

    PS C:\>Get-ADFSCertificate –CertificateType token-signing

   > [!NOTE]
   > Bei Verwendung von AD FS 2.0 müssen Sie zuerst „Add-Pssnapin Microsoft.Adfs.Powershell“ ausführen.
   >
   >
3. Sehen Sie sich in der Ausgabe des Befehls die aufgeführten Zertifikate an. Wenn AD FS ein neues Zertifikat generiert hat, sollten Sie zwei Zertifikate in der Ausgabe sehen: Bei einem ist der **IsPrimary**-Wert **True** und das **NotAfter**-Datum liegt innerhalb von 5 Tagen, und beim anderen ist der **IsPrimary**-Wert **False** und das **NotAfter**-Datum liegt etwa ein Jahr in der Zukunft.
4. Wenn nur ein Zertifikat angezeigt wird und das **NotAfter** -Datum innerhalb von 5 Tagen liegt, müssen Sie ein neues Zertifikat generieren.
5. Um ein neues Zertifikat zu generieren, führen Sie an einer PowerShell-Eingabeaufforderung den folgenden Befehl aus: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Überprüfen Sie das Update, indem Sie den folgenden Befehl erneut ausführen: PS C:\>Get-ADFSCertificate –CertificateType token-signing

Nun sollten zwei Zertifikate aufgeführt werden, bei denen das **NotAfter**-Datum in etwa ein Jahr in der Zukunft liegt und bei dem der **IsPrimary**-Wert **False** lautet.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Schritt 2: Aktualisieren der neuen Tokensignaturzertifikate für die Office 365-Vertrauensstellung
Aktualisieren Sie Office 365 wie folgt mit den neuen Tokensignaturzertifikaten für die Vertrauensstellung:

1. Öffnen Sie das Microsoft Azure Active Directory-Modul für Windows PowerShell.
2. Führen Sie $cred=Get-Credential aus. Wenn Sie dieses Cmdlet zur Eingabe von Anmeldeinformationen auffordert, geben Sie die Anmeldeinformationen Ihres Clouddienstadministrators ein.
3. Führen Sie Connect-MsolService –Credential $cred aus. Dieses Cmdlet verbindet Sie mit dem Clouddienst. Sie müssen einen Kontext erstellen, der Sie mit dem Clouddienst verbindet, bevor Sie andere Cmdlets ausführen, die vom Tool installiert werden.
4. Wenn Sie diese Befehle auf einem Computer ausführen, der nicht der primäre AD FS-Verbundserver ist, führen Sie „Set-MSOLAdfscontext -Computer &lt;Primärer AD FS-Server&gt;“ aus, wobei &lt;Primärer AD FS-Server&gt; für den internen FQDN des primären AD FS-Servers steht. Dieses Cmdlet erstellt einen Kontext, der Sie mit AD FS verbindet.
5. Führen Sie „Update-MSOLFederatedDomain –DomainName &lt;Domäne&gt;“ aus. Dieses Cmdlet aktualisiert die Einstellungen von AD FS im Clouddienst und konfiguriert die Vertrauensstellung zwischen den beiden Komponenten.

> [!NOTE]
> Wenn Sie mehrere Domänen der obersten Ebene unterstützen müssen, z.B. „contoso.com“ und „fabrikam.com“, müssen Sie den **SupportMultipleDomain**-Switch mit den Cmdlets verwenden. Weitere Informationen finden Sie unter [Unterstützung mehrerer Domänen der obersten Ebene](how-to-connect-install-multiple-domains.md).
>


## Reparieren einer Azure AD-Vertrauensstellung mit Azure AD Connect <a name="connectrenew"></a>
Wenn Sie Ihre AD FS-Farm und die Azure AD-Vertrauensstellung unter Verwendung von Azure AD Connect konfiguriert haben, können Sie mit Azure AD Connect erkennen, ob Sie für Ihre Tokensignaturzertifikate Maßnahmen ergreifen müssen. Falls Sie die Zertifikate erneuern müssen, können Sie dafür Azure AD Connect nutzen.

Weitere Informationen finden Sie unter [Reparieren der Vertrauensstellung](how-to-connect-fed-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Schritte zum Aktualisieren von AD FS- und Azure AD-Zertifikaten
Tokensignaturzertifikate sind X509-Standardzertifikate, mit denen alle Token sicher signiert werden, die vom Verbundserver ausstellt werden. Tokenentschlüsselungszertifikate sind X509-Standardzertifikate, mit denen alle eingehenden Token entschlüsselt werden. 

Standardmäßig ist AD FS so konfiguriert, dass die Zertifikate für die Tokensignatur und Tokenentschlüsselung automatisch generiert werden, sowohl bei der Erstkonfiguration als auch zu dem Zeitpunkt, wenn sich die Zertifikate dem Ablaufdatum nähern.

Azure AD versucht 30 Tage vor Ablauf des aktuellen Zertifikats, ein neues Zertifikat von Ihren Verbunddienstmetadaten abzurufen. Falls zu diesem Zeitpunkt kein neues Zertifikat verfügbar ist, setzt Azure AD die Überwachung der Metadaten in regelmäßigen täglichen Intervallen fort. Sobald das neue Zertifikat in den Metadaten verfügbar ist, werden die Verbundeinstellungen für die Domäne mit den neuen Zertifikatsinformationen aktualisiert. Sie können mit `Get-MsolDomainFederationSettings` überprüfen, ob das neue Zertifikat in NextSigningCertificate/SigningCertificate angezeigt wird.

Weitere Informationen zu Tokensignaturzertifikaten in AD FS finden Sie unter [Abrufen und Konfigurieren von Tokensignaturzertifikaten und Tokenentschlüsselungszertifikaten für AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)
