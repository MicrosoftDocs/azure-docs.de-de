---
title: Installieren von HANA unter SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie HANA unter SAP HANA in Azure (große Instanzen) installieren.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 994130ce2ecb4578a8d34f1f77b310bb558d3c7a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967906"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installieren von HANA unter SAP HANA in Azure (große Instanzen)

Um HANA unter SAP HANA in Azure (große Instanzen) zu installieren, müssen Sie zunächst folgende Aktionen ausführen:
- Sie liefern alle Daten an Microsoft, die auf einer großen SAP HANA-Instanz bereitgestellt werden sollen.
- Sie erhalten die große SAP HANA-Instanz von Microsoft.
- Sie erstellen ein virtuelles Azure-Netzwerk, das mit Ihrem lokalen Netzwerk verbunden ist.
- Sie stellen die ExpressRoute-Verbindung für HANA (große Instanzen) mit demselben virtuellen Azure-Netzwerk her.
- Sie installieren eine Azure-VM, die Sie als Jumpbox für HANA (große Instanzen) verwenden.
- Sie stellen sicher, dass Sie eine Verbindung von der Jumpbox mit der HANA-Einheit (große Instanzen) und umgekehrt herstellen können.
- Sie überprüfen, ob alle erforderlichen Pakete und Patches installiert sind.
- Sie lesen die SAP-Hinweise und die Dokumentation zur HANA-Installation für das von Ihnen verwendete Betriebssystem. Stellen Sie sicher, dass das ausgewählte HANA-Release für Ihre Betriebssystemversion unterstützt wird.

Im nächsten Abschnitt finden Sie ein Beispiel für das Herunterladen der HANA-Installationspakete auf die VM, die als Jumpbox fungiert. In diesem Fall ist das Betriebssystem Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Herunterladen der SAP HANA-Installationsdaten
Die HANA-Einheiten (große Instanzen) sind nicht direkt mit dem Internet verbunden. Sie können die Installationspakete nicht direkt von SAP auf die HANA-VM (große Instanzen) herunterladen. Stattdessen laden Sie die Pakete auf die Jumpbox-VM herunter.

Sie benötigen einen SAP S-Benutzer oder einen anderen Benutzer, mit dem Sie auf den SAP Marketplace zugreifen können.

1. Melden Sie sich an, und wechseln Sie zum [SAP Service Marketplace](https://support.sap.com/en/index.html). Wählen Sie **Download Software** > **Installations and Upgrade** > **By Alphabetical Index** aus (Software herunterladen -> Installationen und Upgrades -> Nach alphabetischem Index). Wählen Sie dann unter H **SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installation** aus. Laden Sie die im folgenden Screenshot gezeigten Dateien herunter.

   ![Screenshot der herunterzuladenden Dateien](./media/hana-installation/image16_download_hana.PNG)

2. In diesem Beispiel haben wir SAP HANA 2.0-Installationspakete heruntergeladen. Auf der Azure-Jumpbox-VM erweitern Sie die selbstextrahierenden Archive in das Verzeichnis, wie unten gezeigt.

   ![Screenshot eines selbstextrahierenden Archivs](./media/hana-installation/image17_extract_hana.PNG)

3. Wenn die Archive extrahiert wurden, kopieren Sie das durch die Extraktion erstellte Verzeichnis (in diesem Fall „51052030“). Kopieren Sie das Verzeichnis aus dem Volume „/hana/shared“ der HANA-Einheit (große Instanzen) in das erstellte Verzeichnis.

   > [!Important]
   > Kopieren Sie die Installationspakete nicht in die Stamm- oder Start-LUN, da der Speicherplatz begrenzt ist und auch von anderen Prozessen genutzt werden muss.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installieren von SAP HANA auf der Einheit von HANA (große Instanzen)
Für die Installation von SAP HANA müssen Sie sich als Stammbenutzer (root) anmelden. Nur der Stammbenutzer verfügt über ausreichende Berechtigungen zum Installieren von SAP HANA. Legen Sie Berechtigungen für das Verzeichnis fest, das Sie in „/hana/shared“ kopiert haben.

```
chmod –R 744 <Installation bits folder>
```

Wenn Sie SAP HANA über die grafische Benutzeroberfläche installieren möchten, muss das Paket „gtk2“ auf der HANA-Einheit (große Instanzen) installiert werden. Um zu überprüfen, ob das Paket installiert ist, führen Sie den folgenden Befehl aus:

```
rpm –qa | grep gtk2
```

(In späteren Schritten zeigen wir Ihnen das SAP HANA-Setup mit der grafischen Benutzeroberfläche.)

Wechseln Sie zum Installationsverzeichnis und dann in das Unterverzeichnis „HDB_LCM_LINUX_X86_64“. 

Starten Sie in diesem Verzeichnis folgende Elemente:

```
./hdblcmgui 
```
Nun wird eine Reihe von Bildschirmen angezeigt, in denen Sie Daten für die Installation angeben müssen. In diesem Beispiel installieren wir den SAP HANA-Datenbankserver und die SAP HANA-Clientkomponenten. Daher wählen wir **SAP HANA Database** aus.

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „SAP HANA Database“](./media/hana-installation/image18_hana_selection.PNG)

