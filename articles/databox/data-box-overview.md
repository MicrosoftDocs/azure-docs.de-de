---
title: Microsoft Azure Data Box – Übersicht | Microsoft-Dokumentation
description: In diesem Artikel wird Azure Data Box beschrieben, eine Cloudlösung, die zum Übertragen sehr großer Datenmengen in Azure dient.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 52772519cc3b9aebc42175e812ad47ae54b529e9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94336680"
---
# <a name="what-is-azure-data-box"></a>Was ist Azure Data Box?

Mit der Cloudlösung Microsoft Azure Data Box können Sie Daten in Terabyte-Größe schnell, kostengünstig und zuverlässig in und aus Azure übertragen. Die sichere Datenübertragung wird beschleunigt, indem Sie ein spezielles Data Box-Speichergerät erhalten. Jedes Speichergerät verfügt über eine maximal nutzbare Speicherkapazität von 80 TB und wird von einem regionalen Zustelldienst zu Ihrem Rechenzentrum transportiert. Das Gerät verfügt über ein widerstandsfähiges Gehäuse zum Schützen und Absichern von Daten während des Transports.

Sie können das Data Box-Gerät im Azure-Portal bestellen, um Daten aus Azure zu importieren oder in Azure zu exportieren. Nach Empfang des Geräts können Sie es auf der lokalen Webbenutzeroberfläche schnell einrichten. Je nachdem, ob Sie Daten importieren oder exportieren möchten, kopieren Sie die Daten von Ihren Servern auf das Gerät oder umgekehrt und senden das Gerät zurück an Azure. Im Azure-Rechenzentrum werden Ihre Daten dann automatisch vom Gerät in Azure hochgeladen, wenn Sie Daten in Azure importieren möchten. Der gesamte Prozess wird im Azure-Portal vom Data Box-Dienst von Anfang bis Ende nachverfolgt.

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]


## <a name="use-cases"></a>Anwendungsfälle

Data Box eignet sich ideal für die Übertragung von Datenmengen von mehr als 40 TB in Szenarien ohne oder mit eingeschränkter Netzwerkkonnektivität. Die Datenverschiebung kann eine einmalige, eine periodische oder eine erste Massenübertragung von Daten sein, auf die regelmäßige Übertragungen folgen. 

Im Folgenden finden Sie verschiedene Szenarien, in denen Data Box zum Importieren von Daten in Azure verwendet werden kann.

 - **Einmalige Migration** : Wird verwendet, wenn eine große Menge von lokalen Daten in Azure verschoben wird. 
     - Verschieben einer Medienbibliothek von Offlinebändern in Azure, um eine Onlinemedienbibliothek zu erstellen
     - Migrieren Ihrer VM-Farm, SQL-Server und Anwendungen in Azure
     - Verschieben von Verlaufsdaten in Azure für eine detaillierte Analyse und Berichterstellung mithilfe von HDInsight

 - **Erste Massenübertragung** : Die Ausführung einer ersten Massenübertragung mithilfe von Data Box (Seeding) gefolgt von inkrementellen Übertragungen über das Netzwerk. 
     - Beispielsweise werden Partner im Bereich Sicherungslösungen wie Commvault und Data Box herangezogen, um die erste große Sicherung von Verlaufsdaten in Azure zu verschieben. Im Anschluss werden die inkrementellen Daten über das Netzwerk in Azure Storage übertragen.

- **Periodische Uploads** : Werden verwendet, wenn regelmäßig große Datenmengen generiert werden und in Azure verschoben werden müssen. Ein Beispiel hierfür ist die Exploration in der Energiebranche, bei der Videodaten auf Bohrinseln und für Windfarmen generiert werden. 

Im Folgenden finden Sie verschiedene Szenarien, in denen Data Box zum Exportieren von Daten aus Azure verwendet werden kann.

