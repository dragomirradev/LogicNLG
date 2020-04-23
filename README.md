# LogicNLG
The data and code for ACL2020 paper [Logical Natural Language Generation from Open-Domain Tables](https://arxiv.org/abs/2004.10404), which aims to study the problem of natural language generation with logical inference in the intermediate steps. Going beyond simply surface-level copying, LogicNLG requires the model to deeply understand the content in the table and infer information implicitly expressed by the table.

<p align="center">
<img src="examples.png" width="400">
</p>

## Data
The data used for LogicNLG is provided in data/ folder, the details are described in [data](https://github.com/wenhuchen/LogicNLG/blob/master/data/README.md)

## Preparation
### Download the NLI scorer
```
wget https://logicnlg.s3-us-west-2.amazonaws.com/NLI_models.zip
unzip NLI_models.zip
```
### Download the Semantic Parser
```
wget https://logicnlg.s3-us-west-2.amazonaws.com/parser_models.zip
unzip parser_models.zip
```

## Evaluation Command
### Compute BLEU-1/2/3 score
```
python GPT2.py --do_test --load_from models/[Your_Model] --model gpt2
python GPT2-coarse-to-fine.py --do_test --load_from models/[Your_Model] --model gpt2
```

### Compute Adv-ACC score
```
python GPT2.py --do_verify --load_from models/[Your_Model] --model gpt2
python GPT2-coarse-to-fine.py --do_verify --load_from models/[Your_Model] --model gpt2 --stage 2
```

### Compute SP-ACC score
1. Parsing your output file into programs:
```
python parse_programs.py --parse --score_file outputs/[Your_File]
```
2. Run the ranker model to predict the entailment relationship:
```
CUDA_VISIBLE_DEVICES=0 python parse_programs.py --compute_score --load_from parser_models/parser_step49161_acc0.58.pt --score_file program_outputs/[Your_File]
```
### Compute NLI-ACC score
```
CUDA_VISIBLE_DEVICES=0 python NLI.py --model bert-base-multilingual-uncased --do_verify --encoding gnn --load_from NLI_models/model_ep4.pt --fp16 --verify_file outputs/[Your_File] --verify_linking data/test_lm.json
```
