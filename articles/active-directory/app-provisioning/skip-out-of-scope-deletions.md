---
title: Überspringen des Löschvorgangs für Benutzer außerhalb des Gültigkeitsbereichs
description: Erfahren Sie, wie Sie das Standardverhalten beim Aufheben der Bereitstellung von Benutzern außerhalb des gültigen Bereichs außer Kraft setzen.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: f459a804b4c375eea17cbc22ded2f41f808c1b82
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995368"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Überspringen des Löschens von Benutzerkonten außerhalb des gültigen Bereichs

Standardmäßig werden Benutzer, die sich außerhalb des gültigen Bereichs befinden, vom Azure AD-Bereitstellungsmodul vorläufig gelöscht oder deaktiviert. In bestimmten Szenarien (z. B. bei der eingehenden Benutzerbereitstellung von Workday in AD) ist dieses Verhalten jedoch möglicherweise nicht das erwartete Verhalten, sodass Sie dieses Standardverhalten außer Kraft setzen möchten.  

In diesem Artikel wird beschrieben, wie Sie die Microsoft Graph-API und den Microsoft Graph-Tester verwenden, um das Flag ***SkipOutOfScopeDeletions** _ festzulegen, das die Verarbeitung von Konten steuert, die außerhalb des gültigen Bereichs liegen. Wenn ***SkipOutOfScopeDeletions** _ auf „0“ (FALSE) festgelegt ist, werden Konten, die außerhalb des gültigen Bereichs liegen, im Ziel deaktiviert.
Wenn ***SkipOutOfScopeDeletions** _ auf „1“ (TRUE) festgelegt ist, werden Konten, die außerhalb des gültigen Bereichs liegen, nicht im Ziel deaktiviert. Dieses Flag wird auf der Ebene der _Bereitstellungs-App* festgelegt und kann mithilfe der Graph-API konfiguriert werden. 

Da diese Konfiguration häufig bei der App für die *Benutzerbereitstellung von Workday in Active Directory* verwendet wird, enthalten die folgenden Schritte Screenshots der Workday-Anwendung. Die Konfiguration kann jedoch auch mit *allen anderen Apps* wie etwa ServiceNow, Salesforce und Dropbox verwendet werden.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Schritt 1: Abrufen der Dienstprinzipal-ID der Bereitstellungs-App (Objekt-ID)

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Abschnitt „Eigenschaften“ Ihrer Bereitstellungsanwendung. Wenn Sie z. B. die Zuordnung Ihrer *Anwendung für die Benutzerbereitstellung von Workday in AD* exportieren möchten, navigieren Sie zum Abschnitt „Eigenschaften“ dieser App. 
1. Kopieren Sie im Abschnitt „Eigenschaften“ Ihrer Bereitstellungs-App den GUID-Wert, der dem Feld *Objekt-ID* zugeordnet ist. Dieser Wert wird auch als die **ServicePrincipalId** Ihrer App bezeichnet, und wird in Graph-Tester-Vorgängen verwendet.

   ![Dienstprinzipal-ID der Workday-App](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Schritt 2: Anmelden bei Microsoft Graph-Tester

1. Starten Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer).
1. Klicken Sie auf die Schaltfläche „Mit Microsoft anmelden“, und melden Sie sich mit den globalen Azure AD-Anmeldeinformationen oder mit den Anmeldeinformationen für den App-Administrator an.

    ![Graph-Anmeldung](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Nach der erfolgreichen Anmeldung sehen Sie die Details des Benutzerkontos im linken Bereich.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Schritt 3: Abrufen der App-Anmeldeinformationen und -Verbindungsdetails

Führen Sie im Microsoft Graph-Tester die folgende GET-Abfrage durch und ersetzen Sie [servicePrincipalId] durch die in [Schritt 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) extrahierte **ServicePrincipalId**.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![GET-Auftragsabfrage](./media/skip-out-of-scope-deletions/skip-03.png)

Kopieren Sie die Antwort in eine Textdatei. Sie sieht wie der folgende JSON-Text aus, wobei die für Ihre Bereitstellung spezifischen Werte gelb hervorgehoben sind. Fügen Sie die grün hervorgehobenen Zeilen am Ende hinzu, und aktualisieren Sie das blau hervorgehobene Kennwort für die Workday-Verbindung. 

   ![GET-Auftragsantwort](./media/skip-out-of-scope-deletions/skip-04.png)

Nachfolgend sehen Sie den JSON-Block, der der Zuordnung hinzugefügt werden soll. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Schritt 4: Aktualisieren des Endpunkts für Geheimnisse mit dem Flag „SkipOutOfScopeDeletions“

Führen Sie im Graph-Tester den folgenden Befehl aus, um den Endpunkt für Geheimnisse mit dem Flag **_SkipOutOfScopeDeletions_* _ zu aktualisieren. 

Ersetzen Sie in der folgenden URL den Platzhalter „[servicePrincipalId]“ durch die _ *ServicePrincipalId**, die Sie in [Schritt 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id) extrahiert haben. 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Kopieren Sie den aktualisierten Text aus Schritt 3 in den Anforderungstext („Request Body“), und legen Sie den Header „Content-Type“ in „Request Headers“ auf „application/json“ fest. 

   ![PUT-Anforderung](./media/skip-out-of-scope-deletions/skip-05.png)

Klicken Sie auf „Run Query“ (Abfrage ausführen). 

Als Ausgabe sollte „Success – Status Code 204“(Erfolg – Statuscode 204) angezeigt werden. 

   ![PUT-Antwort](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Schritt 5: Sicherstellen, dass Benutzer außerhalb des gültigen Bereichs nicht deaktiviert werden

Sie können die Ergebnisse dieses Flags im erwarteten Verhalten testen, indem Sie Ihre Bereichsregeln so aktualisieren, dass ein bestimmter Benutzer übersprungen wird. Im folgenden Beispiel wird der Mitarbeiter mit der ID 21173 (der sich zuvor im Bereich befand) durch Hinzufügen einer neuen Bereichsregel ausgeschlossen: 

   ![Screenshot des Bereichs „Bereichsfilter hinzufügen“ mit hervorgehobenem Beispielbenutzer](./media/skip-out-of-scope-deletions/skip-07.png)

Im nächsten Bereitstellungszeitraum erkennt der Azure AD-Bereitstellungsdienst, dass sich der Benutzer 21173 außerhalb des gültigen Bereichs befindet. Und wenn die „SkipOutOfScopeDeletions“-Eigenschaft aktiviert ist, wird aufgrund der Synchronisierungsregel für diesen Benutzer eine Meldung wie im folgenden Screenshot angezeigt: 

   ![Bereichsbeispiel](./media/skip-out-of-scope-deletions/skip-08.png)


