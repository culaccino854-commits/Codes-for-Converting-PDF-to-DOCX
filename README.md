"""
PDF to Docx Converter (Non-OCR)
-------------------------------
A generic Python script to convert text-based PDF documents into editable Word (.docx) files.
Supports both single-file processing and batch directory conversion.

"""

import os
import sys
import time
from pathlib import Path
from pdf2docx import Converter


def convert_single_pdf(pdf_path: str, docx_path: str = None) -> bool:
    """
    Converts a single vector/text-based PDF file to DOCX format.

    :param pdf_path: Path to the input PDF file.
    :param docx_path: Path to the output DOCX file. If None, saves in the same directory.
    :return: True if successful, False otherwise.
    """
    pdf_file = Path(pdf_path).resolve()

    if not pdf_file.exists() or pdf_file.suffix.lower() != ".pdf":
        print(f"❌ Invalid file or file does not exist: {pdf_file}")
        return False

    if docx_path is None:
        docx_file = pdf_file.with_suffix(".docx")
    else:
        docx_file = Path(docx_path).resolve()

    print("\n--------------------------------------------------")
    print(f"📄 Processing: {pdf_file.name}")
    print(f"📁 Output to : {docx_file}")
    print("--------------------------------------------------")

    start_time = time.time()
    try:
        cv = Converter(str(pdf_file))
        # Convert all pages (start=0, end=None)
        cv.convert(str(docx_file), start=0, end=None)
        cv.close()

        elapsed = round(time.time() - start_time, 2)
        print(f"✅ Success! Saved to: {docx_file} (Time taken: {elapsed}s)")
        return True

    except Exception as e:
        print(f"❌ Conversion failed for {pdf_file.name}. Error: {e}")
        return False


def batch_convert_directory(input_dir: str, output_dir: str = None) -> None:
    """
    Batch converts all PDF files in a given directory to DOCX format.

    :param input_dir: Path to the directory containing PDF files.
    :param output_dir: Path to save converted DOCX files. If None, saves in input_dir.
    """
    src_dir = Path(input_dir).resolve()

    if not src_dir.exists() or not src_dir.is_dir():
        print(f"❌ Directory not found: {src_dir}")
        return

    dst_dir = Path(output_dir).resolve() if output_dir else src_dir
    dst_dir.mkdir(parents=True, exist_ok=True)

    pdf_files = list(src_dir.glob("*.pdf"))
    total = len(pdf_files)

    if total == 0:
        print(f"⚠️ No PDF files found in: {src_dir}")
        return

    print("==================================================")
    print(f"🚀 Batch Converting {total} PDF file(s)")
    print(f"📂 Source Directory: {src_dir}")
    print(f"📂 Target Directory: {dst_dir}")
    print("==================================================")

    success_count = 0
    for idx, pdf_path in enumerate(pdf_files, 1):
        print(f"\n[Progress {idx}/{total}]")
        target_docx = dst_dir / f"{pdf_path.stem}.docx"
        if convert_single_pdf(str(pdf_path), str(target_docx)):
            success_count += 1

    print("\n==================================================")
    print(f"🎉 Batch Process Completed: {success_count}/{total} files converted successfully.")
    print("==================================================")


def main():
    """
    Interactive terminal CLI entry point.
    """
    print("=== General PDF to DOCX Converter ===")
    print("1. Convert a single PDF file")
    print("2. Batch convert a directory of PDF files")
    
    choice = input("\nSelect mode (1 or 2): ").strip()

    if choice == "1":
        target = input("Enter full path to PDF file: ").strip('"\'')
        if target:
            convert_single_pdf(target)
    elif choice == "2":
        folder = input("Enter directory path containing PDFs: ").strip('"\'')
        if folder:
            batch_convert_directory(folder)
    else:
        print("❌ Invalid selection. Exiting.")


if __name__ == "__main__":
    main()# Codes-for-Converting-PDF-to-DOCX

Note:  
## Features
- **Single File & Batch Processing**: Easily convert individual files or an entire folder of PDFs.
- **Cross-Platform**: Uses standard `pathlib` for smooth handling of Windows/macOS/Linux file paths.
- **Pure Python Solution**: Built on top of `pdf2docx`.

## Prerequisites & Installation

Make sure you have Python 3.8+ installed.

```bash
pip install pdf2docx
