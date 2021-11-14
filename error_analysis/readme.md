# Contents

This is a description for our error analysis code and result.

**error_analysis.ipynb** is the code to output error analysis result.

**baseline_error_analysis.png** and **new_error_analysis.png** are the result.


# Error Analysis 

The original error analysis of the MTMSN model is based on only 100 randonly selected examples, from the wrongly predicted data. Because this is a subset only, we have re-conducted the error analysis on all the wrongly predicted data, for both the baseline MTMSN model and our model (which has an additional input embedding). The data used for error analysis is the predictions performed on the [cv_fold-4](dropdata/cv_fold-4.json) dataset. The corresponding predictions output can be found in [baseline_predictions.json](../baseline_predictions.json) and [new_predictions.json](../new_predictions.json). 

Among the cv_fold-4 dataset which has size 3376: 
- 1294 are span extraction problems, 
- 73 are data identification problems, 
- and 2009 are numerics problems. <br>

Our analysis results are shown below: 

<img src="https://github.com/valance/DROP-GroupFantasy/blob/main/error_analysis/baseline_error_analysis.png" width=400 height=1200/> <img src="https://github.com/valance/DROP-GroupFantasy/blob/main/error_analysis/new_error_analysis.png" width=400 height=1200/>. 


For the **span extraction problems**, we categorize the errors into the following four types:
- wrong type: the task predicted is not span extraction
- wrong span: the span(s) are wrongly identified
- missing span: the predicted answer contains fewer spans compared to the ground truth answer
- extra span: the predicted answer contains more spans compared to the ground truth answer

For the **date identification problems**, we categorize the errors into two types:
- wrong type: the task predicted is not date identification
- wrong date: the date is wrongly identified

For the **numerics problems**, we categorize the errors into the six types as listed below:
- wrong type: the task predicted is not numerical problem
- counting: counting problem
- negation: negation problem
- addition or subtraction: arithmetic operations of addition/subtraction
- wrong sign: the result is correct but is of opposite sign, e.g. calculating the difference between A and B
- percentage calculation: calculation of percentage 

# Result

By comparing the error analysis pie charts, we can tell that though our model produces more extra-span errors which is not desirable, it is able to produce fewer wrong-span errors. Additionally, our model can better cope with the counting problems compared to the baseline model. 

