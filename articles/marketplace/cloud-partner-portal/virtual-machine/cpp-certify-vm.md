---
title: Zertifizieren Ihres VM-Images für den Azure Marketplace
description: Dieser Artikel erläutert, wie Sie ein VM-Image für die Azure Marketplace-Zertifizierung testen und übermitteln.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/26/2018
ms.author: pabutler
ms.openlocfilehash: 0dbf1abbb91f9e5c3bd2d042c57f87591d52c9cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938497"
---
# <a name="certify-your-vm-image"></a>Zertifizieren Ihres VM-Images

Nachdem Sie Ihren virtuellen Computer (VM) erstellt und bereitgestellt haben, müssen Sie das VM-Image für die Azure Marketplace-Zertifizierung testen und übermitteln. In diesem Artikel wird erläutert, wo Sie das *Certification Test Tool for Azure Certified* herunterladen können, wie Sie dieses Tool verwenden, um Ihr VM-Image zu zertifizieren und wie Sie die Ergebnisse der Überprüfung in den Azure-Container hochladen, in dem sich Ihre virtuellen Festplatten befinden. 


## <a name="download-and-run-the-certification-test-tool"></a>Herunterladen und Ausführen des Tools zum Testen der Zertifizierung

Das Certification Test Tool for Azure Certified wird auf einem lokalen Windows-Computer ausgeführt, testet aber eine Azure-basierte Windows- oder Linux-VM.  Das Tool überprüft, ob Ihr VM-Benutzerimage mit Microsoft Azure kompatibel ist und ob während der Vorbereitung der virtuellen Festplatte die Anforderungen erfüllt und die Anleitungen befolgt wurden. Die Ausgabe des Tools ist ein Kompatibilitätsbericht, den Sie in das [Cloud-Partnerportal](https://cloudpartner.azure.com) hochladen, um die Zertifizierung Ihrer VM anzufordern.

1. Laden Sie das neueste [Certification Test Tool for Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299) herunter, und installieren Sie es. 
2. Öffnen Sie das Zertifizierungstool, und klicken Sie dann auf **Neuen Test starten**.
3. Geben Sie auf dem Bildschirm **Testinformationen** einen **Testnamen** für den Testlauf ein.
4. Wählen Sie die **Plattform** für Ihre VM aus: `Windows Server` oder `Linux`. Die Auswahl der Plattform wirkt sich auf die restlichen Optionen aus.
5. Wenn Ihre VM diesen Datenbankdienst verwendet, aktivieren Sie das Kontrollkästchen **Für Azure SQL-Datenbank testen**.

   ![Zertifizierungstesttool – erste Seite](./media/publishvm_025.png)


## <a name="connect-the-certification-tool-to-a-vm-image"></a>Verbinden des Zertifizierungstools mit einem VM-Image

  Das Tool verbindet Windows-basierte VMs mit [PowerShell](https://docs.microsoft.com/powershell/) und Linux-VMs mit [SSH.Net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Verbinden des Zertifizierungstools mit einem Linux-VM-Image

1. Wählen Sie den Modus für die **SSH-Authentifizierung** aus: `Password Authentication` oder `key File Authentication`.
2. Wenn Sie die Authentifizierung per Kennwort verwenden, geben Sie Werte für den **VM-DNS-Namen**, den **Benutzernamen** und das **Kennwort** ein.  Optional können Sie die standardmäßige Nummer für den **SSH-Port** ändern.

     ![Kennwortauthentifizierung eines Linux-VM-Images](./media/publishvm_026.png)

3. Wenn Sie die Authentifizierung per Schlüsseldatei verwenden, geben Sie Werte für den **VM-DNS-Namen**, den **Benutzernamen** und den Speicherort des **privaten Schlüssels** ein.  Optional können Sie eine **Passphrase** angeben oder die standardmäßige Nummer für den **SSH-Port** ändern.

     ![Dateiauthentifizierung eines Linux-VM-Images](./media/publishvm_027.png)

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Verbinden des Zertifizierungstools mit einem Windows-basierten VM-Image**
1. Geben Sie den vollqualifizierten **VM-DNS-Namen** ein (z.B. `MyVMName.Cloudapp.net`).
2. Geben Sie Werte für den **Benutzernamen** und das **Kennwort** ein.

   ![Kennwortauthentifizierung eines Windows-VM-Images](./media/publishvm_028.png)


## <a name="run-a-certification-test"></a>Ausführen eines Zertifizierungstests

Nachdem Sie die Parameterwerte für Ihr VM-Image im Zertifizierungstool eingegeben haben, klicken Sie auf **Verbindung testen**, um eine gültige Verbindung mit Ihrer VM sicherzustellen. Klicken Sie nach dem Überprüfen der Verbindung auf **Weiter**, um den Test zu starten.  Wenn der Test abgeschlossen ist, wird eine Tabelle mit den Testergebnissen angezeigt (Erfolgreich/Fehler/Warnung).  Das folgende Beispiel zeigt die Testergebnisse für den Test einer Linux-VM. 

![Zertifizierungstestergebnisse für Linux-VM-Image](./media/publishvm_029.png)

Wenn einer der Tests nicht erfolgreich durchgeführt wird, wird das Image *nicht* zertifiziert. Überprüfen Sie in diesem Fall die Anforderungen und Fehlermeldungen, nehmen Sie die angegebenen Änderungen vor, und führen Sie den Test erneut aus. 

Nach dem automatisierten Test werden Sie im Bildschirm **Fragebogen** zur Eingabe zusätzlicher Informationen zum VM-Image aufgefordert.  Der Bildschirm enthält zwei Registerkarten, die Sie ausfüllen müssen.  Die Registerkarte **Allgemeine Bewertung** enthält Fragen, die Sie mit **richtig oder falsch** beantworten können, die Registerkarte **Kernelanpassung** enthält Fragen mit mehreren Antwortmöglichkeiten oder mit frei formulierbaren Antworten.  Beantworten Sie die Fragen auf beiden Registerkarten, und klicken Sie dann auf **Weiter**.

![Fragebogen im Zertifizierungstool](./media/publishvm_030.png)

Auf dem letzten Bildschirm können Sie zusätzliche Informationen angeben, z.B. SSH-Zugriffsinformationen für das Linux-VM-Image und Erläuterungen für nicht erfolgreiche Bewertungen, wenn Sie Ausnahmen angeben möchten. 

Klicken Sie zum Schluss auf **Bericht generieren**, um zusätzlich zu Ihren Antworten des Fragebogens auch die Testergebnisse und Protokolldateien für die ausgeführten Testfälle herunterzuladen. Speichern Sie die Ergebnisse in dem Container, in dem sich auch Ihre virtuellen Festplatten befinden.

![Zertifizierungstestergebnisse speichern](./media/publishvm_031.png)


## <a name="next-steps"></a>Nächste Schritte

Als nächstes müssen Sie [einen URI für jede virtuelle Festplatte generieren](./cpp-get-sas-uri.md), die Sie an den Marketplace übermitteln möchten. 
