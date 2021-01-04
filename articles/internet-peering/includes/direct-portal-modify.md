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
ms.openlocfilehash: c3212acd5015edbb639b8904b885c718d654e5b4
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356260"
---
In diesem Abschnitt wird beschrieben, wie Sie die folgenden Änderungsvorgänge für Direct-Peering ausführen.

### <a name="add-direct-peering-connections"></a>Hinzufügen von Direct Peering-Verbindungen
1. Wählen Sie die Schaltfläche **+ Verbindungen hinzufügen** aus, und konfigurieren Sie eine neue Peeringverbindung.
    > [!div class="mx-imgBorder"]
    > ![Die Seite „AshburnPeering – Verbindungen“ listet die Verbindungen auf. Die Schaltfläche „+ Verbindungen hinzufügen“ ist hervorgehoben.](../media/setup-direct-modify-addconnection.png)

1. Füllen Sie das Formular **Direct Peering Connection** (Direct-Peeringverbindung) aus, und wählen Sie **Speichern** aus. Hilfe zum Konfigurieren einer Peeringverbindung finden Sie in den Schritten zum Erstellen und Bereitstellen einer Direct-Peeringverbindung.
    > [!div class="mx-imgBorder"]
    > ![Formular für Direct-Peeringverbindungen](../media/setup-direct-modify-savenewconnection.png)

### <a name="remove-direct-peering-connections"></a>Entfernen von Direct Peering-Verbindungen

Das Entfernen einer Verbindung über das Azure-Portal wird derzeit nicht unterstützt. Wenden Sie sich für weitere Informationen an das [Microsoft-Peering-Team](mailto:peeringexperience@microsoft.com).

### <a name="upgrade-or-downgrade-bandwidth-on-active-connections"></a>Upgrade oder Downgrade der Bandbreite in aktiven Verbindungen
1. Wählen Sie eine Peeringverbindung aus, die Sie ändern möchten, und wählen Sie dann **...**  > **Verbindung bearbeiten** aus.
    > [!div class="mx-imgBorder"]
    > ![Bearbeiten der Verbindung](../media/setup-direct-modify-editconnection.png)

1. Ändern Sie die Bandbreite, indem Sie den Schieberegler verschieben, und wählen Sie dann **Speichern** aus.
    > [!div class="mx-imgBorder"]
    > ![Ändern der Bandbreite](../media/setup-direct-modify-editconnectionsettings.png)

### <a name="add-ipv4-or-ipv6-session-information-on-active-connections"></a>Hinzufügen von IPv4-/IPv6-Sitzungsinformationen in aktiven Verbindungen
1. Wählen Sie eine Peeringverbindung aus, die Sie ändern möchten, und wählen Sie dann **...**  > **Verbindung bearbeiten** wie in Schritt 1 gezeigt aus.
1. Geben Sie Informationen zum **IPv4-Sitzungspräfix** oder zum **IPv6-Sitzungspräfix** ein, und wählen Sie **Speichern** aus.

### <a name="remove-ipv4-or-ipv6-session-information-on-active-connections"></a>Entfernen von IPv4-/IPv6-Sitzungsinformationen aus aktiven Verbindungen
Das Entfernen von Informationen zum **IPv4-Sitzungspräfix** oder zum **IPv6-Sitzungspräfix** über das Portal wird derzeit nicht unterstützt. Wenden Sie sich für weitere Informationen an das [Microsoft-Peering-Team](mailto:peeringexperience@microsoft.com).
