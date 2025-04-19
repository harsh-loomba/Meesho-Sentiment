# Meesho Review Summarizer

## Problem Statement

### Task 1: Dataset Collection  
Automatically collect a dataset of 50 Meesho products (specifically focusing on lipsticks) with at least 500 user reviews each, filtered to only include reviews by Indian users. Based on the review text, annotate whether each product is deemed useful in the Indian market or not (useful vs. not useful). citeturn0file0

### Task 2: Perform analysis  
1. **Sentiment Analysis:** Determine overall user sentiment for each product (positive, negative, neutral) using a fine-tuned Hugging Face classification model.  
2. **Aspect-Based Opinion Mining:** Extract and classify sentiments tied to specific product attributes (e.g., color payoff, longevity, texture), highlighting appreciated and criticized aspects.  
3. **Review Summarization:** Produce concise summaries of user reactions, highlighting pros, cons, and final recommendations for each product. citeturn0file0

### Task 3: Model  
Train a classification model that combines the derived sentiment and aspect-based features to predict whether new, similar products will be useful in the Indian market.

## Solution Steps

1. **Dataset Collection**  
   - Scrape 50 lipstick product links from Meesho with ≥2000 ratings to ensure ≥500 reviews.  
   - For each product, collect details (ID, name, URL, overall rating, price, description) and 500 unique reviews (username, rating, review text, date, helpful count) via Selenium + undetected-chromedriver with scroll simulation and anti-bot measures.  
   - Translate all reviews to English using Google Translate API. Exports stored as CSVs: `meesho_product_details.csv`, `meesho_product_reviews_translated.csv`.

2. **Data Preprocessing**  
   - Load translated reviews.  
   - Clean text: demojize, correct common misspellings, remove special characters and extra whitespace.  
   - Deduplicate reviews based on username + text + date.  
   - Split data into training and validation sets (10% validation).

3. **Sentiment Analysis**  
   - Fine-tune `LiYuan/amazon-review-sentiment-analysis` model on cleaned dataset (4000 iterations).  
   - Perform overall sentiment prediction on all reviews.  
   - Use `gauneg/roberta-base-absa-ate-sentiment` for aspect-based sentiment extraction.  
   - Aggregate positive, negative, and neutral aspect counts per review and compute review-level scores.

4. **Review Summarization**  
   - Prompt a text-to-text T5 model (`google/flan-t5-base`) to generate pros, cons, and recommendations based on sentiment-tagged reviews.  
   - Customize prompt:  
     ```python
     prompt = (
         "You are a product expert. Given the following product summary, write a brief analysis with pros, cons, and a final recommendation:\n\n" + text
     )
     ```  
   - Store summaries alongside aggregated sentiment features. citeturn0file0

5. **Product Usefulness Classification**  
   - Combine sentiment, aspect, and summarization features.  
   - Use T5 (`google/flan-t5-base`) or a lightweight classifier to predict the target label (useful vs. not useful).  
   - Evaluate model performance and refine features.

## Results & Analyses

- Detailed results, visualizations, and analysis slides:  
  [Meesho Review Summarizer - Presentation PDF](https://drive.google.com/file/d/1t6_MSQCkmVIQXQFbaFG-yDlfsGyda8Po/view?usp=sharing)

---

**Repository & Code**  
Explore the full implementation and scripts:  
https://github.com/harsh-loomba/NLP-Sentiment.git
