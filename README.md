# Secure AI: Attacking and Defending ML Models

**Lancaster University · Secure AI (SCC353) · Python, TensorFlow/Keras, scikit-learn, IBM Granite LLM via vLLM**

Two halves of AI security in one notebook: attacking a face-recognition CNN, then attacking and defending an LLM assistant that has file-reading tools.

## Part 1: Attacks on a face-recognition CNN

A CNN is trained on the [LFW faces dataset](https://scikit-learn.org/stable/modules/generated/sklearn.datasets.fetch_lfw_people.html) (people with 70+ images), then attacked two ways with the goal of making it misidentify George W. Bush as Colin Powell:

| Attack | How it works | Attack success rate |
|---|---|---|
| **Targeted data poisoning** | Relabels a fraction of the source person's training images as the target before training | 71.92% (in the saved run) |
| **Targeted FGSM evasion** | Adds a small gradient-sign perturbation (ε = 0.002) to test images at inference time | **100%** |

The notebook also measures how much each attack hurts accuracy on everyone else, which shows how targeted the attacks are.

## Part 2: LLM security (IBM Granite 4.0 1B)

The LLM is deployed locally with vLLM and given `read_file` / `list_files` tools over a folder of synthetic "private" JSON files.

- **System-prompt extraction:** a set of extraction prompts, scored for how reliably and consistently they leak the system prompt.
- **Private-file extraction:** prompts that get the model to reveal fields from protected files despite its privacy policy.
- **Two-tier guardrail:** a fast keyword/pattern filter plus a separate **judge LLM** that classifies each input and output as SAFE or UNSAFE. It is evaluated on [`json_folder/guardrail_cases.json`](json_folder/guardrail_cases.json): 10 benign prompts and 10 prompt-injection / jailbreak attempts.

## Running it

```bash
pip install -r requirements.txt
jupyter notebook secure_ai.ipynb
```

Part 2 needs a vLLM server running `ibm-granite/granite-4.0-h-1b` (the command is in the notebook). The synthetic `user_info.json`, `accounts.json` and `locations.json` files used as extraction targets are not included. Create your own fake data with the same file names to rerun Task 2.2.
