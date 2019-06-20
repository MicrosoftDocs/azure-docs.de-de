---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 52084b065ef65a69a6691b6646d1e199f011910d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177926"
---
### <a name="gwipnoconnection"></a> So ändern Sie die IP-Adresse des lokalen Netzwerkgateways (ohne Gatewayverbindung)

Ändern Sie das Gateway eines lokalen Netzwerks, für das keine Gatewayverbindung besteht, anhand des Beispiels. Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern.

1. Klicken Sie für die Ressource für das lokale Netzwerkgateway unter **Einstellungen** auf **Konfiguration**.
2. Ändern Sie im Feld **IP-Adresse** die IP-Adresse.
3. Klicken Sie auf **Speichern** , um die Einstellungen zu speichern.

### <a name="gwipwithconnection"></a>So ändern Sie die IP-Adresse des lokalen Netzwerkgateways (vorhandene Gatewayverbindung)

Um ein lokales Netzwerkgateway mit einer Verbindung zu ändern, müssen Sie zuerst die Verbindung entfernen. Nachdem die Verbindung entfernt wurde, können Sie die Gateway-IP-Adresse ändern und eine neue Verbindung erstellen. Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern. Dies führt zu Ausfallzeiten bei Ihrer VPN-Verbindung. Beim Ändern der Gateway-IP-Adresse müssen Sie das VPN-Gateway nicht löschen. Sie müssen nur die Verbindung entfernen.
 
#### <a name="1-remove-the-connection"></a>1. Entfernen Sie die Verbindung.

1. Klicken Sie für die Ressource für das lokale Netzwerkgateway im Abschnitt **Einstellungen** auf **Verbindungen**.
2. Klicken Sie in der Zeile für die Verbindung auf **...** und dann auf **Löschen**.
3. Klicken Sie auf **Save**, um Ihre Einstellungen zu speichern.

#### <a name="2-modify-the-ip-address"></a>2. Ändern Sie die IP-Adresse.

Bei dieser Gelegenheit können Sie auch die Adresspräfixe ändern.

1. Ändern Sie im Feld **IP-Adresse** die IP-Adresse.
2. Klicken Sie auf **Speichern** , um die Einstellungen zu speichern.

#### <a name="3-recreate-the-connection"></a>3. Erstellen Sie die Verbindung erneut.

1. Navigieren Sie zum Gateway für virtuelle Netzwerke für Ihr VNet. (Dies ist nicht das lokale Netzwerkgateway.)
2. Klicken Sie für das lokale Netzwerkgateway im Abschnitt **Einstellungen** auf **Verbindungen**.
3. Klicken Sie auf **+ Hinzufügen**, um das Blatt **Verbindung hinzufügen** zu öffnen.
4. Erstellen Sie die Verbindung erneut.
5. Klicken Sie auf **OK**, um die Verbindung zu erstellen.