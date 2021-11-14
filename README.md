# DROP-GroupFantasy

The coding for training with a additional input embeddings generated from an external NER model (i.e. corenlp) is on https://github.com/KanaCS/MTMSN. 

The trained model checkpoint is available in the mtmsn_base folder in the aforementioned repo. However, this is not the finalized one. Because of the long training time, even longer time is need for parameters exploration. The checkpoint trained with new setting given in the mtmsn_base fold is the first attempt (but failed as we just keep using the same parameters setting as training the baseline). 

In addition, we assumed the best parameters for training the baseline is the same as the one given in the author github repo. We replicated the training and give a baseline checkpoint in the mtmsn_base folder also. This is trained only by pretrained_split.json. As getting the result of CV, additional 5 checkpoints is generated, you can contact ttchungac@connect.ust.hk if you want them but because consideration of drive storage, they are not available for download. The result of CV is (1/5)(+++++) = .

For our new attempt in embeddings modification, this is still ongoing, improvement over the baseline model is acheive for current attempted parameters setting. But as it is still ongoing (even training the cv for our current best model need a lot of time), the result is not shown before 14/11 (code and result submission deadline of COMP5222). Thanks for your understanding.

For our new approach, the training command is different and a additional input file is needed,
Training command:
```
python -m bert.run_mtmsn \
  --vocab_file $BERT_DIR/vocab.txt \
  --bert_config_file $BERT_DIR/config.json \
  --init_checkpoint $BERT_DIR/pytorch_model.bin \
  --do_train \
  --do_predict \
  --do_lower_case \
  --train_file $DATA_DIR/drop_dataset_train.json \
  --predict_file $DATA_DIR/drop_dataset_dev.json \
  --train_batch_size 12 \
  --predict_batch_size 24 \
  --num_train_epochs 10.0 \
  --learning_rate 3e-5 \
  --max_seq_length 512 \
  --span_extraction \
  --addition_subtraction \
  --counting \
  --negation \
  --gradient_accumulation_steps 2 \
  --output_dir out/mtmsn_base
  --type_file type2id.json
  ```
  
  A example of the additional type_file type2id.json,
  ```
  {"NONE": 0, "CARDINAL": 1, "DATE": 2, "EVENT": 3, "FAC": 3, "GPE": 3, "LANGUAGE": 3, "LAW": 3, "LOC": 3, "MONEY": 4, "NORP": 3, "ORDINAL": 5, "ORG": 3, "PERCENT": 6, "PERSON": 3, "PRODUCT": 3, "QUANTITY": 7, "TIME": 8, "WORK_OF_ART": 3}
  ```
All of the above keys should exists in the type2id.json file as it is the standard output entity type for ontonotes v5 data. But their corresponding id can be amend if you want to combine some classes together, for example, in the above setting, FAC, GPE, LANGUAGEm LAW, LOC, NORP, ORG, PERSON, PRODUCT and WORK_OF_ART type of entities are treated as the same classes. Intuitively, they are all belong to the TEXT class while the remaining classes are more specific numerical classes of entity types.

Environment 
```
!pip install -U scikit-learn==0.21
!pip install stanza
!pip install torch==1.1.0 torchvision==0.3.0
!pip install Allennlp==0.8.4
!pip install overrides==3.1.0
```
Modification on run_mtmsn.py:
```
import sys
sys.path.append('master_folder_path') # master folder = the folder contains drop,bert,...
```
To get the exact path,try to print the current sys.path and find the one that leads to your master folder.
```
import sys
print(sys.path)
```
Set dictionary to your master folder ( you can upload files to G drive and link your colab to your G drive)
```
%cd /content/.....
!pwd # show your current dictionary
```
load checkpoint:
```
from bert.modeling import BertConfig
bert_config = BertConfig.from_json_file('bert_config.json') #if you follow this code, put bert_config.json in your master folder

from bert.modeling_drop import MTMSN
import torch
from collections import OrderedDict
checkpoint = torch.load('checkpoint.pth.tar',map_location=torch.device('cpu'))
model_cp = []
for i in checkpoint['model']:
    model_cp.append(('.'.join(i.split('.')[1:]),checkpoint['model'][i]))
model_cp = OrderedDict(model_cp)
torch.save(model_cp, 'new.bin')
```

new.bin is the init_checkpoint in the running command

(optional)cv training command:
```
export BERT_DIR=bert-base-uncased
```
while your cd is at master folder, (if you follow this code, put bert_config.json and vocab.txt in your master folder)
```
!python bert/run_mtmsn.py \
  "--vocab_file" vocab.txt \ 
  "--bert_config_file" bert_config.json \
  "--init_checkpoint" new.bin \
  "--do_train" \
  "--do_predict" \
  "--do_lower_case" \
  "--train_file" dropdata/leave-2.json \
  "--predict_file" dropdata/cv_fold-2.json \
  "--train_batch_size" 12 \
  "--predict_batch_size" 24 \
  "--num_train_epochs" 15.0 \
  "--learning_rate" 1e-5 \
  "--max_seq_length" 512 \
  "--span_extraction" \
  "--addition_subtraction" \
  "--counting" \
  "--negation" \
  "--gradient_accumulation_steps" 1 \
  "--output_dir" out/mtmsn_base
```
