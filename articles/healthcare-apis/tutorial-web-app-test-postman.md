---
title: 'Web-App-Tutorial: Testen der Verbindung mit Azure API for FHIR'
description: Diese Tutorials führen Sie Schritte für Schritt durch ein Beispiel für die Bereitstellung einer einfachen Webanwendung. In diesem Teil des Tutorials wird das Testen der Verbindung mit dem FHIR-Server mithilfe von Postman beschrieben.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023311"
---
# <a name="testing-the-fhir-api"></a>Testen der FHIR-API
In den beiden vorherigen Schritten haben Sie Azure API for FHIR bereitgestellt und die Clientanwendung registriert. Nun können Sie testen, ob Azure API for FHIR für Ihre Clientanwendung eingerichtet ist. 

## <a name="retrieve-capability-statement"></a>Abrufen der Funktionsbestätigung
Zuerst rufen Sie die Funktionsbestätigung für Azure API for FHIR ab. 
1. Öffnen Sie Postman.
1. Rufen Sie die Funktionsbestätigung ab, indem Sie einen GET-Vorgang für „https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata“ ausführen. In der folgenden Abbildung lautet der Name des FHIR-Servers **fhirserver**.

![Funktionsbestätigung](media/tutorial-web-app/postman-capability-statement.png)

Als Nächstes versuchen Sie, Patientendaten abzurufen. Zum Abrufen von Patientendaten geben Sie einen GET-Vorgang für „https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient“ ein. Es wird ein Fehler vom Typ „401 – Nicht autorisiert“ angezeigt. Dieser Fehler tritt auf, weil Sie noch nicht nachgewiesen haben, dass Sie Zugriff auf Patientendaten haben müssen.

## <a name="get-patient-from-fhir-server"></a>Abrufen von Patientendaten vom FHIR-Server
![Fehler beim Abrufen von Patientendaten](media/tutorial-web-app/postman-patient-authorization-failed.png)

Um Zugriff zu erhalten, benötigen Sie ein Zugriffstoken.
1. Wählen Sie in Postman die Option **Autorisierung** aus, und legen Sie den Typ auf **OAuth 2.0** fest.
1. Wählen Sie **Neues Zugriffstoken abrufen** aus.
1. Füllen Sie die Felder aus, und wählen Sie **Token anfordern** aus. Nachfolgend sind die Werte der einzelnen Felder für dieses Tutorial angegeben.

|Feld                |Wert                                                               |
|---------------------|--------------------------------------------------------------------|
|Tokenname           |Ein Name für das Token                                               |
|Gewährungstyp           |Autorisierungscode                                                  |
|Rückruf-URL         |https://www.getpostman.com/oauth2/callback                          |
|Authentifizierungs-URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|Zugriffstoken-URL     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|Client-ID            |Die Client-ID, die Sie in den vorherigen Schritten kopiert haben             |
|Geheimer Clientschlüssel        |\<BLANK>                                                            |
|Bereich                |\<BLANK>                                                            |
|Status                |1234                                                                |
|Clientauthentifizierung|Clientanmeldeinformationen im Text senden                                     |

4. Melden Sie sich mit Ihren Anmeldeinformationen an, und wählen Sie **Akzeptieren** aus.
1. Scrollen Sie im Ergebnis nach unten, und wählen Sie **Token verwenden** aus.
1. Wählen Sie oben erneut **Senden** aus. Dieses Mal sollten Sie ein Ergebnis erhalten. ![Erfolgreiches Abrufen von Patientendaten](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>Veröffentlichen von Patientendaten auf dem FHIR-Server
Nun haben Sie Zugriff und können einen neuen Patienten erstellen. Hier sehen Sie ein Beispiel für einfache Patientendaten, die Sie auf dem FHIR-Server hinzufügen können. Geben Sie den folgenden Code in den Abschnitt **Text** von Postman ein.

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
Mit diesem POST-Vorgang wird ein neuer Patient auf dem FHIR-Server mit dem Namen James Tiberious Kirk erstellt.
![Veröffentlichen von Patientendaten](media/tutorial-web-app/postman-post-patient.png)

Wenn Sie den oben beschriebenen GET-Schritt zum Abrufen von Patientendaten erneut ausführen, wird James Tiberious Kirk in der Ausgabe aufgelistet.

## <a name="troubleshooting-access-issues"></a>Behandeln von Zugriffsproblemen
Wenn bei einem dieser Schritte Probleme aufgetreten sind, sehen Sie in den Dokumenten nach, die wir zu Azure Active Directory und Azure API for FHIR zusammengestellt haben. 

* [Azure AD und Azure API for FHIR](azure-ad-hcapi.md): In diesem Dokument werden einige der grundlegenden Prinzipien von Azure Active Directory und dessen Interaktion mit Azure API for FHIR beschrieben.
* [Überprüfung von Zugriffstoken](azure-ad-hcapi-token-validation.md): Diese Schrittanleitung enthält genauere Informationen zur Überprüfung von Zugriffstoken sowie Schritte zur Behebung von Zugriffsproblemen.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun erfolgreich eine Verbindung mit Ihrer Clientanwendung hergestellt haben, können Sie die Webanwendung schreiben.

>[!div class="nextstepaction"]
>[Schreiben einer Webanwendung](tutorial-web-app-write-web-app.md)



