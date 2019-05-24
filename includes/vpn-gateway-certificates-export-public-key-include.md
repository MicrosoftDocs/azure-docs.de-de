---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: afd4836229c60ebef1536d4fa1ca4206a492e56d
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150328"
---
Exportieren Sie nach dem Erstellen eines selbstsignierten Stammzertifikats die CER-Datei für den öffentlichen Schlüssel des Stammzertifikats (nicht den privaten Schlüssel). Sie laden diese Datei später in Azure hoch. Führen Sie die folgenden Schritte aus, um die CER-Datei für Ihr selbstsigniertes Stammzertifikat zu exportieren:

1. Öffnen Sie **Benutzerzertifikate verwalten**, um eine CER-Datei für das Zertifikat zu erhalten. Suchen Sie das selbstsignierte Stammzertifikat (in der Regel in „Zertifikate – Aktueller Benutzer\Eigene Zertifikate\Zertifikate“), und klicken Sie mit der rechten Maustaste darauf. Klicken Sie auf **Alle Aufgaben** und anschließend auf **Exportieren**. Dadurch wird der **Zertifikatexport-Assistent**geöffnet. Wenn Sie das Zertifikat unter „Aktueller Benutzer\Eigene Zertifikate\Zertifikate“ nicht finden, haben Sie unter Umständen versehentlich „Zertifikate – Lokaler Benutzer“ anstelle von „Zertifikate – Aktueller Benutzer“ geöffnet. Wenn Sie den Zertifikat-Manager im Bereich für den aktuellen Benutzer mithilfe von PowerShell öffnen möchten, geben Sie *certmgr* ins Konsolenfenster ein.

   ![Export](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klicken Sie im Assistenten auf **Weiter**.

   ![Exportieren des Zertifikats](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Wählen Sie **Nein, privaten Schlüssel nicht exportieren** aus, und klicken Sie dann auf **Weiter**.

   ![Nein, privaten Schlüssel nicht exportieren](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Wählen Sie auf der Seite **Dateiformat für den Export** die Option **Base-64-codiert X.509 (.CER)** aus, und klicken Sie dann auf **Weiter**.

   ![Base64-codiert](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Wählen Sie unter **Zu exportierende Datei** die Option **Durchsuchen** aus, um zu dem Speicherort zu wechseln, an den das Zertifikat exportiert werden soll. Geben Sie unter **Dateiname**einen Namen für die Zertifikatdatei ein. Klicken Sie auf **Weiter**.

   ![Durchsuchen](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klicken Sie auf **Fertig stellen** , um das Zertifikat zu exportieren.

   ![Finish](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Das Zertifikat wurde erfolgreich exportiert.

   ![Erfolgreich](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Das exportierte Zertifikat sieht in etwa wie folgt aus:

   ![Exportiert](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Wenn Sie das exportierte Zertifikat mit Editor öffnen, ähnelt die Datei diesem Beispiel. Der blaue Abschnitt enthält die Informationen, die in Azure hochgeladen werden. Wenn Sie das Zertifikat mit Editor öffnen und es nicht so ähnlich aussieht, bedeutet dies in der Regel, dass Sie es nicht als Base64-codiertes X.509-Zertifikat (.cer) exportiert haben. Wenn Sie darüber hinaus einen anderen Text-Editor verwenden möchten, sollten Sie beachten, dass einige Editoren unbeabsichtigte Formatierung im Hintergrund einführen können. Dies kann Probleme beim Hochladen des Texts aus diesem Zertifikat in Azure verursachen.

   ![Mit Editor öffnen](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
