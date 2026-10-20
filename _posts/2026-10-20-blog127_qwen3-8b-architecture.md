---
layout: post
title: "#128 Inside Qwen3-8B: Layers, Attention Heads and Key/Value Heads You Can Build Yourself 🏗️"
categories: [AI, Software Engineering]
tags: [LLM, Qwen3, Transformer, Architecture, Attention, Visualization]
difficulty: Medium
---

A language model like Qwen3-8B looks mysterious until you notice that it is one small recipe repeated 36 times. In this post we put the network together from its real `config.json`: what a *hidden layer* is, why there are 36 of them, what an *attention head* is, why 32 query heads share only 8 key/value heads, and where all 8.19 billion weights sit. There is a zoomable map of the architecture and a builder where you assemble the network yourself.

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


## Contents

1. [Where the numbers come from: `config.json`](#config)
2. [What you need to know first](#basics)
3. [The architecture, drawn with the config values](#architecture)
4. [The KV cache](#kvcache)
5. [The four questions everyone asks](#questions)
6. [The map: zoom from the whole model down to one head (interactive)](#map)
7. [Assemble it yourself: depth, width and the size of the model (interactive)](#builder)
8. [One token's route through the network, in words](#route)
9. [Glossary](#glossary)
10. [Limits of this post and sources](#sources)

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

Ten of those keys define the shape of the network:

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
- `use_cache: true` is the KV cache from section 4.

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

## 2. What you need to know first

Five words are enough to read the rest of this post.

- **Vector**: a list of numbers. Inside the model every token is a vector of 4,096 numbers.
- **Matrix**: a grid of numbers. The **weights** (also called parameters) of the model are matrices. The "8B" in the name is the count of all the numbers in them.
- **Dot product**: multiply two lists number by number and add up the results. A large positive value means the two lists "match".
- **Matrix × vector**: every output number is the dot product of one row of the matrix with the vector. So every output is a weighted sum of *all* input numbers, with learned weights. This is the operation the whole network is built from.
- **FLOP**: one multiplication or one addition. A matrix with `m` rows and `k` columns times a vector costs `2 × m × k` FLOPs: 2 FLOPs per weight.

A tiny example, a 2×3 matrix times a vector of 3 numbers:

```
[ 1 2 3 ]   [ 1 ]     [ 1·1 + 2·0 + 3·2 ]   [  7 ]
[ 4 5 6 ] × [ 0 ]  =  [ 4·1 + 5·0 + 6·2 ] = [ 16 ]
            [ 2 ]
```

Everything in Qwen3-8B is this operation with bigger matrices (4,096 columns instead of 3), plus a few cheap extras such as normalising a vector or squashing numbers through a smooth switch.

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

<a id="kvcache"></a>

## 4. The KV cache

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

**Grouped-query attention (GQA) is why this is affordable.** With 32 KV heads instead of 8, every number above would be 4× larger (4.7 GB per user at 8,000 tokens). Notice the asymmetry: the cache stores **8** heads (bytes) but the attention math runs over **32** query heads (FLOPs). Every cached byte is therefore used by 4 query heads, which gives the KV part a fixed intensity of about 4 FLOP/byte, no matter how many users you have. Post #127 shows what this does to decode speed on a T4 and an A100.

<a id="questions"></a>

## 5. The four questions everyone asks

**What is a "hidden layer"?** A neural network has an input (here: the token), an output (here: a score for every word) and a lot of computation in between. Everything in between is called *hidden*, because nobody reads those intermediate vectors directly. In Qwen3-8B the hidden layers are **36 transformer blocks** that look alike, and `hidden_size` = 4,096 is the width of the vector that travels between them. (Each block itself contains several smaller steps. The config counts the blocks.)

**Why 36 layers?** It is a design choice, not a law of nature. One layer does one round of "gather information from the other tokens" (attention) followed by one round of "think about it" (MLP). Stacking layers lets later rounds build on the results of earlier ones, so depth buys **sequential steps of refinement**. The exact number follows from the designers' size budget: with a width of 4,096 one layer holds 11.5 × 4,096² = 192.9 M weights, and 36 of them plus the 1.24 B weights of embedding table and output head make the 8.19 B that give the model its name. The same budget could have been spent on fewer, wider layers or more, narrower ones. The builder below lets you try that.

**What is an attention head?** A head is one independent attention "reader". The 4,096 numbers of the query are cut into 32 slices of 128, and each slice computes its own scores and its own softmax, so each head can focus on different earlier tokens. Splitting is free: 32 heads × 128 numbers = 4,096, the same math, just in slices. Studies of transformer models in general found heads that follow the previous word, heads that copy repeated text and heads that match a number to its label. I did not verify what the heads of Qwen3-8B specifically do.

**Why only 8 key/value heads?** Memory. The keys and values of all earlier tokens are kept in the KV cache and read again for every generated token, and the cache grows with the number of key/value heads. In classic multi-head attention every query head has its own key/value head (32 of them). Qwen3-8B lets **4 query heads share one key/value head**, which makes the cache 4× smaller. This design is called grouped-query attention (GQA), and the paper that introduced it reports quality close to full multi-head attention at a much lower memory cost. The extreme version with a single shared head (multi-query attention) is even smaller but can cost quality. Eight is a compromise. It also divides 32 evenly and is a common head count when a model is split over 8 GPUs, but I cannot verify that this was Qwen's reason.

<a id="map"></a>

## 6. The map: zoom from the whole model down to one head

Click the level buttons, click a box in the drawing to zoom into it, or use **Zoom in** and **Zoom out**. At level 4 you can change the number of key/value heads and see what it does to the cache and to decode speed (estimated with the same assumptions as in post #127).

<div class="lq" id="lqE">
<div class="lq-row" style="gap:6px">
<button type="button" data-lv="1">1 Whole model</button>
<button type="button" data-lv="2">2 The stack</button>
<button type="button" data-lv="3">3 One layer</button>
<button type="button" data-lv="4">4 Attention heads</button>
<button type="button" data-lv="5">5 The MLP</button>
<button type="button" data-zoom="-1">Zoom out</button>
<button type="button" data-zoom="1">Zoom in</button>
</div>
<div id="lqE-stage" class="lq-card"></div>
<div id="lqE-info" class="lq-grid"></div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
function gb(x){return (x/1e9).toFixed(2)+" GB"}
var GRY="rgba(128,128,128,.14)",GS="rgba(128,128,128,.6)",GRN="rgba(27,175,122,.2)",GRS="#1baf7a",PUR="rgba(138,123,234,.22)",PUS="#8a7bea";
var PAL=["#2a78d6","#1baf7a","#eb6834","#8a7bea","#eda100","#e87ba4","#00a3bf","#8a8a86"];
var DEF='<defs><marker id="lqE-a" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse"><path d="M2 1L8 5L2 9" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></marker></defs>';
function T(x,y,s,o){o=o||{};return '<text x="'+x+'" y="'+y+'" text-anchor="'+(o.a||"middle")+'" fill="currentColor" font-size="'+(o.s||12)+'"'+(o.w?' font-weight="600"':"")+(o.o?' opacity="'+o.o+'"':"")+'>'+s+'</text>'}
function bx(x,y,w,h,fill,st,t1,t2,go){var g='<g'+(go?' data-go="'+go+'" style="cursor:pointer" tabindex="0" role="button" aria-label="Zoom into '+t1+'"':"")+'><rect x="'+x+'" y="'+y+'" width="'+w+'" height="'+h+'" rx="8" fill="'+fill+'" stroke="'+st+'"/>';
if(t2)g+=T(x+w/2,y+h/2-6,t1,{w:1,s:13})+T(x+w/2,y+h/2+12,t2,{o:0.8});else g+=T(x+w/2,y+h/2+4,t1,{w:1,s:13});
return g+'</g>'}
function ar(x1,y1,x2,y2){return '<line x1="'+x1+'" y1="'+y1+'" x2="'+x2+'" y2="'+y2+'" stroke="currentColor" stroke-width="1.5" marker-end="url(#lqE-a)"/>'}
function ln(x1,y1,x2,y2){return '<line x1="'+x1+'" y1="'+y1+'" x2="'+x2+'" y2="'+y2+'" stroke="currentColor" stroke-opacity=".4"/>'}
function svg(h,inner,label){return '<svg viewBox="0 0 680 '+h+'" width="100%" style="max-width:680px;display:block;margin:0 auto" role="img" aria-label="'+label+'">'+DEF+inner+'</svg>'}
function L1(){var s=bx(170,10,260,34,GRY,GS,"Text: “The invoice total is”")+ar(300,44,300,62)+bx(170,62,260,44,GRY,GS,"Tokenizer","151,936-piece dictionary")+ar(300,106,300,124)+bx(170,124,260,48,GRY,GS,"Embedding lookup","[151,936 × 4,096]")+ar(300,172,300,194);
s+=bx(170,194,260,96,GRN,GRS,"36 hidden layers","num_hidden_layers: 36",2)+ar(300,290,300,312)+bx(170,312,260,34,GRY,GS,"Final RMSNorm")+ar(300,346,300,364)+bx(170,364,260,48,GRY,GS,"LM head","[4,096 → 151,936]")+ar(300,412,300,432)+bx(170,432,260,34,GRY,GS,"Score for every word");
s+=ln(448,10,448,172)+T(458,84,"Input side",{a:"start",w:1})+T(458,102,"values you can read",{a:"start",o:0.75});
s+=ln(448,194,448,290)+T(458,236,"Hidden layers",{a:"start",w:1})+T(458,254,"vectors nobody reads",{a:"start",o:0.75});
s+=ln(448,312,448,466)+T(458,386,"Output side",{a:"start",w:1})+T(458,404,"scores you can read",{a:"start",o:0.75});
s+=T(40,242,"click to zoom in",{a:"start",s:11,o:0.7})+'<line x1="116" y1="238" x2="166" y2="238" stroke="currentColor" stroke-opacity=".5" stroke-dasharray="3 3"/>';
return svg(480,s,"The whole model: input side, hidden layers, output side")}
function L2(){var s=T(24,18,"The residual stream: one vector of 4,096 numbers rides down the belt",{a:"start",o:0.85})+'<line x1="80" y1="30" x2="80" y2="318" stroke="currentColor" stroke-width="2" marker-end="url(#lqE-a)"/>';
var ys=[52,96,140,212,256],nm=["Layer 1","Layer 2","Layer 3","Layer 35","Layer 36"];
ys.forEach(function(y,i){s+='<g data-go="3" style="cursor:pointer" tabindex="0" role="button" aria-label="Zoom into '+nm[i]+'"><rect x="170" y="'+y+'" width="160" height="34" rx="6" fill="'+GRN+'" stroke="'+GRS+'"/><rect x="336" y="'+y+'" width="164" height="34" rx="6" fill="'+PUR+'" stroke="'+PUS+'"/>'+T(250,y+21,"Attention")+T(418,y+21,"MLP")+'</g>'+T(512,y+21,nm[i],{a:"start",s:11,o:0.8})+ar(82,y+10,168,y+10)+ar(168,y+26,82,y+26)});
s+=T(335,198,"⋮  layers 4 to 34, 31 more  ⋮",{o:0.8})+T(24,338,"…and arrives at the LM head as one [4,096] vector",{a:"start",o:0.85});
s+=T(170,364,"Every layer: 192.9 M weights, same shape, its own learned values.",{a:"start"})+T(170,382,"Every layer reads the belt, adds a correction and writes it back.",{a:"start"})+T(170,400,"Every layer also writes 4,096 bytes per token into the KV cache.",{a:"start"});
return svg(414,s,"The stack of 36 layers along the residual stream")}
function L3(){var s='<line x1="80" y1="20" x2="80" y2="203" stroke="currentColor" stroke-width="2"/><line x1="80" y1="225" x2="80" y2="405" stroke="currentColor" stroke-width="2"/><line x1="80" y1="427" x2="80" y2="440" stroke="currentColor" stroke-width="2" marker-end="url(#lqE-a)"/>';
s+=T(24,16,"belt",{a:"start",o:0.7})+ar(80,52,168,52)+bx(170,36,150,32,GRY,GS,"RMSNorm")+ar(245,68,245,90)+bx(170,90,300,96,GRN,GRS,"Attention","tokens look at each other",4)+'<polyline points="320,186 320,214 93,214" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#lqE-a)"/>';
s+='<circle cx="80" cy="214" r="11" fill="'+GRY+'" stroke="'+GS+'"/>'+T(80,219,"+",{w:1,s:14});
s+=ar(80,254,168,254)+bx(170,238,150,32,GRY,GS,"RMSNorm")+ar(245,270,245,292)+bx(170,292,300,96,PUR,PUS,"MLP (SwiGLU)","each token thinks alone",5)+'<polyline points="320,388 320,416 93,416" fill="none" stroke="currentColor" stroke-width="1.5" marker-end="url(#lqE-a)"/>';
s+='<circle cx="80" cy="416" r="11" fill="'+GRY+'" stroke="'+GS+'"/>'+T(80,421,"+",{w:1,s:14});
s+=T(486,130,"Wq, Wk, Wv, Wo",{a:"start"})+T(486,148,"41.9 M weights",{a:"start",o:0.8})+T(486,332,"Wgate, Wup, Wdown",{a:"start"})+T(486,350,"151.0 M weights",{a:"start",o:0.8});
s+=T(486,60,"normalise first",{a:"start",o:0.75})+T(486,250,"normalise first",{a:"start",o:0.75})+T(122,208,"add",{a:"start",s:11,o:0.7})+T(122,410,"add",{a:"start",s:11,o:0.7});
return svg(452,s,"One layer: normalise, attention, add, normalise, MLP, add")}
function L5(){var s="",sc=500/12288,cx=340;
function bar(y,h,w,t,f){return '<rect x="'+(cx-w/2)+'" y="'+y+'" width="'+w+'" height="'+h+'" rx="4" fill="'+f+'" stroke="currentColor" stroke-opacity=".45"/>'+T(cx,y+h/2+4,t)}
s+=bar(14,26,4096*sc,"x [4,096]",GRY)+ar(cx,40,cx,60)+bar(62,24,12288*sc,"gate [12,288]",PUR)+bar(90,24,12288*sc,"up [12,288]",PUR)+ar(cx,114,cx,134)+bar(136,26,12288*sc,"h = SiLU(gate) × up   [12,288]",PUR)+ar(cx,162,cx,182)+bar(184,26,4096*sc,"out [4,096]",GRY);
s+=T(cx+4096*sc/2+10,32,"hidden_size",{a:"start",s:11,o:0.7})+T(cx+12288*sc/2+8,92,"3 ×",{a:"start",s:11,o:0.7});
var parts=[["Wq",16777216,0.5],["Wk",4194304,0.3],["Wv",4194304,0.3],["Wo",16777216,0.5],["MLP",150994944,1]],tot=192937984,x=40;
s+=T(40,246,"Where the 192.9 M weights of one layer sit",{a:"start",w:1});
parts.forEach(function(p,i){var w=600*p[1]/tot,att=i<4;s+='<rect x="'+x+'" y="256" width="'+w+'" height="30" fill="'+(att?"rgba(27,175,122,"+(0.22+p[2]*0.2)+")":"rgba(138,123,234,.3)")+'" stroke="currentColor" stroke-opacity=".45"/>';x+=w});
s+=T(40,304,"Attention 21.7%",{a:"start",o:0.9})+T(640,304,"MLP 78.3%",{a:"end",o:0.9})+T(40,324,"Wq 8.7% · Wk 2.2% · Wv 2.2% · Wo 8.7%      MLP: Wgate + Wup + Wdown",{a:"start",s:11,o:0.75});
return svg(338,s,"MLP widths and the share of weights in one layer")}
function fmtKV(kv){var qh=32,per=qh/kv,gw=600/kv,x0=40,y0=36,sq=12,st=16,cols=Math.max(1,Math.min(per,Math.floor((gw-6)/st))),rows=Math.ceil(per/cols),ah=rows*st,ky=y0+ah+30,s=T(40,20,"32 query heads (each square is one head of 128 numbers)",{a:"start",o:0.85});
for(var g=0;g<kv;g++){var gx=x0+g*gw,c=PAL[g%8];
s+='<g data-g="'+g+'"><rect x="'+(gx+2)+'" y="'+(y0-4)+'" width="'+(gw-4)+'" height="'+(ah+8)+'" rx="4" fill="'+c+'" fill-opacity=".1" stroke="'+c+'" stroke-opacity=".6"/>';
for(var i=0;i<per;i++){var r=Math.floor(i/cols),cc=i%cols;s+='<rect x="'+(gx+5+cc*st)+'" y="'+(y0+r*st)+'" width="'+sq+'" height="'+sq+'" rx="2" fill="'+c+'"/>'}
s+='<line x1="'+(gx+gw/2)+'" y1="'+(y0+ah+4)+'" x2="'+(gx+gw/2)+'" y2="'+ky+'" stroke="'+c+'" stroke-width="1.5"/><rect x="'+(gx+4)+'" y="'+ky+'" width="'+(gw-8)+'" height="28" rx="4" fill="'+c+'" fill-opacity=".28" stroke="'+c+'"/>';
if(gw>=44)s+=T(gx+gw/2,ky+18,"KV "+g,{s:11});s+='</g>'}
s+=T(40,ky+50,kv+" key/value head"+(kv>1?"s":"")+" (each 128 numbers for K and 128 for V)",{a:"start",o:0.85});
return svg(ky+62,s,"Query heads grouped around "+kv+" key value heads")}
function est(kv){var tok=18432*kv,per=tok*8000,bytes=5e9+6*per;return {tok:tok,per:per,a:1/(bytes/(0.6*1935e9)),t:1/(bytes/(0.6*320e9)),fit:bytes<=12.9e9}}
var kvSel=8;
function l4(){var per=32/kvSel,name=kvSel===32?"MHA, multi-head attention: every query head has its own key/value head":kvSel===1?"MQA, multi-query attention: all query heads share one key/value head":"GQA, grouped-query attention: "+per+" query heads share each key/value head";
var h='<div class="lq-row"><label>Key/value heads <select id="lqE-K">';
[32,16,8,4,2,1].forEach(function(k){h+='<option value="'+k+'"'+(k===kvSel?" selected":"")+'>'+k+(k===8?" (Qwen3-8B)":k===32?" (classic MHA)":k===1?" (MQA)":"")+'</option>'});
h+='</select></label></div><div class="lq-sm" style="margin:2px 0 8px"><b>'+name+'</b></div>'+fmtKV(kvSel);
h+='<div class="lq-sm" style="margin:8px 0">head_dim = 4,096 ÷ 32 = 128. Wq: [4,096 × 4,096], Wk and Wv: [4,096 × '+nf(kvSel*128)+'] each, Wo: [4,096 × 4,096]. Hover a group to highlight it.</div>';
h+='<table class="lq-t"><tr><th>KV heads</th><th>Cache per token</th><th>Per user at 8,000</th><th>A100 decode per user</th><th>T4 decode per user</th></tr>';
[32,16,8,4,2,1].forEach(function(k){var e=est(k);h+='<tr'+(k===kvSel?' style="font-weight:600"':"")+'><td>'+k+(k===8?" ← Qwen3-8B":"")+'</td><td>'+nf(e.tok)+' B</td><td>'+gb(e.per)+'</td><td>≈ '+Math.round(e.a)+' t/s</td><td>'+(e.fit?"≈ "+Math.round(e.t)+" t/s":"does not fit")+'</td></tr>'});
return h+'</table><div class="lq-sm lq-mut" style="margin-top:6px">Estimate for 36 layers, INT4 weights (≈ 5 GB), 6 users, 8,000 tokens each, 60% of peak bandwidth. It shows the memory effect only. Quality is not computed here.</div>'}
var LV={
1:{t:"The whole model",what:"Text goes in, a score for every possible next word comes out. In between sits the big stack of hidden layers.",cfg:"<code>vocab_size</code> 151,936 · <code>hidden_size</code> 4,096 · <code>num_hidden_layers</code> 36 · <code>tie_word_embeddings</code> false",
why:["<b>Input layer:</b> the tokens you feed in. <b>Output layer:</b> the scores you read out. Everything between them is <b>hidden</b>, because nobody looks at those vectors directly.","In a transformer the hidden layers are the stack of 36 blocks you zoom into next. <code>hidden_size</code> is the width of the vector that moves between them.","Embedding table and output head together hold 1.24 B of the 8.19 B weights (15%). A large dictionary covers many languages, code and symbols with few pieces per text.","The 36 blocks hold the other 6.95 B weights (85%)."]},
2:{t:"The stack of 36 layers",what:"Picture a conveyor belt, the residual stream. A vector of 4,096 numbers rides down the belt past 36 stations. Every station reads the vector, computes a correction and adds it back. Nothing is ever replaced.",cfg:"<code>num_hidden_layers</code> 36",
why:["<b>Why layers at all?</b> One layer can only do one round of “gather information from other tokens” and one round of “think about it”. Harder patterns need several rounds, each building on the previous result. Depth buys sequential steps of refinement.","<b>Are they all the same?</b> Same shape, different weights. Each layer learns its own job. In studies of transformers, early layers tend to pick up local patterns such as grammar and later layers more abstract relations. That is a tendency, not a rule, and I did not verify it for Qwen3-8B.","<b>Why 36?</b> A design choice. With a width of 4,096 one layer has 11.5 × 4,096² = 192.9 M weights. 36 layers plus 1.24 B for embedding and output head give the 8.19 B that name the model. The same budget could go into fewer, wider layers or more, narrower ones (try it in the builder below).","<b>What depth costs:</b> every layer's weights are read from memory for every generated token, the KV cache grows by 4,096 bytes per layer and token, and the layers must run strictly one after another."]},
3:{t:"One layer",what:"Two blocks in a row. Attention lets the tokens exchange information, the MLP processes every token alone. Before each block the vector is normalised, after each block the result is added back onto the belt.",cfg:"<code>hidden_size</code> 4,096 · <code>intermediate_size</code> 12,288 · <code>hidden_act</code> silu · <code>rms_norm_eps</code> 1e-6",
why:["<b>Attention</b> is the only place where tokens talk to each other: 41.9 M weights, 2.5 × hidden².","<b>MLP</b> thinks about each token on its own, like a huge learned memory: 151 M weights, 9 × hidden², which is 78% of the layer.","<b>Residual add (+):</b> new vector = old vector + correction. The original information cannot get lost and a layer only has to learn a small improvement. This is what makes 36 layers trainable.","<b>RMSNorm before each block</b> keeps the numbers in a stable range."]},
4:{t:"Attention heads",what:"The 4,096-number query is cut into 32 slices of 128 numbers. Each slice is a head: its own small attention reader with its own scores and its own softmax. Keys and values are cut into only 8 slices, and every group of 4 query heads reads the same key/value head.",cfg:"<code>num_attention_heads</code> 32 · <code>num_key_value_heads</code> 8 · <code>head_dim</code> 128",
why:["<b>What is a head?</b> One independent attention pattern. With 32 heads a layer can look at the earlier tokens in 32 different ways at once.","<b>Why not one big head?</b> One head makes one set of percentages. Different relationships need different percentages. Splitting costs nothing extra: 32 × 128 = 4,096 = <code>hidden_size</code>.","<b>Why head_dim 128?</b> A common size: big enough for a meaningful dot product and a good fit for GPU kernels such as FlashAttention.","<b>Why only 8 key/value heads?</b> Memory. The KV cache is read for every generated token and grows with the number of KV heads. Sharing each K/V head between 4 query heads makes the cache 4× smaller than classic multi-head attention. Try the selector: with 32 KV heads six users at 8,000 tokens no longer fit on a T4 at all, and an A100 decodes about 2.7× slower than with 8.","8 also divides 32 evenly and is a common count when a model is split across 8 GPUs. I cannot verify that this was Qwen's reason."]},
5:{t:"The MLP",what:"The vector is widened from 4,096 to 12,288 numbers twice in parallel (gate and up), combined with a smooth switch (SiLU) and squeezed back to 4,096.",cfg:"<code>intermediate_size</code> 12,288 · <code>hidden_act</code> silu",
why:["<b>Why wider?</b> The wide middle gives room to keep thousands of features apart. Research suggests that MLP layers act like a learned memory for facts and patterns, so width is capacity.","<b>Why 3×?</b> 12,288 = 3 × 4,096, a design choice. Gated MLPs (SwiGLU) have three matrices instead of two and therefore usually use a smaller ratio than the older 4× design. This is a general observation, not a statement about Qwen's reasoning.","<b>Three matrices:</b> Wgate and Wup (4,096 → 12,288) and Wdown (12,288 → 4,096), 50.3 M weights each.","The MLP is 78% of a layer's weights, so most of the weight traffic of decoding is MLP. Attention has fewer weights, but its KV cache grows with the context."]}
};
var lv=1;
function render(){var d=LV[lv],body=lv===1?L1():lv===2?L2():lv===3?L3():lv===4?l4():L5(),ul="";
d.why.forEach(function(w){ul+="<li>"+w+"</li>"});
$("lqE-stage").innerHTML='<div style="font-size:18px;font-weight:600;margin:0 0 8px">Level '+lv+': '+d.t+'</div>'+body;
$("lqE-info").innerHTML='<div class="lq-card"><b>What you see</b><p style="margin:6px 0 10px">'+d.what+'</p><b>Config keys</b><p style="margin:6px 0 0">'+d.cfg+'</p></div><div class="lq-card sm-desc"><b>Why these numbers</b><ul>'+ul+'</ul></div>';
document.querySelectorAll("#lqE button[data-lv]").forEach(function(b){b.className=(+b.getAttribute("data-lv")===lv)?"on":""})}
var root=$("lqE");
root.addEventListener("click",function(e){var b=e.target.closest("button[data-lv]");if(b){lv=+b.getAttribute("data-lv");render();return}
b=e.target.closest("button[data-zoom]");if(b){lv=Math.max(1,Math.min(5,lv+(+b.getAttribute("data-zoom"))));render();return}
var g=e.target.closest("[data-go]");if(g){lv=+g.getAttribute("data-go");render()}});
root.addEventListener("keydown",function(e){if(e.key==="Enter"||e.key===" "){var g=e.target.closest&&e.target.closest("[data-go]");if(g){e.preventDefault();lv=+g.getAttribute("data-go");render()}}});
root.addEventListener("change",function(e){if(e.target.id==="lqE-K"){kvSel=+e.target.value;render()}});
root.addEventListener("mouseover",function(e){var g=e.target.closest("[data-g]");if(!g)return;var id=g.getAttribute("data-g");root.querySelectorAll("[data-g]").forEach(function(n){n.style.opacity=n.getAttribute("data-g")===id?1:0.25})});
root.addEventListener("mouseout",function(e){if(e.relatedTarget&&e.relatedTarget.closest&&e.relatedTarget.closest("[data-g]"))return;root.querySelectorAll("[data-g]").forEach(function(n){n.style.opacity=1})});
render();
})();
</script>

<a id="builder"></a>

## 7. Assemble it yourself: depth, width and the size of the model

Press **Build it** to watch the network being put together piece by piece: first the embedding table, then layer after layer, finally the output head, while the parameter counter climbs towards 8.19 B. Then change the design. The tower is drawn with **width = hidden size** and **height = number of layers**. Its layers are split into an attention part (green) and an MLP part (purple) according to their share of the weights, and the dashed outline is Qwen3-8B for comparison.

Things to try:

- Keep the parameter count near 8.19 B and change the hidden size. The card "layers needed for 8.19 B" tells you how deep the tower would have to be (a wide, short tower or a narrow, tall one).
- Raise the number of layers and watch the **KV cache per token** grow in proportion.
- Raise the KV heads to the number of query heads (classic multi-head attention) and see the cache and the estimated decode speed react.

<div class="lq" id="lqF">
<div class="lq-row"><label for="lqF-L" style="min-width:70px">Layers</label><input type="range" id="lqF-L" min="4" max="80" value="36"><b id="lqF-Lv" style="min-width:34px;text-align:right"></b></div>
<div class="lq-row"><label>Hidden size <select id="lqF-H"><option value="2048">2,048</option><option value="3072">3,072</option><option value="4096" selected>4,096</option><option value="5120">5,120</option><option value="6144">6,144</option><option value="8192">8,192</option></select></label><label>KV heads <select id="lqF-K"></select></label><span id="lqF-q" class="lq-sm lq-mut"></span></div>
<div class="lq-row"><button type="button" id="lqF-b">▶ Build it</button><button type="button" id="lqF-r">Reset to Qwen3-8B</button></div>
<div class="lq-grid" style="grid-template-columns:repeat(auto-fit,minmax(290px,1fr));align-items:start"><div id="lqF-svg"></div><div id="lqF-m"></div></div>
<div class="lq-sm lq-mut" style="margin-top:8px">Fixed in this builder: head_dim 128, intermediate_size = 3 × hidden_size, vocabulary 151,936, separate embedding and output head. Estimates use 60% of peak A100 bandwidth, 6 users and 8,000 tokens each.</div>
</div>
<script>
(function(){
function $(id){return document.getElementById(id)}
function nf(x){return Math.round(x).toLocaleString("en-US")}
function B(x){if(x>=1e9)return (x/1e9).toFixed(2)+" B";if(x>=1e6)return (x/1e6).toFixed(1)+" M";return nf(x)}
function GB(x){if(x>=1e9)return (x/1e9).toFixed(1)+" GB";if(x>=1e6)return (x/1e6).toFixed(1)+" MB";return nf(x)+" B"}
var V=151936,TARGET=8190427136,stage=-1,timer=null;
var CAND=[1,2,3,4,6,8,12,16,24,32,40,48,64];
function fillK(keep){var H=+$("lqF-H").value,qh=H/128,sel=$("lqF-K"),opts="",valid=[];
CAND.forEach(function(k){if(k<=qh&&qh%k===0){valid.push(k);opts+='<option value="'+k+'">'+k+(k===qh?" (every head)":"")+'</option>'}});
var best=valid.indexOf(keep)>=0?keep:valid.filter(function(k){return k<=8}).pop();
sel.innerHTML=opts;sel.value=String(best);$("lqF-q").textContent=qh+" query heads"}
function calc(){var L=+$("lqF-L").value,H=+$("lqF-H").value,kv=+$("lqF-K").value,per=11*H*H+256*H*kv,emb=V*H,total=L*per+2*emb;
return {L:L,H:H,kv:kv,per:per,emb:emb,total:total,att:(2*H*H+256*H*kv)/per}}
function tower(c){var cx=170,sc=300/8192,w=c.H*sc,ref=4096*sc,yb=526,lh=5,st=stage;
function ok(i){return st<0||st-1>=i}
var s='<svg viewBox="0 0 340 540" width="100%" style="max-width:340px;display:block;margin:0 auto" role="img" aria-label="Tower of layers: width is hidden size, height is the number of layers">';
var topRef=yb-18-36*6-20;
s+='<rect x="'+(cx-ref/2-3)+'" y="'+(topRef-3)+'" width="'+(ref+6)+'" height="'+(yb-topRef+6)+'" rx="6" fill="none" stroke="currentColor" stroke-opacity=".5" stroke-dasharray="4 3"/><text x="'+(cx+ref/2+8)+'" y="'+(topRef+8)+'" font-size="11" fill="currentColor" opacity=".7">Qwen3-8B</text>';
s+='<rect x="'+(cx-w/2)+'" y="'+(yb-18)+'" width="'+w+'" height="18" rx="3" fill="rgba(128,128,128,.3)" stroke="currentColor" stroke-opacity=".5" opacity="'+((st<0||st>=1)?1:0.2)+'"/><text x="'+cx+'" y="'+(yb-5)+'" text-anchor="middle" font-size="11" fill="currentColor">Embedding</text>';
for(var i=1;i<=c.L;i++){var y=yb-18-i*6,on=ok(i),wa=w*c.att;
s+='<rect x="'+(cx-w/2)+'" y="'+y+'" width="'+wa+'" height="5" fill="rgba(27,175,122,.7)" opacity="'+(on?1:0.15)+'"/><rect x="'+(cx-w/2+wa)+'" y="'+y+'" width="'+(w-wa)+'" height="5" fill="rgba(138,123,234,.75)" opacity="'+(on?1:0.15)+'"/>'}
var yh=yb-18-c.L*6-20;
s+='<rect x="'+(cx-w/2)+'" y="'+yh+'" width="'+w+'" height="18" rx="3" fill="rgba(128,128,128,.3)" stroke="currentColor" stroke-opacity=".5" opacity="'+((st<0||st>=c.L+2)?1:0.2)+'"/><text x="'+cx+'" y="'+(yh+13)+'" text-anchor="middle" font-size="11" fill="currentColor">LM head</text>';
return s+'</svg>'}
function row(l,v){return '<div class="lq-m"><span>'+l+'</span><span>'+v+'</span></div>'}
function render(){var c=calc(),st=stage;$("lqF-Lv").textContent=c.L;
var built=st<0?c.total:(st>=1?c.emb:0)+Math.max(0,Math.min(c.L,st-1))*c.per+(st>=c.L+2?c.emb:0);
var tok=512*c.L*c.kv,bytes=c.total*0.61+6*8000*tok,speed=1/(bytes/(0.6*1935e9)),need=(TARGET-2*c.emb)/c.per;
var pct=Math.min(100,100*built/TARGET);
var m='<div class="lq-card"><div class="lq-m"><span>Parameters built</span><span><b>'+B(built)+'</b></span></div><div class="lq-bar" style="height:12px;margin:4px 0 10px"><div style="width:'+pct+'%;background:#2a78d6"></div></div>'+
row("Target (Qwen3-8B)","8.19 B")+row("Weights per layer",B(c.per))+row("Total parameters",B(c.total))+row("Size in FP16",GB(c.total*2))+row("Weight FLOPs per token",(2*(c.L*c.per+c.emb)/1e9).toFixed(1)+" GFLOPs")+row("KV cache per token",nf(tok)+" B")+row("KV cache per user at 8,000",GB(tok*8000))+row("A100 decode per user, INT4",c.L>0?"≈ "+Math.round(speed)+" t/s":"-")+row("Layers needed for 8.19 B at this width","≈ "+(need>0?need.toFixed(0):"none, embeddings alone are too big"))+'</div>';
$("lqF-svg").innerHTML=tower(c);$("lqF-m").innerHTML=m}
function stop(){if(timer){clearInterval(timer);timer=null}}
function start(){stop();var L=+$("lqF-L").value;stage=0;render();var inc=Math.max(1,Math.ceil(L/50));
timer=setInterval(function(){stage=Math.min(L+2,stage+(stage<1?1:inc));if(stage>=L+2){stage=-1;stop()}render()},90)}
$("lqF-b").addEventListener("click",start);
$("lqF-L").addEventListener("input",function(){stop();stage=-1;render()});
$("lqF-H").addEventListener("change",function(){stop();stage=-1;fillK(+$("lqF-K").value);render()});
$("lqF-K").addEventListener("change",function(){stop();stage=-1;render()});
$("lqF-r").addEventListener("click",function(){stop();stage=-1;$("lqF-L").value="36";$("lqF-H").value="4096";fillK(8);render()});
fillK(8);render();
})();
</script>

What the builder shows, in short:

- **Width vs depth at the same size.** About 8.19 B parameters can be reached with roughly 22 layers at width 5,120, 36 at 4,096, or about 66 at 3,072 (if the other ratios are kept). Qwen's designers picked one point on this line. I cannot tell you why that point and not another. Models of similar size from other families use different depths, so it is a design trade-off rather than a rule.
- **Depth costs memory traffic and cache.** Doubling the layers doubles the KV cache per token and, at the same width, roughly doubles the weights that must be streamed for every generated token.
- **Width costs weights quadratically.** One layer has 11.5 × hidden² weights, so a 25% wider model has about 56% more weights per layer.
- **Heads are free, KV heads are not.** Changing the number of query heads (which follows the hidden size at head_dim 128) does not change the FLOPs. Changing the number of KV heads changes the cache directly.


<a id="route"></a>

## 8. One token's route through the network, in words

The architecture above becomes a recipe once you follow a single token. The animated version, with real arithmetic in a miniature network and plain-words explanations for every step, is section 4 of post #127. Here is the route in short:

1. **Tokenizer**: the text is cut into pieces and every piece becomes an ID (a number from 0 to 151,935).
2. **Embedding lookup**: the ID selects one row of the embedding table, a vector of 4,096 numbers. This vector now travels through the network. It is called the *residual stream*.
3. **Repeat 36 times** (one hidden layer each):
   1. *RMSNorm*: rescale the vector to a standard size.
   2. *Q, K, V projections*: three matrix × vector products give the query, key and value (32 query heads, 8 key/value heads).
   3. *RoPE*: rotate the query and key by an angle that depends on the position of the token.
   4. *KV cache*: store the new key and value for later tokens.
   5. *Attention*: compare the query with all cached keys, turn the scores into percentages (softmax) and blend the cached values with them.
   6. *Output projection and residual*: mix the heads with `Wo` and add the result onto the vector.
   7. *RMSNorm, then the MLP*: widen to 12,288 numbers (gate and up), apply the SiLU switch, narrow back to 4,096 (down) and add the result onto the vector again.
4. **Final RMSNorm and LM head**: one score for each of the 151,936 dictionary entries.
5. **Softmax and sampling**: scores become probabilities, one token is chosen and appended. The loop restarts at step 2 with only that token. Everything older is read from the KV cache.

<a id="glossary"></a>

## 9. Glossary

| Term | Meaning in one line |
|---|---|
| Token / ID | A piece of text and its number in the dictionary (151,936 entries) |
| Embedding | The learned list of 4,096 numbers that stands for one token |
| Hidden layer, `num_hidden_layers` | One transformer block (attention + MLP). The 36 blocks between input and output are called hidden because nobody reads their vectors directly |
| `hidden_size` | The width of the vector that travels through all layers (4,096 numbers) |
| Residual stream | That vector. Every block adds a correction to it and never replaces it |
| Weights / parameters | The learned numbers inside the matrices (8.19 billion in total) |
| RMSNorm | Rescales a vector so that the typical size of its numbers is 1, then applies a learned scale |
| Query, key, value | What a token is looking for, how it is labelled, and what it hands over |
| Attention head | One independent group of query/key/value numbers (128 each) with its own scores and softmax |
| `head_dim` | The size of one head: 128 |
| MHA, MQA, GQA | Multi-head attention (every query head has its own key/value head), multi-query attention (one shared key/value head), grouped-query attention (groups of query heads share one; 4 per group here) |
| KV cache | Stored keys and values of all earlier tokens, so they are not recomputed |
| RoPE | Rotates pairs of numbers in the query and key by an angle that depends on the position |
| Softmax | Turns any list of scores into percentages that add up to 100% |
| MLP / feed-forward | The per-token block with the widest vectors; holds 78% of the weights of a layer |
| SiLU / SwiGLU | A smooth switch, and the gated MLP design that uses it |
| Logit | The raw score of one dictionary entry before softmax |

<a id="sources"></a>

## 10. Limits of this post and sources

**What is exact:** every shape, weight count, FLOP count and KV-cache size is derived from the real `config.json` and checked against the 8.19 billion parameters of the model.

**What is not verified:**

- *Why* the designers chose 36 layers, 8 key/value heads or a 3× wider MLP. I explain the trade-offs, but I cannot know their reasoning.
- What the heads and layers of Qwen3-8B specifically do. Statements such as "early layers tend to capture local patterns" or "some heads follow the previous word" come from research on transformer models in general.
- The decode speeds in the map and in the builder are estimates for memory effects only: 60% of peak A100 bandwidth, INT4 weights of about 5 GB (0.61 bytes per parameter), 6 users with 8,000 tokens each. Quality is not computed.

**Sources**

- Qwen3-8B `config.json`: [huggingface.co/Qwen/Qwen3-8B](https://huggingface.co/Qwen/Qwen3-8B/blob/main/config.json)
- Grouped-query attention: Ainslie et al., [GQA: Training Generalized Multi-Query Transformer Models from Multi-Head Checkpoints](https://arxiv.org/abs/2305.13245) (EMNLP 2023). It reports quality close to multi-head attention at a speed comparable to multi-query attention.
- The transformer: Vaswani et al., [Attention Is All You Need](https://arxiv.org/abs/1706.03762)
- Rotary position embedding: Su et al., [RoFormer](https://arxiv.org/abs/2104.09864)
- RMSNorm: Zhang and Sennrich, [Root Mean Square Layer Normalization](https://arxiv.org/abs/1910.07467)
- SwiGLU: Shazeer, [GLU Variants Improve Transformer](https://arxiv.org/abs/2002.05202)

*Next: the same network on a GPU, and what happens when we serve it to ten users at once (post #127).*
