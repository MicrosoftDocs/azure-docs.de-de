---
title: Verwenden einer App Service-Umgebung
description: Hier erfahren Sie, wie Sie Ihre App Service-Umgebung zum Hosten isolierter Anwendungen verwenden.
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 11/16/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 3679bf9d55ddccefddb4bf3b2a96ec1b427315af
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94663249"
---
# <a name="using-an-app-service-environment"></a>Verwenden einer App Service-Umgebung

Die App Service-Umgebung (App Service Environment, ASE) ist eine Azure App Service-Bereitstellung mit einem einzelnen Mandanten, die direkt in ein virtuelles Azure-Netzwerk (Virtual Network, VNET) Ihrer Wahl integriert wird. Dieses System wird nur von einem einzelnen Kunden verwendet. Für in der ASE bereitgestellte Apps gelten die Netzwerkfeatures, die auf das ASE-Subnetz angewendet werden. Für Ihre Apps müssen keine zusätzlichen Features aktiviert werden, damit diese Netzwerkfeatures für sie gelten. 

## <a name="create-an-app-in-an-ase"></a>Erstellen einer App in einer ASE

Um eine App in einer ASE zu erstellen, verwenden Sie denselben Prozess wie bei der normalen Erstellung einer App, jedoch mit einigen kleinen Unterschieden. Beim Erstellen eines neuen App Service-Plans gilt:

- Anstatt einen geografischen Standort auszuwählen, an dem Sie Ihre App bereitstellen, wählen Sie eine ASE als Ihren Standort aus.
- In einer ASE erstellte App Service-Pläne können sich nur in einem Tarif vom Typ „Isoliert V2“ befinden.

Wenn Sie über keine ASE verfügen, können Sie eine erstellen. Folgen Sie hierzu den Anweisungen in [Erstellen einer App Service-Umgebung][MakeASE].

So erstellen Sie eine App in einer ASE

1. Wählen Sie **Ressource erstellen** > **Web + Mobil** > **Web-Apps** aus.

1. Wählen Sie ein Abonnement aus.

1. Geben Sie einen Namen für eine neue Ressourcengruppe ein, oder wählen Sie **Vorhandene verwenden** aus, und wählen Sie in der Dropdownliste eine Ressourcengruppe aus.

1. Geben Sie einen Namen für die App ein. Wenn Sie bereits einen App Service-Plan in einer ASE ausgewählt haben, entspricht der Domänenname der App dem Domänenname der ASE:

    ![Erstellen einer App in einer ASE][1]

1. Wählen Sie Ihren Veröffentlichungstyp, Ihren Stack und Ihr Betriebssystem aus.

1.  Wählen Sie eine Region aus. Hier muss eine bereits vorhandene App Service-Umgebung der Version 3 ausgewählt werden.  Im Rahmen der App-Erstellung kann keine ASE der Version 3 erstellt werden. 

1. Wählen Sie einen vorhandenen App Service-Plan in Ihrer ASE aus, oder erstellen Sie einen neuen Plan. Wenn Sie eine neue App erstellen, wählen Sie die gewünschte Größe für Ihren App Service-Plan aus. Die einzige SKU, die Sie für Ihre App auswählen können, ist eine SKU mit Tarif vom Typ „Isoliert V2“.

    ![Tarife vom Typ „Isoliert V2“][2]

    > [!NOTE]
    > Linux- und Windows-Apps können sich zwar nicht im gleichen App Service-Plan, aber in der gleichen App Service-Umgebung befinden.
    >

1. Wählen Sie **Weiter: Überwachen** aus. Wenn Sie App Insights für Ihre App aktivieren möchten, können Sie dies hier im Rahmen der Erstellung tun. 

1.  Klicken Sie auf **Weiter: Tags**. Fügen Sie der App ggf. gewünschte Tags hinzu.  

1. Wählen Sie **Bewerten + erstellen** aus, stellen Sie sicher, dass die Informationen richtig sind, und wählen Sie dann **Erstellen** aus.

