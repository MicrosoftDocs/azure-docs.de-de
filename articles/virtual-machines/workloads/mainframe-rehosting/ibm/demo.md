---
title: Festlegen einer Application Developers Controlled Distribution (ADCD) in IBM zD&T v1 | Microsoft-Dokumentation
description: Führen Sie eine IBM Z Development and Test Environment (zD&T) – eine Entwicklungs- und Testumgebung – auf virtuellen Azure-Computern (VMs) aus.
services: virtual-machines-linux
ms.service: virtual-machines-linux
ms.subservice: workloads
documentationcenter: ''
author: njray
manager: edprice
ms.author: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: dcd354b906b4d6c92d8b3186fc8e09c94a31ca55
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968399"
---
# <a name="set-up-an-application-developers-controlled-distribution-adcd-in-ibm-zdt-v1"></a>Festlegen einer Application Developers Controlled Distribution (ADCD) in IBM zD&T v1

Sie können eine IBM Z Development and Test Environment (zD&T) auf virtuellen Azure-Computern (VMs) ausführen. Diese Umgebung emuliert die Z Series-Architektur von IBM. Sie können eine Vielzahl von Betriebssystemen oder Installationen der Z-Serie (auch als Z-Instanzen oder -Pakete bezeichnet) hosten, die durch benutzerdefinierte Pakete verfügbar gemacht werden, die als IBM Application Developers Controlled Distributions (ADCDs) bezeichnet werden.

In diesem Artikel wird das Einrichten einer ADCD-Instanz in einer zD&T-Umgebung in Azure veranschaulicht. ADCDs erstellen vollständige Implementierungen von Z Series-Betriebssystemen für Entwicklungs- und Testumgebungen, die in zD&T ausgeführt werden.

