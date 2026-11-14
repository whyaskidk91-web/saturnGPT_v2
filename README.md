# saturnGPT V2
https://huggingface.co/3V4N3SC3NC3/saturnGPT_v2

## Creation process:
Created around mid November 2026.
Created with pytorch and written almost only by hand, and a lot of trial and error to learn how pytorch works.

## The model: 
This is a basic Transformer model in GPT2 Style with 124.6million parameters.

**Build:**
- It uses BPE tokenizer(~16k tokens)
- AdamW
- causal multi head self-attention
- bf16
- transformer(14 pre norm blocks with 12 attention heads per block)
- context length of 768 and d_model 768
- optional dropout
- gradient clipping
- cosine learn rate
- warmup
- weight decay
- GELU MLP


## Chat samples:


```
cuda 768
- Tokens loaded
- Model loaded 

You: Hello, nice to meet you!
Bot: Hello! How can I help you today?
You: What would be a good vacation destination?
Bot: A good vacation destination would be a beach in the Pacific Ocean.  Sandwich, Rhode Island, or SeaWorld are other popular destinations that offer beautiful beach destinations.  You can also visit the Tahoe Landmarks or the Golden Gate Bridge.  For a breathtaking sunset experience, a musty beach or a relaxing beach vacation would be a great way to experience the beauty of the Pacific Ocean and enjoy the natural beauty of the world around it.
You: What would be the best season to travel to the Pacific Ocean beach?
Bot: The best season to travel to the Pacific Ocean beach would be in spring and summer. The beach is a great place to relax and have fun. During spring the beach is a great time to relax and enjoy the sun and water.
You: Thanks this helped me a lot
Bot: You're welcome! Have a great day and ready to start your day with the basics like baking, baking, and basic baking.
```

## Pipeline:
tokenizer → pretrain → midtrain → SFT

**Datasizes:**
| training phase | Dataset | What got used | Size in Saturn |
|---|---|---|---|
| 1 | TinyStories V2 (GPT-4) | first ~800M characters | part of the ~1.73B pretraining tokens |
| 1 | FineWeb-Edu (sample-10BT) | streamed ~6.5B characters | the rest of the ~1.73B pretraining tokens |
| 2 | smol-smoltalk | full train split | ~412M tokens (mid-training) |
| 3 | everyday-conversations-llama3.1-2k	| ~2,250 conversations | SFT |
| 3 | Dolly-15k | 15,011 examples	 | SFT, together with the line above: ~23k training pairs |

## Results:
| Stage | Data | Tokens | Steps | LR | Final test loss |
|---|---|---|---|---|---|
| pretrain | TinyStories V2 + FineWeb-Edu | ~1.73B | 9500 | 6e-4 | 3.17 |
| midtrain | smol-smoltalk | ~412M | 3000 | 2e-4 | 1.85 |
| SFT | everyday-conversations + Dolly-15k | 23k examples | 2000 |  5e-5 | ~1.7–1.9 |

~20 minutes on one A100 per 1000 steps: ~4.5h training

## What saturnGPT version 2 can and cant do:
Saturn does create linguistic mostly valid sentences while grabbing up themes. But it often doesnt entirely stick to the topic and rather broader correlations as well as it misses critical data, so it often makes things up. Same with complex themes or niche topics, it prob will output more nonsense sounding texts.

## What i added/changed:
- weight decay
- RUN ME from huggingface
- heads run at once in multi head now

## Errors i fixed:
- fitting file names
- way better training phase material distribution
- training data gets streamed to avoid RAM overload
- cache can get cleared to avoid pushing over its limits when restarting in session
- END is now one token


## RUN MODEL:
Following code will run the model with one click in one cell. The weights will get loaded from my hugging face repository:

## FULL MODEL:
Important mention:
- The full model uses your google drive to store the weights

How to run:
- the agenda got updated to easier understand
- to load the data run the alphabet in order, to train run the roman numbers in order, to chat run the modern numbers in order
- 2 optional coding cells included: 
  to resume when disconnected in training phase 1
  to clear the cache
- A100 suggested for computing,T4 for running the model

Agenda:
- [0],[1],..: set up to chat
- [A],[B],..: prepare the data(once)
- [I],[II],..: training

## What I learned:
That frontier models must have burned billions of dollars.


## Limitations:
This is a small research model. Its outputs may be inaccurate,inconsistent or inappropriate. Its knowledge is small and it cant form complex english sentences, do medium/advanced maths or give reliable facts about the real world.

## Previous version(16M):
(insert link)

## Data credits:
- **TinyStories**: Ronen Eldan & Yuanzhi Li (Microsoft Research), 2023. CDLA-Sharing-1.0
- **FineWeb-Edu**: Hugging Face (Penedo, Lozhkov et al., "The FineWeb Datasets"), 2024. ODC-By 1.0
- **smol-smoltalk** & **everyday-conversations-llama3.1-2k**: Hugging Face TB (SmolLM team). Apache 2.0
- **databricks-dolly-15k**: Databricks, 2023. CC BY-SA 3.0
- Tokenizer pre-split regex adapted from OpenAI's GPT-2 tokenizer