## <a name="how-scale-works"></a>Skalieren

Jede App Service-App wird in einem App Service-Plan ausgeführt. App Service-Umgebungen enthalten App Service-Pläne, und App Service-Pläne enthalten Apps. Wenn Sie eine App skalieren, skalieren Sie auch den App Service-Plan und damit alle Apps in diesem Plan.

Wenn Sie einen App Service-Plan skalieren, wird die erforderliche Infrastruktur automatisch hinzugefügt. Bei der Skalierung von Vorgängen kommt es zu einer Zeitverzögerung, während die Infrastruktur hinzugefügt wird. Wenn Sie einen App Service-Plan skalieren, werden alle anderen Skalierungsvorgänge, die mit dem gleichen Betriebssystem und der gleichen Größe angefordert wurden, erst nach Abschluss des ersten Vorgangs ausgeführt. Nach Abschluss des blockierenden Skalierungsvorgangs werden alle in die Warteschlange eingereihten Anforderungen gleichzeitig verarbeitet. Ein Skalierungsvorgang mit einer bestimmten Größe und einem bestimmten Betriebssystem führt nicht zur Blockierung der Skalierung anderer Kombinationen von Größe und Betriebssystem. Wenn Sie also beispielsweise einen App Service-Plan vom Typ „Windows I2v2“ skaliert haben, werden andere Skalierungsanforderungen für „Windows I2v2“ in dieser ASE in die Warteschlange eingereiht, bis die erste Skalierung abgeschlossen ist.   

Bei einer mehrinstanzenfähigen App Service-Instanz erfolgt die Skalierung unmittelbar, da sofort ein Ressourcenpool für die Unterstützung verfügbar ist. In einer ASE gibt es keinen solchen Puffer. Die Ressourcen werden nach Bedarf zugeordnet.

## <a name="app-access"></a>App-Zugriff

In einer ASE lautet das Domänensuffix für die App-Erstellung *.&lt;asename&gt;.appserviceenvironment.net*. Wenn Ihre ASE _my-ase_ heißt und Sie in dieser ASE eine App mit dem Namen _contoso_ hosten, erreichen Sie sie unter den folgenden URLs:

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

Informationen zum Erstellen einer ASE finden Sie unter [Erstellen einer App Service-Umgebung][MakeASE].

Die SCM-URL wird verwendet, um auf die Kudu-Konsole zuzugreifen oder Ihre App per Web Deploy zu veröffentlichen. Informationen zur Kudu-Konsole finden Sie in [Kudu-Konsole für Azure App Service][Kudu]. Die Kudu-Konsole bietet eine Webbenutzeroberfläche für das Debuggen, Hochladen und Bearbeiten von Dateien und vieles mehr.

### <a name="dns-configuration"></a>DNS-Konfiguration 

Die ASE verwendet private Endpunkte für eingehenden Datenverkehr und wird automatisch mit Azure DNS Private Zones konfiguriert. Wenn Sie einen eigenen DNS-Server verwenden möchten, müssen die folgenden Einträge hinzugefügt werden:

1. Erstellen Sie eine Zone für „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie in dieser Zone einen A-Eintrag, durch den „*“ auf die vom privaten ASE-Endpunkt verwendete IP-Adresse für eingehenden Datenverkehr verwiesen wird.
1. Erstellen Sie in dieser Zone einen A-Eintrag, durch den „@“ auf die vom privaten ASE-Endpunkt verwendete IP-Adresse für eingehenden Datenverkehr verwiesen wird.
1. Erstellen Sie eine Zone namens „scm“ in „&lt;ASE-Name&gt;.appserviceenvironment.net“.
1. Erstellen Sie in der Zone „scm“ einen A-Eintrag, durch den „*“ auf die vom privaten ASE-Endpunkt verwendete IP-Adresse verwiesen wird.