- **Notfallwiederherstellung** : Wenn eine Kopie der Daten aus Azure in einem lokalen Netzwerk wiederhergestellt wird. Dies geschieht typischerweise im Falle eines Notfallwiederherstellungsszenarios, bei dem eine große Menge an Azure-Daten in Data Box exportiert wird. Microsoft liefert diese Data Box dann aus, und die Daten werden in kurzer Zeit bei Ihnen vor Ort wiederhergestellt. 

- **Sicherheitsanforderungen** : Wenn Sie in der Lage sein müssen, Daten aus Azure aufgrund von behördlichen oder Sicherheitsanforderungen zu exportieren. Beispielsweise ist Azure Storage in den US-Clouds „Secret“ und „Top Secret“ verfügbar, und Sie können Data Box verwenden, um Daten aus Azure zu exportieren. 

- **Rückmigration zu einer lokalen Lösung oder zu einem anderen Clouddienstanbieter** : Wenn Sie alle Daten zurück an Ihren lokalen Standort oder zu einem anderen Clouddienstanbieter verschieben möchten, exportieren Sie Daten über Data Box, um die Workloads zu migrieren.


## <a name="benefits"></a>Vorteile

Data Box ist so ausgelegt, dass große Datenmengen ohne bzw. geringfügigen negativen Auswirkungen auf das Netzwerk in Azure verschoben werden können. Diese Lösung hat die folgenden Vorteile:

- **Geschwindigkeit** : Data Box nutzt eine Netzwerkschnittstelle mit 1 oder 10 GBit/s, um bis zu 80 TB Daten in Azure zu verschieben.

- **Sicherheit** : Data Box bietet einen integrierten Sicherheitsschutz für Geräte, Daten und den Dienst.
  - Das Gerät verfügt über ein widerstandsfähiges Gehäuse, das durch manipulationssichere Schrauben und Etiketten gesichert ist. 
  - Die Daten auf dem Gerät sind jederzeit durch AES-256-Bit-Verschlüsselung geschützt.
  - Das Gerät kann nur mit einem Kennwort entsperrt werden, das über das Azure-Portal bereitgestellt wird.
  - Der Dienst ist durch Azure-Sicherheitsfunktionen geschützt.
  - Nachdem Ihre Daten für einen Importauftrag in Azure hochgeladen wurden, werden die Datenträger auf dem Gerät gemäß NIST-Standards (800 bis 88r1) vollständig bereinigt. Bei einem Exportauftrag werden die Datenträger gelöscht, sobald das Gerät das Azure-Rechenzentrum erreicht hat.
    
    Weitere Informationen finden Sie unter [Azure Data Box – Sicherheit und Schutz von Daten](data-box-security.md).

## <a name="features-and-specifications"></a>Funktionen und Spezifikationen

Das Data Box-Gerät weist in dieser Version folgende Merkmale auf.

| Spezifikationen                                          | BESCHREIBUNG              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | Weniger als 23 kg                |
| Dimensionen                                              | Gerät: Breite: 309,0 mm Höhe: 430,4 mm Tiefe: 502,0 mm |            
| Platzbedarf im Rack                                              | 7 HE bei seitlicher Platzierung im Rack (kann nicht im Rack montiert werden)|
| Erforderliche Kabel                                         | 1 Netzkabel (inbegriffen) <br> 2 RJ45-Kabel <br> 2 SFP+-Twinax-Kupferkabel|
| Speicherkapazität                                        | Das Gerät mit 100 TB hat nach der Aktivierung von RAID-5-Schutz 80 TB nutzbare Kapazität.|
| Nennleistung                                            | Das Netzteil ist für 700 W ausgelegt. <br> Die Leistungsaufnahme liegt in der Regel bei 375 W.|
| Netzwerkschnittstellen                                      | 2 1-GbE-Schnittstellen: MGMT, DATA 3. <br> MGMT: Zur Verwaltung, nicht vom Benutzer konfigurierbar, dient zur erstmaligen Einrichtung <br> DATA3: Für Daten, vom Benutzer konfigurierbar und standardmäßig dynamisch <br> MGMT und DATA 3 sind auch in der 10-GbE-Ausführung möglich <br> 2 10-GbE-Schnittstellen: DATA 1, DATA 2 <br> Beide für Daten vorgesehen, als dynamisch (Standard) oder statisch konfigurierbar |
| Datenübertragung                                      | Sowohl Import als auch Export werden unterstützt.  |
| Datenübertragungsmedien                                     | RJ45, SFP+-Kupferkabel (10 GbE)  |
| Sicherheit                                                | Widerstandsfähiges Gerätegehäuse mit manipulationssicheren Spezialschrauben <br> Manipulationsgeschütztes Etikett auf der Unterseite des Geräts|
| Datenübertragungsrate                                      | Bis zu 80 TB am Tag über 10-GbE-Netzwerkschnittstelle        |
| Verwaltung                                              | Lokale Webbenutzeroberfläche für einmalige anfängliche Einrichtung und Konfiguration <br> Azure-Portal für die sonstige Geräteverwaltung        |

