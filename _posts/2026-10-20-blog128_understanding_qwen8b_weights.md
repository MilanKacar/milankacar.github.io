---
layout: post
title: "#128 How an LLM Runs on a GPU: Qwen3-8B, Memory vs Compute"
categories: [AI, Software Engineering, GPU]
tags: [LLM, Qwen3, GPU, CUDA, vLLM, Quantization, Visualization]
difficulty: Medium
---

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
</style>

> **TL;DR**: A GPU does not run "a model". It runs thousands of tiny threads that each compute a few multiplications and additions. In this post we follow one token through **Qwen3-8B**, starting at its real `config.json`, count every weight, FLOP and byte, find out why generating text is *memory-bound*, compare an NVIDIA T4 with an A100, and finally see how **CUDA indexing** (`threadIdx`, `blockIdx`, `blockDim`, grids) maps the matrix-vector products onto the hardware. Everything has a widget you can play with.

I wrote this while moving a JSON-extraction service (a quantized 8B model on a single T4, around ten concurrent users) to a bigger GPU on Azure. The question "which GPU, and how fast will it be?" turned out to be answerable with arithmetic you can do on paper. The numbers in the widgets all come from the model's config, so you can check every one of them.

**All interactive widgets work in the light and the dark theme**: they inherit your text color and use transparent backgrounds.

## Contents

