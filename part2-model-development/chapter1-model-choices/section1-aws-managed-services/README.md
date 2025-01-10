# AWS Managed AI Services

When model development from scratch is overkill, managed AI services can be a good start. It's *token-based pricing* so there comes a tipping point where overall cost for developing model from scratch and then serve it on custom server gets cheaper. Still, since it's fully managed service by AWS, their *performance(both in hardware and algorithm) is optimized* at a decent level and available in many availability zones.

* Text
    * **Comprehend** : supervised tasks like NER, sentiment analysis or categorization
    * **Translate** : language translation for content localization
    * **Kendra** : document search service where its source can be S3, Google Drive etc(incremental learning enabled)
    * **Lex** : chatbot creator with integration with Lambda(like RAG), Comprehend, Kendra
* Vision
    * **Textract** : extract text from image, PDF
    * **Rekognition** : object detection(and labeling), facial search
    * **A2I**(Augmented AI) : enable human evaluation on low confidence result from Textract and Rekognition
    * **Lookout** : detectors monitor datasets to find anomalies by optimizing configured measure
* Speech
    * **Polly** : text to speech with custom vocabulary reinforcement(i.e. how certain term should be pronounced)
    * **Transcribe** : speech to text with PII redaction, automatic language identification and custom vocabulary reinforcement
* Recommendations
    * **Personalize** : collection of recipes for different recommendation purpose(ex. POPULAR_ITEMS, RELATED_ITEMS)
