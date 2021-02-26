---
title: Verwenden und Verwalten einer Azure App Service-Umgebung
description: Informationen zum Erstellen, Veröffentlichen und Skalieren von Apps in einer App Service-Umgebung. In diesem Artikel finden Sie alle gängigen Aufgaben.
author: ccompy
ms.assetid: a22450c4-9b8b-41d4-9568-c4646f4cf66b
ms.topic: article
ms.date: 9/22/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: e8cc42500b9d8bfc507683b5cc96c7b12f95909b
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548453"
---
# <a name="use-an-app-service-environment"></a>Verwenden einer App Service-Umgebung

Eine App Service-Umgebung (ASE) ist eine Bereitstellung des Azure App Service in einem Subnetz in der Azure Virtual Network-Instanz eines Kunden. Eine ASE besteht aus den folgenden Komponenten:

- **Front-Ends**: Der Ort, an dem HTTP oder HTTPS in einer App Service-Umgebung endet.
- **Worker**: Die Ressourcen, die Ihre Apps hosten.
- **Datenbank**: Enthält Informationen, durch die die Umgebung definiert wird.
- **Storage**: Wird zum Hosten der vom Kunden veröffentlichten Apps verwendet.

Sie können eine ASE mit einer externen oder internen virtuellen IP (VIP) für den App-Zugriff bereitstellen. Eine Bereitstellung mit einer externen VIP wird im Allgemeinen als *externe ASE* bezeichnet. Eine Bereitstellung mit einer internen VIP wird als *ILB-ASE* bezeichnet, da ein interner Load Balancer (ILB) verwendet wird. Weitere Informationen zur ILB-ASE finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE].

## <a name="create-an-app-in-an-ase"></a>Erstellen einer App in einer ASE

Um eine App in einer ASE zu erstellen, verwenden Sie denselben Prozess wie bei der normalen Erstellung einer App, jedoch mit einigen kleinen Unterschieden. Beim Erstellen eines neuen App Service-Plans gilt:

- Anstatt einen geografischen Standort auszuwählen, an dem Sie Ihre App bereitstellen, wählen Sie eine ASE als Ihren Standort aus.
- Alle in einer ASE erstellten App Service-Pläne können nur den Tarif „Isoliert“ aufweisen.

Wenn Sie über keine ASE verfügen, können Sie eine erstellen. Folgen Sie hierzu den Anweisungen in [Erstellen einer App Service-Umgebung][MakeExternalASE].

So erstellen Sie eine App in einer ASE

1. Wählen Sie **Ressource erstellen** > **Web + Mobil** > **Web-Apps** aus.

1. Geben Sie einen Namen für die App ein. Wenn Sie bereits einen App Service-Plan in einer ASE ausgewählt haben, entspricht der Domänenname der App dem Domänenname der ASE:

    ![Namensauswahl für Apps][1]

1. Wählen Sie ein Abonnement aus.

1. Geben Sie einen Namen für eine neue Ressourcengruppe ein, oder wählen Sie **Vorhandene verwenden** aus, und wählen Sie in der Dropdownliste eine Ressourcengruppe aus.

1. Wählen Sie Ihr Betriebssystem aus.

1. Wählen Sie einen in Ihrer ASE vorhandenen App Service-Plan aus, oder erstellen Sie mit den folgenden Schritten einen neuen:

    a. Wählen Sie im Azure-Portal im Menü auf der linken Seite die Option **Ressource erstellen > Web-App** aus.

    b. Wählen Sie das Abonnement aus.

    c. Wählen Sie die Ressourcengruppe aus bzw. erstellen Sie sie.

    d. Geben Sie den Namen Ihrer Web-App ein.

    e. Wählen Sie **Code** oder **DockerContainer** aus.

    f. Wählen Sie einen Runtimestapel aus.

    g. Wählen Sie zwischen **Linux** und **Windows** aus. 

    h. Wählen Sie Ihre ASE in der Dropdownliste **Region** aus. 

    i. Wählen Sie einen neuen App Service-Plan aus, oder erstellen Sie ihn. Wählen Sie beim Erstellen eines neuen App Service-Plans die entsprechende SKU-Größe **Isoliert** aus.

    ![Isolierte Tarife][2]

    > [!NOTE]
    > Linux-Apps und Windows-Apps können sich nicht im selben App Service-Plan befinden, jedoch in derselben App Service-Umgebung.
    >

