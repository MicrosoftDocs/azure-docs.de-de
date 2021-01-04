---
title: Lokaler Cache
description: Erfahren Sie, wie der lokale Cache in Azure App Service funktioniert und wie Sie den lokalen Cache Ihrer App aktivieren, seine Größe ändern und seinen Status abfragen.
tags: optional
ms.assetid: e34d405e-c5d4-46ad-9b26-2a1eda86ce80
ms.topic: article
ms.date: 03/04/2016
ms.custom: seodec18
ms.openlocfilehash: 81782f63199a9fe8f43f56aeefcd1c68951d57a4
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96852251"
---
# <a name="azure-app-service-local-cache-overview"></a>Übersicht über den lokalen Cache von Azure App Service

> [!NOTE]
> Lokaler Cache wird in Funktions-Apps oder containerisierten App Service-Apps nicht unterstützt, z. B. in [Windows-Containern](quickstart-custom-container.md?pivots=container-windows) oder in [App Service für Linux](overview.md#app-service-on-linux).


Azure App Service-Inhalt wird in Azure Storage gespeichert und dauerhaft als Inhaltsfreigabe bereitgestellt. Dieses Design ist auf den Einsatz mit einer Vielzahl von Apps ausgelegt und weist die folgenden Merkmale auf:  

* Der Inhalt wird über mehrere VM-Instanzen der App hinweg freigegeben.
* Der Inhalt wird dauerhaft bereitgestellt und kann durch ausgeführte Apps geändert werden.
* Protokolldateien und Diagnosedatendateien stehen unterhalb desselben freigegebenen Inhaltsordners zur Verfügung.
* Beim Veröffentlichen neuer Inhalte wird der Inhaltsordner direkt aktualisiert. Der Inhalt kann umgehend über die SCM-Website und die ausgeführte App angezeigt werden. (Einige Technologien wie z. B. ASP.NET initiieren bei einigen Dateiänderungen üblicherweise einen App-Neustart, um die aktuellen Inhalte abzurufen.)

Während viele Apps einzelne oder alle dieser Features nutzen, benötigen einige Apps lediglich einen hochleistungsfähigen schreibgeschützten Inhaltsspeicher, aus dem sie mit Hochverfügbarkeit ausgeführt werden können. Diese Apps können von einer VM-Instanz eines bestimmten lokalen Caches profitieren.

Der lokale Cache von Azure App Service bietet eine Webrollenansicht Ihrer Inhalte. Dabei handelt es sich um einen Cache Ihres Speicherinhalts mit „write but discard“-Prinzip, der beim lokalen Start asynchron erstellt wird. Wenn der Cache bereit ist, wird die Site zur Ausführung mit dem zwischengespeicherten Inhalt umgeschaltet. Apps, die mit lokalem Cache ausgeführt werden, bieten die folgenden Vorteile:

* Sie werden nicht durch Latenzen beeinträchtigt, die beim Zugriff auf Inhalt in Azure Storage auftreten.
* Sie werden nicht durch geplante Upgrades oder ungeplante Downtimes der Server beeinträchtigt, die die Inhaltsfreigabe verwalten, und sie werden nicht durch andere Unterbrechungen im Zusammenhang mit Azure Storage beeinträchtigt.
* Es sind weniger App-Neustarts aufgrund von Änderungen an der Speicherfreigabe erforderlich.

## <a name="how-the-local-cache-changes-the-behavior-of-app-service"></a>Auswirkung des lokalen Caches auf das Verhalten von App Service
* _D:\home_ verweist auf den lokalen Cache, der beim Starten der App auf der VM-Instanz erstellt wird. _D:\local_ verweist weiterhin auf den temporären VM-spezifischen Speicher.
* Der lokale Cache enthält eine einmalige Kopie der Ordner _/site_ und _/siteextensions_ des freigegebenen Inhaltsspeichers unter _D:\home\site_ bzw. _D:\home\siteextensions_. Die Dateien werden beim Starten der App in den lokalen Cache kopiert. Die Größe der beiden Ordner ist pro App standardmäßig auf 1 GB beschränkt, kann aber auf bis zu 2 GB erhöht werden. Beachten Sie, dass mit zunehmender Größe das Laden des Caches länger dauert. Wenn Sie die Größe des lokalen Caches auf 2 GB heraufgesetzt haben und die kopierten Dateien das Maximum von 2 GB überschreiten, ignoriert App Service den lokalen Cache im Hintergrund und führt Lesevorgänge in der Remotedateifreigabe durch. Wenn kein Grenzwert definiert oder der Grenzwert auf einen niedrigeren Wert als 2 GB festgelegt ist und die kopierten Dateien den Grenzwert überschreiten, kann bei Bereitstellung oder Austausch ein Fehler auftreten.
* Der lokale Cache bietet Lese- und Schreibzugriff. Änderungen werden jedoch verworfen, wenn die App zwischen virtuellen Computern verschoben oder neu gestartet wird. Verwenden Sie den lokalen Cache nicht für Apps, die unternehmenskritische Daten im Inhaltsspeicher speichern.
* _D:\home\LogFiles_ und _D:\home\Data_ enthalten Protokolldateien und App-Daten. Die zwei Unterordner werden lokal auf der VM-Instanz gespeichert und regelmäßig in den freigegebenen Inhaltsspeicher kopiert. Apps können Protokolldateien und Daten speichern, indem sie diese in die jeweiligen Ordner schreiben. Das Kopieren in den freigegebenen Inhaltsspeicher erfolgt jedoch nach dem Prinzip der besten Leistung, daher können Protokolldateien und Daten aufgrund eines plötzlichen Absturzes einer VM-Instanz verloren gehen.
* Das [Protokollstreaming](troubleshoot-diagnostic-logs.md#stream-logs) wird durch den bestmöglichen Kopiervorgang beeinträchtigt. Bei den gestreamten Protokollen kann es zu einer Verzögerung von bis zu einer Minute kommen.
* Im freigegebenen Inhaltsspeicher ergibt sich für Apps, die den lokalen Cache verwenden, eine Änderung in der Ordnerstruktur der Ordner _LogFiles_ und _Data_. In diesen Ordnern sind jetzt Unterordner vorhanden, die das Benennungsmuster „eindeutiger Bezeichner + Zeitstempel“ aufweisen. Jeder dieser Unterordner entspricht einer VM-Instanz, auf der die App ausgeführt wird oder wurde.
* Andere Ordner im Verzeichnis _D:\home_ verbleiben im lokalen Cache und werden nicht in den freigegebenen Inhaltsspeicher kopiert.
* Bei der App-Bereitstellung über eine unterstützte Methode erfolgt die Veröffentlichung direkt im permanenten freigegebenen Inhaltsspeicher. Zum Aktualisieren der Ordner _D:\home\site_ und _D:\home\siteextensions_ im lokalen Cache muss die App neu gestartet werden. Informationen zu einem nahtlosen Lebenszyklus finden Sie weiter unten in diesem Artikel.
* Die standardmäßige Inhaltsansicht der SCM-Site ist weiterhin die des freigegebenen Inhaltsspeichers.

## <a name="enable-local-cache-in-app-service"></a>Aktivieren des lokalen Caches in App Service 

> [!NOTE]
> Der lokale Cache wird in den Tarifen **F1** oder **D1** nicht unterstützt. 

Der lokale Cache wird mithilfe einer Kombination aus reservierten App-Einstellungen konfiguriert. Diese App-Einstellungen können über die folgenden Methoden konfiguriert werden:

* [Azure portal](#Configure-Local-Cache-Portal)
* [Azure Resource Manager](#Configure-Local-Cache-ARM)

### <a name="configure-local-cache-by-using-the-azure-portal"></a>Konfigurieren des lokalen Caches über das Azure-Portal
<a name="Configure-Local-Cache-Portal"></a>

Der lokale Cache wird für jede Web-App über die folgende App-Einstellung aktiviert: `WEBSITE_LOCAL_CACHE_OPTION` = `Always`  

![App-Einstellungen im Azure-Portal: Lokaler Cache](media/app-service-local-cache-overview/app-service-local-cache-configure-portal.png)

### <a name="configure-local-cache-by-using-azure-resource-manager"></a>Konfigurieren des lokalen Caches mit Azure Resource Manager
<a name="Configure-Local-Cache-ARM"></a>

```json

...

{
    "apiVersion": "2015-08-01",
    "type": "config",
    "name": "appsettings",
    "dependsOn": [
        "[resourceId('Microsoft.Web/sites/', variables('siteName'))]"
    ],

    "properties": {
        "WEBSITE_LOCAL_CACHE_OPTION": "Always",
        "WEBSITE_LOCAL_CACHE_SIZEINMB": "1000"
    }
}

...
```

## <a name="change-the-size-setting-in-local-cache"></a>Ändern der Größeneinstellung im lokalen Cache
Standardmäßig ist der lokale Cache **1 GB** groß. Dies schließt die Ordner „/site“ und „/siteextensions“ ein, die aus dem Inhaltspeicher kopiert werden, sowie alle lokal erstellten Protokolle und Datenordner. Um dieses Limit zu erhöhen, verwenden Sie die folgende App-Einstellung: `WEBSITE_LOCAL_CACHE_SIZEINMB`. Die Größe kann auf bis zu **2 GB** (2.000 MB) pro App erhöht werden. Beachten Sie, dass mit zunehmender Größe das Laden des lokalen Caches länger dauert.

## <a name="best-practices-for-using-app-service-local-cache"></a>Bewährte Methoden für die Verwendung des lokalen Caches von App Service
Es wird empfohlen, den lokalen Cache gemeinsam mit dem Feature [Stagingumgebungen](../app-service/deploy-staging-slots.md) zu verwenden.

* Fügen Sie die *persistente* App-Einstellung `WEBSITE_LOCAL_CACHE_OPTION` mit dem Wert `Always` zu Ihrem **Produktionsslot** hinzu. Wenn Sie `WEBSITE_LOCAL_CACHE_SIZEINMB`verwenden, fügen Sie diese Einstellung ebenfalls als persistente Einstellung zu Ihrem Produktionsslot hinzu.
* Erstellen Sie einen **Stagingslot**, und führen Sie eine Veröffentlichung in Ihrem Stagingslot durch. Für den Stagingslot wird üblicherweise nicht die Verwendung des lokalen Caches festgelegt. So wird ein nahtloser Lebenszyklus für das Erstellen/Bereitstellen/Testen für das Staging ermöglicht, während die Vorteile des lokalen Caches für den Produktionsslot genutzt werden.
* Testen Sie Ihre Site mit dem Stagingslot.  
* Wenn Sie bereit dazu sind, wechseln Sie über einen [swap-Vorgang](../app-service/deploy-staging-slots.md#Swap) zwischen Ihrem Staging- und Ihrem Produktionsslot.  
* Persistente Einstellungen umfassen einen Namen und gelten dauerhaft für einen Slot. Wenn also der Stagingslot auf den Produktionsslot umgeschaltet wird, erbt dieser die App-Einstellungen für den lokalen Cache. Der neue Produktionsslot wird nach ein paar Minuten mit dem lokalen Cache ausgeführt und im Rahmen der Slotaufwärmung nach dem Wechsel aufgewärmt. Wenn der Slotwechsel abgeschlossen ist, wird Ihr Produktionsslot mit dem lokalen Cache ausgeführt.

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

### <a name="how-can-i-tell-if-local-cache-applies-to-my-app"></a>Ist der lokale Cache für meine App geeignet?
Wenn Ihre App einen zuverlässigen Hochleistungs-Inhaltsspeicher benötigt, den Inhaltsspeicher nicht zum Schreiben von unternehmenskritischen Daten zur Laufzeit nutzt und eine Gesamtgröße von maximal 2 GB erforderlich ist, dann lautet die Antwort: Ja, Ihre Anwendung ist für den lokalen Cache geeignet! Um die Gesamtgröße der Ordner „/site“ und „/siteextensions“ zu ermitteln, können Sie die Siteerweiterung „Azure Web Apps Disk Usage“ verwenden.

### <a name="how-can-i-tell-if-my-site-has-switched-to-using-local-cache"></a>Woher weiß ich, dass meine Site auf die Verwendung des lokalen Caches umgeschaltet hat?
Wenn Sie das Feature für den lokalen Cache mit Stagingumgebungen verwenden, findet der Wechsel erst dann statt, wenn der lokale Cache aktiviert wurde. Um zu prüfen, ob Ihre Site mit dem lokalen Cache ausgeführt wird, können Sie die Workerprozess-Umgebungsvariable `WEBSITE_LOCALCACHE_READY`überprüfen. Befolgen Sie die auf der Seite für die [Workerprozess-Umgebungsvariable](https://github.com/projectkudu/kudu/wiki/Process-Threads-list-and-minidump-gcdump-diagsession#process-environment-variable) bereitgestellten Anweisungen, um auf mehreren Instanzen auf die Workerprozess-Umgebungsvariable zuzugreifen.  

### <a name="i-just-published-new-changes-but-my-app-does-not-seem-to-have-them-why"></a>Ich habe soeben neue Änderungen veröffentlicht, aber in meiner App scheinen diese nicht verfügbar zu sein. Warum?
Wenn Ihre App den lokalen Cache verwendet, müssen Sie Ihre Site neu starten, um die neuesten Änderungen abzurufen. Sie möchten Änderungen nicht für eine Produktionssite veröffentlichen? Relevante Informationen finden Sie bei den Slotoptionen im oben stehenden Abschnitt zu den bewährten Methoden.

> [!NOTE]
> Die Bereitstellungsoption [aus Paket ausführen](deploy-run-package.md) ist mit dem lokalen Cache nicht kompatibel.

### <a name="where-are-my-logs"></a>Wo sind meine Protokolle?
Bei Verwendung des lokalen Caches sehen Ihre Protokolle und Datenordner etwas anders aus. Die Struktur Ihrer Unterordner bleibt jedoch erhalten, mit der Ausnahme, dass sie unterhalb eines Unterordners mit folgendem Format geschachtelt sind: „eindeutiger VM-Bezeichner“ + Zeitstempel

### <a name="i-have-local-cache-enabled-but-my--app-still-gets-restarted-why-is-that-i-thought-local-cache-helped-with-frequent-app-restarts"></a>Ich habe den lokalen Cache aktiviert, aber meine App wird weiterhin neu gestartet. Warum? Ich dachte, durch den lokalen Cache werden App-Neustarts verringert.
Der lokale Cache trägt dazu bei, speicherbezogene Neustarts von Apps zu vermeiden. Ihre App kann jedoch aufgrund von geplanten Upgrades der VM-Infrastruktur weiterhin neu gestartet werden. Die App sollte bei aktiviertem lokalen Cache insgesamt jedoch seltener neu gestartet werden.

### <a name="does-local-cache-exclude-any-directories-from-being-copied-to-the-faster-local-drive"></a>Gibt es Verzeichnisse, die für den lokalen Cache nicht auf das schnellere lokale Laufwerk kopiert werden?
Bei dem Schritt, in dem der Speicherinhalt kopiert wird, werden alle Ordner mit dem Namen „repository“ ausgeschlossen. Dies hilft bei Szenarien, in denen eine Website ein Quellcodeverwaltungs-Repository enthält, das für den täglichen Betrieb der App möglicherweise nicht benötigt wird. 

### <a name="how-to-flush-the-local-cache-logs-after-a-site-management-operation"></a>Wie werden die Protokolle im lokalen Cache nach einem Standortverwaltungsvorgang geleert?
Zum Leeren der Protokolle im lokalen Cache beenden Sie die App, und starten Sie sie erneut. Durch diese Aktion wird der alte Cache gelöscht. 
