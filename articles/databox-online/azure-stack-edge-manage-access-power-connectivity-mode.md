---
title: 'Azure Stack Edge Pro-Gerät: Zugriff, Energieeinstellungen und Konnektivitätsmodus | Microsoft-Dokumentation'
description: Hier wird beschrieben, wie Sie den Zugriff, die Energieeinstellungen und den Konnektivitätsmodus für das Azure Stack Edge Pro-Gerät verwalten, mit dem Daten an Azure übertragen werden.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 6455e4c9fa9460e47e3be7c860f89be540a514b0
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453075"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro"></a>Verwalten des Zugriffs, der Energieeinstellungen und des Konnektivitätsmodus für Ihr Azure Stack Edge Pro-Gerät

In diesem Artikel wird beschrieben, wie Sie den Zugriff, die Energieeinstellungen und den Konnektivitätsmodus für Ihr Azure Stack Edge Pro-Gerät verwalten. Diese Vorgänge werden über die lokale Webbenutzeroberfläche oder das Azure-Portal ausgeführt.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> * Verwalten des Gerätezugriffs
> * Verwalten des Konnektivitätsmodus
> * Verwalten der Energieeinstellungen


## <a name="manage-device-access"></a>Verwalten des Gerätezugriffs

Der Zugriff auf Ihr Azure Stack Edge Pro-Gerät wird mithilfe eines Gerätekennworts gesteuert. Sie können das Kennwort über die lokale Webbenutzeroberfläche ändern. Das Gerätekennwort kann auch über das Azure-Portal zurückgesetzt werden.

### <a name="change-device-password"></a>Ändern des Gerätekennworts