1. Wählen Sie **Bewerten + erstellen** aus, stellen Sie sicher, dass die Informationen richtig sind, und wählen Sie dann **Erstellen** aus.

## <a name="how-scale-works"></a>Skalieren

Jede App Service-App wird in einem App Service-Plan ausgeführt. App Service-Umgebungen enthalten App Service-Pläne, und App Service-Pläne enthalten Apps. Wenn Sie eine App skalieren, skalieren Sie auch den App Service-Plan und damit alle Apps in diesem Plan.

Wenn Sie einen App Service-Plan skalieren, wird die erforderliche Infrastruktur automatisch hinzugefügt. Bei der Skalierung von Vorgängen kommt es zu einer Zeitverzögerung, während die Infrastruktur hinzugefügt wird. Wenn Sie mehrere Skalierungsvorgänge nacheinander durchführen, wird die erste Skalierungsanforderung für die Infrastruktur verarbeitet, und die anderen werden in die Warteschlange eingereiht. Nachdem der erste Skalierungsvorgang abgeschlossen ist, werden die anderen Infrastrukturanforderungen zusammen ausgeführt. Und nach dem Hinzufügen der Infrastruktur werden die App Service-Pläne entsprechend zugewiesen. Auch die Erstellung eines neuen App Service-Plans selbst ist ein Skalierungsvorgang, weil zusätzliche Hardware angefordert wird.

Bei einer mehrinstanzenfähigen App Service-Instanz erfolgt die Skalierung unmittelbar, da sofort ein Ressourcenpool für die Unterstützung verfügbar ist. In einer ASE gibt es keinen solchen Puffer. Die Ressourcen werden nach Bedarf zugeordnet.

In einer ASE können Sie einen App Service-Plan auf bis zu 100 Instanzen zentral hochskalieren. Eine ASE kann übergreifend für alle App Service-Pläne in dieser ASE eine Gesamtmenge von bis zu 201 Instanzen enthalten.

## <a name="ip-addresses"></a>IP-Adressen

App Service kann eine dedizierte IP-Adresse an eine App vergeben. Diese Funktion ist nach dem Konfigurieren von IP-basiertem SSL verfügbar, wie unter [Binden eines vorhandenen benutzerdefinierten TLS/SSL-Zertifikats an Azure App Service][ConfigureSSL] beschrieben. In einer ILB-ASE können Sie keine weiteren IP-Adressen hinzufügen, die für IP-basiertes SSL verwendet werden sollen.

Bei einer externen ASE können Sie IP-basiertes SSL für Ihre App genauso wie für eine mehrinstanzenfähige App Service-Instanz konfigurieren. In der ASE ist immer eine freie Adresse vorhanden (bis zu 30 IP-Adressen). Bei jeder Verwendung einer IP-Adresse wird eine weitere hinzugefügt, sodass immer sofort eine Adresse zur Verfügung steht. Bei der Vergabe einer weiteren IP-Adresse ist eine zeitliche Verzögerung erforderlich. Diese Verzögerung verhindert das Hinzufügen von IP-Adressen in schneller Abfolge.

## <a name="front-end-scaling"></a>Front-End-Skalierung

Beim Aufskalieren Ihrer App Service-Pläne werden die Worker zur Unterstützung der App Service-Pläne automatisch hinzugefügt. Jede ASE wird mit zwei Front-Ends erstellt. Die Front-Ends werden mit einer Rate von einem Front-End pro jeder Gruppe von 15 App Service-Planinstanzen automatisch erweitert. Wenn Sie beispielsweise drei App Service-Pläne mit jeweils fünf Instanzen verwenden, verfügen Sie insgesamt über 15 Instanzen und drei Front-Ends. Wenn Sie auf insgesamt 30 Instanzen skalieren, haben Sie vier Front-Ends. Dieses Muster wird bei der Erweiterung fortgesetzt.

