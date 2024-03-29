# NLP_task
This project constitutes the extraction of Age and treatment information from the Medical transcriptions

## Dataset

The dataset used for this task is 'mtsamples.csv' with columns names  "Description, Keyword, Medical_specialty, Sample_name, Transcription".

There are some Null values in the transcriptions column and no duplicates.

## Task:
The two tasks are to extract 'Age' for the patients and to extract the treatment administered to the patient.

### Age:
Method1:
For this task, I used regex to extract the age, using pattern matching and then filtering them accordingly. Regex is a popular and my favorite tool to match and extract patterns. So, I chose regex to extract patterns.

In order to indetify the distinct patterns in the dataset, first I filtered the desirerd column, 'description', for the presence of numerical values. Upon doing that, the resultant dataframe sixe reduced to a little over 1000. Then I tried finiding the possible patterns from the available text. After finding the patterns, I created a regex pattern that covers all the possible patterns that are found and used it to extract the ages. 

The pattern matching covers the follwing types:
  2(-)(year/years)/(month/months)/(day/days)(-)old, '2 (year/years)/(day/days)/(month/months) old, '2 (year/years) of age', '2-1/2 years'

There are a couple of edge cases that did cause a bit of problem, some of them are:
    > The patient had ultrasound done on admission that showed gestational age of 38-2/7 weeks.<br>
    >  External cephalic version.  A 39-week intrauterine pregnancy with complete breech presentation. <br>
    >   She underwent a lipomyomeningocele repair at 3 days of age and then again at 3-1/2 years of age. 

These are ambiguous and are inducing errors into the data. these are some examples and there are others with past ages mentioned as well. 

While working on Task2, the NER is also identifying the age accurately. So, I also captured the ages using the same and stored and compared with the regex result and the algorithm is doing a great job in identifying the entities.

##### It is also to be noted that, the transcription column also has ages of people and often times it has different timelines of a patient age while describing the illness. So, ignored it for regex as it induces erroneous values. 

### Treatment Extraction

Method1:

The first method I tried for this is, `Named Entity Recognition`. The main reason I chose to go with this method is, that it maybe more accurate to find the treatment based in the NER as it would help us discrimate between common drug descriptions from the used ones based on the context.

I found a pretrained model that is trained to identify 107 entities from a given corpus. It is build on top of `distillbert-base-incased`. I tested it on a couple of transcriptions and validated them manually, the model seems to work fine on the data. As far as I checked, the model is able to identify which is medication and which is not. I also printed the first 5 columns in the notebook to show the distinction.

Method2:
I have used two pretrained question answering models to see if they fare any better than the NER tagging. It is to be noted that the, first model is trained on the covid data and there is not much information available on the model card for the training data, but it's mentioned that it's been trained on medical data. 

The model1 is able to identify the diseases or rather symptoms that pertain more to the Covid and failed to identify the treatments, the result is the same for the model2.

So, I tried to change the prompt and see the change in result, but it didn't make the results any better. When the prompt is changed, the model seems to drop. 

Method3:

I tried BERT large model (uncased) whole word masking finetuned on SQuAD which is based on the BERT architecture except the model is trained on the Wholeword masking where all the tokens corresponding to a word are masked at once and are predicted by the model. 

This model seems to perform reasonably well compared to the models that are finetuned on medical data explicilty. It's able to identify the treatment accuractely and the drug administered to the patient for both the prompts. 

The response time of all these models are longer due to the deep learning architecture. Finetuning this BERT model may produce even better results. 

question = "What is the treatment administered to the patient?"
<br>
question_2 = "What are the drugs administered to the patient"

[Model1]([url](https://huggingface.co/d4data/biomedical-ner-all))
<br>
[Model2]([url](https://huggingface.co/GonzaloValdenebro/MedicalQuestionAnswering))
<br>
[Model3]([url](https://huggingface.co/google-bert/bert-large-uncased-whole-word-masking-finetuned-squad))
