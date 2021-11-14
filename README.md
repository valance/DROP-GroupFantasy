# DROP-GroupFantasy

We list four folders:
- **MTMSN_new_embedding** for our version of MTMSN model (it has an additional embedding - type embedding)
- **data_statistics** for our code and result for data_statistics
- **dropdata** for generating drop data with cross validation
- **error_analysis** for our code and result for error_analysis - in which you can find a detailed description

# Our Approach

Folder **MTMSN_new_embedding** includes the code and result of the MTMSN model of our version, which has an additional input embedding, generated from an external NER model (i.e. coreNLP). 

The subfolder **mtmsn_base** includes the trained model checkpoint (the new model). <br>
However, this is not the finalized version. Because of the long training time, we need even longer time for parameter exploration. <br>
The checkpoint trained with the new setting (given in the **MTMSN_new_embedding/mtmsn_base** folder) is the first attempt, but it is not so successful since we just kept the same parameter setting the MTMSN baseline model. 

The subfolder **mtmsn_base** also includes the original MTMSN baseline model, which is trained on **pretrain_split.json**. <br>
We assumed the best parameters for training the baseline is the same as the one given in the author github repo. <br>
For the Cross Validation, if one retains the same parameters setting, the model cannot be successfully trained. <br>
After parameters exploration, we found a different learning rate can help and thus we opt for a smaller learning rate of 1e-5. 

For getting the result of CV, additional 5 checkpoints is generated, you can contact ttchungac@connect.ust.hk if you want them but considering the driver storage limit, they are not available for download. 

The CV result is (1/5)(61.027+?+63.747+68.552+65.318) for running 10 epoches. We have also try to run more epoches, the performance can actually be further increased. Because of the time limitation, we did not experiment through all 5 cv model and the potential performance is expected to better than the cv result shown here.

For our new attempt in embeddings modification, this is still ongoing, improvement over the baseline model is acheive for current attempted parameters setting. But as it is still ongoing (even training the cv for our current best model need a lot of time), the result is not available before 14/11 (code and result submission deadline of COMP5222). Thanks for your understanding.

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