Die standardmäßig zugeordnete Anzahl von Front-Ends ist gut für mittlere Auslastungen geeignet. Sie können das Verhältnis bis hin zu einem Front-End für je fünf Instanzen verringern. Darüber hinaus können Sie auch die Größe der Front-Ends ändern. Standardmäßig sind sie vom Typ „Einzelkern“. Im Azure-Portal können Sie ihre Größe stattdessen auf zwei oder vier Kerne ändern.

Für die Änderung des Verhältnisses oder der Größen von Front-Ends fällt eine Gebühr an. Weitere Informationen finden Sie unter [Azure App Service – Preise][Pricing]. Wenn Sie die Auslastungskapazität Ihrer ASE verbessern möchten, erzielen Sie ein besseres Ergebnis, indem Sie zuerst auf Front-Ends mit zwei Kernen skalieren, bevor Sie das Skalierungsverhältnis anpassen. Das Ändern der Kerngröße Ihrer Front-Ends führt zu einem Upgrade Ihrer ASE und sollte außerhalb der regulären Geschäftszeiten durchgeführt werden.

Front-End-Ressourcen sind die HTTP/HTTPS-Endpunkte der ASE. Mit der standardmäßigen Front-End-Konfiguration beträgt der Speicherverbrauch pro Front-End konstant etwa 60 %. Der Hauptgrund für die Skalierung Ihrer Front-Ends ist die CPU-Auslastung, die vorrangig auf HTTPS-Datenverkehr basiert.

## <a name="app-access"></a>App-Zugriff

In einer externen ASE lautet das Domänensuffix für die App-Erstellung *.&lt;asename&gt;.p.azurewebsites.net*. Wenn Ihre ASE den Namen _external-ase_ hat und Sie in dieser ASE eine App mit dem Namen _contoso_ hosten, erreichen Sie sie über diese URLs:

- contoso.external-ase.p.azurewebsites.net
- contoso.scm.external-ase.p.azurewebsites.net

Informationen zum Erstellen einer externen ASE finden Sie unter [Erstellen einer App Service-Umgebung][MakeExternalASE].

In einer ILB-ASE lautet das Domänensuffix für die App-Erstellung *.&lt;asename&gt;.appserviceenvironment.net*. Wenn Ihre ASE den Namen _ilb-ase_ hat und Sie in dieser ASE eine App mit dem Namen _contoso_ hosten, erreichen Sie sie über diese URLs:

- contoso.ilb-ase.appserviceenvironment.net
- contoso.scm.ilb-ase.appserviceenvironment.net

Informationen zum Erstellen einer ILB-ASE finden Sie unter [Erstellen und Verwenden einer ILB-ASE][MakeILBASE].

Die SCM-URL wird verwendet, um auf die Kudu-Konsole zuzugreifen oder Ihre App per Web Deploy zu veröffentlichen. Informationen zur Kudu-Konsole finden Sie in [Kudu-Konsole für Azure App Service][Kudu]. Die Kudu-Konsole bietet eine Webbenutzeroberfläche für das Debuggen, Hochladen und Bearbeiten von Dateien und vieles mehr.

### <a name="dns-configuration"></a>DNS-Konfiguration 

Wenn Sie eine externe ASE verwenden, werden in ihrer ASE erstellte Apps bei Azure DNS registriert. Damit Ihre Apps öffentlich verfügbar sind, sind keine zusätzlichen Schritte in einer externen ASE erforderlich. In einer ILB-ASE müssen Sie Ihr eigenes DNS verwalten. Sie können dies entweder in Ihrem eigenen DNS-Server oder mit privaten Azure DNS-Zonen erzielen.

So konfigurieren Sie DNS in Ihrem eigenen Server mit Ihrer ILB-ASE

1. Erstellen Sie eine Zone für „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die ILB-IP-Adresse verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die ILB-IP-Adresse verweist.
1. Erstellen Sie eine Zone namens „scm“ in „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie einen A-Eintrag in der Zone „scm“, der * auf die ILB-IP-Adresse verweist.

So konfigurieren Sie DNS in privaten Azure DNS-Zonen

