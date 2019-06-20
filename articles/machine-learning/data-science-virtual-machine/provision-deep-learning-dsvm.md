---
title: Erstellen einer Deep Learning Data Science Virtual Machine
titleSuffix: Azure
description: Konfigurieren und erstellen Sie für Analysen und Machine Learning eine Deep Learning Data Science Virtual Machine in Azure.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 318df03c7c4447d051dfa396098462c0f8bbf423
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65410439"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Bereitstellen einer Data Science Virtual Machine in Azure 

Die Deep Learning Virtual Machine (DLVM) ist eine speziell konfigurierte Variante der [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM), die ein einfacheres und schnelleres Training von Deep Learning-Modellen anhand GPU-basierter VM-Instanzen ermöglicht. Sie wird von Windows 2016 oder der Ubuntu DSVM als Basis unterstützt. Die DLVM nutzt dieselben VM-Kernimages und daher das gesamte umfangreiche Toolset, das von der DSVM geboten wird. 

DLVM bietet mehrere Tools für KI, einschließlich GPU-Editionen beliebter Deep Learning-Frameworks wie Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer. Ferner bietet sie Tools zum Abrufen und Vorverarbeiten von Bild- und Textdaten, Tools für Data Science-Modellierungs- und Entwicklungsaktivitäten wie Microsoft R Server Developer Edition, Anaconda Python, Jupyter-Notebooks für Python und R, IDEs für Python und R, SQL-Datenbanken und zahlreiche weitere Data Science- und ML-Tools. 

