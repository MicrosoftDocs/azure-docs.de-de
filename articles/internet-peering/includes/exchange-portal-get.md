---
title: include file
titleSuffix: Azure
description: include file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e139954e6550e33edb75d01ab9dbec0bba543ea6
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92548067"
---
Nach erfolgreicher Bereitstellung der **Peeringressource** können Sie sie anhand der folgenden Schritte anzeigen.

1. Navigieren Sie zu **Ressourcengruppen** , und wählen Sie die Ressourcengruppe aus, die Sie beim Erstellen der **Peeringressource** ausgewählt haben. Verwenden Sie das Feld **Filter** , wenn zu viele Ressourcengruppen aufgeführt werden.

    > [!div class="mx-imgBorder"]
    > ![Ressourcengruppen](../media/setup-direct-get-resourcegroup.png)

1. Wählen Sie die erstellte **Peeringressource** aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mit der von Ihnen erstellten Peeringressource.](../media/setup-direct-get-open.png)

1. Auf der Seite **Übersicht** werden allgemeine Informationen wie in diesem Beispiel angezeigt.

    > [!div class="mx-imgBorder"]
    > ![Übersichtsbereich für Peeringressourcen](../media/setup-exchange-get-overview.png)

1. Wählen Sie auf der linken Seite **ASN-Informationen** aus, um die Informationen anzuzeigen, die bei der Erstellung von PeerAsn übermittelt wurden.

    > [!div class="mx-imgBorder"]
    > ![ASN-Informationen zur Peeringressource](../media/setup-direct-get-asninfo.png)

1. Wählen Sie auf der linken Seite die Option **Verbindungen** aus. Oben auf dem Bildschirm finden Sie eine Zusammenfassung der Peeringverbindungen zwischen Ihrer ASN und Microsoft über verschiedene Einrichtungen innerhalb der Metropolregion hinweg. Sie können auch auf der Seite **Übersicht** auf die Verbindungszusammenfassung zugreifen, indem Sie wie hier gezeigt im mittleren Bereich **Verbindungen** auswählen.

    > [!div class="mx-imgBorder"]
    > ![Verbindungen von Peeringressourcen](../media/setup-exchange-get-connectionssummary.png)

    * **Verbindungsstatus** gibt den Status der eingerichteten Peeringverbindung an. Die in diesem Feld angezeigten Zustände entsprechen dem Statusdiagramm, das in der [exemplarischen Vorgehensweise zum Exchange-Peering](../walkthrough-exchange-all.md) abgebildet ist.
    * **IPv4-Sitzungsstatus** und **IPv6-Sitzungsstatus** entsprechen den Statuswerten der IPv4- bzw. IPv6-BGP-Sitzung.  
    * Wenn Sie oben auf dem Bildschirm eine Zeile auswählen, werden im Abschnitt **Verbindung** im unteren Bereich Details zu den einzelnen Verbindungen angezeigt. Wählen Sie die Pfeile aus, um **Konfiguration** , **IPv4-Adresse** und **IPv6-Adresse** zu erweitern.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mit Hervorhebung eines Pfeils zum Aufklappen eines Abschnitt.](../media/setup-exchange-get-connectionsipv4.png)
