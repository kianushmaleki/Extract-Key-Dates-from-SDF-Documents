# PDF Data Extraction: Dates, Graphics, and Tables

This project provides a comprehensive Python pipeline for extracting structured data from PDF documents, specifically targeting Dates, Tables, and Graphical Elements (Vector and Raster). It's designed to handle complex, borderless PDF layouts typical in industrial and official documents.

## Table of Contents

*   [Key Features](#key-features)
*   [Methodology: Geometric Table Extraction](#methodology-geometric-table-extraction)
*   [Strengths](#strengths)
*   [Weaknesses](#weaknesses)
*   [Future Improvements & Scaling](#future-improvements--scaling)
*   [Setup and Configuration](#setup-and-configuration)
*   [Usage](#usage)
*   [Output Files](#output-files)

## Key Features

*   **Intelligent Date Classification:** Categorizes dates based on surrounding textual context (e.g., Expiration vs. Manufacturing Date, Revision Date, DocuSign Timestamp).
*   **Advanced Table Extraction:** Utilizes geometric analysis, vertical gap analysis, and fuzzy horizontal alignment to accurately group text into tables, even in documents without visible borders.
*   **Vendor Information Extraction:** Identifies and extracts vendor and signatory details from 'letter' type pages.
*   **Visual Auditing:** Generates highlighted PDFs to visually verify all extracted entities (dates, tables, and graphics).
*   **Structured Export:** Compiles all findings into a master JSON file for easy integration with other systems.
*   **Configurable Parameters:** Centralized `CONFIG` dictionary for easy tuning of extraction parameters (e.g., `GAP_THRESHOLD`, `TOLERANCE`).

## Methodology: Geometric Table Extraction

The core of this pipeline is a geometric coordinate analysis method. Instead of treating the PDF as a simple stream of characters, the algorithm reconstructs the document's structure by "snapping" text spans into logical rows based on their vertical alignment. By implementing coordinate-based proximity thresholds and vertical gap thresholds, the system successfully groups misaligned columns and segments distinct data blocks, ensuring high data integrity even for complex, borderless industry tables.

## Strengths

*   **Geometric Precision**: Excels in reconstructing tables from borderless PDFs by utilizing coordinate-based "snapping," offering significantly higher accuracy compared to traditional text-stream extraction approaches.
*   **Flexible Alignment (Tolerance)**: Designed to be "forgiving" regarding object placement. Using `X_TOLERANCE` and `TOLERANCE` settings, it correctly groups columns and rows even if slightly misaligned, preventing data loss due to minor PDF formatting variations.
*   **Context-Aware Labeling**: Analyzes surrounding text to assign semantic meaning to extracted dates (e.g., "Manufacturing Date" vs. "Expiration Date"), providing richer, more actionable data.
*   **Configurability**: A central `CONFIG` dictionary allows for easy hyperparameter tuning and adjustments, adhering to MLOps best practices.

## Weaknesses

*   **Heuristic Dependency**: Rules for extracting vendor information and signatory details are specific to current document templates. These heuristics may become fragile if future documents deviate significantly.
*   **Non-Searchable Content**: Relies on the presence of a text layer within the PDF. Cannot extract information from text embedded within raster images or complex vector drawings.
*   **Coordinate Sensitivity**: While fuzzy logic mitigates some issues, extremely complex layouts or documents with overlapping tables might still present challenges.

## Future Improvements & Scaling

*   **OCR Integration (Critical)**: Integrate an Optical Character Recognition (OCR) engine (e.g., Tesseract, Azure Form Recognizer) to analyze content within graphical areas (RED BOXES) for complete data extraction.
*   **Regular Expression Expansion**: Expand the `MASTER_PATTERN` for date extraction to include a wider array of global date formats to accommodate diverse international documents.
*   **Validation Layer**: Implement a Pydantic schema for the extracted JSON output to ensure data integrity and type validation before ingestion into downstream systems.

## Setup and Configuration

To run this notebook, you'll need to install the following Python libraries:

```bash
pip install pymupdf pandas

All necessary modules are imported and regular expression patterns are compiled at the beginning of the script for optimized performance.
Usage

    Run the Colab notebook.
    When prompted, upload your PDF file (e.g., sample-sdf-document-1.pdf).
    The notebook will process the document, extracting dates, tables, and identifying graphics.
    Vendor information will be printed to the console if a 'letter' type page is detected.
    An extraction summary table will be displayed, showing counts of dates, tables, vector drawings, and pixel images per page.
    Two output files will be generated and made available for download:
        extracted_data.json: A structured JSON file containing all extracted data.
        verification_highlights.pdf: A visual PDF with color-coded highlights:
            RED: Graphics (Vector drawings or Pixel images)
            GREEN: Extracted Dates
            BLUE: Extracted Tables

IMPORTANT: Always manually inspect verification_highlights.pdf. Red boxes indicate areas with non-text graphical data that may contain critical information requiring human oversight or OCR-assisted extraction.
Output Files

    extracted_data.json: A JSON file containing the extracted entities (dates with types and bounding boxes), tables (structured as lists of lists of text with bounding boxes), and graphic counts per page, along with page type and contextual information (e.g., vendor details).
    verification_highlights.pdf: A PDF copy of your input document with all detected entities highlighted for visual verification.


What can I help you build?
