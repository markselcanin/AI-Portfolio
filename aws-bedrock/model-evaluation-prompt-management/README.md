# AWS Bedrock Model Evaluation & Prompt Management Lab

## Overview

This project documents hands-on work with **Amazon Bedrock** focused on foundation model testing, inference configuration, model comparison, prompt management, and reusable prompt development.

The lab included both a guided exercise and a DIY challenge. The goal was to move beyond introductory Generative AI concepts and gain practical experience configuring and evaluating models in Amazon Bedrock.

## Technologies & Services

- Amazon Bedrock
- Amazon Nova 2 Lite
- Meta Llama 3 8B Instruct
- Bedrock Playground
- Bedrock Compare Mode
- Bedrock Prompt Management
- Prompt variables and versioning
- Model inference parameters

---

## Guided Lab

### Model Selection

Used the Amazon Bedrock model catalog to explore available serverless foundation models.

Selected:

**Amazon Nova 2 Lite**

The model was opened in the Bedrock Chat Playground for testing.

### System Instructions

Configured the model with the following behavioral instructions:

```text
You are a helpful AI assistant that provides accurate and concise information. Do not include any inappropriate content.
```

### Inference Configuration

Experimented with model inference settings including:

```text
Temperature: 0
Top P: 0.5
Top K: 50
Maximum Output Tokens: Maximum
```

A stop sequence using `\n` was also tested.

### Troubleshooting

The Temperature, Top P, and Top K settings were initially unavailable because **Model Reasoning was enabled**.

Disabling Model Reasoning exposed the inference controls and allowed the parameters to be modified.

This demonstrated how Bedrock model features can affect which inference parameters are available.

---

## Model Reasoning Test

Model Reasoning was later enabled with:

```text
Reasoning Effort: Medium
```

Test prompt:

```text
What is 20% tip on a $47.50 dinner?
```

The model correctly calculated:

```text
Tip: $9.50
Total: $57.00
```

The internal reasoning content was redacted while the final response provided a structured explanation.

---

# Foundation Model Comparison

Bedrock **Compare Mode** was used to compare:

- Amazon Nova 2 Lite
- Meta Llama 3 8B Instruct

Both models received the same system instructions and test prompts.

## Test 1 — Generative AI Explanation

Prompt:

```text
Explain generative AI and hyperparameters.
```

### Observations

**Amazon Nova 2 Lite**

- Produced a significantly more detailed and technical response.
- Explained multiple generative AI architectures.
- Discussed training and inference hyperparameters.
- Included hyperparameter tuning approaches.
- Generated substantially more content than requested despite instructions to remain concise.

**Meta Llama 3 8B Instruct**

- Produced a shorter and easier-to-scan response.
- Clearly explained the major concepts.
- Provided fewer technical details than Nova.
- Better matched the instruction to remain concise.

### Observed Latency

| Model | Observed Response Measurements |
|---|---|
| Amazon Nova 2 Lite | 108 ms / 3,272 ms / 21,813 ms |
| Meta Llama 3 8B Instruct | 42 ms / 4,129 ms / 4,339 ms |

These measurements represent observations from this lab environment only and should not be interpreted as production benchmarks.

### Multimodal Capability

An important difference was discovered during testing:

**Meta Llama 3 8B Instruct did not support image uploads in this configuration.**

Bedrock indicated that uploaded images would be ignored and only the text portion of the prompt would be processed.

This is significant for customer-service scenarios that require analysis of:

- product photographs
- screenshots
- troubleshooting images
- other multimodal customer input

---

# Customer Service Model Testing

## Test 2 — Laptop Sales Conversation

Prompt:

```text
Create a simulated customer service conversation to demonstrate an AI assistant that helps a customer select the best laptop.
```

### Llama 3 8B Instruct

Observed latency:

```text
489 ms / 686 ms / 6,925 ms
```

The model produced a relatively concise customer-service interaction and gathered basic customer requirements before recommending products.

### Amazon Nova 2 Lite

Observed latency:

```text
3,216 ms / 2,537 ms / 16,338 ms
```

Nova produced a substantially more detailed conversation that included:

- customer discovery questions
- budget requirements
- portability requirements
- product comparison
- changing customer requirements
- follow-up recommendations

### Key Finding

The more detailed response also created additional risk.

Nova confidently generated specific laptop specifications, pricing, weights, accessory compatibility, and other details that would require verification before being presented to a real customer.

This demonstrated an important Generative AI principle:

> A response that appears more detailed or sophisticated is not automatically more reliable.

For production customer-service systems, product information should ideally be grounded in trusted product data rather than relying exclusively on the foundation model's internal knowledge.

---

# Instruction-Following Comparison