1. Erstellen Sie eine private Azure DNS-Zone namens „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der * auf die ILB-IP-Adresse verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der @ auf die ILB-IP-Adresse verweist.
1. Erstellen Sie einen A-Eintrag in dieser Zone, der *.scm auf die ILB-IP-Adresse verweist.

Die DNS-Einstellungen für Ihr ASE-Standarddomänensuffix schränken Ihre Apps nicht dahingehend ein, dass sie nur über diese Namen zugänglich sind. Sie können einen benutzerdefinierten Domänennamen ohne jegliche Validierung für Ihre Apps in einer ILB-ASE festlegen. Wenn Sie dann eine Zone namens *contoso.net* erstellen möchten, können Sie dies tun und die Zone auf die ILB-IP-Adresse verweisen lassen. Der benutzerdefinierte Domänenname funktioniert für App-Anforderungen, aber nicht für die SCM-Site. Die SCM-Site ist nur unter *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* verfügbar. 

Die Zone namens *.&lt;asename&gt;.appserviceenvironment.net* ist global eindeutig. Vor Mai 2019 konnten Kunden das Domänensuffix der ILB-ASE angeben. Wenn Sie *.contoso.com* als Domänensuffix verwenden wollten, konnten Sie dies tun, wobei dies dann die SCM-Site einschloss. Dieses Modell bot Herausforderungen, einschließlich: Verwalten des SSL-Standardzertifikats, fehlendes einmaliges Anmelden (Single Sign-On) bei der SCM-Site und die Anforderung, ein Platzhalterzertifikat zu verwenden. Der Upgradeprozess für das ILB-ASE-Standardzertifikat war außerdem führte außerdem zu Unterbrechungen und Neustarts von Anwendungen. Um diese Probleme zu beheben, wurde das ILB-ASE-Verhalten dahingehend geändert, dass ein Domänensuffix, das auf dem Namen der ASE basiert, zusammen mit einem Suffix, das Microsoft gehört, verwendet wird. Die Änderung des ILB-ASE-Verhaltens wirkt sich nur auf ILB-ASEs aus, die nach Mai 2019 erstellt wurden. Davor vorhandene ILB-ASEs müssen weiterhin das Standardzertifikat der ASE und seine DNS-Konfiguration verwalten.

## <a name="publishing"></a>Veröffentlichung

Wie bei dem mehrinstanzenfähigen App Service können Sie in einer ASE mithilfe dieser Methoden veröffentlichen:

- Webbereitstellung
- FTP
- Continuous Integration (CI)
- Drag and Drop in der Kudu-Konsole
- Eine IDE wie Visual Studio, Eclipse oder IntelliJ IDEA

Mit einer externen ASE funktionieren diese Veröffentlichungsoptionen alle gleich. Weitere Informationen finden Sie unter [Bereitstellen in Azure App Service][AppDeploy].

In einer ILB-ASE sind die Veröffentlichungsendpunkte nur über die ILB verfügbar. Die ILB liegt in einer privaten IP-Adresse im ASE-Subnetz im virtuellen Netzwerk. Wenn Sie keinen Netzwerkzugriff auf die ILB haben, können Sie in der betreffenden ASE keine Apps veröffentlichen. Wie in [Erstellen und Verwenden einer ILB-ASE][MakeILBASE] erwähnt, müssen Sie für die Apps im System ein DNS konfigurieren. Diese Anforderung beinhaltet den SCM-Endpunkt. Wenn die Endpunkte nicht ordnungsgemäß definiert sind, ist keine Veröffentlichung möglich. Darüber hinaus müssen Ihre IDEs über Netzwerkzugriff auf den ILB verfügen, um direkt an ihn veröffentlichen zu können.

Ohne zusätzliche Änderungen lassen sich internetbasierte CI-Systeme wie GitHub oder Azure DevOps nicht standardmäßig mit einer ILB-ASE verwenden, da der Veröffentlichungsendpunkt nicht über das Internet erreichbar ist. Sie können die Veröffentlichung in einer ILB-ASE über Azure DevOps ermöglichen, indem Sie in dem virtuellen Netzwerk, das die ILB-ASE enthält, einen selbstgehosteten Release-Agent installieren. Alternativ können Sie auch ein CI-System wie Dropbox verwenden, das ein Pullmodell verwendet.

