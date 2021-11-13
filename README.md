# DROP-GroupFantasy
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

0: sally
1: valance
2: alex
3, 4: cindy 

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
