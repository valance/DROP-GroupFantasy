# DROP-GroupFantasy

We list four folders:
- **MTMSN_new_embedding** for our version of MTMSN model (it has an additional embedding - type embedding)
- **data_statistics** for our code and result for data_statistics
- **dropdata** for generating drop data with cross validation
- **error_analysis** for our code and result for error_analysis - in which you can find a detailed description

# Our Approach

The codes for training with a additional input embeddings generated from an external NER model (i.e. corenlp) is on the MTMSN_new_embedding folder. 

The trained model checkpoint is available in the mtmsn_base folder in the aforementioned repo. However, this is not the finalized one. Because of the long training time, even longer time is need for parameters exploration. The checkpoint trained with new setting given in the MTMSN_new_embedding/mtmsn_base fold is the first attempt (but failed as we just keep using the same parameters setting as training the baseline). 

In addition, we assumed the best parameters for training the baseline is the same as the one given in the author github repo. We replicated the training and give a baseline checkpoint in the MTMSN_new_embedding/mtmsn_base folder also. This is trained only by pretrained_split.json. For the training of CV, if retaining the same parameters setting, the model cannot be successfully trained. After parameters exploration, we found a different learning rate can help and thus we opt for a smaller learning rate of 1e-5. As getting the result of CV, additional 5 checkpoints is generated, you can contact ttchungac@connect.ust.hk if you want them but because consideration of drive storage, they are not available for download. The CV result is (1/5)(61.027+?+63.747+68.552+65.318) for running 10 epoches. We have also try to run more epoches, the performance can actually be further increase. Because of the time limitation, we did not experiment through all 5 cv model and the potential performance is expected to better than the cv result shown here.

For our new attempt in embeddings modification, this is still ongoing, improvement over the baseline model is acheive for current attempted parameters setting. But as it is still ongoing (even training the cv for our current best model need a lot of time), the result is available before 14/11 (code and result submission deadline of COMP5222). Thanks for your understanding.

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
All of the above keys should exist in the type2id.json file as it is the standard output entity type for ontonotes v5 data. But their corresponding id can be amended if you want to combine some classes together, for example, in the above setting, FAC, GPE, LANGUAGEm LAW, LOC, NORP, ORG, PERSON, PRODUCT and WORK_OF_ART type of entities are treated as the same classes. Intuitively, they all belong to the TEXT class while the remaining classes are more specific numerical classes of entity types.

