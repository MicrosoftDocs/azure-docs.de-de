---
title: Überwachen und Verwalten der Zertifikaterstellung
description: Szenarien, die eine Reihe von Erstellungs-, Verarbeitungs-, Überwachungs- und Interaktionsoptionen für den Zertifikatserstellungsprozess mit Key Vault veranschaulichen.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d86960e726ae18fba8d171ab9f85d7c991b4e40
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729234"
---
# <a name="monitor-and-manage-certificate-creation"></a>Überwachen und Verwalten der Zertifikaterstellung
Anwendungsbereich: Azure

Folgendes 

In diesem Artikel beschriebene Szenarien/Vorgänge:

- Anfordern eines KV-Zertifikats mit einem unterstützten Aussteller
- Ausstehende Anforderung abrufen – Anforderungsstatus ist „inProgress“
- Ausstehende Anforderung abrufen – Anforderungsstatus ist „complete“
- Ausstehende Anforderung abrufen – Status der ausstehenden Anforderung ist „canceled“ oder „failed“
- Ausstehende Anforderung abrufen – Status der ausstehenden Anforderung ist „deleted“ oder „overwritten“
- Erstellen (oder importieren) wenn ausstehende Anforderung vorhanden ist – Status ist „inProgress“
- Zusammenführen, wenn ausstehende Anforderung mit einem Aussteller (z.B. DigiCert) erstellt wird
- Einen Abbruch anfordern, während der Status der ausstehenden Anforderung „inProgress“ ist
- Ein ausstehendes Anforderungsobjekt löschen
- Ein KV-Zertifikat manuell erstellen
- Zusammenführen, wenn eine ausstehende Anforderung erstellt wird – manuelle Zertifikaterstellung

## <a name="request-a-kv-certificate-with-a-supported-issuer"></a>Anfordern eines KV-Zertifikats mit einem unterstützten Aussteller 

|Methode|Anforderungs-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

Die folgenden Beispiele erfordern ein Objekt mit dem Namen „mydigicert“ um bereits in Ihrem Schlüsseltresor mit dem Zertifikataussteller DigiCert verfügbar zu sein. Der Zertifikataussteller ist eine Entität, die in Azure Key Vault (KV) als eine CertificateIssuer-Ressource dargestellt wird. Sie dient zur Bereitstellung von Informationen zur Quelle eines KV-Zertifikats: Ausstellername, Anbieter, Anmeldeinformationen und andere administrative Details.

### <a name="request"></a>Anforderung

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert",
      "cty": "OV-SSL",
    }
  }
}
```

### <a name="response"></a>response

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "mydigicert"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "InProgress",
  "status_details": "Pending certificate created. Certificate request is in progress. This may take some time based on the issuer provider. Please check again later",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-inprogress"></a>Ausstehende Anforderung abrufen – Anforderungsstatus ist „inProgress“

|Methode|Anforderungs-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Anforderung
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

> [!NOTE]
> Wenn *request_id* in der Abfrage angegeben ist, verhält sie sich wie ein Filter. Wenn das *request_id*-Element in der Abfrage und im ausstehenden Objekt unterschiedlich ist, wird der HTTP-Statuscode 404 zurückgegeben.

### <a name="response"></a>response

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="get-pending-request---request-status-is-complete"></a>Ausstehende Anforderung abrufen – Anforderungsstatus ist „complete“

### <a name="request"></a>Anforderung

|Methode|Anforderungs-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>response

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "completed",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "target": “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
}

```

## <a name="get-pending-request---pending-request-status-is-canceled-or-failed"></a>Ausstehende Anforderung abrufen – Status der ausstehenden Anforderung ist „canceled“ oder „failed“

### <a name="request"></a>Anforderung

|Methode|Anforderungs-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>response

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "failed",
  "status_details": "",
  "request_id": "a76827a18b63421c917da80f28e9913d",
  "error": {
    "code": "<errorcode>",
    "message": "<message>"
  }
}

