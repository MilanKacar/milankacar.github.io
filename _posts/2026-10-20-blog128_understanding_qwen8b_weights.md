---
layout: post
title: "#128 How an LLM Runs on a GPU: Qwen3-8B, Memory vs Compute"
categories: [AI, Software Engineering, GPU]
tags: [LLM, Qwen3, GPU, CUDA, vLLM, Quantization, Visualization]
difficulty: Medium
---

I wrote this while moving a JSON-extraction service (a quantized 8B model on a single T4, around ten concurrent users) to a bigger GPU on Azure. The question "which GPU, and how fast will it be?" turned out to be answerable with arithmetic you can do on paper. The numbers in the widgets all come from the model's config, so you can check every one of them.

<style>
.lq{--lq-line:rgba(128,128,128,.4);--lq-soft:rgba(128,128,128,.12);--lq-acc:#2a78d6;--lq-grn:#1baf7a;--lq-org:#eb6834;--lq-vio:#8a7bea;border:1px solid var(--lq-line);border-radius:12px;padding:16px;margin:24px 0;font-size:15px;line-height:1.5;color:inherit;background:transparent;overflow-x:auto}
.lq *{box-sizing:border-box}
.lq button,.lq select,.lq input{font:inherit;color:inherit}
.lq button{background:var(--lq-soft);border:1px solid var(--lq-line);border-radius:8px;padding:6px 12px;cursor:pointer}
.lq button:hover{border-color:var(--lq-acc)}
.lq button.on{background:rgba(42,120,214,.25);border-color:var(--lq-acc)}
.lq button:focus-visible,.lq select:focus-visible,.lq input:focus-visible{outline:2px solid var(--lq-acc);outline-offset:2px}
.lq select,.lq input[type=number]{background:var(--lq-soft);border:1px solid var(--lq-line);border-radius:8px;padding:6px 8px;max-width:100%}
.lq select option{background:Canvas;color:CanvasText}
.lq input[type=range]{accent-color:#2a78d6;flex:1;min-width:120px}
.lq-mut{opacity:.7}
.lq-sm{font-size:12px}
.lq-row{display:flex;align-items:center;gap:12px;flex-wrap:wrap;margin:0 0 10px}
.lq-row label{display:flex;align-items:center;gap:8px}
.lq-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(240px,1fr));gap:12px;margin-top:12px}
.lq-fld{display:flex;flex-direction:column;gap:4px;font-size:13px}
.lq-card{border:1px solid var(--lq-line);border-radius:12px;padding:12px 14px}
.lq-m{display:flex;justify-content:space-between;gap:12px;font-size:13px;padding:3px 0}
.lq-m span:first-child{opacity:.7}
.lq-badge{display:inline-block;font-size:12px;padding:3px 10px;border-radius:8px;margin-top:8px;border:1px solid var(--lq-line)}
.lq-badge.mem{background:rgba(42,120,214,.22);border-color:var(--lq-acc)}
.lq-badge.cmp{background:rgba(27,175,122,.22);border-color:var(--lq-grn)}
.lq-badge.bad{background:rgba(227,73,72,.22);border-color:#e34948}
.lq-flow{display:flex;align-items:stretch;gap:8px;flex-wrap:wrap;margin:12px 0}
.lq-bx{flex:1;min-width:150px;border:1px solid var(--lq-line);border-radius:8px;padding:10px 12px;font-size:13px}
.lq-bx small{display:block;opacity:.7;font-size:11px;margin-bottom:2px}
.lq-bx.op{background:rgba(42,120,214,.18);border-color:var(--lq-acc)}
.lq-ar{align-self:center;opacity:.6;font-size:18px}
.lq-mg{display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:8px;margin-top:10px}
.lq-mc{background:var(--lq-soft);border-radius:8px;padding:8px 12px}
.lq-mc small{display:block;font-size:11px;opacity:.7}
.lq-mc b{font-weight:600;font-size:15px}
.lq-bar{display:flex;height:22px;border-radius:6px;overflow:hidden;background:var(--lq-soft)}
.lq-seg{height:100%;padding:0!important;border:0!important;border-radius:0!important;background:none}
.lq-leg{display:flex;gap:16px;flex-wrap:wrap;margin-top:6px;font-size:12px}
.lq-sw{display:inline-block;width:10px;height:10px;border-radius:2px;margin-right:4px}
.lq-chips{display:grid;grid-template-columns:repeat(auto-fill,minmax(150px,1fr));gap:6px;margin:10px 0}
.lq-chip{text-align:left;font-size:12px!important;padding:6px 8px!important;line-height:1.3}
.lq table.lq-t{width:100%;border-collapse:collapse;font-size:13px}
.lq table.lq-t td,.lq table.lq-t th{border-bottom:1px solid var(--lq-line);padding:5px 8px;text-align:left;vertical-align:top}
.lq code{font-size:12px}
.sm-row{display:flex;align-items:center;gap:8px;margin:6px 0}
.sm-lab{min-width:132px;font-size:12px;opacity:.85}
.sm-strip{display:flex;flex-wrap:wrap;gap:3px}
.sm-grid{display:flex;flex-direction:column;gap:3px;margin:6px 0}
.sm-c{display:inline-flex;align-items:center;justify-content:center;border:1px solid var(--lq-line);border-radius:4px;font-size:11px;font-variant-numeric:tabular-nums}
.sm-hl{background:rgba(42,120,214,.16);border-radius:6px}
.sm-tok{display:flex;gap:8px;flex-wrap:wrap;margin:10px 0}
.sm-t{border:1px solid var(--lq-line);border-radius:8px;padding:6px 12px;text-align:center;display:flex;flex-direction:column}
.sm-t small{opacity:.7;font-size:11px}
.sm-sent{font-size:18px;margin:6px 0}
.sm-bar{position:relative;height:16px;flex:1;min-width:120px;background:var(--lq-soft);border-radius:4px;overflow:hidden}
.sm-bar i{position:absolute;top:0;bottom:0}
.sm-val{min-width:70px;text-align:right;font-size:12px;font-variant-numeric:tabular-nums}
.sm-note{font-size:12px;opacity:.8;margin-top:6px;line-height:1.5}
.sm-desc ul{margin:6px 0 0 18px;padding:0}
.sm-desc li{margin:5px 0;font-size:14px}
@media (max-width:640px){.sm-row{flex-wrap:wrap}.sm-lab{min-width:100%}}
</style>

> **TL;DR**: A GPU does not run "a model". It runs thousands of tiny threads that each compute a few multiplications and additions. In this post we follow one token through **Qwen3-8B**, starting at its real `config.json`, count every weight, FLOP and byte, find out why generating text is *memory-bound*, compare an NVIDIA T4 with an A100, and finally see how **CUDA indexing** (`threadIdx`, `blockIdx`, `blockDim`, grids) maps the matrix-vector products onto the hardware. Everything has a widget you can play with.

I wrote this while moving a JSON-extraction service (a quantized 8B model on a single T4, around ten concurrent users) to a bigger GPU on Azure. The question "which GPU, and how fast will it be?" turned out to be answerable with arithmetic you can do on paper. The numbers in the widgets all come from the model's config, so you can check every one of them.

**All interactive widgets work in the light and the dark theme**: they inherit your text color and use transparent backgrounds.

## Contents

1. [Where the numbers come from: `config.json`](#config)
2. [The three ideas: FLOPs, bytes, arithmetic intensity](#basics)
3. [The architecture, drawn with the config values](#architecture)
4. [One token's journey: an animated simulation (interactive)](#journey)
5. [The KV cache](#kvcache)
6. [The hardware: T4, A100, H100 and what Azure offers](#hardware)
7. [Memory-bound or compute-bound? The full calculation (interactive)](#roofline)
8. [Prefill: why reading the prompt is a different problem](#prefill)
9. [Quantization: what INT4, FP8 and FP16 really change](#quant)
10. [CUDA indexing: threads, blocks and grids (interactive)](#cuda)
11. [Reading a pipeline timeline](#timeline)
12. [A vLLM checklist](#vllm)
13. [Limits of this post and sources](#sources)

<a id="config"></a>

## 1. Where the numbers come from: `config.json`

Every model on Hugging Face ships a small file called `config.json`. It does not contain any weights, only the **shape** of the network. Everything in this post is derived from it. This is the real file for Qwen3-8B ([source](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json), fetched 2026-10-20):

```json
{
  "architectures": ["Qwen3ForCausalLM"],
  "attention_bias": false,
  "attention_dropout": 0.0,
  "bos_token_id": 151643,
  "eos_token_id": 151645,
  "head_dim": 128,
  "hidden_act": "silu",
  "hidden_size": 4096,
  "initializer_range": 0.02,
  "intermediate_size": 12288,
  "max_position_embeddings": 40960,
  "max_window_layers": 36,
  "model_type": "qwen3",
  "num_attention_heads": 32,
  "num_hidden_layers": 36,
  "num_key_value_heads": 8,
  "rms_norm_eps": 1e-06,
  "rope_scaling": null,
  "rope_theta": 1000000,
  "sliding_window": null,
  "tie_word_embeddings": false,
  "torch_dtype": "bfloat16",
  "transformers_version": "4.51.0",
  "use_cache": true,
  "use_sliding_window": false,
  "vocab_size": 151936
}
```

Only ten of those keys matter for performance:

| Key | Value | What it controls |
|---|---|---|
| `hidden_size` | 4,096 | Width of the vector that represents one token everywhere in the network |
| `num_hidden_layers` | 36 | How many times the attention + MLP pair is repeated |
| `num_attention_heads` | 32 | Number of **query** heads. This drives attention FLOPs |
| `num_key_value_heads` | 8 | Number of **key/value** heads. This drives KV-cache bytes (grouped-query attention) |
| `head_dim` | 128 | Size of each head. 32 × 128 = 4,096 = `hidden_size` |
| `intermediate_size` | 12,288 | Width of the MLP in the middle (3× `hidden_size`) |
| `vocab_size` | 151,936 | Rows of the embedding table and columns of the output head |
| `tie_word_embeddings` | false | Embedding table and output head are two **separate** matrices |
| `sliding_window` | null | Full attention: every token sees *all* earlier tokens |
| `max_position_embeddings` | 40,960 | Native context limit |

Three more keys matter for deployment:

- `torch_dtype: bfloat16`. The T4 has **no bf16 support**, so on a T4 you must run FP16 (`--dtype half` in vLLM) or a quantized checkpoint.
- `rope_theta: 1000000` is the base for rotary position encoding. A large value helps with long contexts.
- `use_cache: true` is the KV cache from section 5.

### Try it: compute everything from the config

Change any value (or paste the numbers from another model's `config.json`) and see parameters, FLOPs per token and KV-cache size update. The defaults are Qwen3-8B.

<div class="lq" id="lqA">
<div class="lq-grid" id="lqA-in"></div>
<div class="lq-row" style="margin-top:10px"><label><input type="checkbox" id="lqA-tie"> tie_word_embeddings</label><button type="button" id="lqA-reset">Reset to Qwen3-8B</button></div>
<div id="lqA-out" style="margin-top:12px"></div>
</div>
<script>
(function(){
var F=[["hidden_size",4096],["num_hidden_layers",36],["num_attention_heads",32],["num_key_value_heads",8],["head_dim",128],["intermediate_size",12288],["vocab_size",151936],["context L (tokens)",8000]];
var inEl=document.getElementById("lqA-in"),out=document.getElementById("lqA-out"),tie=document.getElementById("lqA-tie");
function nf(x){return Math.round(x).toLocaleString("en-US")}
function sz(x){if(x>=1e9)return (x/1e9).toFixed(2)+" GB";if(x>=1e6)return (x/1e6).toFixed(1)+" MB";return nf(x)+" B"}
F.forEach(function(f,i){var d=document.createElement("label");d.className="lq-fld";d.innerHTML='<span class="lq-mut">'+f[0]+'</span><input type="number" min="1" id="lqA-f'+i+'" value="'+f[1]+'">';inEl.appendChild(d);d.querySelector("input").addEventListener("input",calc)});
function v(i){var x=+document.getElementById("lqA-f"+i).value;return x>0?x:1}
function calc(){
var H=v(0),NL=v(1),nh=v(2),kv=v(3),hd=v(4),I=v(5),V=v(6),L=v(7);
var q=H*nh*hd,k=H*kv*hd,o=nh*hd*H,m=3*H*I,layer=q+2*k+o+m;
var emb=V*H,head=tie.checked?0:V*H,total=NL*layer+emb+head;
var fl=2*(NL*layer+V*H),att=4*NL*nh*hd,kvt=2*NL*kv*hd*2,kvfull=2*NL*nh*hd*2;
var R=[
["Wq","hidden × (heads·head_dim)",nf(q)],
["Wk and Wv (each)","hidden × (kv_heads·head_dim)",nf(k)],
["Wo","(heads·head_dim) × hidden",nf(o)],
["MLP (gate, up, down)","3 × hidden × intermediate",nf(m)],
["Weights per layer","sum of the above",nf(layer)],
["All layers","layers × per layer",nf(NL*layer)],
["Embedding table","vocab × hidden",nf(emb)],
["Output head",tie.checked?"tied: shares the embedding":"vocab × hidden",nf(head)],
["Total parameters","",nf(total)+" ("+(total/1e9).toFixed(2)+" B)"],
["Size in FP16 / INT4","params × 2 B / ≈ params × 0.5 B",sz(total*2)+" / "+sz(total*0.5)],
["Weight FLOPs per token","2 × (layer weights + output head)",(fl/1e9).toFixed(2)+" GFLOPs"],
["Attention FLOPs per token","4 × layers × heads × head_dim × L",(att*L/1e9).toFixed(2)+" GFLOPs at L = "+nf(L)],
["KV cache per token","2 × layers × kv_heads × head_dim × 2 B",nf(kvt)+" B"],
["KV cache per user at L","per token × L",sz(kvt*L)],
["Without grouped-query attention","same, but kv_heads = heads",sz(kvfull*L)+" ("+(nh/kv).toFixed(1)+"× larger)"]
];
var h='<table class="lq-t"><tr><th>Quantity</th><th>Formula</th><th>Value</th></tr>';
R.forEach(function(r){h+="<tr><td>"+r[0]+'</td><td class="lq-mut">'+r[1]+"</td><td><b>"+r[2]+"</b></td></tr>"});
var note=(nh*hd===H)?"":'<p class="lq-sm">Note: heads × head_dim = '+nf(nh*hd)+', which differs from hidden_size. The formulas above still hold because the Q and O matrices use heads × head_dim.</p>';
out.innerHTML=h+"</table>"+note;
}
tie.addEventListener("change",calc);
document.getElementById("lqA-reset").addEventListener("click",function(){F.forEach(function(f,i){document.getElementById("lqA-f"+i).value=f[1]});tie.checked=false;calc()});
calc();
})();
</script>

<a id="basics"></a>

## 2. The three ideas: FLOPs, bytes, arithmetic intensity

A GPU is limited by two different things, and which one hurts depends on the job.

- **FLOPs** (floating-point operations): one multiply or one add is one FLOP. A multiply-then-add counts as **2 FLOPs**. The GPU's calculating speed is measured in TFLOPS (10¹² FLOPs per second).
- **Bytes moved**: every number the GPU computes with first has to be fetched from its memory (VRAM). The fetching speed is the **memory bandwidth**, measured in GB/s.
- **Arithmetic intensity** = FLOPs ÷ bytes fetched, in FLOP/byte. It says how much math you do per trip to memory.

Think of a cook and a fridge in another building. Bandwidth is how fast you can carry ingredients, TFLOPS is how fast you can chop. If you chop only a little per trip, you wait for the walk (**memory-bound**). If you carry a lot and chop for ages, the knife is the limit (**compute-bound**).

### The golden rule for matrix × vector

A matrix with `m` rows and `k` columns times a vector of length `k` gives `m` outputs. Each output is a dot product: `k` multiplies and `k` adds.

```
FLOPs = 2 × m × k          (2 per weight)
bytes = m × k × bytes_per_weight    (each weight is fetched once)
intensity = 2 / bytes_per_weight   → 1 FLOP/byte for FP16
```

One FLOP per byte is tiny. If you now multiply the **same** matrix by `n` vectors at once (n users), the weights are still fetched once but used `n` times, so intensity becomes `n` FLOP/byte. This is why batching users is so effective.

### The ridge point

Every GPU has a balance point called the **ridge point**:

```
ridge = peak FLOPS ÷ peak bandwidth
```

For an A100 that is 312 TFLOPS ÷ 1.935 TB/s ≈ **161 FLOP/byte**. Below it you are memory-bound, above it compute-bound. Hold on to the number 161. In section 7 we will see how far below it LLM decoding sits.

<a id="architecture"></a>

## 3. The architecture, drawn with the config values

An LLM generates text by repeating one loop: *take the text so far, output a score for every possible next token, pick one, append it.* Inside the model every token is a vector of 4,096 numbers that travels through a stack of identical layers.

<div class="lq">
<svg viewBox="0 0 680 600" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-labelledby="lq-arch-t lq-arch-d" font-size="13">
<title id="lq-arch-t">Qwen3-8B architecture with config.json values</title>
<desc id="lq-arch-d">A token ID passes through an embedding table, then 36 identical layers, each containing an attention block with 32 query heads and 8 key-value heads and an MLP block that expands 4096 to 12288, then an output head that produces scores for 151936 vocabulary tokens.</desc>
<defs><marker id="lq-arr" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>
<rect x="240" y="16" width="200" height="40" rx="8" fill="rgba(128,128,128,.12)" stroke="currentColor" stroke-opacity=".4"/>
<text x="340" y="41" text-anchor="middle" fill="currentColor">Token ID</text>
<line x1="340" y1="56" x2="340" y2="82" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-arr)"/>
<rect x="190" y="84" width="300" height="52" rx="8" fill="rgba(128,128,128,.12)" stroke="currentColor" stroke-opacity=".4"/>
<text x="340" y="106" text-anchor="middle" fill="currentColor" font-weight="600">Embedding lookup</text>
<text x="340" y="124" text-anchor="middle" fill="currentColor" opacity=".75" font-size="12">vocab_size 151,936 × hidden_size 4,096</text>
<line x1="340" y1="136" x2="340" y2="160" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-arr)"/>
<rect x="40" y="162" width="600" height="262" rx="20" fill="none" stroke="currentColor" stroke-opacity=".5" stroke-dasharray="6 4"/>
<text x="60" y="186" fill="currentColor" font-weight="600">num_hidden_layers: 36, repeated</text>
<rect x="60" y="202" width="260" height="206" rx="12" fill="rgba(27,175,122,.16)" stroke="#1baf7a"/>
<text x="190" y="230" text-anchor="middle" fill="currentColor" font-weight="600">Attention</text>
<text x="190" y="262" text-anchor="middle" fill="currentColor" font-size="12">num_attention_heads: 32</text>
<text x="190" y="284" text-anchor="middle" fill="currentColor" font-size="12">num_key_value_heads: 8</text>
<text x="190" y="306" text-anchor="middle" fill="currentColor" font-size="12">head_dim: 128</text>
<text x="190" y="338" text-anchor="middle" fill="currentColor" font-size="12" opacity=".8">sliding_window: none</text>
<text x="190" y="360" text-anchor="middle" fill="currentColor" font-size="12" opacity=".8">every token sees all L tokens</text>
<line x1="320" y1="305" x2="358" y2="305" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-arr)"/>
<rect x="360" y="202" width="260" height="206" rx="12" fill="rgba(138,123,234,.18)" stroke="#8a7bea"/>
<text x="490" y="230" text-anchor="middle" fill="currentColor" font-weight="600">MLP (SwiGLU)</text>
<text x="490" y="262" text-anchor="middle" fill="currentColor" font-size="12">hidden_size: 4,096</text>
<text x="490" y="284" text-anchor="middle" fill="currentColor" font-size="12">intermediate_size: 12,288</text>
<text x="490" y="306" text-anchor="middle" fill="currentColor" font-size="12">hidden_act: silu</text>
<text x="490" y="338" text-anchor="middle" fill="currentColor" font-size="12" opacity=".8">gate and up: 4,096 → 12,288</text>
<text x="490" y="360" text-anchor="middle" fill="currentColor" font-size="12" opacity=".8">down: 12,288 → 4,096</text>
<line x1="340" y1="424" x2="340" y2="450" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-arr)"/>
<rect x="190" y="452" width="300" height="52" rx="8" fill="rgba(128,128,128,.12)" stroke="currentColor" stroke-opacity=".4"/>
<text x="340" y="474" text-anchor="middle" fill="currentColor" font-weight="600">LM head</text>
<text x="340" y="492" text-anchor="middle" fill="currentColor" opacity=".75" font-size="12">4,096 → 151,936, not tied</text>
<line x1="340" y1="504" x2="340" y2="530" stroke="currentColor" stroke-width="1.5" marker-end="url(#lq-arr)"/>
<rect x="220" y="532" width="240" height="40" rx="8" fill="rgba(128,128,128,.12)" stroke="currentColor" stroke-opacity=".4"/>
<text x="340" y="557" text-anchor="middle" fill="currentColor">Score for every token</text>
</svg>
</div>

Inside each layer there are two blocks, each wrapped in a **residual connection** (the block's output is *added* to its input, never replaces it):

- **Attention** lets the current token look at earlier tokens. It needs four matrices: `Wq`, `Wk`, `Wv`, `Wo`.
- **MLP** (feed-forward network) processes each token on its own. It widens the vector to 12,288 numbers and narrows it again. Qwen uses the SwiGLU design with three matrices: `Wgate`, `Wup`, `Wdown`.
- **RMSNorm** layers before each block keep the numbers in a stable range. Their cost is negligible.

### Counting the weights

The "8B" in the name is just the sum of all matrix entries. Using rows × columns from the config:

| Matrix | Shape | Weights |
|---|---|---|
| `Wq` | 4,096 × 4,096 (32 heads × 128) | 16,777,216 |
| `Wk` | 4,096 × 1,024 (8 heads × 128) | 4,194,304 |
| `Wv` | 4,096 × 1,024 | 4,194,304 |
| `Wo` | 4,096 × 4,096 | 16,777,216 |
| `Wgate` | 4,096 × 12,288 | 50,331,648 |
| `Wup` | 4,096 × 12,288 | 50,331,648 |
| `Wdown` | 12,288 × 4,096 | 50,331,648 |
| **One layer** | | **192,937,984** |
| 36 layers | | 6,945,767,424 |
| Embedding table | 151,936 × 4,096 | 622,329,856 |
| LM head (not tied) | 4,096 × 151,936 | 622,329,856 |
| **Total** | | **≈ 8.19 billion** |

The few thousand norm weights per layer are negligible. In FP16 that is 16.4 GB, in INT4 roughly 4 to 5 GB (depending on what the quantizer leaves in FP16).

### FLOPs per token

The golden rule says 2 FLOPs per weight that takes part in a multiplication. The embedding table is only a lookup, so it does not count; the LM head does:

```
weight FLOPs per token = 2 × (6,945,767,424 + 622,329,856) = 15.14 GFLOPs
```

Attention adds math that depends on the context length `L`. For each of the 32 query heads in each of the 36 layers the new token does a dot product with `L` cached keys (256 FLOPs each: 128 multiplies + 128 adds) and then mixes `L` cached values (another 256 per position):

```
attention FLOPs per token = 36 layers × 32 heads × 512 × L
                          = 589,824 × L
```

At `L` = 8,000 that is 4.72 GFLOPs, so one token costs about **19.9 GFLOPs** in total.

<a id="journey"></a>

## 4. One token's journey: an animated simulation

Below is a **miniature Qwen3**. It follows the same recipe as the real model, but with 8 numbers instead of 4,096 (4 query heads, 2 key/value heads, 16 numbers in the MLP). The weights are random, so the word it predicts is meaningless, **but every number on screen is really computed**: the dot products, the RMSNorm, the rotation, the softmax.

How to use it:

- Press **Play** to watch all 16 steps in a row, or click a **step number** to jump to it.
- Drag the **slider** to scrub through the animation of one step, forwards and backwards.
- Under the animation you get a plain-words explanation, the formula and the **real Qwen3-8B** sizes for that step (weights, FLOPs, KV-cache reads).
- **Head** changes which attention head you look at in steps 7 to 9. **Context** changes how long the real context is for the real-size numbers.
- Colors: every box is one number. **Orange is positive, blue is negative**, a stronger color means a larger magnitude.

<div class="lq" id="lqB">
<div class="lq-row" style="gap:8px">
<button type="button" id="lqB-prev" aria-label="Previous step">◀</button>
<button type="button" id="lqB-play" style="min-width:96px">▶ Play</button>
<button type="button" id="lqB-next" aria-label="Next step">▶</button>
<label>Speed <select id="lqB-spd"><option value="0.5">0.5×</option><option value="1" selected>1×</option><option value="2">2×</option></select></label>
<label>Head <select id="lqB-head"><option value="0">0</option><option value="1">1</option><option value="2">2</option><option value="3">3</option></select></label>
<label>Context L <select id="lqB-L"><option value="1000">1,000</option><option value="4000">4,000</option><option value="8000" selected>8,000</option><option value="16000">16,000</option><option value="40000">40,000</option></select></label>
</div>
<div id="lqB-chips" class="lq-row" style="gap:4px"></div>
<input type="range" id="lqB-t" min="0" max="1000" value="0" style="width:100%" aria-label="Progress inside this step">
<div id="lqB-title" style="margin-top:8px"></div>
<div id="lqB-stage" class="lq-card" style="min-height:300px"></div>
<div id="lqB-desc" class="lq-grid sm-desc"></div>
<div id="lqB-real" class="lq-card" style="margin-top:12px"></div>
<div class="lq-sm lq-mut" style="margin:16px 0 4px">Share of the real FLOPs for one decoded token (click a segment to jump to that step)</div>
<div id="lqB-bar" class="lq-bar"></div>
<div class="lq-leg"><span><span class="lq-sw" style="background:#1baf7a"></span>Attention</span><span><span class="lq-sw" style="background:#2a78d6"></span>MLP</span><span><span class="lq-sw" style="background:#8a8a86"></span>Output head</span><span id="lqB-tot" style="margin-left:auto"></span></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function f2(v){var s=v.toFixed(2);return s==="-0.00"?"0.00":s}
function nf(x){return Math.round(x).toLocaleString("en-US")}
function F(x){if(x>=1e9)return (x/1e9).toFixed(2)+" G";if(x>=1e6)return (x/1e6).toFixed(1)+" M";if(x>=1e3)return (x/1e3).toFixed(1)+" K";return Math.round(x)+""}
function B(x){if(x>=1e9)return (x/1e9).toFixed(2)+" GB";if(x>=1e6)return (x/1e6).toFixed(1)+" MB";if(x>=1e3)return (x/1e3).toFixed(1)+" KB";return Math.round(x)+" B"}
var seed=11;
function rnd(){seed|=0;seed=seed+0x6D2B79F5|0;var t=Math.imul(seed^seed>>>15,1|seed);t=t+Math.imul(t^t>>>7,61|t)^t;return((t^t>>>14)>>>0)/4294967296}
function gauss(){return (rnd()+rnd()+rnd()+rnd()-2)*0.95}
function vec(n,s){var a=[];for(var i=0;i<n;i++)a.push(gauss()*s);return a}
function mat(r,c,s){var m=[];for(var i=0;i<r;i++)m.push(vec(c,s));return m}
function gain(n){var a=[];for(var i=0;i<n;i++)a.push(0.8+rnd()*0.4);return a}
function mv(W,x){return W.map(function(r){var s=0;for(var j=0;j<x.length;j++)s+=r[j]*x[j];return s})}
function dot(a,b){var s=0;for(var i=0;i<a.length;i++)s+=a[i]*b[i];return s}
function rmsOf(v){var s=0;v.forEach(function(a){s+=a*a});return Math.sqrt(s/v.length+1e-6)}
function softmax(v){var m=Math.max.apply(null,v),e=v.map(function(a){return Math.exp(a-m)}),s=0;e.forEach(function(a){s+=a});return e.map(function(a){return a/s})}
function silu(a){return a/(1+Math.exp(-a))}
function rot(a,b,p){var c=Math.cos(p),s=Math.sin(p);return [a*c-b*s,a*s+b*c]}
var vocab=["The","invoice","total","is","120","EUR","due","paid","on","."],ids=[0,1,2,3],POS=3;
var E=mat(10,8,0.9),x=E[3].slice(),g1=gain(8),g2=gain(8),g3=gain(8);
var Wq=mat(8,8,0.6),Wk=mat(4,8,0.6),Wv=mat(4,8,0.6),Wo=mat(8,8,0.6),Wg=mat(16,8,0.7),Wu=mat(16,8,0.7),Wd=mat(8,16,0.5),Wl=mat(10,8,0.9);
var ck=[vec(4,0.9),vec(4,0.9),vec(4,0.9)],cv=[vec(4,0.9),vec(4,0.9),vec(4,0.9)];
var rms1=rmsOf(x),xn=x.map(function(a){return a/rms1}),n1=xn.map(function(a,i){return a*g1[i]}),ms1=0;
x.forEach(function(a){ms1+=a*a/8});
var q=mv(Wq,n1),k=mv(Wk,n1),v=mv(Wv,n1),qr=[],kr=[],h,j;
for(h=0;h<4;h++)qr.push(rot(q[2*h],q[2*h+1],POS));
for(h=0;h<2;h++)kr.push(rot(k[2*h],k[2*h+1],POS));
var ckr=ck.map(function(row,p){var a=[];for(var hh=0;hh<2;hh++)a.push(rot(row[2*hh],row[2*hh+1],p));return a});
function keyOf(hk,jj){return jj<3?ckr[jj][hk]:kr[hk]}
function valOf(hk,jj){return jj<3?[cv[jj][2*hk],cv[jj][2*hk+1]]:[v[2*hk],v[2*hk+1]]}
var sc=[],ex=[],al=[],oh=[];
for(h=0;h<4;h++){var s=[],o2=[0,0];for(j=0;j<4;j++)s.push(dot(qr[h],keyOf(h>>1,j))/Math.sqrt(2));sc.push(s);ex.push(s.map(function(a){return Math.exp(a)}));al.push(softmax(s));for(j=0;j<4;j++){var vv=valOf(h>>1,j);o2[0]+=al[h][j]*vv[0];o2[1]+=al[h][j]*vv[1]}oh.push(o2)}
var o=[].concat.apply([],oh),att=mv(Wo,o),x1=x.map(function(a,i){return a+att[i]});
var r2=rmsOf(x1),n2=x1.map(function(a,i){return a/r2*g2[i]});
var gate=mv(Wg,n2),up=mv(Wu,n2),sg=gate.map(silu),hh=sg.map(function(a,i){return a*up[i]});
var dn=mv(Wd,hh),x2=x1.map(function(a,i){return a+dn[i]});
var r3=rmsOf(x2),n3=x2.map(function(a,i){return a/r3*g3[i]}),logits=mv(Wl,n3),probs=softmax(logits),pick=0;
probs.forEach(function(a,i){if(a>probs[pick])pick=i});
function sh(u,n){return Math.min(n,Math.floor(u*n+1e-6))}
function ph(t,a,b){return Math.max(0,Math.min(1,(t-a)/(b-a)))}
function col(v,m){var a=Math.min(1,Math.abs(v)/(m||1))*0.6+0.06;return v>=0?"rgba(235,104,52,"+a.toFixed(2)+")":"rgba(42,120,214,"+a.toFixed(2)+")"}
function cells(vals,show,o){o=o||{};var m=o.m||2,w=o.w||44,ht=o.h||28,gs=o.g||0,s="";
for(var i=0;i<vals.length;i++){var on=i<show,hl=o.hl&&o.hl.indexOf(i)>=0;
s+='<span class="sm-c" style="width:'+w+'px;height:'+ht+'px;background:'+(on?col(vals[i],m):"transparent")+';opacity:'+(on?1:0.3)+(hl?";box-shadow:inset 0 0 0 2px currentColor":"")+((gs&&(i+1)%gs===0&&i<vals.length-1)?";margin-right:8px":"")+'">'+(on?f2(vals[i]):"·")+'</span>'}
return s}
function strip(label,vals,show,o){return '<div class="sm-row"><span class="sm-lab">'+label+'</span><div class="sm-strip">'+cells(vals,show,o)+'</div></div>'}
function sbar(v,mx,r){var w=Math.min(50,Math.abs(v)/(mx||1)*50*r);return '<div class="sm-bar"><i style="left:50%;width:1px;background:currentColor;opacity:.35"></i>'+(v>=0?'<i style="left:50%;width:'+w+'%;background:rgba(235,104,52,.75)"></i>':'<i style="right:50%;width:'+w+'%;background:rgba(42,120,214,.75)"></i>')+'</div>'}
function pbar(p,r,hl){return '<div class="sm-bar"><i style="left:0;width:'+(p*100*r)+'%;background:'+(hl?"rgba(235,104,52,.85)":"rgba(27,175,122,.75)")+'"></i></div>'}
function row(label,mid,val){return '<div class="sm-row"><span class="sm-lab">'+label+'</span>'+mid+'<span class="sm-val">'+val+'</span></div>'}
function mc(l,v){return '<div class="lq-mc"><small>'+l+'</small><b>'+v+'</b></div>'}
var head=0;
var R=[
function(t){var n=sh(t,4),s='<div class="sm-sent">“The invoice total is”</div><div class="sm-note">↓ tokenizer: cut the text into pieces and look each piece up in the dictionary</div><div class="sm-tok">';
for(var i=0;i<4;i++)s+='<span class="sm-t" style="opacity:'+(i<n?1:0.2)+'"><b>'+vocab[ids[i]]+'</b><small>ID '+ids[i]+'</small></span>';
return s+'</div><div class="sm-note">Toy dictionary: 10 words, IDs 0 to 9. Qwen3-8B: 151,936 pieces, IDs 0 to 151,935. The model will now predict token number 4.</div>'},
function(t){var p1=ph(t,0,0.5),p2=ph(t,0.5,1),sel=t<0.5?Math.min(3,Math.floor(p1*4)):3,s='<div class="sm-note" style="margin:0 0 6px">Embedding table: one row per dictionary entry, 8 numbers per row (real: 4,096)</div>';
for(var r=0;r<10;r++)s+='<div class="sm-row'+(r===sel?" sm-hl":"")+'" style="margin:1px 0"><span class="sm-lab" style="min-width:90px">'+r+'  '+vocab[r]+'</span><div class="sm-strip">'+cells(E[r],8,{w:40,h:20,m:2})+'</div></div>';
return s+'<div class="sm-note" style="margin-top:10px">↓ copy row 3 ('+vocab[3]+') out of the table. No calculation, just a copy.</div>'+strip("x = row 3",x,sh(p2,8))},
function(t){var p1=ph(t,0,1/3),p2=ph(t,1/3,2/3),p3=ph(t,2/3,1),sq=x.map(function(a){return a*a});
return strip("x (input)",x,8)+strip("x² (squared)",sq,sh(p1,8),{m:4})+'<div class="sm-note">'+(p1>=1?"mean of the squares = "+f2(ms1)+",  rms = √(mean + ε) = <b>"+f2(rms1)+"</b>":"first: square every number…")+'</div>'+strip("x ÷ rms",xn,sh(p2,8))+strip("scale g (learned)",g1,p3>0?8:0,{m:1.5})+strip("x̂ = (x ÷ rms) × g",n1,sh(p3,8))+'<div class="sm-note">After the division the typical size of the numbers is 1, however big or small x was.</div>'},
function(t){var u=t*16,idx=Math.min(15,Math.floor(u)),fr=u-idx,done=idx+(fr>0.5?1:0),W,r,nm,out;
if(idx<8){W=Wq;r=idx;nm="Wq";out=q}else if(idx<12){W=Wk;r=idx-8;nm="Wk";out=k}else{W=Wv;r=idx-12;nm="Wv";out=v}
var terms=[];for(var jj=0;jj<8;jj++)terms.push(f2(W[r][jj])+"×"+f2(n1[jj]));
var s=strip("x̂ (input)",n1,8)+'<div class="sm-note" style="margin-top:8px"><b>'+nm+'</b>, row '+r+': multiply the row with x̂ number by number, then add everything up</div><div class="sm-grid">';
for(var i=0;i<W.length;i++)s+='<div class="sm-strip" style="'+(i===r?"background:rgba(42,120,214,.16);border-radius:6px":"")+'">'+cells(W[i],8,{w:34,h:16,m:1.5})+'</div>';
return s+'</div><div class="sm-note">'+terms.join(" + ")+" = <b>"+f2(out[r])+"</b></div>"+strip("q (4 heads × 2)",q,Math.min(8,done),{g:2})+strip("k (2 heads × 2)",k,Math.max(0,Math.min(4,done-8)),{g:2})+strip("v (2 heads × 2)",v,Math.max(0,Math.min(4,done-12)),{g:2})},
function(t){var P=[],i;for(i=0;i<4;i++)P.push({n:"q head "+i,a:[q[2*i],q[2*i+1]],b:qr[i]});for(i=0;i<2;i++)P.push({n:"k head "+i,a:[k[2*i],k[2*i+1]],b:kr[i]});
var mx=0;P.forEach(function(p){mx=Math.max(mx,Math.hypot(p.a[0],p.a[1]))});
var s='<svg viewBox="0 0 680 190" width="100%" role="img" aria-label="Arrows rotating by their position angle">';
P.forEach(function(p,i){var cx=60+i*112,cy=70,Rr=42,r=Math.hypot(p.a[0],p.a[1])/mx*Rr*0.95,a0=Math.atan2(p.a[1],p.a[0]),a1=a0+POS*t;
s+='<circle cx="'+cx+'" cy="'+cy+'" r="'+Rr+'" fill="none" stroke="currentColor" stroke-opacity=".35"/><line x1="'+(cx-Rr)+'" y1="'+cy+'" x2="'+(cx+Rr)+'" y2="'+cy+'" stroke="currentColor" stroke-opacity=".15"/><line x1="'+cx+'" y1="'+(cy-Rr)+'" x2="'+cx+'" y2="'+(cy+Rr)+'" stroke="currentColor" stroke-opacity=".15"/>';
s+='<line x1="'+cx+'" y1="'+cy+'" x2="'+(cx+r*Math.cos(a0))+'" y2="'+(cy-r*Math.sin(a0))+'" stroke="currentColor" stroke-opacity=".4" stroke-dasharray="3 3"/>';
s+='<line x1="'+cx+'" y1="'+cy+'" x2="'+(cx+r*Math.cos(a1))+'" y2="'+(cy-r*Math.sin(a1))+'" stroke="'+(i<4?"#1baf7a":"#8a7bea")+'" stroke-width="3" stroke-linecap="round"/>';
s+='<text x="'+cx+'" y="132" text-anchor="middle" font-size="12" fill="currentColor">'+p.n+'</text><text x="'+cx+'" y="150" text-anchor="middle" font-size="11" fill="currentColor" opacity=".75">('+f2(p.a[0])+', '+f2(p.a[1])+')</text><text x="'+cx+'" y="166" text-anchor="middle" font-size="11" fill="currentColor">'+(t>=0.999?"→ ("+f2(p.b[0])+", "+f2(p.b[1])+")":"rotating…")+'</text>'});
return s+'</svg><div class="sm-note">Dashed arrow: before. Solid arrow: after turning by φ = position × frequency = '+f2(POS*t)+' of '+f2(POS)+' radians ('+Math.round(POS*t*180/Math.PI)+'° of '+Math.round(POS*180/Math.PI)+'°). The length of an arrow never changes, only its direction.</div>'},
function(t){var s='<div class="sm-note" style="margin:0 0 6px">The notebook: one row per token, keys are stored after RoPE.</div>',p;
for(p=0;p<4;p++){var K=p<3?[ckr[p][0][0],ckr[p][0][1],ckr[p][1][0],ckr[p][1][1]]:[kr[0][0],kr[0][1],kr[1][0],kr[1][1]],V=p<3?cv[p]:v,vis=p<3?1:ph(t,0.1,0.6);
s+='<div class="sm-row'+(p===3?" sm-hl":"")+'" style="opacity:'+(0.25+0.75*vis)+';margin:3px 0"><span class="sm-lab" style="min-width:110px">pos '+p+' · '+vocab[ids[p]]+'</span><span class="sm-lab" style="min-width:16px">K</span><div class="sm-strip">'+cells(K,4,{w:40,g:2})+'</div><span class="sm-lab" style="min-width:16px">V</span><div class="sm-strip">'+cells(V,4,{w:40,g:2})+'</div></div>'}
return s+'<div class="sm-note">Rows 0 to 2 were written when the earlier tokens were processed. Row 3 is written now. Real size: 4,096 bytes per layer and token.</div>'},
function(t){var h0=head,kh=h0>>1,s=sc[h0],mx=0;s.forEach(function(a){mx=Math.max(mx,Math.abs(a))});
var jj=Math.min(3,Math.floor(t*4)),kj=keyOf(kh,jj),out='<div class="sm-note" style="margin:0 0 6px">Head '+h0+' (reads key head '+kh+'). Query pair after RoPE: ('+f2(qr[h0][0])+', '+f2(qr[h0][1])+')</div>';
for(var i=0;i<4;i++){var r=ph(t,i/4,(i+1)/4);out+=row("q · k"+i+"  ("+vocab[ids[i]]+")",sbar(s[i],mx,r),r>0?f2(s[i]):"·")}
return out+'<div class="sm-note">Token '+jj+': (q · k) ÷ √2 = ('+f2(qr[h0][0])+'×'+f2(kj[0])+' + '+f2(qr[h0][1])+'×'+f2(kj[1])+') ÷ 1.41 = <b>'+f2(s[jj])+'</b>. Orange: match, blue: mismatch.</div>'},
function(t){var s=sc[head],e=ex[head],a=al[head],sum=0;e.forEach(function(z){sum+=z});var p1=ph(t,0,0.5),p2=ph(t,0.5,1);
var out='<div class="sm-row sm-lab" style="opacity:.7"><span class="sm-lab">token</span><span class="sm-val" style="text-align:left;min-width:80px">score s</span><span class="sm-val" style="text-align:left;min-width:90px">e^s</span><span style="flex:1">share = e^s ÷ Σ</span></div>';
for(var i=0;i<4;i++){var r1=ph(p1,i/4,(i+1)/4),r2=ph(p2,i/4,(i+1)/4);
out+='<div class="sm-row"><span class="sm-lab">'+vocab[ids[i]]+'</span><span class="sm-val" style="text-align:left;min-width:80px">'+f2(s[i])+'</span><span class="sm-val" style="text-align:left;min-width:90px">'+(r1>0?f2(e[i]):"·")+'</span>'+pbar(a[i],r2,false)+'<span class="sm-val">'+(r2>0?(a[i]*100).toFixed(0)+"%":"·")+'</span></div>'}
return out+'<div class="sm-note">'+(p1>=1?"Σ e^s = "+f2(sum)+". Dividing each e^s by this total makes the shares add up to 100%.":"First: e^s for every score. It is always positive and it amplifies the winners.")+'</div>'},
function(t){var h0=head,kh=h0>>1,a=al[h0],p1=ph(t,0,0.7),p2=ph(t,0.7,0.85),p3=ph(t,0.85,1),n=sh(p1,4),acc=[0,0],out='<div class="sm-note" style="margin:0 0 6px">Head '+h0+': take α × v from every token and add it all up</div>';
for(var i=0;i<4;i++){var vv=valOf(kh,i),on=i<n;if(on){acc[0]+=a[i]*vv[0];acc[1]+=a[i]*vv[1]}
out+=row(vocab[ids[i]],'<span style="flex:1;font-size:12px;opacity:'+(on?1:0.25)+'">'+(a[i]*100).toFixed(0)+"% × ["+f2(vv[0])+", "+f2(vv[1])+"] = ["+f2(a[i]*vv[0])+", "+f2(a[i]*vv[1])+"]</span>","")}
out+='<div class="sm-note">'+(p2>0?"Sum = o"+h0+" = <b>["+f2(oh[h0][0])+", "+f2(oh[h0][1])+"]</b>":"running sum: ["+f2(acc[0])+", "+f2(acc[1])+"]")+'</div>';
return out+strip("o = all 4 heads joined",o,sh(p3,8),{g:2,hl:[2*h0,2*h0+1]})},
function(t){var p1=ph(t,0,0.5),p2=ph(t,0.5,1);
return strip("o (from step 9)",o,8,{g:2})+strip("Wo · o",att,sh(p1,8))+'<div class="sm-note">+ add the vector that entered this block (residual connection)</div>'+strip("x (block input)",x,8)+strip("x₁ = x + Wo · o",x1,sh(p2,8))},
function(t){var p1=ph(t,0,0.34),p2=ph(t,0.34,0.67),p3=ph(t,0.67,1);
return strip("x₁",x1,8)+strip("RMSNorm(x₁)",n2,sh(p1,8))+'<div class="sm-note">two parallel expansions: 8 → 16 numbers (real: 4,096 → 12,288)</div>'+strip("gate = Wgate · n",gate,sh(p2,16),{w:36,m:3})+strip("up = Wup · n",up,sh(p3,16),{w:36,m:3})},
function(t){var p1=ph(t,0,0.5),p2=ph(t,0.5,1),n=sh(p1,16),i;
function X(a){return 20+(a+4)/8*300}
function Y(b){return 150-(b+1)/5*140}
var pts=[];for(i=0;i<=40;i++){var a=-4+i*0.2;pts.push(X(a).toFixed(1)+","+Y(silu(a)).toFixed(1))}
var s='<svg viewBox="0 0 340 170" width="340" style="max-width:100%" role="img" aria-label="SiLU curve with the gate values as dots"><line x1="20" y1="'+Y(0)+'" x2="320" y2="'+Y(0)+'" stroke="currentColor" stroke-opacity=".3"/><line x1="'+X(0)+'" y1="10" x2="'+X(0)+'" y2="150" stroke="currentColor" stroke-opacity=".3"/><polyline points="'+pts.join(" ")+'" fill="none" stroke="currentColor" stroke-width="2"/>';
for(i=0;i<n;i++){var gx=Math.max(-4,Math.min(4,gate[i]));s+='<circle cx="'+X(gx)+'" cy="'+Y(silu(gx))+'" r="4" fill="#eb6834"/>'}
s+='<text x="320" y="'+(Y(0)-6)+'" text-anchor="end" font-size="11" fill="currentColor">gate value →</text><text x="28" y="22" font-size="11" fill="currentColor">SiLU(gate)</text></svg>';
return s+'<div class="sm-note">Each orange dot is one of the 16 gate values sitting on the curve. Far left of 0 the door is almost closed, right of 0 it opens.</div>'+strip("gate",gate,16,{w:36,m:3})+strip("SiLU(gate)",sg,n,{w:36,m:3})+strip("up",up,16,{w:36,m:3})+strip("h = SiLU(gate) × up",hh,sh(p2,16),{w:36,m:3})},
function(t){var p1=ph(t,0,0.5),p2=ph(t,0.5,1);
return strip("h (16 numbers)",hh,16,{w:36,m:3})+strip("Wdown · h",dn,sh(p1,8))+'<div class="sm-note">+ add it to the stream again (residual connection)</div>'+strip("x₁",x1,8)+strip("x₂ = x₁ + Wdown · h",x2,sh(p2,8))},
function(t){var n=sh(t,36),s='<div class="sm-note" style="margin:0 0 6px">Layer 1 is what you just watched. The same recipe now repeats 35 more times, each time with different weights.</div><div style="display:grid;grid-template-columns:repeat(12,1fr);gap:4px;max-width:520px">';
for(var i=0;i<36;i++)s+='<div style="height:26px;border-radius:4px;border:1px solid var(--lq-line);font-size:11px;display:flex;align-items:center;justify-content:center;background:'+(i<n?(i===0?"rgba(235,104,52,.55)":"rgba(27,175,122,.45)"):"transparent")+'">'+(i+1)+'</div>';
return s+'</div><div class="sm-note">Layers finished: '+n+' of 36. KV cache written for this token so far: '+nf(n*4096)+' bytes (real model).</div>'+strip("vector after layer 1 (toy)",x2,8)},
function(t){var p1=ph(t,0,0.4),p2=ph(t,0.4,1),mx=0;logits.forEach(function(a){mx=Math.max(mx,Math.abs(a))});
var s=strip("x after the last layer",x2,8)+strip("RMSNorm(x)",n3,sh(p1,8))+'<div class="sm-note" style="margin-top:8px">One score (logit) for every word of the dictionary:</div>';
for(var i=0;i<10;i++){var r=ph(p2,i/10,(i+1)/10);s+=row(vocab[i],sbar(logits[i],mx,r),r>0?f2(logits[i]):"·")}
return s},
function(t){var p1=ph(t,0,0.5),p2=ph(t,0.5,0.75),p3=ph(t,0.75,1),s='';
for(var i=0;i<10;i++){var r=ph(p1,i/10,(i+1)/10);s+=row(vocab[i],pbar(probs[i],r,p2>0&&i===pick),r>0?(probs[i]*100).toFixed(1)+"%":"·")}
s+='<div class="sm-note">'+(p2>0?"Greedy choice: the highest probability wins → <b>"+vocab[pick]+"</b>":"softmax turns the 10 scores into probabilities that add up to 100%")+'</div>';
if(p3>0)s+='<div class="sm-sent" style="opacity:'+p3+'">“The invoice total is <b>'+vocab[pick]+'</b>”</div><div class="sm-note">The new token goes back to step 2. Only this single token is processed again; everything older comes from the KV cache.</div>';
return s}
];
var S=[
{t:"Text becomes token IDs",p:"Input",tag:"once",a:"“The invoice total is”",b:"Tokenizer (dictionary lookup)",c:"T integer IDs",wc:0,fl:function(){return 0},g:"o",
plain:"Computers cannot read letters, only numbers. A tokenizer cuts the text into pieces from a fixed dictionary and swaps every piece for its number in that dictionary (its ID). Think of a library where every word has a shelf number: the model never sees the word, only the shelf number.",
what:["Common words are usually one token. Rare words are split into several pieces, so “invoice” may be one piece and an unusual name three.","Spaces, punctuation and even single bytes have tokens, so any text can be represented.","The pieces are chosen by the Byte Pair Encoding algorithm (see my post #126 on BPE).","Qwen3-8B's dictionary has <b>151,936</b> entries (<code>vocab_size</code>)."],
fo:"text → [piece₁, piece₂, …] → [ID₁, ID₂, …]   (each ID from 0 to 151,935)"},
{t:"Embedding lookup",p:"Input",tag:"once",a:"T IDs",b:"Copy rows out of the table [151,936 × 4,096]",c:"x: [T × 4,096]",wc:622329856,fl:function(){return 0},g:"o",nomath:1,
plain:"An ID is only a label, like a locker number. The embedding table is a huge wall of lockers, and locker 3 holds a list of numbers that describes that token. The lists were learned during training so that words used in similar ways (“invoice” and “bill”) get similar lists. A lookup just copies that list out: no calculation at all.",
what:["The table has one row per dictionary entry and one column per hidden dimension: <b>151,936 × 4,096</b> in Qwen3-8B.","The chosen row (4,096 numbers) is called the <b>embedding</b> or <b>hidden state</b>. This vector represents the token in the rest of the network.","It is the start of the <b>residual stream</b>: every block that follows adds a small correction to this same vector.","The table does not know where the token sits in the text. Position is added later by RoPE (step 5).","Cost: zero FLOPs. Only one 8 KB row is read from memory per token."],
fo:"x = EmbeddingTable[ID]     (a row copy, shape [4,096])"},
{t:"RMSNorm: rescale the vector",p:"Attention",tag:"× 36 layers",a:"x: [4,096]",b:"÷ rms, × learned scale g [4,096]",c:"x̂: [4,096]",wc:4096,fl:function(){return 0},g:"att",tiny:1,
plain:"The numbers in the vector can be tiny or huge. Before every block the model rescales the vector to a standard size, like levelling the volume of all songs before a playlist. RMS stands for <b>root mean square</b>, a typical size of the numbers.",
what:["Square every number, take the average, take the square root: that is the <b>rms</b>, the typical size of the numbers.","Divide every number by the rms. The typical size is now 1, whatever it was before.","Multiply each number by its own learned <b>scale</b> g, so the model can turn important features up and others down.","The tiny ε (<code>rms_norm_eps</code> = 1e-6) only prevents dividing by zero.","Without this the numbers would drift and could explode across 36 layers, which makes training unstable. RMSNorm is a cheaper cousin of LayerNorm because it does not subtract the mean.","Cost: about 4 × 4,096 operations, negligible."],
fo:"x̂ᵢ = xᵢ ÷ √( mean(x²) + ε ) × gᵢ"},
{t:"Q, K, V projections: three notes per token",p:"Attention",tag:"× 36 layers",a:"x̂: [4,096]",b:"× Wq [4,096×4,096], Wk and Wv [4,096×1,024]",c:"q: [32×128], k: [8×128], v: [8×128]",wc:25165824,fl:function(){return 2*25165824*36},g:"att",
plain:"Every token writes three notes. The <b>query</b> says what I am looking for. The <b>key</b> is a label that says what I contain. The <b>value</b> is the information I hand over if somebody picks me. Each note is made by a matrix-vector product: every output number is a weighted sum of all the input numbers, using learned weights.",
what:["Matrix × vector: output i = (row i of the matrix) · (input vector) = w₁x₁ + w₂x₂ + …. The animation shows one row at a time.","The notes are split into <b>heads</b>: small independent groups of numbers (128 each in the real model) that can specialise in different kinds of relationships.","Qwen3-8B has 32 query heads but only 8 key/value heads. This is <b>grouped-query attention</b> (GQA): four query heads share one key/value head, which makes the KV cache 4× smaller. The toy has 4 and 2.","This is the biggest memory cost of attention: 25 M weights per layer that must be streamed from VRAM for every token."],
fo:"q = Wq · x̂      k = Wk · x̂      v = Wv · x̂"},
{t:"RoPE: stamp the position into q and k",p:"Attention",tag:"× 36 layers",a:"q, k",b:"Q/K norm, then rotate number pairs (rope_theta 1,000,000)",c:"q, k: same shapes",wc:256,fl:function(){return 0},g:"att",tiny:1,
plain:"Attention alone has no sense of order: “dog bites man” and “man bites dog” would look identical. RoPE (rotary position embedding) stamps the position into the query and key by turning them. Picture each pair of numbers as an arrow on a clock face: token 0 is not turned, token 1 a little, token 2 a bit more, and so on.",
what:["Every pair of numbers (a, b) is treated as a point in 2D and rotated by the angle <b>position × frequency</b>.","The key property: the match score between a rotated query and a rotated key depends only on how far apart the two tokens are, not on their absolute positions.","Every pair uses its own frequency, from fast to slow (set by <code>rope_theta</code> = 1,000,000), so the model can tell near from far.","Only q and k are rotated, v is not. There are no weights here and nothing is learned.","Qwen3 also normalises every q and k head with a small RMSNorm just before this step (part of the architecture, not a config key). The toy skips it.","In the toy each head has one pair and a frequency of 1 radian per position. Real heads have 64 pairs."],
fo:"(a′, b′) = (a·cosφ − b·sinφ,  a·sinφ + b·cosφ),   φ = position × θ^(−2i/128)"},
{t:"Append to the KV cache",p:"Attention",tag:"× 36 layers",a:"k: [8×128], v: [8×128]",b:"Write as a new row at position L",c:"cache K, V: [L+1 × 8 × 128] each",wc:0,fl:function(){return 0},g:"att",kvw:147456,
plain:"To decide what to look at, the new token needs the keys and values of all earlier tokens. They never change, so the model keeps them in a notebook, the <b>KV cache</b>, and writes just one new line per token instead of recomputing everything.",
what:["Rows 0 to 2 were written when the earlier tokens were processed. Only row 3, the current token, is new.","Every layer has its own cache. In the real model one token adds 2 × 8 heads × 128 numbers × 2 bytes = 4,096 bytes per layer, 147,456 bytes over 36 layers.","The cache lives in GPU memory and is read again for <i>every</i> new token. It is private to each user and grows with the context, which is why it competes with the weights for VRAM and bandwidth.","Without a cache every generated token would cost <i>L</i> times more work."],
fo:"cache_K ← append(k)      cache_V ← append(v)      (per layer)"},
{t:"Attention scores: query meets keys",p:"Attention",tag:"× 36 layers",a:"q: [32×128], K cache: [L×8×128]",b:"Every query head · every cached key, ÷ √128",c:"scores: [32 × L]",wc:0,fl:function(L){return 8192*L*36},kv:function(L){return 2048*L*36},g:"att",
plain:"Now the query compares itself with every key in the cache. The comparison is a dot product: multiply matching numbers and add them up. A big positive result means “these two match”, a negative one means “opposite”. Every head does this on its own, so different heads can look for different things (use the Head selector to compare).",
what:["One score per earlier token, plus the token itself: <b>L</b> scores per head. The model can only look backwards, never at tokens that do not exist yet (<b>causal masking</b>).","The score is divided by √(head size) so the numbers do not grow with the head size.","Real model: 32 heads × L scores per layer, 256 × L FLOPs per head.","Four query heads read the same key head: the cache bytes are counted for 8 heads, the FLOPs for 32."],
fo:"scoreⱼ = (q · kⱼ) ÷ √head_dim"},
{t:"Softmax: scores become percentages",p:"Attention",tag:"× 36 layers",a:"scores: [32 × L]",b:"Softmax over the L scores of each head",c:"weights α: [32 × L]",wc:0,fl:function(){return 0},g:"att",tiny:1,
plain:"Raw scores can have any size and sign. Softmax turns them into percentages that add up to 100%. First raise e to the power of every score (this makes everything positive and lets the winners stand out), then divide by the total.",
what:["Large scores get a large share, small or negative scores get almost nothing.","The shares are the <b>attention weights</b>: how much of each earlier token's information the current token takes in.","The result is a probability distribution over the L earlier tokens.","Cost: a few operations per score, negligible next to the matrices."],
fo:"αⱼ = e^(scoreⱼ) ÷ Σᵢ e^(scoreᵢ)"},
{t:"Mix the values",p:"Attention",tag:"× 36 layers",a:"weights α: [32 × L], V cache: [L×8×128]",b:"Weighted sum of the value vectors",c:"o: [32×128] = [4,096]",wc:0,fl:function(L){return 8192*L*36},kv:function(L){return 2048*L*36},g:"att",
plain:"Each earlier token hands over its value note, but only as much as its attention share: 70% of this one, 20% of that one, and so on. Adding everything up gives one blended note per head, the information the current token has gathered from its context.",
what:["o = Σ αⱼ · vⱼ. The values are read from the cache (the second cache read, after the keys).","The results of all heads are joined side by side. Toy: 4 × 2 = 8 numbers. Real: 32 × 128 = 4,096.","Real cost: 256 × L FLOPs per head, plus reading L × 8 × 128 values from the cache."],
fo:"oₕ = Σⱼ αⱼ · vⱼ,     o = [o₀ | o₁ | … | o₃₁]"},
{t:"Output projection and residual",p:"Attention",tag:"× 36 layers",a:"o: [4,096]",b:"× Wo [4,096×4,096], then add the block input",c:"x₁: [4,096]",wc:16777216,fl:function(){return 2*16777216*36},g:"att",
plain:"The matrix Wo mixes the findings of all heads into one new vector. Then comes the <b>residual connection</b>: the result is added onto the vector that entered the block. The block never replaces the vector, it only adds a correction, like editing a document with tracked changes instead of rewriting it.",
what:["Wo is a 4,096 × 4,096 matrix: 16.8 M weights per layer.","Residual connection: new = old + correction. The original information can never get lost, and each layer only has to learn small improvements.","This is what makes a 36-layer stack trainable, and why the vector is called the <b>residual stream</b>."],
fo:"x₁ = x + Wo · o"},
{t:"MLP: normalise, then gate and up",p:"MLP",tag:"× 36 layers",a:"x₁: [4,096]",b:"RMSNorm, then × Wgate and × Wup, each [4,096×12,288]",c:"gate, up: [12,288] each",wc:100663296,fl:function(){return 2*100663296*36},g:"mlp",
plain:"Attention moved information between tokens. The <b>MLP</b> (multi-layer perceptron, also called the feed-forward network) now thinks about every token on its own. It works like a huge learned memory of facts and patterns. It starts with another normalisation and then widens the vector in two parallel ways, called <b>gate</b> and <b>up</b>.",
what:["The vector grows from 4,096 to 12,288 numbers (3× wider). More room means more features can be kept apart.","The MLP holds most of the weights: 151 M of the 193 M weights in one layer.","gate and up are two different matrix products of the same input. Their roles are explained in the next step.","Because it is the largest block it also has the largest share of the FLOPs (see the bar below)."],
fo:"gate = Wgate · RMSNorm(x₁)      up = Wup · RMSNorm(x₁)"},
{t:"SiLU gating: the dimmer switch",p:"MLP",tag:"× 36 layers",a:"gate, up: [12,288]",b:"h = SiLU(gate) × up, element by element",c:"h: [12,288]",wc:0,fl:function(){return 0},g:"mlp",tiny:1,
plain:"The gate works like a dimmer switch for each of the 12,288 features. SiLU is a smooth switch: for strongly negative inputs it is close to 0 (door closed), for large positive inputs it equals the input (door open), and in between it opens gradually. The result is multiplied with the <b>up</b> values one by one: the gate decides, up supplies.",
what:["SiLU(g) = g × sigmoid(g) = g ÷ (1 + e^(−g)).","This gated design is called <b>SwiGLU</b> and is why the MLP has three matrices instead of two.","An element-wise operation: cheap, with no weights."],
fo:"h = SiLU(gate) ⊙ up        (⊙ = multiply element by element)"},
{t:"Down projection and residual",p:"MLP",tag:"× 36 layers",a:"h: [12,288]",b:"× Wdown [12,288×4,096], then add the block input",c:"x₂: [4,096]",wc:50331648,fl:function(){return 2*50331648*36},g:"mlp",
plain:"The wide vector is squeezed back from 12,288 to 4,096 numbers by Wdown and then, as before, added onto the stream. The layer is finished.",
what:["Wdown is 12,288 × 4,096: 50 M weights.","Another residual connection: x₂ = x₁ + correction.","After this step the vector has been refined twice in this layer: once with information from other tokens (attention) and once with the model's own knowledge (MLP)."],
fo:"x₂ = x₁ + Wdown · h"},
{t:"Repeat for all 36 layers",p:"Repeat",tag:"once",a:"x: [4,096]",b:"Steps 3 to 13, 36 times, always with different weights",c:"x after layer 36: [4,096]",wc:0,fl:function(){return 0},g:"o",
custom:function(L){var wl=192937984;return mc("Weights per layer",F(wl))+mc("All 36 layers",F(wl*36)+" ("+B(wl*72)+" FP16)")+mc("FLOPs, 36 layers",F(2*wl*36+589824*L)+" at L = "+nf(L))+mc("KV cache written","147,456 B per token")+mc("Decode bottleneck","Memory-bound")},
plain:"Steps 3 to 13 are one <b>layer</b>. The same vector now goes through 36 layers, each with its own, different weights. Roughly speaking, early layers pick up simple patterns such as grammar and word shapes, and later layers combine them into more abstract meaning.",
what:["Layers run strictly one after another, so a single token cannot be spread over layers.","Every layer writes its own entry to the KV cache: 36 × 4,096 bytes = 147,456 bytes per token.","One layer has 192.9 M weights, all 36 layers 6.95 B. All of them are read from memory for every generated token, which is why decoding is memory-bound.","The toy only simulates layer 1. Layers 2 to 36 are the same recipe with different numbers."],
fo:"x ← Layer₃₆( … Layer₂( Layer₁(x) ) … )"},
{t:"Final norm and LM head: a score for every word",p:"Output",tag:"once",a:"x after layer 36: [4,096]",b:"RMSNorm, then × LM head [4,096 × 151,936]",c:"logits: [151,936]",wc:622329856,fl:function(){return 2*622329856},g:"o",
plain:"After the last layer the vector holds the model's understanding of the text so far. The <b>LM head</b> (language-model head) compares this vector with a description of every word in the dictionary and gives each word a score, called a <b>logit</b>: how well does this word fit as the next token?",
what:["One more RMSNorm, then a matrix of [151,936 × 4,096] = 622 M weights, the second largest matrix.","It is separate from the embedding table (<code>tie_word_embeddings</code> = false). Some models reuse the table here.","Output: one score for every token in the dictionary, 151,936 numbers.","Cost: 1.24 GFLOPs, and 1.24 GB of weights to stream in FP16."],
fo:"logits = LMhead · RMSNorm(x)        → [151,936]"},
{t:"Softmax and pick the next token",p:"Output",tag:"once",a:"logits: [151,936]",b:"Softmax, then choose a token",c:"next token ID",wc:0,fl:function(){return 0},g:"o",tiny:1,
plain:"Softmax (the same trick as in step 8) turns the scores into probabilities. Then the model chooses one token. <b>Greedy</b> takes the most probable one. <b>Sampling</b> rolls weighted dice, so likelier tokens win more often. Temperature and top-p change how adventurous the dice are. The chosen token is added to the text and the whole loop starts again.",
what:["The new token goes back to step 2 (embedding lookup). Only this one token is processed, everything older is read from the KV cache.","Generation stops when the model produces the end token (<code>eos_token_id</code> = 151645 in the config) or your <code>max_tokens</code> limit is reached.","The toy weights are random, so the chosen word is meaningless. Only the arithmetic is real."],
fo:"pᵢ = e^(logitᵢ) ÷ Σⱼ e^(logitⱼ),     next token = argmax p (greedy) or a sample from p"}
];
var N=S.length,cur=0,t=0,playing=false,spd=1,last=0,hold=0.7,PC={Input:"#8a8a86",Attention:"#1baf7a",MLP:"#2a78d6",Repeat:"#8a7bea",Output:"#8a8a86"};
function Lval(){return +$("lqB-L").value}
function realHtml(s,L){
var o='<div style="font-weight:600;margin-bottom:6px">The real Qwen3-8B in this step</div><div class="lq-flow"><div class="lq-bx"><small>Input</small>'+s.a+'</div><span class="lq-ar">→</span><div class="lq-bx op"><small>Operation</small>'+s.b+'</div><span class="lq-ar">→</span><div class="lq-bx"><small>Output</small>'+s.c+'</div></div><div class="lq-mg">';
if(s.custom)return o+s.custom(L)+"</div>";
var lay=s.tag==="× 36 layers"?36:1,fl=s.fl(L),kv=s.kv?s.kv(L):0,w=s.wc*lay;
o+=mc("Weights in this step",s.wc?F(s.wc)+(lay>1?" per layer":""):"none");
if(lay>1&&s.wc)o+=mc("All 36 layers",F(w)+" ("+B(w*2)+" FP16)");
if(s.wc&&lay===1)o+=mc("FP16 size",B(w*2));
o+=mc("FLOPs, whole model, 1 token",fl?F(fl):(s.tiny?"negligible":"0"));
if(kv)o+=mc("KV cache read, 1 user",B(kv));
if(s.kvw)o+=mc("KV cache written",B(s.kvw)+" per token");
o+=mc("Decode bottleneck",s.nomath?"Lookup, no math":(s.wc>=1e6||kv)?"Memory-bound":"Negligible cost");
return o+"</div>"}
function renderAll(){
var s=S[cur],L=Lval(),i;
var c="";for(i=0;i<N;i++)c+='<button type="button" data-i="'+i+'" class="'+(i===cur?"on":"")+'" title="'+S[i].t+'" aria-label="Step '+(i+1)+': '+S[i].t+'" style="min-width:32px;padding:4px 6px;border-bottom:3px solid '+PC[S[i].p]+'">'+(i+1)+'</button>';
$("lqB-chips").innerHTML=c;
$("lqB-title").innerHTML='<div class="lq-row" style="margin:0"><span class="lq-sm lq-mut">Step '+(cur+1)+' of '+N+'</span><span class="lq-badge" style="margin:0">'+s.p+'</span><span class="lq-badge" style="margin:0">'+s.tag+'</span></div><div style="font-size:18px;font-weight:600;margin:4px 0 8px">'+s.t+'</div>';
var ul="";s.what.forEach(function(w){ul+="<li>"+w+"</li>"});
$("lqB-desc").innerHTML='<div class="lq-card"><b>In plain words</b><p style="margin:6px 0 10px">'+s.plain+'</p><b>Formula</b><pre style="white-space:pre-wrap;margin:6px 0 0;font-size:12px"><code>'+s.fo+'</code></pre></div><div class="lq-card"><b>What happens</b><ul>'+ul+'</ul></div>';
$("lqB-real").innerHTML=realHtml(s,L);
var tot=0,f=[];for(i=0;i<N;i++){f[i]=S[i].fl(L);tot+=f[i]}
var bar="";for(i=0;i<N;i++){if(f[i]>0)bar+='<button type="button" class="lq-seg" data-i="'+i+'" title="'+S[i].t+'" aria-label="'+S[i].t+'" style="width:'+(100*f[i]/tot)+'%;background:'+({att:"#1baf7a",mlp:"#2a78d6",o:"#8a8a86"})[S[i].g]+';box-shadow:'+(i===cur?"inset 0 0 0 2px currentColor":"none")+'"></button>'}
$("lqB-bar").innerHTML=bar;
$("lqB-tot").textContent="Total: "+(tot/1e9).toFixed(1)+" GFLOPs per token at L = "+nf(L);
renderFrame()}
function renderFrame(){
$("lqB-stage").innerHTML=R[cur](t);
$("lqB-t").value=Math.round(t*1000);
$("lqB-play").textContent=playing?"⏸ Pause":(cur===N-1&&t>=1?"↺ Replay":"▶ Play")}
function goStep(i){cur=Math.max(0,Math.min(N-1,i));t=0;hold=0.7;renderAll()}
function loop(ts){
if(!playing){last=0;return}
if(!last)last=ts;
var dt=Math.min(0.1,(ts-last)/1000);last=ts;
if(t<1){t=Math.min(1,t+dt*spd/8)}
else{hold-=dt;if(hold<=0){if(cur<N-1){cur++;t=0;hold=0.7;renderAll();requestAnimationFrame(loop);return}else{playing=false}}}
renderFrame();
if(playing)requestAnimationFrame(loop)}
$("lqB-play").addEventListener("click",function(){
if(playing){playing=false;renderFrame();return}
if(cur===N-1&&t>=1){cur=0;t=0;renderAll()}
playing=true;last=0;hold=0.7;requestAnimationFrame(loop);renderFrame()});
$("lqB-prev").addEventListener("click",function(){goStep(cur-1)});
$("lqB-next").addEventListener("click",function(){goStep(cur+1)});
$("lqB-spd").addEventListener("change",function(){spd=+this.value});
$("lqB-head").addEventListener("change",function(){head=+this.value;renderFrame()});
$("lqB-L").addEventListener("change",renderAll);
$("lqB-t").addEventListener("input",function(){playing=false;t=(+this.value)/1000;renderFrame()});
function jump(e){var b=e.target.closest("button[data-i]");if(b)goStep(+b.getAttribute("data-i"))}
$("lqB-chips").addEventListener("click",jump);$("lqB-bar").addEventListener("click",jump);
renderAll();
})();
</script>

### What to look for

1. **Steps 3 and 4.** Watch the row-by-row dot products. The whole network is built from this one operation, repeated billions of times.
2. **Step 5.** The arrow length never changes, only its direction. That is why RoPE can encode position without destroying the content.
3. **Steps 7 to 9 with different heads.** Change the **Head** selector and see that each head attends to the earlier tokens differently, even in this tiny random model.
4. **Step 10 and 13.** After every block the vector is *old + correction*. Compare the strips: most of the numbers move only a little.
5. **The FLOP bar.** At 8,000 tokens of context the MLP accounts for roughly 55% of all FLOPs and attention math for about 24%. Switch **Context** to 1,000 and attention nearly vanishes, switch it to 40,000 and it rivals the MLP.
6. **Where the KV cache is read.** Only in steps 7 and 9 (scores and mixing). Every other weight-heavy step reads weights that are **shared by all users**. That asymmetry is the story of section 7.

### Glossary: the words of this section

| Term | Meaning in one line |
|---|---|
| Token / ID | A piece of text and its number in the dictionary |
| Embedding | The learned list of numbers (4,096 in Qwen3-8B) that stands for one token |
| Hidden state / residual stream | The vector that travels through all layers; every block adds a correction to it |
| Weights / parameters | The learned numbers inside the matrices (8.19 billion in total) |
| Matrix × vector | Every output number is a weighted sum of all input numbers |
| Dot product | Multiply two lists number by number and add up the results; measures how well they match |
| RMSNorm | Rescales a vector so that the typical size of its numbers is 1, then applies a learned scale |
| Query, key, value | What a token is looking for, how it is labelled, and what it hands over |
| Head | One independent group of query/key/value numbers (128 in Qwen3-8B) |
| GQA | Grouped-query attention: several query heads share one key/value head (32 vs 8) to shrink the KV cache |
| RoPE | Rotates pairs of numbers in q and k by an angle that depends on the position |
| KV cache | Stored keys and values of all earlier tokens, so they are not recomputed |
| Causal masking | A token can only look at earlier tokens and itself |
| Softmax | Turns any list of scores into percentages that add up to 100% |
| Residual connection | New vector = old vector + the block's correction |
| MLP / feed-forward | The per-token block with the widest vectors; holds most of the weights |
| SiLU / SwiGLU | A smooth switch and the gated design that uses it |
| Logit | The raw score of one dictionary word before softmax |
| Greedy / sampling / temperature | Take the best token, or roll weighted dice; temperature sets how adventurous the dice are |
| Decode / prefill | Generating one token at a time, versus reading the whole prompt at once (section 8) |

<a id="kvcache"></a>

## 5. The KV cache

The keys and values of earlier tokens never change. Instead of recomputing them for all `L` tokens at every step (which would multiply the work by `L`), the model **stores** them once and reads them back. That store is the **KV cache**.

```
bytes per token = 2 (K and V) × 36 layers × 8 kv_heads × 128 head_dim × 2 bytes (FP16)
                = 147,456 bytes ≈ 147 KB
```

| Context per user | KV cache per user | 10 users |
|---|---|---|
| 1,000 tokens | 0.15 GB | 1.5 GB |
| 4,000 tokens | 0.59 GB | 5.9 GB |
| 8,000 tokens | 1.18 GB | 11.8 GB |
| 16,000 tokens | 2.36 GB | 23.6 GB |
| 40,960 tokens (model limit) | 6.04 GB | 60.4 GB |

**Grouped-query attention (GQA) is why this is affordable.** With 32 KV heads instead of 8, every number above would be 4× larger (4.7 GB per user at 8,000 tokens). Notice the asymmetry: the cache stores **8** heads (bytes) but the attention math runs over **32** query heads (FLOPs). Every cached byte is therefore used by 4 query heads, which gives the KV part a fixed intensity of about 4 FLOP/byte, no matter how many users you have. Remember this when we reach section 7.

<a id="hardware"></a>

## 6. The hardware: T4, A100, H100 and what Azure offers

Only two spec-sheet numbers matter for this analysis:

| | T4 | A100 80 GB | H100 NVL |
|---|---|---|---|
| Memory | 16 GB GDDR6 | 80 GB HBM2e | 94 GB HBM3 |
| Bandwidth | 320 GB/s | 1,935 GB/s | ≈ 3,900 GB/s |
| FP16 tensor FLOPS (dense) | 65 TFLOPS | 312 TFLOPS | ≈ 835 TFLOPS |
| Ridge point | ≈ 203 FLOP/byte | ≈ 161 FLOP/byte | ≈ 214 FLOP/byte |
| bf16 support | no | yes | yes |
| Native FP8 compute | no | no | yes |

The A100 bandwidth figure matches the numbers published for Azure's NC A100 v4 series (A100 PCIe, 80 GB, 1,935 GB/s). The T4 and H100 figures are the vendor's published values as I know them, so check them against the current NVIDIA datasheets before you rely on them.

The ratios are what count: the A100 has **6× the bandwidth** and **4.8× the compute** of a T4.

### What you can ask for in Azure

| Azure VM series / size | GPU | GPU memory | Notes |
|---|---|---|---|
| NC4as / NC8as / NC16as T4 v3 | 1× T4 | 16 GB | the starting point |
| NC64as T4 v3 | 4× T4 | 64 GB | more of the same, memory still 16 GB per GPU |
| NV36ads A10 v5 | 1× A10 | 24 GB | modest step up |
| **NC24ads A100 v4** | 1× A100 PCIe | 80 GB | good fit for 8B models, NC48ads (2×) and NC96ads (4×) also exist |
| **NC40ads H100 v5** | 1× H100 NVL | 94 GB | highest single-GPU option, NC80adis has 2× |
| NCCads H100 v5 | 1× H100 | 94 GB | confidential computing variant (CPU and GPU in a trusted execution environment) |

Practical notes:

- GPU **quota is 0 by default**. Request it under *Subscriptions → Usage + quotas* for the right VM family (1× A100 needs 24 vCPUs, 1× H100 NVL needs 40).
- Microsoft's NC A100 v4 documentation says that only the NCads H100 v5 series receives net-new capacity, so A100 availability varies by region. Check *Azure Regions by Product*.
- One third-party comparison listed on-demand prices of roughly $3.20/h (A10), $4.41/h (A100), and $8.30/h (H100 NVL). These move, so use the Azure pricing calculator for your region. 1-year and 3-year reservations are discounted.
- **Spot** VMs can be evicted at any time, which is a poor fit for a production API.

<a id="roofline"></a>

## 7. Memory-bound or compute-bound? The full calculation

This is the core of the post. We take **decode** (one new token per user per step), `n` = 6 users, `L` = 8,000 tokens of context each, INT4 weights (≈ 5 GB) on both GPUs.

### Step 1: bytes read per step

Everything the GPU must fetch to produce one token for every user:

```
weights (shared by all users)      = 5.0 GB          (INT4, one read)
KV cache (private per user)        = 6 × 8,000 × 147,456 B = 7.08 GB
total per step                     = 12.08 GB
```

### Step 2: FLOPs per step

```
weights:    15.14 GFLOPs per user
attention:  589,824 × 8,000 = 4.72 GFLOPs per user
per user:   19.86 GFLOPs
6 users:    119.1 GFLOPs
```

INT4 does **not** reduce this: the weights are expanded back to FP16 inside the kernel, and the multiplication still happens at FP16.

### Step 3: arithmetic intensity

```
119.1 GFLOPs ÷ 12.08 GB = 9.9 FLOP/byte
```

Compare with the ridge points: 203 (T4) and 161 (A100). Your workload sits at about **5% and 6% of the ridge**. Both GPUs are deep in memory-bound territory: the tensor cores are idle more than 90% of the time.

### Step 4: time per step

I assume real kernels reach 60% of peak bandwidth, 30% of peak FLOPS on the T4 and 50% on the A100. These percentages are assumptions, not measurements.

| | T4 (INT4) | A100 (INT4) |
|---|---|---|
| Memory time: 12.08 GB ÷ (0.6 × bandwidth) | 62.9 ms | 10.4 ms |
| Compute time: 119.1 GFLOPs ÷ (efficiency × peak) | 6.1 ms | 0.8 ms |
| Step time = the larger of the two | **62.9 ms** | **10.4 ms** |
| Tokens/s per user | ≈ 16 | ≈ 96 |
| Aggregate tokens/s | ≈ 95 | ≈ 577 |

The A100 is **6.0× faster, exactly the bandwidth ratio** (1,935 ÷ 320 = 6.05). Same bytes, same FLOPs, both memory-bound, so only bandwidth matters. The A100's much larger compute capacity does nothing for decode here.

### Step 5: will more users make it compute-bound?

Weights are shared, but every user brings a private KV cache. The intensity as a function of users is:

```
I(n) = n × 19.86 GFLOPs ÷ (5 GB + n × 1.18 GB)
```

| Users n | Intensity (FLOP/byte) |
|---|---|
| 1 | 3.2 |
| 6 | 9.9 |
| 10 | 11.8 |
| 32 | 14.9 |
| ∞ | 19.86 ÷ 1.18 = **16.8** |

The curve flattens at 16.8, an order of magnitude below the ridge points. **At 8,000 tokens of context, decode never becomes compute-bound, however many users you add.** More users still raise the *aggregate* throughput, but with diminishing returns, because each user also brings their own cache to read. With short contexts (a few hundred tokens) the weights dominate and the ceiling is much higher.

### Try it: the roofline calculator

Move the sliders. The bar shows how many bytes per step are weights (blue) and how many are KV cache (orange). Each GPU card shows intensity, step times, tokens per second, the bottleneck, whether everything fits in VRAM, and the time to prefill one document of `L` tokens.

<div class="lq" id="lqC">
<div class="lq-row"><label style="min-width:150px" for="lqC-n">Concurrent users</label><input type="range" id="lqC-n" min="1" max="48" step="1" value="6"><b id="lqC-nv" style="min-width:50px;text-align:right"></b></div>
<div class="lq-row"><label style="min-width:150px" for="lqC-l">Context per user</label><input type="range" id="lqC-l" min="500" max="40000" step="500" value="8000"><b id="lqC-lv" style="min-width:60px;text-align:right"></b></div>
<div class="lq-row"><label>Weights <select id="lqC-w"><option value="5">INT4 AWQ, about 5 GB</option><option value="8.2">FP8, about 8.2 GB</option><option value="16.4">FP16, about 16.4 GB</option></select></label><label>KV cache <select id="lqC-k"><option value="1">FP16</option><option value="0.5">FP8</option></select></label></div>
<div class="lq-sm lq-mut" style="margin:10px 0 4px">Bytes read per decode step (the same on both GPUs)</div>
<div class="lq-bar"><div id="lqC-bw" style="background:#2a78d6"></div><div id="lqC-bk" style="background:#eb6834"></div></div>
<div class="lq-leg"><span><span class="lq-sw" style="background:#2a78d6"></span><span id="lqC-lw"></span></span><span><span class="lq-sw" style="background:#eb6834"></span><span id="lqC-lk"></span></span></div>
<div class="lq-grid" id="lqC-cards"></div>
</div>
<script>
(function(){
var G=[{name:"T4",bw:320e9,peak:65e12,ce:0.3,vram:16e9},{name:"A100 80GB",bw:1935e9,peak:312e12,ce:0.5,vram:80e9}];
function f(x,d){return x.toFixed(d)}
function $(id){return document.getElementById(id)}
function upd(){
var n=+$("lqC-n").value,L=+$("lqC-l").value,W=+$("lqC-w").value*1e9,kvb=147456*(+$("lqC-k").value);
$("lqC-nv").textContent=n;$("lqC-lv").textContent=L.toLocaleString("en-US");
var kv=n*L*kvb,bytes=W+kv,flops=n*(15.14e9+589824*L);
$("lqC-bw").style.width=(100*W/bytes)+"%";$("lqC-bk").style.width=(100*kv/bytes)+"%";
$("lqC-lw").textContent="Weights "+f(W/1e9,1)+" GB ("+f(100*W/bytes,0)+"%)";
$("lqC-lk").textContent="KV cache "+f(kv/1e9,1)+" GB ("+f(100*kv/bytes,0)+"%)";
var pf=L*15.14e9+589824*L*L/2;
var html="";
G.forEach(function(g){
var usable=0.9*g.vram-1.5e9,fits=(W+kv)<=usable,maxU=Math.max(0,Math.floor((usable-W)/(L*kvb)));
var tm=bytes/(0.6*g.bw),tc=flops/(g.ce*g.peak),step=Math.max(tm,tc),ridge=g.peak/g.bw,inten=flops/bytes,mem=tm>tc;
var b=!fits?'<span class="lq-badge bad">Does not fit, max '+maxU+' users</span>':mem?'<span class="lq-badge mem">Memory-bound</span>':'<span class="lq-badge cmp">Compute-bound</span>';
html+='<div class="lq-card"><div style="font-weight:600;margin-bottom:6px">'+g.name+'</div>'+
'<div class="lq-m"><span>FLOPs per step</span><span>'+f(flops/1e9,0)+' GFLOPs</span></div>'+
'<div class="lq-m"><span>Arithmetic intensity</span><span>'+f(inten,1)+' FLOP/byte</span></div>'+
'<div class="lq-m"><span>Ridge point</span><span>'+f(ridge,0)+' FLOP/byte</span></div>'+
'<div class="lq-m"><span>Memory time</span><span>'+f(tm*1e3,1)+' ms</span></div>'+
'<div class="lq-m"><span>Compute time</span><span>'+f(tc*1e3,1)+' ms</span></div>'+
'<div class="lq-m"><span>Per user</span><span>'+f(1/step,0)+' t/s</span></div>'+
'<div class="lq-m"><span>Aggregate</span><span>'+f(n/step,0)+' t/s</span></div>'+
'<div class="lq-m"><span>Max users that fit</span><span>'+maxU+'</span></div>'+
'<div class="lq-m"><span>Prefill of one L-token document</span><span>'+f(pf/(g.ce*g.peak),1)+' s</span></div>'+b+'</div>';
});
$("lqC-cards").innerHTML=html;
}
["lqC-n","lqC-l","lqC-w","lqC-k"].forEach(function(id){$(id).addEventListener("input",upd)});
upd();
})();
</script>

Things to try:

- **48 users**: intensity creeps toward 16.8 and never reaches either ridge point.
- **500 tokens of context**: weights dominate the bytes, and intensity rises.
- **FP8 KV cache**: the orange part of the bar halves.
- **FP16 weights on the T4**: it reports that nothing fits. The weights alone are 16.4 GB, which is why INT4 is mandatory on that card.

<a id="prefill"></a>

## 8. Prefill: why reading the prompt is a different problem

Before the first output token, the model must read the whole prompt. During this **prefill** phase all `T` prompt tokens are processed together, so each weight matrix multiplies a `[T × 4,096]` matrix instead of a single vector. Every weight is fetched once but used `T` times, so intensity is in the hundreds to thousands of FLOP/byte: **prefill is compute-bound**.

```
prefill FLOPs ≈ T × 15.14 GFLOPs + 589,824 × T² / 2
8,000 tokens  ≈ 121 TFLOPs + 19 TFLOPs ≈ 140 TFLOPs
```

| GPU | Effective compute | Prefill of 8,000 tokens |
|---|---|---|
| T4 (30% of 65) | ≈ 19.5 TFLOPS | ≈ 7.2 s |
| A100 (50% of 312) | ≈ 156 TFLOPS | ≈ 0.9 s |
| H100 NVL FP16 (50% of 835) | ≈ 417 TFLOPS | ≈ 0.34 s |

Two consequences:

1. **Quantizing the weights does not speed up prefill.** The memory term is tiny and the compute term is unchanged.
2. **Bursts hurt.** If ten users submit documents at the same instant, their prefills queue up: ten × 7.2 s means the last user waits over a minute on a T4 before seeing the first token.

<a id="quant"></a>

## 9. Quantization: what INT4, FP8 and FP16 really change

Quantization only changes **bytes** (and with them decode speed). It does not change FLOPs.

| Format | Bytes per weight | 8.2 B weights | Notes |
|---|---|---|---|
| FP16 | 2 | ≈ 16.4 GB | does not fit with cache on a 16 GB T4 |
| FP8 | 1 | ≈ 8.2 GB | native FP8 compute only on Hopper/Ada. Older GPUs can use weight-only kernels |
| INT4 (AWQ/GPTQ) | ≈ 0.5 + scales | ≈ 4 to 5 GB | smallest, small accuracy loss |

**On the T4 (Turing)**: no bf16, no FP8, and the fast Marlin INT4 kernels need Ampere (compute capability 8.0) or newer. The realistic choices are FP16 or INT4 (AWQ or GPTQ). AWQ usually preserves quality a little better, GPTQ is sometimes slightly faster. Benchmark both. bitsandbytes NF4 is for fine-tuning, not for serving.

**Decode speed per user on an A100** (n = 6, L = 8,000, 60% of peak bandwidth):

| Weights | KV cache | Bytes per step | Step time | Tokens/s per user |
|---|---|---|---|---|
| FP16 (16.4 GB) | FP16 | 23.5 GB | 20.2 ms | ≈ 50 |
| FP8 (8.2 GB) | FP16 | 15.3 GB | 13.2 ms | ≈ 76 |
| INT4 (5 GB) | FP16 | 12.1 GB | 10.4 ms | ≈ 96 |
| INT4 (5 GB) | FP8 | 8.5 GB | 7.4 ms | ≈ 136 |

Notice that with INT4 weights the 7.08 GB KV cache is already **more than half of the bytes** you read each step. At that point the cache, not the weights, is what you should shrink next.

**My recommendation for an extraction workload:** on the T4 you have no choice but INT4. On the A100 start with FP16 or FP8 weights for the best accuracy (wrong digits or broken schemas are expensive in structured extraction), and move to INT4 only if you measure that you need the speed and your accuracy test set still passes. Support for a quantized KV cache differs between GPUs and vLLM versions, so check it for your setup.

<a id="cuda"></a>

## 10. CUDA indexing: threads, blocks and grids

We have established that decode is a long sequence of matrix × vector products, each limited by how fast the weights stream out of VRAM. Now let us look at how a GPU actually executes one of them.

### The hierarchy

A CUDA kernel launch creates a **grid** of **blocks**, and each block contains **threads**. Inside the kernel every thread can read built-in variables that tell it who it is:

| Variable | Meaning |
|---|---|
| `threadIdx.x` | index of the thread inside its block |
| `blockIdx.x` | index of the block inside the grid |
| `blockDim.x` | number of threads per block |
| `gridDim.x` | number of blocks in the grid |

The classic recipe for a unique 1-D thread number is:

```
global_tid = blockIdx.x * blockDim.x + threadIdx.x
```

The hardware groups the threads of a block into **warps of 32 threads** that execute in lockstep. A block runs on one **streaming multiprocessor (SM)**. A T4 has 40 SMs, an A100 has 108. The number of blocks that can be resident on an SM at the same time is limited by thread count (T4: 1,024 threads per SM, A100: 2,048), so a large grid is processed in "waves".

### A matrix-vector kernel

`y = W · x` for a matrix `W` with `M` rows and `K` columns. The natural mapping is: **one warp per row**, so the 32 lanes of a warp read 32 neighbouring elements of the same row (one coalesced load) and then add up their partial sums:

```cpp
__global__ void gemv_warp_per_row(const half* W, const half* x, float* y, int M, int K) {
    int global_tid = blockIdx.x * blockDim.x + threadIdx.x;
    int row  = global_tid / 32;          // which row this warp owns
    int lane = threadIdx.x % 32;         // position inside the warp
    if (row >= M) return;                // the whole warp leaves together

    float acc = 0.f;
    for (int k = lane; k < K; k += 32)   // lanes stride across the row
        acc += __half2float(W[row * K + k]) * __half2float(x[k]);

    for (int off = 16; off > 0; off >>= 1)          // sum the 32 partial results
        acc += __shfl_down_sync(0xffffffff, acc, off);

    if (lane == 0) y[row] = acc;
}

// launch: threads = 256 means 8 rows per block
// int threads = 256;
// int blocks  = (M * 32 + threads - 1) / threads;
// gemv_warp_per_row<<<blocks, threads>>>(W, x, y, M, K);
```

Every weight is read exactly once and used for exactly one multiply and one add. That is intensity 1 FLOP/byte for a single vector, which is memory-bound by construction. Production kernels (Marlin, FlashInfer, cuBLAS) use vectorised loads and more tricks, but the indexing logic is the same.

### Play with the indexing

Pick a strategy and a block size. Every chip is one row of a small toy matrix and shows which block and thread (or warp) owns it. Click a chip for the exact index arithmetic. The lower part scales the same launch up to the real Qwen3-8B matrices and shows block counts, waves per GPU, how many bytes a warp pulls in per load, and the lower bound on time from memory bandwidth.

<div class="lq" id="lqD">
<div class="lq-row">
<label>Strategy <select id="lqD-s"><option value="warp">one warp (32 threads) per row</option><option value="thread">one thread per row</option></select></label>
<label>Threads per block <select id="lqD-b"><option>32</option><option>64</option><option selected>128</option><option>256</option></select></label>
<label>Toy matrix rows <select id="lqD-m"><option>8</option><option selected>16</option><option>24</option></select></label>
</div>
<div id="lqD-launch" class="lq-card"></div>
<div id="lqD-map" class="lq-chips"></div>
<div id="lqD-detail" class="lq-card lq-sm" style="margin-bottom:14px">Click a row to see its indices.</div>
<div class="lq-row"><label>Real Qwen3-8B matrix <select id="lqD-r">
<option value="4096,4096,Wq (4,096 × 4,096)">Wq (4,096 × 4,096)</option>
<option value="1024,4096,Wk (1,024 × 4,096)">Wk (1,024 × 4,096)</option>
<option value="12288,4096,Wgate (12,288 × 4,096)">Wgate (12,288 × 4,096)</option>
<option value="4096,12288,Wdown (4,096 × 12,288)">Wdown (4,096 × 12,288)</option>
<option value="151936,4096,LM head (151,936 × 4,096)">LM head (151,936 × 4,096)</option>
</select></label></div>
<div id="lqD-real" class="lq-grid"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
var pal=["rgba(42,120,214,.30)","rgba(27,175,122,.30)","rgba(235,104,52,.30)","rgba(138,123,234,.30)","rgba(237,161,0,.30)","rgba(232,123,164,.30)"];
var palB=["#2a78d6","#1baf7a","#eb6834","#8a7bea","#eda100","#e87ba4"];
var sel=0;
function demo(){
var warp=$("lqD-s").value==="warp",B=+$("lqD-b").value,M=+$("lqD-m").value,tpr=warp?32:1;
var total=M*tpr,blocks=Math.ceil(total/B),launched=blocks*B,idle=launched-total;
$("lqD-launch").innerHTML='<div class="lq-m"><span>Launch</span><span><code>kernel&lt;&lt;&lt;'+blocks+', '+B+'&gt;&gt;&gt;</code> = '+blocks+' block(s) of '+B+' threads</span></div>'+
'<div class="lq-m"><span>Threads needed</span><span>'+M+' rows × '+tpr+' = '+total+'</span></div>'+
'<div class="lq-m"><span>Threads launched / idle</span><span>'+launched+' / '+idle+(idle?' (they return at <code>if (row &gt;= M)</code>)':'')+'</span></div>'+
'<div class="lq-m"><span>Rows per block</span><span>'+(B/tpr)+'</span></div>';
var h="";
for(var r=0;r<M;r++){
var g0=r*tpr,bl=Math.floor(g0/B),t0=g0%B,wi=Math.floor(t0/32);
var txt=warp?'row '+r+'<br>block '+bl+', warp '+wi+'<br>threads '+t0+' to '+(t0+31):'row '+r+'<br>block '+bl+'<br>thread '+t0;
h+='<button type="button" class="lq-chip" data-r="'+r+'" style="background:'+pal[bl%6]+';border-color:'+palB[bl%6]+(r===sel?';box-shadow:inset 0 0 0 2px currentColor':'')+'">'+txt+'</button>';
}
$("lqD-map").innerHTML=h;
detail();
real();
}
function detail(){
var warp=$("lqD-s").value==="warp",B=+$("lqD-b").value,tpr=warp?32:1,r=sel;
if(r>=+$("lqD-m").value){sel=0;r=0}
var g0=r*tpr,bl=Math.floor(g0/B),t0=g0%B;
var s='<b>Row '+r+'</b><br>blockIdx.x = '+bl+', blockDim.x = '+B+', threadIdx.x = '+t0+(warp?' to '+(t0+31):'')+'<br>';
s+=warp?'global_tid = '+bl+' × '+B+' + '+t0+' = '+g0+' (first lane)<br>row = global_tid / 32 = '+r+', lane = threadIdx.x % 32 = 0 to 31':'global_tid = '+bl+' × '+B+' + '+t0+' = '+g0+'<br>row = global_tid = '+r;
$("lqD-detail").innerHTML=s;
}
function real(){
var p=$("lqD-r").value.split(","),R=+p[0],K=+p[1],warp=$("lqD-s").value==="warp",B=+$("lqD-b").value,tpr=warp?32:1;
var blocks=Math.ceil(R*tpr/B);
var gpus=[{n:"T4",sm:40,thr:1024,mb:16,bw:320e9},{n:"A100",sm:108,thr:2048,mb:32,bw:1935e9}];
var bytes=R*K*2;
var h="";
gpus.forEach(function(g){
var per=Math.min(g.mb,Math.floor(g.thr/B)),cap=g.sm*per,waves=Math.ceil(blocks/cap);
h+='<div class="lq-card"><div style="font-weight:600;margin-bottom:6px">'+g.n+' ('+g.sm+' SMs)</div>'+
'<div class="lq-m"><span>Blocks in the grid</span><span>'+nf(blocks)+'</span></div>'+
'<div class="lq-m"><span>Resident blocks per SM</span><span>'+per+'</span></div>'+
'<div class="lq-m"><span>Blocks running at once</span><span>'+nf(cap)+'</span></div>'+
'<div class="lq-m"><span>Waves</span><span>'+waves+(blocks<cap?' (grid is smaller than the GPU can hold)':'')+'</span></div>'+
'<div class="lq-m"><span>Weights streamed (FP16)</span><span>'+(bytes/1e6).toFixed(1)+' MB</span></div>'+
'<div class="lq-m"><span>Time at 60% of bandwidth</span><span>'+(bytes/(0.6*g.bw)*1e6).toFixed(0)+' µs</span></div></div>';
});
var stride=K*2,sect=warp?2:32,useful=64,moved=sect*32;
h+='<div class="lq-card"><div style="font-weight:600;margin-bottom:6px">Memory access of one warp load</div>'+
'<div class="lq-m"><span>Address gap between lane 0 and lane 1</span><span>'+(warp?'2 B':nf(stride)+' B')+'</span></div>'+
'<div class="lq-m"><span>32-byte sectors touched</span><span>'+sect+'</span></div>'+
'<div class="lq-m"><span>Bytes moved / bytes used</span><span>'+moved+' / '+useful+'</span></div>'+
'<div class="lq-m"><span>Load efficiency (first estimate)</span><span>'+(100*useful/moved).toFixed(0)+'%</span></div>'+
'<div class="lq-sm lq-mut" style="margin-top:6px">'+(warp?'Neighbouring lanes read neighbouring elements: coalesced.':'Each lane sits in a different row, one full row apart. Caches soften this, but it is the reason real kernels give a warp to a row.')+'</div></div>';
$("lqD-real").innerHTML=h;
}
$("lqD-map").addEventListener("click",function(e){var b=e.target.closest("button[data-r]");if(b){sel=+b.getAttribute("data-r");demo()}});
["lqD-s","lqD-b","lqD-m","lqD-r"].forEach(function(id){$(id).addEventListener("change",demo)});
demo();
})();
</script>

What to notice:

- **One thread per row** with a toy matrix of 16 rows and a block of 128 threads launches 112 idle threads, and one warp's loads are scattered over 32 separate rows.
- **One warp per row** needs 32× more threads, but each load is one contiguous 64-byte read. That is why this layout is the textbook choice for matrix-vector products.
- **Small matrices underfill big GPUs.** `Wk` (1,024 rows) with a 256-thread block makes only 128 blocks, which does not even give each of the A100's 108 SMs two blocks. The `Wq` and `Wgate` matrices produce enough blocks to keep everything busy. This is one reason why kernels for small matrices split the work further across the `K` dimension.
- The "time at 60% of bandwidth" line is the **lower bound** for streaming that matrix once. Add the 36 layers and seven matrices and you get back to the 62.9 ms and 10.4 ms steps from section 7.

<a id="timeline"></a>

## 11. Reading a pipeline timeline

I plotted every document of a batch run as a horizontal bar and coloured the phases: **beige** = waiting for the LLM (queued), **orange** = prefill until the first token, **dark green** = decode. With the numbers above you can read such a chart quantitatively.

What it showed on the T4 and what it means:

1. **Decode (green) dominated**, around 80 to 90% of each bar. Prefill was small. At ≈ 15 t/s per user, a 100 to 450 s green bar is roughly 1,500 to 6,800 generated tokens, much more than I had assumed. Generation length, not prompt length, was the cost.
2. **About six parallel slots.** The first six documents started together and every later one started exactly when an earlier one finished. A client-side worker pool or a server limit (`max_num_seqs`) caps concurrency. On a T4 with 8,000-token contexts, the KV cache allows about 6 users anyway (the calculator in section 7 reports 6).
3. **The first orange bars were long** (45 to 90 s). Six prefills at the same moment compete for compute, the burst effect from section 8.
4. **One document ran for 2,100 s**, which at ≈ 15 t/s is about 30,000 tokens. That is a runaway generation (a repetition loop or a missing stop condition). It blocked one of six slots for the whole run and decided the length of the whole batch.

What would change on an A100 (estimates from the model above):

| | T4 now | A100, same 6 slots | A100, 16 to 32 slots |
|---|---|---|---|
| Per-user decode speed | ≈ 15 t/s | ≈ 50 (FP16) to 96 (INT4) t/s | ≈ 20 to 35 t/s |
| First-token delay for the first six | 45 to 90 s | 6 to 12 s | a few seconds |
| Queueing (beige) | long | shorter | almost none |
| 36 documents without the runaway | ≈ 1,500 s | ≈ 450 s | ≈ 250 s |

**Fix these before buying hardware:**

- Set `max_tokens` to what your JSON can plausibly need and add a request timeout.
- Use JSON-schema guided (structured) decoding so the model cannot loop outside the schema.
- Write compact JSON: no indentation, short keys, only the fields you need. Every token you do not generate is time saved, because decode is the expensive phase.
- For Qwen3, check that thinking mode is off (`enable_thinking=False` in the chat template). Reasoning tokens would easily explain multi-thousand-token outputs.
- Raise concurrency only after moving to the A100.

<a id="vllm"></a>

## 12. A vLLM checklist

Flag names change between versions, so confirm them with `vllm serve --help`.

```bash
# T4: FP16 compute, INT4 weights, bounded context
vllm serve <your-awq-checkpoint> \
  --quantization awq \
  --dtype half \
  --max-model-len 8192 \
  --max-num-seqs 6 \
  --gpu-memory-utilization 0.90

# A100: native bf16, higher concurrency, longer headroom
vllm serve Qwen/Qwen3-8B \
  --dtype bfloat16 \
  --max-model-len 8192 \
  --max-num-seqs 32 \
  --gpu-memory-utilization 0.90
```

- `--max-model-len` should match your real maximum prompt plus output. A smaller number frees VRAM for more concurrent users.
- `--max-num-seqs` is the server-side concurrency. Compare 6, 16 and 32 and look for the knee in aggregate throughput.
- To measure with your own prompts, use vLLM's `benchmark_serving.py` at 10, 20 and 50 parallel requests and record time to first token, tokens per second per user and **extraction accuracy** (not only speed).
- For a quantized KV cache (`--kv-cache-dtype`), check what your GPU and vLLM version support before relying on it.

<a id="sources"></a>

## 13. Limits of this post and sources

**What is exact:** every shape, weight count, FLOP count and KV-cache size is derived from the real `config.json`. They add up to the advertised 8B parameters.

**What is a toy:** the animated simulation in section 4 runs a miniature network (8 numbers per token, random weights). Every number it shows is really computed with the true formulas, but because the weights are random, the word it predicts means nothing. The "real Qwen3-8B" panels next to it use the true sizes from the config.

**What is an estimate:**

- The efficiency factors (60% of bandwidth, 30% and 50% of peak FLOPS) and the 5 GB size of an INT4 checkpoint are assumptions. Real kernels vary.
- The model ignores softmax, normalization, activation functions, rotary encoding, kernel-launch overhead and the cost of dequantizing INT4. Matrix-vector attention also often cannot use tensor cores efficiently. None of this changes the memory-bound verdict, because compute is only a few percent of the step time, but it does shift the absolute numbers.
- The overhead of 1.5 GB and 90% usable VRAM in the calculators are rough defaults. Your real limit depends on `--gpu-memory-utilization`.
- Hardware figures other than the A100 bandwidth come from vendor data as I know it, and prices from a third-party comparison. Verify them before you buy.

**What to do with the numbers:** use them to understand *why* things are slow and *what to measure*, then benchmark on the real hardware with your own prompts.

**Sources**

- Qwen3-8B `config.json`: [huggingface.co/Qwen/Qwen3-8B](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json)
- Azure NC-family GPU sizes: [learn.microsoft.com, NC family](https://learn.microsoft.com/en-us/azure/virtual-machines/sizes/gpu-accelerated/nc-family)
- Azure NC A100 v4 series: [learn.microsoft.com, NC A100 v4](https://learn.microsoft.com/en-us/azure/virtual-machines/nc-a100-v4-series)
- Hugging Face on Azure, supported hardware list: [huggingface.co/docs/microsoft-azure](https://huggingface.co/docs/microsoft-azure/azure-ai/hardware)
- Third-party Azure GPU price comparison: [thundercompute.com](https://www.thundercompute.com/blog/azure-gpu-instances)
- CUDA programming guide (threads, blocks, grids, warps): [docs.nvidia.com/cuda](https://docs.nvidia.com/cuda/cuda-c-programming-guide/)

*Next time: what happens when we replace the single-GPU setup with tensor parallelism and why the interconnect suddenly matters.*
