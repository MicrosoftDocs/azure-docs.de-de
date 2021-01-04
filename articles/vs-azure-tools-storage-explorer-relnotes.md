---
title: Anmerkungen zu dieser Version vom Microsoft Azure Storage-Explorer
description: Lesen Sie die Versionshinweise zur aktuellen Version von Microsoft Azure Storage-Explorer. Weiter unten finden Sie auch die Versionshinweise zu früheren Versionen.
services: storage
documentationcenter: na
author: cawaMS
manager: paulyuk
editor: ''
ms.assetid: ''
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/12/2018
ms.author: cawa
ms.openlocfilehash: 7c250f1351d83d213c5c7af5b00d7a648c5d8bc6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499661"
---
# <a name="microsoft-azure-storage-explorer-release-notes"></a>Versionshinweise zu Microsoft Azure Storage-Explorer

Dieser Artikel enthält die aktuellen Versionshinweise für Azure Storage-Explorer, sowie Versionshinweise für frühere Versionen. 

Beim [Microsoft Azure Storage-Explorer](./vs-azure-tools-storage-manage-with-storage-explorer.md) handelt es sich um eine eigenständige App, über die Sie ganz einfach mit Azure Storage-Daten arbeiten können – unter Windows, macOS und Linux.

Wenn Sie frühere Versionen von Storage-Explorer herunterladen möchten, können Sie die Seite [Releases](https://github.com/microsoft/AzureStorageExplorer/releases) unseres GitHub-Repositorys besuchen.

## <a name="version-1110"></a>Version 1.11.0
4.11.2019

### <a name="new"></a>Neu
* Vorgänge für Blobs, ADLS Gen2 und Managed Disks verwenden das integrierte AzCopy. Konkret werden die folgenden Vorgänge mithilfe von AzCopy ausgeführt:
   * BLOBs
      * Offen für Bearbeiten und Hochladen
      * Hochladen, einschließlich Drag & Drop
      * Download
      * Kopieren und Einfügen #1249
      * Löschen
   * ADLS Gen2-Blobs
      * Hochladen, einschließlich Drag & Drop
      * Download
      * Kopieren und Einfügen
      * Löschen, einschließlich Löschen von Ordnern
   * Managed Disks
      * Upload
      * Download
      * Kopieren und Einfügen

   Außerdem wurden der integrierten AzCopy-Oberfläche einige häufig angeforderte Features hinzugefügt:
   * Konfliktlösungen – Sie werden nur während Übertragungen aufgefordert, Konflikte zu löschen. #1455
   * Als Seitenblobs hochladen – Sie können auswählen, ob AzCopy VHD-Dateien und VHDX-Dateien als Seitenblobs hochlädt. #1164 und #1601
   * Konfigurierbare AzCopy-Parameter – Es wurde eine Reihe von Einstellungen zum Optimieren der Leistung und Ressourcennutzung von AzCopy hinzugefügt. Weitere Einzelheiten finden Sie unten.

* Um den ADLS Gen2- und Multi-Protokollzugriff für Blobs zu ermöglichen und die ADLS Gen2-Erfahrung weiter zu verbessern, haben wir für die ADLS Gen2-Konten die folgenden Features hinzugefügt:
   * Suchen mithilfe von Anzeigenamen, um Berechtigungen für Zugriffssteuerungslisten festzulegen
   * Anzeigen von ausgeblendeten Containern wie $logs und $web
   * Abrufen und Unterbrechen von Containerleases
   * Abrufen und Unterbrechen von Blob-Leases #848
   * Verwalten von Zugriffsrichtlinien für Container
   * Konfigurieren von Blob-Zugriffsebenen
   * Kopieren und Einfügen von Blobs

* In dieser Version gibt es eine Vorschau von 17 zusätzlichen Sprachen. Sie können auf der Seite für Einstellungen unter „Anwendung“ → „Regionale Einstellungen“ → „Sprache (Vorschau)“ zu einer Sprache Ihrer Wahl wechseln. Wir arbeiten weiterhin daran, weitere Zeichenfolgen zu übersetzen und die Übersetzungsqualität zu verbessern. Wenn Sie Feedback zu einer Übersetzung haben oder feststellen, dass eine Zeichenfolge noch nicht übersetzt wurde, [öffnen Sie ein Issue auf GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%F0%9F%8C%90%20localization&template=bug-report.md&title=).
* In jeder Release versuchen wir, ein Onboarding einiger Einstellungen durchzuführen, um Storage-Explorer zu optimieren. Diese Version haben wir um Einstellungen für das weitere Konfigurieren von AzCopy sowie für das Ausblenden von Dienstknoten erweitert:
   * AzCopy-Bandbreitenbegrenzung – hiermit kann gesteuert werden, in welchem Umfang AzCopy das Netzwerk nutzt. Sie finden diese Einstellung unter „Übertragungen“ → „AzCopy“ → „Maximale Übertragungsrate“. #1099
   * AzCopy MD5-Überprüfung – hiermit können Sie konfigurieren ob und wie strikt AzCopy beim Download eine Überprüfung auf MD5-Hashes durchführt. Sie finden diese Einstellung unter „Übertragungen“ → „AzCopy“ → „MD5 überprüfen“.
   * Parallelität und Arbeitsspeicher-Puffergröße von AzCopy – AzCopy analysiert standardmäßig Ihren Computer und bestimmt sinnvolle Standardwerte für diese Einstellungen. Sollten jedoch Leistungsprobleme auftreten, können Sie mithilfe dieser fortgeschrittenen Einstellungen die Ausführung von AzCopy auf Ihrem Computer weiter anpassen. Sie finden diese Einstellungen unter „Übertragungen“ → „AzCopy“. #994
   * Einblenden und Ausblenden von Dienstknoten – diese Einstellungen bieten Ihnen Optionen zum Ein- und Ausblenden der von Storage-Explorer unterstützten Azure-Dienste. Sie finden diese Einstellungen im Abschnitt „Dienste“. #1877

* Beim Erstellen einer Momentaufnahme eines verwalteten Datenträgers wird nun ein Standardname angegeben. #1847
* Wenn Sie mit Azure AD einen ADLS Gen2 Blob-Container anfügen, wird „(ADLS Gen2)“ neben dem Knoten angezeigt. #1861

### <a name="fixes"></a>Fehlerbehebungen
* Beim Kopieren, Hochladen oder Herunterladen großer Datenträger widerrief Storage-Explorer in einigen Fällen nicht den Zugriff auf die am Vorgang beteiligten Datenträger. Dies wurde behoben. #2048
* Beim Anzeigen einer Partitionsschlüsselabfrage trat ein Tabellenstatistikfehler auf. Dies wurde behoben. #1886

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer 1.11.0 erfordert nun einen DFS-Endpunkt (wie „myaccount.dfs.core.windows.net“) zum Anfügen an ADLS Gen2-Container. In früheren Versionen von Storage-Explorer konnte ein Blob-Endpunkt verwendet werden. Diese Anfügungen funktionieren nach dem Upgrade auf 1.11.0 nicht mehr. Sollte dieses Problem auftreten, müssen Sie die Anfügung mit dem DFS-Endpunkt erneut ausführen.
* Es wird nicht überprüft, ob numerische Einstellungen in einem gültigen Bereich liegen. #2140
* Beim Kopieren von Blobcontainern aus einem Speicherkonto in ein anderes Speicherkonto in der Strukturansicht kann ein Fehler auftreten. Wir untersuchen das Problem. #2124
* Die Einstellung „Automatische Aktualisierung“ wirkt sich noch nicht auf alle Vorgänge im Blob-Explorer aus.
* Die Features von verwalteten Datenträgern werden in Azure Stack nicht unterstützt.
* Wenn beim Hochladen oder Einfügen eines Datenträgers ein Fehler auftritt und vor dem Fehler ein neuer Datenträger erstellt wurde, löscht Storage-Explorer den Datenträger nicht für Sie.
* Je nachdem, wann Sie das Hochladen oder Einfügen eines Datenträgers abbrechen, ist es möglich, den neuen Datenträger in einem beschädigten Zustand zu belassen. In diesem Fall müssen Sie entweder den neuen Datenträger löschen oder die Datenträger-APIs manuell aufrufen, um den Inhalt des Datenträgers so zu ersetzen, dass er nicht mehr beschädigt ist.
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
   * Managed Disks
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.

## <a name="previous-releases"></a>Vorgängerversionen

* [Version 1.10.1](#version-1101)
* [Version 1.10.0](#version-1100)
* [Version 1.9.0](#version-190)
* [Version 1.8.1](#version-181)
* [Version 1.8.0](#version-180)
* [Version 1.7.0](#version-170)
* [Version 1.6.2](#version-162)
* [Version 1.6.1](#version-161)
* [Version 1.6.0](#version-160)
* [Version 1.5.0](#version-150)
* [Version 1.4.4](#version-144)
* [Version 1.4.3](#version-143)
* [Version 1.4.2](#version-142)
* [Version 1.4.1](#version-141)
* [Version 1.3.0](#version-130)
* [Version 1.2.0](#version-120)
* [Version 1.1.0](#version-110)
* [Version 1.0.0](#version-100)
* [Version 0.9.6](#version-096)
* [Version 0.9.5](#version-095)
* [Version 0.9.4 und 0.9.3](#version-094-and-093)
* [Version 0.9.2](#version-092)
* [Version 0.9.1 und 0.9.0](#version-091-and-090)
* [Version 0.8.16](#version-0816)
* [Version 0.8.14](#version-0814)
* [Version 0.8.13](#version-0813)
* [Version 0.8.12, 0.8.11 und 0.8.10](#version-0812-and-0811-and-0810)
* [Version 0.8.9 und 0.8.8](#version-089-and-088)
* [Version 0.8.7](#version-087)
* [Version 0.8.6](#version-086)
* [Version 0.8.5](#version-085)
* [Version 0.8.4](#version-084)
* [Version 0.8.3](#version-083)
* [Version 0.8.2](#version-082)
* [Version 0.8.0](#version-080)
* [Version 0.7.20160509.0](#version-07201605090)
* [Version 0.7.20160325.0](#version-07201603250)
* [Version 0.7.20160129.1](#version-07201601291)
* [Version 0.7.20160105.0](#version-07201601050)
* [Version 0.7.20151116.0](#version-07201511160)

## <a name="version-1101"></a>Version 1.10.1
19.09.2019

### <a name="hotfix"></a>Hotfix
* Bei einigen Benutzern ist in 1.10.0 ein Problem aufgetreten, als sie versuchen, die Daten in ihren ADLS Gen 1-Konten anzuzeigen. Dieser Fehler hat das ordnungsgemäße Rendering des Explorer-Bereichs verhindert. Dies wurde behoben. #1853 #1865

### <a name="new"></a>Neu
* Storage-Explorer verfügt jetzt über eine dedizierte Benutzeroberfläche für Einstellungen. Sie können darauf entweder über „Bearbeiten → Einstellungen“ oder durch Klicken auf das Symbol „Einstellungen“ (das Zahnrad) auf der linken vertikalen Symbolleiste zugreifen. Dieses Feature ist der erste Schritt, der zum Bereitstellen einer Vielzahl von [vom Benutzer angeforderten Einstellungen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) erforderlich ist. Ab dieser Version werden die folgenden Einstellungen unterstützt:
  * Design
  * Proxy
  * Beim Beenden abmelden #6
  * Aktivieren der Gerätecodeflow-Anmeldung
  * Automatische Aktualisierung #1526
  * Aktivieren von AzCopy
  * AzCopy SAS-Dauer: Wenn weitere Einstellungen hinzugefügt werden sollen, [öffnen Sie ein Problem in GitHub mit der Beschreibung der gewünschten Einstellung](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage-Explorer unterstützt jetzt verwalteter Datenträger. Ihre Möglichkeiten:
  * Hochladen einer lokalen VHD auf einen neuen Datenträger
  * Herunterladen eines Datenträgers
  * Kopieren und Einfügen von Datenträgern in Ressourcengruppen und Regionen
  * Löschen von Datenträgern
  * Erstellen einer Momentaufnahme eines Datenträgers

Das Hochladen, Herunterladen und regionsübergreifende Kopieren von Datenträgern wird durch AzCopy v10 unterstützt.
* Storage-Explorer kann jetzt über den Snap Store unter Linux installiert werden. Wenn die Installation über den Snap Store erfolgt, werden alle Abhängigkeiten für Sie installiert, einschließlich .NET Core. Derzeit haben wir überprüft, ob Storage-Explorer unter Ubuntu und CentOS ordnungsgemäß ausgeführt wird. Wenn bei der Installation aus dem Snap Store für andere Linux-Distributionen Probleme auftreten, [öffnen Sie ein Problem in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Weitere Informationen zur Installation aus dem Snap Store finden Sie im [Leitfaden für erste Schritte](./vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux). #68
* Es wurden zwei wichtige Änderungen vorgenommen, die an Azure Active Directory (Azure AD) angefügt werden sollen, um das Feature für ADLS Gen2-Benutzer hilfreicher zu gestalten:
  * Sie wählen jetzt den Mandanten aus, in dem sich die von Ihnen anzufügende Ressource befindet. Dies bedeutet, dass Sie keinen RBAC-Zugriff mehr auf das Abonnement der Ressource haben müssen.
  * Wenn Sie einen ADLS Gen2-Blobcontainer anfügen, können Sie ihn jetzt an einen bestimmten Pfad im Container anfügen.
* Bei der Verwaltung von ACLs für ADLS Gen2-Dateien und -Ordner zeigt Storage-Explorer jetzt die Anzeigenamen für Objekte in der ACL an. #957
* Beim Hinzufügen über OID zu einer ADLS Gen2 ACL bestätigt Storage-Explorer jetzt, dass die OID zu einer gültigen Entität in Ihrem Mandanten gehört. #1603
* Die Tastenkombinationen für die Navigation zwischen den Registerkarten verwenden jetzt mehr Standardtastenkombinationen. #1018
* Wenn Sie mit der mittleren Maustaste auf eine Registerkarte klicken, wird diese jetzt geschlossen. #1348
* Wenn eine AzCopy-Übertragung Auslassungen und keine Fehler enthält, zeigt Storage-Explorer jetzt ein Warnsymbol an, um anzuzeigen, dass Auslassungen stattgefunden haben. #1490
* Das integrierte AzCopy wurde auf Version 10.2.1 aktualisiert. Darüber hinaus können Sie jetzt die installierte Version von AzCopy im Dialogfeld „Info“ anzeigen. #1343

### <a name="fixes"></a>Fehlerbehebungen
* Viele Benutzer sind bei der Arbeit mit angefügten Speicherkonten auf verschiedene Fehler vom Typ „Version von Undefiniert kann nicht gelesen werden“ oder „Verbindung von Undefiniert kann nicht gelesen werden“ gestoßen. Obwohl wir die Ursache dieses Problems weiterhin untersuchen, haben wir in 1.10.0 die Fehlerbehandlung beim Laden von angefügten Speicherkonten verbessert. #1626, #985 und #1532
* Die Explorer-Struktur (auf der linken Seite) konnte in einen Zustand wechseln, in dem der Fokus wiederholt zum obersten Knoten springt. Dies wurde behoben. #1596
* Bei der Verwaltung der Momentaufnahmen eines Blobs würden Sprachausgaben nicht den mit der Momentaufnahme verbundenen Zeitstempel lesen. Dies wurde behoben. #1202
* Die Proxyeinstellung unter macOS wurde nicht rechtzeitig so festgelegt, dass sie vom Authentifizierungsprozess verwendet werden konnte. Dies wurde behoben. #1567
* Wenn ein Speicherkonto in einer Sovereign Cloud mit Namen und Schlüssel angefügt wurde, würde AzCopy nicht funktionieren. Dies wurde behoben. #1544
* Wenn das Anfügen über eine Verbindungszeichenfolge erfolgt, entfernt Storage-Explorer jetzt nachgestellte Leerzeichen. #1387

### <a name="known-issues"></a>Bekannte Probleme
* Die Einstellung „Automatische Aktualisierung“ wirkt sich noch nicht auf alle Vorgänge im Blob-Explorer aus.
* Die Features von verwalteten Datenträgern werden in Azure Stack nicht unterstützt.
* Wenn beim Hochladen oder Einfügen eines Datenträgers ein Fehler auftritt und vor dem Fehler ein neuer Datenträger erstellt wurde, löscht Storage-Explorer den Datenträger nicht für Sie.
* Je nachdem, wann Sie das Hochladen oder Einfügen eines Datenträgers abbrechen, ist es möglich, den neuen Datenträger in einem beschädigten Zustand zu belassen. In diesem Fall müssen Sie entweder den neuen Datenträger löschen oder die Datenträger-APIs manuell aufrufen, um den Inhalt des Datenträgers so zu ersetzen, dass er nicht mehr beschädigt ist.
* Je nachdem, wann Sie das Hochladen oder Einfügen eines Datenträgers abbrechen, ist es möglich, den neuen Datenträger in einem beschädigten Zustand zu belassen. In diesem Fall müssen Sie entweder den neuen Datenträger löschen oder die Datenträger-APIs manuell aufrufen, um den Inhalt des Datenträgers so zu ersetzen, dass er nicht mehr beschädigt ist.
* Beim Herunterladen eines Nicht-AzCopy-Blobs wird MD5 bei größeren Dateien nicht überprüft. Ursache hierfür ist ein Fehler im Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
   * Managed Disks
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.


## <a name="version-1100"></a>Version 1.10.0
9/12/2019

### <a name="new"></a>Neu

* Storage-Explorer verfügt jetzt über eine dedizierte Benutzeroberfläche für Einstellungen. Sie können darauf entweder über „Bearbeiten → Einstellungen“ oder durch Klicken auf das Symbol „Einstellungen“ (das Zahnrad) auf der linken vertikalen Symbolleiste zugreifen. Dieses Feature ist der erste Schritt, der zum Bereitstellen einer Vielzahl von [vom Benutzer angeforderten Einstellungen](https://github.com/microsoft/AzureStorageExplorer/labels/%3Abulb%3A%20setting%20candidate) erforderlich ist. Ab dieser Version werden die folgenden Einstellungen unterstützt:
    * Design
    * Proxy
    * Beim Beenden abmelden [#6](https://www.github.com/Microsoft/AzureStorageExplorer/issues/6)
    * Aktivieren der Gerätecodeflow-Anmeldung
    * Automatische Aktualisierung [#1526](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1526)
    * Aktivieren von AzCopy
    * AzCopy SAS-Dauer

    Wenn weitere Einstellungen hinzugefügt werden sollen, [öffnen Sie ein Problem in GitHub mit der Beschreibung der gewünschten Einstellung](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=%3Abulb%3A%20setting%20candidate&template=feature_request.md&title=).
* Storage-Explorer unterstützt jetzt verwalteter Datenträger. Ihre Möglichkeiten:
    * Hochladen einer lokalen VHD auf einen neuen Datenträger
    * Herunterladen eines Datenträgers
    * Kopieren und Einfügen von Datenträgern in Ressourcengruppen und Regionen
    * Löschen von Datenträgern
    * Erstellen einer Momentaufnahme eines Datenträgers

    Das Hochladen, Herunterladen und regionsübergreifende Kopieren von Datenträgern wird durch AzCopy v10 unterstützt.
* Storage-Explorer kann jetzt über den Snap Store unter Linux installiert werden. Wenn die Installation über den Snap Store erfolgt, werden alle Abhängigkeiten für Sie installiert, einschließlich .NET Core. Derzeit haben wir überprüft, ob Storage-Explorer unter Ubuntu und CentOS ordnungsgemäß ausgeführt wird. Wenn bei der Installation aus dem Snap Store für andere Linux-Distributionen Probleme auftreten, [öffnen Sie ein Problem in GitHub](https://github.com/microsoft/AzureStorageExplorer/issues/new?assignees=&labels=snaps&template=bug-report.md&title=). Weitere Informationen zur Installation aus dem Snap Store finden Sie im [Leitfaden für erste Schritte](./vs-azure-tools-storage-manage-with-storage-explorer.md). [#68](https://www.github.com/Microsoft/AzureStorageExplorer/issues/68)
* Es wurden zwei wichtige Änderungen vorgenommen, die an Azure Active Directory (Azure AD) angefügt werden sollen, um das Feature für ADLS Gen2-Benutzer hilfreicher zu gestalten: * Sie wählen jetzt den Mandanten aus, in dem sich die von Ihnen anzufügende Ressource befindet. Dies bedeutet, dass Sie keinen RBAC-Zugriff mehr auf das Abonnement der Ressource haben müssen.
        * Wenn Sie einen ADLS Gen2-Blobcontainer anfügen, können Sie ihn jetzt an einen bestimmten Pfad im Container anfügen.
* Bei der Verwaltung von ACLs für ADLS Gen2-Dateien und -Ordner zeigt Storage-Explorer jetzt die Anzeigenamen für Objekte in der ACL an. [#957](https://www.github.com/Microsoft/AzureStorageExplorer/issues/957)
* Beim Hinzufügen über OID zu einer ADLS Gen2 ACL bestätigt Storage-Explorer jetzt, dass die OID zu einer gültigen Entität in Ihrem Mandanten gehört. [#1603](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1603)
* Die Tastenkombinationen für die Navigation zwischen den Registerkarten verwenden jetzt mehr Standardtastenkombinationen. [#1018](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1018)
* Wenn Sie mit der mittleren Maustaste auf eine Registerkarte klicken, wird diese jetzt geschlossen. [#1348](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1348)
* Wenn eine AzCopy-Übertragung Auslassungen und keine Fehler enthält, zeigt Storage-Explorer jetzt ein Warnsymbol an, um anzuzeigen, dass Auslassungen stattgefunden haben. [#1490](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1490)
* Das integrierte AzCopy wurde auf Version 10.2.1 aktualisiert. Darüber hinaus können Sie jetzt die installierte Version von AzCopy im Dialogfeld „Info“ anzeigen. [#1343](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1343)

### <a name="fixes"></a>Fehlerbehebungen

* Viele Benutzer sind bei der Arbeit mit angefügten Speicherkonten auf verschiedene Fehler vom Typ „Version von Undefiniert kann nicht gelesen werden“ oder „Verbindung von Undefiniert kann nicht gelesen werden“ gestoßen. Obwohl wir die Ursache dieses Problems weiterhin untersuchen, haben wir in 1.10.0 die Fehlerbehandlung beim Laden von angefügten Speicherkonten verbessert. [#1626](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1626), [#985](https://www.github.com/Microsoft/AzureStorageExplorer/issues/985) und [#1532](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1532)
* Die Explorer-Struktur (auf der linken Seite) konnte in einen Zustand wechseln, in dem der Fokus wiederholt zum obersten Knoten springt. Dies wurde behoben. [#1596](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1596)
* Bei der Verwaltung der Momentaufnahmen eines Blobs würden Sprachausgaben nicht den mit der Momentaufnahme verbundenen Zeitstempel lesen. Dies wurde behoben. [#1202](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1202)
* Die Proxyeinstellung unter macOS wurde nicht rechtzeitig so festgelegt, dass sie vom Authentifizierungsprozess verwendet werden konnte. Dies wurde behoben. [#1567](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1567)
* Wenn ein Speicherkonto in einer Sovereign Cloud mit Namen und Schlüssel angefügt wurde, würde AzCopy nicht funktionieren. Dies wurde behoben. [#1544](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1544)
* Wenn das Anfügen über eine Verbindungszeichenfolge erfolgt, entfernt Storage-Explorer jetzt nachgestellte Leerzeichen. [#1387](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1387)

### <a name="known-issues"></a>Bekannte Probleme

* Die Einstellung „Automatische Aktualisierung“ wirkt sich noch nicht auf alle Vorgänge im Blob-Explorer aus.
* Die Features von verwalteten Datenträgern werden in Azure Stack nicht unterstützt.
* Wenn beim Hochladen oder Einfügen eines Datenträgers ein Fehler auftritt und vor dem Fehler ein neuer Datenträger erstellt wurde, löscht Storage-Explorer den Datenträger nicht für Sie.
* Je nachdem, wann Sie das Hochladen oder Einfügen eines Datenträgers abbrechen, ist es möglich, den neuen Datenträger in einem beschädigten Zustand zu belassen. In diesem Fall müssen Sie entweder den neuen Datenträger löschen oder die Datenträger-APIs manuell aufrufen, um den Inhalt des Datenträgers so zu ersetzen, dass er nicht mehr beschädigt ist.
* Beim Herunterladen eines Nicht-AzCopy-Blobs wird MD5 bei größeren Dateien nicht überprüft. Ursache hierfür ist ein Fehler im Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
   * Managed Disks
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.

## <a name="version-190"></a>Version 1.9.0
7/1/2019

### <a name="download-azure-storage-explorer-190"></a>Herunterladen von Azure Storage-Explorer 1.9.0
- [Azure Storage-Explorer 1.9.0 für Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage-Explorer 1.9.0 für Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage-Explorer 1.9.0 für Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Neu

* Sie können jetzt Blob-Container über Azure AD (RBAC oder ACL-Berechtigungen) anfügen. Diese Funktion soll Benutzern helfen, die Zugriff auf Container, aber nicht auf Speicherkonten haben, in denen sich die Container befinden. Weitere Informationen zu diesem Feature finden Sie in unserem Leitfaden für erste Schritte.
* Das Abrufen und Abbrechen von Leases funktioniert jetzt mit RBAC. [#1354](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1354)
* Das Verwalten von Richtlinien für den Zugriff und die Einrichtung öffentlicher Zugriffsebenen funktioniert jetzt mit RBAC. [#1355](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1355)
* Das Löschen von Blob-Ordnern funktioniert jetzt mit RBAC. [#1450](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1450)
* Die Änderung der Blob-Zugriffsebene funktioniert jetzt mit RBAC. [#1446](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1446)
* Sie können den Schnellzugriff jetzt schnell über „Hilfe“ → „Zurücksetzen“ zurücksetzen. [#1327](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1327)

### <a name="preview-features"></a>Previewfunktionen

* In der Vorschauversion ist nun die Gerätecodeflussanmeldung verfügbar. Wenn Sie diese aktivieren möchten, navigieren Sie zu „Preview“ → „Use Device Code Flow Sign-in“ (Gerätecodeflussanmeldung verwenden). Benutzern, die Probleme mit leeren Anmeldefenstern hatten, empfehlen wir die Verwendung dieses Features, da dieses sich als zuverlässigere Anmeldeform herausstellen dürfte.
* In der Vorschauversion ist der Storage-Explorer momentan als Integration in AzCopy verfügbar. Wenn Sie ihn aktivieren möchten, navigieren Sie zu „Preview“ → „Use AzCopy for Improved Blob Upload and Download“ (AzCopy für verbessertes Hoch- und Herunterladen von Blobs verwenden). Blob-Übertragungen mithilfe von AzCopy sollten schneller und leistungsstärker erfolgen.

### <a name="fixes"></a>Fehlerbehebungen

* Ein Problem wurde behoben, bei dem nicht mehr als 50 Abonnements für ein Konto geladen werden konnten. [#1416](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1416)
* Ein Problem wurde behoben, bei dem die Schaltfläche „Anmelden“ in der Infoleiste, die erscheint, wenn ein direkter Link fehlschlägt, nicht funktioniert hat. [#1358](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1358)
* Ein Problem wurde behoben, bei dem .app-Dateien nicht auf macOS hochgeladen werden konnten. [#1119](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1119)
* Ein Problem wurde behoben, bei dem „Alle wiederholen“ bei einer fehlerhaften Blob-Umbenennung nicht funktioniert hat. [#992](https://www.github.com/Microsoft/AzureStorageExplorer/issues/992)
* Ein Problem wurde behoben, bei dem „Abbrechen“ nicht beim Öffnen eines Blobs funktioniert hat. [#1464](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1464)
* Mehrere Rechtschreibe- und ToolTip-Probleme wurden im gesamten Produkt behoben. Vielen Dank an alle, die uns diese Probleme gemeldet haben! [#1303](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1303), [#1328](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1328), [#1329](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1329), [#1331](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1331), [#1336](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1336), [#1352](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1352), [#1368](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1368), [#1395](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1395)

### <a name="known-issues"></a>Bekannte Probleme

* Beim Herunterladen eines Nicht-AzCopy-Blobs wird MD5 bei größeren Dateien nicht überprüft. Ursache hierfür ist ein Fehler im Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Der Versuch, auf ADLS Gen2-Blobs zuzugreifen, wenn man sich hinter einem Proxy befindet, schlägt möglicherweise fehl.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.

## <a name="version-181"></a>Version 1.8.1
13.5.2019

### <a name="hotfixes"></a>Hotfixes
* In einigen Fällen würde das Klicken auf „Mehr laden“ auf Ressourcenebene nicht die nächste Seite mit Ressourcen zurückgeben. Dies wurde behoben. [#1359](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1359)
* Unter Windows schlagen AzCopy-Downloads fehl, wenn eine einzelne Datei oder ein einzelner Ordner heruntergeladen wird und der Name der Datei oder des Ordners ein Zeichen enthält, das für einen Windows-Pfad ungültig ist. Dies wurde behoben. [#1350](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1350)
* In sehr seltenen Fällen konnte es während des Umbenennens einer Dateifreigabe oder der Umbenennung innerhalb einer Dateifreigabe, wenn die Kopien für die Umbenennung fehlschlugen oder wenn Storage-Explorer den Erfolg der Kopien mit Azure nicht bestätigen konnte, potenziell dazu kommen, dass Storage-Explorer die Originaldateien löschte, bevor der Kopiervorgang abgeschlossen war. Dies wurde behoben.

### <a name="new"></a>Neu

* Die integrierte Version von AzCopy wurde auf Version 10.1.0 aktualisiert.
* Strg/Cmd+R können nun verwendet werden, um den derzeit fokussierten Editor zu aktualisieren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Die Version der Azure Stack Storage-API wurde in 2017-04-17 geändert.
* Im Dialogfeld zum Verwalten des Zugriffs für ADLS Gen2 wird die Maske jetzt auf ähnliche Weise wie bei anderen Tools für POSIX-Berechtigung synchron gehalten. Zudem werden Sie über die Benutzeroberfläche gewarnt, wenn eine Änderung vorgenommen wird, die dazu führt, dass die Berechtigungen eines Benutzers oder einer Gruppe die Grenzen der Maske überschreitet. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Bei AzCopy-Uploads ist nun das Flag zum Berechnen und Festlegen des MD5-Hashs aktiviert. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Previewfunktionen

* In der Vorschauversion ist nun die Gerätecodeflussanmeldung verfügbar. Wenn Sie diese aktivieren möchten, navigieren Sie zu „Preview“ → „Use Device Code Flow Sign-in“ (Gerätecodeflussanmeldung verwenden). Benutzern, die Probleme mit leeren Anmeldefenstern hatten, empfehlen wir die Verwendung dieses Features, da dieses sich als zuverlässigere Anmeldeform herausstellen dürfte.
* In der Vorschauversion ist der Storage-Explorer momentan als Integration in AzCopy verfügbar. Wenn Sie ihn aktivieren möchten, navigieren Sie zu „Preview“ → „Use AzCopy for Improved Blob Upload and Download“ (AzCopy für verbessertes Hoch- und Herunterladen von Blobs verwenden). Blob-Übertragungen mithilfe von AzCopy sollten schneller und leistungsstärker erfolgen.

### <a name="fixes"></a>Fehlerbehebungen

* Mit dem Dialogfeld für Zugriffsrichtlinien wird für Speicherzugriffsrichtlinien ohne Ablaufdatum kein Ablaufdatum mehr festgelegt. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Am Dialogfeld „SAS generieren“ wurden einige Änderungen vorgenommen, um sicherzustellen, dass die gespeicherten Zugriffsrichtlinien beim Generieren einer SAS ordnungsgemäß verwendet werden. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Beim Hochladen einer ausgerichteten Nicht-512-Byte-Datei auf ein Seitenblob wird im Storage-Explorer jetzt eine aussagekräftigere Fehlermeldung angezeigt. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Beim Kopieren eines Blobcontainers mit einem Anzeigenamen ist bisher ein Fehler aufgetreten. Daher wird nun der tatsächliche Name des Blobcontainers verwendet. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Beim Durchführen bestimmter Aktionen für einen ADLS Gen2-Ordner, dessen Name Unicode-Zeichen enthält, ist bisher ein Fehler aufgetreten. Nun sollten alle Aktionen funktionieren. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekannte Probleme

* Beim Herunterladen eines Nicht-AzCopy-Blobs wird MD5 bei größeren Dateien nicht überprüft. Ursache hierfür ist ein Fehler im Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Der Versuch, auf ADLS Gen2-Blobs zuzugreifen, wenn man sich hinter einem Proxy befindet, schlägt möglicherweise fehl.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.

## <a name="version-180"></a>Version 1.8.0
1.5.2019

### <a name="new"></a>Neu

* Die integrierte Version von AzCopy wurde auf Version 10.1.0 aktualisiert.
* Strg/Cmd+R können nun verwendet werden, um den derzeit fokussierten Editor zu aktualisieren. [#1097](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1097)
* Die Version der Azure Stack Storage-API wurde in 2017-04-17 geändert.
* Im Dialogfeld zum Verwalten des Zugriffs für ADLS Gen2 wird die Maske jetzt auf ähnliche Weise wie bei anderen Tools für POSIX-Berechtigung synchron gehalten. Zudem werden Sie über die Benutzeroberfläche gewarnt, wenn eine Änderung vorgenommen wird, die dazu führt, dass die Berechtigungen eines Benutzers oder einer Gruppe die Grenzen der Maske überschreitet. [#1253](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1253)
* Bei AzCopy-Uploads ist nun das Flag zum Berechnen und Festlegen des MD5-Hashs aktiviert. [#1223](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1223)


### <a name="preview-features"></a>Previewfunktionen

* In der Vorschauversion ist nun die Gerätecodeflussanmeldung verfügbar. Wenn Sie diese aktivieren möchten, navigieren Sie zu „Preview“ → „Use Device Code Flow Sign-in“ (Gerätecodeflussanmeldung verwenden). Benutzern, die Probleme mit leeren Anmeldefenstern hatten, empfehlen wir die Verwendung dieses Features, da dieses sich als zuverlässigere Anmeldeform herausstellen dürfte.
* In der Vorschauversion ist der Storage-Explorer momentan als Integration in AzCopy verfügbar. Wenn Sie ihn aktivieren möchten, navigieren Sie zu „Preview“ → „Use AzCopy for Improved Blob Upload and Download“ (AzCopy für verbessertes Hoch- und Herunterladen von Blobs verwenden). Blob-Übertragungen mithilfe von AzCopy sollten schneller und leistungsstärker erfolgen.

### <a name="fixes"></a>Fehlerbehebungen

* Mit dem Dialogfeld für Zugriffsrichtlinien wird für Speicherzugriffsrichtlinien ohne Ablaufdatum kein Ablaufdatum mehr festgelegt. [#764](https://www.github.com/Microsoft/AzureStorageExplorer/issues/764)
* Am Dialogfeld „SAS generieren“ wurden einige Änderungen vorgenommen, um sicherzustellen, dass die gespeicherten Zugriffsrichtlinien beim Generieren einer SAS ordnungsgemäß verwendet werden. [#1269](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1269)
* Beim Hochladen einer ausgerichteten Nicht-512-Byte-Datei auf ein Seitenblob wird im Storage-Explorer jetzt eine aussagekräftigere Fehlermeldung angezeigt. [#1050](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1050)
* Beim Kopieren eines Blobcontainers mit einem Anzeigenamen ist bisher ein Fehler aufgetreten. Daher wird nun der tatsächliche Name des Blobcontainers verwendet. [#1166](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1166)
* Beim Durchführen bestimmter Aktionen für einen ADLS Gen2-Ordner, dessen Name Unicode-Zeichen enthält, ist bisher ein Fehler aufgetreten. Nun sollten alle Aktionen funktionieren. [#980](https://www.github.com/Microsoft/AzureStorageExplorer/issues/980)

### <a name="known-issues"></a>Bekannte Probleme

* Beim Herunterladen eines Nicht-AzCopy-Blobs wird MD5 bei größeren Dateien nicht überprüft. Ursache hierfür ist ein Fehler im Storage SDK. [#1212](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1212)
* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Der Versuch, auf ADLS Gen2-Blobs zuzugreifen, wenn man sich hinter einem Proxy befindet, schlägt möglicherweise fehl.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
   * ADLS Gen2
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Bevor Sie Storage-Explorer unter Linux ausführen können, müssen Sie zunächst bestimmte Abhängigkeiten installieren. Weitere Informationen finden Sie im [Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md?tabs=1804#linux-dependencies) zum Storage-Explorer.

## <a name="version-170"></a>Version 1.7.0
05.03.2019

### <a name="download-azure-storage-explorer-170"></a>Herunterladen von Azure Storage-Explorer 1.7.0
- [Azure Storage-Explorer 1.7.0 für Windows](https://go.microsoft.com/fwlink/?LinkId=708343)
- [Azure Storage-Explorer 1.7.0 für Mac](https://go.microsoft.com/fwlink/?LinkId=708342)
- [Azure Storage-Explorer 1.7.0 für Linux](https://go.microsoft.com/fwlink/?LinkId=722418)

### <a name="new"></a>Neu

* Sie können nun den Besitzer und die besitzende Gruppe bei der Zugriffsverwaltung eines ADLS Gen2-Containers, einer ADLS Gen2-Datei oder eines ADLS Gen2-Ordners ändern.
* Die Aktualisierung des Storage-Explorers innerhalb des Produkts ist unter Windows jetzt eine inkrementelle Installation. Dies sollte Aktualisierungsvorgänge insgesamt verkürzen. Wenn Sie eine Neuinstallation vorziehen, können Sie den [Installer](https://azure.microsoft.com/features/storage-explorer/) selbst herunterladen und dann manuell installieren. #1089

### <a name="preview-features"></a>Previewfunktionen

* In der Vorschauversion ist nun die Gerätecodeflussanmeldung verfügbar. Wenn Sie diese aktivieren möchten, navigieren Sie zu „Preview“ → „Use Device Code Flow Sign-in“ (Gerätecodeflussanmeldung verwenden). Benutzern, die Probleme mit leeren Anmeldefenstern hatten, empfehlen wir die Verwendung dieses Features, da dieses sich als zuverlässigere Anmeldeform herausstellen dürfte. #938
* In der Vorschauversion ist der Storage-Explorer momentan als Integration in AzCopy verfügbar. Wenn Sie ihn aktivieren möchten, navigieren Sie zu „Preview“ → „Use AzCopy for Improved Blob Upload and Download“ (AzCopy für verbessertes Hoch- und Herunterladen von Blobs verwenden). Blob-Übertragungen mithilfe von AzCopy sollten schneller und leistungsstärker erfolgen.

### <a name="fixes"></a>Fehlerbehebungen

* Wenn AzCopy aktiviert wurde, können Sie nun den Blob-Typen auswählen, den Sie hochladen möchten. #1111
* Wenn Sie zuvor statische Websites für ein ADLS Gen2-Speicherkonto aktiviert hatten, und diesem dann Name und Schlüssel anfügten, konnte der Storage-Explorer nicht erkennen, dass hierarchische Namespaces aktiviert waren. Dies wurde behoben. #1081
* Das Sortieren im Blob-Editor nach „retention days remaining“ (verbleibende Tage für die Aufbewahrung) oder „Status“ war fehlerhaft. Dies wurde behoben. #1106
* Nach 1.5.0 wartete der Storage-Explorer nicht mehr darauf, dass serverseitige Kopien abgeschlossen wurden, bevor er einen Erfolg während einer Umbenennung oder eines Copy&Paste-Vorgangs meldete. Dies wurde behoben. #976
* Bei der Verwendung des experimentellen AzCopy-Features, konnte der Befehl zum Kopieren nach dem Klicken auf „Copy command to clipboard“ (Befehl in die Zwischenablage kopieren) nicht immer eigenständig ausgeführt werden. Jetzt werden alle Befehle kopiert, für die die Übertragung manuell ausgeführt werden muss. #1079
* Bisher konnten Sie nicht auf ADLS Gen2-Blobs zugreifen, wenn Sie sich hinter einem Proxy befanden. Dies war einem Fehler in einer neuen Netzwerkbibliothek geschuldet, der vom Storage SDK verwendet wurde. In 1.7.0 wurde bereits versucht, dieses Problem zu lösen, teilweise kann es aber weiterhin zu Problemen kommen. In einem zukünftigen Update wird das Problem vollständig gelöst werden. #1090
* In 1.7.0 kann das Dialogfeld „Datei speichern“ nun den korrekten Speicherort bestimmen, an dem Sie eine Datei das letzte Mal abgespeichert haben. #16
* Im Eigenschaftenbereich wurde der SKU-Tarif eines Speicherkontos als Kontotyp angezeigt. Dies wurde behoben. #654
* Manchmal konnte das Leasen eines Blobs nicht unterbrochen werden, auch nicht, wenn Sie den Namen des Blobs korrekt eingaben. Dies wurde behoben. #1070

### <a name="known-issues"></a>Bekannte Probleme

* Bei der Verwendung der RBAC erfordert der Storage-Explorer mehrere Berechtigungen auf Verwaltungsebene, damit er auf Ihre Speicherressourcen zugreifen kann. Weitere Informationen finden Sie im [Azure Storage-Explorer – Leitfaden zur Problembehandlung](./storage/common/storage-explorer-troubleshooting.md).
* Der Versuch, auf ADLS Gen2-Blobs zuzugreifen, wenn man sich hinter einem Proxy befindet, schlägt möglicherweise fehl.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Siehe #537 für weitere Informationen.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie unter #537.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-162"></a>Version 1.6.2
9.1.2019

### <a name="hotfixes"></a>Hotfixes
* In Version 1.6.1 wurden Entitäten, die ADLS Gen2-ACLs nach ObjectId hinzugefügt wurden und keine Benutzer waren, immer als Gruppen hinzugefügt. Jetzt werden nur Gruppen als Gruppen hinzugefügt, und Entitäten wie Unternehmensanwendungen und Dienstprinzipale werden als Benutzer hinzugefügt. [#1049](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1049)
* Wenn ein ADLS Gen2-Speicherkonto keine Container hatte und mit Name und Schlüssel angefügt wurde, hat der Storage-Explorer nicht erkannt, dass es sich um ein ADLS Gen2-Speicherkonto handelt. Dies wurde behoben. [#1048](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1048)
* In Version 1.6.0 wurde bei Konflikten beim Kopieren und Einfügen keine Lösung angefordert. Stattdessen führte der Kopiervorgang mit dem Konflikt einfach zu einem Fehler. Jetzt werden Sie beim ersten Konflikt aufgefordert, die gewünschte Lösung anzugeben. [#1014](https://www.github.com/Microsoft/AzureStorageExplorer/issues/1014)
* Aufgrund von API-Einschränkungen wurden sämtliche Validierungen von Objekt-IDs im Dialogfeld „Zugriff verwalten“ deaktiviert. Die Validierung erfolgt nun ausschließlich für Benutzer-UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Im Dialogfeld „Zugriff verwalten“ von ADLS Gen2 konnten die Berechtigungen für eine Gruppe nicht geändert werden. Dies wurde behoben. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Unterstützung für das Hochladen per Drag & Drop wurde im ADLS Gen2-Editor hinzugefügt. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* In der URL-Eigenschaft im Eigenschaftendialogfeld für ADLS Gen2-Dateien und -Ordner fehlte manchmal ein „/“. Dies wurde behoben. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Wenn beim Abrufen der aktuellen Berechtigungen für einen Container, eine Datei oder einen Ordner in ADLS Gen2 ein Fehler auftritt, wird dieser Fehler nun ordnungsgemäß im Aktivitätsprotokoll angezeigt. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Der zum Öffnen von Dateien erstellte temporäre Pfad wurde gekürzt, um die Wahrscheinlichkeit der Erstellung eines Pfads, der länger als MAX_PATH für Windows ist, zu verringern. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Das Dialogfeld „Verbinden“ wird jetzt ordnungsgemäß angezeigt, wenn keine Benutzer angemeldet sind und keine Ressourcen angefügt wurden. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 wurde beim Speichern von Eigenschaften für Nicht-HNS-Blobs und -Dateien der Wert jeder Eigenschaft codiert. Dies führte zu unnötigen Codierungen von Werten, die nur ASCII-Zeichen enthielten. Jetzt werden die Werte nur codiert, wenn sie Nicht-ASCII-Zeichen enthalten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Das Hochladen eines Ordners in einen Nicht-HNS-Blobcontainer führte zu einem Fehler, wenn eine SAS verwendet wurde, die über keine Leseberechtigungen verfügte. Dies wurde behoben. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Das Abbrechen einer AzCopy-Übertragung funktionierte nicht. Dies wurde behoben. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy verursachte einen Fehler, wenn versucht wurde, einen Ordner aus einem ADLS Gen2-Blobcontainer herunterzuladen, und der Ordner Leerzeichen im Namen enthielt. Dies wurde behoben. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Der Cosmos DB-Editor war in Version 1.6.0 fehlerhaft. Dies wurde jetzt behoben. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Neu

* Sie können Storage-Explorer jetzt für den Zugriff auf Ihre Blobdaten per [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) verwenden. Wenn Sie angemeldet sind und Storage-Explorer die Schlüssel für Ihr Speicherkonto nicht abrufen kann, wird für die Authentifizierung bei der Interaktion mit Ihren Daten ein OAuth-Token verwendet.
* Storage-Explorer unterstützt jetzt ADLS Gen2-Speicherkonten. Wenn Storage-Explorer erkennt, dass ein hierarchischer Namespace für ein Speicherkonto aktiviert wurde, wird neben dem Namen Ihres Speicherkontos „(ADLS Gen2 Preview)“ angezeigt. Storage-Explorer kann erkennen, ob ein hierarchischer Namespace aktiviert wurde, wenn Sie sich anmelden oder Ihr Speicherkonto mit Namen und Schlüssel angefügt haben. Sie können den Storage-Explorer auch für ADLS Gen2-Speicherkonten verwenden:
  * Erstellen und Löschen von Containern
  * Verwalten von Containereigenschaften und -berechtigungen (linke Seite)
  * Anzeigen von Daten in Containern und Navigieren zu diesen
  * Erstellen neuer Ordner
  * Hochladen, Herunterladen, Umbenennen und Löschen von Dateien und Ordnern
  * Verwalten von Eigenschaften und Berechtigungen von Dateien und Ordnern (rechte Seite).
    
    Andere typische Blobfunktionen wie vorläufiges Löschen und Momentaufnahmen sind aktuell noch nicht verfügbar. Das Verwalten von Berechtigungen ist ebenfalls nur möglich, wenn Sie angemeldet sind. Darüber hinaus verwendet Storage-Explorer bei der Arbeit in einem ADLS Gen2-Speicherkonto AzCopy für sämtliche Uploads und Downloads. Dabei nutzt er standardmäßig die Namen und Schlüsselanmeldeinformationen für alle Vorgänge (sofern verfügbar).
* Nach deutlichem Benutzerfeedback kann „Lease abbrechen“ wieder zum Unterbrechen von Leases in mehreren Blobs gleichzeitig verwendet werden.

### <a name="known-issues"></a>Bekannte Probleme

* Wenn beim Herunterladen aus einem ADLS Gen2-Speicherkonto eine der übertragenen Dateien bereits vorhanden ist, stürzt AzCopy manchmal ab. Dies wird mit einem zukünftigen Hotfix behoben.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie unter #537.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-161"></a>Version 1.6.1
18.12.2018

### <a name="hotfixes"></a>Hotfixes
* Aufgrund von API-Einschränkungen wurden sämtliche Validierungen von Objekt-IDs im Dialogfeld „Zugriff verwalten“ deaktiviert. Die Validierung erfolgt nun ausschließlich für Benutzer-UPNs. [#954](https://www.github.com/Microsoft/AzureStorageExplorer/issues/954)
* Im Dialogfeld „Zugriff verwalten“ von ADLS Gen2 konnten die Berechtigungen für eine Gruppe nicht geändert werden. Dies wurde behoben. [#958](https://www.github.com/Microsoft/AzureStorageExplorer/issues/958)
* Unterstützung für das Hochladen per Drag & Drop wurde im ADLS Gen2-Editor hinzugefügt. [#953](https://www.github.com/Microsoft/AzureStorageExplorer/issues/953)
* In der URL-Eigenschaft im Eigenschaftendialogfeld für ADLS Gen2-Dateien und -Ordner fehlte manchmal ein „/“. Dies wurde behoben. [#960](https://www.github.com/Microsoft/AzureStorageExplorer/issues/960)
* Wenn beim Abrufen der aktuellen Berechtigungen für einen Container, eine Datei oder einen Ordner in ADLS Gen2 ein Fehler auftritt, wird dieser Fehler nun ordnungsgemäß im Aktivitätsprotokoll angezeigt. [#965](https://www.github.com/Microsoft/AzureStorageExplorer/issues/965)
* Der zum Öffnen von Dateien erstellte temporäre Pfad wurde gekürzt, um die Wahrscheinlichkeit der Erstellung eines Pfads, der länger als MAX_PATH für Windows ist, zu verringern. [#93](https://www.github.com/Microsoft/AzureStorageExplorer/issues/93)
* Das Dialogfeld „Verbinden“ wird jetzt ordnungsgemäß angezeigt, wenn keine Benutzer angemeldet sind und keine Ressourcen angefügt wurden. [#944](https://www.github.com/Microsoft/AzureStorageExplorer/issues/944)
* In 1.6.0 wurde beim Speichern von Eigenschaften für Nicht-HNS-Blobs und -Dateien der Wert jeder Eigenschaft codiert. Dies führte zu unnötigen Codierungen von Werten, die nur ASCII-Zeichen enthielten. Jetzt werden die Werte nur codiert, wenn sie Nicht-ASCII-Zeichen enthalten. [#986](https://www.github.com/Microsoft/AzureStorageExplorer/issues/986)
* Das Hochladen eines Ordners in einen Nicht-HNS-Blobcontainer führte zu einem Fehler, wenn eine SAS verwendet wurde, die über keine Leseberechtigungen verfügte. Dies wurde behoben. [#970](https://www.github.com/Microsoft/AzureStorageExplorer/issues/970)
* Das Abbrechen einer AzCopy-Übertragung funktionierte nicht. Dies wurde behoben. [#943](https://www.github.com/Microsoft/AzureStorageExplorer/issues/943)
* AzCopy verursachte einen Fehler, wenn versucht wurde, einen Ordner aus einem ADLS Gen2-Blobcontainer herunterzuladen, und der Ordner Leerzeichen im Namen enthielt. Dies wurde behoben. [#990](https://www.github.com/Microsoft/AzureStorageExplorer/issues/990)
* Der Cosmos DB-Editor war in Version 1.6.0 fehlerhaft. Dies wurde jetzt behoben. [#950](https://www.github.com/Microsoft/AzureStorageExplorer/issues/950)
        
### <a name="new"></a>Neu

* Sie können Storage-Explorer jetzt für den Zugriff auf Ihre Blobdaten per [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) verwenden. Wenn Sie angemeldet sind und Storage-Explorer die Schlüssel für Ihr Speicherkonto nicht abrufen kann, wird für die Authentifizierung bei der Interaktion mit Ihren Daten ein OAuth-Token verwendet.
* Storage-Explorer unterstützt jetzt ADLS Gen2-Speicherkonten. Wenn Storage-Explorer erkennt, dass ein hierarchischer Namespace für ein Speicherkonto aktiviert wurde, wird neben dem Namen Ihres Speicherkontos „(ADLS Gen2 Preview)“ angezeigt. Storage-Explorer kann erkennen, ob ein hierarchischer Namespace aktiviert wurde, wenn Sie sich anmelden oder Ihr Speicherkonto mit Namen und Schlüssel angefügt haben. Sie können den Storage-Explorer auch für ADLS Gen2-Speicherkonten verwenden:
  * Erstellen und Löschen von Containern
  * Verwalten von Containereigenschaften und -berechtigungen (linke Seite)
  * Anzeigen von Daten in Containern und Navigieren zu diesen
  * Erstellen neuer Ordner
  * Hochladen, Herunterladen, Umbenennen und Löschen von Dateien und Ordnern
  * Verwalten von Eigenschaften und Berechtigungen von Dateien und Ordnern (rechte Seite).
    
    Andere typische Blobfunktionen wie vorläufiges Löschen und Momentaufnahmen sind aktuell noch nicht verfügbar. Das Verwalten von Berechtigungen ist ebenfalls nur möglich, wenn Sie angemeldet sind. Darüber hinaus verwendet Storage-Explorer bei der Arbeit in einem ADLS Gen2-Speicherkonto AzCopy für sämtliche Uploads und Downloads. Dabei nutzt er standardmäßig die Namen und Schlüsselanmeldeinformationen für alle Vorgänge (sofern verfügbar).
* Nach deutlichem Benutzerfeedback kann „Lease abbrechen“ wieder zum Unterbrechen von Leases in mehreren Blobs gleichzeitig verwendet werden.

### <a name="known-issues"></a>Bekannte Probleme

* Wenn beim Herunterladen aus einem ADLS Gen2-Speicherkonto eine der übertragenen Dateien bereits vorhanden ist, stürzt AzCopy manchmal ab. Dies wird mit einem zukünftigen Hotfix behoben.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie unter #537.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-160"></a>Version 1.6.0
5.12.2018

### <a name="new"></a>Neu

* Sie können Storage-Explorer jetzt für den Zugriff auf Ihre Blobdaten per [RBAC](./storage/common/storage-auth-aad-rbac-portal.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) verwenden. Wenn Sie angemeldet sind und Storage-Explorer die Schlüssel für Ihr Speicherkonto nicht abrufen kann, wird für die Authentifizierung bei der Interaktion mit Ihren Daten ein OAuth-Token verwendet.
* Storage-Explorer unterstützt jetzt ADLS Gen2-Speicherkonten. Wenn Storage-Explorer erkennt, dass ein hierarchischer Namespace für ein Speicherkonto aktiviert wurde, wird neben dem Namen Ihres Speicherkontos „(ADLS Gen2 Preview)“ angezeigt. Storage-Explorer kann erkennen, ob ein hierarchischer Namespace aktiviert wurde, wenn Sie sich anmelden oder Ihr Speicherkonto mit Namen und Schlüssel angefügt haben. Sie können den Storage-Explorer auch für ADLS Gen2-Speicherkonten verwenden:
  * Erstellen und Löschen von Containern
  * Verwalten von Containereigenschaften und -berechtigungen (linke Seite)
  * Anzeigen von Daten in Containern und Navigieren zu diesen
  * Erstellen neuer Ordner
  * Hochladen, Herunterladen, Umbenennen und Löschen von Dateien und Ordnern
  * Verwalten von Eigenschaften und Berechtigungen von Dateien und Ordnern (rechte Seite).
    
    Andere typische Blobfunktionen wie vorläufiges Löschen und Momentaufnahmen sind aktuell noch nicht verfügbar. Das Verwalten von Berechtigungen ist ebenfalls nur möglich, wenn Sie angemeldet sind. Darüber hinaus verwendet Storage-Explorer bei der Arbeit in einem ADLS Gen2-Speicherkonto AzCopy für sämtliche Uploads und Downloads. Dabei nutzt er standardmäßig die Namen und Schlüsselanmeldeinformationen für alle Vorgänge (sofern verfügbar).
* Nach deutlichem Benutzerfeedback kann „Lease abbrechen“ wieder zum Unterbrechen von Leases in mehreren Blobs gleichzeitig verwendet werden.

### <a name="known-issues"></a>Bekannte Probleme

* Wenn beim Herunterladen aus einem ADLS Gen2-Speicherkonto eine der übertragenen Dateien bereits vorhanden ist, stürzt AzCopy manchmal ab. Dies wird mit einem zukünftigen Hotfix behoben.
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie unter #537.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-150"></a>Version 1.5.0
29.10.2018

### <a name="new"></a>Neu

* Sie können ab sofort [AzCopy v10 (Vorschau)](https://github.com/Azure/azure-storage-azcopy) zum Hochladen und Herunterladen von Blobs verwenden. Um dieses Feature zu aktivieren, wechseln Sie zum Menü „Experimentell“, und klicken Sie dann auf „AzCopy für verbesserten Upload und Download von Blobs verwenden“. Sofern aktiviert, wird AzCopy in den folgenden Szenarien verwendet:
   * Hochladen von Ordnern und Dateien in Blobcontainer, entweder über die Symbolleiste oder per Drag & Drop.
   * Herunterladen von Ordnern und Dateien, entweder über die Symbolleiste oder über das Kontextmenü.

* Zusätzlich gilt bei Verwendung von AzCopy Folgendes:
   * Sie können den AzCopy-Befehl zum Ausführen der Übertragung in die Zwischenablage kopieren. Klicken Sie im Aktivitätsprotokoll einfach auf „AzCopy-Befehl in Zwischenablage kopieren“.
   * Sie müssen den Blob-Editor nach dem Hochladen manuell aktualisieren.
   * Das Hochladen von Dateien zum Anfügen von Blobs wird nicht unterstützt. VHD-Dateien werden als Seitenblobs hochgeladen, alle anderen Dateien werden als Blockblobs hochgeladen.
   * Fehler und Konflikte, die während eines Uploads oder Downloads auftreten, werden erst nach dem Abschluss von Upload oder Download angezeigt.

Eine Unterstützung der Verwendung von AzCopy mit Dateifreigaben ist geplant.
* Der Storage-Explorer verwendet ab sofort Electron 2.0.11.
* Das Abbrechen von Leases kann jetzt immer nur für ein Blob durchgeführt werden. Zusätzlich müssen Sie den Namen des Blobs eingeben, deren Lease sie abbrechen möchten. Diese Änderung wurde vorgenommen, um die Wahrscheinlichkeit des versehentlichen Abbrechens einer Lease zu verringern, insbesondere bei VMs. #394
* Falls jemals Anmeldeprobleme auftreten sollten, können Sie jetzt die Authentifizierung zurücksetzen. Wechseln Sie zum Hilfemenü, und klicken Sie auf „Zurücksetzen“, um auf diese Funktion zuzugreifen. #419

### <a name="fix"></a>Fix

* Aufgrund von zahlreichem Benutzerfeedback wurde der standardmäßige Emulatorknoten reaktiviert. Sie können über das Dialogfeld „Verbinden“ weiterhin zusätzliche Emulatorverbindungen hinzufügen, aber wenn Ihr Emulator zur Verwendung der Standardports konfiguriert ist, können Sie auch den Knoten „Emulator * Standardports“ unter „Lokale und verbundene Speicherkonten“ verwenden. #669
* Der Storage-Explorer lässt es nicht mehr zu, dass Blobmetadaten festgelegt werden, die führende oder nachgestellte Leerzeichen aufweisen. #760
* Auf einigen Seiten des Dialogfelds „Verbinden“ war die Schaltfläche „Anmelden“ immer aktiviert. Die Schaltfläche ist nun bei Bedarf deaktiviert. #761
* Der Schnellzugriff löst keinen Fehler in der Konsole mehr aus, wenn keine Schnellzugriffelemente hinzugefügt wurden.

### <a name="known-issues"></a>Bekannte Probleme

* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie unter #537.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie einen Kommentar zu diesem Problem.
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert. Um dieses Problem beim Hochladen oder Herunterladen aus einem Blobcontainer zu umgehen, können Sie das experimentelle AzCopy-Feature verwenden.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Die folgenden Features werden von Azure Stack nicht unterstützt. Der Versuch, diese Features bei der Arbeit mit Azure Stack-Ressourcen zu verwenden, kann zu unerwarteten Fehlern führen.
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-144"></a>Version 1.4.4
10/15/2018

### <a name="hotfixes"></a>Hotfixes
* Die API-Version der Azure-Ressourcenverwaltung wurde zurückgesetzt, um die Azure US Government-Benutzer zu entsperren. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Wartekreisel für Ladevorgänge verwenden jetzt CSS-Animationen, um die vom Storage-Explorer verwendete GPU-Menge zu reduzieren. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Neu
* Externe Ressourcenanlagen, z.B. für SAS-Verbindungen und Emulatoren, wurden erheblich verbessert. Sie können jetzt:
   * Anpassen des Anzeigenamens der Ressource, die angefügt wird. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ausführen von Anfügevorgängen für mehrere lokale Emulatoren unter Verwendung verschiedener Ports. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Hinzufügen angefügter Ressourcen zum Schnellzugriff. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage-Explorer unterstützt jetzt das vorläufige Löschen. Ihre Möglichkeiten:
   * Konfigurieren Sie eine Richtlinie für das vorläufige Löschen, indem Sie mit der rechten Maustaste auf den Knoten „Blobcontainer“ für Ihr Speicherkonto klicken.
   * Zeigen Sie vorläufig gelöschte Blobs im Blob-Editor an, indem Sie im Dropdownmenü neben der Navigationsleiste auf „Aktive und gelöschte Blobs“ klicken.
   * Stellen Sie vorläufig gelöschte Blobs wieder her.

### <a name="fixes"></a>Fehlerbehebungen
* Die Aktion „CORS-Einstellungen konfigurieren“ ist in Storage Premium-Konten nicht mehr verfügbar, weil Storage Premium-Konten keine Unterstützung für CORS bieten. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Es gibt jetzt eine neue Eigenschaft „Shared Access Signature“ für über SAS angefügte Dienste. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Ab sofort steht die Aktion „Standardzugriffsebene festlegen“ für Blob Storage- und GPV2 Storage-Konten zur Verfügung, die an den Schnellzugriff angeheftet wurden. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Gelegentlich kam es im Storage-Explorer bei der Anzeige von klassischen Speicherkonten zu Fehlern. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekannte Probleme
* Bei der Verwendung von Emulatoren, z.B. Azure-Speicheremulator oder Azurite, müssen Sie diese so einrichten, dass sie über die Standardports auf Verbindungen lauschen. Andernfalls kann per Speicher-Explorer keine Verbindung damit hergestellt werden.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-143"></a>Version 1.4.3
10/11/2018

### <a name="hotfixes"></a>Hotfixes
* Die API-Version der Azure-Ressourcenverwaltung wurde zurückgesetzt, um die Azure US Government-Benutzer zu entsperren. [#696](https://github.com/Microsoft/AzureStorageExplorer/issues/696)
* Wartekreisel für Ladevorgänge verwenden jetzt CSS-Animationen, um die vom Storage-Explorer verwendete GPU-Menge zu reduzieren. [#653](https://github.com/Microsoft/AzureStorageExplorer/issues/653)

### <a name="new"></a>Neu
* Externe Ressourcenanlagen, z.B. für SAS-Verbindungen und Emulatoren, wurden erheblich verbessert. Sie können jetzt:
   * Anpassen des Anzeigenamens der Ressource, die angefügt wird. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ausführen von Anfügevorgängen für mehrere lokale Emulatoren unter Verwendung verschiedener Ports. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Hinzufügen angefügter Ressourcen zum Schnellzugriff. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage-Explorer unterstützt jetzt das vorläufige Löschen. Ihre Möglichkeiten:
   * Konfigurieren Sie eine Richtlinie für das vorläufige Löschen, indem Sie mit der rechten Maustaste auf den Knoten „Blobcontainer“ für Ihr Speicherkonto klicken.
   * Zeigen Sie vorläufig gelöschte Blobs im Blob-Editor an, indem Sie im Dropdownmenü neben der Navigationsleiste auf „Aktive und gelöschte Blobs“ klicken.
   * Stellen Sie vorläufig gelöschte Blobs wieder her.

### <a name="fixes"></a>Fehlerbehebungen
* Die Aktion „CORS-Einstellungen konfigurieren“ ist in Storage Premium-Konten nicht mehr verfügbar, weil Storage Premium-Konten keine Unterstützung für CORS bieten. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Es gibt jetzt eine neue Eigenschaft „Shared Access Signature“ für über SAS angefügte Dienste. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Ab sofort steht die Aktion „Standardzugriffsebene festlegen“ für Blob Storage- und GPV2 Storage-Konten zur Verfügung, die an den Schnellzugriff angeheftet wurden. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Gelegentlich kam es im Storage-Explorer bei der Anzeige von klassischen Speicherkonten zu Fehlern. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekannte Probleme
* Bei der Verwendung von Emulatoren, z.B. Azure-Speicheremulator oder Azurite, müssen Sie diese so einrichten, dass sie über die Standardports auf Verbindungen lauschen. Andernfalls kann per Speicher-Explorer keine Verbindung damit hergestellt werden.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-142"></a>Version 1.4.2
09/24/2018

### <a name="hotfixes"></a>Hotfixes
* Aktualisieren Sie die API-Version der Azure-Ressourcenverwaltung auf 2018-07-01, um Unterstützung für neue Azure Storage-Kontoarten hinzuzufügen. [#652](https://github.com/Microsoft/AzureStorageExplorer/issues/652)

### <a name="new"></a>Neu
* Externe Ressourcenanlagen, z.B. für SAS-Verbindungen und Emulatoren, wurden erheblich verbessert. Sie können jetzt:
   * Anpassen des Anzeigenamens der Ressource, die angefügt wird. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ausführen von Anfügevorgängen für mehrere lokale Emulatoren unter Verwendung verschiedener Ports. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Hinzufügen angefügter Ressourcen zum Schnellzugriff. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage-Explorer unterstützt jetzt das vorläufige Löschen. Ihre Möglichkeiten:
   * Konfigurieren Sie eine Richtlinie für das vorläufige Löschen, indem Sie mit der rechten Maustaste auf den Knoten „Blobcontainer“ für Ihr Speicherkonto klicken.
   * Zeigen Sie vorläufig gelöschte Blobs im Blob-Editor an, indem Sie im Dropdownmenü neben der Navigationsleiste auf „Aktive und gelöschte Blobs“ klicken.
   * Stellen Sie vorläufig gelöschte Blobs wieder her.

### <a name="fixes"></a>Fehlerbehebungen
* Die Aktion „CORS-Einstellungen konfigurieren“ ist in Storage Premium-Konten nicht mehr verfügbar, weil Storage Premium-Konten keine Unterstützung für CORS bieten. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Es gibt jetzt eine neue Eigenschaft „Shared Access Signature“ für über SAS angefügte Dienste. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Ab sofort steht die Aktion „Standardzugriffsebene festlegen“ für Blob Storage- und GPV2 Storage-Konten zur Verfügung, die an den Schnellzugriff angeheftet wurden. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Gelegentlich kam es im Storage-Explorer bei der Anzeige von klassischen Speicherkonten zu Fehlern. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekannte Probleme
* Bei der Verwendung von Emulatoren, z.B. Azure-Speicheremulator oder Azurite, müssen Sie diese so einrichten, dass sie über die Standardports auf Verbindungen lauschen. Andernfalls kann per Speicher-Explorer keine Verbindung damit hergestellt werden.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-141"></a>Version 1.4.1
28.08.2018

### <a name="hotfixes"></a>Hotfixes
* Beim ersten Start war es im Storage-Explorer nicht möglich, den Schlüssel zum Verschlüsseln sensibler Daten zu generieren. Dies führte zu Problemen bei der Verwendung von „Schnellzugriff“ und beim Anfügen von Ressourcen. [#535](https://github.com/Microsoft/AzureStorageExplorer/issues/535)
* Wenn für Ihr Konto kein MFA für den Startmandanten angefordert wurde, MFA aber für andere Mandanten erforderlich war, konnten im Storage-Explorer keine Abonnements aufgelistet werden. Jetzt werden Sie vom Storage-Explorer nach der Anmeldung bei einem solchen Konto zum erneuten Eingeben Ihrer Anmeldeinformationen und zum Durchlaufen der mehrstufigen Authentifizierung aufgefordert. [#74](https://github.com/Microsoft/AzureStorageExplorer/issues/74)
* Im Storage-Explorer konnten keine Ressourcen aus Azure Deutschland und Azure US Government hinzugefügt werden. [#572](https://github.com/Microsoft/AzureStorageExplorer/issues/572)
* Nach der Anmeldung bei zwei Konten mit derselben E-Mail-Adresse wurden im Storage-Explorer gelegentlich keine Ressourcen in der Strukturansicht angezeigt. [#580](https://github.com/Microsoft/AzureStorageExplorer/issues/580)
* Auf langsameren Windows-Computern dauerte es mitunter sehr lange, bis der Begrüßungsbildschirm angezeigt wurde. [#586](https://github.com/Microsoft/AzureStorageExplorer/issues/586)
* Das Dialogfeld für die Verbindungsherstellung wurde angezeigt, obwohl Konten oder Dienste verbunden waren. [#588](https://github.com/Microsoft/AzureStorageExplorer/issues/588)

### <a name="new"></a>Neu
* Externe Ressourcenanlagen, z.B. für SAS-Verbindungen und Emulatoren, wurden erheblich verbessert. Sie können jetzt:
   * Anpassen des Anzeigenamens der Ressource, die angefügt wird. [#31](https://github.com/Microsoft/AzureStorageExplorer/issues/31)
   * Ausführen von Anfügevorgängen für mehrere lokale Emulatoren unter Verwendung verschiedener Ports. [#193](https://github.com/Microsoft/AzureStorageExplorer/issues/193)
   * Hinzufügen angefügter Ressourcen zum Schnellzugriff. [#392](https://github.com/Microsoft/AzureStorageExplorer/issues/392)
* Storage-Explorer unterstützt jetzt das vorläufige Löschen. Ihre Möglichkeiten:
   * Konfigurieren Sie eine Richtlinie für das vorläufige Löschen, indem Sie mit der rechten Maustaste auf den Knoten „Blobcontainer“ für Ihr Speicherkonto klicken.
   * Zeigen Sie vorläufig gelöschte Blobs im Blob-Editor an, indem Sie im Dropdownmenü neben der Navigationsleiste auf „Aktive und gelöschte Blobs“ klicken.
   * Stellen Sie vorläufig gelöschte Blobs wieder her.

### <a name="fixes"></a>Fehlerbehebungen
* Die Aktion „CORS-Einstellungen konfigurieren“ ist in Storage Premium-Konten nicht mehr verfügbar, weil Storage Premium-Konten keine Unterstützung für CORS bieten. [#142](https://github.com/Microsoft/AzureStorageExplorer/issues/142)
* Es gibt jetzt eine neue Eigenschaft „Shared Access Signature“ für über SAS angefügte Dienste. [#184](https://github.com/Microsoft/AzureStorageExplorer/issues/184)
* Ab sofort steht die Aktion „Standardzugriffsebene festlegen“ für Blob Storage- und GPV2 Storage-Konten zur Verfügung, die an den Schnellzugriff angeheftet wurden. [#229](https://github.com/Microsoft/AzureStorageExplorer/issues/229)
* Gelegentlich kam es im Storage-Explorer bei der Anzeige von klassischen Speicherkonten zu Fehlern. [#323](https://github.com/Microsoft/AzureStorageExplorer/issues/323)

### <a name="known-issues"></a>Bekannte Probleme
* Bei der Verwendung von Emulatoren, z.B. Azure-Speicheremulator oder Azurite, müssen Sie diese so einrichten, dass sie über die Standardports auf Verbindungen lauschen. Andernfalls kann per Speicher-Explorer keine Verbindung damit hergestellt werden.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-130"></a>Version 1.3.0
09.07.2018

### <a name="new"></a>Neu
* Der Zugriff auf $web-Container, die von statischen Websites verwendet werden, wird jetzt unterstützt. Dies ermöglicht ein problemloses Hochladen und Verwalten von Dateien und Ordnern, die von Ihrer Website genutzt werden. [#223](https://github.com/Microsoft/AzureStorageExplorer/issues/223)
* Die App-Leiste unter macOS wurde neu organisiert. Die Änderungen umfassen ein Menü „Datei“, einige Änderungen an den Tastenkombinationen sowie mehrere neue Befehle unter dem App-Menü. [#99](https://github.com/Microsoft/AzureStorageExplorer/issues/99)
* Der Autoritätsendpunkt für die Anmeldung bei Azure US Government wurde in https://login.microsoftonline.us/ geändert.
* Barrierefreiheit: Wenn ein Bildschirmleser aktiv ist, funktioniert die Tastaturnavigation jetzt in den Tabellen, die für die Anzeige von Elementen auf der rechten Seite verwendet werden. Mit den Pfeiltasten können Sie durch Zeilen und Spalten navigieren, mit der Eingabetaste können Sie Standardaktionen aufrufen, mit der Kontextmenütaste das Kontextmenü für ein Element öffnen und mit Shift oder Control die Mehrfachauswahl vornehmen. [#103](https://github.com/Microsoft/AzureStorageExplorer/issues/103)

### <a name="fixes"></a>Fehlerbehebungen
*  Auf einigen Computern dauerte es lange, bis untergeordnete Prozesse gestartet wurden. Dann wurde der Fehler „Untergeordnete Prozesse konnten nicht rechtzeitig gestartet werden“ angezeigt. Die vorgegebene Zeit für das Starten eines untergeordneten Prozesses wurde jetzt von 20 auf 90 Sekunden erhöht. Sollte dieses Problem weiterhin bestehen, erstellen Sie einen Kommentar zum verknüpften GitHub-Thema. [#281](https://github.com/Microsoft/AzureStorageExplorer/issues/281)
* Bei Verwendung einer SAS ohne Leseberechtigungen war es nicht möglich, ein großes Blob hochzuladen. Die Logik für den Upload wurde geändert, um in diesem Szenario zu funktionieren. [#305](https://github.com/Microsoft/AzureStorageExplorer/issues/305)
* Die Einstellung der öffentlichen Zugriffsebene für einen Container entfernte alle Zugriffsrichtlinien und umgekehrt. Jetzt bleiben die öffentliche Zugriffsebene und die Zugriffsrichtlinien erhalten, wenn eine der beiden Einstellungen vorgenommen wird. [#197](https://github.com/Microsoft/AzureStorageExplorer/issues/197)
* „AccessTierChangeTime“ wurde im Dialogfeld „Eigenschaften“ abgeschnitten. Dies wurde behoben. [#145](https://github.com/Microsoft/AzureStorageExplorer/issues/145)
* Das Präfix „Microsoft Azure Storage-Explorer -“ fehlte im Dialogfeld „Neues Verzeichnis erstellen“. Dies wurde behoben. [#299](https://github.com/Microsoft/AzureStorageExplorer/issues/299)
* Barrierefreiheit: Die Navigation im Dialogfeld „Entität hinzufügen“ war bei der Verwendung von VoiceOver kompliziert. Es wurden Verbesserungen vorgenommen. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Barrierefreiheit: Die Hintergrundfarbe der Schaltfläche zum Zuklappen bzw. Aufklappen für den Bereich „Aktionen und Eigenschaften“ war mit ähnlichen Steuerelementen der Benutzeroberfläche im Thema „Hoher Kontrast (Schwarz)“ inkonsistent. Die Farbe wurde geändert. [#123](https://github.com/Microsoft/AzureStorageExplorer/issues/123)
* Barrierefreiheit: Im Thema „Hoher Kontrast (Schwarz)“ war der Fokusstil für die Schaltfläche „X“ im Dialogfeld „Eigenschaften“ nicht sichtbar. Dies wurde behoben. [#243](https://github.com/Microsoft/AzureStorageExplorer/issues/243)
* Barrierefreiheit: In den Registerkarten „Aktionen und Eigenschaften“ fehlten mehrere Ariawerte, was dazu führte, dass der Bildschirmleser nur teilweise angezeigt wurde. Die fehlenden Ariawerte wurden nun hinzugefügt. [#316](https://github.com/Microsoft/AzureStorageExplorer/issues/316)
* Barrierefreiheit: Reduzierte Strukturknoten auf der linken Seite wurden nicht mit einem Aria-erweiterten Wert von FALSEversehen. Dies wurde behoben. [#352](https://github.com/Microsoft/AzureStorageExplorer/issues/352)

### <a name="known-issues"></a>Bekannte Probleme
* Das Trennen einer über einen SAS-URI angefügten Ressource, z.B. eines Blobcontainers, kann zu einem Fehler führen, durch den andere Anlagen nicht ordnungsgemäß angezeigt werden. Aktualisieren Sie zur Umgehung dieses Problems den Gruppenknoten. Weitere Informationen finden Sie in [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/537).
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Azure Stack bietet keine Unterstützung für die folgenden Features, und der Versuch, diese Features bei der Arbeit mit Azure Stack zu verwenden, kann zu unerwarteten Fehlern führen:
   * Dateifreigaben
   * Zugriffsebenen
   * Vorläufiges Löschen
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-120"></a>Version 1.2.0
12.06.2018

### <a name="new"></a>Neu
* Falls mit Storage-Explorer keine Abonnements einer Teilmenge Ihrer Mandanten geladen werden können, werden alle erfolgreich geladenen Abonnements zusammen mit einer Fehlermeldung für die nicht erfolgreichen Mandanten angezeigt. [#159](https://github.com/Microsoft/AzureStorageExplorer/issues/159)
* Unter Windows können Sie jetzt die Option „Update on Close“ (Beim Schließen aktualisieren) wählen, wenn ein Update verfügbar ist. Wenn Sie diese Option auswählen, wird das Installationsprogramm für das Update ausgeführt, nachdem Sie den Speicher-Explorer geschlossen haben. [#21](https://github.com/Microsoft/AzureStorageExplorer/issues/21)
* Die Option „Momentaufnahme wiederherstellen“ wurde dem Kontextmenü des Dateifreigaben-Editors hinzugefügt. Sie ist verfügbar, wenn die Momentaufnahme einer Dateifreigabe angezeigt wird.[#131](https://github.com/Microsoft/AzureStorageExplorer/issues/131)
* Die Schaltfläche „Warteschlangen löschen“ ist jetzt immer aktiviert.[#135](https://github.com/Microsoft/AzureStorageExplorer/issues/135)
* Die Unterstützung für die Anmeldung an ADFS Azure Stack wurde wieder aktiviert. Hierfür ist Azure Stack-Version 1804 oder höher erforderlich. [#150](https://github.com/Microsoft/AzureStorageExplorer/issues/150)

### <a name="fixes"></a>Fehlerbehebungen
* Wenn Sie Momentaufnahmen für eine Dateifreigabe angezeigt haben, deren Name ein Präfix einer anderen Dateifreigabe unter demselben Speicherkonto war, wurden die Momentaufnahmen für die andere Dateifreigabe ebenfalls aufgelistet. Dieses Problem wurde behoben. [#255](https://github.com/Microsoft/AzureStorageExplorer/issues/255)
* Beim Anfügen per SAS hat die Wiederherstellung einer Datei aus der Momentaufnahme einer Dateifreigabe zu einem Fehler geführt. Dieses Problem wurde behoben. [#211](https://github.com/Microsoft/AzureStorageExplorer/issues/211)
* Beim Anzeigen von Momentaufnahmen für ein Blob wurde die Aktion „Momentaufnahme höher stufen“ aktiviert, wenn das Basisblob und eine einzelne Momentaufnahme ausgewählt wurden. Die Aktion wird jetzt nur aktiviert, wenn eine einzelne Momentaufnahme ausgewählt ist. [#230](https://github.com/Microsoft/AzureStorageExplorer/issues/230)
* Wenn ein einzelner Auftrag (z.B. das Herunterladen eines Blobs) gestartet wurde, der dann nicht erfolgreich war, wurden automatisch erneute Versuche unternommen, bis Sie einen anderen Auftrag desselben Typs gestartet haben. Jetzt sollten für alle Aufträge unabhängig davon, wie viele Aufträge sich in der Warteschlange befinden, automatisch erneute Versuche unternommen werden.
* Editoren, die für neu erstellte Blobcontainer unter GPV2- und Blob Storage-Konten geöffnet wurden, verfügten nicht über die Spalte „Zugriffsebene“. Dieses Problem wurde behoben. [#109](https://github.com/Microsoft/AzureStorageExplorer/issues/109)
* Die Spalte „Zugriffsebene“ wurde in einigen Fällen nicht angezeigt, wenn per SAS ein Speicherkonto oder Blobcontainer angefügt wurde. Die Spalte wird jetzt immer angezeigt. Sie enthält aber einen leeren Wert, falls keine Zugriffsebene festgelegt wurde. [#160](https://github.com/Microsoft/AzureStorageExplorer/issues/160)
* Das Festlegen der Zugriffsebene eines neu hochgeladenen Blockblobs wurde deaktiviert. Dieses Problem wurde behoben. [#171](https://github.com/Microsoft/AzureStorageExplorer/issues/171)
* Wenn die Schaltfläche „Registerkarte geöffnet lassen“ über die Tastatur aufgerufen wurde, ging der Tastaturfokus verloren. Jetzt wird der Fokus auf die Registerkarte verschoben, die geöffnet gelassen wurde. [#163](https://github.com/Microsoft/AzureStorageExplorer/issues/163)
* Für eine Abfrage im Abfrage-Generator wurde mit VoiceOver keine nützliche Beschreibung des aktuellen Operators ausgegeben. Die Beschreibung ist jetzt aussagekräftiger. [#207](https://github.com/Microsoft/AzureStorageExplorer/issues/207)
* Die Paginierungslinks für die unterschiedlichen Editoren waren nicht aussagekräftig. Sie wurden in aussagekräftigere Versionen geändert. [#205](https://github.com/Microsoft/AzureStorageExplorer/issues/205)
* Im Dialogfeld „Entität hinzufügen“ wurde per VoiceOver nicht angekündigt, zu welcher Spalte ein Eingabeelement gehörte. Der Name der aktuellen Spalte ist jetzt in der Beschreibung des Elements enthalten. [#206](https://github.com/Microsoft/AzureStorageExplorer/issues/206)
* Optionsfelder und Kontrollkästchen enthielten keinen sichtbaren Rahmen, wenn sie den Fokus besaßen. Dieses Problem wurde behoben. [#237](https://github.com/Microsoft/AzureStorageExplorer/issues/237)

### <a name="known-issues"></a>Bekannte Probleme
* Bei der Verwendung von Emulatoren, z.B. Azure-Speicheremulator oder Azurite, müssen Sie diese so einrichten, dass sie über die Standardports auf Verbindungen lauschen. Andernfalls kann per Speicher-Explorer keine Verbindung damit hergestellt werden.
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-110"></a>Version 1.1.0
09.05.2018

### <a name="new"></a>Neu
* Storage-Explorer unterstützt jetzt Azurite. Hinweis: Die Verbindung mit Azurite ist mit standardmäßigen Entwicklungsendpunkten hartcodiert.
* Storage-Explorer unterstützt jetzt Zugriffsebenen für Blob Storage-Konten und GPv2-Speicherkonten. [Hier finden Sie weitere Informationen zu Zugriffsebenen.](./storage/blobs/storage-blob-storage-tiers.md)
* Beim Generieren einer SAS ist keine Startzeit mehr erforderlich.

### <a name="fixes"></a>Fehlerbehebungen
* Beim Abrufen der Abonnements von US-Behördenkonten traten Fehler auf. Dieses Problem wurde behoben. [#61](https://github.com/Microsoft/AzureStorageExplorer/issues/61)
* Die Ablaufzeit für Zugriffsrichtlinien wurde nicht richtig gespeichert. Dieses Problem wurde behoben. [#50](https://github.com/Microsoft/AzureStorageExplorer/issues/50)
* Beim Generieren einer SAS-URL für ein Containerelement wurde der Name des Elements der URL nicht angefügt. Dieses Problem wurde behoben. [#44](https://github.com/Microsoft/AzureStorageExplorer/issues/44)
* Beim Erstellen einer SAS waren vergangene Ablaufzeiten gelegentlich die Standardwerte. Das war darauf zurückzuführen, dass Storage-Explorer die letzte Start- und Ablaufzeit als Standardwert verwendete. Jetzt wird jedes Mal, wenn Sie das SAS-Dialogfeld öffnen, ein neues Set von Standardwerten generiert. [#35](https://github.com/Microsoft/AzureStorageExplorer/issues/35)
* Beim Kopieren zwischen Speicherkonten wird eine 24 Stunden gültige SAS generiert. Nach diesen 24 Stunden schlugen Kopiervorgänge fehl. Jetzt ist die SAS eine Woche gültig. So treten weniger Fehler beim Kopiervorgang wegen einer abgelaufenen SAS auf. [#62](https://github.com/Microsoft/AzureStorageExplorer/issues/62)
* Bei einigen Aktivitäten löste das Klicken auf „Abbrechen“ nicht immer die entsprechende Aktion aus. Dieses Problem wurde behoben. [#125](https://github.com/Microsoft/AzureStorageExplorer/issues/125)
* Die Übertragungsgeschwindigkeit einiger Aktivitäten war falsch. Dieses Problem wurde behoben. [#124](https://github.com/Microsoft/AzureStorageExplorer/issues/124)
* In der englischen Version war „Previous“ im Menü „View“ (Ansicht) falsch geschrieben. Der Rechtschreibfehler wurde korrigiert. [#71](https://github.com/Microsoft/AzureStorageExplorer/issues/71)
* Auf der letzten Seite von Windows Installer befand sich die Schaltfläche „Weiter“. Diese Schaltfläche heißt nun „Fertig stellen“. [#70](https://github.com/Microsoft/AzureStorageExplorer/issues/70)
* Beim Verwenden des schwarzen Designs wurden auf der Registerkarte fokussierte Schaltflächen in Dialogfeldern nicht angezeigt. Jetzt sind sie sichtbar.[#64](https://github.com/Microsoft/AzureStorageExplorer/issues/64)
* In der englischen Version war die Groß-/Kleinschreibung von „Auto-Resolve“ (Automatisch auflösen) für Aktionen im Aktivitätsprotokoll falsch. Jetzt ist sie richtig. [#51](https://github.com/Microsoft/AzureStorageExplorer/issues/51)
* Beim Löschen einer Entität aus einer Tabelle, zeigte das Dialogfeld, das zum Bestätigen aufforderte, ein Fehlersymbol an. Das Dialogfeld verwendet jetzt ein Warnsymbol. [#148](https://github.com/Microsoft/AzureStorageExplorer/issues/148)

### <a name="known-issues"></a>Bekannte Probleme
* Wenn Sie VS für Mac verwenden und irgendwann eine benutzerdefinierte AAD-Konfiguration erstellt haben, können Sie sich möglicherweise nicht anmelden. Um das Problem zu umgehen, löschen Sie den Inhalt von ~/.IdentityService/AadConfigurations. Wenn der Fehler dadurch nicht behoben wird, schreiben Sie bitte einen Kommentar zu [diesem Problem](https://github.com/Microsoft/AzureStorageExplorer/issues/97).
* Azurite hat noch nicht alle Speicher-APIs vollständig implementiert. Deswegen können unerwartete Fehler oder ein unerwartetes Verhalten auftreten, wenn Azurite als Entwicklungsspeicher verwendet wird.
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Wegen eines NodeJS-Fehlers können keine Inhalte aus dem OneDrive-Ordner hochgeladen werden. Der Fehler wurde behoben, jedoch noch nicht in Electron integriert.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```


## <a name="version-100"></a>Version 1.0.0
16.04.2018

### <a name="new"></a>Neu
* Verbesserte Authentifizierung, durch die Storage-Explorer den gleichen Kontospeicher wie Visual Studio 2017 verwenden kann. Für die Verwendung dieses Features müssen Sie sich erneut bei Ihren Konten anmelden und Ihre gefilterten Abonnements neu festlegen.
* Bei Azure Stack-Konten mit Sicherung durch AAD ruft Storage-Explorer jetzt Azure Stack-Abonnements ab, wenn „Target Azure Stack“ (Ziel-Azure Stack) aktiviert ist. Sie müssen keine benutzerdefinierte Anmeldungsumgebung mehr erstellen.
* Es wurden mehrere Verknüpfungen für eine schnellere Navigation hinzugefügt. Dazu gehören das Umschalten verschiedener Bereiche und das Verschieben zwischen Editoren. Weitere Einzelheiten finden Sie im Menü „Ansicht“.
* Feedback zu Storage-Explorer ist jetzt bei GitHub zu finden. Sie erreichen unsere Seite „Probleme“, indem Sie unten links auf die Schaltfläche „Feedback“ klicken oder [https://github.com/Microsoft/AzureStorageExplorer/issues](https://github.com/Microsoft/AzureStorageExplorer/issues) aufrufen. Sie können gerne Vorschläge machen, Probleme melden, Fragen stellen oder auf andere Weise ein Feedback hinterlassen.
* Wenn Probleme mit Ihrem TLS/SSL-Zertifikat auftreten und Sie das betreffende Zertifikat nicht finden können, können Sie Storage-Explorer jetzt mit dem Flag `--ignore-certificate-errors` über die Befehlszeile starten. Wenn Storage-Explorer mit diesem Flag gestartet wird, ignoriert er TLS/SSL-Zertifikatfehler.
* Das Kontextmenü enthält jetzt die Option „Herunterladen“ für Blob- und Dateielemente.
* Die Barrierefreiheit und Unterstützung von Bildschirmlesegeräten wurden verbessert. Wenn Sie Barrierefreiheitsfeatures benötigen, finden Sie unter [Dokumentationen zu Barrierefreiheit](./vs-azure-tools-storage-explorer-accessibility.md) weitere Informationen.
* Storage-Explorer verwendet jetzt Electron 1.8.3.

### <a name="breaking-changes"></a>Aktuelle Änderungen
* Storage-Explorer hat zu einer neuen Authentifizierungsbibliothek gewechselt. Im Rahmen des Wechsels zu der Bibliothek müssen Sie sich erneut bei Ihren Konten anmelden und Ihre gefilterten Abonnements neu festlegen.
* Die Methode zum Verschlüsseln vertraulicher Daten wurde geändert. Dadurch müssen möglicherweise einige Ihrer Elemente für den Schnellzugriff erneut hinzugefügt und/oder einige Ihrer angefügten Ressourcen erneut angefügt werden.

### <a name="fixes"></a>Fehlerbehebungen
* Bei einigen Benutzern hinter Proxys wurden Uploads oder Downloads der Gruppe „Blob“ durch folgende Fehlermeldung unterbrochen: „Unable to resolve“ (Auflösung nicht möglich). Dieses Problem wurde behoben.
* Wenn bei der Verwendung eines direkten Links eine Anmeldung erforderlich war, wurde durch Klicken auf die Aufforderung „Anmelden“ in einem Popupfenster ein leeres Dialogfeld geöffnet. Dieses Problem wurde behoben.
* Wenn Storage-Explorer nicht gestartet werden kann, da ein GPU-Prozess abgebrochen wird, werden Sie jetzt unter Linux darüber informiert und angewiesen, die Option „--disable-gpu“ zu verwenden. Anschließend wird Storage-Explorer mit der aktivierten Option automatisch neu gestartet.
* Ungültige Zugriffsrichtlinien konnten im Dialogfeld „Zugriffsrichtlinien“ nur schwer identifiziert werden. Ungültige Zugriffsrichtlinien-IDs werden jetzt für bessere Sichtbarkeit in Rot hervorgehoben.
* Zwischen den verschiedenen Bereichen einer Aktivität waren im Aktivitätsprotokoll manchmal viele Leerzeichen enthalten. Dieses Problem wurde behoben.
* Im Abfrage-Editor der Tabelle ist der Editor abgestürzt, wenn Sie eine Zeitstempelklausel in einem ungültigen Status belassen und anschließend versucht haben, eine andere Klausel zu ändern. Der Editor stellt die Zeitstempelklausel jetzt im letzten gültigen Zustand wieder her, wenn in einer anderen Klausel eine Änderung festgestellt wird.
* Wenn Sie während der Eingabe Ihrer Suchabfrage in die Strukturansicht eine Pause gemacht haben, wurde die Suche gestartet und der Fokus vom Textfeld gelenkt. Jetzt müssen Sie die Suche explizit durch Drücken der Eingabetaste oder durch Klicken auf die Schaltfläche „Suche starten“ starten.
* Der Befehl „Get Shared Access Signature“ (Shared Access Signature abrufen) wurde manchmal deaktiviert, wenn mit der rechten Maustaste auf eine Datei in einer Dateifreigabe geklickt wurde. Dieses Problem wurde behoben.
* Wenn der relevante Ressourcenstrukturknoten während der Suche herausgefiltert wurde, konnten Sie mit der Tabulatortaste nicht in die Ressourcenstruktur gelangen und mit den Pfeiltasten nicht durch die Ressourcenstruktur navigieren. Wenn der relevante Ressourcenstrukturknoten ausgeblendet wird, wird der erste Knoten in der Ressourcenstruktur jetzt automatisch als relevant angezeigt.
* In der Editor-Symbolleiste war gelegentlich ein zusätzliches Trennzeichen sichtbar. Dieses Problem wurde behoben.
* Das Breadcrumb-Textfeld war gelegentlich überfüllt. Dieses Problem wurde behoben.
* Der Blob-Editor und der Dateifreigabe-Editor wurden manchmal konstant aktualisiert, wenn viele Dateien gleichzeitig hochgeladen wurden. Dieses Problem wurde behoben.
* Das Feature „Ordnerstatistik“ hatte in der Ansicht „Verwaltung der Momentaufnahmen von Dateifreigaben“keinen Nutzen. Es wurde jetzt deaktiviert.
* Unter Linux wurde das Menü „Datei“ nicht angezeigt. Dieses Problem wurde behoben.
* Wenn Sie einen Ordner in eine Dateifreigabe hochgeladen haben, wurden standardmäßig nur die Inhalte des Ordners hochgeladen. Jetzt werden die Inhalte des Ordners standardmäßig in einen geeigneten Ordner in der Dateifreigabe hochgeladen.
* Die Reihenfolge von Schaltflächen wurde in mehreren Dialogfeldern umgekehrt. Dieses Problem wurde behoben.
* Verschiedene sicherheitsbezogene Fehlerbehebungen.

### <a name="known-issues"></a>Bekannte Probleme
* In seltenen Fällen kann der Fokus in der Struktur beim Schnellzugriff hängen bleiben. Klicken Sie auf „Alle aktualisieren“, um den Fokus zu lösen.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Linux-Benutzer müssen Sie [.NET Core 2.0](https://dotnet.microsoft.com/download/dotnet-core/2.0) installieren.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-096"></a>Version 0.9.6
28.02.2018

### <a name="fixes"></a>Fehlerbehebungen
* Ein Problem hat die Auflistung erwarteter Blobs/Dateien im Editor verhindert. Dieses Problem wurde behoben.
* Ein Problem hat dazu geführt, dass beim Wechseln zwischen Momentaufnahmeansichten Elemente falsch angezeigt wurden. Dieses Problem wurde behoben.

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer unterstützt keine ADFS-Konten.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die [hier](https://github.com/Azure/azure-storage-node/issues/317) beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-095"></a>Version 0.9.5
06.02.2018

### <a name="new"></a>Neu

* Unterstützung für Momentaufnahmen von Dateifreigaben:
    * Erstellen und verwalten Sie Momentaufnahmen für Ihre Dateifreigaben.
    * Wechseln Sie beim Durchsuchen problemlos zwischen den Ansichten der Momentaufnahmen Ihrer Dateifreigaben.
    * Stellen Sie frühere Versionen Ihrer Dateien wieder her.
* Unterstützung der Vorschauversion für Azure Data Lake Store:
    * Stellen Sie Verbindungen mit Ihren ADLS-Ressourcen unter mehreren Konten her.
    * Stellen Sie mit ADL-URIs Verbindungen mit ADLS-Ressourcen her, und geben Sie diese frei.
    * Führen Sie grundlegende Vorgänge für Dateien und Ordner rekursiv aus.
    * Heften Sie einzelne Ordner an den Schnellzugriff an.
    * Zeigen Sie Ordnerstatistiken an.

### <a name="fixes"></a>Fehlerbehebungen
* Leistungsverbesserungen beim Starten
* Verschiedene Fehler behoben.

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer unterstützt keine ADFS-Konten.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer.exe --disable-gpu
    ```

* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-094-and-093"></a>Version 0.9.4 und 0.9.3
01/21/2018

### <a name="new"></a>Neu
* Das vorhandene Storage-Explorer-Fenster wird in folgenden Fällen wiederverwendet:
    * Sie öffnen Links, die im Storage-Explorer generiert wurden.
    * Sie öffnen den Storage-Explorer vom Portal aus.
    * Sie öffnen den Storage-Explorer über die Azure Storage VS Code-Erweiterung (demnächst verfügbar).
* Es wurde die Möglichkeit hinzugefügt, aus dem Storage-Explorer heraus ein neues Storage-Explorer-Fenster zu öffnen.
    * Bei Windows befindet sich im Menü „Datei“ und im Kontextmenü der Taskleiste eine Option „Neues Fenster“.
    * Bei Mac befindet sich im App-Menü eine Option „Neues Fenster“.

### <a name="fixes"></a>Fehlerbehebungen
* Ein Sicherheitsproblem wurde behoben. Führen Sie baldmöglichst das Update auf 0.9.4 durch.
* Alte Aktivitäten wurden nicht ordnungsgemäß bereinigt. Dies wirkte sich auf die Leistung von Aufträgen mit langer Ausführungszeit aus. Diese Aktivitäten werden jetzt ordnungsgemäß bereinigt.
* Aktionen mit großen Mengen an Dateien und Verzeichnissen führten gelegentlich zu einem Einfrieren von Storage-Explorer. Anforderungen an Azure für Dateifreigaben werden jetzt gedrosselt, um die Nutzung von Systemressourcen zu begrenzen.

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer unterstützt keine ADFS-Konten.
* Tastenkombinationen für „Explorer anzeigen“ und „Kontenverwaltung anzeigen“ sollten entsprechend STRG/BEFEHL+UMSCHALT+E oder STRG/BEFEHL+UMSCHALT+A sein.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-092"></a>Version 0.9.2
01.11.2017

### <a name="hotfixes"></a>Hotfixes
* Abhängig von der lokalen Zeitzone waren beim Bearbeiten von Edm.DateTime-Werten für Tabellenentitäten unerwartete Datenänderungen möglich. Der Editor verwendet nun ein Nur-Text-Feld, mit dem die Edm.DateTime-Werte präzise und einheitlich gesteuert werden können.
* Der Upload/Download einer Gruppe von Blobs wurde nicht gestartet, wenn ein Name und Schlüssel angefügt war. Dieses Problem wurde behoben.
* Zuvor wurden Sie von Storage-Explorer nur zur erneuten Authentifizierung eines veralteten Kontos aufgefordert, wenn mindestens ein Abonnement des Kontos ausgewählt wurde. Nun fordert Sie Storage-Explorer auch dazu auf, wenn das Konto vollständig herausgefiltert wurde.
* Die Endpunktdomäne für Azure US Government war falsch. Dies wurde korrigiert.
* Manchmal war es schwierig, im Bereich „Konten verwalten“ auf die Schaltfläche „Anwenden“ zu klicken. Dieses Problem sollte nicht mehr auftreten.

### <a name="new"></a>Neu
* Unterstützung der Vorschau für Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Erstellen von Datenbanken und Sammlungen
    * Bearbeiten von Daten
    * Abfragen, Erstellen oder Löschen von Dokumenten
    * Aktualisieren von gespeicherten Prozeduren, benutzerdefinierten Funktionen oder Triggern
    * Verwenden von Verbindungszeichenfolgen zum Verbinden und Verwalten Ihrer Datenbanken
* Die Leistung beim Hochladen/Herunterladen zahlreicher kleiner Blobs wurde verbessert.
* Es wurde die Aktion „Alle wiederholen“ hinzugefügt, falls in einer Upload- oder Downloadgruppe für Blobs Fehler auftreten.
* Storage-Explorer hält jetzt die Iteration beim Hochladen/Herunterladen von Blobs an, wenn erkennt wird, dass Ihre Netzwerkverbindung unterbrochen ist. Sie können die Iteration dann fortsetzen, sobald die Verbindung wiederhergestellt wurde.
* Die Optionen „Alle schließen“, „Andere schließen“ und „Schließen“ wurden für Registerkarten zum Kontextmenü hinzugefügt.
* Storage-Explorer verwendet jetzt native Dialogfelder und native Kontextmenüs.
* Storage-Explorer ist jetzt leichter zugänglich. Die Verbesserungen umfassen:
    * Verbesserte Unterstützung für die Sprachausgabe für NVDA unter Windows und für VoiceOver unter Mac
    * Verbesserte Designs mit hohem Kontrast
    * Fehlerbehebungen für TAB-TASTE und Tastaturfokus

### <a name="fixes"></a>Fehlerbehebungen
* Wenn Sie versucht haben, einen Blob mit einem ungültigen Windows-Dateinamen zu öffnen oder herunterzuladen, ist bei dem Vorgang ein Fehler aufgetreten. Storage-Explorer erkennt jetzt, ob ein Blobname ungültig ist und fragt Sie, ob Sie den Blob codieren oder überspringen möchten. Storage-Explorer erkennt zudem, ob ein Dateiname codiert ist und fragt Sie, ob Sie den Dateinamen vor dem Hochladen decodieren möchten.
* Der Editor für den Blobzielcontainer würde beim Hochladen des Blobs in einigen Fällen nicht ordnungsgemäß aktualisiert werden. Dieses Problem wurde behoben.
* Die Unterstützung für verschiedene Typen von Verbindungszeichenfolgen und SAS-URIs ist rückläufig. Wir haben alle bekannten Probleme behandelt. Senden Sie jedoch weiterhin Feedback, wenn weitere Probleme auftreten.
* Die Updatebenachrichtigung hat für einige Benutzer in Version 0.9.0 nicht funktioniert. Dieses Problem wurde behoben. Die von diesem Fehler betroffenen Benutzer können die neueste Version von Storage-Explorer [hier](https://azure.microsoft.com/features/storage-explorer/) herunterladen.

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer unterstützt keine ADFS-Konten.
* Tastenkombinationen für „Explorer anzeigen“ und „Kontenverwaltung anzeigen“ sollten entsprechend STRG/BEFEHL+UMSCHALT+E oder STRG/BEFEHL+UMSCHALT+A sein.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-091-and-090"></a>Version 0.9.1 und 0.9.0
10/20/2017
### <a name="new"></a>Neu
* Unterstützung der Vorschau für Azure Cosmos DB:
    * [Onlinedokumentation](./cosmos-db/storage-explorer.md)
    * Erstellen von Datenbanken und Sammlungen
    * Bearbeiten von Daten
    * Abfragen, Erstellen oder Löschen von Dokumenten
    * Aktualisieren von gespeicherten Prozeduren, benutzerdefinierten Funktionen oder Triggern
    * Verwenden von Verbindungszeichenfolgen zum Verbinden und Verwalten Ihrer Datenbanken
* Die Leistung beim Hochladen/Herunterladen zahlreicher kleiner Blobs wurde verbessert.
* Es wurde die Aktion „Alle wiederholen“ hinzugefügt, falls in einer Upload- oder Downloadgruppe für Blobs Fehler auftreten.
* Storage-Explorer hält jetzt die Iteration beim Hochladen/Herunterladen von Blobs an, wenn erkennt wird, dass Ihre Netzwerkverbindung unterbrochen ist. Sie können die Iteration dann fortsetzen, sobald die Verbindung wiederhergestellt wurde.
* Die Optionen „Alle schließen“, „Andere schließen“ und „Schließen“ wurden für Registerkarten zum Kontextmenü hinzugefügt.
* Storage-Explorer verwendet jetzt native Dialogfelder und native Kontextmenüs.
* Storage-Explorer ist jetzt leichter zugänglich. Die Verbesserungen umfassen:
    * Verbesserte Unterstützung für die Sprachausgabe für NVDA unter Windows und für VoiceOver unter Mac
    * Verbesserte Designs mit hohem Kontrast
    * Fehlerbehebungen für TAB-TASTE und Tastaturfokus

### <a name="fixes"></a>Fehlerbehebungen
* Wenn Sie versucht haben, einen Blob mit einem ungültigen Windows-Dateinamen zu öffnen oder herunterzuladen, ist bei dem Vorgang ein Fehler aufgetreten. Storage-Explorer erkennt jetzt, ob ein Blobname ungültig ist und fragt Sie, ob Sie den Blob codieren oder überspringen möchten. Storage-Explorer erkennt zudem, ob ein Dateiname codiert ist und fragt Sie, ob Sie den Dateinamen vor dem Hochladen decodieren möchten.
* Der Editor für den Blobzielcontainer würde beim Hochladen des Blobs in einigen Fällen nicht ordnungsgemäß aktualisiert werden. Dieses Problem wurde behoben.
* Die Unterstützung für verschiedene Typen von Verbindungszeichenfolgen und SAS-URIs ist rückläufig. Wir haben alle bekannten Probleme behandelt. Senden Sie jedoch weiterhin Feedback, wenn weitere Probleme auftreten.
* Die Updatebenachrichtigung hat für einige Benutzer in Version 0.9.0 nicht funktioniert. Dieses Problem wurde behoben. Die von diesem Fehler betroffenen Benutzer können die neueste Version von Storage-Explorer [hier](https://azure.microsoft.com/features/storage-explorer/) herunterladen.

### <a name="known-issues"></a>Bekannte Probleme
* Storage-Explorer unterstützt keine ADFS-Konten.
* Tastenkombinationen für „Explorer anzeigen“ und „Kontenverwaltung anzeigen“ sollten entsprechend STRG/BEFEHL+UMSCHALT+E oder STRG/BEFEHL+UMSCHALT+A sein.
* Beim Hochladen bestimmter Dateien als Anfügeblobs für Azure Stack tritt möglicherweise ein Fehler auf.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Der Grund hierfür ist, dass wir die hier beschriebene Problemumgehung für „Filter abbrechen“ verwenden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Die von Storage-Explorer verwendete Electron-Shell hat Probleme mit einigen GPU-Hardwarebeschleunigern (Grafikprozessor). Wenn Storage-Explorer ein leeres Hauptfenster anzeigt, können Sie versuchen, Storage-Explorer über die Befehlszeile zu starten und die GPU-Beschleunigung durch Hinzufügen des Switches `--disable-gpu` zu deaktivieren:

    ```
    ./StorageExplorer --disable-gpu
    ```

* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

## <a name="version-0816"></a>Version 0.8.16
21.08.2017

### <a name="new"></a>Neu
* Beim Öffnen eines Blobs fordert Storage-Explorer Sie zum Hochladen der heruntergeladenen Datei auf, wenn eine Änderung erkannt wird
* Verbesserter Azure Stack-Anmeldevorgang
* Die Leistung beim gleichzeitigen Hochladen/Herunterladen zahlreicher kleiner Dateien wurde verbessert.


### <a name="fixes"></a>Fehlerbehebungen
* Bei einigen Blobtypen führt die Auswahl von „Ersetzen“ während eines Uploadkonflikts manchmal zu einem Neustart des Uploads.
* In Version 0.8.15 wurden Uploads manchmal bei 99 Prozent angehalten.
* Beim Hochladen von Dateien in eine Dateifreigabe trat beim Upload ein Fehler auf, wenn Sie ein noch nicht vorhandenes Verzeichnis ausgewählt haben.
* Der Storage-Explorer hat falsche Zeitstempel für Shared Access Signatures und Tabellenabfragen generiert.


### <a name="known-issues"></a>Bekannte Probleme
* Derzeit kann keine Verbindungszeichenfolge mit Name und Schlüssel verwendet werden. Dies wird im nächsten Release behoben. Bis dahin können Sie die Anfügefunktion mit Name und Schlüssel verwenden.
* Wenn Sie versuchen, eine Datei mit einem ungültigen Windows-Dateinamen zu öffnen, wird für den Download der Fehler „Datei nicht gefunden“ angezeigt.
* Nach dem Klicken auf „Abbrechen“ für eine Aufgabe kann es eine Weile dauern, bis die betreffende Aufgabe abgebrochen wird. Dies ist eine Einschränkung der Azure Storage-Knotenbibliothek.
* Nach dem Abschluss des Blob-Uploads wird die Registerkarte aktualisiert, von der der Upload initiiert wurde. Dies ist eine Änderung gegenüber dem früheren Verhalten; sie bewirkt zudem, dass Sie wieder in das Stammverzeichnis des Containers gelangen, in dem Sie sich befinden.
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird.
* Im Bereich mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Für Benutzer unter Ubuntu 14.04 müssen Sie sicherstellen, dass GCC auf dem neuesten Stand ist. Hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

* Für Benutzer unter Ubuntu 17.04 müssen Sie GConf installieren; hierzu können Sie die folgenden Befehle ausführen und anschließend Ihren Computer neu starten:

    ```
    sudo apt-get install libgconf-2-4
    ```

### <a name="version-0814"></a>Version 0.8.14
22.06.2017

### <a name="new"></a>Neu

* Die Electron-Version wurde auf 1.7.2 aktualisiert, sodass verschiedene wichtige Sicherheitsupdates genutzt werden können
* Sie können nun rasch über das Hilfemenü auf den Onlineleitfaden zur Problembehandlung zugreifen
* Storage-Explorer – [Leitfaden zur Problembehandlung][2]
* [Anweisungen][3] zum Herstellen einer Verbindung mit einem Azure Stack-Abonnement

### <a name="known-issues"></a>Bekannte Probleme

* Schaltflächen im Dialogfeld zum Bestätigen des Löschens von Ordnern reagieren nicht auf Mausklicks unter Linux. Die Problemumgehung besteht darin, die EINGABETASTE zu verwenden
* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten
* Im Panel mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Für die Ubuntu 14.04-Installation muss ein Update oder Upgrade der gcc-Version ausgeführt werden – Schritte zum Ausführen eines Upgrades finden Sie unten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```

### <a name="version-0813"></a>Version 0.8.13
05/12/2017

#### <a name="new"></a>Neu

* Storage-Explorer – [Leitfaden zur Problembehandlung][2]
* [Anweisungen][3] zum Herstellen einer Verbindung mit einem Azure Stack-Abonnement

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Beim Dateiupload trat mit hoher Wahrscheinlichkeit ein Fehler durch ungenügenden Arbeitsspeicher auf
* Behoben: Sie können jetzt sich jetzt per PIN/Smartcard anmelden
* Behoben: „Im Portal öffnen“ funktioniert nun für Azure China 21Vianet, Azure Deutschland, Azure US Government und Azure Stack
* Behoben: Beim Hochladen eines Ordners in einen Blobcontainer trat gelegentlich ein Fehler „Illegal operation“ (Der Vorgang ist nicht gestattet) auf
* Behoben: „Alle auswählen“ war beim Verwalten von Momentaufnahmen deaktiviert
* Behoben: Die Metadaten des Basisblobs wurden u.U. nach Anzeigen der Eigenschaften seiner Momentaufnahmen überschrieben

#### <a name="known-issues"></a>Bekannte Probleme

* Wenn Sie die falsche PIN/das falsche Smartcard-Zertifikat auswählen, müssen Sie einen Neustart ausführen, damit diese Entscheidung im Storage-Explorer unwirksam gemacht wird
* Beim Vergrößern oder Verkleinern der Anzeige wurde die Zoomstufe u.U. vorübergehend auf den Standardwert zurückgesetzt
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten
* Im Panel mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Für die Ubuntu 14.04-Installation muss ein Update oder Upgrade der gcc-Version ausgeführt werden – Schritte zum Ausführen eines Upgrades finden Sie unten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-0812-and-0811-and-0810"></a>Version 0.8.12, 0.8.11 und 0.8.10
07.04.2017

#### <a name="new"></a>Neu

* Storage-Explorer wird nun automatisch geschlossen, wenn Sie ein Update aus der Updatebenachrichtigung installieren
* Der direkte schnelle Zugriff verbessert Ihre Arbeit mit häufig aufgerufenen Ressourcen
* Im Blobcontainer-Editor ist nun ersichtlich, zu welchem virtuellen Computer ein geleaster Blob gehört
* Das linke Panel kann nun reduziert werden
* Die Ermittlung wird nun gleichzeitig mit dem Download ausgeführt
* Bestimmen Sie anhand von Statistiken im Blobcontainer, Dateifreigabe- und Tabellen-Editor die Größe Ihrer Ressource oder Auswahl
* Sie können sich nun bei auf Azure Active Directory (AAD) basierenden Azure Stack-Konten anmelden.
* Sie können nun Archivdateien mit einer Größe von mehr als 32 MB in Storage Premium-Konten hochladen
* Verbesserte Unterstützung für Barrierefreiheit
* Sie können nun vertrauenswürdige Base-64-codierte X.509-TLS/SSL-Zertifikate hinzufügen, indem Sie „Bearbeiten“ -&gt; „SSL-Zertifikate“ -&gt; „Zertifikate importieren“ auswählen

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Nach dem Aktualisieren der Anmeldeinformationen eines Kontos wurde die Strukturansicht in einigen Fällen nicht automatisch aktualisiert
* Behoben: Beim Generieren einer SAS für Emulator-Warteschlangen und -Tabellen wurde früher eine ungültige URL erhalten
* Behoben: Storage Premium-Konten können nun erweitert werden, während ein Proxy aktiviert ist
* Behoben: Die Schaltfläche „Anwenden“ auf der Seite zum Verwalten von Konten funktionierte nicht, wenn ein oder kein Konto ausgewählt war
* Behoben: Das Hochladen von Blobs, die Konfliktlösungen benötigen, schlug u.U. fehl – behoben in 0.8.11
* Behoben: Senden von Feedback war fehlerhaft in 0.8.11 – behoben in 0.8.12

#### <a name="known-issues"></a>Bekannte Probleme

* Nach dem Upgrade auf 0.8.10 müssen Sie alle Ihre Anmeldeinformationen aktualisieren.
* Beim Vergrößern oder Verkleinern der Anzeige wurde die Zoomstufe u.U. vorübergehend auf den Standardwert zurückgesetzt.
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten.
* Im Panel mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern.
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Obwohl Azure Stack derzeit keine Dateifreigaben unterstützt, wird dennoch ein Knoten „Dateifreigaben“ unter einem angefügten Azure Stack-Speicherkonto angezeigt.
* Für die Ubuntu 14.04-Installation muss ein Update oder Upgrade der gcc-Version ausgeführt werden – Schritte zum Ausführen eines Upgrades finden Sie unten:

    ```
    sudo add-apt-repository ppa:ubuntu-toolchain-r/test
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get dist-upgrade
    ```


### <a name="version-089-and-088"></a>Version 0.8.9 und 0.8.8
23.02.2017

>[!VIDEO https://www.youtube.com/embed/R6gonK3cYAc?ecver=1]

>[!VIDEO https://www.youtube.com/embed/SrRPCm94mfE?ecver=1]


#### <a name="new"></a>Neu

* Storage-Explorer 0.8.9 lädt automatisch die neueste Version für Updates herunter.
* Hotfix: Beim Anfügen eines Speicherkontos mit einem vom Portal generierten SAS-URI trat ein Fehler auf.
* Sie können nun Blob-Momentaufnahmen erstellen, verwalten und höher stufen.
* Sie können sich nun an Azure China 21Vianet-, Azure Deutschland- und Azure US Government-Konten anmelden.
* Sie können jetzt die Zoomstufe ändern. Verwenden Sie die Optionen im Menü „Ansicht“ zum Vergrößern, zum Verkleinern und zum Zurücksetzen der Zoomstufe.
* Unicode-Zeichen werden jetzt in Benutzermetadaten für Blobs und Dateien unterstützt.
* Barrierefreiheitsverbesserungen
* Versionshinweise für die nächste Version können nun über die Updatebenachrichtigung eingesehen werden. Die aktuellen Versionshinweise können auch über das Hilfemenü angezeigt werden.

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Die Versionsnummer wird nun in der Systemsteuerung von Windows korrekt angezeigt
* Behoben: Die Suche ist nicht mehr auf 50.000 Knoten beschränkt
* Behoben: Der Upload in eine Dateifreigabe dauerte unendlich lange, wenn das Zielverzeichnis nicht bereits vorhanden war
* Behoben: Verbesserte Stabilität bei langen Upload- und Downloadvorgängen

#### <a name="known-issues"></a>Bekannte Probleme

* Beim Vergrößern oder Verkleinern der Anzeige wurde die Zoomstufe u.U. vorübergehend auf den Standardwert zurückgesetzt.
* Der Schnellzugriff funktioniert nur mit abonnementbasierten Elementen. Lokale Ressourcen oder Ressourcen, die per Schlüssel oder SAS-Token angefügt werden, werden in dieser Version nicht unterstützt.
* Beim Schnellzugriff kann das Navigieren zur Zielressource einige Sekunden dauern. Dies hängt davon ab, wie viele Ressourcen Sie verwenden.
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten.

16.12.2016
### <a name="version-087"></a>Version 0.8.7

>[!VIDEO https://www.youtube.com/embed/Me4Y4jxoer8?ecver=1]

#### <a name="new"></a>Neu

* Sie können im Fenster „Aktivitäten“ wählen, wie Sie Konflikte zu Beginn einer Update-, Download- oder Kopiersitzung lösen
* Bewegen Sie den Mauszeiger auf eine Registerkarte, um den vollständigen Pfad der Speicherressource anzuzeigen
* Wenn Sie auf eine Registerkarte klicken, wird sie mit dem Speicherort im Navigationsbereich auf der linken Seite synchronisiert

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Storage-Explorer ist unter Mac jetzt eine vertrauenswürdige App
* Behoben: Ubuntu 14.04 wird wieder unterstützt
* Behoben: Die Benutzeroberflächenoption „Konto hinzufügen“ blinkte manchmal beim Laden von Abonnements
* Behoben: Es kam vor, dass im Navigationsbereich auf der linken Seite nicht alle Speicherressourcen aufgeführt wurden
* Behoben: Im Aktionsbereich wurden manchmal leere Aktionen angezeigt
* Behoben: Die Fenstergröße von der letzten geschlossenen Sitzung wird jetzt beibehalten
* Behoben: Sie können mit dem Kontextmenü mehrere Registerkarten für dieselbe Ressource öffnen

#### <a name="known-issues"></a>Bekannte Probleme

* Der Schnellzugriff funktioniert nur mit abonnementbasierten Elementen. Lokale Ressourcen oder Ressourcen, die per Schlüssel oder SAS-Token angefügt werden, werden in dieser Version nicht unterstützt
* Beim Schnellzugriff kann das Navigieren zur Zielressource einige Sekunden dauern. Dies hängt davon ab, wie viele Ressourcen Sie verwenden
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten
* Bei der Suche wird auf ca. 50.000 Knoten gesucht. Anschließend kann es zu einer Leistungsbeeinträchtigung oder zu Ausnahmefehlern kommen
* Wenn Sie den Storage-Explorer unter macOS zum ersten Mal verwenden, werden unter Umständen mehrere Eingabeaufforderungen angezeigt, in denen die Berechtigung des Benutzers zum Zugreifen auf den Schlüsselbund abgefragt wird. Es wird empfohlen, die Option „Immer zulassen“ zu wählen, damit die Eingabeaufforderung nicht mehr angezeigt wird

18.11.2016
### <a name="version-086"></a>Version 0.8.6

#### <a name="new"></a>Neu

* Sie können die am häufigsten verwendeten Dienste jetzt für den Schnellzugriff anheften, um die Navigation zu vereinfachen.
* Sie können jetzt mehrere Editoren auf unterschiedlichen Registerkarten öffnen. Einmal klicken, um eine temporäre Registerkarte zu öffnen, und doppelklicken, um eine dauerhafte Registerkarte zu öffnen. Sie können auch auf die temporäre Registerkarte klicken, um sie zu einer dauerhaften Registerkarte zu machen
* Es wurden merkliche Verbesserungen der Leistung und Stabilität für Uploads und Downloads erzielt, vor allem für große Dateien auf schnellen Computern
* Erstellen von leeren „virtuellen“ Ordnern in Blobcontainern ist jetzt möglich
* Die bereichsbezogene Suche mit der neuen, verbesserten Teilzeichenfolgen-Suche wurde hinzugefügt, sodass Sie nun über zwei Suchoptionen verfügen:
    * Globale Suche: Hier können Sie einfach einen Suchbegriff in das Suchtextfeld eingeben
    * Bereichsbezogene Suche: Klicken Sie auf das Lupensymbol neben einem Knoten, und fügen Sie dann einen Suchbegriff am Ende des Pfads hinzu, bzw. klicken Sie mit der rechten Maustaste, und wählen Sie die Option „Ab hier suchen“ aus
* Wir haben verschiedene Designs hinzugefügt: Hell (Standard), Dunkel, Hoher Kontrast (Schwarz) und Hoher Kontrast (Weiß). Navigieren Sie zu „Bearbeiten“ -&gt; „Designs“, um die Designeinstellung zu ändern
* Sie können Blob- und Dateieigenschaften ändern
* Ab jetzt werden codierte (base64) und nicht codierte Warteschlangennachrichten unterstützt
* Unter Linux ist nun ein 64-Bit-Betriebssystem erforderlich. Für diese Version wird nur 64-Bit-Ubuntu 16.04.1 LTS unterstützt
* Wir haben unser Logo aktualisiert!

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Probleme mit dem Einfrieren des Bildschirms
* Behoben: Erweiterte Sicherheit
* Behoben: Gelegentlich treten doppelte angefügte Konten auf
* Behoben: Durch einen Blob mit einem nicht definierten Inhaltstyp konnte eine Ausnahme generiert werden
* Behoben: Öffnen des Abfragepanels für eine leere Tabelle war nicht möglich
* Behoben: Diverse Fehler bei der Suche
* Behoben: Beim Klicken auf „Mehr laden“ wurde die Anzahl der geladenen Ressourcen wurde von 50 auf 100 erhöht
* Behoben: Wenn bei der ersten Ausführung die Anmeldung bei einem Konto erfolgt, werden nun standardmäßig sämtliche Abonnements für das betreffende Konto ausgewählt

#### <a name="known-issues"></a>Bekannte Probleme

* Diese Version des Storage-Explorers wird nicht unter Ubuntu 14.04 ausgeführt
* Wenn mehrere Registerkarten für dieselbe Ressource geöffnet werden sollen, klicken Sie nicht ständig auf die gleiche Ressource. Klicken Sie auf eine andere Ressource, wechseln Sie zurück, und klicken Sie dann auf die ursprüngliche Ressource, um sie in einer weiteren Registerkarte zu öffnen
* Der Schnellzugriff funktioniert nur mit abonnementbasierten Elementen. Lokale Ressourcen oder Ressourcen, die per Schlüssel oder SAS-Token angefügt werden, werden in dieser Version nicht unterstützt
* Beim Schnellzugriff kann das Navigieren zur Zielressource einige Sekunden dauern. Dies hängt davon ab, wie viele Ressourcen Sie verwenden
* Wenn mehr als drei Gruppen von Blobs oder Dateien gleichzeitig hochgeladen werden, können Fehler auftreten
* Bei der Suche wird auf ca. 50.000 Knoten gesucht. Anschließend kann es zu einer Leistungsbeeinträchtigung oder zu Ausnahmefehlern kommen

10/03/2016
### <a name="version-085"></a>Version 0.8.5

#### <a name="new"></a>Neu

* Sie können nun im Portal generierte SAS-Schlüssel zum Anfügen an Speicherkonten und Ressourcen verwenden

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Eine Racebedingung während der Suche bewirkte gelegentlich, dass Knoten nicht erweitert werden konnten
* Behoben: „HTTP verwenden“ funktioniert nicht, wenn eine Verbindung mit Speicherkonten mithilfe von Kontoname und -schlüssel hergestellt wird
* Behoben: SAS-Schlüssel (insbesondere vom Portal generierte) geben einen Fehler „mit nachgestelltem Schrägstrich“ zurück
* Behoben: Probleme beim Tabellenimport
    * Gelegentlich wurden Partitionsschlüssel und Zeilenschlüssel umgekehrt
    * „Null“-Partitionsschlüssel können nicht gelesen werden

#### <a name="known-issues"></a>Bekannte Probleme

* Bei der Suche wird auf ca. 50.000 Knoten gesucht; anschließend kann es zu einer Leistungsbeeinträchtigung kommen
* Dateien werden von Azure Stack derzeit nicht unterstützt; beim Versuch, Dateien zu erweitern, wird also ein Fehler ausgegeben

12.09.2016
### <a name="version-084"></a>Version 0.8.4

>[!VIDEO https://www.youtube.com/embed/cr5tOGyGrIQ?ecver=1]

#### <a name="new"></a>Neu

* Generieren von direkten Verknüpfungen mit Speicherkonten, Containern, Warteschlangen, Tabellen oder Dateifreigaben zum gemeinsamen Nutzen und einfachen Aufrufen Ihrer Ressourcen – Windows- und Mac OS-Unterstützung
* Suchen nach Blobcontainern, Tabellen, Warteschlangen, Dateifreigaben oder Speicherkonten über das Suchfeld
* Sie können nun Klauseln im Abfrage-Generator für Tabellen generieren
* Umbenennen und Kopieren/Einfügen von Blobcontainern, Dateifreigaben, Tabellen, Blobs, Blobordnern, Dateien und Verzeichnissen aus mit SAS angefügten Konten und Containern
* Beim Umbenennen und Kopieren von Blobcontainern und Dateifreigaben bleiben jetzt Eigenschaften und Metadaten erhalten

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Tabellenentitäten können nicht bearbeitet werden, wenn sie boolesche oder binäre Eigenschaften enthalten

#### <a name="known-issues"></a>Bekannte Probleme

* Bei der Suche wird auf ca. 50.000 Knoten gesucht; anschließend kann es zu einer Leistungsbeeinträchtigung kommen

03.08.2016
### <a name="version-083"></a>Version 0.8.3

>[!VIDEO https://www.youtube.com/embed/HeGW-jkSd9Y?ecver=1]

#### <a name="new"></a>Neu

* Umbenennen von Containern, Tabellen, Dateifreigaben
* Verbesserte Oberfläche des Abfrage-Generators
* Möglichkeit zum Speichern und Laden von Abfragen
* Direkte Verknüpfungen mit Speicherkonten oder Containern, Warteschlangen, Tabellen oder Dateifreigaben zum gemeinsamen Nutzen und einfachen Aufrufen Ihrer Ressourcen (nur Windows; macOS-Unterstützung wird in Kürze verfügbar sein!)
* Möglichkeit zum Verwalten und Konfigurieren von CORS-Regeln

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Bei Microsoft-Konten ist alle 8 bis 12 Stunden eine erneute Authentifizierung erforderlich

#### <a name="known-issues"></a>Bekannte Probleme

* Gelegentlich scheint die Benutzeroberfläche nicht zu reagieren – dieses Problem kann durch Maximieren des Fensters behoben werden
* Für die Installation unter macOS sind u.U. erhöhte Berechtigungen erforderlich
* Im Panel mit den Kontoeinstellungen wird unter Umständen angezeigt, dass Sie Anmeldeinformationen erneut eingeben müssen, um Abonnements zu filtern
* Beim Umbenennen von Dateifreigaben, Blobcontainern und Tabellen werden Metadaten oder sonstige Eigenschaften für den Container nicht beibehalten, z.B. Dateifreigabe-Kontingent, öffentliche Zugriffsebene und Zugriffsrichtlinien
* Beim Umbenennen von Blobs (einzeln oder in einem umbenannten Blobcontainer) werden Momentaufnahmen nicht beibehalten. Alle anderen Eigenschaften und Metadaten für Blobs, Dateien und Entitäten werden beim Umbenennen beibehalten.
* Das Kopieren und Umbenennen von Ressourcen funktioniert nicht in über SAS angefügten Konten

07.07.2016
### <a name="version-082"></a>Version 0.8.2

>[!VIDEO https://www.youtube.com/embed/nYgKbRUNYZA?ecver=1]

#### <a name="new"></a>Neu

* Speicherkonten werden nach Abonnements gruppiert; Entwicklungsspeicher und -ressourcen , die über Schlüssel oder SAS angefügt wurden, werden unter dem Knoten „(Local and Attached)“ (Lokal und angefügt) angezeigt
* Abmelden von Konten im Panel „Azure-Kontoeinstellungen“
* Konfigurieren von Proxyeinstellungen zum Aktivieren und Verwalten der Anmeldung
* Erstellen und Unterbrechen von Blob-Leases
* Öffnen von Blobcontainern, Warteschlangen, Tabellen und Dateien per einfachen Klick

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Mit .NET- oder Java-Bibliotheken eingefügte Warteschlangennachrichten werden nicht ordnungsgemäß aus base64 decodiert
* Behoben: $metrics-Tabellen werden für Blob Storage-Konten nicht angezeigt
* Behoben: Tabellenknoten funktioniert nicht für lokalen Speicher (Entwicklung)

#### <a name="known-issues"></a>Bekannte Probleme

* Für die Installation unter macOS sind u.U. erhöhte Berechtigungen erforderlich

15.06.2016
### <a name="version-080"></a>Version 0.8.0

>[!VIDEO https://www.youtube.com/embed/ycfQhKztSIY?ecver=1]

>[!VIDEO https://www.youtube.com/embed/k4_kOUCZ0WA?ecver=1]

>[!VIDEO https://www.youtube.com/embed/3zEXJcGdl_k?ecver=1]

#### <a name="new"></a>Neu

* Unterstützung von Dateifreigaben: Anzeigen, Hochladen, Herunterladen, Kopieren von Dateien und Verzeichnissen, SAS-URIs (Erstellen und Herstellen von Verbindungen)
* Verbesserte Benutzererfahrung beim Herstellen von Verbindungen mit dem Speicher mit SAS-URIs oder Kontoschlüsseln
* Exportieren von Ergebnissen der Tabellenabfrage
* Neuanordnen und Anpassen von Tabellenspalten
* Anzeigen von $logs-Blobcontainern und $metrics-Tabellen für Speicherkonten mit aktivierten Metriken
* Verbessertes Export- und Importverhalten, nun ist der Typ von Eigenschaftswerten eingeschlossen

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Eventuelle unvollständige Uploads oder Downloads von großen Blobs
* Behoben: Beim Bearbeiten, Hinzufügen oder Importieren einer Entität mit einem numerischen Zeichenfolgenwert („1“) wird dieser in einen double-Wert konvertiert
* Behoben: Der Tabellenknoten kann in der lokalen Entwicklungsumgebung nicht erweitert werden

#### <a name="known-issues"></a>Bekannte Probleme

* $metrics-Tabellen sind für Blob Storage-Konten nicht sichtbar
* Programmgesteuert hinzugefügte Warteschlangennachrichten werden u.U. nicht ordnungsgemäß angezeigt, wenn sie mit Base64-Codierung codiert sind

17.05.2016
### <a name="version-07201605090"></a>Version 0.7.20160509.0

#### <a name="new"></a>Neu

* Bessere Fehlerbehandlung bei Abstürzen von Apps

#### <a name="fixes"></a>Fehlerbehebungen

* Korrigierter Fehler: Gelegentlich wurden InfoBar-Meldungen nicht angezeigt, wenn Anmeldeinformationen angefordert wurden

#### <a name="known-issues"></a>Bekannte Probleme

* Tabellen: Wenn der Benutzer beim Hinzufügen, Bearbeiten oder Importieren einer Entität mit einer Eigenschaft mit einem mehrdeutigen Wert wie „1“ oder „1,0“ versucht, diese als `Edm.String` zu senden, wird der Wert über die Client-API als Edm.Double zurückgegeben

31.03.2016

### <a name="version-07201603250"></a>Version 0.7.20160325.0

>[!VIDEO https://www.youtube.com/embed/imbgBRHX65A?ecver=1]

>[!VIDEO https://www.youtube.com/embed/ceX-P8XZ-s8?ecver=1]

#### <a name="new"></a>Neu

* Unterstützung von Tabellen: Anzeigen, Abfragen, Exportieren, Importieren und CRUD-Vorgänge für Entitäten
* Unterstützung von Warteschlangen: Anzeigen, Hinzufügen und Entfernen von Nachrichten aus der Warteschlange
* Generieren von SAS-URIs für Speicherkonten
* Herstellen einer Verbindung mit Speicherkonten mit SAS-URIs
* Updatebenachrichtigungen für künftige Aktualisierungen von Storage-Explorer
* Aktualisiertes Erscheinungsbild

#### <a name="fixes"></a>Fehlerbehebungen

* Verbesserungen von Leistung und Zuverlässigkeit

### <a name="known-issues-amp-mitigations"></a>Bekannte Probleme &amp; Lösungen

* Herunterladen von großen Blobdateien funktioniert nicht ordnungsgemäß – es wird empfohlen, AzCopy zu verwenden, während wir dieses Problem beheben
* Kontoanmeldeinformationen werden weder abgerufen noch zwischengespeichert, wenn der Basisordner nicht gefunden wurde bzw. in den Basisordner nicht geschrieben werden kann
* Wenn beim Hinzufügen, Bearbeiten oder Importieren einer Entität mit einer Eigenschaft mit einem mehrdeutigen Wert wie „1“ oder „1,0“ versucht wird, diese als `Edm.String` zu senden, wird der Wert über die Client-API als Edm.Double zurückgegeben
* Beim Importieren von CSV-Dateien mit mehrzeiligen Datensätzen werden die Daten möglicherweise aufgeteilt oder verschlüsselt

03.02.2016

### <a name="version-07201601291"></a>Version 0.7.20160129.1

#### <a name="fixes"></a>Fehlerbehebungen

* Verbesserte Gesamtleistung beim Hochladen, Herunterladen und Kopieren von Blobs

14.01.2016

### <a name="version-07201601050"></a>Version 0.7.20160105.0

#### <a name="new"></a>Neu

* Linux-Unterstützung (Paritätsfunktionen für OSX)
* Hinzufügen von Blobcontainern mit SAS-Schlüssel (Shared Access Signatures)
* Hinzufügen von Speicherkonten für Azure China 21Vianet
* Hinzufügen von Speicherkonten mit benutzerdefinierten Endpunkten
* Öffnen und Anzeigen der Inhaltstexte und Bildblobs
* Anzeigen von Blobeigenschaften und Metadaten

#### <a name="fixes"></a>Fehlerbehebungen

* Behoben: Das Hochladen oder Herunterladen einer großen Anzahl von Blobs (500+) bewirkt gelegentlich, dass die App einen weißen Bildschirm aufweist
* Behoben: Beim Festlegen der öffentlichen Zugriffsebene für Blobcontainer wird der neue Wert erst aktualisiert, wenn der Fokus auf den Container neu festgelegt wird. Zudem wird im Dialogfeld standardmäßig „No public access“ (Kein öffentlicher Zugriff) und nicht der tatsächliche aktuelle Wert angezeigt.
* Bessere Gesamtunterstützung von Tastatur/Barrierefreiheit und Benutzeroberfläche
* Langer Breadcrumb-Verlaufstext wird mit Leerraum umbrochen
* SAS-Dialogfeld unterstützt die Eingabeüberprüfung
* Lokaler Speicher ist weiterhin verfügbar, selbst wenn die Benutzeranmeldeinformationen abgelaufen sind
* Beim Löschen eines geöffneten Blobcontainers wird der Blob-Explorer auf der rechten Seite geschlossen

#### <a name="known-issues"></a>Bekannte Probleme

* Für die Linux-Installation muss ein Update oder Upgrade der gcc-Version ausgeführt werden – Schritte zum Ausführen eines Upgrades finden Sie unten:
    * `sudo add-apt-repository ppa:ubuntu-toolchain-r/test`
    * `sudo apt-get update`
    * `sudo apt-get upgrade`
    * `sudo apt-get dist-upgrade`

18.11.2015
### <a name="version-07201511160"></a>Version 0.7.20151116.0

#### <a name="new"></a>Neu

* macOS- und Windows-Versionen
* Anmelden zum Anzeigen Ihrer Speicherkonten – Verwenden Sie Ihr Organisationskonto, Microsoft-Konto, 2FA usw.
* Lokaler Entwicklungsspeicher (Verwenden des Speicheremulators, nur Windows)
* Unterstützung von Azure Resource Manager sowie von klassischen Ressourcen
* Erstellen und Löschen von Blobs, Warteschlangen oder Tabellen
* Suchen nach bestimmten Blobs, Warteschlangen oder Tabellen
* Untersuchen des Inhalts von Blobcontainern
* Anzeigen von Verzeichnissen und Navigieren durch Verzeichnisse
* Hochladen, Herunterladen und Löschen von Blobs und Ordnern
* Anzeigen von Blobeigenschaften und Metadaten
* Generieren von SAS-Schlüsseln
* Verwalten und Erstellen von SAPs (Stored Access Policies, gespeicherte Zugriffsrichtlinien)
* Suchen nach Blobs anhand des Präfix
* Hochladen oder Herunterladen von Dateien per Drag & Drop

#### <a name="known-issues"></a>Bekannte Probleme

* Beim Festlegen der öffentlichen Zugriffsebene für Blobcontainer wird der neue Wert erst aktualisiert, wenn der Fokus auf den Container neu festgelegt wird
* Wenn Sie das Dialogfeld öffnen, um die öffentliche Zugriffsebene festzulegen, wird als Standardwert „No public access“ (Kein öffentlicher Zugriff) und nicht der tatsächliche aktuelle Wert angezeigt
* Heruntergeladene Blobs können nicht umbenannt werden
* Einträge von Aktivitätsprotokollen bleiben bei Auftreten eines Fehlers gelegentlich in einem Statuszustand „hängen“, wobei der Fehler jedoch nicht angezeigt wird
* Die App stürzt ab oder der Bildschirm wird komplett weiß, wenn versucht wird, eine große Anzahl von Blobs hochzuladen oder herunterzuladen
* In einigen Fällen kann ein Kopiervorgang nicht abgebrochen werden
* Wenn Sie beim Erstellen eines Containers (Blob/Warteschlange/Tabelle) einen ungültigen Namen eingeben und mit dem Erstellen eines Containers eines anderen Typs fortfahren, kann der Fokus nicht auf den neuen Typ festgelegt werden
* Neuer Ordner kann nicht erstellt werden, oder Ordner kann nicht umbenannt werden




[2]: https://support.microsoft.com/en-us/help/4021389/storage-explorer-troubleshooting-guide
[3]: vs-azure-tools-storage-manage-with-storage-explorer.md