---
title: Azure AD Connect, mehrere Domänen
description: In diesem Dokument wird das Einrichten und Konfigurieren mehrerer Domänen der obersten Ebene mit Office 365 und Azure AD beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 5595fb2f-2131-4304-8a31-c52559128ea4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e822906a072ec8244c7108e98289482adebb5a7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60245123"
---
# <a name="multiple-domain-support-for-federating-with-azure-ad"></a>Unterstützung mehrerer Domänen für den Verbund mit Azure AD
Die folgende Dokumentation enthält eine Anleitung dazu, wie Sie mehrere Domänen der obersten Ebene und Unterdomänen verwenden, wenn Sie einen Verbund mit Office 365- oder Azure AD-Domänen erstellen.

## <a name="multiple-top-level-domain-support"></a>Unterstützung mehrerer Domänen der obersten Ebene
Für die Erstellung mehrerer Domänen der obersten Ebene als Verbund mit Azure AD sind einige zusätzliche Konfigurationsschritte erforderlich, die nicht benötigt werden, wenn ein Verbund mit nur einer Domäne der obersten Ebene erstellt wird.

Bei einem Verbund einer Domäne mit Azure AD werden für die Domäne in Azure mehrere Eigenschaften festgelegt.  Eine wichtige Eigenschaft ist die IssuerUri-Eigenschaft.  Diese Eigenschaft ist ein URI, der von Azure AD zum Identifizieren der Domäne verwendet wird, der das Token zugeordnet ist.  Der URI muss nicht in einen bestimmten Wert aufgelöst werden, aber es muss sich um einen gültigen URI handeln.  Standardmäßig wird er von Azure AD auf den Wert des Bezeichners des Verbunddiensts in Ihrer lokalen AD FS-Konfiguration festgelegt.

> [!NOTE]
> Der Bezeichner des Verbunddiensts ist ein URI, mit dem ein Verbunddienst eindeutig identifiziert wird.  Der Verbunddienst ist eine Instanz von AD FS, die als Sicherheitstokendienst fungiert.
>
>

Sie können das IssuerUri-Element mit dem folgenden PowerShell-Befehl anzeigen: `Get-MsolDomainFederationSettings -DomainName <your domain>`.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Ein Problem tritt auf, wenn Sie mehr als eine Domäne der obersten Ebene hinzufügen.  Nehmen wir beispielsweise an, dass Sie einen Verbund zwischen Azure AD und Ihrer lokalen Umgebung eingerichtet haben.  Für dieses Dokument wird die Domäne „bmcontoso.com“ verwendet.  Nun wurde eine zweite Domäne der obersten Ebene hinzugefügt: „bmfabrikam.com“.

![Domänen](./media/how-to-connect-install-multiple-domains/domains.png)

Wenn Sie versuchen, die Domäne „bmfabrikam.com“ in einen Verbund zu konvertieren, wird ein Fehler angezeigt.  Der Grund hierfür ist, dass für Azure AD eine Einschränkung gilt. Es ist nicht zulässig, dass die IssuerUri-Eigenschaft für mehr als eine Domäne den gleichen Wert aufweist.  

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/error.png)

### <a name="supportmultipledomain-parameter"></a>SupportMultipleDomain-Parameter
Um diese Einschränkung zu umgehen, müssen Sie einen anderen IssuerUri hinzufügen. Hierfür kann der Parameter `-SupportMultipleDomain` verwendet werden.  Dieser Parameter wird mit den folgenden Cmdlets verwendet:

* `New-MsolFederatedDomain`
* `Convert-MsolDomaintoFederated`
* `Update-MsolFederatedDomain`

Mit diesem Parameter wird erreicht, dass Azure AD den IssuerUri so konfiguriert, dass er auf dem Namen der Domäne basiert.  Der IssuerUri ist für Verzeichnisse in Azure AD eindeutig.  Mit dem Parameter kann der PowerShell-Befehl erfolgreich abgeschlossen werden.

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/convert.png)

