---
title: Flytta data i ML-pipelines
titleSuffix: Azure Machine Learning
description: Läs om indata & datautdata i Azure Machine Learning pipeliner.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.custom: contperfq4
ms.openlocfilehash: 233361fb238342cde3c692174e85fb57f69979b1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858466"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Flytta data till och mellan olika steg i ML-pipelinen (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Den här artikeln innehåller kod för att importera, transformera och flytta data mellan stegen i en Azure Machine Learning pipeline. En översikt över hur data fungerar i Azure Machine Learning finns i [få åtkomst till data i Azure Storage-tjänster](how-to-access-data.md). För fördelarna och strukturen i Azure Machine Learning pipelines, se [Vad är Azure Machine Learning pipelines?](concept-ml-pipelines.md).

Den här artikeln visar hur du kan:

- Använd `Dataset` objekt för tidigare befintliga data
- Få åtkomst till data i dina steg
- Dela `Dataset` upp data i del mängder, till exempel inlärnings-och validerings under uppsättningar
- Skapa `PipelineData` objekt för att överföra data till nästa pipeline-steg
- Använd `PipelineData` objekt som inmatade steg i pipeline-steg
- Skapa nya `Dataset` objekt som `PipelineData` du vill behålla

## <a name="prerequisites"></a>Krav

Du behöver:

