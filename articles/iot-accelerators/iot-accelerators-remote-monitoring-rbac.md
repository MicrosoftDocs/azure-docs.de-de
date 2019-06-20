---
title: Remoteüberwachung-Zugriffssteuerung – Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung im Solution Accelerators der Remoteüberwachung konfigurieren.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/08/2019
ms.topic: conceptual
ms.openlocfilehash: 9accb41cdb4d780bf137d6872cca022226f902e6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443096"
---
# <a name="configure-role-based-access-controls-in-the-remote-monitoring-solution-accelerator"></a>Konfigurieren der rollenbasierten Zugriffssteuerung im Solution Accelerator der Remoteüberwachung

In diesem Artikel erfahren Sie, wie Sie die rollenbasierte Zugriffssteuerung im Solution Accelerators der Remoteüberwachung konfigurieren. Mit der rollenbasierten Zugriffssteuerung können Sie den Zugriff einzelner Benutzer auf bestimmte Funktionen der Lösung beschränken.

## <a name="default-settings"></a>Standardeinstellungen

Wenn Sie die Lösung für die Remoteüberwachung zum ersten Mal bereitstellen, stehen zwei Rollen zur Verfügung: **Administrator** und **Schreibgeschützt**.

Benutzer mit der Rolle **Administrator** verfügen über Vollzugriff auf die Lösung, einschließlich der nachfolgend aufgeführten Berechtigungen. Benutzer mit der Rolle **Lesezugriff** verfügen lediglich über den Zugriff zum Anzeigen der Lösung.

| Berechtigung            | Administrator | Nur Leseberechtigung |
|----------------       |-------|-----------|
| Anzeigen der Lösung         | Ja   | Ja       |
| Aktualisieren von Alarmen         | Ja   | Nein        |
| Löschen von Alarmen         | Ja   | Nein        |
| Erstellen von Geräten        | Ja   | Nein        |
| Aktualisieren von Geräten        | Ja   | Nein        |
| Löschen von Geräten        | Ja   | Nein        |
| Erstellen von Gerätegruppen  | Ja   | Nein        |
| Aktualisieren von Gerätegruppen  | Ja   | Nein        |
| Löschen von Gerätegruppen  | Ja   | Nein        |
| Erstellen von Regeln          | Ja   | Nein        |
| Aktualisieren von Regeln          | Ja   | Nein        |
| Löschen von Regeln          | Ja   | Nein        |
| Erstellen von Aufträgen           | Ja   | Nein        |
| Aktualisieren der SIM-Verwaltung | Ja   | Nein        |

Standardmäßig erhält der Benutzer, der die Lösung bereitgestellt hat, automatisch die Rolle **Administrator** und ist ein Azure Active Directory-Anwendungsbesitzer. Als Anwendungsbesitzer können Sie anderen Benutzern im Azure-Portal Rollen zuweisen. Wenn Sie möchten, dass ein anderer Benutzer Rollen in der Lösung zuweist, müssen Sie diesen im Azure-Portal als Anwendungsbesitzer bestimmen.

> [!NOTE]
> Der Benutzer, der die Lösung bereitgestellt hat, kann als **einzige Person** die Lösung unmittelbar nach der Bereitstellung anzeigen. Informationen dazu, wie anderen Benutzern der Zugriff zum Anzeigen der Anwendung mit der Rolle „Lesezugriff“, „Administrator“ oder „Benutzerdefiniert“ gewährt wird, finden Sie in den folgenden Anweisungen zum Hinzufügen oder Entfernen von Benutzern.

## <a name="add-or-remove-users"></a>Hinzufügen oder Entfernen von Benutzern

