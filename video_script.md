NLP Project — Video Script (5 minutes)
Supervised Learning on French Insurance Reviews

[0:00 – 0:40] INTRODUCTION
Welcome. Our goal for this project was to build a complete NLP pipeline to analyze over 30,000 reviews of French insurance companies culminating in a sentiment classification model, predicting positive, neutral, or negative sentiment in a review. To complete this project we built an end-to-end analytical system covering data cleaning, topic modelling, semantic search, supervised learning, and explainability. Every technical choice you will see was driven by one central challenge: processing messy, real-world data and transforming it into actionable insights.

[0:40 – 1:30] SECTION 1 — DATA CLEANING
First, we built a two-track cleaning pipeline creating a column avis_clean, to store reviews without stopwords for bag-of-words models, and avis_light, to preserve grammatical context for transformer models. 

For spelling corrections, generic tools like pyspellchecker destroyed domain-specific names, changing the insurer "APRIL" to the month "avril" and therefore degrading data quality rather than enhancing it. This pushed us to chose LanguageTool, a French grammar-aware tool that used sentence context to fix complex syntax errors, successfully correcting 83% of the corpus without introducing more problems in the data than it fixed.

Finally, our exploratory data analysis revealed multiple insights: the dataset is skewed towards one star review and over represents L'olivier assurance and Direct Assurance aswell as the auto Product in the reviews meaning we have multiple class imbalances in the dataset. What more analysing N-Grams and frequent words we realised positive reviews use highly repetitive formulas like "rapport qualité prix", while negative reviews are lexically diverse and specific.

[1:30 – 2:15] SECTION 2 & 3 — SUMMARISATION AND TOPIC MODELLING
For summarisation, we used Qwen2.5-1.5B, however to prevent generic outputs, we built a pipeline that extracts recurring themes via TF-IDF, selects representative reviews using centroid sampling, and feeds those as context to the LLM model. It worked well, though we hit the structural limitations of the 1.5-Billion parameter model which has a context window too small to have truly reliable results for a production application, proving that model scale is a hard constraint.

For topic modelling, LDA with 8 topics gave the best coherence. Interestingly, "Telephone Customer Service" emerged as two completely polarized topics, it is simultaneously the biggest source of praise and the biggest source of complaints, mapping perfectly on our pyLDAvis charts.

[2:15 – 2:50] SECTION 4 — EMBEDDINGS
We trained Word2Vec on the French corpus, successfully capturing domain synonyms like "prix" and "tarif". We also evaluated English GloVe vectors on translated text and visualized them via TensorBoard.

For semantic search, we deployed a multilingual Sentence-BERT model indexed with FAISS. Unlike TF-IDF, it retrieves semantically equivalent complaints—like "impossible to cancel"—even when users use completely different vocabulary.

[2:50 – 3:50] SECTION 5 — SUPERVISED LEARNING
We framed sentiment as a three-class problem and trained six model families. Classical TF-IDF models like Linear SVM provided a strong baseline of 0.65 F1-macro, actually outperforming GloVe with BiLSTM, which suffered from translation artefacts.

The absolute standout was CamemBERT, a French-native transformer, achieving an incredible 90.8% F1 on binary positive/negative classification.

Our biggest hurdle was the neutral class—the 3-star reviews. Every model had a 90%+ error rate here due to mixed linguistic signals. Our breakthrough was using Ridge Regression to predict stars as a continuous value and applying thresholds, giving us the only meaningful neutral detection in the project.

[3:50 – 4:35] SECTION 6 — RESULTS INTERPRETATION (LIME & SHAP)
We used LIME and SHAP to interpret these models.

LIME showed the model relied on words like "trop" and "chère" (too expensive). The model was actually correct linguistically; the customer was clearly dissatisfied but just randomly assigned 3 stars. This proved our neutral class failure wasn't a modeling error, but an inconsistency between the text and the user's label.

[4:35 – 5:00] CONCLUSION
In conclusion, context-aware cleaning and domain-adapted models like CamemBERT are essential for French NLP. Deep learning doesn't automatically beat classical models on small datasets, and human rating labels are often structurally flawed.

Our complete pipeline—featuring deep thematic mapping, semantic search, and robust CamemBERT classification—is fully documented and ready for deployment. Thank you.