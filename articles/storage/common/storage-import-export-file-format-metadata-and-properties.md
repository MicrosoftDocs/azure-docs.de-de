---
title: Format der Metadaten- und Eigenschaftendateien von Azure Import/Export | Microsoft Docs
description: Informationen zum Angeben von Metadaten und Eigenschaften für mindestens ein Blob, das Teil eines Import- oder Exportauftrags ist.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 2066d4a2ed6db97285d92d15e14dbd21629dbdfa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478554"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Format der Metadaten- und Eigenschaftendateien des Azure Import/Export-Diensts
Sie können Metadaten und Eigenschaften als Teil eines Import- oder Exportauftrags für mindestens ein Blob angeben. Zum Festlegen von Metadaten oder Eigenschaften für Blobs, die als Teil eines Importauftrags erstellt werden, geben Sie eine Metadaten- oder Eigenschaftendatei auf der Festplatte an, die die zu importierenden Daten enthält. Bei einem Exportauftrag werden Metadaten und Eigenschaften in eine entsprechende Datei auf der Festplatte geschrieben, die an Sie zurückgesendet wird.  
  
## <a name="metadata-file-format"></a>Format der Metadatendatei  
Das Format einer Metadatendatei lautet wie folgt:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML-Element|Type|BESCHREIBUNG|  
|-----------------|----------|-----------------|  
|`Metadata`|Stammelement|Das Stammelement der Metadatendatei|  
|`metadata-name`|string|Optional. Das XML-Element gibt den Namen der Metadaten für das Blob an, und sein Wert gibt den Wert der Metadateneinstellung an.|  
  
## <a name="properties-file-format"></a>Format der Eigenschaftendatei  
Das Format einer Eigenschaftendatei lautet wie folgt:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML-Element|Type|BESCHREIBUNG|  
|-----------------|----------|-----------------|  
|`Properties`|Stammelement|Das Stammelement der Eigenschaftendatei|  
|`Last-Modified`|string|Optional. Die Zeit der letzten Änderung des Blobs. Nur für Exportaufträge|  
|`Etag`|string|Optional. Der ETag-Wert des Blobs. Nur für Exportaufträge|  
|`Content-Length`|string|Optional. Die Größe des Blobs in Byte. Nur für Exportaufträge|  
|`Content-Type`|string|Optional. Der Inhaltstyp des Blobs|  
|`Content-MD5`|string|Optional. MD5-Hash des Blobs|  
|`Content-Encoding`|string|Optional. Inhaltscodierung des Blobs|  
|`Content-Language`|string|Optional. Inhaltssprache des Blob|  
|`Cache-Control`|string|Optional. Die Cachesteuerelement-Zeichenfolge für das Blob|  

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Regeln zum Festlegen der Blobmetadaten und -eigenschaften finden Sie unter [Festlegen von Blobeigenschaften](/rest/api/storageservices/set-blob-properties), [Festlegen von Blobmetadaten](/rest/api/storageservices/set-blob-metadata) und [Festlegen und Abrufen von Eigenschaften und Metadaten für Blobressourcen](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources).