Die DNS-Einstellungen für Ihr ASE-Standarddomänensuffix schränken Ihre Apps nicht dahingehend ein, dass sie nur über diese Namen zugänglich sind. Sie können einen benutzerdefinierten Domänennamen ohne jegliche Validierung für Ihre Apps in einer ASE festlegen. Wenn Sie dann eine Zone namens *contoso.net* erstellen möchten, können Sie dies tun und auf die IP-Adresse für eingehenden Datenverkehr verweisen. Der benutzerdefinierte Domänenname funktioniert für App-Anforderungen, aber nicht für die SCM-Site. Die SCM-Site ist nur unter *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net* verfügbar. 

## <a name="publishing"></a>Veröffentlichung

Wie bei dem mehrinstanzenfähigen App Service können Sie in einer ASE mithilfe dieser Methoden veröffentlichen:

- Webbereitstellung
- Continuous Integration (CI)
- Drag and Drop in der Kudu-Konsole
- Eine IDE wie Visual Studio, Eclipse oder IntelliJ IDEA

Mit einer ASE sind die Veröffentlichungsendpunkte nur über die vom privaten Endpunkt verwendete Adresse für eingehenden Datenverkehr verfügbar. Ohne Netzwerkzugriff auf die Adresse des privaten Endpunkts können Sie in dieser ASE keine Apps veröffentlichen.  Darüber hinaus müssen Ihre IDEs über Netzwerkzugriff auf den ILB verfügen, um direkt an ihn veröffentlichen zu können.

Ohne zusätzliche Änderungen lassen sich internetbasierte CI-Systeme wie GitHub oder Azure DevOps nicht standardmäßig mit einer ILB-ASE verwenden, da der Veröffentlichungsendpunkt nicht über das Internet erreichbar ist. Sie können die Veröffentlichung in einer ILB-ASE über Azure DevOps ermöglichen, indem Sie in dem virtuellen Netzwerk, das die ILB-ASE enthält, einen selbstgehosteten Release-Agent installieren. 

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

Bei der Integration mit Log Analytics können Sie die Protokolle anzeigen, indem Sie im ASE-Portal die Option **Protokolle** auswählen und eine Abfrage für **AppServiceEnvironmentPlatformLogs** erstellen. Protokolle werden nur ausgegeben, wenn Ihre ASE über ein Ereignis verfügt, das diese auslöst. Wenn Ihre ASE nicht über ein derartiges Ereignis verfügt, stehen keine Protokolle zur Verfügung. Um sich schnell ein Beispiel für Protokolle in Ihrem Log Analytics-Arbeitsbereich anzusehen, führen Sie einen Skalierungsvorgang mit einem der App Service-Pläne in ihrer ASE aus. Anschließend können Sie eine Abfrage an **AppServiceEnvironmentPlatformLogs-** ausführen, um diese Protokolle anzuzeigen. 

**Erstellen einer Warnung**

Anleitungen zum Erstellen einer Warnung für Ihre Protokolle befolgen Sie die Anleitungen unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../../azure-monitor/platform/alerts-log.md). Kurz gesagt:

* Öffnen Sie die Seite „Warnungen“ in Ihrem ASE-Portal.
* Wählen Sie **Neue Warnungsregel** aus.
* Wählen Sie Ihre Ressource aus, die Ihr Log Analytics-Arbeitsbereich sein soll.
* Legen Sie Ihre Bedingung mit einer benutzerdefinierten Protokollsuche so fest, dass eine Abfrage wie „AppServiceEnvironmentPlatformLogs | wenn ResultDescription „Skalierung hat begonnen“ enthält“, oder was Sie sonst möchten. Legen Sie den Schwellenwert entsprechend fest. 
* Fügen Sie nach Wunsch eine Aktionsgruppe hinzu, oder erstellen Sie eine. Die Aktionsgruppe ist der Ort, an dem Sie die Reaktion auf die Warnung definieren, z. B. das Senden einer E-Mail oder SMS.
* Benennen Sie Ihrer Warnung, und speichern Sie sie.

