---
url: "https://mirascope.com/docs/mirascope/guides/more-advanced/extract-from-pdf"
title: "Extracting from PDF | Mirascope"
---

# Extracting from PDF [Link to this heading](https://mirascope.com/docs/mirascope/guides/more-advanced/extract-from-pdf\#extracting-from-pdf)

This recipe demonstrates how to leverage Large Language Models (LLMs) -- specifically the OpenAI API -- to extract pages and content from PDF files. We'll cover single PDF document as well as multiple PDF files and also use OCR to extract text from scanned documents.

Mirascope Concepts Used

Background

Prior to LLMs, extracting pages from pdf files has been a time-consuming and expensive task. Natural Language Processing (NLP) would be used to identify and categorize information in text specific to the PDF document, or worse yet manually by humans. This would need to happen on every new PDF with new categories, which is not scalable. LLMs possess the ability to understand context, and the versatility to handle diverse data beyond PDFs such as word documents, powerpoints, email, and more.

## Setup [Link to this heading](https://mirascope.com/docs/mirascope/guides/more-advanced/extract-from-pdf\#setup)

To set up our environment, first let's install all of the packages we will use:

```
!pip install "mirascope[openai]" PyMuPDF
```

```
import os

os.environ["OPENAI_API_KEY"] = "YOUR_API_KEY"
# Set the appropriate API key for the provider you're using
```

## Text-Based Extraction [Link to this heading](https://mirascope.com/docs/mirascope/guides/more-advanced/extract-from-pdf\#text-based-extraction)

```
import asyncio

from mirascope.core import openai, prompt_template
from pydantic import BaseModel, Field

class ResumeInfo(BaseModel):
    name: str = Field(..., description="The name of the person")
    email: str = Field(..., description="The email of the person")
    phone: str = Field(..., description="The phone number of the person")
    skills: list[str] = Field(..., description="The skills of the person")
    experience: list[str] = Field(..., description="The experience of the person")
    education: list[str] = Field(..., description="The education of the person")

@openai.call(model="gpt-4o", response_model=ResumeInfo)
@prompt_template(
    """
    Extract the resume information from the pdf file.
    {pdf_text}
    """
)
async def extract_resume_info(pdf_text: str): ...

async def convert_pdf_to_text(pdf_file_path: str) -> str:
    doc = fitz.open(pdf_file_path, filetype="pdf")
    text = []
    for page in doc:
        text.append(page.get_text())  # type: ignore
    return "\n".join(text)

async def process_pdf(pdf_file_path: str) -> ResumeInfo:
    pdf_text = await convert_pdf_to_text(pdf_file_path)
    return await extract_resume_info(pdf_text)

async def run(pdf_file_paths: list[str]):
    tasks = [process_pdf(pdf_file_path) for pdf_file_path in pdf_file_paths]
    await asyncio.gather(*tasks)

# In jupyter notebook to run an async function
await run(["resume.pdf"])

# In Python scripts to run an async function
# asyncio.run(run(["resume.pdf"]))
```

This script takes in multiple text PDF documents and extracts them into a `ResumeInfo` Pydantic model. However, PDF documents contain not only text but images, tables, and more. In order to handle these more complex documents, we use Optical Character Recognition (OCR).

## Vision-Based Extraction [Link to this heading](https://mirascope.com/docs/mirascope/guides/more-advanced/extract-from-pdf\#vision-based-extraction)

For PDFs with complex layouts or embedded images, vision-based extraction can be more effective. We first write a helper function that uses `PyMuPDF` to convert the PDF document into a PNG image:

```
import fitz

async def convert_pdf_to_png(pdf_file_path: str) -> list[bytes]:
    doc = fitz.open(pdf_file_path, filetype="pdf")
    images = []
    for page_num in range(len(doc)):
        page = doc.load_page(page_num)
        pix = page.get_pixmap()
        images.append(pix.tobytes(output="png"))
    return images
```

Afterwards, we update our previously defined call function to take in image bytes and run:

```
from mirascope.core import openai, prompt_template

@openai.call(model="gpt-4o-mini", response_model=ResumeInfo)
@prompt_template(
    """
    Extract the resume information from the pdf file.
    {pdf_imgs:images}
    """
)
async def extract_resume_info_as_image(pdf_imgs: list[bytes]): ...

async def process_pdf_as_image(pdf_file_path: str) -> ResumeInfo:
    pdf_pngs = await convert_pdf_to_png(pdf_file_path)
    return await extract_resume_info_as_image(pdf_pngs)

# In jupyter notebook to run an async function
await process_pdf_as_image("resume.pdf")

# In Python scripts to run an async function
# asyncio.run(process_pdf_as_image("resume.pdf"))
```

This approach converts the PDF to images, allowing the AI model to analyze the visual layout and content. It's particularly useful for PDFs with non-standard layouts or those containing charts and diagrams.

Not all providers support vision, so be sure to check provider documentation.

Additional Real-World Examples

- **Automating document processing**: Take invoices, statements, tax-forms, etc and other manual data entry tasks and automate them.
- **Medical Document Integrations**: Extract patient records, often a PDF document into Electronic Health Record (EHR) systems.
- **Resume Parsing Advanced**: Update the `ResumeInfo` schema to a format for a Customer Relationship Management (CRM) tool.

When adapting this recipe to your specific use-case, consider the following:

- OCR accuracy: For better OCR results when using Vision-based extraction, use high-quality scans.
- Multiple providers: Use multiple LLM providers to verify whether the extracted information is accurate and not hallucination.
- Implement Pydantic `ValidationError` and Tenacity `retry` to improve reliability and accuracy.

Copy as Markdown

#### Provider

OpenAI

#### On this page

Copy as Markdown

#### Provider

OpenAI

#### On this page

## Cookie Consent

We use cookies to track usage and improve the site.

RejectAccept