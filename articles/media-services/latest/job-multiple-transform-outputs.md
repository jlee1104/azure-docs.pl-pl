---
title: Tworzenie zadania usługi Azure Media Services z wieloma wyjściami transformacji
description: W tym temacie pokazano, jak utworzyć zadanie usługi Azure Media Services z wieloma wyjściami transformacji.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2020
ms.author: juliako
ms.openlocfilehash: dbbeeb33ee46b37ec920fe598483c332d3439689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563143"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Tworzenie zadania z wieloma wyjściami transformacji

W tym temacie pokazano, jak utworzyć transformować z dwóch wyjść transformacji. Pierwszy z nich wymaga, aby dane wejściowe były kodowane do adaptacyjnego przesyłania strumieniowego bitrate z wbudowanym ustawieniem [adaptiveStreaming.](encoding-concept.md#builtinstandardencoderpreset) Drugi wymaga, aby sygnał audio w wejściowym wideo był przetwarzany za pomocą [audioanalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets). Po utworzeniu transformacji można przesłać zadanie, które odpowiednio przetworzy Twój film. Ponieważ w tym przykładzie określamy dwa wyjścia przekształcania, musimy określić dwa wyjścia zadania. Można skierować oba wyjścia zadań do tego samego zasobu (jak pokazano poniżej) lub mieć wyniki zapisywane w oddzielnych zasobach.
 

> [!TIP]
> Przed rozpoczęciem tworzenia, przegląd [tworzenie za pomocą interfejsów API usługi Media Services w wersji 3](media-services-apis-overview.md) (zawiera informacje na temat uzyskiwania dostępu do interfejsów API, konwencje nazewnictwa, itp.)

## <a name="create-a-transform"></a>Tworzenie przekształcenia

Poniższy kod pokazuje, jak utworzyć transformację, która produkuje dwa wyjścia.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>Przesyłanie zadania

Utwórz zadanie z wejściem adresu URL HTTPS i dwoma wyjściami zadań.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Kody błędów zadań

Zobacz [Kody błędów](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Następne kroki

[Przykłady usługi Azure Media Services w wersji 3 przy użyciu platformy .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
