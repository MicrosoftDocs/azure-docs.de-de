---
title: 'Azure AD Connect: Benutzeranmeldung | Microsoft-Dokumentation'
description: Azure AD Connect-Benutzeranmeldung für benutzerdefinierte Einstellungen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997782"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect-Optionen für die Benutzeranmeldung
Mit Azure Active Directory Connect (Azure AD) können sich Ihre Benutzer sowohl bei Cloud- als auch bei lokalen Ressourcen mit denselben Kennwörtern anmelden. In diesem Artikel werden die wichtigsten Konzepte für jedes Identitätsmodell beschrieben, um Sie bei der Auswahl der Identität zu unterstützen, die Sie für die Registrierung für Azure AD verwenden möchten.

Wenn Sie mit dem Azure AD-Identitätsmodell bereits vertraut sind und mehr über ein bestimmtes Modell erfahren möchten, finden Sie Informationen unter dem folgenden Link.

* [Kennworthashsynchronisierung](#password-hash-synchronization) mit [einmaliger Anmeldung (Single Sign-On, SSO)](how-to-connect-sso.md)
* [Passthrough-Authentifizierung](how-to-connect-pta.md) mit [einmaliger Anmeldung (Single Sign-On, SSO)](how-to-connect-sso.md)
* [Verbund-SSO (mit Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Verbund mit PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Es ist wichtig zu beachten, dass Sie durch die Konfiguration des Verbunds für Azure AD eine Vertrauensstellung zwischen Ihrem Azure AD-Mandanten und Ihren Verbunddomänen einrichten. Mit dieser Vertrauensstellung verfügen Benutzer der Verbunddomäne über Zugriff auf Azure AD-Cloudressourcen innerhalb des Mandanten.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Auswählen einer Benutzeranmeldemethode für Ihre Organisation
Die erste Entscheidung bei der Implementierung von Azure AD Connect besteht in der Auswahl, welche Authentifizierungsmethode Ihre Benutzer zum Anmelden verwenden sollen. Es ist wichtig, sicherzustellen, dass Sie die richtige Methode auswählen, die die Sicherheits- und erweiterten Anforderungen Ihrer Organisation erfüllt. Authentifizierung ist von kritischer Bedeutung, weil sie die Identitäten von Benutzern für den Zugriff auf Apps und Daten in der Cloud überprüft. Um die richtige Authentifizierungsmethode auszuwählen, müssen Sie die Zeit, die vorhandene Infrastruktur, die Komplexität und die Implementierungskosten für die gewählte Lösung berücksichtigen. Diese Faktoren sind für jede Organisation unterschiedlich und können sich im Laufe der Zeit ändern.

Azure AD unterstützt die folgenden Authentifizierungsmethoden: 

* **Cloudauthentifizierung** – Wenn Sie diese Authentifizierungsmethode auswählen, übernimmt Azure AD den Authentifizierungsprozess für die Anmeldung der Benutzer. Bei der Cloudauthentifizierung können Sie zwischen zwei Optionen wählen: 
   * **Kennworthashsynchronisierung (PHS)** – Die Kennworthashsynchronisierung ermöglicht es Benutzern, denselben Benutzernamen und dasselbe Kennwort wie lokal zu verwenden, ohne eine zusätzliche Infrastruktur neben Azure AD Connect bereitstellen zu müssen. 
   * **Passthrough-Authentifizierung (PTA)** – Diese Option ist vergleichbar mit der Kennworthashsynchronisierung, bietet aber eine einfache Kennwortvalidierung, die lokale Software-Agents für Organisationen mit strengen Sicherheits- und Compliancerichtlinien verwendet.
* **Verbundauthentifizierung** – Wenn Sie diese Authentifizierungsmethode auswählen, übergibt Azure AD den Authentifizierungsprozess zur Validierung der Benutzeranmeldung an ein separates, vertrauenswürdiges Authentifizierungssystem, z. B. an AD FS oder ein Drittanbieter-Verbundsystem. 

Für die meisten Organisationen, die lediglich die Benutzeranmeldung für Microsoft 365, SaaS-Anwendungen und andere Azure AD-basierte Ressourcen aktivieren möchten, empfiehlt sich die Standardoption für die Kennworthashsynchronisierung.
 
Ausführliche Informationen zum Auswählen einer Authentifizierungsmethode finden Sie unter [Wählen der richtigen Authentifizierungsmethode für Ihre Azure Active Directory-Hybrididentitätslösung](./choose-ad-authn.md).

### <a name="password-hash-synchronization"></a>Kennworthashsynchronisierung
Mit der Kennworthashsynchronisierung werden Benutzerkennworthashes aus Ihrem lokalen Active Directory mit Azure AD synchronisiert. Werden Kennwörter geändert oder lokal zurückgesetzt, werden die neuen Kennworthashes sofort mit Azure AD synchronisiert, damit Ihre Benutzer für Cloudressourcen und lokale Ressourcen dasselbe Kennwort verwenden können. Die Kennwörter werden weder an Azure AD übermittelt noch als Klartext in Azure AD gespeichert. Sie können die Kennworthashsynchronisierung mit der Kennwortrückschreibung kombinieren, um es den Benutzern zu ermöglichen, ihre Kennwörter in Azure AD selbst zurückzusetzen.

Darüber hinaus können Sie auch das [nahtlose einmalige Anmelden](how-to-connect-sso.md) für Benutzer auf in die Domäne eingebundenen Computern aktivieren, die sich im Unternehmensnetzwerk befinden. Beim einmaligen Anmelden müssen aktivierte Benutzer nur einen Benutzernamen eingeben, um sicher auf die Cloudressourcen zuzugreifen.

![Kennworthashsynchronisierung](./media/plan-connect-user-signin/passwordhash.png)

Weitere Informationen finden Sie im Artikel [Kennworthashsynchronisierung](how-to-connect-password-hash-synchronization.md).

### <a name="pass-through-authentication"></a>Passthrough-Authentifizierung
Bei der Passthrough-Authentifizierung wird das Kennwort des Benutzers anhand des lokalen Active Directory-Controllers überprüft. Das Kennwort muss in Azure AD nicht hinterlegt sein. So können lokale Richtlinien, z.B. Einschränkungen der Anmeldestunden, während der Authentifizierung für Clouddienste ausgewertet werden.

Bei der Passthrough-Authentifizierung wird ein einfacher Agent auf einem in die Domäne eingebundenen Windows Server 2012 R2-Computer in der lokalen Umgebung genutzt. Dieser Agent lauscht auf Anforderungen zur Kennwortüberprüfung. Es müssen für ihn keine Ports für eingehenden Datenverkehr aus dem Internet geöffnet sein.

Darüber hinaus können Sie auch das einmalige Anmelden für Benutzer auf in die Domäne eingebundenen Computern aktivieren, die sich im Unternehmensnetzwerk befinden. Beim einmaligen Anmelden müssen aktivierte Benutzer nur einen Benutzernamen eingeben, um sicher auf die Cloudressourcen zuzugreifen.
![Passthrough-Authentifizierung](./media/plan-connect-user-signin/pta.png)

Weitere Informationen finden Sie unter
- [Passthrough-Authentifizierung](how-to-connect-pta.md)
- [Einmaliges Anmelden](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Verbund, der eine neue oder vorhandene Farm mit AD FS in Windows Server 2012 R2 verwendet
Durch eine Verbundanmeldung können sich Ihre Benutzer bei Azure AD-basierten Diensten mit ihren lokalen Kennwörtern anmelden. Während sie sich auf dem internen Netzwerk befinden, müssen sie nicht einmal ihre Kennwörter eingegeben. Mit der Verbundoption für AD FS können Sie eine neue oder vorhandene Windows Server 2012 R2-Farm mit AD FS bereitstellen. Wenn Sie eine vorhandene Farm angeben, konfiguriert Azure AD Connect die Vertrauensstellung zwischen der Farm und Azure AD, sodass sich Ihre Benutzer anmelden können.

<center>

![Verbund mit AD FS unter Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Bereitstellen des Verbunds mit AD FS unter Windows Server 2012 R2

Wenn Sie eine neue Farm bereitstellen, ist Folgendes erforderlich:

* Ein Windows Server 2012 R2-Server für den Verbundserver.
* Ein Windows Server 2012 R2-Server für den Webanwendungsproxy.
* Eine PFX-Datei mit einem TLS/SSL-Zertifikat für den vorgesehenen Verbunddienstnamen. Zum Beispiel: „fs.contoso.com“.

Wenn Sie eine neue Farm bereitstellen oder eine vorhandene Farm verwenden, ist Folgendes erforderlich:

* Lokale Administratorrechte auf Ihren Verbundservern.
* Lokale Administratorrechte für jeden Arbeitsgruppenserver (keiner Domäne angehörend), auf dem Sie die Webanwendungsproxy-Rolle bereitstellen möchten.
* Der Computer, auf dem Sie den Assistenten ausführen, muss eine Verbindung mit allen weiteren Computern herstellen können, auf denen Sie AD FS oder den Webanwendungsproxy über die Windows-Remoteverwaltung installieren möchten.

Weitere Informationen finden Sie unter [Konfigurieren von SSO mit AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Verbund mit PingFederate
Durch eine Verbundanmeldung können sich Ihre Benutzer bei Azure AD-basierten Diensten mit ihren lokalen Kennwörtern anmelden. Während sie sich auf dem internen Netzwerk befinden, müssen sie nicht einmal ihre Kennwörter eingegeben.

Weitere Informationen zum Konfigurieren von PingFederate für die Verwendung mit Azure Active Directory finden Sie unter [PingFederate Integration with Azure Active Directory and Office 365](https://www.pingidentity.com/AzureADConnect) (PingFederate-Integration in Azure Active Directory und Office 365).

Informationen zum Einrichten von Azure AD Connect mit PingFederate finden Sie unter [Benutzerdefinierte Installation von Azure AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate).

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Anmelden mit einer früheren Version von AD FS oder einer Drittanbieterlösung
Wenn Sie die Cloudanmeldung bereits mit einer früheren Version von AD FS (z.B. AD FS 2.0) oder einer Drittanbieterlösung konfiguriert haben, können Sie die Konfiguration der Benutzeranmeldung über Azure AD Connect überspringen. Auf diese Weise profitieren Sie von den neuesten Synchronisierungsfunktionen und anderen Features Azure AD Connect und können gleichzeitig Ihre vorhandene Lösung für die Anmeldung nutzen.

Weitere Informationen finden Sie unter [Azure AD-Verbund – Kompatibilitätsliste für Drittanbieter](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Benutzeranmeldung und Benutzerprinzipalname
### <a name="understanding-user-principal-name"></a>Grundlegendes zu Benutzerprinzipalnamen
In Active Directory ist das Standardsuffix des Benutzerprinzipalnamens (UPN) der DNS-Name der Domäne, in der das Benutzerkonto erstellt wurde. In den meisten Fällen ist dies der Domänenname, der als Organisationsdomäne im Internet registriert ist. Allerdings können Sie weitere UPN-Suffixe mithilfe von Active Directory-Domänen und -Vertrauensstellungen hinzufügen.

Der UPN des Benutzers hat das Format username@domain. Für eine Active Directory-Domäne mit dem Namen „contoso.com“ hat Benutzer John beispielsweise den UPN „john@contoso.com“. Der UPN des Benutzers basiert auf RFC 822. Obwohl der UPN und die E-Mail-Adresse das gleiche Format verwenden, entspricht der Wert des UPN für einen Benutzer möglicherweise nicht der E-Mail-Adresse dieses Benutzers.

### <a name="user-principal-name-in-azure-ad"></a>Benutzerprinzipalnamen in Azure AD
Der Azure AD Connect-Assistent verwendet entweder das userPrincipalName-Attribut, oder Sie können das Attribut (bei der benutzerdefinierten Installation) angeben, das lokal als Benutzerprinzipalname in Azure AD verwendet werden soll. Dies ist der Wert, der zur Anmeldung bei Azure AD verwendet wird. Wenn der Wert des Attributs für den Benutzerprinzipalnamen – userPrincipalName – nicht einer überprüften Domäne in Azure AD entspricht, ersetzt ihn Azure AD durch den Standardwert „.onmicrosoft.com“.

Jedes Verzeichnis in Azure Active Directory verfügt über einen integrierten Domänennamen der Form „contoso.onmicrosoft.com“, den Sie verwenden können, um mit der Nutzung von Azure oder anderen Microsoft-Diensten zu beginnen. Sie können mithilfe von benutzerdefinierten Domänen die Anmeldung vereinfachen und die Benutzerfreundlichkeit erhöhen. Informationen zu benutzerdefinierten Domänennamen in Azure AD und eine Anleitung zur Überprüfung einer Domäne finden Sie unter [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfiguration der Azure AD-Anmeldung
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfigurieren der Azure AD-Anmeldung mit Azure AD Connect
Die Azure AD-Anmeldung hängt davon ab, ob Azure AD das UPN-Suffix eines zu synchronisierenden Benutzers mit einer der überprüften benutzerdefinierten Domänen im Azure AD-Verzeichnis abgleichen kann. Azure AD Connect bietet Unterstützung beim Konfigurieren der Einstellungen für die Azure AD-Anmeldung, damit die Benutzeranmeldung in der Cloud der lokalen Anmeldung ähnelt.

Azure AD Connect listet die UPN-Suffixe auf, die für die Domänen definiert sind, und versucht, diese mit einer benutzerdefinierten Domäne in Azure AD abzugleichen. Danach hilft Azure AD Connect Ihnen mit der entsprechenden Aktion, die ausgeführt werden muss.
Die Azure AD-Anmeldeseite listet die UPN-Suffixe auf, die für das lokale Active Directory-Verzeichnis definiert wurden, und zeigt den entsprechenden Status für jedes Suffix. Mögliche Statuswerte:

| State | BESCHREIBUNG | Erforderliche Aktion |
|:--- |:--- |:--- |
| Überprüft |Azure AD Connect hat eine übereinstimmende überprüfte Domäne in Azure AD festgestellt. Alle Benutzer dieser Domäne können sich mit ihren lokalen Anmeldeinformationen anmelden. |Es ist keine Aktion erforderlich. |
| Nicht überprüft |Azure AD Connect hat eine übereinstimmende benutzerdefinierte Domäne in Azure AD gefunden, die allerdings nicht überprüft ist. Das UPN-Suffix der Benutzer dieser Domäne wird nach der Synchronisierung in das Standardsuffix „.onmicrosoft.com“ geändert, wenn die Domäne nicht überprüft wurde. | [Überprüfen der benutzerdefinierten Domäne in Azure AD](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nicht hinzugefügt |Azure AD Connect konnte keine benutzerdefinierte Domäne finden, die dem UPN-Suffix entspricht. Das UPN-Suffix der Benutzer der Benutzer dieser Domäne wird in das Standardsuffix „.onmicrosoft.com“ geändert, wenn die Domäne nicht in Azure hinzugefügt und überprüft wurde. | [Hinzufügen eines benutzerdefinierten Domänennamens zu Azure Active Directory](../fundamentals/add-custom-domain.md) |

Die Azure AD-Anmeldeseite listet das für die lokale Active Directory-Instanz definierte UPN-Suffix und die entsprechende benutzerdefinierte Domäne in Azure AD mit ihrem aktuellen Überprüfungsstatus auf. In einer benutzerdefinierten Installation können Sie jetzt das Attribut für Benutzerprinzipalnamen auf der Seite **Azure AD-Anmeldung** auswählen.

![Azure AD-Anmeldeseite](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Sie können auf die Schaltfläche für die Aktualisierung klicken, um den aktuellen Status der benutzerdefinierten Domänen erneut aus Azure AD abzurufen.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Auswählen des Attributs für den Benutzerprinzipalnamen in Azure AD
Das userPrincipalName-Attribut wird von Benutzern verwendet, wenn sie sich bei Azure AD und Microsoft 365 anmelden. Sie müssen die Domänen überprüfen (auch als UPN-Suffix bezeichnet), die in Azure AD verwendet werden, bevor die Benutzer synchronisiert werden.

Es wird dringend empfohlen, das Standardattribut „userPrincipalName“ beizubehalten. Wenn dieses Attribut nicht routingfähig ist und nicht überprüft werden kann, können Sie ein anderes Attribut als das Attribut mit der Anmelde-ID auswählen, beispielsweise „email“. Dieser Wert wird als alternative ID bezeichnet. Der Attributwert der alternativen ID muss dem RFC 822-Standard entsprechen. Sie können eine alternative ID mit Kennwort- und Verbund-SSO als Anmeldungslösung verwenden.

> [!NOTE]
> Eine alternative ID ist nicht mit allen Microsoft 365-Workloads kompatibel. Weitere Informationen finden Sie unter [Konfigurieren der alternativen Anmelde-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Andere benutzerdefinierte Zustandswerte für Domänen und Auswirkungen auf die Azure-Anmeldung
Es ist wichtig, die Beziehung zwischen den benutzerdefinierten Zustandswerten für Domänen in Ihrem Azure AD-Verzeichnis und den lokal definierten UPN-Suffixen zu verstehen. Gehen wir die verschiedenen Azure-Anmeldemöglichkeiten beim Einrichten der Synchronisierung mit Azure AD Connect durch.

Nehmen wir für die folgenden Informationen einmal an, dass wir mit dem UPN-Suffix „contoso.com“ arbeiten, das zum Beispiel im lokalen Verzeichnis als Teil des UPN verwendet wird. Beispiel: user@contoso.com.

###### <a name="express-settingspassword-hash-synchronization"></a>Express-Einstellungen/Kennworthashsynchronisierung

| State | Auswirkung auf die Azure-Benutzeranmeldung |
|:---:|:--- |
| Nicht hinzugefügt |In diesem Fall wurde keine benutzerdefinierte Domäne für „contoso.com“ im Azure AD-Verzeichnis hinzugefügt. Benutzer mit lokalem UPN und dem Suffix „@contoso.com“ können nicht ihren lokalen UPN zur Azure-Anmeldung verwenden. Sie müssen stattdessen einen neuen, von Azure AD bereitgestellten UPN verwenden, indem Sie das Suffix für das Azure AD-Standardverzeichnis hinzufügen. Wenn Sie beispielsweise Benutzer mit dem Azure AD-Verzeichnis „azurecontoso.onmicrosoft.com“ synchronisieren, erhält der lokale Benutzer user@contoso.com den UPN user@azurecontoso.onmicrosoft.com. |
| Nicht überprüft |In diesem Fall wurde dem Azure AD-Verzeichnis die benutzerdefinierte Domäne „contoso.com“ hinzugefügt. Diese ist jedoch noch nicht überprüft. Wenn Sie mit der Synchronisierung von Benutzern fortfahren, ohne die Domäne zu überprüfen, weist Azure AD den Benutzern wie im Szenario „Nicht hinzugefügt“ neue UPNs zu. |
| Überprüft |In diesem Fall wurde die benutzerdefinierte Domäne „contoso.com“ bereits hinzugefügt, und die Domäne wurde in Azure AD auf das UPN-Suffix überprüft. Benutzer sind nun in der Lage, ihren lokalen Benutzerprinzipalnamen (z.B. „user@contoso.com“) zu verwenden, um sich bei Azure anzumelden, nachdem sie mit Azure AD synchronisiert wurden. |

###### <a name="ad-fs-federation"></a>AD FS-Verbund
Sie können mit der Standarddomäne „.onmicrosoft.com“ in Azure AD oder einer nicht überprüften benutzerdefinierten Domäne in Azure AD keinen Verbund erstellen. Wenn Sie bei der Ausführung des Azure AD Connect-Assistenten eine nicht überprüfte Domäne auswählen, um einen Verbund zu erstellen, fordert Sie Azure AD Connect auf, über den DNS-Hostinganbieter für die Domäne zunächst die erforderlichen Datensätze zu erstellen. Weitere Informationen finden Sie unter [Überprüfen der für den Verbund ausgewählten Azure AD-Domäne](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Wenn Sie die Benutzeranmeldeoption **Verbund mit AD FS** ausgewählt haben, müssen Sie über eine benutzerdefinierte Domäne verfügen, um mit der Erstellung eines Verbunds in Azure AD fortzufahren. Im vorliegenden Fall bedeutet dies, dass wir dem Azure AD-Verzeichnis die benutzerdefinierte Domäne „contoso.com“ hinzufügen müssen.

| State | Auswirkung auf die Azure-Benutzeranmeldung |
|:---:|:--- |
| Nicht hinzugefügt |In diesem Fall konnte Azure AD Connect im Azure AD-Verzeichnis keine übereinstimmende benutzerdefinierte Domäne für das UPN-Suffix „contoso.com“ finden. Sie müssen die benutzerdefinierte Domäne „contoso.com“ hinzufügen, wenn Benutzer sich mithilfe von AD FS mit ihren lokalen Benutzerprinzipalnamen (z.B. user@contoso.com) anmelden sollen. |
| Nicht überprüft |In diesem Fall zeigt Azure AD Connect entsprechende Angaben an, damit Sie Ihre Domäne zu einem späteren Zeitpunkt überprüfen können. |
| Überprüft |In diesem Fall können Sie ohne weitere Aktionen direkt mit der Konfiguration fortfahren. |

## <a name="changing-the-user-sign-in-method"></a>Ändern der Benutzeranmeldungsmethode
Nach der Erstkonfiguration von Azure AD Connect durch den Assistenten können Sie mit den verfügbaren Aufgaben in Azure AD Connect die Benutzeranmeldemethoden „Verbund“, „Kennworthashsynchronisierung“ oder „Passthrough-Authentifizierung“ ändern. Führen Sie den Azure AD Connect-Assistenten erneut aus. Es wird eine Liste mit Aufgaben angezeigt, die Sie durchführen können. Wählen Sie **Ändern der Benutzeranmeldung** aus der Liste der Aufgaben.

![Benutzeranmeldung ändern](./media/plan-connect-user-signin/changeusersignin.png)

Auf der nächsten Seite werden Sie aufgefordert, die Anmeldeinformationen für Azure AD anzugeben.

![Screenshot, der zeigt, wo Sie die Anmeldeinformationen für Azure AD eingeben müssen](./media/plan-connect-user-signin/changeusersignin2.png)

Wählen Sie auf der Seite **Benutzeranmeldung** die gewünschte Benutzeranmeldung aus.

![Stellen Sie eine Verbindung mit Azure AD her.](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Wenn Sie nur vorübergehend zur Kennworthashsynchronisierung wechseln, wählen Sie das Kontrollkästchen **Benutzerkonten nicht konvertieren** aus. Ist diese Option nicht aktiviert, werden alle Benutzer zu Verbundbenutzern konvertiert. Dieser Vorgang kann mehrere Stunden dauern.
>
>

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
- Erfahren Sie mehr über [Entwurfskonzepte für Azure AD Connect](plan-connect-design-concepts.md)