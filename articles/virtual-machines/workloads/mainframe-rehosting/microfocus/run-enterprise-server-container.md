---
title: Ausführen von Micro Focus Enterprise Server 4.0 in einem Docker-Container auf virtuellen Azure-Computern
description: Platzieren Sie Ihre IBM z/OS-Mainframeworkloads auf einem neuen Host, indem Sie den Micro Focus Enterprise Server in einem Docker-Container auf virtuellen Azure-Computern ausführen.
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: sread
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 30d99c3f4767eb50361f7074c0d508fcf309faca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61488457"
---
# <a name="run-micro-focus-enterprise-server-40-in-a-docker-container-on-azure"></a>Ausführen von Micro Focus Enterprise Server 4.0 in einem Docker-Container auf Azure

Sie können Micro Focus Enterprise Server 4.0 in einem Docker-Container auf Azure ausführen. In diesem Tutorial erfahren Sie, wie. Es verwendet die Windows CICS (Customer Information Control System) acctdemo-Demo für Enterprise Server.

Durch Docker werden Anwendungen portabel und isoliert. Beispielsweise können Sie ein Docker-Image aus einer Windows-VM exportieren, um es in einer anderen auszuführen, oder aus einem Repository in einen Windows-Server mit Docker exportieren. Das Docker-Image wird am neuen Speicherort mit der gleichen Konfiguration ausgeführt – ohne dass Enterprise Server installiert werden muss. Er ist Bestandteil des Images. Lizenzierungsaspekte sind aber nach wie vor zu beachten.

In diesem Tutorial wird der virtuelle **Windows 2016 Datacenter mit Containern**-Computer aus dem Azure Marketplace installiert. Dieser virtuelle Computer beinhaltet **Docker 18.09.0**. In den Schritten unten erfahren Sie, wie Sie den Container bereitstellen, ihn ausführen und anschließend mit einem 3270-Emulator eine Verbindung mit ihm herstellen.

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie zunächst diese Voraussetzungen:

