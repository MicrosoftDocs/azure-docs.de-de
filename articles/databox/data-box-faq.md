---
title: Microsoft Azure Data Box – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Enthält häufig gestellte Fragen und Antworten zu Azure Data Box, einer Cloudlösung, mit der Sie große Datenmengen in Azure übertragen können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 05/23/2019
ms.author: alkohli
ms.openlocfilehash: 18c6175ede1e8459d3a9ca60f7890cc43d75e5ba
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236280"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Häufig gestellte Fragen

Mit der Hybridlösung Microsoft Azure Data Box und einem Versandgerät können Sie Daten in Terabyte-Größe schnell, kostengünstig und zuverlässig in Azure übertragen. In diesem Artikel finden Sie Antworten auf Fragen, die bei Verwenden von Data Box im Azure-Portal ggf. aufkommen. 

Die Fragen und Antworten sind in folgende Kategorien unterteilt:

- Informationen zum Dienst
- Bestellen des Geräts
- Konfigurieren und Verbinden 
- Nachverfolgen des Status
- Kopieren von Daten 
- Versenden des Geräts
- Überprüfen und Hochladen von Daten 
- Unterstützung der Rückverfolgbarkeit

## <a name="about-the-service"></a>Informationen zum Dienst

### <a name="q-what-is-azure-data-box-service"></a>F: Was ist der Azure Data Box-Dienst? 
A.  Der Azure Data Box-Dienst wurde für die Offlineerfassung von Daten konzipiert. Dieser Dienst verwaltet eine Palette von Produkten mit unterschiedlichen Speicherkapazitäten, die alle auf den Datentransport ausgelegt sind. 

### <a name="q-what-is-azure-data-box"></a>F: Was ist Azure Data Box?
A. Azure Data Box ermöglicht eine schnelle, kostengünstige und sichere Übertragung von Daten im Terabyte-Bereich in Azure. Sie können das Data Box-Gerät im Azure-Portal bestellen. Microsoft liefert Ihnen über einen regionalen Zustelldienst ein Speichergerät mit einer nutzbaren Kapazität von 80 TB. 

Nach Empfang des Geräts können Sie es auf der lokalen Webbenutzeroberfläche schnell einrichten. Kopieren Sie die Daten von Ihren Servern auf das Gerät, und senden Sie es zurück an Azure. Im Azure-Rechenzentrum werden Ihre Daten dann automatisch vom Gerät in Azure hochgeladen. Der gesamte Prozess wird im Azure-Portal vom Data Box-Dienst von Anfang bis Ende nachverfolgt.

### <a name="q-when-should-i-use-data-box"></a>F: Wann sollte ich Data Box nutzen?
A. Sie profitieren von der Verwendung von Data Box, wenn Sie über 40-500 TB an Daten verfügen, die Sie in Azure übertragen möchten. Verwenden Sie Data Box Disk für Datenmengen von weniger als 40 TB und [Data Box Heavy](data-box-heavy-overview.md) für Datenmengen von mehr als 500 TB.

