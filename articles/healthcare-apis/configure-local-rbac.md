---
title: Konfigurieren der lokalen rollenbasierten Zugriffssteuerung für Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie die Azure API for FHIR für die Verwendung eines externen Azure AD-Mandanten für die Datenebene konfigurieren.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524197"
---
# <a name="configure-local-rbac-for-fhir"></a>Konfigurieren der lokalen RBAC für FHIR 

In diesem Artikel wird erläutert, wie Sie die Azure API for FHIR so konfigurieren, dass ein externer, sekundärer Azure Active Directory-Mandant zum Verwalten des Datenebenenzugriffs verwendet wird. Verwenden Sie diesen Modus nur, wenn Sie mit Ihrem Abonnement nicht den Azure Active Directory-Mandanten verwenden können.

> [!NOTE]
> Wenn Ihre FHIR-Dienstdatenebene für die Verwendung Ihres primären, Ihrem Abonnement zugeordneten Azure Active Directory-Mandanten konfiguriert ist, [weisen Sie mit Azure RBAC Datenebenenrollen zu](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Dienstprinzipal hinzufügen

Mit der lokalen RBAC können Sie einen externen Azure Active Directory-Mandanten mit Ihrem FHIR-Server verwenden. Damit das lokale RBAC-System Gruppenmitgliedschaften in diesem Mandanten überprüfen kann, muss die Azure API for FHIR im Mandanten über einen Dienstprinzipal verfügen. Dieser Dienstprinzipal wird automatisch in Mandanten erstellt, die an Abonnements gebunden sind, die die Azure API for FHIR bereitgestellt haben. Falls Ihr Mandant an kein Abonnement gebunden ist, muss ein Mandantenadministrator diesen Dienstprinzipal mit einem der folgenden Befehle erstellen:

Verwendung des `Az`-PowerShell-Moduls:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

Oder verwenden Sie das `AzureAd`-PowerShell-Modul:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

Alternativ können Sie die Azure-Befehlszeilenschnittstelle verwenden:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Konfigurieren der lokalen RBAC

Sie können die Azure API for FHIR auf dem Blatt **Authentifizierung** so konfigurieren, dass sie einen externen oder einen sekundären Azure Active Directory-Mandanten verwendet:

![Lokale RBAC-Zuweisungen](media/rbac/local-rbac-guids.png)erforderlich.

Geben Sie im Feld „Autorität“ einen gültigen Azure Active Directory-Mandanten ein. Nachdem der Mandant überprüft wurde, sollte das Feld **Zulässige Objekt-IDs** aktiviert werden, und Sie können eine Liste mit Identitätsobjekt-IDs eingeben. Diese IDs können die Identitätsobjekt-IDs sein von:

* Einem Azure Active Directory-Benutzer.
* Einem Azure Active Directory-Dienstprinzipal.
* Einer Azure Active Directory-Sicherheitsgruppe.

Weitere Informationen finden Sie im Artikel [Finden von Identitätsobjekt-IDs für die Authentifizierungskonfiguration](find-identity-object-ids.md).

Nachdem Sie die erforderlichen Objekt-IDs eingegeben haben, klicken Sie auf **Speichern**, und warten Sie, bis die Änderungen gespeichert sind, bevor Sie versuchen, mit den zugewiesenen Benutzern, Dienstprinzipalen oder Gruppen auf die Datenebene zuzugreifen.

## <a name="caching-behavior"></a>Verhalten beim Zwischenspeichern

Die Azure API for FHIR nimmt eine Zwischenspeicherung von Entscheidungen für bis zu fünf Minuten vor. Wenn Sie einem Benutzer Zugriff auf den FHIR-Server gewähren, indem Sie ihn der Liste der zulässigen Objekt-IDs hinzufügen, oder wenn Sie ihn aus der Liste entfernen, sollten Sie davon ausgehen, dass es bis zu fünf Minuten dauert, bis die Berechtigungsänderungen weitergegeben sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Zugriff auf die FHIR-Datenebene mithilfe eines externen (sekundären) Azure Active Directory-Mandanten zuweisen. Im nächsten Artikel erfahren Sie mehr über zusätzliche Einstellungen für Azure API for FHIR:
 
>[!div class="nextstepaction"]
>[Zusätzliche Einstellungen für Azure API for FHIR](azure-api-for-fhir-additional-settings.md)