Wenn Sie sich die Einstellungen der Domäne „bmfabrikam.com“ ansehen, erkennen Sie Folgendes:

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/settings.png)

`-SupportMultipleDomain` führt nicht zu einer Änderung der anderen Endpunkte, die weiterhin so konfiguriert sind, dass sie auf den Verbunddienst unter „adfs.bmcontoso.com“ verweisen.

Außerdem wird mit `-SupportMultipleDomain` sichergestellt, dass das AD FS-System den richtigen Issuer-Wert in Token einfügt, die für Azure AD ausgegeben werden. Dazu wird der Domänenteil des Benutzerprinzipalnamens (UPN) verwendet und als Domäne im IssuerUri festgelegt, d.h. „https://{upn suffix}/adfs/services/trust“.

Während der Authentifizierung in Azure AD oder Office 365 wird daher das IssuerUri-Element im Token des Benutzers verwendet, um die Domäne in Azure AD zu finden.  Wenn keine Übereinstimmung gefunden wird, schlägt die Authentifizierung fehl.

Wenn der UPN eines Benutzers beispielsweise bsimon@bmcontoso.com lautet, wird das IssuerUri-Element in dem von AD FS ausgestellten Token auf <http://bmcontoso.com/adfs/services/trust> festgelegt. Dieses Element entspricht der Azure AD-Konfiguration und die Authentifizierung ist erfolgreich.

Unten sehen Sie die angepasste Anspruchsregel, die diese Logik implementiert:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)", "http://${domain}/adfs/services/trust/"));


> [!IMPORTANT]
> Zum Verwenden des Switch -SupportMultipleDomain bei dem Versuch, neue Domänen hinzuzufügen oder vorhandene Domänen zu konvertieren, müssen Sie die Verbundvertrauensstellung bereits so eingerichtet haben, dass diese standardmäßig unterstützt werden.
>
>

## <a name="how-to-update-the-trust-between-ad-fs-and-azure-ad"></a>Aktualisieren der Vertrauensstellung zwischen AD FS und Azure AD
Wenn Sie die Vertrauensstellung zwischen AD FS und Ihrer Instanz von Azure AD nicht eingerichtet haben, müssen Sie diese Vertrauensstellung unter Umständen neu erstellen.  Dies liegt daran, dass für den IssuerUri der Standardwert festgelegt wird, wenn er anfänglich ohne den Parameter `-SupportMultipleDomain` eingerichtet wird.  Im Screenshot unten ist zu sehen, dass der IssuerUri auf https://adfs.bmcontoso.com/adfs/services/trust festgelegt ist.

Sie erhalten jetzt also den folgenden Fehler, wenn Sie im Azure AD-Portal erfolgreich eine neue Domäne hinzugefügt und anschließend versucht haben, diese mit `Convert-MsolDomaintoFederated -DomainName <your domain>` zu konvertieren:

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/trust1.png)

Wenn Sie versuchen, den Switch `-SupportMultipleDomain` hinzuzufügen, erhalten Sie den folgenden Fehler:

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/trust2.png)

Der einfache Versuch, `Update-MsolFederatedDomain -DomainName <your domain> -SupportMultipleDomain` in der ursprünglichen Domäne auszuführen, führt ebenfalls zu einem Fehler.

![Partnerverbundfehler](./media/how-to-connect-install-multiple-domains/trust3.png)

Verwenden Sie die unten angegebenen Schritte, um eine weitere Domäne der obersten Ebene hinzuzufügen.  Wenn Sie bereits eine Domäne hinzugefügt und den Parameter `-SupportMultipleDomain` nicht verwendet haben, beginnen Sie mit den Schritten für das Entfernen und Aktualisieren Ihrer ursprünglichen Domäne.  Falls Sie noch keine Domäne der obersten Ebene hinzugefügt haben, können Sie mit den Schritten zum Hinzufügen einer Domäne mit PowerShell von Azure AD Connect beginnen.

