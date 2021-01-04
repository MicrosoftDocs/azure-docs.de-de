---
title: Betriebssystemfunktionen
description: Erfahren Sie etwas über die Betriebssystemfunktionalität in Azure App Service unter Windows. Lernen Sie die Arten des Zugriffs auf Dateien, Netzwerke und Registrierungen für Ihre App kennen.
ms.assetid: 39d5514f-0139-453a-b52e-4a1c06d8d914
ms.topic: article
ms.date: 10/30/2018
ms.custom: seodec18
ms.openlocfilehash: 949e408544e25cb55622cf2a1b1d2dddb92350a6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001506"
---
# <a name="operating-system-functionality-on-azure-app-service"></a>Betriebssystemfunktionen für Azure App Service
In diesem Artikel werden allgemeine, grundlegende Betriebssystemfunktionen beschrieben, die für alle Windows-Apps zur Verfügung stehen, die in [Azure App Service](./overview.md) ausgeführt werden. Diese Funktionen umfassen Zugriff auf Dateien, Netzwerke und Registrierung sowie Diagnoseprotokolle und Ereignisse. 

> [!NOTE] 
> [Linux-Apps](overview.md#app-service-on-linux) in App Service werden in eigenen Containern ausgeführt. Es wird kein Zugriff auf das Hostbetriebssystem gewährt, Sie erhalten Rootzugriff auf den Container. Ebenso erhalten Sie für [in Windows-Containern ausgeführte Apps](quickstart-custom-container.md?pivots=container-windows) Verwaltungszugriff auf die Container, aber keinen Zugriff auf das Hostbetriebssystem. 
>

<a id="tiers"></a>

## <a name="app-service-plan-tiers"></a>App Service-Planstufen
App Service führt Kunden-Apps in einer mandantenfähigen Hostingumgebung aus. Apps mit Bereitstellung in den Tarifen **Free** und **Shared** werden mit Workerprozessen auf gemeinsamen virtuellen Computern ausgeführt. Apps mit Bereitstellung in den Tarifen **Standard** und **Premium** werden hingegen auf virtuellen Computern ausgeführt, die nur für die Apps eines einzelnen Kunden bestimmt sind.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Da App Service eine nahtlose Skalierung zwischen unterschiedlichen Tarifen unterstützt, bleibt die durchgeführte Sicherheitskonfiguration für App Service Apps gleich. So wird gewährleistet, dass sich Apps nicht plötzlich anders verhalten und unerwartet ausfallen, wenn der App Service-Plan gewechselt wird.

<a id="developmentframeworks"></a>

## <a name="development-frameworks"></a>Entwicklungsframeworks
App Service-Tarife steuern die Rechnerressourcen (CPU, Datenspeicher, Arbeitsspeicher und Netzwerkausgang), die Apps zur Verfügung stehen. Unabhängig von den gewählten Tarifen bleibt der Umfang verfügbarer Frameworkfunktionen für Apps jedoch gleich.

App Service bietet Unterstützung für eine Vielzahl an Entwicklungsframeworks, einschließlich ASP.NET, klassischem ASP, Node.js, PHP und Python. Diese werden alle als Erweiterungen innerhalb von IIS ausgeführt. App Service Apps führen die verschiedenen Entwicklungsframeworks mit deren Standardeinstellungen aus, um die Sicherheitskonfiguration zu vereinfachen und zu normalisieren. Ein Ansatz für die Konfiguration von Apps hätte die Anpassung der API-Oberfläche und der Funktionen für jedes einzelne Entwicklungsframework sein können. Stattdessen ist der Ansatz von App Service allgemeiner. Es wird eine allgemeine Grundlage an Betriebssystemfunktionen ermöglicht, unabhängig vom Anwendungsentwicklungsframework einer App.

In den folgenden Abschnitten werden die allgemeinen Betriebssystemfunktionen für App Service-Apps zusammengefasst.

<a id="FileAccess"></a>

## <a name="file-access"></a>Dateizugriff
In App Service gibt es viele Laufwerke, einschließlich lokaler Laufwerke und Netzwerklaufwerke.

<a id="LocalDrives"></a>

### <a name="local-drives"></a>Lokale Laufwerke
Im Grunde ist App Service ein Dienst, der auf der Azure-PaaS-Infrastruktur (Platform-as-a-Service) ausgeführt wird. Daher sind die lokalen Laufwerke, die an einen virtuellen Computer "angehängt" sind, die gleichen Laufwerkstypen wie die für jede in Azure ausgeführte Workerrolle verfügbaren Typen. Dies schließt Folgendes ein:

- Ein Betriebssystemlaufwerk (Laufwerk D:\)
- Ein Anwendungslaufwerk mit den CSPKG-Dateien des Azure-Pakets, das ausschließlich von App Service verwendet wird (und auf das Kunden keinen Zugriff haben)
- Ein „Benutzer-Laufwerk“ (Laufwerk C:\), dessen Größe von der Größe des virtuellen Computer abhängt. 

Die Überwachung der Datenträgerauslastung ist wichtig, wenn Ihre Anwendung wächst. Wenn das Datenträgerkontingent erreicht ist, kann sich das negativ auf Ihre Anwendung auswirken. Beispiel: 

- Die App gibt möglicherweise eine Fehlermeldung zurück, dass nicht genug Speicherplatz auf dem Datenträger vorhanden ist.
- Wenn Sie die Kudu-Konsole durchsuchen, werden Ihnen möglicherweise Datenträgerfehler angezeigt.
- Die Bereitstellung über Azure DevOps oder Visual Studio funktioniert möglicherweise mit `ERROR_NOT_ENOUGH_DISK_SPACE: Web deployment task failed. (Web Deploy detected insufficient space on disk)` nicht.
- Ihrer App wird möglicherweise langsam ausgeführt.

<a id="NetworkDrives"></a>

### <a name="network-drives-unc-shares"></a>Netzwerklaufwerke (UNC-Freigaben)
Ein Alleinstellungsmerkmal von App Service, das die Webanwendungsbereitstellung und -wartung so unkompliziert macht, ist die Tatsache, dass alle Benutzerinhalte auf einem Satz an UNC-Freigaben gespeichert werden. Dieses Modell passt gut zum allgemeinen Muster der Inhaltsspeicherung, das von lokalen Webhostingumgebungen verwendet wird, die über mehrere Server mit Lastenausgleich verfügen. 

In App Service werden in jedem Rechenzentrum mehrere UNC-Freigaben erstellt. Ein Prozentsatz der Benutzerinhalte aller Kunden in jedem Rechenzentrum wird auf alle UNC-Freigaben verteilt. Außerdem werden alle Dateiinhalte für das Abonnement eines Kunden immer auf derselben UNC-Freigabe abgelegt. 

Aufgrund der Funktionsweise von Azure-Diensten ändert sich der spezifische virtuelle Computer, der die UNC-Freigabe hostet, im Lauf der Zeit. Es wird gewährleistet, dass UNC-Freigaben von unterschiedlichen virtuellen Computern eingebunden werden, da diese während des normalen Verlaufs von Azure-Vorgängen ein- und ausgeschaltet werden. Aus diesem Grund sollten Apps nicht hartcodiert annehmen, dass die Computerinformationen in einem UNC-Dateipfad immer stabil bleiben. Stattdessen muss der praktische *pseudoabsolute* Pfad **D:\home\site** verwendet werden, den App Service zur Verfügung stellt. Dieser pseudoabsolute Pfad bietet eine portable, Web-App- und benutzeragnostische Methode für die Verknüpfung zur eigenen App. Wenn Sie **D:\home\site** verwenden, können Sie freigegebene Dateien von App zu App übertragen, ohne für jede Übertragung einen neuen absoluten Pfad konfigurieren zu müssen.

<a id="TypesOfFileAccess"></a>

### <a name="types-of-file-access-granted-to-an-app"></a>Dateizugriffsarten für eine Webanwendung
Jedes Abonnement eines Kunden verfügt über eine reservierte Verzeichnisstruktur auf einer bestimmten UNC-Freigabe innerhalb eines Rechenzentrums. Ein Kunde kann über mehrere Apps verfügen, die in einem bestimmten Rechenzentrum erstellt werden. Alle Verzeichnisse, die zu einem Abonnement eines Kunden gehören, werden daher auf derselben UNC-Freigabe erstellt. In der Freigabe können Verzeichnisse für Inhalt, Fehler- und Diagnoseprotokolle und frühere Versionen der App, erstellt von der Quellcodeverwaltung, enthalten sein. Wie erwartet, sind App-Verzeichnisse eines Kunden für Lese- und Schreibzugriff während der Laufzeit über den Anwendungscode der App verfügbar.

Auf den lokalen Laufwerken, die zum virtuellen Computer gehören, auf dem eine App ausgeführt wird, reserviert App Service Speicherplatz auf Laufwerk C:\ für eine App-spezifische, temporäre lokale Speicherung. Obwohl eine App vollständigen Lese- und Schreibzugriff für den eigenen temporären lokalen Speicher besitzt, ist dieser Speicherplatz nicht dazu gedacht, direkt vom Anwendungscode verwendet zu werden. Stattdessen dient er dem Zweck, temporären Dateispeicher für IIS und Webanwendungsframeworks bereitzustellen. App Service beschränkt außerdem den temporären lokalen Speicherplatz für jede App, damit einzelne Apps nicht übermäßig viel lokalen Speicherplatz verbrauchen können.

Zwei Beispiele dazu, wie App Service temporären lokalen Speicherplatz verwendet, sind das Verzeichnis für ASP.NET-Dateien und das Verzeichnis für komprimierte IIS-Dateien. Das Kompilierungssystem von ASP.NET verwendet das Verzeichnis "Temporary ASP.NET Files" als temporären Speicherort für den Kompilierungscache. IIS verwenden das Verzeichnis "IIS Temporary Compressed Files", um komprimierte Rückmeldungen zu speichern. Diese beiden Arten der Dateinutzung (und weitere) werden in App Service über temporären lokalen Speicherplatz pro App neu zugewiesen. Durch diese Neuzuweisung wird eine durchgängige Funktionalität gewährleistet.

Jede App in App Service wird als zufällige, eindeutige Workerprozessidentität mit geringen Berechtigungen ausgeführt. Weitere Informationen zu dieser als „Anwendungspoolidentität“ bezeichneten Identität finden Sie unter [https://www.iis.net/learn/manage/configuring-security/application-pool-identities](https://www.iis.net/learn/manage/configuring-security/application-pool-identities). Anwendungscode nutzt diese Identität für grundlegenden schreibgeschützten Zugriff auf das Betriebssystemlaufwerk (Laufwerk D:\). Das bedeutet, Anwendungscode kann allgemeine Verzeichnisstrukturen auflisten und allgemeine Dateien auf dem Betriebssystemlaufwerk lesen. Dies erscheint zwar wie ein sehr umfassender Zugriff, dieselben Verzeichnisse und Dateien sind jedoch zugänglich, wenn Sie in einem von Azure gehosteten Dienst eine Workerrolle bereitstellen und die Laufwerksinhalte lesen. 

<a name="multipleinstances"></a>

### <a name="file-access-across-multiple-instances"></a>Dateizugriff über mehrere Instanzen
Das Basisverzeichnis enthält den Inhalt einer App, und ein Anwendungscode kann darauf schreiben. Wenn eine App auf mehreren Instanzen ausgeführt wird, wird das Homeverzeichnis auf alle Instanzen verteilt, sodass für alle Instanzen das gleiche Verzeichnis angezeigt wird. Wenn also beispielsweise eine App hochgeladene Dateien im Homeverzeichnis speichert, sind diese Dateien sofort für alle Instanzen verfügbar. 

<a id="NetworkAccess"></a>

## <a name="network-access"></a>Netzwerkzugriff
Anwendungscode kann TCP/IP- und UDP-basierte Protokolle verwenden, um ausgehende Verbindungen zu zugänglichen Endpunkten im Internet herzustellen, die externe Dienste bereitstellen. Apps können die gleichen Protokolle für eine Verbindung mit Diensten innerhalb von Azure verwenden, z.B. durch Herstellen von HTTPS-Verbindungen mit SQL-Datenbank.

Es ist außerdem eine eingeschränkte Funktion für Apps zur Erstellung einer lokalen Loopbackverbindung vorhanden, deren lokaler Loopbacksocket von Apps überwachtwerden kann. Diese Funktion dient vor allem dazu, Apps zu ermöglichen, als Teil ihrer Funktionalität lokale Loopbacksockets zu überwachen. Jede App sieht eine „private“ Loopbackverbindung. Die App „A“ kann nicht an einem lokalen Loopbacksocket lauschen, der durch die App „B“ eingerichtet wurde.

Named Pipes werden auch als Mechanismus für die Kommunikation zwischen Prozessen (IPC) unterstützt. Dies umfasst unterschiedliche Prozesse, die zusammen eine App ausführen. Beispielsweise nutzt das IIS FastCGI-Modul Named Pipes für die Koordinierung der individuellen Prozesse, die PHP-Websites ausführen.

<a id="Code"></a>

## <a name="code-execution-processes-and-memory"></a>Codeausführung, Prozesse und Speicher
Wie bereits zuvor erwähnt, werden Apps innerhalb von Workerprozessen mit geringen Berechtigungen und mit einer zufälligen Anwendungspoolidentität ausgeführt. Anwendungscode besitzt Zugriff auf den Speicherplatz, der zum Workerprozess gehört, sowie alle untergeordneten Prozesse, die aus CGI-Prozessen oder anderen Anwendungen stammen. Eine App kann jedoch nicht auf den Speicher oder die Daten einer anderen App zugreifen, selbst wenn diese auf demselben virtuellen Computer ausgeführt wird.

Apps können Skripts oder Seiten ausführen, die mit unterstützten Webanwendungsframeworks erstellt wurden. App Service konfiguriert die Einstellungen von Webanwendungsframeworks nicht auf eingeschränktere Modi. Beispielsweise werden ASP.NET-Apps, die in App Service ausgeführt werden, als „voll“ vertrauenswürdig statt auf eingeschränkter Vertrauensebene ausgeführt. Webframeworks, einschließlich klassischem ASP und ASP.NET, können prozessinterne COM-Komponenten aufrufen (jedoch keine prozessexternen COM-Komponenten), zum Beispiel ADO (ActiveX Data Objects), die standardmäßig auf dem Windows-Betriebssystem registriert sind.

Apps können beliebigen Code erzeugen und ausführen. Es ist zulässig, dass eine App beispielsweise eine Befehlsshell erzeugt oder ein PowerShell-Skript ausführt. Obwohl beliebiger Code und beliebige Prozesse von einer App erzeugt werden können, sind ausführbare Programme und Skripts jedoch immer durch die Berechtigungen beschränkt, die der übergeordnete Anwendungspool besitzt. Beispielsweise kann eine App ein ausführbares Programm erzeugen, das einen ausgehenden HTTP-Aufruf ausführt. Dieses ausführbare Programm kann jedoch nicht versuchen, die IP-Adresse eines virtuellen Computers von deren NIC loszulösen. Die Durchführung eines ausgehenden Netzwerkaufrufes ist für Code mit geringen Berechtigungen zulässig, der Versuch, die Netzwerkeinstellungen auf einem virtuellen Computer zu konfigurieren erfordert jedoch Administratorberechtigungen.

<a id="Diagnostics"></a>

## <a name="diagnostics-logs-and-events"></a>Diagnoseprotokolle und Ereignisse
Bei Protokollinformationen handelt es sich um einen weiteren Datensatz, auf den einige Apps versuchen, zuzugreifen. Die Typen von Protokollinformationen, die für in App Service ausgeführten Code verfügbar sind, umfassen Diagnose- und Protokollinformationen, die von einer App generiert wurden, und auf welche diese einfach zugreifen kann. 

Beispielsweise sind W3C-HTTP-Protokolle, die von einer aktiven App generiert werden, entweder in einem Protokollverzeichnis in der Netzwerkfreigabe, die für die App erstellt wurde, verfügbar oder im Blobspeicher, wenn ein Kunde W3C-Protokollierung in einem Speicher eingerichtet hat. Die zweite Option ermöglicht es Ihnen, große Mengen an Protokollen zu sammeln, ohne die Dateispeicherbeschränkungen der Netzwerkfreigabe zu überschreiten.

Ebenso können Echtzeitdiagnoseinformationen aus .NET-Apps protokolliert werden. Dies ist mit der Nachverfolgungs- und Diagnosestruktur von .NET möglich, wobei die Nachverfolgungsinformationen entweder auf die Netzwerkfreigabe der App oder in einen Blob-Speicherort geschrieben werden können.

Bereiche der Diagnoseprotokollierung und Nachverfolgung, die für Apps nicht verfügbar sind, sind Windows-ETW-Ereignisse und Windows-Ereignisprotokolle (z. B. System-, Anwendungs- und Sicherheitsereignisprotokolle). Da ETW-Nachverfolgungsinformationen potenziell computerweit sichtbar sind (mit den entsprechenden ACLs), sind Lese- und Schreibzugriff auf ETW-Ereignisse blockiert. Entwickler erkennen wahrscheinlich, dass API-Aufrufe für das Schreiben und Lesen von ETW-Ereignissen und allgemeinen Windows-Ereignisprotokollen scheinbar funktionieren. Dies liegt jedoch daran, dass App Service die Aufrufe „fälscht“, sodass sie scheinbar erfolgreich ausgeführt werden. Tatsächlich erhält der Anwendungscode keinen Zugriff auf diese Ereignisdaten.

<a id="RegistryAccess"></a>

## <a name="registry-access"></a>Registrierungszugriff
Apps verfügen über schreibgeschützten Zugriff auf einen großen Teil der Registrierung des virtuellen Computers (jedoch nicht auf die gesamte), auf dem sie ausgeführt werden. In der Praxis bedeutet das, dass Registrierungsschlüssel, die schreibgeschützten Zugriff auf lokale Benutzergruppen gewähren, für Apps zugänglich sind. Ein Bereich der Registrierung, der aktuell nicht für Lese- oder Schreibzugriff unterstützt wird, ist die Struktur HKEY\_CURRENT\_USER.

Der Schreibzugriff auf die Registrierung ist blockiert, einschließlich des Zugriffs auf benutzerbezogene Registrierungsschlüssel. Aus Sicht der App sollte in einer Azure-Umgebung nie von Schreibzugriff auf eine Registrierung ausgegangen werden, da Apps über unterschiedliche virtuelle Computer migriert werden können (und dies auch geschieht). Der einzige dauerhaft beschreibbare Speicher, auf den sich eine App stützen kann, ist die Inhaltsverzeichnisstruktur jeder App, die auf UNC-Freigaben von App Service gespeichert wird. 

## <a name="remote-desktop-access"></a>Remotedesktopzugriff

App Service stellt keinen Remotedesktopzugriff auf die VM-Instanzen bereit.

## <a name="more-information"></a>Weitere Informationen

[Azure App Service-Sandbox](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox): Aktuelle Informationen zur Ausführungsumgebung von App Service. Diese Seite wird direkt vom App Service-Entwicklungsteam betreut.