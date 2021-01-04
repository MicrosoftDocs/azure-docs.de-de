---
title: Verwenden des Portals zum Aktivieren von Remotedesktop für eine Rolle
titleSuffix: Azure Cloud Services
description: Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen.
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 507af87c3126be00a802bcbc5170f8ad364c06fc
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099316"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

Sie können eine Remotedesktopverbindung in Ihrer Rolle während der Entwicklung aktivieren, indem Sie die Remotedesktopmodule in ihre Dienstdefinition aufnehmen, oder Sie können Remotedesktop über die Remotedesktoperweiterung aktivieren. Der bevorzugte Ansatz ist die Verwendung der Remotedesktoperweiterung, da sie Remotedesktop damit auch nach der Bereitstellung der Anwendung aktivieren können, ohne die Anwendung erneut bereitzustellen.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurieren von Remotedesktop über das Azure-Portal

Das Azure-Portal ermöglicht die Remotedesktoperweiterung, sodass Sie Remotedesktop auch nach der Bereitstellung der Anwendung aktivieren können. In den Einstellungen unter **Remotedesktop** für Ihren Clouddienst können Sie Remotedesktop aktivieren, das lokale Administratorkonto, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, und das bei der Authentifizierung verwendete Zertifikat ändern und das Ablaufdatum festlegen.

1. Wählen Sie **Cloud Services** , den Namen des Clouddiensts und dann die Option **Remotedesktop**.

    ![Screenshot, auf dem die Option „Remotedesktop“ hervorgehoben ist](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Legen Sie fest, ob Sie Remotedesktop für eine einzelne Rolle oder für alle Rollen aktivieren möchten, und ändern Sie dann den Wert des Umschalters in **Aktiviert**.

3. Füllen Sie die erforderlichen Felder für Benutzername, Kennwort, Ablauf und Zertifikat aus.

    ![Clouddienste – Remotedesktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alle Rolleninstanzen werden neu gestartet, wenn Sie Remotedesktop erstmals aktivieren und **OK** (Häkchen) wählen. Um einen Neustart zu verhindern, muss in der Rolle das Zertifikat installiert sein, mit dem das Kennwort verschlüsselt wird. Zum Verhindern eines Neustarts [laden Sie ein Zertifikat für den Clouddienst hoch](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) und kehren dann zu diesem Dialogfeld zurück.

4. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

5. Wählen Sie **Speichern** , nachdem Sie Ihre Konfigurationsupdates beendet haben. Es dauert einige Minuten, bis die Rolleninstanzen Verbindungen empfangen können.

## <a name="remote-into-role-instances"></a>Remotezugriff auf Rolleninstanzen

Nachdem Remotedesktop für die Rollen aktiviert wurde, können Sie direkt im Azure-Portal eine Verbindung initiieren:

1. Klicken Sie auf **Instanzen** , um die Einstellungen zu **Instanzen** zu öffnen.
2. Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.
3. Klicken Sie auf **Verbinden** , um eine RDP-Datei für die Rolleninstanz herunterzuladen.

    ![Screenshot mit hervorgehobener Schaltfläche „Verbinden“](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicken Sie auf **Öffnen** und dann auf **Verbinden** , um die Remotedesktopverbindung zu starten.

>[!NOTE]
> Wenn Ihr Clouddienst durch eine NSG geschützt ist, müssen Sie u.U. Regeln erstellen, die den Datenverkehr an den Ports **3389** und **20000** zulassen.  Remotedesktop verwendet Port **3389**.  Für Cloud Services-Instanzen erfolgt ein Lastenausgleich, daher können Sie nicht direkt steuern, mit welcher Instanz eine Verbindung hergestellt wird.  Die Agents *RemoteForwarder* und *RemoteAccess* verwalten den RDP-Datenverkehr und ermöglichen es dem Client, ein RDP-Cookie zu senden und eine bestimmte Instanz anzugeben, mit der eine Verbindung hergestellt werden soll.  Für die Agents *RemoteForwarder* und *RemoteAccess* ist es erforderlich, dass Port **20000** \* offen ist. Dieser ist bei Vorhandensein einer NSG unter Umständen blockiert.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure-portal.md)
