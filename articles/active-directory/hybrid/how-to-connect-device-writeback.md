---
title: 'Azure AD Connect: Aktivieren des Geräterückschreibens | Microsoft Docs'
description: Dieses Dokument erläutert das Aktivieren des Geräterückschreibens mit Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: femila
editor: curtand
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/08/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d526394ac89e2d29b2002004736e8480bb15b954
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973421"
---
# <a name="azure-ad-connect-enabling-device-writeback"></a>Azure AD Connect: Aktivieren des Geräterückschreibens
> [!NOTE]
> Für das Geräterückschreiben ist ein Azure AD Premium-Abonnement erforderlich.
> 
> 

Die folgende Dokumentation enthält Informationen zum Aktivieren des Features "Geräterückschreiben" in Azure AD Connect. Das Geräterückschreiben wird in den folgenden Szenarien verwendet:

* Aktivieren von [Windows Hello for Business mithilfe der Hybridbereitstellung von Zertifikatvertrauensstellungen](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust-prereqs#device-registration)
* Aktivieren des bedingten Zugriffs basierend auf Geräten auf von ADFS (2012 R2 oder höher) geschützte Anwendungen (Vertrauensstellungen für vertrauende Seiten).

Dies bietet zusätzliche Sicherheit und die Gewissheit, dass nur vertrauenswürdige Geräte auf die Anwendung zugreifen können. Weitere Informationen zum bedingten Zugriff finden Sie unter [Verwalten von Risiken mit bedingtem Zugriff](../conditional-access/overview.md) und [Einrichten des lokalen bedingten Zugriffs mithilfe von Azure Active Directory Device Registration](../devices/overview.md).

> [!IMPORTANT]
> <li>Geräte müssen sich in der gleichen Gesamtstruktur befinden wie die Benutzer. Da Geräte in eine einzelne Gesamtstruktur zurückgeschrieben werden müssen, unterstützt diese Funktion derzeit keine Bereitstellung mit mehreren Gesamtstrukturen für Benutzer.</li>
> <li>In der lokalen Active Directory-Gesamtstruktur kann nur ein Konfigurationsobjekt für die Geräteregistrierung hinzugefügt werden. Diese Funktion ist nicht mit einer Topologie kompatibel, in der das lokale Active Directory mit mehreren Azure AD-Verzeichnissen synchronisiert wird.</li>

## <a name="part-1-install-azure-ad-connect"></a>Teil 1: Installieren von Azure AD Connect
Installieren Sie Azure AD Connect mit benutzerdefinierten Einstellungen oder Expresseinstellungen. Microsoft empfiehlt, zunächst alle Benutzer und Gruppen erfolgreich zu synchronisieren, bevor Sie das Geräterückschreiben aktivieren.

## <a name="part-2-enable-device-writeback-in-azure-ad-connect"></a>Teil 2: Aktivieren des Geräterückschreibens in Azure AD Connect
1. Führen Sie den Installations-Assistenten erneut aus. Wählen Sie auf der Seite „Weitere Aufgaben“ die Option **Geräteoptionen konfigurieren**, und klicken Sie auf **Weiter**. 

    ![Geräteoptionen konfigurieren](./media/how-to-connect-device-writeback/deviceoptions.png)

    >[!NOTE]
    > Die neuen Optionen zum Konfigurieren von Geräten sind nur ab Version 1.1.819.0 verfügbar.

2. Wählen Sie auf der Seite mit den Geräteoptionen die Option **Geräterückschreiben konfigurieren**. Die Option **Geräterückschreiben deaktivieren** ist erst verfügbar, nachdem das Geräterückschreiben aktiviert wurde. Klicken Sie auf **Weiter**, um die nächste Seite des Assistenten anzuzeigen.
    ![Auswählen des Gerätevorgangs](./media/how-to-connect-device-writeback/configuredevicewriteback1.png)

3. Auf der Seite "Rückschreiben" sehen Sie die angegebene Domäne als die standardmäßige Gesamtstruktur für das Geräterückschreiben.
   ![Benutzerdefinierte Installation – Zielgesamtstruktur für Geräterückschreiben](./media/how-to-connect-device-writeback/writebackforest.png)

4. Die Seite **Gerätecontainer** enthält eine Option zum Vorbereiten von Active Directory, bei der Sie zwei Möglichkeiten haben:

    a. **Anmeldeinformationen eines Unternehmensadministrators angeben**: Wenn die Anmeldeinformationen eines Unternehmensadministrators für die Gesamtstruktur angegeben werden, für die Geräte zurückgeschrieben werden müssen, bereitet Azure AD Connect die Gesamtstruktur während der Konfiguration des Geräterückschreibens automatisch vor.

    b. **PowerShell-Skript herunterladen**: Azure AD Connect generiert automatisch ein PowerShell-Skript, mit dem Active Directory für das Geräterückschreiben vorbereitet werden kann. Falls die Anmeldeinformationen eines Unternehmensadministrators in Azure AD Connect nicht angegeben werden können, wird vorgeschlagen, das PowerShell-Skript herunterzuladen. Stellen Sie das heruntergeladene PowerShell-Skript **CreateDeviceContainer.ps1** für den Unternehmensadministrator der Gesamtstruktur bereit, in die das Rückschreiben der Geräte erfolgt.
    ![Vorbereiten der Active Directory-Gesamtstruktur](./media/how-to-connect-device-writeback/devicecontainercreds.png)
    
    Zur Vorbereitung der Active Directory-Gesamtstruktur werden die folgenden Vorgänge durchgeführt:
    * Falls sie noch nicht vorhanden sind, werden die neuen Container und Objekte unter „CN=Device Registration Configuration,CN=Services,CN=Configuration,[forest-dn]“ erstellt und konfiguriert.
    * Falls sie noch nicht vorhanden sind, werden die neuen Container und Objekte unter „CN=RegisteredDevices,[domain-dn]“ erstellt und konfiguriert. Geräteobjekte werden in diesem Container erstellt.
    * Legt die erforderlichen Berechtigungen für das Azure AD Connector-Konto fest, um Geräte in Active Directory zu verwalten.
    * Muss nur in einer Gesamtstruktur ausgeführt werden, auch wenn Azure AD Connect in mehreren Gesamtstrukturen installiert ist.

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Überprüfen, ob die Geräte mit Active Directory synchronisiert werden
Das Geräterückschreiben sollte jetzt ordnungsgemäß ausgeführt werden. Bedenken Sie, dass es bis zu drei Stunden dauern kann, bis Geräteobjekte in Active Directory zurückgeschrieben werden.  Um sicherzustellen, dass Ihre Geräte ordnungsgemäß synchronisiert werden, gehen Sie nach Abschluss der Synchronisierungsregeln wie folgt vor:

1. Starten Sie das Active Directory-Verwaltungscenter.
2. Erweitern Sie "RegisteredDevices" innerhalb der Verbunddomäne.

   ![Active Directory-Verwaltungscenter – registrierte Geräte](./media/how-to-connect-device-writeback/devicewriteback5.png)

3. Die gegenwärtig registrierten Geräte werden hier aufgeführt.

   ![Active Directory-Verwaltungscenter – Liste der registrierten Geräte](./media/how-to-connect-device-writeback/devicewriteback6.png)

## <a name="enable-conditional-access"></a>Aktivieren des bedingten Zugriffs
Ausführliche Informationen zum Aktivieren dieses Szenarios finden Sie unter [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](../devices/overview.md).

## <a name="troubleshooting"></a>Problembehandlung
### <a name="the-writeback-checkbox-is-still-disabled"></a>Kontrollkästchen für das Rückschreiben ist weiterhin deaktiviert
Wenn das Kontrollkästchen für das Geräterückschreiben nicht aktiviert ist, obwohl Sie die oben beschriebenen Schritte befolgt haben, können Sie mit den folgenden Schritten nachvollziehen, was der Installations-Assistent überprüft, bevor das Kontrollkästchen aktiviert wird.

Zuerst die wichtigen Dinge:

* Das Schema der Gesamtstruktur, in der die Geräte vorhanden sind, muss auf Windows 2012 R2 aktualisiert werden, damit das Geräteobjekt und die zugehörigen Attribute vorhanden sind.
* Wenn der Installations-Assistent bereits ausgeführt wird, werden keine Änderungen erkannt. Führen Sie den Installations-Assistenten in diesem Fall aus, und führen Sie ihn dann erneut aus.
* Stellen Sie sicher, dass das von Ihnen im Initialisierungsskript bereitgestellte Konto tatsächlich der richtige Benutzer ist, der vom Active Directory Connector verwendet wird. Gehen Sie dazu wie folgt vor:
  * Öffnen Sie im Startmenü den **Synchronisierungsdienst**.
  * Öffnen Sie die Registerkarte **Connectors** .
  * Suchen Sie nach dem Connector mit dem Typ „Active Directory-Domänendienste“, und wählen Sie ihn aus.
  * Klicken Sie unter **Aktionen** auf **Eigenschaften**.
  * Navigieren Sie zu **Mit Active Directory-Gesamtstruktur verbinden**. Stellen Sie sicher, dass die Domäne und der Benutzername, die auf diesem Bildschirm angegeben sind, mit dem im Skript angegebenen Konto übereinstimmen.
    ![Connector-Konto in Synchronization Service Manager](./media/how-to-connect-device-writeback/connectoraccount.png)

Überprüfen der Konfiguration in Active Directory:

* Überprüfen Sie, ob sich der Geräteregistrierungsdienst am unten angegebenen Ort (CN=DeviceRegistrationService,CN=Device Registration Services,CN=Device Registration Configuration,CN=Services,CN=Configuration) unter dem Konfigurationsnamenskontext befindet.

![Problembehandlung – DeviceRegistrationService im Konfigurationsnamespace](./media/how-to-connect-device-writeback/troubleshoot1.png)

* Stellen Sie sicher, dass nur ein Konfigurationsobjekt vorhanden ist, indem Sie den Konfigurationsnamespace durchsuchen. Löschen Sie das Duplikat, falls mehr als ein Objekt vorhanden ist.

![Problembehandlung – Suchen doppelter Objekte](./media/how-to-connect-device-writeback/troubleshoot2.png)

* Stellen Sie für das Geräteregistrierungsdienst-Objekt sicher, dass msDS-DeviceLocation für das Attribut vorhanden ist und über einen Wert verfügt. Suchen Sie nach diesem Ort, und vergewissern Sie sich, dass er vorhanden ist und über objectType msDS-DeviceContainer verfügt.

![Problembehandlung – msDS-DeviceLocation](./media/how-to-connect-device-writeback/troubleshoot3.png)

![Problembehandlung – RegisteredDevices-Objektklasse](./media/how-to-connect-device-writeback/troubleshoot4.png)

* Überprüfen Sie, ob das vom Active Directory Connector verwendete Konto über die erforderlichen Berechtigungen für den Container „Registrierte Geräte“ aus dem vorherigen Schritt verfügt. Dies sind die erwarteten Berechtigungen für diesen Container:

![Problembehandlung – Überprüfen der Berechtigungen für Container](./media/how-to-connect-device-writeback/troubleshoot5.png)

* Stellen Sie sicher, dass das Active Directory-Konto über Berechtigungen für das Objekt „CN=Device Registration Configuration,CN=Services,CN=Configuration“ verfügt.

![Problembehandlung – Überprüfen der Berechtigungen für Device Registration Configuration](./media/how-to-connect-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Zusätzliche Informationen
* [Verwalten von Risiken mit bedingtem Zugriff](../conditional-access/overview.md)
* [Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung](../devices/overview.md)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).