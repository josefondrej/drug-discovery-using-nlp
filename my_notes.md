# Drug Discovery Using NLP

Source code: https://github.com/dataclair-ai/mlprague2023

My notes for the lecture "Drug Discovery Using NLP" at the ML Prague 2023 conference.

Lecturers: Aisling O'Sullivan, Michal Kubista from dataclair.ai (O2 Czech Republic)
PySpark, Azure, Azure Databricks

Companies using it:

- https://www.exscientia.ai/ (cancer drug in 8 months instead of 4-5 years -> raised $525)
- https://www.benevolent.com/ (knowledge graphs -- found out drug for arthritis helps to treat covid -- already was FDA
  approved), 200k articles on covid have been published -- LLMs to summarize

## Drug Discovery Basics

- Find protein that binds to some protein on the virus (?)
- Protein = sequence of amino acids, fold to alpha helixes and beta sheets
- Often we know the sequence but not the shape -- which is crucial for the function
- -> alphafold came along to solve this, 2018 Alphafold, 2020 Alphafold 2
- Before 170k structures -> 200M structures when it was published

## Process

- train ml model to identify desired molecule
- score a lot of molecules
- give this to experts

Problems

- few positive examples (drugs that work)

## Data

- sources -- see presentation after
- protein seqs can be broken to "words" (3 amino acids)
- they behave like natural language
- molecules can also be represented as string -- **SMILES** string
- typically same molecule can have more SMILES strings
- the SMILES have some drawback -- misses 3D info, introduces long range dependencies, small changes in molecule can
  lead to big changes in SMILES
- IUPAC names -- functional names of molecules

## NLP

- word embeddings
- word2vec -- word embeddings are averaged across contexts they appear in
- transformers -- word embeddings are context dependant, uses attention, task: predict masked word or next sentence
- will use transformers -- pretrained one
    - can be encoder (BERT) -- we will use this
    - decoder (GPT)
    - encoder-decoder (T5, translation)
- huggingface.co/course -- course on transformers

### Encoder

- fixed input, fixed output
- trained by masking words at random
- 12 layers (110M params ~ good to use 2G tokens to train not to underfit)
- training from scratch $1500 - 2000 on the cloud
- **Attention** -- for each word in input it applies score to other words -- if the score is high, the other word is
  related to the selected one
- bias in attention scores (the doctor asked the nurse a question. She <- now there is higher attention score for nurse
  the doctor for the "She" word)

## 1st task

- Get familiar with how to download language model from HuggingFace and how to use it
    - you have to download proper tokenizer that belongs
    - tokenize the raw input
    - use the model on it

## 2nd task

- The dataset is relatively big (7k molecules :))
- BBB+/- encode if the molecule can cross the blood-brain barrier
- we remove just numerical names of molecules, because this particular model will probably not recognize it
- sometimes english name / iupac name is missing (molecule is new, does not have a name yet etc.)

## 3rd task

- download model and modify it to predict BBB+/- (binary classification) using the `simpletransformers` library

-----

## How to deal with small molecules?

- Build own embeddings
- **ChemBERTa**
    - Used 10M SMILES
    - trained: predict missing segments of SMILES
    - performance: not the best (likely to get better with more data)
    - byte-wise encoding (hybrid between word and character) -- build subwords based on frequency (in English you would
      likely get "ing", "ed")
    - SMILES with some parts masked -> Tokenizer -> Transformer -> what the masked parts were

## 4th task