## <a name="create-your-deep-learning-virtual-machine"></a>Erstellen Ihrer Deep Learning Virtual Machine
Es folgen die Schritte zum Erstellen einer Instanz der Deep Learning Virtual Machine: 

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
)zur Auflistung der virtuellen Computer.
2. Klicken Sie unten auf der Seite auf die Schaltfläche **Create**, um einen Assistenten aufzurufen.![configure-dlvm](./media/dlvm-provision-wizard.PNG)
3. Der Assistent zum Erstellen der DLVM erfordert **Eingaben** für jeden der **vier Schritte**, die in der Abbildung rechts aufgelistet sind. Die zum Konfigurieren der einzelnen Schritte erforderlichen Eingaben sind:

   <a name="basics"></a>   
   1. **Grundlagen**
      
      1. **Name**: Der Name des Data Science-Servers, den Sie erstellen.
      2. **Auswählen des Betriebssystemtyps für die Deep Learning-VM**: Wählen Sie Windows- oder Linux (für Windows 2016 und Ubuntu Linux-Basis-DSVM.)
      2. **Benutzername**: Die ID des Administratoranmeldekontos.
      3. **Kennwort**: Das Kennwort des Administratorkontos.
      4. **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, wählen Sie eines aus, über das der Computer erstellt und abgerechnet wird.
      5. **Ressourcengruppe**: Sie können eine neue Ressourcengruppe erstellen oder eine **leere** in Ihrem Abonnement vorhandene Azure-Ressourcengruppe verwenden.
      6. **Standort**: Wählen Sie das Datencenter aus, das am besten geeignet ist. Normalerweise ist es das Rechenzentrum, in dem der größte Teil Ihrer Daten gespeichert ist oder das Ihrem Standort am nächsten ist, um den schnellsten Netzwerkzugriff zu erreichen. 
      
      > [!NOTE]
      > Die DLVM unterstützt alle GPU-VM-Instanzen der Serie NC und ND. Bei der Bereitstellung der DLVM müssen Sie einen der in Azure verfügbaren Standorte mit GPUs auswählen. Überprüfen Sie auf der Seite [Azure Produkte nach Region](https://azure.microsoft.com/regions/services/) die verfügbaren Standorte, und suchen Sie unter **Compute** nach **NC-Serie**, **NCv2-Serie**, **NCv3-Serie** oder **ND-Serie**. 

   1. **Einstellungen**: Wählen Sie eine GPU-VM-Größe der NC-Serie (NC, NCv2, NCv3) oder der ND-Serie aus, der die funktionalen Anforderungen erfüllt und dem Kostenrahmen entspricht. Erstellen Sie ein Speicherkonto für Ihre VM.  ![dlvm-settings](./media/dlvm-provision-step-2.PNG)
   
   1. **Zusammenfassung**: Stellen Sie sicher, dass alle eingegebenen Informationen richtig sind.

   1. **Kaufen**: Klicken Sie auf **Kaufen**, um die Bereitstellung zu starten. Ein Link zu den Bedingungen der Transaktion wird bereitgestellt. Für die VM gelten keine über die Computekosten für die Servergröße, die Sie im Schritt **Größe** ausgewählt haben, hinausgehenden Kosten. 

> [!NOTE]
> Die Bereitstellung sollte ungefähr 10 bis 20 Minuten dauern. Der Status der Bereitstellung wird im Azure-Portal angezeigt.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Zugreifen auf die Deep Learning Virtual Machine

### <a name="windows-edition"></a>Windows-Edition
Nach der VM-Erstellung können Sie sich mithilfe von Remotedesktop mit den Anmeldeinformationen des Administratorkontos anmelden, die Sie zuvor im Abschnitt **Grundlagen** erstellt haben. 

### <a name="linux-edition"></a>Linux-Edition

Nachdem die VM erstellt wurde, können Sie sich mithilfe von SSH an der VM anmelden. Verwenden Sie dazu die Kontoanmeldeinformationen, die Sie in Schritt 3 im Abschnitt [**Grundlagen**](#basics) für die Textshellschnittstelle erstellt haben. Weitere Informationen zu SSH-Verbindungen mit virtuellen Azure-Computern finden Sie unter [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](/azure/virtual-machines/linux/use-remote-desktop). Auf einem Windows-Client können Sie ein SSH-Clienttool wie [Putty](https://www.putty.org) herunterladen. Falls Sie einen grafischen Desktop bevorzugen (X Windows System), können Sie die X11-Weiterleitung von PuTTY verwenden oder den X2Go-Client installieren. 

> [!NOTE]
> Der X2Go-Client hat bei unseren Tests eine bessere Leistung als X11 und höher erzielt. Es wird empfohlen, den X2Go-Client als grafische Desktop-Benutzeroberfläche zu nutzen.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installieren und Konfigurieren des X2Go-Clients
Die Linux DLVM wird bereits mit X2Go-Server bereitgestellt und ist zum Akzeptieren von Clientverbindungen bereit. Führen Sie auf dem Client die folgenden Schritte aus, um eine Verbindung mit dem grafischen Desktop des virtuellen Linux-Computers herzustellen:

1. Laden Sie den X2Go-Client für Ihre Clientplattform von [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient)herunter, und installieren Sie ihn.    
2. Führen Sie den X2Go-Client aus, und wählen Sie die Option **Neue Sitzung**aus. Es wird ein Konfigurationsfenster mit mehreren Registerkarten geöffnet. Geben Sie die folgenden Konfigurationsparameter ein:
   * **Registerkarte „Sitzung“:**
     * **Host**: Der Hostname oder die IP-Adresse Ihrer Linux Data Science VM.
     * **Anmeldung**: Der Benutzername für die Linux-VM.
     * **SSH-Port**: Übernehmen Sie den Standardwert 22.
     * **Sitzungstyp**: Ändern Sie den Wert in **XFCE**. Derzeit unterstützt die Linux DSVM nur den XFCE-Desktop.
   * **Registerkarte „Medien“** : Sie können die Soundunterstützung und die Clientausgabe deaktivieren, wenn Sie diese Funktionen nicht benötigen.
   * **Freigegebene Ordner**: Wenn Verzeichnisse von Ihren Clientcomputern auf der Linux-VM bereitgestellt werden sollen, fügen Sie auf dieser Registerkarte die Clientcomputerverzeichnisse hinzu, die Sie für die VM freigeben möchten.

Nachdem Sie sich bei der VM angemeldet haben, indem Sie entweder den SSH-Client oder den grafischen XFCE-Desktop über den X2Go-Client nutzen, können Sie die Tools verwenden, die auf der VM installiert und konfiguriert sind. Auf dem XFCE-Desktop können Sie Anwendungsmenü-Tastenkombinationen und Desktopsymbole für viele Tools anzeigen.

Sobald Ihre VM erstellt und bereitgestellt wurde, können Sie beginnen, die installierten und konfigurierten Tools zu verwenden. Für viele der Tools gibt es Desktopsymbole und Kacheln im Startmenü. 