- Ein Azure-Abonnement. Wenn Sie keins besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Die Micro Focus-Software und eine gültige Lizenz (oder Testlizenz). Wenn Sie ein Micro Focus-Bestandskunde sind, wenden Sie sich an Ihren Micro Focus-Vertreter. Andernfalls können Sie [eine Testversion anfordern](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

     > [!NOTE]
     > Die Docker-Demodateien sind im Lieferumfang von Enterprise Server 4.0 enthalten. In diesem Tutorial wird die Datei „ent\_server\_dockerfiles\_4.0\_windows.zip“ verwendet. Greifen Sie darauf von dem gleichen Ort zu, an dem Sie auch auf die Enterprise Server-Installationsdatei zugegriffen haben, oder navigieren Sie zu *Micro Focus*, um anzufangen.

- Die Dokumentation für [Enterprise Server und Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#").

## <a name="create-a-vm"></a>Erstellen einer VM

1. Sichern Sie die Medien aus der Datei „ent\_server\_dockerfiles\_4.0\_windows.zip“. Sichern Sie die Lizenzierungsdatei „ES-Docker-Prod-XXXXXXXX.mflic“ (sie wird zum Erstellen der Docker-Images benötigt).

2. Erstellen Sie den virtuellen Computer. Öffnen Sie zu diesem Zweck das Azure-Portal, wählen Sie oben links **Ressource erstellen** aus, und filtern Sie nach *windows server*. Wählen Sie in den Ergebnissen **Windows Server 2016 Datacenter – mit Containern** aus.

     ![Suchergebnisse im Azure-Portal](media/01-portal.png)

3. Um die Eigenschaften der VM zu konfigurieren, wählen Sie die Instanzdetails aus:

    1. Wählen Sie eine VM-Größe aus. Erwägen Sie für dieses Tutorial die Verwendung eines virtuellen Computers **Standard DS2\_v2** mit 2 vCPUs und 7 GB Arbeitsspeicher.

    2. Wählen Sie die **Region** und **Ressourcengruppe** aus, in denen Sie bereitstellen möchten.

    3. Verwenden Sie für die **Verfügbarkeitsoptionen** die Standardeinstellung.

    4. Geben Sie als **Benutzername** das gewünschte Administratorkonto und dessen Kennwort ein.

    5. Vergewissern Sie sich, dass **Port 3389 RDP** geöffnet ist. Nur dieser Port muss öffentlich verfügbar gemacht werden, damit Sie sich bei der VM anmelden können. Akzeptieren Sie dann alle Standardwerte, und klicken Sie auf **Überprüfen + erstellen**.

     ![Bereich „Virtuellen Computer erstellen“](media/container-02.png)

4. Warten Sie, bis die Bereitstellung abgeschlossen ist (das dauert ein paar Minuten). Eine Meldung zeigt an, dass Ihre VM erstellt wurde.

5. Klicken Sie auf **Zu Ressource wechseln**, um zum Blatt **Übersicht** Ihrer VM zu gelangen.

6. Klicken Sie auf der rechten Seite auf die Schaltfläche **Verbinden**. Die Optionen für **Verbindung mit virtuellem Computer herstellen** werden auf der rechten Seite angezeigt.

7. Klicken Sie auf die Schaltfläche **RDP-Datei herunterladen**, um die RDP-Datei herunterzuladen, mit der Sie sich mit dem virtuellen Computer verbinden können.

8. Nachdem der Download der Datei abgeschlossen wurde, öffnen Sie sie, und geben Sie den Benutzernamen und das Kennwort ein, die Sie für die VM erstellt haben.

     > [!NOTE]
     > Verwenden Sie nicht Ihre Firmenanmeldeinformationen für die Anmeldung. (Der RDP-Client geht davon aus, dass Sie diese verwenden möchten. Dies ist nicht der Fall.)

9.  Wählen Sie **Weitere Optionen** und dann Ihre VM-Anmeldeinformationen aus.

An diesem Punkt wird die VM ausgeführt und mithilfe von RDP verbunden. Sie sind angemeldet und bereit, den nächsten Schritt auszuführen.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Erstellen eines Sandboxverzeichnisses und Hochladen der ZIP-Datei

1.  Erstellen Sie ein Verzeichnis auf dem virtuellen Computer, in das Sie die Demo- und Lizenzdateien hochladen können. Beispielsweise **C:\\Sandbox**.

2.  Laden Sie **ent\_server\_dockerfiles\_4.0\_windows.zip** und die Datei **ES-Docker-Prod-XXXXXXXX.mflic** in das erstellte Verzeichnis hoch.

3.  Extrahieren Sie den Inhalt der ZIP-Datei in das Verzeichnis **ent\_server\_dockerfiles\_4.0\_windows**, das vom Extrahierungsvorgang erstellt wird. Dieses Verzeichnis enthält eine Infodatei (in Form einer HTML- und einer TXT-Datei) und zwei Unterverzeichnisse, **EnterpriseServer** und **Examples**.

4.  Kopieren Sie **ES-Docker-Prod-XXXXXXXX.mflic** in das Verzeichnis „C:\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\EnterpriseServer“ und das Verzeichnis „C:\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS“.

> [!NOTE]
> Achten Sie darauf, die Lizenzierungsdatei in beide Verzeichnisse zu kopieren. Sie sind erforderlich, damit der Docker-Erstellungsschritt sicherstellen kann, dass die Images ordnungsgemäß lizenziert sind.

## <a name="check-docker-version-and-create-base-image"></a>Überprüfen der Docker-Version und Erstellen des Basisimage

> [!IMPORTANT]
> Das Erstellen des geeigneten Docker-Image erfolgt in zwei Schritten. Erstellen Sie zunächst das Enterprise Server 4.0-Basisimage. Erstellen Sie anschließend ein weiteres Image für die x64-Plattform. Zwar können Sie ein x86-Image (32-Bit) erstellen, Sie sollten aber das 64-Bit-Image verwenden.

1. Öffnen Sie eine Eingabeaufforderung.

2. Überprüfen Sie, ob Docker installiert ist. Geben Sie an der Eingabeaufforderung Folgendes ein:

    ```
        docker version
    ```

     Als dieser Text geschrieben wurde, war die Version 18.09.0 aktuell.

3. Um das Verzeichnis zu wechseln, geben Sie **cd \Sandbox\ent_server_dockerfiles_4.0_windows\EnterpriseServer** ein.

4. Geben Sie **bld.bat IacceptEULA** ein, um den Erstellungsprozess für das anfängliche Basisimage zu starten. Warten Sie einige Minuten, bis der Vorgang vollständig ausgeführt ist. Achten Sie in den Ergebnissen auf die zwei Images, die erstellt wurden – eins für x64 und eins für x86:

     ![Befehlsfenster mit Anzeige der Images](media/container-04.png)

5. Um das endgültige Image für die CICS-Demo zu erstellen, wechseln Sie in das CICS-Verzeichnis, indem Sie **cd\\Sandbox\\ent\_server\_dockerfiles\_4.0\_windows\\Examples\\CICS** eingeben.

6. Geben Sie **bld.bat x64** ein, um das Image zu erstellen. Warten Sie einige Minuten, bis der Prozess vollständig ausgeführt wurde, und eine Meldung anzeigt, dass das Image erstellt wurde.

7. Geben Sie **docker images** ein, um eine Liste aller auf dem virtuellen Computer installierten Docker-Images anzuzeigen. Vergewissern Sie sich, dass **microfocus/es-acctdemo** eins davon ist.

     ![Befehlsfenster mit der Anzeige des es-acctdemo-Images](media/container-05.png)

## <a name="run-the-image"></a>Ausführen des Images 

1.  Um Enterprise Server 4.0 und die acctdemo-Anwendung zu starten, geben Sie an der Eingabeaufforderung Folgendes ein:

    ```
         docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win_4.0_x64
    ```

2.  Installieren Sie einen 3270-Terminalemulator, beispielsweise [x3270](http://x3270.bgp.nu/), und verwenden Sie ihn, um über Port 9040 eine Verbindung mit dem ausgeführten Image herzustellen.

3.  Rufen Sie die IP-Adresse des acctdemo-Containers ab, damit Docker als DHCP-Server für die von ihm verwalteten Container fungieren kann:

    1.  Rufen Sie die ID des ausgeführten Containers ab. Geben Sie an der Eingabeaufforderung **Docker ps** ein, und halten Sie die ID fest (in diesem Beispiel **22a0fe3159d0**). Speichern Sie sie für den nächsten Schritt.

    2.  Um die IP-Adresse für den acctdemo-Container abzurufen, verwenden Sie die Container-ID aus dem vorherigen Schritt in folgender Weise:

    ```
       docker inspect <containerID> --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

       Beispiel:

    ```   
        docker inspect 22a0fe3159d0 --format="{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```
4. Halten Sie die IP-Adresse des acctdemo-Images fest. Beispielsweise ist die Adresse in der folgenden Ausgabe 172.19.202.52.

     ![Befehlsfenster mit Anzeige der IP-Adresse](media/container-06.png)

5. Binden Sie das Abbild mithilfe des Emulators ein. Konfigurieren Sie den Emulator mit der Adresse des acctdemo-Images und Port 9040. In diesem Fall lauten diese **172.19.202.52:9040**. Ihre werden ähnlich sein. Der Bildschirm **Signon to CICS** wird geöffnet.

    ![Bildschirm „Signon to CICS“](media/container-07.png)

6. Melden Sie sich bei der CICS-Region an, indem Sie **SYSAD** als **USERID** und **SYSAD** als **Kennwort** eingeben.

7. Löschen Sie den Bildschirm mithilfe der Tastenzuordnung des Emulators. Wählen Sie für x3270 die Menüoption **Keymap** aus.

8. Geben Sie **ACCT** ein, um die acctdemo-Anwendung zu starten. Der Startbildschirm der Anwendung wird angezeigt.

     ![Kontodemo-Bildschirm](media/container-08.png)

9. Experimentieren Sie mit Typen von Anzeigekonten. Geben Sie z.B. **D** als Anforderung und **11111** für das **KONTO** ein. Andere Kontonummern, die Sie ausprobieren können, sind 22222, 33333 usw.

     ![Kontodemo-Bildschirm](media/container-09.png)

10. Um die Enterprise Server-Verwaltungskonsole anzuzeigen, wechseln Sie zur Eingabeaufforderung, und geben Sie **start http:172.19.202.52:86** ein.

     ![Enterprise Server-Verwaltungskonsole](media/container-010.png)

Das ist alles! Jetzt können Sie eine CICS-Anwendung in einem Docker-Container ausführen und verwalten.

## <a name="next-steps"></a>Nächste Schritte

- [Installieren von Micro Focus Enterprise Server 4.0 und Enterprise Developer 4.0 in Azure](./set-up-micro-focus-azure.md)
- [Migration von Mainframeanwendungen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