- En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett kostnadsfritt konto innan du börjar. Prova den [kostnads fria eller betalda versionen av Azure Machine Learning](https://aka.ms/AMLFree).

- [Azure Machine Learning SDK för python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)eller åtkomst till [Azure Machine Learning Studio](https://ml.azure.com/).

- En Azure Machine Learning-arbetsyta.
  
  [Skapa antingen en Azure Machine Learning arbets yta](how-to-manage-workspace.md) eller Använd en befintlig via python SDK. Importera `Workspace` och `Datastore` -klassen och Läs in din prenumerations information från filen `config.json` med hjälp av `from_config()`funktionen. Den här funktionen söker efter JSON-filen i den aktuella katalogen som standard, men du kan också ange en Sök vägs parameter som pekar på filen `from_config(path="your/file/path")`med hjälp av.

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Vissa redan befintliga data. Den här artikeln visar kortfattat användningen av en [Azure Blob-behållare](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Valfritt: en befintlig maskin inlärnings pipeline, till exempel den som beskrivs i [skapa och köra Machine Learning-pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Använd `Dataset` objekt för tidigare befintliga data 

Det bästa sättet att mata in data i en pipeline är att använda ett [data mängds](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) objekt. `Dataset`objekt representerar beständiga data som är tillgängliga i en arbets yta.

Det finns många sätt att skapa och registrera `Dataset` objekt. Tabell data uppsättningar är för avgränsade data tillgängliga i en eller flera filer. Fil data uppsättningar är för binära data (till exempel bilder) eller för data som du ska parsa. De enklaste sätten att skapa `Dataset` objekt är att använda befintliga blobbar i lagrings utrymmen för arbets ytor eller offentliga URL: er:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

För fler alternativ för att skapa data uppsättningar med olika alternativ och från olika källor, registrera dem och granska dem i Azure Machine Learning användar gränssnitt, förstå hur data storleken interagerar med beräknings kapacitet och hur de versioner kan hanteras, se [skapa Azure Machine Learning data uppsättningar](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Skicka data uppsättningar till ditt skript

Om du vill överföra data uppsättningens sökväg till skriptet använder du `Dataset` objektets `as_named_input()` metod. Du kan antingen skicka det resulterande `DatasetConsumptionConfig` objektet till ditt skript som ett argument, eller genom att `inputs` använda argumentet till ditt pipeline-skript, kan du hämta data uppsättningen `Run.get_context().input_datasets[]`med hjälp av.

När du har skapat en namngiven indatamängd kan du välja dess åtkomst läge: `as_mount()` eller `as_download()`. Om skriptet bearbetar alla filer i data uppsättningen och disken på beräknings resursen är tillräckligt stor för data uppsättningen är nedladdnings åtkomst läget det bästa valet. Nedladdnings åtkomst läget gör att du slipper överföra data direkt vid körning. Om ditt skript har åtkomst till en delmängd av data uppsättningen eller om den är för stor för din beräkning, använder du monterings åtkomst läge. Mer information finns i [montering jämfört med nedladdning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Skicka en data uppsättning till pipeline-steget:

1. Använd `TabularDataset.as_named_inputs()` eller `FileDataset.as_named_input()` (ingen) i slutet för att skapa ett `DatasetConsumptionConfig` objekt
1. Använda `as_mount()` eller `as_download()` för att ange åtkomst läge
1. Skicka data uppsättningarna till dina pipeline-steg med antingen `arguments` `inputs` argumentet eller

I följande kodfragment visas ett gemensamt mönster för att kombinera de här stegen `PythonScriptStep` i konstruktorn: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Du kan också använda metoder som `random_split()` och `take_sample()` för att skapa flera indata eller minska mängden data som skickas till pipeline-steget:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Få åtkomst till data uppsättningar i skriptet

Namngivna indata till ditt pipeline-steg skript är tillgängliga som en ord lista `Run` i objektet. Hämta det aktiva `Run` objektet med `Run.get_context()` och hämta sedan ord listan med namngivna indata `input_datasets`med hjälp av. Om du har skickat `DatasetConsumptionConfig` objektet med hjälp `arguments` av argumentet i stället `inputs` för argumentet, kan du komma `ArgParser` åt data med hjälp av kod. Båda metoderna visas i följande kodfragment.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

Det skickade värdet är sökvägen till data uppsättnings fil (er).

Det går också att komma åt en registrerad `Dataset` direkt. Eftersom registrerade data uppsättningar är beständiga och delade i en arbets yta kan du hämta dem direkt:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Använd `PipelineData` för mellanliggande data

Objekt representerar beständiga data, och PipelineData-objekt används för temporära data som är utdata från pipeline-steg. [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) `Dataset` Eftersom livs längd för ett `PipelineData` objekt är längre än ett enda pipeline-steg, definierar du dem i definitions skriptet för pipelinen. När du skapar ett `PipelineData` objekt måste du ange ett namn och ett data lager där data ska finnas. Skicka dina `PipelineData` objekt till din `PythonScriptStep` användning med _både_ - `arguments` och- `outputs` argumenten:

```python
default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)
```

Du kan välja att skapa ett `PipelineData` objekt med ett åtkomst läge som ger en omedelbar uppladdning. I så fall `PipelineData`ställer du in `upload_mode` till `"upload"` och använder `output_path_on_compute` argumentet för att ange sökvägen till vilken du ska skriva data när du skapar.

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Använd `PipelineData` som utdata i ett utbildnings steg

I din pipeline `PythonScriptStep`kan du hämta tillgängliga sökvägar med hjälp av programmets argument. Om det här steget är det första och kommer att initiera utdata måste du skapa katalogen på den angivna sökvägen. Du kan sedan skriva de filer som du vill ska ingå i `PipelineData`.

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Om du har skapat `PipelineData` din med `is_directory` argumentet inställt på `True`, är det tillräckligt för att bara `os.makedirs()` utföra anropet och sedan är du kostnads fri att skriva de filer som du vill ha i sökvägen. Mer information finns i referens dokumentationen för [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) .

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Läs `PipelineData` som indata till icke-inledande steg

När det första pipeline-steget skriver data till `PipelineData` sökvägen och det blir utdata från det inledande steget, kan det användas som indata till ett senare steg:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

Värdet för `PipelineData` indata är sökvägen till föregående utdata. Om det första steget som visas tidigare skrev en enskild fil kan det se ut så här: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Konvertera `PipelineData` objekt till `Dataset`s

Om du vill göra din `PipelineData` tillgänglig längre än körnings tiden använder du dess `as_dataset()` funktion för att konvertera den till en. `Dataset` Du kan sedan registrera dig `Dataset`, vilket gör den till första klassens medborgare i din arbets yta. Eftersom ditt `PipelineData` objekt har en annan sökväg varje gång pipelinen körs, rekommenderar vi starkt att du ställer `create_new_version` in på `True` när du registrerar en `Dataset` skapad från ett `PipelineData` objekt.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Machine Learning-datauppsättning](how-to-create-register-datasets.md)
* [Skapa och kör maskin inlärnings pipeliner med Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
