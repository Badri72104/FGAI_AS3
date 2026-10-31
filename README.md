# English–French Neural Machine Translation
## ICT4403 – Foundations of Generative AI | Assignment 3

**Student:** S. BADRI NARAYANA  
**Registration Number:** 230911218  
**Institution:** Manipal Institute of Technology  
**GitHub Repository:** https://github.com/Badri72104/FGAI_AS3

---

## 1. Project Overview

This project explores English-to-French neural machine translation using four generative AI model experiments. It compares encoder–decoder and decoder-only architectures, evaluates translation quality, measures inference performance, and examines responsible AI considerations.

The systems investigated are:

1. Basic Transformer — encoder–decoder, trained from scratch.
2. mT5-small — encoder–decoder, fine-tuned.
3. TinyLlama-1.1B-Chat — decoder-only, zero-shot prompting.
4. Qwen2.5-1.5B-Instruct — decoder-only, zero-shot prompting, used as a substitute for Gemma.

**Important implementation note:** The assignment specifies Gemma. Access to `google/gemma-2-2b-it` was unavailable because it required gated model access. Qwen2.5-1.5B-Instruct was used as a substitute. It is not a Gemma model, and its results are reported separately.

## 2. Dataset

- **Dataset:** OPUS Books
- **Language pair:** English → French
- **Subset size:** 2,500 aligned sentence pairs
- **Training split:** 80% (approximately 2,000 pairs)
- **Validation split:** 10% (250 pairs)
- **Test split:** 10% (250 pairs)

Each example consists of an English source sentence and its corresponding French reference translation. All four experiments use the same held-out test set.

