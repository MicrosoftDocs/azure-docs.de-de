---
title: Oracle-Lösungen auf virtuellen Azure-Computern | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu unterstützten Konfigurationen und Einschränkungen von VM-Images für Oracle in Microsoft Azure.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 05/12/2020
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 093d3c0da0b40f2a287b1cb451a419f71ce4b8d7
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957587"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-Images und deren Bereitstellung in Microsoft Azure

Dieser Artikel enthält Informationen zu Oracle-Lösungen, die auf VM-Images basieren, die von Oracle im Azure Marketplace veröffentlicht wurden. Wenn Sie an cloudübergreifenden Anwendungslösungen mit Oracle Cloud Infrastructure interessiert sind, finden Sie Informationen unter [Oracle-Anwendungslösungen, die Microsoft Azure in Oracle Cloud Infrastructure integrieren](oracle-oci-overview.md).

Um eine Liste der derzeit verfügbaren Images zu erhalten, führen Sie den folgenden Befehl aus:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Stand Juni 2020 sind folgende Images verfügbar:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

Diese Images werden als „Bring Your Own License“ betrachtet. Daher werden nur die Kosten für Compute, Speicherung und Netzwerke berechnet, die durch die Ausführung eines virtuellen Computers anfallen.  Es wird vorausgesetzt, dass Sie über eine ordnungsgemäße Lizenz für die Verwendung von Oracle-Software und über einen aktuellen Supportvertrag mit Oracle verfügen. Oracle bietet eine garantierte Lizenzmobilität von einer lokalen Verwendung zu Azure. Informationen zur Lizenzmobilität finden Sie im veröffentlichten Artikel [Oracle und Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html).

Einzelpersonen können als Basis für ihre Lösungen auch ein benutzerdefiniertes Image verwenden, das sie in Azure von Grund auf neu erstellen, oder sie können ein benutzerdefiniertes Image aus ihren lokalen Umgebungen hochladen.

## <a name="oracle-database-vm-images"></a>Oracle Database VM-Images

