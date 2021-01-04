---
title: Clientseitige Verschlüsselung mit Python
titleSuffix: Azure Storage
description: Die Azure Storage-Clientbibliothek für Python unterstützt die clientseitige Verschlüsselung. Dies bietet maximale Sicherheit für Ihre Azure Storage-Anwendungen.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 511166e156591562b2120b58cc420f3fccd1d8c4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008928"
---
# <a name="client-side-encryption-with-python"></a>Clientseitige Verschlüsselung mit Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Übersicht
Die [Azure Storage-Clientbibliothek für Python](https://pypi.python.org/pypi/azure-storage) unterstützt die Verschlüsselung von Daten innerhalb von Clientanwendungen vor dem Hochladen der Daten nach Azure Storage sowie die Entschlüsselung von Daten während des Herunterladens auf den Client.

> [!NOTE]
> Die Azure Storage Python-Bibliothek befindet sich in der Vorschau.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Verschlüsselung und Entschlüsselung über das Umschlagverfahren
Die Prozesse bei der Verschlüsselung und Entschlüsselung folgen dem Umschlagverfahren.

### <a name="encryption-via-the-envelope-technique"></a>Verschlüsselung über das Umschlagverfahren
Die Verschlüsselung über das Umschlagverfahren funktioniert wie folgt:

1. Die Azure-Speicherclientbibliothek generiert einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), bei dem es sich um einen einmalig verwendeten symmetrischen Schlüssel handelt.
2. Benutzerdaten werden mit diesem CEK verschlüsselt.
3. Der CEK wird dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen (verschlüsselt). Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein, das bzw. der lokal verwaltet wird.
   Die Speicherclientbibliothek hat selbst nie Zugriff auf den KEK. Die Bibliothek ruft lediglich den Algorithmus für das Umschließen des Schlüssels aus, der vom KEK bereitgestellt wird. Benutzer können bei Bedarf benutzerdefinierte Anbieter für das Umschließen von Schlüsseln bzw. das Aufheben dieses Zustands verwenden.
4. Die verschlüsselten Daten werden dann in den Azure Storage-Dienst hochgeladen. Der umschlossene Schlüssel und einige zusätzliche Verschlüsselungsmetadaten werden entweder als Metadaten (in einem Blob) gespeichert oder mit den verschlüsselten Daten (Warteschlangennachrichten und Tabellenentitäten) interpoliert.

### <a name="decryption-via-the-envelope-technique"></a>Entschlüsselung über das Umschlagverfahren
Die Entschlüsselung über das Umschlagverfahren funktioniert wie folgt:

1. Die Clientbibliothek geht davon aus, dass der Benutzer den KEK verwaltet. Der Benutzer muss den spezifischen Schlüssel, der für die Verschlüsselung verwendet wurde, nicht kennen. Stattdessen kann ein Schlüsselresolver eingerichtet und verwendet werden, der verschiedene Schlüsselbezeichner in Schlüssel auflöst.
2. Die Clientbibliothek lädt die verschlüsselten Daten zusammen mit sämtlichem Verschlüsselungsmaterial herunter, das für den Dienst gespeichert ist.
3. Der umschlossene Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK) wird dann mithilfe des Schlüsselverschlüsselungschlüssels (Key Encryption Key, KEK) entpackt. Die Clientbibliothek hat wiederum keinen Zugriff auf den KEK. Sie ruft einfach den Entpackungsalgorithmus des benutzerdefinierten Anbieters auf.
4. Der Inhaltsverschlüsselungsschlüssel (CEK) wird dann zum Entschlüsseln der verschlüsselten Benutzerdaten verwendet.