Führen Sie auf der lokalen Webbenutzeroberfläche die folgenden Schritte aus, um das Gerätekennwort zu ändern.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung > Password change** (Kennwortänderung).
2. Geben Sie das aktuelle Kennwort und dann das neue Kennwort ein. Das angegebene Kennwort muss zwischen 8 und 16 Zeichen umfassen. Das Kennwort muss drei der folgenden Zeichen enthalten: Großbuchstaben, Kleinbuchstaben, Ziffern und Sonderzeichen. Bestätigen Sie das neue Kennwort.

    ![Kennwort ändern](media/azure-stack-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Wählen Sie **Kennwort ändern** aus.
 
### <a name="reset-device-password"></a>Zurücksetzen des Gerätekennworts

Beim Workflow zum Zurücksetzen muss der Benutzer das alte Kennwort nicht kennen. Dies ist nützlich, wenn das Kennwort verloren gegangen ist. Dieser Workflow wird im Azure-Portal ausgeführt.

1. Navigieren Sie im Azure-Portal zu **Übersicht > Administratorkennwort zurücksetzen**.

    ![Kennwort zurücksetzen](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Geben Sie das neue Kennwort ein, und bestätigen Sie es. Das angegebene Kennwort muss zwischen 8 und 16 Zeichen umfassen. Das Kennwort muss drei der folgenden Zeichen enthalten: Großbuchstaben, Kleinbuchstaben, Ziffern und Sonderzeichen. Klicken Sie auf **Zurücksetzen**.

    ![Zurücksetzen von Kennwort 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Verwalten des Ressourcenzugriffs

Für das Erstellen Ihrer Azure Stack Edge-/Data Box Gateway-, IoT Hub- und Azure Storage-Ressourcen müssen Sie mindestens über Berechtigungen als Mitwirkender auf einer Ressourcengruppenebene verfügen. Außerdem müssen die entsprechenden Ressourcenanbieter registriert sein. Für sämtliche Vorgänge mit Aktivierungsschlüssel und Anmeldeinformationen sind zudem Berechtigungen für die Microsoft Graph-API erforderlich. Diese werden in den folgenden Abschnitten beschrieben. 

### <a name="manage-microsoft-graph-api-permissions"></a>Verwalten von Microsoft Graph-API-Berechtigungen

Wenn Sie den Aktivierungsschlüssel für das Azure Stack Edge Pro-Gerät generieren oder Vorgänge ausführen, die Anmeldeinformationen erfordern, müssen Sie über Berechtigungen für die Azure Active Directory-Graph-API verfügen. Im Anschluss folgen einige Beispiele für Vorgänge, die Anmeldeinformationen erfordern:

-  Erstellen einer Freigabe mit einem zugeordneten Speicherkonto
-  Erstellen eines Benutzers, der auf die Freigaben auf dem Gerät zugreifen kann

Sie müssen über `User`-Zugriff auf den Active Directory-Mandanten verfügen, da Sie die Aktion `Read all directory objects` ausführen können müssen. Gastbenutzer sind nicht zur Aktion `Read all directory objects` berechtigt. Für Gastbenutzer tritt bei Vorgängen wie dem Generieren eines Aktivierungsschlüssels, dem Erstellen einer Freigabe auf Ihrem Azure Stack Edge Pro-Gerät, dem Erstellen eines Benutzers, dem Konfigurieren der Edgecomputingrolle und dem Zurücksetzen des Gerätekennworts jeweils ein Fehler auf.

Weitere Informationen dazu, wie Sie Benutzern Zugriff auf die Microsoft Graph-API gewähren, finden Sie unter [Referenz zu Microsoft Graph-Berechtigungen](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrieren von Ressourcenanbietern

Wenn Sie eine Ressource in Azure (im Azure Resource Manager-Modell) bereitstellen möchten, benötigen Sie einen Ressourcenanbieter, der die Erstellung der Ressource unterstützt. Wenn Sie also beispielsweise einen virtuellen Computer bereitstellen möchten, muss im Abonnement ein Ressourcenanbieter vom Typ „Microsoft.Compute“ zur Verfügung stehen.
 
Ressourcenanbieter werden auf der Abonnementebene registriert. Standardmäßig wird jedes neue Azure-Abonnement vorab mit einer Liste gängiger Ressourcenanbieter registriert. Der Ressourcenanbieter für „Microsoft.DataBoxEdge“ ist in dieser Liste nicht enthalten.

Sie müssen Benutzern keine Zugriffsberechtigungen auf der Abonnementebene gewähren, damit sie Ressourcen wie „Microsoft.DataBoxEdge“ innerhalb ihrer Ressourcengruppen erstellen können, bei denen sie über Besitzerrechte verfügen – sofern die Ressourcenanbieter für diese Ressourcen bereits registriert sind.

Vergewissern Sie sich vor dem Erstellen einer Ressource, dass der Ressourcenanbieter im Abonnement registriert ist. Ist der Ressourcenanbieter nicht registriert, müssen Sie sicherstellen, dass der Benutzer, der die neue Ressource erstellt, über ausreichende Berechtigungen verfügt, um den erforderlichen Ressourcenanbieter auf der Abonnementebene registrieren zu können. Andernfalls tritt der folgende Fehler auf:

*Das Abonnement \<Subscription name> ist nicht berechtigt, die folgenden Ressourcenanbieter zu registrieren: Microsoft.DataBoxEdge.*


Führen Sie den folgenden Befehl aus, um eine Liste mit den registrierten Ressourcenanbietern im aktuellen Abonnement abzurufen:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Für das Azure Stack Edge Pro-Gerät sollte `Microsoft.DataBoxEdge` registriert werden. Zum Registrieren von `Microsoft.DataBoxEdge` muss der Abonnementadministrator den folgenden Befehl ausführen:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Weitere Informationen zum Registrieren eines Ressourcenanbieters finden Sie unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Verwalten des Konnektivitätsmodus

Ihr Gerät kann nicht nur im Standardmodus „Vollständig verbunden“, sondern auch im Modus „Teilweise getrennt“ oder im Modus „Vollständig getrennt“ ausgeführt werden. Die einzelnen Modi sind nachfolgend beschrieben:

- **Vollständig verbunden**: Dies ist der Standardmodus, in dem das Gerät betrieben wird. In diesem Modus ist sowohl der Hochladen von Daten in die Cloud als auch das Herunterladen von Daten aus der Cloud möglich. Sie können das Gerät im Azure-Portal oder auf der lokalen Webbenutzeroberfläche verwalten.

- **Teilweise getrennt**: In diesem Modus kann das Gerät keine freigegebenen Daten hochladen oder herunterladen, jedoch über das Azure-Portal verwaltet werden.

    Dieser Modus kommt in der Regel in einem getakteten Satellitennetzwerk zum Einsatz und dient zum Minimieren der Netzwerkbandbreitennutzung. Bei Geräteüberwachungsvorgängen kommt es aber unter Umständen dennoch zu einer geringfügigen Netzwerknutzung.

- **Getrennt**: In diesem Modus ist das Gerät vollständig von der Cloud getrennt, und Clouduploads und -downloads sind deaktiviert. Das Gerät kann nur über die lokale Weboberfläche verwaltet werden.

    Dieser Modus wird normalerweise verwendet, wenn Sie Ihr Gerät offline schalten möchten.

Führen Sie zum Ändern des Gerätemodus die folgenden Schritte aus:

1. Navigieren Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts zu **Konfiguration > Cloudeinstellungen**.
2. Wählen Sie in der Dropdownliste den Betriebsmodus des Geräts aus. Sie können zwischen **Vollständig verbunden**, **Teilweise verbunden** und **Vollständig getrennt** wählen. Wenn Sie das Gerät im Modus „Teilweise getrennt“ ausführen möchten, aktivieren Sie **Azure portal management** (Verwaltung im Azure-Portal).

    ![Konnektivitätsmodus](media/azure-stack-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Verwalten der Energieeinstellungen

Sie können Ihr physisches Gerät über die lokale Webbenutzeroberfläche herunterfahren und neu starten. Wir empfehlen, vor dem Neustart die Freigaben auf dem Datenserver und dann auf dem Gerät offline zu schalten. Dadurch wird das Risiko einer Datenbeschädigung minimiert.

1. Wechseln Sie auf der lokalen Webbenutzeroberfläche zu **Wartung > Power settings** (Energieeinstellungen).
2. Klicken Sie abhängig von der gewünschten Aktion auf **Herunterfahren** oder **Neu starten**.

    ![Energieeinstellungen](media/azure-stack-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja**, um fortzufahren.

> [!NOTE]
> Wenn Sie das physische Gerät herunterfahren, müssen Sie den Netzschalter am Gerät drücken, um es einzuschalten.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Verwaltung von Freigaben](azure-stack-edge-manage-shares.md).