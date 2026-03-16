# AI--CHATBOT
## NAME:THEJA SREE G
## REG NO:212224110056
# Intelligent Enterprise Assistant (Easy Version)
# Run: python chatbot_app.py

import os
from transformers import pipeline
from PyPDF2 import PdfReader
import docx

# ---------------------------------------------------
# 1. Load Question-Answering Model
# ---------------------------------------------------
print("Loading AI model... please wait...")
qa_pipeline = pipeline("question-answering", model="distilbert-base-cased-distilled-squad")
print("✅ Model loaded successfully!\n")

# ---------------------------------------------------
# 2. Helper Functions to Read Different File Types
# ---------------------------------------------------
def read_pdf(file_path):
    """Extract text from all pages of a PDF file."""
    text = ""
    with open(file_path, "rb") as f:
        reader = PdfReader(f)
        for page in reader.pages:
            text += page.extract_text() or ""
    return text

def read_docx(file_path):
    """Extract text from all paragraphs of a DOCX file."""
    doc = docx.Document(file_path)
    return "\n".join([p.text for p in doc.paragraphs])

def read_text(file_path):
    """Read text from a plain text file."""
    with open(file_path, "r", encoding="utf-8") as f:
        return f.read()

# ---------------------------------------------------
# 3. Load and Process Document
# ---------------------------------------------------
def load_document():
    file_path = input("Enter the document file path (PDF/DOCX/TXT): ").strip()

    if not os.path.exists(file_path):
        print("❌ File not found. Please check the path and try again.")
        return None, None

    # Identify file type
    if file_path.lower().endswith(".pdf"):
        context = read_pdf(file_path)
    elif file_path.lower().endswith(".docx"):
        context = read_docx(file_path)
    elif file_path.lower().endswith(".txt"):
        context = read_text(file_path)
    else:
        print("❌ Unsupported file format. Please use PDF, DOCX, or TXT.")
        return None, None

    print(f"✅ Successfully loaded {len(context.split())} words from {file_path}\n")
    return file_path, context

# ---------------------------------------------------
# 4. Chatbot Interaction Loop
# ---------------------------------------------------
def chatbot():
    file_path, context = load_document()
    if not context:
        return

    print("🤖 Chatbot ready! You can now ask questions about your document.")
    print("Type 'exit' to stop.\n")

    while True:
        question = input("You: ")
        if question.lower() == "exit":
            print("Chatbot: Goodbye! 👋")
            break

        if not question.strip():
            continue

        try:
            result = qa_pipeline(question=question, context=context)
            print(f"Chatbot: {result['answer']}\n")
        except Exception as e:
            print(f"⚠️ Sorry, I couldn't process that. ({e})\n")

# ---------------------------------------------------
# 5. Run Application
# ---------------------------------------------------
if __name__ == "__main__":
    chatbot()
## SAMPLE IMPLEMENTATION:
$ python chatbot_app.py Loading AI model... please wait... ✅ Model loaded successfully!

Enter the document file path (PDF/DOCX/TXT): sample.pdf ✅ Successfully loaded 3540 words from sample.pdf

🤖 Chatbot ready! You can now ask questions about your document. Type 'exit' to stop.

You: What is the main topic of the report? Chatbot: The report is about the company's financial performance.

You: Who prepared the report? Chatbot: It was prepared by the finance department.

You: exit Chatbot: Goodbye! 👋
