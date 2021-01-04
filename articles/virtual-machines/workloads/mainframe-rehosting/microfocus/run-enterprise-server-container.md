---
title: Ausführen von Micro Focus Enterprise Server 5.0 in einem Docker-Container auf Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Micro Focus Enterprise Server 5.0 in einem Docker-Container auf Microsoft Azure ausgeführt wird.
services: virtual-machines-linux
documentationcenter: ''
author: maggsl
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 06/29/2020
tags: ''
keywords: ''
ms.service: multiple
ms.openlocfilehash: f34767c160c8229eb5b63806924926a46ea00cc2
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127194"
---
# <a name="run-micro-focus-enterprise-server-50-in-a-docker-container-on-azure"></a>Ausführen von Micro Focus Enterprise Server 5.0 in einem Docker-Container auf Azure

Sie können Micro Focus Enterprise Server 5.0 in einem Docker-Container auf Azure ausführen. In diesem Tutorial erfahren Sie, wie. Es verwendet die Windows CICS (Customer Information Control System) acctdemo-Demo für Enterprise Server.

Durch Docker werden Anwendungen portabel und isoliert. Beispielsweise können Sie ein Docker-Image von einer Windows-VM exportieren, um es auf einer anderen auszuführen, oder aus einem Repository auf einen Windows-Server mit Docker exportieren. Das Docker-Image wird am neuen Speicherort mit der gleichen Konfiguration ausgeführt – ohne dass Enterprise Server installiert werden muss. Er ist Bestandteil des Images. Lizenzierungsaspekte sind aber nach wie vor zu beachten.

In diesem Tutorial wird die **Windows 2016 Datacenter mit Containern** -VM aus dem Azure Marketplace installiert. Dieser virtuelle Computer beinhaltet **Docker 18.09.0**. In den Schritten weiter unten erfahren Sie, wie Sie den Container bereitstellen, ihn ausführen und anschließend mit einem 3270-Emulator eine Verbindung mit ihm herstellen.

## <a name="prerequisites"></a>Voraussetzungen

Überprüfen Sie zunächst diese Voraussetzungen:

-   Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

