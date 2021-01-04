---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 018cf621c65e86877a76c9861c999caf67f3b8cf
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553197"
---
Wenn Sie ein Clientzertifikat generieren, wird es automatisch auf dem Computer installiert, mit dem Sie es generiert haben. Falls Sie das Clientzertifikat auf einem anderen Clientcomputer installieren möchten, müssen Sie das Clientzertifikat exportieren, das Sie generiert haben.

1. Wählen Sie zum Exportieren eines Clientzertifikats die Option **Benutzerzertifikate verwalten** aus. Die Clientzertifikate, die Sie generiert haben, befinden sich standardmäßig in „Certificates - Current User\Personal\Certificates“. Klicken Sie mit der rechten Maustaste auf das Clientzertifikat, das Sie exportieren möchten, und klicken Sie dann auf **Alle Aufgaben** und anschließend auf **Exportieren**, um den **Zertifikatexport-Assistenten** zu öffnen.

   ![Screenshot des Fensters „Zertifikate“ für den aktuellen Benutzer, in dem „Zertifikate“ und unter „Alle Aufgaben“ die Option „Exportieren“ ausgewählt sind](./media/vpn-gateway-certificates-export-client-cert-include/export.png)
2. Klicken Sie im Zertifikatexport-Assistenten auf **Weiter**, um den Vorgang fortzusetzen.

   ![Screenshot der Begrüßungsnachricht im Zertifikatexport-Assistenten](./media/vpn-gateway-certificates-export-client-cert-include/next.png)
3. Wählen Sie **Ja, privaten Schlüssel exportieren** aus, und klicken Sie dann auf **Weiter**.

   ![Privaten Schlüssel exportieren](./media/vpn-gateway-certificates-export-client-cert-include/privatekeyexport.png)
4. Übernehmen Sie auf der Seite **Format der zu exportierenden Datei** die Standardwerte. Stellen Sie sicher, dass die Option **Wenn möglich, alle Zertifikate im Zertifizierungspfad einbeziehen** aktiviert ist. Mit dieser Einstellung werden auch die Stammzertifikatinformationen exportiert, die für eine erfolgreiche Clientauthentifizierung erforderlich sind. Andernfalls tritt bei der Clientauthentifizierung ein Fehler auf, da der Client nicht über das vertrauenswürdige Stammzertifikat verfügt. Klicken Sie auf **Weiter**.

   ![Dateiformat exportieren](./media/vpn-gateway-certificates-export-client-cert-include/includeallcerts.png)
5. Auf der Seite **Sicherheit** müssen Sie den privaten Schlüssel schützen. Wenn Sie ein Kennwort verwenden möchten, müssen Sie sich das für dieses Zertifikat festgelegte Kennwort unbedingt merken oder notieren. Klicken Sie auf **Weiter**.

   ![Screenshot der Seite „Sicherheit“ des Zertifikatexport-Assistenten, auf der das Kennwort eingegeben und bestätigt wurde und „Weiter“ hervorgehoben ist](./media/vpn-gateway-certificates-export-client-cert-include/security.png)
6. Wählen Sie unter **Zu exportierende Datei** die Option **Durchsuchen** aus, um zu dem Speicherort zu wechseln, an den das Zertifikat exportiert werden soll. Geben Sie unter **Dateiname** einen Namen für die Zertifikatdatei ein. Klicken Sie auf **Weiter**.

   ![Zu exportierende Datei](./media/vpn-gateway-certificates-export-client-cert-include/filetoexport.png)
7. Klicken Sie auf **Fertig stellen** , um das Zertifikat zu exportieren.

   ![Screenshot des Zertifikatexport-Assistenten mit den eingegebenen Einstellungen](./media/vpn-gateway-certificates-export-client-cert-include/finish.png)