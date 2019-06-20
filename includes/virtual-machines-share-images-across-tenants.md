---
title: include file
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 499aeccdf00980eeb66ac6ee06e45267fd515143
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178055"
---
Mithilfe von Katalogen mit freigegebenen Images können Sie Images über RBAC freigeben. Sie können RBAC verwenden, um Images innerhalb Ihres Mandanten und sogar für Personen außerhalb Ihres Mandanten freizugeben. Wenn Sie jedoch Images außerhalb Ihres Azure-Mandanten freigeben möchten, sollten Sie eine App-Registrierung erstellen, um die Freigabe zu erleichtern.  Die Verwendung einer App-Registrierung kann komplexere Freigabeszenarien ermöglichen, wie z.B.: 

* Verwaltung freigegebener Images, wenn ein Unternehmen ein anderes erwirbt, und die Azure-Infrastruktur ist auf verschiedene Mandanten verteilt. 
* Azure-Partner verwalten Azure-Infrastruktur im Namen ihrer Kunden. Die Anpassung der Images erfolgt innerhalb des Partners, aber die Bereitstellung der Infrastruktur erfolgt beim Mandanten des Kunden. 


## <a name="create-the-app-registration"></a>Erstellen der App-Registrierung

Erstellen Sie eine App-Registrierung, die von beiden Mandanten verwendet wird, um die Ressourcen des Imagekatalogs freizugeben.
1. Öffnen Sie die [App-Registrierungen (Vorschauversion) im Azure-Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Wählen im Menü oben auf der Seite die Schaltfläche **Neue Registrierung** aus.
1. Geben Sie im Feld **Name** *myGalleryApp* ein.
1. Wählen Sie unter **Unterstützte Kontotypen** **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus.
1. Geben Sie unter **Umleitungs-URI** *https://www.microsoft.com* ein, und wählen Sie **Registrieren** aus. Nachdem die App-Registrierung erstellt wurde, öffnet sich die Übersichtsseite.
1. Kopieren Sie auf der Übersichtsseite die **Anwendungs-ID (Client)** und speichern Sie sie zur späteren Verwendung.   
1. Wählen Sie **Zertifikate & Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus.
1. Geben Sie in **Beschreibung** *Mandantenübergreifendes App-Geheimnis für Katalog mit freigegebenen Images*.
1. Behalten Sie unter **Ablauf** die Standardeinstellung von **In 1 Jahr** bei, und wählen Sie dann **Hinzufügen** aus.
1. Kopieren Sie den Wert des geheimen Schlüssels, und speichern Sie ihn an einem sicheren Ort. Sie können ihn nicht mehr abrufen, nachdem Sie die Seite verlassen haben.


Erteilen Sie der App-Registrierung die Berechtigung, den Katalog mit freigegebenen Images zu nutzen.
1. Wählen Sie im Azure-Portal den Katalog mit freigegebenen Images aus, den Sie für einen anderen Mandanten freigeben möchten.
1. Wählen Sie **Zugriffssteuerung (IAM) auswählen** und dann unter **Rollenzuordnung hinzufügen** *Hinzufügen* aus. 
1. Wählen Sie unter **Rolle** die Option **Leser** aus.
1. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
1. Geben Sie unter **Auswählen** *myGalleryApp* ein, und wählen Sie es aus, wenn es in der Liste angezeigt wird. Wählen Sie **Speichern** aus, wenn der Vorgang abgeschlossen ist.


## <a name="give-tenant-2-access"></a>Mandant 2 Zugriff gewähren

Gewähren Sie Mandant 2 durch Anfordern einer Anmeldung mit einem Browser Zugriff auf die Anwendung. Ersetzen Sie *<Tenant2 ID>* durch die Mandanten-ID des Mandanten, für den Sie Ihren Imagekatalog freigeben möchten. Ersetzen Sie *<Anwendungs-ID (Client)>* durch die Anwendungs-ID der von Ihnen erstellten App-Registrierung. Wenn Sie die Ersetzungen vorgenommen haben, fügen Sie die URL in einen Browser ein, und folgen Sie den Anmeldeaufforderungen, um sich bei Mandant 2 anzumelden.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

Melden Sie sich im [Azure-Portal](https://portal.azure.com) als Mandant 2 an, und geben Sie der App-Registrierung Zugriff auf die Ressourcengruppe, in der Sie die VM erstellen möchten.

1. Wählen Sie die Ressourcengruppe und dann **Zugriffssteuerung (IAM)** aus. Wählen Sie unter **Rollenzuweisung hinzufügen** die Option **Hinzufügen** aus. 
1. Geben Sie unter **Rolle** die Option **Mitwirkender** ein.
1. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
1. Geben Sie unter **Auswählen** *myGalleryApp* ein, und wählen Sie es dann aus, wenn es in der Liste angezeigt wird. Wählen Sie **Speichern** aus, wenn der Vorgang abgeschlossen ist.

> [!NOTE]
> Sie müssen warten, bis die Imageversion vollständig erstellt und repliziert wurde, bevor Sie dieses verwaltete Image verwenden können, um eine weitere Imageversion zu erstellen.

