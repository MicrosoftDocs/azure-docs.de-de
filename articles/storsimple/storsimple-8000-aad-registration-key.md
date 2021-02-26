---
title: Azure AD-Authentifizierung für StorSimple 8000 in Geräte-Manager
description: Hier erfahren Sie, wie Sie die AAD-basierte Authentifizierung für Ihren Dienst verwenden, einen neuen Registrierungsschlüssel generieren und eine manuelle Registrierung der Geräte durchführen.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b09d68e7859a787c05a2fc62294f081c4345ae08
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805039"
---
# <a name="use-azure-active-directory-ad-authentication-for-your-storsimple"></a>Verwenden der Azure Active Directory-Authentifizierung (Azure AD) für StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Übersicht

Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er verfügt über Verbindungen mit mehreren StorSimple-Geräten. Bislang hat der StorSimple-Geräte-Manager-Dienst einen Zugriffssteuerungsdienst (Access Control Service, ACS) verwendet, um den Dienst bei Ihrem StorSimple-Gerät zu authentifizieren. Der ACS-Mechanismus wird in Kürze eingestellt und durch eine AAD-Authentifizierung (Azure Active Directory) ersetzt. Weitere Informationen finden Sie in den folgenden Ankündigungen zur Einstellung von ACS und zur Verwendung der AAD-Authentifizierung.

- [The future of Azure ACS is Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/) (Die Zukunft von Azure ACS ist Azure Active Directory)
- [Upcoming changes to the Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/) (Anstehende Änderungen beim Microsoft Access Control Service)

Dieser Artikel behandelt die Details der AAD-Authentifizierung und den zugeordneten neuen Dienstregistrierungsschlüssel sowie Änderungen an den Firewallregeln im Zusammenhang mit den StorSimple-Geräten. Die Informationen in diesem Artikel gelten nur für Geräte der StorSimple 8000-Serie.

Die AAD-Authentifizierung erfolgt für StorSimple 8000-Geräte mit Update 5 oder einer höheren Version. Die Einführung der AAD-Authentifizierung hat Änderungen in folgenden Bereichen zur Folge:

- URL-Muster für Firewallregeln
- Dienstregistrierungsschlüssel

Diese Änderungen werden in den folgenden Abschnitten ausführlich erläutert.

## <a name="url-changes-for-aad-authentication"></a>URL-Änderungen für AAD-Authentifizierung

Um zu gewährleisten, dass der Dienst die AAD-basierte Authentifizierung verwendet, müssen alle Benutzer die neuen Authentifizierungs-URLs in ihre Firewallregeln einschließen.

Stellen Sie bei Verwendung von StorSimple 8000-Geräten sicher, dass die folgende URL in den Firewallregeln enthalten ist:

| URL-Muster                         | Cloud | Komponente/Funktionalität         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure – Öffentlich |AAD-Authentifizierungsdienst      |
| `https://login.microsoftonline.us` | US Government |AAD-Authentifizierungsdienst      |