Wie zD&T sind ADCDs nur für IBM-Kunden und -Partner verfügbar und ausschließlich für Entwicklungs- und Testzwecke bestimmt. Sie dürfen nicht für Produktionsumgebungen verwendet werden. Zahlreiche Installationspakete von IBM stehen zum Herunterladen mithilfe von [Passport Advantage](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.guide.adcd.doc/topics/installation_ps.html) oder [IBM PartnerWorld](https://www.ibm.com/partnerworld/public) zur Verfügung.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Die zuvor in Azure eingerichtete [zD&T-Umgebung][ibm-install-z]. Dieser Artikel setzt voraus, dass Sie das gleiche Ubuntu 16.04 VM-Image verwenden, dass Sie früher erstellt haben.

- Zugriff auf die ADCD-Medien über IBM PartnerWorld oder Passport Advantage.

- Ein [ Lizenzierungsserver](https://www.ibm.com/support/knowledgecenter/en/SSTQBD_12.0.0/com.ibm.zsys.rdt.tools.user.guide.doc/topics/zdt_ee.html). Dies ist zum Ausführen von IBM zD&T erforderlich. Die Erstellungsart ist davon abhängig, wie Sie die Software von IBM lizenzieren:

  - Für einen **hardwarebasierten Lizenzierungsserver** ist ein USB-Hardwaregerät erforderlich, das die zum Zugriff auf alle Teile der Software erforderlichen Rational Tokens enthält. Dieses müssen Sie bei IBM anfordern.

  - Für einen **softwarebasierten Lizenzierungsserver** müssen Sie einen zentralen Server für die Verwaltung der Lizenzschlüssel einrichten. Diese Methode wird bevorzugt und erfordert, dass Sie die Schlüssel einrichten, die Sie von IBM auf dem Verwaltungsserver erhalten.

## <a name="download-the-installation-packages-from-passport-advantage"></a>Herunterladen der Installationspakete von Passport Advantage

Zugriff auf die ADCD-Medien ist erforderlich. Die folgenden Schritte setzen voraus, dass Sie ein IBM-Kunde sind und Passport Advantage nutzen können. IBM-Partner können [IBM PartnerWorld](https://www.ibm.com/partnerworld/public) nutzen.

> [!NOTE]
> In diesem Artikel wird vorausgesetzt, dass zum Zugriff auf das Azure-Portal und Herunterladen der IBM-Medien ein Windows-PC verwendet wird. Wenn Sie einen Mac- oder Ubuntu-Desktop verwenden, können die Befehle und der Vorgang zum Abrufen der IBM-Medien sich leicht unterscheiden.

1. Melden Sie sich bei [Passport Advantage](https://www.ibm.com/software/howtobuy/passportadvantage/paocustomer) an.

2. Wählen Sie **Software Downloads** und **Media Access** (Medienzugriff) aus.

3. Wählen Sie **Program offering and agreement number** (Programmangebot und Vertragsnummer) aus, und klicken Sie auf **Continue** (Weiter).

4. Geben Sie die Teilebeschreibung oder -nummer ein, und klicken Sie auf **Finder** (Sucher).

5. Klicken Sie optional auf die alphabetisch geordnete Liste, um die Produkte nach Namen sortiert anzuzeigen.

6. Wählen Sie **All Operating Systems** (Alle Betriebssysteme) im Feld **Operating system** (Betriebssystem) und **All Languages** (Alle Sprachen) im Feld **Languages** (Sprachen) aus. Klicken Sie dann auf **Go** (Los).

7. Klicken Sie auf **Select individual files** (Einzelne Dateien auswählen), um die Liste zu erweitern und die einzelnen herunterzuladenden Medien anzuzeigen.

8. Überprüfen Sie die Pakete, die Sie herunterladen möchten, wählen Sie **Download** (Herunterladen) aus, und laden Sie dann die Dateien in das gewünschte Verzeichnis herunter.

## <a name="upload-the-adcd-packages"></a>Hochladen der ADCD-Pakete

Nun müssen Sie die heruntergeladenen Pakete zu Ihrem virtuellen Computer in Azure hochladen.

1. Initiieren Sie im Azure-Portal eine **ssh**-Sitzung mit der Ubuntu-VM, die Sie erstellt haben. Wechseln Sie zu Ihrem virtuellen Computer, wählen Sie das Blatt **Übersicht** und dann **Verbinden** aus.

2. Wählen Sie die **SSH**-Registerkarte aus, und kopieren Sie den ssh-Befehl in die Zwischenablage.

3. Melden Sie sich mit Ihren Anmeldeinformationen und dem [SSH-Client](../../../linux/use-remote-desktop.md) Ihrer Wahl bei Ihrem virtuellen Computer an. Diese Demo verwendet die Linux-Erweiterungen für Windows 10, wodurch der Windows-Eingabeaufforderung eine Bash-Shell hinzugefügt wird. PuTTY funktioniert genauso gut.

4. Wenn Sie angemeldet sind, erstellen Sie ein Verzeichnis, um die IBM-Pakete hochzuladen. Denken Sie daran, dass in Linux die Groß-/Kleinschreibung beachtet wird. Diese Demo setzt beispielsweise voraus, dass die Pakete zu diesem Speicherort hochgeladen werden:

    `/home/MyUserID/ZDT/adcd/nov2017/volumes`

5. Laden Sie die Dateien mit einem SSH-Client wie[WinSCP](https://winscp.net/eng/index.php) hoch. Da SCP ein Teil von SSH ist, wird Port 22 verwendet, den auch SSH nutzt. Wenn Ihr lokaler Computer kein Windows-Computer ist, können Sie den [scp-Befehl](http://man7.org/linux/man-pages/man1/scp.1.html) in Ihrer SSH-Sitzung eingeben.

6. Initiieren Sie den Upload in das Azure-VM-Verzeichnis, das Sie erstellt haben, das damit zum Imagespeicher für zD&T wird.

    > [!NOTE]
    > Stellen Sie sicher, dass **ADCDTOOLS.XML** sich im Upload in das Verzeichnis **home/MyUserID/ZDT/adcd/nov2017** befindet. Sie benötigen sie später.

7. Warten Sie, bis die Dateien hochgeladen sind, was je nach Ihrer Verbindung mit Azure einige Zeit dauern kann.

8. Wenn die Uploads abgeschlossen sind, navigieren Sie zum Volumesverzeichnis, und dekomprimieren Sie alle **gz**-Volumes:

    ```console
    gunzip \*.gz
    ```
    
![Datei-Explorer mit dekomprimierten gz-Volumes](media/01-gunzip.png)

## <a name="configure-the-image-storage"></a>Konfigurieren des Imagespeichers

Der nächste Schritt ist, zD&T zur Verwendung der hochgeladenen Pakete zu konfigurieren. Über den Imagespeicherprozess in zD&T können Sie die Images einbinden und verwenden. Sie können SSH oder FTP verwenden.

1. Starten Sie den **zDTServer**. Dies müssen Sie auf der Stammebene ausführen. Geben Sie der Reihe nach diese beiden Befehle ein:
    ```console
    sudo su -
    /opt/ibm/zDT/bin/startServer
    ```
2. Beachten Sie die URL-Ausgabe des Befehls, und verwenden Sie diese URL zum Zugriff auf den Webserver. Sie sieht etwa so aus:
     > https://(Name Ihrer VM oder IP-Adresse):9443/ZDTMC/index.html
     >
     > Beachten Sie, dass Ihr Webzugriff über Port 9443 erfolgt. Melden Sie sich hiermit beim Webserver an. Die Benutzer-ID für zD&T ist **zdtadmin**, und das Kennwort ist **password**.

    ![Willkommensbildschirm der IBM zD&T Enterprise Edition](media/02-welcome.png)

3. Wählen Sie auf der Seite **Quick Start** (Schnellstart) unter **Configure** (Konfigurieren) die Option **Image Storage** (Imagespeicher) aus.

     ![Schnellstartbildschirm der IBM zD&T Enterprise Edition](media/03-quickstart.png)

4. Wählen Sie auf der Seite **Configure image storage** (Imagespeicher konfigurieren) die Option **SSH File Transfer Protocol** (SSH-Dateiübertragungsprotokoll) aus.

5. Geben Sie für **Host name** (Hostname) **Localhost** ein, und geben Sie den Verzeichnispfad zu den hochgeladenen Images ein. Beispiel: „/home/MyUserID/ZDT/adcd/nov2017/volumes“.

6. Geben Sie **User ID** (Benutzer-ID) und **password** (Kennwort) für die VM ein. Verwenden Sie nicht Benutzer-ID und Kennwort für zD&T.

7. Testen Sie die Verbindung, um sicherzustellen, dass Sie Zugriff haben, und wählen Sie dann **Save** (Speichern) zum Speichern der Konfiguration aus.

## <a name="configure-the-target-environments"></a>Konfigurieren der Zielumgebungen

Der nächste Schritt ist das Konfigurieren der zD&T-Zielumgebung. In dieser emulierten gehosteten Umgebung werden Ihre Images ausgeführt.

1. Wählen Sie auf der Seite **Quick Start** (Schnellstart) unter **Configure** (Konfigurieren) die Option **Target environments** (Zielumgebungen) aus.

2. Wählen Sie auf der Seite **Configure target environments** (Zielumgebungen konfigurieren) **Add Target** (Ziel hinzufügen) aus.

3. Wählen Sie **Linux** aus. IBM unterstützt zwei Arten von Umgebungen, Linux und Cloud(OpenStack), aber diese Demo wird unter Linux ausgeführt.

4. Geben Sie auf der Seite **Add target environment** (Zielumgebung hinzufügen) für **Host name** (Hostname) **localhost** ein. Behalten Sie für **SSH port** (SSH-Port) den Wert **22** bei.

5. Geben Sie in das Feld **Target Environment label** (Bezeichnung der Zielumgebung) eine Bezeichnung wie z.B. **MyCICS** ein.

     ![Bildschirm zum Hinzufügen der Zielumgebung](media/04-add-target.png)

## <a name="configure-adcd-and-deploy"></a>Konfigurieren von ADCD und Bereitstellen

Nach Abschluss der vorherigen Konfigurationsschritte müssen Sie zD&T zur Verwendung von Paketen und Zielumgebung konfigurieren. Erneut verwenden Sie den Imagespeicherprozess in zD&T, über den Sie die Images einbinden und verwenden können. Sie können SSH oder FTP verwenden.

1. Wählen Sie auf der Seite **Quick Start** (Schnellstart) unter **Configure** (Konfigurieren) die Option **ADCD** aus. Ein Satz von Anweisungen wird angezeigt, in denen die Schritte erläutert werden, die ausgeführt werden müssen, bevor ein ADCD-Paket eingebunden werden kann. Dies erklärt unsere frühere Benennung des Zielverzeichnisses.

2. Klicken Sie unter der Annahme, dass alle Bilder in die richtigen Verzeichnisse hochgeladen wurden, auf den Link **IMAGE from ADCD** (IMAGE aus ADCD), das in der unteren rechten Ecke angezeigt wird (im folgenden Screenshot in Schritt 7 dargestellt).

     ![IBM zD&T Enterprise Edition – Konfigurieren des ADCD-Bildschirms](media/05-adcd.png)

## <a name="create-the-image"></a>Erstellen des Images

Nach dem Abschluss des vorherigen Konfigurationsschritts wird die Seite **Create an image using ADCD Components** (Erstellen eines Images mithilfe von ADCD-Komponenten) angezeigt.

1. Wählen Sie das Volume aus („Nov 2017“ in diesem Fall), um die anderen Pakete anzuzeigen, die sich in diesem Volume befinden.

2. Wählen Sie für diese Demo **Customer Information Control System (CICS) – 5.3** aus.

3. Geben Sie in das Feld **Image name** einen Namen für das Image wie z.B. **MyCICS Image** ein.

4. Wählen Sie die Schaltfläche **Create Image** (Image erstellen) unten rechts aus.

     ![IBM zD&T Enterprise Edition – Erstellen eines Images mithilfe des ADCD Components-Bildschirms](media/06-adcd.png)

5. Wählen Sie im angezeigten Fenster, das Ihnen die erfolgreiche Bereitstellung des Images meldet, **Deploy images** (Images bereitstellen) aus.

6. Wählen Sie auf der Seite **Deploy an image to a target environment** (Bereitstellen eines Images in einer Zielumgebung) das Image, das Sie auf der vorherigen Seite erstellt haben (**MyCICS Image**), und die zuvor erstellte Zielumgebung (**MyCICS**) aus.

7. Geben Sie auf dem nächsten Bildschirm Ihre Anmeldeinformationen für den virtuellen Computer ein (d.h. nicht die ztadmin-Anmeldeinformationen).

8. Geben Sie im Bereich „Properties“ (Eigenschaften) die Anzahl der **Central processors (CPs)** (Hauptprozessoren (CPs)), die Menge an **System memory (GB)** (Systemspeicher (GB)) und das **Deployment directory** (Bereitstellungsverzeichnis) für die Ausführung des Images ein. Da dies eine Demo ist, halten Sie die Werte klein.

9. Stellen Sie sicher, dass das Kontrollkästchen für **Automatically issue IPL command to z/OS after deploy** (Nach der Bereitstellung automatisch IPL-Befehl an z/OS ausgeben) aktiviert ist.

     ![Bildschirm „Properties“ (Eigenschaften)](media/07-properties.png)

10. Wählen Sie **Complete** (Fertig stellen) aus.

11. Wählen Sie **Deploy Image** (Image bereitstellen) auf der Seite **Deploy an image to a target environment** (Image für eine Zielumgebung bereitstellen) aus.

Sie können Ihr Image jetzt bereitstellen, und es kann von einem 3270-Terminalemulator eingebunden werden.

> [!NOTE]
> Wenn Sie eine Fehlermeldung erhalten, dass Sie nicht über genügend Festplatten-Speicherplatz verfügen, beachten Sie, dass die Region 151GB benötigt.

Glückwunsch! Sie führen jetzt eine IBM-Mainframeumgebung in Azure aus.

## <a name="learn-more"></a>Weitere Informationen

- [Mainframemigration: Mythen und Fakten](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [IBM DB2 pureScale in Azure](../../../linux/ibm-db2-purescale-azure.md)
- [Problembehandlung](../../../troubleshooting/index.yml)
- [Demystifying mainframe to Azure migration](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/) (Beseitigen von Irrtümern in Bezug auf die Migration von Mainframesystemen zu Azure)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