Als Azure Active Directory-Anwendungsbesitzer können Sie über das Azure-Portal einer Rolle in der Remoteüberwachungslösung einen Benutzer hinzufügen oder aus ihr entfernen. In den folgenden Schritten wird die [Azure Active Directory-Unternehmensanwendung](../active-directory/manage-apps/add-application-portal.md#find-your-azure-ad-tenant-application) verwendet, die beim Bereitstellen der Remoteüberwachungslösung erstellt wurde.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Überprüfen Sie, ob der [Benutzer sich in dem Verzeichnis befindet](../active-directory/fundamentals/add-users-azure-active-directory.md), das Sie verwenden. Sie haben das zu verwendende Verzeichnis ausgewählt, als Sie sich auf der Seite [Microsoft Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) angemeldet haben. Der Verzeichnisname wird oben rechts auf der [Seite](https://www.azureiotsolutions.com/Accelerators) angezeigt.

1. Suchen Sie nach der **Unternehmensanwendung** für Ihre Lösung im Azure-Portal. Filtern Sie dort die Liste, indem Sie **Anwendungstyp** auf **Alle Anwendungen** festlegen. Suchen Sie anhand des Anwendungsnamens nach Ihrer Anwendung. Der Anwendungsname ist der Name Ihrer Remoteüberwachungslösung. Im folgenden Screenshot sind der Anzeigename der Lösung und der Anwendung **contoso-rm4**.

    ![Unternehmensanwendung](media/iot-accelerators-remote-monitoring-rbac/appregistration.png)

1. Überprüfen Sie, ob Sie der Besitzer der Anwendung sind, indem Sie auf die Anwendung und dann auf **Besitzer** klicken. Im folgenden Screenshot ist **Contoso Admin** ein Besitzer der Anwendung **contoso-rm4**:

    ![Besitzer](media/iot-accelerators-remote-monitoring-rbac/owners.png)

    Wenn Sie kein Besitzer sind, müssen Sie einen vorhandenen Besitzer darum bitten, Sie der Liste hinzuzufügen. Nur Besitzer können anderen Benutzern Anwendungsrollen wie **Administrator** oder **Lesezugriff** zuweisen.

1. Klicken Sie auf **Benutzer und Gruppen**, um die Liste der Benutzer anzuzeigen, denen Rollen in der Anwendung zugewiesen sind.

1. Klicken Sie auf **+ Benutzer hinzufügen**, um einen Benutzer hinzuzufügen, und klicken Sie dann auf **Benutzer und Gruppen, keine ausgewählt**, um einen Benutzer aus dem Verzeichnis auszuwählen.

1. Um dem Benutzer eine Rolle zuzuweisen, klicken Sie auf **Rolle auswählen, keine ausgewählt**, und wählen Sie für den Benutzer die Rolle **Administrator** oder **Lesezugriff** aus. Klicken Sie auf **Auswählen** und dann auf **Zuweisen**.

    ![Rolle wählen](media/iot-accelerators-remote-monitoring-rbac/selectrole.png)

1. Der Benutzer kann nun auf die Remoteüberwachungslösung mit den rollenspezifischen Berechtigungen zugreifen.

1. Auf der Portalseite **Benutzer und Gruppen** können Sie Benutzer aus der Anwendung löschen.

## <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle

Die Remoteüberwachungslösung umfasst bei der ersten Bereitstellung die beiden Rollen **Administrator** und **Lesezugriff**. Sie können benutzerdefinierte Rollen mit unterschiedlichen Berechtigungen hinzufügen. Um eine benutzerdefinierte Rolle zu definieren, müssen Sie:

- Der Anwendung im Azure-Portal eine neue Rolle hinzufügen
- Eine Richtlinie für die neue Rolle im Microservice „Authentifizierung und Autorisierung“ definieren
- Die Webbenutzeroberfläche der Lösung aktualisieren

### <a name="define-a-custom-role-in-the-azure-portal"></a>Erstellen einer benutzerdefinierten Rolle im Azure-Portal

Die folgenden Schritte beschreiben, wie Sie einer Anwendung in Azure Active Directory eine Rolle hinzufügen. In diesem Beispiel erstellen Sie eine neue Rolle, mit der Benutzer in der Remoteüberwachungslösung Geräte erstellen, aktualisieren und löschen können.

1. Suchen Sie nach der **App-Registrierung** für Ihre Lösung im Azure-Portal. Der Anwendungsname ist der Name Ihrer Remoteüberwachungslösung. Im folgenden Screenshot sind der Anzeigename der Lösung und der Anwendung **contoso-rm4**.

    ![App-Registrierung](media/iot-accelerators-remote-monitoring-rbac/appregistration2.png)

1. Wählen Sie Ihre Anwendung aus, und klicken Sie auf **Manifest**. Es werden die beiden vorhandenen [App-Rollen](https://docs.microsoft.com/azure/architecture/multitenant-identity/app-roles) angezeigt, die für die Anwendung definiert sind:

    ![Anzeigen des Manifests](media/iot-accelerators-remote-monitoring-rbac/viewmanifest.png)

1. Bearbeiten Sie das Manifest, um die Rolle **ManageDevices** hinzuzufügen, wie im folgenden Ausschnitt dargestellt. Sie benötigen eine eindeutige Zeichenfolge wie eine GUID für die neue Rollen-ID. Sie können eine neue GUID mit einem Dienst wie [Online GUID Generator](https://www.guidgenerator.com/) generieren:

    ```json
    "appRoles": [
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Admin",
        "id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "isEnabled": true,
        "description": "Administrator access to the application",
        "value": "Admin"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "Read Only",
        "id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "isEnabled": true,
        "description": "Read only access to device information",
        "value": "ReadOnly"
      },
      {
        "allowedMemberTypes": [
          "User"
        ],
        "displayName": "ManageDevices",
        "id": "ADD A NEW GUID HERE",
        "isEnabled": true,
        "description": "A new role for the application.",
        "value": "ManageDevices"
      }
    ],
    ```

    Speichern Sie die Änderungen.

### <a name="define-a-policy-for-the-new-role"></a>Definieren einer Richtlinie für die neue Rolle

Nachdem Sie die Rolle der App im Azure-Portal hinzugefügt haben, müssen Sie für die Rolle eine Richtlinie in [roles.json](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/auth/Services/data/policies/roles.json) definieren, die zur Geräteverwaltung erforderliche Berechtigungen zuweist.

1. Klonen des [Remoteüberwachung-Microservices](https://github.com/Azure/remote-monitoring-services-dotnet)-Repository von GitHub auf Ihren lokalen Computer.

1. Bearbeiten Sie die Datei **auth/Services/data/policies/roles.json**, um die Richtlinie für die Rolle **ManageDevices** hinzuzufügen (siehe den folgenden Ausschnitt). Die Werte für **ID** und **Rolle** müssen der Rollendefinition im App-Manifest aus dem vorherigen Abschnitt entsprechen. Die Liste der zulässigen Aktionen ermöglicht Benutzern mit der Rolle **ManageDevices** mit der Lösung verbundene Geräte zu erstellen, zu aktualisieren und zu löschen:

    ```json
    {
    "Items": [
      {
        "Id": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
        "Role": "admin",
        "AllowedActions": [
          "UpdateAlarms",
          "DeleteAlarms",
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices",
          "CreateDeviceGroups",
          "UpdateDeviceGroups",
          "DeleteDeviceGroups",
          "CreateRules",
          "UpdateRules",
          "DeleteRules",
          "CreateJobs",
          "UpdateSimManagement"
        ]
      },
      {
        "Id": "e5bbd0f5-128e-4362-9dd1-8f253c6082d7",
        "Role": "readOnly",
        "AllowedActions": []
      },
      {
        "Id": "GUID FROM APP MANIFEST",
        "Role": "ManageDevices",
        "AllowedActions": [
          "CreateDevices",
          "UpdateDevices",
          "DeleteDevices"
        ]
      }
    ]
    }
    ```

1. Wenn Sie die Datei **Services/data/policies/roles.json** bearbeitet haben, erstellen Sie den Microservice „Authentifizierung und Autorisierung“ neu, und stellen Sie ihn erneut für Ihren Solution Accelerator bereit.

### <a name="how-the-web-ui-enforces-permissions"></a>Erzwingen von Berechtigungen durch die Webbenutzeroberfläche

Die Webbenutzeroberfläche verwendet den Microservice [Authentifizierung und Autorisierung](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) um festzulegen, welche Aktionen ein Benutzer ausführen darf, und welche Steuerelemente in der Benutzeroberfläche angezeigt werden. Wenn Ihre Lösung beispielsweise **contoso-rm4** heißt, ruft die Webbenutzeroberfläche eine Liste der zulässigen Aktionen für den aktuellen Benutzer ab, indem sie die folgende Anforderung sendet:

```http
http://contoso-rm4.azurewebsites.net/v1/users/current
headers:
X-Source: true
Authorization: Bearer <JWT Token from ADAL>
```

Für einen Benutzer namens **Geräte-Manager** mit der Rolle **ManageDevices** enthält die Antwort den folgenden JSON-Code im Text:

```json
{
  "Id": "userIdExample",
  "Email": "devicemanager@contoso.com",
  "Name": "Device Manager",
  "AllowedActions": [
    "CreateDevices",
    "UpdateDevices",
    "DeleteDevices"
  ]
}
```

Der folgende Ausschnitt aus [deviceDelete.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/pages/devices/flyouts/deviceDelete/deviceDelete.js) in der [Webbenutzeroberfläche](https://github.com/Azure/pcs-remote-monitoring-webui/) zeigt, wie die Berechtigungen deklarativ erzwungen werden:

```json
<FlyoutContent>
  <Protected permission={permissions.deleteDevices}>
    <form className="device-delete-container" onSubmit={this.deleteDevices}>
      ...
    </form>
  </Protected>
</FlyoutContent>
```

Weitere Informationen finden Sie unter [Geschützte Komponenten](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/components/shared/protected/README.md). Sie können zusätzliche Berechtigungen in der Datei [authModel.js](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/src/services/models/authModels.js) definieren.

### <a name="how-the-microservices-enforce-permissions"></a>Erzwingen von Berechtigungen durch Microservices

Die Microservices überprüfen die Berechtigungen, um Schutz vor nicht autorisierten API-Anforderungen zu bieten. Wenn ein Microservice eine API-Anforderung empfängt, decodiert und überprüft er das JWT-Token, um die Benutzer-ID und die Berechtigungen der Benutzerrolle abzurufen.

Der folgende Ausschnitt aus der Datei [DevicesController.cs](https://github.com/Azure/remote-monitoring-services-dotnet/blob/master/iothub-manager/WebService/v1/Controllers/DevicesController.cs) im [IoT Hub-Manager-Microservice](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) zeigt, wie Berechtigungen erzwungen werden:

```csharp
[HttpDelete("{id}")]
[Authorize("DeleteDevices")]
public async Task DeleteAsync(string id)
{
    await this.devices.DeleteAsync(id);
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die rollenbasierte Zugriffssteuerung im Solution Accelerator der Remoteüberwachung implementieren.

Informationen zum Verwalten des Zugriffs auf den Time Series Insights-Explorer im Solution Accelerator für die Remoteüberwachung finden Sie unter [Konfigurieren der Zugriffssteuerung für den Time Series Insights-Explorer](iot-accelerators-remote-monitoring-rbac-tsi.md).

Weitere konzeptuelle Informationen zum Solution Accelerator für die Remoteüberwachung finden Sie unter [Remoteüberwachungsarchitektur](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Weitere Informationen zum Anpassen der Remoteüberwachungslösung finden Sie unter [Anpassen und erneutes Bereitstellen eines Microservices](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->