-   Die Micro Focus-Software und eine gültige Lizenz (oder Testlizenz). Wenn Sie ein Micro Focus-Bestandskunde sind, wenden Sie sich an Ihren Micro Focus-Vertreter. Andernfalls können Sie [eine Testversion anfordern](https://www.microfocus.com/products/enterprise-suite/enterprise-server/trial/).

    > [!Note] 
    > Die Docker-Demodateien sind im Lieferumfang von Enterprise Server 5.0 enthalten. In diesem Tutorial wird „ent\_server\_dockerfiles\_5.0\_windows.zip“ verwendet. Greifen Sie darauf von dem gleichen Ort zu, an dem Sie auch auf die Enterprise Server-Installationsdatei zugegriffen haben, oder navigieren Sie zu *Micro Focus* , um anzufangen.

-   Die Dokumentation für [Enterprise Server und Enterprise Developer](https://www.microfocus.com/documentation/enterprise-developer/#%22).

## <a name="create-a-vm"></a>Erstellen einer VM

1.  Sichern Sie die Medien aus der Datei „ent\_server\_dockerfiles\_5.0\_windows.zip“. Sichern Sie die Lizenzierungsdatei „ES-Docker-Prod-XXXXXXXX.mflic“ (sie wird zum Erstellen der Docker-Images benötigt).

2.  Erstellen Sie den virtuellen Computer. Öffnen Sie zu diesem Zweck das Azure-Portal, wählen Sie **Ressource erstellen** im obersten linken Menü aus, und filtern Sie nach *windows server betriebssystem*. Wählen Sie in den Ergebnissen den Eintrag **Windows Server.** aus. Wählen Sie in der nächsten Anzeige den Eintrag **Windows Server 2016 Datacenter – mit Containern** aus.

    ![Screenshot von Suchergebnisse im Azure-Portal](./media/run-image-1.png)

3.  Um die Eigenschaften der VM zu konfigurieren, wählen Sie die Instanzdetails aus:

    1.  Wählen Sie eine VM-Größe aus. Erwägen Sie für dieses Tutorial die Verwendung einer **Standard DS2\_v3** -VM mit 2 vCPUs und 16 GB Arbeitsspeicher.

    2.  Wählen Sie die **Region** und **Ressourcengruppe** aus, in denen Sie die Bereitstellung vornehmen möchten.

    3.  Verwenden Sie für die **Verfügbarkeitsoptionen** die Standardeinstellung.

    4.  Geben Sie als **Benutzername** das gewünschte Administratorkonto und dessen Kennwort ein.

    5.  Vergewissern Sie sich, dass **Port 3389 RDP** geöffnet ist. Nur dieser Port muss öffentlich verfügbar gemacht werden, damit Sie sich bei der VM anmelden können. Akzeptieren Sie dann alle Standardwerte, und klicken Sie auf **Überprüfen + erstellen**.

    ![Screenshot von Fenster „Virtuellen Computer erstellen“](./media/run-image-2.png)

4.  Warten Sie, bis die Bereitstellung abgeschlossen ist (das dauert ein paar Minuten). Eine Meldung zeigt an, dass Ihre VM erstellt wurde.

5.  Wählen Sie **Zu Ressource wechseln** aus, um zum Blatt **Übersicht** Ihrer VM zu gelangen.

6.  Wählen Sie auf der rechten Seite **Verbinden** aus. Die Optionen für **Verbindung mit virtuellem Computer herstellen** werden auf der rechten Seite angezeigt.

7.  Wählen Sie die Schaltfläche **RDP-Datei herunterladen** aus, um die RDP-Datei (Remote Desktop Protocol) herunterzuladen, mit der Sie sich mit dem virtuellen Computer verbinden können.

8.  Nachdem der Download der Datei abgeschlossen wurde, öffnen Sie sie, und geben Sie den Benutzernamen und das Kennwort ein, die Sie für die VM erstellt haben.

    > [!Note]    
    > Verwenden Sie nicht Ihre Firmenanmeldeinformationen für die Anmeldung. (Der RDP-Client geht davon aus, dass Sie diese verwenden möchten. Dies ist nicht der Fall.)

9.  Wählen Sie **Weitere Optionen** und dann Ihre VM-Anmeldeinformationen aus.

An diesem Punkt wird die VM ausgeführt und mithilfe von RDP verbunden. Sie sind angemeldet und bereit, den nächsten Schritt auszuführen.

## <a name="create-a-sandbox-directory-and-upload-the-zip-file"></a>Erstellen eines Sandboxverzeichnisses und Hochladen der ZIP-Datei

1.  Erstellen Sie ein Verzeichnis auf dem virtuellen Computer, in das Sie die Demo- und Lizenzdateien hochladen können. Beispielsweise **C:\\Sandbox**.

2.  Laden Sie **ent\_server\_dockerfiles\_5.0\_windows.zip** und die Datei **ES-Docker-Prod-XXXXXXXX.mflic** in das erstellte Verzeichnis hoch.

3.  Extrahieren Sie den Inhalt der ZIP-Datei in das Verzeichnis **ent\_server\_dockerfiles\_5.0\_windows** , das vom Extrahierungsvorgang erstellt wird. Dieses Verzeichnis enthält eine Infodatei (in Form einer HTML- und einer TXT-Datei) und zwei Unterverzeichnisse, **EnterpriseServer** und **Examples**.

4.  Kopieren Sie **ES-Docker-Prod-XXXXXXXX.mflic** in das Verzeichnis „C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer“ und das Verzeichnis „C:\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS“.  
      
    > [!Note]
    > Achten Sie darauf, die Lizenzierungsdatei in beide Verzeichnisse zu kopieren. Sie sind erforderlich, damit der Docker-Erstellungsschritt sicherstellen kann, dass die Images ordnungsgemäß lizenziert sind.

## <a name="check-docker-version-and-create-base-image"></a>Überprüfen der Docker-Version und Erstellen des Basisimage

> [!Important]  
> Das Erstellen des geeigneten Docker-Image erfolgt in zwei Schritten. Erstellen Sie zunächst das Enterprise Server 5.0-Basisimage. Erstellen Sie anschließend ein weiteres Image für die x64-Plattform. Zwar können Sie ein x86-Image (32-Bit) erstellen, Sie sollten aber das 64-Bit-Image verwenden.

1.  Öffnen Sie eine Eingabeaufforderung.

2.  Überprüfen Sie, ob Docker installiert ist. Geben Sie an der Eingabeaufforderung Folgendes ein: **docker version**.  
    Als dieser Text geschrieben wurde, war die Version 18.09.0 aktuell.

3.  Um das Verzeichnis zu wechseln, geben Sie Folgendes ein:  
    **cd \\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\EnterpriseServer**.

4.  Geben Sie **bld.bat IacceptEULA** ein, um den Erstellungsprozess für das anfängliche Basisimage zu starten. Warten Sie einige Minuten, bis der Vorgang vollständig ausgeführt ist. Achten Sie in den Ergebnissen auf die zwei Images, die erstellt wurden – eins für x64 und eins für x86:

    ![Befehlsfenster mit Anzeige der Images](./media/run-image-3.png)

5.  Um das endgültige Image für die CICS-Demo zu erstellen, wechseln Sie in das CICS-Verzeichnis, indem Sie **cd\\Sandbox\\ent\_server\_dockerfiles\_5.0\_windows\\Examples\\CICS** eingeben.

6.  Geben Sie **bld.bat x64** ein, um das Image zu erstellen. Warten Sie einige Minuten, bis der Prozess vollständig ausgeführt wurde, und eine Meldung anzeigt, dass das Image erstellt wurde.

7.  Geben Sie **docker images** ein, um eine Liste aller auf dem virtuellen Computer installierten Docker-Images anzuzeigen. Vergewissern Sie sich, dass **microfocus/es-acctdemo** eins davon ist.

    ![Befehlsfenster mit der Anzeige des es-acctdemo-Images](./media/run-image-4.png)

## <a name="run-the-image"></a>Ausführen des Images

1.  Um Enterprise Server 5.0 und die acctdemo-Anwendung zu starten, geben Sie an der Eingabeaufforderung Folgendes ein:

    ```
    **docker run -p 16002:86/tcp -p 16002:86/udp -p 9040-9050:9040-9050 -p 9000-9010:9000-9010 -ti --network="nat" --rm microfocus/es-acctdemo:win\_5.0\_x64
    ```

1.  Installieren Sie einen 3270-Terminalemulator, beispielsweise [x3270](http://x3270.bgp.nu/), und verwenden Sie ihn, um über Port 9040 eine Verbindung mit dem ausgeführten Image herzustellen.

2.  Rufen Sie die IP-Adresse des acctdemo-Containers ab, damit Docker als DHCP-Server (Dynamic Host Configuration Protocol) für die von ihm verwalteten Container fungieren kann:

    1.  Rufen Sie die ID des ausgeführten Containers ab. Geben Sie an der Eingabeaufforderung **Docker ps** ein, und halten Sie die ID fest (in diesem Beispiel **22a0fe3159d0** ). Speichern Sie sie für den nächsten Schritt.

    2.  Um die IP-Adresse für den acctdemo-Container abzurufen, verwenden Sie die Container-ID aus dem vorherigen Schritt in folgender Weise:

    ```
    docker inspect \<containerID\> --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

    Beispiel:

    ```
    docker inspect 22a0fe3159d0 --format="{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}"
    ```

4. Halten Sie die IP-Adresse des acctdemo-Images fest. Beispielsweise ist die Adresse in der folgenden Ausgabe 172.19.202.52.

    ![Screenshot von Befehlsfenster mit Anzeige der IP-Adresse](./media/run-image-5.png)

5. Binden Sie das Abbild mithilfe des Emulators ein. Konfigurieren Sie den Emulator mit der Adresse des acctdemo-Images und Port 9040. In diesem Fall lauten diese **172.19.202.52:9040**. Ihre werden ähnlich sein. Der Bildschirm **Signon to CICS** wird geöffnet.

    ![Screenshot von „Signon to CICS“](./media/run-image-6.png)

6. Melden Sie sich bei der CICS-Region an, indem Sie **SYSAD** als **USERID** und **SYSAD** als **Kennwort** eingeben.

7. Löschen Sie den Bildschirm mithilfe der Tastenzuordnung des Emulators. Wählen Sie für x3270 die Menüoption **Keymap** aus.

8. Geben Sie **ACCT** ein, um die acctdemo-Anwendung zu starten. Der Startbildschirm der Anwendung wird angezeigt.

     ![Der Screenshot zeigt ein Konsolenfenster, in dem die Anwendung angezeigt wird.](./media/run-image-7.png)

9. Experimentieren Sie mit Typen von Anzeigekonten. Geben Sie z.B. **D** als Anforderung und **11111** für das **KONTO** ein. Andere Kontonummern, die Sie ausprobieren können, sind 22222, 33333 usw.

    ![Der Screenshot zeigt die Bearbeitung verschiedener Werte in der Anwendung.](./media/run-image-8.png)

10. Um die Enterprise Server-Verwaltungskonsole anzuzeigen, wechseln Sie zur Eingabeaufforderung, und geben Sie **start http:172.19.202.52:86** ein.

    ![Enterprise Server-Verwaltungskonsole](media/run-image-9.png)

Das ist alles! Jetzt können Sie eine CICS-Anwendung in einem Docker-Container ausführen und verwalten.

## <a name="next-steps"></a>Nächste Schritte

-   [Installieren von Micro Focus Enterprise Server 5.0 und Enterprise Developer 5.0 in Azure](./set-up-micro-focus-azure.md)

-   [Migration von Mainframeanwendungen](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/application-strategies)