### <a name="q-what-is-the-price-of-data-box"></a>F: Was kostet Data Box?
A. Data Box ist gegen eine Schutzgebühr für 10 Tage erhältlich. Wenn Sie beim Anlegen eines Auftrags im Azure-Portal das Produktmodell auswählen, werden die Gebühren für das Gerät angezeigt. Der Versand ist kostenlos, aber es fallen Gebühren für Azure Storage an. Weitere Informationen finden Sie unter [Azure Data Box – Preise](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>F: Welche maximale Datenmenge kann ich mit Data Box auf einmal übertragen?
A. Data Box hat eine Rohkapazität von 100 TB und eine nutzbare Kapazität von 80 TB. Sie können mit Data Box bis zu 80 TB Daten übertragen. Um eine größere Datenmenge zu übertragen, müssen Sie weitere Datenträger bestellen.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>F: Wie kann ich prüfen, ob Data Box in meiner Region verfügbar ist? 
A.  Informationen dazu, in welchen Ländern/Regionen Data Box verfügbar ist, finden Sie unter [Was ist Azure Data Box?](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>F: In welchen Regionen kann ich Daten mit Data Box speichern?
A. Data Box wird in allen Regionen in den USA, Europa, Norden, Europa, Westen, Frankreich und im Vereinigten Königreich unterstützt. Es werden nur die öffentlichen Azure-Cloudregionen unterstützt. Azure Government oder andere Sovereign Clouds werden nicht unterstützt. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>F: An wen sollte ich mich wenden, falls Probleme mit Data Box auftreten?
A. Falls Probleme mit Data Box auftreten, wenden Sie sich an den [Microsoft-Support](data-box-disk-contact-microsoft-support.md).

### <a name="q-i-have-lost-my-data-box-is-there-a-lost-device-charge"></a>F: Ich habe meine Data Box verloren. Fallen für ein verlorenes Gerät Gebühren an?
A. Ja. Für ein verloren gegangenes oder beschädigtes Gerät fällt eine Gebühr an. Informationen zu dieser Gebühr finden Sie auf der Seite mit den [Preisen](https://azure.microsoft.com/pricing/details/storage/databox/) sowie in den [Nutzungsbedingungen für das Produkt](https://www.microsoft.com/licensing/product-licensing/products).


## <a name="order-device"></a>Bestellen des Geräts

### <a name="q-how-do-i-get-data-box"></a>F: Wie erhalte ich Data Box? 
A.  Melden Sie sich beim Azure-Portal an, und erstellen Sie einen Data Box-Auftrag. Geben Sie Ihre Kontaktinformationen und Benachrichtigungsdetails an. Nachdem Sie den Auftrag erteilt haben, wird die Data Box Ihnen je nach Verfügbarkeit binnen zehn Tagen zugestellt. Weitere Informationen finden Sie unter [Bestellen einer Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>F: Ich konnte im Azure-Portal keinen Data Box-Auftrag erstellen. Was könnte der Grund sein?
A. Wenn Sie keinen Data Box-Auftrag erstellen konnten, liegt entweder mit Ihrem Abonnementtyp oder Ihrem Zugriff ein Problem vor. 

Überprüfen Sie Ihr Abonnement. Data Box ist nur für Kunden mit Enterprise Agreement (EA) und Cloud Solution Provider (CSP) verfügbar. Falls Ihr Abonnement in keine dieser genannten Kategorien fällt, wenden Sie sich an den Microsoft-Support, um Ihr Abonnement zu ändern.

Wenn Sie eine für das Abonnement unterstützte Angebotsart haben, überprüfen Sie Ihre Zugriffsebene im Abonnement. Sie müssen Mitwirkender oder Besitzer in Ihrem Abonnement sein, um einen Auftrag erstellen zu können.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>F: Ich habe mehrere Data Box-Geräte bestellt. Ich kann keine zusätzlichen Aufträge erteilen. Was könnte der Grund sein?
A. Wir erlauben maximal fünf aktive Aufträge pro Abonnement pro Handelsgrenze (Kombination aus dem ausgewählten Land und der ausgewählten Region). Wenn Sie ein zusätzliches Gerät bestellen müssen, wenden Sie sich an den Microsoft-Support, um den Grenzwert für Ihr Abonnement zu erhöhen.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>F: Wenn ich versuche, einen Auftrag zu erstellen, erhalte ich die Meldung, dass der Data Box-Dienst nicht verfügbar ist. Was bedeutet dies?
A. Dies bedeutet, dass der Data Box-Dienst für die von Ihnen gewählte Kombination aus Land und Region nicht verfügbar ist. Wenn Sie diese Kombination ändern, können Sie den Data Box-Dienst wahrscheinlich nutzen. Eine Liste der Regionen, in denen der Dienst verfügbar ist, finden Sie unter [Regionale Verfügbarkeit von Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>F: Ich habe meinen Data Box-Auftrag vor einigen Tagen erteilt. Wann erhalte ich meine Data Box?
A. Wenn Sie einen Auftrag erteilen, prüfen wir, ob ein Gerät zur Verfügung steht. Wenn ein Gerät verfügbar ist, versenden wir es binnen 10 Tagen. Es ist denkbar, dass es Zeiten mit hoher Nachfrage gibt. In diesem Fall wird Ihr Auftrag in eine Warteschlange gestellt, und Sie können die Statusänderung im Azure-Portal verfolgen. Wenn Ihr Auftrag innerhalb von 90 Tagen nicht erfüllt wird, wird der Auftrag automatisch storniert.

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>F: Ich habe meine Data Box mit Daten gefüllt und muss eine weitere bestellen. Gibt es eine Möglichkeit, die Bestellung ohne viel Zeitaufwand aufzugeben?
A. Sie können Ihren vorherigen Auftrag klonen. Beim Klonen wird derselbe Auftrag noch einmal erstellt, und Sie müssen nur noch die Auftragsdetails angeben, ohne Adresse, Kontaktinformationen und Benachrichtigungsdetails erneut eingeben zu müssen.

## <a name="configure-and-connect"></a>Konfigurieren und Verbinden

### <a name="q-how-do-i-unlock-the-data-box"></a>F: Wie entsperre ich die Data Box? 
A.  Navigieren Sie im Azure-Portal zu Ihrem Data Box-Auftrag und dann zu **Gerätedetails**. Kopieren Sie das Kennwort zum Entsperren. Melden Sie sich mit diesem Kennwort auf der lokalen Webbenutzeroberfläche bei Ihrer Data Box an. Weitere Informationen finden Sie unter [Tutorial: Verkabeln und Herstellen einer Verbindung mit der Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>F: Kann ich einen Linux-Hostcomputer verwenden, um eine Verbindung herzustellen und die Daten auf die Data Box zu kopieren?
A.  Ja. Sie können mit Data Box eine Verbindung mit SMB- und NFS-Clients herstellen. Weitere Informationen finden Sie in der Liste mit den [unterstützten Betriebssystemen](data-box-system-requirements.md) für Ihren Hostcomputer.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F: Meine Data Box wurden versendet, aber jetzt möchte ich diesen Auftrag stornieren. Warum ist die Schaltfläche zum Stornieren nicht verfügbar?
A.  Sie können den Auftrag nur stornieren, wenn die Data Box bestellt, aber der Auftrag noch nicht bearbeitet wurde. Sobald der Data Box-Auftrag bearbeitet wurde, können Sie ihn nicht mehr stornieren. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>F: Kann ich eine Data Box gleichzeitig an mehrere Hostcomputer anschließen, um Daten zu übertragen?
A. Ja. Mehrere Hostcomputer können sich mit der Data Box verbinden, um Daten zu übertragen, und mehrere Kopieraufträge können parallel erfolgen. Weitere Informationen finden Sie unter [Tutorial: Kopieren von Daten auf Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-can-i-connect-to-both-the-10-gbe-interfaces-on-the-data-box-to-transfer-data"></a>F: Kann ich eine Verbindung mit beiden 10-GbE-Schnittstellen auf der Data Box herstellen, um Daten zu übertragen?
A. Ja. Sie können mit beiden 10-GbE-Schnittstellen auf der Data Box eine Verbindung herstellen, um gleichzeitig Daten zu kopieren. Weitere Informationen zum Kopieren von Daten finden Sie unter [Tutorial: Kopieren von Daten auf Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>F: Die LED zur Anzeige von Systemfehlern auf dem vorderen Bedienfeld leuchtet. Wie sollte ich vorgehen?
A. Wenn die LED zur Anzeige von Systemfehlern leuchtet, bedeutet dies, dass Ihr System nicht fehlerfrei ist. [Wenden Sie sich an den Microsoft Support](data-box-disk-contact-microsoft-support.md) für weitere Schritte.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>F: Ich kann im Azure-Portal nicht auf das Kennwort zum Entsperren der Data Box zugreifen. Was könnte der Grund sein?
A. Wenn Sie im Azure-Portal nicht auf das Entsperrkennwort zugreifen können, überprüfen Sie die Berechtigungen in Ihrem Abonnement und Speicherkonto. Vergewissern Sie sich, dass Sie auf Ressourcengruppenebene über die Berechtigung „Mitwirkender“ oder „Besitzer“ verfügen. Falls nicht, benötigen Sie mindestens die Berechtigung des Data Box-Bedieners, um die Anmeldeinformationen für den Zugriff einzusehen.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>F: Wird die Portkanalkonfiguration in Data Box unterstützt? Wie sieht es mit Multipfad-E/A aus?
A. Die Portkanalkonfiguration, die Konfiguration von Multipfad-E/A (Multipath IO, MPIO) und die vLAN-Konfiguration werden in Data Box nicht unterstützt.

## <a name="track-status"></a>Nachverfolgen des Status

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>F: Wie kann ich für die Data Box den Status ab der Erteilung des Auftrags bis zur Rücksendung des Geräts nachverfolgen? 
A.  Sie können den Status des Data Box-Auftrags im Azure-Portal nachverfolgen. Beim Erstellen des Auftrags werden Sie auch aufgefordert, eine E-Mail-Adresse für Benachrichtigungen anzugeben. Wenn Sie eine E-Mail-Adresse angegeben haben, werden Sie per E-Mail über alle Statusänderungen des Auftrags informiert. [Hier finden Sie weitere Informationen zum Konfigurieren von E-Mail-Benachrichtigungen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>F: Wie kann ich das Gerät zurücksenden? 
A.  Microsoft zeigt auf der E-Ink-Anzeige ein Versandetikett an. Navigieren Sie zu **Übersicht > Versandetikett herunterladen**, falls das Versandetikett nicht auf der E-Ink-Anzeige erscheint. Laden Sie das Etikett herunter, und drucken Sie es aus. Legen Sie das Etikett in die am Gerät angebrachte Klarsichthülle aus Kunststoff ein, und geben Sie das Gerät bei Ihrem Paketdienst ab. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>F: Ich habe eine E-Mail-Benachrichtigung erhalten, dass mein Gerät im Azure-Rechenzentrum angekommen ist. Wie erfahre ich, ob das Hochladen der Daten im Gange ist?
A. Navigieren Sie im Azure-Portal zu Ihrem Data Box-Auftrag und dann zu **Übersicht**. Wenn das Hochladen der Daten in Azure gestartet wurde, sehen Sie im rechten Bereich den Status des Kopiervorgangs. 

## <a name="migrate-data"></a>Migrieren von Daten

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>F: Welche Datenmenge wird von Data Box maximal unterstützt?  
A.  Data Box bietet eine nutzbare Speicherkapazität von 80 TB. Sie können ein einzelnes Data Box-Gerät für eine Datenmenge von 40 TB bis 80 TB verwenden. Für größere Datenmengen bis zu 500 TB können Sie mehrere Data Box-Geräte bestellen. Bei mehr als 500 TB Daten registrieren Sie sich für Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>F: Welche Größen für Blockblobs und Seitenblobs werden von Data Box maximal unterstützt? 
A.  Die maximale Größe richtet sich nach den Speichergrenzwerten für Azure. Die maximale Größe für Blockblobs beträgt ca. 4,768 TiB, und die maximale Größe für Seitenblobs beträgt 8 TiB. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../storage/common/storage-scalability-targets.md). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F: Woher weiß ich, dass meine Daten während der Übertragung geschützt sind? 
A. Mehrere Sicherheitsmaßnahmen sind aktiviert, um dafür zu sorgen, dass Ihre Data Box während des Versands geschützt ist. Dazu gehören u.a. manipulationssichere Siegel, die Erkennung von Manipulationen an Hardware und Software sowie das Kennwort zum Entsperren des Geräts. Weitere Informationen finden Sie unter [Azure Data Box – Sicherheit und Schutz von Daten](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>F: Wie kopiere ich die Daten auf die Data Box? 
A.  Wenn Sie einen SMB-Client verwenden, können Sie ein SMB-Kopiertool wie Robocopy, Diskboss oder sogar Windows-Datei-Explorer und Drag & Drop verwenden, um Daten auf das Gerät zu kopieren. 

Wenn Sie einen NFS-Client nutzen, können Sie [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) oder [Ultracopier](https://ultracopier.first-world.info/) verwenden. 

Weitere Informationen finden Sie unter [Tutorial: Kopieren von Daten auf Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F: Gibt es Tipps zur Beschleunigung des Datenkopiervorgangs?
A.  Sie können den Kopiervorgang wie folgt beschleunigen:

- Nutzen Sie mehrere Datenströme zum Kopieren von Daten. Verwenden Sie bei Robocopy beispielsweise die Option für Multithreading. Weitere Informationen zu den genauen Befehlen, die jeweils verwendet werden, finden Sie unter [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-deploy-copy-data.md).
- Verwenden Sie mehrere Sitzungen.
- Stellen Sie sicher, dass sich die Daten lokal auf dem Computer befinden, an den die Data Box angeschlossen ist, anstatt den Kopiervorgang über die Netzwerkfreigabe durchzuführen (um Beeinträchtigungen aufgrund der Netzwerkgeschwindigkeit zu vermeiden).
- Erstellen Sie Benchmarkwerte zur Leistung des Computers, der zum Kopieren der Daten verwendet wird. Laden Sie das [Bluestop-Tool FIO](https://bluestop.org/fio/) herunter, um Benchmarkwerte zur Leistung der Serverhardware zu erhalten.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>F: Kann ich mehrere Speicherkonten mit Data Box verwenden?
A.  Ja. Data Box unterstützt maximal 10 Speicherkonten (universell, klassisch oder Blob Storage). Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt. Von der allgemein verfügbaren Version werden in der öffentlichen Azure-Cloud Speicherkonten in allen Regionen in den USA, Europa, Westen, Europa, Norden, Frankreich und im Vereinigten Königreich unterstützt.


## <a name="ship-device"></a>Versenden des Geräts

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>F: Mein Gerät wurde zugestellt, aber es scheint beschädigt zu sein. Wie sollte ich vorgehen?
A. Wenn Ihr Gerät beschädigt zugestellt wurde oder es Anzeichen von Manipulationen gibt, verwenden Sie das Gerät nicht. [Wenden Sie sich an den Microsoft-Support](data-box-disk-contact-microsoft-support.md), und senden Sie das Gerät schnellstmöglich zurück. Sie können auch einen neuen Data Box-Auftrag für ein Ersatzgerät erstellen. In diesem Fall wird Ihnen das Ersatzgerät nicht in Rechnung gestellt.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>F: Kann ich für den Versand der Data Box meinen eigenen Zustelldienst nutzen?
A. Beim Data Box-Dienst übernimmt Microsoft den Versand zum und aus dem Azure Rechenzentrum. Wenn Sie Ihren eigenen Zustelldienst nutzen möchten, können Sie den Azure Import/Export-Dienst verwenden. Weitere Informationen finden Sie unter [Was ist der Azure Import/Export-Dienst?](../storage/common/storage-import-export-service.md)

### <a name="q-will-my-data-box-devices-cross-country-borders-during-shipping"></a>F: Überqueren meine Data Box-Geräte beim Versand Ländergrenzen?
A. Alle Data Box-Geräte werden aus dem Bestimmungsland versendet, sie überqueren keine internationalen Grenzen. Die einzige Ausnahme betrifft Bestellungen innerhalb der Europäischen Union (EU), wo Geräte in und aus beliebigen EU-Länder(n) gesendet werden können. Dies gilt sowohl für Data Box- als auch für Data Box Heavy-Geräte.

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>F: In der E-Ink-Anzeige wird das Rücksendeetikett nicht angezeigt. Wie sollte ich vorgehen?
A. Führen Sie die folgenden Schritte aus, wenn in der E-Ink-Anzeige das Rücksendeetikett nicht angezeigt wird:
- Entfernen Sie das alte Adressetikett und alle zur vorherigen Lieferung gehörigen Etiketten.
- Wechseln Sie im Azure-Portal zu Ihrem Auftrag. Navigieren Sie zu **Übersicht** und **Versandetikett herunterladen**. Weitere Informationen finden Sie unter [Herunterladen von Adressetiketten](data-box-portal-admin.md#download-shipping-label).
- Drucken Sie das Adressetikett, und stecken Sie es in die am Gerät angebrachte Klarsichthülle aus Kunststoff. 
- Stellen Sie sicher, dass das Adressetikett gut sichtbar ist. 

### <a name="q-how-is-my-data-protected-during-transit"></a>F: Wie sind meine Daten während des Versands geschützt? 
A.  Während des Versands dienen die folgenden Merkmale der Data Box dem Schutz der Daten.
 - Die Data Box-Datenträger werden mit AES-256-Verschlüsselung verschlüsselt. 
 - Das Gerät ist gesperrt und benötigt ein Entsperrkennwort, um Daten eingeben und darauf zugreifen zu können.
Weitere Informationen finden Sie unter [Sicherheitsmerkmale von Azure Data Box](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>F: Ich habe den Schritt „Für den Versand vorbereiten“ abgeschlossen und das Gerät heruntergefahren. Kann ich der Data Box noch weitere Daten hinzufügen?
A. Ja. Sie können das Gerät einschalten und weitere Daten hinzufügen. Sie müssen den Schritt **Für den Versand vorbereiten** erneut ausführen, nachdem Sie den Kopiervorgang abgeschlossen haben.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>F: Ich habe mein Gerät erhalten, und es startet nicht. Wie kann ich das Gerät zurücksenden?
A. Sollte Ihr Gerät nicht starten, navigieren Sie im Azure-Portal zu Ihrer Bestellung. Laden Sie ein Adressetikett herunter, und bringen Sie es an dem Gerät an. Weitere Informationen finden Sie unter [Herunterladen von Adressetiketten](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Überprüfen und Hochladen

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>F: Wie schnell kann ich in Azure auf meine Daten zugreifen, nachdem ich die Data Box zurückgesendet habe? 
A.  Sobald der Auftragsstatus für **Datenkopiervorgang** als **Abgeschlossen** angezeigt wird, sollten Sie sofort auf Ihre Daten zugreifen können.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F: Wo befinden sich meine Daten nach dem Upload in Azure?
A.  Wenn Sie die Daten in die Data Box kopieren, werden die Daten in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen – je nachdem, ob es sich dabei um Blockblob-, Seitenblob- oder Azure-Dateien handelt:
- `https://<storage_account_name>.blob.core.windows.net/<containername>` 
- `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
  Alternativ hierzu können Sie auch im Azure-Portal auf Ihr Azure-Speicherkonto zugreifen und von dort aus entsprechend navigieren.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F: Ich habe gerade festgestellt, dass ich mich für meine Container nicht an die Azure-Anforderungen für die Benennung gehalten habe. Können meine Daten deshalb nicht in Azure hochgeladen werden?
A.  Falls die Containernamen Großbuchstaben enthalten, werden diese Namen automatisch in Kleinbuchstaben konvertiert. Wenn die Namen auf sonstige Weise nicht konform sind (Sonderzeichen, andere Sprachen usw.), tritt beim Upload ein Fehler auf. Weitere Informationen zu bewährten Methoden für die Benennung von Freigaben, Containern und Dateien finden Sie hier:
- [Benennen und Referenzieren von Freigaben, Verzeichnissen, Dateien und Metadaten](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Grundlegendes zu Blockblobs, Anfügeblobs und Seitenblobs](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>F: Wie kann ich die Daten überprüfen, die in Data Box kopiert wurden?
A.  Nachdem der Datenkopiervorgang abgeschlossen ist, werden Ihre Daten überprüft, wenn Sie **Für den Versand vorbereiten** ausführen. Data Box generiert während des Validierungsprozesses eine Liste von Dateien und Prüfsummen für die Daten. Sie können die Liste der Dateien herunterladen und diese mit den Dateien in den Quelldaten vergleichen. Weitere Informationen finden Sie unter [Für den Versand vorbereiten](data-box-deploy-picked-up.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>F: Was passiert mit meinen Daten, nachdem ich die Data Box zurückgesendet habe?
A.  Nach Abschluss des Kopierens der Daten in Azure werden die Daten gemäß den NIST-Richtlinien (SP 800-88 Revision 1) auf sichere Weise von den Datenträgern der Data Box gelöscht. Weitere Informationen finden Sie unter [Löschen von Daten von Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Überwachungsbericht

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Wie unterstützt der Azure Data Box-Dienst für Kunden das Verfahren zur Rückverfolgbarkeit?
A.  Der Azure Data Box-Dienst stellt nativ Berichte zur Verfügung, die Sie für Ihre Dokumentation zur Rückverfolgbarkeit verwenden können. Die Prüf- und Kopierprotokolle sind in Ihrem Speicherkonto in Azure verfügbar, und Sie können im Azure-Portal [den Auftragsverlauf herunterladen](data-box-portal-admin.md#download-order-history), nachdem der Auftrag ausgeführt wurde.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Welche Art der Berichterstellung ist zur Unterstützung von Rückverfolgbarkeit verfügbar?
A.  Die folgende Art der Berichterstellung ist zur Unterstützung von Rückverfolgbarkeit verfügbar:

- Transportlogistik von UPS
- Protokollierung des Einschaltens und Zugriffs auf die Benutzerfreigabe
- Manifestdatei mit CRC-64-Prüfung (Cyclic Redundancy Check) oder Prüfsumme für jede Datei, die erfolgreich in der Data Box erfasst wurde
- Berichte zu Dateien, die nicht in das Azure Storage-Konto hochgeladen wurden
- Bereinigung des Data Box-Geräts (gemäß NIST 800-88R1-Standards) nach Kopieren der Daten in Ihr Azure Storage-Konto

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Stehen die Sendungsverfolgungsprotokolle des Zustelldiensts (UPS) zur Verfügung? 
A.  Die Sendungsverfolgungsprotokolle des Zustelldiensts werden im Data Box-Auftragsverlauf erfasst. Dieser Bericht steht Ihnen zur Verfügung, nachdem das Gerät an das Azure-Rechenzentrum zurückgesendet wurde und die Daten auf den Datenträgern des Geräts bereinigt wurden. Bei dringendem Bedarf können Sie auch direkt die Website des Zustelldiensts besuchen, die Sendungsverfolgungsnummer eingeben und die Sendungsverfolgungsinformationen abrufen.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Kann ich die Data Box selbst zum Azure-Rechenzentrum transportieren? 
A.  Nein. Derzeit nimmt das Azure-Rechenzentrum die Zustellung der Data Box von Kunden oder anderen Zustelldiensten als UPS nicht an.


## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Data Box-Systemanforderungen](data-box-system-requirements.md) an.
- Machen Sie sich mit den [Data Box-Einschränkungen](data-box-limits.md) vertraut.
- Erfahren Sie mehr zur schnellen Bereitstellung von [Azure Data Box](data-box-quickstart-portal.md) im Azure-Portal.