Oracle unterstützt die Ausführung der Standard und Enterprise Edition von Oracle Database 12.1 und höher in Azure auf VM-Images, die auf Oracle Linux basieren.  Damit Oracle Database-Produktionsworkloads in Azure eine optimale Leistung erzielen, achten Sie darauf, dass Sie das VM-Image auf eine geeignete Größe festlegen und Managed Disks vom Typ „SSD Premium“ oder „SSD Ultra“ verwenden. Anweisungen dazu, wie Sie eine Oracle Database mit dem von Oracle veröffentlichten VM-Image schnell in Azure einrichten, erhalten Sie in der exemplarischen Vorgehensweise zum [Oracle Database-Schnellstart](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Optionen für die Konfiguration angefügter Datenträger

Angefügte Datenträger nutzen den Azure Blob Storage-Dienst. Auf jedem Standarddatenträger können theoretisch bis zu 500 E/A-Vorgänge pro Sekunde (IOPS) ausgeführt werden. Unser Premium-Datenträgerangebot wird in erster Linie für Datenbanknutzlasten mit hoher Leistung verwendet und kann bis zu 5000 IOps pro Datenträger erreichen. Sie können einen einzelnen Datenträger verwenden, wenn dieser Ihre Leistungsanforderungen erfüllt. Sie können die effektive IOPS-Leistung aber auch häufig verbessern, wenn Sie mehrere angefügte Datenträger verwenden, Datenbankdaten auf diesen verteilen und dann Oracle Automatic Storage Management (ASM) verwenden. Spezifische Informationen zu Oracle ASM finden Sie unter [Oracle Automatic Storage Management Overview](https://www.oracle.com/technetwork/database/index-100339.html) (in englischer Sprache). Ein Beispiel für das Installieren und Konfigurieren von Oracle ASM auf einem virtuellen Azure-Linux-Computer finden Sie im Tutorial [Installing and Configuring Oracle Automated Storage Management](configure-oracle-asm.md) (Installieren und Konfigurieren von Oracle Automated Storage Management).

### <a name="shared-storage-configuration-options"></a>Konfigurationsoptionen für freigegebenen Speicher

Azure NetApp Files wurde entworfen, um grundlegende Anforderungen für die Ausführung von Hochleistungsworkloads wie Datenbanken in der Cloud zu erfüllen. Der Dienst bietet Folgendes:

- Nativen Azure-Dienst für freigegebenen NFS-Speicher zum Ausführen von Oracle-Workloads entweder mit einem nativen NFS-Client der VM oder mit Oracle dNFS
- Skalierbare Leistungsstufen, die sich an tatsächlichen IOPS-Anforderungen orientieren
- Geringe Wartezeit
- Hochverfügbarkeit, hohe Dauerhaftigkeit und Verwaltbarkeit im jedem Umfang – in der Regel für geschäftskritische Unternehmensworkloads (wie SAP und Oracle)
- Schnelle und effiziente Sicherung und Wiederherstellung, für die höchsten RTO- und RPO-SLAs

Diese Funktionen sind möglich, da Azure NetApp Files auf den vollständig auf Flashspeicher basierenden NetApp® ONTAP®-Systemen aufbaut, die in Azure-Rechenzentren als ein nativer Azure-Dienst ausgeführt werden. Das Ergebnis ist eine perfekte Speichertechnologie für Datenbanken, die genau wie andere Azure Storage-Optionen bereitgestellt und genutzt werden kann. In der [Dokumentation zu Azure NetApp Files](../../../azure-netapp-files/index.yml) finden Sie weitere Informationen zum Bereitstellen von Azure NetApp Files-NFS-Volumes und zum Zugreifen darauf. Im Dokument [Oracle on Azure deployment best practice guide using Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) (Oracle auf Azure-Bereitstellung – Leitfaden mit bewährten Methoden unter Verwendung von Azure NetApp Files) finden Sie Empfehlungen für den Betrieb einer Oracle-Datenbank in Azure NetApp Files.

## <a name="licensing-oracle-database--software-on-azure"></a>Lizenzieren von Oracle Database und Software in Azure

Microsoft Azure ist eine autorisierte Cloudumgebung zum Ausführen von Oracle Database. Die Oracle Core Factor-Tabelle ist nicht anwendbar, wenn Sie Oracle-Datenbanken in der Cloud lizenzieren. Stattdessen können Sie, wenn Sie virtuelle Computer mit Hyperthreading-Technologie verwenden, die für Enterprise Edition-Datenbanken aktiviert ist, zwei vCPUs als gleichwertig zu einer Oracle Prozessor-Lizenz zählen, wenn Hyperthreading aktiviert ist (wie im Richtliniendokument angegeben). Die Richtliniendetails finden Sie [hier](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Oracle-Datenbanken erfordern grundsätzlich mehr Arbeitsspeicher und E/A. Aus diesem Grund werden für diese Workloads [virtuelle Computer mit optimierter Arbeitsspeichergröße](../../sizes-memory.md) empfohlen. Um die Workloads weiter zu optimieren, werden [vCPUs mit eingeschränkten Kerngrößen](../../constrained-vcpu.md) für Oracle Database-Workloads empfohlen, die viel Arbeitsspeicher, Speicher und E/A-Bandbreite beanspruchen, aber keine hohe Anzahl von Kernen erfordern.

Wenn Oracle-Software und -Workloads aus einer lokalen Umgebung zu Microsoft Azure migriert werden, bietet Oracle Lizenzmobilität, wie sie unter [Oracle and Microsoft Azure: Frequently Asked Questions](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) beschrieben ist.

## <a name="high-availability-and-disaster-recovery-considerations"></a>Überlegungen zu Hochverfügbarkeit und Notfallwiederherstellung

Wenn Sie Oracle-Datenbanken in Azure verwenden, müssen Sie eine Lösung für Hochverfügbarkeit und Notfallwiederherstellung implementieren, um Ausfallzeiten zu vermeiden.

Hochverfügbarkeit und Notfallwiederherstellung für Oracle Database Enterprise Edition (ohne Abhängigkeit von Oracle RAC) lassen sich in Azure mithilfe von [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) oder [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate) mit zwei Datenbanken auf zwei getrennten virtuellen Computern erreichen. Beide virtuellen Computer sollten sich in demselben [virtuellen Netzwerk](https://azure.microsoft.com/documentation/services/virtual-network/) befinden, um sicherzustellen, dass sie über die private statische IP-Adresse Zugriff aufeinander haben.  Darüber hinaus wird empfohlen, die virtuellen Computer in derselben Verfügbarkeitsgruppe zu platzieren, damit sie von Azure in eigenen Fehlerdomänen und Upgradedomänen angeordnet werden können. Falls Sie Georedundanz verwenden möchten, richten Sie die zwei Datenbanken so ein, dass sie zwischen verschiedenen Regionen repliziert werden und die zwei Instanzen mit einem VPN Gateway verbinden.

Das Tutorial [Implementieren von Oracle DataGuard in Azure](configure-oracle-dataguard.md) führt Sie durch die grundlegenden Schritte der Einrichtung unter Azure.  

Mit Oracle Data Guard kann Hochverfügbarkeit mit einer primären Datenbank auf einem virtuellen Computer, einer sekundären Datenbank (Standbydatenbank) auf einem weiteren virtuellen Computer und unidirektionaler Replikation zwischen diesen Komponenten erzielt werden. Das Ergebnis ist Lesezugriff auf die Kopie der Datenbank. Mit Oracle GoldenGate können Sie bidirektionale Replikation zwischen den beiden Datenbanken konfigurieren. Informationen zum Einrichten einer Hochverfügbarkeitslösung für die Datenbanken mithilfe dieser Tools finden Sie in der Dokumentation zu [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) und [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) auf der Oracle-Website. Wenn Sie Schreibzugriff auf die Kopie der Datenbank benötigen, können Sie [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)verwenden.

Das Tutorial [Implementieren von Oracle GoldenGate in Azure](configure-oracle-golden-gate.md) führt Sie durch die grundlegenden Schritte der Einrichtung unter Azure.

Zusätzlich dazu, dass Sie eine Lösung für Hochverfügbarkeit und Notfallwiederherstellung in Azure besitzen, sollten Sie über eine Sicherungsstrategie zum Wiederherstellen der Datenbank verfügen. Das Tutorial zum [Sichern und Wiederherstellen einer Oracle-Datenbank](./oracle-overview.md) führt Sie durch das grundlegenden Verfahren der Einrichtung einer konsistenten Sicherung.

## <a name="support-for-jd-edwards"></a>Unterstützung für JD Edwards

Laut dem Oracle-Support-Artikel [Dok.-ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) werden JD Edwards EnterpriseOne, Versionen 9.2 und höher, in **jedem öffentlichen Cloudangebot** unterstützt, das ihre spezifischen `Minimum Technical Requirements` (MTR, technische Mindestanforderungen) erfüllt.  Sie müssen benutzerdefinierte Images erstellen, die ihre MTR-Spezifikationen für das Betriebssystem und die Anwendungskompatibilität erfüllen.

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Angebote für virtuelle Oracle WebLogic Server-Computer

Oracle und Microsoft arbeiten zusammen, um WebLogic Server in Form einer Sammlung von Azure-Anwendungsangeboten in Azure Marketplace anzubieten.  Diese Angebote werden im Artikel [Oracle WebLogic Server: Azure-Anwendungen](oracle-weblogic.md) beschrieben.

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Images von virtuellen Oracle WebLogic Server-Computern

- **Clustering wird nur in der Enterprise Edition unterstützt.** Sie sind nur für die Verwendung des WebLogic-Clusterings lizenziert, wenn Sie die Enterprise Edition von Oracle WebLogic Server verwenden. Verwenden Sie Clustering nicht mit der Standard Edition von Oracle WebLogic Server.
- **UDP-Multicast wird nicht unterstützt.** Azure unterstützt UDP-Unicasting, jedoch weder Multicasting noch Broadcasting. Oracle WebLogic Server kann die UDP-Unicast-Funktionen von Azure nutzen. Um optimale Ergebnisse bei der Nutzung von UDP-Unicast zu erzielen, empfiehlt es sich, eine statische Größe des WebLogic-Clusters zu verwenden oder nicht mehr als 10 verwaltete Server aufzunehmen.
- **Oracle WebLogic Server erwartet, dass für den T3-Zugriff (z. B. bei Verwendung von Enterprise JavaBeans) öffentliche und private Ports identisch sind.** Beispielszenario für mehrere Ebenen: Eine Anwendung auf Dienstebene (EJB) wird in einem Oracle WebLogic Server-Cluster, der aus mindestens zwei VMs besteht, in einem virtuellen Netzwerk mit dem Namen *SLWLS* ausgeführt. Die Clientebene befindet sich in einem anderen Subnetz in demselben virtuellen Netzwerk, und es wird ein einfaches Java-Programm ausgeführt, das versucht, EJB auf der Dienstebene aufzurufen. Da die Dienstebene einen Lastenausgleich erfordert, muss für die virtuellen Computer im Oracle WebLogic Server-Cluster ein öffentlicher Endpunkt mit Lastenausgleich erstellt werden. Wenn der private Port, den Sie angeben, nicht mit dem öffentlichen Port identisch ist (z.B. 7006:7008), tritt ein Fehler wie der folgende auf:

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   Dies liegt daran, dass Oracle WebLogic Server für jeden T3-Remotezugriff erwartet, dass der Load Balancer-Port und der Port des verwalteten WebLogic-Servers identisch sind. Im obigen Fall greift der Client auf Port 7006 (den Port für den Lastenausgleich) zu, und der verwaltete Server lauscht an Port 7008 (dem privaten Port). Diese Einschränkung gilt nur für den T3-Zugriff und nicht für HTTP.

   Sie können dieses Problem mit einer der folgenden Problemumgehungen vermeiden:

- Verwenden Sie für Endpunkte mit Lastenausgleich, die für T3-Zugriff vorgesehen sind, die gleiche Nummer für den öffentlichen und privaten Port.
- Geben Sie beim Starten von Oracle WebLogic Server auch den folgenden JVM-Parameter an:

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

Verwandte Informationen finden Sie im KB-Artikel **860340.1** unter <https://support.oracle.com>.

- **Einschränkungen für dynamisches Clustering und Lastenausgleich**. Angenommen, Sie möchten in Oracle WebLogic Server einen dynamischen Cluster verwenden und über einen einzelnen öffentlichen Endpunkt mit Lastenausgleich in Azure verfügbar machen. Dies ist möglich, solange Sie für jeden verwalteten Server eine feste Portnummer (keine dynamisch aus einem Bereich zugewiesene Nummer) verwenden und nicht mehr verwaltete Server starten, als Computer vorhanden sind, die vom Administrator nachverfolgt werden. Das heißt, es gibt nicht mehr als einen verwalteten Server pro virtuellem Computer. Wenn die Konfiguration bewirkt, dass mehr Oracle WebLogic Server gestartet werden, als virtuelle Computer vorhanden sind (d. h. mehrere Oracle WebLogic Server-Instanzen verwenden denselben virtuellen Computer), kann nur eine dieser Oracle WebLogic Server-Instanzen an eine bestimmte Portnummer gebunden werden. Die anderen Instanzen auf diesem virtuellen Computer schlagen fehl.

   Wenn Sie festlegen, dass der Administratorserver automatisch den verwalteten Servern eindeutige Portnummern zuweist, ist kein Lastenausgleich möglich. Der Grund dafür ist, dass diese Konfiguration das Zuordnen eines einzelnen öffentlichen Ports zu mehreren privaten Ports erfordert. Dies wird jedoch von Azure nicht unterstützt.
- **Mehrere Oracle WebLogic Server-Instanzen auf einem virtuellen Computer**. Je nach den Anforderungen der Bereitstellung sollten Sie in Betracht ziehen, mehrere Oracle WebLogic Server-Instanzen auf demselben virtuellen Computer auszuführen, sofern die Größe des virtuellen Computers ausreicht. Beispielsweise können Sie auf einem virtuellen Computer mittlerer Größe, der zwei Kerne enthält, zwei Oracle WebLogic Server-Instanzen ausführen. Wir raten aber weiterhin davon ab, nur einen virtuellen Computer zur Ausführung mehrerer Oracle WebLogic Server-Instanzen zu verwenden, da hierdurch SPOFs (Single Points of Failure) in der Architektur entstehen würden. Ein besserer Ansatz ist die Verwendung von mindestens zwei virtuellen Computern. Auf jedem virtuellen Computer könnten dann mehrere Oracle WebLogic Server-Instanzen ausgeführt werden. Jede Oracle WebLogic Server-Instanz könnte weiterhin demselben Cluster angehören. Zurzeit kann Azure jedoch nicht für den Lastenausgleich bei Endpunkten verwendet werden, die durch Oracle WebLogic Server-Bereitstellungen auf demselben virtuellen Computer verfügbar gemacht werden. Der Grund dafür ist, dass der Azure Load Balancer die Verteilung der Server mit Lastenausgleich auf eindeutige virtuelle Computer erfordert.

## <a name="oracle-jdk-virtual-machine-images"></a>Images von virtuellen Oracle JDK-Computern

- **Neueste Updates von JDK 6 und 7**. Es wird zwar empfohlen, die neueste öffentliche unterstützte Version von Java (derzeit Java 8) zu verwenden, aber für Azure werden auch JDK 6- und JDK 7-Images verfügbar gemacht. Diese sind für ältere Anwendungen vorgesehen, die noch nicht auf JDK 8 aktualisiert werden können. Updates früherer JDK-Images sind möglicherweise nicht mehr für die allgemeine Öffentlichkeit verfügbar. Aufgrund der Partnerschaft von Microsoft mit Oracle bieten die von Azure bereitgestellten JDK 6- und JDK 7-Images jedoch ein neueres nicht öffentliches Update, das Oracle normalerweise nur einer bestimmten Gruppe unterstützter Oracle-Kunden anbietet. Im Lauf der Zeit werden neuere Version der JDK-Images mit aktualisierten Versionen von JDK 6 und 7 zur Verfügung gestellt.

   Das in den JDK 6- und JDK 7-Images verfügbare JDK und die von ihnen abgeleiteten virtuellen Computer und Images können nur in Azure verwendet werden.
- **64-Bit-JDK.** Die von Azure bereitgestellten Images virtueller Oracle WebLogic Server-Computer und virtueller Oracle JDK-Computer enthalten die 64-Bit-Versionen von Windows Server und dem JDK.

## <a name="next-steps"></a>Nächste Schritte

Sie haben jetzt einen Überblick über aktuelle Oracle-Lösungen, die auf VM-Images basieren, in Microsoft Azure. Der nächste Schritt besteht darin, die erste Oracle-Datenbank in Azure bereitzustellen.

> [!div class="nextstepaction"]
> [Erstellen einer Oracle-Datenbank in Azure](oracle-database-quick-create.md)
