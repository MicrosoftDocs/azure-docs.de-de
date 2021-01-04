---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 5/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 09723030f0da0252120f66f36347cad2e3a3546a
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375826"
---
Navigieren Sie zu dem Speicherkonto, für das Sie einen privaten Endpunkt erstellen möchten. Wählen Sie im Inhaltsverzeichnis für das Speicherkonto die Option **Private Endpunktverbindungen** und dann **+ Privater Endpunkt** aus, um einen neuen privaten Endpunkt zu erstellen. 

[![Screenshot des Eintrags „Private Endpunktverbindungen“ im Inhaltsverzeichnis des Speicherkontos](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png)](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-0.png#lightbox)

Im anschließend angezeigten Assistenten müssen mehrere Seiten ausgefüllt werden.

Wählen Sie auf dem Blatt **Grundlagen** die gewünschte Ressourcengruppe, den Namen und die Region für Ihren privaten Endpunkt aus. Diese können beliebig sein und müssen nicht mit dem Speicherkonto übereinstimmen. Allerdings müssen Sie den privaten Endpunkt in derselben Region erstellen wie das virtuelle Netzwerk, in dem Sie den privaten Endpunkt erstellen möchten.

![Screenshot des Abschnitts „Grundlagen“ im Abschnitt zum Erstellen eines privaten Endpunkts](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-1.png)

Wählen Sie auf dem Blatt **Ressourcen** das Optionsfeld für **Verbindung mit einer Azure-Ressource im eigenen Verzeichnis herstellen** aus. Wählen Sie als **Ressourcentyp** **Microsoft.Storage/storageAccounts** aus. Das Feld **Ressourcen** ist das Speicherkonto mit der Azure-Dateifreigabe, mit der Sie eine Verbindung herstellen möchten. Die untergeordnete Zielressource ist **file**, da es sich hier um Azure Files handelt.

Auf dem Blatt **Konfiguration** können Sie das spezifische virtuelle Netzwerk und das Subnetz auswählen, dem Sie Ihren privaten Endpunkt hinzufügen möchten. Sie müssen ein anderes Subnetz als das Subnetz auswählen, zu dem Sie oben Ihren Dienstendpunkt hinzugefügt haben. Das Blatt „Konfiguration“ enthält auch die Informationen zum Erstellen bzw. Aktualisieren der privaten DNS-Zone. Wir empfehlen Ihnen die Verwendung der Standardzone `privatelink.file.core.windows.net`.

![Screenshot: Abschnitt „Konfiguration“](media/storage-files-networking-endpoints-private-portal/create-private-endpoint-2.png)

Klicken Sie auf **Überprüfen + Erstellen**, um den privaten Endpunkt zu erstellen. 
