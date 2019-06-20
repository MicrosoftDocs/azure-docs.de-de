---
title: Lokale Weiterleitung der verteilten Ablaufverfolgung von OpenCensus an Azure Application Insights (Vorschau) | Microsoft-Dokumentation
description: Leitet verteilte Ablaufverfolgungsdaten und Spannen von OpenCensus aus Sprachen wie Python und Go an Azure Application Insights weiter.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2018
ms.reviewer: nimolnar
ms.author: mbullwin
ms.openlocfilehash: a7efe663a75fa29a31e7157c5eab24c2973a3758
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699323"
---
# <a name="local-forwarder-preview"></a>Lokale Weiterleitung (Vorschau)

Die lokale Weiterleitung ist ein Agent, der Application Insights- oder [OpenCensus](https://opencensus.io/)-Telemetriedaten aus einer Vielzahl von SDKs erfasst und an Application Insights weiterleitet. Sie kann unter Windows und Linux ausgeführt werden. Sie kann möglicherweise auch unter macOS ausgeführt werden, aber dies wird derzeit nicht offiziell unterstützt.

## <a name="running-local-forwarder"></a>Ausführen der lokalen Weiterleitung

Die lokale Weiterleitung ist ein [Open Source-Projekt auf GitHub](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases). Es gibt eine Vielzahl von Möglichkeiten, die lokale Weiterleitung auf mehreren Plattformen auszuführen.

### <a name="windows"></a>Windows

#### <a name="windows-service"></a>&Windows-Dienst

Am einfachsten können Sie die lokale Weiterleitung unter Windows ausführen, indem Sie sie als Windows-Dienst installieren. Die Version wird mit einer ausführbaren Windows-Dienstdatei (*WindowsServiceHost/Microsoft.LocalForwarder.WindowsServiceHost.exe*) geliefert, die einfach beim Betriebssystem registriert werden kann.

> [!NOTE]
> Für den lokalen Weiterleitungsdienst ist mindestens .NET Framework 4.7 erforderlich. Wenn .NET Framework 4.7 nicht installiert ist, wird der Dienst zwar installiert, aber nicht gestartet. Die neueste Version von .NET Framework finden Sie auf der **[.NET Framework-Downloadseite](
https://www.microsoft.com/net/download/dotnet-framework-runtime/net472?utm_source=getdotnet&utm_medium=referral)** .

1. Laden Sie die Datei „LF.WindowsServiceHost.zip“ von der [Seite der lokalen Weiterleitung](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/releases) auf GitHub herunter.

    ![Screenshot der Downloadseite der lokalen Weiterleitung](./media/opencensus-local-forwarder/001-local-forwarder-windows-service-host-zip.png)

2. In diesem Beispiel extrahieren wir zur Vereinfachung der Demonstration die ZIP-Datei einfach in den Pfad `C:\LF-WindowsServiceHost`.

    Um den Dienst zu registrieren und so zu konfigurieren, dass er beim Systemstart startet, führen Sie folgenden Befehl in der Befehlszeile als Administrator aus:

    ```
    sc create "Local Forwarder" binpath="C:\LF-WindowsServiceHost\Microsoft.LocalForwarder.WindowsServiceHost.exe" start=auto
    ```
    
    Sie sollten folgende Antwort erhalten:
    
    `[SC] CreateService SUCCESS`
    
    Um den neuen Dienst über die Dienste-GUI zu überprüfen, geben Sie ``services.msc`` ein
        
     ![Screenshot des lokalen Weiterleitungsdiensts](./media/opencensus-local-forwarder/002-services.png)

3. **Klicken Sie mit der rechten Maustaste** auf die neue lokale Weiterleitung, und wählen Sie **Starten** aus. Ihr Dienst geht jetzt ist den Status „Wird ausgeführt“ über.

4. Standardmäßig wird der Dienst ohne Wiederherstellungsaktionen erstellt. Sie können **rechtsklicken** und **Eigenschaften** > **Wiederherstellung** auswählen, um automatische Reaktionen auf einen Dienstausfall zu konfigurieren.

    Oder wenn Sie es vorziehen, automatische Wiederherstellungsoptionen programmgesteuert für den Fall von Ausfällen festzulegen, können Sie dies verwenden:

    ```
    sc failure "Local Forwarder" reset= 432000 actions= restart/1000/restart/1000/restart/1000
    ```

5. Am selben Ort wie Ihre ``Microsoft.LocalForwarder.WindowsServiceHost.exe``-Datei (im vorliegenden Beispiel ``C:\LF-WindowsServiceHost``) befindet sich eine Datei namens ``LocalForwarder.config``. Dies ist eine XML-basierte Datei, mit der Sie die Konfiguration Ihrer lokalen Weiterleitung anpassen und den Instrumentierungsschlüssel der Application Insights-Ressource angeben können, an die Sie Ihre verteilten Ablaufverfolgungsdaten weiterleiten möchten. 

    Nachdem Sie die Datei ``LocalForwarder.config`` bearbeitet haben, um Ihren Instrumentierungsschlüssel hinzuzufügen, müssen Sie den **lokalen Weiterleitungsdienst** neu starten, damit Ihre Änderungen wirksam werden.
    
6. Um sicherzustellen, dass die gewünschten Einstellungen vorgenommen wurden und die lokale Weiterleitung wie gewünscht auf Ablaufverfolgungsdaten wartet, überprüfen Sie die Datei ``LocalForwarder.log``. Sie sollten Ergebnisse ähnlich dem Bild unten am Ende der Datei sehen:

    ![Screenshot der Datei „LocalForwarder.log“](./media/opencensus-local-forwarder/003-log-file.png)

#### <a name="console-application"></a>Konsolenanwendung

In bestimmten Anwendungsfällen kann es sinnvoll sein, die lokale Weiterleitung als Konsolenanwendung auszuführen. Die Version wird mit den folgenden ausführbaren Versionen des Konsolenhosts ausgeliefert:
* Einer frameworkabhängigen .NET Core-Binärdatei */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Um diese Binärdatei auszuführen, muss eine .NET Core Runtime installiert sein. Weitere Informationen finden Sie auf dieser [Downloadseite](https://www.microsoft.com/net/download/dotnet-core/2.1).
  ```batchfile
  E:\uncdrop\ConsoleHost\publish>dotnet Microsoft.LocalForwarder.ConsoleHost.dll
  ```
* Einem eigenständigen .NET Core-Satz von Binärdateien für x86- und x64-Plattformen. Für diesen muss keine .NET Core Runtime ausgeführt werden. */ConsoleHost/win-x86/publish/Microsoft.LocalForwarder.ConsoleHost.exe*, */ConsoleHost/win-x64/publish/Microsoft.LocalForwarder.ConsoleHost.exe*.
  ```batchfile
  E:\uncdrop\ConsoleHost\win-x86\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  E:\uncdrop\ConsoleHost\win-x64\publish>Microsoft.LocalForwarder.ConsoleHost.exe
  ```

### <a name="linux"></a>Linux

Wie bei Windows wird die Version mit den folgenden ausführbaren Versionen des Konsolenhosts ausgeliefert:
* Einer frameworkabhängigen .NET Core-Binärdatei */ConsoleHost/publish/Microsoft.LocalForwarder.ConsoleHost.dll*. Um diese Binärdatei auszuführen, muss eine .NET Core Runtime installiert sein. Weitere Informationen finden Sie auf dieser [Downloadseite](https://www.microsoft.com/net/download/dotnet-core/2.1).

```batchfile
dotnet Microsoft.LocalForwarder.ConsoleHost.dll
```

* Einem eigenständigen .NET Core-Satz von Binärdateien für linux-64. Für diesen muss keine .NET Core Runtime ausgeführt werden. */ConsoleHost/linux-x64/publish/Microsoft.LocalForwarder.ConsoleHost*.

```batchfile
user@machine:~/ConsoleHost/linux-x64/publish$ sudo chmod +x Microsoft.LocalForwarder.ConsoleHost
user@machine:~/ConsoleHost/linux-x64/publish$ ./Microsoft.LocalForwarder.ConsoleHost
```

Viele Linux-Benutzer möchten die lokale Weiterleitung möglicherweise als Daemon ausführen. Linux-Systeme verfügen über eine Vielzahl von Lösungen für die Dienstverwaltung, z. B. Upstart, sysv oder systemd. Unabhängig davon, welche spezielle Version Sie verwenden, können Sie die lokale Weiterleitung damit in der für Ihr Szenario am besten geeigneten Weise ausführen.

Als Beispiel erstellen wir einen Daemondienst mithilfe von „systemd“. Wir verwenden die frameworkabhängige Version, aber Sie können dieselben Schritte auch für eine eigenständige Version ausführen.

* Erstellen Sie die folgende Dienstdatei mit dem Namen *localforwarder.service*, und speichern Sie sie unter */lib/systemd/system*.
In diesem Beispiel wird davon ausgegangen, dass Ihr Benutzername SAMPLE_USER ist und Sie die frameworkabhängigen Binärdateien der lokalen Weiterleitung (von */ConssoleHost/publish*) nach */home/SAMPLE_USER/LOCALFORWARDER_DIR* kopiert haben.

```
# localforwarder.service
# Place this file into /lib/systemd/system/
# Use 'systemctl enable localforwarder' to start the service automatically on each boot
# Use 'systemctl start localforwarder' to start the service immediately

[Unit]
Description=Local Forwarder service
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=1
User=SAMPLE_USER
WorkingDirectory=/home/SAMPLE_USER/LOCALFORWARDER_DIR
ExecStart=/usr/bin/env dotnet /home/SAMPLE_USER/LOCALFORWARDER_DIR/Microsoft.LocalForwarder.ConsoleHost.dll noninteractive

[Install]
WantedBy=multi-user.target
```

* Führen Sie den folgenden Befehl aus, um systemd anzuweisen, die lokale Weiterleitung bei jedem Start zu starten.

```
systemctl enable localforwarder
```

* Führen Sie den folgenden Befehl aus, um systemd anzuweisen, die lokale Weiterleitung sofort zu starten.

```
systemctl start localforwarder
```

* Überwachen Sie den Dienst, indem Sie die * *.log*-Dateien im Verzeichnis /home/SAMPLE_USER/LOCALFORWARDER_DIR überprüfen.

### <a name="mac"></a>Mac
Die lokale Weiterleitung kann möglicherweise mit macOS verwendet werden, aber dies wird derzeit nicht offiziell unterstützt.

### <a name="self-hosting"></a>Self-Hosting
Die lokale Weiterleitung wird auch als ein .NET Standard-NuGet-Paket verteilt, sodass Sie sie in Ihrer eigenen .NET-Anwendung hosten können.

```C#
using Library;
...
Host host = new Host();

// see section below on configuring local forwarder
string configuration = ...;
    
host.Run(config, TimeSpan.FromSeconds(5));
...
host.Stop();
```

## <a name="configuring-local-forwarder"></a>Konfigurieren der lokalen Weiterleitung

* Wenn Sie einen der eigenen Hosts der lokalen Weiterleitung (Konsolenhost oder Windows-Diensthost) ausführen, wird **LocalForwarder.config** neben der Binärdatei angezeigt.
* Beim Self-Hosting des NuGet-Pakets der lokalen Weiterleitung muss die Konfiguration im Code im gleichen Format angegeben werden (siehe Abschnitt über Self-Hosting). Die Konfigurationssyntax finden Sie in der Datei [LocalForwarder.config](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/ConsoleHost/LocalForwarder.config) im GitHub-Repository. 

> [!NOTE]
> Die Konfiguration kann sich von Release zu Release ändern, also achten Sie darauf, welche Version Sie verwenden.

## <a name="monitoring-local-forwarder"></a>Überwachen der lokalen Weiterleitung

Ablaufverfolgungsdateien werden in das Dateisystem neben der ausführbaren Datei geschrieben, die die lokale Weiterleitung ausführt (suchen Sie nach * *.log*-Dateien). Sie können eine Datei mit dem Namen *NLog.config* neben der ausführbaren Datei platzieren, um Ihre eigene Konfiguration anstelle der Standardkonfiguration bereitzustellen. Die Beschreibung des Formats finden Sie in der [Dokumentation](https://github.com/NLog/NLog/wiki/Configuration-file#configuration-file-format).

Wenn keine Konfigurationsdatei bereitgestellt wird (dies ist die Standardeinstellung), verwendet die lokale Weiterleitung die Standardkonfiguration, die Sie [hier](https://github.com/Microsoft/ApplicationInsights-LocalForwarder/blob/master/src/Common/NLog.config) finden.

## <a name="next-steps"></a>Nächste Schritte

* [OpenCensus](https://opencensus.io/)