1. [Where the numbers come from: `config.json`](#config)
2. [The three ideas: FLOPs, bytes, arithmetic intensity](#basics)
3. [The architecture, drawn with the config values](#architecture)
4. [One token's journey, step by step (interactive)](#journey)
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

## 4. One token's journey, step by step (interactive)

Here is the whole forward pass for **one decoded token**, with the tensor shapes from the config. Click through the 14 steps. The bar at the bottom shows how the FLOPs are distributed, and the slider changes the context length so you can watch attention grow.

<div class="lq" id="lqB">
<div id="lqB-nav" class="lq-row" style="gap:6px"></div>
<div class="lq-row"><label style="min-width:130px" for="lqB-L">Context so far (L)</label><input type="range" id="lqB-L" min="500" max="40000" step="500" value="8000"><b id="lqB-Lv" style="min-width:60px;text-align:right"></b></div>
<div id="lqB-stage" class="lq-card" style="margin-top:12px"></div>
<div class="lq-sm lq-mut" style="margin:14px 0 4px">Share of the FLOPs for one decoded token (click a segment to jump to that step)</div>
<div id="lqB-bar" class="lq-bar"></div>
<div class="lq-leg"><span><span class="lq-sw" style="background:#1baf7a"></span>Attention</span><span><span class="lq-sw" style="background:#2a78d6"></span>MLP</span><span><span class="lq-sw" style="background:#8a8a86"></span>Embedding and output head</span><span id="lqB-tot" style="margin-left:auto"></span></div>
</div>
<script>
(function(){
var S=[
{t:"Text becomes token IDs",tag:"once",a:"\"The invoice total is 120 EUR\"",b:"Tokenizer",c:"T integer IDs",cap:"The text is cut into pieces called tokens. Each token is an integer from 0 to 151,935 (vocab_size).",wc:0,fl:function(){return 0},g:"o"},
{t:"Embedding lookup",tag:"once",a:"T IDs",b:"Copy rows from the table [151,936 × 4,096]",c:"x: [T × 4,096]",cap:"Pure lookup, no math. Each ID selects one row of 4,096 numbers. During decode T = 1, so only one 8 KB row is read.",wc:622329856,fl:function(){return 0},g:"o",nomath:1},
{t:"RMSNorm before attention",tag:"× 36 layers",a:"x: [4,096]",b:"Divide by the root-mean-square, multiply by a learned scale [4,096]",c:"x̂: [4,096]",cap:"Keeps the numbers in a stable range. About 4 × 4,096 operations, negligible next to the matrix products.",wc:4096,fl:function(){return 0},g:"att",tiny:1},
{t:"Q, K, V projections",tag:"× 36 layers",a:"x̂: [4,096]",b:"× Wq [4,096 × 4,096], Wk [4,096 × 1,024], Wv [4,096 × 1,024]",c:"q: [32 × 128], k: [8 × 128], v: [8 × 128]",cap:"Three matrix-vector products. The query gets 32 heads of 128 numbers, key and value only 8 heads each (grouped-query attention).",wc:25165824,fl:function(){return 2*25165824*36},g:"att",gqa:1},
{t:"Q/K norm and RoPE",tag:"× 36 layers",a:"q, k",b:"RMSNorm per head, then rotate number pairs by a position angle (rope_theta 1,000,000)",c:"q, k: same shapes",cap:"RoPE encodes where the token sits in the sequence. The per-head q/k norm is part of the Qwen3 design, not a config key.",wc:256,fl:function(){return 0},g:"att",tiny:1},
{t:"Append to the KV cache",tag:"× 36 layers",a:"k: [8 × 128], v: [8 × 128]",b:"Write as a new row at position L",c:"cache K, V: [L+1 × 8 × 128] each",cap:"The new k and v are stored so no later token has to recompute them. That costs 2 × 8 × 128 × 2 bytes = 4,096 bytes per layer.",wc:0,fl:function(){return 0},g:"att",kvw:147456},
{t:"Attention scores",tag:"× 36 layers",a:"q: [32 × 128], K cache: [L × 8 × 128]",b:"Each query head · every cached key, ÷ √128",c:"scores: [32 × L]",cap:"Every query head asks how relevant each earlier token is. Four query heads reuse each K head, which is why FLOPs use 32 heads but bytes only 8.",wc:0,fl:function(L){return 8192*L*36},kv:function(L){return 2048*L*36},g:"att",gqa:1},
{t:"Softmax and mix the values",tag:"× 36 layers",a:"scores: [32 × L], V cache: [L × 8 × 128]",b:"Softmax over L, then weighted sum of the value vectors",c:"o: [32 × 128] = [4,096]",cap:"Scores become percentages that sum to 100%, and each head blends the values of the tokens it attends to.",wc:0,fl:function(L){return 8192*L*36},kv:function(L){return 2048*L*36},g:"att",gqa:1,ill:1},
{t:"Output projection and residual",tag:"× 36 layers",a:"o: [4,096]",b:"× Wo [4,096 × 4,096], then add the layer input",c:"x: [4,096]",cap:"Heads are mixed back into one vector. The residual add keeps the original information flowing through.",wc:16777216,fl:function(){return 2*16777216*36},g:"att"},
{t:"MLP gate and up projections",tag:"× 36 layers",a:"RMSNorm(x): [4,096]",b:"× Wgate and × Wup, each [4,096 × 12,288]",c:"gate, up: [12,288] each",cap:"The vector is expanded three times wider. This is the largest block of weights in each layer.",wc:100663296,fl:function(){return 2*100663296*36},g:"mlp"},
{t:"SiLU gating",tag:"× 36 layers",a:"gate, up: [12,288]",b:"h = silu(gate) × up, element by element",c:"h: [12,288]",cap:"The gate decides how much of each of the 12,288 features passes through. Cheap element-wise math.",wc:0,fl:function(){return 0},g:"mlp",tiny:1},
{t:"MLP down projection and residual",tag:"× 36 layers",a:"h: [12,288]",b:"× Wdown [12,288 × 4,096], then add the block input",c:"x: [4,096]",cap:"Back to 4,096 numbers. Steps 3 to 12 now repeat for layers 2 through 36, each with its own weights, always on this same single vector.",wc:50331648,fl:function(){return 2*50331648*36},g:"mlp"},
{t:"Final norm and LM head",tag:"once",a:"x after layer 36: [4,096]",b:"RMSNorm, then × LM head [4,096 × 151,936]",c:"logits: [151,936]",cap:"One score for every token in the vocabulary. The head is separate from the embedding table (tie_word_embeddings is false).",wc:622329856,fl:function(){return 2*622329856},g:"o"},
{t:"Softmax and pick the next token",tag:"once",a:"logits: [151,936]",b:"Softmax, then sample or take the highest",c:"next token ID",cap:"The chosen token is appended and the loop restarts at step 2 with only that one token. Everything older is read from the KV cache.",wc:0,fl:function(){return 0},g:"o",tiny:1}
];
var cur=0,col={att:"#1baf7a",mlp:"#2a78d6",o:"#8a8a86"};
var toks=["The","invoice","total","is","120","EUR"],aw=[0.04,0.18,0.12,0.10,0.46,0.10];
var elL=document.getElementById("lqB-L"),elNav=document.getElementById("lqB-nav"),elBar=document.getElementById("lqB-bar");
function F(x){if(x>=1e9)return (x/1e9).toFixed(2)+" G";if(x>=1e6)return (x/1e6).toFixed(1)+" M";if(x>=1e3)return (x/1e3).toFixed(1)+" K";return Math.round(x)+""}
function B(x){if(x>=1e9)return (x/1e9).toFixed(2)+" GB";if(x>=1e6)return (x/1e6).toFixed(1)+" MB";if(x>=1e3)return (x/1e3).toFixed(1)+" KB";return Math.round(x)+" B"}
function gq(){var h='<div style="display:flex;gap:6px;flex-wrap:wrap;margin-top:12px">';for(var g=0;g<8;g++){h+='<div style="border:1px solid var(--lq-line);border-radius:6px;padding:4px 6px;font-size:11px;text-align:center"><div style="display:flex;gap:2px;justify-content:center">';for(var q=0;q<4;q++)h+='<span style="width:8px;height:8px;border-radius:2px;background:#1baf7a;display:inline-block"></span>';h+='</div><div class="lq-mut" style="margin-top:3px">KV head '+g+'</div></div>'}return h+'</div><div class="lq-sm lq-mut" style="margin-top:4px">32 query heads (squares) in 8 groups, each group shares one K/V head</div>'}
function ill(){var h='<div class="lq-sm lq-mut" style="margin-top:12px">Illustrative only: one made-up head attending from the next position</div>';for(var i=0;i<toks.length;i++)h+='<div class="lq-sm" style="display:flex;align-items:center;gap:8px;margin-top:3px"><span style="min-width:56px">'+toks[i]+'</span><div style="height:10px;border-radius:2px;background:#eb6834;width:'+(aw[i]*240)+'px"></div><span>'+Math.round(aw[i]*100)+'%</span></div>';return h}
function render(){
var L=+elL.value;document.getElementById("lqB-Lv").textContent=L.toLocaleString("en-US");
var s=S[cur],lay=s.tag==="× 36 layers"?36:1,fl=s.fl(L),kv=s.kv?s.kv(L):0,w=s.wc*lay;
var m='<div class="lq-mc"><small>Weights in this step</small><b>'+(s.wc?F(s.wc)+(lay>1?" per layer":""):"none")+'</b></div>';
if(lay>1&&s.wc)m+='<div class="lq-mc"><small>All 36 layers</small><b>'+F(w)+" ("+B(w*2)+" FP16)</b></div>";
if(s.wc&&lay===1)m+='<div class="lq-mc"><small>FP16 size</small><b>'+B(w*2)+'</b></div>';
m+='<div class="lq-mc"><small>FLOPs, whole model, 1 token</small><b>'+(fl?F(fl):(s.tiny?"negligible":"0"))+'</b></div>';
if(kv)m+='<div class="lq-mc"><small>KV cache read, 1 user</small><b>'+B(kv)+'</b></div>';
if(s.kvw)m+='<div class="lq-mc"><small>KV cache written</small><b>'+B(s.kvw)+' per token</b></div>';
var bound=s.nomath?"Lookup, no math":(s.wc>=1e6||kv)?"Memory-bound in decode":"Negligible cost";
m+='<div class="lq-mc"><small>Decode bottleneck</small><b>'+bound+'</b></div>';
document.getElementById("lqB-stage").innerHTML=
'<div class="lq-row" style="margin:0"><span class="lq-sm lq-mut">Step '+(cur+1)+' of '+S.length+'</span><span class="lq-sm lq-badge" style="margin:0">'+s.tag+'</span></div>'+
'<div style="font-size:18px;font-weight:600;margin:6px 0 0">'+s.t+'</div>'+
'<div class="lq-flow"><div class="lq-bx"><small>Input</small>'+s.a+'</div><span class="lq-ar">→</span><div class="lq-bx op"><small>Operation</small>'+s.b+'</div><span class="lq-ar">→</span><div class="lq-bx"><small>Output</small>'+s.c+'</div></div>'+
'<div class="lq-mut" style="line-height:1.6">'+s.cap+'</div>'+(s.gqa?gq():"")+(s.ill?ill():"")+'<div class="lq-mg">'+m+'</div>';
var n='<button type="button" data-i="'+(cur-1)+'" aria-label="Previous step">←</button>';
for(var i=0;i<S.length;i++)n+='<button type="button" data-i="'+i+'" class="'+(i===cur?"on":"")+'" style="min-width:32px;padding:4px 6px" aria-label="Step '+(i+1)+'">'+(i+1)+'</button>';
elNav.innerHTML=n+'<button type="button" data-i="'+(cur+1)+'" aria-label="Next step">→</button>';
var tot=0,f=[];for(i=0;i<S.length;i++){f[i]=S[i].fl(L);tot+=f[i]}
var bar="";for(i=0;i<S.length;i++){if(f[i]>0)bar+='<button type="button" class="lq-seg" data-i="'+i+'" title="'+S[i].t+'" aria-label="'+S[i].t+'" style="width:'+(100*f[i]/tot)+'%;background:'+col[S[i].g]+';box-shadow:'+(i===cur?'inset 0 0 0 2px currentColor':'none')+'"></button>'}
elBar.innerHTML=bar;
document.getElementById("lqB-tot").textContent="Total: "+(tot/1e9).toFixed(1)+" GFLOPs per token at L = "+L.toLocaleString("en-US");
}
function go(i){cur=Math.max(0,Math.min(S.length-1,i));render()}
function click(e){var b=e.target.closest("button[data-i]");if(b)go(+b.getAttribute("data-i"))}
elNav.addEventListener("click",click);elBar.addEventListener("click",click);elL.addEventListener("input",render);
render();
})();
</script>

What to look for while stepping through:

1. **The shapes.** The main vector stays at `[4,096]` through all 36 layers. Inside a layer it temporarily widens to `[12,288]` (MLP) and splits into `[32 × 128]` query heads and `[8 × 128]` key/value heads (attention). At the very end it fans out to `[151,936]` logits.
2. **The FLOP bar.** At 8,000 tokens of context the MLP accounts for roughly 55% of all FLOPs, attention math for about 24%. Slide the context to 500 and attention nearly vanishes. Slide it to 40,000 and attention rivals the MLP.
3. **Where the KV cache is read.** Only steps 7 and 8. Every other weight-heavy step reads weights that are **shared by all users**. That asymmetry is the whole story of the next sections.

The made-up attention percentages in step 8 are illustrative only. Real attention patterns depend on the trained weights.

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
