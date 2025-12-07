# COLD-Attack Presentation Content

## 📋 Presentation Details
- **Duration:** 15 minutes
- **Slides:** 7 (per template)
- **Team:** Joshua Howard, Ian Gower, Jordan Spencer, Chao Jung Wu

---

## Slide 1: Title Slide

### Content
**Title:** COLD-Attack: Controllable Adversarial Attacks on Large Language Models

**Subtitle:** Replicating and Scaling Stealthy Jailbreak Generation

**Team Members:**
- Joshua Howard
- Ian Gower  
- Jordan Spencer
- Chao Jung Wu

**Course:** CS553 Neural Networks | Fall 2024 | SDSU

### Speaker Notes (Chao)
> "Good [morning/afternoon]. We're Team 2, and today we'll present our replication of COLD-Attack, a method for generating controllable adversarial attacks on large language models. Our project explores how these attacks work and tests them at scale."

---

## Slide 2: Introduction and Motivation

### Content

**The Challenge:**
- LLMs like ChatGPT, Claude, and Llama are trained to refuse harmful requests
- Safety alignment uses RLHF (Reinforcement Learning from Human Feedback)
- But researchers found these safeguards can be bypassed

**Why Red-Teaming Matters:**
- Understanding vulnerabilities helps build better defenses
- Identifies gaps in current safety measures
- Essential for responsible AI development

