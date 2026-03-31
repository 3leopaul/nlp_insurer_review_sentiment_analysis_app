NLP Project: Supervised Learning on French Insurance Reviews
Total Run Time: ~5 Minutes

[0:00 – 0:35] INTRODUCTION
Welcome. This project explores a complete NLP pipeline built on over 34,000 French insurance reviews covering 40 different providers. Our core goal was sentiment classification—predicting whether a customer is happy, neutral, or frustrated. But the real challenge wasn't just the math; it was the data. We’re dealing with informal, domain-specific French—a language where many "off-the-shelf" NLP tools, usually designed for English, often stumble. Every technical choice you’re about to see was driven by that specific linguistic constraint.

[0:35 – 1:25] SECTION 1 — DATA CLEANING & EDA
The first surprise hit us immediately: 30% of our star ratings were missing. While it looked like a data quality issue at first, a quick look at the "type" column revealed the truth: these rows were the pre-partitioned test split for the assignment. We preserved them as an unlabeled set, which dictated how we structured our training splits for all six models later on.

To handle the text, we built a two-track cleaning pipeline.

avis_clean strips stopwords and punctuation for TF-IDF and Word2Vec.

avis_light preserves grammatical structure. This was vital for our CamemBERT model, where a phrase like "n’est pas satisfait" needs its negation intact, or the sentiment flips entirely.

For spelling, we found that simple dictionary lookups like pyspellchecker were too blunt—it tried to turn the insurer "APRIL" into the month of "avril." Instead, we moved to LanguageTool. Because it understands sentence-level grammar, it could distinguish between a typo and a conjugation error, like fixing "J’attend" to "J’attends". We successfully corrected 83% of the corpus this way.

Our Exploratory Data Analysis (EDA) revealed a fascinating asymmetry: Happy customers are formulaic—they all say "rapport qualité prix." Frustrated customers, however, are creatively diverse. This "lexical diversity" in negative reviews gave us an early warning: the models were going to have a much harder time identifying complaints than compliments.

[1:25 – 2:10] SECTION 2 & 3 — SUMMARIZATION AND TOPIC MODELING
For summarization, we used Qwen2.5-1.5B. Initially, the LLM gave us generic paragraphs that ignored the actual data. To fix this, we built a four-step "structured context" pipeline: calculating rating stats, extracting themes via TF-IDF, and selecting representative reviews through centroid sampling. The quality jumped significantly, though we hit a hardware ceiling. We tried to upgrade to the 4B model, but the architecture wasn't yet supported by our environment. It proved that our pipeline was solid—we just needed a bigger engine.

Moving to Topic Modeling, a coherence sweep identified 8 optimal topics. They were remarkably clear: things like Claims Processing, Price Hikes, and Health Insurance. Interestingly, "Telephone Support" appeared as both a top source of praise and a top source of complaints. Our pyLDAvis map confirmed this, showing these two polar opposite topics sitting far apart from the central cluster.

[2:10 – 2:50] SECTION 4 — EMBEDDINGS & SEARCH
We trained Word2Vec specifically on our corrected French corpus. It worked well—synonyms like "prix" and "tarif" showed a high similarity of 0.83. However, we still found some "noise," like the word "sévice" (abuse) appearing near "service" due to lingering typos.

For GloVe, we used English vectors on translated reviews, achieving 76% coverage. We exported both sets to TensorBoard, allowing us to visualize the embedding space in 3D and see how the model actually "clusters" insurance concepts.

Finally, for Semantic Search, we deployed Sentence-BERT with FAISS. Unlike a keyword search, if you type "impossible to cancel," it finds semantically similar complaints even if the reviewer used completely different words.

[2:50 – 3:55] SECTION 5 — SUPERVISED LEARNING
We tested six model families on the three-class sentiment task.

The Baselines: A Linear SVM with TF-IDF performed strongly with an F1-macro of 0.649. Interestingly, our BiLSTM with GloVe actually performed worse (0.591), likely because translation artifacts and the "English domain gap" outweighed the benefit of the pre-trained vectors.

The Heavyweight: CamemBERT was the standout, hitting a 0.908 F1-score on binary classification.

The biggest hurdle, however, was the Neutral class. Every model struggled with 3-star reviews because the language used in them is often quite polarized. Our most original fix was reframing the problem as Ridge Regression. By predicting the star rating as a continuous number and then converting it to a category, we achieved a much better F1-macro of 0.678.

[3:55 – 4:40] SECTION 6 — EXPLAINABILITY & TRENDS
To see why the models made these choices, we used LIME and SHAP.
LIME highlighted a key issue: a review saying a price was "double the price of competitors" was labeled as "Neutral" by the user, but the model—correctly—identified it as "Negative." This proved that our "neutral" failures weren't necessarily a modeling error, but a lack of consistency in how users assign star ratings.

SHAP confirmed our earlier EDA: words like "satisfait" and "rapide" dominate positive predictions, while "fuir" (flee) is the ultimate negative signal.

Lastly, our time-series analysis showed a massive spike in both praise and complaints in early 2021. We attribute this to the COVID-19 "digital push"—more people signing up online, but also more people frustrated by lockdown-related claim delays.

[4:40 – 5:00] CONCLUSION
To wrap up, three main takeaways:

Grammar matters: In French NLP, tools like LanguageTool are non-negotiable for informal text.

Context is King: CamemBERT’s 90% accuracy proves that domain-adapted French models beat "general" models every time.

The "Neutral" Problem: Rating-derived labels are inherently messy. Regression is a strong workaround, but the human element remains the biggest variable.