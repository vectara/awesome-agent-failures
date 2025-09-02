# Vectara HHEM (Hallucination Evaluation Model)

## Overview

Vectara's Hallucination Evaluation Model (HHEM) is designed to detect hallucinations in Large Language Model outputs, particularly effective for Retrieval-Augmented Generation (RAG) applications. HHEM is available in two variants:

**HHEM-2.1-Open**: Open-source model available on Hugging Face and Kaggle, suitable for self-hosted deployments and integration into evaluation frameworks. HHEM-2.1-Open only supports the English language.

**HHEM-2.3 (Commercial)**: Enterprise-grade model available through Vectara's platform API, automatically integrated with every query and optimized for production use. HHEM-2.3 is faster, supports longer context, and 10 languages.

## Key Features

**HHEM-2.1-Open:**
- **Lightweight**: <600MB RAM requirement, 335MB model size
- **High Performance**: Superior to GPT-3.5-Turbo and GPT-4 on benchmarks (95% accuracy)
- **Open Source**: Available via Hugging Face and Kaggle
- **No Token Limits**: Removes 512-token context restrictions
- **Fast Inference**: ~1.5 seconds on modern x86 CPU for 2k-token input
- **Framework Integration**: Native support in TruLens, benchmarked against RAGAs

**HHEM-2.3 (Commercial via Vectara API):**
- **Automatic Integration**: Built into every Vectara query API call
- **Production Optimized**: Enterprise-grade performance and reliability
- **Real-time Scoring**: Immediate hallucination likelihood assessment
- **Scalable**: Handles high-volume production workloads

## Pricing

**HHEM-2.1-Open:**
- **Open Source Model**: Free via Hugging Face and Kaggle
- **No Usage Restrictions**: Unlimited evaluations
- **Self-Hosted**: No licensing fees

**HHEM-2.3 (Commercial):**
- **Vectara Platform**: Contact Vectara for enterprise pricing
- **API Access**: Usage-based pricing through Vectara's platform

## Hallucination Leaderboard

HHEM-2.3 powers Vectara's [Hallucination Leaderboard](https://github.com/vectara/hallucination-leaderboard), which compares LLM performance at producing hallucinations when summarizing documents. This leaderboard is valuable for:
- **Model Selection**: Choose the LLM that hallucinates least for your RAG application
- **Performance Comparison**: Benchmark different models' factual consistency
- **Research Insights**: Track improvements in hallucination reduction across model versions

## Additional Resources

### Official Documentation & Models
- **Hugging Face Model**: [vectara/hallucination_evaluation_model](https://huggingface.co/vectara/hallucination_evaluation_model)
- **Kaggle Model**: [Hallucination Evaluation Model](https://www.kaggle.com/models/vectara/hallucination_evaluation_model)
- **Vectara API Documentation**: [Evaluate Factual Consistency](https://docs.vectara.com/docs/rest-api/evaluate-factual-consistency)
- **Hallucination Leaderboard**: [GitHub Repository](https://github.com/vectara/hallucination-leaderboard)

### Framework Integrations
- **TruLens Integration**: [HHEM Evaluator Quickstart](https://www.trulens.org/cookbook/models/local_and_OSS_models/Vectara_HHEM_evaluator/)
- **RAGAs Comparison**: [Evaluating RAG with RAGAs vs HHEM](https://www.vectara.com/blog/evaluating-rag) - Performance comparison showing HHEM's 35x speed advantage
- **Vectara Platform Integration**: Automatic HHEM-2.3 scoring with every query API call

### Research & Technical Papers
- **Research Paper**: [HHEM: A Breakthrough in LLM Reliability](https://arxiv.org/abs/2311.09261)
- **HHEM v2 Technical Blog**: [New and Improved Factual Consistency Scoring Model](https://www.vectara.com/blog/hhem-v2-a-new-and-improved-factual-consistency-scoring-model)
- **HHEM 2.1 Release**: [A Better Hallucination Detection Model](https://www.vectara.com/blog/hhem-2-1-a-better-hallucination-detection-model)

### News Coverage & Press Releases
- **BusinessWire Press Release** (March 2024): [Vectara Launches Factual Consistency Score](https://www.businesswire.com/news/home/20240326712242/en/Vectara-Launches-Factual-Consistency-Score-Powered-by-Upgraded-Hughes-Hallucination-Evaluation-Model-to-Enhance-Transparency-in-GenAI-Responses)
- **2 Million Downloads Milestone**: [Celebrating 2 Million Downloads of HHEM](https://www.vectara.com/blog/celebrating-2-million-downloads-of-hhem)
- **Automating Hallucination Detection**: [Introducing Vectara Factual Consistency Score](https://www.vectara.com/blog/automating-hallucination-detection-introducing-vectara-factual-consistency-score)

### Educational Content
- **RAG Evaluation Guide**: [Measuring Hallucinations in RAG Systems](https://www.vectara.com/blog/measuring-hallucinations-in-rag-systems)
- **Hallucination Correction**: [Correcting Hallucinations in Large Language Models](https://www.vectara.com/blog/correcting-hallucinations-in-large-language-models)
- **Vectara Documentation**: [Hallucination Evaluation Overview](https://docs.vectara.com/docs/learn/hallucination-evaluation)