---
title: Verwenden des Azure-Plug-Ins für Sekundärgeräte mit Hudson Continuous Integration | Microsoft Docs
description: Beschreibt das Verwenden des Azure-Plug-Ins für Sekundärgeräte mit Hudson Continuous Integration.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444223"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Verwenden des Azure-Plug-Ins für Sekundärgeräte mit Hudson Continuous Integration
Das Azure-Plug-In für Sekundärgeräte für Hudson ermöglicht Ihnen das Bereitstellen untergeordneter Knoten in Azure beim Ausführen von verteilten Builds.

## <a name="install-the-azure-slave-plug-in"></a>Installieren des Azure Slave-Plug-Ins
1. Klicken Sie im Hudson-Dashboard auf **Manage Hudson**.
2. Klicken Sie unter **Manage Hudson** auf **Manage Plugins**.
3. Klicken Sie auf die Registerkarte **Available**.
4. Klicken Sie auf **Search**, und geben Sie **Azure** ein, um relevante Plug-Ins aufzulisten.
   
    Wenn Sie einen Bildlauf durch die Liste der verfügbaren Plug-Ins durchführen möchten, finden Sie das Azure Slave-Plug-In im Abschnitt **Cluster Management and Distributed Build** auf der Registerkarte **Others**.
5. Aktivieren Sie das Kontrollkästchen **Azure Slave Plugin**.
6. Klicken Sie auf **Installieren**.
7. Starten Sie Hudson neu.

Da das Plug-In jetzt installiert ist, wären die nächsten Schritte das Konfigurieren des Plug-Ins mit Ihrem Azure-Abonnementprofil und das Erstellen einer Vorlage, die verwendet wird, um den virtuellen Computer für den untergeordneten Knoten zu erstellen.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurieren des Azure Slave-Plug-Ins mit Ihrem Abonnementprofil
Ein Abonnementprofil, das als Veröffentlichungseinstellungen bezeichnet wird, ist eine XML-Datei, die sichere Anmeldeinformationen und einige zusätzliche Informationen enthält, die Sie für die Arbeit mit Azure in Ihrer Entwicklungsumgebung benötigen. Um das Azure Slave-Plug-In zu konfigurieren, benötigen Sie Folgendes:

* Ihre Abonnement-ID
* Ein Verwaltungszertifikat für Ihr Abonnement

Sie finden dies in Ihrem [Abonnementprofil]. Es folgt ein Beispiel für ein Abonnementprofil.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Sobald Sie Ihr Abonnementprofil haben, gehen Sie folgendermaßen vor, um das Azure Slave-Plug-In zu konfigurieren.

1. Klicken Sie im Hudson-Dashboard auf **Manage Hudson**.
2. Klicken Sie auf **Configure System**.
3. Führen Sie einen Bildlauf nach unten bis zum Abschnitt **Cloud** durch.
4. Klicken Sie auf **Add new cloud > Microsoft Azure**.
   
    ![neue Cloud hinzufügen][add new cloud]
   
    Dadurch werden die Felder angezeigt, in denen Sie Ihre Abonnementdetails eingeben müssen.
   
    ![Profil konfigurieren][configure profile]
5. Kopieren Sie die Abonnement-ID und das Verwaltungszertifikat aus Ihrem Abonnementprofil, und fügen Sie diese in die entsprechenden Felder ein.
   
    Beim Kopieren der Abonnement-ID und des Verwaltungszertifikats schließen Sie **nicht** die Anführungszeichen ein, die die Werte einschließen.
6. Klicken Sie auf **Verify Configuration**.
7. Wenn die Konfiguration erfolgreich überprüft wurde, klicken Sie auf **Speichern**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Einrichten einer Vorlage für virtuelle Computer für das Azure Slave-Plug-In
Eine Vorlage für virtuelle Computer definiert die Parameter, die das Plug-In zum Erstellen eines untergeordneten Knotens in Azure verwendet. In den folgenden Schritten erstellen Sie eine Vorlage für einen virtuellen Ubuntu-Computer.

1. Klicken Sie im Hudson-Dashboard auf **Manage Hudson**.
2. Klicken Sie auf **Configure System**.
3. Führen Sie einen Bildlauf nach unten bis zum Abschnitt **Cloud** durch.
4. Suchen Sie im Abschnitt **Cloud** nach **Add Azure Virtual Machine Template**, und klicken Sie auf die Schaltfläche **Hinzufügen**.
   
    ![VM-Vorlage hinzufügen][add vm template]
