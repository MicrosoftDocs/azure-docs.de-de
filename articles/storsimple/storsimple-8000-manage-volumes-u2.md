---
title: Verwalten von StorSimple-Volumes (Update 3) | Microsoft-Dokumentation
description: Erläutert, wie Sie StorSimple-Volumes hinzufügen, ändern, überwachen und löschen sowie ggf. offline schalten können.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: alkohli
ms.openlocfilehash: f7bfe41b4cdc9989c6b949011bc240275886b6f0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60634821"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-volumes-update-3-or-later"></a>Verwalten von Volumes mithilfe des StorSimple-Geräte-Manager-Diensts (Update 3 und höher)

## <a name="overview"></a>Übersicht

In diesem Tutorial wird erläutert, wie Sie den StorSimple-Geräte-Manager-Dienst zum Erstellen und Verwalten von Volumes auf Geräten der StorSimple 8000-Serie verwenden können, auf denen Update3 und höher ausgeführt wird.

Der StorSimple-Geräte-Manager-Dienst ist eine Erweiterung im Azure-Portal, mit der Sie Ihre StorSimple-Lösung über eine einzelne Weboberfläche verwalten können. Im Azure-Portal können Sie Volumes auf allen Ihren Geräten verwalten. Sie können StorSimple-Dienste erstellen und verwalten, Geräte, Sicherungsrichtlinien und den Sicherungskatalog verwalten und Warnungen anzeigen.

## <a name="volume-types"></a>Volumetypen

Folgende StorSimple-Volumes sind möglich:

* **Lokal angeheftete Volumes**: Die Daten in diesen Volumes verbleiben jederzeit auf dem lokalen StorSimple-Gerät.
* **Mehrstufige Volumes**: Die Daten in diesen Volumes können in die Cloud ausweichen.

Ein Archivierungsvolume ist ein mehrstufiger Volumetyp. Die für Archivierungsvolumes verwendeten größeren Deduplizierungsblöcke ermöglichen dem Gerät die Übertragung größerer Datensegmente in die Cloud.

