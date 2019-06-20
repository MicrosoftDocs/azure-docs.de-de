---
title: 'Problembehandlung bei Domänen- und SSL-Zertifikaten: Azure App Service | Microsoft-Dokumentation'
description: Problembehandlung bei Domänen- und SSL-Zertifikaten in Azure App Service
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: c0584a69349c2785b5b6bce1d17c023c95b36151
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66136176"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-app-service"></a>Problembehandlung bei Domänen- und SSL-Zertifikaten in Azure App Service

In diesem Artikel werden häufige Probleme aufgelistet, die beim Konfigurieren einer Domäne oder eines SSL-Zertifikats für Ihre Web-Apps in Azure App Service auftreten können. Darüber hinaus werden die möglichen Ursachen und Lösungen für diese Probleme beschrieben.

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie die Option **Support erhalten**.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="certificate-problems"></a>Probleme mit Zertifikaten

### <a name="you-cant-add-an-ssl-certificate-binding-to-an-app"></a>Sie können keine SSL-Zertifikatbindung zu einer App hinzufügen 

#### <a name="symptom"></a>Symptom

Wenn Sie eine SSL-Bindung hinzufügen, erhalten Sie die folgende Fehlermeldung:

„Fehler beim Hinzufügen der SSL-Bindung. Das Zertifikat für den vorhandenen VIP kann nicht festgelegt werden, da ein anderer VIP dieses Zertifikat bereits verwendet.“

#### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn Sie App-übergreifend mehrere IP-basierte SSL-Bindungen für dieselbe IP-Adresse verwenden. Beispiel: App A verfügt über eine IP-basierte SSL-Bindung mit einem alten Zertifikat. App B verwendet für dieselbe IP-Adresse eine IP-basiertes SSL-Bindung mit einem neuen Zertifikat. Wenn Sie die SSL-Bindung der App mit dem neuen Zertifikat aktualisieren, kommt es zu diesem Fehler, weil dieselbe IP-Adresse für eine andere App verwendet wird. 

#### <a name="solution"></a>Lösung 

Verwenden Sie zur Problembehebung eine der folgenden Methoden:

- Löschen Sie die IP-basierte SSL-Bindung für die App, die das alte Zertifikat verwendet. 
- Erstellen Sie eine neue IP-basierte SSL-Bindung, die das neue Zertifikat verwendet.

### <a name="you-cant-delete-a-certificate"></a>Sie können ein Zertifikat nicht löschen. 

#### <a name="symptom"></a>Symptom

Wenn Sie versuchen, ein Zertifikat zu löschen, erhalten Sie die folgende Fehlermeldung:

„Das Zertifikat kann nicht gelöscht werden, da es derzeit in einer SSL-Bindung verwendet wird. Die SSL-Bindung muss entfernt werden, bevor Sie das Zertifikat löschen können.“

#### <a name="cause"></a>Ursache

Dieses Problem kann auftreten, wenn eine weitere App das Zertifikat verwendet.

#### <a name="solution"></a>Lösung

Entfernen Sie die SSL-Bindung für dieses Zertifikat aus den Apps. Versuchen Sie anschließend, das Zertifikat zu löschen. Wenn Sie das Zertifikat immer noch nicht löschen können, löschen Sie den Cache Ihres Internet-Browsers, und öffnen Sie das Azure-Portal erneut in einem neuen Browserfenster. Versuchen Sie anschließend, das Zertifikat zu löschen.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Sie können ein App Service-Zertifikat nicht erwerben. 

#### <a name="symptom"></a>Symptom
Sie können über das Azure-Portal kein [Azure App Service-Zertifikat](./web-sites-purchase-ssl-web-site.md) erwerben.

#### <a name="cause-and-solution"></a>Ursache und Lösung
Dieses Problem kann aus einem der folgenden Gründe auftreten:

- Der App Service-Plan ist „Free“ oder „Shared“. In diesen Tarifen wird SSL nicht unterstützt. 

    **Lösung**: Führen Sie ein Upgrade des App Service-Plans der App auf den Tarif „Standard“ durch.

- Für das Abonnement ist keine gültige Kreditkarte angegeben.

    **Lösung**: Fügen Sie Ihrem Abonnement eine gültige Kreditkarte hinzu. 