Wählen Sie auf dem nächsten Bildschirm die Option **Install New System** (Neues System installieren) aus.

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „Install New System“](./media/hana-installation/image19_select_new.PNG)

Wählen Sie als Nächstes zusätzliche Komponenten aus, die Sie installieren möchten.

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit einer Liste zusätzlicher Komponenten](./media/hana-installation/image20_select_components.PNG)

Hier wählen wir den SAP HANA-Client und SAP HANA Studio aus. Außerdem installieren wir eine Instanz zum zentralen Hochskalieren. Wählen Sie jetzt **Single-Host System** (Einzelhostsystem) aus. 

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit ausgewählter Option „Single-Host System“](./media/hana-installation/image21_single_host.PNG)

Geben Sie als nächstes einige Daten an.

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit zu definierenden Systemeigenschaftenfeldern](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Als HANA-SID (System-ID) müssen Sie die gleiche SID angeben, die Sie für Microsoft bereitgestellt haben, als Sie die HANA-Bereitstellung (große Instanzen) bestellt haben. Wenn Sie eine andere SID wählen, kann die Installation nicht erfolgreich durchgeführt werden, da sich für die verschiedenen Volumes Probleme mit der Zugriffsberechtigung ergeben.

Als Installationspfad verwenden Sie das Verzeichnis „/hana/shared“. Im nächsten Schritt geben Sie die Speicherorte für die HANA-Datendateien und die HANA-Protokolldateien an.


![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit Feldern für Daten und Protokollbereich](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> Die SID, die Sie während der Definition der Systemeigenschaften (zwei Bildschirme vorher) angegeben haben, sollte der SID der Mountpunkte entsprechen. Wenn diese Werte nicht übereinstimmen, gehen Sie zwei Bildschirme zurück, und legen Sie die SID auf denselben Wert wie für die Mountpunkte fest.

Überprüfen Sie im nächsten Schritt den Hostnamen, und korrigieren Sie ihn bei Bedarf. 

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit dem Hostnamen](./media/hana-installation/image24_review_host_name.PNG)

Im nächsten Schritt müssen Sie auch Daten abrufen, die Sie für Microsoft bereitgestellt haben, als Sie die Bereitstellung von HANA (große Instanzen) bestellt haben. 

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit zu definierenden Systemadministratorfeldern](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Geben Sie die gleiche **System Administrator User ID** (Benutzer-ID für den Systemadministrator) und **ID of User Group** (Benutzergruppen-ID) an, die Sie beim Bestellen der Einheitenbereitstellung bei Microsoft angegeben haben. Andernfalls kann die Installation von SAP HANA auf der HANA-Einheit (große Instanzen) nicht erfolgreich durchgeführt werden.

Die beiden nächsten Bildschirme werden hier nicht gezeigt. Dort können Sie das Kennwort für den SYSTEM-Benutzer der SAP HANA-Datenbank und das Kennwort für den sapadm-Benutzer angeben. Letzteres wird für den SAP-Host-Agent verwendet, der als Teil der SAP HANA-Datenbankinstanz installiert wird.

Nach dem Definieren des Kennworts wird ein Bestätigungsfenster angezeigt. Überprüfen Sie alle aufgeführten Daten, und fahren Sie mit der Installation fort. Sie gelangen zu einem Statusfenster, auf dem der Installationsfortschritt angezeigt wird:

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“ mit Anzeige des Installationsfortschritts](./media/hana-installation/image27_show_progress.PNG)

Wenn die Installation abgeschlossen ist, sollte ein Bildschirm wie dieser angezeigt werden:

![Screenshot des Bildschirms „SAP HANA Lifecycle Management“, der auf den Abschluss der Installation hinweist](./media/hana-installation/image28_install_finished.PNG)

Die SAP HANA-Instanz sollte jetzt ausgeführt werden und betriebsbereit sein. Sie sollten von SAP HANA Studio aus eine Verbindung damit herstellen können. Stellen Sie auch sicher, dass Sie nach den neuesten Updates suchen und diese anwenden.


## <a name="next-steps"></a>Nächste Schritte

- [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md)