Die Veröffentlichungsendpunkte für die Apps in einer ILB-ASE verwenden die Domäne, mit der die ILB-ASE erstellt wurde. Dies wird im Veröffentlichungsprofil und im Portalbereich der App angezeigt (in **Übersicht** > **Zusammenfassung** sowie in den **Eigenschaften**).

## <a name="storage"></a>Storage

Eine ASE verfügt für alle Apps in der ASE über Speicher mit einer Größe von 1 TB. Ein App Service-Plan in der SKU mit dem Tarif „Isoliert“ verfügt über ein Limit von 250 GB. In einer ASE werden 250 GB Speicherplatz pro App Service-Plan bis zu einem Limit von 1 TB hinzugefügt. Sie können mehr App Service-Pläne als nur vier haben, aber es wird kein über den Grenzwert von 1 TB hinausgehender Speicherplatz hinzugefügt.

## <a name="logging"></a>Protokollierung

Sie können Ihre ASE in Azure Monitor integrieren, um Protokolle zur ASE an Azure Storage, Azure Event Hubs oder Log Analytics zu senden. Diese Elemente werden heute protokolliert:

| Situation | `Message` |
|---------|----------|
| ASE ist fehlerhaft | Die angegebene ASE ist aufgrund einer ungültigen VNET-Konfiguration fehlerhaft. Die ASE wird angehalten, falls der Fehlerzustand weiter besteht. Stellen Sie sicher, dass die folgenden Richtlinien befolgt werden: https://docs.microsoft.com/azure/app-service/environment/network-info. |
| Speicherplatz im ASE-Subnetz ist nahezu erschöpft | Die angegebene ASE befindet sich in einem Subnetz, dessen Speicherplatz nahezu erschöpft ist. Es sind {0} verbleibende Adressen vorhanden. Nachdem diese Adressen ausgeschöpft wurden, kann die ASE nicht mehr skaliert werden.  |
| ASE nähert sich dem Limit für die Höchstmenge der Instanzen | Für die angegebene ASE wird in Kürze das Limit für die Höchstmenge der Instanzen erreicht. Derzeit sind {0} App Service-Planinstanzen bei einer maximalen Anzahl von 201 Instanzen vorhanden. |
| ASE kann eine Abhängigkeit nicht erreichen | Die angegebene ASE kann {0} nicht erreichen.  Stellen Sie sicher, dass die folgenden Richtlinien befolgt werden: https://docs.microsoft.com/azure/app-service/environment/network-info. |
| ASE wurde angehalten | Die angegebene ASE wurde angehalten. Der Grund für das Anhalten der ASE kann ein Kontoausfall oder eine ungültige Konfiguration des virtuellen Netzwerks sein. Beseitigen Sie die Grundursache, und setzen Sie die Ausführung der ASE fort, damit die Bereitstellung von Datenverkehr erfolgen kann. |
| ASE-Upgrade wurde gestartet | Ein Plattformupgrade für die angegebene ASE wurde gestartet. Es ist mit Verzögerungen bei den Skalierungsvorgängen zu rechnen. |
| ASE-Upgrade wurde abgeschlossen | Ein Plattformupgrade für die angegebene ASE wurde abgeschlossen. |
| Skalierungsvorgänge wurden gestartet | Für einen App Service-Plan ({0}) wurde die Skalierung gestartet. Gewünschter Status: {1} I{2}-Worker.
| Skalierungsvorgänge wurden abgeschlossen | Die Skalierung eines App Service-Plans ({0}) wurde abgeschlossen. Aktueller Status: {1} I{2}-Worker. |
| Für Skalierungsvorgänge sind Fehler aufgetreten | Bei der Skalierung eines App Service-Plans ({0}) ist ein Fehler aufgetreten. Aktueller Status: {1} I{2}-Worker. |

Aktivieren Sie die Protokollierung für Ihre ASE wie folgt:

1. Wechseln Sie im Portal zu **Diagnoseeinstellungen**.
1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.
1. Geben Sie einen Namen für die Protokollintegration an.
1. Wählen Sie die gewünschten Protokollziele aus, und konfigurieren Sie sie.
1. Wählen Sie **AppServiceEnvironmentPlatformLogs** aus.

