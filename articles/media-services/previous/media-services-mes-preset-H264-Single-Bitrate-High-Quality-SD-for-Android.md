---
title: H264 Single Bitrate Wysokiej jakości SD dla Androida | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono omówienie **zestawu H264 Single Bitrate High Quality SD dla systemu Android.**
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 4a190f24-ec6a-47e7-8bca-6294e064b15b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 217c4874f0375aeb4d80162af1b8453a3f7f625f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463518"
---
# <a name="h264-single-bitrate-high-quality-sd-for-android"></a>Pojedyncza szybkość transmisji bitów H264 SD wysokiej jakości dla systemu Android
`Media Encoder Standard`definiuje zestaw ustawień predefiniowanych kodowania, których można używać podczas tworzenia zadań kodowania. Można użyć a, `preset name` aby określić, w jakim formacie chcesz zakodować plik multimedialny. Można też utworzyć własne ustawienia predefiniowane oparte na języku JSON lub XML (przy użyciu kodowania UTF-8 lub UTF-16. Następnie należy przekazać niestandardowe ustawienia predefiniowane do kodera. Aby uzyskać listę wszystkich predefiniowanych `Media Encoder Standard` nazw obsługiwanych przez ten koder, zobacz [Presety zadań dla standardu kodera multimediów](media-services-mes-presets-overview.md).  
  
 W tym `H264 Single Bitrate High Quality SD for Android` temacie przedstawiono ustawienia predefiniowane w formacie XML i JSON.  
  
 To ustawienie wstępne tworzy pojedynczy plik MP4 o szybkości transmisji bitów 500 kb/s i stereofoniczny dźwięk AAC. Aby uzyskać szczegółowe informacje na temat profilu, szybkości transmisji bitów, częstotliwości próbkowania itp. Aby uzyskać wyjaśnienia, co oznacza każdy element w tych ustawieniach predefiniowanych i prawidłowe wartości dla każdego elementu, zobacz temat [schematu Standardowy koder nośnika.](media-services-mes-schema.md)  
  
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
          <Bitrate>500</Bitrate>  
          <Width>480</Width>  
          <Height>360</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Baseline</Profile>  
          <Level>3</Level>  
          <BFrames>0</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>false</AdaptiveBFrame>  
          <EntropyMode>Cavlc</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>500</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>2</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>128</Bitrate>  
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
          "Level": "3",  
          "Bitrate": 500,  
          "MaxBitrate": 500,  
          "BufferWindow": "00:00:05",  
          "Width": 480,  
          "Height": 360,  
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
      "Bitrate": 128,  
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
