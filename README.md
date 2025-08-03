# **Multicolor Flow Cytometry Immunophenotyping of Mouse Hematopoietic Organs: Data Analysis with R**

This project demonstrates a streamlined R workflow for analyzing multicolor flow cytometry data from murine hematopoietic organs. It showcases automated data cleaning, statistical testing, and visualization of immune cell populations between control and genetically altered mice.

---

## 🧪 Biological Background

IKZF1 encodes IKAROS, a transcription factor critical for B-cell development. IKZF1 deletions/mutations are frequent in high-risk B-ALL, disrupting differentiation and promoting leukemogenesis. We used a mouse model with a Cre-inducible mutant Ikzf1 allele to study immune phenotypes across key organs (bone marrow, spleen, blood).

---

## 🎯 Objective

To compare immune populations between mutant and wild-type mice using a reproducible and scalable R-based analysis pipeline following multicolor flow cytometry.

---

## ⚙️ Workflow Overview

1. Flow cytometry data exported from **FlowJo** as an Excel file  
2. R script:  
   * Cleans marker names  
   * Splits data by cell type  
   * Performs group comparison (t-test)  
   * Generates per-cell-type bar plots with statistical annotations  
3. Outputs multi-page PDF summarizing the entire analysis

---

## 🧠 Why Use This?

✅ **Reduces manual error and copy-paste steps**  
✅ **Automates repetitive analysis steps**  
✅ **Ensures reproducibility** (ideal for teams or large datasets)  
✅ **Produces publication-ready plots**  
✅ **Flexible structure** – easily modified for other flow panels or experiments

---

## 📂 Input

* `Data for flow and R analysis.xlsx`: FlowJo-exported table with rows = samples, columns = cell population percentages.

---

## 📜 Code Walkthrough

### 1️⃣ Extract and Clean Data

```r
library(readxl)

# Load Excel file
file_path <- "Data for flow and R analysis.xlsx"
data <- read_excel(file_path)

# Identify sample and cell type columns
sample_col <- colnames(data)[1]
cell_types <- colnames(data)[-1]

# Function: Replace + with 'pos', - with 'neg' (outside brackets)
replace_outside_brackets <- function(text) {
  matches <- gregexpr("\\([^)]*\\)", text)
  parts <- regmatches(text, matches)
  replaced <- gsub("\\([^)]*\\)", "___BRACKET___", text)
  replaced <- gsub("\\+", "pos", replaced)
  replaced <- gsub("-", "neg", replaced)
  if (length(parts[[1]]) > 0 && parts[[1]][1] != -1) {
    for (p in parts[[1]]) replaced <- sub("___BRACKET___", p, replaced)
  }
  replaced <- gsub("[^a-zA-Z0-9()]", "_", replaced)
  return(replaced)
}

# Loop through cell types and export separate CSVs
for (i in seq_along(cell_types)) {
  cell <- cell_types[i]
  subset_data <- data[, c(sample_col, cell)]
  clean_cell_name <- replace_outside_brackets(cell)
  output_filename <- sprintf("%02d_CellType_%s.csv", i, clean_cell_name)
  write.csv(subset_data, output_filename, row.names = FALSE)
}
