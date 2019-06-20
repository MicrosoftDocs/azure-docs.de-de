---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: 05673885336dbfb9a70843bd0ccc4e700091ad7e
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178760"
---
|     Fehlercode     |      Fehlerbeschreibung     |
|--------------------|--------------------------|
|    100             | Der Name des Containers oder der Freigabe muss zwischen 3 und 63 Zeichen umfassen.|
|    101             | Der Name des Containers oder der Freigabe darf nur Buchstaben, Zahlen oder Bindestriche enthalten.|
|    102             | Der Name des Containers oder der Freigabe darf nur Buchstaben, Zahlen oder Bindestriche enthalten.|
|    103             | Der Blob- oder Dateiname enthält nicht unterstützte Steuerzeichen.|
|    104             | Der Blob- oder Dateiname enthält unzulässige Zeichen.|
|    105             | Der Blob- oder Dateiname enthält zu viele Segmente. Segmente werden jeweils durch einen Schrägstrich (/) getrennt.|
|    106             | Der Blob- oder Dateiname ist zu lang.|
|    107             | Eines der Segmente des Blob- oder Dateinamens ist zu lang. |
|    108             | Die Datei ist zu groß zum Hochladen.    |
|    109             | Der Blob- oder Dateiname ist nicht ordnungsgemäß ausgerichtet.  |
|    110             | Der Blob- oder Dateiname im Unicodeformat ist ungültig.|
|    111             | Der Name oder das Präfix der Datei oder des Blobs ist ein nicht unterstützter reservierter Name (beispielsweise COM1).|
|    2000            | Ein ETag-Konflikt deutet auf einen Konflikt zwischen einem Blockblob in der Cloud und auf dem Gerät hin. Löschen Sie zur Behebung dieses Konflikts eine dieser Dateien (entweder die Version in der Cloud oder die Version auf dem Gerät).    |
|    2001            | Bei der Verarbeitung einer hochgeladenen Datei ist ein unerwartetes Problem aufgetreten.    Sollte dieser Fehler auftreten und länger als 24 Stunden bestehen bleiben, wenden Sie sich an den Support. |
|    2002            | Die Datei ist bereits in einem anderen Prozess geöffnet und kann erst hochgeladen werden, wenn das Handle geschlossen wurde.|
|    2003            | Die hochzuladende Datei konnte nicht geöffnet werden. Sollte dieser Fehler auftreten, wenden Sie sich an den Microsoft-Support.|
|    2004            | Mit dem Zielcontainer für die hochzuladenden Daten konnte keine Verbindung hergestellt werden.|
|    2005            | Mit dem Container konnte keine Verbindung hergestellt werden, da die Kontoberechtigungen entweder nicht korrekt oder nicht mehr aktuell sind. Überprüfen Sie Ihren Zugriff.|
|    2006            | In das Konto konnten keine Daten hochgeladen werden, da das Konto oder die Freigabe deaktiviert ist.|
|    2007            | Mit dem Container konnte keine Verbindung hergestellt werden, da die Kontoberechtigungen entweder nicht korrekt oder nicht mehr aktuell sind. Überprüfen Sie Ihren Zugriff.|
|    2008            | Es konnten keine neuen Daten hinzugefügt werden, da der Container voll ist. Überprüfen Sie die Azure-Spezifikationen für unterstützte Containergrößen unter Berücksichtigung des Typs. Azure Files unterstützt beispielsweise nur eine maximale Dateigröße von 5 TB.|
|    2009            | Daten konnten nicht hochgeladen werden, da der Container, welcher der Freigabe zugeordnet ist, nicht vorhanden ist.|    
|    2997            | Ein unerwarteter Fehler ist aufgetreten. Dies ist ein vorübergehender Fehler, der sich von selbst behebt.|
|    2998            | Ein unerwarteter Fehler ist aufgetreten. Der Fehler behebt sich unter Umständen von selbst. Sollte er jedoch länger als 24 Stunden bestehen bleiben, wenden Sie sich an den Microsoft-Support.|
|    16000           | Diese Datei konnte nicht abgerufen werden.|
|    16001           | Diese Datei konnte nicht abgerufen werden, da sie auf Ihrem lokalen System bereits vorhanden ist.|
|    16002           |Diese Datei konnte nicht aktualisiert werden, da sie nicht vollständig hochgeladen wurde.|

