---
title: "Activity 5 Reflection - Constituent Services Hub"
type: reflection
version: "1.0.0"
---

# Activity 5 Reflection

Answer each question in 3-5 sentences. Thoughtful, specific responses earn full credit.

## 1. PII Redaction in Practice

What PII categories did the Azure AI Language service detect in the Memphis 311 complaints? Did you notice any false positives (non-PII flagged as sensitive) or false negatives (PII that was missed)? How might PII detection requirements differ between a government agency processing citizen complaints and a commercial customer service system?

The service picked up things like names, phone numbers, and addresses from the Memphis complaints pretty easily. I didn't see too many glaring false positives, but sometimes it might accidentally flag a public park or street name as a personal address if somebody words it weirdly. A government agency definitely needs to scrub absolutely everything due to public record laws and strict privacy rules, whereas a random online store might just care about hiding credit card numbers and passwords while keeping your name visible to say "Hi [Name]!" in their CRM.

## 2. Sentiment as a Routing Signal

How could sentiment analysis be used to prioritize Memphis 311 complaints — for example, routing highly negative complaints to senior staff? What are the risks of relying solely on sentiment for prioritization (consider complaints that are neutral in tone but urgent in nature)? How would you combine sentiment with other signals for a more robust routing system?

You could definitely flag super angry or panicked complaints so an actual human manager sees them ASAP to de-escalate things before they get out of hand. But leaning entirely on sentiment is risky because someone might casually write, "hey just an fyi, the main bridge collapsed," which sounds completely neutral but is a massive emergency! To fix that, it's best to combine the sentiment score with key phrase extraction so you can spot "bridge collapsed" or "fire" regardless of whether the person used exclamation marks or not.

## 3. Multilingual Challenges

How accurately did the Language service detect the language of short versus long text samples? What challenges might arise with code-switching (mixing languages in a single message), which is common in multilingual communities? How would you handle a complaint where language detection confidence is low?

It generally nails long texts effortlessly, but super short messages like "Hola" or a single name can sometimes confuse it if the word exists in multiple languages. Code-switching is super tricky because if someone swaps between Spanish and English mid-sentence, the AI might just translate half of it poorly or guess the wrong language for the whole thing. If the text has a really low confidence score, the safest bet is probably just to flag it for a bilingual human to review instead of risking a botched AI translation.

## 4. CLU vs. Keyword Matching

Compare the CLU model's intent classification with the keyword-based fallback. In what scenarios did CLU perform better, and where did keyword matching suffice? What are the trade-offs of training and maintaining a custom CLU model versus using a simpler rule-based approach for a city's 311 system? *(If CLU was not configured in your environment, discuss how you would expect it to differ based on the training data in `data/intent_examples.json`.)*

The CLU model is way smarter at understanding the actual *meaning* of a messy, rambling sentence, whereas the keyword fallback only works if they happen to use the exact magic words like "pothole" or "status." Building a custom CLU model takes a ton of upfront work to train and tweak, but the payoff is that it can seamlessly handle weird phrasings. Keyword matching is super cheap and easy to set up for a city, but it's really easy to trick and misses the mark entirely if citizens get creative with their vocabulary.

## 5. Pipeline Design

Which step in your NLP pipeline was most critical to get right first, and why? If you were deploying this pipeline for production use handling thousands of complaints daily, what monitoring, fallback mechanisms, or error handling would you add? How would you measure pipeline health over time?

PII Redaction was definitely the most critical step to get right first because you absolutely cannot be sending sensitive citizen data downstream to other APIs or logs! If this was running in real life, I'd definitely want to add proper alert monitoring for when an Azure endpoint goes down, and wrap everything in solid try/except blocks so one bad text doesn't crash the whole pipeline. You could keep an eye on health by tracking how many complaints successfully route to a department compared to how many fall into an "unknown" bucket or throw errors.
