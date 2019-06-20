---
title: Konfigurieren von sicherem LDAP (LDAPS) in Azure AD Domain Services | Microsoft Docs
description: Konfigurieren von sicherem LDAP (LDAPS) für eine durch Azure AD Domain Services verwaltete Domäne
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: e961b6c4b97103668c591e319a81d20f533b4acb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245361"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Konfigurieren von sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne
Dieser Artikel zeigt, wie Sie sicheres LDAP (LDAPS, Secure Lightweight Directory Access Protocol) für eine durch Azure AD Domain Services verwaltete Domäne aktivieren können. Sicheres LDAP ist auch bekannt als „Lightweight Directory Access Protocol (LDAP) über Secure Sockets Layer (SSL)/Transport Layer Security (TLS)“.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Voraussetzungen
Um die in diesem Artikel beschriebenen Aufgaben auszuführen, benötigen Sie Folgendes:

1. Ein gültiges **Azure-Abonnement**.
2. Ein **Azure AD-Verzeichnis** – entweder synchronisiert mit einem lokalen Verzeichnis oder als reines Cloud-Verzeichnis
3. **Azure AD Domain Services** müssen für das Azure AD-Verzeichnis aktiviert sein. Wenn dies noch nicht der Fall ist, führen Sie alle Aufgaben im Leitfaden [Erste Schritte](create-instance.md)aus.
4. Ein **Zertifikat, das zum Aktivieren von sicherem LDAP verwendet werden kann**

   * **Empfehlung:** Beziehen Sie ein Zertifikat von einer vertrauenswürdigen öffentlichen Zertifizierungsstelle. Diese Konfigurationsoption bietet mehr Sicherheit.
   * Alternativ kann können Sie [ein selbstsigniertes Zertifikat erstellen](#task-1---obtain-a-certificate-for-secure-ldap). Das Verfahren dafür wird weiter unten in diesem Artikel beschrieben.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Anforderungen an ein Zertifikat für sicheres LDAP
Erwerben Sie ein gültiges Zertifikat, das den folgenden Richtlinien entspricht, bevor Sie sicheres LDAP aktivieren. Es kommt zu Fehlern, wenn Sie beim Aktivieren von sicherem LDAP für Ihre verwaltete Domäne ein ungültiges oder falsches Zertifikat verwenden.

1. **Vertrauenswürdiger Aussteller**: Das Zertifikat muss von einer Zertifizierungsstelle ausgestellt sein, der die Computer vertrauen, die über sicheres LDAP eine Verbindung mit der Domäne herstellen. Hierbei kann es sich um eine öffentliche Zertifizierungsstelle oder um eine Unternehmenszertifizierungsstelle handeln, die von diesen Computern als vertrauenswürdig eingestuft wird.
2. **Lebensdauer** : Das Zertifikat muss mindestens für die nächsten 3 bis 6 Monate gültig sein. Der Zugriff auf Ihre verwaltete Domäne über sicheres LDAP wird unterbrochen, wenn das Zertifikat abläuft.
3. **Antragstellername**: Der Name des Antragstellers im Zertifikat muss Ihre verwaltete Domäne sein. Wenn Ihre Domäne z.B. „contoso100.com“ heißt, muss als Antragstellername im Zertifikat „contoso100.com“ angegeben sein. Legen Sie als DNS-Namen (bzw. alternativen Antragstellernamen) einen Platzhalternamen für Ihre verwaltete Domäne fest.
4. **Schlüsselverwendung** : Das Zertifikat muss für die folgenden Verwendungszwecke konfiguriert sein: digitale Signaturen und Schlüsselverschlüsselung.
5. **Zertifikatzweck** : Das Zertifikat muss für die SSL-Serverauthentifizierung gültig sein.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Aufgabe 1: Erwerben eines Zertifikats für sicheres LDAP
Zuerst müssen Sie ein Zertifikat erwerben, das Sie zum Zugriff auf die verwaltete Domäne über sicheres LDAP verwenden. Sie haben zwei Möglichkeiten:

* Abrufen eines Zertifikats von einer öffentlichen Zertifizierungsstelle oder einer Unternehmenszertifizierungsstelle.
* Erstellen eines selbstsignierten Zertifikats

> [!NOTE]
> Die Clientcomputer, die über sicheres LDAP eine Verbindung mit der verwalteten Domäne herstellen sollen, müssen dem Aussteller des Zertifikats für sicheres LDAP vertrauen.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Option A (empfohlen): Erwerben eines Zertifikats für sicheres LDAP von einer Zertifizierungsstelle
Wenn Ihre Organisation ihre Zertifikate von einer öffentlichen Zertifizierungsstelle erhält, fordern Sie das Zertifikat für sicheres LDAP bei dieser öffentlichen Zertifizierungsstelle an. Wenn Sie eine Unternehmenszertifizierungsstelle bereitstellen, fordern Sie das Zertifikat für sicheres LDAP bei der Unternehmenszertifizierungsstelle an.

> [!TIP]
> **Verwenden Sie selbstsignierte Zertifikate für verwaltete Domänen mit den Domänensuffixen „.onmicrosoft.com“.**
> Wenn der DNS-Domänenname Ihrer verwalteten Domäne auf „.onmicrosoft.com“ endet, können Sie kein sicheres LDAP-Zertifikat von einer öffentlichen Zertifizierungsstelle anfordern. Da Microsoft die Domäne „onmicrosoft.com“ besitzt, verweigern öffentliche Zertifizierungsstellen das Ausstellen eines sicheren LDAP-Zertifikats für eine Domäne mit diesem Suffix. Erstellen Sie in diesem Szenario ein selbstsigniertes Zertifikat, und verwenden Sie es, um sicheres LDAP zu konfigurieren.
>

Stellen Sie sicher, dass das Zertifikat, das Sie von der öffentlichen Zertifizierungsstelle erhalten, alle Anforderungen erfüllt, die in [Anforderungen an ein Zertifikat für sicheres LDAP](#requirements-for-the-secure-ldap-certificate) beschrieben sind.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Option B: Erstellen eines selbstsignierten Zertifikats für sicheres LDAP
Wenn Sie voraussichtlich kein Zertifikat einer öffentlichen Zertifizierungsstelle verwenden, können Sie ein selbstsigniertes Zertifikat für sicheres LDAP erstellen. Wählen Sie diese Option aus, wenn der DNS-Domänenname Ihrer verwalteten Domäne auf „.onmicrosoft.com“ endet.

**Erstellen eines selbstsignierten Zertifikats mit PowerShell**

Öffnen Sie auf Ihrem Windows-Computer als **Administrator** ein neues PowerShell-Fenster, und geben Sie die folgenden Befehle ein, um ein neues selbstsigniertes Zertifikat zu erstellen.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

Ersetzen Sie im vorherigen Beispiel „contoso100.com“ durch den DNS-Domänennamen Ihrer verwalteten Domäne. Wenn Sie zum Beispiel eine verwaltete Domäne mit dem Namen „contoso100.onmicrosoft.com“ erstellt haben, ersetzen Sie im Subject-Attribut „contoso100.com“ durch „contoso100.onmicrosoft.com“ und im DnsName-Attribut „ *.contoso100.com“ durch „* .contoso100.onmicrosoft.com“.

![Auswählen des Azure AD-Verzeichnisses](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

Die neu erstellten selbstsignierten Zertifikate werden im Zertifikatspeicher des lokalen Computers platziert.


## <a name="next-step"></a>Nächster Schritt
[Task 2 - export the secure LDAP certificate to a .PFX file (Aufgabe 2: Exportieren des Zertifikats für sicheres LDAP in eine PFX-Datei)](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
