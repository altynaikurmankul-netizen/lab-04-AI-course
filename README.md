Lab 04 - Invoice to JSON
What was done

Scanned invoices were turned into JSON in two ways: classic OCR (Tesseract) and a vision-language model (Qwen2-VL-2B). There were 24 invoices in Kazakh, Russian and English, each as a clean image and as a degraded phone photo, 48 images in total and 456 fields per condition.

OCR was measured as an upper bound: does the value of each field appear in the OCR text at all. The model ran on a Colab T4 GPU; the full run of 48 images took 470 seconds, and 6 answers were not valid JSON.

The notebook lab04_invoice_to_json.ipynb has all cells run and all questions answered.

Results (all languages, 95% Wilson intervals)

OCR upper bound, clean: 426 of 456 = 0.934, interval 0.91 to 0.95
OCR upper bound, degraded: 4 of 456 = 0.009, interval 0.00 to 0.02
VLM normalised, clean: 397 of 456 = 0.871, interval 0.84 to 0.90
VLM normalised, degraded: 204 of 456 = 0.447, interval 0.40 to 0.49

VLM by language
Kazakh: clean 0.730, degraded 0.553
Russian: clean 0.908, degraded 0.132
English: clean 0.974, degraded 0.658

Findings

1. Predictions. Kazakh names were the weakest point of the model. On degraded images OCR lost far more (from 93.4% to 0.9%) than the model (from 87.1% to 44.7%). On clean images OCR was higher than the model.

2. Kazakh letters. The most frequent mistakes on clean Kazakh invoices: қ read as к (11 times), ұ read as ү (6 times), ұ read as у (4 times). This looks mostly like a problem of knowing the language, not of reading: the letters are sharp and OCR finds 97.4% of Kazakh fields, but the model writes the nearest Russian letter. To check this, the model could be given the same names as typed text, without an image, and asked to copy them.

3. Arithmetic checks. On clean images: 0 wrong totals, 1 of 24 right totals flagged, 23 documents passed but only 8 of them were fully correct. On degraded images: all 8 wrong totals caught, 14 of 16 right totals flagged, 2 documents passed and neither was fully correct. The checks only see numbers that must agree, so they can send a document to a person for review but should not approve a payment.

4. Recommendation. Use the model pipeline, with an image quality check before it that rejects blurry scans and asks for a rescan, and with the arithmetic checks and human review after it.
