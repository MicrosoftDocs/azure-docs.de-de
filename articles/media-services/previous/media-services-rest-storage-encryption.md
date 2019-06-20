---
title: Verschlüsseln von Inhalten mit der Speicherverschlüsselung und AMS-REST-API
description: Erfahren Sie, wie Sie Inhalte mit der Speicherverschlüsselung und AMS-REST-APIs verschlüsseln.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 30ac6a94142c9b9d987fb3fd32b3483cc6dc130c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64867594"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Verschlüsseln von Inhalten mit der Speicherverschlüsselung 

> [!NOTE]
> Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).   > Media Services v2 werden derzeit keine neuen Features oder Funktionen hinzugefügt. <br/>Sehen Sie sich die neuste Version – [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/) – an. Lesen Sie außerdem die [Hinweise zur Migration von v2 zu v3](../latest/migrate-from-v2-to-v3.md).
>   

Es wird dringend empfohlen, Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung zu verschlüsseln und sie dann in Azure Storage hochzuladen, wo sie verschlüsselt im Ruhezustand gespeichert werden.

Dieser Artikel bietet einen Überblick über die AMS-Speicherverschlüsselung und zeigt, wie Sie die verschlüsselten Inhalte hochladen:

* Erstellen Sie einen Inhaltsschlüssel.
* Erstellen Sie ein Medienobjekt. Legen Sie AssetCreationOption auf StorageEncryption fest, wenn Sie das Medienobjekt erstellen.
  
     Verschlüsselte Medienobjekte werden Inhaltsschlüsseln zugeordnet.
* Verknüpfen Sie den Inhaltsschlüssel mit dem Medienobjekt.  
* Legen Sie die Parameter für die Verschlüsselung in den AssetFile-Entitäten fest.

## <a name="considerations"></a>Überlegungen 

Wenn Sie ein speicherverschlüsseltes Medienobjekt übermitteln möchten, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie gestreamt. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-rest-configure-asset-delivery-policy.md).

