---
title: Tutorial – Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne | Microsoft-Dokumentation
description: In diesem Tutorial wird beschrieben, wie Sie HTTPS in Ihrer benutzerdefinierten Azure CDN-Endpunktdomäne aktivieren und deaktivieren.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/1/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 12cf4b029424bbbdb7449e6e1d04684ed485ef97
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779015"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Konfigurieren von HTTPS in einer benutzerdefinierten Azure CDN-Domäne

In diesem Tutorial erfahren Sie, wie Sie das HTTPS-Protokoll für eine benutzerdefinierte Domäne aktivieren, die einem Azure CDN-Endpunkt zugeordnet ist. Durch die Verwendung des HTTPS-Protokolls in Ihrer benutzerdefinierten Domäne (Beispiel: https:\//www.contoso.com) stellen Sie sicher, dass Ihre sensiblen Daten per TLS/SSL-Verschlüsselung sicher zugestellt werden, wenn diese über das Internet gesendet werden. Wenn Ihr Webbrowser über HTTPS eine Verbindung mit einer Website herstellt, überprüft er das Sicherheitszertifikat der Website, um sicherzustellen, dass es von einer legitimen Zertifizierungsstelle stammt. Dieser Prozess sorgt für Sicherheit und schützt Ihre Webanwendungen vor Angriffen.

Azure CDN unterstützt HTTPS standardmäßig für einen CDN-Endpunkt-Hostnamen. Wenn Sie also beispielsweise einen CDN-Endpunkt erstellen (z.B. „https:\//contoso.azureedge.net“), wird HTTPS automatisch aktiviert.  

Zu den wichtigsten Attributen des benutzerdefinierten HTTPS-Features zählen unter anderem folgende:

- Keine zusätzlichen Kosten: Zertifikatabruf und -verlängerung sind kostenlos, und für HTTPS-Datenverkehr fallen keine zusätzlichen Kosten an. Sie zahlen nur für den ausgehenden Datenverkehr des CDNs (nach GB).

- Unkomplizierte Aktivierung: Über das [Azure-Portal](https://portal.azure.com) ist die Bereitstellung mit einem einzelnen Klick möglich. Das Feature kann aber auch per REST-API oder mithilfe anderer Entwicklertools aktiviert werden.

- Umfassende Zertifikatverwaltung: Die gesamte Zertifikatbeschaffung und -verwaltung wird für Sie erledigt. Zertifikate werden automatisch bereitgestellt und vor Ablauf verlängert. Dadurch ist gewährleistet, dass keine Dienstunterbrechungen aufgrund eines abgelaufenen Zertifikats auftreten.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> - Aktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne
> - Verwenden eines CDN-verwalteten Zertifikats 
> - Verwenden Ihres eigenen Zertifikats
> - Überprüfen der Domäne
> - Deaktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Bevor Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst ein CDN-Profil und mindestens einen CDN-Endpunkt erstellen. Weitere Informationen finden Sie unter [Quickstart: Erstellen eines Azure CDN-Profils und -Endpunkts](cdn-create-new-endpoint.md).

Zusätzlich müssen Sie Ihrem CDN-Endpunkt eine benutzerdefinierte Azure CDN-Domäne zuordnen. Weitere Informationen finden Sie im [Tutorial: Hinzufügen einer benutzerdefinierten Domäne zum Azure CDN-Endpunkt](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> CDN-verwaltete Zertifikate sind für Stamm- oder Apex-Domänen nicht verfügbar. Wenn Ihre benutzerdefinierte Azure CDN-Domäne eine Stamm- oder Apex-Domäne ist, müssen Sie die Funktion „Bereitstellen eines eigenen Zertifikats“ verwenden. 
>

---

## <a name="tlsssl-certificates"></a>TLS-/SSL-Zertifikate
Sie müssen ein TLS-/SSL-Zertifikat verwenden, um das HTTPS-Protokoll für die sichere Übermittlung von Inhalten in einer benutzerdefinierten Azure CDN-Domäne zu aktivieren. Sie können ein von Azure CDN verwaltetes Zertifikat oder ein eigenes Zertifikat verwenden.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[Option 1 (Standard): Aktivieren von HTTPS mit einem CDN-verwalteten Zertifikat](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Bei Verwendung eines CDN-verwalteten Zertifikats kann das HTTPS-Feature mit nur wenigen Klicks aktiviert werden. Das Azure CDN übernimmt sämtliche Zertifikatverwaltungsaufgaben wie etwa Beschaffung und Verlängerung. Der Prozess wird umgehend nach der Aktivierung des Features gestartet. Wenn die benutzerdefinierte Domäne bereits dem CDN-Endpunkt zugeordnet ist, ist keine weitere Aktion erforderlich. Das Azure CDN durchläuft die Schritte und schließt Ihre Anforderung automatisch ab. Sollte Ihre benutzerdefinierte Domäne an anderer Stelle zugeordnet sein, müssen Sie per E-Mail bestätigen, dass Sie der Besitzer der Domäne sind.

Um HTTPS für eine benutzerdefinierte Domäne zu aktivieren, führen Sie die folgenden Schritte aus:

1. Rufen Sie das [Azure-Portal](https://portal.azure.com) auf, um ein von Ihrer Azure CDN-Instanz verwaltetes Zertifikat zu ermitteln. Suchen Sie nach **CDN-Profile** , und wählen Sie diese Option aus. 

2. Wählen Sie das Profil **Azure CDN Standard von Microsoft** , **Azure CDN Standard von Akamai** , **Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon** aus.

3. Wählen Sie in der Liste mit den CDN-Endpunkten den Endpunkt aus, der Ihre benutzerdefinierte Domäne enthält.

    ![Liste mit Endpunkten](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Die Seite **Endpunkt** wird angezeigt.

4. Wählen Sie in der Liste mit den benutzerdefinierten Domänen die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

    ![Screenshot, der die Seite „Benutzerdefinierte Domäne“ mit der Option „Eigenes Zertifikat verwenden“ zeigt.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Die Seite **Benutzerdefinierte Domäne** wird angezeigt.

5. Wählen Sie unter „Zertifikatverwaltungstyp“ die Option **CDN verwaltet** aus.

6. Klicken Sie auf **Ein** , um HTTPS zu aktivieren.

    ![HTTPS-Status benutzerdefinierter Domänen](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Fahren Sie mit [Domäne überprüfen](#validate-the-domain) fort.


# <a name="option-2-enable-https-with-your-own-certificate"></a>[Option 2: Aktivieren von HTTPS mit Ihrem eigenen Zertifikat](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Diese Option steht nur in den Profilen **Azure CDN von Microsoft** und **Azure CDN von Verizon** zur Verfügung. 
>
 
Sie können das HTTPS-Feature mit Ihrem eigenen Zertifikat aktivieren. Dabei erfolgt eine Integration in Azure Key Vault, was eine sichere Speicherung Ihrer Zertifikate ermöglicht. Azure CDN nutzt diesen sicheren Mechanismus zum Abrufen Ihres Zertifikats, und es sind einige zusätzliche Schritte erforderlich. Wenn Sie Ihr TLS-/SSL-Zertifikat erstellen, müssen Sie dafür eine zulässige Zertifizierungsstelle verwenden. Bei Verwendung einer unzulässigen Zertifizierungsstelle wird Ihre Anforderung abgelehnt. Eine Liste mit zulässigen Zertifizierungsstellen für die Aktivierung von benutzerdefiniertem HTTPS für Azure CDN finden Sie [hier](cdn-troubleshoot-allowed-ca.md). Für **Azure CDN von Verizon** wird jede gültige Zertifizierungsstelle akzeptiert. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Vorbereiten Ihres Azure Key Vault-Kontos und Ihres Zertifikats
 
1. Azure Key Vault: Sie benötigen ein aktives Azure Key Vault-Konto. Dieses muss zu dem gleichen Abonnement gehören wie das Azure CDN-Profil und die CDN-Endpunkte, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. Erstellen Sie bei Bedarf ein Azure Key Vault-Konto.
 
2. Azure Key Vault-Zertifikate: Wenn Sie bereits über ein Zertifikat verfügen, können Sie es direkt in Ihr Azure Key Vault-Konto hochladen. Alternativ können Sie direkt in Azure Key Vault ein neues Zertifikat über eine der Partnerzertifizierungsstellen mit Azure Key Vault-Integration erstellen. 

### <a name="register-azure-cdn"></a>Registrieren von Azure CDN

Registrieren Sie Azure CDN über PowerShell als App in Ihrem Azure Active Directory.

1. Installieren Sie bei Bedarf [Azure PowerShell](/powershell/azure/install-az-ps) auf Ihrem lokalen Computer.

2. Führen Sie in PowerShell den folgenden Befehl aus:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Registrieren von Azure CDN in PowerShell](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Gewähren von Zugriff auf Ihren Schlüsseltresor für Azure CDN
 
Gewähren Sie Azure CDN Berechtigungen für den Zugriff auf die Zertifikate (Geheimnisse) in Ihrem Azure Key Vault-Konto.

1. Klicken Sie in Ihrem Key Vault-Konto unter „EINSTELLUNGEN“ auf **Zugriffsrichtlinien** und anschließend auf **Neu hinzufügen** , um eine neue Richtlinie zu erstellen.

    ![Erstellen einer neuen Zugriffsrichtlinie](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. Suchen Sie unter **Prinzipal auswählen** nach **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** , und wählen Sie **Microsoft.Azure.Cdn** aus. Klicken Sie auf **Auswählen** .

    ![Zugriffsrichtlinieneinstellungen](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Wählen Sie **Zertifikatberechtigungen** aus, und aktivieren Sie dann die Kontrollkästchen für das **Abrufen** und **Auflisten** , um CDN diese Berechtigungen zu erteilen und das Abrufen und Auflisten der Zertifikate zu ermöglichen.

4. Wählen Sie **Berechtigungen für Geheimnis** aus, und aktivieren Sie dann die Kontrollkästchen für das **Abrufen** und **Auflisten** , um CDN diese Berechtigungen zu erteilen und das Abrufen und Auflisten der Geheimnisse zu ermöglichen.

5. Klicken Sie auf **OK** . 

    Das Azure CDN kann nun auf diesen Schlüsseltresor und auf die darin gespeicherten Zertifikate (Geheimnisse) zugreifen.
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Auswählen des bereitzustellenden Zertifikats für Azure CDN
 
1. Kehren Sie zum Azure CDN-Portal zurück, und wählen Sie das Profil und den CDN-Endpunkt aus, für die Sie benutzerdefiniertes HTTPS aktivieren möchten. 

2. Wählen Sie in der Liste mit den benutzerdefinierten Domänen die benutzerdefinierte Domäne aus, für die Sie HTTPS aktivieren möchten.

    Die Seite **Benutzerdefinierte Domäne** wird angezeigt.

3. Wählen Sie unter „Zertifikatverwaltungstyp“ die Option **Eigenes Zertifikat verwenden** aus. 

    ![Konfigurieren Ihres Zertifikats](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Wählen Sie einen Schlüsseltresor, ein Zertifikat (Geheimnis) und eine Zertifikatversion aus.

    Das Azure CDN listet folgende Informationen auf: 
    - Die Schlüsseltresorkonten für Ihre Abonnement-ID 
    - Die Zertifikate (Geheimnisse) unter dem ausgewählten Schlüsseltresor 
    - Die verfügbaren Zertifikatversionen 
 
5. Klicken Sie auf **Ein** , um HTTPS zu aktivieren.
  
6. Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort.

---

## <a name="validate-the-domain"></a>Überprüfen der Domäne

Wenn Sie bereits eine benutzerdefinierte Domäne verwenden, die Ihrem benutzerdefinierten Endpunkt mit einem CNAME-Eintrag zugeordnet ist, oder Sie Ihr eigenes Zertifikat verwenden, können Sie mit  
[Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt mit einem CNAME-Eintrag zugeordnet](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record) fortfahren. Fahren Sie andernfalls mit [Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt nicht zugeordnet](#custom-domain-is-not-mapped-to-your-cdn-endpoint) fort, falls der CNAME-Eintrag für Ihren Endpunkt nicht mehr vorhanden ist oder falls er die Unterdomäne „cdnverify“ enthält.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt mit einem CNAME-Eintrag zugeordnet

Nach dem Hinzufügen einer benutzerdefinierten Domäne zu Ihrem Endpunkt haben Sie einen CNAME-Eintrag in der DNS-Tabelle Ihrer Domänenregistrierungsstelle erstellt, um ihn Ihrem CDN-Endpunkt-Hostnamen zuzuordnen. Wenn dieser CNAME-Eintrag noch vorhanden ist und die cdnverify-Unterdomäne nicht enthält, wird er von der DigiCert-CA verwendet, um den Besitz Ihrer benutzerdefinierten Domäne automatisch zu überprüfen. 

Wenn Sie Ihr eigenes Zertifikat verwenden, ist keine Domänenüberprüfung erforderlich.

Ihr CNAME-Eintrag sollte im folgenden Format vorliegen, wobei *Name* Ihr benutzerdefinierter Domänenname und *Wert* Ihr CDN-Endpunkt-Hostname ist:

| Name            | type  | Wert                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

Weitere Informationen über CNAME-Einträge finden Sie unter [Erstellen Sie die CNAME-DNS-Einträge](./cdn-map-content-to-custom-domain.md).

Wenn Ihr CNAME-Eintrag im richtigen Format vorliegt, überprüft DigiCert automatisch Ihren benutzerdefinierten Domänennamen und erstellt ein dediziertes Zertifikat für Ihren Domänennamen. DigiCert sendet Ihnen keine Bestätigungs-E-Mail, und Sie müssen Ihre Anforderung nicht genehmigen. Das Zertifikat ist ein Jahr lang gültig und wird automatisch verlängert, bevor es abläuft. Fahren Sie mit [Warten auf die Weitergabe](#wait-for-propagation) fort. 

Die automatische Überprüfung dauert normalerweise einige Stunden. Öffnen Sie ein Supportticket, falls Ihre Domäne nicht innerhalb von 24 Stunden validiert wurde.

>[!NOTE]
>Wenn Sie über einen CAA-Datensatz (Certificate Authority Authorization) bei Ihrem DNS-Anbieter verfügen, muss dieser DigiCert als gültige Zertifizierungsstelle enthalten. Ein CAA-Datensatz ermöglicht es Domänenbesitzern, ihrem jeweiligen DNS-Anbieter mitzuteilen, welche Zertifizierungsstellen zum Ausstellen von Zertifikaten für ihre Domäne autorisiert sind. Wenn eine Zertifizierungsstelle einen Auftrag für ein Zertifikat für eine Domäne empfängt, für die ein CAA-Datensatz vorliegt und die Zertifizierungsstelle nicht als autorisierter Aussteller angegeben ist, darf sie das Zertifikat für die Domäne oder Unterdomäne nicht ausstellen. Informationen zum Verwalten von CAA-Einträgen finden Sie unter [Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/) (Verwalten von CAA-Einträgen). Ein Tool für CAA-Einträge finden Sie unter [CAA Record Helper](https://sslmate.com/caa/) (Hilfsprogramm für CAA-Einträge).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Benutzerdefinierte Domäne ist Ihrem CDN-Endpunkt nicht zugeordnet

>[!NOTE]
>Bei der Verwendung von **Azure CDN von Akamai** muss der folgende CNAME-Eintrag eingerichtet werden, um die automatisierte Domänenüberprüfung zu aktivieren: „_acme-challenge.&lt;Hostname der benutzerdefinierten Domäne&gt; -> CNAME -> &lt;Hostname der benutzerdefinierten Domäne&gt;.ak-acme-challenge.azureedge.net“

Wenn der CNAME-Eintrag die cdnverify-Unterdomäne enthält, folgen Sie den weiteren Anweisungen in diesem Schritt.

DigiCert sendet zur Überprüfung eine E-Mail an die folgenden E-Mail-Adressen. Vergewissern Sie sich, dass Sie die Bestätigung direkt über eine der folgenden Adressen vornehmen können:

admin@&lt;Ihr-Domänenname.com&gt;  
administrator@&lt;Ihr-Domänenname.com&gt;  
webmaster@&lt;Ihr-Domänenname.com&gt;  
hostmaster@&lt;Ihr-Domänenname.com&gt;  
postmaster@&lt;Ihr-Domänenname.com&gt;  

Sie sollten ähnlich wie im folgenden Beispiel nach wenigen Minuten eine E-Mail-Nachricht erhalten, in der Sie aufgefordert werden, die Anforderung zu genehmigen. Wenn Sie einen Spamfilter verwenden, fügen Sie verification@digicert.com der Zulassungsliste hinzu. Sollten Sie nach 24 Stunden noch keine E-Mail erhalten haben, setzen Sie sich mit dem Microsoft-Support in Verbindung.
    
![E-Mail zur Bestätigung der Domäne](./media/cdn-custom-ssl/domain-validation-email.png)

Wenn Sie auf den Genehmigungslink klicken, werden Sie auf das folgende Onlineformular für die Genehmigung weitergeleitet: 
    
![Formular zur Bestätigung der Domäne](./media/cdn-custom-ssl/domain-validation-form.png)

Befolgen Sie die Anweisungen im Formular. Sie haben zwei Möglichkeiten zur Genehmigung:

- Sie können alle zukünftigen Aufträge genehmigen, die über dasselbe Konto und für dieselbe Stammdomäne (beispielsweise „contoso.com“) getätigt werden. Dies empfiehlt sich, wenn Sie planen, zusätzliche benutzerdefinierte Domänen für die gleiche Stammdomäne hinzuzufügen.

- Sie können lediglich den spezifischen Hostnamen aus dieser Anforderung genehmigen. Für nachfolgende Anforderungen ist eine zusätzliche Genehmigung erforderlich.

Nach der Genehmigung führt DigiCert die Erstellung des Zertifikats für Ihren benutzerdefinierten Domänennamen durch. Das Zertifikat ist ein Jahr lang gültig und wird automatisch verlängert, bevor es abläuft.

## <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nach der Überprüfung des Domänennamens dauert es maximal 6 bis 8 Stunden, bis das HTTPS-Feature für die benutzerdefinierte Domäne aktiviert wird. Wenn der Prozess abgeschlossen ist, wird der HTTP-Status für benutzerdefinierte Domänen im Azure-Portal auf **Aktiviert** festgelegt, und die vier Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne ist jetzt bereit zur Verwendung von HTTPS.

![Dialogfeld „HTTPS aktivieren“](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Aktivieren von HTTPS. Nach der Aktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne vier Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden weitere Details zum jeweiligen Teilschritt angezeigt. Nicht alle diese Teilschritte werden vorkommen. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsschritt | Details zum Teilschritt des Vorgangs | 
| --- | --- |
| 1: Übermitteln der Anforderung | Übermitteln der Anforderung |
| | Die HTTPS-Anforderung wird übermittelt. |
| | Die HTTPS-Anforderung wurde erfolgreich übermittelt. |
| 2: Überprüfen der Domäne | Die Domäne wird automatisch überprüft, wenn sie per CNAME dem CDN-Endpunkt zugeordnet wird. Andernfalls wird an die E-Mail-Adresse, die im Registrierungsdatensatz (WHOIS-Registrant) Ihrer Domäne angegeben ist, eine Überprüfungsanforderung gesendet. Überprüfen Sie die Domäne schnellstmöglich. |
| | Der Besitz der Domäne wurde erfolgreich bestätigt. |
| | Die Überprüfungsanforderung für den Domänenbesitz ist abgelaufen (Kunde hat wahrscheinlich nicht innerhalb von 6 Tagen reagiert). HTTPS wird für Ihre Domäne nicht aktiviert. * |
| | Die Überprüfungsanforderung für den Domänenbesitz wurde vom Kunden zurückgewiesen. HTTPS wird für Ihre Domäne nicht aktiviert. * |
| 3: Zertifikatbereitstellung | Die Zertifizierungsstelle stellt zurzeit das Zertifikat aus, das zum Aktivieren von HTTPS für Ihre Domäne erforderlich ist. |
| | Das Zertifikat wurde ausgegeben und wird derzeit im CDN-Netzwerk bereitgestellt. Dies kann bis zu sechs Stunden dauern. |
| | Das Zertifikat wurde erfolgreich im CDN-Netzwerk bereitgestellt. |
| 4: Abschließen | HTTPS wurde in Ihrer Domäne erfolgreich aktiviert. |

\* Diese Meldung wird nur bei einem Fehler angezeigt. 

Sollte vor dem Übermitteln der Anforderung ein Fehler auftreten, wird die folgende Fehlermeldung angezeigt:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Bereinigen von Ressourcen – Deaktivieren von HTTPS

In den obigen Schritten haben Sie das HTTPS-Protokoll für Ihre benutzerdefinierte Domäne aktiviert. Falls Sie Ihre benutzerdefinierte Domäne nicht mehr mit HTTPS verwenden möchten, können Sie HTTPS mit diesen Schritten deaktivieren:

### <a name="disable-the-https-feature"></a>Deaktivieren des HTTPS-Features 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **CDN-Profile** , und wählen Sie den Eintrag aus. 

2. Wählen Sie das Profil **Azure CDN Standard von Microsoft** , **Azure CDN Standard von Verizon** oder **Azure CDN Premium von Verizon** aus.

3. Wählen Sie in der Liste mit den Endpunkten den Endpunkt aus, der Ihre benutzerdefinierte Domäne enthält.

4. Wählen Sie die benutzerdefinierte Domäne aus, für die Sie HTTPS deaktivieren möchten.

    ![Liste mit benutzerdefinierten Domänen](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. Klicken Sie auf **Aus** , um HTTPS zu deaktivieren, und wählen Sie dann **Anwenden** aus.

    ![Dialogfeld „HTTPS für benutzerdefinierte Domänen“](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Warten auf die Weitergabe

Nachdem das HTTPS-Feature für die benutzerdefinierte Domäne deaktiviert wurde, dauert es maximal 6 bis 8 Stunden, bis die Änderung wirksam wird. Nach Abschluss des Prozesses wird der HTTP-Status für benutzerdefinierte Domänen im Azure-Portal auf **Deaktiviert** festgelegt, und die drei Schritte im Dialogfeld der benutzerdefinierten Domäne werden als abgeschlossen markiert. Die benutzerdefinierte Domäne kann HTTPS nicht mehr verwenden.

![Dialogfeld „HTTPS deaktivieren“](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Vorgangsstatus

Die folgende Tabelle zeigt den Status des Vorgangs zum Deaktivieren von HTTPS. Nach der Deaktivierung von HTTPS werden im Dialogfeld der benutzerdefinierten Domäne drei Schritte angezeigt. Beim Durchlaufen der einzelnen Schritte werden weitere Details zum jeweiligen Schritt angezeigt. Nachdem ein Schritt erfolgreich abgeschlossen wurde, wird neben diesem ein grünes Häkchen angezeigt. 

| Vorgangsstatus | Vorgangsdetails | 
| --- | --- |
| 1: Übermitteln der Anforderung | Ihre Anforderung wird übermittelt. |
| 2: Aufheben der Zertifikatbereitstellung | Das Zertifikat wird gelöscht. |
| 3: Abschließen | Das Zertifikat wurde gelöscht. |

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

1. *Wer ist der Zertifikatanbieter, und welche Art von Zertifikat wird verwendet?*

    Sowohl für **Azure CDN von Verizon** als auch für **Azure CDN von Microsoft** wird ein dediziertes/einzelnes Zertifikat, das von Digicert bereitgestellt wird, für Ihre benutzerdefinierte Domäne verwendet. 

2. *Basiert TLS/SSL auf IP oder auf SNI?*

    Sowohl für **Azure CDN von Verizon** als auch für **Azure CDN Standard von Microsoft** wird SNI-basiertes TLS/SSL verwendet.

3. *Was passiert, wenn ich die Domänenüberprüfungs-E-Mail von DigiCert nicht erhalte?*

    Wenn Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne haben, der direkt auf Ihren Endpunkt-Hostnamen verweist (und Sie verwenden nicht den cdnverify-Unterdomänennamen), erhalten Sie keine Bestätigungs-E-Mail für die Domäne. Die Bestätigung erfolgt automatisch. Wenden Sie sich andernfalls an den Microsoft-Support, falls Sie nicht über einen CNAME-Eintrag verfügen und innerhalb von 24 Stunden keine E-Mail erhalten.

4. *Ist ein SAN-Zertifikat weniger sicher als ein dediziertes Zertifikat?*
    
    Ein SAN-Zertifikat bietet die gleichen Verschlüsselungs- und Sicherheitsstandards wie ein dediziertes Zertifikat. Zur Verbesserung der Serversicherheit verwenden alle ausgestellten TLS-/SSL-Zertifikate SHA-256.

5. *Benötige ich einen CAA-Datensatz (Certificate Authority Authorization) bei meinem DNS-Anbieter?*

    Nein. Ein CAA-Datensatz ist momentan nicht erforderlich. Wenn Sie allerdings über einen solchen Datensatz verfügen, muss er DigiCert als gültige Zertifizierungsstelle enthalten.

6. *Am 20. Juni 2018 wurde für Azure CDN von Verizon mit der standardmäßigen Nutzung eines dedizierten Zertifikats mit SNI-basiertem TLS/SSL begonnen. Was passiert mit meinen vorhandenen benutzerdefinierten Domänen, für die ein Zertifikat vom Typ „Alternativer Antragstellername“ (Subject Alternative Names, SAN) und IP-basiertes TLS/SSL verwendet wird?*

    Ihre vorhandenen Domänen werden in den kommenden Monaten nach und nach zur Verwendung eines einzelnen Zertifikats migriert, wenn von Microsoft analysiert wird, dass nur SNI-Clientanforderungen für Ihre Anwendung erfolgen. Falls Microsoft erkennt, dass für Ihre Anwendung auch einige andere Anforderungen als SNI-Clientanforderungen durchgeführt werden, wird für Ihre Domänen weiterhin das SAN-Zertifikat mit IP-basiertem TLS/SSL verwendet. Auf keinen Fall kommt es zu Unterbrechungen Ihres Diensts oder der Unterstützung Ihrer Clientanforderungen – unabhängig davon, ob es sich dabei um SNI-basierte oder nicht SNI-basierte Anforderungen handelt.

7. *Wie werden eigene Zertifikate verlängert?*

    Um sicherzustellen, dass ein neueres Zertifikat in der PoP-Infrastruktur bereitgestellt wird, laden Sie einfach Ihr neues Zertifikat in Azure Key Vault hoch, und wählen Sie anschließend in Azure CDN in Ihren TLS-/SSL-Einstellungen die aktuelle Zertifikatversion aus. Klicken Sie anschließend auf „Speichern“. Azure CDN wird dann Ihr neues aktualisiertes Zertifikat verteilen. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> - Aktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne
> - Verwenden eines CDN-verwalteten Zertifikats 
> - Verwenden Ihres eigenen Zertifikats
> - Validieren der Domäne
> - Deaktivieren des HTTPS-Protokolls für Ihre benutzerdefinierte Domäne

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie das Caching auf Ihrem CDN-Endpunkt konfigurieren.

> [!div class="nextstepaction"]
> [Tutorial: Festlegen der Azure CDN-Cacheregeln](cdn-caching-rules-tutorial.md)