```

> [!NOTE]
> Der Wert des *Fehlercodes* kann je nach Aussteller oder Benutzerfehler „Zertifikatausstellerfehler“ oder „Anforderung abgelehnt“ lauten.

## <a name="get-pending-request---pending-request-status-is-deleted-or-overwritten"></a>Ausstehende Anforderung abrufen – Status der ausstehenden Anforderung ist „deleted“ oder „overwritten“
Ein ausstehendes Objekt kann durch einen Erstellungs-/Importvorgang gelöscht oder überschrieben werden, wenn der Status nicht „inProgress“ ist.

|Methode|Anforderungs-URI|
|------------|-----------------|
|GET|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Anforderung
GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

GET `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>response

```
StatusCode: 404, ReasonPhrase: 'Not Found'
{
  "error": {
    "code": "PendingCertificateNotFound",
    "message": "…"
  }
}

```

## <a name="create-or-import-when-pending-request-exists---status-is-inprogress"></a>Erstellen (oder importieren) wenn ausstehende Anforderung vorhanden ist – Status ist „inProgress“
Ein ausstehende Objekt kann einen von vier Status aufweisen: „inprogress“, „canceled“, „failed“ oder „completed“.

Wenn der Status einer ausstehende Anforderung „inprogress“, schlagen Erstellungs- und Importvorgänge mit dem HTTP-Statuscode 409 (Konflikt) fehl.

Beheben eines Konflikts:

- Wenn das Zertifikat manuell erstellt wird, können Sie das KV-Zertifikat abschließen, indem Sie das ausstehende Objekt entweder zusammenführen oder löschen.

- Wenn das Zertifikat mit einem Aussteller erstellt wird, können Sie warten, bis das Zertifikat abgeschlossen wird, fehlschlägt oder abgebrochen wird. Alternativ können Sie das ausstehende Objekt löschen.

> [!NOTE]
> Durch das Löschen eines ausstehenden Objekts wird u.U. die Anforderung des X509-Zertifikats beim Anbieter abgebrochen.

|Methode|Anforderungs-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Anforderung

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "mydigicert"
    }
  }
}
```

### <a name="response"></a>response

```
StatusCode: 409, ReasonPhrase: 'Conflict'
{
  "error": {
    "code": "Forbidden",
    "message": "A new key vault certificate can not be created or imported while a pending key vault certificate's status is inProgress."
  }
}

```

## <a name="merge-when-pending-request-is-created-with-an-issuer"></a>Zusammenführen, wenn ausstehende Anforderung mit einem Aussteller erstellt wird
Das Zusammenführen ist nicht zulässig, wenn ein ausstehendes Objekt mit einem Aussteller erstellt wird, ist jedoch zulässig, wenn sein Zustand „inProgress“ ist. 

Wenn die Anforderung zum Erstellen des X509-Zertifikats aus irgendeinem Grund fehlschlägt oder abgebrochen wird, und wenn ein X509-Zertifikat mit Out-of-Band-Methoden abgerufen werden kann, kann ein Zusammenführungsvorgang erfolgen, um das KV-Zertifikat abzuschließen.

|Methode|Anforderungs-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Anforderung

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

### <a name="response"></a>response

```json
StatusCode: 403, ReasonPhrase: 'Forbidden'
{
  "error": {
    "code": "Forbidden",
    "message": "Merge is forbidden on pending object created with issuer : <issuer-name> while it is in progess."
  }
}

```

## <a name="request-a-cancellation-while-the-pending-request-status-is-inprogress"></a>Einen Abbruch anfordern, während der Status der ausstehenden Anforderung „inProgress“ ist
Ein Abbruch kann nur angefordert werden. Eine Anforderung wird abgebrochen oder nicht. Wenn eine Anforderung nicht den Status „inProgress“ hat, wird der HTTP-Status 400 (Ungültige Anforderung) zurückgegeben.

|Methode|Anforderungs-URI|
|------------|-----------------|
|PATCH|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Anforderung
PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

PATCH `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

```json
{
  "cancellation_requested": true
}

```

### <a name="response"></a>response

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": true,
  "status": "inProgress",
  "status_details": "…",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}
```

## <a name="delete-a-pending-request-object"></a>Ein ausstehendes Anforderungsobjekt löschen

> [!NOTE]
> Durch das Löschen des ausstehenden Objekts wird u.U. die Anforderung des X509-Zertifikats beim Anbieter abgebrochen.

|Methode|Anforderungs-URI|
|------------|-----------------|
|DELETE|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}`|