The models were then tested with a much more constrained prompt:

```text
Create a 5-turn conversation between a friendly customer service agent named Alex and a customer named Sarah. Sarah is looking for a lightweight laptop under $700 for travel. Alex should ask 2 clarifying questions and end with a product recommendation.
```

## Llama 3 8B Instruct

Observed latency:

```text
1,231 ms / 486 ms / 5,114 ms
```

### Results

Llama successfully:

- created five turns
- used the required names
- kept the scenario under the $700 budget
- addressed the travel requirement
- asked two clarifying questions

However, the product recommendation occurred before the final turn rather than having Alex **end with the recommendation**.

## Amazon Nova 2 Lite

Observed latency:

```text
4,819 ms / 8,370 ms / 49,810 ms
```

### Results

Nova successfully:

- used Alex and Sarah
- asked exactly two clarifying questions
- focused on travel and battery requirements
- recommended a product under the requested budget
- ended the core interaction with a recommendation

Nova demonstrated stronger adherence to several explicit instructions, although its response latency was considerably higher during this test.

---

# Prompt Management

After experimenting with one-time prompts in the Playground, the workflow moved into **Bedrock Prompt Management**.

Created:

```text
CustomerServiceDemo
```

Description:

```text
A prompt for generating realistic customer service conversations about laptops.
```

The prompt used a reusable variable:

```text
Create a 5-turn conversation between a friendly customer service agent named Alex and a customer named Sarah. Sarah is looking for a lightweight {{product}} under $700 for travel. Alex should ask 2 clarifying questions and end with a product recommendation.
```

Test variable:

```text
product = laptop
```

The prompt was tested, saved as a draft, and then versioned.

This demonstrated how prompt templates can be managed as reusable application assets rather than repeatedly entering static prompts.

---

# DIY Challenge

The lab concluded with an independent challenge.

## Objective

Create a new Bedrock prompt named:

```text
ProductDescriptionWriter
```

The reusable prompt contained four variables:

```text
Write a compelling product description for a {{product}} targeted at {{audience}}. The description should be {{length}} and use a {{tone}} tone. Include:
- 3 key features that matter most to this audience
- 1 benefit statement explaining why this product solves their problem
- A clear call-to-action at the end
```

## Test Variables

```text
product = wireless headphones
audience = remote workers
length = 2 paragraphs
tone = professional
```

Amazon Nova 2 Lite was selected and the prompt was tested successfully.

The generated output:

- targeted remote workers
- used a professional tone
- produced two paragraphs
- included three product features
- included a benefit statement
- ended with a call-to-action

The prompt was then:

1. Saved as a draft
2. Reviewed
3. Created as a version

This completed the DIY challenge without relying on the guided workflow.

---

# Lessons Learned

This lab demonstrated several practical Generative AI concepts.

## Model Selection Depends on Requirements

There is no universally "best" foundation model.

Factors such as:

- multimodal capabilities
- latency
- instruction following
- output quality
- verbosity
- factual reliability

can all influence model selection.

## Inference Settings Affect Model Behavior

Temperature, Top P, Top K, output length, reasoning options, and other settings affect how a model generates responses.

## More Detail Can Increase Hallucination Risk

A model providing more information can also introduce more opportunities for incorrect information.

Production AI systems should ground important factual responses against trusted data sources.

## Prompt Engineering Benefits from Structure

Explicit requirements produced more predictable responses than broad prompts.

## Reusable Prompts Should Be Managed Like Application Assets

Bedrock Prompt Management allows prompts to be:

- parameterized
- tested
- saved
- reused
- versioned

## Hands-On Testing Is Important

Reading about foundation models is different from directly observing how multiple models respond to the same requirements.

---

# Screenshots

Recommended project screenshots:

```text
/images/model-comparison.png
/images/product-description-prompt.png
/images/prompt-version.png
/images/bedrock-workflow-diagram.png
```

Suggested screenshots include:

1. Nova 2 Lite and Llama 3 8B running in Compare Mode
2. `ProductDescriptionWriter` with its prompt variables populated
3. Successfully created prompt version
4. Bedrock-generated workflow or architecture diagram

> Before publishing screenshots, remove or crop AWS account identifiers, usernames, ARNs, email addresses, and other sensitive information.

---

# Key Takeaway

The most important lesson from this exercise was that foundation-model evaluation requires looking beyond whether a model simply produces a correct-looking response.

Model selection involves tradeoffs between **capabilities, latency, instruction adherence, factual reliability, and application requirements**.

Amazon Bedrock provides a practical environment for experimenting with those tradeoffs while also providing tools for managing reusable prompts as part of a larger Generative AI application workflow.