Nach Bedarf können Sie den Volumetyp von lokal in mehrstufig ändern oder umgekehrt. Weitere Informationen finden Sie unter [Ändern des Volumetyps](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Lokale Volumes

Lokale Volumes werden vollständig bereitgestellt und lagern keine Daten in die Cloud aus. So gewährleisten sie lokale Garantien für primäre Daten, unabhängig von der Cloudkonnektivität. Daten auf lokalen Volumes werden nicht dedupliziert und komprimiert. Allerdings werden Momentaufnahmen von lokal angehefteten Volumes dedupliziert. 

Lokale Volumes werden vollständig bereitgestellt. Aus diesem Grund müssen Sie bei deren Erstellung über genügend Speicherplatz auf dem Gerät verfügen. Sie können lokale Volumes mit einer Größe von maximal 8 TB auf dem StorSimple 8100-Gerät und 20 TB auf dem 8600-Gerät bereitstellen. StorSimple reserviert den verbleibenden lokalen Speicherplatz auf dem Gerät für Momentaufnahmen, Metadaten und die Datenverarbeitung. Sie können die Größe eines lokalen Volumes auf den maximal verfügbaren Speicherplatz erhöhen, jedoch können Sie die Größe eines Volumes nach der Erstellung nicht mehr verringern.

Wenn Sie ein lokales Volume erstellen, verringert sich dadurch der verfügbare Speicherplatz für die Erstellung mehrstufiger Volumes. Das Gegenteil trifft ebenfalls zu: Wenn Sie mehrstufige Volumes verwenden, ist der Speicherplatz für die Erstellung lokaler Volumes geringer als die oben genannten Obergrenzen. Weitere Informationen zu lokalen Volumes finden Sie in den [häufig gestellten Fragen zu lokalen Volumes](storsimple-8000-local-volume-faq.md).

### <a name="tiered-volumes"></a>Mehrstufige Volumes

Mehrstufige Volumes sind Volumes mit schlanker Speicherzuweisung, in denen häufig verwendete Daten lokal auf dem Gerät bleiben und weniger häufig verwendete Daten automatisch in die Cloud ausgelagert werden. Die Bereitstellung nach Bedarf ist eine Virtualisierungstechnologie, bei der der verfügbare Speicher scheinbar die physischen Ressourcen überschreitet. Anstatt ausreichend Speicher im Voraus zu reservieren, verwendet StorSimple die schlanke Bereitstellung, um nur eben genug Speicher zum Erfüllen der aktuellen Anforderungen zuzuweisen. Die Elastizität von Cloudspeicher ermöglicht diesen Ansatz, weil StorSimple den Cloudspeicher vergrößern oder verkleinern kann, um sich ändernde Anforderungen zu erfüllen.

Bei Verwendung des mehrstufigen Volumes für Archivdaten wird durch Aktivieren des Kontrollkästchens **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** die Deduplizierungsblockgröße für das Volume in 512 KB geändert. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.


### <a name="provisioned-capacity"></a>Bereitgestellte Kapazität

In der folgenden Tabelle finden Sie die bereitgestellte Maximalkapazität für die einzelnen Geräte und Volumetypen. (Beachten Sie, dass lokale Volumes nicht auf einem virtuellen Gerät verfügbar sind.)

|  | Maximale Größe mehrstufiger Volumes | Maximale Größe lokaler Volumes |
| --- | --- | --- |
| **Physische Geräte** | | |
| 8100 |64 TB |8 TB |
| 8600 |64 TB |20 TB |
| **Virtuelle Geräte** | | |
| 8010 |30 TB |– |
| 8020 |64 TB |– |

## <a name="the-volumes-blade"></a>Das Blatt „Volumes“

Auf dem Blatt **Volumes** können Sie die Speichervolumes verwalten, die auf dem Microsoft Azure StorSimple-Gerät für die Initiatoren (Server) bereitgestellt werden. Es zeigt die Liste der Volumes auf den StorSimple-Geräten, die mit Ihrem Dienst verbunden sind.

 ![Seite „Volumes“](./media/storsimple-8000-manage-volumes-u2/volumeslist.png)

Ein Volume umfasst eine Reihe von Attributen:

* **Volumename** : Ein beschreibender Name, der eindeutig sein muss und das Volume identifiziert. Dieser Name wird auch in Überwachungsberichten zum Filtern nach einem bestimmten Volume verwendet. Nachdem das Volume erstellt wurde, kann es nicht umbenannt werden.
* **Status** – online oder offline. Offline-Volumes sind für die Initiatoren (Server), die über Zugriff für die Verwendung des Volumes verfügen, nicht sichtbar.
* **Kapazität** : Gibt die Gesamtmenge der Daten an, die vom Initiator (Server) gespeichert werden kann. Lokale Volumes werden vollständig bereitgestellt und befinden sich auf dem StorSimple-Gerät. Mehrstufige Volumes werden mit schlanker Speicherzuweisung bereitgestellt, und die Daten werden dedupliziert. Bei Volumes mit schlanker Speicherzuweisung weist Ihr Gerät die physische Speicherkapazität weder intern noch in der Cloud anhand der konfigurierten Volumekapazität vorab zu. Die Volumekapazität wird nach Bedarf zugewiesen und genutzt.
* **Typ**: Gibt an, ob das Volume **mehrstufig** (Standard) oder **lokal** ist.

Verwenden Sie die Anweisungen in diesem Tutorial, um die folgenden Aufgaben auszuführen:

* Hinzufügen eines Volumes 
* Ändern von Volumes 
* Ändern des Volumetyps
* Löschen von Volumes 
* Offlineschalten von Volumes 
* Volume überwachen 

## <a name="add-a-volume"></a>Hinzufügen eines Volumes

Sie haben beim Bereitstellen Ihres Geräts der StorSimple 8000-Serie [ein Volume erstellt](storsimple-8000-deployment-walkthrough-u2.md#step-6-create-a-volume). Das Hinzufügen eines Volumes ist ein ähnliches Verfahren.

#### <a name="to-add-a-volume"></a>So fügen Sie ein Volume hinzu

1. Wählen Sie auf dem Blatt **Geräte** in der tabellarischen Geräteauflistung Ihr Gerät aus. Klicken Sie auf **+ Volume hinzufügen**.

    ![Hinzufügen eines neuen Volumes](./media/storsimple-8000-manage-volumes-u2/step5createvol1.png)

2. Auf dem Blatt **Volume hinzufügen**:
   
    1. Das Feld **Gerät auswählen** wird automatisch mit den Informationen zum aktuellen Gerät ausgefüllt.

    2. Wählen Sie in der Dropdownliste den Volumecontainer aus, dem Sie ein Volume hinzufügen müssen.

    3.  den **Namen** für das Volume ein. Sie können ein Volume nicht mehr umbenennen, nachdem es erstellt wurde.

    4. Wählen Sie in der Dropdownliste den **Typ** für das Volume aus. Für Workloads, die lokale Garantien, niedrige Latenzzeiten und höhere Leistung benötigen, wählen Sie ein **lokales** Volume aus. Wählen Sie für alle anderen Daten ein **mehrstufiges** Volume aus. Wenn Sie dieses Volume für archivierte Daten verwenden, aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**.
      
       Ein mehrstufiges Volume wird mit schlanker Speicherzuweisung bereitgestellt und kann schnell erstellt werden. Bei Verwendung des mehrstufigen Volumes für archivierte Daten wird durch Auswahl der Option **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen** die Deduplizierungblockgröße für das Volume in 512 KB geändert. Wenn dieses Feld nicht aktiviert ist, verwendet das entsprechende mehrstufige Volume eine Blockgröße von 64 KB. Bei Verwendung größerer Deduplizierungsblöcke kann das Gerät die Übertragung umfangreicher Archivdaten in die Cloud beschleunigen.
       
       Ein lokales Volume wird mit vollständiger Speicherzuweisung (Thick Provisioning) bereitgestellt und stellt sicher, dass die primären Daten auf dem Volume lokal auf dem Gerät verbleiben und nicht in die Cloud übergehen.  Wenn Sie ein lokales Volume erstellen, überprüft das Gerät den verfügbaren Speicherplatz auf den lokalen Ebenen, um das Volume in der angeforderten Größe bereitzustellen. Bei der Erstellung eines lokalen Volumes werden vorhandene Daten eventuell vom Gerät in die Cloud übertragen, und das Erstellen des Volumes kann längere Zeit in Anspruch nehmen. Die gesamte Zeit hängt von der Größe des bereitgestellten Volumes, von der verfügbaren Netzwerkbandbreite und von den Daten auf Ihrem Gerät ab.

    5. Geben Sie die **Bereitgestellte Kapazität** für das Volume an. Notieren Sie die Kapazität, die basierend auf dem ausgewählten Volumetyp verfügbar ist. Die angegebene Volumegröße darf den verfügbaren Speicherplatz nicht überschreiten.
      
       Sie können lokale Volumes von bis zu 8,5 TB oder mehrstufige Volumes von bis zu 200 TB auf dem 8100-Gerät bereitstellen. Auf dem größeren 8600-Gerät können Sie lokale Volumes von bis zu 22,5 TB oder mehrstufige Volumes von bis zu 500 TB bereitstellen. Da für das Hosten des Arbeitssatzes mehrstufiger Volumes lokaler Speicher auf dem Gerät erforderlich ist, wirkt sich die Erstellung lokaler Volumes auf den Speicherplatz aus, der für die Bereitstellung mehrstufiger Volumes zur Verfügung steht. Wenn Sie daher ein lokales Volume erstellen, verringert sich dadurch der verfügbare Speicherplatz für die Erstellung mehrstufiger Volumes. Wenn Sie ein mehrstufiges Volume erstellen, reduziert sich in gleicher Weise der verfügbare Speicherplatz für die Erstellung lokaler Volumes.
      
       Wenn Sie ein lokales Volume von 8,5 TB (maximal zulässige Größe) auf dem 8100-Gerät bereitstellen, haben Sie damit den gesamten lokalen Speicherplatz ausgeschöpft, der auf dem Gerät verfügbar ist. Ab diesem Zeitpunkt können Sie keine mehrstufigen Volumes mehr erstellen, da auf dem Gerät kein lokaler Speicherplatz zum Hosten des Arbeitssatzes mehrstufiger Volumes mehr vorhanden ist. Vorhandene mehrstufige Volumes wirken sich ebenfalls auf den verfügbaren Speicherplatz aus. Wenn Sie beispielsweise ein 8100-Gerät verwenden, auf dem bereits mehrstufige Volumes von ca. 106 TB vorhanden sind, stehen nur noch 4 TB Speicherplatz für lokale Volumes zur Verfügung.

    6. Klicken Sie im Feld **Verbundene Hosts** auf den Pfeil. Wählen Sie auf dem Blatt **Verbundene Hosts** einen vorhandenen ACR aus oder fügen Sie einen neuen ACR hinzu. Wenn Sie einen neuen ACR auswählen, geben Sie einen **Namen** für Ihren Access Control-Datensatz ein. Geben Sie außerdem den **qualifizierten iSCSI-Namen** (IQN) Ihres Windows-Hosts ein. Wenn Sie den IQN nicht kennen, fahren Sie mit „Abrufen des IQNs eines Windows Server-Hosts“ fort. Klicken Sie auf **Create**. Ein Volume wird mit den angegebenen Einstellungen erstellt.

        ![Klicken Sie auf „Erstellen“.](./media/storsimple-8000-manage-volumes-u2/step5createvol3.png)

Das neue Volume kann nun verwendet werden.

> [!NOTE]
> Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume erstellen, werden die Aufträge zur Volumeerstellung nacheinander ausgeführt. Der erste Auftrag zur Volumeerstellung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumeerstellung beginnt.

## <a name="modify-a-volume"></a>Ändern von Volumes

Zum Ändern eines Volumes müssen Sie dieses erweitern oder die Hosts ändern, die auf das Volume zugreifen.

> [!IMPORTANT]
> * Wenn Sie die Größe des Volumes auf dem Gerät ändern, muss auch die Größe des Volumes auf dem Host geändert werden.
> * Die hier beschriebenen hostseitigen Schritte gelten für Windows Server 2012 (2012R2). Vorgehensweisen für Linux oder andere Hostbetriebssysteme können davon abweichen. Beachten Sie beim Ändern des Volumes auf einem Host mit einem anderen Betriebssystem die Anweisungen zu Ihrem Hostbetriebssystem.

#### <a name="to-modify-a-volume"></a>So ändern Sie ein Volume

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie in der Tabelle mit den Geräten das Gerät mit dem Volume aus, das Sie ändern möchten. Klicken Sie auf **Einstellungen > Volumes**.

    ![Wechseln Sie zum Blatt „Volumes“](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

2. Wählen Sie in der Tabelle mit den Volumes das Volume aus, und klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen. Wählen Sie **Offline schalten** aus, um das Volume offline zu schalten, das Sie ändern möchten.

    ![Auswählen und Offlineschalten des Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Überprüfen Sie auf dem Blatt **Offline schalten** die Auswirkungen des Offlineschalten des Volumes, und aktivieren Sie das entsprechende Kontrollkästchen. Stellen Sie sicher, dass das entsprechende Volume auf dem Host zuerst offline ist. Informationen zum Offlineschalten eines Volumes auf Ihrem mit StorSimple verbundenen Hostserver finden Sie in den betriebssystemspezifischen Anweisungen. Klicken Sie auf **Offline schalten**.

    ![Überprüfen der Auswirkungen des Offlineschaltens eines Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

4. Sobald das Volume offline ist (siehe den Status des Volumes), klicken mit der rechten Maustaste auf das Volume, um das Kontextmenü aufzurufen. Wählen Sie **Volume ändern** aus.

    ![Wählen Sie „Volume ändern“ aus](./media/storsimple-8000-manage-volumes-u2/modifyvol9.png)


5. Auf dem Blatt **Volume ändern** können Sie die folgenden Änderungen vornehmen:
   
   1. Der **Name** des Volumes kann nicht geändert werden.
   2. Konvertieren Sie den **Typ** von lokal in mehrstufig bzw. von mehrstufig in lokal (weitere Informationen finden Sie unter [Ändern des Volumetyps](#change-the-volume-type)).
   3. Erhöhen Sie die **Bereitgestellte Kapazität**. Die **Bereitgestellte Kapazität** kann nur erhöht werden. Ein Volume kann nach seiner Erstellung nicht mehr verkleinert werden.
   4. Unter **Verbundene Hosts** können Sie den Access Control-Datensatz ändern. Um einen Access Control-Datensatz ändern zu können, muss das Volume offline sein.

       ![Überprüfen der Auswirkungen des Offlineschaltens eines Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

5. Klicken Sie zum Speichern der Änderungen auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Azure-Portal zeigt eine Meldung zur Aktualisierung des Volumes an. Eine Erfolgsmeldung wird angezeigt, wenn das Volume aktualisiert wurde.

    ![Überprüfen der Auswirkungen des Offlineschaltens eines Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)

7. Wenn Sie ein Volume erweitern, führen Sie folgende Schritte auf dem Windows-Hostcomputer aus:
   
   1. Wechseln Sie zu **Computerverwaltung** ->**Datenträgerverwaltung**.
   2. Klicken Sie mit der rechten Maustaste auf **Datenträgerverwaltung**, und wählen Sie **Datenträger neu einlesen** aus.
   3. Wählen Sie in der Liste der Datenträger die aktualisierten Volumes aus und dann **Volume erweitern**. Der Assistent zum Erweitern von Volumes wird gestartet. Klicken Sie auf **Weiter**.
   4. Schließen Sie den Assistenten unter Verwendung der angegebenen Standardwerte ab. Nach Abschluss des Assistenten sollte für das Volume die höhere Speichergröße angezeigt werden.
      
      > [!NOTE]
      > Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume erweitern, werden die Aufträge zur Volumeerweiterung nacheinander ausgeführt. Der erste Auftrag zur Volumeerweiterung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumeerweiterung beginnt.
      

## <a name="change-the-volume-type"></a>Ändern des Volumetyps

Sie können den Volumetyp von mehrstufig in lokal ändern oder umgekehrt. Diese Konvertierung sollte jedoch nicht häufig vorkommen.

### <a name="tiered-to-local-volume-conversion-considerations"></a>Überlegungen zur Konvertierung von mehrstufig in lokal

Für die Konvertierung eines Volumes von mehrstufig in lokal gibt es folgende Gründe:

* Lokale Garantien in Bezug auf Datenverfügbarkeit und Leistung
* Beseitigung von Cloudlatenzzeiten und Cloudverbindungsproblemen.

Normalerweise handelt es sich dabei um kleine vorhandene Volumes, auf die Sie häufig zugreifen möchten. Ein lokales Volume wird bei seiner Erstellung vollständig bereitgestellt. 

Wenn Sie ein mehrstufiges Volume in ein lokales Volume konvertieren, stellt StorSimple vor der Konvertierung sicher, dass auf dem Gerät genügend Speicherplatz verfügbar ist. Wenn Sie nicht über genügend Speicherplatz verfügen, erhalten Sie eine Fehlermeldung und der Vorgang wird abgebrochen. 

> [!NOTE]
> Bevor Sie eine Konvertierung von mehrstufig in lokal starten, berücksichtigen Sie unbedingt den Platzbedarf der anderen Workloads. 

Die Konvertierung von einem mehrstufigen in ein lokales Volume kann sich negativ auf die Geräteleistung auswirken. Darüber hinaus kann sich die erforderliche Dauer für die Konvertierung durch die folgenden Faktoren verlängern:

* Es ist nicht genügend Bandbreite vorhanden.
* Es ist keine aktuelle Sicherung vorhanden.

So können Sie die möglichen Auswirkungen dieser Faktoren minimieren

* Überprüfen Sie die Richtlinien für die Bandbreitendrosselung, und stellen Sie sicher, dass eine dedizierte Bandbreite von 40 MBit/s verfügbar ist.
* Planen Sie die Durchführung der Konvertierung außerhalb der Spitzenzeiten.
* Erstellen Sie eine Cloudmomentaufnahme, bevor Sie die Konvertierung starten.

Wenn Sie mehrere Volumes (mit verschiedenen Workloads) konvertieren, sollten Sie die Konvertierung der Volumes so priorisieren, dass Volumes mit einer höheren Priorität zuerst konvertiert werden. Beispielsweise sollten Sie Volumes, die virtuelle Computer oder Volumes mit SQL-Workloads hosten, konvertieren, bevor Sie Volumes mit Workloads zur Dateifreigabe konvertieren.

### <a name="local-to-tiered-volume-conversion-considerations"></a>Überlegungen zur Konvertierung von lokal in mehrstufig

Möglicherweise empfiehlt es sich, ein lokales Volume in ein mehrstufiges Volume zu ändern, wenn Sie zusätzlichen Speicherplatz zur Bereitstellung anderer Volumes benötigen. Bei der Konvertierung des lokalen Volumes in ein mehrstufiges erhöht sich die auf dem Gerät verfügbare Kapazität um die Größe der freigegebenen Kapazität. Wenn Verbindungsprobleme die Konvertierung eines Volumes vom lokalen in den mehrstufigen Typ verhindern, zeigt das lokale Volume Eigenschaften eines mehrstufigen Volumes, bis die Konvertierung abgeschlossen ist. Dies liegt daran, dass einige Daten möglicherweise in die Cloud übergegangen sind. Diese übergelaufenen Daten belegen weiterhin lokalen Speicherplatz auf dem Gerät, der erst freigegeben werden kann, nachdem der Vorgang neu gestartet und abgeschlossen wurde.

> [!NOTE]
> Das Konvertieren eines Volumes kann einige Zeit dauern, und eine Konvertierung kann nach ihrem Start nicht abgebrochen werden. Das Volume bleibt während der Konvertierung online, und Sie können Sicherungen erstellen. Sie können das Volume jedoch nicht erweitern oder wiederherstellen, solange die Konvertierung stattfindet.


#### <a name="to-change-the-volume-type"></a>So ändern Sie den Volumetyp

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie in der Tabelle mit den Geräten das Gerät mit dem Volume aus, das Sie ändern möchten. Klicken Sie auf **Einstellungen > Volumes**.

    ![Wechseln Sie zum Blatt „Volumes“](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Wählen Sie in der Tabelle mit den Volumes das Volume aus, und klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen. Wählen Sie **Ändern** aus.

    ![Wählen Sie „Ändern“ im Kontextmenü aus](./media/storsimple-8000-manage-volumes-u2/changevoltype2.png)

4. Ändern Sie auf dem Blatt **Volume ändern** den Volumetyp, indem Sie den neuen Typ in der Dropdownliste **Typ** auswählen.
   
   * Wenn Sie den Typ in **Lokal**ändern, prüft StorSimple, ob genügend Kapazität vorhanden ist.
   * Wenn Sie den Typ in **Mehrstufig** ändern und dieses Volume für archivierte Daten verwendet werden soll, aktivieren Sie das Kontrollkästchen **Verwenden Sie dieses Volume für Archivdaten, auf die Sie seltener zugreifen**.
   * Wenn Sie ein lokales Volume als mehrstufig konfigurieren oder _umgekehrt_, wird die folgende Meldung angezeigt.
   
     ![Meldung beim Ändern des Volumetyps](./media/storsimple-8000-manage-volumes-u2/changevoltype3.png)

7. Klicken Sie zum Speichern der Änderungen auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um den Konvertierungsvorgang fortzusetzen. 

    ![Speichern und bestätigen](./media/storsimple-8000-manage-volumes-u2/modifyvol11.png)

8. Das Azure-Portal zeigt eine Benachrichtigung zur Auftragserstellung an, durch die das Volume aktualisiert wird. Klicken Sie auf die Benachrichtigung zum Überwachen des Status des Auftrags zur Volumekonvertierung.

    ![Auftrag zur Volumekonvertierung](./media/storsimple-8000-manage-volumes-u2/changevoltype5.png)

## <a name="take-a-volume-offline"></a>Offlineschalten von Volumes

Möglicherweise müssen Sie ein Volume offline schalten, wenn Sie es ändern oder löschen möchten. Wenn ein Volume offline ist, ist es nicht für den Lese-/ Schreibzugriff verfügbar. Sie müssen das Volume auf dem Host und Gerät offline schalten.

#### <a name="to-take-a-volume-offline"></a>So schalten Sie ein Volume offline

1. Stellen Sie vor dem Offlineschalten sicher, dass das betreffende Volume nicht verwendet wird.
2. Schalten Sie das Volume zunächst auf dem Host offline. Dadurch werden potenzielle Schäden an den Daten auf dem Volume vermieden. Genaue Anweisungen finden Sie in den Anweisungen für das Hostbetriebssystem.
3. Wenn der Host offline ist, schalten Sie das Volume anhand der folgenden Schritte auf dem Gerät offline:
   
    1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie in der Tabelle mit den Geräten das Gerät mit dem Volume aus, das Sie ändern möchten. Klicken Sie auf **Einstellungen > Volumes**.

        ![Wechseln Sie zum Blatt „Volumes“](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

    2. Wählen Sie in der Tabelle mit den Volumes das Volume aus, und klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen. Wählen Sie **Offline schalten** aus, um das Volume offline zu schalten, das Sie ändern möchten.

        ![Auswählen und Offlineschalten des Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol4.png)

3. Überprüfen Sie auf dem Blatt **Offline schalten** die Auswirkungen des Offlineschalten des Volumes, und aktivieren Sie das entsprechende Kontrollkästchen. Klicken Sie auf **Offline schalten**. 

    ![Überprüfen der Auswirkungen des Offlineschaltens eines Volumes](./media/storsimple-8000-manage-volumes-u2/modifyvol5.png)
      
      Sie werden benachrichtigt, sobald das Volume offline ist. Der Status des Volumes wird in „Offline“ geändert.
      
4. Wenn Sie, sobald ein Volume offline ist, das Volume auswählen und mit der rechten Maustaste darauf klicken, wird im Kontextmenü die Option **Online schalten** verfügbar.

> [!NOTE]
> Mit dem Befehl **Offline schalten** wird eine Anforderung an das Gerät gesendet, das Volume offline zu schalten. Wenn das Volume weiterhin von Hosts verwendet wird, führt dies zur Unterbrechung von Verbindungen, das Offlineschalten des Volumes schlägt jedoch nicht fehl.

## <a name="delete-a-volume"></a>Löschen von Volumes

> [!IMPORTANT]
> Sie können ein Volume nur löschen, wenn dieses offline ist.

Führen Sie die folgenden Schritte durch, um ein Volume zu löschen.

#### <a name="to-delete-a-volume"></a>So löschen Sie ein Volume

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie in der Tabelle mit den Geräten das Gerät mit dem Volume aus, das Sie ändern möchten. Klicken Sie auf **Einstellungen > Volumes**.

    ![Wechseln Sie zum Blatt „Volumes“](./media/storsimple-8000-manage-volumes-u2/modifyvol2.png)

3. Überprüfen Sie den Status des zu löschenden Volumes. Wenn das Volume, das Sie löschen möchten, nicht offline ist, schalten Sie sie zunächst offline. Führen Sie die unter [Offlineschalten von Volumes](#take-a-volume-offline)genannten Schritte aus.
4. Sobald das Volume offline ist, klicken Sie mit der rechten Maustaste darauf, um das Kontextmenü aufzurufen, und wählen Sie dann **Löschen** aus.

    ![Wählen Sie „Löschen“ im Kontextmenü aus](./media/storsimple-8000-manage-volumes-u2/deletevol1.png)

5. Überprüfen Sie das Blatt **Löschen**, und aktivieren Sie das Kontrollkästchen für die Auswirkungen des Löschens eines Volumes. Wenn Sie ein Volume löschen, gehen alle auf dem Volume vorhandenen Daten verloren. 

    ![Speichern und Bestätigen von Änderungen](./media/storsimple-8000-manage-volumes-u2/deletevol2.png)

6. Nachdem das Volume gelöscht wird, wird die Tabelle mit den Volumes entsprechend aktualisiert.

    ![Aktualisierte Volumeliste](./media/storsimple-8000-manage-volumes-u2/deletevol3.png)
   
   > [!NOTE]
   > Wenn Sie ein lokales Volume löschen, wird der für neue Volumes verfügbare Speicherplatz möglicherweise nicht sofort aktualisiert. Der StorSimple-Geräte-Manager-Dienst aktualisiert in regelmäßigen Abständen den verfügbaren lokalen Speicherplatz. Warten Sie einige Minuten, bevor Sie das neue Volume erstellen.
   >
   > Außerdem gilt: Wenn Sie ein lokales Volume und unmittelbar danach ein weiteres lokales Volume löschen, werden die Aufträge zur Volumelöschung nacheinander ausgeführt. Der erste Auftrag zur Volumelöschung muss abgeschlossen sein, bevor der nächste Auftrag zur Volumelöschung beginnt.

## <a name="monitor-a-volume"></a>Volume überwachen

Mit der Volumeüberwachung können Sie E/A-bezogene Statistiken für ein Volume erfassen. Die Überwachung wird standardmäßig für die ersten 32 von Ihnen erstellten Volumes aktiviert. Die Überwachung der zusätzlichen Volumes ist in der Standardeinstellung deaktiviert. 

> [!NOTE]
> Die Überwachung geklonter Volumes ist standardmäßig deaktiviert.


Führen Sie die folgenden Schritte durch, um die Überwachung eines Volumes zu aktivieren oder zu deaktivieren.

#### <a name="to-enable-or-disable-volume-monitoring"></a>So aktivieren oder deaktivieren Sie die Volumeüberwachung

1. Navigieren Sie zum StorSimple-Geräte-Manager-Dienst, und klicken Sie dann auf **Geräte**. Wählen Sie in der Tabelle mit den Geräten das Gerät mit dem Volume aus, das Sie ändern möchten. Klicken Sie auf **Einstellungen > Volumes**.
2. Wählen Sie in der Tabelle mit den Volumes das Volume aus, und klicken Sie mit der rechten Maustaste, um das Kontextmenü aufzurufen. Wählen Sie **Ändern** aus.
3. Wählen Sie auf dem Blatt **Volume ändern** für **Überwachung** entweder **Aktivieren** oder **Deaktivieren** aus, um die Überwachung zu aktivieren oder zu deaktivieren.

    ![Deaktivieren der Überwachung](./media/storsimple-8000-manage-volumes-u2/monitorvol1.png) 

4. Klicken Sie auf **Speichern**. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**. Das Azure-Portal zeigt eine Benachrichtigung zur Aktualisierung des Volumes und dann eine Erfolgsmeldung, nachdem das Volume erfolgreich aktualisiert wurde.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie ein [StorSimple-Volume klonen](storsimple-8000-clone-volume-u2.md).
* Erfahren Sie, wie Sie [Ihr StorSimple-Gerät mithilfe des StorSimple-Geräte-Manager-Diensts verwalten](storsimple-8000-manager-service-administration.md).