### <a name="request"></a>Anforderung
DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"`

OR

DELETE `“https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}"`

### <a name="response"></a>response

```
StatusCode: 200, ReasonPhrase: 'OK'
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "{issuer-name}"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "cancellation_requested": false,
  "status": "inProgress",
  "request_id": "a76827a18b63421c917da80f28e9913d",
}
```

## <a name="create-a-kv-certificate-manually"></a>Ein KV-Zertifikat manuell erstellen
Sie können manuell ein Zertifikat erstellen, das mit einer Zertifizierungsstelle Ihrer Wahl ausgestellt wird. Legen Sie den Namen des Ausstellers auf „Unbekannt“, oder geben Sie das Ausstellerfeld nicht an.

|Methode|Anforderungs-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/create?api-version={api-version}`|

### <a name="request"></a>Anforderung

```json
{
  "policy": {
    "x509_props": {
      "subject": "CN=MyCertSubject1"
    },
    "issuer": {
      "name": "Unknown"
    }
  }
}

```

### <a name="response"></a>response

```
StatusCode: 202, ReasonPhrase: 'Accepted'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1/pending?api-version={api-version}&request_id=a76827a18b63421c917da80f28e9913d"
{
  "id": “https://mykeyvault.vault.azure.net/certificates/mycert1/pending",
  "issuer": {
    "name": "Unknown"
  },
  "csr": "MIICq......DD5Lp5cqXg==",
  "status": "inProgress",
  "status_details": "Pending certificate created. Please Perform Merge to complete the request.",
  "request_id": "a76827a18b63421c917da80f28e9913d"
}

```

## <a name="merge-when-a-pending-request-is-created---manual-certificate-creation"></a>Zusammenführen, wenn eine ausstehende Anforderung erstellt wird – manuelle Zertifikaterstellung

|Methode|Anforderungs-URI|
|------------|-----------------|
|POST|`https://mykeyvault.vault.azure.net/certificates/mycert1/pending/merge?api-version={api-version}`|

### <a name="request"></a>Anforderung

```json
{
  "x5c": [ "MIICxTCCAbi………………………trimmed for brevitiy……………………………………………EPAQj8=" ]
}

```

|Elementname|Erforderlich|Type|Version|BESCHREIBUNG|
|------------------|--------------|----------|-------------|-----------------|
|x5c|Ja|Array|\<Einführung in Version >|X509-Zertifikatkette als Base-64-Zeichenfolgenarray.|

### <a name="response"></a>response

```
StatusCode: 201, ReasonPhrase: 'Created'
Location: “https://mykeyvault.vault.azure.net/certificates/mycert1?api-version={api-version}"
{
    "id": "https mykeyvault.vault.azure.net/certificates/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "kid": "https:// mykeyvault.vault.azure.net/keys/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "sid": " mykeyvault.vault.azure.net/secrets/mycert1/f366e1a9dd774288ad84a45a5f620352",
    "cer": "……de34534……",
    "x5t": "n14q2wbvyXr71Pcb58NivuiwJKk",
    "attributes": {
        "enabled": true,
        "exp": 1530394215,
        "nbf": 1435699215,
        "created": 1435699919,
        "updated": 1435699919
    },
    "pending": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/pending"
    },
    "policy": {
        "id": "https:// mykeyvault.vault.azure.net/certificates/mycert1/policy",
        "key_props": {
            "exportable": false,
            "kty": "RSA",
            "key_size": 2048,
            "reuse_key": false
        },
        "secret_props": {
            "contentType": "application/x-pkcs12"
        },
        "x509_props": {
            "subject": "CN=Mycert1",
            "ekus": ["1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2"],
            "validity_months": 12
        },
        "lifetime_actions": [{
            "trigger": {
                "lifetime_percentage": 80
            },
            "action": {
                "action_type": "EmailContacts"
            }
        }],
        "issuer": {
            "name": "Unknown"
        },
        "attributes": {
            "enabled": true,
            "created": 1435699811,
            "updated": 1435699811
        }
    }
}

```

## <a name="see-also"></a>Siehe auch
- [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-and-certificates.md)
