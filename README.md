# DROP-GroupFantasy

load checkpoint:
```
from bert.modeling_drop import MTMSN
import torch
from collections import OrderedDict
checkpoint = torch.load('18/checkpoint.pth.tar',map_location=torch.device('cpu'))
model_cp = []
for i in checkpoint['model']:
    model_cp.append(('.'.join(i.split('.')[1:]),checkpoint['model'][i]))
model_cp = OrderedDict(model_cp)
model = MTMSN(bert_config)
torch.save(model_cp, 'new.bin')
```

new.bin is the init_checkpoint in the running command

0: sally
1: valance
2: alex
3, 4: cindy 

python -m bert.run_mtmsn \
  --vocab_file $BERT_DIR/vocab.txt \
  --bert_config_file $BERT_DIR/config.json \
  --init_checkpoint new.bin \
  --do_train \
  --do_predict \
  --do_lower_case \
  --train_file dropdata/leave-<id>.json \
  --predict_file dropdata/cv_fold-<id>.json \
  --train_batch_size 12 \
  --predict_batch_size 24 \
  --num_train_epochs 15.0 \
  --learning_rate 1e-5 \
  --max_seq_length 512 \
  --span_extraction \
  --addition_subtraction \
  --counting \
  --negation \
  --gradient_accumulation_steps 1 \
  --output_dir out/mtmsn_base