Führen Sie die folgenden Schritte aus, um die Microsoft Online-Vertrauensstellung zu entfernen und die ursprüngliche Domäne zu aktualisieren.

1. Öffnen Sie auf Ihrem AD FS-Verbundserver die Option für die **AD FS-Verwaltung**
2. Erweitern Sie auf der linken Seite die Optionen **Vertrauensstellungen** und **Vertrauensstellungen der vertrauenden Seite**.
3. Löschen Sie auf der rechten Seite den Eintrag **Microsoft Office 365 Identity Platform** .
   ![Microsoft Online entfernen](./media/how-to-connect-install-multiple-domains/trust4.png)
4. Führen Sie auf einem Computer, auf dem das [Azure Active Directory-Modul für Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installiert ist, Folgendes aus: `$cred=Get-Credential`.  
5. Geben Sie den Benutzernamen und das Kennwort eines globalen Administrators für die Azure AD-Domäne ein, mit der Sie den Verbund erstellen.
6. Geben Sie in PowerShell `Connect-MsolService -Credential $cred` ein.
7. Geben Sie in PowerShell `Update-MSOLFederatedDomain -DomainName <Federated Domain Name> -SupportMultipleDomain` ein.  Dies ist das Update für die ursprüngliche Domäne.  Mit den obigen Domänen ergibt sich Folgendes: `Update-MsolFederatedDomain -DomainName bmcontoso.com -SupportMultipleDomain`

Führen Sie die folgenden Schritte aus, um die neue Domäne der obersten Ebene mit PowerShell hinzuzufügen.

1. Führen Sie auf einem Computer, auf dem das [Azure Active Directory-Modul für Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx) installiert ist, Folgendes aus: `$cred=Get-Credential`.  
2. Geben Sie den Benutzernamen und das Kennwort eines globalen Administrators für die Azure AD-Domäne ein, mit der Sie den Verbund erstellen.
3. Geben Sie in PowerShell `Connect-MsolService -Credential $cred` ein.
4. Geben Sie in PowerShell `New-MsolFederatedDomain –SupportMultipleDomain –DomainName` ein.

Führen Sie die folgenden Schritte aus, um die neue Domäne der obersten Ebene mit Azure AD Connect hinzuzufügen.

1. Starten Sie Azure AD Connect über den Desktop oder das Menü „Start“
2. Wählen Sie „Weitere Azure AD-Domäne hinzufügen“ ![Weitere Azure AD-Domäne hinzufügen](./media/how-to-connect-install-multiple-domains/add1.png)
3. Geben Sie Ihre Anmeldeinformationen für Azure AD und Active Directory ein.
4. Wählen Sie die zweite Domäne aus, die Sie für den Verbund konfigurieren möchten.
   ![Weitere Azure AD-Domäne hinzufügen](./media/how-to-connect-install-multiple-domains/add2.png)
5. Klicken Sie auf „Installieren“.

### <a name="verify-the-new-top-level-domain"></a>Überprüfen der neuen Domäne der obersten Ebene
Mit dem PowerShell-Befehl `Get-MsolDomainFederationSettings -DomainName <your domain>`können Sie den aktualisierten IssuerUri anzeigen.  Im folgenden Screenshot ist dargestellt, dass die Verbundeinstellungen für die ursprüngliche Domäne http://bmcontoso.com/adfs/services/trust aktualisiert wurden.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/MsolDomainFederationSettings.png)

Außerdem wurde der IssuerUri für die neue Domäne auf https://bmfabrikam.com/adfs/services/trust festgelegt.

![Get-MsolDomainFederationSettings](./media/how-to-connect-install-multiple-domains/settings2.png)

## <a name="support-for-subdomains"></a>Unterstützung von Unterdomänen
Wenn Sie eine Unterdomäne hinzufügen, erbt sie die Einstellungen der übergeordneten Domäne. Dies liegt an der Art und Weise, wie Azure AD Domänen behandelt.  Dies bedeutet, dass der IssuerUri mit den übergeordneten Elementen übereinstimmen muss.

Angenommen, Sie verfügen über „bmcontoso.com“ und fügen dann „corp.bmcontoso.com“ hinzu.  Dies bedeutet, dass der IssuerUri für einen Benutzer von „corp.bmcontoso.com“ wie folgt lauten muss: **http://bmcontoso.com/adfs/services/trust.**  Mit der oben für Azure AD implementierten Standardregel wird aber ein Token mit folgendem Aussteller generiert: **http://corp.bmcontoso.com/adfs/services/trust.** Dies stimmt nicht mit dem erforderlichen Wert der Domäne überein, und bei der Authentifizierung tritt ein Fehler auf.

### <a name="how-to-enable-support-for-subdomains"></a>Aktivieren der Unterstützung von Unterdomänen
Um dieses Verhalten zu umgehen, muss die AD FS-Vertrauensstellung der vertrauenden Seite für Microsoft Online aktualisiert werden.  Hierzu müssen Sie eine benutzerdefinierte Anspruchsregel so konfigurieren, dass beim Erstellen des benutzerdefinierten Issuer-Werts alle Unterdomänen aus dem UPN-Suffix des Benutzers entfernt werden.

Dies ist mit dem folgenden Anspruch möglich:

    c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

[!NOTE]
Die letzte Zahl im regulären Ausdruck legt fest, wie viele übergeordnete Domänen in der Stammdomäne vorhanden sind. Im Fall von „bmcontoso.com“ sind also zwei übergeordnete Domänen erforderlich. Wenn drei übergeordnete Domänen erforderlich sind (d.h. corp.bmcontoso.com), muss die Anzahl drei lauten. Es kann auch ein Bereichs angegeben werden. Dabei wird immer eine Übereinstimmung mit der maximalen Anzahl von Domänen hergestellt. „{2,3}“ entspricht zwei bis drei Domänen (d.h. bmfabrikam.com und corp.bmcontoso.com).

Führen Sie die folgenden Schritte aus, um einen benutzerdefinierten Anspruch zur Unterstützung von Unterdomänen hinzuzufügen.

1. Öffnen Sie die AD FS-Verwaltung.
2. Klicken Sie mit der rechten Maustaste auf die Microsoft Online-Vertrauensstellung der vertrauenden Seite, und wählen Sie „Anspruchsregeln bearbeiten“ aus.
3. Wählen Sie die dritte Anspruchsregel aus, und ersetzen Sie ![Anspruch bearbeiten](./media/how-to-connect-install-multiple-domains/sub1.png).
4. Ersetzen Sie den aktuellen Anspruch:

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)","http://${domain}/adfs/services/trust/"));

       with

        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"] => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, "^.*@([^.]+\.)*?(?<domain>([^.]+\.?){2})$", "http://${domain}/adfs/services/trust/"));

    ![Anspruch ersetzen](./media/how-to-connect-install-multiple-domains/sub2.png)

5. Klicken Sie auf "OK".  Klicken Sie auf „Übernehmen“.  Klicken Sie auf "OK".  Schließen Sie die AD FS-Verwaltung.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Azure AD Connect installiert haben, können Sie [die Installation überprüfen und Lizenzen zuweisen](how-to-connect-post-installation.md).

Hier finden Sie weitere Informationen zu diesen Features, die bei der Installation aktiviert wurden: [Automatisches Upgrade](how-to-connect-install-automatic-upgrade.md), [Azure AD Connect-Synchronisierung: Verhindern von versehentlichen Löschvorgängen](how-to-connect-sync-feature-prevent-accidental-deletes.md) und [Überwachen der Azure AD Connect-Synchronisierung mit Azure AD Connect Health](how-to-connect-health-sync.md).

Weitere Informationen zu folgenden allgemeinen Themen: [Scheduler und Auslösen der Synchronisierung](how-to-connect-sync-feature-scheduler.md).

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
