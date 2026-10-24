# SHEIN Social-Media Sentiment Analysis — Big Data Analytics Project 1

A reproducible **Google Colab + Python** project that uses the **official YouTube Data API v3** (not unauthorized webpage scraping) to collect public **SHEIN clothing-review video comments**. It cleans the data, classifies English opinions as positive/negative/neutral using a pretrained RoBERTa model, identifies **mixed reviews** by splitting comments into separate opinion segments, and exports charts and a marketing report.

**Academic objective:** roughly 2,000 social-media records -> EDA, limitations, and an evidence-based marketing recommendation. This is a social-listening study: YouTube comments are **not verified customer purchases or SHEIN website product reviews**. The target is a goal, not a guarantee; the program will not invent records.

## Fastest method: Google Colab

1. In your Google Cloud project, enable **YouTube Data API v3**: https://console.cloud.google.com/apis/library/youtube.googleapis.com
2. Create a **standard API key** at https://console.cloud.google.com/apis/credentials . Restrict it to **YouTube Data API v3**. Never send the actual key in chat or publish it.
3. Go to https://colab.research.google.com/ and upload `shein_sentiment_analysis.ipynb` (File -> Upload notebook).
4. In Colab, click the **key icon (Secrets)** in the left sidebar, add `YOUTUBE_API_KEY` as the secret name, paste your own API key as the value, and **enable notebook access** for the secret.
5. Click **Runtime -> Run all**. The notebook installs its dependencies, collects comments (or loads a cached raw CSV), runs predictions, saves graphs, and produces a ZIP of outputs for download.
6. If you want to select videos yourself, add their actual YouTube URLs in the notebook's `VIDEO_URLS` list (optional). If empty, the program searches relevant videos with the YouTube API.

**Colab Secrets are recommended.** The provided `.env` is a **blank credential template** and contains no functional key. You do not need to upload it to Colab. The notebook is standalone: it contains its own code and does not require uploading the `.py` file.

## Run locally (optional)

```bash
python -m pip install -r requirements.txt
# Edit the local .env template to add YOUR OWN key, then:
python shein_sentiment_analysis.py --target 2000
```

On a computer with only 4 GB RAM, use the Colab notebook rather than local model inference.

Test without a key (with only 5 **synthetic** comments, not for assignment evidence):

```bash
python shein_sentiment_analysis.py --demo
```

## Key concepts

- **Data source:** public top-level comments under YouTube videos about SHEIN clothing/dresses. Automatic queries are editable; user-provided video URLs override search.
- **Collection target:** 2,000 cleaned comments; attempts up to 3,000 raw records to compensate for basic cleaning.
- **Cleaning:** strip links, remove blank/very short comments, duplicate IDs and repeated text. A simple product-keyword flag is provided; it does **not** prove relevance.
- **Model:** `cardiffnlp/twitter-roberta-base-sentiment-latest` from Hugging Face; labels = positive, negative, neutral. Intended chiefly for English. No extra Hugging Face API key required.
- **Mixed comment:** derived flag if at least one individual segment is positive AND at least one negative. This is **not** a separately trained fourth AI class. Contrast words (e.g., "but", "however") also trigger a split, which is heuristic and may be imperfect.
- **Analysis:** sentiment counts/percentages, per-video chart, keyword-derived negative themes, written marketing recommendation, a 40-comment blank manual review sheet.
- **Bias:** YouTube commenters are not randomly selected or verified buyers. Search ranking, commenters' humor, sarcasm, mixed languages, omitted replies, and unavailable comments limit the claims.

## Output files

Generated automatically inside `outputs/` or the notebook's `/content/shein_project_outputs/` directory:

| File | Purpose |
|---|---|
| `review_comments_raw.csv` | Original collected records |
| `review_comments_clean.csv` | Cleaned sample and simple product-word indicator |
| `review_comments_analyzed.csv` | Predicted overall and segment-level sentiment |
| `sentiment_distribution.png` | Four-label distribution |
| `sentiment_by_video.png` | Sentiment breakdown by video |
| `negative_aspects.png` | Keyword-based negative opinion themes |
| `manual_validation_sample.csv` | Random sample for you to hand-label and check errors |
| `marketing_report.txt` | Results, recommendation, and limitations |
| `run_metadata.json` | Model, count, source, and execution date metadata |

## Troubleshooting

- **Missing key / SecretNotFoundError**: In Colab Secrets add exactly `YOUTUBE_API_KEY` and enable notebook access.
- **403 accessNotConfigured / API key not valid**: Confirm you selected the same Cloud project, enabled YouTube Data API v3, and applied the correct API restriction.
- **403 quotaExceeded**: Check project quota in Google Cloud Console; don't keep refreshing the API unnecessarily. Once raw data is cached, the notebook reuses it.
- **Fewer than 2,000 cleaned comments**: This reflects genuine public data availability/filters. Select more relevant high-comment review videos, set `FORCE_REFRESH=True`, or report the actual sample count. Do not fabricate records.
- **Comments disabled**: The script skips such videos. Some comments may be moderated, deleted, private, or inaccessible.
- **Weak sentiment on Urdu/Roman Urdu**: The chosen model is primarily English; manually check examples or switch to a multilingual model in an improved version.
- **Teacher specifically wants Instagram or reviews from a single SHEIN listing**: This YouTube project has a different data source. Confirm whether the lecturer allows YouTube comments before submitting.

## Security and academic integrity

- `.env` is in `.gitignore`. Never commit or share a filled `.env`; never include your live API key in the notebook source.
- In Cloud Console, restrict the key to **YouTube Data API v3**. Colab uses shared/dynamic server infrastructure, so fixed-IP restrictions usually aren't suitable for simple Colab notebooks.
- Use only permitted public API methods; respect YouTube's data/retention policies. No commenter name or account ID is saved in this project.
- Cite YouTube API documentation and model card in your report:
  - https://developers.google.com/youtube/data/getting-started
  - https://developers.google.com/youtube/data/reference/rest/v3/commentThreads/list
  - https://huggingface.co/cardiffnlp/twitter-roberta-base-sentiment-latest