Eine vollständige Liste mit URL-Mustern für StorSimple 8000-Geräte finden Sie unter [URL-Muster für Firewallregeln](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Wenn die Authentifizierungs-URL nach dem Datum der Einstellung nicht in den Firewallregeln enthalten ist und auf dem Gerät Update 5 ausgeführt wird, wird den Benutzern eine URL-Warnung angezeigt. Die Benutzer müssen die neue Authentifizierungs-URL in die Firewallregeln aufnehmen. Wird auf dem Gerät eine Version vor Update 5 ausgeführt, wird den Benutzern eine Heartbeatwarnung angezeigt. In beiden Fällen kann das StorSimple-Gerät nicht beim Dienst authentifiziert werden, und der Dienst kann nicht mit dem Gerät kommunizieren.

## <a name="device-version-and-authentication-changes"></a>Geräteversion und Authentifizierungsänderungen

Ermitteln Sie bei Verwendung eines StorSimple 8000-Geräts anhand der folgenden Tabelle, welche Aktion Sie abhängig von der verwendeten Gerätesoftwareversion ausführen müssen.

| Geräteversion| Auszuführende Aktion                                    |
|--------------------------|------------------------|
| Das Gerät verfügt mindestens über Update 5 und ist offline. <br> Sie erhalten eine Warnung mit dem Hinweis, dass die URL nicht genehmigt ist.|1. Ändern Sie die Firewallregeln, um die Authentifizierungs-URL einzuschließen. Siehe [Authentifizierungs-URLs](#url-changes-for-aad-authentication).<br>2. [Rufen Sie den AAD-Registrierungsschlüssel aus dem Dienst ab](#aad-based-registration-keys).<br>3. [Stellen Sie eine Verbindung mit der Windows PowerShell-Schnittstelle des StorSimple 8000-Geräts her](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Verwenden Sie das Cmdlet `Redo-DeviceRegistration`, um das Gerät über Windows PowerShell zu registrieren. Geben Sie den Schlüssel an, den Sie im vorherigen Schritt abgerufen haben.|
| Das Gerät verfügt mindestens über Update 5 und ist online.| Keine Aktion erforderlich.                                       |
| Das Gerät verfügt über Update 4 oder eine niedrigere Version und ist offline. |1. Ändern Sie die Firewallregeln, um die Authentifizierungs-URL einzuschließen.<br>2. [Laden Sie Update 5 über den Katalogserver herunter](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Wenden Sie Update 5 über die Hotfixmethode an](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Rufen Sie den AAD-Registrierungsschlüssel aus dem Dienst ab](#aad-based-registration-keys).<br>5. [Stellen Sie eine Verbindung mit der Windows PowerShell-Schnittstelle des StorSimple 8000-Geräts her](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Verwenden Sie das Cmdlet `Redo-DeviceRegistration`, um das Gerät über Windows PowerShell zu registrieren. Geben Sie den Schlüssel an, den Sie im vorherigen Schritt abgerufen haben.|
| Das Gerät verfügt über Update 4 oder eine niedrigere Version und ist online. |Ändern Sie die Firewallregeln, um die Authentifizierungs-URL einzuschließen.<br> Installieren Sie Update 5 über das Azure-Portal.              |
| Setzen Sie das Gerät auf eine Version vor Update 5 zurück.      |Das Portal zeigt einen AAD-basierten Registrierungsschlüssel an, das Gerät führt jedoch ältere Software aus. Führen Sie die Schritte aus dem vorherigen Szenario aus, wenn auf dem Gerät Update 4 oder eine niedrigere Version ausgeführt wird.              |

## <a name="aad-based-registration-keys"></a>AAD-basierte Registrierungsschlüssel

Ab Update 5 für StorSimple 8000-Geräte werden neue AAD-basierte Registrierungsschlüssel verwendet. Die Registrierungsschlüssel dienen dazu, Ihren StorSimple-Geräte-Manager-Dienst bei dem Gerät zu registrieren.

Sie können die neuen AAD-Dienstregistrierungsschlüssel nicht verwenden, wenn Sie ein StorSimple 8000-Gerät mit Update 4 oder einer niedrigeren Version verwenden (dies schließt ältere Geräte ein, die jetzt aktiviert werden).
In diesem Szenario muss der Dienstregistrierungsschlüssel neu generiert werden. Nachdem Sie den Schlüssel neu generiert haben, wird der neue Schlüssel für die Registrierung aller weiteren Geräte verwendet. Der alte Schlüssel ist dann nicht mehr gültig.

- Der neue AAD-Registrierungsschlüssel läuft nach drei Tagen ab.
- Die AAD-Registrierungsschlüssel können nur für StorSimple 8000-Geräte verwendet werden, die mindestens über Update 5 verfügen.
- Die AAD-Registrierungsschlüssel sind länger als die entsprechenden ACS-Registrierungsschlüssel.

Führen Sie die folgenden Schritte aus, um einen AAD-Dienstregistrierungsschlüssel zu generieren.

#### <a name="to-generate-the-aad-service-registration-key"></a>So generieren Sie den AAD-Dienstregistrierungsschlüssel

1. Navigieren Sie im **StorSimple-Geräte-Manager** zu **Verwaltung &gt;** **Schlüssel**. Sie können auch mithilfe der Suchleiste nach _Schlüsseln_ suchen.
    
2. Klicken Sie auf **Schlüssel generieren**.

    ![Klicken Sie auf „Neu generieren“.](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieren Sie den neuen Schlüssel. Der ältere Schlüssel funktioniert nicht mehr.

    ![Bestätigen des erneuten Generierens](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Wenn Sie eine StorSimple Cloud Appliance im Dienst erstellen, bei dem Ihr StorSimple 8000-Gerät registriert ist, generieren Sie den Registrierungsschlüssel nicht während des Erstellungsvorgangs. Warten Sie, bis der Erstellungsvorgang abgeschlossen wurde, und generieren Sie dann den Registrierungsschlüssel.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [StorSimple 8000-Geräte](storsimple-8000-deployment-walkthrough-u2.md) bereitstellen.