Wenn Sie in Media Services auf Entitäten zugreifen, müssen Sie bestimmte Headerfelder und Werte in Ihren HTTP-Anforderungen festlegen. Weitere Informationen finden Sie unter [Installation für die Entwicklung mit der Media Services-REST-API](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Speicherseitige Verschlüsselung

|Verschlüsselungsoption|BESCHREIBUNG|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services-Speicherverschlüsselung|AES-256-Verschlüsselung, Schlüssel von Media Services verwaltet|Unterstützt<sup>(1)</sup>|Nicht unterstützt<sup>(2)</sup>|
|[Speicherdienstverschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Durch Azure Storage angebotene serverseitige Verschlüsselung – Schlüssel wird von Azure oder vom Kunden verwaltet|Unterstützt|Unterstützt|
|[Clientseitige Storage-Verschlüsselung](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Durch Azure Storage angebotene clientseitige Verschlüsselung – Schlüssel wird vom Kunden in Key Vault verwaltet|Nicht unterstützt|Nicht unterstützt|

<sup>1</sup> Media Services unterstützt zwar die Behandlung von Inhalten in Klartext/ohne jede Form der Verschlüsselung, doch wird davon abgeraten.

<sup>2</sup> In Media Services v3 wird Speicherverschlüsselung (AES-256-Verschlüsselung) nur für die Abwärtskompatibilität unterstützt, wenn Ihre Ressourcen mit Media Services v2 erstellt wurden. Dies bedeutet, dass v3 mit vorhandenen speicherverschlüsselten Ressourcen funktioniert, jedoch nicht die Erstellung neuer zulässt.

## <a name="connect-to-media-services"></a>Verbinden mit Mediendiensten

Informationen zum Herstellen einer Verbindung mit der AMS-API finden Sie unter [Zugreifen auf die Azure Media Services-API per Azure AD-Authentifizierung](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Übersicht über die Speicherverschlüsselung
Die AMS-Speicherverschlüsselung wendet die Verschlüsselung im **AES-CTR-Modus** auf die gesamte Datei an.  AES-CTR ist eine Blockchiffre, die Daten beliebiger Länge ohne Auffüllen verschlüsseln kann. AES-CTR verschlüsselt einen Zählerblock mit dem AES-Algorithmus und kombiniert die Ausgabe von AES per XOR-Operation mit den zu verschlüsselnden oder zu entschlüsselnden Daten.  Der verwendete Zählerblock wird erstellt, indem der InitializationVector-Wert in die Bytes 0 bis 7 des Zählerwerts kopiert und die Bytes 8 bis 15 des Zählerwerts auf 0 gesetzt werden. Im 16 Byte langen Zählerblock werden die Bytes 8 bis 15 (also die niedrigstwertigen Bytes) als einfache 64-Bit-Ganzzahl ohne Vorzeichen verwendet, die für jeden nachfolgenden Block verarbeiteter Daten um eins erhöht wird. Die Reihenfolge der Netzwerkbytes wird beibehalten. Wenn diese Ganzzahl ihren Maximalwert erreicht (0xFFFFFFFFFFFFFFFF), wird durch weitere Erhöhung der Blockzähler auf Null zurückgesetzt (Bytes 8 bis 15), ohne Auswirkung auf die restlichen 64 Bits des Zählers (also Bytes 0 bis 7).   Um die Sicherheit der Verschlüsselung im AES-CTR-Modus zu gewährleisten, muss der InitializationVector-Wert jeder Schlüsselkennung für jeden Inhaltsschlüssel für jede Datei eindeutig sein, und die Dateien müssen weniger als 2^64 Blöcke lang sein.  Mit diesem eindeutigen Wert wird sichergestellt, dass ein Zählerwert nie mit einem vorhandenen Schlüssel wiederverwendet wird. Weitere Informationen zum CTR-Modus finden Sie auf [dieser Wiki-Seite](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (der Wiki-Artikel verwendet den Begriff „Nonce“ anstelle von „InitializationVector“).

Verwenden Sie die **Speicherverschlüsselung** , um Ihre unverschlüsselten Inhalte lokal mithilfe der AES-256-Bit-Verschlüsselung zu verschlüsseln und sie dann in Azure Storage hochzuladen, wo sie verschlüsselt im Ruhezustand gespeichert werden. Medienobjekte, die durch Speicherverschlüsselung geschützt sind, werden automatisch entschlüsselt, vor der Codierung in einem verschlüsselten Dateisystem platziert und optional vor dem Hochladen als neues Ausgabemedienobjekt erneut verschlüsselt. Der primäre Anwendungsfall für Storage Encryption ist, wenn Sie Ihre qualitativ hochwertigen Eingabemediendateien mit starker Verschlüsselung beim Speichern im Ruhezustand auf dem Datenträger sichern möchten.

Um ein speicherverschlüsseltes Medienobjekt zu übermitteln, müssen Sie die Übermittlungsrichtlinie des Medienobjekts konfigurieren und Media Services so mitteilen, wie die Inhalte bereitgestellt werden sollen. Bevor das Medienobjekt gestreamt werden kann, wird die Speicherverschlüsselung vom Streamingserver entfernt und der Inhalt mithilfe der angegebenen Übermittlungsrichtlinie (AES, Common Encryption oder unverschlüsselt) gestreamt.

## <a name="create-contentkeys-used-for-encryption"></a>Erstellen von Inhaltsschlüsseln zur Verschlüsselung
Verschlüsselte Medienobjekte werden einem Speicherverschlüsselungsschlüssel zugeordnet. Erstellen Sie vor der Erstellung der Medienobjektdateien den Inhaltsschlüssel, der zur Verschlüsselung verwendet werden soll. In diesem Abschnitt wird beschrieben, wie ein Inhaltsschlüssel erstellt wird.

Im Folgenden finden Sie allgemeine Schritte zum Generieren von Inhaltsschlüsseln, die Sie den zu verschlüsselnden Medienobjekten zuordnen. 

1. Generieren Sie für die Speicherverschlüsselung einen 32-Byte-AES-Schlüssel nach dem Zufallsprinzip. 
   
    Der 32-Byte-AES-Schlüssel ist der Inhaltsschlüssel für Ihr Medienobjekt. Das bedeutet, dass alle mit diesem Medienobjekt verknüpften Dateien denselben Inhaltsschlüssel zur Entschlüsselung verwenden müssen. 
2. Rufen Sie die [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid)-Methode und die [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey)-Methode auf, um das richtige X.509-Zertifikat zur Verschlüsselung Ihres Inhaltsschlüssels abzurufen.
3. Verschlüsseln Sie Ihren Inhaltsschlüssel mit dem öffentlichen Schlüssel des X.509-Zertifikats. 
   
   Das Media Services .NET SDK verwendet RSA mit OAEP zur Verschlüsselung.  Ein .NET-Beispiel finden Sie in der [EncryptSymmetricKeyData-Funktion](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Erstellen Sie einen Prüfsummenwert, der mithilfe der Schlüsselkennung und des Inhaltsschlüssels berechnet wird. Im folgenden .NET-Beispiel wird die Prüfsumme anhand des GUID-Abschnitts der Schlüsselkennung und des unverschlüsselten Inhaltsschlüssels berechnet.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Erstellen Sie den Inhaltsschlüssel mit den Werten für **EncryptedContentKey** (in eine Base64-codierte Zeichenfolge konvertiert), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** und **Checksum**, die Sie in den vorherigen Schritten erhalten haben.

    Bei der Speicherverschlüsselung sollten folgende Eigenschaften im Anforderungstext enthalten sein.

    Eigenschaft im Anforderungstext    | BESCHREIBUNG
    ---|---
    id | Die ContentKey-ID wird in folgendem Format generiert: „nb:kid:UUID:\<NEW GUID“.
    ContentKeyType | Der Inhaltsschlüsseltyp ist eine Ganzzahl, die den Schlüssel definiert. Für das Speicherverschlüsselungsformat ist der Wert 1.
    EncryptedContentKey | Wir erstellen einen neuen Inhaltsschlüsselwert mit einer Länge von 256 Bits (32 Bytes). Der Schlüssel wird mithilfe des X.509-Speicherverschlüsselungszertifikats verschlüsselt, das wir von Microsoft Azure Media Services abrufen, indem wir eine HTTP-GET-Anforderung für die Methoden „GetProtectionKeyId“ und „GetProtectionKey“ ausführen. Folgender .NET-Code dient als Beispiel: die **EncryptSymmetricKeyData**-Methode, die [hier](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)definiert ist.
    ProtectionKeyId | Dies ist die Schutzschlüssel-ID für das X.509-Speicherverschlüsselungszertifikat, das zur Verschlüsselung des Inhaltsschlüssels verwendet wurde.
    ProtectionKeyType | Dies ist der Verschlüsselungstyp für den Schutzschlüssel, der zur Verschlüsselung des Inhaltsschlüssels verwendet wurde. In unserem Beispiel lautet der Wert "StorageEncryption(1)".
    Checksum |Die per MD5 berechnete Prüfsumme für den Inhaltsschlüssel. Die Berechnung erfolgt durch Verschlüsselung der Inhalts-ID mit dem Inhaltsschlüssel. Der Beispielcode zeigt, wie die Prüfsumme berechnet wird.


### <a name="retrieve-the-protectionkeyid"></a>Abrufen der ProtectionKeyId
Im folgenden Beispiel wird veranschaulicht, wie Sie die ProtectionKeyId abrufen. Dabei handelt es sich um einen Zertifikatfingerabdruck für das Zertifikat, das Sie zur Verschlüsselung Ihres Inhaltsschlüssels verwenden. Führen Sie den folgenden Schritt aus, um sicherzustellen, dass das entsprechende Zertifikat bereits auf Ihrem Computer vorhanden ist.

Anforderung:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

Antwort:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Abrufen des ProtectionKey für die ProtectionKeyId
Das folgende Beispiel zeigt, wie Sie das X.509-Zertifikat mithilfe der im vorherigen Schritt abgerufenen ProtectionKeyId abrufen.

Anforderung:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Antwort:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Erstellen des Inhaltsschlüssels
Nachdem Sie das X.509-Zertifikat abgerufen und dessen öffentlichen Schlüssel zum Verschlüsseln Ihres Inhaltsschlüssels verwendet haben, erstellen Sie eine **ContentKey** -Entität und legen die entsprechenden Eigenschaftswerte fest.

Beim Erstellen des Inhaltsschlüssels muss neben anderen Werten auch der Typ festgelegt werden. Bei Verwendung der Speicherverschlüsselung sollte der Wert auf „1“ festgelegt werden. 

Das folgende Beispiel zeigt, wie Sie einen **ContentKey** mit einem **ContentKeyType** erstellen, für den die Speicherverschlüsselung festgelegt („1“) und **ProtectionKeyType** auf „0“ festgelegt wurde. Dies zeigt an, dass es sich bei der Schutzschlüssel-ID um den X.509-Zertifikatfingerabdruck handelt.  

Anforderung

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Antwort:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Erstellen eines Medienobjekts
Im folgenden Beispiel wird veranschaulicht, wie Sie ein Medienobjekt erstellen.

**HTTP-Anforderung**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**HTTP-Antwort**

Im Erfolgsfall wird die folgende Antwort zurückgegeben:

    HTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Zuordnen des ContentKey zu einem Medienobjekt
Nachdem Sie den ContentKey erstellt haben, ordnen Sie ihn mithilfe des $links-Vorgangs Ihrem Medienobjekt zu, wie im folgenden Beispiel beschrieben:

Anforderung:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Antwort:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Erstellen einer AssetFile
Die [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) -Entität stellt eine Video- oder Audiodatei dar, die in einem Blobcontainer gespeichert ist. Eine Medienobjektdatei ist immer mit einem Medienobjekt verknüpft, wobei ein Medienobjekt eine oder mehrere Medienobjektdateien enthalten kann. Der Media Services Encoder-Task kann nicht ausgeführt werden, wenn ein Medienobjektdatei-Objekt keiner digitalen Datei in einem Blobcontainer zugeordnet ist.

Die **AssetFile** -Instanz und die eigentliche Mediendatei sind zwei verschiedene Objekte. Die AssetFile-Instanz enthält Metadaten zur Mediendatei, während die Mediendatei die tatsächlichen Medieninhalte enthält.

Nachdem Sie Ihre digitale Mediendatei in einen Blobcontainer hochgeladen haben, verwenden Sie die **MERGE**-HTTP-Anforderung, um das AssetFile-Element anhand von Informationen über Ihre Mediendatei zu aktualisieren (in diesem Artikel nicht beschrieben). 

**HTTP-Anforderung**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }

**HTTP-Antwort**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
