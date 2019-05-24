---
title: 'Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Domäne für Azure Front Door Service | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie HTTPS in Ihrer Azure Front Door Service-Konfiguration für eine benutzerdefinierte Domäne aktivieren und deaktivieren.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/05/2018
ms.author: sharadag
ms.openlocfilehash: fc4db12f722d1330f0642e155c02a1936373e256
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65520492"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Front Door-Domäne

In diesem Tutorial erfahren Sie, wie Sie im Abschnitt mit den Front-End-Hosts das HTTPS-Protokoll für eine benutzerdefinierte Domäne aktivieren, die Ihrer Front Door-Instanz zugeordnet ist. Durch die Verwendung des HTTPS-Protokolls in Ihrer benutzerdefinierten Domäne (Beispiel: https:\//www.contoso.com) stellen Sie sicher, dass Ihre sensiblen Daten per TLS/SSL-Verschlüsselung sicher zugestellt werden, wenn diese über das Internet gesendet werden. Wenn Ihr Webbrowser über HTTPS eine Verbindung mit einer Website herstellt, überprüft er das Sicherheitszertifikat der Website, um sicherzustellen, dass es von einer legitimen Zertifizierungsstelle stammt. Dieser Prozess sorgt für Sicherheit und schützt Ihre Webanwendungen vor Angriffen.

Azure Front Door Service unterstützt HTTPS standardmäßig für einen Front Door-Standardhostnamen. Wenn Sie beispielsweise eine Front Door-Instanz erstellen (z.B. „https:\//contoso.azurefd.net“), wird HTTPS automatisch für Anforderungen an https://contoso.azurefd.net aktiviert. Nachdem Sie jedoch die benutzerdefinierte Domäne „www.contoso.com“ integriert haben, müssen Sie zusätzlich HTTPS für den Front-End-Host aktivieren.   

Zu den wichtigsten Attributen des benutzerdefinierten HTTPS-Features zählen unter anderem folgende:

- Keine zusätzlichen Kosten: Zertifikatabruf und -verlängerung sind kostenlos, und für HTTPS-Datenverkehr fallen keine zusätzlichen Kosten an. 

- Unkomplizierte Aktivierung: Über das [Azure-Portal](https://portal.azure.com) ist die Bereitstellung mit einem einzelnen Klick möglich. Das Feature kann aber auch per REST-API oder mithilfe anderer Entwicklertools aktiviert werden.

- Umfassende Zertifikatverwaltung: Die gesamte Zertifikatbeschaffung und -verwaltung wird für Sie erledigt. Zertifikate werden automatisch bereitgestellt und vor Ablauf verlängert. Dadurch ist gewährleistet, dass keine Dienstunterbrechungen aufgrund eines abgelaufenen Zertifikats auftreten.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Aktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne
> - Verwenden eines AFD-verwalteten Zertifikats 
> - Verwenden Ihres eigenen Zertifikats (ein benutzerdefiniertes SSL-Zertifikat)
> - Überprüfen der Domäne
> - Deaktivieren des HTTPS-Protokolls in Ihrer benutzerdefinierten Domäne


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst eine Front Door-Instanz und mindestens eine integrierte benutzerdefinierte Domäne erstellen. Weitere Informationen finden Sie unter [Tutorial: Hinzufügen einer benutzerdefinierten Domäne für Ihre „Front Door“](front-door-custom-domain.md).

## <a name="ssl-certificates"></a>SSL-Zertifikate

Sie müssen ein SSL-Zertifikat verwenden, um das HTTPS-Protokoll für die sichere Übermittlung von Inhalten in einer benutzerdefinierten Front Door-Domäne zu aktivieren. Sie können ein von Azure Front Door Service verwaltetes Zertifikat oder ein eigenes Zertifikat verwenden.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>Option 1 (Standard): Verwenden eines von Azure Front Door Service verwalteten Zertifikats

Wenn Sie ein von Azure Front Door Service verwaltetes Zertifikat verwenden, kann das HTTPS-Feature mit nur wenigen Mausklicks aktiviert werden. Azure Front Door Service übernimmt sämtliche Zertifikatverwaltungsaufgaben wie etwa Beschaffung und Verlängerung. Der Prozess wird umgehend nach der Aktivierung des Features gestartet. Wenn die benutzerdefinierte Domäne bereits dem standardmäßigen Front-End-Host von Front Door (`{hostname}.azurefd.net`) zugeordnet ist, ist keine weitere Aktion erforderlich. Front Door durchläuft die Schritte und schließt Ihre Anforderung automatisch ab. Sollte Ihre benutzerdefinierte Domäne an anderer Stelle zugeordnet sein, müssen Sie per E-Mail bestätigen, dass Sie der Besitzer der Domäne sind.

Um HTTPS für eine benutzerdefinierte Domäne zu aktivieren, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem **Front Door**-Profil.

2. Wählen Sie in der Liste der Front-End-Hosts die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

3. Klicken Sie im Abschnitt **HTTPS für benutzerdefinierte Domänen** auf **Aktiviert**, und wählen Sie **Mit Front Door verwaltet** als Zertifikatquelle aus.

4. Klicken Sie auf Speichern.

5. Fahren Sie mit [Domäne überprüfen](#validate-the-domain) fort.


### <a name="option-2-use-your-own-certificate"></a>Option 2: Verwenden Ihres eigenen Zertifikats

Sie können das HTTPS-Feature mit Ihrem eigenen Zertifikat aktivieren. Dabei erfolgt eine Integration in Azure Key Vault, was eine sichere Speicherung Ihrer Zertifikate ermöglicht. Azure Front Door Service nutzt diesen sicheren Mechanismus zum Abrufen Ihres Zertifikats, und es sind einige zusätzliche Schritte erforderlich. Wenn Sie Ihr SSL-Zertifikat erstellen, müssen Sie dafür eine zulässige Zertifizierungsstelle (certificate authority, CA) verwenden. Bei Verwendung einer unzulässigen Zertifizierungsstelle wird Ihre Anforderung abgelehnt. Eine Liste mit zulässigen Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS für Azure Front Door Service finden Sie [hier](front-door-troubleshoot-allowed-ca.md).

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Vorbereiten Ihres Azure Key Vault-Kontos und Ihres Zertifikats
 
1. Azure Key Vault: Sie benötigen ein aktives Azure Key Vault-Konto. Dieses muss zum gleichen Abonnement gehören wie die Front Door-Instanz, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. Erstellen Sie bei Bedarf ein Azure Key Vault-Konto.
 
2. Azure Key Vault-Zertifikate: Wenn Sie bereits über ein Zertifikat verfügen, können Sie es direkt in Ihr Azure Key Vault-Konto hochladen. Alternativ können Sie direkt in Azure Key Vault ein neues Zertifikat über eine der Partnerzertifizierungsstellen mit Azure Key Vault-Integration erstellen.

> [!WARNING]
> </br> – Azure Front Door Service unterstützt derzeit nur Key Vault-Konten im selben Abonnement wie die Front Door-Konfiguration. Das Auswählen eines Schlüsseltresors in einem anderen Abonnement als Ihre Front Door-Konfiguration führt zu einem Fehler.
> </br> – Azure Front Door Service unterstützt derzeit nur Zertifikate, die mit einer PFX-Datei **ohne** Kennwort hochgeladen wurden.

#### <a name="register-azure-front-door-service"></a>Registrieren von Azure Front Door Service

Registrieren Sie den Dienstprinzipal für Azure Front Door Service als App in Azure Active Directory mithilfe von PowerShell.

1. Installieren Sie bei Bedarf auf dem lokalen Computer [Azure PowerShell](/powershell/azure/install-az-ps) in PowerShell.

2. Führen Sie in PowerShell den folgenden Befehl aus:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-service-access-to-your-key-vault"></a>Gewähren von Zugriff auf Ihren Schlüsseltresor für Azure Front Door Service
 
Gewähren Sie Azure Front Door Service Berechtigungen für den Zugriff auf die Zertifikate unter „Geheimnisse“ in Ihrem Azure Key Vault-Konto.

1. Klicken Sie in Ihrem Key Vault-Konto unter „EINSTELLUNGEN“ auf **Zugriffsrichtlinien** und anschließend auf **Neu hinzufügen**, um eine neue Richtlinie zu erstellen.

2. Suchen Sie in **Prinzipal auswählen** nach **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037**, und wählen Sie **Microsoft.Azure.Frontdoor** aus. Klicken Sie auf **Auswählen**.


3. Wählen Sie unter **Berechtigungen für Geheimnis** die Option **Abrufen** aus, um Front Door das Abrufen und Auflisten der Zertifikate zu ermöglichen. 

4. Klicken Sie auf **OK**. 

    Azure Front Door Service kann nun auf diesen Schlüsseltresor und auf die darin gespeicherten Zertifikate (Geheimnisse) zugreifen.
 
#### <a name="select-the-certificate-for-azure-front-door-service-to-deploy"></a>Auswählen des bereitzustellenden Zertifikats für Azure Front Door Service
 
1. Kehren Sie im Portal zu Front Door zurück. 

2. Wählen Sie in der Liste mit den benutzerdefinierten Domänen die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

    Die Seite **Benutzerdefinierte Domäne** wird angezeigt.

3. Wählen Sie unter „Zertifikatverwaltungstyp“ die Option **Eigenes Zertifikat verwenden** aus. 

4. Azure Front Door Service erfordert, dass das Abonnement des Key Vault-Kontos mit dem von Front Door identisch ist. Wählen Sie einen Schlüsseltresor, ein Zertifikat (Geheimnis) und eine Zertifikatversion aus.

    Azure Front Door Service führt die folgenden Informationen auf: 
    - Die Schlüsseltresorkonten für Ihre Abonnement-ID 
    - Die Zertifikate (Geheimnisse) unter dem ausgewählten Schlüsseltresor 
    - Die verfügbaren Zertifikatversionen 
 
5. Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort.

## <a name="validate-the-domain"></a>Überprüfen der Domäne

Wenn Sie bereits eine benutzerdefinierte Domäne verwenden, die Ihrem benutzerdefinierten Endpunkt mit einem CNAME-Eintrag zugeordnet ist, oder Sie Ihr eigenes Zertifikat verwenden, können Sie mit  
[Benutzerdefinierte Domäne ist Front Door zugeordnet](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record) fortfahren. Fahren Sie andernfalls mit [Benutzerdefinierte Domäne ist Front Door nicht zugeordnet](#custom-domain-is-not-mapped-to-your-front-door) fort, falls der CNAME-Eintrag für Ihre Domäne nicht mehr vorhanden ist oder falls er die Unterdomäne „afdverify“ enthält.

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Benutzerdefinierte Domäne ist Front Door mit einem CNAME-Eintrag zugeordnet

Beim Hinzufügen einer benutzerdefinierten Domäne zu den Front Door-Front-End-Hosts haben Sie einen CNAME-Eintrag in der DNS-Tabelle Ihrer Domänenregistrierungsstelle erstellt, um ihn dem Standardhostnamen „.azurefd.net“ von Front Door zuzuordnen. Wenn dieser CNAME-Eintrag noch vorhanden ist und die afdverify-Unterdomäne nicht enthält, wird er von der DigiCert-Zertifizierungsstelle verwendet, um den Besitz Ihrer benutzerdefinierten Domäne automatisch zu überprüfen. 

Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich.

Ihr CNAME-Eintrag sollte im folgenden Format vorliegen, wobei *Name* Ihr benutzerdefinierter Domänenname und *Wert* der Standardhostname „.azurefd.net“ von Front Door ist:

| NAME            | Type  | Wert                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

Weitere Informationen über CNAME-Einträge finden Sie unter [Erstellen Sie die CNAME-DNS-Einträge](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain).

Wenn Ihr CNAME-Eintrag im richtigen Format vorliegt, überprüft DigiCert automatisch Ihren benutzerdefinierten Domänennamen und erstellt ein dediziertes Zertifikat für Ihren Domänennamen. DigiCert sendet Ihnen keine Bestätigungs-E-Mail, und Sie müssen Ihre Anforderung nicht genehmigen. Das Zertifikat ist ein Jahr lang gültig und wird automatisch verlängert, bevor es abläuft. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort. 

Die automatische Überprüfung dauert normalerweise einige Minuten. Öffnen Sie ein Supportticket, falls Ihre Domäne nicht innerhalb einer Stunde validiert wurde.

>[!NOTE]
>Wenn Sie über einen CAA-Datensatz (Certificate Authority Authorization) bei Ihrem DNS-Anbieter verfügen, muss dieser DigiCert als gültige Zertifizierungsstelle enthalten. Ein CAA-Datensatz ermöglicht es Domänenbesitzern, ihrem jeweiligen DNS-Anbieter mitzuteilen, welche Zertifizierungsstellen zum Ausstellen von Zertifikaten für ihre Domäne autorisiert sind. Wenn eine Zertifizierungsstelle einen Auftrag für ein Zertifikat für eine Domäne empfängt, für die ein CAA-Datensatz vorliegt und die Zertifizierungsstelle nicht als autorisierter Aussteller angegeben ist, darf sie das Zertifikat für die Domäne oder Unterdomäne nicht ausstellen. Informationen zum Verwalten von CAA-Einträgen finden Sie unter [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Verwalten von CAA-Einträgen). Ein Tool für CAA-Einträge finden Sie unter [CAA Record Helper](https://sslmate.com/caa/) (Hilfsprogramm für CAA-Einträge).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Benutzerdefinierte Domäne ist Front Door nicht zugeordnet

Wenn der CNAME-Eintrag für Ihren Endpunkt nicht mehr vorhanden ist oder die afdverify-Unterdomäne enthält, folgen Sie den weiteren Anweisungen in diesem Schritt.

Nach der Aktivierung von HTTPS für Ihre benutzerdefinierte Domäne überprüft die DigiCert-CA, ob die Domäne wirklich Ihnen gehört. Hierzu setzt sich DigiCert mit dem Registranten der Domäne in Verbindung, der in den [WHOIS](http://whois.domaintools.com/)-Registranteninformationen der Domäne angegeben ist. Der Kontakt wird über die E-Mail-Adresse (Standardverfahren) oder die Telefonnummer in der WHOIS-Registrierung hergestellt. HTTPS wird für Ihre benutzerdefinierte Domäne erst nach Abschluss der Domänenüberprüfung aktiviert. Die Genehmigung der Domäne muss innerhalb von sechs Werktagen erfolgen. Anforderungen, die nicht innerhalb von sechs Werktagen genehmigt werden, werden automatisch abgebrochen. 

![WHOIS-Datensatz](./media/front-door-custom-domain-https/whois-record.png)

DigiCert sendet zur Überprüfung auch eine E-Mail an zusätzliche E-Mail-Adressen. Falls die WHOIS-Registranteninformationen privat sind, vergewissern Sie sich, dass Sie die Bestätigung direkt über eine der folgenden Adressen vornehmen können:

admin@&lt;Ihr-Domänenname.com&gt;  
administrator@&lt;Ihr-Domänenname.com&gt;  
webmaster@&lt;Ihr-Domänenname.com&gt;  
hostmaster@&lt;Ihr-Domänenname.com&gt;  
postmaster@&lt;Ihr-Domänenname.com&gt;  

Sie sollten ähnlich wie im folgenden Beispiel nach wenigen Minuten eine E-Mail-Nachricht erhalten, in der Sie aufgefordert werden, die Anforderung zu genehmigen. Wenn Sie einen Spamfilter verwenden, fügen Sie admin@digicert.com der Positivliste hinzu. Sollten Sie nach 24 Stunden noch keine E-Mail erhalten haben, setzen Sie sich mit dem Microsoft-Support in Verbindung.

Wenn Sie auf den Genehmigungslink klicken, werden Sie an ein Onlineformular für die Genehmigung weitergeleitet. Befolgen Sie die Anweisungen im Formular. Sie haben zwei Möglichkeiten zur Genehmigung:

- Sie können alle zukünftigen Aufträge genehmigen, die über dasselbe Konto und für dieselbe Stammdomäne (beispielsweise „contoso.com“) getätigt werden. Dies empfiehlt sich, wenn Sie planen, zusätzliche benutzerdefinierte Domänen für die gleiche Stammdomäne hinzuzufügen.

- Sie können lediglich den spezifischen Hostnamen aus dieser Anforderung genehmigen. Für nachfolgende Anforderungen ist eine zusätzliche Genehmigung erforderlich.

Nach der Genehmigung führt DigiCert die Erstellung des Zertifikats für Ihren benutzerdefinierten Domänennamen durch. Das Zertifikat ist ein Jahr lang gültig und wird automatisch verlängert, bevor es abläuft.

## <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nach der Überprüfung des Domänennamens dauert es maximal 6 bis 8 Stunden, bis das HTTPS-Feature für die benutzerdefinierte Domäne aktiviert wird. Wenn der Prozess abgeschlossen ist, wird der HTTP-Status für benutzerdefinierte Domänen im Azure-Portal auf **Aktiviert** festgelegt, und die vier Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne ist jetzt bereit zur Verwendung von HTTPS.

### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Aktivieren von HTTPS. Nach der Aktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne vier Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden weitere Details zum jeweiligen Teilschritt angezeigt. Nicht alle diese Teilschritte werden vorkommen. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsschritt | Details zum Teilschritt des Vorgangs | 
| --- | --- |
| 1: Übermitteln der Anforderung | Übermitteln der Anforderung |
| | Die HTTPS-Anforderung wird übermittelt. |
| | Die HTTPS-Anforderung wurde erfolgreich übermittelt. |
| 2: Überprüfen der Domäne | Die Domäne wird automatisch überprüft, wenn der CNAME-Eintrag dem standardmäßigen Front-End-Host „.azurefd.net“ von Front Door zugeordnet ist. Andernfalls wird an die E-Mail-Adresse, die im Registrierungsdatensatz (WHOIS-Registrant) Ihrer Domäne angegeben ist, eine Überprüfungsanforderung gesendet. Überprüfen Sie die Domäne schnellstmöglich. |
| | Der Besitz der Domäne wurde erfolgreich bestätigt. |
| | Die Überprüfungsanforderung für den Domänenbesitz ist abgelaufen (Kunde hat wahrscheinlich nicht innerhalb von 6 Tagen reagiert). HTTPS wird für Ihre Domäne nicht aktiviert. * |
| | Die Überprüfungsanforderung für den Domänenbesitz wurde vom Kunden zurückgewiesen. HTTPS wird für Ihre Domäne nicht aktiviert. * |
| 3: Zertifikatbereitstellung | Die Zertifizierungsstelle stellt zurzeit das Zertifikat aus, das zum Aktivieren von HTTPS für Ihre Domäne erforderlich ist. |
| | Das Zertifikat wurde ausgegeben und wird derzeit in Front Door bereitgestellt. Dieser Vorgang kann bis zu einer Stunde dauern. |
| | Das Zertifikat wurde erfolgreich für Front Door bereitgestellt. |
| 4: Abschließen | HTTPS wurde in Ihrer Domäne erfolgreich aktiviert. |

\* Diese Meldung wird nur bei einem Fehler angezeigt. 

Sollte vor dem Übermitteln der Anforderung ein Fehler auftreten, wird die folgende Fehlermeldung angezeigt:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Bereinigen von Ressourcen – Deaktivieren von HTTPS

In den obigen Schritten haben Sie das HTTPS-Protokoll für Ihre benutzerdefinierte Domäne aktiviert. Falls Sie Ihre benutzerdefinierte Domäne nicht mehr mit HTTPS verwenden möchten, können Sie HTTPS mit diesen Schritten deaktivieren:

### <a name="disable-the-https-feature"></a>Deaktivieren des HTTPS-Features 

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer **Azure Front Door Service**-Konfiguration.

2. Klicken Sie in der Liste der Front-End-Hosts auf die benutzerdefinierte Domäne, für die Sie HTTPS deaktivieren möchten.

3. Klicken Sie auf **Deaktiviert**, um HTTPS zu deaktivieren, und klicken Sie dann auf **Speichern**.

### <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nachdem das HTTPS-Feature für die benutzerdefinierte Domäne deaktiviert wurde, dauert es maximal 6 bis 8 Stunden, bis die Änderung wirksam wird. Nach Abschluss des Prozesses wird der HTTP-Status für benutzerdefinierte Domänen im Azure-Portal auf **Deaktiviert** festgelegt, und die drei Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne kann HTTPS nicht mehr verwenden.

#### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Deaktivieren von HTTPS. Nach der Deaktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne drei Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden weitere Details zum jeweiligen Schritt angezeigt. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsstatus | Vorgangsdetails | 
| --- | --- |
| 1: Übermitteln der Anforderung | Ihre Anforderung wird übermittelt. |
| 2: Aufheben der Zertifikatbereitstellung | Das Zertifikat wird gelöscht. |
| 3: Abschließen | Das Zertifikat wurde gelöscht. |

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. *Wer ist der Zertifikatanbieter, und welche Art von Zertifikat wird verwendet?*

    Ein dediziertes/einzelnes Zertifikat von DigiCert wird für Ihre benutzerdefinierte Domäne verwendet. 

2. *Basiert TLS/SSL auf IP oder auf SNI?*

    Azure Front Door Service verwendet SNI TLS/SSL.

3. *Was passiert, wenn ich die Domänenüberprüfungs-E-Mail von DigiCert nicht erhalte?*

    Wenn Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne haben, der direkt auf Ihren Endpunkt-Hostnamen verweist (und wenn Sie den afdverify-Unterdomänennamen nicht verwenden), erhalten Sie keine Bestätigungs-E-Mail für die Domäne. Die Bestätigung erfolgt automatisch. Wenden Sie sich andernfalls an den Microsoft-Support, falls Sie nicht über einen CNAME-Eintrag verfügen und innerhalb von 24 Stunden keine E-Mail erhalten.

4. *Ist ein SAN-Zertifikat weniger sicher als ein dediziertes Zertifikat?*
    
    Ein SAN-Zertifikat bietet die gleichen Verschlüsselungs- und Sicherheitsstandards wie ein dediziertes Zertifikat. Zur Verbesserung der Serversicherheit verwenden alle ausgestellten SSL-Zertifikate SHA-256.

5. *Benötige ich einen CAA-Datensatz (Certificate Authority Authorization) bei meinem DNS-Anbieter?*

    Nein. Ein CAA-Datensatz ist momentan nicht erforderlich. Wenn Sie allerdings über einen solchen Datensatz verfügen, muss er DigiCert als gültige Zertifizierungsstelle enthalten.


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Azure Front Door Service-Konfiguration](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