## <a name="internal-encryption"></a>Interne Verschlüsselung

Die App Service-Umgebung wird als Blackbox-System betrieben, bei dem Sie die internen Komponenten oder die Kommunikation innerhalb des Systems nicht sehen können. Um einen höheren Durchsatz zu ermöglichen, ist die Verschlüsselung zwischen internen Komponente standardmäßig nicht aktiviert. Das System ist sicher, da der Datenverkehr vollständig vor Überwachung und Zugriff geschützt ist. Sollte aus Compliancegründen allerdings eine End-to-End-Verschlüsselung des Datenpfads erforderlich sein, können Sie dies im Bereich **Konfiguration** der ASE-Benutzeroberfläche aktivieren.

![Aktivieren der internen Verschlüsselung][5]

Dadurch werden der interne Netzwerkdatenverkehr in Ihrer App Service-Umgebung (ASE) zwischen den Front-Ends und Workern, die Auslagerungsdatei und die Workerdatenträger verschlüsselt. Nach der Aktivierung der Clustereinstellung (clusterSetting) „InternalEncryption“ kann es zu einer Beeinträchtigung der Systemleistung kommen. Wenn Sie „InternalEncryption“ aktivieren, befindet sich die ASE in einem instabilen Zustand, bis die Änderung vollständig weitergegeben wurde. Die vollständige Weitergabe der Änderung kann abhängig von der Anzahl von Instanzen in Ihrer ASE einige Stunden in Anspruch nehmen. Es wird dringend empfohlen, diese Option nicht für eine ASE zu aktivieren, während sie verwendet wird. Wenn Sie die Option für eine aktiv genutzte ASE aktivieren müssen, wird dringend empfohlen, den Datenverkehr an eine Sicherungsumgebung umzuleiten, bis der Vorgang abgeschlossen ist.

## <a name="upgrade-preference"></a>Upgradeeinstellung

Bei Verwendung von mehreren ASEs kann es sein, dass einige ASEs vor anderen ASEs aktualisiert werden sollen. Im **Resource Manager-Objekt „HostingEnvironment“** der ASE können Sie einen Wert für **upgradePreference** festlegen. Die Einstellung **upgradePreference** kann mithilfe einer Vorlage, mit ARMClient oder per https://resources.azure.com konfiguriert werden. Es gibt drei mögliche Werte:

- **Keine:** Azure führt das Upgrade Ihrer ASE in keinem bestimmten Batch durch. Dies ist der Standardwert.
- **Early**: Ihre ASE wird in der ersten Hälfte der App Service-Upgrades aktualisiert.
- **Late**: Ihre ASE wird in der zweiten Hälfte der App Service-Upgrades aktualisiert.

Ihre Upgradepräferenz können Sie im Bereich **Konfiguration** der ASE-Benutzeroberfläche konfigurieren. 

Die Verwendung der Funktion **upgradePreferences** ist am sinnvollsten, wenn Sie mehrere ASEs besitzen, weil Ihre als „Early“ gekennzeichneten ASEs vor den mit „Late“ gekennzeichneten ASEs aktualisiert werden. Bei Verwendung von mehreren ASEs sollten Sie für Ihre Entwicklungs- und Test-ASEs die Option „Early“ und für Ihre Produktions-ASEs die Option „Late“ festlegen.

## <a name="delete-an-ase"></a>Löschen einer ASE

So löschen Sie eine ASE

1. Wählen Sie **Löschen** am Anfang des Bereichs **App Service-Umgebung** aus.

1. Geben Sie den Namen Ihrer ASE ein, um zu bestätigen, dass Sie sie löschen möchten. Beim Löschen einer ASE wird auch ihr gesamter Inhalt gelöscht.

    ![Löschen einer ASE][3]

1. Klicken Sie auf **OK**.

<!--Image references-->
[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png

<!--Links-->
[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/platform/alerts-log.md