![Einstellungen für ASE-Diagnoseprotokollierung][4]

Bei der Integration mit Log Analytics können Sie die Protokolle anzeigen, indem Sie im ASE-Portal die Option **Protokolle** auswählen und eine Abfrage für **AppServiceEnvironmentPlatformLogs** erstellen. Protokolle werden nur ausgegeben, wenn Ihre ASE über ein Ereignis verfügt, das diese auslöst. Wenn Ihre ASE nicht über ein derartiges Ereignis verfügt, gibt es keine Protokolle. Um sich schnell ein Beispiel für Protokolle in Ihrem Log Analytics-Arbeitsbereich anzusehen, führen Sie einen Skalierungsvorgang mit einem der App Service-Pläne in ihrer ASE aus. Anschließend können Sie eine Abfrage an **AppServiceEnvironmentPlatformLogs-** ausführen, um diese Protokolle anzuzeigen. 

**Erstellen einer Warnung**

Anleitungen zum Erstellen einer Warnung für Ihre Protokolle befolgen Sie die Anleitungen unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-log.md). Kurz gesagt:

* Öffnen Sie die Seite „Warnungen“ in Ihrem ASE-Portal.
* Wählen Sie **Neue Warnungsregel** aus.
* Wählen Sie Ihre Ressource aus, die Ihr Log Analytics-Arbeitsbereich sein soll.
* Legen Sie Ihre Bedingung mit einer benutzerdefinierten Protokollsuche so fest, dass eine Abfrage wie „AppServiceEnvironmentPlatformLogs | wenn ResultDescription „Skalierung hat begonnen“ enthält“, oder was Sie sonst möchten. Legen Sie den Schwellenwert entsprechend fest. 
* Fügen Sie nach Wunsch eine Aktionsgruppe hinzu, oder erstellen Sie eine. Die Aktionsgruppe ist der Ort, an dem Sie die Reaktion auf die Warnung definieren, z. B. das Senden einer E-Mail oder SMS.
* Benennen Sie Ihrer Warnung, und speichern Sie sie.

## <a name="upgrade-preference"></a>Upgradeeinstellung

Bei Verwendung von mehreren ASEs kann es sein, dass einige ASEs vor anderen ASEs aktualisiert werden sollen. Im **Resource Manager-Objekt „HostingEnvironment“** der ASE können Sie einen Wert für **upgradePreference** festlegen. Die Einstellung **upgradePreference** kann mithilfe einer Vorlage, mit ARMClient oder per https://resources.azure.com konfiguriert werden. Es gibt drei mögliche Werte:

- **Keine:** Azure führt das Upgrade Ihrer ASE in keinem bestimmten Batch durch. Dies ist der Standardwert.
- **Early**: Ihre ASE wird in der ersten Hälfte der App Service-Upgrades aktualisiert.
- **Late**: Ihre ASE wird in der zweiten Hälfte der App Service-Upgrades aktualisiert.

Bei Verwendung von https://resources.azure.com führen Sie die folgenden Schritte aus, um den Wert für **upgradePreferences** festzulegen:

1. Wechseln Sie zu „resources.azure.com“, und melden Sie sich mit Ihrem Azure-Konto an.
1. Gehen Sie die Ressourcen durch bis zu „subscriptions\/\[Abonnementname\]\/resourceGroups\/\[Ressourcengruppenname\]\/providers\/Microsoft.Web\/hostingEnvironments\/\[ASE-Name\]“.
1. Wählen Sie ganz oben **Lesen/Schreiben** aus.
1. Wählen Sie **Bearbeiten** aus.
1. Legen Sie **upgradePreference** auf den gewünschten der drei Werte fest.
1. Wählen Sie **Patch** aus.

![Ressourcenanzeige auf „azure.com“][5]

Die Verwendung der Funktion **upgradePreferences** ist am sinnvollsten, wenn Sie mehrere ASEs besitzen, weil Ihre als „Early“ gekennzeichneten ASEs vor den mit „Late“ gekennzeichneten ASEs aktualisiert werden. Bei Verwendung von mehreren ASEs sollten Sie für Ihre Entwicklungs- und Test-ASEs die Option „Early“ und für Ihre Produktions-ASEs die Option „Late“ festlegen.

