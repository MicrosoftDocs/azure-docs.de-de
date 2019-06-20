---
title: H264 Single Bitrate 720p für Android | Microsoft Docs
description: Das Thema enthält eine Übersicht über die Aufgabenvoreinstellung **H264 Single Bitrate 720p für Android**.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4f9569a3-5aca-4fea-8242-024925a8af90
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: da44cf33882d2658b20f117053d486177117a5a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61131017"
---
# <a name="h264-single-bitrate-720p-for-android"></a>H264 Single Bitrate 720p für Android
`Media Encoder Standard` definiert eine Reihe von Codierungsvoreinstellungen, die Sie beim Erstellen von Codierungsaufträgen verwenden können. Sie können mithilfe von `preset name` angeben, in welchem Format Ihre Mediendatei codiert werden soll. Oder Sie erstellen eigene JSON- oder XML-basierte Voreinstellungen (mithilfe von UTF-8- oder UTF-16-Codierung). In diesem Fall übergeben Sie die benutzerdefinierte Voreinstellung dann an den Encoder. Eine Liste aller von diesem `Media Encoder Standard`-Encoder unterstützten Voreinstellungsnamen finden Sie unter [Task Presets for Media Encoder Standard](media-services-mes-presets-overview.md) (Aufgabenvoreinstellungen für Media Encoder Standard).  
  
In diesem Thema wird die Voreinstellung `H264 Single Bitrate 720p for Android` im XML- und JSON-Format gezeigt.  
  
Diese Voreinstellung erzeugt eine einzelne MP4-Datei mit einer Bitrate von 2.000KBit/s und AAC-Stereo. Ausführliche Informationen zu Profil, Bitrate, Samplingrate usw. dieser Voreinstellung finden Sie im unten aufgeführten XML- bzw. JSON-Code. Erläuterungen zur Bedeutung der einzelnen Elemente in diesen Voreinstellungen sowie gültige Werte für jedes Element finden Sie im Thema [Media Encoder Standard schema](media-services-mes-schema.md) (Media Encoder Standard-Schema).  
  
 XML  
  
```
<?xml version="1.0" encoding="utf-16"?>
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
  <Encoding>
    <H264Video>
      <KeyFrameInterval>00:00:05</KeyFrameInterval>
      <SceneChangeDetection>true</SceneChangeDetection>
      <H264Layers>
        <H264Layer>
          <Bitrate>2000</Bitrate>
          <Width>1280</Width>
          <Height>720</Height>
          <FrameRate>0/1</FrameRate>
          <Profile>Baseline</Profile>
          <Level>3.1</Level>
          <BFrames>0</BFrames>
          <ReferenceFrames>3</ReferenceFrames>
          <Slices>0</Slices>
          <AdaptiveBFrame>false</AdaptiveBFrame>
          <EntropyMode>Cavlc</EntropyMode>
          <BufferWindow>00:00:05</BufferWindow>
          <MaxBitrate>2000</MaxBitrate>
        </H264Layer>
      </H264Layers>
      <Chapters />
    </H264Video>
    <AACAudio>
      <Profile>AACLC</Profile>
      <Channels>2</Channels>
      <SamplingRate>48000</SamplingRate>
      <Bitrate>192</Bitrate>
    </AACAudio>
  </Encoding>
  <Outputs>
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
      <MP4Format />
    </Output>
  </Outputs>
</Preset>
```
  
 JSON  
  
```
{
  "Version": 1.0,
  "Codecs": [
    {
      "KeyFrameInterval": "00:00:05",
      "SceneChangeDetection": true,
      "H264Layers": [
        {
          "Profile": "Baseline",
          "Level": "3.1",
          "Bitrate": 2000,
          "MaxBitrate": 2000,
          "BufferWindow": "00:00:05",
          "Width": 1280,
          "Height": 720,
          "ReferenceFrames": 3,
          "EntropyMode": "Cavlc",
          "Type": "H264Layer",
          "FrameRate": "0/1"
        }
      ],
      "Type": "H264Video"
    },
    {
      "Profile": "AACLC",
      "Channels": 2,
      "SamplingRate": 48000,
      "Bitrate": 192,
      "Type": "AACAudio"
    }
  ],
  "Outputs": [
    {
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
      "Format": {
        "Type": "MP4Format"
      }
    }
  ]
}
```
