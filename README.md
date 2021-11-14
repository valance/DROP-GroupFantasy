# DROP-GroupFantasy

# Our Approach

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
All of the above keys should exist in the type2id.json file as it is the standard output entity type for ontonotes v5 data. But their corresponding id can be amended if you want to combine some classes together, for example, in the above setting, FAC, GPE, LANGUAGEm LAW, LOC, NORP, ORG, PERSON, PRODUCT and WORK_OF_ART type of entities are treated as the same classes. Intuitively, they all belong to the TEXT class while the remaining classes are more specific numerical classes of entity types.

# Error Analysis 

Due to the deficiency of the error analysis provided with the MTMSN model, which is based only on 100 randomy selected examples from the wrongly predicted data, we reconducted the error analysis on both the baseline model and our new model, using the predictions performed on the [cv_fold-4](dropdata/cv_fold-4.json) dataset. 

The corresponding predictions output can be found in [baseline_predictions.json](baseline_predictions.json) and [new_predictions.json](new_predictions.json). 

Among the cv_fold-4 dataset with size 3376, 1294 are span extraction problems, 73 are data identification problems, and 2009 are numerics problems. Our analysis results are shown below: 

<img src="https://github.com/valance/DROP-GroupFantasy/blob/main/baseline_error_analysis.png" width=400 height=1200/> <img src="https://github.com/valance/DROP-GroupFantasy/blob/main/new_error_analysis.png" width=400 height=1200/>. 


For the **span extraction problems**, we categorize the errors into the following four types:
- wrong type: the task predicted is not span extraction
- wrong span: the span(s) are wrongly identified
- missing span: the predicted answer contains less spans compared to the ground truth answer
- extra span: the predicted answer contains more spans compared to the ground truth answer

For the **date identification problems**, we categorize the errors into two types:
- wrong type: the task predicted is not date identification
- wrong date: the date is wrongly identified

For the **numerics problems**, we categorize the errors into the six types as listed below:
- wrong type: the task predicted is not numerics problem
- counting: counting problem
- negation: negation problem
- addition or subtraction: arithmetic operations of addition/subtraction
- wrong sign: the result is correct but in opposite sign, e.g. calculating the difference between A and B
- percentage calculation: calculation of percentage 

By comparing the error analysis pie charts, we can tell that though our model produces more extra-span errors which is not desirable, it is able to produce less wrong-span errors. Additionally, our model can better cope with the counting problems compared to the baseline model. 