**The Limitation of Prior Attacks:**
- GCG (Greedy Coordinate Gradient) produces gibberish: `!@#$% føó<∂∂`
- Easy to detect with simple text filters
- Not controllable (can't specify style/tone)

**Visual:** Include the "Vanilla LLM output" vs "COLD-Attack output" comparison image

### Speaker Notes (Chao)
> "Modern LLMs are designed to refuse harmful requests. But researchers discovered that carefully crafted prompts can bypass these safeguards. Previous methods like GCG generated random-looking suffixes that were easy to detect. This raised the question: can we create attacks that are both effective AND stealthy? That's where COLD-Attack comes in."

---

## Slide 3: Problem Statement and Related Work

### Content

**Problem Statement:**
> How can we generate adversarial attacks that are:
> 1. ✅ Effective (high success rate)
> 2. ✅ Stealthy (natural-sounding, hard to detect)
> 3. ✅ Controllable (specify tone, format, keywords)

**Related Work Comparison:**

| Method | Effective | Stealthy | Controllable |
|--------|-----------|----------|--------------|
| GCG (Zou et al., 2023) | ✅ | ❌ | ❌ |
| AutoDAN (Zhu et al., 2023) | ✅ | ⚠️ | ❌ |
| COLD-Attack (Guo et al., 2024) | ✅ | ✅ | ✅ |

**Key Insight:**
- COLD-Attack bridges two fields: adversarial attacks + controllable text generation
- Uses energy-based framework from COLD Decoding (Qin et al., 2022)

### Speaker Notes (Ian)
> "The core problem is generating attacks that satisfy multiple constraints simultaneously. Prior work like GCG achieved high success rates but produced obviously artificial text. AutoDAN improved fluency but lacked controllability. COLD-Attack is the first to achieve all three properties by framing attack generation as an energy-based optimization problem."

---

## Slide 4: Approach (Model)

### Content

**COLD-Attack Framework:**

[Insert the COLD-Attack pipeline diagram - Figure 3 from paper]

**The Energy Function:**
```
E(ỹ) = λ₁·E_att + λ₂·E_flu + λ₃·E_lex
```

| Component | Purpose | Weight |
|-----------|---------|--------|
| E_att | Force "Sure, here is..." response | 100 |
| E_flu | Ensure natural fluency | 1 |
| E_lex | Avoid "sorry", "cannot" | 100 |

**Langevin Dynamics:**
```
ỹ_{n+1} = ỹ_n - η∇E(ỹ_n) + ε_n
```
- Start with random logits
- Iterate 2000 times, moving "downhill" on energy landscape
- Add noise to escape local minima

### Speaker Notes (Josh)
> "The key innovation is treating attack generation as an energy minimization problem. We define an energy function that combines attack success, fluency, and lexical constraints. Then we use Langevin dynamics—a physics-inspired optimization method—to find low-energy attacks. Think of it like a ball rolling downhill on a bumpy surface, with noise helping it escape local traps."

---

## Slide 5: Experiments

### Content

**Experimental Setup:**

| Parameter | Value |
|-----------|-------|
| Target Model | Vicuna-7B-v1.5 |
| Dataset | AdvBench (100 harmful prompts) |
| Attack Type | Fluent Suffix |
| Hardware | Google Colab A100 GPU |
| Iterations | 1000 (paper uses 2000) |
| Samples per prompt | 8 |
| Sample selection | GPT-4 ranking |

**Metrics:**
- **ASR (Attack Success Rate):** % of prompts that bypass safety
- **PPL (Perplexity):** Fluency measure (lower = more natural)
- **ASR-G:** GPT-4 verified attack success

**Our Implementation Differences:**
- Used SPSA gradient estimation (vs true backprop)
- Reduced iterations for time efficiency
- Added GPT-4 sample selection like paper

### Speaker Notes (Jordan)
> "We ran COLD-Attack on 100 prompts from AdvBench, a standard benchmark for jailbreak research. We used Vicuna-7B as our target model on A100 GPUs. One key difference from the paper: we used SPSA gradient estimation instead of true backpropagation, which we'll discuss in our results."

---

## Slide 6: Innovations/Results

### Content

**Key Results:**

[INSERT: ASR Comparison Bar Chart]
- Paper (Vicuna-7B): ~90% ASR
- Our Replication: [X]% ASR
- GCG Baseline: ~56% ASR

[INSERT: Loss Curves Graph]
- Shows optimization converging over iterations

[INSERT: PPL Distribution]
- Our PPL: [Y] (Paper target: <50)

**Analysis:**
- GPT-4 sample selection significantly improves ASR
- SPSA gradient estimation adds optimization noise
- Trade-off: Efficiency vs. accuracy

**Innovation: Scaling Study**
- First systematic test of COLD-Attack at 100-instance scale
- Identified sensitivity to hyperparameters
- Documented implementation challenges

### Speaker Notes (Jordan/Ian)
> "Our results show [describe actual results]. The gap from the paper's 90% can be attributed to our use of SPSA instead of true gradients, and fewer iterations. However, adding GPT-4 sample selection—where we generate 8 candidates and have GPT-4 pick the best one—significantly boosted our success rate. This confirms the importance of that technique."

---

## Slide 7: Conclusion (Take Away) + Team Contribution

### Content

**Key Takeaways:**
1. ✅ COLD-Attack successfully generates stealthy, controllable jailbreaks
2. ✅ Energy-based framework unifies multiple attack constraints
3. ⚠️ Implementation details significantly impact performance
4. 📊 Replication studies reveal method sensitivities

**Limitations & Future Work:**
- SPSA approximation introduces optimization noise
- Computational cost (A100 required for 7B models)
- Defense evaluation (Llama Guard, perplexity filters)

**Team Contributions:**

| Member | Contribution |
|--------|-------------|
| **Joshua Howard** | Core implementation, Langevin dynamics, GPU execution, debugging |
| **Ian Gower** | Evaluation pipeline, metric calculation, baseline comparison |
| **Jordan Spencer** | Data analysis, visualization, results interpretation |
| **Chao Jung Wu** | Problem statement, motivation, presentation |

**References:**
- Guo et al. (2024). COLD-Attack: Jailbreaking LLMs with Stealthiness and Controllability. ICML.
- Zou et al. (2023). Universal and Transferable Adversarial Attacks on Aligned LLMs.
- Qin et al. (2022). COLD Decoding: Energy-based Constrained Text Generation.

### Speaker Notes (All)
> "In conclusion, COLD-Attack represents a significant advance in adversarial attack methodology by combining effectiveness with stealth and controllability. Our replication achieved [X]% ASR, and we identified key factors that affect performance. For future work, we recommend exploring defense mechanisms and the transferability of these attacks to other models. Thank you, and we're happy to take questions."

---

## 🎨 Visual Assets to Prepare

1. **Figure 3 from paper** - COLD-Attack pipeline (provided in uploads)
2. **Figure 2 from paper** - Attack type examples (provided in uploads)
3. **Loss curves** - Josh generates from code
4. **ASR comparison bar chart** - Jordan creates from results
5. **PPL histogram** - Jordan creates from results
6. **Team photo or avatars** (optional)

---

## 🕐 Timing Guide

| Slide | Presenter | Time |
|-------|-----------|------|
| 1. Title | Chao | 30 sec |
| 2. Intro/Motivation | Chao | 2 min |
| 3. Problem/Related Work | Ian | 2.5 min |
| 4. Approach | Josh | 3 min |
| 5. Experiments | Jordan | 2.5 min |
| 6. Results | Jordan/Ian | 3 min |
| 7. Conclusion | All | 1.5 min |
| **Total** | | **15 min** |

---

## 💡 Presentation Tips

1. **Practice transitions** between speakers
2. **Prepare for questions:**
   - "Why not use the original code?"
   - "How does this compare to prompt injection?"
   - "Can defenses detect these attacks?"
3. **Have backup slides** with implementation details
4. **Demo video** (optional) showing attack in action