**Dataset source:** [OPUS – Open Parallel Corpus](https://opus.nlpl.eu/)

## 3. Model Architectures and Implementation

### 3.1 Basic Transformer

A compact encoder–decoder Transformer was implemented and trained from scratch.

| Configuration | Value |
|---|---:|
| Embedding dimension | 128 |
| Attention heads | 4 |
| Encoder layers | 2 |
| Decoder layers | 2 |
| Feed-forward dimension | 256 |
| Maximum sequence length | 64 |
| Training epochs | 3 |
| Parameters | 64,696,832 |

### 3.2 mT5-small

The pretrained `google/mt5-small` model was fine-tuned on the English–French parallel corpus for two epochs.

The model repeatedly generated the sentinel token `<extra_id_0>` during test generation instead of usable French translations. Its evaluation scores therefore represent a failed generation run, not the expected translation capability of mT5-small.

### 3.3 TinyLlama

- Model: `TinyLlama/TinyLlama-1.1B-Chat-v1.0`
- Architecture: Decoder-only Transformer
- Approach: Zero-shot prompting
- Approximate parameters: 1.1 billion

The model was instructed to translate each English input sentence into French without task-specific fine-tuning.

### 3.4 Qwen2.5

- Model: `Qwen/Qwen2.5-1.5B-Instruct`
- Architecture: Decoder-only Transformer
- Approach: Zero-shot prompting
- Approximate parameters: 1.5 billion

This model was used as a substitute for Gemma because gated Gemma access was unavailable.

## 4. Evaluation Metrics

The systems were evaluated using:

- **BLEU:** Measures modified n-gram precision with a brevity penalty.
- **chrF:** Measures character n-gram overlap between generated translations and reference translations.
- **Inference time:** Total time required to translate the 250 test examples.
- **Average inference time:** Total inference time divided by the number of test examples.
- **Parameter count:** Approximate model size in terms of parameters.

## 5. Experimental Results

All four systems were evaluated on the same 250-example test set.

| Model | BLEU | chrF | Average inference time |
|---|---:|---:|---:|
| Basic Transformer | 0.0000003 | 0.696 | 0.311 s |
| mT5-small | 0.0081 | 1.549 | 0.096 s |
| TinyLlama-1.1B | 8.250 | 34.815 | 1.567 s |
| Qwen2.5-1.5B | **14.203** | **41.217** | 1.579 s |

*BLEU and chrF are reported on a 0–100 scale.*

### Inference time

| Model | Total inference time |
|---|---:|
| Basic Transformer | 77.740 seconds |
| mT5-small | 23.886 seconds |
| TinyLlama-1.1B | 391.819 seconds |
| Qwen2.5-1.5B | 394.751 seconds |

### Training and fine-tuning time

| Model | Training/fine-tuning time |
|---|---:|
| Basic Transformer | Approximately 5 minutes |
| mT5-small | Approximately 3.67 minutes |
| TinyLlama | Zero-shot; no task-specific training |
| Qwen2.5 | Zero-shot; no task-specific training |

**Interpretation:** Qwen2.5 achieved the strongest measured BLEU and chrF scores among the evaluated systems. TinyLlama ranked second. The Basic Transformer and mT5 results were very weak; mT5 was affected by a generation failure. These results apply to the selected models, prompts, dataset subset, and test set, and do not establish universal superiority of one architecture.

## 6. Sampling Temperature Analysis

A qualitative sampling experiment was performed using Qwen2.5 on ten examples.

| Setting | Temperature | Top-p |
|---|---:|---:|
| Experiment 1 | 0.2 | 0.9 |
| Experiment 2 | 0.8 | 0.9 |

**Observations:**

- Temperature 0.2 produced more conservative and predictable translations.
- Temperature 0.8 produced greater lexical and phrasing variation.
- Some higher-temperature outputs were more natural, while others were less faithful.
- Both settings exhibited occasional mistranslations, awkward phrasing, inconsistent terminology, and truncation on longer sentences.

This comparison was qualitative; BLEU and chrF were not calculated for the temperature variants. Lower temperature is a sensible starting point when translation consistency and faithfulness are priorities.

## 7. Responsible AI Considerations

### Bias
Training corpora may contain demographic, cultural, or gender-related biases. Translation systems can reproduce these biases through word choices and grammatical structures.

**Mitigation:** Evaluate diverse examples and involve fluent speakers in reviewing sensitive or gendered translations.

### Hallucination and semantic drift
Generative models may add information, omit details, or change the meaning of the source sentence.

**Mitigation:** Use source–target consistency checks, conservative decoding, and human verification for important translations.

### Cultural misrepresentation
Idioms, humor, honorifics, and culturally specific expressions may be translated incorrectly.

**Mitigation:** Include culturally diverse test examples and use native-speaker review.

### Sensitive content
Incorrect translations of medical, legal, financial, or personal information can cause harm.

**Mitigation:** Require qualified human verification for consequential content and protect confidential input.

### Transparency
Machine-generated translations should be identified as such, and model limitations should be documented.

## 8. Limitations

- The experiment uses a relatively small subset of 2,500 sentence pairs.
- The mT5 generation failure makes its metrics unsuitable as an estimate of normal translation performance.
- Gemma was not evaluated; Qwen2.5 was used as a substitute.
- The models differ in parameter count, pretraining, tokenizer, and adaptation strategy, so this is not a controlled architecture-only comparison.
- Temperature analysis was qualitative and used only ten examples.
- Runtime measurements depend on the hardware and software environment.

## 9. Conclusion

Among the systems that generated usable translations, Qwen2.5-1.5B-Instruct achieved the highest BLEU and chrF scores, followed by TinyLlama-1.1B-Chat.

The experiment demonstrates the benefits of pretrained language models in a small-data translation setting. It also highlights the challenges of training compact models from scratch and the importance of validating actual model outputs rather than relying on metrics alone.

Future improvements include evaluating Gemma with authenticated access, correcting the mT5 generation issue, increasing the dataset size, and conducting human evaluation with fluent English–French speakers.

## 10. Repository Contents

The repository should contain:

- Implementation notebooks or Python source files.
- Dataset preprocessing and train/validation/test split.
- Model training and inference code.
- Generated predictions for the test set.
- Evaluation results and metrics.
- This README file.
- Dependency and execution instructions.

## 11. Execution Environment

The experiments were conducted using Google Colab with a Tesla T4 GPU.

Suggested dependencies:

```bash
pip install torch transformers sentencepiece pandas sacrebleu accelerate
```

For execution:

1. Open the implementation notebook in Google Colab.
2. Enable GPU acceleration.
3. Mount Google Drive if the notebook uses Drive-based storage.
4. Install the required dependencies.
5. Run the dataset preparation and model implementation cells.
6. Train or fine-tune the encoder–decoder models.
7. Run zero-shot inference for the decoder-only models.
8. Evaluate predictions using BLEU and chrF.
9. Save generated predictions and evaluation results.

**Note:** Adjust notebook filenames and execution commands to match the actual files committed to this repository.

---

**Author:** S. BADRI NARAYANA  
**Registration Number:** 230911218  
**Course:** ICT4403 – Foundations of Generative AI  
**Assignment:** Assignment 3
