# Financial Report Intelligence
<img width="800" height="597" alt="front" src="https://github.com/user-attachments/assets/ce980aa4-5e12-458a-a43b-f56beed8fdfc" />
An AI-powered document analysis project for extracting structured
information from **independent audit reports** and **credit rating
reports**.

The project combines traditional PDF processing techniques with
**RegEx**, selective **OCR**, and the **Qwen2.5 Large Language Model**
to analyze financial reports and convert unstructured document content
into structured information.

## Project Overview

Financial reports can contain long sections of unstructured text,
tables, scanned pages, historical dates, auditor information, rating
agencies, and multiple company names. This makes extracting the correct
information more difficult than simply searching for keywords.

This project was developed as an end-to-end document analysis pipeline
that can:

-   Read machine-readable PDF documents with **PyMuPDF (fitz)**
-   Use **OCR / Image-to-Text** only when a page cannot be read directly
    as text
-   Identify the document type using **RegEx-based rules**
-   Extract contextual information with **Qwen2.5**
-   Identify the main company / subject of the report
-   Extract the customer name
-   Extract the correct report year
-   Analyze multiple PDF files through a **Gradio frontend**
-   Display results in a structured table
-   Export analysis results as a **CSV file**

## Documents Used

The dataset consists of **20 financial reports from different
companies**:

-   10 Independent Audit Reports
-   10 Independent Rating Reports

The reports were manually reviewed first to create reference values for
evaluating the automated extraction pipeline.

> The financial reports themselves may be excluded from the repository
> when redistribution is not appropriate. The notebook can be used with
> compatible PDF reports supplied by the user.

## Project Workflow

``` text
PDF Reports
    |
    v
PDF Text Extraction
(PyMuPDF / fitz)
    |
    +----> Image-based page? ----> OCR / Image-to-Text
    |
    v
Text Cleaning & Preparation
    |
    v
RegEx Document Classification
    |
    v
Context Selection
    |
    +----> Report Year Context
    |
    +----> Company / Customer Context
    |
    v
Qwen2.5 Information Extraction
    |
    v
Structured Results
    |
    v
Evaluation
    |
    v
Gradio Frontend
    |
    v
CSV Export
```

## Technologies

-   Python
-   Google Colab
-   PyMuPDF (`fitz`)
-   RegEx
-   OCR / Image-to-Text
-   Qwen2.5
-   Hugging Face Transformers
-   PyTorch
-   Pandas
-   Gradio

## PDF Processing

The project does **not** apply OCR to every document by default.

For machine-readable PDF files, text is extracted directly using
**PyMuPDF**, which is faster and preserves the existing digital text.

OCR is reserved for pages where direct text extraction is not
sufficient. This creates a hybrid document-processing approach:

``` text
Digital PDF  -> PyMuPDF
Scanned Page -> OCR
```

This avoids unnecessary OCR processing while still supporting
image-based document content.

## Document Type Detection

RegEx-based rules are used for rule-oriented document analysis,
including distinguishing between report categories.

The classifier can return categories such as:

``` text
audit
rating
undefined
```

This demonstrates how deterministic text-processing methods can work
together with an LLM instead of using the language model for every task.

## Qwen2.5 Information Extraction

Qwen2.5 is used for information that requires contextual understanding
rather than simple keyword matching.

The main extracted fields are:

-   **Company Name**
-   **Customer Name**
-   **Report Year**

During development, several issues were discovered. For example, rating
reports can mention the rating agency before the company being rated,
and reports may contain several historical years.

Because of this, the extraction pipeline was improved through:

-   Prompt engineering
-   Page-based context selection
-   Separate extraction instructions for different fields
-   Company-name normalization
-   Error analysis and iterative testing

One important observation was that providing more document text did not
always improve extraction quality. Smaller and more relevant document
contexts often produced better results.

## Evaluation

The extracted information was compared with manually prepared reference
values.

After improving the prompts, context-selection strategy, and evaluation
logic, the final pipeline reached:

  Extracted Field     Final Accuracy
  ----------------- ----------------
  Company Name              **100%**
  Customer Name             **100%**
  Report Year               **100%**

Company-name evaluation also accounts for equivalent legal naming
formats and commonly used shortened names so that formatting differences
are not incorrectly counted as extraction errors.

> These results describe the evaluation performed on the 20-document
> project dataset and should not be interpreted as guaranteed
> performance on arbitrary financial reports.

## Frontend

A **Gradio-based frontend** was added after completing the backend
pipeline.

The interface allows users to upload one or multiple PDF reports without
interacting directly with notebook code.

For each document, the application displays:

  Output          Description
  --------------- -------------------------------------------------
  Document Type   Audit or rating report classification
  Company Name    Main company identified in the report
  Customer Name   Organization receiving the audit/rating service
  Report Year     Year associated with the current report

The frontend also supports:

-   Multiple PDF uploads
-   Batch document analysis
-   Structured result tables
-   CSV export
-   A custom green financial/AI dashboard interface

## Example Output

``` text
Document Type : audit
Company Name  : Nurol Holding A.Ş.
Customer Name : Nurol Holding A.Ş.
Report Year   : 2023
```

For multiple files, the application returns a table similar to:

  --------------------------------------------------------------------------------
  File Name            Document Type  Company Name   Customer Name  Report Year
  -------------------- -------------- -------------- -------------- --------------
  example_report.pdf   audit          Example        Example        2024
                                      Company A.Ş.   Company A.Ş.   

  --------------------------------------------------------------------------------

## Running the Project

The project was developed in **Google Colab** with GPU acceleration.

1.  Open the notebook in Google Colab.
2.  Enable a GPU runtime when running Qwen2.5.
3.  Install the required libraries from the notebook.
4.  Upload or mount the PDF reports.
5.  Run the notebook cells in order.
6.  Launch the Gradio interface.
7.  Upload one or more PDF reports.
8.  Select **Analyze Documents**.
9.  Review the extracted information or export the results as CSV.

## Project Structure

A simple repository structure can be used:

``` text
financial-report-intelligence/
|
|-- Denetim_ve_Derecelendirme_Raporlari_Okuma.ipynb
|-- README.md
|-- requirements.txt
|
|-- sample_reports/        # Optional
|-- screenshots/           # Optional frontend screenshots
```

## Key Takeaways

The most important lesson from the project was that successful document
information extraction depends on more than the language model itself.

Reliable results required combining:

-   Appropriate PDF extraction methods
-   Selective OCR
-   Rule-based RegEx processing
-   Relevant document context
-   Carefully designed prompts
-   Output normalization
-   Manual ground-truth evaluation
-   Iterative error analysis

The final result is an end-to-end financial document analysis workflow
that connects document processing, NLP/LLM extraction, evaluation, and
frontend development in a single project.

## Future Improvements

Possible future extensions include:

-   Supporting additional financial document types
-   Extracting more financial fields
-   Adding confidence scores
-   Processing larger document collections
-   Saving results to a database
-   Adding Excel export
-   Improving OCR for difficult scanned reports
-   Deploying the Gradio application as a standalone web service

## Disclaimer

This project was developed for educational and experimental purposes.
Extracted information should be verified before being used in financial,
audit, compliance, or other high-stakes decisions.