## <a name="encryption-mechanism"></a>Verschlüsselungsmechanismus
Die Speicherclientbibliothek verwendet [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) , um Benutzerdaten zu verschlüsseln. Insbesondere wird der [CBC-Modus (Blockchiffreverkettung, Cipher Block Chaining)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mit AES verwendet. Da jeder Dienst eine andere Funktionsweise aufweist, werden die Dienste hier erörtert.

### <a name="blobs"></a>BLOBs
Die Clientbibliothek unterstützt momentan nur die Verschlüsselung vollständiger Blobs. Die Verschlüsselung wird unterstützt, wenn Benutzer die **create** _-Methoden verwenden. Es werden sowohl vollständige als auch Bereichsdownloads unterstützt, und Uploads und Downloads können parallelisiert werden.

Bei der Verschlüsselung generiert die Clientbibliothek einen zufälligen Initialisierungsvektor (IV) mit einer Größe von 16 Byte zusammen mit einem zufälligen Inhaltsverschlüsselungsschlüssel (CEK) mit einer Größe von 32 Byte. Mithilfe dieser Informationen wird die Umschlagverschlüsselung der Blobdaten ausgeführt. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden dann als Blobmetadaten zusammen mit dem verschlüsselten Blob für den Dienst gespeichert.

> [!WARNING]
> Wenn Sie eigene Metadaten für den Blob bearbeiten oder hochladen, müssen Sie sicherstellen, dass diese Metadaten beibehalten werden. Wenn Sie neue Metadaten ohne diese Metadaten hochladen, gehen der umschlossene CEK, der IV und andere Metadaten verloren und der Blobinhalt wird nie wieder abrufbar sein.
> 
> 

Beim Herunterladen eines verschlüsselten Blobs wird der Inhalt des gesamten Blobs mit den _*get**_ -Hilfsmethoden abgerufen. Der umschlossene CEK wird entpackt und zusammen mit dem IV (in diesem Fall als Blobmetadaten gespeichert) verwendet, um die entschlüsselten Daten an die Benutzer zurückzugeben.

Beim Herunterladen eines beliebigen Bereichs (_*get**_ -Methoden mit übergebenen Bereichsparametern) im verschlüsselten Blob wird der von den Benutzern angegebene Bereich angepasst, um eine kleine Menge zusätzlicher Daten abzurufen, die zum erfolgreichen Entschlüsseln des angeforderten Bereichs verwendet werden können.

Es können nur Blockblobs und Seitenblobs mit diesem Schema verschlüsselt/entschlüsselt werden. Das Verschlüsseln von Anfügeblobs wird zurzeit nicht unterstützt.

### <a name="queues"></a>Warteschlangen
Da Warteschlangenmeldungen ein beliebiges Format aufweisen können, definiert die Clientbibliothek ein benutzerdefiniertes Format, das den Initialisierungsvektor (IV) und den verschlüsselten Inhaltsverschlüsselungsschlüssel (CEK) im Meldungstext enthält.

Bei der Verschlüsselung generiert die Clientbibliothek einen zufälligen IV mit einer Größe von 16 Byte zusammen mit einem zufälligen CEK mit einer Größe von 32 Byte. Mithilfe dieser Informationen wird die Umschlagverschlüsselung des Texts der Warteschlangenmeldung durchgeführt. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden der verschlüsselten Warteschlangenmeldung dann hinzugefügt. Diese geänderte Meldung (siehe unten) wird für den Dienst gespeichert.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Bei der Entschlüsselung wird der umschlossene Schlüssel aus der Warteschlangenmeldung extrahiert und entpackt. Der Initialisierungsvektor wird ebenfalls aus der Warteschlangenmeldung extrahiert und zusammen mit dem entpackten Schlüssel verwendet, um die Daten der Warteschlangenmeldung zu entschlüsseln. Beachten Sie, dass die Verschlüsselungsmetadaten eine geringe Größe aufweisen (weniger als 500 Byte). Dies wird zwar für die 64-KB-Begrenzung für eine Warteschlangenmeldung angerechnet, die Auswirkungen sollten vertretbar sein.

### <a name="tables"></a>Tabellen
Die Clientbibliothek unterstützt die Verschlüsselung von Entitätseigenschaften für Einfüge- und Ersetzungsvorgänge.

> [!NOTE]
> Das Zusammenführen wird derzeit nicht unterstützt. Da eine Teilmenge der Eigenschaften möglicherweise bereits mit einem anderen Schlüssel verschlüsselt wurde, führen das einfache Zusammenführen der neuen Eigenschaften und das Aktualisieren der Metadaten zu Datenverlusten. Das Zusammenführen erfordert entweder zusätzliche Dienstaufrufe, um die bereits vorhandene Entität aus dem Dienst zu lesen, oder die Verwendung eines neuen Schlüssels pro Eigenschaft. Beide Verfahren sind aus Leistungsgründen nicht geeignet.
> 
> 

Die Verschlüsselung von Tabellendaten funktioniert wie folgt:

1. Die Benutzer geben die Eigenschaften an, die verschlüsselt werden sollen.
2. Die Clientbibliothek generiert einen zufälligen Initialisierungsvektor (IV) mit einer Größe von 16 Byte zusammen mit einem zufälligen Inhaltsverschlüsselungsschlüssel (CEK) mit einer Größe von 32 Byte für jede Entität und führt die Umschlagverschlüsselung für die einzelnen zu verschlüsselnden Eigenschaften durch, indem ein neuer IV pro Eigenschaft abgeleitet wird. Die verschlüsselte Eigenschaft wird als Binärdaten gespeichert.
3. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden dann als zwei zusätzliche reservierte Eigenschaften gespeichert. Die erste reservierte Eigenschaft (\_ClientEncryptionMetadata1) ist eine Zeichenfolgeneigenschaft, die die Informationen über den IV, die Version und den umschlossenen Schlüssel enthält. Die zweite reservierte Eigenschaft (\_ClientEncryptionMetadata2) ist eine binäre Eigenschaft, die die Informationen zu den Eigenschaften enthält, die verschlüsselt werden. Die Informationen in dieser zweiten Eigenschaft (\_ClientEncryptionMetadata2) sind selbst verschlüsselt.
4. Aufgrund dieser zusätzlichen reservierten Eigenschaften, die für die Verschlüsselung erforderlich sind, verfügen die Benutzer jetzt möglicherweise nur über 250 benutzerdefinierte Eigenschaften anstelle von 252. Die Gesamtgröße der Entität muss weniger als 1 MB betragen.

   Beachten Sie, dass nur Zeichenfolgeneigenschaften verschlüsselt werden können. Wenn andere Typen von Eigenschaften verschlüsselt werden sollen, müssen sie in Zeichenfolgen konvertiert werden. Die verschlüsselten Zeichenfolgen werden als binäre Eigenschaften für den Dienst gespeichert, und sie werden nach der Entschlüsselung wieder in Zeichenfolgen konvertiert (unformatierte Zeichenfolgen, nicht EntityProperties vom Typ EdmType.STRING).

   Für Tabellen müssen die Benutzer zusätzlich zur Verschlüsselungsrichtlinie die Eigenschaften angeben, die verschlüsselt werden sollen. Dies kann erfolgen, indem entweder diese Eigenschaften in TableEntity-Objekten vom Typ EdmType.STRING gespeichert werden und die Verschlüsselung auf TRUE festgelegt wird, oder indem die „encryption_resolver_function“ im tableservice-Objekt festgelegt wird. Ein Verschlüsselungsresolver ist eine Funktion, die einen Partitionsschlüssel, einen Zeilenschlüssel und einen Eigenschaftennamen annimmt und einen booleschen Wert zurückgibt, der angibt, ob die Eigenschaft verschlüsselt werden soll. Bei der Verschlüsselung verwendet die Clientbibliothek diese Informationen, um zu entscheiden, ob eine Eigenschaft beim Schreiben in das Netzwerk verschlüsselt werden soll. Der Delegat bietet zudem die Möglichkeit einer Logik bezüglich der Verschlüsselung der Eigenschaften. (Beispiel: Wenn X, dann wird Eigenschaft A verschlüsselt, andernfalls werden die Eigenschaften A und B verschlüsselt.) Beachten Sie, dass es nicht notwendig ist, diese Informationen beim Lesen oder Abfragen von Entitäten bereitzustellen.

### <a name="batch-operations"></a>Batchvorgänge
Für alle Zeilen im Batch gilt eine einzige Verschlüsselungsrichtlinie. Die Clientbibliothek generiert intern einen neuen zufälligen IV und einen zufälligen CEK pro Zeile im Batch . Die Benutzer können auch verschiedene Eigenschaften für jeden Vorgang im Batch verschlüsseln, indem dieses Verhalten im Verschlüsselungsresolver definiert wird.
Wenn ein Batch als Kontext-Manager durch die Methode „tableservice batch()“ erstellt wird, wird die Verschlüsselungsrichtlinie automatisch auf das Batch angewendet. Wenn ein Batch explizit durch Aufrufen des Konstruktors erstellt wird, muss die Verschlüsselungsrichtlinie als Parameter übergeben werden und darf sich während der gesamten Lebensdauer des Batches nicht ändern.
Beachten Sie Folgendes: Entitäten werden beim Einfügen in das Batch mithilfe der Verschlüsselungsrichtlinie des Batches verschlüsselt (Entitäten werden NICHT zum Zeitpunkt des Commits des Batches über die Verschlüsselungsrichtlinie von „tableservice“ verschlüsselt).

### <a name="queries"></a>Abfragen
> [!NOTE]
> Da die Entitäten verschlüsselt sind, können Sie keine Abfragen ausführen, die nach einer verschlüsselten Eigenschaft filtern.  Wenn Sie dies versuchen, erhalten Sie falsche Ergebnisse, da der Dienst verschlüsselte Daten mit unverschlüsselten Daten vergleicht.
> 
> 
> Zum Ausführen von Abfragevorgängen müssen Sie einen Schlüsselresolver angeben, der alle Schlüssel im Resultset auflösen kann. Wenn eine im Abfrageergebnis enthaltene Entität nicht in einen Anbieter aufgelöst werden kann, löst die Clientbibliothek einen Fehler aus. Für jede Abfrage, die serverseitige Projektionen ausführt, fügt die Clientbibliothek den ausgewählten Spalten standardmäßig die spezifischen Verschlüsselungsmetadateneigenschaften (\_ClientEncryptionMetadata1 und \_ClientEncryptionMetadata2) hinzu.
> 
> [!IMPORTANT]
> Beachten Sie bei Verwendung einer clientseitigen Verschlüsselung die folgenden wichtigen Punkte:
> 
> _ Verwenden Sie beim Lesen aus einem verschlüsselten Blob oder beim Schreiben in diesen Befehle zum Hochladen des vollständigen Blobs und zum Herunterladen des bereichsbasierten oder vollständigen Blobs. Vermeiden Sie beim Schreiben in einen verschlüsselten Blob Protokollvorgänge wie z. B. "Put Block", "Put Block List", "Write Pages" oder "Clear Pages". Andernfalls wird der verschlüsselte Blob möglicherweise beschädigt und kann nicht mehr gelesen werden.
> * Für Tabellen gilt eine ähnliche Einschränkung. Achten Sie darauf, dass Sie beim Aktualisieren verschlüsselter Eigenschaften auch die Verschlüsselungsmetadaten aktualisieren.
> * Wenn Sie Metadaten für den verschlüsselten Blob festlegen, werden die für die Entschlüsselung erforderlichen verschlüsselungsbezogenen Metadaten möglicherweise überschrieben, da das Festlegen von Metadaten kein additiver Vorgang ist. Dies gilt auch für Momentaufnahmen: Geben Sie während der Erstellung einer Momentaufnahme eines verschlüsselten Blobs keine Metadaten an. Wenn Metadaten festgelegt werden müssen, rufen Sie zunächst die **get_blob_metadata**-Methode auf, um die aktuellen Verschlüsselungsmetadaten abzurufen. Vermeiden Sie zudem gleichzeitige Schreibvorgänge, während Metadaten festgelegt werden.
> * Aktivieren Sie das **require_encryption**-Flag im Dienstobjekt für Benutzer, die nur mit verschlüsselten Daten arbeiten sollen. Weitere Details finden Sie nachstehend.

Die Clientbibliothek des Speichers erwartet den bereitgestellten KEK und Schlüsselresolver, um die folgende Schnittstelle zu implementieren. [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -Unterstützung für die Python-KEK-Verwaltung steht noch aus und wird in diese Bibliothek integriert, sobald sie fertig gestellt ist.

## <a name="client-api--interface"></a>Client-API/Schnittstelle
Nachdem ein Speicherdienstobjekt (d.h. „blockblobservice“) erstellt wurde, kann der Benutzer Werte für die Felder zuweisen, aus denen sich eine Verschlüsselungsrichtlinie zusammensetzt: „key_encryption_key“, „key_resolver_function“ und „require_encryption“. Benutzer können entweder nur einen KEK, nur einen Resolver oder beides bereitstellen. „key_encryption_key“ ist der grundlegende Schlüsseltyp, der mit einer Schlüsselkennung gekennzeichnet wird und die Logik für das Einschließen/Entpacken bereitstellt. „key_resolver_function“ wird verwendet, um einen Schlüssel während des Entschlüsselungsvorgangs aufzulösen. Die Funktion gibt einen gültigen KEK zu einem Schlüsselbezeichner zurück. Dies ermöglicht den Benutzern die Auswahl zwischen mehreren Schlüsseln, die an mehreren Speicherorten verwaltet werden.

Der KEK muss die folgenden Methoden implementieren, um Daten erfolgreich verschlüsseln zu können:

* wrap_key(cek): Umschließt den angegebenen CEK (Bytes) mithilfe eines Algorithmus, den der Benutzer auswählt. Gibt den umschlossenen Schlüssel zurück.
* get_key_wrap_algorithm(): Gibt den Algorithmus zurück, der für das Umschließen von Schlüsseln verwendet wird.
* get_kid(): Gibt die Zeichenfolgenschlüssel-ID für diesen KEK zurück.
  Der KEK muss die folgenden Methoden implementieren, um Daten erfolgreich entschlüsseln zu können:
* unwrap_key(cek, algorithm): Gibt die nicht umschlossene Form des angegebenen CEK mithilfe des durch die Zeichenfolge angegebenen Algorithmus zurück.
* get_kid(): Gibt eine Zeichenfolgenschlüssel-ID für diesen KEK zurück.

Der Schlüsselresolver muss mindestens eine Methode implementieren, die nach Empfang eines Schlüsselbezeichners den entsprechenden KEK zurückgibt, der die obige Schnittstelle implementiert. Nur diese Methode darf der key_resolver_function-Eigenschaft im Dienstobjekt zugewiesen werden.

* Für die Verschlüsselung wird immer der Schlüssel verwendet. Ein fehlender Schlüssel führt zu einem Fehler.
* Für die Entschlüsselung gilt:

  * Der Schlüsselresolver wird aufgerufen, wenn er angegeben wurde, um den Schlüssel abzurufen. Wenn der Resolver angegeben wird, dieser aber nicht über eine Zuordnung für die Schlüsselkennung verfügt, wird ein Fehler ausgelöst.
  * Ist kein Resolver, aber ein Schlüssel angegeben, wird der Schlüssel verwendet, wenn seine Kennung mit der geforderten Schlüsselkennung übereinstimmt. Stimmt die Kennung nicht überein, wird ein Fehler ausgelöst.

    Die Verschlüsselungsbeispiele in „azure.storage.samples“ veranschaulichen ein ausführlicheres End-to-End-Szenario für Blobs, Warteschlangen und Tabellen.
      Beispielimplementierungen des KEK und des Schlüsselresolvers werden in den Beispieldateien als KeyWrapper bzw. KeyResolver bereitgestellt.

### <a name="requireencryption-mode"></a>RequireEncryption-Modus
Benutzer können optional einen Betriebsmodus aktivieren, in dem alle hoch- oder herunterzuladenden Daten verschlüsselt werden müssen. In diesem Modus schlagen Versuche, Daten ohne eine Verschlüsselungsrichtlinie hochzuladen oder Daten herunterzuladen, die nicht für den Dienst verschlüsselt sind, auf dem Client fehl. Das Flag **require_encryption** im Dienstobjekt steuert dieses Verhalten.

### <a name="blob-service-encryption"></a>Blob-Diensterschlüsselung
Legen Sie die Felder für die Verschlüsselungsrichtlinie auf das blockblobservice-Objekt fest. Alles Weitere wird intern von der Clientbibliothek behandelt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Warteschlangendienst-Verschlüsselung
Legen Sie die Felder für die Verschlüsselungsrichtlinie auf das queueservice-Objekt fest. Alles Weitere wird intern von der Clientbibliothek behandelt.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Tabellendienstverschlüsselung
Zusätzlich zum Erstellen einer Verschlüsselungsrichtlinie und zum Festlegen der Richtlinie für die Anforderungsoptionen müssen Sie entweder eine **encryption_resolver_function** im **tableservice** angeben oder das encrypt-Attribut für die EntityProperty festlegen.

### <a name="using-the-resolver"></a>Verwenden des Resolvers

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Verwenden von Attributen
Wie oben erwähnt, kann eine Eigenschaft zur Verschlüsselung gekennzeichnet werden, indem sie in einem EntityProperty-Objekt gespeichert und das Verschlüsselungsfeld festgelegt wird.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Verschlüsselung und Leistung
Beachten Sie, dass ein Verschlüsseln Ihrer Storage-Daten einen zusätzlichen Leistungsaufwand verursacht. Der Inhaltsschlüssel und der IV müssen generiert, der Inhalt selbst muss verschlüsselt, und zusätzliche Metadaten müssen formatiert und hochgeladen werden. Dieser Aufwand variiert abhängig von der Menge der zu verschlüsselnden Daten. Es empfiehlt sich, dass Kunden ihre Anwendungen während der Entwicklung immer hinsichtlich der Leistung testen.

## <a name="next-steps"></a>Nächste Schritte
* Herunterladen der [Azure Storage-Clientbibliothek für das Java-PyPi-Paket](https://pypi.python.org/pypi/azure-storage)
* Herunterladen der [Azure Storage-Clientbibliothek für Python-Quellcode aus GitHub](https://github.com/Azure/azure-storage-python)