- Der Erwerb eines App Service-Zertifikats wird vom Abonnementangebot (z. B. Microsoft Student) nicht unterstützt.  

    **Lösung**: Upgraden Sie Ihr Abonnement. 

- Für das Abonnement wurde die Obergrenze von Käufen erreicht, die für ein Abonnement zulässig sind.

    **Lösung**: Für App Service-Zertifikate gilt bei Abonnements des Typs „Nutzungsbasierte Bezahlung“ und „Enterprise Agreement“ ein Limit von zehn Zertifikatskäufen. Bei anderen Abonnementtypen sind nur drei (3) Käufe zulässig. Wenden Sie sich an den [Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um dieses Limit zu erhöhen.
- Das App Service-Zertifikat wurde als betrugsverdächtig gekennzeichnet. Sie haben die folgende Fehlermeldung erhalten: „Ihr Zertifikat wurde als betrugsverdächtig gekennzeichnet. Die Anforderung wird aktuell geprüft. Wenn das Zertifikat nicht innerhalb von 24 Stunden verwendbar ist, wenden Sie sich an den Azure-Support.“

    **Lösung**: Wenn das Zertifikat als betrugsverdächtig gekennzeichnet und das Problem nach 24 Stunden noch nicht behoben ist, führen Sie die folgenden Schritte aus:

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
    2. Wechseln Sie zu **App Service-Zertifikate**, und wählen Sie das Zertifikat aus.
    3. Wählen Sie **Zertifikatkonfiguration** > **Schritt 2: Überprüfen** > **Domänenüberprüfung** aus. In diesem Schritt wird eine E-Mail-Nachricht an den Azure-Zertifikatanbieter gesendet mit der Bitte, das Problem zu beheben.

## <a name="custom-domain-problems"></a>Probleme mit benutzerdefinierten Domänen

### <a name="a-custom-domain-returns-a-404-error"></a>Eine benutzerdefinierte Domäne gibt einen 404-Fehler zurück. 

#### <a name="symptom"></a>Symptom

Wenn Sie mithilfe des benutzerdefinierten Domänennamens zur Website navigieren, erhalten Sie die folgende Fehlermeldung:

„Fehler 404 – Web-App wurde nicht gefunden.“

#### <a name="cause-and-solution"></a>Ursache und Lösung

**Ursache 1** 

Der benutzerdefinierten Domäne, die Sie konfiguriert haben, fehlt ein CNAME- oder ein A-Eintrag. 

**Lösung für Ursache 1**

- Wenn Sie einen A-Eintrag hinzugefügt haben, stellen Sie sicher, dass auch ein TXT-Eintrag hinzugefügt wird. Weitere Informationen finden Sie unter [Erstellen des A-Eintrags](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Wenn Sie die Stammdomäne für Ihre App nicht verwenden müssen, empfiehlt es sich, dass Sie anstelle eines A-Eintrags einen CNAME-Eintrag verwenden.
- Verwenden Sie nicht gleichzeitig einen CNAME-Eintrag und einen A-Eintrag für dieselbe Domäne. Dieses Problem kann einen Konflikt verursachen und verhindern, dass die Domäne aufgelöst wird. 

**Ursache 2** 

Im Internet-Browser ist möglicherweise immer noch die alte IP-Adresse für Ihre Domäne im Cache gespeichert. 

**Lösung für Ursache 2**

Löschen Sie den Browser-Cache. Bei Windows-Geräten können Sie den Befehl `ipconfig /flushdns` ausführen. Überprüfen Sie mithilfe von [WhatsmyDNS.net](https://www.whatsmydns.net/), ob Ihre Domäne auf die IP-Adresse der App zeigt. 

### <a name="you-cant-add-a-subdomain"></a>Sie können keine Unterdomäne hinzufügen. 

#### <a name="symptom"></a>Symptom

Sie können einer App keinen neuen Hostnamen hinzufügen, um eine Unterdomäne zuzuweisen.

#### <a name="solution"></a>Lösung

- Lassen Sie Ihren Abonnementadministrator überprüfen, ob Sie Berechtigungen zum Hinzufügen von Hostnamen zur App haben.
- Wenn Sie weitere Unterdomänen benötigen, empfiehlt es sich, dass Sie für das Domänenhosting zu Azure Domain Name Service (DNS) wechseln. Wenn Sie Azure DNS verwenden, können Sie Ihrer App 500 Hostnamen hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen einer Unterdomäne](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).

### <a name="dns-cant-be-resolved"></a>DNS kann nicht aufgelöst werden

#### <a name="symptom"></a>Symptom

Sie haben die folgende Fehlermeldung erhalten:

„Der DNS-Eintrag konnte nicht gefunden werden.“

#### <a name="cause"></a>Ursache
Dieses Problem tritt aus einem der folgenden Gründe auf:

- Die Gültigkeitsdauer (Time to Live, TTL) ist noch nicht abgelaufen. Überprüfen Sie die DNS-Konfiguration für Ihre Domäne, um den TTL-Wert zu bestimmen, und warten Sie dann bis zum Ablauf der Gültigkeitsdauer.
- Die DNS-Konfiguration ist fehlerhaft.

#### <a name="solution"></a>Lösung
- Warten Sie 48 Stunden, bis sich dieses Problem von selbst löst.
- Wenn Sie die Einstellung für die Gültigkeitsdauer (TTL) in Ihrer DNS-Konfiguration ändern können, ändern Sie den Wert auf 5 Minuten, um festzustellen, ob das Problem dadurch behoben wird.
- Überprüfen Sie mithilfe von [WhatsmyDNS.net](https://www.whatsmydns.net/), ob Ihre Domäne auf die IP-Adresse der App zeigt. Wenn dies nicht der Fall ist, konfigurieren Sie den A-Eintrag mit der richtigen IP-Adresse der App.

### <a name="you-need-to-restore-a-deleted-domain"></a>Sie müssen eine gelöschte Domäne wiederherstellen 

#### <a name="symptom"></a>Symptom
Die Domäne wird nicht mehr im Azure-Portal angezeigt.

#### <a name="cause"></a>Ursache 
Die Besitzer des Abonnements hat die Domäne möglicherweise versehentlich gelöscht.

#### <a name="solution"></a>Lösung
Wurde Ihre Domäne vor weniger als sieben Tagen gelöscht, wurde der Löschvorgang für die Domäne noch nicht gestartet. In diesem Fall können Sie dieselbe Domäne erneut im Azure-Portal unter demselben Abonnement erwerben. (Achten Sie darauf, dass Sie den genauen Domänennamen in das Suchfeld eingeben.) Diese Domäne wird Ihnen nicht erneut in Rechnung gestellt. Wenn die Domäne vor mehr als sieben Tagen gelöscht wurde, wenden Sie sich an den [Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um Hilfe beim Wiederherstellen der Domäne zu erhalten.

## <a name="domain-problems"></a>Probleme mit Domänen

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Sie haben ein SSL-Zertifikat für die falsche Domäne erworben.

#### <a name="symptom"></a>Symptom

Sie haben ein App Service-Zertifikat für die falsche Domäne erworben. Sie können das Zertifikat nicht so aktualisieren, dass die richtige Domäne verwendet wird.

#### <a name="solution"></a>Lösung

Löschen Sie dieses Zertifikat, und erwerben Sie anschließend ein neues Zertifikat.

Wenn sich das aktuelle Zertifikat, in dem die falsche Domäne verwendet wird, im Status „Ausgestellt“ befindet, wird Ihnen dieses Zertifikat auch in Rechnung gestellt. App Service-Zertifikate sind nicht erstattbar. Sie können sich jedoch an den [Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) wenden und prüfen, ob es andere Optionen gibt. 

### <a name="an-app-service-certificate-was-renewed-but-the-app-shows-the-old-certificate"></a>Ein App Service-Zertifikat wurde erneuert, aber in der App wird das alte Zertifikat angezeigt 

#### <a name="symptom"></a>Symptom

Das App Service-Zertifikat wurde erneuert, aber die App, die das App Service-Zertifikat verwendet, nutzt weiterhin das alte Zertifikat. Darüber hinaus haben Sie eine Warnmeldung erhalten, dass das HTTPS-Protokoll erforderlich ist.

#### <a name="cause"></a>Ursache 
Azure App Service führt alle acht Stunden einen Hintergrundauftrag aus und synchronisiert die Zertifikatressource, wenn Änderungen vorgenommen wurden. Wenn Sie ein Zertifikat aktualisieren oder wechseln, ruft die Anwendung manchmal weiterhin das alte Zertifikat und nicht das neu aktualisierte Zertifikat ab. Dies liegt daran, dass der Auftrag zum Synchronisieren der Zertifikatsressource noch nicht ausgeführt wurde. 
 
#### <a name="solution"></a>Lösung

Sie können eine Synchronisierung des Zertifikats erzwingen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie **App Service-Zertifikate** aus, und wählen Sie dann das Zertifikat aus.
2. Wählen Sie **Erstellung neuer Schlüssel und Synchronisierung** aus, und wählen Sie dann **Synchronisieren** aus. Die Synchronisierung nimmt einige Zeit in Anspruch. 
3. Nach Abschluss der Synchronisierung wird die folgende Benachrichtigung angezeigt: „Alle Ressourcen wurden erfolgreich mit dem neuesten Zertifikat aktualisiert.“

### <a name="domain-verification-is-not-working"></a>Die Domänenüberprüfung funktioniert nicht 

#### <a name="symptom"></a>Symptom 
Für das App Service-Zertifikat ist zuerst eine Überprüfung der Domäne erforderlich, bevor das Zertifikat verwendet werden kann. Wenn Sie **Überprüfen** auswählen, schlägt der Vorgang fehl.

#### <a name="solution"></a>Lösung
Überprüfen Sie Ihre Domäne manuell durch Hinzufügen eines TXT-Eintrags:
 
1.  Wechseln Sie zum DNS-Anbieter (Domain Name Service), der Ihre Domäne (Domänennamen) hostet.
2.  Fügen Sie einen TXT-Eintrag für Ihre Domäne hinzu, in dem der Wert des Domänentokens verwendet wird, das im Azure-Portal angezeigt wird. 

Warten Sie einige Minuten, bis die DNS-Daten weitergegeben wurden, und wählen Sie dann die Schaltfläche **Aktualisieren** aus, um die Überprüfung auszulösen. 

Als Alternative können Sie die HTML-Webseitenmethoden verwenden, um Ihre Domäne manuell zu überprüfen. Diese Methode ermöglicht es der Zertifizierungsstelle, den Domänenbesitz für die Domäne zu bestätigen, für die das Zertifikat ausgestellt ist.

1.  Erstellen Sie eine HTML-Datei namens „{Domänenüberprüfungstoken}.html“. Der Inhalt dieser Datei muss der Wert des Domänenüberprüfungstokens sein.
3.  Laden Sie diese Datei in das Stammverzeichnis des Webservers hoch, der Ihre Domäne hostet.
4.  Wählen Sie **Aktualisieren** aus, um den Status des Zertifikats zu prüfen. Es kann einige Minuten dauern, bis die Überprüfung abgeschlossen ist.

Beispiel: Sie erwerben ein Standardzertifikat für azure.com mit dem Domänenüberprüfungstoken „1234abcd“. Eine Webanforderung, die für https://azure.com/1234abcd.html gestellt wird, sollte „1234abcd“ zurückgeben. 

> [!IMPORTANT]
> Bei einer Zertifikatbestellung muss der Domänenüberprüfungsvorgang innerhalb von 15 Tagen abgeschlossen sein. Nach 15 Tagen wird das Zertifikat von der Zertifizierungsstelle verweigert, und das Zertifikat wird Ihnen nicht in Rechnung gestellt. Löschen Sie in diesem Fall das Zertifikat, und versuchen Sie es erneut.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Sie können keine Domäne erwerben.

#### <a name="symptom"></a>Symptom
Sie können im Azure-Portal keine App Service-Domäne erwerben.

#### <a name="cause-and-solution"></a>Ursache und Lösung

Dieses Problem tritt aus einem der folgenden Gründe auf:

- Es ist keine Kreditkarte für das Azure-Abonnement angegeben, oder die Kreditkarte ist ungültig.

    **Lösung**: Fügen Sie Ihrem Abonnement eine gültige Kreditkarte hinzu.

- Sie sind nicht der Besitzer des Abonnements, sodass Sie nicht berechtigt sind, eine Domäne zu erwerben.

    **Lösung**: [Weisen Sie Ihrem Konto die Rolle „Besitzer“](../role-based-access-control/role-assignments-portal.md) zu. Oder wenden Sie sich an den Abonnementadministrator, um die Berechtigung zum Erwerben einer Domäne zu erhalten.
- Sie haben das Limit für den Erwerb von Domänen für Ihr Abonnement erreicht. Das aktuelle Limit beläuft sich auf 20 Domänenkäufe.

    **Lösung**: Wenden Sie sich an den [Azure-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), um eine Erhöhung des Limits anzufordern.
- Der Kauf einer App Service-Domäne wird von Ihrem Azure-Abonnementtyp nicht unterstützt.

    **Lösung**: Führen Sie ein Upgrade für Ihr Azure-Abonnement auf einen anderen Abonnementtyp durch, z. B. auf ein Abonnement mit nutzungsbasierter Zahlung.

### <a name="you-cant-add-a-host-name-to-an-app"></a>Sie können einer App keinen Hostnamen hinzufügen 

#### <a name="symptom"></a>Symptom

Wenn Sie einen Hostnamen hinzufügen, schlägt die Domänenvalidierung und -überprüfung fehl.

#### <a name="cause"></a>Ursache 

Dieses Problem tritt aus einem der folgenden Gründe auf:

- Sie haben keine Berechtigung zum Hinzufügen eines Hostnamens.

    **Lösung**: Bitten Sie den Abonnementadministrator, Ihnen die Berechtigung zum Hinzufügen eines Hostnamens zu erteilen.
- Der Besitz der Domäne konnte nicht überprüft werden.

    **Lösung**: Vergewissern Sie sich, dass Ihr CNAME- oder A-Eintrag richtig konfiguriert ist. Erstellen Sie einen CNAME-Eintrag oder einen A-Eintrag, um der App eine benutzerdefinierte Domäne zuzuordnen. Wenn Sie eine Stammdomäne verwenden möchten, müssen Sie einen A- und einen TXT-Eintrag verwenden:

    |Eintragstyp|Host|Verweist auf|
    |------|------|-----|
    |Eine Datei|@|IP-Adresse für App|
    |TXT|@|`<app-name>.azurewebsites.net`|
    |CNAME|www|`<app-name>.azurewebsites.net`|

## <a name="faq"></a>Häufig gestellte Fragen

**Muss ich meine benutzerdefinierte Domäne nach dem Kauf für meine Website konfigurieren?**

Wenn Sie eine Domäne über das Azure-Portal erwerben, wird die App Service-Anwendung automatisch so konfiguriert, dass die benutzerdefinierte Domäne verwendet wird. Zusätzliche Schritte sind nicht erforderlich. Weitere Informationen finden Sie im Video [Azure App Service Self Help: Add a Custom Domain Name (Selbsthilfe für Azure App Service: Hinzufügen eines benutzerdefinierten Domänennamens)](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) auf Channel 9.

**Kann ich eine Domäne, die ich über das Azure-Portal erworben habe, so nutzen, dass sie auf eine Azure-VM verweist?**

Ja, Sie können die Domäne so einrichten, dass sie auf eine VM verweist. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../dns/dns-custom-domain.md).

**Wird meine Domäne von GoDaddy oder Azure DNS gehostet?**

App Service-Domänen verwenden GoDaddy zur Domänenregistrierung und Azure DNS zum Hosten der Domänen. 

**Die automatische Verlängerung ist aktiviert. Trotzdem habe ich per E-Mail eine Verlängerungsbenachrichtigung für meine Domäne erhalten. Wie soll ich vorgehen?**

Wenn die automatische Verlängerung aktiviert ist, müssen Sie keine Maßnahmen ergreifen. In der Benachrichtigungs-E-Mail werden Sie darüber informiert, dass die Domäne in Kürze abläuft und manuell verlängert werden muss, falls die automatische Verlängerung nicht aktiviert ist.

**Fallen Gebühren dafür an, dass Azure DNS meine Domäne hostet?**

Beim Erwerb einer Domäne umfassen die Anschaffungskosten nur die Domänenregistrierung. Zusätzlich fallen für Azure DNS Gebühren an, deren Höhe von Ihrem Nutzungsverhalten abhängig ist. Weitere Informationen finden Sie unter [Azure DNS – Preise](https://azure.microsoft.com/pricing/details/dns/).

**Ich habe meine Domäne über das Azure-Portal erworben und möchte nun Azure DNS anstelle von GoDaddy für das Hosting verwenden. Wie gehe ich dazu vor?**

Eine Migration zu Azure DNS ist für das Hosting nicht zwingend erforderlich. Wenn Sie sich für diesen Schritt entscheiden, finden Sie im Bereich zur Domänenverwaltung im Azure-Portal Informationen zu den Schritten, die für einen Wechsel zu Azure DNS erforderlich sind. Wenn Sie die Domäne über App Service erworben haben, ist die Migration von GoDaddy zu Azure DNS insgesamt unkompliziert.

**Ich möchte eine Domäne über die App Service-Domäne erwerben. Kann ich diese auf GoDaddy und nicht auf Azure DNS hosten?**

Seit dem 24. Juli 2017 werden App Service-Domänen, die über das Azure-Portal erworben wurden, in Azure DNS gehostet. Wenn Sie einen anderen Hostinganbieter verwenden möchten, müssen Sie auf dessen Website nach einer Domänenhostinglösung suchen.

**Fallen Kosten an, wenn ich für meine Domäne die Datenschutzfunktion verwende?**

Wenn Sie eine Domäne über das Azure-Portal erwerben, können Sie die Datenschutzfunktion kostenlos hinzufügen. Dies ist einer der Vorteile, wenn Sie Ihre Domäne über Azure App Service erwerben.

**Gibt es eine Geld-zurück-Garantie für den Fall, dass ich meine Domäne nicht mehr verwenden möchte?**

Nach dem Erwerb einer Domäne fallen fünf Tage lang keine Gebühren an. In diesem Zeitraum können Sie sich gegen die Nutzung der Domäne entscheiden. Diese Regelung gilt jedoch nicht für .uk-Domänen. Nach dem Erwerb einer solchen Domäne fallen sofort Gebühren an, die nicht rückerstattet werden können.

**Kann ich die Domäne für eine andere Azure App Service-App in meinem Abonnement verwenden?**

Ja. Wenn Sie im Azure-Portal das Blatt „Benutzerdefinierte Domänen und SSL“ aufrufen, werden die von Ihnen erworbenen Domänen angezeigt. Sie können Ihre App so konfigurieren, dass eine dieser Domänen verwendet wird.

**Kann ich eine Domäne aus einem Abonnement in ein anderes übertragen?**

Sie können mit dem PowerShell-Cmdlet [Move-AzResource](https://docs.microsoft.com/powershell/module/az.Resources/Move-azResource) eine Domäne in ein anderes Abonnement bzw. in eine andere Ressourcengruppe übertragen.

**Wie kann ich meine benutzerdefinierte Domäne verwalten, falls ich aktuell nicht über eine Azure App Service-App verfüge?**

Sie können Ihre Domäne auch ohne eine App Service-Web-App verwalten. Die Domäne kann für Azure-Dienste wie beispielsweise Virtual Machines oder Storage verwendet werden. Wenn Sie die Domäne für App Service-Web-Apps nutzen möchten, müssen Sie eine Web-App angeben, die nicht Teil des Free-App Service-Plans ist, um die Domäne an Ihre Web-App zu binden.

**Kann ich eine Web-App mit einer benutzerdefinierten Domäne in anderes Abonnement oder von einer App Service-Umgebung v1 in eine App Service-Umgebung v2 übertragen?**

Ja, können Sie Ihre Web-App aus einem Abonnement in ein anderes übertragen. Führen Sie dazu die Schritte im Leitfaden [Verschieben von Ressourcen in Azure](../azure-resource-manager/resource-group-move-resources.md) aus. Beim Übertragen einer Web-App müssen einige Einschränkungen berücksichtigt werden. Weitere Informationen finden Sie unter [Einschränkungen beim Verschieben von App Service-Ressourcen](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations
).

Nach dem Übertragen der Web-App sollten die Hostnamensbindungen der Domänen innerhalb der Einstellung für die benutzerdefinierten Domänen beibehalten werden. Zum Konfigurieren der Hostnamensbindungen sind keine zusätzlichen Schritte erforderlich.
