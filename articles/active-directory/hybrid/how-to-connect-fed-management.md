---
title: Azure AD Connect – Verwalten und Anpassen von AD FS | Microsoft-Dokumentation
description: Enthält eine Beschreibung der AD FS-Verwaltung mithilfe von Azure AD Connect und Anpassung der AD FS-Anmeldung für Benutzer mit Azure AD Connect und PowerShell.
keywords: AD FS, ADFS, AD FS Verwaltung, AAD Connect, Connect, anmelden, AD FS Anpassung, Vertrauensstellung reparieren, O365, Verbund, vertrauende Seite
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021e13dafcc659337d4096a068e224312e69db1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60353630"
---
# <a name="manage-and-customize-active-directory-federation-services-by-using-azure-ad-connect"></a>Verwaltung und Anpassung der Active Directory-Verbunddienste mit Azure AD Connect
In diesem Artikel wird beschrieben, wie Active Directory-Verbunddienste (AD FS) mit Azure Active Directory (Azure AD) Connect verwaltet und angepasst werden. Darüber hinaus werden andere gängige AD FS-Aufgaben behandelt, die Sie möglicherweise zur vollständigen Konfiguration einer AD FS-Farm benötigen.

| Thema | Inhalt |
|:--- |:--- |
| **Verwalten von AD FS** | |
| [Reparieren der Vertrauensstellung](#repairthetrust) |Beschreibung des Vorgangs zum Reparieren der Verbundvertrauensstellung mit Office 365 |
| [Erstellen eines Verbunds mit Azure AD mithilfe einer alternativen Anmelde-ID](#alternateid) | Konfigurieren eines Verbunds mithilfe einer alternativen Anmelde-ID  |
| [Hinzufügen eines AD FS-Servers](#addadfsserver) |Beschreibung des Vorgangs zum Erweitern einer AD FS-Farm mit einem zusätzlichen AD FS-Server |
| [Hinzufügen eines AD FS-Webanwendungsproxy-Servers](#addwapserver) |Beschreibung des Vorgangs zum Erweitern einer AD FS-Farm mit einem zusätzlichen WAP-Server (Web Application Proxy) |
| [Hinzufügen einer Verbunddomäne](#addfeddomain) |Beschreibung des Vorgangs zum Hinzufügen einer Verbunddomäne |
| [Aktualisieren des SSL-Zertifikats](how-to-connect-fed-ssl-update.md)| Beschreibung des Vorgangs zum Aktualisieren des SSL-Zertifikats für eine AD FS-Farm |
| **Anpassen von AD FS** | |
| [Hinzufügen eines benutzerdefinierten Firmenlogos oder einer Abbildung](#customlogo) |Beschreibung des Vorgangs zum Anpassen einer AD FS-Anmeldeseite mit einem Firmenlogo und einer Abbildung |
| [Hinzufügen einer Anmeldebeschreibung](#addsignindescription) |Beschreibung des Vorgangs zum Hinzufügen einer Beschreibung für die Anmeldeseite |
| [Ändern von AD FS-Anspruchsregeln](#modclaims) |Beschreibung des Vorgangs zum Ändern der AD FS-Ansprüche für verschiedene Verbundszenarien |

## <a name="manage-ad-fs"></a>Verwalten von AD FS
Sie können verschiedene AD FS-bezogene Aufgaben in Azure AD Connect durchführen, die mithilfe des Azure AD Connect-Assistenten mit minimalem Benutzereingriff ausgeführt werden können. Nachdem Sie die Installation von Azure AD Connect durch Ausführen des Assistenten abgeschlossen haben, können Sie den Assistenten erneut ausführen, um zusätzliche Aufgaben durchzuführen.

## <a name="repairthetrust"></a>Reparieren der Vertrauensstellung 
Mithilfe von Azure AD Connect können Sie den aktuellen Status der AD FS- und Azure AD-Vertrauensstellung überprüfen und entsprechende Maßnahmen ergreifen, um die Vertrauensstellung zu reparieren. Führen Sie diese Schritte aus, um Ihre Azure AD- und AD FS-Vertrauensstellung zu reparieren.

1. Wählen Sie in der Liste mit den weiteren Aufgaben die Option **AAD- und ADFS-Vertrauensstellung reparieren** .
   ![AAD- und ADFS-Vertrauensstellung reparieren](./media/how-to-connect-fed-management/RepairADTrust1.PNG)

2. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen des globalen Administrators für Azure AD an, und klicken Sie auf **Weiter**.
   ![Mit Azure AD verbinden](./media/how-to-connect-fed-management/RepairADTrust2.PNG)

3. Geben Sie auf der Seite **Anmeldeinformationen für den Remotezugriff** die Anmeldeinformationen für den Domänenadministrator ein.

   ![Anmeldeinformationen für den Remotezugriff](./media/how-to-connect-fed-management/RepairADTrust3.PNG)

    Nachdem Sie auf **Weiter** geklickt haben, überprüft Azure AD Connect die Zertifikatsintegrität und zeigt etwaige Probleme an.

    ![Status von Zertifikaten](./media/how-to-connect-fed-management/RepairADTrust4.PNG)

    Auf der Seite **Bereit zur Konfiguration** wird die Liste der Aktionen angezeigt, die ausgeführt werden, um die Vertrauensstellung zu reparieren.

    ![Bereit zur Konfiguration](./media/how-to-connect-fed-management/RepairADTrust5.PNG)

4. Klicken Sie auf **Installieren** , um die Vertrauensstellung zu reparieren.

> [!NOTE]
> Azure AD Connect kann nur selbstsignierte Zertifikate reparieren bzw. Maßnahmen dafür ergreifen. Azure AD Connect kann nicht zum Reparieren von Drittanbieterzertifikaten verwendet werden.

## <a name="alternateid"></a>Erstellen eines Verbunds mit Azure AD mithilfe von AlternateID 
Es wird empfohlen, lokal und in der Cloud identische Benutzerprinzipalnamen (UPN) zu verwenden. Wenn der lokale UPN eine nicht routingfähige Domäne verwendet (z.B. „Contoso.local“) oder aufgrund von lokalen Anwendungsabhängigkeiten nicht geändert werden kann, wird die Einrichtung einer alternativen Anmelde-ID empfohlen. Alternative Anmelde-IDs bieten die Möglichkeit, eine Anmeldeumgebung zu konfigurieren, in der sich Benutzer mit anderen Attributen als dem UPN anmelden können, z.B. mit der E-Mail-Adresse. Bei der Entscheidung für den Benutzerprinzipalnamen in Azure AD Connect wird standardmäßig das userPrincipalName-Attribut in Active Directory verwendet. Wenn Sie ein anderes Attribut als Benutzerprinzipalnamen auswählen und einen Verbund mit AD FS verwenden, konfiguriert Azure AD Connect AD FS für eine alternative Anmelde-ID. Ein Beispiel für die Auswahl eines anderen Attributs als Benutzerprinzipalnamen finden Sie unten:

![Auswahl des AlternateID-Attributs](./media/how-to-connect-fed-management/attributeselection.png)

Das Konfigurieren einer alternativen Anmelde-ID für AD FS besteht aus zwei Hauptschritten:
1. **Konfigurieren der richtigen Gruppe von Ausstellungsansprüchen**: Die Ausstellungsanspruchsregeln auf der vertrauenden Seite bei Azure AD werden geändert, um das ausgewählte UserPrincipalName-Attribut als alternative ID des Benutzers zu verwenden.
2. **Aktivieren der alternativen Anmelde-ID in der AD FS-Konfiguration**: Die AD FS-Konfiguration wird aktualisiert, damit AD FS Benutzer in den entsprechenden Gesamtstrukturen mithilfe der alternativen ID suchen können. Diese Konfiguration wird für AD FS unter Windows Server 2012 R2 (mit KB2919355) oder höher unterstützt. Wenn die AD FS-Server unter 2012 R2 ausgeführt werden, überprüft Azure AD Connect das Vorhandensein des erforderlichen KB-Updates. Wenn das KB-Update nicht installiert ist, wird nach Abschluss der Konfiguration eine Warnung angezeigt, wie unten dargestellt:

    ![Warnung zu fehlendem KB-Update unter 2012 R2](./media/how-to-connect-fed-management/kbwarning.png)

    Installieren Sie zur Korrektur der Konfiguration bei fehlendem KB-Update das erforderliche Update [KB2919355](https://go.microsoft.com/fwlink/?LinkID=396590), und reparieren Sie dann die Vertrauensstellung mithilfe der Informationen unter [AAD reparieren und AD FS-Vertrauensstellung](#repairthetrust).

> [!NOTE]
> Weitere Informationen zur alternativen Anmelde-ID sowie Schritte zur manuellen Konfiguration finden Sie unter [Konfigurieren alternativer Anmelde-IDs](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configuring-alternate-login-id)

## <a name="addadfsserver"></a>Hinzufügen eines AD FS-Servers 

> [!NOTE]
> Für Azure AD Connect ist das PFX-Zertifikat erforderlich, um einen AD FS-Server hinzuzufügen. Daher können Sie diesen Vorgang nur ausführen, wenn Sie die AD FS-Farm mit Azure AD Connect konfiguriert haben.

1. Wählen Sie **Weiteren Verbundserver bereitstellen** und klicken Sie auf **Weiter**.

   ![Weiterer Verbundserver](./media/how-to-connect-fed-management/AddNewADFSServer1.PNG)

2. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen des globalen Administrators für Azure AD ein und klicken Sie auf **Weiter**.

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/how-to-connect-fed-management/AddNewADFSServer2.PNG)

3. Geben Sie die Anmeldeinformationen des Domänenadministrators an.

   ![Anmeldeinformationen des Domänenadministrators](./media/how-to-connect-fed-management/AddNewADFSServer3.PNG)

4. Sie werden von Azure AD Connect zum Eingeben des Kennworts für die PFX-Datei aufgefordert, die Sie beim Konfigurieren der neuen AD FS-Farm mit Azure AD Connect angegeben haben. Klicken Sie auf **Kennwort eingeben** , um das Kennwort für die PFX-Datei anzugeben.

   ![Zertifikatkennwort](./media/how-to-connect-fed-management/AddNewADFSServer4.PNG)

    ![SSL-Zertifikat angeben](./media/how-to-connect-fed-management/AddNewADFSServer5.PNG)

5. Geben Sie auf der Seite **AD FS-Server** den Servernamen oder die IP-Adresse ein, der bzw. die der AD FS-Farm hinzugefügt werden soll.

   ![AD FS-Server](./media/how-to-connect-fed-management/AddNewADFSServer6.PNG)

6. Klicken Sie auf **Weiter** und führen Sie die Schritte auf der letzten **Konfigurationsseite** aus. Nachdem Azure AD Connect die Server der AD FS-Farm hinzugefügt hat, haben Sie die Möglichkeit, die Konnektivität zu überprüfen.

   ![Bereit zur Konfiguration](./media/how-to-connect-fed-management/AddNewADFSServer7.PNG)

    ![Installation abgeschlossen](./media/how-to-connect-fed-management/AddNewADFSServer8.PNG)

## <a name="addwapserver"></a>Hinzufügen eines AD FS-WAP-Servers 

> [!NOTE]
> Für Azure AD Connect ist das PFX-Zertifikat erforderlich, um einen WAP-Server hinzuzufügen. Daher können Sie diesen Vorgang nur ausführen, wenn Sie die AD FS-Farm mit Azure AD Connect konfiguriert haben.

1. Wählen Sie in der Liste mit den verfügbaren Aufgaben die Option **Webanwendungsproxy bereitstellen** .

   ![Webanwendungsproxy bereitstellen](./media/how-to-connect-fed-management/WapServer1.PNG)

2. Geben Sie die Anmeldeinformationen des globalen Azure-Administrators an.

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/how-to-connect-fed-management/wapserver2.PNG)

3. Geben Sie auf der Seite **SSL-Zertifikat angeben** das Kennwort für die PFX-Datei an, die Sie beim Konfigurieren der AD FS-Farm mit Azure AD Connect angegeben haben.
   ![Zertifikatkennwort](./media/how-to-connect-fed-management/WapServer3.PNG)

    ![SSL-Zertifikat angeben](./media/how-to-connect-fed-management/WapServer4.PNG)

4. Fügen Sie den Server als WAP-Server hinzu. Da der WAP-Server möglicherweise nicht der Domäne angehört, werden Sie vom Assistenten zur Eingabe von Administratoranmeldeinformationen für den hinzugefügten Server aufgefordert.

   ![Anmeldeinformationen für Verwaltungsserver](./media/how-to-connect-fed-management/WapServer5.PNG)

5. Geben Sie auf der Seite **Anmeldeinformationen der Proxyvertrauensstellung** Administratoranmeldeinformationen an, um die Proxyvertrauensstellung zu konfigurieren und auf den primären Server in der AD FS-Farm zuzugreifen.

   ![Anmeldeinformationen der Proxyvertrauensstellung](./media/how-to-connect-fed-management/WapServer6.PNG)

6. Auf der Seite **Bereit zur Konfiguration** zeigt der Assistent die Liste mit den Aktionen an, die ausgeführt werden.

   ![Bereit zur Konfiguration](./media/how-to-connect-fed-management/WapServer7.PNG)

7. Klicken Sie auf **Installieren** , um die Konfiguration abzuschließen. Nach Abschluss der Konfiguration bietet der Assistent Ihnen die Möglichkeit, die Verbindung mit den Servern zu überprüfen. Klicken Sie auf **Überprüfen** , um die Konnektivität zu überprüfen.

   ![Installation abgeschlossen](./media/how-to-connect-fed-management/WapServer8.PNG)

## <a name="addfeddomain"></a>Hinzufügen einer Verbunddomäne 

Das Hinzufügen einer Domäne, die einen Verbund mit Azure AD bilden soll, ist mithilfe von Azure AD Connect einfach. Azure AD Connect fügt die Domäne für den Verbund hinzu und ändert auch die Anspruchsregeln, damit der richtige Aussteller angezeigt wird, falls Sie über mehrere Domänen in einem Verbund mit Azure AD verfügen.

1. Zum Hinzufügen einer Verbunddomäne wählen Sie die Aufgabe **Weitere Azure AD-Domäne hinzufügen**.

   ![Weitere Azure AD-Domäne](./media/how-to-connect-fed-management/AdditionalDomain1.PNG)

2. Geben Sie auf der nächsten Seite des Assistenten die Anmeldeinformationen des globalen Administrators für Azure AD an.

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/how-to-connect-fed-management/AdditionalDomain2.PNG)

3. Geben Sie auf der Seite **Anmeldeinformationen für den Remotezugriff** die Anmeldeinformationen des Domänenadministrators an.

   ![Anmeldeinformationen für den Remotezugriff](./media/how-to-connect-fed-management/additionaldomain3.PNG)

4. Auf der nächsten Seite stellt der Assistent eine Liste mit den Azure AD-Domänen bereit, mit denen Sie für Ihr lokales Verzeichnis einen Verbund eingehen können. Wählen Sie die Domäne in der Liste aus.

   ![Azure AD-Domäne](./media/how-to-connect-fed-management/AdditionalDomain4.PNG)

    Nach der Auswahl der Domäne erhalten Sie vom Assistenten die entsprechenden Informationen zu weiteren Aktionen, die er ausführt, sowie zur Auswirkung der Konfiguration. In einigen Fällen – wenn Sie eine Domäne auswählen, die noch nicht in Azure AD überprüft wurde – erhalten Sie vom Assistenten Informationen zur Überprüfung der Domäne. Ausführliche Informationen finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../active-directory-domains-add-azure-portal.md) .

5. Klicken Sie auf **Weiter**. Auf der Seite **Bereit zur Konfiguration** wird die Liste mit den Aktionen angezeigt, die von Azure AD Connect ausgeführt werden. Klicken Sie auf **Installieren** , um die Konfiguration abzuschließen.

   ![Bereit zur Konfiguration](./media/how-to-connect-fed-management/AdditionalDomain5.PNG)

> [!NOTE]
> Benutzer aus der hinzugefügten Verbunddomäne müssen synchronisiert werden, bevor sie sich bei Azure AD anmelden können.

## <a name="ad-fs-customization"></a>AD FS-Anpassung
Die folgenden Abschnitte enthalten detaillierte Informationen zu einigen häufigen Aufgaben, die zum Anpassen der AD FS-Anmeldeseite eventuell erforderlich sind.

## <a name="customlogo"></a>Hinzufügen eines benutzerdefinierten Firmenlogos oder einer Abbildung 
Zum Ändern des auf der **Anmeldeseite** angezeigten Firmenlogos verwenden Sie das unten angegebene Windows PowerShell-Cmdlet und die entsprechende Syntax.

> [!NOTE]
> Die empfohlene Größe für das Logo beträgt 260 × 35 \@ 96 dpi und einer Dateigröße von höchstens 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [!NOTE]
> Der *TargetName* -Parameter ist erforderlich. Das mit AD FS veröffentlichte Standarddesign heißt „Standard“.

## <a name="addsignindescription"></a>Hinzufügen einer Anmeldebeschreibung 
Zum Hinzufügen einer Beschreibung für die **Anmeldeseite**verwenden Sie das folgende Windows PowerShell-Cmdlet und die folgende Syntax:

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

## <a name="modclaims"></a>Ändern von AD FS-Anspruchsregeln 
AD FS unterstützt eine umfangreiche Sprache für Ansprüche, die Sie zum Erstellen von benutzerdefinierten Anspruchsregeln verwenden können. Weitere Informationen finden Sie unter [Rolle der Anspruchsregelsprache](https://technet.microsoft.com/library/dd807118.aspx).

In den folgenden Abschnitten wird beschrieben, wie Sie benutzerdefinierte Regeln für einige Szenarien in Zusammenhang mit dem Azure AD- und AD FS-Verbund schreiben können.

### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>Unveränderliche ID hängt von einem Wert im Attribut ab
Azure AD Connect ermöglicht Ihnen das Festlegen eines Attributs, das als Quellanker verwendet wird, wenn Objekte mit Azure AD synchronisiert werden. Wenn der Wert im benutzerdefinierten Attribut nicht leer ist, kann es ratsam sein, einen Anspruch mit unveränderlicher ID ausgeben.

Beispielsweise können Sie **ms-ds-consistencyguid** als Attribut für den Quellanker auswählen und **ImmutableID** als **ms-ds-consistencyguid** ausgeben, falls für das Attribut ein Wert vorhanden ist. Wenn kein Wert für das Attribut vorhanden ist, geben Sie **objectGuid** als unveränderliche ID aus. Sie können den Satz mit den benutzerdefinierten Anspruchsregeln wie im folgenden Abschnitt beschrieben erstellen.

**Regel 1: Abfragen von Attributen**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Bei dieser Regel fragen Sie die Werte von **ms-ds-consistencyguid** und **objectGuid** für den Benutzer aus Active Directory ab. Ändern Sie den Speichernamen in einen passenden Speichernamen in Ihrer AD FS-Bereitstellung. Ändern Sie außerdem den Anspruchstyp in einen passenden Anspruchstyp für Ihren Verbund, wie Sie dies für **objectGuid** und **ms-ds-consistencyguid** definiert haben.

Durch die Verwendung von **add** anstelle von **issue** vermeiden Sie außerdem das Hinzufügen eines ausgehenden Ausstellungswerts für die Entität und können die Werte als Zwischenwerte verwenden. Sie geben den Anspruch in einer späteren Regel aus, nachdem Sie festgelegt haben, welcher Wert als unveränderliche ID verwendet wird.

**Regel 2: Überprüfen, ob „ms-ds-consistencyguid“ für den Benutzer vorhanden ist**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Diese Regel definiert das temporäre Flag **idflag**, das auf **useguid** festgelegt ist, wenn **ms-ds-concistencyguid** für den Benutzer nicht ausgefüllt ist. Die Logik dahinter ist die Tatsache, dass AD FS leere Ansprüche nicht zulässt. Wenn Sie also in der Regel 1 die Ansprüche http://contoso.com/ws/2016/02/identity/claims/objectguid und http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid hinzufügen, erhalten Sie den Anspruch **msdsconsistencyguid** nur, wenn der Wert für den Benutzer ausgefüllt ist. Wenn er nicht ausgefüllt ist, merkt AD FS, dass ein leerer Wert vorhanden ist, und verwirft ihn sofort. Alle Objekte verfügen über **objectGuid**, sodass dieser Anspruch immer vorhanden ist, nachdem Regel 1 ausgeführt wurde.

**Regel 3: Ausgeben von „ms-ds-consistencyguid“ als unveränderliche ID, sofern vorhanden**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconsistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Dies ist eine implizite **Exist** -Überprüfung. Wenn der Wert für den Anspruch vorhanden ist, geben Sie ihn als unveränderliche ID aus. Im vorherigen Beispiel wird der Anspruch **nameidentifier** verwendet. Sie müssen diesen in den entsprechenden Anspruchstyp für die unveränderliche ID in Ihrer Umgebung ändern.

**Regel 4: Ausgeben von objectGuid als unveränderliche ID, wenn ms-ds-consistencyGuid nicht vorhanden ist**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

In dieser Regel überprüfen Sie einfach das temporäre Flag **idflag**. Sie entscheiden, ob der Anspruch basierend auf dessen Wert ausgegeben werden soll.

> [!NOTE]
> Die Reihenfolge dieser Regeln ist wichtig.

### <a name="sso-with-a-subdomain-upn"></a>SSO mit Unterdomänen-UPN

Mit Azure AD Connect können Sie mehrere Domänen für den Verbund hinzufügen. Dies ist unter [Hinzufügen einer neuen Verbunddomäne](how-to-connect-fed-management.md#addfeddomain) beschrieben. Ab der Azure AD Connect-Version 1.1.553.0 wird automatisch die korrekte Anspruchsregel für „issuerID“ erstellt. Sollten Sie die Azure AD Connect-Version 1.1.553.0 (oder eine höhere Version) nicht verwenden können, verwenden Sie das Tool [Azure AD RPT Claim Rules](https://aka.ms/aadrptclaimrules), um korrekte Anspruchsregeln für die Azure AD-Vertrauensstellung der vertrauenden Seite zu generieren und festzulegen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über [Azure AD Connect-Optionen für die Benutzeranmeldung](plan-connect-user-signin.md).
