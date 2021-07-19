# PDF_Word_Extraction

This is a simple tool that can extract the individual pixel arrays that represent all words appearing in a PDF document. This targets LaTeX documents that were used to communicate mathematical information. But it performs well enough that it would probably work on simpler types of PDF documents.

The main usage for this is to create datasets for machine learning tasks that need to be trained on pixel arrays representing words appearing in a document. 

The tricky thing about this task is that in a PDF document, such as a book sample or a homework assignment, *anything* can potentially show up, such as some complex mathematical figure, a picture, or a formula. However, the simple approach we take here actually handles those situations well (it just gives you a picture of what it cannot process to be a word, which is the expected behavior in these edge cases)

For example, if we process this page listing exercises in Hartshorne's Algebraic Geometry, then we might worry about the appearance of the fancy mathematical diagram in Exercise 2.10.

<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/page_0.png"/>

However, when the code scans this image, it simply captures the entire thing and treats it as one giant word that it found. It returns the image below.
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/sentence_0_34.png"/>

## How to use
If you have a pdf file `some_document.pdf`, and a directory `data` for where you like to store your pixel data, then simply run the code:
```python
data = "data"
pdf_file = "some_document.pdf"

document = PDF_Document(pdf_file, data)
```
When you're ready, run `document.generate_word_data()` to get your pixel data. Inside your `data` directory it will build subdirectories and store the files below in the following way:
```
        data
        └─── some_document  # Suppose the pdf has n-many pages
              ├── page_0/ 
              │    ├── sentences_0_0/ # Each sentence gets it own directory
              │    │       └── words/ 
              │    │           ├── word_0_0_0.png  # All pngs appearing in sentence 0
              │    │           │     
              │    │           ...
              │    ...
              │   
              └─page_n/   
                 ├── sentences_n_0/ 
                 │       └── words/ 
                 │           ├── word_n_0_0.png  # All pngs appearing in sentence n
                 │           │     
                 │           ...
                 ...
```
This is naturally an expensive function, so try it on small pdfs with a few pages first.

## How it works 
We first convert the PDF to a png image, and then traverse the document vertically to extract the sentences. For example, in the PDF above, the first sentence we'd get is 
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/sentence_0_1.png"/>


So if one just needs to extract the pixels representing sentences, this does that too. 

We then traverse each sentence horizontally to extract the letters. For example, the code identifies the following images as words in the sentence above:
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_0.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_1.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_2.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_3.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_4.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_5.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_6.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_7.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_8.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_9.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_10.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_11.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_12.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_13.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_14.png"/>
<img src="https://github.com/ltrujello/PDF_Word_Extraction/blob/main/example_pngs/words/word_0_1_15.png"/>

The main idea is that a letter is generally a set of "connected islands" of pixels; when you write down any kind of letter, there exists neighborhoods of pixels whose pairwise dot products, when we filter out whitespace, are nonzero. It doesn't matter if it's a capital A or the letter i. The pairwise dot products become zero when we've encountered some white space, indicating that we're done writing down the letter. Thus it turns out it's easy to use dot products of ones and zeros to detect when we've found a letter. 