5. Geben Sie einen Clouddienstnamen im Feld **Name** ein. Bezieht sich der von Ihnen angegebene Name auf einen vorhandenen Clouddienst, wird der virtuelle Computer in diesem Dienst bereitgestellt. Andernfalls erstellt Azure einen neuen.
6. Geben Sie in das Feld **Beschreibung** Text ein, der die Vorlage beschreibt, die Sie erstellen. Diese Informationen dienen nur der Dokumentation und werden nicht bei einer VM-Bereitstellung verwendet.
7. Geben Sie im Feld **Bezeichnungen** **linux** ein. Diese Bezeichnung wird verwendet, um die Vorlage zu identifizieren, die Sie erstellen, und wird anschließend als Verweise auf die Vorlage beim Erstellen eines Hudson-Auftrags verwendet.
8. Wählen Sie eine Region aus, in der der virtuelle Computer erstellt wird.
9. Wählen Sie die passende Größe des virtuellen Computers aus.
10. Geben Sie ein Speicherkonto an, in dem der virtuelle Computer erstellt wird. Stellen Sie sicher, dass es sich in der gleichen Region wie der Clouddienst befindet, den Sie verwenden möchten. Wenn Sie neuen Speicher erstellen möchten, können Sie dieses Feld leer lassen.
11. Die Aufbewahrungszeit gibt die Anzahl der Minuten an, bevor Hudson einen Slave im Leerlauf löscht. Behalten Sie hier den Standardwert 60 bei.
12. Wählen Sie unter **Usage**die entsprechende Bedingung für die Verwendung dieses untergeordneten Knotens aus. Wählen Sie vorerst **Utilize this node as much as possible**aus.
    
     Zu diesem Zeitpunkt sieht das Formular in etwa so aus:
    
     ![Vorlagenkonfiguration][template config]
13. Unter **Image Family or Id** müssen Sie angeben, welches Systemabbild auf Ihrem virtuellen Computer installiert wird. Sie können aus einer Liste von Imagefamilien auswählen oder ein benutzerdefiniertes Image angeben.
    
     Wenn Sie aus einer Liste von Imagefamilien auswählen möchten, geben Sie das erste Zeichen (Groß-/Kleinschreibung beachten) des Namens der Imagefamilie ein. Wenn Sie z.B. **U** eingeben, erhalten Sie eine Liste der Ubuntu Server-Familien. Wenn Sie aus der Liste auswählen, verwendet Jenkins die neueste Version des Systemimages aus dieser Familie bei die VM-Bereitstellung.
    
     ![BS-Familienliste][OS family list]
    
     Wenn Sie stattdessen ein vorhandenes benutzerdefiniertes Image verwenden möchten, geben Sie den Namen dieses benutzerdefinierten Images ein. Benutzerdefiniertes Imagenamen werden nicht in einer Liste angezeigt, daher müssen Sie sicherstellen, dass der Name richtig eingegeben wurde.    
    
     Geben Sie für dieses Tutorial **U** ein, um eine Liste mit Ubuntu-Images anzuzeigen, und wählen Sie **Ubuntu Server 14.04 LTS** aus.
14. Wählen Sie für **Launch method** die Option **SSH** aus.
15. Kopieren Sie das folgende Skript, und fügen Sie es in das Feld **Init script** ein.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     Das **Initialisierungsskript** wird ausgeführt, nachdem der virtuelle Computer erstellt wurde. In diesem Beispiel installiert das Skript Java, git und ant.
16. Geben Sie in die Felder **Benutzername** und **Kennwort** Ihre bevorzugten Werte für das Administratorkonto ein, das auf Ihrem virtuellen Computer erstellt werden soll.
17. Klicken Sie auf **Verify Template** , um zu überprüfen, ob die angegebenen Parameter gültig sind.
18. Klicken Sie auf **Speichern**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Erstellen eines Hudson-Auftrags, der auf einem untergeordneten Knoten in Azure ausgeführt wird
In diesem Abschnitt erstellen Sie eine Hudson-Aufgabe, die auf einen untergeordneten Knoten in Azure ausgeführt wird.

1. Klicken Sie im Hudson-Dashboard auf **New Job**.
2. Geben Sie einen Namen für den Auftrag ein, den Sie erstellen.
3. Wählen Sie für den Auftragstyp **Build a free-style software job**aus.
4. Klicken Sie auf **OK**.
5. Wählen Sie auf der Konfigurationsseite des Auftrags **Restrict where this project can be run**aus.
6. Wählen Sie **Node and label menu** und dann **linux** aus (Sie haben diese Bezeichnung beim Erstellen der virtuellen Computervorlage im vorherigen Abschnitt angegeben).
7. Klicken Sie im Abschnitt **Build** auf **Add build step**, und wählen Sie **Execute shell** aus.
8. Bearbeiten Sie das folgende Skript, ersetzen Sie **{Ihr GitHub-Kontoname}** , **{Name Ihres Projekts}** und **{Ihr Projektverzeichnis}** durch entsprechende Werte, und fügen Sie das bearbeitete Skript im angezeigten Textbereich ein.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Klicken Sie auf **Speichern**.
10. Suchen Sie im Hudson-Dashboard den soeben erstellten Auftrag, und klicken Sie auf das **Schedule a build** -Symbol.

Hudson erstellt dann einen untergeordneten Knoten mithilfe der im vorherigen Abschnitt erstellten Vorlage und führt das Skript aus, das Sie im Buildschritt für diese Aufgabe angegeben haben.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Verwenden von Azure mit Java finden Sie im [Azure Developer Center für Java].

<!-- URL List -->

[Azure Developer Center für Java]: https://azure.microsoft.com/develop/java/
[Abonnementprofil]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