## <a name="data-box-components"></a>Data Box-Komponenten

Data Box umfasst die folgenden Komponenten:

* **Data Box-Gerät** : Ein physisches Gerät, das folgende Aufgaben hat: Primäres Speichermedium, Verwaltung der Kommunikation mit dem Cloudspeicher und Gewährleistung der Sicherheit und Vertraulichkeit aller Daten, die auf dem Gerät gespeichert sind. Das Data Box-Gerät bietet eine nutzbare Speicherkapazität von 80 TB. 

    ![Vorder- und Rückseite des Data Box-Geräts](media/data-box-overview/data-box-combined.png)

    
* **Data Box-Dienst:** Eine Erweiterung des Azure-Portals, mit der Sie ein Data Box-Gerät auf einer zentralen Webbenutzeroberfläche verwalten können, auf die an verschiedenen geografischen Standorten zugegriffen werden kann. Sie verwenden den Data Box-Dienst für die tägliche Verwaltung Ihres Data Box-Geräts. Zu den Aufgaben des Diensts gehören das Erstellen und Verwalten von Aufträgen, das Anzeigen und Verwalten von Warnmeldungen und das Verwalten von Freigaben.  

    ![Der Data Box-Dienst im Azure-Portal](media/data-box-overview/data-box-service.png)

    Weitere Informationen finden Sie unter [Verwalten Ihres Data Box-Geräts mithilfe des Data Box-Diensts](data-box-portal-ui-admin.md).

* **Lokale Webbenutzeroberfläche:** Eine webbasierte Benutzeroberfläche zum Konfigurieren des Geräts für das Herstellen der Verbindung mit dem lokalen Netzwerk und anschließende Registrieren des Geräts beim Data Box-Dienst. Auf der lokalen Webbenutzeroberfläche können Sie auch das Data Box-Gerät herunterfahren und neu starten, Kopierprotokolle anzeigen und den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen.

    ![Die lokale Webbenutzeroberfläche von Data Box](media/data-box-overview/data-box-local-web-ui.png)

    Informationen zur Verwendung der webbasierten Benutzeroberfläche finden Sie unter [Verwenden der webbasierten Benutzeroberfläche zum Verwalten Ihrer Data Box](data-box-portal-ui-admin.md).

## <a name="the-workflow"></a>Workflow

Der Importworkflow umfasst üblicherweise die folgenden Schritte:

1. **Auftrag** : Erstellen Sie im Azure-Portal einen Auftrag, und geben Sie die Versandinformationen und das Azure-Zielspeicherkonto für Ihre Daten an. Wenn das Gerät verfügbar ist, wird es von Azure vorbereitet und mit einer Sendungsverfolgungs-ID versendet.

2. **Empfang** : Nach Erhalt des Geräts verbinden Sie es mit den vorgesehenen Kabeln mit dem Netzwerk und der Stromversorgung. Schalten Sie das Gerät ein, und verbinden Sie sich damit. Konfigurieren Sie die Netzwerk- und Bereitstellungsfreigaben auf dem Hostcomputer, von dem aus Sie die Daten kopieren möchten.

