# DROP-GroupFantasy

load checkpoint:
'''
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
'''

new.bin is the init_checkpoint in the running command
