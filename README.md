# Learning Knowledge Bases with Parameters for Task-Oriented Dialogue Systems
<img src="plot/pytorch-logo-dark.png" width="10%"> [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT) 

<img align="right" src="plot/HKUST.jpg" width="12%">

This is the implementation of the paper:

**Learning Knowledge Bases with Parameters for Task-Oriented Dialogue Systems**. [**Andrea Madotto**](https://andreamad8.github.io), [Samuel Cahyawijaya](https://samuelcahyawijaya.github.io/), [Genta Indra Winata](https://gentawinata.com/), Yan Xu, [Zihan Liu](https://zliucr.github.io/), [Zhaojiang Lin](https://zlinao.github.io/), Pascale Fung **Findings of EMNLP 2020** [[PDF]](https://arxiv.org/pdf/2009.13656.pdf)

If you use any source codes or datasets included in this toolkit in your work, please cite the following paper. The bibtex is listed below:
<pre>
@article{madotto2020learning,
  title={Learning Knowledge Bases with Parameters for Task-Oriented Dialogue Systems},
  author={Madotto, Andrea and Cahyawijaya, Samuel and Winata, Genta Indra and Xu, Yan and Liu, Zihan and Lin, Zhaojiang and Fung, Pascale},
  journal={arXiv preprint arXiv:2009.13656},
  year={2020}
}
</pre>

## Abstract
Task-oriented dialogue systems are either modularized with separate dialogue state tracking (DST) and management steps or end-to-end trainable. In either case, the knowledge base (KB) plays an essential role in fulfilling user requests. Modularized systems rely on DST to interact with the KB, which is expensive in terms of annotation and inference time. End-to-end systems use the KB directly as input, but they cannot scale when the KB is larger than a few hundred entries. In this paper, we propose a method to embed the KB, of any size, directly into the model parameters. The resulting model does not require any DST or template responses, nor the KB as input, and it can dynamically update its KB via finetuning. We evaluate our solution in five taskoriented dialogue datasets with small, medium, and large KB size. Our experiments show that end-to-end models can effectively embed knowledge bases in their parameters and achieve competitive performance in all evaluated datasets.

## Knowledge-embedded Dialogue:
<p align="center">
<img src="plot/main.png" width="50%" />
</p>
During training, the KE dialogues are generated by fulfilling the *TEMPLATE* with the *user goal query* results, and they are used to embed the KB into the model parameter theta. At testing time, the model does not use any external knowledge to generate the correct responses.

## Dependencies
We listed our dependencies on `requirements.txt`, you can install the dependencies by running
``` console
❱❱❱ pip install -r requirements.txt
```

In addition, our code also includes `fp16` support with `apex`. You can find the package from https://github.com/NVIDIA/apex.

## Experiments
### bAbI-5
***Dataset***
Download the preprocessed [**dataset**](https://drive.google.com/file/d/1sARfILgqVeqY7sTkkjX2MLHeWhp0q-_L/view?usp=sharing) and put the zip file inside the `./knowledge_embed/babi5` folder. Extract the zip file by executing

```console
❱❱❱ cd ./knowledge_embed/babi5
❱❱❱ unzip dialog-bAbI-tasks.zip
```

Generate the delexicalized dialogues from bAbI-5 dataset via
```console
❱❱❱ python3 generate_delexicalization_babi.py
```

Generate the lexicalized data from bAbI-5 dataset via
```console
❱❱❱ python generate_dialogues_babi5.py --dialogue_path ./dialog-bAbI-tasks/dialog-babi-task5trn_record-delex.txt --knowledge_path ./dialog-bAbI-tasks/dialog-babi-kb-all.txt --output_folder ./dialog-bAbI-tasks --num_augmented_knowledge <num_augmented_knowledge> --num_augmented_dialogue <num_augmented_dialogues> --random_seed 0
```

Where the maximum `<num_augmented_knowledge>` is 558 (recommended) and `<num_augmented_dialogues>` is 264 as it is corresponds to the number of knowledge and number of dialogues in bAbI-5 dataset.

***Fine-tune GPT-2***

We provide the [**checkpoint**](TODO) of GPT-2 model fine-tuned on bAbI training set. You can also choose to train the model by yourself using the following command.

```console
❱❱❱ cd ./modeling/babi5
❱❱❱ python main.py --model_checkpoint gpt2 --dataset BABI --dataset_path ../../knowledge_embed/babi5/dialog-bAbI-tasks --n_epochs 10 --kbpercentage <num_augmented_dialogues>
```

Notes that the value of `--kbpercentage` is equal to `<num_augmented_dialogues>` the one that comes from the lexicalization. This parameter is used for selecting the augmentation file to embed into the train dataset.

### CamRest
***Dataset***

Create directory ``

Download the preprocessed [**dataset**](https://drive.google.com/file/d/1rmKB4RUJCHrlbqAuxXbcLLjQiPxAq4qq/view?usp=sharing) and put the zip file under `./knowledge_embed/camrest` folder. Unzip the zip file by executing

```console
❱❱❱ cd ./knowledge_embed/camrest
❱❱❱ unzip CamRest.zip
```

Generate the delexicalized dialogues from CamRest dataset via
```console
❱❱❱ python3 generate_delexicalization_CAMREST.py
```

Generate the lexicalized data from CamRest dataset via
```console
❱❱❱ python generate_dialogues_CAMREST.py --dialogue_path ./CamRest/train_record-delex.txt --knowledge_path ./CamRest/KB.json --output_folder ./CamRest --num_augmented_knowledge <num_augmented_knowledge> --num_augmented_dialogue <num_augmented_dialogues> --random_seed 0
```

***Fine-tune GPT-2***

We provide the [**checkpoint**](TODO) of GPT-2 model fine-tuned on CamRest training set. You can also choose to train the model by yourself using the following command.


```console
❱❱❱ cd ./modeling/camrest/
❱❱❱ python main.py --model_checkpoint gpt2 --dataset CAMREST
```

### SMD 
***Dataset***

Download the preprocessed [**dataset**](https://drive.google.com/open?id=1p5FgDcXYPp3s0MzQSbAi-ixqRxNhtfXX) and put it under `./knowledge_embed/smd` folder.

```console
❱❱❱ cd ./knowledge_embed/smd
❱❱❱ unzip SMD.zip
```

***Fine-tune GPT-2***

We provide the [**checkpoint**](https://drive.google.com/open?id=1v7P-UulLBTwdW2EIk2Jz0vdPKlpZlLUI) of GPT-2 model fine-tuned on SMD training set. Download the checkpoint and put it under `./modeling` folder.

```console
❱❱❱ cd ./knowledge_embed/smd
❱❱❱ mkdir ./runs
❱❱❱ unzip ./knowledge_embed/smd/SMD_gpt2_graph_False_adj_False_edge_False_unilm_False_flattenKB_False_historyL_1000000000_lr_6.25e-05_epoch_10_weighttie_False_kbpercentage_0_layer_12.zip -d ./runs
```

You can also choose to train the model by yourself using the following command.

```console
❱❱❱ cd ./modeling/smd
❱❱❱ python main.py --dataset SMD --lr 6.25e-05 --n_epochs 10 --kbpercentage 0 --layers 12
```

***Prepare Knowledge-embedded dialogues***

Firstly, we need to build databases for SQL query.

```console
❱❱❱ cd ./knowledge_embed/smd
❱❱❱ python generate_dialogues_SMD.py --build_db --split test
```

Then we generate dialogues based on pre-designed templates by domains. The following command enables you to generate dialogues in `weather` domain. Please replace `weather` with `navigate` or `schedule` in `dialogue_path` and `domain` arguments if you want to generate dialogues in the other two domains. You can also change number of templates used in relexicalization process by changing the argument `num_augmented_dialogue`.

``` console
❱❱❱ python generate_dialogues_SMD.py --split test --dialogue_path ./templates/weather_template.txt --domain weather --num_augmented_dialogue 100 --output_folder ./SMD/test
```

***Adapt fine-tuned GPT-2 model to the test set***

``` console
❱❱❱ python evaluate_finetune.py --dataset SMD --model_checkpoint runs/SMD_gpt2_graph_False_adj_False_edge_False_unilm_False_flattenKB_False_historyL_1000000000_lr_6.25e-05_epoch_10_weighttie_False_kbpercentage_0_layer_12 --top_k 1 --eval_indices 0,303 --filter_domain ""
```

You can also speed up the finetuning process by running experiments parallelly. Please modify the GPU setting in #L14 of the [code](https://github.com/HLTCHKUST/ke-dialogue/blob/master/modeling/smd/runner_expe_SMD.py#L14).

``` console
❱❱❱ python runner_expe_SMD.py 
```

### MWOZ (2.1)
***Dataset***

Download the preprocessed [**dataset**](https://drive.google.com/file/d/1KdoVWqyYhpqEE8BzVDHMAjGUvl74mhUs) and put it under `./knowledge_embed/mwoz` folder.

```console
❱❱❱ cd ./knowledge_embed/mwoz
❱❱❱ unzip mwoz.zip
```

***Prepare Knowledge-Embedded dialogues (You can skip this step, if you have downloaded the zip file above)***

You can prepare the datasets by running
```console
❱❱❱ bash generate_MWOZ_all_data.sh
```

The shell script generates the delexicalized dialogues from MWOZ dataset by calling
```console
❱❱❱ python generate_delex_MWOZ_ATTRACTION.py
❱❱❱ python generate_delex_MWOZ_HOTEL.py
❱❱❱ python generate_delex_MWOZ_RESTAURANT.py
❱❱❱ python generate_delex_MWOZ_TRAIN.py
❱❱❱ python generate_redelex_augmented_MWOZ.py
❱❱❱ python generate_MWOZ_dataset.py
```

***Fine-tune GPT-2***

We provide the [**checkpoint**](https://drive.google.com/file/d/1sjhP4TMtR_wmWB9kuzCIJnETL5J1S9BL) of GPT-2 model fine-tuned on MWOZ training set. Download the checkpoint and put it under `./modeling` folder.

```console
❱❱❱ cd ./knowledge_embed/mwoz
❱❱❱ mkdir ./runs
❱❱❱ unzip ./mwoz.zip -d ./runs
```

You can also choose to train the model by yourself using the following command.

```console
❱❱❱ cd ./modeling/mwoz
❱❱❱ python main.py --model_checkpoint gpt2 --dataset MWOZ_SINGLE --max_history 50 --train_batch_size 6 --kbpercentage 100 --fp16 O2 --gradient_accumulation_steps 3 --balance_sampler --n_epochs 10
```

### OpenDialKG


## Further Details
For the details regarding to the experiments, hyperparameters, and Evaluation results you can find it in the main paper of and suplementary materials of our work.





