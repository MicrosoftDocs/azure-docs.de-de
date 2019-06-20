---
title: Private-SKUs und -Pläne | Azure Marketplace
description: Erfahren Sie, wie Sie private SKUs verwenden, um die Angebotsverfügbarkeit zu verwalten.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6efdb1c28777d9230727066fdba03d2850be62b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935906"
---
<a name="private-skus-and-plans"></a>Private SKUs und Pläne
============

Private SKUs ermöglichen das Beschränken der Verfügbarkeit von SKUs auf bestimmte Kunden. Wird eine SKU als privat gekennzeichnet, ist sie nicht in jedem öffentlichen Katalog verfügbar. Dies gilt auch für den [Azure Marketplace](https://azuremarketplace.microsoft.com) und das [Azure-Portal](https://portal.azure.com). Im Azure-Portal wird die SKU nur Kunden mit Zugriff auf die SKU angezeigt. Darüber hinaus werden sie auch darüber informiert, dass sie Zugriff auf private Angebote haben.

>[!NOTE]
>Private SKUs benötigen neue eindeutige SKU-/Tarif-IDs, um mögliche Konflikte mit Ihren öffentlichen SKUs zu vermeiden.

Sie können private SKUs für die folgenden Szenarien verwenden:

1.  Veröffentlichen von Software, die nur für bestimmte Kunden und nicht öffentlich verfügbar sein soll
2.  Veröffentlichen von Varianten öffentlicher Software zu einem benutzerdefinierten Preis für bestimmte Kunden
3.  Veröffentlichen von Varianten öffentlicher Software mit einer benutzerdefinierten Beschreibung und angepassten Nutzungsbedingungen (über ein neues Angebot)

Wenn Sie nur den Preis ändern möchten, können Sie die Datenträger einer anderen SKU in demselben Angebot wiederverwenden. Sie müssen bei privaten SKUs keine Datenträger zwischen den SKUs erneut übermitteln.

<a name="mark-a-sku-private"></a>Kennzeichnen einer SKU als privat
---------------------

Um eine SKU als privat zu markieren, schalten Sie die entsprechende Option um:

![Kennzeichnen einer SKU als privat](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Sie können die Datenträger aus einer anderen SKU wiederverwenden und den Preis oder die Beschreibung anpassen. Wenn Sie die Datenträger wiederverwenden möchten, wählen Sie **Ja** als Antwort auf „Does this SKU re-use images from a public SKU“ (Nutzt diese SKU Images einer öffentlichen SKU?) aus.

Wenn die SKU als privat gekennzeichnet wurde und das Angebot andere SKUs mit wiederverwendbaren Datenträgern enthält, müssen Sie angeben, dass die SKU Datenträger aus einer anderen SKU wiederverwendet. Sie müssen auch die Zielgruppe für die private SKU angeben.

>[!NOTE]
>Nachdem eine öffentliche SKU veröffentlicht wurde, kann sie nicht mehr in eine private umgewandelt werden.

<a name="select-an-image"></a>Auswählen eines Images
------------------

Sie können neue Datenträger für die private SKU bereitstellen oder Datenträger, die bereits in einer anderen SKU bereitgestellt wurden, erneut verwenden und nur die Preise oder die Beschreibung ändern. Wenn Sie die Datenträger wiederverwenden möchten, wählen Sie **Ja** als Antwort auf „Does this SKU re-use images from a public SKU“ (Nutzt diese SKU Images einer öffentlichen SKU?) aus.

![Angeben der Wiederverwendung von Images](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Nachdem Sie bestätigt haben, dass die SKU Images einer anderen SKU wiederverwendet, geben Sie die SKU an, die als Quelle für die Images fungiert.

Die Anweisungen im nächsten Screenshot zeigen, wie Sie angeben, dass eine private SKU Images der ausgewählten SKU wiederverwendet:

![Auswählen eines Images](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Wenn Sie das Angebot veröffentlichen, werden die Images der ausgewählten SKU unter der privaten SKU-ID mit den benutzerdefinierten Preisen/Nutzungsbedingungen verfügbar gemacht. Die private SKU wird nur für die Zielgruppe sichtbar gemacht.

Bei Imageupdates müssten Sie lediglich das Image der zugrunde liegenden SKU aktualisieren. Das Image der privaten SKU wird im Hintergrund ebenfalls automatisch aktualisiert. Entsprechend wird ein Image, wenn es aus der zugrunde liegenden SKU gelöscht wird, auch aus der privaten SKU entfernt.

<a name="restricting-the-audience"></a>Einschränken der Zielgruppe
------------------------

Private Angebote können nur von Benutzern aus der Zielgruppe gefunden und bereitgestellt werden.
Derzeit wird das Einschränken der Benutzer anhand von Abonnement-IDs unterstützt.

Diese Abonnements können manuell über ein Formular **für bis zu 10 Abonnements** eingegeben oder durch Hochladen einer CSV-Datei **mit bis zu 20.000 Abonnements** angegeben werden.

Manuelles Eingeben der eingeschränkten Zielgruppe:

![Manuelles Einschränken der Zielgruppe](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Hochladen einer CSV-Datei zum Einschränken der Zielgruppe:

![Verwenden einer CSV-Datei zum Einschränken der Zielgruppe](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Beispielinhalt der CSV-Datei:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Beim Wechsel von der manuellen Eingabe zum Hochladen einer CSV-Datei oder von einer CSV-Datei zur manuellen Eingabe wird die alte Liste mit den Abonnement-IDs mit Zugriff auf die SKU nicht beibehalten. Es wird eine Warnung angezeigt, und die Liste wird erst nach dem Speichern des Angebots überschrieben.

<a name="sync-private-subscriptions"></a>Synchronisieren privater Abonnements
-------------------------

Wenn Sie Abonnements zu einem veröffentlichten Angebot mit privaten SKUs oder Plänen hinzufügen, müssen Sie das Angebot nicht erneut veröffentlichen, um Zielgruppeninformationen hinzuzufügen. Verwenden Sie einfach eine Azure-Abonnement-ID (Pläne und SKUs) oder eine Mandanten-ID (nur Pläne), um die Zielgruppe hinzuzufügen.

<a name="previewing-private-offers"></a>Anzeigen einer Vorschau für private Angebote
-------------------------

Während des Vorschau-/Stagingschritts können nur Abonnements mit einer Berechtigung für die Vorschauversion auf die SKU zugreifen. Dies ist die Testphase, während der Sie überprüfen können, wie das Angebot für Ihre Kundenzielgruppe aussehen würde. Sie gilt für alle Arten von Veröffentlichungen.

Gewähren des Zugriffs auf Angebote im Stagingprozess für Abonnements mit Zugriff auf die Vorschauversion:

![Preview-Abonnement-IDs](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Nachdem das Angebot live geschaltet wurde, können nur Abonnements der eingeschränkten Zielgruppe (die manuell oder per CSV-Datei angegeben wurden) die private SKU anzeigen und bereitstellen. Es wird zu Validierungszwecken für private SKUs empfohlen, stets **Ihre eigenen Abonnements in die eingeschränkte Zielgruppe einzuschließen**.

>[!NOTE]
>Zum Debuggen benötigen auch die Support- und Technikerteams von Microsoft Zugriff auf diese privaten Angebote.
