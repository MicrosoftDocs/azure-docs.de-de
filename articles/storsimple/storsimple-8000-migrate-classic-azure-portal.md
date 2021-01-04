---
title: Migrieren von Speicherkonten und Abonnements für den StorSimple-Geräte-Manager
description: Erfahren Sie, wie Sie Abonnements und Speicherkonten für Ihren StorSimple-Geräte-Manager-Dienst migrieren.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 523788dc727fc5fa32b2f57250fb738352ff6d31
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491519"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Migrieren von Abonnements und Speicherkonten, die dem StorSimple-Geräte-Manager-Dienst zugeordnet sind

Sie müssen möglicherweise den StorSimple-Dienst zu einer neuen Registrierung oder einem neuen Abonnement verschieben. Diese Migrationsszenarien sind entweder Kontoänderungen oder Rechenzentrumsänderungen. Die folgende Tabelle informiert Sie inklusive detaillierter Schrittbeschreibungen zum Verschieben darüber, welche dieser Szenarien unterstützt werden.

## <a name="account-changes"></a>Kontoänderungen

| Können Sie Folgendes verschieben?| Unterstützt| Ausfallzeit| Azure-Supportprozess| Vorgehensweise|
|-----|-----|-----|-----|-----|
| Ein gesamtes Abonnement (einschließlich StorSimple-Dienst und Speicherkonten) zu einer anderen Registrierung | Ja       | Nein       | **Übertragung der Registrierung**<br>Verwendung:<li>Wenn Sie eine neue Azure-Verpflichtung im Rahmen eines neuen Vertrags erwerben.</li><li>Sie möchten alle Konten und Abonnements von der alten Registrierung zur neuen migrieren. Dies schließt alle Azure-Dienste unter dem alten Abonnement ein.</li> | **Schritt 1: Öffnen Sie ein Azure Enterprise Operation Support-Ticket.**<li>Wechseln Sie zu [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li> Wählen Sie **Registrierungsverwaltung** und dann **Übertragung von einer Registrierung zu einer neuen Registrierung**.<br>**Schritt 2: Geben Sie die angeforderten Informationen an**<br>Inklusive:<li>Quellenregistrierungsnummer</li><li> Zielregistrierungsnummer</li><li>Datum des Inkrafttretens der Übertragung|
| StorSimple-Dienst aus einem vorhandenen Konto zu einer neuen Registrierung    | Ja       | Nein       | **Kontoübertragung**<br>Verwendung:<li>Wenn Sie keine vollständige Registrierungsübertragung wünschen.</li><li>Sie möchten nur bestimmte Konten zu einer neuen Registrierung verschieben.</li>| **Schritt 1: Öffnen Sie ein Azure Enterprise Operation Support-Ticket.**<li>Wechseln Sie zu [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).</li><li>Wählen Sie **Registrierungsverwaltung** und dann **Übertragung eines EA-Kontos zu einer neuen Registrierung**.<br>**Schritt 2: Geben Sie die angeforderten Informationen an**<br>Inklusive:<li>Quellenregistrierungsnummer</li><li> Zielregistrierungsnummer</li><li>Datum des Inkrafttretens der Übertragung|
| StorSimple-Dienst von einem Abonnement zu einem anderen Abonnement      | Nein        |    Ja         | Weder noch, dies ist ein manueller Prozess.|<li>Migrieren Sie die Daten aus dem StorSimple-Gerät.</li><li>Führen Sie eine Zurücksetzung des Geräts auf die Werkseinstellungen durch. Damit werden alle lokalen Daten auf dem Gerät gelöscht.</li><li>Registrieren Sie das Gerät mit dem neuen Abonnement bei einem StorSimple-Geräte-Manager-Dienst.</li><li>Migrieren Sie die Daten zurück zum Gerät.|
|Kann ich den Besitz eines Azure-Abonnements auf ein anderes Konto übertragen? | Ja       | Nein       | Zuordnen eines vorhandenen Abonnements zu Ihrem Azure AD-Verzeichnis | Informationen finden Sie unter [So weisen Sie Ihrem Azure AD-Verzeichnis ein vorhandenes Abonnement zu](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Es kann bis zu zehn Minuten dauern, bis alles richtig angezeigt wird.|
| StorSimple-Gerät von einem StorSimple-Geräte-Manager-Dienst zu einem anderen Dienst in einer anderen Region      | Nein        | Ja            | Weder noch, dies ist ein manueller Prozess. |Wie oben.|
| Speicherkonten in ein neues Abonnement oder eine neue Ressourcengruppe?     | Ja        | Nein             |Verschieben eines Speicherkontos in ein anderes Abonnement oder eine andere Ressourcengruppe |Wenn die Speicherkonto-Zugriffsschlüssel nach der Verschiebung aktualisiert werden, muss der Benutzer die Zugriffsschlüssel für das migrierte Speicherkonto manuell über den StorSimple-Geräte-Manager-Dienst konfigurieren.|
| Klassisches Speicherkonto in ein Azure Resource Manager-Speicherkonto      | Ja        | Nein             |Migrieren von einem klassischen Speicherkonto zum Azure Resource Manager |<li>Ausführliche Anweisungen zum Migrieren eines Speicherkontos von einer klassischen Bereitstellung zum Azure Resource Manager finden Sie unter [Migrieren eines klassischen Speicherkontos](../virtual-machines/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account).</li><li> Wenn die Speicherkonto-Zugriffsschlüssel nach der Migration aktualisiert werden, muss der Benutzer die Zugriffsschlüssel für das migrierte Speicherkonto über den StorSimple-Geräte-Manager-Dienst synchronisieren. Dadurch wird sichergestellt, dass StorSimple-Geräte weiterhin ordnungsgemäß ausgeführt werden und diese ein Tiering für primäre bzw. Sicherungsdaten nach Azure durchführen können. Ausführliche Anweisungen zum Synchronisieren von Zugriffsschlüsseln finden Sie unter [Schlüsselrotation für Speicherkonten](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> Wenn das klassische Speicherkonto migriert wird, der zugrunde liegende virtuelle Computer jedoch weiterhin in der klassischen Bereitstellung verbleibt, sollte das Gerät Bei einem StorSimple Cloud Appliance ordnungsgemäß ausgeführt werden. Wenn der zugrunde liegende virtuelle Computer für das Cloudgerät migriert wird, werden die Funktionen zum Deaktivieren und Löschen nicht ordnungsgemäß ausgeführt.</li><li> Sie müssen ein neues StorSimple Appliance Cloud-Gerät im Azure-Portal erstellen und dann ein Failover von älteren Cloudgeräten durchführen. Sie können nicht mithilfe eines klassischen Speicherkontos ein StorSimple Cloud Appliance-Gerät im neuen Azure-Portal erstellen, da hierfür ein Azure Resource Manager-Speicherkonto erforderlich ist. Weitere Informationen finden Sie unter [Bereitstellen und Verwalten einer StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|

## <a name="datacenter-changes"></a>Rechenzentrumsänderungen

| Können Sie Folgendes verschieben?| Unterstützt|Ausfallzeit| Azure-Supportprozess| Vorgehensweise|
|-----|-----|-----|-----|-----|
| Einen StorSimple-Dienst aus einem Azure-Rechenzentrum zu einem anderen | Nein | Ja |Weder noch, dies ist ein manueller Prozess.  |<li>Migrieren Sie die Daten aus dem StorSimple-Gerät.</li><li>Führen Sie eine Zurücksetzung des Geräts auf die Werkseinstellungen durch. Damit werden alle lokalen Daten auf dem Gerät gelöscht.</li><li>Registrieren Sie das Gerät mit dem neuen Abonnement bei einem neuen StorSimple-Geräte-Manager-Dienst.</li><li>Migrieren Sie die Daten zurück zum Gerät.|
| Ein Speicherkonto aus einem Azure-Rechenzentrum zu einem anderen | Nein |Ja  |Weder noch, dies ist ein manueller Prozess.  | Wie oben.|

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen des StorSimple-Geräte-Manager-Diensts für Geräte der StorSimple 8000-Serie](storsimple-8000-manage-service.md)
* [Bereitstellen lokaler StorSimple-Geräte (Update 3 und höher)](storsimple-8000-deployment-walkthrough-u2.md)