3. **Kopieren von Daten** : Kopieren Sie Daten auf die Data Box-Freigaben.

4. **Rücksendung** : Bereiten Sie das Gerät vor, schalten Sie es aus, und senden Sie es an das Azure-Rechenzentrum zurück.

5. **Hochladen** : Daten werden automatisch vom Gerät in Azure kopiert. Die Datenträger des Geräts werden gemäß NIST-Richtlinien (National Institute of Standards and Technology) auf sichere Weise gelöscht.

Bei diesem Prozess werden Sie per E-Mail über alle Statusänderungen informiert. Weitere Informationen zum detaillierten Ablauf finden Sie unter [Bereitstellen von Data Box im Azure-Portal](data-box-deploy-ordered.md).


Der Exportworkflow umfasst üblicherweise die folgenden Schritte:

1. **Auftrag** : Erstellen Sie im Azure-Portal einen Exportauftrag, und geben Sie Versandinformationen sowie das Azure Storage-Quellkonto für Ihre Daten an. Wenn das Gerät verfügbar ist, bereitet Azure ein Gerät vor. Daten werden aus Ihrem Azure Storage-Konto in Data Box kopiert. Nachdem der Datenkopiervorgang abgeschlossen wurde, wird das Gerät von Microsoft mit einer Sendungsnachverfolgungs-ID ausgeliefert.

2. **Empfang** : Nach Erhalt des Geräts verbinden Sie es mit den vorgesehenen Kabeln mit dem Netzwerk und der Stromversorgung. Schalten Sie das Gerät ein, und verbinden Sie sich damit. Konfigurieren Sie die Netzwerk- und Bereitstellungsfreigaben auf dem Hostcomputer, auf den Sie die Daten kopieren möchten.

3. **Kopieren der Daten** : Kopieren Sie Daten aus Data Box-Freigaben auf die lokalen Datenserver.

4. **Rücksendung** : Bereiten Sie das Gerät vor, schalten Sie es aus, und senden Sie es an das Azure-Rechenzentrum zurück.

5. **Datenlöschung** :Die Datenträger des Geräts werden gemäß NIST-Richtlinien (National Institute of Standards and Technology) auf sichere Weise gelöscht.

Während des Exportvorgangs werden Sie per E-Mail über alle Statusänderungen informiert. Weitere Informationen zum detaillierten Ablauf finden Sie unter [Bereitstellen von Data Box im Azure-Portal](data-box-deploy-export-ordered.md).

## <a name="region-availability"></a>Regionale Verfügbarkeit

Data Box kann Daten basierend auf der Region, in der der Dienst bereitgestellt wird, dem Land/der Region, in das/die das Gerät versendet wird, und dem Azure Storage-Zielkonto übertragen, in das Sie die Daten übertragen. 

### <a name="for-import"></a>Für Importvorgänge

- **Dienstverfügbarkeit** : Wenn Sie Data Box für Import- oder Exportaufträge verwenden, finden Sie Informationen zur regionalen Verfügbarkeit unter [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die Exportfunktion von Data Box befindet sich derzeit in der Vorschauphase. 

    Für Importaufträge kann Data Box auch in der Azure Government-Cloud bereitgestellt werden. Weitere Informationen finden Sie unter [What is Azure Government?](../azure-government/documentation-government-welcome.md) (Was ist Azure Government?). 

- **Zielspeicherkonten** : Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar, in denen der Dienst verfügbar ist.


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Data Box-Systemanforderungen](data-box-system-requirements.md) an.
- Machen Sie sich mit den [Data Box-Einschränkungen](data-box-limits.md) vertraut.
- Erfahren Sie mehr zur schnellen Bereitstellung von [Azure Data Box](data-box-quickstart-portal.md) im Azure-Portal.