## <a name="pricing"></a>Preise

Die Preis-SKU *Isoliert* ist nur für die Verwendung mit ASEs gedacht. Alle App Service-Pläne, die in einer ASE gehostet werden, befinden sich in der Preis-SKU „Isoliert“. Die Gebühren für den Tarif „Isoliert“ für App Service-Pläne können sich nach Region unterscheiden.

Zusätzlich zum Preis Ihrer App Service-Pläne fällt eine Pauschalgebühr für die eigentliche ASE an. Diese Pauschalgebühr richtet sich nicht nach der Größe Ihrer ASE. Sie fällt für die ASE-Infrastruktur mit der Standardskalierungsrate mit einem zusätzlichen Front-End für jeweils 15 Instanzen des App Service-Plans an.

Wenn die Standardskalierung von einem Front-End für jeweils 15 Instanzen des App Service-Plans nicht schnell genug ist, können Sie das Verhältnis, nach dem Front-Ends hinzugefügt werden, oder die Größe der Front-Ends anpassen. Wenn Sie das Verhältnis oder die Größe anpassen, bezahlen Sie für die Front-End-Kerne, die standardmäßig nicht hinzugefügt werden würden.

Wenn Sie z.B. das Skalierungsverhältnis auf den Wert 10 anpassen, wird für je zehn Instanzen in Ihren App Service-Plänen ein Front-End hinzugefügt. Die Pauschalgebühr deckt ein Skalierungsverhältnis von einem Front-End pro 15 Instanzen ab. Mit einem Skalierungsverhältnis von 10 bezahlen Sie für das dritte Front-End, das für die 10 Instanzen des App Service-Plans hinzugefügt wird, eine Gebühr. Sie müssen nicht dafür bezahlen, wenn Sie 15 Instanzen erreichen, da es in diesem Fall automatisch hinzugefügt wurde.

Wenn Sie die Größe der Front-Ends in 2 Kerne ändern, aber das Verhältnis nicht anpassen, bezahlen Sie für die zusätzlichen Kerne. Eine ASE wird mit zwei Front-Ends erstellt. Sie würden also selbst unterhalb des Schwellenwerts für die automatische Skalierung zwei zusätzliche Kerne bezahlen, wenn Sie die Größe auf 2-Kern-Front-Ends erhöhen.

Weitere Informationen finden Sie unter [Azure App Service – Preise][Pricing].

## <a name="delete-an-ase"></a>Löschen einer ASE

So löschen Sie eine ASE

1. Wählen Sie **Löschen** am Anfang des Bereichs **App Service-Umgebung** aus.

1. Geben Sie den Namen Ihrer ASE ein, um zu bestätigen, dass Sie sie löschen möchten. Beim Löschen einer ASE wird auch ihr gesamter Inhalt gelöscht.

    ![Löschen einer ASE][3]

1. Klicken Sie auf **OK**.

## <a name="ase-cli"></a>ASE CLI

Es gibt Befehlszeilenfunktionen, die auf eine ASE angewendet werden können.  Die „az clie“-Befehle sind unten aufgeführt.

```azurecli
C:\>az appservice ase --help

Group
    az appservice ase : Manage App Service Environments v2.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    create         : Create app service environment.
    delete         : Delete app service environment.
    list           : List app service environments.
    list-addresses : List VIPs associated with an app service environment.
    list-plans     : List app service plans associated with an app service environment.
    show           : Show details of an app service environment.
    update         : Update app service environment.

For more specific examples, use: az find "az appservice ase"
```



<!--Image references-->
[1]: ./media/using_an_app_service_environment/usingase-appcreate.png
[2]: ./media/using_an_app_service_environment/usingase-pricingtiers.png
[3]: ./media/using_an_app_service_environment/usingase-delete.png
[4]: ./media/using_an_app_service_environment/usingase-logsetup.png
[4]: ./media/using_an_app_service_environment/usingase-logs.png
[5]: ./media/using_an_app_service_environment/usingase-upgradepref.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
