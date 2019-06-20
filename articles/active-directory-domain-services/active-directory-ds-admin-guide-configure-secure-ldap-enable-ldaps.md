---
title: Aktivieren von sicherem LDAP (LDAPS) in Azure AD Domain Services | Microsoft-Dokumentation
description: Aktivieren von sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne
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
ms.date: 08/01/2018
ms.author: ergreenl
ms.openlocfilehash: df189e405dcd5277c1ccbd94e9d5d302660be79b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359828"
---
# <a name="enable-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Aktivieren von sicherem LDAP (LDAPS) für eine über Azure AD Domain Services verwaltete Domäne

## <a name="before-you-begin"></a>Voraussetzungen
Führen Sie [Aufgabe 2: Exportieren der Zertifikate für sicheres LDAP in eine PFX-Datei](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md) aus.


## <a name="task-3-enable-secure-ldap-for-the-managed-domain-using-the-azure-portal"></a>Aufgabe 3: Aktivieren von sicherem LDAP für die verwaltete Domäne über das Azure-Portal
Führen Sie die folgenden Konfigurationsschritte aus, um sicheres LDAP zu aktivieren:

1. Navigieren Sie zum **[Azure-Portal](https://portal.azure.com)** .

2. Suchen Sie nach „Domänendienste“ im Suchfeld **Search resources** (Ressourcen suchen). Wählen Sie aus dem Suchergebnis **Azure AD Domain Services** aus. Die Seite **Azure AD Domain Services** listet Ihre verwaltete Domäne auf.

    ![Suchen der bereitgestellten verwalteten Domäne](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Klicken Sie auf den Namen der verwalteten Domäne (z.B. „contoso100.com“), um weitere Details zu der Domäne zu erhalten.

    ![Domänendienste – Bereitstellungsstatus](./media/getting-started/domain-services-provisioning-state.png)

3. Klicken Sie im Navigationsbereich auf **Secure LDAP**.

    ![Domänendienste – „Secure LDAP“-Seite](./media/active-directory-domain-services-admin-guide/secure-ldap-blade.png)

4. Standardmäßig ist der sichere LDAP-Zugriff auf Ihre verwaltete Domäne deaktiviert. Ändern Sie die Einstellung für **Secure LDAP** in **Aktivieren**.

    ![Aktivieren von sicherem LDAP](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configure.png)
5. Standardmäßig ist der sichere LDAP-Zugriff auf Ihre verwaltete Domäne über das Internet deaktiviert. Ändern Sie ggf. die Einstellung für **Sicheren LDAP-Zugriff über das Internet aktivieren** in **Aktivieren**.

    > [!WARNING]
    > Wenn Sie sicheren LDAP-Zugriff über das Internet aktivieren, ist Ihre Domäne anfällig für über das Internet ausgeführte Brute-Force-Angriffe mit Kennwörtern. Daher sollten Sie eine NSG einrichten, um den Zugriff auf erforderliche Quell-IP-Adressbereiche zu sperren. Beachten Sie die Anweisungen unter [Aufgabe 5: LDAPS-Zugriff auf Ihre verwaltete Domäne über das Internet sperren](active-directory-ds-ldaps-bind-lockdown.md#task-6-lock-down-secure-ldap-access-to-your-managed-domain-over-the-internet).
    >

6. Klicken Sie auf das Ordnersymbol hinter der **PFX-Datei mit sicherem LDAP-Zertifikat**. Geben Sie den Pfad zu der PFX-Datei mit dem Zertifikat für den sicheren LDAP-Zugriff auf die verwaltete Domäne an.

7. Geben Sie das **Kennwort zum Entschlüsseln der PFX-Datei** an. Geben Sie das gleiche Kennwort an, das Sie beim Exportieren des Zertifikats in die PFX-Datei verwendet haben.

8. Wenn Sie fertig sind, klicken Sie auf die Schaltfläche **Speichern**.

9. Sie sehen eine Benachrichtigung, die Sie darüber informiert, dass sicheres LDAP für die verwaltete Domäne konfiguriert wird. Bis dieser Vorgang abgeschlossen ist, können Sie andere Einstellungen der Domäne nicht ändern.

    ![Konfigurieren von sicherem LDAP für die verwaltete Domäne](./media/active-directory-domain-services-admin-guide/secure-ldap-blade-configuring.png)

> [!NOTE]
> Es dauert ungefähr 10 bis 15 Minuten, bis sicheres LDAP für die verwaltete Domäne aktiviert ist. Wenn das bereitgestellte Zertifikat für sicheres LDAP nicht den erforderlichen Kriterien entspricht, wird LDAPS nicht für Ihr Verzeichnis aktiviert, und Sie sehen einen Fehler. Mögliche Fehlerursachen wären etwa ein falscher Domänenname oder ein bereits abgelaufenes oder bald ablaufendes Zertifikat. Wiederholen Sie in diesem Fall den Vorgang mit einem gültigen Zertifikat.
>
>

## <a name="next-step"></a>Nächster Schritt
[Aufgabe 4: Konfigurieren von DNS für den Zugriff auf die verwaltete Domäne über das Internet](active-directory-ds-ldaps-configure-dns.md)
